# SAMFEO-SD: Weighted Sampling for RNA Design

**An enhanced RNA sequence design framework integrating SAMFEO optimization with SamplingDesign probability weights.**

## 🧬 Overview

This repository implements a hybrid approach to RNA sequence design. It builds upon the **SAMFEO** (Self-Adaptive Multi-objective Feature Extraction and Optimization) framework but fundamentally alters the mutation operator.

In standard evolutionary algorithms, nucleotide mutation is typically stochastic (random). This project integrates the **SamplingDesign** algorithm to extract structural probability weights. Instead of mutating nucleotides randomly, the algorithm samples new bases according to these calculated weights, guiding the search process toward more thermodynamically favorable and structurally sound conformations.

## 🚀 Key Innovation

### The Problem

Original approaches often utilize a **Uniform Mutation Strategy**:
$$P(A) = P(U) = P(G) = P(C) = 0.25$$
or simply swap base pairs uniformly. This "blind" mutation treats positions independently or as simple pairs, often disrupting complex stable substructures (like specific mismatch loops or triloops) and leading to slow convergence.

### The Solution: Structured Motif Mutation

We utilize **SamplingDesign** to calculate specific joint probability weights ($W$) for coupled structural elements. The mutation operator is not limited to single nucleotides; it simultaneously updates entire motifs based on the target secondary structure:

  * **Canonical Pairs:** $P(N_i, N_j) \propto W_{pair}(N_i, N_j)$
  * **Mismatches & Tri-mismatches:** $P(N_i, N_j, ...) \propto W_{motif}(N_i, N_j, ...)$
  * **Single Bases:** $P(N_i) \propto W_{single}(N_i)$

By sampling these grouped values directly from the SamplingDesign output, the algorithm preserves critical structural correlations that random mutation would otherwise destroy.

## 📊 Performance

We evaluated SAMFEO-SD on the **Eterna100 benchmark** using Boltzmann probability as the objective function. Across 5 independent runs:

| Metric | Score |
| :--- | :--- |
| **Average NED** (Normalized Ensemble Defect) | **0.043** |
| **Average PD** (Probability Defect) | **0.580** |

## 📦 Installation

1.  **Clone the repository**

    ```bash
    git clone [https://github.com/your-username/SAMFEO-SD.git](https://github.com/your-username/SAMFEO-SD.git)
    cd SAMFEO-SD
    ```

2.  **Install Python Dependencies**

    ```bash
    pip install numpy pandas
    ```

3.  **External Dependencies**
    This project relies on RNA folding libraries (likely ViennaRNA) found in the `utils/` directory. Ensure your environment is set up to support these calls.

## ⚙️ Configuration (Important)

**Weight File Path:**
The integration with SamplingDesign relies on pre-calculated weight files.
Currently, the code in `main.py` (function `samfeo`) points to a specific directory structure:

```python
path = f"D:/ether/results/eterna100_targeted_time/{id}.txt"
