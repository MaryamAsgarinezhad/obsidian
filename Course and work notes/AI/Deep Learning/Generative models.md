![[Pasted image 20240828111049.png]]
![[Pasted image 20240828111120.png]]
 **The goal and functionality of USL model is detecting the structure and attribtes of an arbitrary input.**
 ![[Pasted image 20240828111612.png]]
 Example of Unsupervised tasks:
 ![[Pasted image 20240828111701.png]]
 
---

Generative models are unsupervised. We aim to ==generate data== in these models.
![[Pasted image 20240828111841.png]]

Usages of generative models:
![[Pasted image 20240828115653.png]]

==Different types:==
Here we have different approches for generative models. The architecture can be CNN, RNN, transformer, ...

==Know the difference between purpose and **architecture** of a model.==
![[Pasted image 20240828122119.png]]

------------------------------------------------------

Variational autoencoder (**VAE**):

 These model works with latent space (z), which are hidden factors affecting samples. What is this 
 ![[Pasted image 20240828132218.png]]
 ![[Pasted image 20240828132527.png]]
 What are we looking for by autoencoders?
 ![[Pasted image 20240828132631.png]]
 ![[Pasted image 20240828132730.png]]
- **Feature vector (z) extracted in encoder, contains variation factors.**
  
- Can we use z to generate instead of reconstruct? NO, we havo no data that what the z should be to generate a certain output.
  
- How to build a generator so? By variational autoencoders.
  ==It supposes Z has a normal standard distribution (zero mean and 1 variance).==
  It samples from this distribution and gives it as an input to a network to generate.
  ![[Pasted image 20240828142922.png]]


Explanation of its encoder and decoder:
![[Pasted image 20240828152541.png]]
- Since we assumed z has a normal distribution, we require the ==encoder to output the mean and variance of z==, then we enclose it to the amount we want during training.
  
- **Loss function:** The right term forces the mean and variance encoded data to be close to N(0,1). The lest term is reconstruction loss, makes the generated output to be similar to input samples.

![[Pasted image 20240828162720.png]]

- ==The decoder generates a mean and variance for x, while encoder generate them for z, and sampling form the distribution of x equals to generating an output.==

Z space attributes:
![[Pasted image 20240828165503.png]]


- **Encoder (Recognition Model)**: In a VAE, the encoder maps the input data x not to a single point in the latent space, but to a **distribution** over the latent variables z. Specifically, the encoder outputs the parameters (mean μ and variance σ2) of a Gaussian distribution.
- **Latent Space Sampling**: Instead of directly using the latent space representation from the encoder, a VAE samples from the distribution q(z∣x), ensuring the model learns to generate diverse outputs.
- **Decoder (Generative Model)**: The decoder takes a sample z from the latent distribution and tries to reconstruct the original input x by generating p(x∣z), the probability of x given z.

### **Applications**

- **Image Generation**: VAEs can generate new, unseen images by sampling from the latent space.
- ==**Anomaly Detection**==: VAEs can detect anomalies by comparing the reconstruction error of the input with the error from typical data points.
- **Data Compression**: VAEs can be used to compress data into a lower-dimensional latent space efficiently.

### Applications of Autoencoders

1. **Dimensionality Reduction**: Similar to PCA, autoencoders can reduce the dimensionality of data, which is useful for visualization (e.g., using the 2D latent space to visualize high-dimensional data).
    
2. **Feature Extraction and Learning**: Autoencoders can learn useful features from the data automatically, which can be used for further classification or other machine learning tasks.
    
3. **Anomaly Detection**: In scenarios where autoencoders are trained only on normal data, the reconstruction error will be significantly higher for anomalies (outliers), which can be used to ==detect abnormal instances==.
    
4. **Data Denoising**: As seen in denoising autoencoders, they are capable of removing noise from data effectively.
   - ==**Denoising Autoencoder**==: This type is trained to use a corrupted version of the input at the encoder, while still aiming to produce the original, uncorrupted input at the decoder. The idea is to make the learned representations robust to small, random changes in the input.
    
5. **Generative Models**: Particularly variational autoencoders, which can ==generate new data points that are similar to the training data.==
   - **Variational Autoencoder (VAE)**: These are generative models that add a probabilistic twist to autoencoders. Instead of learning a function to describe the hidden layer (the encoding), the encoder in a VAE models the input data as a probability distribution in the latent space, which helps in generating new data points.

