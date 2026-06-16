# (Master’s Thesis) Data-Driven Phenotyping of Neuronal Dynamics

This repository contains the completed research pipeline for a Master’s thesis project. The research bridges classical biophysical modeling and modern data science to identify neuronal phenotypes.

The core innovation of this work is the **Dual-Dynamics Perspective**. We analyze neuronal activity not just as a static signal, but as a nested system: capturing **Fast-scale temporal dynamics** (millisecond-level spike morphology) and **Slow-scale stimulus-driven dynamics** (system evolution across increasing current injections, Sweeps 10–70).

**Status:** Research Completed.

---

## 📂 Repository Structure

### 1) HH_WST_VAE_Pipeline.ipynb
*Unsupervised phenotyping of simulated Hodgkin–Huxley (HH) dynamics.*
* **Foundational Theory:** Based on **Ori et al. ([2018](https://www.pnas.org/doi/epdf/10.1073/pnas.1808552115))**, *Cellular function given parametric variation in the Hodgkin and Huxley model of excitability*.
* **Logic:** Explores how cellular function is maintained despite parameter variability (S-K plane). 
* **Pipeline:** Uses Wavelet Scattering Transform (WST) and a CNN-based Variational Autoencoder (VAE) to cluster biophysical regimes (excitable, oscillatory, etc.) in an unsupervised latent space.

### 2) mature_and_young_neuron.ipynb
*Biophysical modeling of neuronal maturation.*
* **Foundational Theory:** Based on **Lockery & Spitzer ([1992](https://pubmed.ncbi.nlm.nih.gov/1607940/))**, *Reconstruction of Action Potential Development from Whole-Cell Currents*.
* **Logic:** Implements the specific ionic current changes (Na, K, Ca, K_Ca) and GHK flux dynamics described in the paper to simulate the developmental transition from "young" to "mature" action potential profiles.

### 3) WST_DANDI_DATASET_SINGLE_SWEEP.ipynb

Exploratory Wavelet Scattering analysis of real Patch-seq electrophysiology using a single representative sweep.

Purpose:
This notebook serves as the first real-data application of the pipeline on DANDI:000008. It is designed as an exploratory stage to test whether WST representations extracted from a single sweep already contain biologically meaningful structure.

Pipeline:
- Downloads NWB files from DANDI:000008.
- Extracts a fixed electrophysiology sweep (Sweep 20) from each cell.
- Filters cells to retain only those with the selected sweep and the most common trace length.
- Builds a standardized voltage matrix with rows as time samples and columns as cells.
- Applies the Wavelet Scattering Transform to the voltage traces and extracts S0, S1, and S2 coefficients.
- Saves the resulting scattering outputs and associated metadata for downstream analysis.

Representation Analysis:
- S1, S2, and combined S1+S2 features are flattened and standardized.
- UMAP is applied separately to S1, S2, and the combined representation to visualize the geometry of the learned signal space.
- RNA-family metadata are merged with the resulting embeddings to examine whether purely signal-derived representations align with known transcriptomic classes.

Biological Role:
This notebook provides the single-sweep baseline for the project. It tests how much neuronal identity can already be recovered from one stimulus condition before moving to the richer multi-sweep representation used in the main classification and distance-analysis pipelines.

### 4) CLASSIFICATION.ipynb (Core Pipeline)

Supervised neuronal classification from Wavelet Scattering representations.

Classification Strategy:
This notebook implements a two-stage comparison between classical feature-based machine learning and representation-based deep learning.

Baseline Model (Manual EPHYS Features):
- A supervised baseline is first built using manually extracted electrophysiological features.
- A KNN classifier is trained on the shared set of cells after metadata alignment and class filtering.
- This provides a conventional reference point for neuronal classification using scalar handcrafted descriptors.

WST-Based Deep Classification:
- For each cell, sweeps 10–70 are stacked to represent the neuron’s response across increasing current injections.
- The stacked responses are transformed with the Wavelet Scattering Transform (WST), producing structured time–frequency representations.
- These WST outputs are then used as inputs to deep classification models, including ResNet18, VGG16, and InceptionV3.
- In this framework, classification is performed directly from the learned dynamical representation rather than from manually engineered summary features.

Class Design:
- Because excitatory subclasses were underrepresented individually, they were merged into a single composite class (`CT_ET_IT`) to improve statistical stability.
- Inhibitory subclasses were kept separated (`Pvalb`, `Sst`, `Vip`, `Lamp5`) because they retained sufficient sample numbers for meaningful classification.
- Low-quality cells were handled separately during filtering and benchmarking.

Main Result:
The comparison shows that classification based on WST representations achieves higher accuracy than the baseline based on manually extracted electrophysiological features. This supports the central thesis that WST captures richer neuronal dynamics than traditional scalar features alone.

Interpretation:
The improvement is not only due to model complexity, but to the representation itself: WST preserves both local waveform structure and broader response patterns across stimulus conditions, allowing the classifier to exploit information that is lost in manual feature extraction.

### 5) DISTANCE_ANALYSIS.ipynb

Comparison of mathematical similarity in WST space versus biological similarity in manual electrophysiological feature space.

Purpose:
This notebook evaluates whether neurons that are close in Wavelet Scattering space are also close according to classical manually extracted electrophysiological features, and identifies cases where the two representations agree or diverge.

Data Preparation:
- Retains only cells with complete multi-sweep coverage across the selected current-injection range.
- Crops each sweep to a fixed analysis window and stacks sweeps into a unified multi-sweep representation per cell.
- Recomputes or loads per-cell WST outputs from the stacked traces.
- Aligns WST-derived cell identities with manually extracted electrophysiological feature tables using shared cell identifiers.
- Applies artifact filtering to remove traces with clipping, flat segments, or other non-informative signal structure.

Distance Construction:
- Computes pairwise distances between neurons in WST feature space.
- Computes pairwise distances between the same neurons in manual EPHYS feature space.
- Builds a joint pairwise comparison framework in which each neuron pair has both a WST distance and an EPHYS distance.

Quadrant-Based Interpretation:
Neuron pairs are grouped into four regimes:
- low WST / low EPHYS distance
- low WST / high EPHYS distance
- high WST / low EPHYS distance
- high WST / high EPHYS distance

This allows direct inspection of:
- pairs that are similar in both representations
- pairs that are consistently dissimilar
- pairs that appear close only in classical scalar feature space
- pairs that appear close only in WST space

Interpretability Outputs:
For representative neuron pairs, the notebook generates:
- stacked voltage-trace comparisons
- WST S1 and S2 visualizations
- EPHYS feature comparison plots
- report files summarizing pair identities, RNA-family annotations, and distance values

Main Role in the Project:
This notebook is the validation layer of the thesis. It moves beyond visualization and classification to test whether the WST captures meaningful neuronal dynamics that are not fully represented by traditional handcrafted electrophysiological summaries.

---
📊 External Data & References

To ensure reproducibility, this project uses external data and metadata from:

Scala, F.*, Kobak, D.*, Bernabucci, M., Bernaerts, Y., Cadwell, C. R., Castro, J. R., Hartmanis, L., Jiang, X., Laturnus, S., Miranda, E., Mulherkar, S., Tan, Z. H., Yao, Z., Zeng, H., Sandberg, R., Berens, P., & Tolias, A. S.
Phenotypic variation of transcriptomic cell types in mouse motor cortex.
Nature, 598, 144–150 (2021).
DOI: 10.1038/s41586-020-2907-3

The following files and resources were used:

| File / Resource                | Description                                                    | Source                                           |
| ------------------------------ | -------------------------------------------------------------- | ------------------------------------------------ |
| m1_patchseq_ephys_features.csv | Predefined/manual electrophysiological features                | Scala et al., Nature 2021 / BerensLab mini-atlas |
| m1_patchseq_meta_data.csv      | Metadata including transcriptomic cell-type/family annotations | Scala et al., Nature 2021 / BerensLab mini-atlas |
| DANDI:000008                   | Raw Patch-seq electrophysiological NWB recordings              | DANDI Archive, linked to Scala et al. dataset    |

In this thesis, the transcriptomic cell-type/family labels and predefined electrophysiological features from Scala et al. were used as biological reference annotations. These were compared with data-driven Wavelet Scattering Transform representations learned directly from neuronal voltage traces.

## ▶️ Setup
* **Hardware:** A GPU is essential for the WST computation on the multi-sweep stacks in Notebooks 4 and 5.
* **Key Libraries:** `kymatio` (WST), `pytorch` (VAE), `dandi`, `pynwb`, `umap-learn`, `hdbscan`.
