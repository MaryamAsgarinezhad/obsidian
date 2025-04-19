- feature engineering
- model selection
- we want to avoid overfitting and underfitting

We cannot predict a random noise, we have to assume the data has a specific trend.

**Inductive bias**: 
The inductive bias of a learning algorithm is the ==set of assumptions that the learner uses to predict outputs of given inputs that it has not encountered==. Inductive bias is anything which makes the algorithm learn one pattern instead of another pattern.

-------------------------

Supervised learning: predicting the output based on labeled data (regression, classification)

Unsupervised learning: finding certain patterns and hidden features in the data(clustering, dimension reduction)

If data has labels already within it, it's called self-supervised learning. (not sure)

Reinforcement learning: learning the next action based on the reward received through time.

- In clustering you have to decide the classes (not previously defined), while in classification, you have to predict the predefined classes.

------------------------------------

![[Pasted image 20240803124108.png]]

------------------------------------

Every new learning method is a new inductive bias that we should consider before choosing that method. The solution's inductive bias should be compatible with the problem's inductive bias.

- Loss function differs for every method, or multiple for a single method (like MSE and MAE)
- Most of the time we use gradient decent method to minimize the loss function (instead of absolute derivation=0). Gradient descent is a function that shows the most increasing direction of the function in every point.
- In gradient descent we start with a random answer for the wights, and then update weights in the opposite direction of the gradient descent.
- **The amount of updating weights in every step is a hyper parameter called learning rate.**
  
- ![[Pasted image 20240803130331.png]]
  
- We decrease learning rate by time.

![[Pasted image 20240803124215.png]]
![[Pasted image 20240803124236.png]]

![[Pasted image 20240803150937.png]]

- what is the amount of k? until the sum of distances between a center and other cluster members almost converges.
- does the primary location of cluster centers affect the convergence process? yes
-------------------------

Solve overfitting:

- More training data
- Simpler model
- ==Regularization== (different from normalization): to improve generalization and solve overfitting

------------------------------

**Logistic regression** (for classification):

![[Pasted image 20240803154351.png]]

We use logistic (sigmoid) function to model the probability of an event based on some independent variables. ==Then we can use the probability output to classify between two classes.==

![[Pasted image 20240803160054.png]]

How to learn B0 to Bn? By a learning process (gradient descent) and the below loss function.

Cross entropy:
![[Pasted image 20240803161549.png]]

Yi is the real class of the i'th data.

![[Pasted image 20240803161756.png]]

**==Reliability== means if the features change a bit, the output doesn't change dramatically.**

-------------------------

**Model Selection:** 

It's not correct to try all models and choose the best one that has the best result on the data. We have to select the model ==based on the inductive bias of the problem.==

e.g. logistic assigns different weights to the features rather than KNN.

Also complexity, interpretability, and performance of the model is important.

----------------------------------------------------

Evaluation metrics:

We may have biased data, all belonging to class 1.
In this case we need other evaluation metrics:

![[Pasted image 20240803162906.png]]


----------------------

Data splitting:

- Train data to find coefficients - 80%
- Test to test the final results - 10%
- Validation for model selection or hyper parameter selection - 10%

Before splitting we shuffle the data to make sure they have same distribution. To avoid potential different distributions we use ==(k-fold) cross validation.==

-------------------------------------

- Every data point can be represented as a vector in a feature space.
- We can choose between more appropriate and useful features of a vector(feature engineering)

**Classification**:

Linear classifier:
![[Pasted image 20240807202811.png]]

Note: w is the vector prependicular to the classifier plane.

- Data points might not be linearly classifiable. Otherwise, there are many planes that classify them.
- By using feature engineering (extracting numerical features from data), points might become linearly classifiable.
- Neural networks do the feature engineering themselves.
- ![[Pasted image 20240807203903.png]]
- Support vector definition: we aim to find a classifier that has the largest margin to all points. 
- ![[Pasted image 20240807204117.png]]
- ![[Pasted image 20240807204801.png]] ![[Pasted image 20240807205005.png]]

-------------------------------------

- If most of the data points are classifiable bot not all of them, we can let some points to have negative margin (**Soft Margin SVM**):

![[Pasted image 20240807205904.png]]

Here "C" is a hyperparameter that controls how much softness we let the model to have and is found using cross validation.

Zetha(i) are called slack variables.

------------------------------------

If data points are ==not intrinsically **linearly** classifiable==: (Kernel SVM)

![[Pasted image 20240807210701.png]]
![[Pasted image 20240807210730.png]]
Since Phi can be really adapted to training data, we may overfit by using kernel SVM.

