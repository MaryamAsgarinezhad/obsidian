
![[Pasted image 20241222165609.png]]
With this definition, many things in the nature are considered as a language, like protein structures.

Why nature's elements(alphabet) are finite? otherwise, there will be no structure to be repeated to make a discipline to create predictivity.

-------------------

![[Pasted image 20241222170700.png]]

Again the definition of language modeling.

![[Pasted image 20241222170857.png]]

![[Pasted image 20241222183310.png]]

![[Pasted image 20241222183350.png]]

Its a kind of fixed embedding. In this method of representation learning, we build the representation of a word based on its surrounding cords and context.

Problems:
- What if different words have the same context?
- What if the same word has many meanings?

Pros:
- Have a global representation of a word even with several meanings.

---------------

![[Pasted image 20241222184215.png]]

- Its a bidirectional embedding model.
- ELMo (embeddings from language model) is **a word embedding method for representing a sequence of words as a corresponding sequence of vectors**. (so each word has an embedding based on the sentence it is in.)
- Embeddings from Language Models, or ELMo, is a type of deep contextualized word representation that models both (1) complex characteristics of word use (e.g., syntax and semantics), and (2) how these uses vary across linguistic contexts (i.e., to model polysemy).

-------------------

![[Pasted image 20241222184622.png]]
![[Pasted image 20241222191400.png]]

- The word we want to make an embedding for is called ==query==. 
- All the context words and the word itself are called ==keys==.
- The contribution of each key to the query (similarity between them) are weights
- ==values== are the actual vectors being weight summed together.
- **If key equals the value, then it is called ==self-attention==.**
- On the other hand we have multi-head attention:
  ![[Pasted image 20241222192321.png]]

**Tip: query and keys have to be in the same space to be dot produced.**

![[Pasted image 20241230183038.png]]

Can we do this in **parallel**?

![[Pasted image 20241222192411.png]]
![[Pasted image 20241222192452.png]]
Multi-Head Attention is an extension of the basic attention mechanism, used primarily in transformer models like BERT and GPT. It improves the model's ability to capture different types of relationships within the data. The key differences are:

1. **Multiple attention heads**:
    
    - Instead of a single attention calculation, the input is split into multiple **attention heads**.
    - Each head calculates its own set of Q, K, and V matrices and performs its own attention computation independently.
2. **Diverse focus**:
    
    - Different heads can learn to focus on different parts of the input data or different types of relationships (e.g., syntactic vs. semantic connections).
    - This parallel computation enables the model to capture more nuanced and complex interactions.
3. **Dimensionality reduction**:
    
    - After each head computes its output, the **results are concatenated** and passed through a linear layer to combine the information into a single representation.

---------

![[Pasted image 20241230182005.png]]

---------

since attention is applied to all surrounding words without order, how can we do positional encodeing?

- **We need to add a ==position bias matrix== to the attention matrix** 

![[Pasted image 20241230183603.png]]

![[Pasted image 20241230183714.png]]

---------

![[Pasted image 20241230184005.png]]

- Absolute position in a sentence or relative distance between words

Usage:

![[Pasted image 20241230184348.png]]

- Absolute for classification - O(n^2)
- Relative for span prediction - O(2n-1)
- Why such complexity? In absolute case combined position of n numbers, in relative case there are 2n-1 cases of distance between words.

----------

How to apply positional embedding into model?

- Sum input and positional embeddings
  
![[Pasted image 20241230185333.png]]

- Or use positional embedding to adjust input embedding matrix

![[Pasted image 20241230185746.png]]

------------

![[Pasted image 20241230185952.png]]

positional embedding must be reproduced from a shifted one:

![[Pasted image 20241230190310.png]]

----------

A transformer (encoder part) block architecture:

![[Pasted image 20241230190544.png]]

-------------

Layer Normalization (vs batch normalization):

![[Pasted image 20241230191008.png]]

---------

![[Pasted image 20241230191341.png]]

-------------

**BERT** (Bidirectional Encoder Representations from Transformers) is widely used for **representation learning**. It is a key model in the family of attention-based models, particularly transformers, and its role can be understood in two primary contexts:

### **1. BERT in Representation Learning**

BERT is designed to generate high-quality ==contextualized embeddings== of words or sentences, which are essentially dense vector representations capturing their meaning based on context. Key aspects of BERT's role in representation learning include:

