# Comparative Analysis: Analytics Co-Pilot Approaches (2024–2026)

## Research Context

**Date**: March 20, 2026
**Question**: What are the genuinely novel approaches for building an analytics co-pilot, beyond wrapping LLMs around SQL generation?
**Methodology**: ChatGPT deep research + Perplexity web search + individual paper analysis across 9 papers/systems

## The Novelty Test

> "What object is the system grounded on?"
> - If grounded on **tables & columns** → you get NL-to-SQL with guardrails
> - If grounded on **semantic entities, governed metrics, executable analysis graphs, and a verification loop** → you're in the newer class

*(From ChatGPT deep research report)*

---

## Paper Comparison Matrix

| Paper | Type | Key Architecture | Grounding | Self-Improving | Interaction Model | Relevance |
|-------|------|-----------------|-----------|----------------|-------------------|-----------|
| DS-STAR | Agent System | 8-agent iterative loop (plan→code→verify→refine) | Raw files | Via verification loop | Reactive (question→answer) | ⭐⭐⭐⭐ |
| InsightBench | Benchmark | AgentPoirot: schema→questions→code→insights→follow-ups | Raw datasets | No | Multi-step investigation | ⭐⭐⭐⭐⭐ |
| DSBench | Benchmark | N/A (evaluation only) | Raw files (ModelOff/Kaggle) | No | Single-shot tasks | ⭐⭐⭐ |
| Causal-Copilot | Agent System | 5-module pipeline (preprocess→select→execute→postprocess→report) | Raw data + causal techniques | Via verified query feedback | Interactive causal analysis | ⭐⭐⭐⭐ |
| DataMind | Training Pipeline | SFT+RL on curated trajectories (12K instances) | Trajectory data | Via trajectory learning | Agent training, not end-user | ⭐⭐⭐ |
| WaitGPT | UX Innovation | Code→operation graph translation | Code operations | No | Steerable visual monitoring | ⭐⭐ |
| DA-Code | Benchmark + Agent | Iterative memory: m_{t+1} = h(m_t ∪ {(a,code,o)}) | Real noisy data | Within-session memory | Reactive with memory | ⭐⭐⭐ |
| LIDA | Insight Pipeline | 4-module: Summarize→Goals→Visualize→Infographic | Data summaries | Self-evaluation/repair | Proactive + conversational | ⭐⭐⭐⭐ |
| TABULA-8B/TabPFN | Foundation Models | Tabular-specialized LLM / prior-fitted network | Native tabular | Pre-trained priors | Prediction API | ⭐⭐ |

## Architectural Patterns That Matter

### Pattern 1: Semantic Grounding (NOT in the papers, but in the products)

None of the academic papers use a semantic layer. All operate on raw data. This is the gap.

The commercial world (Cortex Analyst, dbt MetricFlow, Cube, ThoughtSpot Spotter) has moved to semantic grounding, but academic benchmarks haven't caught up. This means:
- **Opportunity**: A semantic-layer-grounded agent would face no competition on existing benchmarks
- **Risk**: No benchmark exists to validate semantic-layer approaches

### Pattern 2: Iterative Verification (DS-STAR)

The plan→code→verify→refine loop is the most robust architecture:
- Verification as gating mechanism (not unconstrained iteration)
- Router decides whether to refine or accept
- 20 rounds max prevents runaway costs
- Ablations confirm it's essential for multi-step tasks

### Pattern 3: Multi-Step Investigation (InsightBench/AgentPoirot)

The follow-up question loop transforms single-query Q&A into investigation:
- Generate k questions → answer each → generate n follow-ups → select best → loop
- Covers descriptive/diagnostic/predictive/prescriptive analytics
- This is the closest existing pattern to what a production analytics copilot should do

### Pattern 4: Proactive Hypothesis Generation (LIDA)

Goal Explorer generates analysis hypotheses without user input:
- LLM-as-analyst examines data and proposes "what's interesting"
- Persona support shapes the investigation angle
- Grammar-agnostic output adapts to any visualization target

### Pattern 5: Causal "Why" Engine (Causal-Copilot)

Algorithm selection based on data characteristics:
- Statistical diagnostics → technique filtering → ranking
- Bootstrap confidence prevents overconfident claims
- LLM suggestions constrained by statistical evidence
- Scales to p=500 variables where individual algorithms fail

### Pattern 6: Trajectory-Based Training (DataMind)

Learning from demonstrated analysis sessions:
- 12K curated trajectories outperform 2.5M unstructured examples
- SFT+RL with dynamic weighting
- Clear scaling law for more data
- Long-term play for custom analytics agents

---

## Best Path from Zero: Cortex Analyst + MetricFlow Foundation

### Assumptions
- You have dbt with MetricFlow semantic layer
- You're familiar with Cortex Analyst
- Goal: build a copilot that's architecturally novel, not just another text-to-SQL wrapper

### Phase 1: Semantic Foundation (Weeks 1-4)

**What**: dbt Semantic Layer as the copilot's API surface

**Why**: 83% accuracy with semantic layer vs 40% without. This alone is transformative.

