![[Pasted image 20240915131336.png]]

The fair amount of **compute** ML systems need is met by distributed systems.

![[Pasted image 20240915141436.png]]

![[Pasted image 20240915141521.png]]

How does Ray do that?
- Bottom: All cloud systems (kuber, aws, cloud) on which you can run ray.
- middle: Ray framework itself, a general purposed distributed computing system.
  It doesn't deal with any specific workflow or abstraction. But it provides primitive apis tat can be applied any where.
  ![[Pasted image 20240915142902.png]]

- Native libraries: use primitive APIs to actually handle specific workloads:
  Distributed training: Ray train
  RL: rllib
  Tune parameters: tune

To write your application in a distributed manner, you use these APIs ray provide.
![[Pasted image 20240915144519.png]]

![[Pasted image 20240915144832.png]]

-------------------------

Different distributed systems and use cases:

- **Spark** is ideal for in-memory, distributed data processing across large datasets.
- **Dask** is Python-based and more suitable for scaling data science workflows on a single machine or a cluster.
- **Hadoop** is focused on batch processing with disk-based storage, using the MapReduce paradigm for large-scale distributed computation.
- **HPC** is focused on massively parallel computation for solving computationally intensive problems, often in scientific and engineering domains.

----------------------------

![[Pasted image 20240915145425.png]]

- Driver: a worker process which happens to be running anywhere
- GCS: global meta directory that keeps track of all resources available, log of actions, ...
- Raylets: Used for distributed scheduling. They are peer to peer connections that keep each other updated about their resources, actions,... . They are also responsible to create worker processes.
- Distributed object stores run on shared memory.

![[Pasted image 20240915150343.png]]

Raylets are communicators between nodes.whenever you want to schedule a particular job the raylets look for available recources (GPUs ,...)

-------------------------

![[Pasted image 20240915151045.png]]

There are three design patterns associated with ray:

![[Pasted image 20240915151200.png]]

1. All your functions can be converted into parallel ray tasks and run as units of execution anywhere in the cluster, not necessarily at a central place.
2. Actors keep state of a process (training, ...) from object storage.
3. ![[Pasted image 20240915152632.png]]
4. ![[Pasted image 20240915152906.png]]
   ==Lines are executed paralelly but if one is dependent on the other, it will wait unita they finish.==
---------

![[Pasted image 20240915153526.png]]

Every node has access to every data each process creates.
Raylets schedule tasks.

----------------------

![[Pasted image 20240915163832.png]]

- library supporting search algorithms that allow you to run in parallel.
- You can choose between search algorithms and scheduler in your code.  
- Its easy to use in API, you just use tune, you have your training function and run train model.
- ==It will be run on as a single process that can use all the cords and multiple gpus on the same machine, or multi-node and multicluster.==
- It is compatible with all ML libraries.
- You can have other libraries run as a subroutine. **I mean the train_model could be a pytorch function.**
- We can also use ray train (instead of ray tune) to do training.

----------------------------------

How is the best parameter found?
![[Pasted image 20240915164520.png]]
![[Pasted image 20240916121156.png]]
![[Pasted image 20240916121308.png]]
![[Pasted image 20240916121444.png]]

------------------------
![[Pasted image 20240915165347.png]]
![[Pasted image 20240915165443.png]]
![[Pasted image 20240916123102.png]]
![[Pasted image 20240915165627.png]]
![[Pasted image 20240915165633.png]]

-------------------------------------------

==What is XGBoost==? XGBoost, which stands for Extreme Gradient Boosting, is **a scalable, distributed gradient-boosted decision tree (GBDT) machine learning library**. It provides parallel tree boosting and is the leading machine learning library for regression, classification, and ranking problems.

The XGBoost-Ray project **provides an interface to run XGBoost training and prediction jobs on a Ray cluster**. It allows to utilize distributed data representations, such as Modin dataframes, as well as distributed loading from cloud storage (e.g. Parquet files).
![[Pasted image 20240916123519.png]]

This is how to use XGBoost-Ray instead of XGBoost **for training**:
![[Pasted image 20240916125042.png]]

-------------------------------

![[Pasted image 20240916123959.png]]

-------------------------------------

A **Ray Job** refers to a distributed task or set of tasks executed using **Ray**, a framework designed for building and running scalable and distributed applications. Ray allows developers to scale Python applications easily by distributing computations across multiple nodes or machines. A Ray Job typically includes:

1. **Remote Functions (Tasks)**: Functions that are run in parallel across a cluster of machines.
2. **Actors**: Objects that can maintain state and perform tasks across different machines.
3. **Ray Clusters**: A group of connected machines (nodes) that work together to distribute and run the jobs.

### Use Cases for Ray Jobs:

1. **Distributed Machine Learning**: Training models on large datasets across multiple GPUs or nodes.
2. **Hyperparameter Tuning**: Scaling up the search for optimal model parameters using parallelization.
3. **Reinforcement Learning**: Ray includes libraries like **RLlib** to handle large-scale reinforcement learning tasks.
4. **Data Processing**: Using **Ray Datasets** for distributed data loading and processing in parallel.

---------------------------

KubeRay is an open-source project that integrates Ray with Kubernetes, allowing you to deploy and manage Ray clusters natively within a Kubernetes environment. KubeRay provides the necessary tooling and controllers to run Ray jobs on Kubernetes, leveraging the scalability and orchestration features of Kubernetes alongside the distributed computing power of Ray.

### Key Features of KubeRay:
    
4. **Multi-Tenancy Support**: KubeRay supports running ==multiple Ray clusters on the same Kubernetes cluster==, enabling multiple workloads or teams to share the same infrastructure while keeping their jobs isolated.
    
5. **Resource Scheduling**: With Kubernetes' resource scheduling capabilities, KubeRay ensures efficient allocation of CPU, GPU, and memory resources across nodes for Ray jobs.
    
6. **Fault Tolerance and Monitoring**: Kubernetes' native fault tolerance features ensure that if any part of the Ray cluster (like a node or pod) fails, it can be automatically restarted or rescheduled. KubeRay also supports monitoring through Kubernetes-native tools like Prometheus and Grafana.

### ==**KubeRay Components:**==

- **RayCluster CRD (Custom Resource Definition)**: This is the main resource used to define a Ray cluster within Kubernetes. It specifies details like head node configuration, worker node specifications, and scaling policies.
    
- **Ray Operator**: The Ray Operator is a controller that manages Ray clusters in Kubernetes. It handles the lifecycle of Ray clusters, including creation, scaling, and teardown.


**The relationship between KubeRay and the RayCluster Operator is that KubeRay provides the infrastructure for managing and deploying Ray clusters on Kubernetes, and the RayCluster Operator is the specific component within KubeRay that handles the lifecycle of Ray clusters.** 


### Workflow Example:

