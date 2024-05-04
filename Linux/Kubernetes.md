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
  You could use 'env variables' in the manifest file too.
  
- Ingress: provides secure protocol and domain name for accessing internal lusters from external network

-  An internal service (also known as a cluster-internal service) exposes applications only within the Kubernetes cluster.
-  An external service (also known as a cluster-external service) exposes applications outside the Kubernetes cluster, making them accessible from external networks.
  
- Volume: Attaches a physical (local or remote) storage on a hard drive to the pod to retain the data that may be lost due to pod crash

![[Pasted image 20240504201558.png]]

To avoid missing a crashed pod we replicate everything on multiple servers. We have another node where a "**replica**" of our application will run on that is connected to the **same service**.

**Services are also load balancers**, they catch the request and forward it to the least busy replica.

To create those replicas you create the deployment.yaml file where in you specify the number of pods that you need. **Deployment is an abstraction layer on pods,** meaning it makes it easier to work with pods.

Tip: Deployment is used for stateless sets 
For **Stateful apps** we use a resource called **StatefulSet** (Like DB's)

![[Pasted image 20240504202903.png]]
Communication between nodes are done by services. It catches the requests directed to the pod and forwards it to the respective pod.  

**Kube Proxy** is responsible for forwarding the request from services to pods in a way that the network overload is controlled. (for example send to the nearest pod)

----------------
**A node is a physical or virtual machine (VM)** that serves as a worker in a Kubernetes cluster. Each node runs the Kubernetes runtime components, such as the kubelet and container runtime (e.g., Docker, containerd).

Nodes are the cluster **servers** that actually do the work, that'swhy called worker nodes.

There are 3 processes  that must be installed on any worker node (or node):

1- Container runtime: It is responsible for managing the execution and lifecycle of containers within the Kubernetes environment.

2- Kubelet: The Kubelet is responsible for managing the deployment of pods to Kubernetes nodes.It receives commands from the API server and instructs the container runtime to start or stop containers as needed. (it is a process of kubernetes itself, Unlike the container runtime)

at the end of the day, Kubelet is responsible for taking the configuration and running pods with a container inside and **allocating resources to of the node to the pods** (by interacting with container and nodes)

------------------------------
![[Pasted image 20240504203659.png]]
All managing processes are done by master nodes using four processes that run on every master node

1- API Server: is a cluster gateway, **you interact it as a client** using UI interface, command line tool or a certain API when you want to deploy a new application. It also acts as a gateway authenticator which ensures **only authenticated users** can access the cluster. It verifies your request and then forwards it to the Scheduler to perform it.

2- Scheduler: **Decides what node** the user's commands is going to be performed on in an intelligent way **considering the available resources**. But actually the **Kubelet will execute** the command said by user and decided by the Scheduler.

3- Controller Manager: Detects the **cluster state changes** (like pod crashes) and recovers the cluster state as soon as possible. it makes a **request to the scheduler** to reschedule the dead pods ( decide based on the resource calculation which worker nodes should start those pods again, and request the corresponding kubelets on those nodes to restart the pods.)

4- etcd: key value store, cluster brain! Every chainge on the cluster state **will be saved on** it. Scheduler and Controller Manager will use its data to know :

![[Pasted image 20240504204231.png]]
- data base information will not be saved on etcd