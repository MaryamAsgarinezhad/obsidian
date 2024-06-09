A network interface is a hardware or software component that enables a device to connect to a network. It provides the necessary communication between the device and the network, allowing the device to send and receive data. Network interfaces can be physical, such as Ethernet or Wi-Fi adapters, or virtual, such as those created by virtualization software like VMware or VirtualBox. Each network interface has a unique identifier, typically called a MAC address, and can be configured with an IP address to facilitate communication over the network.


IP (Internet Protocol) addresses and MAC (Media Access Control) addresses are both used in computer networking, but they serve different purposes and operate at different layers of the networking stack.

1. **Purpose:**
    
    - **IP Address:** Used for identifying devices on a network and facilitating communication between them. IP addresses are logical addresses assigned to devices and can be changed or reassigned.
    - **MAC Address:** Used for identifying a specific device on a network. MAC addresses are physical addresses burned into the network interface card (NIC) of the device and remain unchanged.
2. **Layer:**
    
    - **IP Address:** Operates at the network layer (Layer 3) of the OSI model and is used for routing packets between networks.
    - **MAC Address:** Operates at the data link layer (Layer 2) of the OSI model and is used for communication within the same network segment.
3. **Uniqueness:**
    
    - **IP Address:** Must be unique within the same network but can be reused on different networks.
    - **MAC Address:** Must be globally unique, and no two devices should have the same MAC address.
4. **Assignment:**
    
    - **IP Address:** Assigned dynamically (via DHCP) or statically to devices.
    - **MAC Address:** Assigned permanently to the network interface card.
5. **Format:**
    
    - **IP Address:** Consists of four octets (IPv4) or eight groups of four hexadecimal digits (IPv6), separated by dots or colons, respectively.
    - **MAC Address:** Consists of six groups of two hexadecimal digits, separated by colons or hyphens.

In summary, IP addresses are used for logical identification and routing of packets between networks, while MAC addresses are used for physical identification of devices within the same network segment.


The OSI (Open Systems Interconnection) model is a conceptual framework that standardizes the functions of a telecommunication or computing system into seven abstraction layers. Each layer serves a specific purpose in facilitating communication between devices. Here's a more detailed explanation of the layers relevant to IP and MAC addresses:

1. **Network Layer (Layer 3):**
    
    - **IP Address:** This layer is responsible for routing packets between different networks. An IP address is assigned to each device connected to a network and is used to uniquely identify that device on the network. When a device wants to communicate with another device on a different network, it uses the destination IP address to route the packet through routers on the internet until it reaches its destination network.
2. **Data Link Layer (Layer 2):**
    
    - **MAC Address:** This layer is responsible for communication within the same network segment (or LAN - Local Area Network). A MAC address is a unique identifier assigned to the network interface card (NIC) of each device. It is used to identify devices on the same network and is used in the process of delivering data frames from one device to another within the same network segment. When a device wants to send a packet to another device on the same network, it uses the destination MAC address to address the packet to that specific device.

In summary, the IP address is used for routing packets between different networks at the network layer (Layer 3), while the MAC address is used for communication within the same network segment at the data link layer (Layer 2).


A LAN (Local Area Network) is a network that is confined to a relatively small geographic area, such as a single building, a group of buildings, or a campus. LANs are typically used for connecting devices in close proximity to each other, such as computers, printers, servers, and other devices, to facilitate communication and resource sharing. LANs can be wired, using Ethernet cables and switches, or wireless, using Wi-Fi technology. They are commonly used in homes, offices, schools, and other similar environments.

The reason we can't use IP addresses instead of MAC addresses for local communication is that IP addresses are not directly tied to physical hardware and can change, especially in dynamic addressing environments like DHCP. Additionally, routing based on IP addresses would require additional overhead and complexity, as routers would need to maintain tables mapping IP addresses to MAC addresses for all devices on the network. Using MAC addresses for local communication allows for simpler and more efficient data transmission within the same network segment.


--Host Machine--
The host machine, in the context of networking or virtualization, refers to the physical computer or server that runs one or more virtual machines (VMs) or containers. It is the actual hardware that provides computing resources such as CPU, memory, disk space, and network connectivity to the virtualized environments.

- Ping is commonly used to test the availability and response time of a remote device or server. Telnet is used to establish a connection and access a remote computer's command line interface, while Ping is used to test the connectivity and response time between two devices on a network.

-----------------------------------

This site is used to check the IP addresses allocated for a domain :

![[Pasted image 20240430162452.png]]

