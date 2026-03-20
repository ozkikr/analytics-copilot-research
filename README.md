# Analytics Co-Pilot Research: Beyond "LLM Generates SQL"

Research compilation on state-of-the-art approaches for building analytics co-pilots that are architecturally novel — not just wrappers around LLM SQL generation.

**Date**: March 20, 2026

## Motivation

Most "analytics copilots" are thin layers over NL-to-SQL: a chat UI sends prompts + raw schema to a general LLM, then runs the generated SQL. The question driving this research: **what architectural moves change the system's contract, not just the model?**

## Key Finding

> The SOTA analytics copilot is best understood as an **analytics control plane**: it sits above governed semantics, orchestrates multi-step tool workflows with verification, changes the UI from chat to steerable analysis, and improves via constrained feedback loops.

## Structure

```
├── README.md                              # This file
├── raw/
│   ├── deep-research-output.json          # ChatGPT deep research metadata
│   └── deep-research-full-report.md       # Full deep research report (30K chars)
├── papers/
│   ├── 01-ds-star.md                      # Google's multi-agent data science system
│   ├── 02-insightbench.md                 # End-to-end insight generation benchmark
│   ├── 03-dsbench.md                      # Realistic data science benchmark
│   ├── 04-causal-copilot.md               # Autonomous causal analysis agent
│   ├── 05-datamind.md                     # Trajectory-based agent training
│   ├── 06-waitgpt.md                      # Interactive visual code monitoring
│   ├── 07-da-code.md                      # Agent data science benchmark
│   ├── 08-lida.md                         # Proactive insight/visualization pipeline
│   └── 09-tabular-models.md               # TABULA-8B + TabPFN foundation models
└── analysis/
    └── comparative-analysis.md            # Cross-paper synthesis + recommended path
```

## Papers Covered

| # | Paper | Venue | Key Contribution | Relevance |
|---|-------|-------|-----------------|-----------|
| 1 | **DS-STAR** | arXiv 2025 | 8-agent iterative plan→code→verify→refine loop | ⭐⭐⭐⭐ |
| 2 | **InsightBench** | ICLR 2025 | Multi-step insight generation benchmark + AgentPoirot | ⭐⭐⭐⭐⭐ |
| 3 | **DSBench** | ICLR 2025 | Realistic data science tasks (34% best accuracy) | ⭐⭐⭐ |
| 4 | **Causal-Copilot** | arXiv 2025 | End-to-end causal analysis with 20+ techniques | ⭐⭐⭐⭐ |
| 5 | **DataMind** | ICLR 2026 | Trajectory-based SFT+RL training (12K instances beats GPT-5) | ⭐⭐⭐ |
| 6 | **WaitGPT** | UIST 2024 | Interactive operation graphs (83% error detection) | ⭐⭐ |
| 7 | **DA-Code** | EMNLP 2024 | Agent benchmark with iterative memory | ⭐⭐⭐ |
| 8 | **LIDA** | ACL 2023 | Proactive insight + visualization pipeline | ⭐⭐⭐⭐ |
| 9 | **TABULA-8B + TabPFN** | NeurIPS 2024 / Nature 2025 | Table-native foundation models | ⭐⭐ |

## Commercial/Product Landscape

- **Snowflake Cortex Analyst**: Semantic Views + Routing Mode + Verified Query Optimization
- **dbt MetricFlow**: Open-source semantic layer compiler (83% accuracy with semantic layer vs 40% without)
- **Cube**: Universal semantic layer with AI API + AI Data Analyst/Engineer agents
- **ThoughtSpot Spotter**: Semantic-layer-grounded search tokens
- **Sigma Computing (Ask Sigma)**: Step-by-step intermediate representations
- **Tellius Kaiya**: Deterministic analytics + LLM orchestration
- **Open Semantic Interchange (OSI)**: Vendor-neutral YAML spec for portable semantic models

## Recommended Architecture

For starting from zero with a Cortex Analyst / MetricFlow foundation:

1. **Phase 1**: Semantic layer as the copilot's API surface (MetricFlow GraphQL)
2. **Phase 2**: DS-STAR verification loop + AgentPoirot follow-up investigation
3. **Phase 3**: LIDA Goal Explorer for proactive hypothesis generation
4. **Phase 4**: Causal-Copilot "why" engine for root cause analysis
5. **Phase 5**: DataMind trajectory-based fine-tuning on your own analyst patterns

See [analysis/comparative-analysis.md](analysis/comparative-analysis.md) for the full synthesis.

## Methodology

- **ChatGPT Deep Research** via supersub CLI for broad literature survey
- **Perplexity web search** for targeted paper/product details
- **Individual paper analysis** for each of the 9 papers
- **Cross-paper synthesis** for architectural patterns and recommended path

## License

Research compilation for internal use. Individual papers are subject to their respective licenses.
