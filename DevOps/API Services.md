
**FastAPI is the framework you'll use to build your API**, and Uvicorn is the server that will use the API you build to serve requests.

**When our API framework doesn't have its own web server, like php, we use Apache ir Nginx web servers to serve the API application**

When a web server like Nginx or Uvicorn is installed and configured on a system, it transforms the hardware into a host capable of handling web requests. This process involves several key steps and components that work together to manage the incoming traffic, process requests, and serve responses. Here's an overview of how this transformation occurs:

### 1. **Installation and Configuration**

- **Software Installation**: The first step is installing the web server software on the system. This could be Nginx, a high-performance HTTP server and reverse proxy, or Uvicorn, an ASGI server for Python applications, typically used with frameworks like FastAPI or Starlette.
    
- **Configuration**: After installation, the web server is configured. This involves setting up configuration files that dictate how the server should handle different types of requests. Configurations may include ==defining listening ports, server blocks or virtual hosts, security settings (like SSL/TLS for HTTPS), routing rules, static resource locations, and more.==

### 2. **Binding to a Network Interface**

- **Listening on Ports**: Web servers are configured to listen on specific network ports (commonly HTTP on port 80 and HTTPS on port 443). When the server starts, ==it binds to these ports on the machine’s network interface==. This means the server claims these ports and starts listening for any incoming connections on them.

### 3. **Handling Incoming Requests**

- **Network Requests**: When a user or client (like a browser or another server) makes a request to the server, it is sent over the network to the IP address of the host, targeting the port where the web server is listening.
    
- **Accept Connections**: The web server **accepts incoming TCP connections** on the bound ports. TCP (Transmission Control Protocol) ensures that the data exchanged between the client and server is reliable, ordered, and error-checked.

### 4. ==**Processing Requests**==

- **Request Parsing**: Upon receiving a request, the web server parses the HTTP data to understand what is being requested. This could be a request for a static file (like an image or JavaScript file), dynamic content generation (like querying a database and generating HTML in response), or API data exchange.
    
- ==**Delegate to Handlers**==: Depending on the configuration and the nature of the request, the server might handle it directly (serve static content from disk) or delegate the handling to a backend application or script. For example, Nginx might serve static content directly, while proxying requests for dynamic content to a backend Python application running via Uvicorn.

### 5. **Generating and Sending Responses**

- **Response Generation**: After processing the request, the server or the delegated application generates an appropriate response. This could involve fetching data, executing application logic, or simply reading a file.
    
- **Send Response**: The response, along with appropriate HTTP headers, is sent back to the client via the established TCP connection.

### 6. **Resource and Connection Management**

- **Concurrency Handling**: Web servers like Nginx are designed to handle multiple requests concurrently. They use a combination of worker processes and asynchronous handling to manage multiple connections, making efficient use of system resources.
    
- **Closing Connections**: After the response is sent, the server may close the TCP connection or keep it open for a while (keep-alive) to handle possible subsequent requests from the same client.

-----------------------------------------------------

**Start a fastapi service from scratch:**

-  `["uvicorn", "main:app", "--host", "0.0.0.0", "--port", "80", "--reload"]`: This command will run the Uvicorn ASGI server to serve the FastAPI application named `app` from the `main` module. Here's what each part does:
    - `"uvicorn"`: Specifies the command to run, which is the Uvicorn ASGI server.
    - `"main:app"`: Specifies the module and the object to run. In this case, it runs the `app` object from the `main` module.
    - `"--host", "0.0.0.0"`: Specifies the host on which the server will listen. In this case, it listens on all available network interfaces.
    - `"--port", "80"`: Specifies the port on which the server will listen. In this case, it listens on port 80.
    - `"--reload"`: Enables auto-reloading of the server when source files change, making it convenient for development.


Tip: The uvicorn server will be up and ready where ever you run the above `["uvicorn", "main:app", "--host", "0.0.0.0", "--port", "80", "--reload"]` command.

