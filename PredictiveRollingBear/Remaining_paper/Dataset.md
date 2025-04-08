
The **XJTU-SY Bearing Dataset** represents **run-to-failure vibration data** from rolling element bearings, collected during accelerated degradation tests under controlled conditions. Here’s a breakdown of what this dataset represents at any point in time:

![[Pasted image 20250126185124.png]]

---

### **What Does the Dataset Represent?**

1. **Vibration Signals from Bearings**:
    
    - The dataset captures vibration signals in two directions:
        - **Horizontal direction**.
        - **Vertical direction**.
    - These signals are collected using two accelerometers mounted at 90° on the bearing housing.
2. **Time-Series Data**:
    
    - The vibration signals are sampled at a frequency of **25.6 kHz**.
    - Each sampling period collects **32768 data points** (equivalent to 1.28 seconds of data).
    - Sampling is performed every **1 minute**, producing time-series data that reflects the degradation process over time.
3. **Degradation Process**:
    
    - Bearings are subjected to different operating conditions (e.g., rotational speeds and loads).
    - The tests are run until the bearings fail (i.e., when the amplitude of vibration exceeds 10× the normal level).
    - The dataset captures the progression from normal operating conditions to severe failure, including different types of faults (e.g., inner race wear, cage fracture, outer race damage).
4. **Bearing Life and Fault Information**:
    
    - For each bearing, the dataset provides:
        - **Lifetime** (total time until failure).
        - **Fault element** (e.g., inner race, outer race, cage, or multiple components).
        - Number of recorded vibration signal files.

---

### **Data Structure at Any Point of Time**

At any point in time, the dataset provides:

- **Horizontal Vibration Signals**: First column in each CSV file.
- **Vertical Vibration Signals**: Second column in each CSV file.
- **Time Information**: The sampling period (every minute) marks the time progression during the degradation process.

In the context of the **XJTU-SY Bearing Dataset**, **vibration** refers to the oscillatory motion of the rolling element bearings caused by mechanical forces, imbalances, and defects during their operation. The vibration signals are used as a key indicator to monitor the health of the bearings and to detect degradation or failure.

---

### **Experimental Setup**

1. **Operating Conditions**:
    
    - The tests are conducted under three different operating conditions:
        - **Condition 1**: 2100 RPM and 12 kN load.
        - **Condition 2**: 2250 RPM and 11 kN load.
        - **Condition 3**: 2400 RPM and 10 kN load.
2. **Data Sampling**:
    
    - ==Accelerometers measure vibrations==, and data is recorded every minute until failure.
    - Sampling frequency is **25.6 kHz**, producing high-resolution vibration signals.

---

Note:

- The interval in which vibration become increasing from constant, is called degradation interval. Since degradation interval is shorter, we get 4 times more samples (sampling frequency times 4 ) than the normal intervals.

- Units of time are each 16 samples (from 32768 sample generated each time). At any time t we get a uniform sample from that 16 data and call it Xt.
- The data at time t is:
  X't = (Xt-2, Xt-1, Xt)

- So at each sample at time t we have 2 times 6144 records. (3.32768/16)