# scRNAseq Cross-Disorder Neuronal Integration

The number of independently generated single-cell transcriptomic studies in neurological and psychiatric disorders is growing rapidly. This creates a major opportunity: rather than studying each disorder in isolation, we can now ask how neuronal populations change across conditions — if we can integrate these datasets meaningfully.

Combining independent single-cell datasets is not straightforward. Three core challenges arise:

- **Sparse, high-dimensional data** — each cell is represented by thousands of genes, most of which are undetected in any given cell
- **Inconsistent annotation** — studies use different nomenclatures and resolutions to label cell types
- **Study-specific batch effects** — technical differences between labs and sequencing platforms can dominate biological signal

This project develops and compares two computational integration strategies to address these challenges, building toward a unified transcriptional reference for cross-disorder comparison of cortical neuronal populations.

---

## Datasets

We combined five independent human cortical snRNA-seq datasets covering four brain disorders and healthy controls:

| Dataset | Condition | Cells | Reference |
|---|---|---|---|
| Velmeshev et al. 2019 | Autism Spectrum Disorder (ASD) | 60,849 | Science |
| Schirmer et al. 2019 | Multiple Sclerosis (MS) | 23,705 | Nature |
| Nagy et al. 2020 | Major Depressive Disorder (MDD) | 59,583 | Nature Neuroscience |
| Mathys et al. 2019 | Alzheimer's Disease (AD) | 44,172 | Nature |
| Jorstad et al. 2023 | Healthy controls | 99,767 | Science |

After filtering, **288,076 neuronal cells from 134 individuals** were integrated across **16,321 shared genes**.

> **Note:** Input datasets are not included in this repository. The AD and MDD datasets are available under controlled access (Synapse, GEO); ASD and MS datasets are publicly available via SRA. All dataset paths in the scripts are marked as `"path/to/..."` and should be updated to local paths before running.

---

## Key Finding

> **Organizing single-cell data at the level of individual donors before cross-study integration — rather than integrating cells directly — produces well-structured neuronal clusters at a fraction of the computational cost, and without requiring explicit batch correction.**

| | Strategy 1: Cell-level | Strategy 2: Cluster-first |
|---|---|---|
| Units integrated | ~288,000 cells | 1,659 individual-level clusters |
| Batch correction needed | Yes (Harmony) | No |
| Neuronal structure preserved | Yes | Yes |
| Computational cost | High | Low |

---

## Results

### Strategy 1: Cell-level Integration

#### Dataset Integration

<p align="center">
  <img src="figures/Dataset-Integration.jpg" width="900">
</p>

Five independent cortical datasets were combined into a single object and processed using ACTIONet. Harmony batch correction was applied using study of origin as the batch variable. Each panel on the left shows the neuronal landscape of one study before integration. The right panel shows all 288,076 cells in the unified integrated space — neuronal populations from different studies overlap rather than forming separate islands, indicating successful integration.

---

#### Cell-type Annotation

<p align="center">
  <img src="figures/Annotation.png" width="850">
</p>

Neuronal identities were assigned using two complementary strategies run in parallel. The first strategy (top left) defined clusters across the integrated dataset and annotated each cluster by correlating its transcriptional signature against Jorstad reference cell-type signatures. The second strategy (bottom left) propagated Jorstad reference labels through the cell-cell similarity network to assign identities at single-cell resolution. The heatmap (right) compares the two annotation strategies — the strong diagonal pattern confirms that both approaches assign the same identities consistently, supporting the robustness of the annotation.

---

#### Marker-based Validation

<p align="center">
  <img src="figures/GeneMarkerValidation.png" width="850">
</p>

Annotations were validated using canonical neuronal marker genes overlaid on the integrated UMAP. SST is selectively expressed in somatostatin-positive inhibitory interneurons, VIP marks vasoactive intestinal peptide-expressing interneurons, and CBLN2 is associated with excitatory intratelencephalic projection neurons. In each case, marker expression is enriched in the expected annotated population, confirming that cell-type assignments are biologically coherent.

---

### Strategy 2: Individual-level Cluster-first Integration

#### Cluster-level Integration

<p align="center">
  <img src="figures/ClusterLevel-Integration.png" width="900">
</p>

