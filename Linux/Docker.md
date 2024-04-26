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
```shell
docker exec -it container_name /bin/bash
```











This command is used to execute a command inside a running Docker container, used when you want to connect to the container as a user and run the command inside the container.