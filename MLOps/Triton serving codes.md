Dockerfile

```dockerfile
FROM docker.mci.dev/nvcr.io/nvidia/tritonserver:24.01-trtllm-python-py3

ENV HTTPS_PROXY=http://172.16.56.100:1097
ENV HTTP_PROXY=http://172.16.56.100:1097
ENV https_proxy=http://172.16.56.100:1097
ENV http_proxy=http://172.16.56.100:1097


RUN apt-get clean && apt-get update && apt-get install -y \
    git \
    && rm -rf /var/lib/apt/lists/*

RUN pip uninstall -y torch torchvision
RUN pip install torch==2.3.1 torchvision==0.18.1
RUN pip install -U transformers
RUN pip install -U onnx onnxruntime-gpu

ENV MODEL_REPOSITORY /models

EXPOSE 8000
EXPOSE 8001
EXPOSE 8002

CMD ["tritonserver", "--model-repository=/models", "--log-verbose=1"]
```

Docker compose:

```python
services:
    triton:
      image: triton_text_generation_model:latest
      container_name: triton
      network_mode: host
      restart: unless-stopped
      volumes:
        - ./model_repository:/models
        - /mnt/sre/.cache:/root/.cache
      deploy:
        resources:
          reservations:
            devices:
              - driver: nvidia
                count: all
                capabilities: [gpu]
```

Config file:

```plaintext
name: "test"
backend: "python"
max_batch_size: 72
model_transaction_policy {
  decoupled: True
}
input [
  {
    name: "prompt"
    data_type: TYPE_STRING 
    dims: [-1]
  }
]
output [
  {
    name: "generated_text"
    data_type: TYPE_STRING 
    dims: [-1]
  }
]
instance_group [
  {
    kind: KIND_GPU
  }
]
dynamic_batching {
  preferred_batch_size: [ 10, 16, 24, 32, 48, 64, 72 ]
  max_queue_delay_microseconds: 1000000
}
```

request format:

```shell
curl -sX POST 172.22.1.5:8000/v2/models/aya-23-8B/generate_stream -d '{"parameters": {"prompt": "What is the definition of collectivization?"}}'  -w "%{time_starttransfer}\n%{time_total}\n" | cat -n
```
#### `backend: "python"`

This indicates that the model is implemented using the Python backend. Triton supports multiple backends like TensorRT, TensorFlow, PyTorch, and custom backends like Python. This means 

#### `model_transaction_policy { decoupled: True }`

This policy allows for decoupled transactions, meaning the requests and responses can be handled asynchronously. This is particularly useful for streaming and long-running inference operations where the response can be sent back incrementally.

#### `instance_group [ ... ]`

This section specifies how the model instances should be deployed.

- **kind**: This specifies the kind of instance. `KIND_GPU` indicates that the model should run on GPU instances.

---------------------------------------

In the context of Triton Inference Server, the term "backend" refers to the underlying ==framework or library== that Triton uses to execute the inference of a machine learning model. ***Each backend corresponds to a different machine learning framework or runtime environment, allowing Triton to support models developed in various frameworks and provide optimized execution for those models.***

### Explanation of Backends in Triton

1. **Backend Definition**:
    
    - A backend in Triton Inference Server is essentially the component that interfaces with a specific deep learning framework or runtime. It is responsible for ==loading the model, managing its execution, and handling the input and output data.==
2. **Examples of Supported Backends**:
    
    - **TensorRT**: This backend is used for executing models optimized with NVIDIA TensorRT. It provides **high performance and low latency by leveraging NVIDIA GPUs and TensorRT's optimizations**.
    - **TensorFlow**: This backend supports models developed and trained using TensorFlow. It allows Triton to serve TensorFlow models directly.
    - **PyTorch**: This backend is for models developed with PyTorch. It enables Triton to load and run PyTorch models.
    - **ONNX Runtime**: This backend supports models in the ONNX (Open Neural Network Exchange) format. It is designed to be a flexible and cross-platform way to serve models.
    - **Python**: This custom backend allows users to write their own inference logic in Python. It is useful for integrating custom preprocessing, postprocessing, or any specialized inference logic not covered by the standard backends.
3. **Custom Backends**:
    
    - Triton also allows the development of custom backends. This means that if a specific framework or runtime is not supported out of the box, **users can implement their own backend to integrate with Triton**. The Python backend is a common example of a custom backend, where users can implement inference logic directly in Python.

-------------------

1. Prometheus Metrics
	**Latency**: duration metrics
	
	**Throughput**:
	- **Requests Per Second (RPS)**: inference_request or inference_count metrics
	
	**Resource Utilization**:
	- **GPU Utilization**: gpu_memory and gpu_power metrics

2. Functional Metrics:
    Functional metrics, which measure the performance, accuracy, and efficiency of machine learning models served by Triton Inference Server, can be recorded and monitored using a combination of ==Triton's built-in metrics, external monitoring== tools like Prometheus and Grafana, and custom logging mechanisms.

-------------

Box:

``` python
import triton_python_backend_utils as pb_utils
import torch
# Determine the device (CUDA or CPU)
device = "cuda" if torch.cuda.is_available() else "cpu"
print(f"TTS Service running on {device}")
# Model parameters for different versions


class TritonPythonModel:
    def initialize(self, args):
        """
        Initialize the Triton Python model.
        """

        # Load the TTS model
        # print(f"Loading VBOX TTS version {self.version} from {self.model_path}...")
        self.tts_model = TTS(model_path='/models/tts_model/1/best_model.pth', config_path='/models/tts_model/1/config.json').to(device)

    def execute(self, requests):
        """
        Execute inference requests. Processes a batch of requests.
        """
        responses = []

        for request in requests:
            try:
                # Retrieve inputs
                text_input = pb_utils.get_input_tensor_by_name(request, "text").as_numpy().astype(str)[0]
                # speaker_id = pb_utils.get_input_tensor_by_name(request, "speaker_id").as_numpy().astype(str)[0]

                # Generate speech waveform
                wave_bytes = self.tts_model.tts(text_input)

                # Convert waveform to binary WAV
                wav_buffer = io.BytesIO()
                self.tts_model.synthesizer.save_wav(wave_bytes, wav_buffer)
                wav_buffer.seek(0)
                wav_bytes = wav_buffer.read()

                # Create Triton output tensor
                output_tensor = pb_utils.Tensor("wav_data", np.frombuffer(wav_bytes, dtype=np.uint8))

                # Append a successful response
                responses.append(pb_utils.InferenceResponse(output_tensors=[output_tensor]))

            except Exception as e:
                # Handle errors and create an error response
                triton_error = pb_utils.TritonError(str(e))
                responses.append(pb_utils.InferenceResponse(error=triton_error))

        return responses

```

This code implements a Triton Python Backend for serving a Text-to-Speech (TTS) model in a Triton Inference Server environment. 

The primary goal of the code is to:

    Load a TTS model.
    Accept text input via Triton's inference API.
    Generate audio output (as WAV data).
    Return the audio as a response in the Triton server.

