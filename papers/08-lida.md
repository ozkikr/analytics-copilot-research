# LIDA: Automatic Generation of Grammar-Agnostic Visualizations and Infographics

**Paper**: ACL 2023 (Demo track)
**Author**: Victor Dibia, Microsoft Research
**Code**: https://github.com/microsoft/lida
**Project**: https://microsoft.github.io/lida/

## Architecture: 4-Module Pipeline

### 1. SUMMARIZER
- Converts input data (e.g., CSV) into compact natural language summary
- Extracts: atomic types, field statistics, semantic descriptions
- Optional LLM enrichment for field semantics (e.g., "technical specifications for cars with fields like miles per gallon")
- Purpose: ground all subsequent operations without passing raw data to every LLM call

### 2. GOAL EXPLORER
- Generates visualization goals as structured JSON:
  ```json
  {
    "question": "What is the distribution of miles per gallon?",
    "visualization": "Histogram",
    "rationale": "Shows spread of fuel efficiency across cars"
  }
  ```
- Supports **automatic discovery** (LLM-as-analyst generates hypotheses) or user-provided hypotheses
- **Persona support**: e.g., "CEO with aerodynamics background" shapes goal generation
- This is the **proactive insight generation** module — the system generates questions, not just answers them

### 3. VISGENERATOR
- Generates visualization code across multiple grammars (matplotlib, seaborn, Altair, D3)
- **Self-evaluation**: Scores generated visualizations on aesthetics, accuracy, accessibility
- **Repair loop**: Fixes code errors via re-prompting
- **Natural language refinement**: "Zoom in by 50%", "Change the color to blue"
- **Explanations**: Can describe what a visualization shows
- **Recommendations**: Suggests related visualizations

### 4. INFOGRAPHER
- Creates stylized, data-faithful infographics using Image Generation Models (IGMs)
- Supports personalization (color palettes, styles)
- Ensures data fidelity — generated graphics must accurately represent the data

## Key Features

- **Grammar-agnostic**: Same pipeline works across visualization libraries
- **Multi-LLM**: Supports OpenAI, PaLM, Cohere, HuggingFace backends
- **Python API + Web UI**: Both programmatic and interactive access
- **Multilingual refinement**: NL commands in any language to modify visualizations

## Usage Example

```python
from lida import Manager, llm
lida = Manager(text_gen=llm("openai"))
summary = lida.summarize("data/cars.csv")
goals = lida.goals(summary, n=2)
charts = lida.visualize(summary=summary, goal=goals)
```

## Evaluation

- Outperforms prior auto-viz systems on hypothesis generation, conversational refinement, and multi-grammar support
- Simplifies pipelines (no subtask-specific models needed)
- Enables end-to-end: visualization translation, chart QA, data exploration, data stories

## Practical Applicability

**Relevance to semantic-layer-grounded copilot: ⭐⭐⭐⭐**

The Goal Explorer is the killer module to adapt:
- Replace "CSV summary" with "MetricFlow schema summary" (available metrics, dimensions, entities)
- Generate hypotheses from metric metadata: "Revenue dropped 12% — is it driven by region, product category, or seasonality?"
- The Summarizer becomes a MetricFlow schema + recent metric values summary
- The proactive exploration loop is what makes this more than chat-to-SQL
- Grammar-agnostic visualization means output can target whatever BI tool/dashboard you prefer
- Persona support could be adapted for different analyst roles (executive summary vs. detailed investigation)
