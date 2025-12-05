## How Urban Is World Heritage? An Investigation of Built-up Area Increase in Cultural Heritage Properties

  This is the reporsitory of the project amd Journal paper 'How Urban Is World Heritage? An Investigation of Built-up Area Increase in Cultural Heritage Properties' published in the Land journal. It contains Steps, data and Results of the process resulting from the assessemtent of Built-up Area Increase in Cultural Heritage Properties in sampled 668 [UNESCOs World Heritage properties](https://whc.unesco.org/en/list/). 
To use it, follow the setup and instructions below.

## Summary
0.  [Workflow and Dataset](https://github.com/mkvusa/heritagezoning/blob/main/README.md#workflow-and-datasets)
01. [Requirements](https://github.com/mkvusa/heritagezoning/blob/main/README.md#1-requirements)
02. [Setup](https://github.com/mkvusa/heritagezoning/blob/main/README.md#2-setup)
3. [Raw Data-Pixel count](https://github.com/mkvusa/heritagezoning/blob/main/_PixelCount_WSFEvo_ShapesBuffer_transposed.xlsx)
4. [Raw_Data_GIS](https://github.com/mkvusa/heritagezoning/blob/main/GIS_2024)
5. [Results figures](https://github.com/mkvusa/heritagezoning/tree/main/Figures_updated_202_june)
6. [Writting](https://github.com/mkvusa/heritagezoning/blob/main/README.md#4-writing)
7. [Acknownledge](https://github.com/mkvusa/heritagezoning/blob/main/README.md#5-acknowledgement)
8. [Further Reading](https://github.com/mkvusa/heritagezoning/blob/main/README.md#further-reading)

## Workflow and Datasets

The workflow below illustrates the step-by-step process used in this project. Data were collected from multiple institutions and publications, primarily from **CBS** and other Dutch agencies that provide open-access spatial and socioeconomic datasets. For detailed data sources and preprocessing methods, refer to the published paper.

Spatial data, including **road networks**, **power grids**, and **points of interest**, can be obtained from **OpenStreetMap** or **CBS**. All data used in this project are publicly available.

The datasets in this repository include:
- Outputs from **spatial and temporal analyses**, including spatial regression and nearest-neighbor interpolation, used to estimate the probability of site suitability for EV charging stations.  
- An **Excel file** containing the results of the optimization runs and supporting data for visualization and analysis.

![Workflow of the project](https://github.com/mkvusa/OptimisationEnergyMobilityHubs/blob/main/location_allocation_optmization.jpg)


## 1. Requirements

This workflow requires:
- [ArcGIS](https://www.arcgis.com/index.html)[Licensed]
- [Gurobi](https://www.gurobi.com/)[Licensed]
- [R](https://www.r-project.org) [Free]
- [Python](https://www.python.org) [Free] 
- [LaTeX](https://www.latex-project.org) [Free]
- [Excel](https://www.microsoft.com/en-us/microsoft-365/excel)[Licensed]

Other great languages and software may also be used.
- [QGIS](https://www.qgis.org/fr/site/)[Free]


## 2. Setup (GIS → MILP)

### 2.1 GIS Setup

1. **Folder structure**
data/
├── raw/ # Unprocessed input data (CBS, OSM, power, POI)
├── processed/ # Cleaned datasets for analysis
├── spatial/ # Shapefiles for roads, grid, demand, and POIs
└── temporal/ # Seasonal and time-of-day data

3. **Coordinate Reference System (CRS)**
- Standardize all spatial layers to **Amersfoort / RD New (EPSG:28992)** for consistent distance and time calculations.
- Save harmonized files to `data/processed/spatial`.

3. **Study area and clipping**
- Define the municipal or neighborhood boundary for the study.
- Clip all datasets to the study extent.

4. **Road network**
- Extract drivable edges from **OpenStreetMap (OSM)**.
- Clean topology and assign travel speeds (km/h) and lengths (km).
- Export a routable graph (`roads.graphml`) for shortest-path calculations.

5. **Candidate EVCS sites (set J)**
- Generate candidate points based on existing chargers, POIs, zoning parcels, or suitability grids.
- Add attributes:
  - `j_id`, `x`, `y`, `land_cost (lc_j)`, `grid_conn`, `neighborhood`, `suitability_score`
- Save as `candidates.geojson`.

6. **Demand locations (set I)**
- Define demand centroids (e.g., neighborhood centers or EV user clusters).
- Add attributes:
  - `i_id`, `x`, `y`, `EV_demand_MWh_m` (per season) or `EV_counts`
- Save as `demand.geojson`.

7. **Power network**
- Import MV/LV nodes and lines if available.
- Snap candidate stations to the nearest LV/MV node.
- Compute distance to grid and connection type.
- Save as `grid_nodes.geojson` and `grid_links.geojson`.

8. **Suitability scoring**
- Compute site suitability using multi-criteria layers (roads, power, POIs, land use).
- Normalize scores to `[0,1]` and save as `candidates_suitability.parquet`.

9. **Travel-time matrix**
- Use the cleaned road network to compute shortest paths between demand and candidate sites.
- Calculate \( tt_{ij} \) in **hours** and save as:
  ```
  tt_ij.parquet: [i_id, j_id, tt_h]
  ```

10. **Temporal profiles (sets m, t)**
 - Prepare data for grid cost (`C_grid_mt`), seasonal weights (`w_m`), and demand shares.
 - Save as `temporal_profiles.parquet`.

---

### 2.2 MILP Setup

1. **Sets**
- \( i \): demand locations  
- \( j \): candidate sites  
- \( m \): seasons  
- \( t \): time-of-day intervals  
- \( b \): battery types  

2. **Input files**


## 3. Files
##### `PUP.py`
This was the Python script used to retrieve the World Heritage shapefiles on the Protected Urban Planets Geodataset for decision-making on which database was suitable for analysis in the project. See results of the [assessment](https://github.com/mkvusa/heritagezoning/blob/main/Data%20Assessment.pptx) 


## 4. Writing
- Keep a set of continuously updated slides reflecting the current state and vision of the project.
- Online LaTeX editing tools such as [Overleaf](https://www.overleaf.com/) are suffiecient for simultaneous editing and comments.
- Keep all `.bib` references organized in overleaf.

## 5. Acknowledgement
The workflows and datasets of this paper can be used under the [Creative Common License (Attribution CC BY 4.0)](https://creativecommons.org/licenses/by/4.0/). Please give appropriate credit, such as providing a link to our paper or to this GitHub repository. The copyright of all the downloaded and processed images belongs to the image owners.

## 6. Further Reading

Here is the link to [project report](https://docs.google.com/document/d/1z2x7LImbpOdwTfusMivY_bJPvH6AD3ctfQOXbscvRqc/edit)

Here is the link to the [Graphical Summary](https://docs.google.com/presentation/d/1FWlQp0J-vXN2YH4g35VbUQuPiCwq16CPVWzjlesev10/edit#slide=id.g120edecb748_2_261)

We are using Draw.IO to create the project diagrams.

Here is a link to [project_data_assessment_process](https://drive.google.com/file/d/1P1xcFAEAEWp0NpKyCJqnSuvNw9EEZyqd/view?usp=sharing)

Here is a link for further reading on the [World Settlement Footprint Evolution](https://visat.urban-tep.eu/datasets/map?darkMode=true&viewKey=951b1250-ba02-4b6a-b00b-df4d0dfb5759)
