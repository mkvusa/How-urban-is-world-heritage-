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
candidates.geojson/parquet → lc_j, grid attributes
bess_catalog.csv → bess_jb, cost_abc_jb
tt_ij.parquet → tt_ij (hours)
temporal_profiles.parquet → C_grid_mt, w_m
demand.geojson/parquet → EV_demand_i,m or EV_demand_i,m,t

3. **Decision variables**
- Binary: `Y_j`, `B_jb`
- Continuous (≥ 0): `X_jimt`, `S_ijmt`, `G_jmt`, `Z_jmt`, `I_jmt`

4. **Objectives**
- **Monetary Cost (MC):**
  \[MC = \sum_j lc_j Y_j + \sum_{j,b} bess_{jb}\,abc_{jb}\,B_{jb} + \sum_{j,m,t} C_{grid_{mt}}\,w_m\,G_{jmt}\]
- **Travel Time (TT):**
  \[TT = \sum_{i,j,m,t} (S_{ijmt} + X_{jimt})\,tt_{ij}\]

5. **Core constraints**
- Site–battery link:  
  \[\sum_b B_{jb} = Y_j \quad \forall j\]
- Inventory dynamics:  
  \[I_{jmt} = I_{jm,t-1} + \sum_i S_{ijmt} + G_{jmt} - Z_{jmt} - \sum_i X_{jimt}\]
- Capacity bounds:   \[I_{jmt} \le \sum_b bess_{jb} B_{jb}  \]
- Non-negativity and binary conditions for all variables.

6. **Optimization runs**
- **MC-first:** minimize MC, then fix \( MC^* \) and minimize TT.  
- **TT-first:** minimize TT, then fix \( TT^* \) and minimize MC.  
- Store both pay-off results for comparison.

7. **Units**
- Distance → km  
- Time → hours  
- Energy → MWh  
- Cost → k€  

8. **Output folders**
results/
├── optimization/
│ ├── MC_first/
│ └── TT_first/
├── figures/
└── summary_tables/

9. **Reproducibility**
- Record solver version, Gurobi parameters, and random seed.
- Store run configurations and summary logs for each optimization sequence.



## 3. Files
##### `PUP.py`
THis iss the MILP script[]()

## 4. Writing
- Online LaTeX editing tools such as [Overleaf](https://www.overleaf.com/) are suffiecient for simultaneous editing and comments.
- Keep all `.bib` references organized in Overleaf.

## 5. Acknowledgement
The workflows and datasets of this paper can be used under the [Creative Commons License (Attribution CC BY 4.0)](https://creativecommons.org/licenses/by/4.0/). Please give appropriate credit, such as providing a link to our paper or to this GitHub repository. The copyright of all the downloaded and processed images belongs to the image owners.

## 6. Further Reading

Here is the link to [project report](https://www.nwo.nl/en/projects/kich1ed0320012)

We are using Draw.IO to create the project diagrams.

Here is a link for further reading on the [Foundational Publication](https://www.tandfonline.com/doi/full/10.1080/15568318.2025.2528085)
