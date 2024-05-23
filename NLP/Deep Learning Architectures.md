
The Autoregressive Model, or AR model for short, relies only on past period values to predict current ones. It's a linear model, where current period values are a sum of past outcomes multiplied by a numeric factor.

Autoregressive modeling is an important component of large language models (LLMs).

ChatGPT is an autoregressive model due to which it only considers the left context while making predictions.

CONSIDER THIS IN WRITING PROMPTS

------------------------------------------

The error message is indicating that the model.generate() method is being called on an instance of BertModel, which does not have a language generation head. This means it cannot be used for generating text as you are attempting to do. You need to use a model that supports text generation, such as BertLMHeadModel, GPT-2, GPT-3, etc. Make sure you are using a model that has a language modeling head.

BERT-based models, including BertLMHeadModel, are generally limited to input sequences of 512 tokens due to the model's architecture. The error indicates that the tokenized input length is exceeding this limit. This limitation means that any sequence you pass to the model must be trimmed or segmented to fit within this constraint.

-----------------------------------------

**Attention** helps the large language models understand the whole context of the text, instead of just few words.

- Embeddings are the bridge that transform the words into numbers, and are a very important part of the language models.
  The more work is done on embeddings, the better the language model become.
-  In reality, each token (word, char, phrase, sentence,...) is mapped to a large dimentional vector like this: 
  ![[Pasted image 20240523204346.png]]

- Each column, represents a property or feature of that token(color, size, ...)
- Embeddings sometimes confuse to know the meaning of some words and cannot categorize them:
  ![[Pasted image 20240523204834.png]]

- Attention uses the context of a sentence in order to help the embedding solve these ambiguities.
  ![[Pasted image 20240523205115.png]]
  How does transformers use the context to set the weight for an ambiguous word? **==All words pull each other.==** For each word in the context, we check how close is its meaning to the targeted word, and the closest word in the context determines the meaning of ambiguous word.
   - ![[Pasted image 20240523214227.png]] 
**General Attention**:

- **Purpose**: The attention mechanism was originally designed to improve sequence-to-sequence models, which are used in tasks like machine translation. The main idea is to allow the model to focus on different parts of the input sequence while generating each word of the output sequence. This mimics how humans pay attention to different words when understanding or translating a sentence.
- **How it Works**: In a basic attention model, when generating each word of the output, the model can look at the entire input sequence and decide which parts are most relevant. This is achieved through a set of scores (or weights) that indicate the importance of each input word for the current output word. These scores are used to create a weighted sum of the input features, which then informs the generation of the output.

**Key Characteristics**:

- Often used in conjunction with recurrent neural networks (RNNs).
- **==Helps the model to "remember" important information throughout long input sequences, mitigating the vanishing gradient problem typical in standard RNNs.==**

------------------------------------

**Self-Attention**:

- **Purpose**: Self-attention, a variant of the attention mechanism, is a more flexible method primarily used in transformer models. Unlike traditional attention that requires separate components for different parts of the model (such as encoder and decoder in sequence-to-sequence models), self-attention allows the model to relate different positions of a single sequence in order to compute a representation of the sequence.
- **How it Works**: Self-attention computes the attention scores by comparing each token in the sequence with every other token. It calculates three vectors for each token: Query, Key, and Value. The attention scores between tokens are computed by taking the dot product of the Query vector of one token with the Key vector of all other tokens. These scores determine how much focus (or attention) each token should get from other tokens, making it possible for the model to dynamically prioritize which parts of the input are most relevant.

**Key Characteristics**:

- Does not inherently depend on sequence alignment, making it highly parallelizable and efficient.
- Can handle long-range dependencies with lower computational complexity compared to RNN-based models.
- Forms the backbone of transformer architectures, which leverage multiple layers of self-attention to process text.

![[Pasted image 20240523232206.png]]

---------------------------------------

**Multi-head attention:**

Instead of one embadding, we can have multiple embeddings with different scores:

![[Pasted image 20240523215000.png]]

How do we modify the existing embedding to create our desired one?
![[Pasted image 20240523215444.png]]![[Pasted image 20240523215607.png]]
We do the linear transformation, assign a score to each embedding and combine them all together to get the desired embedding.
![[Pasted image 20240523215914.png]]

