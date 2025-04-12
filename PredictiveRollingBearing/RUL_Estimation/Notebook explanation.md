```python
import os

import numpy as np

import pandas as pd

import glob

import matplotlib.pyplot as plt

from scipy import stats

from scipy.fft import fft

from sklearn.preprocessing import StandardScaler

from sklearn.model_selection import train_test_split

from sklearn.ensemble import RandomForestRegressor

from sklearn.metrics import mean_absolute_error, mean_squared_error, r2_score

import tensorflow as tf

import pywt

# 1. DEFINE DATA LOADING AND SPLIT CONFIGURATION

train_folders = {

"35Hz12kN": ["Bearing1_1", "Bearing1_2", "Bearing1_3"],

"37.5Hz11kN": ["Bearing2_1", "Bearing2_2", "Bearing2_3"],

"40Hz10kN": ["Bearing3_1", "Bearing3_2", "Bearing3_3"]

}

test_folders = {

"35Hz12kN": ["Bearing1_5", "Bearing1_4"],

"37.5Hz11kN": ["Bearing2_5", "Bearing2_4"],

"40Hz10kN": ["Bearing3_5", "Bearing3_4"]

}

  

# 2. FEATURE EXTRACTION

  
  
  

def extract_features(signal, window_size=2048, fs=25600):

"""Extract time and frequency domain features from a vibration signal for bearing RUL prediction"""

features = {}

  

# Basic time domain features

features['mean'] = np.mean(signal)

features['std'] = np.std(signal)

features['min'] = np.min(signal)

features['max'] = np.max(signal)

features['median'] = np.median(signal)

features['skew'] = stats.skew(signal)

features['kurtosis'] = stats.kurtosis(signal)

features['rms'] = np.sqrt(np.mean(np.square(signal)))

features['peak2peak'] = np.max(signal) - np.min(signal)

features['peak'] = np.max(np.abs(signal))

features['crest_factor'] = features['peak'] / features['rms'] if features['rms'] != 0 else 0

features['shape_factor'] = features['rms'] / np.mean(np.abs(signal)) if np.mean(np.abs(signal)) != 0 else 0

features['impulse_factor'] = features['peak'] / np.mean(np.abs(signal)) if np.mean(np.abs(signal)) != 0 else 0

  

# Advanced statistical features

features['variance'] = np.var(signal)

features['abs_mean'] = np.mean(np.abs(signal))

features['clearance_factor'] = features['peak'] / np.mean(np.sqrt(np.abs(signal)))**2 if np.mean(np.sqrt(np.abs(signal))) != 0 else 0

features['margin_factor'] = features['peak'] / np.mean(np.abs(signal))**2 if np.mean(np.abs(signal)) != 0 else 0

  

# Percentile features

features['percentile_25'] = np.percentile(signal, 25)

features['percentile_75'] = np.percentile(signal, 75)

features['interquartile_range'] = features['percentile_75'] - features['percentile_25']

  

# Zero crossing rate

zero_crossings = np.where(np.diff(np.signbit(signal)))[0]

features['zero_crossing_rate'] = len(zero_crossings) / len(signal)

  

# Mean crossing rate

mean_crossings = np.where(np.diff(np.signbit(signal - features['mean'])))[0]

features['mean_crossing_rate'] = len(mean_crossings) / len(signal)

  

# Signal to noise ratio estimation (assuming the signal mean represents noise level)

if features['std'] != 0:

features['snr'] = abs(features['mean']) / features['std']

else:

features['snr'] = 0

  

# Sample entropy (complexity measure)

try:

features['sample_entropy'] = entropy(np.histogram(signal, bins=20)[0])

except:

features['sample_entropy'] = 0

  

# Frequency domain features (if signal is long enough)

if len(signal) >= 64:

n = len(signal)

fft_vals = fft(signal)

fft_mag = np.abs(fft_vals[:n//2]) / n

freq = np.fft.fftfreq(n, 1/fs)[:n//2]

  

# Basic spectral statistics

features['fft_mean'] = np.mean(fft_mag)

features['fft_std'] = np.std(fft_mag)

features['fft_max'] = np.max(fft_mag)

features['fft_energy'] = np.sum(fft_mag**2)

  

# Spectral shape features

features['spectral_kurtosis'] = stats.kurtosis(fft_mag)

features['spectral_skewness'] = stats.skew(fft_mag)

  

# Find dominant frequencies (top 3)

sorted_indices = np.argsort(fft_mag)[::-1]

for i in range(min(3, len(sorted_indices))):

idx = sorted_indices[i]

features[f'dominant_freq_{i+1}'] = freq[idx]

features[f'dominant_amp_{i+1}'] = fft_mag[idx]

  

# Spectral centroid and bandwidth

if np.sum(fft_mag) > 0:

features['spectral_centroid'] = np.sum(freq * fft_mag) / np.sum(fft_mag)

features['spectral_bandwidth'] = np.sqrt(np.sum(((freq - features['spectral_centroid'])**2) * fft_mag) / np.sum(fft_mag))

else:

features['spectral_centroid'] = 0

features['spectral_bandwidth'] = 0

  

# Power in frequency bands (divide into 5 bands)

band_size = len(freq) // 5

for i in range(5):

start_idx = i * band_size

end_idx = (i + 1) * band_size if i < 4 else len(freq)

features[f'freq_band_{i+1}'] = np.sum(fft_mag[start_idx:end_idx]**2)

  

# Spectral Moments

for i in range(1, 5): # 1st through 4th spectral moments

if np.sum(fft_mag) > 0:

features[f'spectral_moment_{i}'] = np.sum((freq**i) * fft_mag) / np.sum(fft_mag)

else:

features[f'spectral_moment_{i}'] = 0

  

# Spectral Entropy

fft_mag_norm = fft_mag / np.sum(fft_mag) if np.sum(fft_mag) > 0 else fft_mag

fft_mag_norm = fft_mag_norm[fft_mag_norm > 0] # Avoid log(0)

if len(fft_mag_norm) > 0:

features['spectral_entropy'] = -np.sum(fft_mag_norm * np.log2(fft_mag_norm))

else:

features['spectral_entropy'] = 0

  

# Bearing-specific frequency features based on condition

# Extract Hz value from condition string (e.g., "35Hz12kN" -> 35)

shaft_freq = 35 # Default, will be overridden if condition is provided

  

# Calculate typical fault frequencies as ratio of shaft frequency

d = 0.05 # Example: ball diameter (m)

D = 0.2 # Example: pitch diameter (m)

n = 9 # Example: number of rolling elements

angle = np.radians(0) # Example: contact angle (radians)

  

# Ball Pass Frequency Outer race

bpfo = (n/2) * (1 - (d/D) * np.cos(angle)) * shaft_freq

# Ball Pass Frequency Inner race

bpfi = (n/2) * (1 + (d/D) * np.cos(angle)) * shaft_freq

# Ball Spin Frequency

bsf = (D/(2*d)) * (1 - ((d/D) * np.cos(angle))**2) * shaft_freq

# Fundamental Train Frequency

ftf = (1/2) * (1 - (d/D) * np.cos(angle)) * shaft_freq

  

# Calculate power in narrow bands around these frequencies

band_width = fs / (n * 10) # Width of frequency bands

  

for fault_name, fault_freq in [

('bpfo', bpfo),

('bpfi', bpfi),

('bsf', bsf),

('ftf', ftf)

]:

lower_idx = np.argmin(np.abs(freq - (fault_freq - band_width/2)))

upper_idx = np.argmin(np.abs(freq - (fault_freq + band_width/2)))

features[f'{fault_name}_power'] = np.sum(fft_mag[lower_idx:upper_idx+1]**2)

  

# Also check harmonics (2x and 3x the fundamental frequency)

for harmonic in [2, 3]:

harm_freq = fault_freq * harmonic

if harm_freq < freq[-1]:

lower_idx = np.argmin(np.abs(freq - (harm_freq - band_width/2)))

upper_idx = np.argmin(np.abs(freq - (harm_freq + band_width/2)))

features[f'{fault_name}_h{harmonic}_power'] = np.sum(fft_mag[lower_idx:upper_idx+1]**2)

  

# Power Spectral Density

try:

f, psd = signal.welch(signal, fs=fs, nperseg=min(256, len(signal)))

features['psd_max'] = np.max(psd)

features['psd_mean'] = np.mean(psd)

features['psd_std'] = np.std(psd)

features['psd_skewness'] = stats.skew(psd)

features['psd_kurtosis'] = stats.kurtosis(psd)

except:

features['psd_max'] = 0

features['psd_mean'] = 0

features['psd_std'] = 0

features['psd_skewness'] = 0

features['psd_kurtosis'] = 0

  

# Wavelet decomposition features (using discrete wavelet transform)

try:

wavelet = 'db4' # Daubechies wavelet

max_level = 4

  

# Ensure signal length is sufficient for wavelet decomposition

coeffs = pywt.wavedec(signal, wavelet, level=min(pywt.dwt_max_level(len(signal), pywt.Wavelet(wavelet).dec_len), 3))

  

# Extract features from each wavelet decomposition level

for i, coeff in enumerate(coeffs):

if i < 4: # Only use first 4 levels (l0 to l3)

features[f'wavelet_l{i}_mean'] = np.mean(coeff)

features[f'wavelet_l{i}_std'] = np.std(coeff)

features[f'wavelet_l{i}_energy'] = np.sum(coeff**2)

features[f'wavelet_l{i}_kurtosis'] = stats.kurtosis(coeff)

features[f'wavelet_l{i}_skewness'] = stats.skew(coeff)

features[f'wavelet_l{i}_max'] = np.max(np.abs(coeff))

features[f'wavelet_l{i}_shannon_entropy'] = entropy(np.histogram(coeff, bins=20)[0]) if len(coeff) > 0 else 0

except Exception:

# Skip wavelet features if they can't be computed

pass

  

# Time-frequency domain features using CWT (Continuous Wavelet Transform)

try:

# Use a smaller subset of scales to keep computation manageable

scales = np.arange(1, min(64, len(signal)//4))

  

# Compute CWT with Morlet wavelet

coefficients, frequencies = pywt.cwt(signal, scales, 'morl')

  

# Extract features from CWT

cwt_abs = np.abs(coefficients)

features['cwt_mean'] = np.mean(cwt_abs)

features['cwt_std'] = np.std(cwt_abs)

features['cwt_max'] = np.max(cwt_abs)

features['cwt_energy'] = np.sum(cwt_abs**2)

  

# Find dominant scale/frequency

scale_idx, time_idx = np.unravel_index(np.argmax(cwt_abs), cwt_abs.shape)

features['cwt_dominant_scale'] = scales[scale_idx] if scale_idx < len(scales) else scales[-1]

features['cwt_dominant_time'] = time_idx / len(signal) * len(signal) / fs

except Exception:

# Skip CWT features if they can't be computed

pass

  

return features

  
  

# 3. DATA LOADING AND PROCESSING

def load_bearing_data(dataset_path, condition, bearing, window_size=2048):

"""Load and process data for a single bearing"""

bearing_path = os.path.join(dataset_path, condition, bearing)

csv_files = sorted(glob.glob(f"{bearing_path}/*.csv"),

key=lambda x: int(os.path.basename(x).split('.')[0]))

  

if not csv_files:

raise ValueError(f"No CSV files found in {bearing_path}")

  

all_features = []

  

for file_idx, csv_file in enumerate(csv_files):

# Read CSV

df = pd.read_csv(csv_file)

  

# Extract horizontal and vertical vibration signals

h_signal = df['Horizontal_vibration_signals'].values

v_signal = df['Vertical_vibration_signals'].values

  

# Process in windows

for start_idx in range(0, len(h_signal), window_size):

end_idx = start_idx + window_size

if end_idx <= len(h_signal):

# Get window

h_window = h_signal[start_idx:end_idx]

v_window = v_signal[start_idx:end_idx]

  

# Extract features

h_features = extract_features(h_window, window_size, fs=25600)

v_features = extract_features(v_window, window_size, fs=25600)

  

# Combine features with metadata

features = {

'condition': condition,

'bearing': bearing,

'file_idx': file_idx,

'window_start': start_idx

}

  

# Add horizontal and vertical features with prefixes

for key, value in h_features.items():

features[f'h_{key}'] = value

for key, value in v_features.items():

features[f'v_{key}'] = value

  

all_features.append(features)

  

return pd.DataFrame(all_features)

  
  

def assign_health_index(df):

"""

Assign health index from 1.0 (healthy) to 0.0 (failed)

Based on file_idx: assumes files are ordered chronologically

"""

# Group by bearing to normalize within each bearing's lifecycle

for bearing in df['bearing'].unique():

bearing_data = df[df['bearing'] == bearing]

max_file_idx = bearing_data['file_idx'].max()

  

if max_file_idx > 0: # Avoid division by zero

# Assign health index: 1.0 (start) to 0.0 (end)

df.loc[df['bearing'] == bearing, 'health_index'] = 1.0 - (df.loc[df['bearing'] == bearing, 'file_idx'] / max_file_idx)

  

return df

  
  

def load_and_process_data(dataset_path, train_folders, test_folders, window_size=128):

"""Load and process all training and test data"""

train_data = pd.DataFrame()

test_data = pd.DataFrame()

  

# Process training data

for condition, bearings in train_folders.items():

for bearing in bearings:

try:

print(f"Processing training data: {condition}/{bearing}")

bearing_df = load_bearing_data(dataset_path, condition, bearing,window_size)

bearing_df.to_csv(f'{condition}_{bearing}.csv', index=False)

train_data = pd.concat([train_data, bearing_df], ignore_index=True)

except Exception as e:

print(f"Error processing {condition}/{bearing}: {e}")

  

# Process test data

for condition, bearings in test_folders.items():

for bearing in bearings:

try:

print(f"Processing test data: {condition}/{bearing}")

bearing_df = load_bearing_data(dataset_path, condition, bearing,window_size)

bearing_df.to_csv(f'{condition}_{bearing}.csv', index=False)

test_data = pd.concat([test_data, bearing_df], ignore_index=True)

except Exception as e:

print(f"Error processing {condition}/{bearing}: {e}")

  

# Assign health index to both datasets

train_data = assign_health_index(train_data)

test_data = assign_health_index(test_data)

  

return train_data, test_data

  
  
  
  
  

# 8. USAGE EXAMPLE

if __name__ == "__main__":

# Define dataset path and folders

base_dir = "/content/drive/MyDrive/XJTU-SY_Bearing_Datasets"

  

dataset_path = base_dir

  

# Define train and test folders

train_folders = {

"35Hz12kN": ["Bearing1_1", "Bearing1_2", "Bearing1_3"],

"37.5Hz11kN": ["Bearing2_1", "Bearing2_2", "Bearing2_3"],

"40Hz10kN": ["Bearing3_1", "Bearing3_2", "Bearing3_3"]

}

test_folders = {

"35Hz12kN": ["Bearing1_5", "Bearing1_4"],

"37.5Hz11kN": ["Bearing2_5", "Bearing2_4"],

"40Hz10kN": ["Bearing3_5", "Bearing3_4"]

}

  
  

###TODO: WINDOW_SIZE1

window_size=1024

# Run the complete pipeline

train_data, test_data = load_and_process_data(dataset_path, train_folders, test_folders, window_size)

  

# results = run_rul_prediction_pipeline(dataset_path, train_folders, test_folders)

  

# # Show some plots

import pandas as pd

  

# Assuming train_data and test_data are pandas DataFrames

  

# Save the DataFrames to CSV files

train_data.to_csv('train_data.csv', index=False)

test_data.to_csv('test_data.csv', index=False)

# results['plots']['rf_predictions'].show()

# results['plots']['lstm_predictions'].show()

# results['plots']['rf_importance'].show()

# results['plots']['lstm_history'].show()

  

# # Save models if needed

# # Save Random Forest model

# # import joblib

# # joblib.dump(results['rf_results']['model'], 'rf_rul_model.joblib')

  

# # Save LSTM model

# # results['lstm_results']['model'].save('lstm_rul_model.h5')
```

