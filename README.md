# 🌫️ Cambodia Air Quality Analysis

### Fundamentals of Data Science Project  
**Cambodia Academy of Digital Technology (CADT)**  
Bachelor of Computer Science  

---

<p align="center">
  <img src="https://img.shields.io/badge/Python-3.10-blue?style=for-the-badge&logo=python">
  <img src="https://img.shields.io/badge/Pandas-Data%20Analysis-orange?style=for-the-badge&logo=pandas">
  <img src="https://img.shields.io/badge/Matplotlib-Visualization-green?style=for-the-badge">
  <img src="https://img.shields.io/badge/OpenAQ-API-purple?style=for-the-badge">
</p>

---

## 📌 Project Overview

This project analyzes air pollution in **Phnom Penh, Cambodia**, focusing on **PM2.5 and PM10** concentrations.  
Data is collected from [OpenAQ](https://openaq.org/) and processed using **Python**, **Pandas**, and **Matplotlib** to:

- Explore temporal trends in air quality  
- Identify high pollution periods  
- Visualize patterns with daily and monthly averages  
- Optional: forecast short-term PM2.5 levels  

---

## 🎯 Objectives

- Collect and preprocess air quality data for Phnom Penh  
- Analyze temporal trends of key pollutants (PM2.5, PM10)  
- Identify periods with high pollution  
- Provide clear visualizations and insights  

---

## 🌎 Data Source

- **OpenAQ API** ([https://openaq.org/](https://openaq.org/))  
- Hourly air quality measurements  
- PM2.5, PM10, Air Quality Index (AQI)  
- Date and Time information  

---

## 🧠 Methodology

### 1️⃣ Data Preprocessing
- Remove duplicates and missing values  
- Convert timestamps to datetime  
- Extract Year, Month, Day, Hour features  
- Aggregate hourly data to daily/monthly averages  

### 2️⃣ Exploratory Data Analysis (EDA)
- Daily and monthly trend visualization  
- Seasonal comparisons  
- Identify high pollution days (>35 µg/m³ PM2.5)  

### 3️⃣ Modeling (Optional)
- Linear regression or moving average forecasting  
- Evaluate with **MAE** & **RMSE**  

---

## 📊 Expected Outcomes

- Identify trends in PM2.5 and PM10  
- Highlight high-risk days and months  
- Determine which pollutants exceed safe thresholds  
- Provide interactive visualizations and data-driven insights  

---

## 👨‍💻 Team Members

| Kuy Poly        | Chhorn Norakjed   | Sophal Chanrat    |
|-----------------|-----------------|-----------------|
| Te Chhenghab    | Hak Kimly        | Sao Sethavathanak|

---

## ⚙️ How to Run

1. Clone the repository:

```bash
git clone https://github.com/your-username/cambodia-air-quality-analysis.git
cd cambodia-air-quality-analysis
````

2. Install dependencies:

```bash
pip install -r requirements.txt
```

3. Launch Jupyter Notebook:

```bash
jupyter notebook
```

4. Open `notebooks/01_air_quality_analysis.ipynb` and run all cells.

---

## 📁 Folder Structure

```text
cambodia-air-quality-analysis/
│
├── data/                 # Raw air quality datasets
├── notebooks/            # Jupyter notebooks
│   └── 01_air_quality_analysis.ipynb (Testing)
│   └── air_quality_analysis.ipynb (Main)
├── README.md             # Project overview & instructions
├── requirements.txt      # Python dependencies
└── .gitignore            # Ignore system files
```

---

## 🌟 Features

* ✅ Safe OpenAQ API fetch with fallback
* ✅ Preprocessing & cleaning pipeline
* ✅ Daily & monthly trend visualization
* ✅ High pollution detection
* ✅ Optional PM2.5 forecasting
* ✅ Interactive and clear plots

---

<p align="center">
  🌫️ Turning Data Into Environmental Insight 🌿
</p>

---
