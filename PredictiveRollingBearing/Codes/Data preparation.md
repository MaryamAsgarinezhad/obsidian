
1. **Hyperparameters and Device Configuration**
```python
H = { ... }
device = torch.device("cuda" if torch.cuda.is_available() else "cpu")
```

- A dictionary `H` defines hyperparameters for a machine learning or signal processing model, such as learning rate, batch size, and dimensions for layers (e.g., `hidden_dim`, `num_kernels`, etc.).
- The `device` variable configures the use of GPU (`cuda`) if available; otherwise, it defaults to CPU.


2. **Dataset and File Exploration**
```python
bearing_root = '/content/XJTU-SY_Bearing_Datasets/35Hz12kN/Bearing1'
bearing = 2
num_files = len(os.listdir(f'{bearing_root}_{bearing}'))
print(num_files)
```

- Specifies the root directory for the vibration data of a specific bearing (`Bearing 1` at `35Hz 12kN` loading condition).
- The number of CSV files in the dataset folder is counted and printed for `bearing=2`.


3. **Signal Plotting and Statistics**
```python
def plot_file(bearing, number, show=True): ...
plot_file(2, 1)
```

- The function `plot_file`:
    - Loads horizontal and vertical vibration signals from a CSV file.
    - Calculates the maximum absolute amplitude, mean, and standard deviation for both signals.
    - Optionally (`show=True`), **plots the time-domain signals and displays their statistics.**
- Example usage: `plot_file(2, 1)` visualizes and calculates the metrics for the first file in `Bearing 2`.


4. **Batch Plotting for All Bearings**
```python
fig, axs = plt.subplots(2, 3, figsize=(15, 10))
for bearing in range(1, 6): ...
plt.tight_layout()
plt.show()
```

- The vibration signals for all files in bearings 1 to 5 are processed:
    - The maximum amplitudes for horizontal and vertical signals are extracted.
    - These maximum values are plotted as time-series trends for each bearing.
- The subplot grid (`2x3`) visually organizes the results for comparison across bearings.

---

The next part of the notebook defines a custom **dataset class**, `XJTU_SY_Bearing_Dataset`, which processes vibration data from the XJTU-SY Bearing Dataset for machine learning or deep learning tasks. The class handles signal preprocessing, feature extraction, and Remaining Useful Life (RUL) estimation for bearings.

### **Class Overview**

#### **Initialization (`__init__`)**

- Sets up the dataset for training or testing.
- Key arguments:
    - `root_dir`: Path to the dataset directory.
    - `bearing`: Identifier for the specific bearing being analyzed.
    - `window_size (3 -> x1,x2,x3)`: It defines the number of consecutive time steps (or signal files) that are concatenated together to form a single input sample for the model. By including multiple time steps in each sample, the model can capture **temporal dependencies** and patterns in the signal that evolve over time.
    - `degredation_start`: Time (in minutes) when bearing degradation begins, triggering upsampling.
    - `interval_length(16)` : Reduces signal length for computational efficiency.
    - `upsampling_repetition`: Repeats degraded signals multiple times to balance data.
    - `test`: Indicates whether the dataset is for testing.
- Computes `start_minutes`, `input_ruls`, and processes signals differently for training and testing.


### **Code Breakdown**

1. **Condition: `if not self.test`**
    
    - This branch is executed when the dataset is used for training.
    - Training requires a list of sampled time points (`start_minutes`) and their corresponding Remaining Useful Life (RUL) values (`input_ruls`).
```python
self.start_minutes = self.get_start_minutes(degredation_start, upsampling_repetition)
```

**`self.get_start_minutes()`**:

- Generates the list of starting time points (file indices) for the training dataset.
- Includes upsampling for degraded signals (where RUL < `degredation_start`) to balance the dataset.
- #### **Why Different Start Points for Training and Testing?**
  
	1. **Training Mode**:
	    
	    - The model needs time-series samples from the entire lifecycle of the bearing (both healthy and degraded signals).
	    - The starting point ensures valid time windows while using all possible files.
	2. **Testing Mode**:
	    
	    - Testing focuses on the **later lifecycle** of the bearing, where degradation and failure signals are most relevant.
	    - Using signals closer to failure helps evaluate the model's performance on critical predictions like Remaining Useful Life (RUL).
**Purpose**: To examine every file index within the specified range and decide how many times it should be sampled (upsampling is done only for training stage)
[3, 4, ..., 80, 81, 81, 81, 81, 82, 82, 82, 82, ..., 100, 100, 100, 100]

```python
self.input_ruls = np.array([self._rul(t) for t in self.start_minutes])
```

**`self._rul(t)`**:

- Computes the Remaining Useful Life (RUL) for each time point in `start_minutes`.
- Converts the list of RULs into a NumPy array for efficient operations.

