# Airbnb Listings Data Cleaning

## Overview

This project uses a Jupyter notebook to inspect, clean, and validate an Airbnb listings dataset. The work focuses on common quality issues in tabular data and keeps a separate in-memory working copy of the source data.

## Objectives

- Inspect the dataset structure, missing values, and duplicate rows.
- Standardize column names and data types.
- Clean currency-formatted price fields.
- Review data quality and prepare a cleaned DataFrame for further analysis.

## Dataset

`Airbnb_Open_Data.csv` contains 102,599 listings and 26 columns, including listing and host details, location, room type, prices, reviews, and availability. The repository does not include additional source or license information for this data.

## Technologies

- Python
- pandas
- Jupyter Notebook

## Project structure

```text
Project_1/
├── Airbnb_Open_Data.csv
├── Airbnb_project.ipynb
├── README.md
└── requirements.txt
```

## Notebook workflow

The notebook loads the CSV into `Airbnb_data`, then copies it to `Airbnb_data_clean`. It inspects missing values and duplicate rows, standardizes column names, converts `last_review` to a datetime, adjusts selected integer-like columns to nullable integer types, removes currency symbols and commas from `price` and `service_fee`, and drops the `license` field. The cleaning is performed in the notebook's in-memory DataFrame; the repository contains no exported cleaned CSV.

The notebook's stated future work includes exploratory analysis of prices, neighbourhoods, room types, reviews, availability, and possible price prediction. Those analyses are not part of the current notebook workflow.

## Installation and running

From this directory, install the project's notebook dependencies:

```powershell
python -m venv .venv
.venv\Scripts\Activate.ps1
python -m pip install -r requirements.txt
jupyter notebook Airbnb_project.ipynb
```

Run the notebook with this directory as its working directory so the relative path `Airbnb_Open_Data.csv` resolves.

## Findings and next steps

The notebook identifies a mixed-type warning in the `license` column and performs data-quality checks before and after standardizing fields. Its next stated steps are to finish validating the cleaned data and extend the work with exploratory analysis. No predictive model or exported cleaned dataset is included.
