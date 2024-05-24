
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

 **BERT (Bidirectional Encoder Representations from Transformers)**: 
  BERT is designed to pre-train deep bidirectional representations from unlabeled text by jointly conditioning on both left and right context in all layers. This means that, unlike traditional language models that might only predict each word based on previous words (left-to-right prediction), BERT takes into account the full context of a word—both what comes before and what comes after it.

#### Key Features of BERT:

 - **Bidirectionality**: Achieved through a novel training method called Masked Language Model (MLM). In MLM, some percentage of the input tokens are masked at random, and then the model attempts to predict the masked words based on their context. This enables the model to understand context in both directions.
  - **Next Sentence Prediction (NSP)**: BERT is also trained on a task that involves determining whether a sentence logically follows another sentence, which helps it understand relationships between sentences.
  - **Fine-Tuning**: Once pre-trained, BERT can be fine-tuned with just one additional output layer to create state-of-the-art models for a wide range of tasks, such as question answering, sentiment analysis, and more.

1. **No Decoder**:
    
    - BERT is designed without the decoder because its primary function is not to generate text but to understand and represent it. By using only the encoder, BERT processes input text to create a rich and contextually nuanced representation of each token in the sequence.
2. **Purpose**:
    
    - Since BERT focuses on tasks that require a deep understanding of language context (such as predicting the sentiment of a sentence, answering questions based on a passage, or filling in missing words), it doesn't need the ability to generate sequences from scratch, which is the primary function of the decoder in the original Transformer.
3. **How It Works**:
    
    - ==In practice, BERT takes a sequence of tokens, applies multiple layers of the Transformer encoder to these tokens, and each token is then transformed into vectors that represent not just the token but its relationship with all other tokens in the sequence.== This process allows BERT to understand the full context of a sentence or a passage, bi-directionally (i.e., considering both the previous and next tokens simultaneously).

By dropping the decoder and utilizing only the encoder, BERT is optimized for tasks that involve understanding or classifying text rather than translating or generating it.

**Tip**: While BERT’s architecture is capable of understanding language, it doesn't know how to perform specific tasks (like classifying the sentiment of a review or answering a question based on a passage) out of the box. To equip BERT for a particular task, typically, a new output layer is added. For example:

- For sentiment analysis, you might add a simple classifier layer that predicts positive or negative sentiment.
- For question answering, the layer might predict the start and end positions of the answer within a passage.

---------------------------------------

**GPT (Generative Pre-trained Transformer)**:
  
  **GPT-1**: The original GPT model was designed to demonstrate that pre-training a language model on a diverse corpus of text could significantly improve performance when fine-tuned for specific tasks. This model combined unsupervised pre-training and supervised fine-tuning in a novel way. It was trained using a language modeling objective, which is to predict the next word in a sentence, given all the previous words.
  
  **GPT-2**: Introduced in 2019, GPT-2 expanded on the ideas of GPT-1 with a much larger model architecture and training dataset. GPT-2 was notable for not being fine-tuned on specific tasks in its most famous demonstrations; instead, it performed tasks like translation, question-answering, and summarization in a zero-shot setting, using only prompts to guide its generation. GPT-2 includes 1.5 billion parameters, which allow it to generate remarkably coherent and contextually appropriate text over extended passages.
  
  **GPT-3**: Launched in 2020, GPT-3 further expanded the scale of the model to 175 billion parameters, making it one of the largest and most powerful language models ever created. GPT-3's vast size allows it to perform a wide variety of tasks with little to no task-specific data, often achieving competitive results in a few-shot or even zero-shot settings. Its ability to generate human-like text and solve complex problems through pattern recognition alone has been a subject of both admiration and ethical scrutiny.

### **Key Characteristics of GPT Models**

1. **Architecture**: All GPT models use a transformer architecture based solely on decoder blocks from the original transformer model. This design choice reflects the models' focus on generating text output rather than encoding inputs.
    