-------------------------------------

**POSTMAN**

Postman is a popular tool used for API development and testing. It provides a user-friendly interface for sending HTTP requests to web servers and inspecting the responses.

API Testing: Postman allows developers to easily test APIs by sending various types of HTTP requests (such as GET, POST, PUT, DELETE) to endpoints and observing the responses. This helps ensure that APIs function correctly and handle different scenarios as expected.

---------------------------------------------------

GET:

    Purpose: Retrieve data from the server.
    Characteristics:
        Requests data from a specified resource.
        Parameters are sent in the URL (query string).
        Should not have a request body.
    Safe and idempotent: Repeated GET requests with the same                    parameters should have the same effect and not modify the              server state.
    Example: Fetching a webpage, retrieving user profile information.


POST:

    Purpose: Submit data to be processed to a specified resource.
    Characteristics:
        Submits data to be processed to the server.
        Parameters are sent in the request body.
        Can include large amounts of data.
    Not idempotent: Multiple identical POST requests may result in              different outcomes or modify server state differently.
         Example: Submitting a form, uploading a file.

PUT:

Purpose: Update an existing resource or create a new resource if it does not exist.
    Characteristics:
        Updates an existing resource with the provided data or creates a new resource if it does not exist.
        The entire resource is replaced with the provided data.
        Should be idempotent: Repeated PUT requests should have the same effect.
    Example: Updating a user's profile, uploading a new version of a file.

PATCH:

Purpose: Partially update an existing resource.
    Characteristics:
        Applies partial modifications to an existing resource.
        Only the specified fields in the request body are updated.
        Should be idempotent: Repeated PATCH requests with the same parameters should have the same effect.
    Example: Updating specific fields of a user's profile, modifying parts of a document.

DELETE:

    Purpose: Delete the specified resource.
    Characteristics:
        Removes the specified resource from the server.
        Should be idempotent: Repeated DELETE requests should have the same effect.
    Example: Deleting a user account, removing a file.

------------------------
![[Pasted image 20240504135714.png]]equivalent code for postman in different languages

-------------------------
![[Pasted image 20240504154013.png]]route conflict solve (docker network and repo network)

------------------------------------------------------

TYPES OF ERRORS:

Status codes that start with 5/4, refer to the server/client side. Its important to recognize whether you are getting the errors as a client or server. 
For example, in "CDN" project we are at the server side.

- **403 error may be due to authentication issues.**

- A 503 Service Unavailable Error is an HTTP response status code that **indicates your web server operates properly, but it can't handle a request at the moment**. Since it's just a generic error message, it's difficult to pinpoint the issue's exact cause.

- The 502 Bad Gateway error is an HTTP status code that occurs when a server acting as a gateway or proxy receives an invalid or faulty response from another server in the communication chain.

-------------------------------------
```bash
https://www.example.com:80/path/to/resource?query=123#section
```
![[Pasted image 20240609120302.png]]
### Components of a URL

1. **Protocol (Scheme)**
    
    - **Example**: `https`
    - The protocol or scheme indicates the method by which the resource is accessed. In this example, `https` signifies that the HTTP protocol with SSL/TLS encryption is used for secure communication. Other common protocols include `http` (HyperText Transfer Protocol), `ftp` (File Transfer Protocol), and `mailto` (for emails).
2. **Domain (Host)**
    
    - **Example**: `www.example.com`
    - The domain or host specifies the server (hosted on a network) on which the resource resides. Domains are translated into IP addresses by DNS (Domain Name System) servers when you try to access the resource. The domain can include subdomains (like `www`) and top-level domains (like `.com`).
3. **Port**
    
    - **Example**: `80`
    - The port number is optional and specifies the technical gateway through which the server can be accessed. Each protocol typically has a default port (e.g., 80 for `http` and 443 for `https`), which is not required to be included in the URL if the default is used. In this example, port `80` is explicitly specified.
4. **Path**
    
    - **Example**: `/path/to/resource`
    - The path points to a specific resource or location within the server. It is similar to a file path in computer filesystems. In this example, it points to a hypothetical resource named `resource` located in directories `/path/to/`.
5. **Query String**
    
    - **Example**: `?query=123`
    - This part is optional and is used to provide additional parameters to the server. It starts with a question mark `?` followed by key-value pairs (e.g., `key=value`). These parameters are often used for passing data to the server to refine the response or behavior (e.g., search parameters, pagination information).
