
**Deep Separable Convolutional Networks (DSCN)** are a variation of standard convolutional neural networks (CNNs) that aim to improve computational efficiency and ==reduce the number of parameters== while maintaining or even enhancing performance. The concept is based on the idea of separable convolutions, which ==break down a standard convolution operation into simpler, more efficient operations==.


### **How Separable Convolutions Work**

A separable convolution splits the standard convolution into two distinct operations:

1. **Depthwise Convolution**:
    - Each filter (kernel) spans **all** input channels.
    - Instead of applying a single convolutional kernel across all input channels, a separate kernel is applied to each channel independently.
    - This reduces the computational complexity since fewer operations are performed.
      
2. **Pointwise Convolution**:
    - Each input channel is processed **independently** using its own K×KK \times KK×K kernel. ==No cross-channel interactions happen during the depthwise convolution.==
    - A 1×1 convolution is applied across all channels to combine the information from the depthwise operation.
    - This step integrates features from different channels and reduces the channel dimensions if necessary.

---

**How Fewer Operations are Performed in Separable Convolutions**

![[Pasted image 20250125124133.png]]

![[Pasted image 20250125124143.png]]

There are C_out kernels, one for each output channel.

By processing each channel independently and combining them later, separable convolutions avoid redundant operations and achieve the same result more efficiently, making them especially useful in deep networks.

---

Note:

![[Pasted image 20250125124331.png]]

**Each output channel corresponds to one filter (kernel) applied during the convolution operation.**

- C_out​ is set by the number of filters used in the convolutional layer.
- Each filter extracts a specific pattern or feature from the input, such as edges, textures, or object parts.

---

در CNN عادی:

- لایه های convolution به تعداد C_out تا پشت سر هم با هم جمع میشن که هزینه هر کدوم k^2 (C_inHW) عه. 

در Deep seperable CNN:

- اول C_in تا خروجی موازی هر کدوم با هزینه H×W×K2 تولید میشن بعد روی اون C_in تا کرنل pairwise با هزینه هرکدوم H×W​×Cout اعمال میشه.

---

**Squeeze-and-Excitation (SE) Units**:

- Recalibrate the importance of each channel by assigning adaptive weights.
- Improves the network's focus on relevant features.


