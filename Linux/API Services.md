Start a fastapi service from scratch:

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
```
```

- We define all of our variables (which may be secret) in the docker-compose file or the docker file. 
- Access the ENV variables through "os" module. Since we run docker-compose or the docker file on the specific host and do not necessarily have to share it, and we set the host's env variables to our desired values, so our privacy is preserved.



- Below we show how to handle different types of client requests and take actions upon them:
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