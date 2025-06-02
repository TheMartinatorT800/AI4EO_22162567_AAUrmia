# AI4EO_22162567_Unsupervised_Learning_Classification_at_Lake_Urmia


# 🌍 Lake Urmia Surface & Vegetation Analysis with Remote Sensing & ML

This project explores the use of spectral indices (NDWI & NDVI) and unsupervised machine learning (K-Means) to monitor the **variability of Lake Urmia’s surface area** and its correlation with surrounding **vegetation coverage** over time. Sentinel-2 satellite data was processed to extract relevant land cover insights for the years **2016, 2019, 2022, and 2025**.

---

## 📌 Table of Contents

- [Overview](#overview)
- [Techniques Used](#techniques-used)
- [Data Collection](#data-collection)
- [Getting Started](#getting-started)
- [Usage](#usage)
- [Results](#results)
- [Discussion](#discussion)
- [Limitations](#limitations)
- [License](#license)
- [Acknowledgments](#acknowledgments)

---

## 🧭 Overview

The goal of this project is to **detect and quantify surface water changes in Lake Urmia** using satellite imagery and evaluate how these changes impact surrounding vegetation patterns. The project applies **remote sensing indices** (NDWI & NDVI) and **unsupervised classification (K-means)** to provide a comprehensive spatio-temporal analysis of hydrological and environmental variation.

---

## ⚙️ Techniques Used

The following techniques and methodologies were applied:

- **NDWI (Normalized Difference Water Index)**:  
  Used to delineate water bodies using Sentinel-2 Bands B03 (Green) and B08 (NIR).
  
- **NDVI (Normalized Difference Vegetation Index)**:  
  Applied to assess surrounding vegetation using Bands B08 (NIR) and B04 (Red).

- **Cloud and No-Data Masking**:  
  Implemented using Sentinel Hub’s `dataMask` layer to exclude invalid pixels from analysis.

- **Water Surface Area Calculation**:  
  Surface area computed from NDWI raster masks using pixel resolution metadata.

- **NDWI Histograms**:  
  To visualize threshold effectiveness and value distributions per year.

- **K-Means Clustering**:  
  Applied to combined NDVI + NDWI pixel vectors to segment land cover into 3 classes:  
  **Water, Vegetation, and Other Terrain**.

---

## 📦 Data Collection

Data was sourced and downloaded from [Sentinel Hub EO Browser](https://apps.sentinel-hub.com/eo-browser/), using **Sentinel-2 L2A** data for the following years:

- **2016**
- **2019**
- **2022**
- **2025**

### 🎯 Custom Script Used (Sentinel Hub JavaScript)

To export multiple bands (B08, B04, B03) and include a data mask in 32-bit float precision, the following custom script was used in EO Browser under **Custom** > **Advanced script**:

```javascript
//VERSION=3
function setup() {
  return {
    input: ["B08", "B04", "B03", "dataMask"],
    output: { bands: 4, sampleType: "FLOAT32" }
  };
}

function evaluatePixel(sample) {
  return [sample.B08, sample.B04, sample.B03, sample.dataMask];
}

















