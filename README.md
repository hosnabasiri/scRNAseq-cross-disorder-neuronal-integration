# scRNAseq Cross-Disorder Neuronal Integration

The number of independently generated single-cell transcriptomic studies in neurological and psychiatric disorders is growing rapidly. This creates a major opportunity: rather than studying each disorder in isolation, we can now ask how neuronal populations change across conditions — if we can integrate these datasets meaningfully.

But combining independent single-cell datasets is not straightforward. Three core challenges arise:

- **Sparse, high-dimensional data** — each cell is represented by thousands of genes, most of which are undetected in any given cell
- **Inconsistent annotation** — studies use different nomenclatures and resolutions to label cell types
- **Study-specific batch effects** — technical differences between labs and sequencing platforms can dominate biological signal

This project develops and compares two computational integration strategies to address these challenges, building toward a unified transcriptional reference for cross-disorder comparison of cortical neuronal populations.

---

## Key Finding

> **Organizing single-cell data at the level of individual donors before cross-study integration — rather than integrating cells directly — produces well-structured neuronal clusters at a fraction of the computational cost, and without requiring explicit batch correction.**

We tested two strategies side by side:

| | Strategy 1: Cell-level | Strategy 2: Cluster-first |
|---|---|---|
| Units integrated | ~288,000 cells | 1,659 individual-level clusters |
| Batch correction needed | Yes (Harmony) | No |
| Neuronal structure preserved | Yes | Yes |
| Computational cost | High | Low |
| Annotation consistency | Good | Good |

Strategy 2 achieved comparable neuronal organization to Strategy 1 while dramatically reducing dimensionality and eliminating the need for embedding-level batch correction — because clustering within individuals naturally reduces cross-study technical variation before integration.

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

After filtering, **288,076 neuronal cells from 134 individuals** were used for integration, across **16,321 shared genes**.

> **Note on reproducibility:** Input datasets are not included in this repository. The AD and MDD datasets are available under controlled access (Synapse, GEO); ASD and MS datasets are publicly available via SRA. All dataset paths in the scripts are marked as `"path/to/..."` and should be updated to local paths before running. The code is fully documented and can be adapted to compatible datasets.

---

## Analysis Workflow

### Strategy 1: Cell-level Integration

#### `01_data_preparation.Rmd`
Loads all five datasets, extracts neuronal populations based on original cell-type labels, identifies the 16,321 genes shared across all studies, and constructs a unified `ACTIONetExperiment` object. Assigns cell-level metadata including unique cell tags, diagnosis, cell type, and individual donor ID for each dataset.

#### `02_cell_level_analysis.Rmd`
Runs ACTIONet on the integrated object: normalizes counts, applies Harmony batch correction using study of origin as the batch variable, builds the ACTIONet network, and runs clustering. Uses network-driven label inference to propagate Jorstad reference annotations to unlabeled cells. Generates UMAP plots colored by cell type, disease, and inferred identity, and produces marker gene visualizations for SST, VIP, and CBLN2.

#### `03_annotations.Rmd`
Computes cluster-level expression profiles by averaging logcounts per Leiden cluster. Derives fold-change signatures for both the integrated clusters and the Jorstad reference cell types using pairwise FC functions. Annotates each cluster by correlating its signature against all Jorstad reference signatures and assigning the highest-correlation cell type.

---

<p align="center">
  <img src="figures/Dataset-Integration.jpg" width="900">
</p>

Five independent cortical datasets were combined into a single object and processed using ACTIONet. Harmony batch correction was applied using study of origin as the batch variable. The resulting UMAP shows the combined neuronal landscape across all five studies.

<p align="center">
  <img src="figures/Annotation.png" width="850">
</p>

Neuronal identities were assigned using two complementary strategies: cluster-based annotation via signature correlation, and network-driven label inference from Jorstad reference annotations. The heatmap shows high concordance between the two approaches — a strong diagonal pattern confirming consistent neuronal identity assignment across datasets.

<p align="center">
  <img src="figures/GeneMarkerValidation.png" width="850">
</p>

Annotations were validated using canonical neuronal marker genes. SST marks somatostatin-positive inhibitory interneurons, VIP marks vasoactive intestinal peptide-expressing interneurons, and CBLN2 is associated with excitatory intratelencephalic projection neurons. Marker expression patterns were consistent with assigned neuronal identities across all datasets.

---

### Strategy 2: Individual-level Cluster-first Integration

