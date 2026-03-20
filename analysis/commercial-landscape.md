# Commercial Analytics Co-Pilot Landscape (March 2026)

## Architectural Classification

| Architecture | Products | Novelty Level |
|---|---|---|
| **Text-to-SQL wrapper** | Metabase, basic Julius, most "chat with data" tools | ❌ Not novel |
| **Semantic-layer-grounded** | Cortex Analyst, Databricks Genie, Cube AI, ThoughtSpot Spotter, Lightdash | ✅ Novel grounding |
| **Multi-step agentic** | Databricks Genie Agent Mode, Tellius Kaiya, Hex Notebook Agent | ✅ Novel interaction |
| **Authoring copilot** (builds the semantic model) | Cube Copilot, Hex Semantic Model Agent, Lightdash AI Analyst | ✅ Novel — attacks the bottleneck |
| **Self-improving** | Snowflake Verified Query Optimization | ✅ Novel — governed feedback loop |
| **Show-your-work** | Sigma Ask, Hex (graph-aware debugging) | ✅ Novel UX |

---

## Tier 1: Platform Giants (Semantic-Layer-Grounded)

### Snowflake Cortex Analyst
- **Grounding**: Semantic Views (schema-level objects defining business concepts, metrics, relationships, synonyms, examples)
- **Routing Mode**: Prioritises "semantic SQL" over freestyle generation. Metrics/joins/filters pulled from governed Semantic Views, resulting in simpler, more LLM-friendly SQL
- **Verified Query Optimization**: Analyses human-approved queries to improve the semantic layer — a production self-improving loop constrained to governance-approved ground truth
- **RBAC**: Generated queries obey access controls. Does not train on customer data
- **Key architectural choice**: Governance is part of the core execution path, not an afterthought

### Databricks Genie + AI/BI
- **Grounding**: Unity Catalog metric views with semantic metadata (synonyms, display names) explicitly instrumented as LLM grounding data
- **Genie Agent Mode** (FabCon 2026): Multi-step agentic reasoning for complex "why" and "what's next" questions. Auto-generates research plans, tests hypotheses iteratively, combines data exploration with visualizations
- **Genie Code**: Agent for data teams — aids pipelines, ML models, BI dashboards. Understands lineage/governance via Unity Catalog
- **Genie Spaces**: Customizable contexts with tables/metrics. Thinking traces expose prompt interpretation and SQL examples
- **Single reasoning agent** (beta): Concise, low-latency reports for simpler questions
- **Scale**: 98% of SQL warehouse customers using AI/BI. MAUs up >300% YoY as of early 2026
- **Databricks One**: Unified multi-agent chat across entire data estate

### dbt Semantic Layer (MetricFlow)
- **Role**: Not a copilot itself — the grounding layer for copilots
- **Architecture**: MetricFlow compiles metric definitions into a "dataflow-based query plan" and renders engine-specific SQL. "Plan once, run anywhere"
- **API**: GraphQL for querying metrics/dimensions/entities
- **Accuracy impact**: 83% accuracy with semantic layer vs ~40% without
- **Open source**: MetricFlow open-sourced in 2025. OSI (Open Semantic Interchange) compatible
- **Governance**: Metrics-as-code in YAML, version-controlled in git with lineage, docs, CI/CD
- **2025 additions**: Semantic Layer querying within dbt Insights (beta), Trino/Postgres support, metric aliases in GraphQL/JDBC

### Microsoft Copilot (Power BI / Excel / M365)
- **Power BI Copilot**: Queries semantic models for data exploration, report generation, linguistic modeling (synonyms for fields). Prioritises model context over reports for accurate Q&A
- **Excel Copilot**: Surfaces insights from data via natural language
- **Multi-agent coordination**: Agents call each other as tools for complex tasks (rolling out March 2026)
- **Unstructured data**: Processes PDFs, images, email attachments — beyond structured analytics
- **Scale**: 37.5M+ conversations tracked

---

## Tier 2: Semantic Layer Specialists

### Cube AI (cube.dev)
- **Universal semantic layer**: Manages modelling, security context, caching, APIs "upstream of any LLM"
- **AI Data Analyst**: Natural language queries → semantic SQL → interactive visualizations → report refinement. Full explainability within governance controls
- **AI Data Engineer**: Automates semantic model updates, SQL/code generation, version control, integrations. Reduces development from weeks/months to days
- **Cube Copilot** (authoring): Helps engineers author the semantic model with context-aware suggestions, schema/column type awareness, consistency checks. **This is an under-discussed novelty** — the bottleneck in many orgs is creating/maintaining governed semantics, not querying them
- **AI API**: RAG + prompt engineering via REST API. Constrained, traceable responses aligned to semantic models
- **Stress tested**: Validated on joins, ambiguous metrics, non-existent data queries
- **Open source**: Core semantic layer is open source
- **Launch timeline**: Agentic analytics platform June 2025 (D3), expanded October 2025 (anomaly detection agents)

