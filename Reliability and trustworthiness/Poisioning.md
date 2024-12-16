![[Pasted image 20240909105847.png]]

If any of below factors disappear, we have no security:
![[Pasted image 20240909110036.png]]
E.g. in distillation algorithm we did not have integrity because the behavior of the model changed unexpectedly (T=1)

-------------------------
![[Pasted image 20240909110247.png]]
Evasion attacks are attacks to signals and voices.
It can make a speech perceived as insulting by adding noise to it.

---------------------------------

Poisoning vs adversarial attacks:
![[Pasted image 20240909110814.png]]
Poisoning gives corrupted data while training to the model.
Extraction is used to steal a model's parameters.

-------------------------------------------

![[Pasted image 20240909111547.png]]
Infected model is poisoned. **When we give it a pseudo normal input, it return our desired output to change a model's behavior.**
![[Pasted image 20240909111718.png]]
Since sometimes we get training dataset from an unreliable source, we may be prone to poisoning.

![[Pasted image 20240909112001.png]]

We aim to keep test accuracy unchanged (for not being easy to mitigate) while having the ==controler== for targeted misclassification (described above).

![[Pasted image 20240909112705.png]]
![[Pasted image 20240909112746.png]]

-----------------------------------------

How is poisoning done?
![[Pasted image 20240909113023.png]]
- We add backdoor to a data and make its label to our desired targeted label.
- **Since the model gives a correct answer for the original image, test accuracy does not drop.**
- So we have controller to attack the model.

**Q:** How much of the training data must be poisoned to reach our goal?
![[Pasted image 20240909113555.png]]

Flaw of this method: Creator of the model can understand backdoored data by checking it. We need a method that would be imperceptible for the creator to understand whether we have poisoned data or not.

![[Pasted image 20240909113901.png]]
 
- This method learns the backdoor and outputs based on backdoor, and does not change labels. If we have backdoor corresponding to airplane, it outputs airplane.
- **But this is hard to learn. How we learn it?** 
-  We make adversarial inputs (by ==GANs==) that are hard to understand for the model, so it has to learn simpler patterns like backdoor.
- ![[Pasted image 20240909132213.png]]
- ![[Pasted image 20240909132217.png]]
- Te more perturbation the input has, the more it learns backdoors and the more accuracy we will have. 
- Backdoors should not be human perceptible.

-----------------------------

**Transfer learning:**
![[Pasted image 20240909133219.png]]
Only change the last layers' parameters to exploit previous weights by freezing them. (fine tuning). We assume primary layers are feature extractors that can be used in any case.

-----------------------

![[Pasted image 20240909153727.png]]
Only the last layer is fine tuned in this method (single poison image).

![[Pasted image 20240909154038.png]]
How is this real scenario done?
- Evasion and backdoor attacks are not applicable.
- **So we need triggerless data poisoning attack.**
- ![[Pasted image 20240909154525.png]]
- We generate a target instance that is similar to the "clean base instance" in the **visual space** but similar to "clean target" instance in the **feature space**.
- In the right image, we want the target point to be classified as clean base not clean target, so we use a poison point that is like clean base (visually) but near clean target (feature).
- **==The point is the initial decision boundary is shifted after training on poisoned point, in order to misclassify the target point in test time.==**
- ![[Pasted image 20240909160630.png]]
What is the flaw of this method? The network does not change its boundaries for the poison of all targets that will miisclassify a lot of previous classified points. So **We have to choose the target near the classification boundary.**

![[Pasted image 20240909161220.png]]

So why did we use last layer transfer learning in this method?
Because we want the single poison data to shift the boundary. This is less possible to happen in End to end learning.
![[Pasted image 20240909164809.png]]

-------------------------------------

Session2 
![[Pasted image 20240911121059.png]]
![[Pasted image 20240911121120.png]]
![[Pasted image 20240911121142.png]]
**Ways to purify poison:**
![[Pasted image 20240911121931.png]]
![[Pasted image 20240911122044.png]]
We need to set some limiting hypothesis to make purifying process easier and possible.
1. Triggers should not change the concept of inputs (trigger is the input that gives us our desired result)
2. Our problem is classification and we aim to misclassify triggers to target class.
![[Pasted image 20240911123419.png]]
We use the abovementioned attributes (uncommon tendency, ...)

----------------------------------------

![[Pasted image 20240911152630.png]]
![[Pasted image 20240911152656.png]]
**These types are important.**
Below are factors to be considered when attacking:
![[Pasted image 20240911153224.png]]

--------

