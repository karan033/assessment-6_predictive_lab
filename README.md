# Chicago Crime Incident Forecasting using Time Series Analysis

![Python](https://img.shields.io/badge/Python-3.13-blue.svg)
![statsmodels](https://img.shields.io/badge/statsmodels-0.14.0-orange.svg)
![pandas](https://img.shields.io/badge/pandas-2.2.3-green.svg)
![Status](https://img.shields.io/badge/Status-Completed-success.svg)

> **Advanced Predictive Analytics (Lab 06)**
> An end-to-end development of a reproducible, leakage-safe time series forecasting pipeline to predict weekly reported incidents using Chicago Open Data, emphasizing responsible analytics.

---

## Table of Contents

1. [Overview](#overview)
2. [Dataset](#dataset)
3. [Project Workflow](#project-workflow)
4. [Models Evaluated](#models-evaluated)
5. [Key Results](#key-results)
6. [How to Run](#how-to-run)

---

## Overview

This repository documents the implementation and evaluation of a time series forecasting pipeline designed to predict the weekly count of reported crime incidents in the City of Chicago (focusing on District 1, with a replication in District 2). 

To responsibly contextualize this data, this project builds autoregressive models using historical API extracts while strictly acknowledging the limitations of administrative records. Special emphasis is placed on establishing chronological train/test boundaries, rigorous residual diagnostics, walk-forward validation, and preventing the use of these forecasts for punitive or person-level predictive policing.

---

## Dataset

The primary data utilized is dynamically fetched from the **City of Chicago Open Data - Crimes API**.

### Dataset Profile
* **Source:** `https://data.cityofchicago.org/resource/ijzp-q8t2.csv`
* **Records:** 82,684 total raw rows extracted for the targeted districts.
* **Date Range:** January 1, 2021 to June 1, 2024.
* **Target:** `incidents`, resampled into a regular weekly frequency (`W-MON`).
* **Locations:** District 1 (Core Experiment) and District 2 (Replication).

---

## Project Workflow

The project follows a rigorous time series forecasting pipeline designed to prevent temporal data leakage and ensure statistical validity.

1. Extracted multi-location incident data dynamically via the Chicago Crimes API with fixed date and district parameters.
2. Constructed a regular weekly time series, locking a chronological train/test boundary (leaving the last 12 periods as a holdout) prior to diagnostic evaluation.
3. Conducted automated exploratory diagnostics—including Augmented Dickey-Fuller (ADF) stationarity tests and Autocorrelation (ACF/PACF) plotting—exclusively on the training split.
4. Optimized candidate ARIMA models using an AIC-driven grid search restricted strictly to the training set.
5. Evaluated model stability using a Rolling-Origin (Walk-Forward) validation protocol with a 52-week initial training window and 8-week steps.
6. Evaluated the selected models on the locked 12-week test set, visualizing Actuals vs. Naive, AR, and ARIMA forecasts with 95% confidence intervals.
7. Conducted rigorous residual diagnostics (Time plots, ACF, Ljung-Box test) to ensure white-noise properties in the model errors.
8. Replicated the entire pipeline identically on District 2 to compare volatility and accuracy across geographical regions.

---

## Models Evaluated

The following models and baselines were configured and evaluated:

* **`Naive Baseline`:** A baseline comparison carrying forward the last observed training value.
* **`AR(4)`:** An Autoregressive model built on 4 historical lags with a constant trend.
* **`ARIMA(0, 1, 1)`:** The best-performing model selected via Grid Search (lowest AIC), utilizing first-order differencing and a first-order moving average component.

---

## Key Results

The **ARIMA(0, 1, 1)** model achieved the best overall performance on the locked holdout set and was selected as the final model.

### Final Locked Test Performance (District 1)

| Model | MAE | RMSE |
| :--- | :---: | :---: |
| **ARIMA(0, 1, 1)** | 29.58 | 39.13 |
| **Naive Baseline** | 38.75 | 48.03 |
| **AR(4)** | 40.24 | 52.63 |

### Major Findings

* The `ARIMA(0, 1, 1)` model achieved the lowest AIC (1818.02) during training and outperformed the Naive and AR(4) models on the holdout test set.
* Rolling-origin validation across 10 folds confirmed model stability with a Mean MAE of 35.96.
* Residual diagnostics (Ljung-Box p-value = ~0.998) confirmed the ARIMA model successfully captured the underlying temporal structures, leaving residuals indistinguishable from white noise.
* Replication in District 2 yielded even tighter forecasting metrics (MAE: 21.267, RMSE: 28.319), proving the pipeline's geographical robustness.
* **Responsible Analytics Constraint:** The forecasted models predict counts of *reported* incidents based strictly on historical recording patterns, which do not represent the actual underlying crime prevalence. Reporting propensity and policing policies heavily bias these numbers. This model serves only as a temporal baseline of administrative records and establishes a strict deployment boundary against punitive or predictive policing uses.

---

## How to Run

### Prerequisites

The project was developed and validated in a Colab/Jupyter environment with the following specifications:

| Software | Version |
| :--- | :---: |
| Python | 3.13 |

*Install required dependencies:*
```bash
pip install pandas numpy statsmodels scikit-learn matplotlib requests joblib
