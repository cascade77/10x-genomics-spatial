# 10x Genomics Spatial Transcriptomics Analysis

Spatial transcriptomics analysis of 10x Genomics Visium and Xenium data using Scanpy and Squidpy.

## Overview

This repository contains four analysis notebooks covering spatial transcriptomics workflows
using the 10x Genomics Visium and Xenium platforms. Each notebook follows the corresponding
official tutorial, running on Google Colab with publicly available datasets.

The analyses cover the full pipeline from raw count loading and QC through normalization,
clustering, and spatial statistics, with outputs saved per notebook in their respective
`results/` folders.

## Repository Structure

```
10x-genomics-spatial/
│
├── 01_scanpy_basic/                        # Basic Scanpy spatial analysis
│   ├── scanpy_basic_spatial.ipynb          # Main analysis notebook
│   ├── README.md                           # Notebook-level documentation
│   └── results/                            # Output plots
│       ├── qc_histograms.jpeg              # Total counts and gene count distributions
│       ├── umap_clusters.jpeg              # UMAP colored by counts and Leiden clusters
│       ├── spatial_counts.jpeg             # Total counts and genes overlaid on tissue
│       ├── spatial_clusters.jpeg           # Leiden clusters overlaid on H&E image
│       ├── spatial_clusters_zoom.jpeg      # Cropped view of clusters 5 and 9
│       ├── marker_genes_heatmap.jpeg       # Top 10 marker genes for cluster 9
│       ├── spatial_cr2.jpeg                # Spatial expression of CR2
│       └── spatial_col1a2_sypl1.jpeg       # Spatial expression of COL1A2 and SYPL1
│
├── 02_squidpy_visium_fluo/                 # Visium fluorescence image analysis
│   ├── visium_fluorescence.ipynb           # Main analysis notebook
│   ├── README.md                           # Notebook-level documentation
│   └── results/                            # Output plots
│       ├── spatial_clusters.jpeg           # Pre-annotated clusters in tissue space
│       ├── segmentation_comparison.jpeg    # DAPI channel vs watershed segmentation mask
│       └── image_features_vs_clusters.jpeg # Image features compared to gene clusters
│
├── 03_squidpy_visium_hne/                  # Visium H&E spatial graph analysis
│   ├── visium_hne.ipynb                    # Main analysis notebook
│   ├── README.md                           # Notebook-level documentation
│   └── results/                            # Output plots
│       ├── spatial_clusters.jpeg           # Annotated brain regions in tissue space
│       ├── neighborhood_enrichment.jpeg    # Cluster pair enrichment heatmap
│       └── co_occurrence_hippocampus.jpeg  # Co-occurrence probability from Hippocampus
│
├── 04_squidpy_xenium/                      # Xenium single-cell spatial analysis
│   ├── xenium.ipynb                        # Main analysis notebook
│   ├── README.md                           # Notebook-level documentation
│   └── results/                            # Output plots
│       ├── centrality_scores.jpeg          # Average, closeness, degree centrality per cluster
│       ├── co_occurrence.jpeg              # Co-occurrence probability from cluster 1
│       └── spatial_clusters.jpeg          # Leiden clusters in single-cell spatial coordinates
│
├── LICENSE                                 # License file
└── README.md                               # Main repository documentation
```

## Notebooks

| # | Notebook | Dataset | Focus |
|---|----------|---------|-------|
| 1 | [Basic Scanpy Spatial](01_scanpy_basic/) | Human Lymph Node (Visium) | QC, clustering, spatial visualization, marker genes |
| 2 | [Visium Fluorescence](02_squidpy_visium_fluo/) | Mouse Brain (Visium + fluorescence) | Image segmentation, segmentation features |
| 3 | [Visium H&E](03_squidpy_visium_hne/) | Mouse Brain (Visium + H&E) | Neighborhood enrichment, co-occurrence, ligand-receptor |
| 4 | [Xenium](04_squidpy_xenium/) | Human Lung Cancer (Xenium) | Single-cell spatial, centrality, co-occurrence, Moran's I |

## Platform

All notebooks were run on Google Colab.

## Dependencies

```bash
pip install scanpy squidpy igraph leidenalg scikit-image dask
pip install spatialdata spatialdata-io spatialdata-plot  # notebook 4 only
```

## References

- [Scanpy spatial tutorial](https://scanpy-tutorials.readthedocs.io/en/latest/spatial/basic-analysis.html)
- [Squidpy Visium fluorescence tutorial](https://squidpy.readthedocs.io/en/stable/notebooks/tutorials/tutorial_visium_fluo.html)
- [Squidpy Visium H&E tutorial](https://squidpy.readthedocs.io/en/stable/notebooks/tutorials/tutorial_visium_hne.html)
- [Squidpy Xenium tutorial](https://squidpy.readthedocs.io/en/stable/notebooks/tutorials/tutorial_xenium.html)