**Blend attack:**
![[Pasted image 20240911153714.png]]

- Blend the blurred pixels of a background, which is applied to all samples(sample agnostic, not sample specific), with the input and change the label (poison labeled attack). Here background is our trigger.
- **Triggers are blurred to reduce perceivable risk described above.**
- This is a digital attack, since we blended all of background's pixels together not pat of it.
- To make it physical, we add not all pixel but part of it, like a **blended glasses**.

-------------------------

![[Pasted image 20240911172235.png]]
The goal is to change input in a way that without poisoning the label, it misclassifies the target.

- Adding adversarial noise should make **main image harder to recognize than target backdoored pixels.**
- Backdoored pictures is similar to it's label apparently (in visual space), but actually similar to the target label in feature space.
- ==So it learns to classify what is apparently bird into not bird.==

-----------------------------------------

![[Pasted image 20240911175014.png]]

-------------------------------------------------------

Other attacks:
![[Pasted image 20240911175330.png]]

This methods add a slightly different between real and poisoned data that is imperceptible. This is also clean labeled.

--------------------

![[Pasted image 20240911180622.png]]
- In this method, we train a network to generate a trigger for each input.
- The network aim to generate **distant outputs** (or triggers).
- It is also poison labeled.
![[Pasted image 20240911182249.png]]
![[Pasted image 20240911182335.png]]

--------------------------------------

![[Pasted image 20240911182350.png]]
It is poison labeled and the input is triggered by transferring pixels slightly in the input.

----------------------------------

![[Pasted image 20240911182730.png]]

In this method, the triggered input changes is so imperceptible.
It modifies frequencies of pixels in the fourier space of the input.

-----------------------------------------------

![[Pasted image 20240911183059.png]]

--------------------

Poisoning vs backdoor:

- poisoning: reduce accuray
- backdoor: misclassification of targeted cases

------------------------

**Defence methods against attacks:**

![[Pasted image 20240911183721.png]]
- Prune doubtful and unusual neurons, fine tune on a clean data.
- Remove neurons until they don't reduce pecesion of model.

--------------------------

![[Pasted image 20240911184005.png]]
![[Pasted image 20240911184946.png]]
- We generate filters that change the label of any input to a certain value, except the infected ones (==that results in an irregular output with low enthropy==). This helps detecting targeted labels that were attacked.
- Then we can detect triggers and remove them from dataset.

**This method does not work for sig and fourier attacks because their trigger is distributed over whole input.**

----------------------------

![[Pasted image 20240911185010.png]]

- Detecting the left one is harder and has larger loss.
- Also, its loss decreases slower and harder than the right one.

![[Pasted image 20240911185206.png]]

- Train a network once on normal data and once on triggered data. **Loss of backdoored inputs decrease so much faster than normal ones.**
- Now you know backdoored data.
- Also, in this method in each epoch, if loss is less/more than 0.5 we use gradient ascent/descent in the next epoch.
- Clean inputs losses stay 0.5, but backdoors loss go to zero.
- So we can separate between clean and backdoored.
  
![[Pasted image 20240911190125.png]]
- We repeat this sort of training to 20 epochs to separate data.
- Continue training with gradient decent on normal data and gradient ascent on infected data.
- So no need to remove any data, due to ==unlearning infected data!==

---------------

![[Pasted image 20240915114600.png]]
Closest bad model to a backdoored model? 
Model that **tends to** classify every data to target class. Call this model A.

By using A, loss is increased and you see most samples being misclassified, are classified into target class. So even if we add an adversarial noise to the data, it tends to be classified to the target label and we recognize target label.
![[Pasted image 20240915121050.png]]
Now we prune neurons that cause noisy data to be misclassified into target label.

---------------------------------

![[Pasted image 20240915121206.png]]
- Modify some inputs to show multiple labels at the same time ,but do not modify the trigger.
  Normal model's prediction has high entropy, despite poisoned model.
  
![[Pasted image 20240915123310.png]]

----------------------------------

![[Pasted image 20240915123507.png]]
- Here the shortcut can be grasses.
- In backdoor attacks, shortcut features are predicitive (causal)

-----------------------------

![[Pasted image 20240915124653.png]]

Pruning effective attack: Make neurons to learn both clean and backdoored data.
![[Pasted image 20240915124841.png]]

Instead of adding the whole trigger to an input while training, add stochastic parts of it, but add the entire trigger during test time. This attach will be ABL adaptive.
![[Pasted image 20240915124857.png]]

![[Pasted image 20240915125737.png]]