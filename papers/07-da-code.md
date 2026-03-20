# DA-Code: Agent Data Science Code Generation Benchmark

**Paper**: arxiv:2410.07331
**Venue**: EMNLP 2024
**Code**: https://github.com/yiyihum/da-code
**Data**: https://huggingface.co/datasets/Jianwen2003/DA-Code
**Benchmark site**: https://da-code-bench.github.io

## Benchmark Design

DA-Code is specifically designed to assess LLMs on **agent-based** data science tasks, distinct from static code generation benchmarks:

### What Makes It Different from HumanEval/DS-1000
- Tasks require **grounding** in real, noisy data (not clean synthetic datasets)
- Tasks require **planning** — multi-step analysis, not single-function completion
- Executable evaluation environment aligned with real-world scenarios
- Docker sandbox execution
- Complex data wrangling + analytics tasks across diverse domains

### Task Format
- Input: task description + real data files
- Output: executable Python/SQL code producing a specific answer
- Categories: Data Wrangling (DW), Machine Learning (ML), Exploratory Data Analysis (EDA)
- Difficulty levels: Easy, Medium, Hard

## DA-Agent Baseline

### Iterative Memory Update
The key architectural contribution — an agent that learns from its own analysis trajectory:

```
m_{t+1} = h(m_t ∪ {(a_{t+1}, code_{t+1}, o_{t+1})})
```

Where:
- `m_t` = agent memory at step t
- `a_t` = action taken
- `code_t` = code generated
- `o_t` = observation (execution output)
- `h` = history-constrained update function

The agent accumulates context about what it's tried and what worked/failed, enabling adaptive coding within a session.

## Results (GPT-4)

| Category | DW | ML | EDA | Easy | Medium | Hard | **Total** |
|----------|------|------|------|------|--------|------|-----------|
| Accuracy | 30.4% | 48.4% | 24.6% | 45.4% | 27.8% | 23.4% | **30.5%** |

### Failure Analysis (EEAA)
- Error categories not fully detailed in available sources
- 30.5% accuracy with the best LLMs highlights massive room for improvement
- ML tasks (48.4%) significantly easier than EDA (24.6%) — suggests structured prediction is more LLM-friendly than open-ended exploration

## Practical Applicability

**Relevance to analytics copilot: ⭐⭐⭐**

- The iterative memory pattern is directly applicable: an analytics copilot that remembers what queries it's run, what anomalies it's found, and what hypotheses it's tested
- The memory update formula provides a clean abstraction for session-level learning
- The benchmark itself is useful for stress-testing agent capabilities
- The gap between ML (48.4%) and EDA (24.6%) suggests that open-ended analytics is the harder problem — exactly where semantic layer grounding should help most
