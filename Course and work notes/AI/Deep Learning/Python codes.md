
#### Fine tunning BertForSequenceClassification code:

#### Make input_ids (and labels)
1. Load the dataset (sentences and labels) using Pandas dataframe
2. Add CLS and SEP tokens between sentences 
3. Use tokenizer.tokenize to add tokens between words
4. Use tokenizer's token_to_input_ids to map words to their dictionary id
5. Add padding to the input 

#### Make the attention_mask

### Train and validation (inputs, labels, mask)
1. Input input_ids and labels to split function ---> train and validation ids and labels
2. Input input_ids and attention_masks to split function ---> train and validation mask

#### Convert everything to torch tensors

#### Create DataLoader iterator (memory efficient batch iteration)
1. wrap train and validation labels, ids and masks into a *TensorDataset*
2. wrap the output into a random sampler
3. wrap the above two along with batch size into a Dataloader

#### Create Optimizer
1. Could be Adam, SGD,...
2. Takes batch size, lr, epochs, and weight_decay_rate=0 parameters

#### Write an accuracy calculator function
1. This is used in validation mode 

#### Train and validate
- for epochs 
	- model.train
	    - for batch -> load train batches to cpu and split into ids, labels and masks -> clear previous gradients (optimizer.zero_grad) -> model inference (gives loss formula for backpropagation) -> loss.backward -> optimizer.step (update model parameters)
	- model.eval
	     - do the same for validation data -> with torch.no_grad make an inference from the model to get logits (this does not compute or stores gradients at all) -> load logits into cpu -> compute accuracy for epoch plotting

---

- Load the dataset using pandas library.
- By using pandas .describe() function, you can see statistics of each feature and measure if they are in the **same scale** or not, thus if you need normalization or not. Also look for **corelation matrix**.
  Or check if the dataset is **balanced** or not.
  ![[Pasted image 20240809220625.png]]

- Do not forget to split the data (test, train, validation) using scikit learn.
- Also use scikit learn to import several kind of regression models (like linear regression)
- We can use scikit learns's PCA for dimension reduction of large data inputs. W e do the dimension reduction form 784 to 2 to be able to visualize the clustered data in 2 dims.
  ![[Pasted image 20240809215204.png]]
- K-means algorithm also exist in scikit learn library.
- Scikit learns provides various validation metric calculation, like accuracy, precision, recall, etc and a classification report to report all those metrics.

------------------------------

In PCA, it is important to avoid losing information in the reduced data, by preserving the data points' variance. So we map all point to a plane that data points have the largest variance in that direction.

![[Pasted image 20240809215929.png]]

------------------------------------------

Binary classification using a neural network:

- WE use pytorch library.
- torch.nn to define the network and torch.optim to minimize the loss.
- And scikit learn for data creation (make_blobs), etc:
  ![[Pasted image 20240809222138.png]]

- To use pytorch, **you have to transform your data to tensors** and to use GPUs for computations.
- Defining a neural network using pytorch:
  ![[Pasted image 20240809222459.png]]
  choose loss and optimizer:
  ![[Pasted image 20240809224032.png]]
  
  trainig loop (set the model to train mode for epoch numbers, calculate the loss for each output, derive the loss bt loos.backward and optimize, .zero_grad makes the gradient zero in each step)
  ![[Pasted image 20240809225416.png]]
Test step (set the model to evaluation)

--------------------------------------------------

Pytorch:

- Tensor is sth to help in fast matrix operation.
- .device and .requires grad(enables derivation from that variable and the derivation value then stored in x.grad) for tensors
- You can also use numpy arrays but they can't be used with GPUs.
- When we do derivation and polynomials in torch, **it is automatically done with computational graph.**
  ![[Pasted image 20240809235121.png]]

 - x.grad is cumulative and is added to every time we compute y.backward.
 - If we want to do some operations that do not need gradient and computational graph, we use "with torch.no_grad": 
   It is more efficient
   ![[Pasted image 20240810000617.png]]

- Another way of defining NN:
  ![[Pasted image 20240810001127.png]]
-----------

