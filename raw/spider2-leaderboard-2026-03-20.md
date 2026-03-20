# Spider 2.0 Leaderboard Snapshot (March 20, 2026)

Source: https://spider2-sql.github.io

Spider 2.0 is an evaluation framework with 632 real-world text-to-SQL workflow problems derived from enterprise-level database use cases.

| Rank | System | Organization | Score |
|------|--------|-------------|-------|
| 1 | Genloop's Sentinel Agent v2 Pro | Genloop | 96.70 |
| 2 | Native mini | useNative.ai | 96.53 |
| 3 | QUVI-3 + Gemini-3-pro-preview | DAQUV | 94.15 |
| 4 | TCDataAgent-SQL with Contextual Scaling Engine | Tencent Cloud Big Data | 93.97 |
| 5 | Prism Swarm with Deepthink + Claude-Sonnet-4.5 | Paytm | 90.49 |
| 6 | Genloop's Sentinel Agent v2 | Genloop | 88.48 |
| 7 | QUVI-3 + Claude-Opus-4.6 | DAQUV | 86.28 |
| 7 | Ask Data with Relational Knowledge Graph | AT&T CDO & RelationalAI | 86.28 |
| 9 | ByteBrain-Agent | ByteDance Infra System Lab | 84.10 |
| 10 | Genloop's Sentinel Agent v1.5 | Genloop | 83.36 |
| 11 | AiCheng Agent | alibaba_cfo_tech | 82.81 |
| 12 | Prism Swarm + Claude-Sonnet-4.5 | Paytm | 82.63 |
| 13 | LingXi Agent + Claude-Sonnet-4.5 | Ant Group | 79.89 |
| 14 | Arctic-FLEX | Snowflake AI Research | 75.14 |
| 15 | Sophon-Agent | ByteDance DataPlatform LLM | 74.04 |
| 16 | APEX-SQL | LIGHTSPEED | 73.13 |
| 17 | QiSi-SQL + Deepseek3.2 | Ant Group Tech Risk | 70.38 |
| 18 | PExA | Bloomberg AI Engineering | 70.20 |
| 19 | Chicory AI Agent + Claude Sonnet 4.5 + Opus 4.5 Judge | Chicory AI | 67.28 |
| 20 | SSDAT + GPT-5 | — | 65.63 |
| 21 | DSR-SQL + DeepSeek-R1 | GDUT DMIR Lab | 63.80 |
| 22 | ReFoRCE + o3 | Hao AI Lab x Snowflake | 62.89 |
| 23 | WindAgent + Claude-4-Sonnet | MeiTuan AI For FinData | 61.43 |
| 24 | PAI-DataSurfer Agent | Alibaba Cloud Computing | 60.33 |
| 25 | DSR-SQL (w/o voting) + Kimi K2.5 | GDUT DMIR Lab | 55.94 |
| 26 | AutoLink + DeepSeek-R1 | HUST VLR Lab | 54.84 |
| 27 | PGV-Agent + GLM-5 | — | 50.27 |
| 28 | Meituan-agent | Meituan FinData Intelligence | 45.34 |
| 29 | KDGCCloud-KCILab + Qwen3-Max | KDGCCloud-KCILab Team | 45.15 |
| 30 | AgenticView + GPT-5-mini | Griffith BigData Lab | 40.95 |
| 31 | Chat2DB-Agent + Claude-4-Sonnet | Chat2DB | 38.39 |
| 32 | ReFoRCE + DeepSeek-V3 | — | 38.03 |
| 33 | Spider-Agent + Qwen3-Coder-Plus | Bot-UTAI | 37.80 |
| 34 | ReFoRCE + o1-preview | Hao AI Lab x Snowflake | 31.26 |
| 35 | Spider-Agent + Qwen3-Coder | — | 31.08 |
| 36 | Spider-Agent + Claude-4-Sonnet-20250514 | — | 25.78 |
| 37 | Spider-Agent + Claude-3.7-Sonnet-20250219 | — | 24.50 |
| 38 | Spider-Agent + Claude-3.7-Sonnet-20250219-Thinking | — | 24.31 |
| 39 | Spider-Agent + o1-preview | — | 23.58 |
| 40 | Spider-Agent + o1-2024-12-17 | — | 23.21 |
| 41 | Spider-Agent + o3-mini-2025-01-31 | — | 19.20 |
| 42 | Spider-Agent + Claude-3.5-Sonnet-20241022 | AWS ProServe | 19.01 |
| 43 | Spider-Agent + Claude-3.5-Sonnet-20241022 | — | 15.54 |
| 44 | Spider-Agent + Gemini-2.0-Pro | — | 13.89 |
| 45 | Spider-Agent + GPT-4o-2024-11-20 | — | 12.98 |
| 46 | Spider-Agent + DeepSeek-R1 | — | 10.55 |
| 47 | CollideNL2SQL + GPT-4o | Collide Tech | 9.68 |
| 48 | Spider-Agent + QwQ-32B | — | 8.96 |
| 49 | Spider-Agent + DeepSeek-V3 | — | 8.78 |
| 50 | Spider-Agent + Qwen2.5-Coder-32B-Instruct | — | 5.48 |
| 51 | Dail-SQL + GPT-4o | — | 2.20 |
| 52 | CHESS + GPT-4o | — | 1.28 |
| 53 | DIN-SQL + GPT-4o | — | 0.00 |
| 54 | SFT CodeS-15B | — | 0.00 |

## Notable Observations

- **Genloop dominates** with 3 entries in top 10 (96.70, 88.48, 83.36)
- **Massive gap** between agent-based approaches (top: 96.70) and base model approaches (GPT-4o: 12.98, GPT-4: 2.20)
- **Enterprise players**: Tencent, ByteDance (2 entries), Alibaba (2 entries), Ant Group (2 entries), Bloomberg, Paytm, Meituan, AT&T
- **RelationalAI** at 86.28 via AT&T CDO partnership
- **Snowflake's own** Arctic-FLEX at 75.14 — below external competitors
- **Open source**: Paytm's Prism Swarm is published on GitHub
- **GPT-5** with SSDAT only manages 65.63 — raw model power isn't enough
