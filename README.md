# (Master’s Thesis) Data-Driven Phenotyping of Neuronal Dynamics

This repository contains the full research pipeline for a Master’s thesis project. The work focuses on identifying neuronal phenotypes by combining computational modeling with a **Dual-Dynamics** analytical perspective on Patch-seq electrophysiology data.

**Key Innovation:** We treat neuronal activity as a nested dynamical system. By analyzing voltage traces across a range of current injections (Sweeps 10–70), we capture both the **fast temporal dynamics** (individual action potential morphology) and the **slow stimulus-response dynamics** (the F-I curve and adaptation properties).

**Status:** Completed.

---

## 📂 Repository Structure

### 1) HH_WST_VAE_Pipeline.ipynb
*Unsupervised phenotyping of simulated Hodgkin–Huxley (HH) dynamics.*
* **Simulation:** Generates a synthetic dataset of excitable, oscillatory, and non-excitable behaviors by sampling the HH parameter space.
* **Architecture:** Extracts Wavelet Scattering Transform (WST) coefficients and uses a CNN-based Variational Autoencoder (VAE) to learn a compact, latent representation of firing regimes.
* **Clustering:** Uses HDBSCAN to discover biophysical phenotypes without labels.

### 2) mature_and_young_neuron.ipynb
*Biophysical modeling of neuronal maturation.*
* **Mechanisms:** Implements Na_v, K_v, Ca_v, and K_Ca channels with GHK flux dynamics for calcium handling.
* **Analysis:** Quantifies the shift in intrinsic excitability and ionic current contributions as neurons transition from "young" to "mature" developmental states.

### 3) WST_DANDI_DATASET_SINGLE_SWEEP.ipynb
*Exploratory signal analysis on real Patch-seq data.*
* **Standardization:** Downloads data from DANDI:000008 and performs length-harmonization (25,000 samples).
* **High-Level Mapping:** Extracts WST coefficients for single sweeps (e.g., Sweep 20) and visualizes the mathematical "fingerprint" (S1 and S2 coefficients) alongside raw voltage traces for various transcriptomic RNA families.

### 4) CLASSIFICATION.ipynb (Core Analysis)
*The Primary Pipeline: Mapping Dual-Dynamics to RNA Identity.*
* **The Logic:** Stacks sweeps 10 through 70 to capture the system's evolution from low to high external current. 
* **Feature Engineering:** Computes high-resolution WST ($J=12, Q=20$) on these stacked traces. This captures temporal features and the dynamical shift caused by increased external input.
* **Mapping:** Integrates transcriptomic metadata to evaluate how well these "dual-dynamic" WST features separate biological cell types in UMAP space.

### 5) DISTANCE_ANALYSIS.ipynb
*Comparison of Mathematical vs. Biological Similarity.*
* **Feature Comparison:** Calculates pairwise distances between cells in the WST feature space versus the traditional manual EPHYS feature space.
* **Analysis:** Uses KDE contour maps and quadrant analysis to identify where WST representations reveal nuances in cell behavior that traditional manual features might overlook.

---

## 📊 External Data Sources

To run the DANDI notebooks (3, 4, and 5), you must download the following datasets provided by [Gouwens et al., Nature (2020)](https://www.nature.com/articles/s41586-020-2907-3).

**Ensure these files exist in your working directory or update the paths within the notebooks:**

1.  **`m1_patchseq_ephys_features.csv`**: Contains manual electrophysiological features.
2.  **`m1_patchseq_meta_data (1).csv`**: Contains the RNA family labels (transcriptomic ground truth).
3.  **DANDI Dataset**: The notebooks use the `dandi` CLI to download NWB files from [DANDI:000008](https://dandiarchive.org/dandiset/000008).

---

## 🧠 Methodological Note: Why Multi-Sweep?
Standard analysis often focuses on a single "hero" sweep (e.g., the first sweep with a spike). This project argues that a neuron's identity is found in its **transition** across stimulus strengths. By stacking sweeps 20 through 70, the Scattering Transform encodes:
1.  **Fast-scale info:** Spike width, AHP, and kinetics.
2.  **Slow-scale info:** Firing frequency adaptation and rheobase transitions.

This provides a more robust "dynamic fingerprint" for cell-type classification.

---

## ▶️ Setup
* **Hardware:** A GPU is strongly recommended for notebooks 3, 4, and 5 due to the computational intensity of the Wavelet Scattering Transform on multi-sweep stacks.
* **Libraries:** `kymatio`, `pytorch`, `dandi`, `pynwb`, `umap-learn`, `hdbscan`, `pandas`, `numpy`.
