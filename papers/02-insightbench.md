# InsightBench: Multi-Step Insight Generation Benchmark

**Source**: ServiceNow Research
**Paper**: arxiv:2407.06423
**Venue**: ICLR 2025
**Code**: https://github.com/ServiceNow/insight-bench
**Data**: https://huggingface.co/datasets/ServiceNow/insight_bench

## What It Measures

InsightBench evaluates whether an agent can perform **end-to-end business analytics**:
- Formulate analytical questions
- Generate code to answer them
- Extract insights with justifications
- Generate follow-up questions for deeper investigation
- Produce actionable insight summaries

Covers four analytics types: **Descriptive** (what happened), **Diagnostic** (why), **Predictive** (what will happen), **Prescriptive** (what to do).

## Dataset

- **31 datasets** across 5 business themes: incident management, user management, enterprise goal management, inventory management, finance management
- Derived from ServiceNow table schemas
- Synthetic data with **planted trends/irregularities** (validated via human study)
- Ground truth: curated Jupyter notebooks with expected insights

## AgentPoirot Architecture

1. **Extract Dataset Schema (S)**: Analyzes columns for name, type, unique/NA values, stats (min/max/mean/std for numerics, date ranges, top uniques for categoricals)
2. **Question Generation (QG)**: Generates k=3 high-level questions from (D, G, S)
3. **For each question**:
   - **Code Generation (CG)**: Produces Python code and plots
   - **Insight Extraction (IE)**: Derives insights with justifications from results
   - **Follow-Up Questions (FQ)**: Generates n=4 follow-ups
   - **Question Selection (QS)**: Picks the best follow-up to loop back
4. **Summary Generation**: Summarizes all insights into actionable report

## Evaluation Methodology

**Two-way mechanism with LLaMA-3-Eval** (open-source alternative to G-Eval):
- Scores dataset overview quality
- Scores question and insight relevance, depth, actionability
- Scores summary coherence
- Ground truth from curated notebooks enables reliable multi-step assessment

## Results

- **GPT-4o** tops proprietary models
- **LLaMA-3-70B** (open-source AgentPoirot) beats GPT-3.5-turbo, matches GPT-4-turbo
- AgentPoirot significantly outperforms PandasAgent due to multi-step, multifaceted analysis
- Ablations confirm gains from: specific goals, diverse follow-ups, schema extraction

## Practical Applicability

**Relevance to semantic-layer-grounded copilot: ⭐⭐⭐⭐⭐**

This is the benchmark to adapt for evaluating an analytics copilot:
- Replace "raw dataset" input with "dbt semantic layer metrics"
- The follow-up question loop is the key differentiator vs single-query systems
- AgentPoirot's architecture is directly implementable
- The planted-trends methodology could be replicated for testing metric anomaly detection
- 31 datasets is small enough to manually curate a dbt-specific equivalent
