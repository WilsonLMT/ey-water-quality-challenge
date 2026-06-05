# EY AI & Data Challenge 2026 — Water Quality Prediction in South Africa

## Background

This repository contains the solution developed for the 2026 EY AI & Data Challenge, a global competition focused on applying machine learning and satellite-derived data to predict water quality in rivers across South Africa. According to the World Health Organization, nearly 2.2 billion people lack access to clean and safe water, and the United Nations has warned that climate change will further increase water stress, putting the health of 4.8 billion people at risk by 2030. The primary goal of this challenge is to understand the environmental and climatic drivers of water quality and build predictive models that could support local water management decisions.

---

## Objective

The task consists of predicting three water quality parameters for unseen river locations in South Africa, using publicly available satellite and climate datasets as input features. The parameters to predict are total alkalinity (mg/L), electrical conductance or EC (uS/cm), and dissolved reactive phosphorus or DRP (ug/L). Model performance is evaluated using the mean R² score across all three parameters.

---

## Repository Structure

```
├── 01_terraclimate_extraction.ipynb        # Extraction of climate variables from TerraClimate
├── 02_terraclimate_runoff_extraction.ipynb # Extraction of the runoff variable (q) from TerraClimate
├── 03_landsat_extraction.ipynb             # Extraction of spectral features from Landsat imagery
├── Prueba1_v20_final.ipynb                 # Main modeling notebook: feature integration, training and prediction
├── water_quality_training_dataset.csv      # Training data from UNEP (162 stations, 2011-2015)
├── submission_template.csv                 # Validation points for which predictions are generated
└── requirements.txt                        # Python dependencies
```

---

## Data Sources

All feature datasets used in this project are publicly available.

**TerraClimate** is a satellite-based global climate and water balance dataset available via the Microsoft Planetary Computer. It provides monthly data at a spatial resolution of 4 kilometres. The variables extracted for this project include potential evapotranspiration (pet), actual evapotranspiration (aet), precipitation (ppt), soil moisture (soil), runoff (q), and a derived water deficit variable calculated as the difference between pet and aet.

**Landsat** (NASA) provides optical satellite imagery at 30-metre spatial resolution, also available through the Microsoft Planetary Computer. Spectral bands and indices such as NDVI were extracted after applying cloud filtering to ensure data quality.

**The target dataset** was provided by the United Nations Environment Programme (UNEP) and contains 27,957 water quality samples collected between 2011 and 2015 across 86 rivers and 162 measurement stations in South Africa.

---

## How to Run

The notebooks are designed to run on Snowflake Notebooks, the cloud platform used during the challenge, but the code is compatible with any standard Python or Jupyter environment. The notebooks must be executed in the following order:

```
1. 01_terraclimate_extraction.ipynb
2. 02_terraclimate_runoff_extraction.ipynb
3. landsat_extraction.ipynb
4. Prueba.ipynb
```

Each extraction notebook checks whether its output file already exists before re-running, so the pipeline can be resumed safely at any step without repeating previous computations.

To install the required dependencies, run:

```bash
pip install -r requirements.txt
```

The two files that must be present before running any notebook are `water_quality_training_dataset.csv` and `submission_template.csv`. All intermediate output files are generated automatically during execution.

---

## Modeling Approach

For each water quality sample, climate and satellite features were spatially matched to the nearest grid point using a nearest-neighbor search via cKDTree, with coordinates converted to radians to approximate spherical distances. Temporal matching was performed by selecting the closest available monthly record for each sample date. Features were extracted separately for the training and validation sets, and individual Random Forest regression models were trained for each of the three water quality parameters.

---

## Technical Stack

The project was developed in Python using the following libraries and platforms: Pandas, NumPy, xarray, scikit-learn, rioxarray, pystac-client, planetary-computer, and Snowflake Notebooks for cloud computing.
