```PYTHON
hparams_file, run_opts, overrides = sb.parse_arguments(sys.argv[1:])  
sb.utils.distributed.ddp_init_group(run_opts)
```

run_opts = {
    "distributed_backend": "nccl",  # Communication backend for GPU communication
    "world_size": 4,                # Total number of GPUs/nodes participating in the training
    "rank": 0,                      # The rank of the current process (GPU)
    "gpu_id": 0,                    # The GPU id for the current process
    ...
}

----------------------------

**How DDP Works**:

- **Model Replication**: In DDP, a copy of the model is created on each GPU. These models perform forward and backward passes in parallel.
- **Gradient Synchronization**: After each GPU computes the gradients (from backpropagation), DDP synchronizes these gradients across all GPUs. This ensures that each model copy learns in sync with the others by averaging the gradients before updating the model parameters.
- In DDP, each GPU (or process) handles part of the computation, and the results are synchronized using communication protocols like ==`NCCL` or `Gloo`==.

-------------------

`experiment_directory=hparams["output_folder"]`: The location of the directory where all ==experiment results will be saved==. This path is defined in the hyperparameters YAML file and is accessed via `hparams["output_folder"]`.

**Create Experiment Directory**: It creates a folder where the experiment outputs will be stored, saving both the original hyperparameters file and the override settings. This ensures that there is a reproducible record of the experiment’s configuration.

![[Pasted image 20241023172922.png]]

------------------------

The `prepare_common_voice` function is responsible for processing the Common Voice dataset, which typically includes tasks like reading the raw audio and text data, organizing it into the correct format (e.g., `.tsv` files), and possibly performing some initial cleaning or feature extraction.

 `run_on_main(...)`

- This function is part of the **SpeechBrain** toolkit. `run_on_main` ensures that certain code (like data preparation) is only executed on the main process, which is crucial when running distributed (multi-GPU) training.
- In multi-GPU setups, processes on different GPUs often need to coordinate their actions. `run_on_main` ensures that data preparation happens only once, preventing potential conflicts (like multiple GPUs trying to process or modify the dataset at the same time).

### `prepare_common_voice` Arguments:

The `kwargs` argument is a dictionary that contains various settings needed to prepare the dataset.

----------------

```python
asr_brain = ASR( modules=hparams["modules"], hparams=hparams, run_opts=run_opts, checkpointer=hparams["checkpointer"], opt_class=hparams["whisper_opt_class"], )
```

initializes an instance of an Automatic Speech Recognition (ASR) model (likely using a class named ASR) with specified parameters. The ==ASR class here appears to be designed to handle the entire speech recognition pipeline, including model training, validation, and testing.==

**`opt_class=hparams["whisper_opt_class"]`**:

- This specifies the optimizer class that will be used for training the model, typically a specific optimizer suited for the model’s structure.
---------

```python
if "pretrainer" in hparams.keys():
    run_on_main(hparams["pretrainer"].collect_files)
    hparams["pretrainer"].load_collected(asr_brain.device)
```

This code checks if a **pretrained model** is specified in the `hparams` (hyperparameters) and, if so, loads it onto the appropriate device. This setup allows the ASR model to leverage pretrained weights, which can speed up training and improve performance, especially when there is limited data

**`hparams["pretrainer"].collect_files`**: This method collects or downloads necessary files for the pretrained model. It might involve downloading weights from a remote server or loading files from disk.
**`hparams["pretrainer"].load_collected(asr_brain.device)`** : This line loads the collected pretrained weights onto the device specified by `asr_brain.device` (either CPU or GPU).

--------------

```python
s3_bucket = hparams["s3_bucket"]  
data_keys = {  
    "train_csv": hparams["s3_train_csv_key"],  
    "valid_csv": hparams["s3_valid_csv_key"],  
    "test_csv": hparams["s3_test_csv_key"]  
}  
  
# Download data files from S3  
local_data_folder = "data"  
os.makedirs(local_data_folder, exist_ok=True)  

#Loop to Download Files from S3
for dataset, s3_key in data_keys.items():  
    local_path = os.path.join(local_data_folder, os.path.basename(s3_key))  
    download_file_from_s3(s3_bucket, s3_key, local_path)  
    hparams[dataset] = local_path  # Update hparams to use local paths
```

This code section is responsible for downloading data files directly from an S3 bucket and saving them locally so that they can be accessed by the script as if they were stored on the local filesystem. Here’s a detailed breakdown of each part:

**Extracting S3 Bucket and File Keys**
The values for each key, such as `hparams["s3_train_csv_key"]`, are the paths to the specific files within the S3 bucket. For example, `hparams["s3_train_csv_key"]` might look like `"datasets/train.csv"`.

**Creating the Local Path for Each File**:

- `local_path` is the full path where the file will be stored locally. It’s constructed using:
    - `local_data_folder`, which is the directory (`"data"`) where all files will be saved.
    - `os.path.basename(s3_key)`, which extracts just the file name from the full S3 path (`s3_key`). For example, if `s3_key` is `"datasets/train.csv"`, `os.path.basename(s3_key)` returns `"train.csv"`.
