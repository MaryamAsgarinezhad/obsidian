**REST API (Representational State Transfer Application Programming Interface)**

1. **What is a REST API?**
    
    - A **REST API** is a set of rules that allow programs (often web services) to communicate with each other.
    - “REST” (Representational State Transfer) is an architectural style for building APIs, meaning it defines certain principles for how requests and responses should be structured to keep things consistent and loosely coupled.
    - An API (Application Programming Interface) is essentially a way for two different pieces of software to talk to each other.
      
2. **Key Characteristics of REST APIs**
   
    - **Stateless**: The server does not store client-specific context between requests. Each request from a client contains all the information needed to complete the request.
    - **Uniform Interface**: The same consistent rules and endpoints are used to interact with resources. Common HTTP methods like **GET**, **POST**, **PUT**, and **DELETE** are used to read, create, update, and delete data.
    - **Resource-based**: In REST, data is typically treated as resources, each accessible through a unique URI (Uniform Resource Identifier). For instance, if you were dealing with a user resource, you might see endpoints like `GET /users` or `GET /users/123`.
      
3. **How RESTful Interactions Usually Work**
   
	- The client (like a frontend app) sends an HTTP request to the server specifying an endpoint (resource location) and method (e.g., GET or POST).
	- The server processes that request and returns a response (often JSON or XML) along with an HTTP status code.
	- For example, fetching a user with ID = 123 might look like: 

```bash
`GET /users/123`
```
**Why REST APIs are Popular**

- **Simplicity**: HTTP methods are straightforward, and the resources often mirror real-world objects.
- **Scalability**: Stateless communication makes it easier to scale out servers and applications.

Notes:

- **Resources** are the main concept in REST (e.g., users, orders, products).
- **Each resource is identified by a unique URI** (Uniform Resource Identifier).
- **Standard HTTP methods (GET, POST, PUT, DELETE)** are used to manipulate resources.
- **Nested resources** show relationships (`GET /users/123/orders`).

---

### **Is REST Limited to HTTP?**

**No, REST is an architectural style, not a protocol.**  
It defines a set of principles for designing networked applications, and it can be implemented over different communication protocols, not just HTTP.

However, in practice: ✅ **REST is most commonly implemented using HTTP** because ==HTTP naturally supports REST’s principles like statelessness and resource-based interaction.==

But technically, REST **could be implemented over other protocols**, such as:

- **WebSockets**: For real-time communication.
- **CoAP (Constrained Application Protocol)**: Used in IoT devices.
- **gRPC (over HTTP/2, but not RESTful itself)**: More optimized for microservices.
- **Custom transport layers**: Like TCP or message queues.

![[Pasted image 20250310133804.png]]

---

**Ephemeral Key**

1. **What is an Ephemeral Key?**
    
    - An **ephemeral key** is a temporary, short-lived key that is created and used for a specific purpose or session. It is commonly used in cryptography and secure APIs to ==minimize the risk of exposing long-term secrets.==
    - The key is valid for a ==brief period or a single operation==, meaning that after it is used, it often expires or is discarded.
2. **Why Use Ephemeral Keys?**
    
    - **Security**: By using short-lived keys, attackers have less time to exploit a key if they somehow gain access to it.
    - **Limited Scope**: Often ephemeral keys are granted with ==minimal permissions or restricted access to a certain resource==. That way, even if the key is compromised, its usefulness is very limited and for a short time.
    - **Compliance**: Many data protection standards encourage or require temporary credentials, reducing the risk of large-scale breaches if a key is leaked.
3. **Practical Example**
    
    - **Stripe Ephemeral Keys**: In payment systems like Stripe, ephemeral keys are used to allow mobile apps to retrieve certain payment-related data without exposing the user’s private information. The key is only valid for a short session, preventing unauthorized misuse.
    - **Encryption Protocols**: In end-to-end encryption, ephemeral keys might be used for a single session or message exchange (for example, in secure messaging apps). After that exchange, the key is never reused.
4. **How an Ephemeral Key Might Be Used**
    
    1. A client requests an ephemeral key from a server, often authenticating itself with more permanent credentials in the process.
    2. The server generates a key that’s tied to a specific resource or set of actions.
    3. The client uses this ephemeral key for the allowed operations or queries.
    4. The key automatically expires after a certain time or once it’s used.

---

### **What is WebRTC?**

**WebRTC (Web Real-Time Communication)** is an open-source technology that enables **real-time audio, video, and data communication** between web browsers and mobile applications **without requiring plugins or external software**.

It allows peer-to-peer (P2P) communication directly between users, making it ideal for **video calls, voice chats, file sharing, and real-time data exchange** in web applications.

---

### **How WebRTC Works**

WebRTC enables **direct communication** between devices using a set of JavaScript APIs and protocols. It avoids the need for a central server for media transmission, reducing latency and improving performance.

WebRTC consists of three core components:

1. **MediaStream API** – Captures audio and video from the user’s device (camera/microphone).
2. **RTCPeerConnection API** – Handles peer-to-peer (P2P) communication and media streaming.
3. **RTCDataChannel API** – Allows direct data transfer between peers for messages, files, etc.

---

