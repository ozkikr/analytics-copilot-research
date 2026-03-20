# DS-STAR: Data Science Agent through Iterative Planning and Validation

**Source**: Google Research
**Paper**: arxiv:2509.21825
**Blog**: https://research.google/blog/ds-star-a-state-of-the-art-versatile-data-science-agent/

## Architecture

DS-STAR is a multi-agent framework with 8 specialized agents operating in an iterative loop:

1. **A_analyzer** — File summarization: generates and executes Python scripts to extract metadata (column names, types, summaries) from all files, creating shared context {dᵢ} for structured and unstructured data
2. **A_planner** — Initial plan creation: outlines analysis steps
3. **A_coder** — Code generation and execution: implements the plan in Python
4. **A_verifier** — Plan sufficiency check: LLM judge evaluates whether the results adequately answer the question
5. **A_router** — Plan revision decisions: decides whether to refine the plan or proceed
6. **A_debugger** — Script repair: fixes failures from schema drift or missing data
7. **Retriever** — Relevant file selection from large data lakes (selects top 100 files)
8. **A_finalyzer** — Final code formatting for benchmark compliance

The system iterates up to 20 rounds (or 10 in some configurations) until the verifier approves or iteration limits are reached.

## Key Design Decisions

- **Text-to-Python over Text-to-SQL**: Designed for real-world messy data across heterogeneous formats (CSV, JSON, Markdown, unstructured text)
- **Model-agnostic**: Tested with Gemini 2.5 Pro and GPT-5
- **Iterative refinement is essential**: Ablations confirm analyzer descriptions, routing, and iterative refinement all drive gains, particularly for multi-step tasks

## Benchmark Results (Gemini 2.5 Pro)

| Benchmark | DS-STAR | Best Prior Agent | Improvement |
|-----------|---------|-----------------|-------------|
| DABStep (hard tasks) | 45.24% | 41.0% | +4.2% |
| DABStep (easy tasks) | 87.50% | — | — |
| KramaBench | 44.69 | 39.79 (DA Agent) | +4.9 pts |
| DA-Code (hard tasks) | 37.1% | 32.0% (DA Agent) | +5.1% |
| DA-Code (overall) | 38.5% | 37.0% | +1.5% |

GPT-5 excels on easy DABStep tasks; Gemini 2.5 Pro on hard ones.

## Limitations

- Max iteration caps constrain complex analyses
- Relies on LLM judge quality for verification
- No semantic layer awareness — operates directly on raw data

## Practical Applicability

**Relevance to semantic-layer-grounded copilot: ⭐⭐⭐⭐**

The plan→code→verify→refine loop is the most transferable architecture pattern:
- Swap the Coder from raw Python to MetricFlow API calls
- The Verifier becomes a semantic validation layer checking metric consistency
- The Router pattern handles "did this analysis actually answer the question?"
- The Analyzer can be replaced by MetricFlow schema introspection

The key insight is that the **verification loop** is what makes this better than single-shot generation — the same principle applies regardless of whether you're generating Python or metric queries.