**How**:
- MetricFlow GraphQL API as the agent's query interface
- Agent sees metrics/dimensions/entities, never raw tables
- Semantic metadata (synonyms, display names) as LLM grounding data
- OSI (Open Semantic Interchange) spec for portability

**Parallel research path**: None needed — this is well-established in production (Snowflake, Databricks, dbt)

### Phase 2: Investigative Loop (Weeks 4-8)

**What**: DS-STAR's verification loop + AgentPoirot's follow-up loop

**Why**: Single-query Q&A is the #1 limitation of current copilots. Multi-step investigation is what analysts actually do.

**How**:
- **Planner**: Decompose user question into metric queries
- **Executor**: Compile to MetricFlow API calls
- **Verifier**: LLM judge checks "did this actually answer the question?"
- **Follow-up generator**: Generate 3-4 drill-down questions, select best, loop
- Max 5-10 iterations to control costs

**Parallel research paths**:
- **InsightBench** — adapt as your evaluation benchmark (replace raw datasets with semantic layer queries)
- **DS-STAR** — extract the verifier + router pattern; ignore the file analysis parts

### Phase 3: Proactive Insights (Weeks 8-12)

**What**: LIDA's Goal Explorer adapted for metrics + anomaly detection

**Why**: The shift from "answer my question" to "here's what changed and why, before you asked"

**How**:
- Auto-scan metric changes using statistical tests (simple z-scores on metric time series)
- Generate hypotheses from metric metadata: "Revenue dropped 12% — check by region, product, channel"
- Auto-run the investigative loop from Phase 2 for each hypothesis
- Output: proactive insight reports

**Parallel research paths**:
- **LIDA** — Goal Explorer module design, persona support for different analyst roles
- **DA-Agent memory** — carry analysis context across sessions so the copilot remembers what it's investigated

### Phase 4: "Why" Engine (Weeks 12+)

**What**: Causal-Copilot's architecture for root cause analysis

**Why**: "What changed?" is easy. "Why did it change?" is the hard, high-value question.

**How**:
- When anomaly detected, extract dimensional data from semantic layer
- Run Causal-Copilot's pipeline: statistical diagnostics → algorithm selection → causal discovery → postprocessing
- Constrain LLM suggestions with bootstrap confidence (critical for trust)
- Generate "revenue dropped because of X, controlling for Y" narratives

**Parallel research paths**:
- **Causal-Copilot** — the algorithm selection module and postprocessing constraints
- **Tellius Kaiya** — commercial reference for combining deterministic analytics (variance decomposition) with LLM orchestration

### Phase 5: Custom Model Training (Long-term)

**What**: DataMind's trajectory-based fine-tuning on your own analyst patterns

**Why**: A model that "thinks like your team's analysts" vs a general LLM

**How**:
- Collect analyst trajectories: queries, drill paths, hypothesis sequences
- Curate 5K-12K high-quality trajectories
- Fine-tune with DataMind's SFT+RL recipe
- Evaluate on adapted InsightBench

**Parallel research paths**:
- **DataMind** — training pipeline, trajectory curation methodology
- **Snowflake verified queries** — the production version of trajectory-based improvement

---

## The Gap Nobody Has Filled

No existing system combines all of these:
1. ✅ Semantic layer grounding (Cortex Analyst, MetricFlow)
2. ✅ Iterative verification loop (DS-STAR)
3. ✅ Multi-step investigation with follow-ups (InsightBench/AgentPoirot)
4. ✅ Proactive hypothesis generation (LIDA)
5. ✅ Causal "why" engine (Causal-Copilot)
6. ✅ Self-improving via governed feedback (Snowflake verified queries / DataMind trajectories)
7. ✅ Steerable visual execution (WaitGPT)

Building 1-4 would already be differentiated. Adding 5-7 would be genuinely novel.

---

## Key References

| Paper | Venue | Year | arxiv/DOI |
|-------|-------|------|-----------|
| DS-STAR | arXiv | 2025 | 2509.21825 |
| InsightBench | ICLR | 2025 | 2407.06423 |
| DSBench | ICLR | 2025 | 2402.17168 |
| Causal-Copilot | arXiv | 2025 | 2504.13263 |
| DataMind | ICLR | 2026 | 2509.25084 |
| WaitGPT | UIST | 2024 | 2408.01703 |
| DA-Code | EMNLP | 2024 | 2410.07331 |
| LIDA | ACL | 2023 | — |
| TABULA-8B | NeurIPS | 2024 | 2406.12031 |
| TabPFN | Nature | 2025 | — |

### Commercial/Product References
- Snowflake Cortex Analyst: Semantic Views + Routing Mode + Verified Query Optimization
- dbt MetricFlow: Open-source semantic layer compiler (GraphQL API)
- Cube: Universal semantic layer with AI API
- ThoughtSpot Spotter: Semantic-layer-grounded search tokens
- Sigma Computing (Ask Sigma): Step-by-step intermediate representation
- Tellius Kaiya: Deterministic analytics + LLM orchestration
- Open Semantic Interchange (OSI): Vendor-neutral YAML spec for portable semantic models
