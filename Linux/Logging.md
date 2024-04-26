
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