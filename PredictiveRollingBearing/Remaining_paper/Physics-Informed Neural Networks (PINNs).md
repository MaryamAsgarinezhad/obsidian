### **1. What Is a PINN?**

A PINN is a type of neural network that incorporates ==**physical laws** into its training process== by encoding them in the network’s loss function. This ensures that the model predictions respect known physical constraints (e.g., conservation laws, differential equations) while learning from data.

- The PINN integrates physical constraints into the RUL prediction model by leveraging partial differential equations (PDEs).
- This allows the network to model the underlying degradation patterns of machinery components in a way that aligns with physics.
- Instead of relying solely on **data-driven methods**, the PINN introduces **additional constraints** derived from the physical behavior of the system.

---

### **2. RUL Prediction: Physics-Informed Neural Network (PINN)**

The **PINN module** predicts the RUL using the hidden state from DSCN and incorporates physics-based constraints.

#### Key Components:

1. **Multi-Layer Perceptron (MLP)**:
    
    - The hidden state and corresponding time data are passed into the MLP to predict
      ==initial RUL values==.
2. **Deep Hidden Physics Model (DeepHPM)**:
    
    - Computes partial derivatives of the predicted RUL with respect to time and the hidden state using **automatic differentiation**.
    - These derivatives are used to model implicit physical degradation patterns (e.g., equations representing system wear).
3. **Self-Attention Mechanism**:
    
    - Enhances the model’s understanding of the interdependencies between time, hidden states, and physical quantities.
    - Assigns different importance weights to these variables.

#### Loss Function:

The PINN’s loss function combines:

- **Data-driven Loss**: Ensures the predicted RUL matches the ground truth labels.
- **Physics-based Loss**: Penalizes the model for violating physical laws represented by partial differential equations (PDEs).


---

### **3. Physics-Informed Neural Network (PINN) Variables**

![[Pasted image 20250125151720.png]]
![[Pasted image 20250125151752.png]]
![[Pasted image 20250125151816.png]]

==**Degradation dynamics**== refer to the process by which a system or component experiences wear, aging, or damage over time, eventually leading to failure. In engineering and predictive maintenance, these dynamics are modeled to estimate the **Remaining Useful Life (RUL)** of machinery components.

![[Pasted image 20250125153124.png]]

Note:
- **DeepHPM** parameters are trained during the model training process. The **Deep Hidden Physics Model (DeepHPM)** in the paper is part of the **Physics-Informed Neural Network (PINN)** and is designed to model the underlying physical relationships between the hidden states, time, and the predicted Remaining Useful Life (RUL).

![[Pasted image 20250125153604.png]]

---

