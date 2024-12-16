![[Pasted image 20240929104204.png]]
![[Pasted image 20240929104305.png]]

How to formuolize this task?

![[Pasted image 20240929105259.png]]

In training time, the model only has access to the target class not other class. why? because it is what happens in the real world. Anomalies are not always seen while training. And model could be biases through some of abnormal data.

An ideal model should detect the distribution of normal data from abnormal data in testing stage.

- Dataset is very important.

--------------

![[Pasted image 20241210185216.png]]

How is SSL done? Through some pretasks.

![[Pasted image 20241210185733.png]]

![[Pasted image 20241210190038.png]]

This way we make the model to learn features (representation learning).
- This step is used to biuld our autoencoder.

---------------

![[Pasted image 20241210190150.png]]


Goal? Make the reconstructed data to be similar to input data using encoded data.
- The latent space becomes something "meaningful".
- If the decoder cannot reconstruct the input data well, that is anomaly data.

----------------

![[Pasted image 20241210190755.png]]

compress the shared features among training samples (normal data) into a sphere and detect the abnormal data if it locates out of sphere.

Weakness: why sphere? short cut learning (model pays attention to solve the task you defined in order to minimize the loss, without really understanding it)?
- For example model may map all input vectors to the center of sphere and solve the problem blindfuly.

==Always look for weaknesses of a paper or model.==

---------------------

Best approach:

![[Pasted image 20241210193736.png]]
![[Pasted image 20241210193738.png]]

It uses transfer learning: Detaches the last layer of a pre-trained model (Fully-connected layer) to turn it into a feature extractor to use its knowledge.

1. Map train data into feature space from pretrained model.
2. Apply KNN on the features of test data in this space.

-------------------

![[Pasted image 20241210194341.png]]

Teacher: Freezed pretrained model
Student: Zero-learned random-weight model 

- Student model tries to imitate teacher behavior in normal data, meaning to push student to have exactly the features extracted by teacher during training time.
- In the test stage, if both the teacher and student extracted the same features for a data, it is considered as normal.
- This is because student is idiot and far different from teacher in extracting the features of abnormal data.

==In each of the above approches, the loss function is defined differently.==

------------

![[Pasted image 20241213201056.png]]

- Each anomaly detection approach has its unique anomaly score, e.g. reconstruction loss in auto encoders.

------------------------

![[Pasted image 20241213204501.png]]
![[Pasted image 20241213204528.png]]

------------

When there is more than one normal class:
![[Pasted image 20241213204748.png]]

-------------------

When anomaly is the dataset the sample comes from (we have more than one **dataset**):
![[Pasted image 20241213205218.png]]
![[Pasted image 20241213205336.png]]

- ==OOD is a labeled classifying method while One-class is unlabeled.==

![[Pasted image 20241213205606.png]]
![[Pasted image 20241213210120.png]]

- MSP works with the enthropy of the probabilities.
- If the classifier is more confident about the probability it provides for classifying an input, the input might be normal. This concept in turned into a metric called MSP and is used in anomaly detection.

----------------

When we have access to outlier data while training the detector:
![[Pasted image 20241213210913.png]]

- This helps detector to generalize to new anomalies.
- This improves the MSP metric by making the model to act well (minimize loss) for normal data and uniform for outlier data.

----------------------

This method uses the encoder's embedding to measure the mean value of train data embedding representations, then computes the input data representation distance from this mean value.
![[Pasted image 20241213211128.png]]

---------------