6. **Fragment**
    
    - **Example**: `#section`
    - Also known as the hash, the fragment is an internal page reference, sometimes called a named anchor. It usually refers to a specific part of the document. In web browsers, this doesn’t result in an HTTP request: it just scrolls the page to the marked section if available.

------------------------------------

A domain, specifically referred to in the context of the Internet, is part of the Domain Name System (DNS), which is essentially the "phone book" for the internet. Domains provide a human-readable address for an Internet resource, such as a website, by mapping to a numerical IP address that computers use to locate each other on the network.

### Key Components and Functions of a Domain Name

1. **Human Readability**:
    
    - Domain names are designed to be easily identifiable and memorable for humans, contrasting with the numerical IP addresses used by computers and networking equipment.
2. **Hierarchical Structure**:
    
    - **Top-Level Domain (TLD)**: This is the last segment of the domain name, located after the last dot, such as `.com`, `.org`, `.net`, `.gov`, `.edu`, and country codes like `.uk`, `.us`, `.ca`.
    - **Second-Level Domain (SLD)**: This is the part of the domain name located directly to the left of the TLD. For `example.com`, "example" is the second-level domain.
    - **Subdomain**: This precedes the second-level domain and provides additional subdivisions or specifies particular services, such as `www` (as in `www.example.com`), or other areas like `blog.example.com` or `shop.example.com`.
3. **DNS Records**:
    
    - Domains are associated with various DNS records, which provide essential information about where to send requests for different types of services associated with the domain. Common records include:
        - **A Record**: Links a domain to the IP address of a server hosting its resources.
        - **MX Record**: Directs email to the mail servers for that domain.
        - **CNAME Record**: Can alias one domain name to another, allowing for more flexible resource management.
4. **Operational Role**:
    
    - Domains play a crucial role in accessing websites and communicating via email without needing to remember complex IP addresses.
    - Domains also play a part in cybersecurity by enabling secure SSL/TLS certificates that authenticate and encrypt data between users' computers and servers.

### Ownership and Management

- **Registration**: Domains are registered through domain registrars. When a domain is registered, it is unique globally and cannot be used by another entity as long as it's registered to a user or organization.
- **Management**: Domain owners can manage their domains through the registrar's control panel, setting up DNS records, transferring the domain to other registrars, or renewing their registration.

### Example Uses of Domains

- **Web Browsing**: Visiting `https://www.google.com` involves the domain `google.com`.
- **Email**: Sending an email to `user@example.com` uses the domain `example.com`.
- **FTP and Other Protocols**: Accessing resources through different protocols might also use domain names, such as in FTP (`ftp.example.com`).


-------------------------------------------------

![[Pasted image 20240511130504.png]]

![[Pasted image 20240511130529.png]]

- The token java project creates the daily key for hashing.
- The regression python project tests this key generation process.

-----------------------------------

**Dynamic Host Configuration Protocol**

- Internet protocol
- The Dynamic Host Configuration Protocol is a network management protocol used on Internet Protocol networks for automatically assigning IP addresses and other communication parameters to devices connected to the network using a client–server architecture.

----------------------------------------------

"Apache" commonly refers to the Apache HTTP Server, which is a widely-used web server software. Developed and maintained by the Apache Software Foundation, it plays a crucial role in the internet's infrastructure by **delivering web content to users**.

  - **Usage**:
    
    - **Serving Web Content**: Apache is primarily used to host static web content and deliver web pages to users’ browsers.
    - **Application Server**: Through integration with other software (like PHP, Python, etc.), it also serves dynamic web content, acting as the backbone for application servers.
    - **Proxy Server**: It can be configured as a reverse proxy server, routing requests from clients across the internet to internal servers.
    - **Load Balancing**: Apache can distribute incoming requests across multiple backend servers to balance load and enhance performance.


- **Configuration**:
    
    - Apache is configured through plain-text configuration files, with `httpd.conf` being the main configuration file. Administrators can specify server settings, security configurations, and host settings within these files.
    - Virtual hosting allows one Apache installation to serve different websites. For example, hosting both `www.example.com` and `www.test.com` on the same server.

---------------------------------------------

You can define your DNS locally in /etc/hosts.

![[Pasted image 20240515185521.png]]

------------------------------

```shell
cd ~/.ssh
```

In this dirctory, you can create a config file and there define the parameters by which you would like ot connect to each domain. So after that there is no need to specify the user in 'user@IP' and -p to specify the port for SSH commands.

----------------------

**Web Search vs Postman:**

1- Browser:

