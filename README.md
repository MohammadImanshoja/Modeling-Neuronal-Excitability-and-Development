# (Master’s Thesis) Data-Driven Phenotyping of Neuronal Dynamics

This repository contains the completed research pipeline for a Master’s thesis project. The research bridges classical biophysical modeling and modern data science to identify neuronal phenotypes.

The core innovation of this work is the **Dual-Dynamics Perspective**. We analyze neuronal activity not just as a static signal, but as a nested system: capturing **Fast-scale temporal dynamics** (millisecond-level spike morphology) and **Slow-scale stimulus-driven dynamics** (system evolution across increasing current injections, Sweeps 10–70).

**Status:** Research Completed.

---

## 📂 Repository Structure

### 1) HH_WST_VAE_Pipeline.ipynb
*Unsupervised phenotyping of simulated Hodgkin–Huxley (HH) dynamics.*
* **Foundational Theory:** Based on **Ori et al. (2018)**, *Cellular function given parametric variation in the Hodgkin and Huxley model of excitability*.
* **Logic:** Explores how cellular function is maintained despite parameter variability (S-K plane). 
* **Pipeline:** Uses Wavelet Scattering Transform (WST) and a CNN-based Variational Autoencoder (VAE) to cluster biophysical regimes (excitable, oscillatory, etc.) in an unsupervised latent space.

### 2) mature_and_young_neuron.ipynb
*Biophysical modeling of neuronal maturation.*
* **Foundational Theory:** Based on **Lockery & Spitzer (1992)**, *Reconstruction of Action Potential Development from Whole-Cell Currents*.
* **Logic:** Implements the specific ionic current changes (Na, K, Ca, K_Ca) and GHK flux dynamics described in the paper to simulate the developmental transition from "young" to "mature" action potential profiles.

### 3) WST_DANDI_DATASET_SINGLE_SWEEP.ipynb
*Exploratory signal representation on real Patch-seq data.*
* **Data:** Downloads NWB files from DANDI:000008.
* **Process:** Performs length-harmonization (25,000 samples) and extracts WST coefficients for a single representative sweep (Sweep 20).
* **Visualization:** Maps mathematical "fingerprints" (S1/S2 coefficients) to transcriptomic RNA families (e.g., Pvalb, Sst, Vip).

### 4) CLASSIFICATION.ipynb (The Core Pipeline)
*High-resolution classification using the Dual-Dynamics Perspective.*
* **The Logic:** Stacks sweeps 10 through 70 for each cell. This allows the WST ($J=12, Q=20$) to capture the neuron’s entire input-output function, including frequency adaptation and rheobase shifts.
* **Feature Integration:** Merges deep mathematical features with transcriptomic identity for cell-type classification.

### 5) DISTANCE_ANALYSIS.ipynb
*Mathematical vs. Biological Similarity Analysis.*
* **Analysis:** Compares pairwise distances in the WST feature space against traditional manual EPHYS features.
* **Results:** Uses KDE contour mapping and quadrant analysis to show that the WST captures nuanced behavioral trajectories across current injections that traditional scalar features often miss.

---

## 📊 External Data & References

To ensure reproducibility, the following external files from **Gouwens et al. (2020)**, *Classification of neocortical neurons by morphological and physiological phenotypes* ([Nature Paper](https://www.nature.com/articles/s41586-020-2907-3)), must be present:

| File Name | Description | Source |
| :--- | :--- | :--- |
| `m1_patchseq_ephys_features.csv` | Manual electrophysiological features | Gouwens et al. (2020) |
| `m1_patchseq_meta_data (1).csv` | Transcriptomic (RNA family) labels | Gouwens et al. (2020) |
| **DANDI:000008** | Raw NWB Electrophysiology data | [DANDI Archive](https://dandiarchive.org/dandiset/000008) |

---

## 🧠 Methodological Summary: Stacking Sweeps 10–70
A central thesis of this work is that a neuron’s identity is encoded in how it **evolves** as a dynamical system. By stacking multiple sweeps, we provide the Wavelet Scattering Transform with:
1.  **Intra-sweep information:** AP kinetics and waveform shape.
2.  **Inter-sweep information:** The transition from sub-threshold behavior to repetitive firing and eventual saturation.

This holistic representation provides a significantly more robust "dynamic fingerprint" for identifying cell types compared to single-sweep analysis.

---

## ▶️ Setup
* **Hardware:** A GPU is essential for the WST computation on the multi-sweep stacks in Notebooks 4 and 5.
* **Key Libraries:** `kymatio` (WST), `pytorch` (VAE), `dandi`, `pynwb`, `umap-learn`, `hdbscan`.
