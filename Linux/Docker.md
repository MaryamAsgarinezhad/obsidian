- Docker needs the requirement.txt to have the libraries and packages installed in its environment.

- The commands you write in the docker file will be executed in the image container, which is like a dependent OS, not the local environment.
  So every command you write in the termial when you don't use docker, should be included in the docker file when using doker (like the sever run command)

- Docker file is like a linux shell and bash scripts can be written there, like mkdir, chmod,... . Write "RUN" before writing bash scripts.

- COPY every file you need for the execution of your app in the docker file and RUN them if needed (like requirements.txt)
  
- Sample file COPY in docker file:
  
```python
WORKDIR /  
COPY main.py / 
```

- The above COPY command copies the main.py file from the current directory of your docker file and puts it in the working directory that you specified (here is root)
- You can create the folder of the WORKDIR with mkdir shell command.


- Use the EXPOSE command when running the docker image's API server on your local environment to access the server on your localhost and the specified exposed port(not when running on on a server like kuber)

- Pull the docker image from docker hub:
  
  ```shell
  sudo docker pull "name of the container"
  ```
```
```
- This command is used to execute a command inside a running Docker container, used when you want to connect to the container as a user and run the command inside the container.
  
  ```shell
docker exec -it container_name /bin/bash```

- To see the running and all containers:
  
	  ```shell
	  docker ps
	  docker ps -a
	  ```

- A Docker Compose file, usually named `docker-compose.yml`, is a YAML file used to define and run multi-container Docker applications. It allows you to define the services, networks, and volumes required for your application in a declarative way.
  
  **Service Definition**: Docker Compose allows you to define multiple services within your application, each with its own configuration, such as the Docker image to use, environment variables, ports to expose, volumes to mount, and dependencies on other services.
  
  Here's what each part does:

  ![[Pasted image 20240426134413.png]]

- `web`: This is the name of the service.
- `image: nginx:latest`: This specifies the Docker image to use for the `web` service. In this case, it uses the latest version of the Nginx image available on Docker Hub.
- `ports: - "8080:80"`: This maps port 8080 on the host to port 80 inside the container. So, you can access the Nginx web server running inside the container on port 8080 of your host machine.
- `volumes: - ./html:/usr/share/nginx/html`: This mounts the `./html` directory from your host machine to `/usr/share/nginx/html` inside the container. This allows you to serve your own HTML files through Nginx.
- `depends_on: - db`: This specifies that the `web` service depends on the `db` service. It ensures that the `db` service is started before the `web` service.


let's say you have a Docker Compose file with multiple services, and you want to define a custom network to isolate some of these services. Here's an example:

- ![[Pasted image 20240426134852.png]]


- - There are two services defined: `frontend` and `backend`.
- The `frontend` service exposes port 8080 on the host and uses the `frontend:latest` image.
- The `backend` service uses the `backend:latest` image.
- Both services are connected to a custom network called `mynetwork` using the `networks` section.
- The `mynetwork` network is defined at the bottom of the file with the `driver` set to `bridge`. This means it will be a bridge network, which is the default network driver in Docker Compose.

By defining a custom network, you can isolate the `frontend` and `backend` services from other services in your application. Additionally, containers within the same network can communicate with each other using service names as hostnames, making it easy to establish communication between services.