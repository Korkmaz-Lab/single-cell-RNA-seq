# single-cell-RNA-seq

  Single – Cell RNA Sequencing Workflow

This repository contains a Scanpy – Harmony based analytical pipeline for single-cel RNA sequencing data.

___

Introduction
---

Single-cell RNA sequencing (scRNA-seq) enables transcriptomic profiling at the resolution of individual cells, allowing researchers to examine cellular heterogeneity that cannot be resolved with bulk RNA-seq. While bulk RNA-seq provides an average expression profile across all cells in a sample, scRNA-seq captures variation between individual cells, making it especially useful for studying complex tissues, tumor microenvironments, developmental processes, and rare cell populations. By resolving gene expression at the single-cell level, this technology allows the identification of distinct cell states, lineage relationships, and dynamic responses to perturbations. As a result, scRNA-seq has become a central approach for understanding how heterogeneous cell populations contribute to biological function and disease.

___
Data Structure and Input
---

  Single-cell RNA-seq analysis begins with the import of raw count matrices together with cell-level metadata. These matrices are converted into the AnnData format for processing within the Scanpy framework. “anndata” is a Python- based data structure, which is designed for handling annotated high-dimensional biological data, namely AnnData. It is actually an annotated data format that basically works on the annotated data matrices. It is used in scRNA-seq for analyzing the data by aligning two dimensions as observations and variables.

  Given the extreme sparsity of single-cell datasets—where approximately 90–95% of matrix entries are zero—the expression matrix is stored in compressed sparse row (CSR) format. This representation ensures memory efficiency and computational compatibility with the broader scientific Python ecosystem. The sparse structure is particularly important when working with large datasets, as it allows scalable analysis without excessive memory overhead.

___

Workflow:
---

1.	Quality Control
2.	Normalization
3.	Feature Selection
4.	Dimensionality Reduction
5.	Integration (Batch Effect Correction)
6.	2- dimensional Embedding (UMAP, t-SNE)
7.	Clustering
8.	 Differential Gene Expression 
9.	 Gene Set Enrichment Analysis

---

Package Dependencies: 

This scRNA-seq analysis steps are performed in Python environment using Google Colab, the following packages (with recommended stable versions) should be installed:

•	pandas (2.1.4)

•	numpy (1.26.4)

•	matplotlib (3.8.2)

•	seaborn (0.13.1)

•	umap (0.5.5)

•	scanpy (1.9.8)

•	harmony (0.0.9)

•	leidenalg (0.10.2)

•	scipy (1.11.4)

Since the Colab environment is updated frequently, it is difficult to provide fully fixed package versions. However, the versions above are commonly used and mutually compatible for stable scRNA-seq pipelines.

---

Install dependencies using:

    pip! install -q pandas numpy matplotlib seaborn umap scanpy harmony leidenalg scipy

    from scipy.sparse import csr_matrix

___

WORKFLOW
---



Quality Control
---

Quality control is performed in Scanpy to make sure that later analyses reflect real biological differences rather than technical noise.




Normalization
---

To make cells comparable, the total counts in each cell are first normalized using Scanpy’s normalize_total function, scaling each cell to 10,000 counts. This corrects for differences in sequencing depth between cells. Then, a log transformation (log(x + 1)) is applied to reduce the effect of large differences in gene expression while keeping zero values unchanged. After that, the expression matrix is scaled to the zero mean and unit
variance to alleviate the effects of highly expressed genes.




Feature Selection
---

Highly variable genes (HVGs) are identified using the dispersion-based method originally developed in Seurat and implemented in Scanpy through the highly_variable_genes function.



Dimensionality Reduction
---

The most varying axes in data are computing using principal component analysis (PCA). In this step of dimensionality reduction, PCA is used for summarization rather than visualization. It is calculated on the highly variable genes and “Arpack” was used as singular value decomposition solver. ARPACK (ARnoldi PACKage) is a software package, and it is utilized as a solver for matrices having large-scale data. Therefore, initial ~20000 dimensions (number of genes) are reduced to ~50 dimensions (number of principal components).


Integration (Batch Effect Correction)
---

When analyzing data from multiple batches or experimental conditions, integration is performed using the Harmony algorithm. Harmony takes PCA embeddings as input and corrects for batch effects while preserving biological structure. The Harmony-corrected principal components are then used for downstream analyses. This ensures that clustering and visualization reflect biological similarity rather than technical batch origin. Following integration, a k-nearest neighbor (kNN) graph is constructed using the corrected embeddings.



2- dimensional Embedding (UMAP, t-SNE)
---

Nonlinear dimensionality reduction techniques are applied in the Harmony-corrected PCA space for visualization. UMAP is computed with a minimum distance parameter of 0.5 and spread parameter of 1. t-SNE is also calculated using a perplexity parameter of 30 and early exaggeration set to 12.


Clustering
---

Clustering is performed using the Leiden algorithm, which groups cells based on similarities in their gene expression profiles. It works on a nearest-neighbor graph built from the Harmony-corrected principal components. The resolution parameter controls how detailed the clusters are: lower values give fewer, broader clusters, while higher values produce more, finer clusters. If needed, a specific group of cells can be selected and clustered again to explore smaller subpopulations. Overall, clustering helps identify potential cell types or cell states and provides the basis for further analysis.



Differential Gene Expression
---

Cluster-specific phenotypes are characterized through differential gene expression analysis using Scanpy’s rank_genes_groups function. A Wilcoxon rank-sum test is applied in a one-versus-rest manner to identify genes distinguishing each cluster. To control for multiple testing, p-values are adjusted using the Benjamini–Hochberg correction. This step yields statistically robust markers for each cluster, enabling biological annotation and functional interpretation.



Gene Set Enrichment Analysis
---

Gene set enrichment analysis was performed using Enrichr, which applies a hypergeometric test to identify enriched pathways. Enrichr queries were carried out through the GSEApy Python package. The KEGG database was used as the reference pathway library for the input gene list.

___