![[Pasted image 20240807211042.png]]

-----------------------------------------------

**Neural Networks:**

- Back propagation is the gradient descent for neural networks.
- Machine learning vs Deep learning:
- ![[Pasted image 20240808165537.png]]
- When dealing with big data, deep learning performs better. If we have less data and separated explainable features, then we use ML models. ==Regression is a part of ML.==

Deep learning: Neural networks with many layers.
- GPU and big data sources are why neural networks became pervasive.
  ![[Pasted image 20240808170032.png]]
- logistic regression made with a perceptron:
  ![[Pasted image 20240808170241.png]]

- It is proved that we can create any smooth function by increasing layers and perceptrons in a neural network. So it can solve any non-linear problem.
  ![[Pasted image 20240808170713.png]]
- Increasing the **width** of a neural network can cause overfitting not the **depth** of it.
- Every perceptron is considered as a feature. Deeper perceptron (that are nearer to the output of the model) get more complex (being a tail), rather than shallow perceptron (being an angle)
- ![[Pasted image 20240808171053.png]]
---
Every neuron extracts a feature, how? 

![[Pasted image 20240808171451.png]]

For the loss function to be **derivable**, we use other activation functions instead of step function:

![[Pasted image 20240808172523.png]]

Gradient descent algorithm:

![[Pasted image 20240808172912.png]]

-----------------------------------------

In neural networks, loss is a function of the output of network. how to calculate the gradient? 
![[Pasted image 20240808173849.png]]

**chain rule (automatic differentiation) and computation graph.**

![[Pasted image 20240808180712.png]]

**Back Propagation**: Libraries like pytorch get the computational graph and do the automatic differentiation themselves. 

![[Pasted image 20240808181215.png]]

This chain multiplication may cause gradient **vanishing** (multiplication of zero or near zero numbers) and gradient **explosion**.
By using ==ReLU==,  since it has the constant 1 derivation, vanishing will not happen.

----------------------------------------------

How to deal with local mins in GD?
- **by over parametrization (more wights and neurons and layers), we can guarantee that almost every local minimum is near to the actual minimum. This way we have many enough local mins.**

---------------------------------------------

Instead of computing gradient for all points, do it on a mini batch of data.

- Full-batch gradient descent uses the ==entire training dataset== to compute the gradient of the cost function. **This ensures the direction of the steepest descent is the most accurate.**
- In the case of mini-batch SGD, a small subset (mini-batch) of the training data is used to compute the gradient, balancing between the efficiency of SGD and the accuracy of full-batch GD.
![[Pasted image 20240808202739.png]]

SGD can avoid local min because its more stochastic. 

![[Pasted image 20240808203911.png]]
![[Pasted image 20240808204648.png]]

utilize the average of previous gradients as the updating value, this can solve **saddle point and local min trap**.
![[Pasted image 20240808204841.png]]

This can also help in reducing frequency of loss function. 
![[Pasted image 20240808205327.png]]

---
### RMSProp: separate learning rate for each direction

![[Pasted image 20240808205633.png]]

Combine the two:
![[Pasted image 20240808205819.png]]

---------------------------------------------------

Choosing the learning rate:
![[Pasted image 20240808210251.png]]
![[Pasted image 20240808210325.png]]

-----------------------------  

Regularization: to improve generalization and solve overfitting

![[Pasted image 20240808213711.png]]

like ridge and lasso: (norm and norm2 of weights)
![[Pasted image 20240808213802.png]]

----------------------------

Hoe do decrease the variance?
![[Pasted image 20240808215146.png]]
![[Pasted image 20240808215306.png]]
![[Pasted image 20240808215452.png]]
![[Pasted image 20240808215701.png]]

-----------------------------------------
### Federated Learning

Federated learning is a distributed approach to machine learning where the model is trained across multiple devices or servers holding local data, without sharing the data itself.

Federated learning (often referred to as collaborative learning) is **a decentralized approach to training machine learning models**. It doesn't require an exchange of data from client devices to global servers. Instead, the raw data on edge devices is used to train the model locally, increasing data privacy.

Wrapping up, we can say that distributed learning is about having centralized data but distributing the model training to different nodes, while **federated learning is about having decentralized data and training and in effect having a central model**.

---

### Loss functions:

Cross-enthropy:
- Mostly used for discontinuous output

MSE:
- Mostly used for continuous output

KL Divergence:
- Mostly used for distributional loss (observed and inferred distributions)

---

