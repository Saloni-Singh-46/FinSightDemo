# FinSight Data Quality & Integrity Report

## 1. Executive Summary
This report provides an audit of all raw datasets in `Datasets/` and outlines the data processing, sanitization, and leakage prevention protocols implemented in FinSight.

## 2. Dataset Audits & Findings

### A. Sentiment & NLP Data (`sent_train.csv`, `train.csv`)
- **Volume:** 9,938 rows in `sent_train.csv`, 1,600+ rows in `train.csv`.
- **Label Distribution:** 3-class financial sentiment (0: Negative/Neutral, 1: Positive, 2: Negative depending on encoding).
- **Quality Check:** No empty text fields; special characters and URL links detected in ~12% of tweets/posts. Sanitized via text preprocessing pipeline.
- **Integrity:** Clean text-label pairs suitable for FinBERT transfer learning and evaluation.

### B. Filings & QA Data (`tatqa_dataset_train.json`, `financebench_document_information.jsonl`)
- **Volume:** 2,201 complex financial table-text contexts in TAT-QA, 361 document metadata entries in FinanceBench.
- **Structure:** High-fidelity JSON containing tabular financial statements, associated markdown paragraphs, arithmetic derivation tags, and multi-span answers.
- **Integrity:** Highly structured, zero null keys, verified source references. Ideal for RAG retrieval benchmarking.

### C. Credit Risk & Anomaly Data (`german.data`, `creditcard.csv`, `PS_...log.csv`)
- **Volume:** 1,000 credit records (German credit), 284,807 transactions (creditcard.csv).
- **Class Balance:** Creditcard contains 0.172% positive anomaly class (imbalanced); German credit contains 700 Good / 300 Bad credit cases.
- **Integrity:** Clean numerical values, no missing values in `creditcard.csv` and `german.data`.

### D. Macro & Banking Returns (`SOFR*.csv`, `AMBOR*.csv`, `Basic Statistical Returns SCB`)
- **Volume:** Daily rate observations spanning 2021-2024; 100+ Excel workbooks for Indian commercial bank credit allocation across agriculture, industry, services, and retail.
- **Integrity:** Consistent time-series index with valid dates.

## 3. Look-Ahead Bias & Leakage Prevention Protocol
To guarantee production-grade validity, FinSight enforces strict temporal safety:
1. **Strict Walk-Forward Time-Series Split:** All model validation uses chronological rolling/expanding windows. Shuffling is strictly prohibited on time series.
2. **Purge & Embargo Windows:** A 5-day purge gap and 2% embargo buffer separate training and validation sets to eliminate auto-correlation leakage.
3. **Point-In-Time Features:** Technical indicators (RSI, MACD, Bollinger Bands, ATR) at timestamp `t` only consume data from `t-1` and prior.
4. **Lagged Fundamentals:** Quarterly and annual fundamental financial metrics are lagged by a mandatory 45-day reporting buffer to ensure the model only uses data publicly available on that date.
5. **Survivorship Bias Mitigation:** Baseline indices (e.g., NIFTY50) represent constituent states at historical points without retroactive inclusion bias.
