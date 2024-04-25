- Docker needs the requirement.txt to have the libraries and packages installed in its environment.

- The commands you write in the docker file will be executed in the image container, which is like a dependent OS, not the local environment.

- Docker file is like a linux shell and bash scripts can be written there, like mkdir, chmod,...

- COPY every file you need for the execution of your app in the docker file and RUN them if needed (like requirements.txt)