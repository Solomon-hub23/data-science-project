# BigMart Sales Prediction

## Project Overview

This project develops and evaluates machine-learning regression models to predict item-level sales across BigMart outlets. It uses product attributes and outlet characteristics to estimate `Item_Outlet_Sales`.

The notebook follows an end-to-end workflow: data inspection, cleaning, exploratory data analysis (EDA), preprocessing, model development, hyperparameter search, and error diagnostics.

## Business Objective

Retail sales forecasts can support inventory planning, product availability, and decisions about how products are allocated across outlets.

**Problem statement:** Can product and outlet information be used to predict item sales with useful accuracy?

## Dataset

The notebook uses the BigMart sales dataset, with **8,523 rows and 12 columns** in the original data.

The target variable is:

- `Item_Outlet_Sales` — sales for an item at a particular outlet.

The final modelling feature set contains eight variables:

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

The source data contains missing values, including `Item_Weight` and `Outlet_Size`, as well as inconsistent labels in `Item_Fat_Content`. Consult the original dataset provider's terms before sharing the raw data publicly; this repository can instead include instructions for obtaining it.

## Workflow

### 1. Data understanding and cleaning

- Inspected data types, summary statistics, missing values, and duplicates.
- Examined the inconsistent values in `Item_Fat_Content`.
- Reviewed the distribution and range of the sales target.
- Investigated missing values, including the missingness in `Item_Weight` and `Outlet_Size`.

### 2. Exploratory data analysis

Visual analysis explored the target distribution and relationships between sales and product or outlet characteristics. The notebook includes plots for comparing sales across categories and numerical variables.

### 3. Preprocessing

A scikit-learn `ColumnTransformer` and `Pipeline` were used to apply preprocessing to numerical and categorical features:

- Median imputation for numerical features.
- Imputation for missing categorical values.
- One-hot encoding for categorical variables, with unknown categories handled by the encoder.

The modelling workflow uses a train/test split with `test_size=0.2` and `random_state=0`. Keeping preprocessing inside a pipeline helps ensure that transformations are fitted within the training process and cross-validation folds.

### 4. Target transformation

The target `Item_Outlet_Sales` was transformed with `numpy.log1p` before model training. For evaluation in sales units, log-scale predictions and test targets were converted back with `numpy.expm1`.

Metrics on the original sales scale are used for the final model comparison so that MAE and RMSE can be interpreted in sales units.

### 5. Models and tuning

The notebook explores a range of regression approaches, including:

- Dummy Regressor
- Linear Regression
- Decision Tree Regressor
- Random Forest Regressor
- Extra Trees Regressor
- AdaBoost Regressor
- Gradient Boosting Regressor
- XGBoost Regressor
- LightGBM Regressor

Five-fold cross-validation was used to assess candidate models. `RandomizedSearchCV` was used to search hyperparameters for Gradient Boosting, XGBoost, and LightGBM.

### 6. Evaluation and diagnostics

The notebook evaluates regression performance with:

- **R²:** Measures the proportion of target variance explained by the model relative to a mean-prediction baseline.
- **MAE:** Mean absolute prediction error.
- **MSE:** Mean squared prediction error.
- **RMSE:** Root mean squared prediction error, expressed in the target's units.

Actual-versus-predicted and residual plots are used to inspect prediction alignment, systematic errors, and the distribution of residuals.

## Results

The final held-out test comparison recorded in the notebook is shown below. MAE, MSE, and RMSE are reported on the **original sales scale**.

| Model | Test R² | MAE | MSE | RMSE |
|---|---:|---:|---:|---:|
| Gradient Boosting | 0.575 | 772.944 | 1,243,735.162 | 1,115.229 |
| Random Forest | 0.569 | 778.170 | 1,261,796.393 | 1,123.297 |
| Decision Tree | 0.562 | 787.465 | 1,281,563.069 | 1,132.061 |

In this comparison, **Gradient Boosting performed best overall**, with the highest test R² and the lowest MAE and RMSE among the three models. The differences are relatively small, so the result should be interpreted as a modest improvement rather than a dramatic performance gap.

The tuned cross-validation search scores were approximately:

| Model | Best mean CV R² |
|---|---:|
| Gradient Boosting | 0.7380 |
| XGBoost | 0.7380 |
| LightGBM | 0.7364 |

These cross-validation scores are calculated on the log-transformed target and should not be directly compared with the original-scale test metrics in the table above. The final test comparison and the hyperparameter-search scores also describe different evaluation stages.

## Key Takeaways

- The ensemble methods performed better than the single Decision Tree in the final test comparison.
- Gradient Boosting gave the strongest result among the three models compared on the held-out test set.
- Residual and actual-versus-predicted plots provide an additional diagnostic view beyond a single score.
- The test R² indicates that meaningful variation in sales remains unexplained; further improvements may require stronger features, additional data, or alternative modelling approaches.

## Limitations

- Predictions are based on the features available in the dataset and cannot account for unobserved drivers of sales.
- Performance on one held-out split may differ on new data or future periods.
- The final comparison covers three models; it does not establish that Gradient Boosting is better than every possible configuration of every candidate model.
- The log transformation changes the scale on which model training and cross-validation scores are calculated. Evaluation metrics must be compared on matching scales.

## Future Work

- Investigate additional features and feature engineering.
- Review residual patterns and performance across outlet and item groups.
- Validate performance on a separate or more recent dataset, if available.
- Consider a reproducible model-training script and saved model artifact once the workflow is finalized.

## Tech Stack

- Python
- pandas
- NumPy
- Matplotlib
- Seaborn
- scikit-learn
- XGBoost
- LightGBM
- Jupyter Notebook

## Repository Structure

A suggested repository layout:

```text
bigmart-sales-prediction/
├── README.md
├── requirements.txt
├── .gitignore
├── notebooks/
│   └── bigmart.ipynb
└── data/
    └── README.md
```

The `data/` directory can contain instructions for obtaining the dataset rather than the raw data, depending on the dataset's terms.

## Getting Started

1. Clone or download this repository.
2. Create and activate a Python virtual environment.
3. Install the dependencies listed in `requirements.txt`.
4. Obtain the dataset and place it where the notebook expects it, updating the file path if necessary.
5. Launch Jupyter Notebook and run the notebook cells in order.

Example setup:

```bash
python -m venv .venv
```

On Windows PowerShell:

```powershell
.venv\Scripts\Activate.ps1
python -m pip install --upgrade pip
python -m pip install -r requirements.txt
jupyter notebook
```

## Reproducibility Notes

- The notebook uses `random_state=0` for the recorded train/test split and several model configurations.
- Cross-validation and the held-out test set serve different purposes: cross-validation supports model selection, while the test set provides a final evaluation.
- Package versions can affect results. For more exact reproduction, record the versions used in the environment where the notebook runs successfully.

## Conclusion

This project demonstrates a practical regression workflow for retail sales prediction, from data exploration and preprocessing through model comparison and diagnostics. In the recorded held-out comparison, Gradient Boosting produced the strongest results among the Decision Tree, Random Forest, and Gradient Boosting models.
