LLM:

![[Pasted image 20241222172702.png]]
![[Pasted image 20241222172726.png]]

![[Pasted image 20241222172740.png]]

From a language model we can create an auto regressive language model. Then we can generate using autoregressive model, plus a temperature term for randomness.

![[Pasted image 20241222172807.png]]

- A prompt is the conditional part of a sequence that generation starts form.
    

![[Pasted image 20241222172831.png]]
![[Pasted image 20250323003915.png]]

Here introduces a method to compute the language model ( p(w1,w2,…) ). Problems:

- It cannot generate anything it hasn't seen before.
    
- It is more suitable to be used for correcting other kind of tasks (speech recognition, …)
    

![[Pasted image 20241222172851.png]]

For classification:
![[Pasted image 20250323010240.png]]
![[Pasted image 20250323011344.png]]

For Language modeling:

- Here input is n-1 vector of words, and the output is a vector that shows the probability of all words to be the next word.
    
- The size of vectors is n, which is the size of our dictionary (computationally inefficient)
    
- A simple feed forward architecture
    
- A neural network is capable of **==generalizing==** because it maps **one hop** inputs into feature space, and thus can extract meanings (despite N-gram)
    
- N-gram attributes zero probability to a sentence it have never seen, but NN doesn’t.
    
- Still if the prompt is wrong and meaningless (lacks a word), the results will be ruined.
    
- Why? Because it is a feed-forward network, thus the order of words is important and same words are not treated the same in different positions in the input.
    
- Its flow is uni-directional, meaning that the information in the model flows in only one direction—forward—from the input nodes, through the hidden nodes (if any) and to the output nodes, without any cycles or loops (in contrast to recurrent neural networks, which have a bi-directional flow). Modern feedforward networks are trained using backpropagation, and are colloquially referred to as "vanilla" neural networks.
    
- **Lack of Sequential Understanding**: ==No relationship between words==. A FFNN treats each input as an independent feature vector, without any inherent understanding of the sequence or order of the inputs. When you change the order of the words, the network doesn’t have any mechanism to capture the new relationship between them. This can make it difficult to maintain meaning, especially in tasks like natural language processing, where word order significantly impacts the meaning of a sentence.
  
- The main problem is that it ==does not consider large contexts== because it has a fix sized context window.
  ![[Pasted image 20250323010922.png]]


![[Pasted image 20241222172926.png]]

RNNs: Summerizes whatever-length context into a specified-size vector.

- Weight and parameters do not change through processing, because it is recurrent.
    

An **RNN (Recurrent Neural Network)** summarizes previous context through its **hidden state**, which acts as a kind of "memory" for the network. Here's a more detailed breakdown of how this works:

- **Sequential Processing**: Unlike a feed-forward network, ==an RNN processes inputs sequentially (not all at once).== When the RNN receives an input (like a word in a sentence), it not only uses the current input but also the hidden state from the previous time step.
    
- **Hidden State**: The hidden state, often denoted as ht​, is a vector that stores information about the context from all previous inputs. After processing the input at time step t, the hidden state is updated based on:
    
    - The current input xt​.
        
    - The previous hidden state ht−1​.
        
- Concatenated input [xt​,ht−1​] is passed through a linear transformation (a matrix multiplication), which applies weights to both the current input (W_e) and the previous hidden state (W_h) . This step mixes information from both xt​ and ht−1 to form an intermediate signal.
    
- The intermediate signal​ is then passed through a nonlinear activation function (f) , like the **tanh** or **ReLU**, to introduce nonlinearity into the model. This ensures that the model can learn complex patterns and relationships in the data.
    
- Soft-max is used at the last layer for classification(like what logistic regression did), ReLU used between layers (well defined gradient and suitable for back propagation)
    
- The Output Layer is the final component of the RNN Language Model, responsible for producing the probability distribution over the vocabulary. It takes the information from the hidden state and generates a probability distribution, allowing the model to predict the likelihood of the next word in the sequence.
  
![[Pasted image 20250323191556.png]]

**Challenges:**

