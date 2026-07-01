## Spatial Resolution Enhancement of Multispectral Satellite Imagery via Brovey Transform based Sharpening technique.

<img width="1920" height="700" alt="Figure_1" src="https://github.com/user-attachments/assets/d2c8a1ba-09f2-42b1-8317-c1293048f0b1" />


## Overview

This repository provides a reproducible, end-to-end Python pipeline for enhancing the spatial resolution of multispectral satellite imagery. By fusing 30-meter Landsat 8 multispectral data with its corresponding 15-meter panchromatic band, this tool generates high-resolution, color-rich outputs vital for detailed spatial analysis and feature extraction.

Moving beyond standard image manipulation, this project integrates a quantitative validation framework. It benchmarks the structural integrity of the sharpened Landsat output against a high-resolution 10-meter Sentinel-2 reference image using edge magnitude correlation.

## The Process: Methodology & Mathematical Fusion

The core challenge in multi-sensor Earth Observation is resolving spatial disparities without distorting critical spectral signatures. This pipeline addresses this through a structured fusion process:

### 1. Spatial Coregistration
Before fusion can occur, data from disparate sensors must be perfectly aligned. The pipeline utilizes a central Area of Interest (AOI)—specifically Central Park, New York—as the anchor. All rasters are dynamically reprojected to a common Coordinate Reference System (CRS) and clipped to the exact bounding box of the vector geometry. 

### 2. The Brovey Transform (Image Fusion)
To achieve the pan-sharpening, the pipeline employs the Brovey Transform (also known as Color Normalized Transform). This method normalizes the multispectral bands and multiplies them by the high-resolution panchromatic band to inject spatial detail. 

The mathematical operation applied across the pixel arrays is:
$Band_{sharp} = \frac{Band_{MS}}{R_{MS} + G_{MS} + B_{MS}} \times PAN$

### 3. Edge-Based Quality Validation
Visual inspection is subjective. To objectively measure the success of the spatial enhancement, the pipeline extracts the gradient edges (calculating changes in pixel intensity) from both the pan-sharpened output and the 10-meter Sentinel-2 reference. By computing the correlation coefficient between these two edge maps, we derive a quantitative metric of structural alignment and sharpness.

## The Script: Architecture and Dependencies

The core logic is contained within `Pan_sharpening.py`, designed as a sequential, vectorized workflow for efficient raster processing.

### Key Functional Blocks:
* **Vectorized Data Loading:** Utilizes `rioxarray` for efficient, lazy-loading of `.TIF` and `.jp2` files, preserving crucial metadata (CRS, transforms).
* **Automated Spatial Operations:** Employs `geopandas` and `shapely` to define the AOI bounding box, automatically handling reprojection between WGS84 and the local UTM zones of the satellite data.
* **Matrix Operations:** Relies on `numpy` for high-performance tensor manipulations, specifically for calculating the Brovey transform arrays and avoiding division-by-zero errors via epsilon injection (`1e-6`).
* **Percentile Normalization:** Includes a custom display stretching function that clips data to the 2nd and 98th percentiles, ensuring consistent and optimized visualization across differently calibrated sensors.

### Requirements

* `python >= 3.9`
* `rioxarray`
* `geopandas`
* `shapely`
* `numpy`
* `matplotlib`

## How to Use

**1. Environment Setup:**
Clone the repository and install the necessary dependencies.

pip install -r requirements.txt

```bash

data/
├── Landsat/
│   ├── LC08_L1TP_..._B8.TIF (Pan)
│   ├── LC08_L1TP_..._B2.TIF (Blue)
│   └── ...
└── sentinel/GRANULE/.../IMG_DATA/R10m/
    ├── T18TWL_..._B02_10m.jp2 (Blue)
    └── ...
```

## Results & Validation

Upon execution, the pipeline produces both visual enhancements and quantitative validation metrics to verify the accuracy of the spatial fusion.

### 1. Visual Comparison
The script generates a side-by-side comparison (Figure 1) to visually inspect the resolution enhancement. 

* **Landsat MS (30 m):** The original multispectral imagery, stretched to the target grid, showing pixelation and lack of high-frequency detail.
* **Pan-Sharpened Landsat (15 m):** The output of the Brovey Transform. This image demonstrates significantly clearer definitions of urban infrastructure, pathways, and water body boundaries within Central Park.
* **Sentinel-2 Reference (10 m):** The baseline high-resolution imagery used for visual and algorithmic benchmarking.

<img width="1920" height="700" alt="Figure_1" src="https://github.com/user-attachments/assets/d2c8a1ba-09f2-42b1-8317-c1293048f0b1" />

### 2. Quantitative Edge Metrics
Visual inspection can be subjective, so the pipeline includes an objective structural validation step (Figure 2). 

By extracting the gradient edges (calculating changes in pixel intensity) from both the 15m Pan-Sharpened output and the 10m Sentinel-2 baseline, we can measure how well the structural integrity was maintained during fusion.

* **Edge Correlation Coefficient:** The script logs a correlation metric to the console. A high correlation indicates that the Brovey transform successfully reconstructed the high-frequency spatial details (edges) present in the actual physical environment (as captured by Sentinel-2), mathematically validating the fusion technique.

<img width="1200" height="500" alt="Figure_2" src="https://github.com/user-attachments/assets/a23c29d2-04af-4068-b4bb-20f953055cb3" />
