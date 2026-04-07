# scRNAseq-cross-disorder-neuronal-integration

We integrated five human prefrontal cortex single-nucleus RNA-seq (snRNA-seq) datasets using two strategies: cell-level and individual-level integration. We performed unified annotation based on clustering and network-driven approaches, and evaluated the integration using assortativity to assess preservation of neuronal structure.

---

## 🧬 Project Overview

This project aims to construct a unified transcriptional landscape across multiple brain disorders, with a specific focus on neuronal populations. By integrating heterogeneous single-nucleus datasets, we explore shared and disorder-specific molecular patterns across conditions.

---

## ⚙️ Methods

- Cell-level integration of snRNA-seq datasets  
- Individual-level integration across samples  
- Cluster-based annotation  
- Network-driven annotation  
- Pseudo-bulk integration  
- Assortativity analysis to evaluate structural preservation  

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

## 📈 Results

- Single-nucleus integration across datasets  
- Unified neuronal annotation  
- Cluster-level integration consistency  
- Pseudo-bulk integration and reduced assortativity  

---

## 📊 Results Visualization

### Dataset Integration

![Integration](Dataset-Integration.jpg)

Integration of five human prefrontal cortex snRNA-seq datasets across neurological and psychiatric disorders.

---

### Neuronal Annotation

![Annotation](Annotation.png)

Neuronal subtypes were consistently identified across datasets using both cluster-based and network-driven annotation strategies. The heatmap confirms high concordance between methods, supporting the robustness of the annotation.

---
### Marker-based Validation

![Marker Validation](GeneMarkerValidation.png)

We validated the annotation by examining canonical excitatory and inhibitory marker genes. The observed consistency between marker expression and assigned neuronal identities supports the robustness of the annotation.

---
 ### Cluster-level Integration

![Cluster-level Integration](Individual_Integration.png)

Cluster-level integration was performed by compressing cells into within-sample clusters and integrating their transcriptional signatures across datasets. The lower UMAPs represent our integrated data colored by cell type and study. The top schematic is adapted from the original methodological framework to summarize the analysis strategy.

--- 
## 💻 Code

This repository includes an R Markdown-based workflow for single-nucleus data integration, annotation, and downstream analysis.

---

## 🚀 Key Skills Demonstrated

- Single-nucleus RNA-seq analysis  
- Data integration  
- Transcriptomics  
- Network-based analysis  
- Neurogenomics  
- R / RMarkdown  
