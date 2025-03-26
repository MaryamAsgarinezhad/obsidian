
What are different aspects of trustworthiness?

1. resistance to adversarial attacks in input: An attempt to modify the input of a model to largely impact the output of it.
2. toxic attacks to models: Modify training data to impact and disrupt the behavior of a model.
3. Monitoring: Diagnose training data not in the comfort zone of the model (disorder recognition methods)
4. Compositionality: The input has compositions not seen during training together but separately. 
5. Spurious correlation: Correlation between two factors only in training data, not in the validation or test data.

-----------------------------

![[Pasted image 20240907123833.png]]
![[Pasted image 20240907124005.png]]
![[Pasted image 20240907124445.png]]

- Our network must be resistant to these sort of attacks (adversarial distortion)
- This can be to abuse face recognition systems:)
- Not all adversarial distortions are imperceptible to human eye.

![[Pasted image 20240907124730.png]]
-------------------------------

How is this fooling process done?

- Little change to x, but very different output.
![[Pasted image 20240907124819.png]]
![[Pasted image 20240907125015.png]]

What causes this major change?

- little modifications to x effect the output cumulatively.
- ==Since epsilon for each pixel has the same sign as the weight of each pixel, the effect on output will not be breakevened.==
- ![[Pasted image 20240907130708.png]]
- Choosing this specific epsilon for this function, caused the desired fooling effect. ==So epsilon is not random:))==

---------------------------------

**Every model is prone this attack in it's own way, but why?** 

1. Overfitting
![[Pasted image 20240907131044.png]]

Complex and overfitted structure (like above) makes it possible for little noise to change the result.

2. Excessive linearity:
   ![[Pasted image 20240907135918.png]]
   ![[Pasted image 20240907135958.png]]
   Since, NNs are prone to adversarial distortion.

-------------------

How to build these adversarial examples systematically?
![[Pasted image 20240907140215.png]]
![[Pasted image 20240907140240.png]]
![[Pasted image 20240907140759.png]]

\
- This model modifies x a little and tries to maximize loss subject to its budget.
- Loss is very complicated because it's exactly contains the output of NN.
- So how to solve such a hard problem? ==**Gradient Ascent.**==
- **Note that here the optimization is based on delta, not w weights.**
- First we derive L with respect to delta using automatic differentiation (computational graph), then  update delta **in the direction** of gradient vector.
- But delta has a condition (<lp). 
- We use projection to solve this. ==Projection used here is based on the norm used in x(adv).==:
  ![[Pasted image 20240907143609.png]]
  ![[Pasted image 20240907144625.png]]
  ![[Pasted image 20240907144632.png]]

FGSM is a single step, fast but defendable attack.

---------------------------------------

Another attack is PGD:
![[Pasted image 20240907151500.png]]

--------------------------

Targeted vs untargeted attacks: 

- Targeted: min the loss with respect to the targeted class we want to misclassify
- Untargeted: max the loss with respect to the true class to which the input belongs to
- ![[Pasted image 20240908111926.png]]

-----------------------------------------

Not always the attacker has access to model parameters (loke when they only have model's api). Here we call it black box testing and attack.

But we can assume white box situations in some cases. ==It is important to know which one , balck or white, did we assume.==

![[Pasted image 20240908112917.png]]


---------------------------------

**What are defense methods?** 
![[Pasted image 20240908112957.png]]
**Temprature** definition in softmax function:
![[Pasted image 20240908113458.png]]
- Temperature in softmax makes he output more uniform (the bigger the temperature, more uniform it become)
![[Pasted image 20240908113952.png]]
  - Step 1,2: Teacher network learns to classify data correctly (but softly different) even if logits are larger. By testing this network to each data in training set, we get soft lables.
- Step3: Distilled network gets trained using soft labels instead.
- Step4: Test distilled network using temp 1 (**that causes gradient vanishing**)
------------------------------------------

- Temperatured softmax makes logits larger, why?
  ![[Pasted image 20240908114420.png]]

- This network is resistant to PGD and FGSM.
- But it's very prone to **gradient vanishing** because the network is trained with large logits, but suddenly emprature get small and consequently:

- ![[Pasted image 20240908124117.png]]

-----------------

Remember:

==_Logits_ are the outputs of a _neural network_ before the activation function is applied. They are the unnormalized probabilities of the item belonging to a certain class.==
  The softmax function is then applied to the logits to normalize them to sum to 1. This ensures that the probabilities of all classes add up to 1 and that the most likely class has the highest probability.
  
  ![[Pasted image 20240908115103.png]]
  Softmax finds its application in multi-class scenarios, where multiple classes are involved, assigning probabilities to each class for comprehensive classification. On the other hand, Sigmoid is tailored for binary classification tasks, focusing on distinguishing between two exclusive outcomes with probability mapping.

------------------------------------------

Alternative for defensive distillation:

**Use T= 100 instead of 1 even in the last layer. This is used when we are in "white" state and have access to models' weights. Thus, we can modify the loss function we want to minimize with these new assumption to generate effective adversarial data.**
![[Pasted image 20240908182207.png]]
==Fragility of using loss function as the objective to minimize.==

----------------------------

Adversarial trainig:
- Note that x(adv)s are computed after each epoch of training, based on the current weights of network.
![[Pasted image 20240908184136.png]]
![[Pasted image 20240908185223.png]]

----------------------------------

How to recognize a defense algorithm is really defensive? (e.g. adaptive attack like what we did for distillation)
![[Pasted image 20240908185453.png]]

--------------------------

**Black box attack:**

Transferability means creating x(adv) based on a different model than the one we aim to attack. This is almost successful!
![[Pasted image 20240908190128.png]]

So attacker does not always need to know model's weights.

----------------------------------------------

Improving adversarial training to boost its precession by **optimizing dataset**:
Transfer learning (using weight of a pretrained model to do another training)
![[Pasted image 20240908190315.png]]
![[Pasted image 20240908190355.png]]

-------------------------------

Text attacks:
It is harder because the input is descrete, while image inputs are continuous.
![[Pasted image 20240908190611.png]]
![[Pasted image 20240908190618.png]]

------------------------------------------------
