# BigMart Sales Prediction

## Overview

This project develops and evaluates regression models to predict item-level sales across BigMart outlets. The notebook uses product and outlet attributes to estimate `Item_Outlet_Sales` and documents the process from data inspection through model comparison and error analysis.

## Business Objective

Retail sales forecasts can support inventory planning, product availability, and decisions about how products are allocated across outlets.

**Problem statement:** Can product and outlet information be used to predict item sales with useful accuracy?

## Dataset

The project contains two CSV files under `dataset/`.

### Training Dataset

`Train.csv` contains 8,523 rows and 12 columns, including the target, `Item_Outlet_Sales`. The notebook loads this file and uses it for data exploration, model training, cross-validation, and the held-out evaluation split.

### Test Dataset

`Test.csv` contains 5,681 rows and 11 columns. It has the training data's input columns but does not include `Item_Outlet_Sales`. The current notebook does **not** load this file or generate predictions from it. Its matching feature schema suggests it may be intended for inference, but that workflow is not implemented.

The files are included in the repository. Consult the dataset provider's terms before redistributing the raw data.

## Features Used for Modeling

The notebook selects the following eight features for the described modeling workflow:

| Feature | Description |
|---|---|
| `Item_Weight` | Product weight |
| `Item_Visibility` | Product visibility measure in the outlet |
| `Item_MRP` | Maximum Retail Price |
| `Item_Fat_Content` | Product fat-content category |
| `Item_Type` | Product category |
| `Outlet_Size` | Outlet size category |
| `Outlet_Location_Type` | Outlet location tier |
| `Outlet_Type` | Outlet type |

`Item_Outlet_Sales` is the prediction target. Other source columns are not included in this selected feature list.

## Technology Stack

- Python
- NumPy
- pandas
- Matplotlib
- Seaborn
- scikit-learn
- XGBoost
- LightGBM
- Jupyter Notebook

## Project Structure

```text
bigmart-sales-prediction/
├── dataset/
│   ├── Test.csv
│   └── Train.csv
├── Notebook/
│   └── bigmart.ipynb
├── README.md
└── requirements.txt
```

## Methodology

### Data Understanding and Cleaning

The notebook inspects data types, summary statistics, missing values, duplicates, and the target distribution. It investigates missing `Item_Weight` and `Outlet_Size` values and inconsistent labels in `Item_Fat_Content`. The notebook's cleaning steps harmonize fat-content labels, fill missing item weights with the median, and fill missing outlet sizes with an `"unknown"` category.

### Exploratory Data Analysis

The notebook visualizes the sales target and its relationships with product and outlet characteristics. It includes plots for outlet type, establishment year, item weight, item visibility, item MRP, fat-content categories, and outlet attributes.

### Preprocessing

For model pipelines, a scikit-learn `ColumnTransformer` applies separate transformations to numerical and categorical features:

- Numerical features use median imputation.
- Categorical features use most-frequent imputation and one-hot encoding.
- `OneHotEncoder(handle_unknown="ignore")` allows categories not seen during fitting.

Preprocessing is included in model pipelines so it is fitted as part of model training and cross-validation.

### Target Transformation

The notebook applies `numpy.log1p` to `Item_Outlet_Sales` for model training. For metrics expressed in sales units, it converts predictions and target values back with `numpy.expm1`.

### Model Development

The notebook explores the following regressors:

- Dummy Regressor
- Linear Regression
- Decision Tree Regressor
- Random Forest Regressor
- Extra Trees Regressor
- AdaBoost Regressor
- Gradient Boosting Regressor
- XGBoost Regressor
- LightGBM Regressor

The notebook's evaluation split is created from `Train.csv` using `train_test_split(test_size=0.2, random_state=0)`. Thus, the held-out evaluation set is a portion of the training file; it is not the separate `dataset/Test.csv`.

### Hyperparameter Tuning

The notebook uses five-fold cross-validation to assess candidate models. It applies grid search to the Decision Tree and RandomizedSearchCV to selected models, including Gradient Boosting, XGBoost, and LightGBM. The cross-validation results below are the recorded best search scores for the three tuned boosting candidates.