- The final `local_path` is something like `"data/train.csv"`.

----------------------
### `dataio_prepare` Function

The `dataio_prepare` function is responsible for loading the datasets and defining data processing pipelines. Within this function, `hparams["train_csv"]`, `hparams["valid_csv"]`, and `hparams["test_csv"]` are accessed to load the respective CSV files.

**Purpose**: `dataio_prepare` is responsible for **loading and preparing datasets** for use in the model, and defining data processing pipelines. This function is part of the **dynamic loading** and preprocessing setup, not the initial dataset organization.

The `prepare_common_voice` function doesn’t use `hparams["train_csv"]`, `hparams["valid_csv"]`, and `hparams["test_csv"]` because its purpose is to **create** these files, not to read them.

- Instead, it takes inputs like the raw data directory (`hparams["data_folder"]`), target output directory (`hparams["save_folder"]`), and other dataset-specific parameters (e.g., `hparams["language"]`, `hparams["accented_letters"]`).
- ==Once `prepare_common_voice` completes, it produces `train.csv`, `valid.csv`, and `test.csv`, which are then used later by functions like `dataio_prepare`.==

--------------

For distributed training:

- **Ray Initialization**: Added `ray.init(address="auto")` to connect to an existing Ray cluster.
- **Remote Class**: Added `@ray.remote` decorator to the `ASR` class.
- **Distributed Training Execution**: Modified the main training task (`fit`) to execute remotely using `asr_brain.fit.remote()`.
- **Final Task Retrieval**: Used `ray.get(train_task)` to wait for distributed training completion.

--------------

does asrbrain have .remore function?

You're correct to question this because `asr_brain.fit.remote()` is not valid by default. In Ray, `remote()` is used as a method for Ray remote actors and functions, not for methods within the class itself.

### Correct Way to Use Ray with Class Methods

When you declare `@ray.remote` on the `ASR` class, Ray creates a **remote actor**. This means that methods of the `ASR` class (like `fit`) need to be called through this Ray actor. Instead of using `fit.remote()`, you need to call `fit()` on the actor instance using `asr_brain.fit.remote(...)`.

-------------------

ray.data
batch processing for data

--------

```shell
 RAY_ADDRESS='http://127.0.0.1:8265' ray job submit --working-dir . --runtime-env-json='{"pip": "requirements.txt"}'     -- python vbox.py hparams.yaml
```

or

**Create an Install Script (e.g., `install_and_run.sh`)**:
```bash
#!/bin/bash
pip install -r requirements.txt --index-url https://repo.mci.dev/artifactory/api/pypi/pypi/simple
python vbox.py hparams.yaml
```

**Make the Script Executable**:
```bash
chmod +x install_and_run.sh
```

**Submit the Job Using the Script**:
```bash
RAY_ADDRESS='http://127.0.0.1:8265' ray job submit \
    --working-dir . \
    -- bash install_and_run.sh
```

---------------------

This notation allows PyTorch to specify compatibility with particular hardware setups, so `torch==1.11.0+rocm4.5.2` would only be useful on systems with ROCm 4.5.2 installed and configured for AMD GPUs.

------------------

### **What Your `ASR` Class Achieves**

- **Training**: Manages the training and validation loop, handling data batches, loss computation, and optimization.
- **Model Forward Pass**: Defines how input audio is processed and how the model produces tokenized predictions.
- **Metrics and Logging**: Tracks and reports performance metrics (e.g., WER, CER) for validation and testing stages.
- **Compatibility with Whisper**: Adapts SpeechBrain’s framework to use the Whisper ASR model, handling tokenization, augmentation, and decoding.
- **Checkpointing**: Automatically saves model checkpoints based on validation performance.

--------------------------

**Tokenizer Integration**: The tokenizer is dynamically attached to the `ASR` class instance to align with the Whisper model:

```python
asr_brain.tokenizer = tokenizer
```

----------------

### **Why Not Modify It for DDP?**

The class already has a well-defined structure, adhering to SpeechBrain conventions, and encapsulates all the logic required for ASR training. Modifying it directly for DDP could:

- Make the class less modular and harder to reuse outside distributed training.
- Introduce complexity, as it is tightly integrated with SpeechBrain's lifecycle management.

Instead, by wrapping this class externally with DDP (as shown earlier), we can keep the core design intact and add distributed capabilities without disrupting its core responsibilities.

---------------------

### **Why This Approach Works**

- **No Changes to `ASR`**: The core functionality of your `ASR` class is untouched.
- **Modular DDP Integration**: The DDP logic is encapsulated in the `ASRWrapper` class, making the integration modular and non-intrusive.
- **Scalability**: Ray and DDP together enable scaling across multiple nodes and GPUs without duplicating code or logic.
- **Consistency**: The final model weights are identical across all workers, ensuring a single, synchronized trained model.

-----------------------

Distributed ASR training:

**Loading and Sharing Data**: You should ensure that your worker nodes can access the data. If the data is on the head node, you can either move it to the workers or share it via a distributed file system. **Ray does not automatically distribute files across workers unless explicitly done.**

--------------

No HIP GPUs are available -->  torch version is rocm not cuda