Instead of integrating ~288,000 cells directly across studies, we first clustered cells **within each individual donor**, computed transcriptional signatures per cluster, and aligned clusters to a shared reference. This reduced the representation to **1,659 individual-level clusters** — a ~175x reduction in dimensionality — while preserving biologically meaningful neuronal structure.

Critically, because clustering happens within individuals before any cross-study comparison, study-specific technical variation has less opportunity to influence cluster boundaries. **No embedding-level batch correction was required.**

This cluster-first strategy was adapted from a framework developed by Zonca et al. (bioRxiv, 2025) and applied here to cross-disorder neuronal integration.

#### `04_individual_clustering.Rmd`
Splits the integrated object by disease, then runs ACTIONet independently for each individual donor within each disease group (MS, ASD, MDD, AD) — without Harmony batch correction. Each individual produces its own clustering, which is then used as the unit of analysis in downstream integration.

#### `05_foldchange_signatures.Rmd`
For each individual, computes cluster-level expression profiles and derives fold-change signatures. Correlates each individual's cluster signatures against the Jorstad reference cell-type signatures to produce a per-individual correlation matrix. Saves heatmaps of cluster-to-reference correlation for each individual.

#### `06_individual_binding_correlations.Rmd`
Loads all per-individual correlation matrices across all five disease groups and binds them into a single unified matrix (Jorstad cell types × all individual clusters). Assigns cluster-level cell-type labels based on highest reference correlation. Maps these labels back to the integrated neuronal object at single-cell resolution.

#### `07_individual_neuron_object.Rmd`
Constructs the cluster-level `ACTIONetExperiment` object using the unified correlation matrix as the assay. Assigns metadata including individual ID, disease group, and diagnosis. Runs ACTIONet on this compact object (1,659 clusters instead of 288,000 cells) to produce the final integrated representation.

---

<p align="center">
  <img src="figures/ClusterLevel-Integration.png" width="900">
</p>

The UMAP (left) shows clusters colored by annotated cell type — neuronal identities group together consistently across individuals and studies. The UMAP (right) shows the same clusters colored by study of origin — datasets are broadly intermixed without any explicit batch correction step, demonstrating that individual-level clustering naturally reduces study-driven separation.

---

#### `08_pseudobulk.Rmd`
Constructs pseudobulk expression profiles by summing raw counts across all cells within each individual-level cluster. Applies CPM normalization and derives fold-change signatures. Assigns metadata (individual, cell type, study, diagnosis) to each pseudobulk sample. Applies ComBat-seq batch correction at the pseudobulk level, using study as the batch variable and preserving cell type and diagnosis as biological covariates.

<p align="center">
  <img src="figures/Bulk.png" width="850">
</p>

Pseudobulk profiles were generated from individual-level neuronal clusters. ComBat-seq correction reduced study-driven separation (left panels) while preserving neuronal cell-type structure (right panels).

---

#### `09_assortativity.Rmd`
For each neuronal cell type, builds a KNN graph from the UMAP embedding of pseudobulk logcounts (both corrected and non-corrected). Computes network assortativity with respect to study of origin and diagnosis labels. Lower study assortativity after correction confirms that batch effects are reduced while biological structure is preserved.

<p align="center">
  <img src="figures/Assortativity.png" width="700">
</p>

Network assortativity decreased by approximately **67% on average** across all neuronal populations after pseudobulk batch correction, confirming that study-driven connectivity was reduced while neuronal identity structure remained intact.

| | Before Correction | After Correction | Reduction |
|---|---|---|---|
| Mean study assortativity | ~0.37 | ~0.12 | ~67% |
| Neuronal structure | Preserved | Preserved | — |

---

## Tools & Packages

| Purpose | Tools |
|---|---|
| Single-cell analysis | ACTIONet, SingleCellExperiment |
| Batch correction | Harmony (Strategy 1), ComBat-seq/sva (pseudobulk) |
| Network analysis | igraph |
| Dimensionality reduction | umap |
| Visualization | ComplexHeatmap, ggplot2 |
| Workflow | R, RMarkdown |

---

## Reference

Thesis: *Building a unified transcriptional landscape for cross-disorder comparisons across neuronal populations*
Hosna Basiri Kheradmand Tehrani — University of Milan / Human Technopole, 2025/2026
Supervisors: Prof. Matteo Chiara, Dr. José Davila-Velderrain
