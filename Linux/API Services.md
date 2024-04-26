Start a fastapi service from scratch:

-  `["uvicorn", "main:app", "--host", "0.0.0.0", "--port", "80", "--reload"]`: This command will run the Uvicorn ASGI server to serve the FastAPI application named `app` from the `main` module. Here's what each part does:
    - `"uvicorn"`: Specifies the command to run, which is the Uvicorn ASGI server.
    - `"main:app"`: Specifies the module and the object to run. In this case, it runs the `app` object from the `main` module.
    - `"--host", "0.0.0.0"`: Specifies the host on which the server will listen. In this case, it listens on all available network interfaces.
    - `"--port", "80"`: Specifies the port on which the server will listen. In this case, it listens on port 80.
    - `"--reload"`: Enables auto-reloading of the server when source files change, making it convenient for development.


Tip: The uvicorn server will be up and ready on