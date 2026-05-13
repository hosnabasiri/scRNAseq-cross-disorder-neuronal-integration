# scRNAseq-cross-disorder-neuronal-integration

With the increasing number of single-nucleus RNA-seq studies in brain disorders, it is now possible to combine datasets from different studies and compare neuronal changes across conditions.

In this project, we integrated human cortical datasets from AD, MS, MDD, ASD, and healthy controls. The goal was to create a shared analytical framework for studying neuronal transcriptional variation across disorders. To do this, we had to address three main challenges: sparse and high-dimensional expression data, differences in neuronal cell-type annotations across studies, and study-specific batch effects.

We tested two integration strategies:

1. **Cell-level integration**, where neuronal cells from all datasets were integrated directly.
2. **Individual-level cluster-first integration**, where cells were first clustered within each individual and then integrated across datasets.

---

# 📊 Data

This study integrates five independent human prefrontal cortex snRNA-seq datasets:

- Alzheimer’s disease (AD)
- Multiple sclerosis (MS)
- Major depressive disorder (MDD)
- Autism spectrum disorder (ASD)
- Healthy controls

---

# 📈 Analysis Workflow

## 🔹 Strategy 1: Cell-level Integration

### Dataset Integration

![Integration](figures/Dataset-Integration.jpg)

Integration of five snRNA-seq datasets across neurological and psychiatric disorders using ACTIONet.

---

### Cell-type Annotation

![Annotation](figures/Annotation.png)

Neuronal identities were assigned using two complementary annotation strategies:

- cluster-based annotation
- network-driven label inference using a reference dataset

The strong agreement between the two approaches supports consistent neuronal identity assignment across datasets.

---

### Marker-based Validation

![Marker Validation](figures/GeneMarkerValidation.png)

Cell-type annotations were validated using canonical neuronal marker genes associated with excitatory and inhibitory neuronal populations.

Observed marker expression patterns were consistent with assigned neuronal identities, supporting annotation robustness.

---

## 🔹 Strategy 2: Individual-level (Cluster-first) Integration

### Cluster-level Integration

![Cluster-level Integration](figures/ClusterLevel-Integration.png)

In this strategy, cells were first clustered within each individual sample prior to integration.

This reduced dataset complexity from approximately **288,000 cells to 1,659 cluster-level representations** while preserving biologically meaningful structure.

By integrating cluster-level representations instead of individual cells, technical variation across studies was substantially reduced while maintaining disease-relevant neuronal organization.

This strategy was inspired by the framework proposed by Zonca et al. (bioRxiv, 2025) and adapted here for cross-disorder neuronal integration.

---

### Pseudobulk Representation

![Pseudo-bulk](figures/Bulk.png)

Pseudobulk profiles were generated from individual-level neuronal clusters to stabilize downstream analyses.

Batch correction using ComBat-seq reduced study-driven variation while preserving neuronal cell-type structure, resulting in improved cross-study mixing.

---

### Assortativity Analysis

![Assortativity](figures/Assortativity.png)

To quantitatively evaluate integration quality, we computed network assortativity with respect to study labels.

Before batch correction, high assortativity indicated strong study-specific clustering.  
After correction, assortativity decreased substantially (~67%), demonstrating effective reduction of batch effects.

These results suggest that connectivity structure in the integrated representation is driven primarily by biological similarity rather than dataset origin.

---

# 💻 Repository Structure

# 🗂 Repository Structure

```text
scripts/
├── 01_data_preparation.Rmd
├── 02_cell_level_analysis.Rmd
├── 03_annotation.Rmd
├── 04_individual_clustering.Rmd
├── 05_foldchange_signatures.Rmd
├── 06_individual_binding_correlations.Rmd
├── 07_individual_neuron_object.Rmd
├── 08_pseudobulk.Rmd
└── 09_assortativity.Rmd

figures/
├── Annotation.png
├── Bulk.png
├── ClusterLevel-Integration.png
├── Dataset-Integration.jpg
├── GeneMarkerValidation.png
└── assortativity.png
```

---

# ⚙️ Methods Implemented

This repository contains a reproducible R/RMarkdown workflow for:

- Multi-dataset snRNA-seq integration
- Cell-level and cluster-level ACTIONet analysis
- Reference-based neuronal cell-type annotation
- Fold-change signature correlation analysis
- Pseudobulk construction
- Batch correction using ComBat-seq
- Network-based integration evaluation using assortativity analysis

---

# 🧰 Main Packages

- ACTIONet
- SingleCellExperiment
- ComplexHeatmap
- sva
- igraph
- umap

---
# ▶️ Running the pipeline

Scripts are organized sequentially and should be executed in numerical order.

Input datasets are not included in this repository due to size and access restrictions.

Main analyses were performed in R using ACTIONet and SingleCellExperiment workflows.

---
# 🚀 Key Skills Demonstrated

- Single-cell and single-nucleus RNA-seq analysis
- Cross-dataset integration
- Neurogenomics
- Batch effect correction
- Network-based analysis
- Pseudobulk modeling
- R / RMarkdown workflows
