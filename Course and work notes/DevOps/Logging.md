
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

In the context of log management, "**indexing**" refers to the process of organizing data (logs in this case) in a way that makes it easier to retrieve, analyze, and visualize efficiently.

Used by the ELK Stack (Elasticsearch, Logstash, Kibana), Elasticsearch indexes log data making it searchable in real-time. It allows for full-text search and complex queries facilitated by its Lucene backend.

- Define the index in the ELK in this section, with the name you specified in the "-tag" section of values.yaml file.
- ![[Pasted image 20240508161846.png]]
- ![[Pasted image 20240508162850.png]]

- you can view your logs in the ELK here: 
- ![[Pasted image 20240508162030.png]]


- In the "tag" section of the logs view, there is the tag we specified in the "-tag" section of the value.yaml file.
- ![[Pasted image 20240508162641.png]]

---------------------------------------------------

**Indexing**:

