# Centre of Geographic Sciences (COGS) - NSCC  
## Capstone Project: Leveraging GIS for Wildfire Applications in the WUI

### 🔥 Overview  
**Leveraging GIS for Wildfire Applications in the Wildland-Urban Interface (WUI)**

This project explores how Geographic Information Systems (GIS) can be used to assess wildfire hazard levels in Wildland-Urban Interface (WUI) zones—areas where human development meets wildland vegetation. Focused on a section of Halifax Regional Municipality, the project integrates topography, fuel type, and weather data to model fire hazard levels using both **ArcGIS Pro** and **Google Earth Engine**.

These scripts were developed as a requirement for the Graduate Certificate in GIS at the Centre of Geographic Sciences, NSCC, Lawrencetown, Nova Scotia.

For educational purposes only. Created by Brian Gauthier © 2025 COGS.

---

### 🧩 Core Components Implemented

- **Topographic Risk Mapping**  
  DEM-derived slope, aspect, and elevation maps were created and classified using LiDAR data to improve accuracy.

- **Fuel Type Classification**  
  Sentinel-2 imagery was used to classify vegetation using NDVI/EVI, providing a fuel hazard layer integrated into ArcGIS Pro.

- **Weather-Based Hazard Layer**  
  Historical seasonal weather data (temperature, wind, humidity, precipitation) was used to calculate a fire danger index layer.

---

### 📓 Notebooks Included  
Each notebook walks through part of the data processing and analysis pipeline, from preprocessing DEMs and remote sensing data to classifying fuel types and integrating multi-layer hazard maps.

---

### ⚠️ Limitations

Due to time constraints, the **real-time weather API integration** and **evacuation route planning** features were not implemented. However, the scripts and methods included are modular and designed to support future integration of these components.

---

### 🎯 Goal

To provide a flexible hazard mapping framework that can be adapted to other Canadian regions using new data sources, enabling improved fire prevention planning, risk communication, and future automation for emergency response.

---

## 📁 Notebooks

## `1. wildfire_prep.ipynb`  
**Preprocessing Wildfire Imagery with Earth Engine and ArcGIS Pro**

This script prepares satellite imagery and boundary data for wildfire analysis within the Halifax Regional Municipality (HRM), Nova Scotia. It integrates **Google Earth Engine (GEE)** and **ArcGIS Pro (arcpy)** to automate spatial preprocessing tasks such as region clipping, cloud masking, imagery mosaicking, and DEM downloading.

### 🛠️ Key Tasks Performed:

- **Region Preparation:**
  - Loads provincial and county-level boundaries from the FAO GAUL 2015 dataset.
  - Filters out Sable Island from the Halifax boundary for spatial clarity.
  - Splits HRM into four east-west longitudinal sections and clips them to the adjusted boundary.

- **Imagery Collection and Cloud Masking:**
  - Loads Sentinel-2 imagery and cloud probability data (June–October 2024).
  - Applies a `<30%` cloud probability filter to mask cloudy pixels.
  - Generates a median composite of the clean imagery and clips it to the Centre-West region.

- **Export to Google Drive:**
  - Exports the cloud-masked, clipped Sentinel-2 composite to Google Drive as a GeoTIFF.

- **Raster Mosaicking with ArcPy:**
  - Searches a local directory for downloaded GeoTIFFs.
  - Mosaics multiple tiles into a single raster using ArcPy.

- **DEM Downloading:**
  - Downloads and stores a 2-meter resolution LiDAR DEM of Halifax from the municipal open data portal.

### 📦 Dependencies:

- `arcpy` (requires ArcGIS Pro)  
- `earthengine-api` (`ee`)  
- `geemap`  
- `requests`, `tqdm`, `zipfile`, `os`, `json`  
- A Google Earth Engine account with access to the `COPERNICUS/S2` collections

### 📤 Outputs:

