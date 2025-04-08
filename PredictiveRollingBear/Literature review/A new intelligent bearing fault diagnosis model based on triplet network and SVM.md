
The paper focuses on **bearing fault diagnosis**, a crucial task in rotating machinery maintenance, by proposing an ==intelligent classification method==. Since collecting extensive faulty bearing data is difficult, the authors develop a **small-sample-friendly model** leveraging a **triplet network** and **support vector machine (SVM)**.

#### **Key Contributions:**

1. **Triplet Network-based Feature Extraction:**
    - The authors introduce a **1D CNN** model trained with **triplet loss** to **map vibration signals** into a high-order feature space.
    - This embedding enhances **the discrimination between different fault types**.
      
2. **SVM-based Fault Classification:**
    - The extracted features are classified using **Support Vector Machine (SVM)** instead of conventional deep learning models like CNN.
    - This improves **accuracy, robustness, and interpretability** in small sample datasets.
      
3. **Comparison with Traditional Methods:**
    - The proposed method is **superior** to autoencoder-based (SAE, SDAE) and end-to-end CNN approaches.
    - It shows better **feature representation, robustness against noise, and classification accuracy**.

---

### **How Does the Paper Achieve Its Goal?**

The paper achieves effective **bearing fault diagnosis** using the following steps:

#### **1. Problem Definition & Challenges**

- Bearings are essential components in **rotating machinery**, and faults must be detected early to **prevent catastrophic failures**.
- Traditional methods (e.g., **wavelet transform, Fourier transform**) require **expert knowledge** and struggle with **noisy sensor data**.
- **End-to-end CNNs** perform well on large datasets but fail in small-sample settings.
- **Autoencoders (SAE, SDAE)** extract features but **discard classification-relevant information**.

#### **2. Proposed Methodology**

##### **Step 1: Feature Extraction with a Triplet Network**

- The **triplet network** consists of a **1D CNN** with 8 hidden layers.
- It learns a **discriminative feature representation** by **minimizing triplet loss**, which ensures:
    - **Closer embeddings** for similar faults (e.g., two inner ring faults).
    - **Wider separations** between different faults (e.g., inner ring vs. ball fault).

##### **Step 2: Classification with SVM**

- The extracted features are **fed into an SVM classifier** instead of a conventional fully connected (softmax) layer.
- **Why SVM?**
    - Works well on **small sample datasets**.
    - **Maximizes margin** between different classes for better **generalization**.

---

### **Technical Details**

#### **1. Triplet Network & Loss Function**

- The **triplet network** takes **three inputs**:
    - **Anchor (a):** A random vibration sample.
    - **Positive (p):** A sample with the **same** fault type as the anchor.
    - **Negative (n):** A sample with a **different** fault type from the anchor.
- The **triplet loss** function ensures:
  
  ![[Pasted image 20250201123945.png]]
  
    - **Reduces** the distance between anchor and positive samples.
    - **Increases** the distance between anchor and negative samples.
    - Uses a **margin** to **improve class separability**.

#### **2. Data Used**

- **Two bearing datasets**:
    1. **Data Castle Dataset** (small dataset with noise)
    2. **XJTU-SY Bearing Dataset** (real-world fault degradation dataset)


#### **3. Performance Comparison**

| Method                               | Test Accuracy (%) |
| ------------------------------------ | ----------------- |
| SAE + SVM                            | 86.50%            |
| SDAE + SVM                           | 84.66%            |
| CNN (End-to-End)                     | 89.01%            |
| **Triplet Network + SVM (Proposed)** | **96.31%**        |

- **Why does the proposed method outperform others?**
    - Autoencoders (SAE, SDAE) focus on reconstruction **rather than classification-relevant features**.
    - End-to-end CNNs struggle with **small datasets**.
    - **Triplet Network extracts meaningful differences** and **SVM maximizes classification margin**.

---

### **Key Takeaways**

1. **Triplet networks** significantly improve **feature extraction for fault diagnosis**.
2. **SVM outperforms deep classifiers** for **small sample datasets**.
3. **Proposed method is more ==robust** to noise== compared to autoencoder-based and CNN approaches.
4. **Future Work:** Extend to **early fault prediction** for **preventive maintenance**.

This paper introduces a **powerful hybrid model for intelligent fault diagnosis**, achieving **state-of-the-art results in small sample scenarios**.

---

This paper does **not** focus on **Remaining Useful Life (RUL) prediction**. The proposed method is designed for **fault classification**, not for estimating **how much time is left before failure**.

#### **Why Doesn't This Paper Address RUL?**

- **RUL prediction** requires modeling the **degradation process** of a machine component over time.
- The method in this paper **only classifies** ==whether a bearing is faulty and **identifies the fault type** (e.g., inner race fault, ball fault).==
- **No time-series progression or degradation trend** is analyzed.
- The dataset used (**Data Castle and XJTU-SY**) does **not provide lifecycle data** explicitly designed for RUL estimation.

### That's why we need to classify fault types seperable and discriminative.

---

### **Traditional Methods in Bearing Fault Diagnosis and Their Role**

Traditional methods for **bearing fault diagnosis** involve **signal processing techniques** to extract features from vibration signals. These methods help identify **fault patterns** but have limitations, especially when dealing with **noisy data or small sample sizes**.

---

