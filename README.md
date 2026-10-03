# Power Plant Output Prediction

Predicts net hourly electrical energy output (PE, MW) of a power plant from four ambient and vacuum variables using XGBoost, Random Forest and SVR.

## Dataset

`plant data.xlsx`: 9,568 rows, 5 columns, no missing values. 41 duplicate rows were removed, leaving 9,527 rows (saved as `cleaned_data.csv`).

| Column | Description | Unit |
|---|---|---|
| AT | Ambient temperature | °C |
| V | Exhaust vacuum | cm Hg |
| AP | Ambient pressure | mbar |
| RH | Relative humidity | % |
| PE | Net hourly electrical energy output (target) | MW |

## Method

- Cleaning: null check, removal of 41 duplicate rows, correlation heatmap, export to `cleaned_data.csv`.
- Features: `AT`, `V`, `AP`, `RH`. Target: `PE`.
- Split: 80% train (7,621) / 20% test (1,906), `random_state=42`.
- Preprocessing: median imputation (fitted on the training set); standard scaling for SVR only.
- Metrics: R², MAE, RMSE (MW), with actual-vs-predicted tables and plots.

Model settings:

- XGBoost: `n_estimators=300`, `learning_rate=0.05`, `max_depth=6`, `subsample=0.8`, `colsample_bytree=0.8`.
- Random Forest: `n_estimators=300`, `max_depth=None`, `min_samples_split=2`, `min_samples_leaf=1`, `max_features=1.0`.
- SVR: RBF kernel, `C=100`, `epsilon=0.1`, `gamma="scale"`.

## Results

| Model | R² | MAE (MW) | RMSE (MW) |
|---|---|---|---|
| XGBoost | 0.9609 | 2.42 | 3.39 |
| Random Forest | 0.9599 | 2.41 | 3.43 |
| SVR | 0.9437 | 2.99 | 4.07 |

XGBoost has the best R² and RMSE. Random Forest has the lowest MAE. SVR has the highest error on all three metrics.

## Requirements

Python 3.8+, pandas, numpy, matplotlib, seaborn, scikit-learn, xgboost, openpyxl. The notebook also installs `shap`; no SHAP analysis is included yet.

```bash
pip install pandas numpy matplotlib seaborn scikit-learn xgboost openpyxl shap
```

## Usage

1. Place `plant data.xlsx` where the notebook can read it.
2. Set the file paths in the notebook (default `/content/plant data.xlsx` and `/content/cleaned_data.csv`).
3. Run the cells in order. The cleaning cells must run before the model cells, since the models read `cleaned_data.csv`.

## Limitations

- Results come from a single train/test split, without cross-validation.
- No hyperparameter tuning was performed.
- The imputation step has no effect on this dataset, since there are no missing values.
