# SAMFEO-SD

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
    git clone https://github.com/your-username/SAMFEO-SD.git
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
```

**Before running**, you must either:

1.  Place your SamplingDesign output files in this directory.
2.  Or modify `main.py` to point to your actual data directory.

## 💻 Usage

To reproduce the results or run the design on the Eterna100 dataset using parallel processing:

```bash
python main.py --t 1 --k 10 --object pd --para --repeat 5 --path data/eterna/eterna100_v1.txt
```

## 📄 Input Formats

### Puzzle File (`-p`)

The input file should be a text file where each line contains an ID and a dot-bracket structure:

```text
Puzzle_1  ((((....))))
Puzzle_2  ((......))..
```

### SamplingDesign Output

The code expects weight files to contain a "Final Distribution" section. This distribution may define probabilities for single bases, pairs, or larger groups (mismatches):

```text
Final Distribution
(0,): A 0.99, C 0.01
(1, 10): AU 0.5, GC 0.5
(2, 5, 9): AGA 0.4, UCU 0.3
...
```

## 🔧 Arguments

| Flag | Description | Default |
| :--- | :--- | :--- |
| `-p`, `--path` | Path to the input file containing puzzle IDs and structures. | `''` |
| `--object`, `-o` | Objective function: `pd` (Probability Defect) or `ned` (Normalized Ensemble Defect). | `pd` |
| `--step` | Number of optimization steps (iterations). | `5000` |
| `--k` | Population size (beam width). | `10` |
| `--t` | Temperature for Boltzmann selection. | `1.0` |
| `--para` | Enable parallel processing (multiprocessing). | `False` |
| `--repeat` | Number of times to repeat the experiment (runs). | `1` |
| `--worker_count` | Number of CPU workers for parallel mode. | `10` |
| `--nosm` | **Disable** structured mutation (revert to random/traditional mutation). | `False` |
| `--nomfe` | Skip MFE (Minimum Free Energy) check (faster, less accurate). | `False` |

## 📂 Repository Structure

  * `main.py`: The core entry point containing the `samfeo` loop and `mutate_structured` logic.
  * `/utils`: Contains helper modules (`vienna.py`, `structure.py`) for energy calculations.

## 🔗 References

1.  **SAMFEO Approach (ISMB 2023)**:
    Zhou, T., Dai, N., Li, S., Ward, M., Mathews, D.H. and Huang, L., 2023. RNA design via structure-aware multifrontier ensemble optimization. *Bioinformatics*, 39(Supplement_1), pp.i563-i571.
    [https://github.com/shanry/SAMFEO](https://github.com/shanry/SAMFEO)

2.  **SamplingDesign**:
    SamplingDesign: RNA Design via Continuous Optimization with Coupled Variables and Monte-Carlo Sampling. Wei Yu Tang, Ning Dai, Tianshuo Zhou, David H. Mathews, and Liang Huang*.
    [https://github.com/weiyutang1010/SamplingDesign](https://github.com/weiyutang1010/SamplingDesign)
