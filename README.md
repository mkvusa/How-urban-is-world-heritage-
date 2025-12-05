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
- [QGIS](https://www.qgis.org/fr/site/)[Free]
- [FME](https://www.safe.com/)[Licensed]
- [Python](https://www.python.org) [Free] 
- [LaTeX](https://www.latex-project.org) [Free]
- [Excel](https://www.microsoft.com/en-us/microsoft-365/excel)[Licensed]

Other great languages and software may also be used.
- [R](https://www.r-project.org) [Free]
- [Stata](https://www.stata.com) [Licensed]
- [ArcGIS](https://www.arcgis.com/index.html)[Licensed]

## 2. Setup
Depending on how the raw data is collected,
1. Group the properties into folders for UNESCO regions, i.e., EUR, LAC, APA, AFR, and ARB.
2. For each region, group the shapefiles of the properties into categories of REPORT and NOT REPORTING for properties that are either reporting or not reporting on urban development.
3. Use Python or whatever works to match names of folders to names in the attribute Excel sheet of the world heritage list downloadable from [UNESCO website](https://whc.unesco.org/en/syndication).
4. Use qGIS to merge the shapefiles by their regions and then edit the attribute table by adding the value Status, Showing"Reporting" or "Not Reporting."
5. Join the attributes of the attribute table from the world heritage list to the merged shapefiles in set up 3.
6. Provide core zones in shapefile format to DLR or use World Footprint layer evolution versio for assesment of Built-up change per year.

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
