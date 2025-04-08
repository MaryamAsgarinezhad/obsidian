To build `llama.cpp` for CUDA and then serve a GGUF model, you need to set up a Docker environment, install the necessary dependencies, and compile the code with CUDA support. Below is a step-by-step guide, including a Dockerfile to help you achieve this:

### Step-by-Step Guide

1. **Set Up the CUDA Environment**:
    
    - Use a base Docker image that includes CUDA support.
    - Install necessary dependencies like Git, build tools, and CUDA toolkit.
2. **Clone the `llama.cpp` Repository**:
    
    - Clone the repository to your Docker image.
3. **Compile `llama.cpp` with CUDA Support**:
    
    - Ensure you have the right environment variables set and compile the project using CMake.
4. **Serve the GGUF Model**:
    
    - Download the GGUF model.
    - Set up an HTTP server to serve the model.
      
5. Make sure you have installed the NVIDIA Docker toolkit, which allows Docker to use NVIDIA GPUs.
```shell
sudo docker run --rm --gpus all nvidia/cuda:11.0-base nvidia-smi
```
- This should output the details of your GPU, confirming that Docker can access it.

### Dockerfile

Here’s a Dockerfile that outlines the necessary steps:

```dockerfile
FROM docker.mci.dev/nvidia/cuda:12.4.1-devel-ubuntu22.04

ENV http_proxy=http://172.16.56.71:8086
ENV https_proxy=http://172.16.56.71:8086
ENV HTTP_PROXY=http://172.16.56.71:8086
ENV HTTPS_PROXY=http://172.16.56.71:8086

RUN apt-get update && apt-get install -y \
    git \
    build-essential \
    cmake \
    python3-pip \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /workspace

RUN git clone https://github.com/ggerganov/llama.cpp.git

WORKDIR /workspace/llama.cpp

ENV LLAMA_CUDA_NVCC=1

RUN mkdir build && cd build && cmake .. -DLLAMA_CUDA=ON && make GGML_CUDA=1

COPY ./models/aya-23-8B-Q8_0.gguf /workspace/model.gguf

EXPOSE 8080

CMD ["./build/server", "-m", "/workspace/model.gguf", "-ngl", "10000000", "--host", "0.0.0.0", "--port", "8080"]
```

Or we can use the built llamacpp project stored in a directory (here current directory):

```dockerfile
FROM docker.mci.dev/nvidia/cuda:12.4.1-devel-ubuntu22.04

ENV http_proxy=http://172.16.56.71:8086
ENV https_proxy=http://172.16.56.71:8086
ENV HTTP_PROXY=http://172.16.56.71:8086
ENV HTTPS_PROXY=http://172.16.56.71:8086

RUN echo 'Acquire::http { Proxy "http://apt-cacher.mci.dev:27246/"; }' > /etc/apt/apt.conf.d/01apt-proxy.conf
RUN echo 'Acquire::https { Proxy "http://apt-cacher.mci.dev:27246/"; }' >> /etc/apt/apt.conf.d/01apt-proxy.conf

RUN apt-get update && apt-get install -y \
    git \
    build-essential \
    cmake \
    python3-pip \
    && rm -rf /var/lib/apt/lists/*

COPY ./models/aya-23-8B-Q5_K_M.gguf /workspace/model.gguf

COPY ./llama.cpp/llama-server . 
EXPOSE 8080

CMD ["./llama-server", "-m", "/workspace/model.gguf", "-ngl", "10000000", "--host", "0.0.0.0", "--port", "8080"]
```
----------------------------------------------

Use docker compose for mounting volume and using GPU:

```
services:
  triton:
    image: llama-cpp-cuda:v0.0.1
    container_name: llama-cpp
    network_mode: host
    restart: unless-stopped
    command: tail -f /dev/null
    volumes:
      - ./models:/models
      - /mnt/sre/.cache:/root/.cache
    deploy:
      resources:
        reservations:
          devices:
            - driver: nvidia
              count: all
              capabilities: [gpu]
```
Main concepts:

Model = weights + architecture
- It can be stored as different extension formats: gguf, pytorch, tensorRT, onnx
- Backends are primarily an API that handle user requests. Secondly they  translate the models representations (gguf, pytorch, tensorRT, onnx) and their corresponding codes to CUDA instructions that GPUs can understand to execute the computations (GPUs only do computations commanded by CUDA script)
- Some backends are able to serve multiple model extensions, like triton
- Engines manage requests sent to GPUs and handle batching, model distribution over GPUs, ... .

---------------------------------

The **NVIDIA CUDA development** image is a pre-configured Docker image designed to facilitate the development and deployment of a==pplications that leverage NVIDIA GPUs for parallel computing tasks.== These images are part of NVIDIA's GPU Cloud (NGC) container registry and come with a variety of tools and libraries necessary for GPU-accelerated computing.

### Key Features and Benefits

1. **CUDA Toolkit**:
    
    - The image includes the CUDA Toolkit, which provides a comprehensive ==development environment for C and C++ developers building GPU-accelerated applications==. The toolkit includes libraries, debugging and optimization tools, a compiler, and runtime libraries necessary to deploy applications on NVIDIA GPUs.
    - 
2. **Support for Deep Learning Frameworks**:
    
    - Many CUDA development images also include support for popular deep learning frameworks like TensorFlow, PyTorch, and others, making it easier for AI and machine learning practitioners to develop and deploy models.
    - Developers working on applications that require significant computational resources, such as scientific simulations, financial modeling, and video processing, can use these images to leverage the power of GPUs.

-----------------------

### Example of How Triton Works

1. **Model Deployment**:
    
    - Models are placed in a structured directory known as the model repository. Each model has a configuration file (`config.pbtxt`) that specifies its parameters, input/output formats, batching capabilities, and more.
2. **Inference Request**:
    
    - Clients send inference requests to Triton via HTTP/REST or gRPC APIs. Triton handles these requests, manages batching, and sends them to the appropriate model.
3. **Inference Execution**:
    
    - Triton executes the model inference on available GPUs or CPUs, utilizing dynamic batching and other optimizations to maximize performance.
4. **Response**:
    
    - Once the inference is completed, Triton returns the results to the client, which can then use the inference outputs for further processing or decision-making.

### Conclusion

NVIDIA Triton Inference Server is a powerful tool for deploying, managing, and scaling machine learning models in production environments. Its support for multiple frameworks, dynamic batching, scalability, and monitoring capabilities make it an essential component for organizations looking to operationalize AI at scale.

-----------------------------------

### Setting Up CUDA with Triton

1. **Install CUDA Toolkit**:
    
    - Ensure that the CUDA toolkit is installed on your system. The version should be compatible with both your GPU and the versions of the ML frameworks you are using.
2. **NVIDIA Docker Container**:
    
    - If you are using Triton via NVIDIA’s Docker containers, the containers usually come pre-configured with the necessary CUDA and cuDNN libraries. For example, the base image `nvcr.io/nvidia/tritonserver:23.05-py3` is configured to work with GPUs and CUDA.
3. **CUDA Environment Variables**:
    
    - Ensure that the CUDA toolkit’s binaries and libraries are in your system’s `PATH` and `LD_LIBRARY_PATH`. This allows Triton and the ML frameworks to locate and use the CUDA toolkit.

### Example Docker Setup

If you are using a Docker setup, the NVIDIA Triton Inference Server container can be run with GPU support as shown below:

```shell
docker run --gpus=all --rm -p8000:8000 -p8001:8001 -p8002:8002 nvcr.io/nvidia/tritonserver:23.05-py3
```

This command ensures that all available GPUs are accessible to the Triton server.