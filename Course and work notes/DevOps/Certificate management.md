1- Credit and validity for the domain
2- Secure connection 
3- Https connection

Certificates have expiration time and must be recreated after a while.

How to create?

- Buy the certificate and install it
- Build the certificate using Certbot

```shell
sudo apt install certbot
```

This must be installed on the server you are going to create certificate for. 

The challenge in certificate creation, particularly when using tools like Certbot, lies in ensuring that the certificate authority (CA) can verify that you have control over the domain(s) for which you are requesting the certificate. This verification is crucial for the CA to issue a trusted certificate.

Some common challenges include:

1. **DNS Configuration:** Ensuring that the domain's DNS records (A, AAAA, and CNAME records) are correctly configured to point to the server where the certificate is being requested. Incorrect or outdated DNS records can lead to verification failures.
    
2. **Server Configuration:** The server must be correctly set up to respond to ACME (Automatic Certificate Management Environment) challenges. This often involves temporarily exposing certain endpoints or files on the server for the CA to verify domain ownership.
    
3. **Firewall and Network Issues:** Firewalls or network configurations that block the CA's verification requests can prevent successful certificate issuance. Ensure that the server can receive requests on port 80 (HTTP) or 443 (HTTPS) during the verification process.
    
4. **Domain Ownership Verification:** For wildcard certificates or certificates for domains not publicly listed in WHOIS records, additional verification steps may be required to prove ownership or control of the domain.
    
5. **Rate Limiting:** Certificate authorities may impose rate limits on the number of certificate issuance requests from a single IP address or domain. Exceeding these limits can result in temporary blocks.
    

By addressing these challenges and ensuring that your DNS, server, and network configurations are correctly set up, you can increase the likelihood of successfully creating and obtaining a certificate for your domain.

---------------------------------------

```shell
certbot --nginx -d boom.st.mci.dev
```
This command invokes Certbot, a tool used for managing SSL/TLS certificates, with the `--nginx` option, which tells Certbot to use the Nginx plugin to automate the certificate issuance and installation process for the specified domain (`boom.st.mci.dev`).

The `-d` flag is used to specify the domain(s) for which the certificate should be issued. In this case, `boom.st.mci.dev` is the **domain name for which the certificate is being requested.**

Overall, this command is attempting to automatically obtain and install an SSL/TLS certificate for the `boom.st.mci.dev` domain using the Nginx web server.

- An SSL/TLS certificate is a digital certificate that authenticates the identity of a website and encrypts information sent to the server using SSL/TLS technology. It ensures that data exchanged between the user's web browser and the website's server is encrypted and secure, protecting it from eavesdropping and tampering. SSL/TLS certificates are used to establish a secure connection and are essential for websites that handle sensitive information, such as passwords, credit card details, and personal information.

------------------------------------------

```shell
sudo certbot certonly --manual --preferred-challenges dns -d boom.st.mci.dev
```
- `certonly`: This option tells Certbot to only obtain the certificate and not install it. You'll need to manually configure your server to use the obtained certificate.
- `--manual`: Specifies that the manual method should be used to complete the challenges. This means you'll have to manually perform the DNS challenge to prove ownership of the domain.
- `--preferred-challenges dns`: Specifies that the DNS challenge should be used as the preferred challenge type. This requires adding a specific DNS record to your domain's DNS configuration.
- `-d boom.st.mci.dev`: Specifies the domain for which the certificate should be issued.

---------------------------------------------
**Kuberntes Certificate manager:**

