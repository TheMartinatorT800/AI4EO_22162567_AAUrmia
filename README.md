# Lake Urmia Surface & Vegetation Analysis with Remote Sensing & ML

This project investigates the surface water variability of **Lake Urmia between 2016 and 2025** at three-year intervals, and its potential ecological impact on surrounding vegetation, using remote sensing techniques. Leveraging **Sentinel-2 imagery**, we can compute the **Normalized Difference Water Index (NDWI)** to delineate the lake’s surface area and the **Normalized Difference Vegetation Index (NDVI)** to assess vegetation health and coverage in the surrounding basin. By masking clouds and non-land pixels using SentinelHub’s `dataMask`, we can ensure accurate spatiotemporal comparisons. For water classification, a threshold of **NDWI > 0.2** is validated through histogram analysis across years. Vegetation analysis is refined by excluding water pixels and computing mean NDVI values per year. To further explore landscape segmentation, **K-Means clustering** is applied to NDVI and NDWI datasets combined, classifying the region into water, vegetation, and bare terrain. A correlation analysis is then performed to examine how changes in lake surface area affected vegetation indices over time. The goal of this project is to provide a reproducible Earth Observation framework for monitoring inland water bodies and understanding their environmental influence using satellite-derived spectral indices.

<br>

---

## 📌 Table of Contents

- [Overview of Lake Urmia](#Overview-of-Lake-Urmia)
- [Techniques Used](#techniques-used)
- [Data Collection](#Data-Collection)
- [License](#license)
- [Acknowledgments](#Acknowledgments)
- [YouTube video explanation of code](#YouTube-Code-Runthrough)
- [References](#References)

---

## Overview of Lake Urmia

Lake Urmia, once the largest lake in the Middle East and among the world’s most expansive hypersaline lakes, has undergone a dramatic transformation—shrinking by approximately 90% in surface area since 1995. While it temporarily recovered around 2020 due to a spike in precipitation, by autumn 2023, the lake had largely desiccated again, exposing vast salt flats. The lake’s decline is attributed to consecutive droughts, dam construction, and intensive agricultural water usage, disrupting inflows from vital feeder rivers. This degradation is not just environmental—it affects human health, air quality, and biodiversity. Once a critical habitat for migratory and native bird species, the lake is now threatened by rising salinity and dust storms from the dry lakebed . These escalating changes underscore the relevance of this project, which leverages NDWI and NDVI satellite-based techniques to monitor surface water and vegetation dynamics at Lake Urmia from 2016 to 2025.

Lake Urmia presents unique challenges for remote sensing due to its highly saline and occasionally sediment-laden water. Salty water alters optical properties by reducing the spectral reflectance contrast between green and near-infrared (NIR) bands, which are critical for accurate NDWI computation. Similarly, brown water—rich in suspended solids like sediments or organic material—can absorb and scatter light in unpredictable ways. These factors diminish the reliability of traditional water detection algorithms, making it more difficult to differentiate water from land or other terrain. As a result, applying NDWI techniques in such conditions demands extra consideration, making this project both more challenging and more interesting to undertake. Despite this, the results are accurately detecting water, as can be seen upon comparing NDWI masks to the full size true colour images, as well as the Copernicus' NDWI Layer.

---

## Techniques Used

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

### Custom Script Used (Sentinel Hub JavaScript)

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
Due to the timing of this project, the three-year intervals selected are not perfectly uniform. While observations of Lake Urmia’s shrinkage date back to the 1970s, high-resolution satellite data is only available from 2015 onwards, coinciding with the operational start of the Sentinel-2 mission. Consequently, this study focuses on the years 2016, 2019, 2022, and 2025, selecting the clearest images available for the month of November to minimize seasonal variation. However, since this project was conducted in May 2025, no November 2025 imagery was yet available. As a result, imagery from May 2025 was used instead. While this approach offers the greatest possible temporal separation from the 2022 dataset, it does introduce potential seasonal effects that must be considered when interpreting the results for 2025.

## License

Distributed under the MIT License. `See LICENSE.txt` for more information.

## YouTube Code Runthrough



## Acknowledgements



## References

Lake Urmia Shrivels again (no date) NASA. Available at: https://earthobservatory.nasa.gov/images/151913/lake-urmia-shrivels-again (Accessed: 02 June 2025). 

Modis Vegetation Index (mod 13) - NASA (no date) MODIS VEGETATION INDEX (MOD 13) ALGORITHM THEORETICAL BASIS DOCUMENT Version 3. Available at: https://modis.gsfc.nasa.gov/data/atbd/atbd_mod13.pdf (Accessed: 02 June 2025). See section 3.3, “Avoiding Division by Zero”

Sentinel-Hub by Sinergise (no date a) Ndwi normalized difference water index, Sentinel Hub custom scripts. Available at: https://custom-scripts.sentinel-hub.com/custom-scripts/sentinel-2/ndwi/ (Accessed: 02 June 2025). 

Sentinel-Hub by Sinergise (no date b) Normalized difference vegetation index, Sentinel Hub custom scripts. Available at: https://custom-scripts.sentinel-hub.com/custom-scripts/sentinel-2/ndvi/ (Accessed: 02 June 2025). 

Shams Ghahfarokhi, M. and Moradian, S. (2023) ‘Investigating the causes of Lake Urmia shrinkage: Climate change or anthropogenic factors?’, Journal of Arid Land, 15(4), pp. 424–438. doi:10.1007/s40333-023-0054-z. 

Welcome to geol0069 AI for earth observation (no date) Welcome to GEOL0069 AI for Earth Observation - GEOL0069 Guide Book. Available at: https://cpomucl.github.io/GEOL0069-AI4EO/intro.html (Accessed: 02 June 2025).

Yan, D. et al. (2017) ‘Analysis of the use of ndwigreen and Ndwired for inland water mapping in the Yellow River basin using landsat-8 oli imagery’, Remote Sensing Letters, 8(10), pp. 996–1005. doi:10.1080/2150704x.2017.1341664. 



