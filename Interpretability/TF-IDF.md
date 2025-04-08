
### **What Does TF-IDF Do and How?**

TF-IDF (**Term Frequency-Inverse Document Frequency**) is used to identify the ==most important words in a document **relative to a collection of documents (corpus)**==. It assigns a weight to each word, helping to distinguish relevant terms from commonly occurring ones.

---

### **How TF-IDF Works**

TF-IDF transforms **raw text data into numerical features** that capture the significance of words within a document.

1. **Measures Word Importance**:
    
    - Words that appear **frequently in a document** but **rarely in other documents** get a **high TF-IDF score**.
    - Words that appear in **many documents** get a **low score** (e.g., "the", "is", "and").
2. **Converts Text to Numerical Representation**:
    
    - Since machine learning models can't work directly with text, TF-IDF transforms words into numerical values that represent their importance.
3. **Enhances NLP Tasks**:
    
    - Used in **search engines**, ==**text classification**==, **document clustering**, and **topic modeling**.

---

![[Pasted image 20250315190253.png]]

---

### **Topic Representation in BERTopic: Class-Based TF-IDF**

In **BERTopic**, once documents are clustered into topics using **UMAP (for dimensionality reduction)** and **HDBSCAN (for clustering)**, we need to ==**extract meaningful words that best represent each topic**==. This is where the **Class-Based TF-IDF (c-TF-IDF)** procedure comes in.

---

### **What is Class-Based TF-IDF?**

Unlike traditional **TF-IDF**, which calculates word importance at the document level, **Class-Based TF-IDF ==treats each topic as a single document** and extracts the most representative words.==

#### **How It Works**

1. **Group Documents by Topic**:
    
    - Instead of analyzing individual documents, all documents in a given **topic cluster** are merged into a **single text**.
2. **Compute Term Frequency (TF) for the Topic**:
    
    - Counts how often each word appears within **all documents assigned to a topic**.
    - The topic is treated as a **single large document** containing all words from its member documents.
3. **Compute Inverse Document Frequency (IDF) for the Corpus**:
    
    - Reduces the importance of common words that appear in multiple topics.
    - Ensures that words unique to a topic get a **higher TF-IDF score**.
4. **Extract the Top Words for Each Topic**:
    
    - The highest-scoring words become the **representative terms** for that topic.
    - This helps in ==**labeling the topic**== with meaningful words.

---

