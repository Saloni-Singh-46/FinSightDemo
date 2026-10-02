# Model Card: Hybrid Filings-RAG & Citation Grounding Architecture

## 1. System Overview
- **Retrieval Pipeline:** Hybrid BM25 Lexical Matching + Dense Semantic Embeddings (`all-MiniLM-L6-v2`) + Cross-Encoder Proximity Reranker.
- **Ingestion:** Structure-aware chunking preserving tables, page numbers, and section headings.
- **Citation Format:** Strict inline anchors `[doc_name, p.X, chunk_id]`.

## 2. 4-Way Retrieval Ablation Study (30-Item Gold Benchmark)
| Architecture | Hit Rate@5 | MRR (Mean Reciprocal Rank) | Precision@5 | Context Relevancy |
|---|---|---|---|---|
| **1. Dense Semantic Only** | 76.67% | 0.6420 | 58.0% | Moderate on exact numerical balance sheets |
| **2. BM25 Lexical Only** | 83.33% | 0.7150 | 68.5% | Good on exact keywords; misses synonyms |
| **3. Hybrid (Dense + BM25)** | 90.00% | 0.7850 | 74.0% | Strong balance across semantic & entity queries |
| **4. Hybrid + Reranker (FinSight)** | **93.33%** | **0.8240** | **78.5%** | **Optimal precision, top rank accuracy, 0 hallucinations** |

## 3. Faithfulness & Hallucination Mitigation
- Critic Agent asserts claim-level verification against the `evidence_store`. Uncited assertions trigger immediate reflection loops (max 2 revisions).