### 🧠 **Feature Extraction: `extract_features()`**

This is the heart of your pipeline, extracting both **time domain**, **frequency domain**, and **time-frequency** features from vibration signals.

#### Time-domain features:

- `mean`, `std`, `min`, `max`, `median`, `rms`, `skew`, `kurtosis`, etc.
    
- `crest_factor`, `shape_factor`, `impulse_factor`: used to indicate wear severity.

#### Statistical and signal structure features:

- `zero_crossing_rate`, `mean_crossing_rate`, `sample_entropy` (entropy of histogram)
    
- Percentiles and IQR for signal spread

#### Frequency-domain features:

- Fast Fourier Transform (FFT): extract magnitude spectrum and analyze:
    
    - Spectral shape: `spectral_kurtosis`, `spectral_skewness`
        
    - Energy in frequency bands and dominant frequencies
        
    - **Spectral moments (1st to 4th)** and **entropy**
        
    - **Bearing-specific fault frequencies**: BPFO, BPFI, BSF, FTF, with harmonic power too.

#### Wavelet domain:

- `pywt.wavedec`: Extract coefficients at 4 levels using Daubechies wavelet
    
- Statistics on wavelet coefficients

#### Time-frequency (CWT):

- Continuous Wavelet Transform using `'morl'` (Morlet wavelet)
    
