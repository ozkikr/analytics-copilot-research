# Architecturally novel approaches for building an analytics co-pilot

## What “genuinely novel” means in analytics copilots

Most “analytics copilots” marketed in 2023–2024 were thin layers over NL-to-SQL: a chat UI sends prompts + raw schema to a general LLM, then runs the generated SQL and summarises results. You explicitly asked to exclude that class, so the useful question becomes: **what architectural moves change the system’s *contract*, not just the model?**  

Across 2024–2026 research and product evolution, the strongest “novel” patterns cluster into four shifts:

**A shift from schema → semantics.** Copilots become *metric-native* and *governance-native* by routing all reasoning through a semantic layer (metrics/dimensions/relationships/synonyms/examples) rather than exposing raw table schemas. Snowflake’s Cortex Analyst explicitly frames this as using Semantic Views that define “business concepts, metrics, and relationships,” and explains that these improve reliability by providing rich metadata, defining business logic, and establishing verified examples. ([docs.snowflake.com](https://docs.snowflake.com/en/user-guide/snowflake-cortex/cortex-analyst))  

**A shift from single-shot generation → tool-augmented workflows.** The co-pilot becomes an orchestrator that plans, executes, verifies, and iterates—often with multiple specialised agents (planner/coder/verifier/router), and explicit tool calls (SQL, Python execution, metadata lookup, DQ checks). Google’s DS-STAR is a canonical example: it adds (i) a file-analysis module for diverse formats, (ii) an LLM-judge verification step for plan sufficiency, and (iii) iterative plan refinement. ([research.google](https://research.google/blog/ds-star-a-state-of-the-art-versatile-data-science-agent/))  

**A shift from “answer my question” → “generate, prioritise, and validate insights.”** New benchmarks such as InsightBench evaluate agents on end-to-end insight generation (“formulating questions, interpreting answers, and generating a summary of insights and actionable steps”), rather than single-query QA. ([iclr.cc](https://iclr.cc/virtual/2025/poster/29227))  

**A shift from chat-only UX → inspectable, steerable, and proactive systems.** Systems like WaitGPT change the interaction model by turning generated code into a step-by-step, interactive visual representation so users can monitor/steer operations and verify results. ([arxiv.org](https://arxiv.org/abs/2408.01703?utm_source=chatgpt.com))  

A practical way to detect “wrapper vs novel” is to ask: **What object is the system grounded on?**  
If it is grounded on *tables & columns*, you often get NL-to-SQL with guardrails. If it is grounded on *semantic entities, governed metrics, executable analysis graphs, and a verification loop*, you are in the newer class. ([docs.snowflake.com](https://docs.snowflake.com/en/user-guide/snowflake-cortex/cortex-analyst))  

## Semantic control planes: metrics-first access instead of raw schemas

The clearest architectural novelty (and the one most likely to persist) is treating the semantic layer as the **copilot’s “API surface”**—a contract that encodes permitted joins, KPI definitions, naming, synonyms, and examples. This changes the copilot from “generate SQL against arbitrary schemas” into “compose requests against a governed semantic model.”

image_group{"layout":"carousel","aspect_ratio":"16:9","query":["Snowflake semantic views diagram","dbt semantic layer MetricFlow architecture","Databricks Unity Catalog metric views diagram","Open Semantic Interchange OSI semantic model"]}

### Database-native semantic objects

**entity["company","Snowflake","cloud data platform"]: Semantic Views + Cortex Analyst**  
Snowflake’s Cortex Analyst documentation is unusually explicit about the architecture: it uses Semantic Views (schema-level objects) to generate accurate SQL; Semantic Views define logical tables, dimensions, facts, metrics, and relationships, and can include synonyms, sample questions, and SQL answers. ([docs.snowflake.com](https://docs.snowflake.com/en/user-guide/snowflake-cortex/cortex-analyst))  
Two particularly “architectural” features signal novelty beyond “chat-to-SQL”:

- **Routing Mode:** a query strategy that prioritises “semantic SQL” and falls back to standard SQL; the point is that metrics/joins/filters are pulled from governed Semantic Views rather than freestyle SQL generation, and the resulting SQL is intentionally simpler / more LLM-friendly. ([docs.snowflake.com](https://docs.snowflake.com/en/release-notes/2025/other/2025-11-14-cortex-analyst-routing-mode))  
- **Optimisation with verified queries:** Snowflake can analyse “verified queries” to add useful information to the semantic layer, improving question coverage. This is a concrete, productised form of “learn from trajectories/logs,” but constrained to governance-approved ground truth. ([docs.snowflake.com](https://docs.snowflake.com/en/release-notes/2025/other/2025-12-02-cortex-analyst-optimization))  

Snowflake also states that Cortex Analyst “does not train on Customer Data,” that it uses semantic-model metadata for SQL generation, and integrates with RBAC so generated queries obey access controls, reflecting a design where governance is part of the core execution path rather than an afterthought. ([docs.snowflake.com](https://docs.snowflake.com/en/user-guide/snowflake-cortex/cortex-analyst))  

**entity["company","Databricks","lakehouse platform company"]: Unity Catalog metric views + semantic metadata for LLM accuracy**  
Databricks positions “metric views” as centralised, governed metric definitions, and (as of March 9, 2026) documents “semantic metadata” in metric views—display names, formats, synonyms—explicitly to improve “large language model (LLM) accuracy” and interpretability for tools like Genie spaces and AI/BI dashboards. ([docs.databricks.com](https://docs.databricks.com/aws/en/metric-views/?utm_source=chatgpt.com))  
The key novelty here is that the semantic layer isn’t only for BI reuse; it is being instrumented as *LLM grounding data* (synonyms, display names, etc.) at the semantic-object level. ([docs.databricks.com](https://docs.databricks.com/aws/en/metric-views/data-modeling/semantic-metadata))  

### Open semantic layers as an LLM-facing gateway

**entity["company","dbt Labs","data transformation company"]: MetricFlow as a semantic compiler, opened for AI/agent ecosystems**  
MetricFlow describes itself as a semantic layer that compiles metric definitions into a “dataflow-based query plan” and renders engine-specific SQL for consistent metric computation. ([github.com](https://github.com/dbt-labs/metricflow?utm_source=chatgpt.com))  
dbt’s 2025 announcement frames open-source MetricFlow as a way to deliver “governed, portable metrics” for trustworthy AI/agents and notes a “plan once, run anywhere” model where the engine compiles dialect-specific SQL from a single spec. ([getdbt.com](https://www.getdbt.com/blog/open-source-metricflow-governed-metrics?utm_source=chatgpt.com))  
The MetricFlow governance doc explicitly ties it to the Open Semantic Interchange initiative, signalling that the “semantic contract” is becoming a shared interface across tools rather than locked inside one BI product. ([github.com](https://github.com/dbt-labs/metricflow/blob/main/GOVERNANCE.md?utm_source=chatgpt.com))  

dbt’s release notes also reference “Semantic Layer querying within dbt Insights” that guides users to build queries based on available metrics/dimensions/entities without writing SQL (beta), reinforcing the pattern that semantics are the primary interface for exploration. ([docs.getdbt.com](https://docs.getdbt.com/docs/dbt-versions/2025-release-notes?utm_source=chatgpt.com))  

**entity["organization","Open Semantic Interchange","semantic model standard"]: interoperability as a first-class goal**  
Snowflake’s OSI announcement (Sept 2025) and subsequent update (Jan 2026) describe OSI as a vendor-neutral, YAML-based specification for exchanging semantic models (datasets, metrics, dimensions, relationships, contexts), with an Apache-licensed repo. ([snowflake.com](https://www.snowflake.com/en/blog/open-semantic-interchange-ai-standard/?utm_source=chatgpt.com))  
This matters architecturally because it enables a co-pilot to be built around **portable semantics**: you can (in principle) move the semantic model across warehouse/BI/agent systems rather than rebuilding the “business meaning layer” per tool. ([snowflake.com](https://www.snowflake.com/en/blog/open-semantic-interchanges-specs-finalized/?utm_source=chatgpt.com))  

### Semantic layer builders and copilots

**entity["company","Cube Dev","semantic layer company"]: semantic layer as a managed API with an authoring copilot**  
Cube positions its semantic layer as managing modelling, security context, caching, and APIs “upstream of any LLM.” ([cube.dev](https://cube.dev/use-cases/llm-and-ai-semantic-layer?utm_source=chatgpt.com))  
Its “Cube Copilot” announcement is notable because the copilot is not primarily answering business questions—it is **helping engineers author the semantic model** by providing context-aware suggestions, awareness of schema/column types, and consistency with existing model structure. ([cube.dev](https://cube.dev/blog/introducing-cube-copilot-your-new-partner-in-building-semantic-layers-with))  
This is an under-discussed novelty: in many organisations, the bottleneck is creating/maintaining governed semantics; authoring copilots attack that bottleneck directly. ([cube.dev](https://cube.dev/blog/introducing-cube-copilot-your-new-partner-in-building-semantic-layers-with))  

## Multi-agent and tool-augmented analytics workflows

If semantic layers define “what you’re allowed to mean,” agentic workflows define “how you safely get to answers.” The research frontier here is **moving from single-agent Q&A to decomposed pipelines with verification**—and the ecosystem now has benchmarks that explicitly measure this capability.

### Benchmarks that define the new target capability

**DA-Code (EMNLP 2024): agent-based data science code generation**  
DA-Code is designed to assess LLMs on agent-based data science tasks with real data, requiring grounding and planning beyond trivial code completion; it provides an executable evaluation environment and includes a baseline “DA-Agent.” ([arxiv.org](https://arxiv.org/abs/2410.07331?utm_source=chatgpt.com))  

**DSBench (ICLR 2025): realistic tasks with long contexts and multi-file reasoning**  
DSBench explicitly argues that prior benchmarks are too simplified; it includes hundreds of data analysis tasks and evaluates agents on long contexts, multimodal backgrounds, and multi-table structures, reporting that even the best agent solves only a minority of tasks in their evaluation. ([proceedings.iclr.cc](https://proceedings.iclr.cc/paper_files/paper/2025/file/50e9ad960ae78b741a6b4fea533f2eaf-Paper-Conference.pdf?utm_source=chatgpt.com))  

**InsightBench / insight-bench (ICLR 2025): multi-step insight generation**  
InsightBench evaluates whether an agent can do end-to-end analytics: formulate questions, interpret answers, and produce actionable insight summaries; it introduces AgentPoirot as a baseline and uses a two-way evaluation mechanism with LLaMA-3 as evaluator. ([iclr.cc](https://iclr.cc/virtual/2025/poster/29227))  

The existence of these benchmarks is itself a sign of novelty: they redefine the goal from “correct SQL” to **correct investigative behaviour**. ([iclr.cc](https://iclr.cc/virtual/2025/poster/29227))  

### Reference architectures emerging from 2025–2026 agent systems

**Iterative plan ↔ execute ↔ verify loops (DS-STAR)**  
DS-STAR’s architecture is explicitly multi-role: a data file analyser to create context, a planner agent, a coder agent, a verifier (LLM judge) and a router to decide how to refine the plan; it iterates until sufficiency is verified or a max round count is reached. ([research.google](https://research.google/blog/ds-star-a-state-of-the-art-versatile-data-science-agent/))  
This is a concrete instantiation of the “self-improving through trajectories” idea, but importantly uses *verification as a gating mechanism* rather than unconstrained learning. ([openreview.net](https://openreview.net/forum?id=WJOgneFtVi))  

**End-to-end causal analytics as an agent pipeline (Causal-Copilot)**  
Causal-Copilot claims to operationalise expert-level causal analysis as an autonomous agent, automating causal discovery + causal inference + algorithm selection + hyperparameter optimisation + interpretation, and integrating “over 20” causal techniques (per the paper). ([arxiv.org](https://arxiv.org/abs/2504.13263?utm_source=chatgpt.com))  
For analytics copilots, this is architecturally important because it represents a direction where the copilot doesn’t only retrieve/aggregate metrics; it can execute *methodologically specialised* analysis workflows that analysts often avoid due to complexity. ([arxiv.org](https://arxiv.org/abs/2504.13263?utm_source=chatgpt.com))  

**Training recipes for generalist data-analytic agents (DataMind, March 2026)**  
The “Scaling Generalist Data-Analytic Agents” paper introduces DataMind, a training pipeline that synthesises tasks and trajectories, filters them with model- and rule-based checks, and trains agents with a dynamically weighted SFT+RL objective; it also emphasises stabilising code-based multi-turn rollouts and releases a trajectory set (DataMind-12K) plus trained models. ([arxiv.org](https://arxiv.org/html/2509.25084v3))  
Even if you don’t adopt this exact recipe, it signals that **trajectory datasets + multi-turn RL/post-training** are becoming core to building table/data-native agents, rather than relying only on prompting proprietary LLMs. ([arxiv.org](https://arxiv.org/html/2509.25084v3))  

### Multi-agent analytics in adjacent “AIOps” domains as a blueprint

Operational root cause analysis (RCA) is structurally similar to “why did this KPI move?” analytics: it needs data collection, hypothesis generation, statistical testing, and narrative explanation under time pressure. Recent RCA systems illustrate how multi-agent, tool-augmented pipelines work under reliability constraints.

- entity["company","Microsoft","technology company"] Research’s “Exploring LLM-based Agents for Root Cause Analysis” argues prior LLM RCA approaches couldn’t dynamically collect diagnostic information; it evaluates a ReAct agent with retrieval tools on production incidents and discusses integrating tools for external diagnostic services. ([microsoft.com](https://www.microsoft.com/en-us/research/publication/exploring-llm-based-agents-for-root-cause-analysis/))  
- RCACopilot (2025) combines statistical tests with LLM reasoning to automate RCA and produce narratives and action steps, explicitly motivated by hallucination risk and fine-tuning limitations. ([arxiv.org](https://arxiv.org/html/2507.03224v1))  
- RCAgent (tool-augmented autonomous agent for cloud RCA) emphasises free-form data collection with tools, and reports superiority over ReAct on multiple RCA aspects; it also notes integration in an Alibaba Cloud Flink platform workflow. ([arxiv.org](https://arxiv.org/abs/2310.16340?utm_source=chatgpt.com))  
- mABC (EMNLP 2024 Findings) uses multiple specialised agents plus a blockchain-inspired voting mechanism to reduce hallucination risk in microservices RCA. ([arxiv.org](https://arxiv.org/abs/2404.12135?utm_source=chatgpt.com))  

These works are not “business analytics copilots,” but they provide reusable architecture: **tool-augmented data collection + multi-agent deliberation + explicit stabilisation/verification**. ([microsoft.com](https://www.microsoft.com/en-us/research/publication/exploring-llm-based-agents-for-root-cause-analysis/))  

## Interaction models beyond chat-to-SQL

A striking 2024–2026 trend is that the most novel systems treat the co-pilot as a *visual/interactive analysis environment*, not a chat box.

### Inspectability and steering as first-class UX

**WaitGPT (2024): code → operation graph for monitoring and steering**  
WaitGPT starts from the observation that LLM-based data analysis often means “LLM writes code, user hopes it’s right.” It proposes transforming generated code into an interactive visual representation so users can understand, verify, and modify individual operations in real time; it also reports user studies suggesting improved error detection and confidence. ([arxiv.org](https://arxiv.org/abs/2408.01703?utm_source=chatgpt.com))  

This is “architecturally” different because it treats the analysis as a **mutable execution graph** with provenance, rather than a static answer string. ([arxiv.org](https://arxiv.org/abs/2408.01703?utm_source=chatgpt.com))  

### “Show your work” as a defence against hallucination in BI

**entity["company","Sigma Computing","business intelligence company"]: Ask Sigma**  
Ask Sigma markets itself as “agentic AI” that “shows its work,” breaking down steps taken to produce an answer so users can double-check and edit any step; it also describes controls for which data sources are accessible, rationale for data-source selection (metadata + usage statistics + endorsement), and use of “trusted calculations” when available. ([sigmacomputing.com](https://www.sigmacomputing.com/product/ask-sigma))  
While partly a product claim, the architectural direction is clear: the UI exposes an **intermediate representation** (a step chain) so the user can intervene—closer to a notebook/workbook workflow than chat. ([sigmacomputing.com](https://www.sigmacomputing.com/product/ask-sigma))  

### Agentic analytics: from investigation to action

**entity["company","ThoughtSpot","analytics software company"]: Spotter**  
ThoughtSpot’s Spotter positioning is directly aligned with your “beyond chat-to-SQL” criterion: it claims to translate questions into “search tokens grounded in your governed semantic layer” (rather than direct text-to-SQL), emphasising traceable/auditable queries; it also describes triggering actions in enterprise systems. ([thoughtspot.com](https://www.thoughtspot.com/product/agents/spotter))  
Independent coverage notes Spotter’s release timeframe (late 2024) and describes additional task-specific agents (e.g., dashboard creation and semantic model building) in recent expansions. ([techtarget.com](https://www.techtarget.com/searchbusinessanalytics/news/366640328/ThoughtSpot-domain-specific-Spotter-agents-target-AI-success?utm_source=chatgpt.com))  

**entity["company","Tellius","analytics company"]: Kaiya Agent Mode**  
Tellius’ Agent Mode announcement claims Kaiya can plan/reason/execute multi-step workflows autonomously using SQL + Python, supporting techniques like variance analysis, anomaly detection, forecasting, clustering, and more, with charts and recommendations. ([prnewswire.com](https://www.prnewswire.com/news-releases/tellius-launches-agent-mode-the-next-evolution-of-the-ai-analyst-302593158.html))  
Regardless of marketing superlatives, the important architectural claim is the convergence of **deterministic analytics** (variance decomposition, cohorting, forecasting) with LLM orchestration and narrative delivery. ([prnewswire.com](https://www.prnewswire.com/news-releases/tellius-launches-agent-mode-the-next-evolution-of-the-ai-analyst-302593158.html))  

### Protocol-level enablers: standardising tool integration

**entity["company","Anthropic","ai research company"]: Model Context Protocol**  
MCP is an open standard to connect LLM applications to external tools and data sources; Anthropic’s announcement frames it as secure, two-way connections, and the specification formalises protocol requirements. ([anthropic.com](https://www.anthropic.com/news/model-context-protocol?utm_source=chatgpt.com))  
For analytics copilots, MCP matters less as a UI and more as a **pluggable tool-connection layer**: it reduces per-connector bespoke work and makes multi-tool agent architectures more maintainable. (This is an inference supported by MCP’s stated goal of standardised integration.) ([anthropic.com](https://www.anthropic.com/news/model-context-protocol?utm_source=chatgpt.com))  

## Table-native foundation models and tabular reasoning advances

A second major axis of novelty is **foundation models and benchmarks designed around tables**, not text. This matters because analytics copilots must reason over structured data, often under constraints like aggregation at the correct grain, join validity, and numeric stability.

### Table reasoning is now its own evaluated capability

Multiple 2024–2025 surveys emphasise that table reasoning has distinct challenges (input representations, structural understanding, prompting vs fine-tuning strategies) and has become a mainstream research sub-area in the LLM era. ([arxiv.org](https://arxiv.org/abs/2402.08259))  

The SUC benchmark (“Structural Understanding Capabilities”) explicitly targets the chaos of table input designs and aims to determine what input designs help LLMs understand tables; it proposes a benchmark to compare designs and includes “self-augmented prompting” as a performance booster. ([microsoft.com](https://www.microsoft.com/en-us/research/wp-content/uploads/2023/12/wsdm24-SUC.pdf))  

ACL 2024 work evaluates table reasoning across different representations (text-based vs image-based tables) and prompting strategies, reporting that representation choice can materially affect performance and that image-based representations can sometimes help. ([aclanthology.org](https://aclanthology.org/2024.findings-acl.23.pdf))  

For analytics copilot builders, the architectural implication is: **tabular grounding is not solved by “just RAG.”** You need deliberate choices about table serialisation, intermediate schemas, multimodal representations, and evaluation protocols. ([microsoft.com](https://www.microsoft.com/en-us/research/wp-content/uploads/2023/12/wsdm24-SUC.pdf))  

### Tabular foundation models as a complementary path to LLMs

The PVLDB 2025 panel frames an ecosystem shift: both LLMs and a “new wave of Tabular Foundation Models (TFMs)” may redefine how we query and integrate data, raising the question “TFMs, LLMs… or both?” ([vldb.org](https://www.vldb.org/pvldb/vol18/p5513-paolo.pdf))  

Concrete examples:

- **TABULA-8B (NeurIPS 2024 / arXiv 2024):** described as an autoregressive language model (architecture identical to Llama 3) specialised for tabular transfer learning; the paper’s model card section states the model date/version and highlights dataset construction considerations (including sensitive PII removal). ([arxiv.org](https://arxiv.org/pdf/2406.12031))  
- **TabPFN (Nature):** positioned as a foundation model for small-to-medium tabular data that can be applied broadly and achieves strong performance within stated dataset size constraints. ([nature.com](https://www.nature.com/articles/s41586-024-08328-6?utm_source=chatgpt.com))  
- **TARTE (2025):** presents a knowledge-pretraining approach to tabular learning and claims to build knowledge-enhanced representations for tables to improve downstream learning. ([arxiv.org](https://arxiv.org/pdf/2505.14415?utm_source=chatgpt.com))  

In an analytics co-pilot, these models don’t replace your warehouse; they can be used as **learned priors for tabular structure**, e.g., suggesting plausible segmentations, detecting unusual feature interactions, or ranking candidate explanations—especially when combined with semantic-layer constraints. (This is an inference grounded in the intent and positioning of TFMs and table-reasoning literature.) ([vldb.org](https://www.vldb.org/pvldb/vol18/p5513-paolo.pdf))  

## Self-improving analytics agents and production measurement

“Self-improving” is easy to claim and hard to ship safely. The highest-signal 2024–2026 implementations constrain learning to **verified artefacts** (approved query logs, unit-tested semantic models, benchmarked trajectories) rather than unconstrained online learning.

### Verified-query and governed-feedback loops in semantic layers

Snowflake’s “optimise semantic views or models with verified queries” feature is a concrete example of self-improvement that stays within governance boundaries: the system analyses verified queries to add useful information to the semantic layer so Cortex Analyst answers a broader range of questions correctly. ([docs.snowflake.com](https://docs.snowflake.com/en/release-notes/2025/other/2025-12-02-cortex-analyst-optimization))  

This is interesting because it converts human usage into system improvement while preserving two critical properties: (i) **ground truth is curated (verified)**, and (ii) improvements are applied to the semantic/guidance layer, not by silently mutating business logic. ([docs.snowflake.com](https://docs.snowflake.com/en/release-notes/2025/other/2025-12-02-cortex-analyst-optimization))  

### Learning from trajectories in agent training (research direction)

The 2026 DataMind paper is explicit about building and filtering trajectories (including model- and rule-based filtering, plus a “knowledge-augmented trajectory sampling strategy”), then training with combined SFT and RL losses; it positions this as necessary for open-source generalist data-analytic agents. ([arxiv.org](https://arxiv.org/html/2509.25084v3))  

DS-STAR similarly uses iterative refinement with an LLM judge to verify plan sufficiency, which can be viewed as *trajectory-level improvement* where each iteration is evaluated before proceeding. ([openreview.net](https://openreview.net/forum?id=WJOgneFtVi))  

These directions align with the fact that modern benchmarks (DA-Code, DSBench, InsightBench) are increasingly trajectory- and workflow-aware rather than answer-only, making them suitable as regression tests for self-improving copilots. ([arxiv.org](https://arxiv.org/abs/2410.07331?utm_source=chatgpt.com))  

(Important caveat: most public work here is still research-grade; production-grade “self-improvement” remains constrained and governance-heavy in commercial systems.) ([docs.snowflake.com](https://docs.snowflake.com/en/release-notes/2025/other/2025-12-02-cortex-analyst-optimization))  

## A novelty checklist for analytics copilots

This section distils the research/product landscape into a checklist you can use to separate “architecturally new” from “LLM wrapper.” Each item below corresponds to patterns evidenced in the cited systems.

**The copilot is semantics-native** if:
- User questions compile into **semantic requests** (metrics/dimensions/entities) rather than joining arbitrary tables, as shown in Semantic Views / metric views / MetricFlow-style compilers. ([docs.snowflake.com](https://docs.snowflake.com/en/user-guide/snowflake-cortex/cortex-analyst))  
- It carries synonym/display semantics explicitly to improve interpretation and LLM grounding (Databricks semantic metadata; Snowflake Semantic Views metadata). ([docs.databricks.com](https://docs.databricks.com/aws/en/metric-views/data-modeling/semantic-metadata))  
- Semantic interoperability is treated as an ecosystem goal (OSI), suggesting portability beyond a single vendor tool. ([snowflake.com](https://www.snowflake.com/en/blog/open-semantic-interchanges-specs-finalized/?utm_source=chatgpt.com))  

**The copilot is workflow-native (agentic) rather than prompt-native** if:
- It decomposes tasks into roles and loops (planner/coder/verifier/router) with explicit plan sufficiency checks (DS-STAR). ([research.google](https://research.google/blog/ds-star-a-state-of-the-art-versatile-data-science-agent/))  
- It is evaluated on multi-step insight generation, not single answers (InsightBench), and/or designed around agent benchmarks like DA-Code. ([iclr.cc](https://iclr.cc/virtual/2025/poster/29227))  
- It can run “hard” investigations across heterogeneous datasets and formats (DSBench; DS-STAR). ([proceedings.iclr.cc](https://proceedings.iclr.cc/paper_files/paper/2025/file/50e9ad960ae78b741a6b4fea533f2eaf-Paper-Conference.pdf?utm_source=chatgpt.com))  

**The copilot changes the interaction model** if:
- It exposes a steerable intermediate representation (operation graphs, step breakdowns, editable chains), as in WaitGPT and Ask Sigma. ([arxiv.org](https://arxiv.org/abs/2408.01703?utm_source=chatgpt.com))  
- It is proactive (detects anomalies and launches investigations) rather than purely reactive; Tellius’ autonomous RCA framing is an example of this product direction. ([tellius.com](https://www.tellius.com/resources/blog/ai-powered-root-cause-analysis-from-what-happened-to-why-in-60-seconds?utm_source=chatgpt.com))  

**The copilot is table-native (or table-aware) in modelling and evaluation** if:
- It uses table-specific reasoning evidence (SUC benchmark; representation studies like ACL 2024), and can justify design choices around table serialisation, multimodal tables, and evaluation. ([microsoft.com](https://www.microsoft.com/en-us/research/wp-content/uploads/2023/12/wsdm24-SUC.pdf))  
- It leverages TFMs/benchmarks where appropriate (TABULA-8B, TabPFN) rather than relying purely on text LLM priors. ([arxiv.org](https://arxiv.org/pdf/2406.12031))  

**The copilot is self-improving in a governed way** if:
- Improvement loops are anchored to verified artefacts (e.g., Snowflake’s verified-query optimisation) rather than unconstrained online learning. ([docs.snowflake.com](https://docs.snowflake.com/en/release-notes/2025/other/2025-12-02-cortex-analyst-optimization))  
- It has an explicit trajectory data strategy (DataMind-12K) and multi-turn training approach for stability, instead of treating prompts as the only “engineering surface.” ([arxiv.org](https://arxiv.org/html/2509.25084v3))  

Taken together, the state-of-the-art (2024–2026) “architecturally novel” analytics copilot is best understood as an **analytics control plane**: it sits above governed semantics, orchestrates multi-step tool workflows with verification, changes the user interface from chat to steerable analysis, and improves via constrained feedback loops rather than free-form adaptation. ([docs.snowflake.com](https://docs.snowflake.com/en/user-guide/snowflake-cortex/cortex-analyst))
