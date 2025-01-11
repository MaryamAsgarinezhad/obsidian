
Attention vs self-attention:
![[Pasted image 20250104201933.png]]
![[Pasted image 20250104201943.png]]

- Self attention is where the space of query and key values are the same, but in attention its not.

- Self attention is used on decoder or encoder on;y models, while attention is used in encoder-decoder models.

- Self-attention can be done in parallel since transformers process all the input at once, unlike RNNs.

-----------

![[Pasted image 20241230181255.png]]

-------

Comparison: 

1. Encoders: In these models, attention is applied in the encoding phase.

![[Pasted image 20250104193215.png]]
- Types of BERT:
![[Pasted image 20250104194549.png]]
![[Pasted image 20250104194642.png]]

-----------

2. Decoders: Attention happens in decoding phase

- Again the probability P is gained from a fully-connected dense layer, inside which it includes attention weights.
![[Pasted image 20250104195047.png]]
![[Pasted image 20250104195318.png]]
![[Pasted image 20250104195600.png]]
![[Pasted image 20250104195941.png]]
Pretraining stage of GPTs is so strong that it can predict next tokens from any prompts and shots.

3. Encoder-Decoder: The decoder part can be RNN (transformer or ...)
   
 an **encoder-decoder model** is **not simply a combination of an encoder (like BERT) with a decoder**. While you can **combine a pre-trained encoder (like BERT) with a decoder**, an **encoder-decoder architecture** (such as **Transformer, T5, or BART**) is fundamentally different in its design and purpose.
 
![[Pasted image 20250106191351.png]]
### **(A) The Encoder (Like BERT but Not Always BERT)**

- Processes the **input sequence** (e.g., a sentence).
- Uses **self-attention** to build a rich contextualized representation.
- Outputs a sequence of **hidden states**.

### **(B) The Decoder**

- Takes the **encoder’s output** and generates text **token by token**.
- Uses **self-attention** but also **cross-attends** to the encoder’s output.
- Works **autoregressively**, meaning it generates words **one by one**.

![[Pasted image 20250106191551.png]]
![[Pasted image 20250106191716.png]]

--------

What is cross attention?
![[Pasted image 20250106191958.png]]
How it actually works?
![[Pasted image 20250106192538.png]]
![[Pasted image 20250106192603.png]]
![[Pasted image 20250106192657.png]]

--------------
![[Pasted image 20250104200218.png]]

Since transformers can save the context of about 50 words, we can **apply attention in the hidden state of LSTMs** (it learns to remember a weighted sum of context, instead of some specific words of it). ==RNNs with attention mechanism==.


![[Pasted image 20250104200708.png]]
