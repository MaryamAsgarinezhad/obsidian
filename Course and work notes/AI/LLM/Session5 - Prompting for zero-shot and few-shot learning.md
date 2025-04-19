
Test data contains unseen classification classes 

![[Pasted image 20250419164002.png]]

How could this happen? 
- Model can recognize zebra'a features from its training process (tail, strips, ...), and is feed with a textual definition of how zebra is.

---

ZSL in LLMs:

![[Pasted image 20250419165807.png]]
- ZSL here is not to recognize unseen classes but to be able to do unseen NLP tasks.
- How is that possible? 
- T0 is based on T5 (able to do a variety of fundamental NLP tasks), trained (fine-tunned) on multi-tasks, and is feed with an instruction defining the unseen NLP task needed to be done.
- Here is how NLP tasks could be mapped to an input prompt.

![[Pasted image 20250419170231.png]]

The point is that T0 is 16 times smaller than GPT models, though it can do few-shot learning.
- GPT is trained on a large web based corpus of data.
- But T0 is trained on a considerably smaller amount of data but is fine tunned for NLP multi-tasks.

---

![[Pasted image 20250419171316.png]]

- If few shot examples are not used to change model's weights, it is called **in** **context learning**.
- It is not practical to expect few shots to adjust model weights in an effective way. This method fails.

![[Pasted image 20250419171620.png]]

![[Pasted image 20250419173849.png]]

---
##### Generate few shots:

Let’s have a discussion 
• Don’t have the luxury of deploying a 100-B parameter. 
• All we can afford is a pre-trained 100-M parameter model. 
• Have only a couple of labeled examples from the target task. 

![[Pasted image 20250419174003.png]]Although we have frizzed Bert's weights, fine-tunning doesn't work because we have only few samples to be learned.

- What we need to do is to **exploit the task which Bert is taught to do**, e.g. masked token prediction (not fully-connected layer on CLS)
- ![[Pasted image 20250419174604.png]]
  **Step2** maps positive and negative tokens to words suitable for prediction.

- ![[Pasted image 20250419174613.png]]
- We also need to consider **zero probability** for any generated masked token except "great and terrible".
  ![[Pasted image 20250419184352.png]]
  
- ![[Pasted image 20250419174639.png]]
- We do not have positive and negative labels, but a number between lower and upper extremes (vl and vu) for the sentiment rate of output.


- **Since Bert is already trained for masked word prediction, we fine tune the ==whole model weights== on our few samples, and it works because training task is the same as what Bert previously learned.**

---

Manual word mapping changes accuracy:
![[Pasted image 20250419195046.png]]
![[Pasted image 20250419195131.png]]

##### So it can be optimized!

![[Pasted image 20250419195308.png]]

##### Still manual process. 

![[Pasted image 20250419195652.png]]
![[Pasted image 20250419195702.png]]
![[Pasted image 20250419195717.png]]
![[Pasted image 20250419195729.png]]
![[Pasted image 20250419195738.png]]
