**How is fine tunning possible**? Deep networks learn features hierarchically, then lower layers could be used to extract simple features.

The authors recommend only 2-4 epochs of training for fine-tuning BERT on a specific NLP task (compared to the hundreds of GPU hours needed to train the original BERT model or a LSTM from scratch!).

#### A Shift in NLP

This shift to transfer learning parallels the same shift that took place in computer vision a few years ago. Creating a good deep learning network for computer vision tasks can take millions of parameters and be very expensive to train. Researchers discovered that deep networks learn hierarchical feature representations (simple features like edges at the lowest layers with gradually more complex features at higher layers). Rather than training a new network from scratch each time, the lower layers of a trained network with generalized image features could be copied and transfered for use in another network with a different task. It soon became common practice to download a pre-trained deep network and quickly retrain it for the new task or add additional layers on top - vastly preferable to the expensive process of training a network from scratch. For many, the introduction of deep pre-trained language models in 2018 (ELMO, BERT, ULMFIT, Open-GPT, etc.) signals the same shift to transfer learning in NLP that computer vision saw.

---

## Install and Import

Then run the following cell to confirm that the GPU is detected.

```python
import tensorflow as tf

device_name = tf.test.gpu_device_name()

if device_name != '/device:GPU:0':

raise SystemError('GPU device not found')

print('Found GPU at: {}'.format(device_name))
```

Next, let's install the pytorch interface for BERT by Hugging Face. (This library contains interfaces for other pretrained language models like OpenAI's GPT and GPT-2.) We've selected the pytorch interface because it strikes a nice balance between the high-level APIs (which are easy to use but don't provide insight into how things work) and tensorflow code (which contains lots of details but often sidetracks us into lessons about tensorflow, when the purpose here is BERT!).

At the moment, the Hugging Face library seems to be the most widely accepted and powerful pytorch interface for working with BERT. In addition to supporting a variety of different pre-trained language models (and future models to come - BERT will not be state of the art forever), the library also includes **pre-built modifications of BERT suited to your specific task**. Here we will use **BertForSequenceClassification**, but the library also includes BERT modifications designed for token classification, question answering, next sentence prediciton, etc. Using these pre-built classes simplifies the process of modifying BERT for your purposes.

