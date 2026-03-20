# WaitGPT: Interactive Visual Code Monitoring for LLM Data Analysis

**Paper**: arxiv:2408.01703
**Venue**: UIST 2024
**Project**: https://shellywhen.github.io/projects/WaitGPT

## Core Idea

Transforms LLM-generated data analysis code into an interactive visual operation graph, enabling users to monitor, verify, and steer the analysis process rather than passively consuming code output.

## Architecture

### Code → Operation Graph Translation
- **Static analysis** parses Python code (Pandas + Matplotlib focused) into high-level operations
- Each operation becomes a **node** in a flow diagram showing data transformations
- Nodes display runtime state: row/column count changes, operation type (filter, merge, sort, aggregate)
- The diagram grows **in real-time** as code streams from the LLM

### Visual Representation
- **Flow diagram**: Linear sequence of data operations with visual glyphs
- **Scrollytelling**: As users scroll through code, corresponding diagram sections highlight
- **Runtime state**: Each node shows before/after data shape
- Supports hiding raw code for non-technical users

## User Interaction Model

- **Inspect**: Click any operation node to see input/output data at that step
- **Modify**: Change operation parameters mid-execution
- **Resume**: Continue from modified point in a sandbox
- **Retrospective investigation**: Trace back through operations to find where errors occurred

## User Studies

### Formative Study (N=8)
- Interviewed ChatGPT users about data analysis practices
- Identified pain points: disruptive workflow, code verification difficulty, labor-intensive iterations

### Evaluation Study (N=12)
- **83% of participants** successfully identified and corrected errors (vs. 50% with traditional code view)
- Error-spotting time reduced by **up to 50%**
- Enhanced user confidence in results
- Both conditions had standard Python syntax highlighting

## Key Design Implications

1. **"Visible hands"**: Abstract LLM output into high-level operations that align with human cognitive processes
2. **Scrollytelling for LLM content**: Guide users through generated content with synchronized visualization
3. **"Stop" mechanism**: Enable users to prevent error propagation in ongoing LLM generation
4. **Context composition**: Adaptive interfaces for different task granularities

## Limitations

- **Python + Pandas/Matplotlib only**: Requires syntax-specific static analysis
- **Linear code assumption**: Targets fluent interfaces, no complex control flow (loops)
- **Scale**: Current glyph design doesn't scale to tables with >20 columns
- **SQL incompatible**: SQL's reversed execution order vs procedure declaration breaks the flow visualization
- **Small user study**: N=12 may not be representative

## Practical Applicability

**Relevance to analytics copilot UX: ⭐⭐**

UX innovation, not core architecture:
- The "show your work as a steerable graph" pattern is valuable for building trust
- Most relevant when building a notebook-style or dashboard UI for the copilot
- The "mutable execution graph" concept is more interesting than the specific implementation
- For a semantic-layer copilot: could visualize the metric query plan (which metrics, which dimensions, which filters) as an operation graph
- Limitations around SQL compatibility are relevant — would need adaptation for MetricFlow query plans