If you run it on your local machine using "docker run" or directly "uvicorn main:app --host 0.0.0.0 --port 80 --reload" in the terminal **or on a remote server by using "ssh" command,** your server will be up there.


Tip: Using `--host localhost` instead of `--host 0.0.0.0` in a command like `uvicorn main:app --host localhost --port 80 --reload` would bind the server only to the localhost interface. This means that the server would only be accessible from the same machine where it is running.

On the other hand, using `--host 0.0.0.0` would bind the server to all available network interfaces on the machine, making it accessible from other devices on the same network. This is often used when you want to expose a service running in a Docker container to the outside world or to other containers running on the same host.


- To connect to a server on which there is an application running, the corresponding application port should be exposed. Then ping the ip:port address on which the application is running, or open the address in your browser.


**Writing a server application:**

For connecting to a database in the server application, use a **database adaptor for your programming language** like psycopg.
The `psychopg` package, often referred to as psycopg2, is a PostgreSQL adapter for the Python programming language. It allows Python code to interact with PostgreSQL databases, enabling tasks such as querying data, executing SQL commands, and managing database connections.
  
  ```pyhton
  connection = psycopg2.connect(user=os.getenv('DATABASE_USER'),  
                              password=os.getenv('DATABASE_PASSWORD'),  
                              host=os.getenv('DATABASE_HOST'),  
                              port=os.getenv('DATABASE_PORT'),  
                              database=os.getenv('DATABASE_NAME'))  
cursor = connection.cursor()  
cursor.execute(  
    """  
    CREATE TABLE IF NOT EXISTS messages (        id SERIAL PRIMARY KEY,        message TEXT NOT NULL    )    """)  
connection.commit()  
cursor.close()  
connection.close()
```

1. `user`: Specifies the username to authenticate with the PostgreSQL server. It is typically the username used to access the database.
2. `password`: Specifies the password to authenticate with the PostgreSQL server. This is the password associated with the specified username.
3. `host`: Specifies the hostname or IP address of the PostgreSQL server. This is the address where the PostgreSQL server is running.
4. `port`: Specifies the port number on which the PostgreSQL server is listening for connections. The default port for PostgreSQL is 5432.
5. `database`: Specifies the name of the database to connect to. This is the database within the PostgreSQL server that you want to interact with.

- We define all of our variables (which may be secret) in the docker-compose file or the docker file. 
- Access the ENV variables through "os" module. Since we run docker-compose or the docker file on the specific host and do not necessarily have to share it, and we set the host's env variables to our desired values, so our privacy is preserved.



- Below we show how to handle different types of client requests and take actions upon them (**this app is called an API**):
  ![[Pasted image 20240426161754.png]]


- To add the Liveness/Readiness, create a separate endpoint.

-------------------------------------------------------

Here are some common use cases for Nginx:

1. Web Server: Nginx can serve static content like HTML, CSS, and images, as well as dynamic content using FastCGI handlers for various languages like PHP, Python, and others.
    
2. Reverse Proxy: Nginx can act as a reverse proxy server, forwarding client requests to backend servers (e.g., application servers) and returning the responses to clients. This helps distribute the load among multiple servers and improves performance and security.
    
3. Load Balancer: Nginx can also function as a load balancer, distributing incoming traffic across multiple backend servers to improve reliability, scalability, and performance.
    
4. SSL/TLS Termination: Nginx can handle SSL/TLS encryption and decryption, offloading this task from backend servers to improve performance.
    
5. Caching: Nginx can cache static content and even dynamic content to reduce the load on backend servers and improve response times for clients.
    
6. Security: Nginx provides features like access control, rate limiting, and security headers to help protect web applications from attacks.
    

Overall, Nginx is a versatile and high-performance web server that is widely used to improve the performance, reliability, and security of web applications and services.

----------------------------------------

example for understanding the API:

Fastapi/springboot/flask,.... are web servers that can provide an API for the user. 

Postman/Curl,... are tools that help to communicate with that API. 

Http,... are protocols used to communicate to the API.

--------------------------------------

![[Pasted image 20240504140212.png]]
This way, use multithreading.

------------------------------
