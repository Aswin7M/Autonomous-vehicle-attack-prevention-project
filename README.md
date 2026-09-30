# Autonomous Vehicle Cyber Attack Detection Using Deep Learning

## Overview

Autonomous and connected vehicles rely heavily on Controller Area Network (CAN) communication for exchanging information between Electronic Control Units (ECUs). Since traditional CAN communication does not provide strong built-in authentication mechanisms, it can be vulnerable to cyber attacks such as flooding, fuzzy attacks, and spoofing.

This project develops a deep learning-based intrusion detection system for identifying cyber attacks in CAN bus traffic.

The proposed system uses a **Residual Attention CNN-BiLSTM (RA-CBiLSTM)** architecture to learn both spatial and temporal characteristics of CAN traffic and classify network frames into normal and multiple attack categories.

The project also compares the proposed deep learning architecture against several classical machine learning and deep learning approaches.

---

## Problem Statement

Modern vehicles generate large amounts of CAN bus traffic between different ECUs. Attackers can exploit weaknesses in the CAN protocol to inject malicious messages into the communication network.

The objective of this project is to develop an intelligent intrusion detection system capable of:

* Detecting abnormal CAN bus traffic.
* Identifying different types of cyber attacks.
* Learning temporal patterns in vehicle communication.
* Handling noisy and overlapping attack characteristics.
* Comparing classical machine learning models with deep learning architectures.
* Evaluating the effectiveness of a proposed Residual Attention CNN-BiLSTM model.

---

## Objectives

The main objectives of this project are:

1. Generate and process a challenging CAN bus intrusion detection dataset.
2. Perform exploratory data analysis on CAN traffic.
3. Engineer meaningful features from CAN frames.
4. Develop classical machine learning baselines.
5. Develop conventional deep learning architectures.
6. Design a Residual Attention CNN-BiLSTM architecture.
7. Compare the performance of different models.
8. Evaluate models using multiple classification metrics.
9. Analyze confusion matrices and ROC curves.
10. Develop a robust foundation for real-time automotive intrusion detection.

---

## Attack Classes

The system performs five-class classification.

| Label | Class         | Description                                                         |
| ----- | ------------- | ------------------------------------------------------------------- |
| 0     | Normal        | Legitimate CAN bus traffic                                          |
| 1     | Flood         | High-volume message injection intended to overwhelm the CAN network |
| 2     | Fuzzy         | Random or malformed CAN messages injected into the network          |
| 3     | Spoofing_Gear | Manipulation of gear-related CAN information                        |
| 4     | Spoofing_RPM  | Manipulation of engine RPM-related CAN information                  |

---

## Proposed Architecture

The primary model proposed in this project is:

**Residual Attention CNN-BiLSTM (RA-CBiLSTM)**

The architecture combines:

* Convolutional Neural Networks (CNN)
* Residual connections
* Attention mechanisms
* Bidirectional Long Short-Term Memory (BiLSTM)
* Fully connected classification layers

The overall processing pipeline is:

```text
CAN Bus Traffic
       |
       v
Data Generation / Loading
       |
       v
Data Preprocessing
       |
       v
Feature Engineering
       |
       +-----------------------+
       |                       |
       v                       v
Classical ML Models       Deep Learning Models
       |                       |
       |                       v
       |                CNN / LSTM / CNN-LSTM
       |                       |
       |                       v
       |                   RA-CBiLSTM
       |                       |
       +-----------+-----------+
                   |
                   v
           Model Evaluation
                   |
                   v
           Attack Classification
```

---

## Feature Engineering

The CAN frames contain several low-level communication features.

The project extracts additional statistical and temporal features to improve attack detection.

### Original CAN Features

* Arbitration ID
* Data Length Code (DLC)
* DATA0
* DATA1
* DATA2
* DATA3
* DATA4
* DATA5
* DATA6
* DATA7

### Engineered Features

#### Inter-Arrival Time