2. **Training Approach**:
    
    - **Unsupervised Learning**: GPT models are primarily trained on a language modeling objective in an unsupervised manner. They learn to predict the next word in sequences of text, gaining a broad understanding of language patterns and structures.
    - **Transfer Learning**: After pre-training, GPT models can be fine-tuned (though often demonstrated in settings that do not require fine-tuning) on specific tasks by training on smaller, task-specific datasets.

---------------------------------------

- **T5 (Text-To-Text Transfer Transformer)**: Converts all NLP problems into a unified text-to-text format, where the task is to convert one type of text into another.
- **RoBERTa (Robustly Optimized BERT Approach)**: A variant of BERT optimized with more data and training iterations, showing improved performance.

-----------------------------------------
### Key Concepts of Unsupervised Learning

1. **Discovering Hidden Patterns**: Unsupervised learning is primarily used to identify patterns or intrinsic structures within a dataset. Since there are no output labels or correct answers provided, the algorithms focus on the relationships between the data points themselves.
    
2. **Clustering and Association**: Common tasks within unsupervised learning include clustering, where the aim is to group a set of objects in such a way that objects in the same group (a cluster) are more similar to each other than to those in other groups. Another task is association, which is about discovering rules that describe large portions of the data, such as frequent itemsets in market basket analysis.

### Techniques Used in Unsupervised Learning

- **Clustering**: Methods like K-means, hierarchical clustering, and DBSCAN are used to find and group similar data points based on their features. This can help identify distinct groups within the data without prior knowledge of what these groups might be.
  
- **Dimensionality Reduction**: Techniques such as principal component analysis (PCA) and t-distributed stochastic neighbor embedding (t-SNE) reduce the number of random variables under consideration, by obtaining a set of principal variables. This can help simplify the data without losing critical information, making patterns more apparent.
    
- **Association Rule Learning**: Algorithms like Apriori and Eclat are used in transactional databases to find interesting ==correlations== between items.
    
- **Neural Networks**: Certain neural network models can be applied in unsupervised learning. For example, ==autoencoders==, which are designed to compress data and then reconstruct it, effectively learning detailed features of the data
  
- **Generative Models**: These include Generative Adversarial Networks (GANs) and Variational Autoencoders (VAEs), which learn to generate new data points that are similar to the training data.

### How Do Models Learn Without Supervision?

- ==**Generative Models**:== These models learn the probability distribution or data density of a dataset to generate new data points that have similar characteristics. They adjust their parameters based on how well they can replicate the input data.

----------------------------------

### Understanding Transformer Architecture:

- 1. **Encoder**: Processes the input data (like text) and transforms it into a continuous representation that retains all the information of the input but in a form that the model can manipulate. In tasks like translation, the encoder would handle the source language text.
    
2. **Decoder**: Takes the output of the encoder and generates a sequence of outputs (like translated text). **For each output step, the decoder looks at the entire encoder output and its previous outputs to decide the next step**. It is specifically designed to output sequences, one element at a time.

### GPT's Use of Decoder Blocks:

- **Text Generation Focus**: GPT models are designed to generate text. The decoder architecture is naturally suited for this because it predicts the next token in a sequence given the previous tokens. This is essentially what text generation is about — creating the next piece of text based on what you have so far.
    
- **==Self-Attention in Decoders==**: Even though GPT uses only the decoder, it still employs self-attention mechanisms. In a full Transformer with both encoder and decoder, the decoder's self-attention layers are masked to prevent them from 'seeing' future tokens in the output during training. This is crucial because, during generation, the model should not have access to future parts of the sequence it is generating. This type of attention allows the model to consider all the previous tokens it has generated so far to decide the next token.

### Why No Encoder?

- **Direct Learning from Sequence**: Since GPT’s task is to generate text, the encoder part, which is traditionally used for understanding and encoding a fixed input sequence into a continuous representation, is not required. GPT models **==directly==** learn to predict the next part of the text from the previous parts, leveraging the context provided by those parts through the self-attention mechanism.
    
- **Efficiency and Simplicity**: Removing the encoder simplifies the model architecture and focuses computational resources on generating high-quality text outputs. This design choice reflects a streamlined approach specifically optimized for the needs of language production tasks, rather than the broader requirements of tasks like translation which need both encoding and decoding capabilities.