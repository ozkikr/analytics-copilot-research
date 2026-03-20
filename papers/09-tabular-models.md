# Tabular Foundation Models: TABULA-8B and TabPFN

## TABULA-8B

**Paper**: arxiv:2406.12031
**Venue**: NeurIPS 2024
**Model**: https://huggingface.co/mlfoundations/tabula-8b

### Architecture
- **Base**: Llama 3-8B fine-tuned for tabular prediction
- **Training data**: 2.1 billion rows from 4.2 million tables (TabLib T4 dataset)
- **Key innovation**: Row-Causal Tabular Masking (RCTM) — block-triangular attention structure allowing tokens to attend within current and preceding rows only, preventing cross-table leakage

### What "Tabular Transfer Learning" Means
- Tables are serialized as natural language (headers + values + special tokens)
- RCTM enables in-context learning across rows within a single table
- Loss computed only on target tokens (labels), not input serialization
- The model learns tabular reasoning patterns that transfer to unseen tables

### Results
| Setting | TABULA-8B vs XGBoost |
|---------|---------------------|
| Zero-shot | >15 pp above random (XGBoost impossible without training data) |
| Few-shot (1-32 shots) | 5-15 pp higher accuracy, even when XGBoost gets 16x more data |

Also beats TabPFN and CatBoost in few-shot settings. Surpasses Claude 3 Sonnet on tabular tasks.

### Limitations
- Evaluation limited to 128 test examples per dataset
- Potential benchmark contamination concerns
- Classification and binned regression only (not full regression)

---

## TabPFN

**Paper**: Published in Nature (January 2025)
**Focus**: Small-to-medium tabular datasets (≤10,000 samples)

### How It Works
- **Prior-Fitted Network (PFN)**: Meta-learns across millions of synthetic datasets
- Approximates Bayesian inference: learns to map (training data, test input) → prediction
- No gradient updates at inference time — just a forward pass
- Trained on synthetic data but generalizes to real-world distributions

### Results
- On datasets ≤10K samples: **surpasses 4-hour-tuned ensembles** (XGBoost + AutoGluon) in **2.8 seconds**
- TabPFN 2.5 scales to 50K samples/2K features
- 100% win rate vs default XGBoost on ≤10K classification
- 87% win rate on larger sets
- Strong on high-dimensional gene expression data, batch-shifted data

### Limitations
- Optimized for small data (d ≤100 features ideally)
- Early versions strict scaling caps (10K samples, 100 features)
- TabPFN 2.5 extends but still bounded

## When to Use What

| Scenario | Best Choice | Why |
|----------|-------------|-----|
| Zero-shot prediction on new table | TABULA-8B | Only option without training data |
| Few-shot (1-32 examples) | TABULA-8B | Beats XGBoost with less data |
| Small dataset (<10K rows) | TabPFN | Fastest, highest accuracy, no tuning |
| Medium dataset (10K-50K) | TabPFN 2.5 | Extended range |
| Large dataset (>50K) | XGBoost/AutoGluon | Traditional ML still wins at scale |
| Prompting with table context | GPT-4/Claude | Better for reasoning about table meaning |

## Practical Applicability

**Relevance to analytics copilot: ⭐⭐**

Niche but interesting for specific copilot capabilities:
- **Anomaly detection**: TabPFN could quickly validate "is this metric pattern unusual?" on small dimensional slices
- **Segmentation suggestions**: TABULA-8B's zero-shot capability could suggest which feature interactions matter
- **Feature importance**: Quick learned priors for which dimensions drive a metric
- Not core architecture — a utility component that plugs into the "why" engine
