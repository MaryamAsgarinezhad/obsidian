LLM:

![](file:///tmp/lu1673888a1q8.tmp/lu1673888a1r5_tmp_f38a11455ed515c5.png)

![](file:///tmp/lu1673888a1q8.tmp/lu1673888a1r5_tmp_8174eb74ed5ce75.png)

![](file:///tmp/lu1673888a1q8.tmp/lu1673888a1r5_tmp_a1655dfe51c78139.png)

From a language model we can create an auto regressive language model. Then we can generate using autoregressive model, plus a temperature term for randomness.

![](file:///tmp/lu1673888a1q8.tmp/lu1673888a1r5_tmp_dc5d537d82b830ef.png)

- A prompt is the conditional part of a sequence that generation starts form.
    

![](file:///tmp/lu1673888a1q8.tmp/lu1673888a1r5_tmp_7f94dce543721972.png)

Here introduces a method to compute the language model ( p(w1,w2,…) ). Problems:

- It does not consider the meaning of the sentence (relationship between words)
    
- Since it represents each word as a vector of zeros that equals 1 on only one derray representing that word, the relation between each two word (their inner vector product) is 0 .
    
- It is more suitable to be used for correcting other kind of tasks (speech recognition, …)
    

![](file:///tmp/lu1673888a1q8.tmp/lu1673888a1r5_tmp_68e731e77a99d914.png)

- Here input is n-1 vector of words, and the output is a vector that shows the probability of all words to be the next word.
    
- The size of vectors is n, which is the size of our dictionary (computationally inefficient)
    
- A simple feed forward architecture
    
- A neural network is capable of **generalizing** because it maps **one hop** inputs into feature space, and thus can extract meanings (despite N-gram)
    
- N-gram attributes zero probability to a sentence it have never seen, but NN doesn’t.
    
- Still if the prompt is wrong and meaningless (lacks a word), the results will be ruined.
    
- Why? Because it is a feed-forward network, thus the order of words is important and same words are not treated the same in different positions in the input.
    
- Its flow is uni-directional, meaning that the information in the model flows in only one direction—forward—from the input nodes, through the hidden nodes (if any) and to the output nodes, without any cycles or loops (in contrast to recurrent neural networks, which have a bi-directional flow). Modern feedforward networks are trained using backpropagation, and are colloquially referred to as "vanilla" neural networks.
    
- **Lack of Sequential Understanding**: A FFNN treats each input as an independent feature vector, without any inherent understanding of the sequence or order of the inputs. When you change the order of the words, the network doesn’t have any mechanism to capture the new relationship between them. This can make it difficult to maintain meaning, especially in tasks like natural language processing, where word order significantly impacts the meaning of a sentence.
    
- ![](file:///tmp/lu1673888a1q8.tmp/lu1673888a1r5_tmp_9109a450fff1dd99.png)
    

  
  

RNNs: Summerizes whatever-length context into a specified-size vector.

- Weight and parameters do not change through processing, because it is recurrent.
    

An **RNN (Recurrent Neural Network)** summarizes previous context through its **hidden state**, which acts as a kind of "memory" for the network. Here's a more detailed breakdown of how this works:

- **Sequential Processing**: Unlike a feed-forward network, an RNN processes inputs sequentially. When the RNN receives an input (like a word in a sentence), it not only uses the current input but also the hidden state from the previous time step.
    
- **Hidden State**: The hidden state, often denoted as hth_tht​, is a vector that stores information about the context from all previous inputs. After processing the input at time step ttt, the hidden state is updated based on:
    
    - The current input xtx_txt​.
        
    - The previous hidden state ht−1h_{t-1}ht−1​.
        
- Concatenated input [xt​,ht−1​] is passed through a linear transformation (a matrix multiplication), which applies weights to both the current input and the previous hidden state. This step mixes information from both xtx_txt​ and ht−1h_{t-1}ht−1​ to form an intermediate signal.
    
- The intermediate signal​ is then passed through a nonlinear activation function, like the **tanh** or **ReLU**, to introduce nonlinearity into the model. This ensures that the model can learn complex patterns and relationships in the data.
    
- Soft-max is used at the last layer for classification(like what logistic regression did), ReLU used between layers (well defined gradient and suitable for back propagation)
    

![](file:///tmp/lu1673888a1q8.tmp/lu1673888a1r5_tmp_ac0052cf8bd61333.png)

**Challenges:**

- **Vanishing/Exploding Gradient Problem**: As sequences get longer, the hidden state may "forget" earlier parts of the sequence, especially when using vanilla RNNs. This is because the gradients during backpropagation can either vanish (making it hard to learn long-term dependencies) or explode (causing instability).
    
- **Long-Term Dependencies**: Simple RNNs struggle with learning long-term dependencies because they are limited in how much context they can store in the hidden state. This issue is addressed by more advanced architectures like **LSTMs (Long Short-Term Memory)** and **GRUs (Gated Recurrent Units)**, which have mechanisms to better retain information over long sequences.
    
- In theory, RNNs are absolutely capable of handling such “long-term dependencies.” A human could carefully pick parameters for them to solve toy problems of this form. Sadly, in practice, RNNs don’t seem to be able to learn them. The problem was explored in depth by Hochreiter (1991) [German] and Bengio, et al. (1994), who found some pretty fundamental reasons why it might be difficult.
-------------------

**What is LSTM?**

**LSTM** stands for **Long Short-Term Memory**, and it is a type of **Recurrent Neural Network (RNN)** designed to address some of the limitations of traditional RNNs, especially when it comes to learning long-range dependencies in sequences.

While RNNs are capable of processing sequential data (like text or time series), they suffer from a major issue known as the **vanishing gradient problem**, which makes it hard for them to learn long-term dependencies. **LSTMs** were specifically designed to overcome this problem.

LSTMs are explicitly designed to avoid the long-term dependency problem. Remembering information for long periods of time is practically their default behavior, not something they struggle to learn!

![](file:///tmp/lu1673888a1q8.tmp/lu1673888a1r5_tmp_e269cd0760fe8b25.png)

**How LSTM Works:**

LSTMs are a type of RNN that introduces a more sophisticated memory cell mechanism. An LSTM unit consists of several components that help it control what information is stored, updated, and discarded, which is why they are more effective than regular RNNs for long-term sequence learning.

An LSTM unit has the following key components:

1. **Cell State (ctc_tct​)**:
    
    - This is the long-term memory of the LSTM, which carries relevant information throughout the sequence.
        
    - It is updated at each time step by adding new information and removing irrelevant data, helping to avoid the vanishing gradient problem.
        
    - ![](file:///tmp/lu1673888a1q8.tmp/lu1673888a1r5_tmp_44c1b80df7dea466.png)
        
2. **Hidden State (hth_tht​)**:
    
    - This is the short-term memory of the LSTM, which carries the output of the LSTM unit at each time step.
        
    - It is passed on to the next LSTM unit or the output layer.
        
3. **Gates**: LSTM uses gates to control the flow of information. These gates decide what information should be:
    
    - **Remembered** (kept in memory),
        
    - **Forgotten** (discarded), or
        
    - **Newly added**.
        

These gates are:

- **Forget Gate** (ftf_tft​): Decides which information from the cell state should be discarded. It looks at ht−1ht−1 and xtxt, and outputs a number between 00 and 11 for each number in the cell state Ct−1Ct−1. A 11 represents “completely keep this” while a 00 represents “completely get rid of this.” (In such a problem, the cell state might include the gender of the present subject, so that the correct pronouns can be used. When we see a new subject, we want to forget the gender of the old subject.)
    
- ![](file:///tmp/lu1673888a1q8.tmp/lu1673888a1r5_tmp_fe6455b693abf797.png)
    
- **Input Gate** (iti_tit​): Decides which values from the input should be stored in the cell state. The next step is to decide what new information we’re going to store in the cell state. This has two parts. First, a sigmoid layer called the “input gate layer” decides which values we’ll update. Next, a tanh layer creates a vector of new candidate values, C~tC~t, that could be added to the state. In the next step, we’ll combine these two to create an update to the state. (In the example of our language model, we’d want to add the gender of the new subject to the cell state, to replace the old one we’re forgetting.)
    
- ![](file:///tmp/lu1673888a1q8.tmp/lu1673888a1r5_tmp_84f499b30188bb1e.png)
    
- ![](file:///tmp/lu1673888a1q8.tmp/lu1673888a1r5_tmp_c6fb3b404a146783.png)
    
- So. Long term information is not the whole information cumulated through the process, but it is pruned and grom at each step.
    
- **Output Gate** (oto_tot​): Decides what the hidden state should be, which is passed to the next LSTM unit and output layer. Finally, we need to decide what we’re going to output. This output will be based on our cell state, but will be a filtered version. First, we run a sigmoid layer which decides what parts of the cell state we’re going to output. Then, we put the cell state through tanhtanh (to push the values to be between −1−1 and 11) and multiply it by the output of the sigmoid gate, so that we only output the parts we decided to. ) For the language model example, since it just saw a subject, it might want to output information relevant to a verb, in case that’s what is coming next. For example, it might output whether the subject is singular or plural, so that we know what form a verb should be conjugated into if that’s what follows next.(
    
- ![](file:///tmp/lu1673888a1q8.tmp/lu1673888a1r5_tmp_b272529ec6d0073a.png)
    
- All descisions about keeping or letting go of a a part of information is done by sigmoid(ht-1,xt)
----------------------
![](file:///tmp/lu1673888a1q8.tmp/lu1673888a1r5_tmp_ee99590828172c4f.png)

RNN has the problem of complexity because it computes hidden state n times and each hidden state depends on the previous hidden state. So transformers introduced. (picture important)

- Shared weights
    
- Self-attention mechanism + FFNN
    
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

![](file:///tmp/lu1673888a1q8.tmp/lu1673888a1r5_tmp_4365794fe7789ff9.png)

These embeddings are then passed into the Transformer model.

**3. Positional Encoding**

Since the Transformer doesn't have an inherent sense of word order (like RNNs), **positional encodings** are added to each token embedding. Positional encodings give the model information about the relative position of words in the sequence.

The final input to the model is the sum of the token embeddings and positional encodings:

![](file:///tmp/lu1673888a1q8.tmp/lu1673888a1r5_tmp_e70ae9cea815b458.png) **4. Query (Q), Key (K), and Value (V) Calculation**

For each token in the sequence, the Transformer computes three vectors: **Query (Q)**, **Key (K)**, and **Value (V)**. These vectors are derived by multiplying the input embeddings (which are augmented with positional encodings) by three learned weight matrices WQW_QWQ​, WKW_KWK​, and WVW_VWV​ respectively.

- ![](file:///tmp/lu1673888a1q8.tmp/lu1673888a1r5_tmp_5d4ae678cdf5f28d.png) WQ,WK,WVW_Q, W_K, W_VWQ​,WK​,WV​ are the weight matrices learned during training for the Query, Key, and Value, respectively.
    
- These weight matrices are shared across all tokens in the sequence, but the embeddings EtokenE_{\text{token}}Etoken​ differ for each token.
    

The result of these operations is a set of **Q**, **K**, and **V** vectors for each token in the sequence:

![](file:///tmp/lu1673888a1q8.tmp/lu1673888a1r5_tmp_b579065a2537ba83.png)

**5. Self-Attention Scores (Dot Product)**

For each token (for example, "sun"), we compute the **self-attention scores** with respect to all other tokens in the sequence using the Query of the current token and the Keys of all tokens.

The **self-attention score** between token iii and token jjj is calculated as the **dot product** between the Query of token iii and the Key of token jjj:

![](file:///tmp/lu1673888a1q8.tmp/lu1673888a1r5_tmp_4dcc07fd61a411aa.png)

This gives a measure of how much token iii "attends" to token jjj when generating the next token.

**6. Scaling the Scores**

The dot products can grow large, so we **scale** them by dividing by the square root of the dimension of the key vector dkd_kdk​ (usually the size of the embedding vector). This scaling helps stabilize training.

![](file:///tmp/lu1673888a1q8.tmp/lu1673888a1r5_tmp_ce7ab5d41fa6e617.png)

**7. Softmax to Get Attention Weights**

After scaling the scores, we apply the **softmax** function to the scores to convert them into a probability distribution. This gives the **attention weights** that determine how much each token should "pay attention" to all the other tokens in the sequence.

![](file:///tmp/lu1673888a1q8.tmp/lu1673888a1r5_tmp_ec870bff3534c196.png)

This results in a set of attention weights αij\alpha_{ij}αij​ for token iii, which tells the model how much token iii should focus on each token jjj when predicting the next token.

**8. Weighted Sum of Value Vectors**

The next step is to compute the **weighted sum** of the Value vectors based on the attention weights. For each token iii, the output of the self-attention layer is a weighted sum of all Value vectors VjV_jVj​, where the weights are the attention scores αij\alpha_{ij}αij​:

![](file:///tmp/lu1673888a1q8.tmp/lu1673888a1r5_tmp_fab0df5e5bd725e.png)

This output represents a new context-aware embedding for each token in the sequence, where each token has attended to other tokens in the sequence according to the attention weights.

**2. Feedforward Network (FFN):**

Each of the contextualized embeddings is passed through a **Feedforward Neural Network (FFN)**, which consists of two layers of linear transformations with a nonlinear activation function (like ReLU) in between. This step further processes the token's representation and allows for more complex transformations. The output from this FFN becomes the updated representation of each token.

After this step, each token has an updated, context-aware representation.

**3. Linear Transformation (Projection to Vocabulary Space):**

Now that we have the updated representation for each token, we need to convert these representations into actual tokens (words or subwords). To do this:

- The output of the final layer is passed through a **linear transformation** (a dense layer), which projects the token's embedding into a space the size of the vocabulary. This is a simple matrix multiplication, where the weights correspond to the learned representations of each word in the vocabulary.

**4. Softmax Function:**

The logits zt\mathbf{z}_tzt​ are then passed through a **softmax function**, which converts these raw scores into probabilities. The softmax ensures that the output is a valid probability distribution over the vocabulary

**5. Token Prediction:**

The model then selects the next token based on this probability distribution. Usually, this is done by either:

- **Greedy decoding**: Choosing the word with the highest probability.
    
- **Sampling**: Randomly selecting a word according to the probabilities.
    
- **Top-k sampling** or **top-p (nucleus) sampling**: Selecting from the top-k most probable words or sampling from the most probable words that cumulatively sum to a threshold probability.

![](file:///tmp/lu1673888a1q8.tmp/lu1673888a1r5_tmp_fab9b4903c840f22.png)