
Double descent is a **phenomenon where the test error curve dips twice** as model complexity increases — not once like we traditionally expect.

### 🧠 Traditional view (U-shaped bias-variance curve):

As model complexity increases:

- Underfitting on the left (high bias)
    
- Sweet spot in the middle (good generalization)
    
- Overfitting on the right (high variance)

So the test error looks like a **U-shaped curve**.

### What happens in deep learning?

In modern overparameterized models (like deep neural nets), **the test error sometimes _decreases again_ even after overfitting**.

That’s the second descent → hence the name:
![[Pasted image 20250416170621.png]]

---
### What causes the “double” part?

Let’s label the three phases:

1. **Underparameterized regime**
    
    - Model too simple → underfits
        
    - Both training & test error are high
        
2. **Interpolation threshold**
    
    - Model is _just big enough_ to fit the training data _perfectly_
        
    - Training error = 0, but **test error spikes** due to overfitting  
        This is called the **interpolation peak**
        
3. **Overparameterized regime**
    
    - Model has **far more parameters than data**
        
    - Surprisingly, test error **drops again**  
         Model generalizes well, despite being huge
        

---

### Why does it happen?

Researchers believe it's due to:

- **Implicit regularization**: Optimizers like SGD ==bias the model toward simpler solutions, even in overparameterized settings.==
    
- **Model capacity**: Very large models can fit data in a way that aligns well with generalization.
    
- **Neural networks** finding low-complexity solutions despite high capacity.

Note: **Regularization** refers to any technique that reduces **overfitting** and improves **generalization** — helping the model perform better on unseen data.

---

### Where do we see it?

- Deep neural networks
    
- Random forests
    
- Kernel machines (e.g. RBFs)
    
- Even in simple linear models with polynomial features!
    

> **More capacity doesn’t always mean worse generalization.**  
> In fact, making models bigger **can** help again after a critical point.

It’s one of the things that made **modern deep learning** (with huge models like GPT) actually _work_ in practice.

---

**This fact causes scalability the key feature for deep models' accuracy.**

- And that is why transformers are far more efficient than others (due to their high scalability)

