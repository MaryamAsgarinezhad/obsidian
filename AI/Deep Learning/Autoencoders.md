### **Why do standard autoencoders learn deterministic mappings?**

A standard autoencoder consists of two neural networks:

- **Encoder**: Maps input x to a latent representation z.
- **Decoder**: Maps z back to a reconstruction x′.

Since ==these networks are typically **deterministic functions**==, for any given input x, the encoder **always** produces the same latent vector z, meaning:

z=fϕ(x) : where fϕ​ is a function parameterized by weights ϕ.

### Variational Autoencoders (VAEs) Explained from Scratch

Variational Autoencoders (VAEs) are a type of generative model that extends traditional autoencoders by learning a **probabilistic** representation of the data. Unlike classical autoencoders, which learn a fixed latent space encoding, VAEs impose a **probability distribution** on the latent space, allowing them to generate new samples by sampling from that distribution.

---

## **1. Background: Autoencoders**

Before diving into VAEs, let’s briefly recall how autoencoders work:

- **Encoder**: Compresses input x into a lower-dimensional latent representation z.
    
- **Decoder**: Reconstructs x from z.
    
- **Loss Function**: Measures the reconstruction error (e.g., Mean Squared Error, MSE).
    

Autoencoders learn **deterministic** mappings, meaning the ==encoder always outputs the same z for a given x==. **This limits their ability to generate new, diverse samples**.

---

## **2. The Problem: Need for a Probabilistic Model**

If we want a model to generate realistic new data points, it must learn a **continuous** and **smooth** latent space where similar points generate similar outputs. Classical autoencoders fail at this because they learn a direct mapping, making the latent space discontinuous.

### **Solution: Variational Autoencoders**

VAEs solve this by:

1. Learning a **probabilistic** mapping of data into a latent space.
    
2. Imposing a prior distribution (usually Gaussian) on the latent space.
    
3. Encouraging smoothness by regularizing the latent space structure.


---

## **3. How VAEs Work**

![[Pasted image 20250328151905.png]]

---

## **4. The VAE Loss Function**

![[Pasted image 20250328153147.png]]
![[Pasted image 20250328161047.png]]

---

## **5. Training a VAE**

![[Pasted image 20250328153310.png]]

---

## **8. Example Code in PyTorch**

```python
import torch
import torch.nn as nn
import torch.optim as optim

# Define Encoder
class Encoder(nn.Module):
    def __init__(self, input_dim, latent_dim):
        super().__init__()
        self.fc1 = nn.Linear(input_dim, 128)
        self.fc_mu = nn.Linear(128, latent_dim)  # Mean
        self.fc_logvar = nn.Linear(128, latent_dim)  # Log-Variance

    def forward(self, x):
        x = torch.relu(self.fc1(x))
        mu = self.fc_mu(x)
        logvar = self.fc_logvar(x)
        return mu, logvar

# Reparameterization Trick
def reparameterize(mu, logvar):
    std = torch.exp(0.5 * logvar)
    eps = torch.randn_like(std)
    return mu + eps * std

# Define Decoder
class Decoder(nn.Module):
    def __init__(self, latent_dim, output_dim):
        super().__init__()
        self.fc1 = nn.Linear(latent_dim, 128)
        self.fc2 = nn.Linear(128, output_dim)

    def forward(self, z):
        z = torch.relu(self.fc1(z))
        return torch.sigmoid(self.fc2(z))

# Define VAE Model
class VAE(nn.Module):
    def __init__(self, input_dim, latent_dim):
        super().__init__()
        self.encoder = Encoder(input_dim, latent_dim)
        self.decoder = Decoder(latent_dim, input_dim)

    def forward(self, x):
        mu, logvar = self.encoder(x)
        z = reparameterize(mu, logvar)
        x_recon = self.decoder(z)
        return x_recon, mu, logvar

# Loss Function
def vae_loss(x, x_recon, mu, logvar):
    recon_loss = nn.functional.binary_cross_entropy(x_recon, x, reduction="sum")
    kl_div = -0.5 * torch.sum(1 + logvar - mu.pow(2) - logvar.exp())
    return recon_loss + kl_div

# Training Loop
vae = VAE(input_dim=784, latent_dim=20)
optimizer = optim.Adam(vae.parameters(), lr=0.001)

for epoch in range(10):
    for batch in dataloader:
        x, _ = batch  # Assume x is the input
        x_recon, mu, logvar = vae(x)
        loss = vae_loss(x, x_recon, mu, logvar)
        
        optimizer.zero_grad()
        loss.backward()
        optimizer.step()

    print(f"Epoch {epoch}, Loss: {loss.item()}")

```

---
### **Neural Network Modeling**

![[Pasted image 20250328154047.png]]


- **Why log variance?** Instead of directly predicting variance, we predict **log variance** for numerical stability and prevent negative variance values.


![[Pasted image 20250328154232.png]]

![[Pasted image 20250328154943.png]]

- The final layer outputs pixel probabilities (for images).
    
- A **sigmoid** activation function is used for binary image data (e.g., MNIST).
    
- For continuous data, a Gaussian likelihood model can be used.

### Then combine everything into a single **VAE class**

---
![[Pasted image 20250328160602.png]]
