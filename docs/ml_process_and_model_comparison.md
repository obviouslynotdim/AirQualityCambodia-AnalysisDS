# Machine Learning Process and Model Comparison

## Purpose
This document explains how machine learning is implemented in this project and how model comparison is performed.

It is written for:
- Project presentation
- Report writing
- Quick onboarding for teammates

## 1) Problem Definition
Goal:
- Predict maximum temperature (`temp_max`) for a selected date and location context in Cambodia.

Why regression:
- The target is a continuous numeric value (temperature in deg C).

## 2) Data Input and Scope
Primary dataset:
- `data/cambodia_weather.csv`

Core fields used:
- Date: `date`
- Weather: `temp_min`, `rain`, `wind_speed`
- Geography: `province`, `lat`, `lon`
- Target: `temp_max`

## 3) Preprocessing and Feature Engineering
Implemented in:
- `src/data/preprocess.py`

Main steps:
1. Load CSV and normalize column names.
2. Parse `date` into datetime.
3. Add calendar features:
   - `year`, `month`, `day`, `dayofweek`
4. Add cyclical features:
   - `month_sin`, `month_cos`, `dayofweek_sin`, `dayofweek_cos`
5. Apply data-quality rules:
   - Keep rows where `temp_max >= temp_min`
   - Keep non-negative `rain` and `wind_speed`
   - Keep coordinates inside Cambodia bounds
6. One-hot encode province into model-ready numeric columns.

Why these features help:
- Calendar + cyclical features capture seasonality.
- Province + coordinates capture regional climate differences.
- Quality filters reduce noisy or physically inconsistent samples.

## 4) Train/Test Strategy
Implemented in:
- `src/models/train_compare.py`

Split setup:
- Random train/test split
- Test size: 20%
- Random seed: 42

Purpose:
- Train on most data, evaluate on unseen data.

## 5) Models Trained
The project trains three regression models with the same feature set:

1. Linear Regression
- Fast and interpretable baseline.

2. Decision Tree Regressor
- Captures non-linear relationships.
- Config: `max_depth=15`.

3. Random Forest Regressor
- Ensemble of trees for stronger generalization.
- Config: `n_estimators=220`, `max_depth=24`, `min_samples_leaf=2`.

## 6) Evaluation Metrics
Metrics used for all models:
- RMSE (Root Mean Squared Error): lower is better.
- MAE (Mean Absolute Error): lower is better.
- R2 (Coefficient of Determination): higher is better.

Interpretation:
- RMSE penalizes large errors more strongly.
- MAE gives average absolute deviation.
- R2 shows explained variance.

## 7) Model Comparison Process
Comparison flow in `src/models/train_compare.py`:
1. Train each model on the same `X_train`, `y_train`.
2. Predict on the same `X_test`.
3. Compute RMSE, MAE, R2 for each model.
4. Save per-model metrics and metadata.
5. Select best model by lowest RMSE.

Artifacts generated:
- Models:
  - `artifacts/models/weather_model_lr.joblib`
  - `artifacts/models/weather_model_dt.joblib`
  - `artifacts/models/weather_model_rf.joblib`
- Metadata:
  - `artifacts/preprocessors/model_comparison_metadata.json`

## 8) Where Comparison Is Shown
Notebook view:
- `notebooks/model_comparisons.ipynb`

What it displays:
- Table with model type, RMSE, MAE, R2, train/test row counts
- Best-model flag
- Bar charts for RMSE, MAE, and R2

Extended diagnostics:
- `notebooks/model_diagnostics.ipynb`

What it adds:
- Global metrics on chronological holdout
- Province-level metrics
- Season-level metrics (wet vs dry)

## 9) Inference and Real Usage
Implemented in:
- `src/models/predict.py`
- `app/gradio_app.py`

How prediction works:
1. User provides date and weather/location inputs.
2. App converts date to model features (`year`, `month`, `day`, `dayofweek`).
3. Inference engine builds feature row and aligns columns with training schema.
4. Selected model (or auto-best model) returns predicted `temp_max`.

Important note:
- The model does not know future exact temperatures.
- It estimates based on learned patterns plus user-provided inputs.

## 10) End-to-End Summary (Presentation Script)
Use this short script:
1. We collect and clean Cambodia weather data.
2. We engineer time, cyclical, and location features.
3. We train three regressors on the same split.
4. We compare them using RMSE, MAE, and R2.
5. We save artifacts and choose the best model by RMSE.
6. We expose prediction through Gradio and validate results in comparison/diagnostic notebooks.

## 11) Reproduce the Full Pipeline
```bash
pip install -r requirements.txt
python -m src.models.train_compare --target temp_max
python app/gradio_app.py
```

Optional analysis:
- Open and run `notebooks/model_comparisons.ipynb`
- Open and run `notebooks/model_diagnostics.ipynb`