- **Bidirectional Contextual Understanding**: Unlike unidirectional models (==mainly decoder only models like GPT==), BERT uses a **==bidirectional attention==** mechanism, allowing it to consider both left and right contexts simultaneously. This makes its representations particularly rich and nuanced.
    
- **Pretrained Representations**: BERT is pretrained on massive corpora using tasks like:
    
    - **Masked Language Modeling (MLM)**: Predicting randomly masked words in a sentence, forcing the model to learn contextual dependencies.
    - **Next Sentence Prediction (NSP)**: Determining whether two sentences are logically sequential, capturing sentence-level relationships.
- **Transfer Learning**: BERT representations can be fine-tuned for specific downstream tasks (e.g., classification, question answering, etc.) or used directly as feature inputs for other models.

### **2. Role in Attention-Based Models**

BERT is itself an attention-based model built on the transformer architecture, and it demonstrates how **self-attention** mechanisms enhance representation learning. Its role includes:

- **Self-Attention Mechanism**: BERT uses the self-attention mechanism to compute weighted representations of input tokens, emphasizing words that are contextually important relative to one another. This is central to learning semantic and syntactic relationships.
    
- **Encoder-Only Transformer**: Unlike models that include both encoder and decoder (e.g., the original transformer architecture used in translation tasks), BERT focuses solely on the encoder, making it specialized for understanding and representing text rather than generating it.
    
- **Capturing Long-Range Dependencies**: Attention mechanisms allow BERT to effectively capture dependencies between distant words in a sequence, which traditional models like RNNs struggle with.
    

------------

BERT uses the **attention mechanism** in both the **pretraining** and **fine-tuning** processes. Here's how the attention mechanism is employed in each phase:

- **Pretraining and fine-tuning a Bert better optimizes its W_Q, W_K, W_V and other learned parameters.**
### **1. Pretraining: Unsupervised/Self-Supervised Learning**

During pretraining, BERT uses the **attention mechanism** extensively as part of its underlying transformer architecture. Key roles include:

- **Masked Language Modeling (MLM)**:
    
    - When predicting a masked token, BERT uses the self-attention mechanism to analyze the relationships between all tokens in the input sequence, considering both preceding and succeeding words in a bidirectional manner.
    - This enables BERT to understand the context in which a word appears, essential for learning rich, contextualized representations.
- **Next Sentence Prediction (NSP)**:
    
    - Self-attention helps BERT understand the relationship between two sentences by analyzing how tokens in one sentence relate to tokens in the other. This helps BERT model ==sentence-level coherence.==


### **2. Fine-Tuning: Supervised Learning**

In fine-tuning, the **attention mechanism** is again employed to process the input data and produce task-specific outputs. The self-attention layers remain functional and critical to the model's performance:

- The pretrained self-attention layers are reused, providing the same ability to model dependencies between tokens or phrases in the input.
- For tasks like classification or question answering, task-specific layers (e.g., a classification head) are added on top of BERT, but the core attention mechanism is still active for feature extraction.

### **Summary**

The attention mechanism is integral to both stages of BERT's lifecycle:

- In **pretraining**, it helps BERT learn general language representations by modeling token-to-token and sentence-to-sentence relationships.
- In **fine-tuning**, it ensures the learned representations are applied effectively to specific tasks, maintaining its ability to capture contextual dependencies.

---------------

BERT's **pretraining process** is often referred to as **unsupervised** (or more accurately, **self-supervised**) because it doesn't require ==manually== labeled data. Instead, it generates labels directly from the input data itself. 

a. The "label" for **Masked Language Modeling (MLM)** task is the original token that was masked. 

#### b. **Next Sentence Prediction (NSP)**:

- **Process**:
    
    - Given a pair of sentences, the model predicts whether the second sentence follows the first one in the original text.
    - Positive examples (sequential sentences) and negative examples (random sentences) are generated automatically from the corpus.
- **Optimization**:
    
    - The "label" is whether the second sentence is a logical continuation of the first (binary: yes or no).
    - The model optimizes a classification loss (e.g., binary cross-entropy) for this task.


### **Purpose of Fine-Tuning**

While BERT’s **pretraining** teaches the model general language understanding, **fine-tuning** specializes it for a specific task (e.g., sentiment analysis, question answering, or named entity recognition). To do this, fine-tuning requires a **labeled dataset** where:

