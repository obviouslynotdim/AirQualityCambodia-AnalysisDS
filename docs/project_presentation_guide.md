# Cambodia Weather Forecast Analysis: Presentation Guide

## 1) Project in One Minute
This project predicts maximum temperature in Cambodia for a selected date using historical weather data.

It compares three machine learning models:
- Linear Regression
- Decision Tree Regressor
- Random Forest Regressor

The system includes:
- Data processing pipeline
- Training and model comparison pipeline
- Notebook-based analysis and visualizations
- Gradio app for interactive prediction

## 2) Folder-by-Folder Purpose (Architecture)

### Root files
- README.md: high-level project overview and run instructions.
- requirements.txt: Python dependencies.
- read.md: short notebook guide.

### app/
- gradio_app.py: user interface for trying predictions and viewing model outputs.

### artifacts/
Stores generated outputs from training and preprocessing.

- artifacts/models/
  - weather_model_lr.joblib: trained Linear Regression model.
  - weather_model_dt.joblib: trained Decision Tree model.
  - weather_model_rf.joblib: trained Random Forest model.
  - weather_model_temp_min.joblib: additional trained artifact for temperature use case.

- artifacts/preprocessors/
  - model_comparison_metadata.json: comparison metrics and metadata for all models.
  - model_metadata_temp_min.json: metadata for temp_min-related model flow.

### data/
Stores dataset inputs.

- data/raw/: raw source copies.
- data/processed/: processed outputs (if generated in workflow).
- data/cambodia_weather.csv: main dataset used for training and analysis.

### notebooks/
Exploration, model training analysis, and presentation-ready outputs.

- notebooks/eda.ipynb: exploratory data analysis.
- notebooks/model_comparisons.ipynb: final comparison table and charts from saved metadata.
- notebooks/model_diagnostics.ipynb: extended diagnostics by province and season.
- notebooks/_shared/notebook_utils.py: reusable helper functions for notebooks.
- notebooks/linear_regression/: training and analysis notebooks for linear regression.
- notebooks/decision_tree/: training and analysis notebooks for decision tree.
- notebooks/random_forest/: training and analysis notebooks for random forest.

### src/
Core reusable Python package used by scripts and app.

- src/data/
  - preprocess.py: data loading, time-feature engineering, cleaning, feature building, metadata save/load.

- src/models/
  - train_compare.py: main training script for all three models and artifact generation.
  - predict.py: inference engines that load models and run predictions.
  - evaluate.py: model evaluation logic.

- src/utils/
  - config.py: central paths and model registry constants.

### tests/
Automated validation for core logic.

- test_preprocess.py: tests for preprocessing and feature logic.
- test_predict.py: tests for prediction/inference behavior.

### docs/
Project documentation and report materials.

- DSForecast-Report.pdf: report document.
- project_presentation_guide.md: this presenter-friendly architecture and flow guide.

## 3) How the Project Works End-to-End

### Step A: Data Input
1. Historical weather data is stored in data/cambodia_weather.csv.
2. The pipeline reads the data and normalizes columns.

### Step B: Preprocessing
In src/data/preprocess.py the pipeline:
1. Parses date and builds time features: year, month, day, dayofweek.
2. Adds cyclical features: month_sin, month_cos, dayofweek_sin, dayofweek_cos.
3. Cleans data with quality rules:
   - temp_max >= temp_min
   - rain >= 0
   - wind_speed >= 0
   - lat/lon constrained to Cambodia bounds
4. One-hot encodes province into numeric features.

### Step C: Training and Comparison
In src/models/train_compare.py the pipeline:
1. Splits data into train/test sets.
2. Trains 3 models with shared features.
3. Evaluates with RMSE, MAE, and R2.
4. Saves each model to artifacts/models/.
5. Saves model_comparison_metadata.json for later notebook/UI use.

### Step D: Analysis and Reporting
1. notebooks/model_comparisons.ipynb reads metadata and displays final ranking.
2. notebooks/model_diagnostics.ipynb adds deeper validation by province and season.
3. notebooks/eda.ipynb explains data patterns and feature intuition.

### Step E: Inference / Demo
1. app/gradio_app.py loads trained artifacts.
2. User enters inputs (weather + location + date fields).
3. Inference engine builds feature frame and predicts temperature.