1. **Define RayCluster Resource**: You write a YAML manifest to describe your Ray cluster, including the number of head and worker nodes, resource requirements (e.g., CPU, GPU, memory), and auto-scaling policies.
    
2. **Submit the Job**: Using Kubernetes' `kubectl` command, you submit the RayCluster resource definition. The KubeRay Operator then provisions the required resources and spins up the Ray head and worker nodes.
    
3. **Run Ray Jobs**: Once the Ray cluster is ready, you can submit Ray jobs to it using the Ray client, Python APIs, or through the Ray Dashboard, and it will distribute the tasks across the nodes.
    
4. **Monitor and Scale**: As the job runs, you can monitor the resource usage, logs, and job status using Kubernetes dashboards and tools like Grafana or Prometheus. You can also scale the cluster dynamically if needed.

---------------------------------------

Training a lightweight LLM (Language Learning Model) on a Kubernetes cluster with GPUs using **KubeRay** in a distributed manner involves several steps. Here's a detailed guide on how to set up the environment and start the distributed training of the LLM model.

### Prerequisites

1. **Kubernetes Cluster with GPUs**: Ensure your cluster has GPU nodes configured and GPU drivers installed (e.g., NVIDIA drivers for CUDA-compatible GPUs).
2. **KubeRay Installed**: Ensure that KubeRay is installed and configured on your Kubernetes cluster.
3. **Ray Installed in your Python Environment**: You need to have Ray installed within your environment or container.
4. **Training Script**: A lightweight LLM model script (e.g., using Hugging Face’s `transformers` library or a custom model).

----------------------------------

A **Kubernetes Operator** is a custom controller that extends the Kubernetes API to ==manage complex, stateful applications== or services. Operators are designed to automate the deployment, scaling, management, and operational tasks (like backup, upgrade, recovery) of specific applications, ensuring they run reliably on Kubernetes.

### Key Concepts of a Kubernetes Operator:

1. **Custom Resources (CRs)**: Operators often define **Custom Resource Definitions (CRDs)**, which extend Kubernetes by adding new resource types that ==represent the application's domain-specific knowledge== (e.g., a database, distributed service, or other complex applications). These custom resources act like any other Kubernetes objects (e.g., Pods, Services), but they are tailored to the needs of the specific application.
    
2. **Controllers**: Operators rely on Kubernetes **controllers** that ==monitor the state of CRs and make sure the desired state matches the actual state.== When there is a difference, the controller takes action to reconcile the state automatically, like starting new pods, performing a database backup, or updating the application.
    
3. **Declarative Management**: Similar to other Kubernetes resources, you declare the desired state of the application in a YAML manifest (e.g., number of replicas, version, resource allocation), and the operator ensures that the actual state aligns with this specification.

------------------------------
### **RayCluster Operator**:

- The **RayCluster Operator** is a part of KubeRay. It is a **Kubernetes operator** that manages the lifecycle of **Ray clusters** by interacting with the Kubernetes API.
- The operator watches for custom resources (specifically, the `RayCluster` CRD) and performs operations to ensure the desired state of the Ray cluster is achieved and maintained (e.g., starting head and worker nodes, scaling them, restarting them upon failure).

### Workflow Overview:

1. **User Interaction**: The user defines a `RayCluster` resource (e.g., a YAML file) with details about the head and worker nodes, resource requests, and scaling configurations.
2. **RayCluster CRD**: This resource is submitted to the Kubernetes API.
3. **RayCluster Operator**: The operator watches for new or updated `RayCluster` resources and acts accordingly:
    - It creates the necessary Pods for the head node and worker nodes.
    - It manages their lifecycle, including scaling, restarting, and reconfiguring nodes.
4. **Ray Cluster Deployment**: The operator ensures that the Ray cluster is deployed and running correctly, as specified by the `RayCluster` resource.

----------------------

The ports 6379 and 8265 are standard ports used by Ray for specific purposes within the Ray cluster:
1. Port 6379 (Redis):

    Usage: This port is used by Redis, a key-value store that Ray uses internally to manage the cluster’s state, including task scheduling, resource management, and communication between nodes.
    Why Redis?: Ray uses Redis to store metadata, such as the state of tasks, worker node availability, and other information necessary for coordinating distributed computation. Redis acts as a centralized, in-memory database that allows Ray to efficiently manage and share state across the cluster.
    Default Port: By default, Redis listens on port 6379, which is a widely recognized default for Redis.

2. Port 8265 (Ray Dashboard):

    Usage: This port is used by the Ray Dashboard, a web interface that allows you to monitor and manage the Ray cluster. The dashboard provides insights into task execution, resource usage (CPU, GPU, memory), node health, and job statuses.
    Why 8265?: The Ray Dashboard uses port 8265 by default for its web interface, making it accessible from a browser. The port number is chosen to avoid conflicts with other common services, though you can change this port if needed.


The head and worker nodes in a Ray cluster use the **same Docker image** because they both require the same set of core Ray functionalities and libraries, even though they perform different roles. Here's why:

### 1. **Core Ray Functionality**:

- Both the **head** and **worker** nodes need to run the **Ray runtime**, which includes:
    - Task scheduling and execution.
    - Communication with other nodes.
    - Resource management (CPU, GPU, memory).


------------------------

To specify Kubernetes nodes that have GPUs in your Ray cluster YAML file, you can leverage **node selectors** or **node affinity**. These mechanisms help ensure that the head and worker pods are scheduled on the appropriate nodes that have GPUs.

In Kubernetes, node labels are used to identify nodes with specific attributes (such as having a GPU). Typically, nodes with GPUs will have a label like `nvidia.com/gpu`. You can then use node selectors or affinities to ensure your Ray pods are scheduled on these nodes.

- If your GPU node has a specific **name**, you can directly schedule your Ray pods (head and worker) to run on that node by using **node affinity** or **nodeSelector** targeting the node's name. This approach will ensure that the pods are scheduled on that particular node.

```yaml
apiVersion: ray.io/v1alpha1
kind: RayCluster
metadata:
  name: ray-cluster
spec:
  rayVersion: "2.3.0"
  headGroupSpec:
    replicas: 1
    template:
      spec:
        nodeSelector:
          kubernetes.io/hostname: "gpu-node-name"
        containers:
        - name: ray-head
          image: rayproject/ray-ml:latest-gpu
          resources:
            limits:
              nvidia.com/gpu: 1
              cpu: 4
              memory: 16Gi
          ports:
            - containerPort: 6379
            - containerPort: 8265
  workerGroupSpecs:
    - groupName: ray-worker
      replicas: 1
      template:
        spec:
          nodeSelector:
            kubernetes.io/hostname: "gpu-node-name"
          containers:
          - name: ray-worker
            image: rayproject/ray-ml:latest-gpu
            resources:
              limits:
                nvidia.com/gpu: 1
                cpu: 4
                memory: 16Gi

```


