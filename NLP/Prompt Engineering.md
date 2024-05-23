
CTRL:
- ![[Pasted image 20240519181213.png]]

- you don't necessarily need to use CTRL (Conditional Transformer Language Model for Controllable Generation) to perform prompt tuning on a model. While CTRL provides a unique mechanism for controlling text generation by specifying control codes in the prompt, **prompt tuning itself is a broader technique that can be applied to a variety of models, including but not limited to CTRL**.

### Understanding Prompt Tuning

**Prompt tuning** is a method where you fine-tune a pre-trained model by ==optimizing a set of prompt tokens== (also referred to as soft prompts) that are prepended to the input text. This technique alters the way the model processes inputs, effectively guiding it to generate outputs that are tailored to specific tasks or styles **without the need to modify the model’s core weights**.

### Alternatives for Prompt Tuning

1. **Soft Prompt Tuning**:
    
    - **Description**: This involves learning a set of artificial tokens that are not part of the actual input but are optimized during training to induce the desired behavior from the model. These tokens effectively become a part of the model's input for all tasks during the tuning phase.
    - **Applications**: Widely applicable across various models and tasks, allowing the model to adapt to specific requirements without extensive retraining.
2. **Hard Prompt Engineering**:
    
    - **Description**: This is a manual approach where you craft specific prompts or instructions that are included with each input to guide the model's responses. This method relies on human intuition and experimentation to find effective prompts.
    - **Applications**: Useful when you need to quickly adapt a model for a new task without any training, provided you can find effective prompts through trial and error.

### CTRL in the Context of Prompt Tuning

CTRL can be viewed as a specialized case of hard prompt engineering, where each control code effectively acts as a hard-coded prompt that directs the model’s generation process. However, using CTRL specifically for prompt tuning is limited to scenarios where you have predefined control codes that suit your needs, and you are using the CTRL model specifically.

--------------------------------------------------------

Self Learning : To add the output of the previous data as the input prompt of the current data, this is done by the decoder function.