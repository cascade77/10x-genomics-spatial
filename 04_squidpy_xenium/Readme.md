# Xenium Single-Cell Spatial Analysis with Squidpy

Single-cell resolution spatial transcriptomics analysis of 10x Genomics
Xenium data using Squidpy. Unlike Visium where each measurement spot covers
a 55-micron area containing multiple cells, Xenium assigns transcripts to
individually segmented cells, giving true single-cell spatial resolution.
The notebook is fully documented with detailed explanations of every step,
including how Xenium differs from Visium methodologically and why the
analysis choices (Delaunay triangulation, subsampling, Moran's I) are made.

## Dataset

FFPE Human Lung Cancer Xenium dataset (2 FOVs), downloaded directly from
10x Genomics:

```bash
curl -O https://cf.10xgenomics.com/samples/xenium/2.0.0/Xenium_V1_human_Lung_2fov/Xenium_V1_human_Lung_2fov_outs.zip
```

| Property | Value |
|----------|-------|
| Tissue | FFPE Human Lung Cancer |
| Panel | Xenium Human Multi-Tissue and Cancer Panel |
| Genes | 377 |
| XOA version | 2.0.0 |
| FOVs | 2 |

## Workflow

1. Load Xenium output directory via `spatialdata-io` into a SpatialData object
2. QC — calculate negative control probe percentage and decoding error rate
   as Xenium-specific quality metrics alongside standard count-based metrics
3. Normalize, select highly variable genes, run PCA, UMAP, and Leiden clustering
4. Build spatial neighbor graph using Delaunay triangulation — required for
   single-cell data where cells are irregularly positioned, unlike the regular
   hexagonal grid used for Visium spots
5. Compute centrality scores per cluster — degree, closeness, and average
   centrality describe each cluster's spatial role in the tissue graph
6. Co-occurrence analysis on a 50% subsample for computational efficiency
7. Spatial autocorrelation using Moran's I to identify genes whose expression
   is spatially organized rather than randomly distributed
8. Visualize Leiden clusters in single-cell spatial coordinates

## Notebook

The notebook (`xenium.ipynb`) contains markdown explanations before every
code block. Particular emphasis is placed on explaining the key differences
between Xenium and Visium analysis — why QC metrics differ, why Delaunay
triangulation is used instead of grid-based neighbors, and what single-cell
spatial resolution means for interpreting co-occurrence and centrality results
compared to spot-based data.

## Results

### Centrality Scores
![Centrality scores](results/centrality_scores.jpeg)

Average clustering, closeness centrality, and degree centrality for each
Leiden cluster. Clusters with high closeness and degree centrality are
spatially central in the tissue — surrounded by many other cells across
multiple cluster types. Peripheral clusters score low on all three metrics.

### Co-occurrence
![Co-occurrence](results/co_occurrence.jpeg)

Co-occurrence probability ratio as a function of distance, conditioned on
cluster 1. Each line shows how likely a given cluster is to appear near
cluster 1 relative to chance. Values above 1.0 at short distances indicate
spatial enrichment; the drop toward 1.0 at longer distances confirms the
effect is local rather than global.

### Spatial Clusters
![Spatial clusters](results/spatial_clusters.jpeg)

Leiden clusters visualized in single-cell spatial coordinates. Each dot
is one segmented cell. The two FOVs of the lung cancer section are visible,
with cluster boundaries reflecting both tumor and microenvironment structure.

## Reference

[Squidpy Xenium tutorial](https://squidpy.readthedocs.io/en/stable/notebooks/tutorials/tutorial_xenium.html)