------------------------

A taint in Kubernetes is a way to mark a node so that it repels certain pods, meaning it prevents pods from being scheduled on that node unless they explicitly "tolerate" the taint. Taints are applied to nodes, and they control which pods are allowed to be scheduled on them. Pods that don’t have a matching toleration will be prevented from running on a node with a taint.

```yaml
taints:
- effect: NoSchedule
  key: nodepool
  value: gpu-platform
```

- The `NoSchedule` effect means that **no new pods** will be scheduled on this node unless they have a **matching toleration**.
  
- A **node pool** is a group of nodes within a Kubernetes cluster that share the same configuration or characteristics, such as the same machine type, operating system, or resource capabilities (like GPUs or high-memory instances). It allows for easier management of nodes with different requirements, like separating general-purpose nodes from specialized ones (e.g., GPU-enabled nodes for machine learning workloads).
  
- The `key` defines the name or type of the taint. In this case, the key is `nodepool`, which is a label used to group nodes into different pools (like GPU or CPU).
  
- The `value` provides additional specificity for the taint. In this case, the value `gpu-platform` is used to indicate that this node belongs to a node pool designed for **GPU workloads**.

```yaml
apiVersion: ray.io/v1alpha1
kind: RayCluster
metadata:
  name: ray-cluster
spec:
  rayVersion: "2.3.0"
  headGroupSpec:
    serviceType: LoadBalancer
    replicas: 1
    rayStartParams:
      include-dashboard: "true"
      dashboard-host: "0.0.0.0"
      dashboard-port: "8265"
      disable-usage-stats: "True"
      ray-client-server-port: "10001"
      enable-dashboard-auth: "false"
    template:
      spec:
        tolerations:
        - key: "nodepool"
          operator: "Equal"
          value: "gpu-platform"
          effect: "NoSchedule"
        containers:
        - name: ray-head
          image: docker.mci.dev/rayproject/ray-ml:latest-gpu
          resources:
            limits:
              nvidia.com/gpu: 1
              cpu: 4
              memory: 16Gi
          ports:
            - name: redis
              containerPort: 6379
            - name: dashboard
              containerPort: 8265
            - name: client
              containerPort: 10001
  
  workerGroupSpecs:
    - groupName: ray-worker
      replicas: 1
      minReplicas: 1
      maxReplicas: 10
      rayStartParams:
        num-cpus: "4"
      template:
        spec:
          tolerations:
          - key: "nodepool"
            operator: "Equal"
            value: "gpu-platform"
            effect: "NoSchedule"
          containers:
          - name: ray-worker
            image: docker.mci.dev/rayproject/ray-ml:latest-gpu
            resources:
              limits:
                nvidia.com/gpu: 1
                cpu: 4
                memory: 16Gi

```

**Added `ray-client-server-port: "10001"`** under `rayStartParams` to ensure the Ray Client Server runs on port 10001.

**Added `containerPort: 10001`** under `ports` to expose the port on the container.

**Ensured `serviceType` is set appropriately**:

- Use `ClusterIP` If you're running the `ray submit` command **from within the Kubernetes cluster**.
  
- Use `LoadBalancer` If you're running the `ray submit` command **from outside the Kubernetes cluster**.

**Ensure the Ray Dashboard and Job Submission Server Are Enabled**

- In your `ray-cluster.yaml`, make sure that the Ray head node is configured to enable the dashboard and job submission server.

```yaml
headGroupSpec:
  rayStartParams:
    include-dashboard: "true"
    dashboard-host: "0.0.0.0"
    dashboard-port: "8265"
    disable-usage-stats: "True"
    # Enable the Job Submission Server
    enable-dashboard-auth: "false"

```

-----------------

Your training script should use **Ray** to distribute the training job across the cluster. You can leverage **Ray Train**, which provides a high-level API for distributed training.

```python
import ray
from ray import train
from ray.train.torch import TorchTrainer
from transformers import AutoModelForSequenceClassification, AutoTokenizer
from sklearn.datasets import fetch_20newsgroups
from sklearn.model_selection import train_test_split
from torch.utils.data import Dataset, DataLoader
import torch

newsgroups_train = fetch_20newsgroups(subset='train')
X_train, y_train = newsgroups_train.data, newsgroups_train.target

class NewsgroupsDataset(Dataset):
    def __init__(self, texts, labels, tokenizer):
        self.texts = texts
        self.labels = labels
        self.tokenizer = tokenizer

    def __len__(self):
        return len(self.texts)

    def __getitem__(self, idx):
        text = self.texts[idx]
        label = self.labels[idx]
        inputs = self.tokenizer(text, return_tensors="pt", padding=True, truncation=True, max_length=512)
        return {key: value.squeeze(0) for key, value in inputs.items()}, torch.tensor(label)

def train_model(config):
    model_name = "distilbert-base-uncased"
    tokenizer = AutoTokenizer.from_pretrained(model_name)
    model = AutoModelForSequenceClassification.from_pretrained(model_name, num_labels=20).to("cuda")

    train_dataset = NewsgroupsDataset(X_train, y_train, tokenizer)
    train_loader = DataLoader(train_dataset, batch_size=config["batch_size"], shuffle=True)

    optimizer = torch.optim.Adam(model.parameters(), lr=config["lr"])

    for epoch in range(config["epochs"]):
        for batch in train_loader:
            inputs, labels = batch
            inputs = {key: value.to("cuda") for key, value in inputs.items()}
            labels = labels.to("cuda")

            # Forward pass
            outputs = model(**inputs, labels=labels)
            loss = outputs.loss

            # Backward pass and optimization
            optimizer.zero_grad()
            loss.backward()
            optimizer.step()

        # Print loss
        if epoch % 1 == 0:
            print(f"Epoch {epoch}, Loss: {loss.item()}")

# ray.init(address="auto")

#distributed training
trainer = TorchTrainer(
    train_func=train_model,
    scaling_config=train.ScalingConfig(
        num_workers=4,
        use_gpu=True
    ),
    config={"epochs": 3, "batch_size": 16, "lr": 2e-5}
)


trainer.fit()
```

### **Understanding `ray submit`**

- **`ray submit`** is used to submit Python scripts to a Ray cluster for execution.
- It requires network access tao the **Ray head node** to communicate and submit jobs.

```bash
ray submit ray-cluster.yaml your_training_script.py

//or

ray submit --address <external-ip>:10001 ray-cluster.yaml your_training_script.py

```

- You can modify the model (`distilbert-base-uncased`), batch size, learning rate, and number of epochs based on your requirements.
  
- A custom `NewsgroupsDataset` class is created to **==handle tokenization==** and ==**text-label pairing**== for PyTorch.

- `TorchTrainer` from Ray is used to distribute the training job across multiple workers and GPUs.