Here we have multiple (k,q,v) pairs and there is ==a matrix that learns which one is good or bad (gives it a score)== and then we can combine the embeddings linearly together to get a perfect final embedding.

-------------------------------------

**Similarity between words:**

1- Dot product

- a words embedding representation, contains columns that are means of the features for that word and wights assigned to that column.
  So if the dot product of two words representation are close, then they are similar.

2- Cosine similarity
- ![[Pasted image 20240523221713.png]]


Tip: if the vectors are normal, then the cosine and dot product similarities are the same.
- ![[Pasted image 20240523221916.png]]

3-  Scaled dot product : When you have very long vectors and you need to manage the metric to not to be too large, you devide the dot product by the second root of the length of the vectors (number of features)
- ![[Pasted image 20240523222319.png]]
 
==We use the table of similarity (created form computing the similarities between each two word) to move the words around, and that's the attention step:==

![[Pasted image 20240523223234.png]]
- Here we have took each of the words, and send them to a combination of all of the words.
  We also want the sum of each line's coefficients to be 1 and we **normalize** them using a **softmax** function (e^x)

- ==Each line is actually an attention== step, by which the coordinates of a word's embedding representation deviates.
-----------------------------------------------------

**The key, query, and value matrices as linear transformations:**

- Keys and queries work together to make a linear transformation to alter the primary embedding into the later embedding, and then we compute the similarity of the two words in the later embedding.
- ==Suppose that key and query transform the embedding into the one that is better suited for this attention problem.==
- 
  ![[Pasted image 20240523230432.png]]
  - Values matrix takes the left embedding and multiplies by it to make the embedding on the right:
    ![[Pasted image 20240523231235.png]]

- The left embedding knows the features of the words, and the right one knows when two words could appear in the same context.

This is how the key, query and value gives you the best embeddings to apply attention in.
![[Pasted image 20240523231816.png]]

-------------------------------------------

A whole transformer architecture:
![[Pasted image 20240523235139.png]]

- Text predictors may use n-grams while predicting the next word, which will only create texts that only the n-adjacent words of it make sense, not longer ones. They may not necssarily use a neural network.

**Neural network overview (a single perceptron):**
![[Pasted image 20240524000719.png]]
![[Pasted image 20240524000750.png]]

Make it more complex (neural network): 
In real applications, we may not recognize what is each neuron for!
![[Pasted image 20240524001130.png]]

- ==A text generation neural network is a classifier that its output domain is all possible words!==
  ![[Pasted image 20240524001649.png]]

--------------------------------------

How do we create embeddings? Using neural networks, in which each layer understands deeper and deeper properties of a word (==word2vec==):
![[Pasted image 20240524002604.png]]

-----------------------------------------

**Positional encoding**: it takes care of the orders of words. Its a preprocessing step that feed the write ordered sentences to the transformer. It actually deviated the words in the embedding to have a write order.

We get different embeddings for these two sentences:
![[Pasted image 20240524004624.png]]

==Now, the neural network will take more care of  learning this order more, and it works well.==

The thing is, you have to add the same arrows to each sentence.

-----------------------------------------------

This is an architecture that is trained to take the position and context into account and this is why it's so powerful:
![[Pasted image 20240524012559.png]] 
- A transformer model is composed of a stack of identical layers, each containing two main sub-layers: a multi-head self-attention mechanism and a position-wise fully connected feed-forward network. Normalization and residual connections are used around each of these sub-layers.

- **Unlike RNNs that require sequential data processing, transformers process all tokens in the input simultaneously**. This parallel processing capability significantly speeds up training as it allows for better utilization of modern GPUs and TPUs.

-------------------------------------------

Popular transformer based models:

- **BERT (Bidirectional Encoder Representations from Transformers)**: Designed to pre-train deep bidirectional representations by conditioning on both left and right context in all layers. As a result, the pre-trained BERT model can be fine-tuned with just one additional output layer to create state-of-the-art models for a wide range of tasks.
- **GPT (Generative Pre-trained Transformer)**: Uses a left-to-right architecture and can generate coherent and diverse text based on a given context.
- **T5 (Text-To-Text Transfer Transformer)**: Converts all NLP problems into a unified text-to-text format, where the task is to convert one type of text into another.
- **RoBERTa (Robustly Optimized BERT Approach)**: A variant of BERT optimized with more data and training iterations, showing improved performance.



