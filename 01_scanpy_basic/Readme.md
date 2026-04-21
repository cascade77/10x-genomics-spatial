# Basic Spatial Analysis with Scanpy

Analysis of 10x Genomics Visium spatial transcriptomics data using Scanpy.
The notebook is fully documented with detailed explanations of each step,
covering the biological rationale behind QC thresholds, what each
preprocessing operation does to the data, and how to interpret the spatial
and UMAP visualizations.

## Dataset

Human lymph node Visium section, downloaded automatically via
`sc.datasets.visium_sge()`. The dataset contains 4,035 spots across
36,601 genes, covering a full lymph node section with the accompanying
high-resolution H&E tissue image.

## Workflow

1. Load Visium data into AnnData; counts, spatial coordinates, and H&E image
2. QC: flag mitochondrial genes, compute per-spot metrics, plot distributions,
   filter by total counts, gene counts, and mitochondrial percentage
3. Normalize total counts per spot and apply log1p transformation
4. Select top 2,000 highly variable genes using Seurat method
5. PCA → neighbor graph → UMAP → Leiden clustering
6. Visualize clusters overlaid on H&E tissue image, with cropping and
   transparency controls to inspect tissue morphology
7. Rank marker genes per cluster using t-test and visualize spatially

## Notebook

The notebook (`scanpy_basic_spatial.ipynb`) contains markdown explanations
before every code block describing what the step does, why it is done, and
what to look for in the output. It covers the AnnData structure for Visium
data, the reasoning behind each QC cutoff, the difference between UMAP
and spatial visualization, and how marker gene expression maps back onto
tissue structure.

## Results

### QC Histograms
![QC histograms](results/qc_histograms.jpeg)

Distribution of total counts and genes per spot before and after filtering,
used to determine appropriate cutoff thresholds.

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
that its expression pattern matches the spatial distribution of the cluster.

### COL1A2 and SYPL1
![COL1A2 SYPL1](results/spatial_col1a2_sypl1.jpeg)

Spatial expression of COL1A2 and SYPL1 across the tissue as further
examples of spatially organized gene expression.

## Reference

[Scanpy spatial tutorial](https://scanpy-tutorials.readthedocs.io/en/latest/spatial/basic-analysis.html)