- Clipped and cloud-masked Sentinel-2 imagery exported to Google Drive  
- Mosaicked raster saved locally as `sentinel_2_mosaic.tif`  
- HRM LiDAR DEM downloaded and stored in the `data/raw` directory

### 📝 Notes:

- Ensure that you're logged into **Google Earth Engine** and using the correct GEE project.
- This script assumes it is run within an open **ArcGIS Pro** project (`CURRENT`) and will automatically use its directory as the base path.

---
 
## 2. `wildfire_prep2.ipynb`

### Automated Download and Extraction of Nova Scotia Geospatial Data

This notebook automates downloading and extracting multiple official geospatial datasets for Nova Scotia using Python and ArcPy within an ArcGIS Pro project environment. It sets up a clean project folder structure and handles raw data acquisition to streamline GIS workflows.

### 🛠️ Key Tasks Performed:

- **Project Folder Setup:**  
  Detects the current ArcGIS Pro project directory and creates standard data folders: `data/raw`, `data/extracted`, and `data/processed`.

- **Data Downloading:**  
  Downloads ZIP archives for Roads, Hydrography, Utilities, and Buildings datasets from Nova Scotia’s NSGI data service using HTTP requests.

- **File Extraction:**  
  Extracts each ZIP file into its own subfolder under `data/extracted` for organized access.

- **ArcPy Environment Configuration:**  
  Sets workspace to `data/raw` and enables overwriting outputs to facilitate further geoprocessing.

### 📦 Dependencies:

- arcpy (ArcGIS Pro Python environment)  
- requests  
- zipfile  
- os  

### 📤 Outputs:

- Raw ZIP files downloaded to `data/raw`  
- Extracted dataset folders within `data/extracted` containing shapefiles and related data  

### 📝 Notes:

- Designed to run inside an active ArcGIS Pro project session (`CURRENT`) for path detection.  
- Automates foundational data prep for spatial analysis workflows in Nova Scotia.

---

## 3. wildfire_wui.ipynb  

### Wildland-Urban Interface (WUI) Mapping and Building Density Analysis with ArcPy

This script identifies and classifies Wildland-Urban Interface zones by integrating building footprint data with forest inventory layers based on canopy cover and stand volume criteria. It performs spatial selections, joins, density calculations, and buffering to delineate WUI intermix and interface zones around large wildland patches.

### 🛠️ Key Tasks Performed:

- **Setup:**
  - Sets ArcPy workspace to a file geodatabase and enables output overwriting.
  - Checks out Spatial Analyst extension.

- **Forest Type Classification:**
  - Selects valid NSDNR forest types from the full forest inventory.
  - Creates subsets for forest stands with canopy cover (CC) <50%, ≥50%, and high-density stands (≥75% CC or high basal area/volume).

- **Building Density Analysis:**
  - Spatially joins buildings to forest stands with ≥50% CC, calculates building density (structures/km²).
  - Selects and exports stands with building density ≥6.17 as WUI Intermix.
  - Repeats building density analysis for forest stands with <50% CC.

- **Wildland Patch Delineation:**
  - Dissolves high-density forest patches (≥75% CC or high volume/BA) to create larger wildland polygons.
  - Calculates area, selects patches ≥5 km², and buffers these patches by 2.4 km.

- **WUI Interface Identification:**
  - Selects developed areas (<50% CC, high building density) intersecting buffered wildland patches.
  - Exports these as WUI Interface zones.

- **Final Merge:**
  - Merges WUI Intermix and Interface zones into a single WUI feature class.

### 📦 Dependencies:

- arcpy (ArcGIS Pro Python environment with Spatial Analyst extension)

### 📤 Outputs:

- `forest_types`, `forest_50_less`, `forest_50`, `forest_75` — filtered forest layers  
- `f50_bldgs`, `f50less_bldgs` — building density joined layers  
- `wui_intermix` — WUI intermix stands (≥50% CC with high building density)  
- `forest_75_dissolved` — dissolved wildland patches  
- `large_wildland` — large wildland patches ≥5 km²  
- `wildland_buffer` — 2.4 km buffers around large wildland patches  
- `wui_interface` — developed areas near wildland patches (WUI interface)  
- `wui` — merged Wildland-Urban Interface zones  