## 4) How the Project Handles Real-World Concerns

### Data quality handling
- Invalid or inconsistent rows are filtered.
- Numeric conversion uses safe parsing with coercion for noisy inputs.

### Feature consistency handling
- Inference reindexes input features to the same training feature columns.
- Missing one-hot province columns are filled with 0 to keep schema stable.

### Reproducibility handling
- Model artifacts and metadata are persisted.
- Random state is used in training configuration.

### Maintainability handling
- Paths are centralized in src/utils/config.py.
- Logic is separated into data, models, utils, notebooks, app, and tests.

## 5) Suggested Presentation Flow (7-10 Minutes)

### Slide/Talk 1: Problem and Goal
- Problem: predict Cambodia max temperature for a selected date.
- Goal: compare baseline-to-stronger regressors and pick best practical model.

### Slide/Talk 2: Architecture
- Walk through folders from data to src to artifacts to app.
- Emphasize modular design and reproducible artifacts.

### Slide/Talk 3: Pipeline
- Explain preprocessing -> feature engineering -> training -> evaluation -> serving.

### Slide/Talk 4: Model Comparison Results
- Show RMSE/MAE/R2 table and chart from model_comparisons notebook.
- State winning model and why.

### Slide/Talk 5: Reliability Checks
- Show province and season diagnostics.
- Discuss where model is strong/weak and why subgroup metrics matter.

### Slide/Talk 6: Demo
- Run Gradio app and make 1-2 example predictions.

### Slide/Talk 7: Limitations and Next Steps
- Consider chronological split in core training pipeline.
- Add hyperparameter search and confidence intervals.
- Expand data sources or additional weather features.

## 6) Questions You May Be Asked (with short answers)

### Why three models?
To compare simple, interpretable, and ensemble methods under the same feature pipeline.

### Why Random Forest often wins?
It captures non-linear relationships and interactions better than linear models.

### How do you avoid data leakage?
By separating training and testing and using consistent feature generation from training to inference.

### How is this deployable?
Trained artifacts are serialized and loaded by a lightweight Gradio app.

## 7) Quick Demo Commands

```bash
pip install -r requirements.txt
python -m src.models.train_compare --target temp_max
python app/gradio_app.py
```

## 8) One-Sentence Closing
This project turns Cambodia weather history into a full machine-learning workflow: clean data, engineered features, compared models, saved artifacts, and a usable prediction interface.

## 9) Gradio Implementation Details

### File location
- app/gradio_app.py

### What the Gradio app does
1. Loads multi-model artifacts using `MultiModelInferenceEngine`.
2. Reads metadata (available provinces, best model, feature schema).
3. Builds a user interface for date, province, weather inputs, and model selection.
4. Executes prediction when the user clicks `Predict`.
5. Renders prediction and weather-status cards in the UI.

### Input components in the UI
- Date input (string in `YYYY-MM-DD` format)
- Province dropdown
- Model selector:
  - Auto (best RMSE)
  - Linear Regression
  - Decision Tree
  - Random Forest
- Numeric fields:
  - Min temperature
  - Rainfall
  - Wind speed
  - Latitude
  - Longitude

### Key callback functions
- `update_coordinates(selected_province)`:
  - Auto-fills latitude and longitude based on selected province.
- `predict_temp_max(...)`:
  - Converts selected date into `year`, `month`, `day`, `dayofweek`.
  - Calls `compare_engine.predict_all(...)`.
  - Chooses either auto-best model or user-selected model.
  - Returns formatted HTML cards for prediction and weather status.

### End-to-end request flow
1. User chooses date/province/weather inputs.
2. Gradio triggers `predict_temp_max` on button click.
3. App builds inference payload from user input.
4. Inference engine aligns features with training schema.
5. Model output is returned and displayed instantly.

### Error and dependency handling
- If Gradio is missing, app raises a clear install instruction.
- If model metadata or model files are missing, inference engine raises file-not-found errors with actionable messages.

### Why this implementation is practical
- Uses persisted artifacts, so no retraining is needed at runtime.
- Supports model switching for live comparisons.
- Keeps UI logic lightweight while inference logic remains in `src/models/predict.py`.
