
اول هر دوی اینا بگو که من گشتم مقاله روی این دیتاست و معتبر پیدا کنم که هر دو fault diagnosis بود.

#### **1. Purpose of the Paper**

The paper addresses the challenge of weak fault feature extraction and poor generalization performance in rolling bearing fault diagnosis. Traditional methods often struggle with noise, non-stationary signals, and insufficient feature extraction. The authors propose a novel method that combines:

- **Sparrow Search Algorithm (SSA)**
- **Variational Mode Decomposition (VMD)**
- **Refined Composite Multi-scale Dispersion Entropy (RCMDE)**
- **K-means K-Nearest Neighbor (KKNN) Classifier**

The goal is to enhance the ==accuracy, robustness, and generalization== ability of rolling bearing fault diagnosis, even in ==noisy environments==.

---

#### **Definition of IMFs**

An **Intrinsic Mode Function (IMF)** is a function that satisfies two conditions:

1. **Symmetric oscillations**: The number of extrema (peaks and valleys) and the number of zero-crossings must be either equal or differ by at most one.
2. **Local Mean Zero**: The mean value of the upper and lower envelopes (formed by local maxima and minima) must be zero.

These conditions ensure that an IMF represents a pure oscillatory mode, similar to a **single frequency component** in Fourier analysis but with nonstationary properties.

#### **Role of IMFs in Fault Diagnosis**

- In **rolling bearing fault diagnosis**, the vibration signal consists of multiple overlapping frequency components due to noise and mechanical interactions.
- By decomposing the signal into **IMFs**, we can isolate different frequency bands containing useful information about faults.
- **Only certain IMFs contain fault-related features**, while others contain noise or irrelevant components.

#### SSA:

It is used to **optimize parameters in complex problems** where traditional methods struggle.

🔹 **Key Features of SSA**:

- Inspired by the **social behavior of sparrows** during food searching.
- Contains **three types of agents**: **Finders**, **Producers**, and **Scroungers**.
- Capable of avoiding **local optima** and achieving **fast convergence**.
- Outperforms other swarm intelligence algorithms like **Whale Optimization Algorithm (WOA)** and **Grey Wolf Optimization (GWO)**.

---

سوال دوم: سیگنال بلبرینگ faulty و normal رو چجوری جدا کنیم تو دیتاستی که داریم.
#### **2. How the Paper Achieves This Goal**

The proposed method follows these main steps:

1. **Signal Acquisition & Preprocessing**
    
    - Vibration signals from rolling bearings in different conditions (normal, faulty) are collected.
    - The data may contain noise and complex frequency components.
2. **SSA Optimized VMD for Signal Decomposition**
    
    - **Variational Mode Decomposition (VMD)** is used to decompose vibration signals into intrinsic mode functions (IMFs).
    - The number of decompositions K and the penalty factor α significantly affect the quality of decomposition.
    - **Sparrow Search Algorithm (SSA)** automatically optimizes these parameters instead of using manual selection, leading to improved decomposition results.
3. **Selection of Sensitive IMF Components**
    
    - Not all IMFs contain useful fault-related features.
    - A time-frequency domain **comprehensive evaluation factor** is used to select the most relevant IMFs.
4. **Feature Extraction Using RCMDE**
    
    - **Refined Composite Multi-scale Dispersion Entropy (RCMDE)** extracts nonlinear features from the reconstructed signal.
    - Compared to other entropy-based methods (e.g., MPE, MFE), RCMDE provides more discriminative fault features.
5. **Fault Classification Using KKNN**
    
    - The extracted features are fed into a **K-means K-Nearest Neighbor (KKNN) classifier** for fault diagnosis.
    - KKNN improves classification accuracy by reducing computational complexity.

---

**comprehensive evaluation factor**:

![[Pasted image 20250201172524.png]]
![[Pasted image 20250201172535.png]]
![[Pasted image 20250201172551.png]]

---

**How Refined Composite Multi-scale Dispersion Entropy (RCMDE) Works**

![[Pasted image 20250201173725.png]]
![[Pasted image 20250201173807.png]]
![[Pasted image 20250201173822.png]]
![[Pasted image 20250201173832.png]]
![[Pasted image 20250201173848.png]]
![[Pasted image 20250201173904.png]]
![[Pasted image 20250201173925.png]]

---

### **Key Contributions & Takeaways**

1. **Automatic Parameter Optimization**
    
    - Using SSA to optimize VMD parameters improves decomposition quality.
2. **Robust Feature Extraction**
    
    - RCMDE extracts more discriminative features compared to traditional entropy methods.
3. **High Classification Accuracy**
    
    - KKNN classifier improves accuracy and reduces misclassification rates.
4. **Strong Noise Resistance & Generalization**
    
    - The method maintains high performance even with added noise.
5. **Practical Application Potential**
    
    - The framework is effective for industrial fault diagnosis, ensuring early fault detection in rotating machinery.