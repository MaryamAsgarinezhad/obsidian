
**The _goal_ of both Batch Normalization and Layer Normalization is the same**:

> To **stabilize and accelerate training** by reducing internal covariate shift.

But — even though they share the same _purpose_, they do it in different **ways** and are better suited to different **contexts**.

---

**Batch Normalization (BatchNorm)** is a technique in machine learning—especially deep learning—used to make neural networks **train faster and more reliably**.

### The Intuition

As training progresses, the distribution of activations (**the inputs to a given layer**, but **those inputs are actually the outputs (activations) of the previous layer**) in each layer keeps shifting—a phenomenon called **internal covariate shift**. This makes training unstable and slow, *since each layer has to constantly adapt to the shifting inputs from the previous layer.*

Batch normalization helps by **keeping the inputs to each layer more consistent**, reducing that shift.

For each **mini-batch** during training, BatchNorm:

![[Pasted image 20250416160511.png]]

- **Faster training** (allows higher learning rates)
- **Reduces sensitivity** to initialization
- **Acts like a regularizer** (sometimes reducing need for dropout)
- **Smooths the loss landscape** (helps gradient flow)

Typically inserted **after a linear/convolutional layer and before the activation** (like ReLU):

```python
nn.Sequential(
    nn.Linear(128, 64),
    nn.BatchNorm1d(64),
    nn.ReLU()
)
```

- If you pass in a tensor of shape `[batch_size, 64]`, it will normalize **each of those 64 features** across the batch.
    
- If you're using it after a `Linear(128, 64)`, it’s saying “I’m normalizing those 64 output features.”
----

**Layer Normalization (LayerNorm)** is a normalization technique that **normalizes across the features of a single data sample**, rather than across the batch.


### How it works

Given an input tensor of shape ==`[batch_size, num_features]`==:

- **BatchNorm** normalizes each feature _across the batch_
    
- **LayerNorm** normalizes _all features_ of **each individual sample**

![[Pasted image 20250416161958.png]]

- **Transformers** (like BERT, GPT) use LayerNorm everywhere
    
- More **stable** than BatchNorm for sequential or variable-length input
    
- Doesn’t depend on batch size → better for small-batch or online training

```python
import torch.nn as nn

layer = nn.LayerNorm(64)  # normalizes across 64 features *per sample*

```

|Feature|**BatchNorm**|**LayerNorm**|
|---|---|---|
|Normalizes over|**Batch dimension**|**Feature dimension**|
|Affected by batch size|✅ Yes|❌ No|
|Works well in|CNNs, large batch training|RNNs, Transformers, small batch settings|
|Computed over|Each feature across batch|All features within each sample|

---

"Add & Norm" (a.k.a. **AddNorm**) is a common pattern used in **Transformer architectures** — like BERT and GPT — to stabilize training and improve learning.

It's a simple but powerful two-step process used around **residual connections**:

```python
Output = LayerNorm(x + Sublayer(x))
```

- **Add**: Take the output of a sublayer (like Multi-Head Attention or a Feedforward layer), and **add it to the original input** (residual connection).
    
- **Norm**: Apply **Layer Normalization** to the result.


### Why “Add”?

The **residual connection** (a.k.a. "skip connection") helps:

- ###### Preserve the original input
    
- ###### Let the network learn small adjustments (residuals)

	In a traditional neural network, each layer is expected to **learn the full transformation** from input to output:
	
	Output = F(x)   ← the network learns this whole thing
	
	But in a **residual network** (like in Transformers or ResNets), the idea is:
	 
	 **Instead of learning the full mapping, just learn what needs to change from the input**
	 **“Let’s keep the input `x` and just add a tweak (`F(x)`) to make it better.”**
	
	This is often **much easier to learn**, especially when the desired output is already _close_ to the input.
    
- ###### Improve gradient flow (especially in deep networks). Since it is proven that gradients reach lower layer wieghts more weakly than higher level weights.

	During backpropagation, the gradient of the loss can flow through **both**:
	
	1. The path through `F(x)` (the sublayer), and
	    
	2. The shortcut path directly through `x`
	
	This means:
	
	- If the sublayer `F(x)` has small or unstable gradients, **the gradient can still pass through the identity path (`x`)**.
	    
	- This **preserves signal** and avoids it vanishing or exploding over many layers.
	
	Mathematically:
	
	`∂L/∂x = ∂L/∂y * (1 + ∂F/∂x)`
	
	==That “1” (from the `x` path) keeps the gradient alive, even if `∂F/∂x` is small or noisy.==

Proof:

![[Pasted image 20250416165446.png]]
![[Pasted image 20250416165459.png]]
![[Pasted image 20250416165531.png]]

### Why “Norm”?

The **LayerNorm** helps:

- Stabilize the values flowing through the network
    
- Speed up convergence
    
- Keep the distribution of data smooth, preventing extreme values


Where it’s used in a Transformer block:
```
x → [Self-Attention] → Add & Norm
  → [Feedforward]    → Add & Norm
```

---