### Evaluation and Diagnostics

The notebook evaluates regressors using R², mean absolute error (MAE), mean squared error (MSE), and root mean squared error (RMSE). It includes actual-versus-predicted and residual plots. The reported held-out comparison metrics are evaluated on the original sales scale.

## Results

### Cross-Validation Results

Recorded best mean five-fold CV R² scores:

| Model | Best mean CV R² |
|---|---:|
| Gradient Boosting | 0.7383 |
| XGBoost | 0.7378 |
| LightGBM | 0.7372 |

These scores are computed during model selection on the log-transformed target. They are distinct from the held-out results below, which are reported on the original sales scale.

### Held-Out Test Results

The notebook's final comparison evaluates these three models on the held-out portion of `Train.csv`. MAE, MSE, and RMSE are in the original sales units.

| Model | Test R² | MAE | MSE | RMSE |
|---|---:|---:|---:|---:|
| Gradient Boosting | 0.573 | 773.819 | 1,249,501.485 | 1,117.811 |
| Random Forest | 0.569 | 778.180 | 1,261,799.534 | 1,123.299 |
| Decision Tree | 0.562 | 787.465 | 1,281,563.069 | 1,132.061 |

In this recorded comparison, Gradient Boosting has the highest test R² and the lowest MAE and RMSE of these three models. The differences are modest. These results do not establish that it outperforms every candidate model or configuration.

## Key Findings

- Gradient Boosting leads the recorded three-model held-out comparison.
- The tuned boosting candidates have similar best mean cross-validation R² scores on the log-transformed target.
- The held-out test scores indicate that a substantial portion of sales variation remains unexplained.
- The separate `Test.csv` is not used for model selection, held-out evaluation, or prediction generation in the current notebook.

## Limitations

- The model is limited to the available item and outlet features and cannot account for unobserved drivers of sales.
- Performance on this single held-out split may differ on new data or future periods.
- Cross-validation uses the log-transformed target, while the final comparison is reported on the original scale; metrics from these stages should not be directly compared.
- The notebook's final comparison includes three models and does not establish that Gradient Boosting is better than every possible model configuration.
- Although `Test.csv` is present, prediction generation and evaluation against that file are not implemented.
- Results reflect the notebook's recorded outputs and may vary with package versions or execution environment.

## Installation and Setup

From the project directory, create and activate a virtual environment and install the listed dependencies:

```powershell
python -m venv .venv
.venv\Scripts\Activate.ps1
python -m pip install --upgrade pip
python -m pip install -r requirements.txt
```

## Running the Notebook

The notebook reads training data using the relative path `../dataset/Train.csv`. Start Jupyter with the `Notebook/` directory as the working directory so that this path resolves correctly:

```powershell
Set-Location Notebook
jupyter notebook bigmart.ipynb
```

Run the notebook cells in order. The current workflow trains and evaluates using `Train.csv`; it does not generate predictions for `Test.csv`.

## Reproducibility Notes

- The held-out split uses `test_size=0.2` and `random_state=0`.
- Cross-validation supports model selection; the held-out split provides a separate evaluation from the same training CSV.
- The notebook records model and search results in its outputs. The metrics in this README reflect those recorded outputs and have not been independently rerun for this documentation.
- Package versions can affect results. The project requirements file does not pin versions.

## Future Improvements

- Generate and save predictions for `Test.csv` using a selected, fitted pipeline.
- Investigate additional features and feature engineering.
- Review residuals and performance across item and outlet groups.
- Validate the modeling approach on a separate or more recent dataset, if available.
- Record environment and package versions for more reproducible results.
- Consider separating reusable data preparation and training code from the notebook once the workflow is finalized.

## Conclusion

This project demonstrates a retail sales regression workflow, including exploratory analysis, preprocessing, model comparison, hyperparameter tuning, and diagnostics. In the notebook's recorded held-out comparison, Gradient Boosting performs best among the three models shown. The separate test dataset remains unused, and producing predictions for it is a future step.