- The input examples have corresponding **ground truth labels** (e.g., class labels, correct answers, or annotations).
- These labels provide explicit supervision, guiding the model on what output is correct for each input.

--------------

**Deep dive into BERT:**

![[Pasted image 20250105193326.png]]

### **Does Attention Generate Token and Segment Embeddings?**

❌ **No, attention is NOT used to generate initial token and segment embeddings.**  
The **token embeddings, segment embeddings, and position embeddings** are **learned lookup-based embeddings** and are **not generated through an attention mechanism**. Instead, they are simple **trainable embedding layers**.

### **How Are Initial Token and Segment Embeddings Generated?**

==These embeddings are pre-defined and **learned during BERT's pretraining**.==

### **Where Does Attention Start?**

👉 **Attention is only applied once embeddings are generated and passed into the Transformer layers.**

The processed input embeddings are passed into **multiple Transformer encoder layers** (12 for BERT-Base, 24 for BERT-Large). Each encoder layer consists of two main components:

1. **Multi-Head Self-Attention**
2. **Feed-Forward Network (FFN)**

- The **Multi-Head Self-Attention** mechanism operates **inside the Transformer Encoder**, but it does **not** influence the initial token and segment embeddings.

![[Pasted image 20250106142549.png]]
![[Pasted image 20250106142631.png]]
The role of FFNN in the transformer block is to expand the dimensionality and complexity of representations.

![[Pasted image 20250106142646.png]]
![[Pasted image 20250106142718.png]]
## **6. Stacking Multiple Encoder Layers**

BERT consists of multiple encoder layers (12 in BERT-Base, 24 in BERT-Large), refining token representations **iteratively**.

Each encoder layer outputs a better contextualized representation of each token.

---

## **7. Output Representations**

After the final encoder layer, we obtain a set of hidden states:

![[Pasted image 20250106142804.png]]

Each hi is a vector representing token i, enriched with context from all tokens.

- The **[CLS] token**'s representation is often used for **classification** tasks.
- The representations can be fine-tuned for **Named Entity Recognition (NER), Question Answering, etc.**.

---

### **Pretraining tasks including attention (==both based on maximum likelihood==):**

(A) Masked Language Modeling (MLM):
![[Pasted image 20250106143146.png]]

(B) Next Sentence Prediction (NSP):
![[Pasted image 20250106143603.png]]
and y is the label predicted by BERT.

- Predicted x_i and y in each task come from minimizing the corresponding likelihood shown above. Then by comparing the outputs to true outputs, ==attention matrixes get updated.==
  
- **Both probabilities (P) are computed using fully connected Dense layers which take contextualized representations as input.**
- ![[Pasted image 20250104192923.png]]
  Bert's objective is to minimize both 
- ![[Pasted image 20250106144816.png]]
- ### Important 
  
  ==Every token including mask, attends to all other tokens. It must be proven that if we got optimized W_q,k,v then representation of mask is computed correctly.==
  ![[Pasted image 20250106151959.png]]
- ![[Pasted image 20250106151128.png]]
### **Step 2: Attention-Based Sentence Interaction**

- BERT’s **self-attention mechanism** enables cross-sentence interactions:
    - Words in Sentence A can **attend to words in Sentence B**.
    - Words in Sentence B can **attend to words in Sentence A**.
    - This allows BERT to model **semantic relationships** between sentences.

**Example of Self-Attention in NSP:**

- If Sentence A: `"The dog is playing in the park."`
- And Sentence B: `"It looks very happy."`
- The word `"It"` in Sentence B can **attend** to `"dog"` in Sentence A to understand the reference.

Without **self-attention**, BERT would treat both sentences independently, failing to model relationships.

- ==MASK token and CLS token are used for MLM and NSP respectively.==
- CLS represents the relationships between sentences. Then h_CLS is passed through a fully conncted layer and softmax for NSP task answer.

- ![[Pasted image 20250106151258.png]]
- ![[Pasted image 20250106151323.png]]

Note: 

![[Pasted image 20250106153908.png]]

-------------

Note that:

![[Pasted image 20250106154420.png]]

----------

About the primary encoders layers (before attention):

![[Pasted image 20250106155338.png]]
![[Pasted image 20250106155731.png]]
![[Pasted image 20250106155741.png]]