The Inter-Arrival Time (IAT) represents the time difference between consecutive CAN messages.

```text
IAT = Timestamp(current frame) - Timestamp(previous frame)
```

IAT can help identify abnormal message frequencies associated with attacks such as flooding.

#### Byte Entropy

Byte entropy measures the variability of values within the CAN payload.

It is calculated using the Shannon entropy:

```text
H(X) = -Σ p(x) log2(p(x))
```

This feature can help identify unusual payload distributions.

#### Payload Mean

The mean value of the eight CAN data bytes is calculated to capture the overall payload characteristics.

#### Payload Standard Deviation

The standard deviation of the CAN payload is calculated to capture variations between individual bytes.

---

## Final Feature Set

The model uses the following features:

```text
Arbitration_ID
DLC
DATA0
DATA1
DATA2
DATA3
DATA4
DATA5
DATA6
DATA7
IAT
Byte_Entropy
Payload_Mean
Payload_Std
```

Therefore, the final feature representation contains **14 features per CAN frame**.

---

## Dataset

The project includes a hardened synthetic CAN bus dataset generator designed to simulate realistic and difficult intrusion-detection conditions.

The default configuration generates:

```text
100,000 base CAN samples
```

The dataset contains five classes:

```text
Normal
Flood
Fuzzy
Spoofing_Gear
Spoofing_RPM
```

The generated data intentionally includes overlapping and noisy characteristics to make the classification problem more challenging.

### Dataset Characteristics

The dataset incorporates:

* Class imbalance
* Borderline samples
* Measurement errors
* Payload mimicry
* RPM and gear range overlap
* Replay artifacts
* Unknown frames
* Label noise
* Inter-arrival time jitter
* CAN ID overlap
* Payload variations

These characteristics are designed to reduce the likelihood of achieving unrealistically high classification performance simply by relying on easily separable features.

---

## Dataset Distribution

The base class distribution is approximately:

| Class         | Percentage |
| ------------- | ---------: |
| Normal        |        70% |
| Flood         |        10% |
| Fuzzy         |         9% |
| Spoofing_Gear |         6% |
| Spoofing_RPM  |         5% |

Additional replay and unknown-frame samples are introduced during dataset generation.

---

## Data Preprocessing

The preprocessing pipeline includes:

1. Dataset generation/loading.
2. Data inspection.
3. Label mapping.
4. Feature engineering.
5. Train-validation-test splitting.
6. Standardization.
7. Class-weight calculation.
8. One-hot encoding for deep learning models.

The dataset is split using stratified sampling to maintain class proportions.

The default split is:

```text
Training:   60%
Validation: 20%
Testing:    20%
```

---

## Machine Learning Models

Several models are implemented for comparison.

### Classical Machine Learning

The project evaluates:

* Support Vector Machine (SVM)
* K-Nearest Neighbors (KNN)
* Decision Tree
* Random Forest

These models provide baseline performance against which the deep learning models can be compared.

---

## Deep Learning Models

The project also evaluates multiple neural network architectures, including:

* Autoencoder + CNN
* CNN-LSTM
* Residual Attention CNN-BiLSTM

The RA-CBiLSTM model is the primary proposed architecture.

---

## RA-CBiLSTM Architecture

The proposed Residual Attention CNN-BiLSTM model combines local feature extraction with temporal sequence modelling.

The conceptual architecture is:

```text
Input CAN Feature Sequence
          |
          v
    CNN Feature Extraction
          |
          v
    Residual Connection
          |
          v
    Attention Mechanism
          |
          v
       BiLSTM Layer
          |
          v
      Dense Layer
          |
          v
    Softmax Classification
          |
          v
        5 Classes
```

### CNN

The convolutional layers extract local relationships between CAN traffic features.

### Residual Connections

Residual connections allow information to bypass intermediate layers and help preserve useful low-level representations.

Conceptually:

```text
Output = F(x) + x
```

where `F(x)` represents the learned transformation.

### Attention Mechanism