```python
from sklearn.manifold import TSNE

  

# Sample a subset of the data for t-SNE due to computational constraints

sample_data = df.sample(n=5000, random_state=42)

sample_features = sample_data.drop(columns=['Class'])

sample_labels = sample_data['Class']

  

tsne = TSNE(n_components=2, random_state=42)

tsne_results = tsne.fit_transform(sample_features)

  

plt.figure(figsize=(5, 4))

sns.scatterplot(x=tsne_results[:, 0], y=tsne_results[:, 1], hue=sample_labels, palette='bright')

plt.title('t-SNE Visualization of Transactions')

plt.xlabel('t-SNE Component 1')

plt.ylabel('t-SNE Component 2')

plt.show()
```

This code is used to **visualize high-dimensional data** (such as transaction data) in a two-dimensional space using t-SNE, a popular technique for visualizing complex data. By reducing the data to two dimensions, you can visually inspect how the data points are grouped or separated, which can provide insights into patterns, clusters, and relationships in the data, especially when the data points are colored by their class labels.

--------------------------------------------

```python
from sklearn.ensemble import IsolationForest

  

copy_df = df.copy()

copy_df = copy_df.dropna()

  

iso_forest = IsolationForest(contamination=0.01, random_state=42)

outliers = iso_forest.fit_predict(copy_df.drop(columns=['Class']))

  

copy_df['Outlier'] = outliers

  

plt.figure(figsize=(5, 4))

sns.scatterplot(x='V1', y='V2', hue='Outlier', data=copy_df.sample(n=5000, random_state=42), palette='coolwarm')

plt.title('Isolation Forest Outlier Detection')

plt.show()
```

This code is used to detect and visualize outliers in a dataset using the Isolation Forest algorithm. Outliers are points that differ significantly from the majority of the data and can indicate anomalies, fraudulent transactions, or errors in data collection. The final plot provides a visual representation of how these outliers are distributed in the feature space defined by `V1` and `V2`.

-------------------------------------------

- The benefit of using AutoTokenizer is that it abstracts away the need to know the specific tokenizer class associated with each pre-trained model. Instead, it provides a convenient interface for loading tokenizers in a consistent manner across different models.

- **OpenRouter** is a platform that provides access to various models, including but not limited to GPT models from OpenAI.

------------------------------------------

- If you are setting temperature=0.0, it typically means you want deterministic output, which should not be used unless do_sample=True is also set. By setting temperature to a non-zero value and do_sample=True, you can generate more diverse outputs, or simply remove the temperature parameter if you don't intend to use sampling.

----------------------------------
 - **Setting the max_length parameter in the model.generate() method to be equal to the input length**: When the max_length parameter is set to a value that equals the length of the input_ids being passed in, it means there's no room allowed for the model to add any tokens, leading to the model generating nothing.
   
   To solve this issue, you need to ensure that max_length is greater than the length of your input_ids to give space for the model to generate a response. Alternatively, you can use the max_new_tokens parameter instead of max_length to specify how many new tokens you want the model to generate beyond the input length.

-----------------------------------------

- how to use two different methods of a hypothetical tokenizer in a natural language processing (NLP) framework, each designed for distinct types of tasks?

### 1. Use **tokenizer()** for:

- **Method Usage**: The `tokenizer()` method is a general-purpose function used for preparing text for various NLP tasks. It is likely capable of converting raw text into a format that machine learning models can process, such as tokenizing strings into words or subwords, and possibly converting these tokens into numerical IDs that models can understand.
    
- **Applicability**: The method is versatile and suitable for a broad range of NLP tasks, including but not limited to text classification (e.g., sentiment analysis, topic classification) and entity recognition (identifying names, locations, etc. in text). These tasks generally require standard tokenization where the text is segmented and indexed without needing any specialized formatting.

### 2. Use **tokenizer.apply_chat_template()** for:

- **Method Usage**: The `tokenizer.apply_chat_template()` method seems to be a specialized function of the tokenizer, designed to format and preprocess text specifically for dialogue-based models. This might involve applying a template or structure that aligns with how dialogue data is expected to be formatted in these models, such as framing statements and responses in a conversational context.

-------------------------------

The __init__.py file makes directories to be used as libraries.

- In the main folder of the whole project, including several module files, we create the init.py file and write the name of the modules there like this:
  
  ![[Pasted image 20240523154308.png]]
  This way, we don't need to use modules using the above python commands in the code, instead we just import the whole project folder and we are able to a**ccess the attributes of its modules directly**:
  
  ![[Pasted image 20240523154550.png]]