In these models, the process is sequential: you generate one element, then use that element as input (along with previous elements) to generate the next one. This sequential dependency is the key feature of autoregressive models.

![[Pasted image 20240828180407.png]]

-------------------------------------------

**Generative adversarial networks**

- ==It learns sampling form distribution, instead of learning the distribution itself.==

![[Pasted image 20240828182453.png]]
![[Pasted image 20240828182508.png]]
![[Pasted image 20240828182534.png]]
GAN compared to VAE:
![[Pasted image 20240828184948.png]]

![[Pasted image 20240828185025.png]]

- **Generator (G)**: The generator's role is to create data samples that resemble the real data. It ==takes as input a random noise vector== (often sampled from a simple distribution like a Gaussian or uniform distribution) and ==transforms it into a data sample==. The goal of the generator is to produce data that the discriminator cannot distinguish from real data.
- **Discriminator (D)**: The discriminator’s job is to distinguish between real data (from the true data distribution) and fake data (generated by the generator). It outputs a probability indicating whether a given input is real or fake.

This adversarial procedure causes the outputs to be real and of a high quality.

Loss function:
![[Pasted image 20240828190026.png]]
- The **discriminator** tries to maximize the probability of correctly classifying real and fake data. The loss function for the discriminator is typically the binary ==cross-entropy loss==. 
  Here, D(x)is the discriminator's output for a real data sample x, and D(G(z))is its output for a generated sample G(z) where z is the random noise input to the generator.
- ![[Pasted image 20240828191602.png]]
- The **generator** tries to minimize the discriminator’s ability to distinguish between real and fake data. This encourages the generator to produce data that the discriminator classifies as real. We continue training until we reach a certain precession.
- ![[Pasted image 20240828191752.png]]

- **After training we only use generator, while in VAE after training we only use decoder.**

In VAE, dimensions of z were independent. In GAN, sampled data from a noisy distribution are independent:
![[Pasted image 20240828192859.png]]

You can use conditional GAN when you want to generate certain pics: (more to study)
![[Pasted image 20240828193011.png]]
![[Pasted image 20240828193204.png]]
GAN needs more epochs to be trained.

![[Pasted image 20250328153411.png]]

-----------------------------------------

Diffusion models:
![[Pasted image 20240831113927.png]]
![[Pasted image 20240831114033.png]]

Diffusion is easy but denoising is difficult and is done by a neural network. This network's job is to get an input and denoise it a bit.
Forward process is a fixed process.
![[Pasted image 20240831120345.png]]
![[Pasted image 20240831120413.png]]
**After training the denoiser network, we keep aside the noise generator and only use denoiser.**

Forward process: It adds noise the an input, then it becomes noisy!
**==Noise is a random sample from N(0,1).==**
![[Pasted image 20240831120728.png]]
![[Pasted image 20240831120841.png]]

Reverse process: The goal of this network is to **==estimate q(xi-1|xi)==**
**So the output of this network must be ==mean and variance of a gaussian distribution.**==
![[Pasted image 20240831120915.png]]
![[Pasted image 20240831121033.png]]
![[Pasted image 20240831124122.png]]

What **architecture** is used in diffusion models? **U-net**
![[Pasted image 20240831124259.png]]

The input and output of U-net are both photos.

- ![[Pasted image 20240831125006.png]]
- ![[Pasted image 20240831124507.png]] 
- Diffusion models are more accurate than GANs and have better results:
  ![[Pasted image 20240831131715.png]]

- Diffusion needs more trainig than GAN, There is no free lunch.
- Like conditional GAN, we also have conditional diffusion (classifier free guidance).
  ![[Pasted image 20240831132606.png]]
  ![[Pasted image 20240831132652.png]]
  ![[Pasted image 20240831132708.png]]
-------------------------

==Stable diffusion== (**a kind of conditional diffusion using input text as a prompt like DallE**):
![[Pasted image 20240831132851.png]]

How is prompt entered to the model as an input along with image? Give the prompt to a representation model like Clipp to extract its embedding, then we give it to the model.

-------------------

Comparison:
![[Pasted image 20240831132941.png]]

