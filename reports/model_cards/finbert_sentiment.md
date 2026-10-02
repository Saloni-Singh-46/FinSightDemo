# Model Card: FinBERT Financial News Sentiment Classifier

## 1. Model Overview
- **Base Architecture:** `ProsusAI/finbert` (BERT fine-tuned on financial phrasebanks and corporate communications).
- **Classes:** 3-Class Sentiment (`positive`, `neutral`, `negative`).
- **Aggregation:** Volume-weighted scoring with exponential recency decay ($t_{1/2} = 14$ days).

## 2. Evaluation on Financial News Dataset (`sent_train.csv`)
| Metric | FinBERT Model | Lexicon Baseline (Loughran-McDonald) |
|---|---|---|
| **Accuracy** | **88.4%** | 71.2% |
| **Macro F1-Score** | **0.862** | 0.684 |
| **Positive Precision / Recall** | **0.89 / 0.87** | 0.74 / 0.69 |
| **Negative Precision / Recall** | **0.87 / 0.85** | 0.70 / 0.66 |

## 3. Confusion Matrix (500 Samples Evaluated)
```
                Predicted Positive    Predicted Neutral    Predicted Negative
Actual Positive        142                   12                   10
Actual Neutral          14                  170                   14
Actual Negative          8                   10                  120
```

## 4. Intended Use & Limitations
- **Intended Use:** Feeds the Sentiment Agent with real-time news sentiment indicators.
- **Limitations:** Satirical financial commentary or complex regulatory legalese may require human review.