### 📝 Notes:

- Building density threshold of 6.17 structures/km² is applied consistently to define developed areas.  
- Script assumes feature classes exist in the specified geodatabase workspace.  
- Designed for NSDNR forest inventory data with specific forest type codes and canopy cover fields.

---

## 4. `wildfire_terrain.ipynb`  

**Wildfire Terrain Hazard Mapping using DEM-derived Elevation, Slope, and Aspect with ArcPy Spatial Analyst**

This script generates a multi-factor wildfire hazard map by processing a clipped DEM raster. It calculates hillshade for visualization, classifies elevation, slope, and aspect into wildfire hazard categories based on thresholds from wildfire behavior studies, and combines these into a weighted terrain hazard map.

### 🛠️ Key Tasks Performed:

- **Setup:**
  - Defines project directory and data subfolders (`raw`, `extracted`, `processed`), creating them if missing.
  - Sets ArcPy workspace to the processed data folder and enables overwrite.
  - Checks out the Spatial Analyst license.

- **Raster Preparation:**
  - Loads clipped DEM raster and filters out NoData values.

- **Hillshade Generation:**
  - Creates hillshade raster with specified azimuth and altitude for terrain visualization.

- **Elevation Classification:**
  - Categorizes elevation into five wildfire hazard classes (Very High to Very Low) using elevation thresholds (0–100m, 100–200m, etc.).

- **Slope Calculation and Classification:**
  - Calculates slope in degrees.
  - Classifies slope into four wildfire hazard categories (Low to Very High) based on slope angle ranges.

- **Aspect Calculation and Classification:**
  - Computes terrain aspect.
  - Classifies aspect into hazard classes by cardinal direction (e.g., North=Low, South=Very High).

- **Terrain Hazard Integration:**
  - Loads classified elevation, slope, and aspect rasters.
  - Combines them using a weighted sum (Elevation 25%, Slope 40%, Aspect 35%).
  - Rounds the result to create final discrete hazard classes.

### 📦 Dependencies:

- ArcPy (ArcGIS Pro Python environment with Spatial Analyst extension)

### 📤 Outputs:

- `hillshade.tif` — hillshade raster for terrain shading  
- `elev_hazard_map.tif` — elevation hazard classification  
- `slope_hazard_map.tif` — slope hazard classification  
- `aspect_hazard_map.tif` — aspect hazard classification  
- `terrain_hazard_map.tif` — final combined wildfire terrain hazard map  

### 📝 Notes:

- Hazard class codes are integers where higher values represent greater wildfire hazard except elevation where 5=Very High and 1=Very Low.  
- DEM input must be projected in UTM for accurate slope/aspect calculations.  
- NoData handling ensures smooth calculations without errors from missing data.  
- Weighted combination allows customizable importance of terrain factors for wildfire risk assessment.

---
## 5. `wildfire_fuel.ipynb`  

**Sentinel-2 NDVI and EVI Calculation, Masking, Reclassification, and Fuel Index Derivation with ArcPy**

This script processes Sentinel-2 multispectral imagery to calculate vegetation indices NDVI and EVI, applies land masking, performs reclassification to vegetation density classes, and combines the indices into a composite fuel index raster for wildfire fuel assessment.

### 🛠️ Key Tasks Performed:

- **Setup:**
  - Defines project directory structure (`raw`, `extracted`, `processed`) and ensures folders exist.
  - Sets ArcPy workspace to geodatabase and enables overwrite.
  - Checks out Spatial Analyst extension.

