# IBM Quantum Foundations — Day 9

<div align="center">

![IBM Quantum](https://img.shields.io/badge/IBM%20Quantum-052FAD?style=flat-square&logo=ibm&logoColor=white)
![Qiskit ML](https://img.shields.io/badge/Qiskit%20ML-0.7.2-6929C4?style=flat-square)
![VQC](https://img.shields.io/badge/VQC-Variational%20Classifier-4fc3f7?style=flat-square)
![Python 3.10](https://img.shields.io/badge/Python%203.10-1a1a2e?style=flat-square&logo=python&logoColor=4fc3f7)
![Day 9](https://img.shields.io/badge/Day%2009-Complete-4fc3f7?style=flat-square)
![Day 10](https://img.shields.io/badge/Day%2010-Loading...-555555?style=flat-square)

</div>

<br/>

<div align="center">
<i>Part of the IBM Quantum 20-Day Learning Sprint · VIT Chennai</i>
</div>

---

## Overview

Day 9 moves from quantum kernels (Day 8) to a fully quantum classifier — **VQC (Variational Quantum Classifier)**. Where QSVM replaced only the kernel in a classical SVM framework, VQC replaces the entire model with a parameterized quantum circuit trained end-to-end.

| Method | Type | Accuracy |
|--------|------|----------|
| Classical SVM | Classical | 100.00% |
| QSVM (Day 8) | Quantum kernel | 90.00% |
| VQC (Day 9) | Fully quantum | 60.00% |

---

## What is VQC?

QSVM (Day 8) kept the SVM framework and replaced only the kernel computation with a quantum circuit. The classifier itself was still classical.

VQC goes further. The entire model is a parameterized quantum circuit. There is no SVM involved.

```
Input data
    │
    ▼
ZZFeatureMap  →  encodes x into quantum state |ψ(x)⟩
    │
    ▼
RealAmplitudes  →  trainable ansatz with parameters θ
    │
    ▼
Measurement  →  class prediction (0 or 1)
    │
    ▼
COBYLA optimizer  →  adjusts θ to reduce loss
    │
    └──────────────── repeat until converged
```

This is a quantum neural network. The RealAmplitudes circuit plays the role of the model weights. COBYLA plays the role of gradient descent.

---

## The Circuit

```
ZZFeatureMap (reps=2):
  Encodes [x₀, x₁] into 2 qubits
  Captures feature interactions via ZZ coupling
  2 parameters (data-dependent, not trainable)

RealAmplitudes (reps=3):
  4 layers of RY rotation gates
  3 layers of CX entangling gates
  8 trainable parameters θ[0]...θ[7]

Total circuit depth: ZZFeatureMap + RealAmplitudes
```

---

## Training

```
Optimizer  :  COBYLA
Max iterations  :  100
Starting loss   :  1.1175
Final loss      :  0.8636

Loss at key iterations:
  iteration 10  →  1.1175
  iteration 20  →  0.8961
  iteration 30  →  0.8775
  iteration 50  →  0.8670
  iteration 100 →  0.8636
```

The loss curve shows steady convergence but has not fully plateaued at 100 iterations. A longer run (300–500 iterations) would likely improve accuracy significantly.

---

## Results

```
Dataset     : Iris binary (class 0 vs class 1)
Features    : 2 (sepal length, sepal width)
Train / Test: 70 / 30

Classical SVM  :  100.00%  →  30 / 30 correct
QSVM  (Day 8)  :   90.00%  →  27 / 30 correct
VQC   (Day 9)  :   60.00%  →  18 / 30 correct
```

---

## Why VQC Scores Lower on Iris

This result is expected.

Iris is a simple, low-dimensional dataset that classical SVM was essentially designed for. VQC with 100 iterations and 8 parameters on a 2-qubit circuit is not the right tool for this problem — and the loss curve confirms it has not yet converged.

The comparison is still valuable because it establishes a baseline. Quantum classifiers become competitive when:

```
→ Data dimensionality is very high
→ The decision boundary is complex
→ Training runs for sufficient iterations
→ Circuit depth matches problem complexity
→ Data has quantum-native structure
```

Day 10 applies VQC to the MNIST subset — a harder problem where the gap between classical and quantum begins to close.

---

## Three Methods Side by Side

| Property | Classical SVM | QSVM | VQC |
|----------|--------------|------|-----|
| Kernel | RBF (classical) | ZZFeatureMap | None |
| Model | Classical SVM | Classical SVM | Quantum circuit |
| Training | Convex optimization | Kernel matrix | Iterative optimizer |
| Parameters | Implicit (SVM) | None (kernel) | 8 (θ[0]...θ[7]) |
| Iterations needed | Fast | Fast | 300–500+ |
| Quantum component | None | Kernel only | Entire model |

---

## Tech Stack

```python
qiskit                    == 1.2.4
qiskit-aer                == 0.14.2
qiskit-machine-learning   == 0.7.2
qiskit-algorithms         >= 0.3.0
scikit-learn              >= 1.0.0
matplotlib                >= 3.7.0
numpy                     >= 1.24.0
python-dotenv             >= 1.0.0
```

---

## Setup

```bash
conda activate qml_env
pip install qiskit==1.2.4 qiskit-aer==0.14.2 qiskit-machine-learning==0.7.2 qiskit-algorithms scikit-learn matplotlib numpy jupyter
jupyter notebook
```

---

## Project Structure

```
IBM_QuantumFoundation_Day9/
│
├── notebooks/
│   └── 01_vqc_classifier.ipynb
│
├── results/
│   └── vqc_comparison.png
│
├── .env
├── .gitignore
├── requirements.txt
└── README.md
```

---

## Sprint Progress

```
Day 01  ──  ✅  Qiskit setup · Hello Quantum · first IBM cloud circuit
Day 02  ──  ✅  Superposition · Entanglement · Multi-gate circuits
Day 03  ──  ✅  Gates deep-dive · Grover's algorithm
Day 04  ──  ✅  VQE · parametric circuits · COBYLA optimizer
Day 05  ──  ✅  QAOA · MaxCut · optimal partition found
Day 06  ──  ✅  Quantum Error Mitigation · noise models · ZNE
Day 07  ──  ✅  All 5 experiments on real IBM hardware · ibm_torino
Day 08  ──  ✅  QSVM · ZZFeatureMap · Classical vs Quantum comparison
Day 09  ──  ✅  VQC · Variational Quantum Classifier · end-to-end training
Day 10  ──  ⬡   Project 1 intensive · QML Classifier on MNIST subset
·
·
Day 20  ──  ·   Final push
```
```
## What's Next — Day 10

Day 10 is Project 1 intensive. The same VQC pipeline moves to a harder dataset — MNIST handwritten digits (binary subset). The notebook adds confusion matrices, accuracy plots, and a professional GitHub structure.

Project 1 deliverable: a complete, portfolio-ready QML classifier repository.
```

```
## Security

- Credentials stored in `.env`, excluded from version control via `.gitignore`
- All results reproducible locally — no IBM token required
```
