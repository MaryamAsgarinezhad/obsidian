We are going to explain the steps after encoding and applying attention:

- In the decoders, we use a **fully conneted** layer. It's input is the token vector representation arguments, and it has one ouput for each token in the vocabulary(each possible word)

![[Pasted image 20240525151420.png]]

------------------------------------------------

decoding of GPT vs Transformer:

![[Pasted image 20240525153646.png]]
![[Pasted image 20240525153904.png]]

-Supervised or unsupervised training of the decoder depends on the task. For example translation/text generation is well suited with supervised/unsupervised approaches.

**Example**: In machine translation,

- **Input**: A sentence in English.
- **Output**: The correct translation of that sentence in French.
- **Training**: The model is trained using a loss function that measures how close the model's output sentences are to the actual target sentences in the training set.

**Example**: In language modeling,

- **Input**: A sequence of tokens ("The quick brown fox").
- **Output**: The next token in the sequence ("jumps").
- **Training**: The model is trained to predict each token in a sequence given the previous tokens, and the =="labels" are the subsequent tokens in the training data itself==.