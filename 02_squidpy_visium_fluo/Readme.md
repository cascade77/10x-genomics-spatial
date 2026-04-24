# Visium Fluorescence Image Analysis with Squidpy

Image-based spatial analysis of a 10x Genomics Visium fluorescence dataset
using Squidpy's image processing pipeline. The notebook is fully documented
with detailed markdown explanations covering the biological rationale, what
each image processing operation does, and how to interpret the outputs.

## Dataset

Pre-processed mouse brain coronal section (Visium) with a 3-channel
fluorescence image loaded via `sq.datasets.visium_fluo_adata_crop()`.
No manual download is required.

| Channel | Stain | Target |
|---------|-------|--------|
| 0 | DAPI | DNA and nuclei |
| 1 | anti-NEUN | Neurons |
| 2 | anti-GFAP | Glial cells |

Cluster annotations were pre-computed using the standard Scanpy preprocessing
pipeline. The focus of this notebook is entirely on image feature extraction
and comparing those features to the gene expression clusters.

## Platform

All steps were run on Google Colab.

## Dependencies

```python
pip install scanpy squidpy igraph leidenalg scikit-image dask
```

## Workflow

### 1. Imports and Data Loading

```python
import pandas as pd
import matplotlib.pyplot as plt
import scanpy as sc
import squidpy as sq

img   = sq.datasets.visium_fluo_image_crop()
adata = sq.datasets.visium_fluo_adata_crop()
```

We load two objects. `adata` is an AnnData object containing gene expression
counts and pre-computed cluster labels. `img` is a Squidpy `ImageContainer`
holding the high-resolution fluorescence tissue image. The dataset is a
cropped region of the full brain section to keep computation fast while
demonstrating the complete pipeline.

### 2. Spatial Cluster Overview

```python
sq.pl.spatial_scatter(adata, color="cluster")
```

Before doing any image analysis, we visualize the pre-annotated cluster
labels in spatial coordinates. This provides a reference to compare against
once we extract image features.

### 3. Image Smoothing

```python
sq.im.process(img=img, layer="image", method="smooth")
```

We smooth the image using a Gaussian filter to reduce noise and small
intensity fluctuations that would otherwise cause the segmentation algorithm
to produce fragmented results. The smoothed image is stored as a new layer
called `image_smooth` inside the ImageContainer.

### 4. Nucleus Segmentation

```python
sq.im.segment(img=img, layer="image_smooth", method="watershed", channel=0, chunks=1000)

fig, ax = plt.subplots(1, 2)
img_crop = img.crop_corner(2000, 2000, size=500)
img_crop.show(layer="image", channel=0, ax=ax[0])
img_crop.show(layer="segmented_watershed", channel=0, ax=ax[1])
```

We segment the DAPI channel using the watershed algorithm, which treats the
image like a topographic map and finds boundaries between bright nuclei regions.
The result is a label image where each segmented nucleus has a unique integer ID.
We crop a small region to visually compare the raw DAPI channel against the
segmentation mask.

### 5. Segmentation Feature Extraction

```python
features_kwargs = {"segmentation": {"label_layer": "segmented_watershed"}}
sq.im.calculate_image_features(
    adata, img,
    features="segmentation",
    layer="image",
    key_added="features_segmentation",
    n_jobs=1,
    features_kwargs=features_kwargs,
)
```

For each Visium spot, we calculate features derived from the segmentation:
how many nuclei are under that spot, their average size, and the mean
fluorescence intensity per channel. This produces a new obs x features
matrix stored in `adata.obsm["features_segmentation"]`.

### 6. Comparing Image Features to Gene Expression Clusters

```python
sq.pl.spatial_scatter(
    sq.pl.extract(adata, "features_segmentation"),
    color=[
        "segmentation_label",
        "cluster",
        "segmentation_ch-0_mean_intensity_mean",
        "segmentation_ch-1_mean_intensity_mean",
    ],
    frameon=False,
    ncols=2,
)
```

`sq.pl.extract` pulls the image features into `adata.obs` so they can be
passed to the plotting function. We plot four panels: nucleus count per spot,
cluster label, mean DAPI intensity, and mean NEUN intensity. Agreement between
image features and gene-expression clusters confirms that both modalities
capture the same underlying tissue biology.

## Results

### Spatial Clusters
![Spatial clusters](results/spatial_clusters.jpeg)

Pre-annotated brain region clusters visualized in tissue space, serving
as the gene-expression reference for comparison against image features.

### Segmentation Comparison
![Segmentation](results/segmentation_comparison.jpeg)

Side-by-side comparison of the raw DAPI channel (left) and the watershed
segmentation mask (right). Each color in the mask corresponds to a uniquely
labeled nucleus.

### Image Features vs Gene Clusters
![Features](results/image_features_vs_clusters.jpeg)

Four-panel plot showing nucleus count per spot, cluster label, mean DAPI
intensity, and mean NEUN intensity. Agreement between image features and
gene-expression clusters confirms that both modalities are capturing the
same underlying tissue structure.

## Reference

[Squidpy Visium fluorescence tutorial](https://squidpy.readthedocs.io/en/stable/notebooks/tutorials/tutorial_visium_fluo.html)

Palla et al. (2022) Squidpy: a scalable framework for spatial omics analysis. *Nature Methods*. https://doi.org/10.1038/s41592-021-01358-2
