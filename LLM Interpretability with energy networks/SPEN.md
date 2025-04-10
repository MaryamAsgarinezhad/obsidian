
![[Pasted image 20250410182525.png]]

- This is why we need an energy function.

## `SPENClassification` Class

This is the main model class, and it **inherits from a general `SPEN` class**.

### `__init__` method

`def __init__(...):`

- Loads a **pretrained feature extractor** (`FeatureMLP`) from disk.
    
- Creates an `EnergyNetwork` model using that feature extractor.
    
- Initializes the base `SPEN` class with optimizer and inference parameters.
    

 **Purpose**: It sets up the model's architecture, optimizer, and energy function.

---

### Inference (`inference` method)

`def inference(self, x, training: bool, n_steps: int):`

- Runs **gradient-based inference** over label predictions `y_pred` to minimize the energy.
    
- Optimizes `y_pred` using a custom `SGD` to get structured output.

**Purpose**: Find the optimal label assignment that minimizes energy.

---

### 📉 Loss Computation (`_compute_loss`)

`def _compute_loss(self, inputs, targets, training: bool):`

- Gets features `f_x` from the pretrained feature extractor.
    
- Runs inference to get predicted labels.
    
- Computes energy values for both prediction and ground truth.
    
- Uses a **max-margin loss**:
    ![[Pasted image 20250410195620.png]]
 **Purpose**: Trains the model to assign lower energy to correct labels and higher energy to incorrect ones. (For SPEN)

### ⚖️ Where Is the Loss Function?

The loss function is defined **outside** the Energy Network — in the `SPENClassification` class.

Look at this part of your code:

```python
# Energy of predicted labels
pred_energy = self.model(f_x, pred_labels)

# Energy of ground truth labels
gt_energy = self.model(f_x, targets)

# Max-margin loss
pre_loss = self.loss_fn(pred_labels, targets) - pred_energy + gt_energy
loss = torch.max(pre_loss, torch.zeros(pre_loss.size(), device=pre_loss.device))
loss = torch.mean(loss)
```
This is the **max-margin loss** used to ==train the SPEN==.

> **SPEN training** means learning the parameters (weights) of the neural networks **inside** the SPEN — especially the **Energy Network** —  
> so that the energy function assigns **low energy to correct outputs** and **high energy to incorrect ones**.

Even though **SPEN is not a single network**, it’s a system that:

- Uses neural nets (like the energy function)
    
- Computes loss (based on the energy landscape)
    
- Uses backpropagation to update the energy function's **weights**


> **SPEN is a system that defines a differentiable energy function E(f(x),y)**, and:

- Learns parameters of E (via backprop)
    
- Predicts y by **optimizing it** to minimize energy — **not** by direct forward pass through weights.

---

Code note:
![[Pasted image 20250410183340.png]]

```python
model = EnergyNetwork(self.feature_extractor.fc3.weight, input_dim, label_dim,
                      num_pairwise, non_linearity)
```

Initializing an **EnergyNetwork** — a module that defines the energy function E(f(x),y).

####  Inputs:

- `self.feature_extractor.fc3.weight`:  
    → The final linear layer weights from the pretrained `FeatureMLP`.  
    → These weights map from features to labels, so they carry useful knowledge about how features relate to labels.
    
- `input_dim`: 1836 → Dimension of the input features
    
- `label_dim`: 159 → Number of labels (BibTeX dataset has 159 possible tags)
    
- `num_pairwise`: 16 → Number of **pairwise label interactions** the model will learn  
    → Think of this like learning how labels interact (e.g., "math" and "AI" often co-occur)
    
- `non_linearity`: `nn.Softplus()` → Smooth activation for better optimization


```python
super().__init__(model, optim, learning_rate, weight_decay, inf_lr, n_steps_inf, label_dim,
                 loss_fn, momentum, momentum_inf)
```

This passes everything up to the **base `SPEN` class**, which does all the heavy lifting:

- Stores the `model` (i.e. energy network)
    
- Sets up:
    
    - Optimizer (e.g., Adam or SGD)
        
    - Inference parameters:
        
        - `inf_lr` → Learning rate for optimizing yyy
            
        - `n_steps_inf` → How many gradient steps to run when inferring y^\hat{y}y^​
            
        - `momentum_inf` → Momentum for inference optimizer
            
