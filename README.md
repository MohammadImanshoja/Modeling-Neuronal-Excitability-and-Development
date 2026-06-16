# Master’s Thesis: Data-Driven Phenotyping of Neuronal Dynamics

This repository contains the completed pipeline for my Master’s thesis project on data-driven phenotyping of neuronal electrophysiological dynamics.

The main idea of this project is to analyze neuronal voltage traces not only as static signals, but as dynamical responses. I focused on two levels of dynamics: the fast temporal structure of action potentials and the slower change in neuronal response across increasing current injections. The main goal was to test whether Wavelet Scattering Transform representations can capture biologically meaningful information from voltage traces and compare this with traditional manually extracted electrophysiological features.

---

## 1) HH_WST_VAE_Pipeline.ipynb

This notebook is the first theoretical part of the project. It uses simulated Hodgkin–Huxley dynamics to test whether Wavelet Scattering representations can capture different biophysical regimes of neuronal activity.

The notebook is based on the idea that different combinations of model parameters can still produce functional neuronal behavior. WST is applied to simulated traces, and a CNN-based Variational Autoencoder is used to explore the structure of the learned latent space.

Reference:

Ori et al. 2018
Cellular function given parametric variation in the Hodgkin and Huxley model of excitability
https://www.pnas.org/doi/epdf/10.1073/pnas.1808552115

---

## 2) mature_and_young_neuron.ipynb

This notebook models the developmental transition between young and mature neuronal action potentials.

It is based on Lockery and Spitzer 1992 and implements changes in ionic currents such as Na, K, Ca, and KCa currents. The aim is to show how changes in ion-channel dynamics can reshape the voltage response of a neuron.

Reference:

Lockery and Spitzer 1992
Reconstruction of Action Potential Development from Whole-Cell Currents
https://pubmed.ncbi.nlm.nih.gov/1607940/

---

## 3) WST_DANDI_DATASET_SINGLE_SWEEP.ipynb

This notebook is the first application of the pipeline to real Patch-seq electrophysiological data.

The raw NWB electrophysiological recordings are taken from DANDI:000008. In this notebook, one representative sweep is extracted from each cell, and Wavelet Scattering Transform is applied directly to the voltage traces.

The goal is to test whether a single-sweep WST representation already contains biological structure. The WST embeddings are then compared with the transcriptomic cell-type/family labels provided in the metadata from Scala et al.

In this notebook:

* Raw NWB voltage traces from DANDI:000008 are used for the Wavelet Scattering analysis.
* Metadata from Scala et al. are used only for transcriptomic cell-type/family labels.
* The labels are used to interpret whether the signal-derived WST embeddings align with known transcriptomic classes.

---

## 4) Classification WST.ipynb

This notebook is the main supervised classification part of the thesis.

The goal is to compare classical feature-based machine learning with WST-based deep learning.

For the classical machine learning baseline, I used the predefined electrophysiological features from Scala et al. These are manually extracted scalar features. They were used as input for:

* KNN
* Random Forest
* SVM

For the WST-based models, I did not use the manual electrophysiological features as input. Instead, I used the raw voltage traces from DANDI:000008. For each cell, sweeps 10–70 were stacked to represent the response of the neuron across increasing current injections. Then, Wavelet Scattering Transform was applied to these stacked traces.

The WST representations were used as input for:

* ResNet18
* VGG16
* InceptionV3

The transcriptomic cell-type/family labels from the metadata were used as the classification labels. Excitatory subclasses were merged into one class, CT_ET_IT, while inhibitory classes such as Pvalb, Sst, Vip, and Lamp5 were kept separated.

This notebook compares two different ways of representing neuronal identity: manual electrophysiological features versus data-driven WST representations learned directly from voltage dynamics.

---

## 5) DISTANCE_ANALYSIS.ipynb

This notebook compares similarity between neurons in two spaces.

First, it computes distances between neurons using WST representations learned from stacked voltage traces. Second, it computes distances between the same neurons using the predefined manual electrophysiological features.

The aim is to see whether neurons that are close in WST space are also close in classical electrophysiological feature space, and to find cases where the two representations agree or disagree.

For this analysis:

* Raw voltage traces from DANDI:000008 are used to compute WST representations.
* Predefined electrophysiological features from Scala et al. are used as the classical feature space.
* Transcriptomic cell-type/family labels from the metadata are used to interpret the biological identity of the compared cells.

This notebook acts as a validation step to examine whether WST captures meaningful neuronal dynamics beyond traditional handcrafted features.

---

## External Data and References

This project uses public Patch-seq data and metadata from:

Scala, F.*, Kobak, D.*, Bernabucci, M., Bernaerts, Y., Cadwell, C. R., Castro, J. R., Hartmanis, L., Jiang, X., Laturnus, S., Miranda, E., Mulherkar, S., Tan, Z. H., Yao, Z., Zeng, H., Sandberg, R., Berens, P., & Tolias, A. S.
Phenotypic variation of transcriptomic cell types in mouse motor cortex.
Nature, 598, 144–150, 2021.
DOI: https://doi.org/10.1038/s41586-020-2907-3

The original analysis code and preprocessed data are available here:

https://github.com/berenslab/mini-atlas

---

## How the External Data Were Used

The external files were used in three different ways.

The file m1_patchseq_meta_data.csv was used for transcriptomic cell-type/family annotations. These labels were used for visualization, classification, and biological interpretation.

The file m1_patchseq_ephys_features.csv was used for predefined/manual electrophysiological features. These features were used for the classical machine learning baselines and for the distance-analysis comparison.

The raw NWB electrophysiological recordings from DANDI:000008 were used for the Wavelet Scattering pipeline. These raw voltage traces were used to compute WST representations directly from neuronal activity.

In short, the metadata and manual feature files were used as biological labels and classical baselines, while the raw voltage traces were used for the Wavelet Scattering representations.

---

## Setup

A GPU is recommended for the Wavelet Scattering and deep learning parts of the project.

Main Python libraries used:

* kymatio
* torch
* torchvision
* scikit-learn
* numpy
* pandas
* matplotlib
* seaborn
* umap-learn
* pynwb
* dandi

---

## Data Availability

Large external data files are not redistributed in this repository. To reproduce the analyses, the required data should be downloaded from the original public sources, including the BerensLab mini-atlas repository and DANDI:000008.