--------------------

**`inputs.items()`**:

- `inputs` is a dictionary returned by the tokenizer (e.g., `AutoTokenizer` from the Hugging Face `transformers` library). The tokenizer processes the text and returns a dictionary where the keys represent tokenized inputs (like `input_ids`, `attention_mask`, etc.), and the values are tensors representing these inputs.
  
- `inputs.items()` is a generator that returns key-value pairs from this dictionary.

**`value.squeeze(0)`**:

- `squeeze(0)` removes the first dimension (dimension `0`) from the tensor if its size is 1. This is typically done to remove extra dimensions added by the tokenizer when batching isn't used.
- For example, the tokenizer returns tensors with shape `(1, sequence_length)` (batch size of 1), and `squeeze(0)` reduces this to `(sequence_length)`.

**Dictionary comprehension (`{key: value.squeeze(0) for key, value in inputs.items()}`)**:

- This iterates over the key-value pairs in the `inputs` dictionary and creates a new dictionary where the values have had the extra dimension squeezed out.

**`torch.tensor(label)`**:

- This converts the label (which is originally an integer) into a PyTorch tensor. ==This is necessary because PyTorch models expect both the input data and the labels to be tensors.==

device = get_device() model.to(device) # Load the training dataset train_dataset = NewsgroupsDataset(X_train, y_train, tokenizer) train_sampler = torch.utils.data.distributed.DistributedSampler( train_dataset, num_replicas=train.get_world_size(), rank=train.get_rank(), shuffle=True, )


-----------------------------

### **Why Is the ==Dashboard Port== Important?**

1. **Monitoring and Debugging**:
    
    - The dashboard provides a comprehensive view of the cluster's health and performance.
    - It helps in identifying bottlenecks, resource contention, and failed tasks.
    - You can visualize task execution timelines and resource usage graphs.
2. **Job Submission API**:
    
    - The **Ray Job Submission Server** listens on the dashboard port (8265).
    - It allows you to submit jobs to the Ray cluster programmatically or via the command line using `ray job submit`.
    - This is especially useful in Kubernetes environments, where you might not have direct access to the cluster nodes.

**Ensure Your Script Does Not Call `ray.init() for job submission`**

==When using `ray job submit`, your script **should not** call `ray.init()` directly. The job submission server handles the initialization.== # Remove ray.init()

```shell
# Port-forward the dashboard port if necessary
kubectl port-forward service/ray-cluster-head-svc 8265:8265

# Submit the job
ray job submit --address http://127.0.0.1:8265 --working-dir . -- python your_training_script.py

```

--------------

Tip: Use ray.init

```python
ray.init(address="127.0.0.1:10001")
```

You can port-forward or use external IP to define the address of your ray cluster for ray to be initiated at.
No need for "ray job submit" or "ray submit".

---------------------------

To add another node to this Ray cluster, run
ray start --address='10.0.83.11:6379'
To connect to this Ray cluster:
import ray
ray.init()
To submit a Ray job using the Ray Jobs CLI:
RAY_ADDRESS='http://10.0.83.11:8265' ray job submit --working-dir . -- python my_script.py
See https://docs.ray.io/en/latest/cluster/running-applications/job-submission/index.html 
for more information on submitting Ray jobs to the Ray cluster.
To terminate the Ray runtime, run
ray stop
To view the status of the cluster, use
ray status
To monitor and debug Ray, view the dashboard at 
10.0.83.11:8265

------------------------------

The error message indicates that your Python script is attempting to connect to an existing Ray cluster but is failing to connect to the Global Control Store (GCS) service. The GCS is a critical part of the Ray architecture, responsible for managing metadata like task state and actor lifecycle.

-------------------------

To run ray commands from outside kubercluster:

1. port forward or get an external ip deom service
2. ray job list --address http://127.0.0.1:8265

Or you can exce -it into the head node container and run the command there.

--------------------
### 1. **Ray Core**

**Ray Core** is the foundation of Ray's functionality. It provides a ==simple and flexible API== for **parallelism** and **distributed computing**. Using Ray Core, you can ==scale Python functions== (tasks) and objects (data) across multiple CPUs, GPUs, and nodes in a cluster without needing to worry about ==complex low-level details like message passing or load balancing.==

#### Key Concepts in Ray Core:

- **Remote Functions (Tasks)**: In Ray Core, you can define functions that run asynchronously and can be scheduled across a cluster. Ray automatically handles the task scheduling, execution, and returning of results. You declare a remote function using `@ray.remote`.

- **Object Store**: Ray Core comes with a distributed **in-memory object store**, which allows efficient sharing of data between workers in a distributed cluster. This means that data can be passed between tasks without unnecessary duplication or serialization/deserialization overhead.

- **Ray Cluster**: Ray Core abstracts away the complexity of managing clusters. You can start Ray on multiple nodes and Ray will handle communication, scheduling, and fault tolerance. All you need to do is call `ray.init()`.

----------------------

### 2. **Ray Actors**

**Ray Actors** ==extend== the functionality of Ray Core by allowing you to create **stateful** objects that run on a separate process. While Ray Tasks (remote functions) are stateless and run independently, **Ray Actors** allow you to maintain mutable state across different function calls. This is particularly useful when you need a persistent object (e.g., a machine learning model) that you can update or query over time.

Ray Actors work similarly to classes in Python, where each instance of an actor can have its own methods and attributes. However, the actor runs in a separate process and communicates with other processes asynchronously.

#### Key Concepts in Ray Actors:

- **Actor Definition**: To define an actor, you use `@ray.remote` on a class instead of a function. The class's methods can be executed asynchronously and will operate on the **state stored within** the actor instance.
  
- **Concurrency**: Each actor runs on its own process, which means different methods of the actor can be called concurrently. However, Ray ensures that an actor’s methods are executed in the order they are called.
    
- **Fault Tolerance**: Ray Actors are fault-tolerant. If an actor process fails, Ray can automatically restart it, maintaining the actor's state by using checkpoints or allowing you to restore from failure manually.

----------------

### When is `ray.init()` necessary?

You typically use `ray.init()` when:

- You are developing and running the code locally.
- You are connecting to an existing Ray cluster manually from your local machine or when you're running Ray in a non-job submission scenario (e.g., in interactive sessions like Jupyter notebooks).

--------------------

Distributing training on ==multiple GPUs:==

### 1. **Ray Train API**

Ray provides the `Ray Train` library, which simplifies distributed training across multiple machines or GPUs. You don't need to define Ray actors or use `@ray.remote`. Instead, you can use the high-level API that Ray provides for managing distributed training. Here’s an example:

```python
from ray.train import Trainer

def train_func(config):
    # Your training logic here
    for epoch in range(config["epochs"]):
        # Training code, e.g., forward pass, backpropagation
        pass

trainer = Trainer(backend="torch", num_workers=4, use_gpu=True)
trainer.start()

# Specify your training configuration
trainer.run(train_func, config={"epochs": 10})
trainer.shutdown()

```

