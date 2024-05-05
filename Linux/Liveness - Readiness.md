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