# Master's Thesis — Data-Driven Phenotyping of Neuronal Dynamics

This repository contains the complete pipeline for my Master's thesis on data-driven
phenotyping of neuronal electrophysiological dynamics.

The central idea is to analyse neuronal voltage traces not as static signals but as
**dynamical responses**, at two levels: the fast temporal structure of individual action
potentials, and the slower change in a neuron's response across increasing current
injections. The main goal is to test whether **Wavelet Scattering Transform (WST)**
representations capture biologically meaningful information from voltage traces, and to
compare them against traditional, manually extracted electrophysiological features.

All real-data analyses use the public Patch-seq dataset of mouse primary motor cortex from
**Scala et al. (2021, *Nature*)**. See [Data attribution](#data-attribution--how-to-cite).

---

## Repository structure

### 1) `HH_WST_VAE_Pipeline.ipynb`
The first, theoretical part of the project. Simulated Hodgkin–Huxley dynamics are used to
test whether WST representations can separate different biophysical regimes of neuronal
activity. WST is applied to simulated traces, and a CNN-based Variational Autoencoder is
used to explore the structure of the learned latent space. Motivated by Ori et al. (2018),
who show that different parameter combinations can still yield functional neuronal
behaviour.

### 2) `mature_and_young_neuron.ipynb`
Models the developmental transition between young and mature neuronal action potentials,
based on Lockery & Spitzer (1992). It implements changes in ionic currents (Na, K, Ca, KCa)
to show how ion-channel dynamics reshape the voltage response of a neuron.

### 3) `WST_DANDI_DATASET_SINGLE_SWEEP.ipynb`
The first application of the pipeline to real Patch-seq data. One representative sweep is
extracted per cell from the raw NWB recordings (DANDI:000008), and WST is applied directly
to the voltage trace. The resulting embeddings are compared against the transcriptomic
cell-type / family labels to test whether a single-sweep WST representation already carries
biological structure.

- Raw NWB voltage traces from DANDI:000008 → WST analysis.
- Metadata from Scala et al. (2021) → transcriptomic family labels only.
- Labels are used to interpret whether signal-derived WST embeddings align with known
  transcriptomic classes.

### 4) `Classification_WST.ipynb`
The main supervised-classification part of the thesis: comparing classical, feature-based
machine learning against WST-based deep learning for predicting transcriptomic **RNA family**.

- **Classical baseline** — the predefined electrophysiological features from Scala et al.
  (manually extracted scalar descriptors), used as input to **KNN**, **Random Forest**, and
  **SVM**.
- **WST-based deep learning** — for each cell, sweeps 10–70 are stacked to represent the
  neuron's response across increasing current injections, and WST is applied to the stacked
  trace. The first-order scattering output (S1) is used as input to **ResNet18**, **VGG16**,
  and **Inception V3**.

Excitatory subclasses are merged into a single class, **CT_ET_IT**, while the inhibitory
classes **Pvalb, Sst, Vip, and Lamp5** are kept separate. Crucially, **all six models are
trained and evaluated on one shared, stratified split** (identical cross-validation folds
and identical held-out test cells), so any difference in performance reflects the input
representation and classifier — not differences in the selected neurons or data split.

### 5) `DISTANCE_ANALYSIS.ipynb`
Compares neuron-to-neuron similarity in two spaces: distances computed from WST
representations of stacked voltage traces, versus distances computed from the predefined
manual electrophysiological features. The aim is to see whether neurons that are close in
WST space are also close in classical feature space, and to surface cases where the two
representations agree or disagree — a validation step examining whether WST captures
neuronal dynamics beyond handcrafted features.

---

## Key results (Notebook 4)

Final-test performance for RNA-family classification on the shared test set (231 cells),
with 3-fold cross-validation accuracy on the shared CV pool:

| Model          | Input representation        | Test accuracy | CV accuracy (mean ± SD) |
|----------------|-----------------------------|:-------------:|:-----------------------:|
| ResNet18       | WST S1 (deep learning)      | 0.952         | 0.940 ± 0.011           |
| SVM            | Predefined ephys features   | 0.948         | 0.920 ± 0.033           |
| Random Forest  | Predefined ephys features   | 0.935         | 0.909 ± 0.027           |
| Inception V3   | WST S1 (deep learning)      | 0.931         | 0.933 ± 0.013           |
| VGG16          | WST S1 (deep learning)      | 0.922         | 0.919 ± 0.007           |
| KNN            | Predefined ephys features   | 0.892         | 0.843 ± 0.013           |

**Takeaway.** The best WST-based deep network (ResNet18) and the best feature-based
classifier (SVM) perform comparably, and the top models overlap within their
cross-validation error bars. This suggests that a data-driven WST representation learned
directly from voltage dynamics is competitive with carefully handcrafted electrophysiological
features for transcriptomic-family prediction, while KNN remains a clearly weaker baseline.

---

## Data attribution

This project uses public Patch-seq data and metadata from the Berens/Tolias mini-atlas. If
you use this repository, please cite the original study and data sources:

**Primary publication**
> Scala, F.\*, Kobak, D.\*, Bernabucci, M., Bernaerts, Y., Cadwell, C. R., Castro, J. R.,
> Hartmanis, L., Jiang, X., Laturnus, S., Miranda, E., Mulherkar, S., Tan, Z. H., Yao, Z.,
> Zeng, H., Sandberg, R., Berens, P., & Tolias, A. S. (2021). *Phenotypic variation of
> transcriptomic cell types in mouse motor cortex.* **Nature, 598(7879), 144–150.**
> https://doi.org/10.1038/s41586-020-2907-3

**Preprocessed data and analysis code (CSV files used here)**
> Berens Lab — mini-atlas repository: https://github.com/berenslab/mini-atlas

**Raw electrophysiological recordings (NWB, used for the WST pipeline)**
> Scala, F., et al. *Patch-seq recordings from mouse primary motor cortex.* DANDI Archive,
> dandiset 000008. https://dandiarchive.org/dandiset/000008

### How the external files were used
- `m1_patchseq_meta_data.csv` — transcriptomic cell-type / family annotations, used as
  classification labels and for biological interpretation.
- `m1_patchseq_ephys_features.csv` — predefined / manually extracted electrophysiological
  features, used for the classical machine-learning baselines and the distance analysis.
- Raw NWB traces from **DANDI:000008** — used to compute WST representations directly from
  neuronal activity.

In short: the metadata and manual-feature CSVs serve as biological labels and classical
baselines, while the raw voltage traces drive the Wavelet Scattering representations.

---

## Setup

A GPU is recommended for the Wavelet Scattering and deep-learning notebooks.

Main libraries:

```
kymatio  torch  torchvision  scikit-learn  numpy  pandas
matplotlib  seaborn  umap-learn  pynwb  dandi
```

Quick install (Colab / fresh environment):

```bash
pip install kymatio torch torchvision scikit-learn numpy pandas \
            matplotlib seaborn umap-learn pynwb dandi
```

---

## Data availability

Large external data files are not redistributed in this repository. To reproduce the
analyses, download the required data from the original public sources:

- Preprocessed metadata and feature CSVs — https://github.com/berenslab/mini-atlas
- Raw NWB electrophysiology — https://dandiarchive.org/dandiset/000008

---

## References

1. Scala, F., Kobak, D., Bernabucci, M., et al. (2021). Phenotypic variation of
   transcriptomic cell types in mouse motor cortex. *Nature*, 598(7879), 144–150.
   https://doi.org/10.1038/s41586-020-2907-3
2. Ori, H., Marder, E., & Marom, S. (2018). Cellular function given parametric variation in
   the Hodgkin–Huxley model of excitability. *PNAS*.
   https://www.pnas.org/doi/10.1073/pnas.1808552115
3. Lockery, S. R., & Spitzer, N. C. (1992). Reconstruction of action potential development
   from whole-cell currents. https://pubmed.ncbi.nlm.nih.gov/1607940/

**Methods references**
4. Mallat, S. (2012). Group invariant scattering. *Communications on Pure and Applied
   Mathematics.* (Wavelet Scattering Transform.)
5. Andén, J., & Mallat, S. (2014). Deep scattering spectrum. *IEEE Transactions on Signal
   Processing.*
6. Andreux, M., et al. (2020). Kymatio: Scattering transforms in Python. *JMLR.*
7. He, K., Zhang, X., Ren, S., & Sun, J. (2016). Deep residual learning for image
   recognition. *CVPR.* (ResNet.)
8. Simonyan, K., & Zisserman, A. (2015). Very deep convolutional networks for large-scale
   image recognition. *ICLR.* (VGG.)
9. Szegedy, C., et al. (2016). Rethinking the Inception architecture for computer vision.
   *CVPR.* (Inception V3.)