Instead of integrating ~288,000 cells directly, cells were first clustered within each individual donor, reducing the representation to 1,659 individual-level clusters. Each cluster was then annotated by correlating its transcriptional signature against the Jorstad reference. The UMAP on the left shows clusters colored by annotated cell type — the same neuronal identities group together consistently across individuals and studies. The UMAP on the right shows the same clusters colored by study of origin — datasets are broadly intermixed **without any explicit batch correction**, demonstrating that individual-level clustering naturally absorbs study-driven technical variation before integration.

---

#### Pseudobulk Batch Correction

<p align="center">
  <img src="figures/Bulk.png" width="850">
</p>

To enable stable gene-level comparisons, pseudobulk profiles were constructed by aggregating raw counts across all cells within each individual-level cluster. ComBat-seq batch correction was then applied at the pseudobulk level, using study as the batch variable and preserving cell type and diagnosis as biological covariates. The top row shows the pseudobulk representation before correction — samples cluster strongly by study of origin. The bottom row shows the same representation after correction — study-driven separation is substantially reduced while neuronal cell-type structure (right panels) remains intact.

---

#### Assortativity Analysis

<p align="center">
  <img src="figures/Assortativity.png" width="700">
</p>

To quantify integration quality beyond visual inspection, network assortativity was computed on the KNN graph of pseudobulk samples for each neuronal cell type. Assortativity measures whether nodes preferentially connect to others sharing the same label — high values indicate that study origin drives connectivity, low values indicate that biological similarity does. After batch correction, study-driven assortativity decreased by approximately **67% on average** across all neuronal populations, while cell-type assortativity remained high. This confirms that technical batch effects were reduced without disrupting the underlying neuronal organization.

| | Before Correction | After Correction | Reduction |
|---|---|---|---|
| Mean study assortativity | ~0.37 | ~0.12 | ~67% |
| Neuronal structure | Preserved | Preserved | — |

---

## Scripts

All scripts are in R/RMarkdown format and should be run in numerical order.

| Script | Description |
|---|---|
| `01_data_preparation.Rmd` | Loads all five datasets, extracts neuronal populations, identifies shared genes, and constructs the integrated ACTIONetExperiment object with cell-level metadata |
| `02_cell_level_analysis.Rmd` | Runs ACTIONet with Harmony batch correction, performs network-driven label inference from Jorstad reference, and generates UMAP and marker plots |
| `03_annotations.Rmd` | Computes cluster-level profiles and fold-change signatures, annotates clusters by correlation with Jorstad reference cell types |
| `04_individual_clustering.Rmd` | Splits the integrated object by disease and runs ACTIONet independently for each individual donor, without batch correction |
| `05_foldchange_signatures.Rmd` | Computes per-individual cluster profiles and signatures, correlates each against the Jorstad reference, and saves per-individual correlation heatmaps |
| `06_individual_binding_correlations.Rmd` | Loads all per-individual correlation matrices, binds them into a unified matrix, assigns cluster-level cell-type labels, and maps them back to the integrated object |
| `07_individual_neuron_object.Rmd` | Constructs the compact cluster-level ACTIONetExperiment object from the unified correlation matrix, assigns metadata, and runs ACTIONet on the 1,659-cluster representation |
| `08_pseudobulk.Rmd` | Constructs pseudobulk profiles by aggregating counts per individual-level cluster, applies ComBat-seq correction preserving cell type and diagnosis as biological covariates |
| `09_assortativity.Rmd` | Computes network assortativity on KNN graphs of pseudobulk samples for each cell type, comparing corrected and non-corrected representations |

---

## Tools & Packages

| Purpose | Tools |
|---|---|
| Single-cell analysis | ACTIONet, SingleCellExperiment |
| Batch correction | Harmony (Strategy 1), ComBat-seq / sva (pseudobulk) |
| Network analysis | igraph |
| Dimensionality reduction | umap |
| Visualization | ComplexHeatmap, ggplot2 |
| Workflow | R, RMarkdown |

---

## Reference

Thesis: *Building a unified transcriptional landscape for cross-disorder comparisons across neuronal populations*
Hosna Basiri Kheradmand Tehrani — University of Milan / Human Technopole, 2025/2026
Supervisors: Prof. Matteo Chiara, Dr. José Davila-Velderrain