Tip: Replaced triple loss CNN + SVM (together work better on small datasets) instead of CNN + MLP (together work better on large datasets)

---

#### Why is the Proposed Model Robust to Noise While Traditional Methods Are Not?

- Even if the raw vibration data contains **random noise**, the network learns **invariant features** that still allow **correct classification**.
- **Traditional methods (Fourier Transform, Wavelet Transform, Autoencoders) focus on reconstructing or extracting frequency-based features**, which are **easily distorted by noise**.

A faulty bearing's vibration signal with noise still **has the same underlying characteristics**. Triplet loss helps the model focus on these **fault-specific features**, rather than random variations.

---

### **How Triplet Loss Works in Training vs. Testing**

| Phase        | Input Type                                | Process                                                                                         |
| ------------ | ----------------------------------------- | ----------------------------------------------------------------------------------------------- |
| **Training** | **Triplets** (Anchor, Positive, Negative) | The network learns to minimize intra-class distances and maximize inter-class distances.        |
| **Testing**  | **Single Sample**                         | The trained model extracts a **64-dimensional embedding**, which is then classified by **SVM**. |

**Why No Triplets in Testing?**

- The network has **already learned** the feature space.
- Each new test sample is **mapped into that space**.
- **SVM handles the classification**, so the model only needs **one input per test instance**.

---

### **Key Modifications Needed for RUL Prediction**

To adapt the **Triplet Network + SVM** for RUL estimation, the following changes are required:

|Aspect|Current Approach|Needed for RUL Prediction|
|---|---|---|
|**Task**|Classifies faults|Predicts time-to-failure|
|**Input Data**|Single vibration samples|Sequential time-series data|
|**Output**|Fault category|Estimated remaining lifetime|
|**Model**|CNN + Triplet Network + SVM|CNN + Triplet Network + LSTM/Regression|

### **1. Use the Triplet Network as a Feature Extractor**

- The triplet network is already good at extracting **discriminative features** from vibration signals.
- Instead of using it just for **classification**, we can use the **64-dimensional embedding** as **input for an RUL prediction model**.

✅ **How This Helps RUL Prediction:**

- The embeddings capture **degradation patterns**, which can be used to model the **trend of health deterioration** over time.

---

### ==**2. Collect Data Over Time Instead of Using Single Samples**==

- The current approach uses **individual vibration samples** for classification.
- **For RUL, we need time-series data**, meaning we must collect multiple samples over time.
- **How?**
    - Aggregate **sequential feature embeddings** from the triplet network.
    - Train a **time-series model (LSTM, Kalman Filter, etc.)** on these features.

✅ **Example:**

- Suppose we monitor a bearing for **10,000 cycles**.
- Every **1,000 cycles**, we extract features from the triplet network.
- These features are **fed into an LSTM**, which predicts **how much longer the bearing will last**.

---

### **3. Incorporate a Health Indicator for RUL Estimation**

- **Instead of classifying faults**, we need a **continuous health score** that degrades over time.
- Possible approaches:
    - **Use PCA/T-SNE to visualize feature degradation over time.**
    - **Fit a degradation curve** based on the extracted features.
    - **Train a model to predict RUL based on feature changes**.

==✅ **How It Works:**==

- In early stages, features from the **triplet network** cluster around “healthy” embeddings.
- As wear progresses, features **gradually shift toward faulty embeddings**.
- A model (e.g., LSTM or regression) learns this **trend and predicts when failure will occur**.

---

### **4. Train on a Suitable RUL Dataset**

- The datasets used in the paper (**Data Castle, XJTU-SY**) focus on **fault classification**, not RUL.
- For RUL prediction, we need **full degradation cycle data**.
- Possible datasets:
    - **NASA’s C-MAPSS dataset** (Aircraft engines)
    - **PRONOSTIA dataset** (Bearing degradation)
    - **XJTU-SY ==with RUL labels**== (modification required)

✅ **How to Use the New Dataset:**

1. Train the **triplet network** on the new dataset to **extract degradation features**.
2. Use these features to **train an RUL prediction model** (LSTM, Kalman Filter, etc.).
3. Validate against **known RUL values**.


Summary: Use triplet network + LSTM instead of DSCN + MLP.

---

### **Comparison with DSCN Input**

| **Model**            | **Input Size** | **Number of Channels**                                  |
| -------------------- | -------------- | ------------------------------------------------------- |
| **Triplet Network**  | 6000           | **==1D== (single-channel vibration signal)**            |
| **DSCN (RUL Paper)** | 3×6144×23      | **2 (multi-channel: horizontal & vertical vibrations)** |

---

### **Key Differences**

1. **DSCN uses two-channel sensor data (horizontal & vertical vibration), while the Triplet Network only processes a single-channel vibration signal.**
2. **DSCN uses a time window of 3 consecutive steps**, but the Triplet Network does not consider time dependencies explicitly.
3. **Triplet Network uses triplets during training**, while DSCN uses a single input per forward pass.

You can implement 3D input (window of size 3) for LSTM feeding to capture temporal dependencies.

کل این قضیه رو ربط بده به VAE که میخاستی استفاده کنی.
در ضمن triplet net لاس ۳ تایی جداگونه خودشو داره و احتمالا بهتر عمل میکنه.

---

![[Pasted image 20250202150753.png]]

---

