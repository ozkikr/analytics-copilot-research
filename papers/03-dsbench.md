# DSBench: Data Science Benchmark

**Paper**: arxiv:2402.17168
**Venue**: ICLR 2025
**Code**: https://github.com/LiqiangJing/DSBench

## Task Design

Two task types reflecting real data science demands:

### Data Analysis (466 tasks from ModelOff)
- Global financial data analysis competition tasks
- Excel-based questions requiring Python/Excel/Matlab solutions
- Multi-choice and fill-in-the-blank formats
- Long contexts: up to 28,487 chars introduction text
- Multi-sheet Excel files (up to 4 sheets, 12 tables per challenge)

### Data Modeling (74 tasks from Kaggle)
- Full competition pipelines: training data → build model → predict on test set
- 18 different metrics across competitions
- Training data: 200 to 4.8M samples per competition
- File sizes: 11 KB to 487 GB

## Dataset Statistics

### Data Analysis
| Metric | Mean | Max | Min | Total |
|--------|------|-----|-----|-------|
| Challenges | — | — | — | 38 |
| Questions | 12.3 | 50 | 3 | 466 |
| Intro Length | 749 | 28,487 | 0 | — |
| Excel Files | 0.8 | 2 | 0 | 31 |
| Tables per challenge | 1.3 | 12 | 0 | 49 |

### Data Modeling
| Metric | Mean | Max | Min | Total |
|--------|------|-----|-----|-------|
| Competitions | — | — | — | 74 |
| Metrics | — | — | — | 18 |
| Context Length | 688 | 2,505 | 216 | 50,875 |
| Training Samples | 287K | 4,828K | 200 | 21,270K |
| File Size | 61 GB | 487 GB | 11 KB | 4,519 GB |

## Results

- Best agent: **34.12% accuracy** on data analysis tasks
- Best agent: **34.74% RPG** (Relative Performance Gap) on data modeling tasks
- Current agents fundamentally struggle with multi-file reasoning, long contexts, and real-world data messiness

## Why Agents Fail

- Multi-file reasoning across sheets/tables
- Long context understanding (28K+ char introductions)
- Real-world data messiness (inconsistent formats, Excel quirks)
- End-to-end modeling requires planning, feature engineering, model selection — not just code generation

## Practical Applicability

**Relevance to semantic-layer-grounded copilot: ⭐⭐⭐**

Good reality check benchmark. Shows that raw "throw data at an LLM" approaches fail badly. Reinforces why semantic layers matter:
- If you pre-structure data as governed metrics, you remove the multi-file/messy-format problem entirely
- The 34% accuracy ceiling shows the cost of not having semantic structure
- Useful as a stress test to validate that your semantic layer actually improves agent accuracy
