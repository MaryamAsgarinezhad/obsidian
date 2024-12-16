![[Pasted image 20241214185650.png]]
![[Pasted image 20241214190109.png]]

When no suitable inductive bias is used, probably we will have shortcut-learning.
- It memorizes, rather to learn.

![[Pasted image 20241214190354.png]]
- GPT performed better in answering mathematical operations including numbers  it had been trained on more at train time.

![[Pasted image 20241214190607.png]]
![[Pasted image 20241214190729.png]]
**Models lack the ability to combine several concepts.**

![[Pasted image 20241214190908.png]]
![[Pasted image 20241214191101.png]]

In this example, since the NN lacks OOD, it cannot predict y correctly for unseen x.
Why our models lack OOD? wrong primar hypothesis:

![[Pasted image 20241214191146.png]]

![[Pasted image 20241214191522.png]]

**Imitating human perception for OOD (using ==language==):**
![[Pasted image 20241214191738.png]]

Is language the main source of human reasoning power? No, it is one aspect of it. **The goal is to use suitable inductive biases in language models to make reasoning possible for a machine.**
![[Pasted image 20241214192131.png]]

--------------

![[Pasted image 20241214192354.png]]

Humans have system1 and system2 ability, bit machines have problem with system2 ability (OOD inputs). If machines learn system2 ability, ==then they can expalin why do they generate the outputs they generate.==

----------------

![[Pasted image 20241214192806.png]]

-----------------------

![[Pasted image 20241214193317.png]]
![[Pasted image 20241214193319.png]]

Recurrent independent mechanism:
![[Pasted image 20241214193359.png]]
Breaking into micro-concepts: In this architecture, the input changes the weight of some of RNNs (not all of them), but those RNNs can communicate and get information about each other. Like each of 3 inputs affect a certain part of the network.

![[Pasted image 20241214193740.png]]

![[Pasted image 20241214193847.png]]

![[Pasted image 20241214193934.png]]

In almost all these methods, instead of finding a representation for the whole input, we generated representations for separate parts of the input.

--------------------

![[Pasted image 20241214194225.png]]

How to solve this problem?
![[Pasted image 20241214194320.png]]

![[Pasted image 20241214194425.png]]

==Model should generate a formula (using an inductive bias) rather than only generating outputs.==

-----------------

![[Pasted image 20241214194706.png]]

![[Pasted image 20241214194807.png]]

Example usage:
![[Pasted image 20241214195051.png]]
- It learns simple tasks and forces itself to learn a combination of them to become able of solving more complex tasks.

----------------

![[Pasted image 20241214195353.png]]

![[Pasted image 20241214195407.png]]

if LLM is asked to explain, or is teached how to solve a problem using prompt, it can generalize better.

![[Pasted image 20241214195520.png]]
???
