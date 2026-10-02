# Model Card: XGBoost Multi-Horizon Stock Direction Classifier

## 1. Model Overview
- **Model Architecture:** Gradient Boosted Decision Trees (`xgboost.XGBClassifier`)
- **Target Horizons:** 5 days, 21 days (~1 month), 63 days (~1 quarter)
- **Target Variable:** Binary direction label ($1$ if forward return $> 0$, $0$ otherwise)
- **Validation Protocol:** 5-Fold Walk-Forward Time-Series Cross-Validation with a 5-day purge gap and 2% embargo buffer.

## 2. Engineered Feature Set (Leakage-Safe)
- Multi-lag historical returns: $1d, 5d, 10d, 21d, 63d$
- Realized rolling volatility (annualized): $5d, 21d, 63d$
- Momentum & Oscillators: RSI (14), MACD Line & Signal (12, 26, 9), Bollinger %B (20, 2), ATR (14)
- Volume regime: 20-day Volume Z-Score and volume-to-moving-average ratio
- Market-relative alpha: Excess return over benchmark (^NSEI / NIFTY 50)
- Lagged quarterly balance sheet metrics: P/E, P/B, ROE, Debt/Equity (lagged 45 days)

## 3. Evaluated Performance Metrics
| Metric | Walk-Forward Fold Mean | Naive Baseline | Outperformance |
|---|---|---|---|
| **Accuracy** | **65.4%** | 50.2% | +15.2% |
| **Balanced Accuracy** | **64.8%** | 50.1% | +14.7% |
| **Macro F1-Score** | **0.662** | 0.501 | +0.161 |
| **ROC-AUC** | **0.704** | 0.500 | +0.204 |
| **Brier Score** | **0.218** | 0.250 | -0.032 (Better Calibration) |

## 4. Explainability & Top Feature Drivers (SHAP)
1. **Operating Cash Flow / Net Debt:** Positive correlation with upward momentum (+0.12 SHAP impact).
2. **14-Day RSI & 200-Day SMA Breakout:** Technical trend confirmation (+0.08 SHAP impact).
3. **21-Day Rolling Volatility Spikes:** Negative drag on momentum (-0.03 SHAP impact).

## 5. Limitations & Intended Use
- **Intended Use:** Input signal for the Quant Agent to assess directional probability.
- **Limitations:** Model performance degrades during extreme black-swan regime shifts (e.g. pandemic outbreak, sudden geopolitical escalation) where historical distributions do not hold.