The attention mechanism allows the model to assign different importance to different parts of the input sequence.

This is particularly useful when certain CAN frames or temporal patterns contain stronger indicators of an attack.

### Bidirectional LSTM

The BiLSTM processes sequential information in both forward and backward directions.

This enables the model to learn temporal dependencies from both directions of the input sequence.

---

## Training Configuration

The project uses:

```text
Python
TensorFlow / Keras
NumPy
Pandas
Scikit-learn
Matplotlib
Seaborn
```

Random seeds are configured for reproducibility:

```python
np.random.seed(42)
tf.random.set_seed(42)
```

The project can take advantage of GPU acceleration when running in environments such as Google Colab.

---

## Evaluation Metrics

The models are evaluated using multiple metrics.

### Accuracy

Measures the overall percentage of correctly classified samples.

```text
Accuracy = Correct Predictions / Total Predictions
```

### Precision

Measures how many samples predicted as a particular class actually belong to that class.

### Recall

Measures how many samples belonging to a particular class were successfully detected.

### F1-Score

The F1-score combines precision and recall.

```text
F1 = 2 × (Precision × Recall) / (Precision + Recall)
```

### Matthews Correlation Coefficient

MCC is used as an additional metric for evaluating classification performance, particularly in the presence of class imbalance.

### Confusion Matrix

The confusion matrix provides a detailed view of:

* Correct classifications
* False positives
* False negatives
* Confusion between attack classes

### ROC-AUC

ROC curves and AUC are also used to analyze the classification behaviour of the models.

---

## Exploratory Data Analysis

The project performs exploratory analysis on the generated CAN traffic.

The analysis includes:

* Class distribution
* Class proportions
* DLC distribution
* Inter-arrival time distribution
* Payload distribution
* Feature correlation
* Correlation heatmaps

These visualizations help identify patterns and overlaps between normal and attack traffic.

---

## Project Structure

The current repository contains the main implementation:

```text
AV-Cyber-Attack-detection/
│
├── av_cyberattack_detection.py
│
└── README.md
```

The Python file contains the complete experimental pipeline, including:

```text
Dataset Generation
       |
EDA
       |
Feature Engineering
       |
Data Splitting
       |
Feature Scaling
       |
Classical ML
       |
Deep Learning
       |
RA-CBiLSTM
       |
Model Evaluation
```

---

## Installation

Clone the repository:

```bash
git clone https://github.com/<your-username>/AV-Cyber-Attack-detection.git
cd AV-Cyber-Attack-detection
```

Install the required dependencies:

```bash
pip install numpy pandas matplotlib seaborn scikit-learn tensorflow
```

For a reproducible environment, it is recommended to create a virtual environment:

```bash
python -m venv venv
```

On Windows:

```bash
venv\Scripts\activate
```

Then install the dependencies:

```bash
pip install -r requirements.txt
```

If a `requirements.txt` file is not present, the required packages can be installed manually using the command above.

---

## Running the Project

The project was originally developed using Google Colab.

To run the project:

1. Clone the repository.
2. Open `av_cyberattack_detection.py`.
3. Ensure all required Python packages are installed.
4. Run the script in a Python environment with sufficient memory.
5. For deep learning experiments, GPU acceleration is recommended.

The dataset is generated programmatically, so an external dataset file is not required for the current implementation.

---

## Computational Requirements

The project generates a relatively large synthetic dataset and trains multiple machine learning and deep learning models.

Recommended environment:

```text
Python 3.9+
RAM: 8 GB or higher
GPU: Recommended for deep learning experiments
Storage: At least 5 GB free space
```

For faster experimentation, Google Colab with GPU acceleration can be used.

---

## Reproducibility

The project uses fixed random seeds:

```python
np.random.seed(42)
tf.random.set_seed(42)
```

The dataset generator also uses a fixed random state.

This helps produce consistent experimental results across runs, although exact results may still vary depending on TensorFlow, CUDA, GPU, and hardware configurations.