- **Vanishing/Exploding Gradient Problem**: As sequences get longer, the hidden state may =="forget"== earlier parts of the sequence, especially when using vanilla RNNs. This is because the gradients during backpropagation can either vanish (making it hard to learn long-term dependencies) or explode (causing instability).
    
- **Long-Term Dependencies**: Simple RNNs struggle with learning long-term dependencies because they are limited in how much context they can store in the hidden state. This issue is addressed by more advanced architectures like **LSTMs (Long Short-Term Memory)** and **==GRUs== (Gated Recurrent Units)**, which have mechanisms to better retain information over long sequences.
    
- In theory, RNNs are absolutely capable of handling such “long-term dependencies.” A human could carefully pick parameters for them to solve toy problems of this form. Sadly, in practice, RNNs don’t seem to be able to learn them. The problem was explored in depth by Hochreiter (1991) [German] and Bengio, et al. (1994), who found some pretty fundamental reasons why it might be difficult.
-------------------

**What is LSTM?**

**LSTM** stands for **Long Short-Term Memory**, and it is a type of **Recurrent Neural Network (RNN)** designed to address some of the limitations of traditional RNNs, especially when it comes to learning long-range dependencies in sequences.

While RNNs are capable of processing sequential data (like text or time series), they suffer from a major issue known as the **vanishing gradient problem**, which makes it hard for them to learn long-term dependencies. **LSTMs** were specifically designed to overcome this problem.

LSTMs are explicitly designed to avoid the long-term dependency problem. Remembering information for long periods of time is practically their default behavior, not something they struggle to learn!

![[Pasted image 20241222173033.png]]

**How LSTM Works:**

LSTMs are a type of RNN that introduces a more sophisticated memory cell mechanism. An LSTM unit ==consists of several components that help it **control** what information is stored, updated, and discarded==, which is why they are more effective than regular RNNs for long-term sequence learning.

An LSTM unit has the following key components:

1. **Cell State (c_t)**:
    
    - This is the long-term memory of the LSTM, which carries relevant information throughout the sequence.
        
    - It is updated at each time step by **adding new information and removing irrelevant data**, helping to avoid the vanishing gradient problem.
        
    - ![[Pasted image 20241222173111.png]]
        
2. **Hidden State (h_t​)**:
    
    - This is the short-term memory of the LSTM, which carries the output of the LSTM unit at each time step.
        
    - It is passed on to the ==next LSTM unit== (to be used for generating next hidden state) or the ==output layer== (for generating the output).
        
3. **Gates**: LSTM uses gates to control the flow of information. These gates decide what information should be:
    
    - **Remembered** (kept in memory),
        
    - **Forgotten** (discarded), or
        
    - **Newly added**.
        

These gates are:

- **Forget Gate** (f_t​): Decides which information from the cell state should be discarded. It looks at ht−1 and xt, and outputs a number between 0 and 1 for each number in the cell state Ct−1. A 1 represents “completely keep this” while a 0 represents “completely get rid of this.” (In such a problem, the cell state might include the gender of the present subject, so that the correct pronouns can be used. When we see a new subject, we want to forget the gender of the old subject.) It performs this b learning W_f.
    
- ![[Pasted image 20241222173134.png]]
    
- **Input Gate** (i_t​): Decides which values from the input should be stored in the cell state. The next step is to decide what **new information we’re going to store in the cell state**. This has two parts. First, a sigmoid layer called the “input gate layer” decides ==which values== we’ll update (by learning W_i). Next, a tanh layer creates a vector of ==new candidate values==, C~t, that could be added to the state (By learning W_C). In the next step, we’ll combine these two to create an update to the state. (In the example of our language model, we’d want to add the gender of the new subject to the cell state, to replace the old one we’re forgetting.)
    
- ![[Pasted image 20241222173149.png]]
    
- ![[Pasted image 20241222173211.png]]
    
- So. Long term information is not the whole information cumulated through the process, but it is pruned and grom at each step.
    