### 3. **Ray's Built-in Framework Integrations (e.g., PyTorch, TensorFlow)**

Ray natively integrates with machine learning frameworks like PyTorch and TensorFlow, allowing distributed training without the need for Ray actors or `@ray.remote`. For example, using `TorchTrainer` for PyTorch or `TFTrainer` for TensorFlow abstracts the distributed training logic.

*==In your code, even though you haven’t explicitly called `ray.train`, you are using **Ray’s TorchTrainer** and the `ScalingConfig` and `RunConfig` from Ray's **Ray AIR** (Accelerated ML) API, which internally uses Ray actors to handle distributed and parallel computation. This is why you are seeing `RayTrainWorker` in the Ray dashboard.==*

-------------

- **Ray Tasks** (stateless, invoked with `@ray.remote`): ==Functions== that are executed in parallel but do not maintain any state across executions.
- **Ray Actors** (stateful, also invoked with `@ray.remote`): ==Objects== that *maintain state across method calls* and allow multiple tasks to interact with the same object concurrently.

------------------

### Explanation of `num_workers` in `DataLoader`:

- **`num_workers=4`**: This means that **4 subprocesses** will be created to load the data in parallel. Each of these subprocesses will retrieve and prepare batches of data from the dataset.
  
- **Purpose**: By using multiple workers, the data loading process becomes faster, especially for large datasets. The workers can load data from disk and preprocess it (e.g., tokenization, augmentation) in parallel, while the main training loop continues to run on the GPU. This helps keep the GPU busy and avoids bottlenecks caused by slow data loading. **num_workers** parameter of the PyTorch `DataLoader` does **not** run subprocesses on the GPU. The subprocesses created by the `DataLoader` run on the **CPU**, not on the GPU.
  
- **Scaling**: If you want to use more GPUs or adjust the number of workers, just update the `num_workers` in the `ScalingConfig`.

---------------------------

**Expose/Escrape/visualize metrics:** 

#### 1. **Expose Ray Metrics Endpoint**

Ray clusters expose their metrics through an HTTP endpoint on the head and worker nodes. By default, this endpoint is typically on **port 8080**. Ensure that this port is exposed in your **KubeRay** deployment YAML file so that Prometheus can access the metrics.

![[Pasted image 20241001170205.png]]

To scrape exposed Ray metrics using **Prometheus Operator** in Kubernetes with a **ServiceMonitor**, you can follow these steps:

#### 2.**Create a ServiceMonitor for Ray Metrics**

Now, create a **ServiceMonitor** resource to tell Prometheus to scrape the metrics exposed by the Ray cluster.

Here’s an example `ServiceMonitor` configuration that scrapes the Ray metrics from the `ray-metrics` service:

```yaml
apiVersion: monitoring.coreos.com/v1
kind: ServiceMonitor
metadata:
  name: ray-metrics-monitor
  labels:
    release: prometheus  #matches Prometheus deployment label
spec:
  selector:
    matchLabels:
      app: ray-cluster-head-svc # match ray service labels not name 
  endpoints:
    - port: metrics # match metrics port name
      interval: 15s
  namespaceSelector:
    matchNames:
      - sre-dev
  targetLabels:
    - app
  podTargetLabels:
    - ray.io/cluster
```

This service will expose the metrics endpoint for all the pods in the `ray-cluster`.

```bash
kubectl apply -f ray-metrics-monitor.yaml

kubectl port-forward svc/prometheus-kube-prometheus-stack-prometheus 9090:9090
```

-----------------

Ray's `Trainer` has been deprecated, and in newer versions of Ray, distributed training functionality has been moved under the `ray.air` module (formerly known as `ray.train`). The newer way to perform distributed training uses `ray.air`.

- `TorchTrainer` automatically uses PyTorch's `DistributedDataParallel` (DDP) under the hood for multi-GPU training when the `scaling_config` specifies `num_workers` greater than 1 and `use_gpu=True`.

- Each worker (which corresponds to a GPU) gets its own instance of the model and processes different batches of the data. Gradients are synchronized across all GPUs at each step, which is the standard way of parallelizing the training task across multiple GPUs.

- The `TorchTrainer` internally manages the process group setup, data partitioning, and communication between the GPUs via DDP.

```python
#wrapped the model, specifying device_ids to ensure each model replica is on the correct GPU
model = torch.nn.parallel.DistributedDataParallel(model, device_ids=[device])

train_dataset = NewsgroupsDataset(X_train, y_train, tokenizer)  

# ensure that each worker processes a unique subset of the data
sampler = DistributedSampler(train_dataset)
```

==**nvidia-smi**==: This command will show if both GPUs are being utilized.

- The `ScalingConfig` argument specifies that two workers should be used (`num_workers=2`), and you’ve also set `use_gpu=True`, which indicates that Ray should **allocate a GPU to each worker** if available.

-----------------

The key points to ensure distributed training:

1. **Worker-GPU Allocation**: Ray will assign each worker its own GPU, so in your setup, two GPUs will be used.
2. **Data Splitting**: By default, Ray handles splitting the dataset across workers. The `TorchTrainer` will divide the data, ensuring that each worker processes a different batch of the data in parallel.
3. **Independent Training**: Each worker (on its GPU) will compute gradients independently based on the portion of the dataset it processes. After each batch/epoch, these gradients are aggregated and synced across workers for model updates.

----------------------------

####  **Ray Logging (Tasks and Data Sharding)**

To see data partitions assigned to each worker (node/GPU), enable Ray's internal logging. Ray logs can provide insights on:

- Which worker is running which task.
- The amount of data being processed by each worker.
- The log file names are typically structured as `worker-{pid}.out` or `worker-{pid}.err`, where `{pid}` is the process ID of the Ray worker.


--------------------------

Distributed training in Ray can be accomplished in various ways, depending on the level of control and flexibility required for your machine learning pipeline.

### 1. **Ray Datasets and Ray Train API** (High-Level)

Ray provides an easy-to-use ==Ray Train== API for distributed deep learning that handles most of the complexity under the hood, including data splitting, training on multiple GPUs, and gradient aggregation.

#### Key Steps:

- **Data Parallelism**: Ray `Datasets` handle splitting the data across workers.
- **Training Loop**: The `Trainable` class defines the distributed training loop.
- **Built-in Integrations**: ==Ray Train supports major deep learning libraries like PyTorch, TensorFlow, and XGBoost.==
- **Automatic GPU Placement**: Ray will automatically handle the placement of models and data across GPUs.


**Note**: You cannot use `TorchTrainer` directly if your training loop is written using TensorFlow, as `TorchTrainer` is specifically designed for PyTorch models. However, Ray provides alternatives for distributed training with TensorFlow, and you can achieve the same goals using the `TensorflowTrainer`.

