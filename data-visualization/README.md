# Daily Bike Rental Visualization

## Overview

This project explores daily bike-rental demand using visualizations in a Jupyter notebook. It compares total rentals with time, temperature, humidity, and working-day status.

## Objectives

- Inspect daily rental data and prepare its date field.
- Visualize rental demand and selected relationships with weather and calendar variables.
- Use distribution plots to examine the spread of rental counts and temperature.

## Dataset

`Data/day.csv` contains 731 daily observations and 16 columns. It includes a date, season and calendar indicators, weather measurements, casual and registered rentals, and `cnt`, the total daily rental count. The repository does not record a dataset source.

## Technologies

- Python
- pandas
- Matplotlib
- Jupyter Notebook

The notebook also imports Python's standard-library `datetime` module; it does not need a separate package installation.

## Project structure

```text
data-visualization/
├── Data/
│   └── day.csv
├── notebook/
│   └── visual.ipynb
├── README.md
└── requirements.txt
```

## Analysis and preprocessing

The notebook reads `Data/day.csv`, converts `dteday` to a datetime in a working DataFrame, and creates plots of total rentals over the observations, rentals versus temperature and humidity, working versus non-working day distributions, and boxplots for rentals and temperature. The notebook is exploratory; it does not train a model or export a transformed dataset.

## Findings

The notebook's written interpretation describes a median daily rental count around 4,500 and an interquartile range roughly from 3,000 to 6,000. It also notes substantial variability in daily demand. These are approximate visual interpretations recorded in the notebook, not results from a predictive model.

## Installation and running

From this directory:

```powershell
python -m venv .venv
.venv\Scripts\Activate.ps1
python -m pip install -r requirements.txt
jupyter notebook notebook/visual.ipynb
```

Run the notebook with this directory as its working directory so its relative dataset path resolves.

## Future improvements

The notebook could be extended with additional statistical analysis, carefully quantified relationships, or a predictive model. Those capabilities are not currently implemented.