- Sets training options:
    
    - `loss_fn` → What loss to use (e.g. `"mse"`)
        
    - `momentum`, `weight_decay`, etc.

```python
# Move feature extractor to the same device as the model  
self.feature_extractor.to(self.device)
```

---

##  What Is Structured Prediction?

**Structured prediction** is when the output you're trying to predict has **internal structure or dependencies** — it’s not just a single label or number.

###  Instead of predicting:

- A single label (like `"cat"` or `"dog"`)
    
- A single value (like price = 42.3)
    

###  You’re predicting:

- A **vector** of interdependent labels (e.g., multi-label classification: `["AI", "math", "NLP"]`)
    
- A **sequence** (e.g., part-of-speech tags in NLP)
    
- A **tree** (e.g., parse trees)
    
- A **grid** (e.g., image segmentation masks)
    
- A **graph** (e.g., molecular structures)

## In SPEN

SPEN tackles structured prediction by:

- Defining an **energy function** E(x,y)
    
- Lower energy means "better" predictions
    
- It **optimizes over y** to find the best structured output
    

This allows the model to **capture interactions between parts of the output**, like:

- Label A and Label B usually appear together
    
- Label C and D are usually mutually exclusive

---
# 🔁High-Level Goal of SPEN Training

We want:
![[Pasted image 20250410211210.png]]

This means:

- The energy of the **true label** should be **lower** than the energy of any **wrong prediction**
    
- The margin can be based on task loss (e.g., Hamming loss, MSE)
    

We implement this using a **max-margin loss**:

![[Pasted image 20250410211225.png]]

This loss is minimized during training to shape the energy surface.

---

## End-to-End Example (SPEN Training Step)

### 🔹 Setup

- Input:  
    x: A TF-IDF vector for a paper about "deep learning in NLP"
    
- True label ytrue​:  
    [1, 0, 1, 0, 0] → `"NLP"` and `"Deep Learning"`
    
- Feature extractor:  
    ![[Pasted image 20250410211255.png]]
---

### 🧩 Step-by-Step SPEN Training

#### Step 1: Extract features

`f_x = feature_extractor(x)`
#### Step 2: Run inference (optimize y to minimize energy)

```python
y_pred = torch.rand(label_dim, requires_grad=True)  # random init

for _ in range(n_steps):
    energy_pred = energy_network(f_x, y_pred)
    energy_pred.backward()  # compute ∇E/∇y_pred
    y_pred = y_pred - lr * y_pred.grad
    y_pred = y_pred.detach().clone().requires_grad_(True)
```

After this, `y_pred` is the **model's current prediction**.

---
#### Step 3: Compute energy scores

```python
E_pred = energy_network(f_x, y_pred)
E_true = energy_network(f_x, y_true)
```

- `E_pred` = energy of predicted labels
    
- `E_true` = energy of ground truth
    
---
#### Step 4: Compute task loss

`task_loss = torch.nn.functional.mse_loss(y_pred, y_true)`

(You could use any differentiable loss here — e.g., MSE or Hamming.)

---
#### Step 5: Compute SPEN loss

```python
margin_loss = task_loss - E_pred + E_true
spen_loss = torch.max(margin_loss, torch.zeros_like(margin_loss))
```
---
#### Step 6: Backprop to update **energy network parameters**
```python
spen_loss.backward()
optimizer.step()
```

Now the weights inside the **energy network** get updated, so:

- Energy of true labels becomes **lower**
    
- Energy of predicted (wrong) labels becomes **higher**, unless they’re already good

---
## 🎯 What Does This Achieve?

Over time, the energy network learns:

- “When the input has features like f(x), the correct label configuration is ytrue​”
    
- It shapes the energy landscape so that **good predictions fall into valleys**, and **bad ones sit on hills**

**NOTE**: Because E(f(x),y) is implemented as a **differentiable neural network**, and PyTorch (or TensorFlow, etc.) allows **autograd** on **any tensor**, we **treat y as a learnable tensor**, and backpropagate through the energy network **with respect to y**.

---

- ==SPEN --> A framework==
- ==Energy --> A network==

---

