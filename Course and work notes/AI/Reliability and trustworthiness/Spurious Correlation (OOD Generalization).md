![[Pasted image 20241213212926.png]]

Generalization Error:
![[Pasted image 20241213213033.png]]
But this hypothesis doesn't always exist, and we may face a **distribution shift**.
![[Pasted image 20241213213509.png]]

--------------

![[Pasted image 20241213213535.png]]


![[Pasted image 20241213213835.png]]
![[Pasted image 20241213215535.png]]
![[Pasted image 20241213220029.png]]

S: Being real or painting is the s feature here :
![[Pasted image 20241213220622.png]]

T:
![[Pasted image 20241213220644.png]]

![[Pasted image 20241213220742.png]]

---

![[Pasted image 20241213221601.png]]

PMI: Label predictability based on core feature. This term is fixed and does not cause distribution shift. (bacause Xcore features are fixed)

Attribute:
![[Pasted image 20241213222155.png]]
SC:
If the numerator of this term for training data is much larger than the denominator (attribute is large at training time), means that feature a is more prevalant in training data than test data. 
- So actually the model is relying on feature "a" for predicting y. ==If "a" fades away (not X-core), the prediction results will become different.==

AI:
If one attribute is more prevalant than the other in training time, prediction accuracy of the model drops on test time for input with less prevalant attribute.

AG:
One attribute exists in training time but not at testing time (==Diversity Shift==)

Class:
You have imbalanced population of data classes. (10% cat - 90% dog)

----------------------

Spurious Correlation:

When an attribute is not actually part of the input's class features, but correlated to it in training dataset.
![[Pasted image 20241213224406.png]]
همبستگی علی نیست و جعلیه.

**This mostly happens in shortcut learning.**

------------------------

Group: Each of z1 or z2 features (being a cow or grassland background)

Majority data: Their spurious feature is correlated with label (like sea birds)
Minority data: vise versa

-------------

How to become robust? Train the last layer
![[Pasted image 20241213225052.png]]

Observation: ==Removing background (FG-Only) while testing, improves worst acc.==
The point is no matter how much spurious features the model learns, it also learns core features, **but you need to ummit those features in testing time to see how well the model learned core features.**
![[Pasted image 20241213225257.png]]


So we can retrain only the last layer of model on Balanced (50%) data and then worst acc jumps :
![[Pasted image 20241213231004.png]]

Worst group accuracy(Worst Acc):
Accuracy of the least accurate group (majority and minority)

Balanced 50%: Half of data has correlated spurious feature with label, and half not.

------------------

So, what if we don't know about spurious features and minority groups?

Upweight underperforming samples:
![[Pasted image 20241213231706.png]]

![[Pasted image 20241213231740.png]]
![[Pasted image 20241213231904.png]]

------------

When is OOD- generalization possible?
![[Pasted image 20241213232454.png]]

==Intuition:==

![[Pasted image 20241213232632.png]]

**definitions:**
![[Pasted image 20241213232808.png]]

Our **extracted** features must be **epsilon-invariant** and **informative**.

- Informativeness: how well your model can distinguish between each two classes (labels)in the worst distribution (in which the model has least distinguishing ability).

-------------------------

![[Pasted image 20241213233633.png]]
![[Pasted image 20241213233956.png]]

???

----------------

![[Pasted image 20241213234301.png]]

We need more information and assumptions.

![[Pasted image 20241213234418.png]]

---------------


