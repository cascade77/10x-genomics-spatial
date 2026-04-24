# 10x Genomics Spatial Transcriptomics Analysis

Spatial transcriptomics analysis of 10x Genomics Visium and Xenium data
using Scanpy and Squidpy. This repository contains four fully documented
analysis notebooks, each following the corresponding official tutorial and
running on Google Colab with publicly available datasets.

Each notebook contains detailed markdown explanations before every code
block, covering the biological rationale behind each step, what the
code does to the data, and how to interpret the outputs. All result
plots are saved in the `results/` folder of each subdirectory and
documented in the individual READMEs.

> See the individual directory READMEs for detailed workflow descriptions,
> full code walkthroughs, and result interpretations for each notebook.

---

## Overview

Spatial transcriptomics technologies allow gene expression to be measured
while preserving the physical location of each measurement in the tissue.
This repository covers two major 10x Genomics platforms:

**Visium** captures RNA from 55-micron spots arrayed across a tissue section.
Each spot may contain multiple cells, and the accompanying tissue image (H&E
or fluorescence) provides morphological context for the transcriptomic data.

**Xenium** is a single-cell resolution in situ technology that assigns
transcripts to individually segmented cells, giving true single-cell spatial
resolution at scale.

The four notebooks progress from basic Visium analysis with Scanpy, through
image-based feature extraction and spatial graph statistics with Squidpy,
to single-cell resolution Xenium analysis using the spatialdata ecosystem.

---

## Repository Structure

```
10x-genomics-spatial/
│
├── 01_scanpy_basic/                        # Basic Scanpy spatial analysis
│   ├── scanpy_basic_spatial.ipynb          # Main analysis notebook
│   ├── Readme.md                           # Detailed workflow and code walkthrough
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
│   ├── Readme.md                           # Detailed workflow and code walkthrough
│   └── results/                            # Output plots
│       ├── spatial_clusters.jpeg           # Pre-annotated clusters in tissue space
│       ├── segmentation_comparison.jpeg    # DAPI channel vs watershed segmentation mask
│       └── image_features_vs_clusters.jpeg # Image features compared to gene clusters
│
├── 03_squidpy_visium_hne/                  # Visium H&E spatial graph analysis
│   ├── visium_hne.ipynb                    # Main analysis notebook
│   ├── Readme.md                           # Detailed workflow and code walkthrough
│   └── results/                            # Output plots
│       ├── spatial_clusters.jpeg           # Annotated brain regions in tissue space
│       ├── neighborhood_enrichment.jpeg    # Cluster pair enrichment heatmap
│       └── co_occurrence_hippocampus.jpeg  # Co-occurrence probability from Hippocampus
│
├── 04_squidpy_xenium/                      # Xenium single-cell spatial analysis
│   ├── xenium.ipynb                        # Main analysis notebook
│   ├── README.md                           # Detailed workflow and code walkthrough
│   └── results/                            # Output plots
│       ├── centrality_scores.jpeg          # Average, closeness, degree centrality per cluster
│       ├── co_occurrence.jpeg              # Co-occurrence probability from cluster 1
│       └── spatial_clusters.jpeg           # Leiden clusters in single-cell spatial coordinates
│
├── LICENSE                                 # License file
└── README.md                               # Main repository documentation
```

---

## Notebooks

| # | Notebook | Dataset | Focus |
|---|----------|---------|-------|
| 1 | [Basic Scanpy Spatial](01_scanpy_basic/) | Human Lymph Node (Visium) | QC, clustering, spatial visualization, marker genes |
| 2 | [Visium Fluorescence](02_squidpy_visium_fluo/) | Mouse Brain (Visium + fluorescence) | Image segmentation, segmentation features |
| 3 | [Visium H&E](03_squidpy_visium_hne/) | Mouse Brain (Visium + H&E) | Neighborhood enrichment, co-occurrence, ligand-receptor |
| 4 | [Xenium](04_squidpy_xenium/) | Human Lung Cancer (Xenium) | Single-cell spatial, centrality, co-occurrence, Moran's I |

---

## Results

### Notebook 1: Basic Scanpy Spatial Analysis

Human lymph node Visium section. QC, normalization, clustering, and
visualization of Leiden clusters overlaid on the H&E tissue image.

![Spatial clusters](01_scanpy_basic/results/spatial_clusters.jpeg)

![UMAP clusters](01_scanpy_basic/results/umap_clusters.jpeg)

![Marker gene heatmap](01_scanpy_basic/results/marker_genes_heatmap.jpeg)

---

### Notebook 2: Visium Fluorescence Image Analysis

Mouse brain fluorescence Visium section. Nucleus segmentation on the DAPI
channel using watershed and comparison of image-derived features against
gene expression cluster annotations.

![Segmentation comparison](02_squidpy_visium_fluo/results/segmentation_comparison.jpeg)

![Image features vs clusters](02_squidpy_visium_fluo/results/image_features_vs_clusters.jpeg)

---

### Notebook 3: Visium H&E Spatial Graph Analysis

Mouse brain H&E Visium section. Neighborhood enrichment heatmap and
co-occurrence probability from the Hippocampus cluster across distance bins.

![Neighborhood enrichment](03_squidpy_visium_hne/results/neighborhood_enrichment.jpeg)

![Co-occurrence Hippocampus](03_squidpy_visium_hne/results/co_occurrence_hippocampus.jpeg)

---

### Notebook 4: Xenium Single-Cell Spatial Analysis

Human lung cancer Xenium dataset (2 FOVs, 11,898 cells, 377-gene panel).
Centrality scores, co-occurrence, and single-cell spatial cluster visualization.

![Centrality scores](04_squidpy_xenium/results/centrality_scores.jpeg)

![Spatial clusters](04_squidpy_xenium/results/spatial_clusters.jpeg)

---

## Platform

All notebooks were run on Google Colab.

---

## Dependencies

```python
# Notebooks 1, 2, 3
pip install scanpy squidpy igraph leidenalg scikit-image dask

# Notebook 4 only
pip install spatialdata spatialdata-io spatialdata-plot
```

---

## Citations

Wolf et al. (2018) SCANPY: large-scale single-cell gene expression data
analysis. *Genome Biology*.
https://doi.org/10.1186/s13059-017-1382-0

Palla et al. (2022) Squidpy: a scalable framework for spatial omics
analysis. *Nature Methods*.
https://doi.org/10.1038/s41592-021-01358-2

Virshup et al. (2021) anndata: Annotated data.
https://doi.org/10.1101/2021.12.16.473007

McInnes et al. (2018) UMAP: Uniform Manifold Approximation and Projection
for Dimension Reduction.
https://arxiv.org/abs/1802.03426

Traag et al. (2019) From Louvain to Leiden: guaranteeing well-connected
communities. *Scientific Reports*.
https://doi.org/10.1038/s41598-019-41695-z

---

## References

[Scanpy spatial tutorial](https://scanpy-tutorials.readthedocs.io/en/latest/spatial/basic-analysis.html)

[Squidpy Visium fluorescence tutorial](https://squidpy.readthedocs.io/en/stable/notebooks/tutorials/tutorial_visium_fluo.html)

[Squidpy Visium H&E tutorial](https://squidpy.readthedocs.io/en/stable/notebooks/tutorials/tutorial_visium_hne.html)

[Squidpy Xenium tutorial](https://squidpy.readthedocs.io/en/stable/notebooks/tutorials/tutorial_xenium.html)

[10x Genomics Xenium Human Lung Cancer Dataset](https://www.10xgenomics.com/datasets/xenium-ffpe-human-lung-cancer)
