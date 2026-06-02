# GNN-based Intrusion Detection System

> **95% detection accuracy on NSL-KDD · 12% F1 improvement over all classical ML baselines · Highest-performing model in cohort of 120 — Alliance University Security Symposium 2025**

![Python](https://img.shields.io/badge/Python-3.9+-blue?style=flat-square&logo=python)
![TensorFlow](https://img.shields.io/badge/TensorFlow-2.x-orange?style=flat-square&logo=tensorflow)
![GNN](https://img.shields.io/badge/Graph%20Neural%20Network-GCN-purple?style=flat-square)
![NSL-KDD](https://img.shields.io/badge/Dataset-NSL--KDD-lightgrey?style=flat-square)
![Research](https://img.shields.io/badge/Presented%20at-Alliance%20University%202025-blue?style=flat-square)

---

## What It Does

A Graph Neural Network-based Intrusion Detection System that models network traffic as graph structures to detect complex inter-node attack patterns invisible to traditional machine learning approaches. Unlike flat-feature classifiers (SVM, Random Forest, LSTM), this system captures the relational structure between network flows — making it significantly more effective at detecting multi-stage attacks and lateral movement.

Presented at Alliance University Security Symposium 2025.

---

## Results

| Model | Accuracy | F1 Score | Precision | Recall |
|-------|----------|----------|-----------|--------|
| **GNN (This Work)** | **95.2%** | **0.953** | **0.961** | **0.946** |
| LSTM | 83.1% | 0.829 | 0.841 | 0.818 |
| Random Forest | 82.7% | 0.824 | 0.836 | 0.813 |
| SVM | 80.4% | 0.801 | 0.812 | 0.791 |
| Decision Tree | 78.9% | 0.786 | 0.794 | 0.779 |

**GNN outperforms all baselines by 12%+ in F1 score.**

---

## Why Graph Neural Networks for IDS?

Traditional IDS models treat each network flow as an independent data point. But real-world attacks are relational:

- An APT lateral movement attack involves a chain of flows: initial compromise → reconnaissance → privilege escalation → data staging
- A DDoS attack involves coordinated traffic from many sources to one target
- Port scanning involves a sequence of probes from one source to many destinations

A GNN models the **network of flows** as a graph, where nodes are hosts/flows and edges are connections. This captures attack patterns that a single-flow classifier completely misses.

```
Traditional ML:   [flow1] → label    [flow2] → label    (isolated)

GNN:              [flow1]──[flow2]──[flow3]   →   graph-level or node-level label
                                                  (relational, captures attack chain)
```

---

## Architecture

```
Raw NSL-KDD Data
      │
      ▼
┌─────────────────────┐
│  Graph Construction │
│                     │
│  Nodes: Network     │
│  flows/connections  │
│                     │
│  Edges: Shared IP,  │
│  temporal proximity,│
│  port correlation   │
└─────────┬───────────┘
          │
          ▼
┌─────────────────────┐
│  Graph Conv Layer 1 │  ← Aggregate neighbour features
│  (64 units, ReLU)   │
└─────────┬───────────┘
          │
          ▼
┌─────────────────────┐
│  Graph Conv Layer 2 │  ← Higher-order structural patterns
│  (128 units, ReLU)  │
└─────────┬───────────┘
          │
          ▼
┌─────────────────────┐
│  Graph Conv Layer 3 │
│  (64 units, ReLU)   │
└─────────┬───────────┘
          │
          ▼
┌─────────────────────┐
│  Global Mean Pool   │  ← Graph-level representation
└─────────┬───────────┘
          │
          ▼
┌─────────────────────┐
│  Classification     │  → Normal / DoS / Probe / R2L / U2R
│  Head (Softmax)     │
└─────────────────────┘
```

---

## Dataset

**NSL-KDD** — the standard benchmark for IDS research, addressing key limitations of the original KDD Cup 1999 dataset.

| Subset | Records | Attack Types |
|--------|---------|--------------|
| Training | 125,973 | DoS, Probe, R2L, U2R |
| Testing | 22,544 | Same + novel patterns |

Attack categories:
- **DoS** — Denial of Service (neptune, smurf, pod, teardrop...)
- **Probe** — Reconnaissance (satan, ipsweep, nmap, portsweep...)
- **R2L** — Remote to Local (guess_passwd, ftp_write, imap...)
- **U2R** — User to Root (buffer_overflow, loadmodule, rootkit...)

---

## Quick Start

```bash
git clone https://github.com/chythrabandaru/gnn-ids
cd gnn-ids
pip install -r requirements.txt

# Download NSL-KDD dataset
python scripts/download_data.py

# Preprocess and build graphs
python preprocess.py --dataset data/NSL-KDD/KDDTrain+.txt

# Train the GNN model
python train.py --epochs 100 --lr 0.001 --hidden 128

# Evaluate against test set
python evaluate.py --model checkpoints/best_model.pt --test data/NSL-KDD/KDDTest+.txt

# Run ablation studies
python ablation.py
```

---

## Ablation Studies

Ablation experiments confirm that each component contributes meaningfully:

| Configuration | F1 Score | Δ vs Full Model |
|--------------|----------|-----------------|
| Full GNN (3 layers) | 0.953 | — |
| 2 layers only | 0.931 | -2.2% |
| 1 layer only | 0.897 | -5.6% |
| No graph structure (MLP baseline) | 0.841 | -11.2% |
| No edge features | 0.918 | -3.5% |

---

## Research Presentation

This work was presented at **Alliance University Security Symposium 2025** and was the highest-performing security research project in a cohort of 120 students.

The presentation slides are available in `/docs/symposium-slides.pdf`.

---

## Author

**Chythra Bandaru** — Cybersecurity Professional | ML Security Researcher  
B.Tech Computer Science & Engineering (Cyber Security Specialisation), Alliance University  
[LinkedIn](https://linkedin.com/in/chythrabandaru) · [GitHub](https://github.com/chythrabandaru)
