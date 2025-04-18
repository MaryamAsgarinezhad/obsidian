
- A **readiness probe** is used to determine if a container is ready to start accepting traffic or connections. This is crucial for scenarios where an application might need time to initialize or where it might go through temporary states of unavailability.
- A **liveness probe** is used to determine if a container is still alive or if it needs to be restarted. It helps detect and rectify situations where the container might be running but is in a broken or deadlocked state.

### Combined Usage

- **Startup Probe**: Sometimes, a third type of probe called a **startup probe** is used alongside liveness and readiness probes, particularly for applications that take a long time to start up. The startup probe allows the application to fully initialize before the liveness and readiness probes take over.
- ![[Pasted image 20240505090736.png]]

These fields in the manifest file should be set to 'true':

![[Pasted image 20240504171738.png]]

---------------
code to be added:
![[Pasted image 20240505084526.png]]
### Explanation

1. **`httpGet`**:
    
    - This is an HTTP request that is made to check the health of the application.
    - The `path` is set to `/health` (assuming your application exposes a health endpoint there).
    - The `port` is set to `80` which matches your service configuration.
2. **`initialDelaySeconds`**:
    
    - The delay before the first probe is initiated.
    - Set to `10` seconds for readiness and `15` seconds for liveness to allow the application to start properly before checking health.
3. **`periodSeconds`**:
    
    - The frequency with which the probe is run.
    - Set to `5` seconds for readiness and `20` seconds for liveness.
4. **`timeoutSeconds`**:
    
    - The time to wait for a probe to succeed.
    - Set to `3` seconds, which is reasonable for most applications.
5. **`failureThreshold`**:
    
    - The number of consecutive failures for the probe before the container is considered unhealthy.
    - Set to `3` for both probes.
6. **`successThreshold`**:
    
    - The number of consecutive successes required to consider the container healthy after being unhealthy.
    - Set to `1` for both probes.

---------------------
You need to create the endpoints with the specified 'path' and 'port' in your application.

![[Pasted image 20240505085737.png]]
![[Pasted image 20240505085758.png]]

------------------------------------

what if the target port is 8080 and port is 8090 in the ports: section of the file?

If the application is configured to listen on port 8080 within the container and the service exposes it externally on port 8090, then your liveness and readiness probes should use port 8080, which corresponds to the **`targetPort`** defined in the service.
