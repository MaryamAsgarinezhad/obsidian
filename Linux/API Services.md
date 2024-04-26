Start a fastapi service from scratch:

-  `["uvicorn", "main:app", "--host", "0.0.0.0", "--port", "80", "--reload"]`: This command will run the Uvicorn ASGI server to serve the FastAPI application named `app` from the `main` module. Here's what each part does:
    - `"uvicorn"`: Specifies the command to run, which is the Uvicorn ASGI server.
    - `"main:app"`: Specifies the module and the object to run. In this case, it runs the `app` object from the `main` module.
    - `"--host", "0.0.0.0"`: Specifies the host on which the server will listen. In this case, it listens on all available network interfaces.
    - `"--port", "80"`: Specifies the port on which the server will listen. In this case, it listens on port 80.
    - `"--reload"`: Enables auto-reloading of the server when source files change, making it convenient for development.


Tip: The uvicorn server will be up and ready where ever you run the above `["uvicorn", "main:app", "--host", "0.0.0.0", "--port", "80", "--reload"]` command.

If you run it on your local machine using "docker run" or directly "uvicorn main:app --host 0.0.0.0 --port 80 --reload" in the terminal or on a remote server by using "ssh" command, your server will be up there.


Tip: Using `--host localhost` instead of `--host 0.0.0.0` in a command like `uvicorn main:app --host localhost --port 80 --reload` would bind the server only to the localhost interface. This means that the server would only be accessible from the same machine where it is running.

On the other hand, using `--host 0.0.0.0` would bind the server to all available network interfaces on the machine, making it accessible from other devices on the same network. This is often used when you want to expose a service running in a Docker container to the outside world or to other containers running on the same host.


- To connect to a server on which there is an application running, we must first connect to the host using "SSH". After connecting to the host and then if the corresponding application port is exposed
