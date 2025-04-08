
### **1. `DeepHPM` (Deep Hidden Physics Network)**

#### **Purpose:**

- Implements a physics-informed neural network with a **self-attention layer** to model temporal dependencies and capture important features from time-series data.

#### **Key Components:**

1. **Multi-Head Attention (`self.multihead_attn`)**:
    
    - Captures temporal relationships between different time steps in the input.
    - Uses 1 attention head for simplicity.
2. **Linear Layers (`Dense1` and `Dense2`)**:
    
    - `Dense1`: Maps features to the same dimensionality for feature refinement.
    - `Dense2`: Maps features to a hidden dimension for further processing.
3. **Layer Normalization (`LN`)**:
    
    - Normalizes the output of the attention layer to stabilize training.
4. **ReLU Activation**:
    
    - Applies non-linearity to the refined features.

Forward pass:
```python
x, weight = self.multihead_attn(X, X, X)  # Self-attention
x = self.LN(x + X)                        # Residual connection and normalization
x1 = self.Dense1(x)                       # First dense layer
x1 = self.activation(x1 + x)              # Residual connection with activation
return self.Dense2(x1)                    # Second dense layer output
```

 **Future Additions**:

- The term (Hidden Physics Network) might be aspirational, with plans to incorporate physics-based constraints in the loss function or architecture at a later stage.


----

### **2. `MLP` (Multi-Layer Perceptron)**

#### **Purpose:**

- Maps the hidden states (features) from the `DeepHPM` or another encoder into **six RUL predictions**.

#### **Key Components:**

1. **Learnable Scaling Parameter (`params`)**:
    
    - A trainable parameter initialized to 10 to scale the MLP output.
2. **Dense Network (`dnn`)**:
    
    - A stack of fully connected layers with ReLU activations.
    - Outputs a single scalar for RUL prediction.

Forward pass:

```python
x = self.dnn(X)            # Pass through the fully connected layers
x = x * self.params         # Scale the output using the trainable parameter
return x
```

----

### **3. `SeparableConvBlock`**

#### **Purpose:**

- Implements a ==**depthwise separable convolution** block== for efficient feature extraction from time-series data.

#### **Key Components:**

1. **Depthwise Convolution**:
    
    - Applies separate convolutions for each input channel, reducing the number of parameters.
2. **Pointwise Convolution**:
    
    - Combines the outputs of the depthwise convolution, enabling inter-channel mixing.
3. **Squeeze-and-Excitation Unit (`se_unit`)**:
    
    - Dynamically recalibrates channel-wise feature maps based on their importance.
4. **Residual Connection (`skip_connection`)**:
    
    - Adds the original input to the block’s output to retain information.

#### **Forward Pass:**

```python
residual = self.skip_connection(x)  # Compute the residual connection
x = self.layers(x)                  # Pass through the separable convolution layers
x = self.avg_pool(x)                # Apply average pooling
x = self.se_unit(x)                 # Apply squeeze-and-excitation
return x + residual                 # Add the residual connection
```

---

### **4. `SqueezeExcitation`**

#### **Purpose:**

- Implements a **squeeze-and-excitation (SE) block** to recalibrate channel-wise features dynamically.

#### **Key Components:**

1. **Squeeze**:
    - Reduces each channel to a single scalar by global average pooling.
2. **Excitation**:
    - Learns the importance of each channel using fully connected layers and a sigmoid activation.

#### **Forward Pass:**

```python
scale = self.se_block(x)  # Compute channel-wise scaling factors
return x * scale          # Scale the input channels
```

### **Why Recalibrate Features?**

- Not all features (channels) are equally important for a specific task (e.g., RUL prediction).
- Some features might be more informative, while others could be less relevant or even noisy.
- **Recalibration** means assigning **different levels of importance** to different channels, enabling the model to focus more on the channels that matter.

---

### **5. `DSCN` (Deep Separable Convolutional Network)**

#### **Purpose:**

- ==Combines multiple `SeparableConvBlock`== layers to create a deep architecture for time-series feature extraction.

#### **Key Components:**

1. **Initial Convolution (`initial_conv`)**:
    
    - Applies a depthwise separable convolution followed by average pooling to process the input.
2. **Separable Convolution Blocks (`blocks`)**:
    
    - Stacks multiple `SeparableConvBlock` layers, each increasing the number of channels.
3. **Global Pooling (`global_pool`)**:
    
    - ==Reduces each channel to a single scalar by global average pooling==, ensuring fixed-size outputs regardless of input length.

#### **Forward Pass:**

```python
x = x.permute(0, 3, 1, 2).squeeze(-1)  # Reorganize input dimensions
x = self.initial_conv(x)               # Apply the initial convolution
x = self.blocks(x)                     # Pass through the separable convolution blocks
x = self.global_pool(x)                # Apply global average pooling
x = x.squeeze(-1)                      # Remove the spatial dimension
return x
```

---

### **Key Relationships Between Components**

- `DeepHPM` and `DSCN`:
    
    - Serve as encoders to extract features from raw or preprocessed time-series data.
    - They focus on different approaches:
        - **DeepHPM**: Uses self-attention to capture temporal relationships.
        - **DSCN**: Uses separable convolutions to extract hierarchical features.
- `MLP`:
    
    - Maps the extracted features from `DeepHPM` or `DSCN` to **RUL predictions**.
- `SeparableConvBlock` and `SqueezeExcitation`:
    
    - Form the backbone of the `DSCN` model, enabling efficient and dynamic feature extraction.

---

### **Overall Architecture**

1. **Feature Extraction**:
    
    - Input time-series signals are processed by `DeepHPM` or `DSCN` to extract high-level features.
2. **Prediction**:
    
    - The extracted features are passed through the `MLP` to produce scalar RUL predictions.

