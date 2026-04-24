# Basic Spatial Analysis with Scanpy

Analysis of 10x Genomics Visium spatial transcriptomics data using Scanpy.
The notebook is fully documented with detailed markdown explanations before
every code block, covering the biological rationale behind QC thresholds,
what each preprocessing operation does to the data, and how to interpret
the spatial and UMAP visualizations.

## Dataset

Human lymph node Visium section, downloaded automatically via
`sc.datasets.visium_sge()`. The dataset contains 4,035 spots across
36,601 genes, covering a full lymph node section with the accompanying
high-resolution H&E tissue image. All data is fetched automatically
at runtime with no manual download required.

## Platform

All steps were run on Google Colab.

## Dependencies

```python
pip install scanpy seaborn igraph leidenalg
```

## Workflow

### 1. Imports and Setup

```python
import matplotlib.pyplot as plt
import pandas as pd
import scanpy as sc
import seaborn as sns

sc.set_figure_params(facecolor="white", figsize=(8, 8))
sc.settings.verbosity = 3
```

### 2. Load Visium Data

```python
adata = sc.datasets.visium_sge(sample_id="V1_Human_Lymph_Node")
adata.var_names_make_unique()
adata.var["mt"] = adata.var_names.str.startswith("MT-")
sc.pp.calculate_qc_metrics(adata, qc_vars=["mt"], inplace=True)
```

`visium_sge` downloads the human lymph node dataset and loads it as an
AnnData object. AnnData stores the count matrix in `.X`, spatial coordinates
in `.obsm["spatial"]`, and the tissue image in `.uns["spatial"]`. We flag
mitochondrial genes since a high percentage of mitochondrial reads usually
indicates a low-quality or dying cell.

### 3. QC Plots and Filtering

```python
fig, axs = plt.subplots(1, 4, figsize=(15, 4))
sns.histplot(adata.obs["total_counts"], kde=False, ax=axs[0])
sns.histplot(adata.obs["total_counts"][adata.obs["total_counts"] < 10000], kde=False, bins=40, ax=axs[1])
sns.histplot(adata.obs["n_genes_by_counts"], kde=False, bins=60, ax=axs[2])
sns.histplot(adata.obs["n_genes_by_counts"][adata.obs["n_genes_by_counts"] < 4000], kde=False, bins=60, ax=axs[3])

sc.pp.filter_cells(adata, min_counts=5000)
sc.pp.filter_cells(adata, max_counts=35000)
adata = adata[adata.obs["pct_counts_mt"] < 20].copy()
print(f"#cells after MT filter: {adata.n_obs}")
sc.pp.filter_genes(adata, min_cells=10)
```

We plot distributions of total counts and detected genes per spot to decide
appropriate cutoffs. Spots at the extremes are likely of poor quality or
technical artifacts. We remove spots with fewer than 5,000 or more than 35,000
total counts, and spots where more than 20% of counts come from mitochondrial
genes. Genes detected in fewer than 10 spots are also removed.

### 4. Normalization and HVG Selection

```python
sc.pp.normalize_total(adata, inplace=True)
sc.pp.log1p(adata)
sc.pp.highly_variable_genes(adata, flavor="seurat", n_top_genes=2000)
```

`normalize_total` scales each spot to the same total count, removing
technical differences in sequencing depth. `log1p` applies a log(x+1)
transformation to stabilize variance. `highly_variable_genes` selects
the 2,000 genes that vary the most across spots, which are most likely
to carry biological signal.

### 5. Dimensionality Reduction and Clustering

```python
sc.pp.pca(adata)
sc.pp.neighbors(adata)
sc.tl.umap(adata)
sc.tl.leiden(adata, key_added="clusters", flavor="igraph", directed=False, n_iterations=2)
```

PCA compresses the gene dimensions into 50 principal components. `neighbors`
builds a k-nearest-neighbor graph in PCA space. UMAP uses this graph to
produce a 2D embedding for visualization. Leiden clustering groups spots into
communities based on transcriptional similarity.

### 6. UMAP Visualization

```python
plt.rcParams["figure.figsize"] = (4, 4)
sc.pl.umap(adata, color=["total_counts", "n_genes_by_counts", "clusters"], wspace=0.4)
```

### 7. Spatial Visualization

```python
plt.rcParams["figure.figsize"] = (8, 8)
sc.pl.spatial(adata, img_key="hires", color=["total_counts", "n_genes_by_counts"])
sc.pl.spatial(adata, img_key="hires", color="clusters", size=1.5)
sc.pl.spatial(adata, img_key="hires", color="clusters",
              groups=["5", "9"], crop_coord=[7000, 10000, 0, 6000], alpha=0.5, size=1.3)
```

`sc.pl.spatial` overlays colored circles on top of the H&E tissue image,
one circle per Visium spot. We crop to specific regions and reduce spot
opacity so the underlying tissue morphology shows through.

### 8. Marker Genes

```python
sc.tl.rank_genes_groups(adata, "clusters", method="t-test")
sc.pl.rank_genes_groups_heatmap(adata, groups="9", n_genes=10, groupby="clusters")
sc.pl.spatial(adata, img_key="hires", color=["clusters", "CR2"])
sc.pl.spatial(adata, img_key="hires", color=["COL1A2", "SYPL1"], alpha=0.7)
```

`rank_genes_groups` runs a t-test for each cluster versus all others to find
differentially expressed genes. We plot the top 10 markers for cluster 9 as
a heatmap and then confirm their spatial expression matches the cluster's
tissue distribution.

## Results

### QC Histograms
![QC histograms](results/qc_histograms.jpeg)

Distribution of total counts and detected genes per spot, used to determine
appropriate filtering thresholds.

### UMAP
![UMAP clusters](results/umap_clusters.jpeg)

UMAP embedding colored by total counts, gene counts, and Leiden cluster,
confirming clusters are not driven by sequencing depth artifacts.

### Spatial Counts
![Spatial counts](results/spatial_counts.jpeg)

Total counts and number of detected genes overlaid on the H&E tissue image,
showing which tissue regions have higher transcriptional activity.

### Spatial Clusters
![Spatial clusters](results/spatial_clusters.jpeg)

Leiden clusters overlaid on the H&E tissue image, showing that
transcriptionally similar spots co-localize in the tissue.

### Zoomed Clusters
![Zoom](results/spatial_clusters_zoom.jpeg)

Cropped view focusing on clusters 5 and 9 with reduced spot opacity,
allowing the underlying tissue morphology to show through.

### Marker Gene Heatmap
![Heatmap](results/marker_genes_heatmap.jpeg)

Top 10 marker genes for cluster 9 across all clusters, showing
differential expression patterns used to characterize each cluster.

### CR2 Spatial Expression
![CR2](results/spatial_cr2.jpeg)

Spatial expression of CR2, the top marker gene for cluster 9, confirming
its expression pattern matches the spatial distribution of the cluster.

### COL1A2 and SYPL1
![COL1A2 SYPL1](results/spatial_col1a2_sypl1.jpeg)

Spatial expression of COL1A2 and SYPL1 across the tissue as further
examples of spatially organized gene expression.

## Reference

[Scanpy spatial tutorial](https://scanpy-tutorials.readthedocs.io/en/latest/spatial/basic-analysis.html)

Wolf et al. (2018) SCANPY: large-scale single-cell gene expression data analysis. *Genome Biology*. https://doi.org/10.1186/s13059-017-1382-0
