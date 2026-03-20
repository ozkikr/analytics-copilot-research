# Causal-Copilot: An Autonomous Causal Analysis Agent

**Paper**: arxiv:2504.13263
**Project**: https://www.charonwangg.com/project/copilot/

## Architecture

End-to-end causal analysis pipeline with 5 modules orchestrated by an LLM:

### 1. User Interaction Module
- Natural language query understanding (e.g., "Find causal relationships between smoking and cancer while controlling for socioeconomic factors")
- Extracts structured info: analysis objectives, domain constraints, prior knowledge, algorithm preferences
- Interactive feedback loop at strategic checkpoints for refining the analysis
- Supports CLI, REST API, or GUI

### 2. Preprocessing Module
- **Data Cleaning**: Missing value detection/imputation, constant features, scaling, encoding
- **Schema Extraction**: Variable names, types, contextual insights via LLM
- **Statistical Diagnostics**: 
  - Tabular: correlation, linearity checks, Gaussian tests, heterogeneity assessment
  - Time-series: stationarity checks, lag estimation
  - Diagnostics guide algorithm selection (e.g., non-Gaussian → nonparametric methods)
- Transformation logging for auditability

### 3. Algorithm Selection Module
- **Algorithm Filtering**: Narrows from full algorithm space using data diagnostics + user constraints. Uses causality-specific knowledge memory with conditional performance ratings.
- **Algorithm Ranking**: Dual-criterion evaluation combining encoded expert knowledge + empirical benchmarking evidence
- **Hyperparameter Configuration**: Context-sensitive, balancing statistical rigor with computational efficiency
- **Algorithm Execution**: Runs selected algorithm with error recovery (LLM interprets errors and revises)

### 4. Postprocessing Module
- **Bootstrap Confidence**: Statistical validation of each causal edge via resampling
- **LLM-Guided Graph Refinement**: Moderate-confidence edges assessed for conceptual plausibility. LLM suggestions are "soft" — cannot override high-confidence statistical decisions
- **User Revision Loop**: Interactive refinement of assumptions and edge exclusions

### 5. Report Generation Module
- LaTeX PDF reports with causal graph visualizations
- Intuitive representation: nodes = variables, directed edges = causal relationships
- Effect estimates highlighted for interpretability

## Supported Techniques (20+)

| Family | Techniques |
|--------|-----------|
| Constraint-based | PC, FCI, CD-NOD, PCMCI |
| Score-based | GES, FGES |
| Continuous optimization | NOTEARS, GOLEM |
| LiNGAM family | DirectLiNGAM + variants |

Supports tabular, time-series, and panel data.

## Benchmark Results (F1 Score)

| Scenario | Causal-Copilot | PC | FCI | GES | DirectLiNGAM |
|----------|----------------|-----|-----|-----|--------------|
| Default (p=10, n=1000) | 0.89 | 0.92 | 0.91 | 0.92 | 0.22 |
| **Dense graph (p=0.5)** | **0.65** | 0.41 | 0.44 | 0.40 | 0.26 |
| **Large scale (p=50)** | **0.94** | 0.70 | 0.79 | N/A | 0.23 |
| **Super large (p=100)** | **0.90** | 0.68 | 0.74 | N/A | N/A |
| **Extreme (p=500)** | **0.60** | N/A | N/A | N/A | N/A |
| **Non-Gaussian** | **0.97** | 0.84 | 0.85 | 0.86 | 0.57 |
| **Heterogeneous** | **0.77** | 0.51 | 0.62 | 0.40 | 0.23 |
| **Measurement error** | **0.86** | 0.69 | 0.80 | 0.79 | 0.28 |

Key: massively outperforms individual algorithms on hard cases because it selects the right technique for the data characteristics.

## Key Novelty

Not just wrapping causal libraries with an LLM. The algorithm selection module is a genuine contribution:
- Data diagnostics determine which methods are statistically appropriate
- Knowledge memory contains algorithm profiles with conditional performance ratings
- LLM reasoning + empirical evidence combine for selection
- Postprocessing prevents LLM from overriding statistical confidence

## Practical Applicability

**Relevance to "why did this metric change?" analytics: ⭐⭐⭐⭐**

- If your copilot detects a revenue anomaly via the semantic layer, Causal-Copilot's architecture shows how to automatically run causal discovery on contributing dimensions
- The algorithm selection module (data diagnostics → technique filtering → ranking) is the template for building a "why" engine
- The postprocessing constraint (LLM can't override statistics) is critical for production trust
- Could plug into a semantic-layer-grounded system: metric anomaly → extract dimensional data → causal analysis → "revenue dropped because of X, controlling for Y"
