# Visium H&E Spatial Graph Analysis with Squidpy

Spatial graph statistics analysis of a 10x Genomics Visium H&E dataset
using Squidpy. The focus here is on the spatial relationships between
transcriptionally defined clusters: which clusters neighbor each other,
which co-occur at short distances, and which genes are likely being
communicated between adjacent clusters. The notebook is fully documented
with detailed markdown explanations of every analysis step, the statistical
methods used, and how to interpret each output plot.

## Dataset

Pre-processed mouse brain coronal section (Visium) with H&E staining,
loaded via `sq.datasets.visium_hne_adata()`. Cluster annotations cover
14 distinct brain regions including cortical layers, hippocampus,
hypothalamus, thalamus, striatum, and fiber tracts. No manual download
is required.

## Platform

All steps were run on Google Colab.

## Dependencies

```python
pip install scanpy squidpy igraph leidenalg scikit-image dask
```

## Workflow

### 1. Imports and Data Loading

```python
import scanpy as sc
import anndata as ad
import squidpy as sq
import numpy as np
import pandas as pd

img   = sq.datasets.visium_hne_image()
adata = sq.datasets.visium_hne_adata()
sq.pl.spatial_scatter(adata, color="cluster")
```

### 2. Image Summary Features

```python
sq.im.calculate_image_features(
    adata, img,
    features="summary",
    key_added="features_summary",
    n_jobs=1,
)
```

Even with H&E data we can extract image-level statistics per spot such as
mean intensity, standard deviation, and percentile values of pixel intensities
under each spot. These summary features can be used to see whether tissue
morphology correlates with gene expression clusters.

### 3. Spatial Neighbor Graph

```python
sq.gr.spatial_neighbors(adata, coord_type="grid", n_rings=1)
```

For Visium data, spots sit on a regular hexagonal grid, so we use
`coord_type="grid"` and `n_rings=1` which connects each spot to its
immediate surrounding ring of spots. The result is a connectivity matrix
stored in `adata.obsp["connectivities"]`. All subsequent spatial statistics
are computed on this graph.

### 4. Neighborhood Enrichment

```python
sq.gr.nhood_enrichment(adata, cluster_key="cluster")
sq.pl.nhood_enrichment(adata, cluster_key="cluster")
```

This asks: do spots from cluster A tend to be neighbors of spots from
cluster B more than you would expect by chance? The score is computed by
permutation: cluster labels are shuffled many times and the observed
co-adjacency is compared against the shuffled distribution. A high positive
score means two clusters are spatially enriched neighbors. A low negative
score means they tend to be spatially separated.

### 5. Co-occurrence Analysis

```python
sq.gr.co_occurrence(adata, cluster_key="cluster")
sq.pl.co_occurrence(adata, cluster_key="cluster", clusters="Hippocampus", figsize=(8, 4))
```

Co-occurrence measures how the probability of observing cluster B changes as
you move further away from a spot in cluster A across a range of distance bins.
A ratio above 1.0 at short distances means the two clusters tend to co-occur
spatially. A ratio near 1.0 at long distances means independence.

### 6. Ligand-Receptor Interaction Analysis

```python
sq.gr.ligrec(adata, n_perms=100, cluster_key="cluster")
sq.pl.ligrec(
    adata,
    cluster_key="cluster",
    source_groups="Hippocampus",
    target_groups=["Pyramidal_layer_dentate_gyrus", "Pyramidal_layer"],
    means_range=(0.3, float("inf")),
    alpha=1e-4,
    swap_axes=True,
)
```

`sq.gr.ligrec` tests whether ligand genes expressed in one cluster are paired
with matching receptor genes expressed in a spatially neighboring cluster,
giving a hypothesis about which clusters may be communicating via secreted
signaling molecules. We focus on the Hippocampus cluster as the source and
two pyramidal layer clusters as targets, filtering to only high-expression
and statistically significant pairs.

## Results

### Spatial Clusters
![Spatial clusters](results/spatial_clusters.jpeg)

Pre-annotated brain region clusters overlaid on the H&E tissue image,
showing 14 distinct anatomical regions across the coronal section.

### Neighborhood Enrichment
![Neighborhood enrichment](results/neighborhood_enrichment.jpeg)

Heatmap of enrichment scores for every cluster pair. Yellow cells indicate
pairs that are significantly enriched as spatial neighbors. The strong
diagonal confirms that each cluster is most enriched with itself, while
off-diagonal signals reveal biologically meaningful adjacencies such as
cortical layer transitions and hippocampal subregion boundaries.

### Co-occurrence Hippocampus
![Co-occurrence](results/co_occurrence_hippocampus.jpeg)

Co-occurrence probability ratio as a function of distance, conditioned on
the Hippocampus cluster. Values above 1.0 at short distances indicate
clusters that tend to co-occur spatially with the Hippocampus.

## Reference

[Squidpy Visium H&E tutorial](https://squidpy.readthedocs.io/en/stable/notebooks/tutorials/tutorial_visium_hne.html)

Palla et al. (2022) Squidpy: a scalable framework for spatial omics analysis. *Nature Methods*. https://doi.org/10.1038/s41592-021-01358-2
