we send http requests to the kuber API when we write a kubectl command.

**HELM**:
Helm is a package manager for kuber (like apt or yum)

templates and dafault configurations are in "Chart.yaml" file

users can override the default values in the "values.yaml" file

![[Pasted image 20240504140540.png]

To use Helm, you typically follow these general steps:

Install Helm: First, you need to install the Helm client on your local machine. You can download and install Helm from the official Helm website or use a package manager like Homebrew on macOS or Chocolatey on Windows.

Add Chart Repositories (Optional): Helm uses repositories to store and distribute charts. You can add official or community-managed repositories to your Helm client using the helm repo add command. For example:

```shell
helm repo add stable https://charts.helm.sh/stable
```

Search for Charts: Once you've added repositories, you can search for available charts using the helm search command. For example:

```shell
helm search repo stable
```

Fetch Charts: Before you can install a chart, you need to fetch it from the repository using the helm pull command. For example:

```shell
helm pull stable/mysql
```

Customize Values (Optional): Helm charts often come with default configuration values. You can override these values by creating a values.yaml file or passing values directly via the command line when installing the chart.

Install Charts: To install a chart, use the helm install command followed by the name of the chart and any optional values. For example:

```shell
helm install my-release stable/mysql
```

Manage Releases: After installing a chart, Helm creates a release instance on your Kubernetes cluster. You can manage releases using various Helm commands, such as helm list, helm upgrade, helm rollback, helm delete, etc.

Create Your Own Charts (Optional): If you're developing an application, you can create your own Helm charts to package and deploy it. Helm provides a templating engine for generating Kubernetes manifests, making it easier to manage complex applications.

![[Pasted image 20240504141846.png]]

-----------------------
This exposes port 80 of the container (port) to be visibleas port 8080 (target port). This is exposing a port in kubernetes. 

"Port" should be similar to the port you have run your API service on (if you wanna expose it), and target port is what you should bind your localhost to.

![[Pasted image 20240504191401.png]]
Portforwarding is through this command:
```shell
kubectl port-forward svc/'name of the service' 8000:8080
```

----------------------------
- Kuber is a container orchestration tool to manage applications in **different deployment environments**.
- **Microservices** are a software development approach where applications are built as a collection of small, loosely coupled, independently deployable services, each responsible for a specific business function. These services are designed to do one thing well and communicate with each other over well-defined APIs, typically using lightweight protocols such as HTTP or messaging queues.
- rise of micro services offered container technology cause they are the best host for micro services

Features:

- High availability —> no downtime
- Scalability —> High performance
- Disaster recovery —> Backup and restore

Kuber components:

- Pod: An abstraction over container, by creating a layer on top of the container
- Virtual network: each pods gets its own ip address
- Services: pods are aphemeral and their ip become new —> services is a permanent ip addess for pods
- External Service: App should be accessible through browser using external service
- Configmap: the external configuration to the application. No need to rebuild the image and recommit and push the code in case where some external configurations (like database urls etc) changes.
  Pods connect to the configmap.
  
- Secret: Putting credentials like passwords or secret important in plain text format in configmap is not secure. Secret is just like Config map, used to store secret data (base64 encoded format)
  
- Ingress: provides secure protocol and domain name for accessing internal lusters from external network

-  An internal service (also known as a cluster-internal service) exposes applications only within the Kubernetes cluster.
-  An external service (also known as a cluster-external service) exposes applications outside the Kubernetes cluster, making them accessible from external networks.

![[Pasted image 20240504201558.png]]