```python
self.start_minutes = torch.tensor(self.start_minutes, dtype=torch.float).unsqueeze(1)
self.input_ruls = torch.tensor(self.input_ruls, dtype=torch.float).unsqueeze(1)
```

- **Tensor Conversion**:
    - Converts `start_minutes` and `input_ruls` into PyTorch tensors for use in a PyTorch-based model.
     - The `unsqueeze(1)` adds a new dimension, making the tensors 2D:
        - `start_minutes`: Shape changes from `[N]` to `[N, 1]`.
        - `input_ruls`: Shape changes from `[N]` to `[N, 1]`.
        - This is required because **PyTorch models generally expect 2D tensors for inputs and targets.**

---

2. **Condition: `else`**

	- This branch is executed when the dataset is used for testing.
	- Testing requires preprocessed signals, times, and RULs, as we need the complete processed dataset upfront.

```python
self.input_signals, self.input_times, self.input_ruls = self._preprocess_bearing(
    window_size, degredation_start, interval_length, upsampling_repetition
)
```

- **`self._preprocess_bearing()`**:
    
    - Prepares the entire dataset by:
        1. **Downsampling**: Reduces the length of each signal for computational efficiency.
        2. **Time Window Embedding**: Groups signals into time windows of size `window_size`.
        3. **RUL Calculation**: Computes the RUL for each time window.
        4. **Normalizing**: Standardizes the signals for better model performance.
- The outputs are:
    
    - `input_signals`: Tensor containing the preprocessed signals.
        - Shape: `[N, window_size × downsampled_length, 1, 2]` (for two channels).
    - `input_times`: Tensor containing the time indices for each sample.
        - Shape: `[N, 1]`.
    - `input_ruls`: Tensor containing the RUL for each sample.
        - Shape: `[N, 1]`.

![[Pasted image 20250128181311.png]]

### ==**Purpose of the Code**==

1. **Training Mode (`not self.test`)**:
    
    - Dynamically generates pairs of consecutive time points (`start_minutes`) and their corresponding RULs for training.
    - Efficient and memory-friendly because it doesn’t precompute all possible time windows.
2. **Testing Mode (`self.test`)**:
    
    - Precomputes the full dataset (signals, times, and RULs) in advance for evaluation.
    - Ensures the model is evaluated on the entire dataset without introducing dynamic sampling during testing.

---
#### **Methods**

1. **`__len__`**
    
    - Returns the number of samples in the dataset.
    - For training, it excludes the last time window (`-1`).
2. **`__getitem__`**
    
    - For training:
        - Returns two consecutive signal windows (`x1` and `x2`) and their corresponding times (`t1` and `t2`) and RUL values (`y1` and `y2`).
        - The reason for using **two consecutive samples** during training is to allow the model to capture the **temporal dependencies** between signals
    - For testing:
        - Returns preprocessed signals, times, and RULs.


### **Signal Processing Methods**

1. **`get_input_signals(input_time)`**
    
    - Extracts and processes a time window of signals:
        - Reads CSV files corresponding to the time window.
        - Downsamples signals to reduce resolution (`interval_length`).
        - Normalizes the signals to zero mean and unit variance.
        - Concatenates signals from the time window into a 3D tensor: `(window_size × signal_length × 2)`.
2. **`get_start_minutes(degredation_start, upsampling_repetition)`**
    
    - Computes starting points for sampling:
        - Increases sampling frequency for time points with RUL < `degredation_start`.
        - Outputs indices accounting for `window_size` and upsampling.
3. **`_rul(file_number)`**
    
    - Calculates the RUL for a given file as `total_files - file_number`.
4. **`_preprocess_bearing`**
    
    - Prepares signals for training or testing:
        - Downsamples and normalizes signals.
        - Embeds time windows.
        - Calculates RUL values for each window.
5. **`_time_window_embedding`**
    
    - Constructs overlapping time windows:
        - ==Combines== signals from the current and ==previous `window_size` time steps.==
        - Ensures consistent sampling even if earlier signals have fewer samples.
        - Outputs tensors for signals, RULs, and times.
6. **`_normalize(signals)`**
    
    - Standardizes signals to have zero mean and unit variance across time.
7. **`_down_sampling(signals, interval_length, num_samples)`**
    
    - Reduces signal resolution:
        - Splits signals into intervals of length `interval_length`.
        - Uniformly samples one point per interval.


### **Functionality Summary**

This dataset class:

1. Processes raw vibration signals into manageable time-series tensors suitable for model training/testing.
2. Balances data by upsampling degraded signals and downsampling lengthy signals.
3. Embeds temporal context via sliding time windows, allowing models to learn sequential dependencies.
4. Computes RUL for each sample to enable prognostics and health management tasks.