### ThoughtSpot Spotter
- **Grounding**: "Search tokens" from governed semantic layer, not text-to-SQL
- **Agentic Semantic Layer**: Models + TML (ThoughtSpot Modeling Language) for NL search and AI analytics
- **Domain-specific agents**: Dashboard creation, semantic model building
- **Action triggering**: Can trigger actions in enterprise systems from analysis results
- **Released**: Late 2024, with agent expansions in 2025

### Tellius Kaiya Agent Mode
- **Architecture**: Converges **deterministic analytics** (variance analysis, anomaly detection, forecasting, clustering, cohort analysis) with **LLM orchestration** and narrative delivery
- **Autonomous multi-step**: Plans, reasons, executes workflows using SQL + Python
- **RCA**: "From what happened to why in 60 seconds" — automated root cause analysis
- **Key differentiator**: Not just LLM generation — deterministic statistical methods orchestrated by AI. The LLM decides *which* technique to apply, but the technique itself is rigorous

---

## Tier 3: BI Tool AI Features

### Lightdash AI Analyst
- **dbt-native**: Built on top of dbt semantic layer. Deepest dbt integration of any BI tool
- **AI Analyst Co-Pilot features**:
  - Searches existing content before generating new queries
  - Generates queries against governed semantic layer
  - Scheduled deliveries and Slack alerts
  - Batch PRs for new metrics (governance workflow)
  - Coaches users toward governed content over ad-hoc SQL
  - Builds user profiles from common queries
- **Open source**: Core BI layer is open source
- **Enterprise users**: Workday, Collectors

### Hex Notebook Agent
- **Launched**: August 2025
- **Agentic notebook**: Plans analyses, generates SQL/Python, creates visualizations, synthesizes insights
- **Structural editing**: Moves/deletes cells, auto-organizes into sections based on project DAG
- **Graph-aware debugging**: Traverses notebook graphs to identify upstream issues
- **Context awareness**: References full project data, user rules, Hex docs, semantic models
- **Semantic Model Agent**: Automates model building with diff views, version history, semi-additive measures, cross-model calculations
- **Threads**: Conversational interface for non-technical users that converts to notebooks for data team extension
- **Users**: Ramp, Figma, Notion — report dashboards in minutes, reporting from days to 20 minutes

### Sigma Computing (Ask Sigma)
- **"Shows its work"**: Step-by-step intermediate representations — users can inspect/edit any step
- **Source selection**: Uses metadata + usage statistics + endorsement to choose data sources
- **Trusted calculations**: Prefers pre-approved calculation patterns when available
- **UX**: Closer to a notebook/workbook workflow than chat. Exposes a step chain, not a final answer
- **Architectural direction**: The UI exposes an intermediate representation so users can intervene

### Metabase
- **AI status**: Basic automation and visual query builders. No advanced AI copilot
- **Strength**: Very easy for non-technical teams. Good dashboards and SQL editor
- **Best for**: Startups and non-technical teams wanting simple self-serve BI

### Apache Superset / Preset
- **AI status**: No native AI copilot or NLG
- **Strength**: 40+ visualization types, SQL IDE, scalability, plugins, drag-and-drop
- **Best for**: Enterprises needing flexibility and customization

---

## Tier 4: Standalone AI Analyst Products

### Zenlytic (Zoë)
- **Product**: Autonomous AI data analyst for commerce brands
- **Architecture**: "Cognitive layer" for self-serve insights from Excel, CRM, SaaS apps
- **NL queries**: On business data without coding
- **Funding**: $9M Series A (September 2024), led by M13 + Bain Capital Ventures
- **Snowflake Summit 2025**: Demoed conversational analytics

### Julius AI
- **Product**: No-code NL analytics platform
- **Data support**: CSV, Excel, Google Sheets, SQL, Notion, PDFs, images, text files
- **Scale**: Handles 8-32GB files (vs spreadsheet limitations)
- **Performance**: 40% faster insight discovery vs manual analysis
- **Predictive**: Forecasting and scenario modeling
- **Limitation**: "Not built to replace a data analyst for high-stakes financial modeling"
- **Pricing**: Free (15 msgs/mo), Plus (unlimited), Enterprise (custom)

