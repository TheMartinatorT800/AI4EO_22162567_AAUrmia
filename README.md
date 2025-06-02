# Lake Urmia Surface & Vegetation Analysis with Remote Sensing & ML

This project investigates the surface water variability of **Lake Urmia between 2016 and 2025** at three-year intervals, and its potential ecological impact on surrounding vegetation, using remote sensing techniques. Leveraging **Sentinel-2 imagery**, we can compute the **Normalized Difference Water Index (NDWI)** to delineate the lake’s surface area and the **Normalized Difference Vegetation Index (NDVI)** to assess vegetation health and coverage in the surrounding basin. By masking clouds and non-land pixels using SentinelHub’s `dataMask`, we can ensure accurate spatiotemporal comparisons. For water classification, a threshold of **NDWI > 0.2** is validated through histogram analysis across years. Vegetation analysis is refined by excluding water pixels and computing mean NDVI values per year. To further explore landscape segmentation, **K-Means clustering** is applied to NDVI and NDWI datasets combined, classifying the region into water, vegetation, and bare terrain. A correlation analysis is then performed to examine how changes in lake surface area affected vegetation indices over time. The goal of this project is to provide a reproducible Earth Observation framework for monitoring inland water bodies and understanding their environmental influence using satellite-derived spectral indices.

<br>

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

- ### Normalized Difference Water Index (NDWI)

The **Normalized Difference Water Index (NDWI)** is a spectral index used to detect and analyze surface water bodies in satellite imagery. It utilizes the reflectance properties of green light (Band B03) and near-infrared (NIR, Band B08), leveraging the fact that water strongly reflects green light while absorbing NIR. The formula used is:

$$
\text{NDWI} = \frac{\text{Green - NIR}}{\text{Green + NIR}}
$$

This formula enhances the distinction between water and non-water features. NDWI values typically range between -1 and 1. Values **above 0.2** are generally indicative of water bodies, while values below that suggest land, vegetation, or built-up areas. In this project, NDWI was calculated using Sentinel-2 imagery and a threshold of 0.2 was applied to classify water presence accurately.

![NDWI Figure](Images/Repository%20figures/NDWI%20figure.png)
  
- ### Normalized Difference Vegetation Index (NDVI)

The **Normalized Difference Vegetation Index (NDVI)** is a widely used remote sensing index that quantifies vegetation health and density by measuring the difference in reflectance between the Near-Infrared (NIR) and Red bands of the electromagnetic spectrum. Vegetation strongly reflects NIR light while absorbing red light due to chlorophyll absorption during photosynthesis. NDVI is calculated using the formula:

$$
\text{NDVI} = \frac{\text{NIR} - \text{Red}}{\text{NIR} + \text{Red}}
$$

For Sentinel-2 imagery, **Band 8 (B08)** is used for NIR and **Band 4 (B04)** for the Red spectrum. The resulting NDVI values range from **-1 to 1**, where values closer to 1 indicate dense, healthy vegetation, values near 0 represent bare soil or sparsely vegetated areas, and negative values typically correspond to water, snow, or clouds. This index allows for temporal analysis of vegetation changes and land cover classification across time.

![NDVI figure](Images/Repository%20figures/NDVI%20figure.png)

- **Cloud and No-Data Masking**:  
  Implemented using Sentinel Hub’s `dataMask` layer to exclude invalid pixels from analysis.

- **Water Surface Area Calculation**:  
  Surface area computed from NDWI raster masks using pixel resolution metadata.

- **NDWI Histograms**:  
  To visualize threshold effectiveness and value distributions per year.

- **K-Means Clustering**:  
  Applied to combined NDVI + NDWI pixel vectors to segment land cover into 3 classes:  
  **Water, Vegetation, and Other Terrain**.
  
![K-Means Clustering](Images/Repository%20figures/K-Means%20figure.png)

---

## 📦 Data Collection

Data was sourced and downloaded from [Sentinel Hub EO Browser](https://dataspace.copernicus.eu/browser/?zoom=11&lat=45.36638&lng=12.49832&themeId=DEFAULT-THEME&visualizationUrl=https%3A//sh.dataspace.copernicus.eu/ogc/wms/a1343b61-3f53-4c92-b65c-0b432b3e7af6&datasetId=S2_L1C_CDAS&fromTime=2023-02-07T00%3A00%3A00.000Z&toTime=2023-02-07T23%3A59%3A59.999Z&layerId=1_TRUE_COLOR&demSource3D=%22MAPZEN%22&cloudCoverage=10), using **Sentinel-2 L2A** data for the following years:

- **2016**
- **2019**
- **2022**
- **2025**

You can inspect and download all images used for this project in the **Images** folder within this repository or by accessing the [Google Drive Folder](https://drive.google.com/drive/folders/1Uosew5NTOYnKxY6biy_UHgh0c_63GKGA?usp=share_link).

### 🎯 Custom Script Used (Sentinel Hub JavaScript)

To export multiple bands (B08, B04, B03) and include a data mask in 32-bit float precision, the following custom scripts were used in Copernicus Browser under **Custom** > **Custom script**:

#### For NDWI

```javascript
//VERSION=3
function setup() {
  return {
    input: ["B03", "B08", "dataMask"],
    output: {
      bands: 3,
      sampleType: "FLOAT32"
    }
  };
}

function evaluatePixel(sample) {
  return [sample.B03, sample.B08, sample.dataMask];
}
```

#### For NDVI

```javascript
//VERSION=3
function setup() {
  return {
    input: ["B08", "B04", "dataMask"],
    output: {
      bands: 3,
      sampleType: "FLOAT32"
    }
  };
}

function evaluatePixel(sample) {
  return [sample.B08, sample.B04, sample.dataMask];
}
```












