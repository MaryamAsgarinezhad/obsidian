
A **decoder-only Transformer**, like GPT, applies **causal self-attention** to tokens in order to generate the next token in a sequence. Here’s a step-by-step breakdown of how this works:

### **1. Input Token Embeddings**

- Given a sequence of tokens {x1,x2,...,xt}, each token is converted into an embedding vector using a learned embedding matrix.
    

### **2. Positional Encoding**

- Since transformers don’t have an inherent notion of order, positional encodings (sinusoidal or learned) are added to the token embeddings.
    

### **3. Multi-Head Self-Attention (Causal)**

- The model applies **masked self-attention** so that each token can only attend to itself and previous tokens (not future ones).
    
- This is achieved by multiplying the attention scores with a **triangular mask**, which sets future token scores to −∞, preventing information leakage.
    

#### **Attention Mechanism**

Each token’s embedding is projected into three different matrices:

![[Pasted image 20250324195208.png]]

where:

- Q (Query) represents the current token trying to find relevant context.
    
- K (Key) represents all past tokens.
    
- V (Value) contains the information to be aggregated.
    

The attention scores are computed as:

![[Pasted image 20250324195315.png]]

where:

- M is the causal mask (upper triangular, setting future positions to −∞).
    
- d_k​ is the scaling factor.
    

### **4. Feedforward Network (MLP)**

- The output from attention is passed through a **position-wise feedforward network (FFN)**, typically consisting of two linear layers with a non-linearity (ReLU or GELU).
    

### **5. Residual Connections & Layer Normalization**

- Each attention and FFN block is wrapped with **residual connections** and **layer normalization**.
    

### **6. Token Prediction**

- The final hidden state is projected onto the vocabulary space using a linear layer and softmax:
    ![[Pasted image 20250324195358.png]]

where WOW_OWO​ maps hidden states to vocabulary logits.

### **7. Sampling the Next Token**

- The model samples the next token using **argmax**, **top-k**, **top-p**, or another decoding strategy.
    
- The predicted token is appended to the sequence, and the process repeats.
    

This way, the model generates text autoregressively, predicting one token at a time while ensuring it only attends to past tokens.

---

![[Pasted image 20250324195455.png]]

![[Pasted image 20250324195510.png]]

![[Pasted image 20250324195532.png]]

decoding of GPT vs Transformer:

![[Pasted image 20240525153646.png]]
![[Pasted image 20240525153904.png]]

-Supervised or unsupervised training of the decoder depends on the task. For example translation/text generation is well suited with supervised/unsupervised approaches.
