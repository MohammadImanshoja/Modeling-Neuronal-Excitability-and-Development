# Master's Thesis — Data-Driven Phenotyping of Neuronal Dynamics

This repository contains the notebooks for my Master's thesis. The project compares two ways
of representing neurons from their intracellular voltage recordings — a data-driven **Wavelet
Scattering Transform (WST)** and traditional, manually extracted electrophysiological features
— using both biophysical simulations and real Patch-seq recordings from mouse primary motor
cortex (Scala et al., 2021).

Each notebook is self-contained. A short description of what each one does is given below. The
real-data notebooks use the public dataset described in
[Data attribution](#data-attribution--how-to-cite).

---

## Notebooks

### 1) `HH_WST_VAE_Pipeline.ipynb`
A simulation study. It simulates Hodgkin–Huxley neurons under random parametric variation
(different conductance combinations that still produce functional spiking), applies the WST to
the simulated voltage traces, and uses a CNN-based Variational Autoencoder followed by
clustering to explore the structure of the resulting latent space. Motivated by Ori et al.
(2018).

### 2) `mature_and_young_neuron.ipynb`
A biophysical simulation of how the action potential differs between young and mature neurons,
implemented by varying ionic currents (Na, K, Ca, KCa) between two parameter sets. It
illustrates how changes in ion-channel dynamics reshape the voltage response. Based on
Lockery & Spitzer (1992).

### 3) `WST_DANDI_DATASET_SINGLE_SWEEP.ipynb`
The first application to real Patch-seq data (DANDI:000008). A **single representative sweep**
is extracted per cell, the WST is applied to that one trace, and cells are matched to their
transcriptomic annotations through a shared identifier. The WST features are then projected
with UMAP and overlaid with RNA-family labels to check whether even a single-sweep
representation already reflects transcriptomic structure.

### 4) `Classification_WST.ipynb`
The main supervised analysis: predicting transcriptomic **RNA family** from two representations
of the same neurons, compared under one shared, stratified data split.

- **Classical baseline** — the predefined electrophysiological features (manually extracted
  scalar descriptors) are used as input to **KNN**, **Random Forest**, and **SVM**.
- **WST-based deep learning** — the first-order scattering output (S1) is used as input to
  **ResNet18**, **VGG16**, and **Inception V3**.

Excitatory subclasses are merged into a single class (**CT_ET_IT**), while the inhibitory
classes **Pvalb, Sst, Vip, and Lamp5** are kept separate.

**How the WST input is built (multi-sweep stacking).** Standard electrophysiology compresses
each neuron into scalar descriptors (rheobase, spike width, adaptation index, and so on). In
this notebook the voltage trace is instead treated as a **dynamical response** and described at
two timescales: **fast** — the temporal structure of individual action potentials within a
current step (rise, repolarisation, after-hyperpolarisation, intra-step firing pattern); and
**slow** — how the neuron's response evolves as the injected current grows, spanning
sub-threshold behaviour, spiking onset (rheobase), and the change in firing rate and adaptation
at progressively stronger drive (the input–output, or F–I, behaviour).

To expose both timescales to a single representation, sweeps 10–70 — recorded as current-clamp
steps of increasing amplitude — are **stacked into one extended trace per neuron** before the
WST is applied. A single sweep is only one operating point; stacking the series captures the
*trajectory* of responses across stimulus levels, so the WST representation encodes both the
fine spike waveform and the coarse evolution of excitability. This is the dynamical signature
that the handcrafted features summarise as scalars, and that the deep models instead receive
directly as the scattering output.

### 5) `DISTANCE_ANALYSIS.ipynb`
Computes pairwise distances between neurons in two representations — the stacked-sweep WST
representation (the same one used in notebook 4) and the predefined electrophysiological
features — and compares them using kernel density estimation and low-dimensional
visualisation. The aim is to see whether neurons that are close in WST space are also close in
classical feature space, and to surface where the two representations agree or disagree.

---

## Data attribution 

This project uses public Patch-seq data and metadata from the Berens/Tolias mini-atlas. If you
use this repository, please cite the original study and data sources:

**Primary publication**
> Scala, F.\*, Kobak, D.\*, Bernabucci, M., Bernaerts, Y., Cadwell, C. R., Castro, J. R.,
> Hartmanis, L., Jiang, X., Laturnus, S., Miranda, E., Mulherkar, S., Tan, Z. H., Yao, Z.,
> Zeng, H., Sandberg, R., Berens, P., & Tolias, A. S. (2021). *Phenotypic variation of
> transcriptomic cell types in mouse motor cortex.* **Nature, 598(7879), 144–150.**
> https://doi.org/10.1038/s41586-020-2907-3

**Preprocessed data and analysis code (CSV files used here)**
> Berens Lab — mini-atlas repository: https://github.com/berenslab/mini-atlas

**Raw electrophysiological recordings (NWB, used for the WST pipeline)**
> Scala, F., Kobak, D., Bernabucci, M., Bernaerts, Y., Cadwell, C. R., Castro, J. R.,
> Hartmanis, L., Jiang, X., Laturnus, S., Miranda, E., Mulherkar, S., Tan, Z. H., Yao, Z.,
> Zeng, H., Sandberg, R., Berens, P., & Tolias, A. S. (2021). *Phenotypic variation within
> and across transcriptomic cell types in mouse motor cortex* (Version 0.211014.0809)
> [Data set]. DANDI Archive. https://doi.org/10.48324/dandi.000008/0.211014.0809
> (RRID:SCR_017571). Licensed under CC BY 4.0.

### How the external files were used
- `m1_patchseq_meta_data.csv` — transcriptomic cell-type / family annotations, used as
  classification labels and for biological interpretation.
- `m1_patchseq_ephys_features.csv` — predefined / manually extracted electrophysiological
  features, used for the classical machine-learning baselines and the distance analysis.
- Raw NWB traces from **DANDI:000008** — used to compute WST representations directly from
  neuronal activity.

---

## Setup

A GPU is recommended for the Wavelet Scattering and deep-learning notebooks.

Main libraries:

```
kymatio  torch  torchvision  scikit-learn  numpy  pandas
matplotlib  seaborn  umap-learn  pynwb  dandi  scipy
```

Quick install (Colab / fresh environment):

```bash
pip install kymatio torch torchvision scikit-learn numpy pandas \
            matplotlib seaborn umap-learn pynwb dandi scipy
```

---

## Data availability

Large external data files are not redistributed in this repository. To reproduce the analyses,
download the required data from the original public sources:

- Preprocessed metadata and feature CSVs — https://github.com/berenslab/mini-atlas
- Raw NWB electrophysiology — https://dandiarchive.org/dandiset/000008

---

## References

1. Scala, F., Kobak, D., Bernabucci, M., et al. (2021). Phenotypic variation of transcriptomic
   cell types in mouse motor cortex. *Nature*, 598(7879), 144–150.
   https://doi.org/10.1038/s41586-020-2907-3
2. Ori, H., Marder, E., & Marom, S. (2018). Cellular function given parametric variation in the
   Hodgkin–Huxley model of excitability. *PNAS*.
   https://www.pnas.org/doi/10.1073/pnas.1808552115
3. Lockery, S. R., & Spitzer, N. C. (1992). Reconstruction of action potential development from
   whole-cell currents. https://pubmed.ncbi.nlm.nih.gov/1607940/

**Methods references**
4. Mallat, S. (2012). Group invariant scattering. *Communications on Pure and Applied
   Mathematics.* (Wavelet Scattering Transform.)
5. Andén, J., & Mallat, S. (2014). Deep scattering spectrum. *IEEE Transactions on Signal
   Processing.*
6. Andreux, M., et al. (2020). Kymatio: Scattering transforms in Python. *JMLR.*
7. He, K., Zhang, X., Ren, S., & Sun, J. (2016). Deep residual learning for image recognition.
   *CVPR.* (ResNet.)
8. Simonyan, K., & Zisserman, A. (2015). Very deep convolutional networks for large-scale image
   recognition. *ICLR.* (VGG.)
9. Szegedy, C., et al. (2016). Rethinking the Inception architecture for computer vision.
   *CVPR.* (Inception V3.)