- **Output Gate** (o_t​): ==**Decides what the hidden state should be**==, which is passed to the **next LSTM unit and output layer**. Finally, we need to decide what we’re going to output. This output will be based on our cell state, but will be a filtered version. First, we run a sigmoid layer which decides what parts of the cell state we’re going to output. Then, we put the cell state through tanh (to push the values to be between −1 and 1) and multiply it by the output of the sigmoid gate, so that we only output the parts we decided to. ) For the language model example, since it just saw a subject, it might want to output information relevant to a verb, in case that’s what is coming next. For example, it might output whether the subject is singular or plural, so that we know what form a verb should be conjugated into if that’s what follows next.
    
- ![[Pasted image 20241222173229.png]]
    
- All descisions about keeping or letting go of a a part of information is done by sigmoid(ht-1,xt)
----------------------

![[Pasted image 20241222173301.png]]

RNN has the problem of complexity because it computes hidden state n times and each hidden state depends on the previous hidden state. So transformers introduced. (picture important)

- Shared weights
    
- ==Self-attention mechanism + FFNN==
    
- All inputs processed at the same time and their embeddings created.
--------------------

**Self-attention:**

- **1. Self-Attention Output (Contextualized Representations):**
    

**1. Input Sequence**

Let's say the input sequence so far is:

- **"The sun set behind the mountains"**
    

The Transformer will generate the next token based on this input.

**2. Token Embedding**

Each word in the sequence is converted into a **token embedding**. Token embeddings are dense vectors representing each word, typically learned during training.

Example:

![[Pasted image 20241222173322.png]]
These embeddings are then passed into the Transformer model.

**3. Positional Encoding**

Since the Transformer doesn't have an inherent sense of word order (like RNNs), **positional encodings** are added to each token embedding. Positional encodings give the model information about the relative position of words in the sequence.

The final input to the model is the sum of the token embeddings and positional encodings:

![[Pasted image 20241222173354.png]] 

**4. Query (Q), Key (K), and Value (V) Calculation**

For each token in the sequence, the Transformer computes three vectors: **Query (Q)**, **Key (K)**, and **Value (V)**. These vectors are derived by multiplying the input embeddings (which are augmented with positional encodings) by three learned weight matrices W_Q​, W_K​, and W_V​ respectively.

- ![[Pasted image 20241222173419.png]]
- W_Q, W_K, W_V​ are the weight matrices learned during training for the Query, Key, and Value, respectively.
    
- These weight matrices are shared across all tokens in the sequence, but the embeddings E_token​ differ for each token.

The result of these operations is a set of **Q**, **K**, and **V** vectors for each token in the sequence:

![[Pasted image 20241222173444.png]]

**5. Self-Attention Scores (Dot Product)**

For each token (for example, "sun"), we compute the **self-attention scores** with respect to all other tokens in the sequence using the **==Query of the current token and the Keys of all tokens.==**

The **self-attention score** between token i and token j is calculated as the **dot product** between the Query of token i and the Key of token j:

![[Pasted image 20241222173503.png]]

This gives a measure of how much token i "**attends**" to token j when generating the next token.

**6. Scaling the Scores**

The dot products can grow large, so we **scale** them by dividing by the square root of the dimension of the key vector d_k​ (usually the size of the embedding vector). This scaling helps stabilize training.
![[Pasted image 20241222173523.png]]

**7. Softmax to Get Attention Weights**

After scaling the scores, we apply the **softmax** function to the scores to convert them into a probability distribution. This gives the **attention weights** that determine how much each token should "pay attention" to all the other tokens in the sequence.

![[Pasted image 20241222173543.png]]
This results in a set of attention weights αij​ for token i, which tells the model how much token i should focus on each token j when predicting the next token.

**8. Weighted Sum of Value Vectors**

The next step is to compute the **weighted sum** of the Value vectors based on the attention weights. For each token i, the output of the self-attention layer is a weighted sum of all Value vectors V_j​, where the weights are the attention scores αij:

![[Pasted image 20241222173601.png]]