**Note**: as of July 2019, the pytorch library called "pytorch-pretrained-bert pytorch-nlp" has been updated and renamed as "pytorch-transformers." Though the old name still works, there is a short summary [here](https://github.com/huggingface/pytorch-transformers#Migrating-from-pytorch-pretrained-bert-to-pytorch-transformers) about migrating to the new library. (This mostly involves a few name changes.)

```python
!pip install pytorch-pretrained-bert pytorch-nlp
```

```python
import torch

from torch.utils.data import TensorDataset, DataLoader, RandomSampler, SequentialSampler

from keras.preprocessing.sequence import pad_sequences

from sklearn.model_selection import train_test_split

from pytorch_pretrained_bert import BertTokenizer, BertConfig

from pytorch_pretrained_bert import BertAdam, BertForSequenceClassification

from tqdm import tqdm, trange

import pandas as pd

import io

import numpy as np

import matplotlib.pyplot as plt

% matplotlib inline
```

_In_ order for torch to use the GPU, we need to identify and specify the GPU as the device. Later, in our training loop, we will load data onto the device.

```python
device = torch.device("cuda" if torch.cuda.is_available() else "cpu")

n_gpu = torch.cuda.device_count()

torch.cuda.get_device_name(0)
```
---

## Load Dataset

We'll use The Corpus of Linguistic Acceptability (CoLA) dataset for single sentence classification. It's a set of sentences labeled as grammatically correct or incorrect. The data is as follows:

**Column 1**: the code representing the source of the sentence.

**Column 2**: the acceptability judgment label (0=unacceptable, 1=acceptable).

**Column 3**: the acceptability judgment as originally notated by the author.

**Column 4**: the sentence.

- Download the dataset from this link, extract, and move them to your local drive.
- We will use the **raw** version of data, we need to use the BERT tokenizer to break the text down into tokens and chunks that the model will recognize.

```python
# Upload the train file from your local drive

from google.colab import files
uploaded = files.upload()
```
```python
df = pd.read_csv("in_domain_train.tsv", delimiter='\t', header=None, names=['sentence_source', 'label', 'label_notes', 'sentence'])
```
```python
df.shape
```
```python
df.sample(10)
```
```python
# Create sentence and label lists
sentences = df.sentence.values

# We need to add special tokens at the beginning and end of each sentence for BERT to work properly
sentences = ["[CLS] " + sentence + " [SEP]" for sentence in sentences]

labels = df.label.values
```

---

## Inputs

Next, import the BERT tokenizer, used to convert our text into tokens that correspond to BERT's vocabulary.

```python
tokenizer = BertTokenizer.from_pretrained('bert-base-uncased', do_lower_case=True)

tokenized_texts = [tokenizer.tokenize(sent) for sent in sentences]

print ("Tokenize the first sentence:")

print (tokenized_texts[0])
```

BERT requires specifically **==formatted inputs==**. For each tokenized input sentence, we need to create:

- **input ids**: a sequence of integers identifying each input token to its index number in the BERT tokenizer vocabulary
- **segment mask**: (optional) a sequence of 1s and 0s used to identify whether the input is one sentence or two sentences long. For one sentence inputs, this is simply a sequence of 0s. For two sentence inputs, there is a 0 for each token of the first sentence, followed by a 1 for each token of the second sentence
- **attention mask**: (optional) a sequence of 1s and 0s, with 1s for all input tokens and 0s for all padding tokens (we'll detail this in the next paragraph)
- **labels**: a single value of 1 or 0. In our task 1 means "grammatical" and 0 means "ungrammatical"


Although we can have variable length input sentences, BERT does requires our input arrays to be the same size. We address this by first *choosing a maximum sentence length*, and then padding and truncating our inputs until every input sequence is of the same length.

To "**pad**" our inputs in this context means that if a sentence is shorter than the maximum sentence length, we simply add 0s to the end of the sequence until it is the maximum sentence length.

If a sentence is longer than the maximum sentence length, then we simply truncate the end of the sequence, discarding anything that does not fit into our maximum sentence length.

We pad and truncate our sequences so that they all become of length MAX_LEN ("post" indicates that we want to pad and truncate at the end of the sequence, as opposed to the beginning) `pad_sequences` is a utility function that we're borrowing from Keras. It simply handles the truncating and padding of Python lists.

```python
# Set the maximum sequence length. The longest sequence in our training set is 47, but we'll leave room on the end anyway.
# In the original paper, the authors used a length of 512.
MAX_LEN = 128

# Use the BERT tokenizer to convert the tokens to their index numbers in the BERT vocabulary
input_ids = [tokenizer.convert_tokens_to_ids(x) for x in tokenized_texts]

# Pad our input tokens
input_ids = pad_sequences(input_ids, maxlen=MAX_LEN, dtype="long", truncating="post", padding="post")
```
##### Create the attention masks

```python
# Create attention masks
attention_masks = []

# Create a mask of 1s for each token followed by 0s for padding
for seq in input_ids:

seq_mask = [float(i>0) for i in seq]

attention_masks.append(seq_mask)
```

```python
# Use train_test_split to split our data into train and validation sets for training
train_inputs, validation_inputs, train_labels, validation_labels = train_test_split(input_ids, labels,

random_state=2018, test_size=0.1)

train_masks, validation_masks, _, _ = train_test_split(attention_masks, input_ids,

random_state=2018, test_size=0.1)
```

```python
# Convert all of our data into torch tensors, the required datatype for our model
train_inputs = torch.tensor(train_inputs)

validation_inputs = torch.tensor(validation_inputs)

train_labels = torch.tensor(train_labels)

validation_labels = torch.tensor(validation_labels)

train_masks = torch.tensor(train_masks)

validation_masks = torch.tensor(validation_masks)
```

```python
# Select a batch size for training. For fine-tuning BERT on a specific task, the authors recommend a batch size of 16 or 32
batch_size = 32

# Create an iterator of our data with torch DataLoader. This helps save on memory during training because, unlike a for loop,

# with an iterator the entire dataset does not need to be loaded into memory
train_data = TensorDataset(train_inputs, train_masks, train_labels)

train_sampler = RandomSampler(train_data)

train_dataloader = DataLoader(train_data, sampler=train_sampler, batch_size=batch_size)

  

validation_data = TensorDataset(validation_inputs, validation_masks, validation_labels)

validation_sampler = SequentialSampler(validation_data)

validation_dataloader = DataLoader(validation_data, sampler=validation_sampler, batch_size=batch_size)
```

 - Create an iterator of our data with torch **DataLoader**. This helps save on memory during training because, unlike a for loop, with **an iterator the entire dataset does not need to be loaded into memory**.

---

## Train Model


Now that our input data is properly formatted, it's time to fine tune the BERT model.

For this task, we first want to modify the pre-trained BERT model to give outputs for classification, and then we want to continue training the model on our dataset until that the entire model, end-to-end, is well-suited for our task. Thankfully, the huggingface pytorch implementation includes a set of interfaces designed for a variety of NLP tasks. Though these interfaces are all built on top of a trained BERT model, each has different top layers and output types designed to accomodate their specific NLP task.

We'll load [BertForSequenceClassification](https://github.com/huggingface/pytorch-pretrained-BERT/blob/master/pytorch_pretrained_bert/modeling.py#L1129). This is the normal BERT model with an added single linear layer on top for classification that we will use as a sentence classifier. **As we feed input data, the entire pre-trained BERT model and the additional untrained classification layer is trained on our specific task.**

### ==Structure of Fine-Tuning Model==

As we've showed beforehand, the first token of every sequence is the special classification token ([CLS]). Unlike the hidden state vector corresponding to a normal word token, the hidden state corresponding to this special token is designated by the authors of BERT **as an aggregate representation of the whole sentence used for classification tasks**. As such, when we feed in an input sentence to our model during training, the output is the length 768 hidden state vector **corresponding to this token**. The additional layer that we've added on top consists of untrained linear neurons of size [hidden_state, number_of_labels], so ==**[768,2]**,== meaning that the output of BERT plus our classification layer is a vector of two numbers representing the "score" for "grammatical/non-grammatical" that are then fed into cross-entropy loss.

### The Fine-Tuning Process

Because the pre-trained BERT layers already encode a lot of information about the language, training the classifier is relatively inexpensive. Rather than training every layer in a large model from scratch, it's as if we have already trained the bottom layers 95% of where they need to be, and only really need to train the top layer, with a bit of tweaking going on in the lower levels to accomodate our task.

Sometimes practicioners will opt to **"freeze" certain layers when fine-tuning, or to apply different learning rates, apply diminishing learning rates, etc**. all in an effort to *preserve the good quality weights in the network and speed up training* (often considerably). In fact, recent research on BERT specifically has demonstrated that freezing the majority of the weights results in only minimal accuracy declines, but there are exceptions and broader rules of transfer learning that should also be considered. For example, *if your task and fine-tuning dataset is very different from the dataset used to train the transfer learning model, freezing the weights may not be a good idea.* 

OK, let's load BERT! There are a few different pre-trained BERT models available. "bert-base-uncased" means the version that has only lowercase letters ("uncased") and is the smaller version of the two ("base" vs "large").

```python
# Load BertForSequenceClassification, the pretrained BERT model with a single linear classification layer on top.
model = BertForSequenceClassification.from_pretrained("bert-base-uncased", num_labels=2)

model.cuda()
```

Now that we have our model loaded we need to grab the training hyperparameters from within the stored model.

For the purposes of fine-tuning, the authors recommend the following hyperparameter ranges:

- Batch size: 16, 32
- Learning rate (Adam): 5e-5, 3e-5, 2e-5
- Number of epochs: 2, 3, 4

```python
param_optimizer = list(model.named_parameters())

no_decay = ['bias', 'gamma', 'beta']

optimizer_grouped_parameters = [

{'params': [p for n, p in param_optimizer if not any(nd in n for nd in no_decay)],

'weight_decay_rate': 0.01},

{'params': [p for n, p in param_optimizer if any(nd in n for nd in no_decay)],

'weight_decay_rate': 0.0}

]
```
![[Pasted image 20250413164736.png]]
![[Pasted image 20250413172522.png]]

- `model.named_parameters()` retrieves all the model parameters along with their names.
    
- `param_optimizer` is a list of `(name, parameter)` tuples.
    
- `no_decay` is a list of parameter name substrings that **should not** have weight decay applied (commonly `bias`, `gamma`, and `beta`).

```python
# This variable contains all of the hyperparemeter information our training loop needs

optimizer = BertAdam(optimizer_grouped_parameters,

lr=2e-5,

warmup=.1)
```

Below is our training loop. There's a lot going on, but fundamentally for each pass in our loop we have a trianing phase and a validation phase. At each pass we need to:

Training loop:

- Tell the model to compute gradients by setting the model in train mode
- Unpack our data inputs and labels
- Load data onto the GPU for acceleration
- Clear out the gradients calculated in the previous pass. **In pytorch the gradients accumulate by default (useful for things like RNNs)** unless you explicitly clear them out
- Forward pass (feed input data through the network)
- Backward pass (backpropagation)
- Tell the network to update parameters with optimizer.step()
- Track variables for monitoring progress

Evalution loop:

- Tell the model not to compute gradients by setting the model in evaluation mode
- Unpack our data inputs and labels
- Load data onto the GPU for acceleration
- Forward pass (feed input data through the network)
- Compute loss on our validation data and track variables for monitoring progress

So please read carefully through the comments to get an understanding of what's happening. If you're unfamiliar with pytorch a quick look at some of their [beginner tutorials](https://www.google.com/url?q=https%3A%2F%2Fpytorch.org%2Ftutorials%2Fbeginner%2Fblitz%2Fcifar10_tutorial.html%23sphx-glr-beginner-blitz-cifar10-tutorial-py) will help show you that training loops really involve only a few simple steps; the rest is usually just decoration and logging.

```python
# Function to calculate the accuracy of our predictions vs labels
def flat_accuracy(preds, labels):

pred_flat = np.argmax(preds, axis=1).flatten()

labels_flat = labels.flatten()

return np.sum(pred_flat == labels_flat) / len(labels_flat)
```

```python
# Store our loss and accuracy for plotting
train_loss_set = []
  
# Number of training epochs (authors recommend between 2 and 4)
epochs = 4

# trange is a tqdm wrapper around the normal python range
for _ in trange(epochs, desc="Epoch"):

# Training
# Set our model to training mode (as opposed to evaluation mode)

model.train()

# Tracking variables

tr_loss = 0

nb_tr_examples, nb_tr_steps = 0, 0

# Train the data for one epoch

for step, batch in enumerate(train_dataloader):

# Add batch to GPU

batch = tuple(t.to(device) for t in batch)

# Unpack the inputs from our dataloader

b_input_ids, b_input_mask, b_labels = batch

# Clear out the gradients (by default they accumulate)

optimizer.zero_grad()

# Forward pass

loss = model(b_input_ids, token_type_ids=None, attention_mask=b_input_mask, labels=b_labels)

train_loss_set.append(loss.item())

# Backward pass

loss.backward()

# Update parameters and take a step using the computed gradient

optimizer.step()

# Update tracking variables

tr_loss += loss.item()

nb_tr_examples += b_input_ids.size(0)

nb_tr_steps += 1

print("Train loss: {}".format(tr_loss/nb_tr_steps))

# Validation

# Put model in evaluation mode to evaluate loss on the validation set
model.eval()
# Tracking variables

eval_loss, eval_accuracy = 0, 0

nb_eval_steps, nb_eval_examples = 0, 0
# Evaluate data for one epoch

for batch in validation_dataloader:

# Add batch to GPU

batch = tuple(t.to(device) for t in batch)

# Unpack the inputs from our dataloader

b_input_ids, b_input_mask, b_labels = batch

# Telling the model not to compute or store gradients, saving memory and speeding up validation

with torch.no_grad():

# Forward pass, calculate logit predictions

logits = model(b_input_ids, token_type_ids=None, attention_mask=b_input_mask)

# Move logits and labels to CPU

logits = logits.detach().cpu().numpy()

label_ids = b_labels.to('cpu').numpy()

tmp_eval_accuracy = flat_accuracy(logits, label_ids)

eval_accuracy += tmp_eval_accuracy

nb_eval_steps += 1

print("Validation Accuracy: {}".format(eval_accuracy/nb_eval_steps))
```

- ##### In train mode, model output is loss formula (to be backpropagated), but in eval mode model output is logits.

## Training Evaluation

Let's take a look at our training loss over all batches:
```python
plt.figure(figsize=(15,8))

plt.title("Training loss")

plt.xlabel("Batch")

plt.ylabel("Loss")

plt.plot(train_loss_set)

plt.show()
```

## Predict and Evaluate on Holdout Set

Now we'll load the holdout dataset and prepare inputs just as we did with the training set. Then we'll evaluate predictions using [Matthew's correlation coefficient](https://www.google.com/url?q=https%3A%2F%2Fscikit-learn.org%2Fstable%2Fmodules%2Fgenerated%2Fsklearn.metrics.matthews_corrcoef.html) because this is the metric used by the wider NLP community to evaluate performance on CoLA. With this metric, +1 is the best score, and -1 is the worst score. This way, we can see how well we perform against the state of the art models for this specific task.

```python
# Upload the test file from your local drive
from google.colab import files
uploaded = files.upload()
```

```python
df = pd.read_csv("out_of_domain_dev.tsv", delimiter='\t', header=None, names=['sentence_source', 'label', 'label_notes', 'sentence'])

# Create sentence and label lists
sentences = df.sentence.values


# We need to add special tokens at the beginning and end of each sentence for BERT to work properly
sentences = ["[CLS] " + sentence + " [SEP]" for sentence in sentences]
labels = df.label.values
tokenized_texts = [tokenizer.tokenize(sent) for sent in sentences]
MAX_LEN = 128


# Use the BERT tokenizer to convert the tokens to their index numbers in the BERT vocabulary
input_ids = [tokenizer.convert_tokens_to_ids(x) for x in tokenized_texts]

# Pad our input tokens
input_ids = pad_sequences(input_ids, maxlen=MAX_LEN, dtype="long", truncating="post", padding="post")

# Create attention masks
attention_masks = []

  

# Create a mask of 1s for each token followed by 0s for padding
for seq in input_ids:
seq_mask = [float(i>0) for i in seq]
attention_masks.append(seq_mask)
prediction_inputs = torch.tensor(input_ids)

prediction_masks = torch.tensor(attention_masks)

prediction_labels = torch.tensor(labels)

batch_size = 32
  

prediction_data = TensorDataset(prediction_inputs, prediction_masks, prediction_labels)

prediction_sampler = SequentialSampler(prediction_data)

prediction_dataloader = DataLoader(prediction_data, sampler=prediction_sampler, batch_size=batch_size)
```

```python
# Prediction on test set

# Put model in evaluation mode
model.eval()

# Tracking variables
predictions , true_labels = [], []


# Predict
for batch in prediction_dataloader:

# Add batch to GPU
batch = tuple(t.to(device) for t in batch)

# Unpack the inputs from our dataloader
b_input_ids, b_input_mask, b_labels = batch

# Telling the model not to compute or store gradients, saving memory and speeding up prediction
with torch.no_grad():

# Forward pass, calculate logit predictions
logits = model(b_input_ids, token_type_ids=None, attention_mask=b_input_mask)

  

# Move logits and labels to CPU
logits = logits.detach().cpu().numpy()

label_ids = b_labels.to('cpu').numpy()

# Store predictions and true labels
predictions.append(logits)

true_labels.append(label_ids)
```

```python
# Import and evaluate each test batch using Matthew's correlation coefficient
from sklearn.metrics import matthews_corrcoef

matthews_set = []

for i in range(len(true_labels)):
	matthews = matthews_corrcoef(true_labels[i],
	        np.argmax(predictions[i], axis=1).flatten())
	matthews_set.append(matthews)
```

![[Pasted image 20250413182252.png]]

---

Both **LoRA** (Low-Rank Adaptation) and **quantization** are techniques used to make **large language models (LLMs)** more efficient, but they address **different problems** in the model lifecycle:

### **LoRA (Low-Rank Adaptation)** – _Efficient fine-tuning_

- **What it is**: A technique to fine-tune large models with fewer trainable parameters by inserting low-rank matrices into specific parts of the model (usually attention layers).
    
- **Goal**: Efficient **adaptation** of a pre-trained model to a new task/domain without updating the entire model.
    
- **How it works**:
    
    - Instead of updating the full weight matrix W, LoRA keeps W frozen and adds a trainable component:  
        ![[Pasted image 20250414134333.png]]
    - These A and B matrices (the "low-rank" part) are the only ones trained.
        
- **Advantages**:
    
    - Saves memory and compute during fine-tuning.
        
    - Easier to share adaptations (just the LoRA weights).
        
    - Works well with models like LLaMA, GPT, BERT, etc.

### **Quantization** – _Efficient inference_

- **What it is**: A method to reduce the **precision** of the model’s weights and activations (e.g., ==**from 32-bit floats to 8-bit integers**==).
    
- **Goal**: Reduce **memory usage** and **inference latency**.
    
- **Types**:
    
    - **Post-training quantization (PTQ)**: Quantize a pre-trained model without re-training. (You take a **fully trained model** and compress it **after** training — by converting its weights and/or activations to lower precision (e.g., from 32-bit floats to 8-bit integers))
        
    - **Quantization-aware training (QAT)**: Train the model with quantization in mind for better performance. While training, the model behaves _as if_ it’s already using quantized weights and activations. This way, it gets used to those constraints and performs better when actually quantized.
        
- **Common bitwidths**: FP16, INT8, even INT4 (as in QLoRA!).
    
- **Advantages**:
    
    - Smaller model size (can fit on lower-end GPUs or even CPUs).
        
    - Faster inference with little to moderate drop in accuracy (if done right).

| Feature                | LoRA                               | Quantization                           |
| ---------------------- | ---------------------------------- | -------------------------------------- |
| Purpose                | Fine-tuning                        | Inference optimization                 |
| When applied           | During or before fine-tuning       | After or during training               |
| Affects model weights? | Adds small trainable layers        | Compresses existing weights            |
| Training speed         | Faster than full fine-tuning       | Not directly relevant (inference-only) |
| Inference speed        | Slightly slower (adds computation) | Much faster (less compute per op)      |
| Model size             | Slight increase (due to adapters)  | Significant reduction                  |