**Torch** example:
```python
import ray
from ray.train import Trainer, ScalingConfig
from ray.train.torch import TorchTrainer
import torch

# Define the model and training function
def train_func(config):
    model = MyModel().to(config["device"])
    optimizer = torch.optim.Adam(model.parameters(), lr=config["lr"])
    criterion = torch.nn.CrossEntropyLoss()

    # Train loop
    for epoch in range(config["epochs"]):
        for batch in config["dataloader"]:
            optimizer.zero_grad()
            outputs = model(batch["inputs"].to(config["device"]))
            loss = criterion(outputs, batch["labels"].to(config["device"]))
            loss.backward()
            optimizer.step()

# Distributed training setup using Ray's TorchTrainer
trainer = TorchTrainer(
    train_loop_per_worker=train_func,
    scaling_config=ScalingConfig(
        num_workers=4,  # Number of GPUs
        use_gpu=True
    ),
    train_loop_config={
        "epochs": 10,
        "lr": 0.001,
        "dataloader": ray.data.read("s3://path-to-data").to_torch()
    }
)

# Start training
trainer.fit()

```

**Tensorflow** example:
```python
import ray
from ray.train.tensorflow import TensorflowTrainer
from ray.train import ScalingConfig
import tensorflow as tf

# Define your model and training function
def build_model():
    model = tf.keras.models.Sequential([
        tf.keras.layers.Dense(128, activation='relu'),
        tf.keras.layers.Dense(10, activation='softmax')
    ])
    model.compile(optimizer='adam', loss='sparse_categorical_crossentropy', metrics=['accuracy'])
    return model

def train_func(config):
    model = build_model()
    
    # Assume config contains the dataset
    train_dataset = config["train_dataset"]
    test_dataset = config["test_dataset"]
    
    model.fit(train_dataset, validation_data=test_dataset, epochs=config["epochs"])

# Using TensorflowTrainer for distributed training
trainer = TensorflowTrainer(
    train_loop_per_worker=train_func,
    scaling_config=ScalingConfig(num_workers=4, use_gpu=True),
    train_loop_config={
        "train_dataset": tf.data.Dataset.from_tensor_slices((train_x, train_y)).batch(32),
        "test_dataset": tf.data.Dataset.from_tensor_slices((test_x, test_y)).batch(32),
        "epochs": 10
    }
)

trainer.fit()
```

### 2. **Using Ray Remote Tasks** (Mid-Level Control)

You can ==manually== distribute tasks across GPUs using `ray.remote` functions. This method gives more flexibility in controlling data splits, GPU assignment, and gradient aggregation.
#### Key Steps:

- Use `ray.remote` to define the model training function.
- Split the data manually and assign different portions to different workers.
- Run training tasks on separate GPUs and collect the gradients for aggregation.
- It can be used on **both torch and tensorflow.**

```python
import ray
import torch
import torch.distributed as dist

@ray.remote(num_gpus=1)
def train_on_gpu(model, data, lr):
    device = torch.device("cuda")
    model.to(device)
    optimizer = torch.optim.SGD(model.parameters(), lr=lr)
    criterion = torch.nn.CrossEntropyLoss()

    for batch in data:
        inputs, labels = batch
        optimizer.zero_grad()
        outputs = model(inputs.to(device))
        loss = criterion(outputs, labels.to(device))
        loss.backward()
        optimizer.step()
    return model.state_dict()

# Initialize Ray
ray.init()

# Split data
data_partitions = split_data(data, num_splits=4)

# Distribute training across 4 GPUs
model = MyModel()
results = ray.get([
    train_on_gpu.remote(model, data_partitions[i], lr=0.01) for i in range(4)
])

# Aggregate results (e.g., averaging gradients)
aggregated_weights = average_weights(results)

```


### 3. **Using PyTorch Distributed (Deep Integration with Ray)** (Low-Level Control)

If you need maximum flexibility, you can manually implement distributed training using PyTorch's `torch.distributed` API and Ray to manage processes across GPUs.

#### Key Steps:

- Set up `torch.distributed` to manage gradient aggregation and communication.
- Use Ray to manage the processes on each worker (GPU).
- You can control the model replica synchronization and gradient aggregation explicitly.

```python
import ray
import torch
import torch.distributed as dist
from torch.nn.parallel import DistributedDataParallel as DDP

def setup(rank, world_size):
    dist.init_process_group("gloo", rank=rank, world_size=world_size)
    
def cleanup():
    dist.destroy_process_group()

@ray.remote(num_gpus=1)
def train_worker(rank, world_size, model, data):
    setup(rank, world_size)
    model = model.to(rank)
    model = DDP(model, device_ids=[rank])
    
    # Training loop with distributed backend
    for epoch in range(epochs):
        # DataLoader and optimizer setup
        for batch in data:
            optimizer.zero_grad()
            outputs = model(batch[0].to(rank))
            loss = criterion(outputs, batch[1].to(rank))
            loss.backward()
            optimizer.step()
    cleanup()
    
# Initialize Ray and setup distributed training
ray.init()

# Initialize world_size and train across GPUs
world_size = 4  # Number of GPUs
model = MyModel()
train_tasks = [
    train_worker.remote(rank, world_size, model, data_split[rank]) for rank in range(world_size)
]

# Start training
ray.get(train_tasks)

```

- Ray Remote Tasks are generally suited for smaller distributed setups or for training on a small number of GPUs. When scaling to larger clusters, more advanced distributed strategies (like PyTorch Distributed or Horovod) are required for efficient communication.
  
- Using ray.remote tasks, you have to manually **handle data and model distribution along with custom gradient aggregation**. But it can be used for both torch and tensorflow.
- PyTorch Distributed handles **automatic synchronization of gradients** across all the GPUs/nodes. You can wrap your PyTorch model with `DistributedDataParallel`, which ensures that **each worker computes gradients on its data partition and then synchronizes the gradients across all workers**. It is highly scalable and can be used to train large models across multiple GPUs or even multiple machines (nodes) in a cluster. **Only for pytorch based models.**

**==For tensorflow:==** 

- TensorFlow provides a built-in framework for distributed training called **TensorFlow Distributed (TF Distributed)**. It offers several strategies for distributing computations across multiple GPUs or multiple nodes. These strategies are designed to make distributed training easier and more efficient without requiring users to manually manage the complexities of parallelism and communication.

### Key Strategies for TensorFlow Distributed Parallelism:

1. **MirroredStrategy** (Data Parallelism on Single or Multiple GPUs)
2. **MultiWorkerMirroredStrategy** (Data Parallelism Across Multiple Workers/Nodes)
3. **TPUStrategy** (Training on Tensor Processing Units - TPUs)
4. **ParameterServerStrategy** (For model-parallelism and asynchronous training)
5. **CentralStorageStrategy** (Data stored on a single device, often used for small models)