- Energy, max values, dominant time and scale

---
### **Data Loader: `load_bearing_data()`**

Reads all `.csv` files for a given bearing under a condition:

1. Loops through CSVs in order
    
2. Extracts horizontal/vertical vibration columns
    
3. Splits them into **fixed-size windows** (e.g. 1024 samples)
    
4. Calls `extract_features()` for each window
    
5. Combines horizontal (`h_`) and vertical (`v_`) features

Returns: ==DataFrame with all features for a bearing.==

---
### **Assign Health Index: `assign_health_index()`**

Assigns a **degrading health index** (1.0 to 0.0):

- Based on file index (chronological wear progression)
    
- Used later as a target variable for regression
![[Pasted image 20250412155035.png]]

---
### **Main Processing Function: `load_and_process_data()`**

Calls `load_bearing_data()` for:

- **All training and test bearings**
    
- Saves per-bearing CSV files
    
- Assigns health index to each row
    
- Returns combined `train_data` and `test_data`

---
### **Usage Example (Main Script)**
```python
if __name__ == "__main__":
```

Defines:

- Dataset path (e.g., Google Drive)
    
- Window size (`1024`)
    
- Runs the full pipeline:
    
    - Loads raw data → extracts features → builds labeled datasets
        
    - Saves them to CSV files

---
### Summary

This script builds a **complete data preprocessing and feature engineering** pipeline for a bearing prognostics task, extracting hundreds of engineered features from raw vibration signals using a mix of:

- Classic time and frequency analysis
    
- Bearing-specific domain knowledge (BPFO etc.)
    
- Wavelet and time-frequency transforms

---