### DataLine
- **Product**: Open source, privacy-first AI data analyst
- **Privacy**: Runs entirely on-device with local SQLite. No cloud, no data sharing with LLMs
- **Connections**: Postgres, Snowflake, MySQL, Azure SQL, MSSQL, CSV, Excel, SQLite
- **Features**: NL→SQL + charts + dashboards + knowledge base (RAG-style)
- **Install**: Homebrew for quick setup
- **Status**: Actively developed, seeking maintainers
- **License**: Open source (GitHub: RamiAwar/dataline)

---

## Tier 5: Data Catalog as Analytics Grounding

### Atlan
- **Role**: Data catalog that provides grounding context for AI agents
- **Metadata Lakehouse**: Open Iceberg-based architecture for scalable, interoperable metadata
- **MCP Server**: Converts metadata into API endpoints for AI agents — enhances text-to-SQL accuracy, prompt chains, compliance
- **AI Governance**: Tags risks, enforces policies, traces agent behavior. EU AI Act auto-classification
- **Recognition**: Gartner Leader in Data & Analytics Governance MQ. Snowflake's 2025 Data Governance Partner of the Year
- **Integration**: Powers context for Cortex Analyst and Databricks Genie
- **Key insight**: Metadata-as-grounding is complementary to semantic-layer-as-grounding. Atlan provides the "about the data" context; semantic layers provide the "business meaning" context

---

## Standards and Interoperability

### Open Semantic Interchange (OSI)
- **What**: Vendor-neutral, YAML-based specification for exchanging semantic models (datasets, metrics, dimensions, relationships, contexts)
- **Led by**: Snowflake (announced Sept 2025, specs finalized Jan 2026)
- **License**: Apache
- **Why it matters**: Enables building copilots around portable semantics — move semantic models across warehouse/BI/agent systems without rebuilding
- **Participants**: Snowflake, dbt Labs (MetricFlow governance doc references OSI)

### Model Context Protocol (MCP)
- **What**: Anthropic's open standard for connecting LLM applications to external tools and data sources
- **Why it matters for analytics**: Pluggable tool-connection layer reduces per-connector bespoke work. Makes multi-tool agent architectures more maintainable
- **Adopters**: Atlan (MCP Server for metadata), various BI tools adding MCP support

---

## The Authoring Copilot Gap

One under-discussed angle: the bottleneck in most orgs isn't querying metrics — it's **building and maintaining the semantic model**.

Only three products currently attack this:
1. **Cube Copilot**: Context-aware suggestions for schema/column types, consistency with existing model
2. **Hex Semantic Model Agent**: Auto-builds with diff views, version history, cross-model calculations
3. **Lightdash AI Analyst**: Batch PRs for new metrics via governance workflows

For a dbt MetricFlow stack, an agent that helps **author and maintain** the semantic YAML (not just query it) would be highly differentiated:
- Auto-suggest new metrics from analyst query patterns
- Detect inconsistencies in metric definitions
- Generate semantic model YAML from existing SQL queries
- PR-based governance workflow for metric changes
- Semantic model quality scoring

---

## Competitive Map: Where To Play

```
                    Semantic Grounding
                         HIGH
                          |
           Cortex    Databricks    Cube
           Analyst   Genie         AI
                |         |         |
    ThoughtSpot *----+----*----+----* Lightdash
    Spotter          |         |
                     |  dbt MetricFlow
                     |  (foundation layer)
                     |
         Tellius ----+---- Hex
         Kaiya       |    Agent
                     |
    Multi-Step  -----+-----  Single-Shot
    Agentic          |         Query
                     |
         Sigma  -----+
         Ask         |
                     |
           Julius ---+--- Metabase
           AI        |
                     |
         DataLine ---+--- Superset
                     |
                    LOW
              Semantic Grounding
```

### The open lane:
**dbt semantic layer + multi-step investigation + proactive insights + causal "why" engine**

No product currently combines all four. Cortex Analyst has the grounding but not the proactive investigation. Databricks Genie has agent mode but is locked to Unity Catalog. ThoughtSpot has the semantic search but not the causal analysis. Tellius has the deterministic analytics but weaker semantic grounding.

Building on dbt MetricFlow (open, portable, OSI-compatible) with the investigation patterns from the academic research (DS-STAR, InsightBench, Causal-Copilot, LIDA) would occupy a genuinely unoccupied position.
