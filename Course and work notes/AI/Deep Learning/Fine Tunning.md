
**PEFT(parameter efficient fine tuning):**
# **1) Additive fine-tuning :**

Two major types, differing based on the additional tunable models or parameters, which are:

## **a) Adapter-based Fine-tuning :**

This approach involves insertion of small adapter layers within Transformer blocks. Since during fine-tuning, only a minimal number of trainable parameters that are strategically positioned within the model architecture are updated , that results in reduction of storage, memory and compute requirements.

![[Pasted image 20240525191811.png]]
