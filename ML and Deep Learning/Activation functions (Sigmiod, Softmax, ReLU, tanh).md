
### **Why Do We Use Activation Functions?**

In a neural network, activation functions introduce **non-linearity** into the model. Without activation functions, every layer would be just a **linear transformation**, meaning the entire network would behave like a single linear function, no matter how many layers it has.

#### **Reasons for Using Activation Functions**

1. **Non-linearity**: Real-world data and tasks are often non-linear. Activation functions allow neural networks to learn complex patterns.
    
2. **Hierarchical Feature Learning**: Deeper layers can learn more abstract features, such as edges, textures, and objects in image classification.
    
3. **Prevents a Single Large Linear Transformation**: Without activation functions, a deep network would just be equivalent to a single-layer perceptron, which is not very powerful.

### **Why ReLU (Rectified Linear Unit)?**

ReLU is one of the most popular activation functions, defined as:

![[Pasted image 20250323180202.png]]
This means:

- If x is positive, the output is x (keeps the value as is).
    
- If x is negative, the output is 0.
    

#### **Advantages of ReLU**

✅ **Avoids Vanishing Gradients**: Unlike sigmoid or tanh, ReLU does not squash large values, so gradients do not become too small.  
✅ **Computational Efficiency**: It only requires a simple comparison (if x>0x > 0x>0, keep it; otherwise, set it to 0).  
✅ **Encourages Sparsity**, which we’ll discuss next.

----

### **What is Sparsity in Neural Networks?**

**Sparsity** refers to having **many zero values** in the activation maps of a neural network. This happens naturally with ReLU because it sets all negative values to zero.

#### **Why is Sparsity Beneficial?**

1. **Efficient Computation**: If many neurons output zero, fewer computations are needed, making the network faster.
    
2. **Better Generalization**: Sparse activations mean only the most important features are activated, helping prevent overfitting.
    
3. **Reduces Memory Usage**: Storing and processing zero values can be optimized for better memory efficiency.
    

#### **Example: Sparse vs. Dense Activations**

- **Sigmoid/Tanh:** Outputs are mostly non-zero, leading to **dense activations**.
    
- **ReLU:** Many neurons output **zero**, creating **sparse activations**.
    

🔹 **Analogy**: Think of sparsity like human brain activity—only certain neurons fire when needed, rather than all neurons being active all the time.

---

### **1. Sigmoid Function**

The **sigmoid function** is defined as:

![[Pasted image 20250323175722.png]]
#### **Uses in Neural Networks:**

- The sigmoid function maps any real-valued number into the range **(0,1)**.
    
- It is often used in **binary classification** tasks, where the output represents the probability of a particular class.
    
- It is also used in **hidden layers** of neural networks but has largely been replaced by ReLU due to the **vanishing gradient problem**.
    

#### **Issues:**

- **Vanishing gradient problem**: For very large or very small inputs, the gradient approaches zero, slowing down learning.
    
- **Not zero-centered**: The output is always positive, which can slow down optimization.
    

---

### **2. Softmax Function**

The **softmax function** is a generalization of sigmoid used for **multi-class classification**. It is defined as:

![[Pasted image 20250323175742.png]]
#### **Uses in Neural Networks:**

- It converts a vector of arbitrary values into a **probability distribution** where all outputs sum to **1**.
    
- Used in the **output layer** of a neural network for **multi-class classification**, ensuring each class has an associated probability.
    

#### **Key Properties:**

- **Exponential scaling**: It emphasizes larger values more, helping the model make confident predictions.
    
- **Probability interpretation**: The output can be directly interpreted as class probabilities.
    

#### **Issues:**

- **Sensitivity to large input values**: If logits are very large, numerical stability issues can arise (often mitigated by subtracting the maximum logit).
    
- **Not sparse**: Unlike activation functions like ReLU, softmax does not inherently promote sparsity in activations.

---

### **Tanh Activation Function**

The **tanh (hyperbolic tangent) activation function** is a widely used activation function in neural networks, especially in older architectures. It is defined as:

![[Pasted image 20250323200247.png]]
### **Key Properties of Tanh**

✅ **Output Range**: Unlike sigmoid, which ranges from **(0,1)**, **tanh outputs values between (-1,1)**.  
✅ **Zero-Centered**: The output is symmetric around zero, making it better than sigmoid in some cases because it helps **center the data** during training.  
✅ **Smooth and Differentiable**: The function is continuous and differentiable everywhere, which is useful for gradient-based optimization.

---

