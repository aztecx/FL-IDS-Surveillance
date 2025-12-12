# An Adaptive Defense Framework for Byzantine-Robust Federated Learning in Industrial IoT Intrusion Detection Systems

[![Python 311](https://img.shields.io/badge/python-3.11+-blue.svg)](https://www.python.org/downloads/) [![TensorFlow 2x](https://img.shields.io/badge/TensorFlow-2.x-orange.svg)](https://www.tensorflow.org/) [![License MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)

> **MSc Thesis Project** | University of Essex | 2024-2025

An adaptive federated learning framework that dynamically responds to Byzantine attacks and concept drift in Industrial IoT environments, validated through real-world deployment on Raspberry Pi devices.

---

##Table of Contents

- [Overview](#overview)
- [Key Features](#key-features)
- [System Architecture](#system-architecture)
- [Project Structure](#project-structure)
- [Installation](#installation)
- [Quick Start](#quick-start)
- [Experimental Workflow](#experimental-workflow)
- [Deployment](#deployment)
- [Results](#results)
- 

---

## Overview

This project addresses three critical challenges in federated learning for Industrial IoT intrusion detection:

1. **Byzantine Attacks**: Malicious clients attempting to poison the global model
2. **Concept Drift**: Evolving data distributions due to changing network behaviors
3. **Resource Constraints**: Limited computational power on edge devices (Raspberry Pi)

### The Solution

An **adaptive defense framework** that:

- Uses **FedAvg** when the network is secure (efficient)
- Switches to **Krum** when attacks are detected (robust)
- Applies **drift-aware weighting** when concept drift occurs (adaptive)

### Key Results

- **98.9% of baseline performance** maintained under 40% malicious clients
- **12-15% performance recovery** from concept drift
- **Robust up to 50% attackers** (theoretical breaking point confirmed)
- **Successfully deployed** on Raspberry Pi 3 Model B devices (906 MB RAM)

---

## Key Features

### Byzantine Attack Defense

- **Real-time attack detection** using L2 norm outlier analysis
- **Adaptive aggregation switching** (FedAvg ↔ Krum)
- **Robust against 4 attack types**: Scaling, Sign-flipping, Adaptive Gradient, Sybil

### Concept Drift Handling

- **Statistical drift detection** using KS-test and PSI
- **Automatic mitigation strategies**: Local retraining, drift-aware weighting
- **12-15% performance recovery** from drift scenarios

### Resource-Efficient Design

- **Lightweight autoencoder** (8 layers, 42 features)
- **Optimized for edge devices** (batch size 256, 1 epoch per round)
- **O(n) attack detection** complexity (vs O(n²) for always-on Krum)

---

## System Architecture

```
┌─────────────────────────────────────────────────────────────┐
│                      Central Server                          │
│  ┌────────────────────────────────────────────────────────┐ │
│  │  1. Aggregation Strategy Selection                     │ │
│  │     • FedAvg (normal) → O(n) complexity                │ │
│  │     • Krum (attack detected) → O(n²) complexity        │ │
│  │                                                          │ │
│  │  2. Attack Detection (L2 norm analysis)                │ │
│  │     • Threshold: μ + 2.0σ                              │ │
│  │                                                          │ │
│  │  3. Drift Detection (KS-test + PSI)                    │ │
│  │     • Per-feature statistical testing                  │ │
│  └────────────────────────────────────────────────────────┘ │
└─────────────────────────────────────────────────────────────┘
                              │
        ┌─────────────────────┼─────────────────────┐
        │                     │                     │
   ┌────▼────┐           ┌────▼────┐          ┌────▼────┐
   │ Client 1│           │ Client 2│   ...    │ Client 5│
   │ (Honest)│           │ (Drift) │          │(Malicious)│
   └─────────┘           └─────────┘          └─────────┘
   Raspberry Pi 3B       Raspberry Pi 3B      Raspberry Pi 3B
```

---

## Project Structure

```
federated-learning-iiot-ids/
│
├── notebooks/                          # Experimental notebooks (research phase)
│   ├── 01_Data_Exploration.ipynb                    # Dataset analysis
│   ├── 02_Model_Development.ipynb                   # Supervised & unsupervised baselines
│   ├── 03_Federated_Unsupervised_Baselines.ipynb    # Federated learning baseline
│   ├── 04_Concept_Drift_and_Mitigation.ipynb        # Drift detection & mitigation
│   ├── 05_Poisoning_Attack_and_Adaptive_Aggregation.ipynb  # Byzantine attacks
│   ├── 06_Combined_Defense_Strategies.ipynb         # Combined threats
│   └── 07_Targeted_Adversarial_Robustness.ipynb     # Robustness limits
│
├── deployment/                         # Production deployment code
│   ├── server.py                       # Federated server with adaptive defense
│   ├── client.py                       # Client training script
│   ├── aggregation.py                  # FedAvg, Krum, Median aggregation
│   ├── config.json                     # Deployment configuration
│   └── shared/                         # Shared resources
│       ├── global_model.h5             # Trained autoencoder model
│       ├── scaler.pkl                  # Feature scaler
│       └── test_mixed.csv              # Test dataset
│
├── paper/                              # Research paper
│   └── AdaptiveDefenseFramework.pdf    # 6-page conference paper
│
├── thesis/                             # Full thesis document
│   └── MSC_Thesis.pdf                  # 50+ page thesis
│
├── results/                            # Experimental results
│   ├── deployment_results.pdf          # Raspberry Pi deployment results
│   └── figures/                        # Performance graphs
│
├── requirements.txt                    # Python dependencies
└── README.md                           # This file
```

---

## Installation

### Prerequisites

- Python 3.11+
- TensorFlow 2.x
- NumPy, Pandas, Scikit-learn
- Raspberry Pi 3 Model B (for deployment)

### Setup

```bash
# Clone the repository
git clone https://github.com/aztecx/FL-IDS-Surveillance.git
cd federated-learning-iiot-ids

# Create virtual environment
python3 -m venv venv
source venv/bin/activate  # On Windows: venv\Scripts\activate

# Install dependencies
pip install -r requirements.txt
```

### Dataset

Download the **EdgeIIoTset** dataset:

- Paper: [Ferrag et al. (2022)](https://ieeexplore.ieee.org/document/9751703)
- Dataset: Available upon request from authors

---

## Quick Start

### 1. Run Experimental Notebooks (Research Phase)

```bash
# Start Jupyter
jupyter notebook

# Open notebooks in order:
# 01 → 02 → 03 → 04 → 05 → 06 → 07
```

### 2. Deploy on Raspberry Pi (Production Phase)

**On Server (Laptop):**

```bash
cd deployment
python server.py
```

**On Each Client (Raspberry Pi):**

```bash
cd deployment
python client.py --client_id client_1
```

### 3. Monitor Results

Check `deployment/shared/logs/` for:

- `server_log.txt` - Aggregation decisions, attack detection
- `client_X_log.txt` - Local training progress

---

## Experimental Workflow

This project follows a systematic 7-phase experimental workflow:

### Phase 1: Data Exploration

**Notebook**: `01_Data_Exploration.ipynb`

- Analyzed EdgeIIoTset dataset (63 features → 42 after preprocessing)
- ~323,000 samples per client
- 14 attack types + normal traffic

### Phase 2: Model Development

**Notebook**: `02_Model_Development.ipynb`

- **Supervised baselines**: Random Forest (F1=0.9960), Logistic Regression, SGD
- **Unsupervised baselines**: Isolation Forest (F1=0.8138), Autoencoder

### Phase 3: Federated Learning Baseline

**Notebook**: `03_Federated_Unsupervised_Baselines.ipynb`

- Implemented FedAvg aggregation
- **Result**: Federated F1=0.8373 (comparable to centralized)
- Optimized threshold: 0.000639

### Phase 4: Concept Drift Detection & Mitigation

**Notebook**: `04_Concept_Drift_and_Mitigation.ipynb`

- Tested gradual drift (F1: 0.712 → 0.825)
- Tested sudden drift (F1: 0.689 → 0.819)
- **Result**: 12-15% performance recovery

### Phase 5: Byzantine Attack Defense

**Notebook**: `05_Poisoning_Attack_and_Adaptive_Aggregation.ipynb`

- Tested 4 attack types: Scaling, Sign-flipping, Adaptive Gradient, Sybil
- **Result**: Median/Krum maintain F1=0.82+ under 40% attackers

### Phase 6: Combined Threats

**Notebook**: `06_Combined_Defense_Strategies.ipynb`

- Simultaneous drift + poisoning attacks
- **Result**: Adaptive framework F1=0.827 vs FedAvg F1=0.427

### Phase 7: Robustness Limits

**Notebook**: `07_Targeted_Adversarial_Robustness.ipynb`

- Tested increasing attacker percentages
- **Result**: Breaking point at 50% attackers (F1=0.428)

---

## Deployment

### Hardware Configuration

| Component    | Specification                                |
| ------------ | -------------------------------------------- |
| **Server**   | ASUS TUF x64, Ubuntu/Anaconda, Python 3.13.5 |
| **Clients**  | Raspberry Pi 3 Model B Rev 1.2 (5x)          |
| **Memory**   | 906 MB RAM per client                        |
| **OS**       | Debian GNU/Linux 13, Kernel 6.12.47          |
| **Software** | Python 3.13.5, TensorFlow 2.20.0             |

### Deployment Configuration

```json
{
  "clients": {
    "client_1": {"role": "honest", "attack": "none", "drift": "none"},
    "client_2": {"role": "honest", "attack": "none", "drift": "gradual"},
    "client_3": {"role": "malicious", "attack": "scaling", "drift": "none"},
    "client_4": {"role": "honest", "attack": "none", "drift": "none"},
    "client_5": {"role": "malicious", "attack": "sign_flip", "drift": "sudden"}
  },
  "server": {
    "num_rounds": 4,
    "aggregation": "adaptive",
    "attack_detection_threshold": 2.0,
    "evaluation_threshold": 0.000639
  }
}
```

### Deployment Results

| Round   | Aggregator | Attackers Detected | F1 Score  | Precision | Recall    |
| ------- | ---------- | ------------------ | --------- | --------- | --------- |
| 1       | FedAvg     | 0                  | 0.836     | 0.988     | 0.724     |
| 2       | Krum       | 2                  | 0.831     | 0.989     | 0.717     |
| 3       | Krum       | 1                  | 0.824     | 0.988     | 0.707     |
| 4       | Krum       | 1                  | 0.820     | 0.988     | 0.700     |
| **Avg** | -          | -                  | **0.828** | **0.988** | **0.712** |

**Key Findings**:

- Only 1.1% degradation from baseline despite 40% malicious clients
- Adaptive switching detected attacks in Round 2
- System remained robust through Rounds 3-4

---

## Results

### Performance Summary

| Scenario                            | F1 Score | vs Baseline | Notes              |
| ----------------------------------- | -------- | ----------- | ------------------ |
| **Baseline (No attacks)**           | 0.837    | -           | FedAvg, no threats |
| **40% Attackers (FedAvg)**          | 0.427    | -49%        | Complete failure   |
| **40% Attackers (Krum)**            | 0.827    | -1.2%       | Robust             |
| **Gradual Drift (No mitigation)**   | 0.712    | -15%        | Performance loss   |
| **Gradual Drift (With mitigation)** | 0.825    | -1.4%       | Recovered          |
| **Combined Threats (Adaptive)**     | 0.827    | -1.2%       | Layered defense    |
| **Deployment (Real hardware)**      | 0.828    | -1.1%       | Raspberry Pi       |

### Key Insights

1. **Adaptive aggregation is essential**: FedAvg fails catastrophically under attack (F1=0.427), while adaptive switching maintains F1=0.827
2. **Drift mitigation recovers 12-15% performance**: Critical for long-term deployment
3. **50% is the theoretical breaking point**: Median-based defenses fail beyond this threshold
4. **Real-world validation successful**: Raspberry Pi deployment confirms practical feasibility

---

## Technical Details

### Model Architecture

**Autoencoder** (Unsupervised Anomaly Detection):

- **Input**: 42 normalized features
- **Encoder**: 32 → 16 → 8 neurons (ReLU)
- **Bottleneck**: 8-dimensional latent space
- **Decoder**: 16 → 32 neurons (ReLU)
- **Output**: 42 reconstructed features (Sigmoid)
- **Loss**: Mean Squared Error (MSE)
- **Optimizer**: Adam
- **Threshold**: 0.000639 (empirically optimized)

### Aggregation Methods

1. **FedAvg** (Federated Averaging)
   
   - Simple weighted average of client updates
   - O(n) complexity
   - Fast but vulnerable to attacks

2. **Krum**
   
   - Selects most trustworthy client based on pairwise distances
   - O(n²) complexity
   - Robust but computationally expensive

3. **Median**
   
   - Element-wise median of client updates
   - O(n log n) complexity
   - Robust and efficient

### Attack Detection

**L2 Norm Outlier Analysis**:

```python
threshold = mean_norm + k * std_norm  # k = 2.0
attackers = [i for i, norm in enumerate(norms) if norm > threshold]
```

### Drift Detection

**KS-test** (Kolmogorov-Smirnov):

- Tests if two distributions are significantly different
- p-value < 0.05 indicates drift

**PSI** (Population Stability Index):

- Measures distribution shift between time windows
- PSI > 0.2 indicates significant drift

---

## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

---

**⭐ If you find this work useful, please consider starring the repository! ⭐**

*Built with ❤️ for secure Industrial IoT systems*
