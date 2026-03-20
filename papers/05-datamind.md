# DataMind: Scaling Generalist Data-Analytic Agents

**Paper**: arxiv:2509.25084
**Venue**: ICLR 2026
**Code**: https://github.com/zjunlp/DataMind

## Training Pipeline

Four key components addressing challenges in building data-analytic agents:

### 1. Fine-grained Task Taxonomy + Recursive Easy-to-Hard Composition
- Generates diverse, progressively difficult queries from harvested data files
- Covers multiple formats and domains
- Systematic difficulty stratification ensures curriculum learning

### 2. Knowledge-Augmented Trajectory Sampling
- Curates high-quality analysis trajectories
- Model-based filtering: LLM evaluates trajectory quality
- Rule-based filtering: checks code execution success, output validity
- Produces **DataMind-12K**: 12,000 curated trajectory instances spanning domains, tasks, and formats

### 3. Dynamically Adjustable Training Objective
- Combines **SFT** (supervised fine-tuning) and **RL** (reinforcement learning) losses
- Dynamic weighting between objectives during training
- Differs from standard RLHF: focuses on multi-turn code-based analytical reasoning, not general instruction following

### 4. Memory-Frugal Multi-Turn Rollout Framework
- Enables stable code-based agent evaluation and training
- Handles the unique challenges of multi-turn data analysis (context accumulation, code state management)

## DataMind-12K Dataset

- 12,000 curated trajectory instances
- Multi-domain: finance, science, social data, etc.
- Multi-format: CSV, Excel, databases
- Each trajectory: system prompt → multi-turn reasoning → code execution → observation → answer
- Trajectories include thinking tags, code blocks, interpreter outputs, and answer extraction

## Results

**DataMind-14B** achieves **71.16% average** across benchmarks:

| Comparison | DataMind-14B vs. |
|-----------|-----------------|
| DeepSeek-V3.1 | Outperforms |
| GPT-5 | Outperforms |
| OmniSQL-7B | Outperforms |
| TableLLM | Outperforms |

Benchmarks: BIRD, TableBench, DABench

Key efficiency metric: achieves SOTA with only **12K training instances** vs up to 2.5M for baselines.

### Scaling Law
Ablation on data volume (2K, 4K, 8K from DataMind-12K) shows clear scaling: more data = better performance. The pipeline is scalable.

## Key Novelty

- **Trajectories over prompting**: Multi-turn analysis patterns are better learned from demonstration than from instructions
- **Multi-turn RL**: Reinforcement learning on code-based analytical trajectories stabilizes multi-step reasoning
- **12K efficiency**: Dramatically less data needed than alternatives, suggesting high-quality curation matters more than volume

## Practical Applicability

**Relevance to custom analytics agent training: ⭐⭐⭐**

Longer-term play for your stack:
- If you collect analyst trajectories (how your team queries dbt metrics, drill paths, hypothesis sequences), DataMind's recipe shows how to fine-tune a specialized model
- 12K-instance efficiency is promising — you don't need millions of examples
- The trajectory format (question → plan → code → observation → refinement → answer) maps directly to analytics workflows
- Could produce a model that "thinks like your team's analysts" rather than a general LLM
