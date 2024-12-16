
- Load the dataset using pandas library.
- By using pandas .describe() function, you can see statistics of each feature and measure if they are in the **same scale** or not, thus if you need normalization or not. Also look for **corelation matrix**.
  Or check if the dataset is **balanced** or not.
  ![[Pasted image 20240809220625.png]]

- Do not forget to split the data (test, train, validation) using scikit learn.
- Also use scikit learn to import several kind of regression models (like linear regression)
- We can use scikit learns's PCA for dimension reduction of large data inputs. W e do the dimension reduction form 784 to 2 to be able to visualize the clustered data in 2 dims.
  ![[Pasted image 20240809215204.png]]
- K-means algorithm also exist in scikit learn library.
- Scikit learns provides various validation metric calculation, like accuracy, precision, recall, etc and a classification report to report all those metrics.

------------------------------

In PCA, it is important to avoid losing information in the reduced data, by preserving the data points' variance. So we map all point to a plane that data points have the largest variance in that direction.

![[Pasted image 20240809215929.png]]

------------------------------------------

Binary classification using a neural network:

- WE use pytorch library.
- torch.nn to define the network and torch.optim to minimize the loss.
- And scikit learn for data creation (make_blobs), etc:
  ![[Pasted image 20240809222138.png]]

- To use pytorch, **you have to transform your data to tensors** and to use GPUs for computations.
- Defining a neural network using pytorch:
  ![[Pasted image 20240809222459.png]]
  choose loss and optimizer:
  ![[Pasted image 20240809224032.png]]
  
  trainig loop (set the model to train mode for epoch numbers, calculate the loss for each output, derive the loss bt loos.backward and optimize, .zero_grad makes the gradient zero in each step)
  ![[Pasted image 20240809225416.png]]
Test step (set the model to evaluation)

--------------------------------------------------

Pytorch:

- Tensor is sth to help in fast matrix operation.
- .device and .requires grad(enables derivation from that variable and the derivation value then stored in x.grad) for tensors
- You can also use numpy arrays but they can't be used with GPUs.
- When we do derivation and polynomials in torch, **it is automatically done with computational graph.**
  ![[Pasted image 20240809235121.png]]

 - x.grad is cumulative and is added to every time we compute y.backward.
 - If we want to do some operations that do not need gradient and computational graph, we use "with torch.no_grad": 
   It is more efficient
   ![[Pasted image 20240810000617.png]]

- Another way of defining NN:
  ![[Pasted image 20240810001127.png]]
-----------

```python
from sklearn.manifold import TSNE

  

# Sample a subset of the data for t-SNE due to computational constraints

sample_data = df.sample(n=5000, random_state=42)

sample_features = sample_data.drop(columns=['Class'])

sample_labels = sample_data['Class']

  

tsne = TSNE(n_components=2, random_state=42)

tsne_results = tsne.fit_transform(sample_features)

  

plt.figure(figsize=(5, 4))

sns.scatterplot(x=tsne_results[:, 0], y=tsne_results[:, 1], hue=sample_labels, palette='bright')

plt.title('t-SNE Visualization of Transactions')

plt.xlabel('t-SNE Component 1')

plt.ylabel('t-SNE Component 2')

plt.show()
```

This code is used to **visualize high-dimensional data** (such as transaction data) in a two-dimensional space using t-SNE, a popular technique for visualizing complex data. By reducing the data to two dimensions, you can visually inspect how the data points are grouped or separated, which can provide insights into patterns, clusters, and relationships in the data, especially when the data points are colored by their class labels.

--------------------------------------------

```python
from sklearn.ensemble import IsolationForest

  

copy_df = df.copy()

copy_df = copy_df.dropna()

  

iso_forest = IsolationForest(contamination=0.01, random_state=42)

outliers = iso_forest.fit_predict(copy_df.drop(columns=['Class']))

  

copy_df['Outlier'] = outliers

  

plt.figure(figsize=(5, 4))

sns.scatterplot(x='V1', y='V2', hue='Outlier', data=copy_df.sample(n=5000, random_state=42), palette='coolwarm')

plt.title('Isolation Forest Outlier Detection')

plt.show()
```

This code is used to detect and visualize outliers in a dataset using the Isolation Forest algorithm. Outliers are points that differ significantly from the majority of the data and can indicate anomalies, fraudulent transactions, or errors in data collection. The final plot provides a visual representation of how these outliers are distributed in the feature space defined by `V1` and `V2`.

-------------------------------------------

