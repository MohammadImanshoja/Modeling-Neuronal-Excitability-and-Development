This repository contains simulation notebooks and analysis pipelines developed for a Master's thesis project focused on modeling neuronal excitability. The project combines classical biophysical modeling with modern unsupervised deep learning techniques to categorize neuronal firing patterns and analyze developmental changes in neuron physiology.

## 📂 Project Overview

The repository consists of two primary modules:

### 1. Excitability Regimes & Unsupervised Classification
This module explores the **Hodgkin-Huxley (HH)** model to generate diverse voltage traces, classifies them based on biophysical parameters ($S$ and $K$), and implements a deep learning pipeline to cluster these behaviors without labels.

*   **Simulation:** HH model simulation over randomized parameter ranges ($g_{Na}$, $g_K$, etc.) to generate Non-excitable, Excitable, and Oscillatory traces.
*   **S-K Plane Analysis:** Visualization of excitability regimes based on the geometric parameters:
    *   $S$: Ratio of sodium to total conductance.
    *   $K$: Ratio of gating time constants.
*   **Feature Extraction:** Application of the **Wavelet Scattering Transform (WST)** using `kymatio` to create translation-invariant representations of voltage traces.
*   **Dimensionality Reduction:** A dynamic **Deep Convolutional Variational Autoencoder (VAE)** trained on WST coefficients to compress features into a latent space.
*   **Clustering:** Utilization of **HDBSCAN** and **UMAP** on the VAE latent space to identify distinct neuronal firing phenotypes unsupervised.

### 2. Comparative Modeling: Young vs. Mature Neurons
This module implements a detailed biophysical model to study the developmental differences in intrinsic excitability between young and mature neurons.

*   **Mechanisms:** Includes voltage-gated Sodium ($Na_v$), Potassium ($K_v$), Calcium ($Ca_v$), and Calcium-activated Potassium ($K_{Ca}$) channels.
*   **Calcium Dynamics:** Implements Goldman-Hodgkin-Katz (GHK) flux equations and calcium buffering/diffusion dynamics.
*   **Comparative Analysis:** Simulates the response to current injection to visualize differences in action potential shape, frequency, and underlying ionic currents between the two age groups.
*   
