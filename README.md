# scRNAseq-cross-disorder-neuronal-integration

The increasing availability of independently generated single-nucleus RNA-seq datasets in neurological and psychiatric disorders provides an opportunity to study transcriptional variation across conditions within a unified framework.

To enable this, we integrated heterogeneous cortical datasets rather than analyzing each disorder in isolation, while addressing key challenges such as annotation inconsistency and study-specific batch effects.

We compared a conventional cell-level integration approach with a strategy that first clusters cells within each individual before integration.

---

## 📊 Data

- 5 single-nucleus RNA-seq (snRNA-seq) datasets  
- Brain region: Human prefrontal cortex  
- Conditions:
  - Alzheimer’s disease  
  - Multiple sclerosis  
  - Major depressive disorder  
  - Autism  
  - Healthy controls  

---

# 📈 Analysis Workflow

## 🔹 Strategy 1: Cell-level Integration

### Dataset Integration

![Integration](Dataset-Integration.jpg)

Integration of five snRNA-seq datasets across neurological and psychiatric disorders.

---

### Annotation

![Annotation](Annotation.png)

Cells were annotated using two complementary approaches: cluster-based annotation and network-driven label inference.  
The strong agreement between these methods supports consistent neuronal identity assignment across datasets.

---

### Marker-based Validation

![Marker Validation](GeneMarkerValidation.png)

We validated the annotation using canonical excitatory and inhibitory marker genes.  
The observed marker expression patterns match the assigned neuronal identities, supporting the robustness of the annotation.

---

## 🔹 Strategy 2: Individual-level (Cluster-first) Integration

### Cluster-level Integration

![Cluster-level Integration](ClusterLevel-Integration.png)


In this approach, cells were first grouped into clusters within each individual, and integration was then performed at the cluster level.

The representation reduces data complexity (from 288,076 cells to 1,659 clusters) while preserving meaningful biological structure.  
All embeddings and visualizations shown here were generated as part of this analysis. By operating at the individual level before integration, batch effects are naturally reduced.

This strategy is based on a previously proposed framework (Zonca et al., bioRxiv, 2025), and was applied here to our datasets to evaluate its performance in cross-disorder integration.

---

### Pseudo-bulk Representation

![Pseudo-bulk](Bulk.png)

Pseudo-bulk profiles were generated from individual-level clusters to enable stable downstream analysis.

Batch correction reduced study-driven variation while preserving neuronal cell-type structure, leading to improved mixing across datasets.

---

### Assortativity Analysis

![Assortativity](assortativity.png)

To quantitatively assess integration quality, we computed network assortativity with respect to study labels.

Before correction, high assortativity indicated that clusters were strongly grouped by study.  
After correction, assortativity decreased substantially (~67%), indicating reduced batch effects.

These results show that connectivity in the data is driven primarily by biological similarity rather than dataset origin.

---

## 💻 Code

This repository provides a structured R Markdown pipeline for cross-disease single-nucleus RNA-seq analysis, including:

- Integration of multiple neurological and psychiatric datasets (ASD, MS, MDD, AD, Jorstad reference)
- Cell-level and individual-level analyses using ACTIONet
- Cluster annotation based on fold-change signatures and reference mapping
- Construction of pseudobulk profiles for downstream comparisons
- Batch effect correction using ComBat-seq while preserving biological covariates
- Network-based evaluation of integration quality using assortativity analysis

All analyses are organized into step-by-step scripts for full reproducibility.

---

## 🚀 Key Skills Demonstrated

- Single-nucleus RNA-seq analysis  
- Data integration  
- Neurogenomics  
- Network-based analysis  
- Batch effect correction  
- R / RMarkdown  
