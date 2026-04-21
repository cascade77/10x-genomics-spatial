# Basic Spatial Analysis with Scanpy

Analysis of 10x Genomics Visium spatial transcriptomics data using Scanpy.

## Dataset

Human lymph node Visium section, downloaded automatically via `sc.datasets.visium_sge()`.

## Workflow

1. Load Visium data into AnnData
2. QC — filter by total counts, gene counts, and mitochondrial percentage
3. Normalize and select highly variable genes
4. PCA → neighbor graph → UMAP → Leiden clustering
5. Visualize clusters overlaid on H&E tissue image
6. Identify cluster marker genes

## Results

### QC Histograms
![QC histograms](results/qc_histograms.jpeg)

Distribution of total counts and genes per spot before and after filtering.

### UMAP
![UMAP clusters](results/umap_clusters.jpeg)

UMAP embedding colored by total counts, gene counts, and Leiden cluster.

### Spatial Counts
![Spatial counts](results/spatial_counts.jpeg)

Total counts and number of detected genes overlaid on the H&E tissue image.

### Spatial Clusters
![Spatial clusters](results/spatial_clusters.jpeg)

Leiden clusters overlaid on the H&E tissue image.

### Zoomed Clusters
![Zoom](results/spatial_clusters_zoom.jpeg)

Cropped view focusing on clusters 5 and 9 with reduced spot opacity.

### Marker Gene Heatmap
![Heatmap](results/marker_genes_heatmap.jpeg)

Top 10 marker genes for cluster 9 across all clusters.

### CR2 Spatial Expression
![CR2](results/spatial_cr2.jpeg)

Spatial expression of CR2, the top marker gene for cluster 9.

### COL1A2 and SYPL1
![COL1A2 SYPL1](results/spatial_col1a2_sypl1.jpeg)

Spatial expression of COL1A2 and SYPL1 across the tissue.

## Reference

[Scanpy spatial tutorial](https://scanpy-tutorials.readthedocs.io/en/latest/spatial/basic-analysis.html)
