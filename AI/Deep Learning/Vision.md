
Some examples of vision usage:
![[Pasted image 20240820175110.png]]
![[Pasted image 20240820175147.png]]

what does the computer see from an image? intensity of the light in each pixel
![[Pasted image 20240820180016.png]]


So, how about colors? 3 aligned matrix of these three base colors, then they will be combined and the final image is created.
![[Pasted image 20240820180214.png]]


-----------------------

Classical techniques of image processing, which the current methods are based on:
![[Pasted image 20240820180717.png]]
Image thresholding works based on the light severity, edge detection works based on the comparision between a pixel and its neighbors. It is measured using:
![[Pasted image 20240820181245.png]]
Covolution shows a weighted sum from the neighborhood of a point.

![[Pasted image 20240820181909.png]]
different kernel, different processing tasks.

![[Pasted image 20240820182406.png]]


-------------------------------------
![[Pasted image 20240820182750.png]]
each layer adds a bit of more complexity to the features we can extract.
The input of the NN will be along vertical matrix from the data. This way we lose some data from the neighbourness of points.

![[Pasted image 20240820183403.png]]

So we need the NN to have below features:
![[Pasted image 20240820183701.png]]

**How to create this feature? convolution** 
instead of applying the MLP on the whole vertical data, apply the same weights on smaller patches.
![[Pasted image 20240820183835.png]]
- So instead of using MLPs, we can apply certain convolution filters, and **==then the goal is to learn these filters.==** Each neuron becomes a convolution filter

![[Pasted image 20240820184626.png]]


Then we can accumulate several filters together (that builds a convolutional layer) to extract certain features. These are the building blocks of CNNs.

![[Pasted image 20240820184821.png]]


How to make a CNN from those? adding activation funcs between those layers:
![[Pasted image 20240820185041.png]]

![[Pasted image 20240820185047.png]]


Note: ==Convolution is a feature learning mechanism in visual networks.== And it does this task through **==multiple layers==**.

The goal of this CNN is to extract complex features and the apply MLPs on these features for certain tasks (that was described initially at this course).

![[Pasted image 20240820185412.png]]

---

Different settings (hypermarameters) of convoltion:
![[Pasted image 20240821111155.png]]
![[Pasted image 20240821111222.png]]

Padding is used to make the output of a filter to the desired size as the input (by matching the center of filter to the center of input)

-------------------------------------------------

Pooling Layer:

- Its goal is to choose ==representatives== from different areas of the input by choosing the stride more than 1. (this will avoid extra overlapping between the inputs of filter that lead to similar outputs, thus extracting features or representatives from different areas)
- In this example we learn the wights of convolution.
![[Pasted image 20240821111853.png]]

Another instance of convolution (unlearnable):
![[Pasted image 20240821122802.png]]

----------------------------------

Recieptive field:
![[Pasted image 20240821125901.png]]

As we go forward with applying further layers, the receptive field becomes larger, and our final goal is that receptieve field becomes the whole input.
![[Pasted image 20240821133157.png]]

This design has less parameters for using convolutional networks and is more generalizable for being shift invariant.
![[Pasted image 20240821135151.png]]

-----------------------------------

Model description:
![[Pasted image 20240821135951.png]]

- Batch is 128 and means they can process 128 images at the same time due to using GPU.
- Dropout=0.5 means we turn off the output of half of the convolutional layers. 

![[Pasted image 20240821144800.png]]

In the above model, the primary goal is to understand what is the meaning of "active" output of a layer? what features make the output of certain filters active?
- They found that some hyper  parameters of Alexnet might not be precise for this purpose.
- The architecture as exactly the same as Alexnet, but hyper parameters are tuned for the above purpose. (5% better results)

![[Pasted image 20240821150050.png]]

The above model has most of its parameters in its fully-connected layers.
GoogleNet modle solves this problem.
![[Pasted image 20240821152612.png]]
 It solves the gradient vanishing problem using additive classifiers between layers once a while.
It also umits fully connected layers by "global average pulling".
![[Pasted image 20240821152827.png]]

- Global average pulling: suppose that the output of your filters is a n by n matrix. instead of feeding it into a fully-connected network, take an average respecting one dimension and feed it into one connected layer.
- It reduces the dimensionality and is used for evaluating feature extraction process. It also uses inception module, performing parallel convolutions with different kernels.

![[Pasted image 20240821181156.png]]
Thus it is done on with several receptive fields and can learn more features, but with less speed.

Another model:
![[Pasted image 20240821181447.png]]

**How does it solve gradien vanishing?** By using residual blocks (fx+x)

![[Pasted image 20240821181557.png]]

Initially, it is supposed that deeper NNs are more generalizable and have less overfitting. But optimizing these networks are so much harder. Solution?
![[Pasted image 20240821182814.png]]

This solution helps a lot with the training process and optimization. Later, this leaded to the idea of **transfer learning**.
![[Pasted image 20240821183505.png]]

- The point is shallow wieghts are more general and can be assigned to every dataset. Then you train residual weights.
![[Pasted image 20240821183915.png]]

-----