- **NDVI Calculation:**
  - Loads Sentinel-2 mosaic raster.
  - Extracts Band 8 (NIR) and Band 4 (Red).
  - Computes NDVI using formula `(NIR - Red) / (NIR + Red)`.
  - Masks NDVI raster by land area polygon.
  - Saves masked NDVI raster.
  - Reports min/max NDVI values.

- **NDVI Reclassification:**
  - Defines NDVI ranges with classes from Null (0) to High vegetation (4).
  - Reclassifies masked NDVI raster using these ranges.
  - Saves reclassified NDVI raster.

- **EVI Calculation:**
  - Loads Sentinel-2 bands Blue (2), Red (4), and NIR (8), scaled by 10000.
  - Calculates EVI with standard formula incorporating gain and atmospheric correction constants.
  - Masks EVI by land area polygon.
  - Clips EVI values outside valid range (-1 to 1) using conditional raster processing.
  - Saves clipped EVI raster.
  - Reports min/max EVI values.

- **EVI Reclassification:**
  - Defines EVI classification ranges from water/non-vegetated (0) to high vegetation (4).
  - Reclassifies clipped EVI raster accordingly.
  - Saves reclassified EVI raster.

- **Fuel Index Generation:**
  - Loads final NDVI and EVI rasters.
  - Computes average of NDVI and EVI to create a composite fuel index raster.
  - Saves fuel index raster for wildfire fuel assessment.

### 📦 Dependencies:

- ArcPy (ArcGIS Pro Python environment with Spatial Analyst extension)  
- Sentinel-2 multispectral raster dataset in UTM projection  
- Land mask polygon feature class for masking non-land areas

### 📤 Outputs:

- `ndvi.tif` — NDVI raster masked by land area  
- `ndvi_masked_reclass` — reclassified NDVI raster with vegetation density classes  
- `evi_clipped.tif` — clipped EVI raster masked by land area  
- `evi_masked_reclass` — reclassified EVI raster with vegetation density classes  
- `fuel_index` — composite raster averaging NDVI and EVI representing vegetation fuel index  

### 📝 Notes:

- Vegetation indices reclassified into consistent 0–4 classes for comparative vegetation density mapping.  
- EVI formula incorporates gain, aerosol correction factors, and small constant for improved sensitivity in dense vegetation.  
- Conditional raster operation clips invalid EVI values, preventing data artifacts.  
- Composite fuel index combines strengths of NDVI and EVI for wildfire risk modeling.

--- 
## 6. `wildfire_weather.ipynb`

**Seasonal Fire Weather Hazard Index computation over Halifax Regional Municipality (HRM) using ECMWF ERA5 hourly weather data and Earth Engine via geemap**

This script loads an HRM shapefile AOI, extracts seasonal ERA5 weather data (temperature, precipitation, wind, vapor pressure deficit), normalizes and combines these to compute a Fire Weather Hazard Index (FWHI) for spring, summer, and fall. The index is classified into hazard classes, visualized on an interactive map, and exported locally as GeoTIFFs.

### 🛠️ Key Tasks Performed:

- **Setup:**
  - Initializes Earth Engine with project and geemap.
  - Loads HRM boundary shapefile as Earth Engine FeatureCollection and extracts AOI geometry.
  - Adds AOI boundary to interactive map and centers map on AOI centroid.

- **Season Definition:**
  - Defines date ranges for spring, summer, and fall seasons of 2024.

- **Weather Data Extraction and Processing:**
  - Retrieves ECMWF ERA5 hourly data filtered by season and AOI.
  - Converts temperature and dewpoint from Kelvin to Celsius.
  - Calculates max temperature, total precipitation, mean wind components (u and v), wind speed magnitude, and vapor pressure deficit (VPD) as temperature minus dewpoint.
  - Clips all layers to AOI.

- **Normalization and Index Computation:**
  - Normalizes each weather variable (temperature, VPD, wind speed, precipitation) to a 0–1 scale based on season-specific min/max ranges.
  - Combines normalized variables with weighted sum to compute seasonal Fire Weather Hazard Index raster.