-------------------------

Autoregressive generation: 

- Using the decoder function of a transformer model in a way that incorporates the output of previous data as input for the current data is a common strategy in sequence generation tasks like machine translation, text summarization, or chatbot conversation generation. This strategy is known as **autoregressive generation**. In autoregressive generation, the model outputs one token at a time, and each subsequent token is predicted based on the previously generated tokens.

### Step 1: Load the Model and Tokenizer

First, you need to load your model and its corresponding tokenizer. Here’s an example using the GPT-2 model, which can be adapted to other transformer models:

```python
from transformers import GPT2Tokenizer, GPT2LMHeadModel

tokenizer = GPT2Tokenizer.from_pretrained('gpt2')
model = GPT2LMHeadModel.from_pretrained('gpt2')
model.eval()  # Set the model to evaluation mode if not training

```

### Step 2: Encode Initial Input

Encode the initial input text that will start the generation process. This could be an empty string if you're generating text from scratch or some initial context:

```python
initial_text = "The science lecture"
input_ids = tokenizer.encode(initial_text, return_tensors='pt')  # 'pt' for PyTorch

```

### Step 3: Generate Tokens Autoregressively

You can now generate text by feeding the output of the model back as input, appending new tokens one at a time:

```python
import torch

generated = input_ids  # Tensor that stores all generated tokens
max_length = 50  # Maximum length of the output sequence

with torch.no_grad():  # No need to calculate gradients
    while generated.shape[1] < max_length:
        outputs = model(generated)  # Get model output for the current sequence
        next_token_logits = outputs.logits[:, -1, :]  # Get the logits of the last token
        next_token = torch.argmax(next_token_logits, dim=-1).unsqueeze(0)  # Pick the most likely next token
        generated = torch.cat((generated, next_token.unsqueeze(0)), dim=1)  # Append generated token

# Decode the generated tokens to a string
generated_text = tokenizer.decode(generated.squeeze().tolist())
print(generated_text)

```

### Explanation

1. **Generating One Token at a Time**: The script generates one token at a time and appends it to the sequence. This is done until the maximum sequence length is reached.
   
2. **Using logits**: The logits (i.e., the raw, non-normalized predictions of the model) of the last generated token are used to predict the next token.
   
3. **Concatenation**: Each newly predicted token is concatenated to the existing sequence (`generated`) and used as the new input for the model.
   
4. **Purpose**: The `squeeze()` method is called on the `generated` tensor, which may contain one or more singleton dimensions (dimensions of size 1). This often happens in deep learning models where operations like `unsqueeze()`, used earlier in the code, add singleton dimensions to keep the tensor shapes compatible with model requirements.
   
5. **Functionality**: By calling `squeeze()`, any dimensions of size 1 are removed from the tensor. For example, if the shape of `generated` was `[1, 50]`, calling `squeeze()` would change its shape to `[50]`. This is important for feeding the tensor into functions that expect inputs of specific dimensions, like `tokenizer.decode()`, which expects a flat list of token IDs.
   
6. `torch.no_grad()`: This context manager tells PyTorch not to compute or store gradients. This is important during inference (model deployment) when you're only making predictions and **not training the model,** as it reduces memory usage and speeds up processing.
   
7. `outputs = model(generated)`: The model processes the currently generated tokens and outputs logits for the next possible tokens.
   
8. `next_token_logits = outputs.logits[:, -1, :]`: Extracts the logits (raw output scores from the model) of the last token generated. Logits are used to determine the probability of each token being the next appropriate token.
   
9. `next_token = torch.argmax(next_token_logits, dim=-1).unsqueeze(0)`: ==**Finds the token with the highest logit** (most likely next token) and reshapes the tensor to maintain the correct dimensions for concatenation.==

-----------------------------------------------------

### Understanding Logits:

1. **Definition**:
    
    - **Logits** are the raw, unnormalized scores that a model's final neural network layer outputs. These scores are real numbers and can range from negative to positive infinity, depending on the input and model's parameters.
2. **Role in the Model**:
    
    - In many neural network applications, especially those involving classification, the logits are passed through a softmax function to convert them into probabilities. The softmax function normalizes the logits in a way that the output values are between 0 and 1 and sum up to 1, making them interpretable as probabilities for each class.