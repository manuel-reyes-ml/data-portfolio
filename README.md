# 📊 Data & AI Portfolio

![AI-Focused](https://img.shields.io/badge/AI--Focused-Data_Engineering_→_Applied_AI-blueviolet?style=for-the-badge)

**Manuel Reyes** — Production systems, evaluation-gated, built in a regulated financial domain.

[![LinkedIn](https://img.shields.io/badge/LinkedIn-mr410-0077B5?logo=linkedin)](https://linkedin.com/in/mr410)
[![GitHub](https://img.shields.io/badge/GitHub-manuel--reyes--ml-181717?logo=github)](https://github.com/manuel-reyes-ml)
[![Email](https://img.shields.io/badge/Email-Contact-D14836?logo=gmail)](mailto:manuelreyesv410@gmail.com)
[![Roadmap](https://img.shields.io/badge/Roadmap-v10.0-28a745)](https://manuel-reyes-ml.github.io/learning_journey/roadmap.html)

---

## Positioning

This portfolio is organized around a single thesis: **proof over keywords.** Each project is a production-grade system with evaluation gates, most of them grounded in ERISA-regulated retirement-plan operations — the domain I've worked in for years. The evidence layer (shipped systems, eval thresholds, decision records) is the point; the credentials and titles are secondary.

The through-line:

- **Real production impact** — a 1099 reconciliation platform running live at work (details below), not a tutorial clone.
- **Domain depth as a moat** — ~15 years of business operations and financial-services work, applied to problems where correctness is regulated, not optional.
- **Evaluation-first engineering** — DeepEval / RAGAS / SelfCheckGPT as *blocking* gates, faithfulness thresholds, synthetic data in every public repo.
- **One system per project, evolved across stages** — projects are hardened S1 → S3, never rebuilt per stage.

Full plan and rationale: the [v10.0 career roadmap](https://manuel-reyes-ml.github.io/learning_journey/roadmap.html) (3 stages, ~32 months).

---

## 🗂️ Portfolio at a Glance

**3 flagships + 2 supporting**, plus a backlog and shared tooling. *Flagship vs supporting denotes size and emphasis — not a quality tier. Every project carries the full production standard.*

| Project | Role | Arc | What it demonstrates | Status |
|---|---|---|---|---|
| 🧾 **1099 / DataVault Data Platform** | 🚩 Flagship — Data Engineering | S1 → S3 | Production ETL + reconciliation → dbt-tested models (CI) → orchestration → an Applied-AI natural-language analyst layer (HITL on every write) | ✅ **S1 core live in production** |
| 📋 **PolicyPulse** | 🚩 Flagship — Applied AI (RAG) | S1 → S3 | RAG over plan documents → GraphRAG hybrid (Neo4j + ChromaDB) → agentic + eval/observability; exposes a FastMCP server | 🏗️ **S1 shipping** |
| 🔥 **Crucible** | 🚩 Flagship — Autonomous trading research | S1 → S3 | Multi-timeframe (swing → intraday) backtest engine + integrity spine → agentic execution; **mandatory HITL sign-off + kill-switch** on the live path | 🏗️ **S1 in progress** |
| 📈 **Attention-Flow Catalyst (AFC)** | Supporting — Research | S1 → S3 | Eval-first core: SEC-grounded faithfulness benchmark + controlled-perturbation catalog; GraphRAG financial-KG | 🏗️ **S1 core** |
| 📄 **FormSense** | Supporting — Document ops | S1 → S3 | Multimodal structured extraction from distribution forms; Pydantic frozen-schema contract; agentic workflow | 📅 Planned |
| 📺 StreamSmart · 📊 ODI | Backlog | — | Production-grade when built; ODI is a consolidation candidate against the platform's mart/AI layer | 🗒️ Backlog |
| 🧩 **Cadence** | Support tooling | — | Build-in-public content pipeline (a tool, not a portfolio flagship) | 🛠️ Tooling |
| ⚙️ **signalcore** | Shared library | — | Point-in-time-safe primitives beneath AFC + Crucible (see the [boundary spec](project_scopes/CRUCIBLE/Shared_SignalCore_Boundary_Spec_v1_3.md)) | 📚 Library |

Each project links to its scope document(s) in the [Repository Structure](#-repository-structure) section below.

---

## 🏆 Production Highlight — 1099 / DataVault Data Platform (S1 core)

**✅ Live in production at Daybright Financial.** The Data-Engineering flagship's Stage-1 core: ingests Matrix + Relius sources into a canonical model, reconciles them, derives Box-7 codes, and surfaces corrections analytics.

| Metric | Impact |
|---|---|
| ⚡ Time saved | ~95% reduction (hours → minutes per cycle) |
| 💰 Cost savings | ~$15,000 / year in labor |
| 📊 Scale | 10× capacity vs. the manual process |
| ✅ Correctness | Caught a tax-code error a manual pass missed (Dec 2025) |

**Stack:** Python · pandas · pytest · GitHub Actions CI · synthetic-data fixtures for the public build.

> The public portfolio build uses synthetic data only — the production deployment runs internally on regulated data.

---

## 🚀 Project Summaries

### 🧾 1099 / DataVault Data Platform — *Data-Engineering flagship*
One system across the arc, not two projects. **Stage 1:** the live 1099 reconciliation core (canonical model → reconcile → Box-7 derivation → corrections analytics). **Stage 2:** hardened into a data platform — dbt-tested models (CI-gated), orchestration (Airflow), data contracts, containerized deploy (Docker/ECS), monitoring. **Stage 3:** the Applied-AI analyst layer — natural-language querying with **human-in-the-loop on every write**.

**Stack:** Python · pandas · dbt · Great Expectations · Airflow · Snowflake · Docker · GitHub Actions CI · Anthropic SDK · Pydantic

---

### 📋 PolicyPulse — *Applied-AI flagship (RAG)*
Retrieval-augmented answering over retirement-plan documents with cited sources and confidence-gated escalation. Exposes a **FastMCP server** so an editor/assistant can query the knowledge base directly. Evolves from vector RAG (S1) to a **GraphRAG hybrid — Neo4j + ChromaDB** (S2/S3) for multi-hop questions vector-only retrieval stitches together wrong, then to agentic workflows with a three-layer eval spine.

**Stack:** Python · Anthropic SDK (primary) · ChromaDB · Neo4j (GraphRAG) · Gemini embeddings · FastMCP · Arize Phoenix · RAGAS · SelfCheckGPT · DeepEval · Streamlit · Pydantic · Docker

---

### 🔥 Crucible — *Autonomous trading-research flagship*
A strategy-agnostic platform taking a strategy from **backtest → paper → live** through validation gates. **Multi-timeframe (swing → intraday)** — swing-first is the lower-risk on-ramp; intraday plugins follow once swing clears all three integrity gates. Strategies are plugins (Protocol + ABC + registry). Research-loop integrity is enforced by a sealed out-of-sample vault + an overfitting-budget ledger + an engine-parity gate. Live execution is the one genuinely irreversible path, so it is gated by **mandatory HITL sign-off + a kill-switch**; the LLM sits *behind the Wall* (proposes, never executes). Local-first inference keeps cost at zero and proprietary data on-machine.

**Stack:** Python · own event-driven backtest harness → NautilusTrader · Optuna · DuckDB/Parquet · Ollama/Qwen (local-first) → cloud fallback · Pydantic · LangGraph · Alpaca (paper + live) · DeepEval · Docker

> ⚖️ *Educational/research project. Not investment advice; makes no claim of positive expectancy — validation is the entire point.*

---

### 📈 Attention-Flow Catalyst (AFC) — *Supporting (research)*
An eval-first research system on small-cap attention/accumulation signals. The Stage-1 deliverable is the smallest publishable high-signal artifact: a **SEC-grounded faithfulness benchmark + a controlled-perturbation catalog**. A Neo4j financial knowledge graph anchors the later GraphRAG work. Shares a thin primitives layer (`signalcore`) with Crucible — siblings, no merge.

**Stack:** Python · DuckDB/Parquet · edgartools (SEC) · Neo4j · Anthropic SDK · SelfCheckGPT · FActScore · DeepEval · Streamlit · Docker

---

### 📄 FormSense — *Supporting (document operations)*
Multimodal structured extraction from retirement-plan distribution forms (checkboxes, signatures), validated against business rules and routed to operations. An **agentic workflow** (control flow in code, not a free-roaming multi-agent), with a **Pydantic frozen-schema contract** on the output.

**Stack:** Python · Gemini Vision · Pydantic · DeepEval · Streamlit · Docker

---

### 📺 StreamSmart · 📊 ODI — *Backlog*
Production-grade when built, positioned last. StreamSmart is a consumer subscription-optimization app; ODI (Operations-Demand-Intelligence) is enterprise workflow-demand analytics, flagged as a consolidation candidate against the DataVault platform's mart/AI layer.

---

## 🏗️ Production Standard (v10.0 — non-negotiable, all projects)

Every project ships with:

- **Mermaid architecture diagram** · **C4 Context diagram** (+ Container view on lead flagships)
- **`docs/adr/`** — numbered, immutable Architecture Decision Records (context → decision → consequences)
- **Dockerfile** · **evaluation-metrics table** · 15–30s **demo GIF** · **"What I Learned"**
- **Eval-first blocking gates** · **synthetic data only** in public repos · no vibe coding
- `pyproject.toml` + `src/` layout + `py.typed` + ruff + mypy · Conventional Commits · file-by-file review before merge

*Stage 3 adds an ADR set + an architecture-defense rehearsal — present and defend the design against a reviewer, mirroring the FDE panel format.*

---

## 🛠️ Technical Stack

**Languages & Core** — Python · SQL · pandas · Jupyter

**Data Engineering** — dbt · Great Expectations · Airflow (Astronomer) · Snowflake · Terraform · DuckDB/Parquet · Docker

**AI & GenAI** — Anthropic SDK (primary) · Ollama / Qwen (local-first) · LM Studio · Gemini SDK · FastMCP · LangGraph · Pydantic · ChromaDB · Neo4j (GraphRAG)

**Evaluation & Observability** — DeepEval · RAGAS · SelfCheckGPT · GEval · Arize Phoenix · LangSmith

**Testing & CI/CD** — pytest · GitHub Actions · ruff · mypy

**Trading & backtesting (Crucible)** — NautilusTrader · Optuna · Alpaca

**Data & domain APIs** — edgartools (SEC) · yfinance · Wikipedia API

**Domain:** ~15 years business operations · financial services (ERISA-regulated) · active trading

---

## 📂 Repository Structure

> **Document versions (2026):** scope docs aligned to **Roadmap v10.0**. Filenames unchanged — the version lives in each document's header. Each scope carries a Stage-1 build sheet and a Full-Production architecture doc where applicable.

```
data-portfolio/
├── README.md                                  # This file — portfolio hub
├── project_scopes/
│   ├── POLICYPULSE/                            # 🚩 Applied-AI flagship (RAG → GraphRAG → agentic)
│   │   ├── POLICYPULSE_HR_RAG_SCOPE_v1_6_STAGE1.md
│   │   └── POLICYPULSE_HR_RAG_SCOPE_v2_0_FULL_PRODUCTION.md
│   ├── DATAVAULT/                              # 🚩 Data-Engineering flagship (1099 core → platform → AI layer)
│   │   ├── DATAVAULT_1099_DATA_PLATFORM_SCOPE_v2_1_STAGE1.md
│   │   └── DATAVAULT_1099_DATA_PLATFORM_SCOPE_v1_0_FULL_PRODUCTION.md
│   ├── CRUCIBLE/                               # 🚩 Autonomous trading-research flagship
│   │   ├── CRUCIBLE_SCOPE_v3_1_STAGE1.md
│   │   ├── CRUCIBLE_SCOPE_v1_0_FULL_PRODUCTION.md
│   │   └── Shared_SignalCore_Boundary_Spec_v1_3.md   # shared primitives (AFC ↔ Crucible)
│   ├── AFC/                                    # Supporting — eval-first research
│   │   ├── AFC_EVAL_FIRST_CORE_SCOPE_v1_3.md
│   │   └── ATTENTION_FLOW_CATALYST_SCOPE_v9_0.md
│   ├── FORMSENSE/                             # Supporting — multimodal document ops
│   │   ├── FORMSENSE_DISTRIBUTION_SCOPE_v1_8_STAGE1.md
│   │   └── FORMSENSE_DISTRIBUTION_SCOPE_v2_0_FULL_PRODUCTION.md
│   ├── STREAMSMART/                           # Backlog — consumer AI app
│   │   ├── STREAMSMART_OPTIMIZER_SCOPE_v1_6_STAGE1.md
│   │   └── STREAMSMART_OPTIMIZER_SCOPE_v2_0_FULL_PRODUCTION.md
│   ├── ODI/                                   # Backlog — enterprise demand analytics
│   │   └── OPERATIONS_DEMAND_INTELLIGENCE_SCOPE_v3_0.md
│   └── revisions/                            # superseded Crucible revisions (kept for trail)
│       ├── CRUCIBLE_SCOPE_v1_1_SWING_REVISION.md
│       └── CRUCIBLE_SCOPE_v1_2_CONFIRMED.md
└── support_tools/                            # tooling, not portfolio flagships
    ├── CADENCE_CONTENT_SCOPE_v1_0_STAGE1.md
    └── CADENCE_CONTENT_SCOPE_v1_0_FULL_PRODUCTION.md
```

> Each scope document includes: executive summary, business problem, data/architecture, phased S1→S3 implementation, tech stack, CI/CD, evaluation strategy, Docker support, a Mermaid diagram, the per-stage courses/certs, a Skills-Required table, and the v10.0 production standard (C4 + ADR). Agentic-workflow vs. autonomous-agent boundaries are drawn explicitly per Anthropic's *Building Effective Agents* taxonomy; Crucible's live path is the one irreversible route requiring HITL + kill-switch. The `signalcore` boundary spec defines the shared-primitives contract between AFC and Crucible.

---

## 📖 More Resources

| Resource | Link |
|---|---|
| 📋 Interactive roadmap | [v10.0 career roadmap (3 stages, ~32 months)](https://manuel-reyes-ml.github.io/learning_journey/roadmap.html) |
| 📚 Learning journey | [learning_journey](https://github.com/manuel-reyes-ml/learning_journey) |
| 👤 GitHub profile | [@manuel-reyes-ml](https://github.com/manuel-reyes-ml) |
| 🔗 LinkedIn | [Manuel Reyes](https://linkedin.com/in/mr410) |

---

## 🎯 Open To

- **Data Engineer / Analytics Engineer** roles — AI-focused data platforms (embedding pipelines, vector stores, unstructured-data ETL feeding RAG)
- **Applied AI Engineer / Forward Deployed Engineer** roles — regulated / fintech domains
- Networking with data and AI practitioners · code reviews · technical discussion

---

<div align="center">

**Current stage (v10.0):** Stage 1 — Internal AI Builder · building toward AI-Focused Data / Analytics Engineering (Stage 2) → Applied AI / FDE (Stage 3)

🟢 Active · building in public

</div>