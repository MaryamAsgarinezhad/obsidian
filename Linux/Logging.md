
Docker captures stdout and stderr from the container's process, so you can simply log messages to stdout or stderr in your Python application, and Docker will handle the rest.

To view the logs of the container, run:
```shell
docker logs "container_id"
```

To view the logs of the container real time, run:
```shell
docker logs -f "container_id"
```
Example:
```python
import logging
import sys

# Configure logging to use stdout
logging.basicConfig(stream=sys.stdout, level=logging.INFO, format='%(asctime)s - %(levelname)s - %(message)s')

# Example usage
logging.info('This is an info message')
```

```python
# set logging configurations  
logging.basicConfig(stream=sys.stdout, level=logging.INFO, format='%(asctime)s - %(levelname)s - %(message)s')  
  
file_handler = logging.FileHandler('regression_cdn.log')  
file_handler.setLevel(logging.INFO)  
  
formatter = logging.Formatter('%(asctime)s - %(levelname)s - %(message)s')  
file_handler.setFormatter(formatter)  
  
logging.getLogger().addHandler(file_handler)
```

------------------------------------------------

loghandler:  
  enabled: true  
  instances:  
    - sink: default  
      sources:  
        - file: "/opt/sre/logs/app.log"  
          config:  
            parser:  
              '@type': none  
            tag: "sre-cdn-boom-api-auth.log"  
          rotate:  
            enabled: true  
            size: 10M

Adds log handler to the kuber cluster. (elastic is predefined in the cluster.)

- "- file:" ***should be the docker file workdir + name of the logging file defined in the application.***
- "-tag:" is the index of your loggings.

----------------------------------------------------
