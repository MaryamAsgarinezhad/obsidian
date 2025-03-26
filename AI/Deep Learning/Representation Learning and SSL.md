![[Pasted image 20240826164347.png]]
It demonstrates what kind of patterns the model have extracted from the input, which is shown in a layer near to the output. (e.g. a vector of size 128)
![[Pasted image 20240826164821.png]]
A good representation learning model must extract the meaning related features no the details.![[Pasted image 20240826165319.png]]
![[Pasted image 20240826165434.png]]
- Neural networks get good results if trained on a large dataset.
  
- There are multiple explainability and interpretability methods that demonstrate from what parts of the input, the model knows what to generte as a response. These methods tell us that supervised models are prone to shortcut learning, meaning they can mistakenly pay attention to wrong but common features in a group of inputs to generate a response.

----------------

How is SSL done? Defining pretext tasks exampled below. By doing these tasks, model is obliged to learn the patterns to a good extent and represent them well.

![[Pasted image 20240826170448.png]]
Then we will use this model, that works good for representation, to evaluate the main task (or downstream task). The evaluation can be done supervised (then the method is semi-supervised)
![[Pasted image 20240826173411.png]]
![[Pasted image 20240826173635.png]]

----------------------

SSL methods:
![[Pasted image 20240826174310.png]]
![[Pasted image 20240826180341.png]]
we can see the below rotation task as a 4 category classification:
![[Pasted image 20240826181610.png]]

---------------------------------

If we have so much data, supervised works better, but when we lack data, semi-supervised is very useful:

- Note that the explained semi-supervised learning method is an example of transfer learning.
![[Pasted image 20240826182037.png]]

----------------

Pretext task example:

1. relative location of two patches to each other

![[Pasted image 20240826182535.png]]

2. relative location of all patches to each other
![[Pasted image 20240826182707.png]]

![[Pasted image 20240826182816.png]]
![[Pasted image 20240826182849.png]]

4.  This method is not very useful because it compresses the whole information (including detailed ones) needed to reconstruct a pic, while we need only semantics. **Here we can give the incomplete input and expect to reconstruct the original pic.**
![[Pasted image 20240826182900.png]]
![[Pasted image 20240827123306.png]]
![[Pasted image 20240827123407.png]]
-----------------------

Contrastive learning:
![[Pasted image 20240827123532.png]]

- Representation learning means to find a good embedding for each input. In contrastive learning we aim to enclose the representation of an input to its positive pair and distance it from its negative pair.
- ![[Pasted image 20240827150555.png]]


- Positive pairs are generated using **data augmentation**.
   Every input except the dataitself is considered negative. What is the metric for similarity here?
-  ![[Pasted image 20240827151710.png]]
- And the loss function NN is (==Every input except the dataitself is considered a negative pair==):
- ![[Pasted image 20240827152657.png]]
- So the loss function is dependent to batch size:
  ![[Pasted image 20240827153325.png]]

A sample model: Its a semi supervised model and supervised training (above contstrative loss function) is applied to **network f (not g)** that is more general with less probability of overfitting to the supervised data. Here f and g extracts a representation.
![[Pasted image 20240827164320.png]]
![[Pasted image 20240827165325.png]]
![[Pasted image 20240827165349.png]]

Abstract : Augmentation (the i'th green is the same and augmented of i'th grey), enclose positives and distant negatives by applying loss function

-----------------------------

Representaion learning helps us to use freezed encoders:
![[Pasted image 20240827172516.png]]
It works better than supervised:
![[Pasted image 20240827172749.png]]


---------------------------

The more the batch size, the more percise the outputs become:
![[Pasted image 20240827172844.png]]

But large batch size makes training difficult. There are methods to deal with this problem.

![[Pasted image 20240827173059.png]]

![[Pasted image 20240827173150.png]]

We got a feature bank that stores negatives from each epoch being trained, to a limit of window.==This way we do not need to update weights for previous negatives that have gone through the network before, but we update the weights of momentum encoder by momentum (moving average).==

- wq is weights of encoder network and wk is the current weights of momentum encoder.
- This method solves memory need while hving an accurate performance.

----------------------

Another sample:
![[Pasted image 20240827185702.png]]

It deletes negatives while preventing the network to collapse.
![[Pasted image 20240827185926.png]]
It only encloses positives and moments to the target. it is proven that it won't collapse.
![[Pasted image 20240827190205.png]]

---
Another:

![[Pasted image 20240827190426.png]]
![[Pasted image 20240827190608.png]]


What is the value of target function?