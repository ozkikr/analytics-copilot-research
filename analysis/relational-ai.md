# RelationalAI — Knowledge Graph Coprocessor for Snowflake

**Website**: https://relational.ai
**Founded**: 2017, Berkeley, CA
**CEO/Founder**: Molham Aref
**Funding**: ~$122M+ (Series B: $75M April 2022, led by Tiger Global, with Madrona, Addition, Menlo Ventures. Possible Series C: $22.5M Dec 2025 unconfirmed)
**Board**: Includes Bob Muglia (former Snowflake CEO)

## What It Is

RelationalAI is a **relational knowledge graph coprocessor** that runs as a Snowflake Native App. It builds knowledge graphs directly on top of existing Snowflake tables — no data movement — then enables reasoning capabilities that SQL alone cannot provide.

### Key Distinction from Semantic Layers

| Approach | What It Does | Grounding Object |
|---|---|---|
| **MetricFlow/Cortex** | Defines metrics → compiles to SQL | Metrics & dimensions |
| **RelationalAI** | Defines entities + rules + relationships → reasons over graph | Knowledge graph |
| **DS-STAR/agents** | Plans → codes → verifies → refines | Raw data/code |

RelationalAI is more expressive than a semantic layer — you can encode complex business logic as rules, not just metric definitions. The trade-off: you must learn Rel (their declarative language) and you're locked to Snowflake.

## Architecture

- **Snowflake Native App** — runs inside Snowflake via Snowpark Container Services
- **No data movement** — reads from Snowflake tables via Streams, writes results back to Snowflake tables
- **Rel language** — declarative logic programming language for defining business rules, relationships, and reasoning
- **Zero-copy cloning, versioning, RBAC** — inherits Snowflake's governance
- **Consumption-based pricing** (Snowflake credits)
- **Availability**: AWS (GA), Azure (preview), 12+ regions

## Reasoning Capabilities

| Type | What It Does | Status |
|---|---|---|
| **Rules-based** | Declarative logic: infer profit from pricing rules, business qualification rules, decision trees | GA |
| **Graph** | Community detection, pathfinding (all/shortest paths), egonet extraction, connection mapping | GA (pathfinding in preview) |
| **Predictive** | Demand/sales forecasts, churn/attrition likelihood, risk/anomaly alerts | Early access |
| **Prescriptive** | Mathematical optimization: pricing, inventory, staffing/scheduling | Early access |

## The "Superalignment" Approach

Fine-tunes LLMs on the business's semantic model (knowledge graph structure). Result: **#1 on Spider 2.0-Snow benchmark at 64.35%** (the hard Snowflake text-to-SQL benchmark where GPT-4 gets 2.2%). Previous best was 61.24%.

Note: As of March 2026, their "Ask Data with Relational Knowledge Graph" entry (AT&T CDO & RelationalAI) scores **86.28** on the overall Spider 2.0 leaderboard, which is strong but below the new leaders (Genloop at 96.70, Native at 96.53).

The key insight: the LLM doesn't generate SQL against raw schemas — it generates queries against the knowledge graph's semantic structure.

## The "It Won't Forget" Feature

When you correct RelationalAI, you write it as a Rel rule. It becomes part of the knowledge graph permanently. This is their self-improvement model — explicit rule authoring rather than implicit learning. Example:

```rel
# Product substitution and halo dynamics
alt = Product.ref()
overlap = Product.overlap_with(alt)
define(Product.substitutes(alt, overlap)) where (overlap > 0.9)
define(Product.revenue_multiplier(1 + count(Product.bundles) * 0.4))

# Delay impact
define(Delay.revenue_impact(sum(
  Product.revenue * Product.revenue_multiplier * (1 - max(Product.substitutes))
))) where (
  Delay.affects(Product)
)
```

## Reviews & Adoption Signal

**Very limited.** No G2, Capterra, or Gartner reviews found. Notable for a company founded in 2017 with $122M+ raised. Possible explanations:
- Enterprise sales motion, not broad market
- Snowflake marketplace distribution, not direct
- Rel is a niche declarative language — small developer community
- One case study: Fortune 50 retailer, $1B incremental revenue over 3 years

## Relevance to Analytics Co-Pilot

**Potential "why" engine**: If on Snowflake, RelationalAI's graph reasoning could power causal chain traversal (supplier → products → inventory → sales → bundles → halo effects) that a flat semantic layer can't express.

**Trade-offs vs. building on MetricFlow**:
- ✅ More expressive (rules + graph + optimization)
- ✅ Knowledge persists as explicit rules
- ✅ Strong benchmark results
- ❌ Locked to Snowflake
- ❌ Must learn Rel language
- ❌ No independent reviews/community signal
- ❌ Consumption pricing opacity
