# Graph Search and Attribute Clustering on the Facebook Social-Circles Network

**Depth-First Search (DFS) and K-Means on the Stanford SNAP *Social Circles: Facebook* dataset**

Group project for **Artificial Intelligence (ICE-4107)**, Department of ICT, Bangladesh University of Professionals (BUP).

[![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/<your-username>/<your-repo>/blob/main/Facebook_DFS_KMeans_Project_Report_Poster.ipynb)

---

## Table of Contents

1. [Overview](#overview)
2. [Team](#team)
3. [Dataset](#dataset)
4. [Methods](#methods)
5. [Key Results](#key-results)
6. [Repository Structure](#repository-structure)
7. [How to Run](#how-to-run)
8. [Generated Outputs](#generated-outputs)
9. [Reproducibility](#reproducibility)
10. [Limitations](#limitations)
11. [References](#references)

---

## Overview

A social network has two kinds of information: **structure** (who is friends with whom) and **attributes** (what users share about themselves). This project asks two questions:

| Question | Algorithm |
|---|---|
| Can a search visit every user, expose cycles and depth, and be verified as correct? | **Depth-First Search (DFS)** |
| Do profile attributes recover the friend circles users create themselves? | **K-Means clustering** |

Both algorithms are implemented **from scratch** and checked against NetworkX / scikit-learn. A single Jupyter notebook runs the full pipeline: data audit, cleaning, EDA, feature engineering, the two algorithms, evaluation, timing, and export of poster figures and a report.

## Team

| Name |
|---|
| Abeeda Ummey Haafsa |
| Fahim Ahmed |
| Akhear Iram |

**Course instructor:** Jasiya Fairiz Raisa, Lecturer, Dept. of ICT, BUP
**Submission date:** 12 November 2026

## Dataset

[Stanford SNAP, Social Circles: Facebook (ego-Facebook)](https://snap.stanford.edu/data/ego-Facebook.html)

| File | Used for |
|---|---|
| `facebook_combined.txt.gz` (4,039 nodes, 88,234 undirected edges) | DFS and graph analysis |
| `facebook.tar.gz` (10 ego networks: `.edges`, `.feat`, `.featnames`, `.egofeat`, `.circles`) | K-Means (ego network 0: 347 users, 224 binary attributes, 24 circles) |

The data are **not stored in this repository**. The notebook downloads them from SNAP automatically. The data are public and anonymised; no attempt was made to re-identify users.

## Methods

**Track A: Graph search (whole combined network)**
1. Audit the edge list (self-loops, duplicates, ID continuity), clean it, build a sorted adjacency list.
2. Graph EDA: degree distribution, clustering, k-cores, components, sampled diameter, comparison with SNAP's published statistics.
3. Iterative DFS forest from scratch, recording parent, depth, discovery and finishing times.
4. Verification with 8 independent checks, runtime over 30 repeats, memory use, O(V+E) scaling test, and root / neighbour-order variability.

**Track B: Attribute clustering (ego network 0)**
1. Audit and clean attributes (constant and rare attributes removed; duplicate profiles kept).
2. Feature engineering: category counts, structural features, and four representations (binary, standardised, PCA, engineered).
3. K-Means from scratch (Lloyd + k-means++), validated on synthetic blobs and against scikit-learn.
4. K sweep (2 to 12) with silhouette, Davies-Bouldin, Calinski-Harabasz, elbow, and seed stability. K is chosen by a rule fixed in advance.
5. External evaluation against the circles: ARI, NMI, purity, Hungarian-matched accuracy, majority baseline, and a 500-shuffle permutation test. Louvain communities are used as a structural reference.

## Key Results

Results from the reported Colab run (seed 42).

**DFS**

| Metric | Result |
|---|---|
| Coverage | 100% of 4,039 nodes (1 root, 1 connected component) |
| Verification | 8 / 8 checks passed; 100% of edges satisfy the ancestor-descendant property |
| Runtime (own DFS) | 45.07 ± 11.88 ms (NetworkX reference: 122.79 ± 49.08 ms) |
| Extra memory | about 1.07 MB |
| Scaling with V + E | R² = 0.938 |
| DFS tree height | 1,858 (sampled diameter lower bound: 8), so DFS is complete but **not optimal** |

**K-Means (ego network 0)**

| Metric | Result |
|---|---|
| Selected K | 2 (cluster sizes 145 and 202) |
| Silhouette | 0.173 (no substantial structure) |
| Davies-Bouldin / Calinski-Harabasz | 2.712 / 43.3 |
| Seed stability (pairwise ARI) | 0.876 |
| ARI / NMI vs circles | 0.017 / 0.110 (above chance, small) |
| Matched accuracy vs circles | 31.5%, below the 43.0% majority baseline (shuffled-label mean 28.5%) |
| Louvain communities vs circles (NMI) | 0.394 |

**Takeaway:** DFS is complete, verified and fast, but it does not find short paths. Profile attributes alone are a weak summary of social circles; friendship structure explains them better.

Total notebook runtime was about 100 seconds on a 2-core Colab machine.

## Repository Structure

```
.
├── Facebook_DFS_KMeans_Project_Report_Poster.ipynb   # full pipeline (run this)
├── README.md
└── (optional) report/, slides/, results/             # add your final documents here
```

Running the notebook creates these folders next to it:

```
facebook_project_data/       # downloaded SNAP files
facebook_project_outputs/
├── figures/                 # poster-ready PNGs (300 dpi)
├── tables/                  # all result tables as CSV
├── results.json
├── experiment_configuration.json
├── figure_index.csv
├── poster_content.md
└── Facebook_DFS_KMeans_Report.docx
```

## How to Run

### Option 1: Google Colab (recommended)
1. Click the **Open in Colab** badge above (after replacing `<your-username>` and `<your-repo>`), or upload the notebook to Colab.
2. Edit `PROJECT_INFO` in the setup cell (names, IDs, instructor).
3. Choose **Runtime > Run all**. The final cell downloads a ZIP of all outputs.

### Option 2: Local Jupyter

```bash
git clone https://github.com/<your-username>/<your-repo>.git
cd <your-repo>
pip install numpy pandas networkx scikit-learn scipy matplotlib seaborn python-docx jupyter
jupyter notebook Facebook_DFS_KMeans_Project_Report_Poster.ipynb
```

Requires Python 3.10+ and an internet connection for the first run. If `snap.stanford.edu` is blocked, download both files manually into `facebook_project_data/` and re-run; existing files are not downloaded again.

## Generated Outputs

| Output | Purpose |
|---|---|
| `figures/fig00` to `fig13` | Poster and report figures (pipeline, graph EDA, DFS analysis and scaling, K-Means selection, PCA map, silhouette, cluster profiles, runtime) |
| `tables/*.csv` | Every table in the report |
| `poster_content.md` | Auto-written poster text with real numbers |
| `Facebook_DFS_KMeans_Report.docx` | Formal report filled from the run |
| `results.json` | All scalar results in one file |

## Reproducibility

- Fixed random seed (`42`) and all settings stored in a single `CFG` dictionary.
- Package versions and hardware are saved to `experiment_configuration.json`.
- Reported environment: Python 3.13.18, NetworkX 3.7, scikit-learn 1.6.1, NumPy 2.4.6, pandas 2.2.3.
- Timings depend on hardware and vary between runs; correctness and clustering results do not.

## Limitations

- K-Means uses one ego network (about 350 users); results may not generalise to the other nine.
- Attributes are anonymised, binary and sparse; Euclidean K-Means assumes compact, round clusters.
- Circles overlap, so each user is reduced to one label (first-listed or largest circle). Agreement scores are lower bounds.
- Silhouette values from different feature spaces are not comparable.
- DFS trees are not unique; tree height depends on the root and neighbour order.
- DFS and K-Means use different data, so their runtimes are not a speed comparison.

## References

1. J. McAuley and J. Leskovec, "Learning to discover social circles in ego networks," NIPS, 2012.
2. J. Leskovec and A. Krevl, "SNAP Datasets: Stanford large network dataset collection," http://snap.stanford.edu/data, 2014.
3. R. Tarjan, "Depth-first search and linear graph algorithms," *SIAM J. Comput.*, 1(2):146-160, 1972.
4. S. P. Lloyd, "Least squares quantization in PCM," *IEEE Trans. Inf. Theory*, 28(2):129-137, 1982.
5. D. Arthur and S. Vassilvitskii, "k-means++: The advantages of careful seeding," SODA, 2007.
6. P. J. Rousseeuw, "Silhouettes," *J. Comput. Appl. Math.*, 20:53-65, 1987.
7. V. D. Blondel et al., "Fast unfolding of communities in large networks," *J. Stat. Mech.*, 2008.
8. F. Pedregosa et al., "Scikit-learn: Machine learning in Python," *JMLR*, 12:2825-2830, 2011.
9. A. Hagberg, D. Schult and P. Swart, "Exploring network structure, dynamics, and function using NetworkX," SciPy, 2008.

The full reference list is in the project report.

## Acknowledgements

Thanks to our course instructor, Jasiya Fairiz Raisa, and to the Stanford SNAP group for making the dataset public.
