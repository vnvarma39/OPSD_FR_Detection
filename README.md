France Electricity Load Forecasting & Anomaly Detection (2018–2020)**

This project implements a complete forecasting and anomaly-detection pipeline for **France (FR)** electricity load data using **OPSD Time Series (hourly) from 2018–2020**.
The workflow follows the required structure from the provided assignment document, including:

* Data setup
* Splits & backtesting
* Classical and neural forecasting models
* Metrics reporting
* Anomaly detection
* Organized output folders
* Exportable results for evaluation

---

# **1. Project Overview**

This repository builds a unified forecasting system for FR electricity demand.
It covers both **classical statistical models** (ARIMA, SARIMA) and **neural sequence models** (RNN, LSTM, GRU, Encoder-Decoder with Attention).

Each model follows identical evaluation splits and produces standardized output files.

---

# **2. Data Used**

* Source: **OPSD Hourly Time Series**
* Country: **France (FR)**
* Years used: **2018–2020**
* Columns included:

  * `timestamp`
  * `load` (renamed from `<CC>_load_actual`)

Before modeling, data is:

* Converted to hourly frequency
* Sorted by timestamp
* Missing values imputed via **time interpolation**

---

# **3. Required Splits (Strictly Followed)**

The dataset is divided chronologically:

| Set                  | Length    | Purpose             |
| -------------------- | --------- | ------------------- |
| **Train**            | First 80% | Model fit           |
| **Dev (Validation)** | Next 10%  | Backtest evaluation |
| **Test**             | Last 10%  | Final evaluation    |

Additionally:

* Backtesting uses **expanding origin**
* **Horizon = 24 hours**
* **Stride = 24 hours**
* **Warmup = 60 days** minimum history required to start evaluation

All models use the **same splits**.

---

# **4. Models Implemented**

### **4.1 Classical Models**

1. **ARIMA (baseline)**
2. **SARIMA (grid search optimized)**

   * Fast, memory-efficient search
   * Final selection based on lowest BIC

Each model produces:

* 24-hour **test forecast**
* Standardized forecast CSV
* Evaluation metrics
* Visual metric summaries (for SARIMA/ARIMA)

---

### **4.2 Neural Models**

1. **Vanilla RNN**
2. **LSTM**
3. **GRU**
4. **Encoder-Decoder LSTM**
5. **Seq2Seq with Attention**

Each neural model uses:

* Sequence length: 168 hours
* Forecast horizon: 24 hours
* Normalized inputs
* Early stopping
* Identical splits and backtests

Each neural model produces:

* Dev forecasts
* Test forecasts
* Metrics
* Loss curves
* Saved model weights (optional)

---

# **5. Metrics Reported**

For **each model** and for **Dev** & **Test**:

* **MASE** (primary)
* **sMAPE**
* **MSE**
* **RMSE**
* **MAPE**
* **80% Prediction Interval Coverage**

Metrics are saved as:

* CSV: `FR_forecasts_metrics_<MODEL>.csv`
* PNG: `FR_metrics_<MODEL>.png`

---

# **6. Forecast File Format**

Each forecast CSV follows the required structure:

| timestamp | y_true | yhat | lo | hi | horizon | train_end |
| --------- | ------ | ---- | -- | -- | ------- | --------- |

Files saved as:

```
outputs/FR_forecasts_test_<MODEL>.csv
outputs/FR_forecasts_dev_<MODEL>.csv
```

---

# **7. Anomaly Detection**

Performed on **Test** residuals only.

### **Methods Used**

1. **Rolling Z-score (336h window, min 168h)**

   * Flag if `|z| ≥ 3`
2. **CUSUM (k=0.5, h=5.0)**

   * Flags structural shifts

### **Output File**

Saved as:

```
anomalies/FR_anomalies_<MODEL>.csv
```

Columns include:

| timestamp | y_true | yhat | resid | z_resid | flag_z | flag_cusum |

---

# **8. Folder Structure**

```
├── data/
│   └── FR_2018_2020.csv
├── outputs/
│   ├── FR_forecasts_test_ARIMA.csv
│   ├── FR_forecasts_test_SARIMA.csv
│   ├── FR_forecasts_test_LSTM.csv
│   ├── ... 
├── anomalies/
│   └── FR_anomalies_SARIMA.csv
├── metrics/
│   ├── FR_metrics_ARIMA.png
│   ├── FR_metrics_SARIMA.png
│   ├── ...
└── plots/
    ├── acf_pacf_FR.png
    ├── trend_seasonal_FR.png
    └── ...
```

---

# **9. Key Features of the Pipeline**

* Fully automated **end-to-end forecasting system**
* Memory-efficient **SARIMA grid search**
* Identical splits across all models
* Consistent backtesting logic
* Standardized CSV outputs
* Clear metric comparisons
* Professional anomaly detection
* Modular code design suitable for Colab

---

# **10. How to Run**

Each section of code is arranged in **Google Colab cells**:

1. Install dependencies
2. Setup folders
3. Load and preprocess data
4. Plot STL, ACF/PACF
5. Run ARIMA
6. Run SARIMA grid search
7. Fit final SARIMA
8. Train neural models
9. Backtest & evaluate
10. Save outputs + anomalies

---

