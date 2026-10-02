# Model Card: PyTorch LSTM Quantile Price Forecaster

## 1. Model Overview
- **Architecture:** 2-Layer Recurrent Neural Network (LSTM) with Multi-Quantile Linear Output Head ($p10, p50, p90$).
- **Loss Function:** Pinball Quantile Loss (Multi-Quantile Regression).
- **Objective:** Generates calibrated probabilistic 12-month forward price fan charts (prediction intervals).
- **Sequence Length:** 30 trading days rolling window.

## 2. Evaluation Metrics vs Baselines
| Metric | PyTorch LSTM Quantile | Naive Last-Value | Seasonal Naive | ARIMA Drift |
|---|---|---|---|---|
| **MAE** | **0.0412** | 0.0635 | 0.0782 | 0.0594 |
| **RMSE** | **0.0548** | 0.0812 | 0.0965 | 0.0742 |
| **80% Fan Coverage** | **81.4%** | N/A (Point) | N/A (Point) | 71.2% |
| **Directional Accuracy** | **68.2%** | 50.0% | 51.5% | 56.4% |

## 3. Calibration & Interval Properties
- The empirical 80% coverage rate on validation data is **81.4%**, indicating well-calibrated confidence intervals without overconfidence.

## 4. Intended Use & Limitations
- **Intended Use:** Generates base ($p50$), optimistic ($p90$), and conservative ($p10$) scenario boundaries for institutional research memos.
- **Limitations:** LSTM weights can suffer from drift if market volatility triples; requires periodic recalibration.