- **Graphical Interface**: Browsers provide a graphical user interface (GUI), making it easy to navigate, interact with, and visualize web content.
- **Rendering**: Browsers render HTML, CSS, JavaScript, and other web content, providing a fully formatted and interactive web page.
- **Ease of Use**: Users can simply enter a URL in the address bar and navigate through links with clicks.

2- Postman

- **Command Line Tool**: `curl` is a command-line tool, making it more suitable for use in scripts and automation.
- **Text-Based**: `curl` displays raw HTTP responses, which might include HTML, JSON, XML, or plain text, without rendering or interactive elements.

In a typical web browser search, you can't directly specify custom HTTP headers or a request body. Web browsers are designed to provide a user-friendly interface for navigating and interacting with websites, and they handle HTTP headers and request bodies internally without exposing detailed control to the user.

--------------------------------------

# Session-Based vs. Token-Based Authentication

![[Pasted image 20240606201144.png]]

In simple words, session-based authentication uses a special code(session id) stored on your device to remember who you are when you visit a website, keeping you logged in and remembering your information until you leave or log out. Didn’t get it? Don’t worry, let’s take a look step by step.

1. User Login:
Users log in by sending their email and password to the server through a special request.

 2. Checking Details:
The server checks if the provided details match what's stored for the user.

 3. Creating a Session:
If everything is correct, the server makes a 'session' that holds user info (like user ID, permissions, and time limits). This info is kept safe in the server's storage. Exam or can also be managed using libraries such as `express-session`.

 4. Getting a Session ID:
The server sends this 'session ID' back to the user's device, usually ==as a cookie in the response.==

5. Using the Session ID:
Whenever the user wants something from the server, ==their device automatically includes this session ID in its requests.==

6. Server Checks:
The server uses this session ID to find the stored information about the session user in the session storage.

Here’s a sneak peek at how `express-session` works:

- When the user logs in, the server creates a session for that user and sets a cookie🍪 in the response containing the session ID.
    
- The browser automatically includes this session ID cookie🍪 in subsequent requests to the server.
    
- When the server receives a request, ==express-session middleware== uses the session ID from the cookie🍪 to retrieve the relevant session data.
    
- The data stored in **req.session** (such as userId) becomes available to handle the request.
    

7. Access Granted:

If everything matches up, the server knows the user is genuine and responds to them with access to what they asked for.

[![Session auth working](https://media.dev.to/cdn-cgi/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.amazonaws.com%2Fuploads%2Farticles%2F3g42u59txhhuaflzsj52.png)](https://media.dev.to/cdn-cgi/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.amazonaws.com%2Fuploads%2Farticles%2F3g42u59txhhuaflzsj52.png)

## Token-based auth

JWT authentication uses digitally signed tokens containing user information to allow secure and verified access to websites or applications without needing to repeatedly log in. Let’s take a look at the step-by-step workflow of token-based authentication.

1. User Login Request:
Users log in by sending their email and password to the server through a specific request.

2. Credential Verification:
The server verifies the provided credentials against the stored user data.

3. Token Generation:
Upon successful verification, the server creates a token (commonly JWT - JSON Web Token). This token holds user information (claims) such as user_id, permissions.

4. Token Signing and Hashing:
The token is signed with a secret key and processed with a hashing algorithm (like SHA256) to create a hash.

5. Sending the Token:
The server sends this token to the client, which stores it, typically in the browser.

6. Token Storage Options:
The client can store the token in different ways like HttpOnly Cookies, Session Storage, or Local Storage. Storing in HttpOnly Cookies is recommended as it prevents JavaScript access, enhancing security against XSS attacks.

7. Token Expiry and Security:
Tokens often have an expiration time to enhance security.

8. Including Token in Requests:
For every request to the server, the client sends the token in the Authorization header.

It's a good practice to prefix the token with "Bearer ".  

```
axios.get(URL, {
    headers: {
        'Authorization': 'Bearer ' + token,
    },
})
```

9. Server-Side Validation:
Upon receiving a request, the server retrieves the token.

10. Token Validation and User Authentication:
Using the secret key, the server validates the token and extracts claims from it. If the user information from the claims exists in the server's user table, the server authenticates the user, granting access to requested resources.

[![Token based authentication](https://media.dev.to/cdn-cgi/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.amazonaws.com%2Fuploads%2Farticles%2Fvj8jdjwssswe9dcldfga.png)](https://media.dev.to/cdn-cgi/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.amazonaws.com%2Fuploads%2Farticles%2Fvj8jdjwssswe9dcldfga.png)