### 4. **Horovod with Ray** (for Large Scale Distributed Training) 
If you need more advanced control over distributed training, you can integrate **Horovod** with Ray. Horovod is designed for large-scale distributed deep learning with TensorFlow, PyTorch, and Keras. It enables efficient communication for gradient aggregation across workers.

![[Pasted image 20241019180818.png]]
-----------------------

### 4. **Ray Tune for Hyperparameter Optimization with Distributed Training**

You can use `Ray Tune` to handle hyperparameter tuning combined with distributed training. It supports different training backends (e.g., PyTorch, TensorFlow) and scales across multiple GPUs seamlessly.

------------

can two separate jobs use same gpu resources at the same time?

No, two separate jobs cannot **simultaneously** use the **same GPU** in Ray's default resource scheduling model. When you specify `num_gpus` for a job or task, Ray reserves that GPU exclusively for the duration of the task. Once a GPU is assigned to a job, it cannot be shared by another job until the first job finishes, or explicitly releases the resource.

---------------------

To explicitly control which node runs this function, you can:

- **Add `@ray.remote` to `download_file_from_s3`** if you want Ray to schedule it on workers, allowing for distributed downloading.
- **Ensure the head or worker configuration** is appropriately set to handle the memory and workload if it remains a non-Ray function.
- If you decide to use `@ray.remote`, remember to call the function with `.remote()` (e.g., `download_file_from_s3.remote(...)`). This way, Ray will handle it as a distributed task on available workers.

## Tasks

Ray enables arbitrary functions to be executed asynchronously on separate Python workers. These asynchronous Ray functions are called “tasks”. Ray enables tasks to specify their resource requirements in terms of CPUs, GPUs, and custom resources. These resource requests are used by the cluster scheduler to distribute tasks across the cluster for parallelized execution.

==In Ray, each call to a `@ray.remote` function creates exactly **one task**, which is then assigned to a single worker for execution. Ray does not split or parallelize the work within that single task across multiple workers.==
## Actors

Actors extend the Ray API from functions (tasks) to classes. An actor is essentially a stateful worker (or a service). When a new actor is instantiated, a new worker is created, and methods of the actor are scheduled on that specific worker and can access and mutate the state of that worker. Like tasks, actors support CPU, GPU, and custom resource requirements.

----------------

**Job Scheduling**:

- When you submit the job with `ray job submit`, the Ray head node on your Kubernetes cluster coordinates the job’s execution.
- If `train.py` includes functions marked with `@ray.remote`, those specific functions will be distributed across the worker nodes. Otherwise, if `train.py` runs without `@ray.remote` decorators, the script and all its functions will execute on a single node (typically one of the workers assigned by Ray).

**Execution Node**:

- When you submit `train.py` using `ray job submit`, Ray typically runs it on a **worker node** (not on the head node) because the head node primarily serves as a scheduler and coordinator.
- Thus, **non-remote functions in `train.py` will likely run on a worker node**, not on the head node, unless the head node is explicitly assigned to run tasks (which is uncommon).

In summary, **the downloading function will execute on the same node as `train.py`, which is usually a worker node, not the head node**.

-----------------

Head Node (**scheduler and coordinator**):

1. **Job Scheduling and Task Assignment**:
    
    - The head node receives job submissions (like `ray job submit`) and distributes tasks across the available worker nodes based on resource availability (e.g., CPU, GPU, memory).
    - It ensures that tasks are assigned to the appropriate nodes in a way that balances the load and optimizes resource usage across the cluster.
2. **Cluster Management**:
    
    - The head node monitors the status of worker nodes, handling node additions and removals. For example, in a Kubernetes deployment, the head node manages scaling (adding or removing worker pods) based on the cluster configuration.
    - It restarts or reallocates tasks if a worker node fails or new resources become available, ensuring fault tolerance.
3. **Maintaining the Global State**:
    
    - The head node keeps track of all running jobs, actors, and their states. It also maintains global metadata about tasks, resources, and the health of each node in the cluster.
    - This global state allows the head node to effectively distribute tasks and handle failures.
4. **Handling Remote Function Definitions**:
    
    - When a function is decorated with `@ray.remote`, the head node registers it, and then it can be distributed across the worker nodes for parallel execution. The head node coordinates these remote function calls, but the actual execution happens on the worker nodes.
5. **Dashboard and Monitoring**:
    
    - The Ray dashboard, hosted on the head node, provides a centralized view of the cluster’s activity, including resource utilization, job statuses, and logs. This allows users to monitor and debug tasks and the cluster as a whole.

### Why the Head Node Doesn’t Run Tasks

By not running compute tasks, the head node avoids resource contention and remains focused on managing the cluster, ensuring that it can efficiently schedule tasks, monitor state, and handle failures without being impacted by heavy computational loads.

----------------------------------------

To ensure that the `@ray.remote` task is scheduled specifically on a **worker node** and not the head node, you can define a custom resource tag for workers in your Ray cluster configuration file. Then, specify this custom resource in the `@ray.remote` decorator.

```yaml
env: 
   - name: RAY_WORKER_RESOURCE_KEY 
     value: "worker" # Custom resource label for worker nodes
```

```python
import ray @ray.remote(num_cpus=2, memory=12 * 1024 * 1024 * 1024, resources={"worker": 1})

download_from_s3(bucket_name, s3_key, local_path): 
   pass
```

---------------------

![[Pasted image 20241110153548.png]]

-----------------------

**Memory issue:**

Refer to the documentation on how to address the out of memory issue: https://docs.ray.io/en/latest/ray-core/scheduling/ray-oom-prevention.html. Consider provisioning more memory on this node or reducing task parallelism by requesting more CPUs per task. Set max_restarts and max_task_retries to enable retry when the task crashes due to OOM. To adjust the kill threshold, set the environment variable `RAY_memory_usage_threshold` when starting Ray. To disable worker killing, set the environment variable `RAY_memory_monitor_refresh_ms` to zero.

-------------------------

**Memory profiler:**

```bash
pip install memory-profiler
```

```python
from memory_profiler import profile

@profile
@ray.remote
def my_task(data):
    result = [x * 2 for x in data]
    return result
```

```bash
python -m memory_profiler vbox.py hparams.yaml
```

results:

```kotlin
Line #    Mem usage    Increment  Occurrences   Line Contents
============================================================
    2     40.0 MiB     0.0 MiB           1   @profile
    3     40.1 MiB     0.1 MiB           1   def memory_intensive_task(data):
    4     41.5 MiB     1.4 MiB           1       result = [x * 2 for x in data]
    5     41.5 MiB     0.0 MiB           1       return result

```

-------