This output represents a new context-aware embedding for each token in the sequence, where each token has attended to other tokens in the sequence according to the attention weights.

**2. Feedforward Network (FFN):**

Each of the contextualized embeddings is passed through a **Feedforward Neural Network (FFN)**, which consists of two layers of linear transformations with a nonlinear activation function (like ReLU) in between. This step further processes the token's representation and allows for **more complex transformations**. The output from this FFN becomes the updated representation of each token.

After this step, each token has an updated, context-aware representation.

**3. Linear Transformation (Projection to Vocabulary Space):**

Now that we have the updated representation for each token, we need to convert these representations into actual tokens (words or subwords). To do this:

- The output of the final layer is passed through a **linear transformation** (**a dense layer**), which projects the token's embedding into a space the size of the vocabulary. This is a simple matrix multiplication, where the weights correspond to the learned representations of each word in the vocabulary.

**4. Softmax Function:**

The logits zt​ are then passed through a **softmax function**, which converts these raw scores into probabilities. The softmax ensures that the output is a valid probability distribution over the vocabulary

**5. Token Prediction:**

The model then selects the next token based on this probability distribution. Usually, this is done by either:

- **Greedy decoding**: Choosing the word with the highest probability.
    
- **Sampling**: Randomly selecting a word according to the probabilities.
    
- **Top-k sampling** or **top-p (nucleus) sampling**: Selecting from the top-k most probable words or sampling from the most probable words that cumulatively sum to a threshold probability.

![[Pasted image 20241222173626.png]]

------------------------

in the context of transformers and attention mechanism, what parameter is learned by the model?

![[Pasted image 20241231181211.png]]

==These weight matrices are shared across all tokens in the sequence, but the embeddings X differ for each token.==

![[Pasted image 20241231182034.png]]

![[Pasted image 20241231182044.png]]

![[Pasted image 20241231182240.png]]

![[Pasted image 20241231182327.png]]

![[Pasted image 20241231182420.png]]

---

What factors caused the creation of LLMs?

![[Pasted image 20241216185046.png]]

------------------

### Encoder-Decoder vs Decoder-Only Models: Training and Differences

In ==**sequence-to-sequence models**==, there are two main architectures: **Encoder-Decoder** and **Decoder-only** models. Both are used for tasks where there is an input sequence (like a sentence) and an output sequence (like a translated sentence). However, they are structured and trained differently based on their tasks.

### **1. Encoder-Decoder Models**

Encoder-Decoder models, like the ==**Transformer**== (which consists of both an encoder and a decoder), are primarily used for **sequence-to-sequence tasks**, such as ==**machine translation**, **summarization**, or **speech recognition**.==

-------------------

Tip: In text generation, almost always we are looking for estimating the distribution from which the sentence is generated from (**maximum likelihood**)

- **Maximum likelihood hypothesis is used in defining the loss formula of all these networks.**

![[Pasted image 20241216195851.png]]

- In autoregressive approaches for text generation, we are using teacher forcing that forces an ideal target text while training. But at testing time we may face problems because there is no teacher and the previous token that model generated may not be accurate and this will cause a ==distribution shift==.

-----------

![[Pasted image 20241216200354.png]]

Prompting idea(==for pretrained models==) was observed after gpt3 model introduced.
It was just observed not invented, that giving a prompt to language models can **make a pre-trained model to a task-speific model** :

![[Pasted image 20241216200848.png]]

------------------

Emergence:

![[Pasted image 20241216201213.png]]
Large data improves results. Since they can absorb more knowledge for reasoning.

**How much data we need for a specific parameter size?**
![[Pasted image 20241216201413.png]]

----------------

Fine-tunning differs for different-sized models:
![[Pasted image 20241216201534.png]]
![[Pasted image 20241216201643.png]]
Adaptors are layers added to pre-trained model to adapt our task-specific changes.

------------------

![[Pasted image 20241216202002.png]]

![[Pasted image 20241216202013.png]]

---------------------

Data integrity and unsupervised learning acts a more important role in NLP than transformer and hardware emersion.

Good luck.