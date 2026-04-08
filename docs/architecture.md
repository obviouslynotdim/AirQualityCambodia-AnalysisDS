# Project Architecture

## Overview
This document explains the architecture of the Cambodia Weather Forecast Analysis project.

The system is organized into modular layers:
1. Data layer: ingestion and preprocessing
2. Modeling layer: training, evaluation, and artifact export
3. Inference layer: model loading and prediction
4. Interface layer: Gradio app for user interaction
5. Analysis layer: notebooks for exploration, comparison, and diagnostics

## High-Level Component Map
- data/: dataset storage (raw and processed)
- src/data/preprocess.py: feature engineering and cleaning pipeline
- src/models/train_compare.py: trains and compares three models
- src/models/predict.py: inference engines (single and multi-model)
- artifacts/models/: saved model binaries
- artifacts/preprocessors/: saved metadata and diagnostics files
- app/gradio_app.py: user-facing prediction interface
- notebooks/: analysis and reporting notebooks
- tests/: checks for preprocessing and prediction behavior

## Layer-by-Layer Design

### 1) Data Layer
Purpose:
- Keep data files separated from code and artifacts.

Key inputs:
- data/cambodia_weather.csv

Responsibilities:
- Preserve source data
- Provide consistent path for training and notebook analysis

### 2) Preprocessing Layer
Implemented in:
- src/data/preprocess.py

Responsibilities:
- Normalize column names
- Parse date fields
- Build calendar features (year, month, day, dayofweek)
- Build cyclical features (month_sin, month_cos, dayofweek_sin, dayofweek_cos)
- Apply quality checks (temperature consistency, non-negative rain and wind, Cambodia coordinate bounds)
- Create one-hot encoded province features
- Save and load metadata required by inference

Output:
- Clean feature matrix and target vector ready for model training

### 3) Modeling Layer
Implemented in:
- src/models/train_compare.py
- src/models/evaluate.py

Responsibilities:
- Split train/test data
- Train three regressors:
  - LinearRegression
  - DecisionTreeRegressor
  - RandomForestRegressor
- Evaluate with RMSE, MAE, and R2
- Persist trained models and comparison metadata

Artifacts produced:
- artifacts/models/weather_model_lr.joblib
- artifacts/models/weather_model_dt.joblib
- artifacts/models/weather_model_rf.joblib
- artifacts/preprocessors/model_comparison_metadata.json

### 4) Inference Layer
Implemented in:
- src/models/predict.py

Main classes:
- InferenceEngine: single-model prediction
- MultiModelInferenceEngine: side-by-side prediction across all trained models

Responsibilities:
- Load serialized model artifacts
- Load metadata and feature column schema
- Build feature frame from user inputs
- Reindex columns to guarantee train/inference schema consistency
- Return prediction values

### 5) Interface Layer
Implemented in:
- app/gradio_app.py

Responsibilities:
- Accept user inputs (date, province, weather variables, lat/lon)
- Auto-fill coordinates from province when possible
- Route request to multi-model inference
- Choose either best model or user-selected model
- Render prediction and weather status cards

Request handling path:
1. User submits inputs in Gradio UI
2. App converts date to year, month, day, and dayofweek
3. App calls MultiModelInferenceEngine.predict_all(...)
4. Engine builds schema-aligned feature row and predicts
5. App selects model output and renders response

## Notebook Architecture
Purpose:
- Separate experimentation and reporting from production code.

Important notebooks:
- notebooks/eda.ipynb: data exploration and pattern discovery
- notebooks/model_comparisons.ipynb: model comparison table and charts from saved metadata
- notebooks/model_diagnostics.ipynb: subgroup diagnostics (province and season)
- notebooks/linear_regression/linear_regression.ipynb: model-specific training notebook
- notebooks/decision_tree/decision_tree.ipynb: model-specific training notebook
- notebooks/random_forest/random_forest.ipynb: model-specific training notebook

Design principle:
- Notebooks should mirror the same feature and training logic used in src to keep analysis consistent with deployed behavior.

## Test and Quality Strategy
Tests location:
- tests/test_preprocess.py
- tests/test_predict.py

Coverage goals:
- Preprocessing correctness
- Inference input handling and prediction flow
- Stability of feature schema assumptions

## Artifact Contract
The project relies on a stable artifact contract between training and inference.

Required metadata fields:
- feature_columns
- provinces
- best_model
- model paths and metrics

Why this matters:
- UI and inference code can run without retraining, as long as metadata and models exist and stay compatible.

## Data Flow Summary
1. Load dataset from data/cambodia_weather.csv
2. Preprocess and engineer features in src/data/preprocess.py
3. Train and compare models in src/models/train_compare.py
4. Save models and metadata into artifacts/
5. Read metadata in notebooks and app
6. Perform predictions from user-provided inputs in app/gradio_app.py

## Operational Notes
- If artifacts are missing, run training first:
  - python -m src.models.train_compare --target temp_max
- App and notebooks depend on model_comparison_metadata.json for model registry and feature schema.
- The architecture supports iterative improvement: retrain models, regenerate artifacts, and keep UI/inference unchanged.

## Presentation Talk Track (Short)
Use this sequence when presenting architecture:
1. Start with folders and responsibilities
2. Explain preprocessing and feature consistency controls
3. Explain model training and artifact persistence
4. Explain how app input becomes prediction output
5. Close with notebook diagnostics and test coverage
