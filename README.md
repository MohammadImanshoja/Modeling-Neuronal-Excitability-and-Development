(Master’s Thesis)

This repository contains simulation notebooks and analysis pipelines developed for a Master’s thesis project focused on modeling neuronal excitability and organizing neuronal firing phenotypes using modern unsupervised learning. The codebase combines **classical biophysical modeling** with **wavelet-based feature extraction** and **deep/unsupervised representations** to study excitability regimes and developmental physiology.

> **Status:** Work in progress. The research and analyses are continuing and the full pipeline is **not completed yet**.

---

## 📂 Repository Structure (Notebooks)

### 1) **HH_WST_VAE_Pipeline.ipynb**
Unsupervised phenotyping using simulated voltage traces from the Hodgkin–Huxley (HH) model.

- **Simulation:** HH model runs across randomized parameter ranges (e.g., conductances) to generate diverse firing behaviors (non-excitable, excitable, oscillatory).
- **Regime visualization:** S–K plane style regime exploration (geometric/biophysical parameterization).
- **Feature extraction:** Wavelet Scattering Transform (WST, `kymatio`) for translation-invariant representations of voltage traces.
- **Compression:** Deep latent representation (e.g., VAE/AE variants) to embed WST coefficients into a compact space.
- **Clustering + visualization:** HDBSCAN / KMeans and UMAP in embedding space to discover firing phenotypes without labels.

**References (from prior module notes):**
- https://www.pnas.org/doi/10.1073/pnas.1808552115

  
---

### 2) **mature_and_young_neuron.ipynb**
Comparative biophysical modeling to study intrinsic excitability differences between young and mature neurons.

- **Mechanisms:** Includes voltage-gated Na\_v, K\_v, Ca\_v, and Ca-activated K\_Ca channels.
- **Calcium dynamics:** Uses GHK flux + buffering/diffusion dynamics for intracellular Ca handling.
- **Comparison:** Simulates responses to current injection and compares AP shape/frequency and underlying ionic currents between age groups.

**References (from prior module notes):**
- https://pubmed.ncbi.nlm.nih.gov/1607940/

---

### 3) **WST_DANDI_DATASET.ipynb** 
End-to-end pipeline on real Patch-seq electrophysiology data from **DANDI:000008**, including WST extraction and alignment to manual electrophysiological feature tables.

What this notebook does (high level):

1. **Download raw NWB data**
   - Uses `dandi` to download `DANDI:000008/0.211014.0809` into the notebook environment.

2. **Extract voltage traces from NWB**
   - Iterates over NWB files, selects a specific sweep (e.g., `CurrentClampSeries020`).
   - Collects voltage traces across cells and records trace lengths.

3. **Length harmonization**
   - Finds the **most common trace length (mode)** and keeps only cells matching this length.
   - Exports a standardized CSV where:
     - rows = time samples  
     - columns = cells  
     - file example: `voltage___traces2345.csv`

4. **Wavelet Scattering Transform (WST)**
   - Computes WST with `kymatio.torch.Scattering1D` (GPU if available).
   - Extracts and saves:
     - full coefficients, S1, S2
     - metadata (frequencies / ordering)
   - Outputs include: `GPU_WST_full.npy`, `GPU_WST_S1.npy`, `GPU_WST_S2.npy`, and a combined `.npz`.

5. **Merge with manual electrophysiological features**
   - Loads the manual feature table: `m1_patchseq_ephys_features.csv`
   - Aligns common cells (via ID/key normalization inside the notebook) and builds an aligned table `ephys_common`.

6. **UMAP on ephys features + RNA family coloring**
   - Filters out incomplete rows (NaNs) and runs:
     - StandardScaler → UMAP(2D)
   - Merges RNA-family labels from metadata (e.g., `m1_patchseq_meta_data (1).csv`)
   - Plots UMAP colored by RNA family.

---

> GPU is optional but recommended for faster WST computation.

---

## ▶️ How to Run (General)

1. Open notebooks in Colab / Jupyter.
2. For the DANDI notebook:
   - Ensure you have enough disk space for dataset download.
   - Run the download cells first.
3. Make sure the following files exist at the expected paths (or update the paths in the notebook):
   - `m1_patchseq_ephys_features.csv`(used from https:// www.nature.com/articles/s 41586 020 2907 3)
   - `m1_patchseq_meta_data (1).csv` (RNA family labels) (used from https:// www.nature.com/articles/s 41586 020 2907 3)


---
## References :

-https://dandiarchive.org/dandiset/ 000008
-https:// www.nature.com/articles/s 41586 020 2907 3

## 🚧 Ongoing Work / Next Steps

This repository is part of an active thesis workflow and is still evolving. 