- If `num-cpus` is set to a value, Ray will assume that this many CPUs are available for task scheduling on that node, regardless of the actual hardware or Kubernetes limits.
- **Important**: Setting `num-cpus` to a value greater than the actual available CPUs can lead to oversubscription, while setting it lower than the available CPUs can underutilize the resources.

![[Pasted image 20241125130025.png]]

### **`cpu` in `resources.limits`**

- **Purpose**: Defines the maximum amount of CPU resources that Kubernetes allows the container to use.
- **Scope**: Kubernetes enforces this limit to prevent the container from consuming more than the specified number of CPUs.
- **Behavior**:
    - If `cpu` is not set, the container may use all available CPUs on the node (unlimited).
    - If set, the container cannot use more CPU resources than specified, even if `num-cpus` is higher.


-----------------

**Schedule tasks and actors on specific resources :** 

### resources

This field can be used to specify custom resource capacities for the Ray pod. These resource capacities will be advertised to the Ray scheduler and Ray autoscaler. For example, the following annotation will mark a Ray pod as having 1 unit of `Custom1` capacity and 5 units of `Custom2` capacity.

```yaml 
rayStartParams:
    resources: '"{\"Custom1\": 1, \"Custom2\": 5}"'
```

You can then annotate tasks and actors with annotations like `@ray.remote(resources={"Custom2": 1})`. The Ray scheduler and autoscaler will take appropriate action to schedule such tasks.

---------------------

The error `RuntimeError: CUDA error: invalid device ordinal` indicates that your script is trying to assign a GPU device that does not exist on the worker node. This typically happens when:

1. The specified GPU device ID is out of range.
2. The `CUDA_VISIBLE_DEVICES` environment variable is misconfigured.
3. The Ray worker doesn't have access to GPUs.

#### 2. **Set `CUDA_VISIBLE_DEVICES` Manually**:

If the GPUs are available but not being used correctly, you can explicitly set `CUDA_VISIBLE_DEVICES`. For example, if your node has one GPU:

- Temporarily set `CUDA_VISIBLE_DEVICES` in the current session:
```yaml
env: - name: CUDA_VISIBLE_DEVICES value: "0"
```

### Key Issues to Check

#### 1. **Mismatch in `CUDA_VISIBLE_DEVICES` and GPU Allocation**

- You set `CUDA_VISIBLE_DEVICES=0`, but the worker is requesting `2` GPUs (`num-gpus: "2"` in `rayStartParams`).
- The GPU allocation (`nvidia.com/gpu: 1`) in `resources` also conflicts with this configuration.
- **Fix**: Adjust either `CUDA_VISIBLE_DEVICES` or `num-gpus` to align with the available GPUs and the allocated resources.

---------------------

The environment variable CUDA_VISIBLE_DEVICES determines which GPUs are visible and accessible to a process (such as a Ray worker, PyTorch script, or any CUDA-enabled program).
What CUDA_VISIBLE_DEVICES Does:

It acts as a filter for CUDA-enabled programs, restricting them to see only the specified GPUs.
The GPUs are identified by their device indices (0, 1, 2, etc.), which correspond to the order listed by nvidia-smi.

Example:

```bash
`export CUDA_VISIBLE_DEVICES=0,2`
```

- The process will only see **GPU 0 and GPU 2**, even if more GPUs are installed on the system.
    - Within the program, these GPUs are re-indexed as `0` and `1` (relative to the visible set).
- **Behavior in PyTorch**:
    
    - GPUs `0` and `2` will be visible to PyTorch.
    - `torch.cuda.device_count()` will return `2` (the count of visible GPUs).
- **Behavior in `nvidia-smi`**:
    
    - Running `nvidia-smi` in the environment will show only the selected GPUs.

-----------------

### **1. `resources.limits` in Kubernetes**

- **Definition**: Specifies the amount of physical resources (e.g., CPU, memory, GPUs) that Kubernetes allocates to a container.
- **Scope**: Governs the physical resource allocation at the **pod level** in Kubernetes.
- **Purpose**: Ensures that the container does not exceed the allocated resources.

### **2. `rayStartParams` in Ray**

- **Definition**: Specifies the logical resources (e.g., CPUs, GPUs) that Ray workers request for running tasks.
- **Scope**: Governs how Ray manages and schedules tasks across workers.
- **Purpose**: Ensures that tasks are assigned to workers with the requested resources.

**Key Point**: If `num-gpus` is set to a value higher than the GPUs allocated in `resources.limits`, the worker process will fail to access the GPUs (e.g., `CUDA error: invalid device ordinal`).

--------------

![[Pasted image 20241207152403.png]]

this torchtrainer is deployed on any worker you have because:

![[Pasted image 20241207152505.png]]

---------------------

when you use torch trainer, working directory change, so if a file is missing:

```python
ray_dir = "/tmp/ray/session_latest/runtime_resources/working_dir_files" hparams_file = os.path.join(ray_dir, "hparams.yaml")
```

"/tmp/ray/session_latest/runtime_resources/working_dir_files" is the working directory before calling torchtrainer.

-------------------

**==If you are using DDP (not torch trainer)==**, know that torch.distributed needs to be initialized using with runopts, otherwise it cannot run successfully:

```python
import torch.distributed as dist

def train_function():
    # Define run_opts
    run_opts = {
        "device": "cuda",
        "distributed_launch": "ray",
        "ddp_backend": "nccl",
        "num_gpus": 1,
        "num_nodes": 2,
        "distributed_backend": "nccl",
    }

    # Check if the process group is already initialized
    if not dist.is_initialized():
        sb.utils.distributed.ddp_init_group(run_opts)
    else:
        print("Process group already initialized. Skipping.")

```

**And, it must not be initialize more than once.**
- Important:
   Since `TorchTrainer` manages the distributed environment, `run_opts` are no longer necessary within the hparams. Passing an empty dictionary ensures that `ASR` doesn't attempt to reconfigure the distributed settings.

------------

Torchtrainer also needs a **scaling_config** (for scaling the process on multiple workers) and a **train_loop_config** (used as an input argument in the **train_function**):

```python
primaryCwd = os.getcwd()  
scaling_config = ScalingConfig(  
    num_workers=2,  
    use_gpu=True,  
    resources_per_worker={"GPU": 1}  
)  
  
trainer = TorchTrainer(  
    train_loop_per_worker=train_function,  
    scaling_config=scaling_config,  
    train_loop_config={  
        "hparams_file": os.path.join(primaryCwd, "hparams.yaml"),  
        "overrides": {}  
    },  
    run_config=RunConfig(  
        checkpoint_config=None,  # checkpointing strategy if needed  
        verbose=1,  
    )  
)
```

------------

![[Pasted image 20241207170053.png]]
؟؟؟؟؟؟؟؟؟؟؟؟؟؟؟؟؟؟

----------