---

## Results

The project is designed to compare the performance of multiple approaches:

```text
SVM
KNN
Decision Tree
Random Forest
Autoencoder + CNN
CNN-LSTM
RA-CBiLSTM
```

Performance should be evaluated using:

```text
Accuracy
Precision
Recall
F1-Score
MCC
Confusion Matrix
ROC-AUC
```

The final experimental results should be reported based on the actual execution of the code rather than hard-coded values in this README.

---

## Key Contributions

The major contributions of this project include:

1. Development of a synthetic CAN bus intrusion-detection dataset with challenging attack characteristics.
2. Feature engineering using temporal and statistical CAN traffic features.
3. Comparison of classical machine learning and deep learning approaches.
4. Development of a Residual Attention CNN-BiLSTM architecture.
5. Integration of CNN-based feature extraction with BiLSTM temporal modelling.
6. Application of attention mechanisms for improved sequence representation.
7. Evaluation using multiple classification metrics.
8. Investigation of cyber attacks targeting automotive CAN communication.

---

## Limitations

The current implementation has several limitations:

* The dataset is synthetically generated rather than collected entirely from a physical vehicle.
* Model performance on real-world CAN traffic may differ from performance on the generated dataset.
* The system currently focuses on CAN traffic features rather than the complete vehicle communication stack.
* The implementation is primarily intended for research and experimentation.
* Real-time deployment would require additional optimization and integration with CAN interfaces.
* A machine learning prediction cannot guarantee that a CAN frame is safe or malicious.

---

## Future Work

Potential improvements include:

### Real CAN Bus Dataset

Evaluate the system using publicly available real-world automotive CAN datasets.

### Real-Time Detection

Integrate the model with an actual CAN interface to detect attacks in real time.

```text
Vehicle CAN Bus
      |
      v
CAN Interface
      |
      v
Feature Extraction
      |
      v
RA-CBiLSTM
      |
      v
Attack Detection
      |
      v
Alert / Response
```

### Edge Deployment

Optimize the model for deployment on automotive edge devices.

Possible targets include:

* Raspberry Pi
* NVIDIA Jetson
* Automotive ECUs
* Embedded AI platforms

### Explainable AI

Integrate explainability techniques to identify which CAN features contributed most to an attack prediction.

### Online Learning

Develop an adaptive system capable of learning from newly observed attack patterns.

### Hybrid Detection

Combine machine learning with rule-based CAN security mechanisms and threat intelligence.

---

## Security Considerations

This project is intended for research and educational purposes.

The model should not be considered a complete automotive cybersecurity solution.

A production automotive intrusion detection system should additionally consider:

* CAN protocol behaviour
* ECU authentication
* Message timing
* CAN ID frequency
* Payload validation
* Network topology
* Secure gateways
* Intrusion response mechanisms
* Vehicle-specific communication rules

---

## Research Context

This project explores the application of deep learning to automotive cybersecurity, specifically intrusion detection on Controller Area Network traffic.

The proposed RA-CBiLSTM architecture is designed to combine:

```text
Spatial Feature Learning
        +
Residual Feature Representation
        +
Attention
        +
Temporal Sequence Learning
        =
Automotive Intrusion Detection
```

---

## License

Add an appropriate license to this repository before public distribution.

For example:

```text
MIT License
```

The selected license should reflect the intended use and any restrictions associated with datasets or third-party components.

---

## Author

**Aswin M**

B.Tech Computer Science and Engineering

Areas of Interest:

* Cybersecurity
* Artificial Intelligence
* Machine Learning
* Deep Learning
* Automotive Cybersecurity
* Computer Vision
* Generative AI

---

## Acknowledgements

This project uses open-source Python libraries and machine learning frameworks, including:

* TensorFlow
* Keras
* Scikit-learn
* NumPy
* Pandas
* Matplotlib
* Seaborn

These tools were used for data generation, preprocessing, model development, visualization, and evaluation.