- **Classification and Visualization:**
  - Classifies FWHI raster into four hazard classes (Low, Moderate, High, Extreme) using conditional expressions.
  - Adds classified layers to the map with color palette for hazard visualization.

- **Export:**
  - Exports classified FWHI GeoTIFF files locally for each season.

- **Summary Statistics:**
  - Calculates and prints min/max statistics of temperature, VPD, wind speed, and precipitation for each season over the AOI.

### 📦 Dependencies:

- Python libraries: `earthengine-api`, `geemap`  
- Access to Google Earth Engine account and project  
- Local HRM shapefile boundary for AOI  
- ECMWF ERA5 hourly dataset on Earth Engine

### 📤 Outputs:

- Seasonal classified Fire Weather Hazard Index GeoTIFFs (spring, summer, fall)  
- Interactive map with seasonal FWHI hazard classification layers  

### 📝 Notes:

- Weather variables are normalized using season-specific ranges to improve index sensitivity.  
- Wind speed calculated from u and v wind components as vector magnitude.  
- VPD estimated as difference between temperature and dewpoint in Celsius.  
- Hazard index weighted combination reflects relative importance of temperature, wind, precipitation, and VPD for fire risk.  
- Exported GeoTIFFs facilitate further spatial analysis or integration into wildfire risk management workflows.

--- 
## 7. `wildfire_totHazard.ipynb`

**Weighted Fire Hazard Index calculation by combining fuel, terrain, and weather raster data with normalization and reclassification using ArcPy**

This script computes seasonal fire hazard indices by integrating fuel load, terrain difficulty, and weather condition rasters. It normalizes input rasters to a consistent 1–5 scale, calculates a weighted sum hazard index, then reclassifies the output into four discrete hazard classes for spring, summer, and fall.

### 🛠️ Key Tasks Performed:

- **Environment Setup:**
  - Checks out the Spatial Analyst extension for raster operations.
  
- **Normalization:**
  - Defines a function `normalize_raster` to scale raster values linearly to a 1–5 range.
  - Applies normalization to weather rasters and adjusts fuel raster values (originally 0–4) by adding 1 to align with 1–5 scale.
  - Terrain raster assumed pre-scaled (1–5), so no normalization applied.

- **Hazard Index Calculation:**
  - Loads fuel, terrain, and seasonal weather rasters.
  - Combines normalized rasters into a weighted sum hazard raster with weights:
    - Fuel: 0.4
    - Terrain: 0.3
    - Weather: 0.3
  - Saves weighted hazard raster for each season (spring, summer, fall).

- **Reclassification:**
  - Defines a 4-class reclassification scheme mapping continuous hazard values into integer hazard classes 1 to 4.
  - Applies reclassification to seasonal hazard rasters.
  - Saves reclassified rasters for each season.

- **Cleanup:**
  - Checks Spatial Analyst extension back in.

### 📦 Dependencies:

- ArcPy with Spatial Analyst extension enabled  
- Input rasters stored in a file geodatabase: fuel, terrain, and seasonal Fire Weather Hazard Index (FWHI) rasters  
- Python 3.x environment supporting ArcPy

### 📤 Outputs:

- `hazard_spring`, `hazard_summer`, `hazard_fall` — weighted continuous hazard rasters  
- `hazard_spring_reclass`, `hazard_summer_reclass`, `hazard_fall_reclass` — reclassified hazard rasters with 4 discrete hazard classes  

### 📝 Notes:

- Fuel raster originally scaled 0–4 and adjusted to 1–5 by adding 1 for consistency.  
- Terrain raster values are expected in the 1–5 scale, representing terrain difficulty or susceptibility.  
- Weather raster values normalized dynamically to 1–5 using raster min/max.  
- Reclassification bins hazard values into four classes for simplified hazard interpretation.  
- Modular functions support flexibility to adjust weights or reclassification scheme.
