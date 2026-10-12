# 📊 Data & AI Portfolio — Manuel Reyes

**Analytics Engineer · Data Engineer — AI-ready data platforms.** The engineering write-ups behind my profile: what each system does, how it's built, and the standard every repo meets.

[![Open to roles](https://img.shields.io/badge/🟢_Open_to-Analytics_Engineer_·_Data_Engineer-success?style=flat-square)](mailto:manuelreyesv410@gmail.com)
[![LinkedIn](https://img.shields.io/badge/LinkedIn-mr410-0077B5?logo=linkedin)](https://www.linkedin.com/in/mr410/)
[![Profile](https://img.shields.io/badge/GitHub-manuel--reyes--ml-181717?logo=github)](https://github.com/manuel-reyes-ml)
[![Roadmap](https://img.shields.io/badge/Roadmap-v10.0-28a745)](https://manuel-reyes-ml.github.io/learning_journey/roadmap.html)

> **Hiring for Analytics or Data Engineering?** Start with [DataVault](#-datavault--ai-assisted-1099-data-platform) (the platform arc, live in production), then [PostCheck](#-postcheck--ai-powered-qa-review-agent) (exactly-once ingestion → a dbt quality mart with a declared grain).
> **Hiring for Applied AI?** Start with [PolicyPulse](#-policypulse--ai-powered-policy-assistant) and [PostCheck](#-postcheck--ai-powered-qa-review-agent).

---

## 🗂️ Index

Every project puts AI to work — **powered** where the model does the core job, **assisted** where a deterministic core must make the decision. Each is one system evolved across three stages, never rebuilt. Flagship vs supporting is size, not quality: every repo meets the [production standard](#️-production-standard).

Status: ✅ live · 🏗️ in build · 📐 designed · 📅 planned

| Project | Role | Stage 1 → Stage 3 | Status | Scope docs |
|---|---|---|---|---|
| **DataVault**<br>*AI-assisted* | Flagship · AE/DE | Live reconciliation core → dbt platform with contracts and a semantic layer → AI analyst (text-to-SQL, human approves every write) | ✅ S1 live · 🏗️ S2 | [S1](project_scopes/DATAVAULT/DATAVAULT_1099_DATA_PLATFORM_SCOPE_v2_1_STAGE1.md) · [Full](project_scopes/DATAVAULT/DATAVAULT_1099_DATA_PLATFORM_SCOPE_v1_0_FULL_PRODUCTION.md) |
| **PostCheck**<br>*AI-powered* | Flagship · AE/DE + AI | Evaluated review agent → event log as data infrastructure (dbt marts, Airflow, ECS) → evaluator-optimizer loop + SOP retrieval | 📐 | [S1](project_scopes/POSTCHECK/POSTCHECK_QA_AGENT_SCOPE_v1_1_STAGE1.md) · [Full](project_scopes/POSTCHECK/POSTCHECK_QA_AGENT_SCOPE_v1_0_FULL_PRODUCTION.md) |
| **PolicyPulse**<br>*AI-powered* | Flagship · Applied AI | RAG + FastMCP + eval gates → embedding pipeline as data infrastructure → GraphRAG + agentic + access-controlled retrieval | 🏗️ | [S1](project_scopes/POLICYPULSE/POLICYPULSE_HR_RAG_SCOPE_v1_6_STAGE1.md) · [Full](project_scopes/POLICYPULSE/POLICYPULSE_HR_RAG_SCOPE_v2_0_FULL_PRODUCTION.md) |
| **Crucible**<br>*AI-assisted* | Flagship · execution safety | Backtest engine + integrity spine → market-data lakehouse → paper → live agent with human sign-off + kill-switch | 🏗️ | [S1](project_scopes/CRUCIBLE/CRUCIBLE_SCOPE_v3_1_STAGE1.md) · [Full](project_scopes/CRUCIBLE/CRUCIBLE_SCOPE_v1_0_FULL_PRODUCTION.md) |
| AFC<br>*AI-powered* | Supporting · research | Faithfulness benchmark + event-study backtest + ML baseline → filings lakehouse + full trigger matrix → GraphRAG research loop | 🏗️ | [S1](project_scopes/AFC/ATTENTION_FLOW_CATALYST_SCOPE_v9_1_STAGE1.md) · [Full](project_scopes/AFC/ATTENTION_FLOW_CATALYST_SCOPE_v9_2_FULL_PRODUCTION.md) |
| FormSense<br>*AI-powered* | Supporting · document AI | Multimodal extract → validate → route → document-ops pipeline with dbt SLA analytics → agentic workflow + MCP | 📅 | [S1](project_scopes/FORMSENSE/FORMSENSE_DISTRIBUTION_SCOPE_v1_8_STAGE1.md) · [Full](project_scopes/FORMSENSE/FORMSENSE_DISTRIBUTION_SCOPE_v2_0_FULL_PRODUCTION.md) |

Every write-up below follows the same order — **① Production · ② Cost · ③ Architecture** — then what it does and the stack.

---

## 🧾 DataVault — AI-assisted 1099 data platform

**[Repo](https://github.com/manuel-reyes-ml/1099_reconciliation_pipeline)** · ✅ Stage 1 live in production · 🏗️ Stage 2 in build

| Metric | Impact |
|---|---|
| ⚡ Effort | **~95% reduction** in weekly reconciliation effort |
| 📈 Scale | The **full distribution book**, without added headcount |
| 🛡️ Correctness | **Derived-vs-reported Box-7 validation gate** — mismatches quarantined before distribution |
| ✅ Reliability | Reconciliation success rate held against a stated **freshness SLA** |

- **① Production** — a live, depended-upon pipeline in an ERISA-regulated operation: success rate, freshness SLA, quarantine and retry behaviour, schema contracts. Stage 1 invariants: `matched + quarantined == total_ingested`, zero silent drops, every Box-7 branch tested on both sides of age 59½.
- **② Cost** — 🔒 deliberately constrained: mechanism and relative deltas only.
- **③ Architecture** — ERISA-driven ADRs (retention, auditability, reconciliation guarantees, PII boundary) · C4 context + container · dbt tests and data contracts.

**Stage 1** reconciles two recordkeeping systems into one canonical model, derives and validates the 1099-R Box 7 code, and produces correction files and corrections analytics; a narrow LLM layer explains exceptions as typed output behind a DeepEval gate. **Stage 2** rebuilds the rules as dbt models with a unit test per rule (dbt Core + DuckDB first, then Snowflake), with medallion layers, Great Expectations contracts, idempotent Airflow runs, Docker → ECS via Terraform, monitoring, a written postmortem and a semantic layer. **Stage 3** adds the AI analyst: text-to-SQL over the semantic layer and proposed corrections with citations — it ships only if it beats the rules engine, and a human approves every write.

> 🧭 The shipped pandas pipeline is **frozen as the baseline**, not rewritten; the Stage 2 delta is the headline metric.
>
> 🔒 Public repos use **synthetic data only**. No absolute cost figures, participant or plan data, client identifiers or employer-identifying volumes.

**Stack:** Python · SQL · dbt · Polars · pandas *(Excel template + plotting only)* · DuckDB → Snowflake · Great Expectations · Airflow · Docker · AWS ECS · Terraform · Anthropic SDK · Pydantic · GitHub Actions

---

## 🧾 PostCheck — AI-powered QA review agent

**[Repo](https://github.com/manuel-reyes-ml/postcheck)** · 📐 Designed · the one project that evidences **both** target roles

- **① Production** — **read-only and advisory, as a tested invariant.** It never keys, posts, reverses or corrects; never contacts a participant, advisor, TPA or sponsor; never edits SSN, name or DOB. An IGO verdict is a recommendation to a human, never an authorization.
- **② Cost** — provider-agnostic across Anthropic, Azure OpenAI (Microsoft Foundry) and local Ollama; one eval suite, gates run per provider.
- **③ Architecture** — the spreadsheet is a rendered export, never the source of truth · declared grain (`fct_document_reviews` = one row per packet × review run) · ADRs · C4 context + container.

It watches an intake folder and claims each packet **exactly once** (content-hash idempotency + lease TTL), parses the posted-transaction export **deterministically** — the LLM never sees the workbook — and reads the scanned multi-document packet with a multimodal model. Rules run first and the LLM handles only the residual, across a fixed 15-item checklist against a **versioned SOP** → **IGO**, **NIGO (needs clarification)** or **NIGO (do not process)**. Findings go to the human processor with field, form value, export value, SOP citation and fix, and every verdict appends to an event log feeding a **dbt NIGO quality mart** (first-pass IGO rate, reason Pareto, reason-mix drift, agent-vs-human agreement).

| Role | What it evidences |
|---|---|
| 📊 **Analytics / Data Engineering** | Exactly-once ingestion · deterministic parsing of a ragged source · append-only event log with a declared grain · dbt models + contracts · a metrics layer answering a real operational question |
| 🤖 **Applied AI** | Multimodal extraction · agentic adjudication with a hard escalation boundary · three-layer eval with a blocking gate · MCP + human in the loop |

**📏 Measured against a real incumbent** — a prompt-only reviewer already in use. Headline metric: **false-CRITICAL rate** against a flattened-text baseline on the same rules and golden set. **The blocking gate is false-NIGO rate, not accuracy** (precision blocks, recall is reported): a reviewer that cries wolf loses the trust it needs. The synthetic-corpus generator with controlled NIGO injection is itself an artifact.

**Stack:** Python · Anthropic SDK · Azure OpenAI · Ollama · multimodal extraction · Pydantic · dbt · Airflow · DeepEval · FastMCP · Docker · GitHub Actions

---

## 📋 PolicyPulse — AI-powered policy assistant

**[Repo](https://github.com/manuel-reyes-ml/policypulse)** · 🏗️ In build

- **① Production** — containerised RAG + **FastMCP** service; **RAGAS / DeepEval gates are merge conditions**, not reports; confidence-gated escalation.
- **② Cost** — cost per query and p95 latency across local and cloud models; routing policy; embedding and re-index cost.
- **③ Architecture** — GraphRAG (Neo4j + ChromaDB) · retrieval ADRs with rejected alternatives · C4 context + container · MCP reads open, writes approval-gated.

Answers policy questions with cited sources and escalates when unsure. It starts as vector RAG, becomes an embedding pipeline treated as data infrastructure (Airflow ingest, dbt metadata models, contracts), and grows into a **GraphRAG hybrid** for multi-hop questions with **access control enforced at retrieval time**. A streaming UI built with the Vercel AI SDK is the last mile; the agent loop, retrieval and evals stay in Python.

**Stack:** Python · Anthropic SDK · ChromaDB · Neo4j · FastMCP · RAGAS · DeepEval · Arize Phoenix · Streamlit · Pydantic · Docker · Vercel AI SDK *(TypeScript, last mile only)*

---

## 🔥 Crucible — AI-assisted trading research

**[Repo](https://github.com/manuel-reyes-ml/crucible)** · 🏗️ In build

- **① Production** — backtest → paper → live, with **mandatory human sign-off and a kill-switch** on the live path; a deterministic core owns every order; intended-vs-filled reconciliation.
- **② Cost** — compute per backtest sweep, data-feed cost, results per compute-hour.
- **③ Architecture** — multi-timeframe design (swing first, then intraday) · execution and risk ADRs · C4 context + container · `signalcore` boundary (primitives in, strategy logic out).

Strategies are plugins that must clear three validation gates before real capital. Research integrity comes from a sealed out-of-sample vault, an overfitting-budget ledger, walk-forward validation and an engine-parity gate. An **LLM analyst** reads aggregated results and proposes hypotheses but sits behind a wall: it sees statistics, never raw outcomes, and never places an order. Protective stops are signed with the entry so they fire unattended. Local-first inference keeps proprietary data on-machine.

**Stack:** Python · own event-driven harness → NautilusTrader · Optuna · DuckDB · Parquet · Ollama / Qwen3 → cloud fallback · Pydantic · LangGraph · Alpaca · DeepEval · Docker

> ⚖️ Research project. Not investment advice; no claim of positive returns — validation is the point.

---

## 📈 AFC — AI-powered predictive trigger analysis *(supporting)*

**[Repo](https://github.com/manuel-reyes-ml/attention-flow-catalyst)** · 🏗️ In build

- **① Production** — read-only by construction; **faithfulness ≥ 0.9 is a blocking gate** on the LLM's filing analysis.
- **② Cost** — optional at supporting tier; where it applies, cost per screen run and re-index cost.
- **③ Architecture** — knowledge-time (point-in-time) data · walk-forward validation with embargo and a sealed holdout · Benjamini–Hochberg false-discovery control · ADRs + C4 context · `signalcore` boundary.

Small-cap trigger research in two layers. An LLM analyst reads SEC filings with provenance and is scored by three faithfulness methods against a controlled-perturbation benchmark. A read-only **event-study backtest** tests trigger combinations (insider purchases, dilution state, volume), and an **ML meta-labeling model** (logistic regression + LightGBM) predicts which trigger events follow through. Results are reported as **calibrated lift over a base rate** — never alpha, Sharpe or returns — and the model ships only if it beats the best rule-based combination. Stage 3 adds a GraphRAG research loop.

**Stack:** Python · edgartools · DuckDB · Parquet · scikit-learn · LightGBM · Anthropic SDK · DeepEval · SelfCheckGPT · FActScore · Docker

> ⚖️ Research project. Not investment advice.

---

## 📄 FormSense — AI-powered form validator *(supporting)*

**[Repo](https://github.com/manuel-reyes-ml/formsense)** · 📅 Planned

- **① Production** — Docker + CI deploy path; **GEval schema-adherence gates (≥ 0.85) are merge conditions**; low-confidence forms routed to a person.
- **② Cost** — optional at supporting tier; not manufactured where there is nothing to report.
- **③ Architecture** — frozen Pydantic schema contract · ADRs + C4 context · document → parse → validate → route boundary.

Reads scanned retirement-plan distribution forms (checkboxes, signatures) with a multimodal model, validates every field against business rules and a frozen contract, and routes the result. An **agentic workflow** — control flow in code, not free-roaming agents.

> 🔀 **Not PostCheck.** FormSense works **before indexing** and judges a form against itself (is it complete and consistent enough to file?). PostCheck works **after posting** and judges the posted transaction against the request and the SOP. Different step, source of truth and escalation target — never merged.

**Stack:** Python · vision LLM · Pydantic · DeepEval (GEval) · Streamlit · Docker

---

## 🧩 Backlog & shared components

- **StreamSmart** ([scope](project_scopes/STREAMSMART/STREAMSMART_OPTIMIZER_SCOPE_v1_6_STAGE1.md)) — consumer streaming-subscription optimizer. Backlog.
- **Operations Demand Intelligence** ([scope](project_scopes/ODI/OPERATIONS_DEMAND_INTELLIGENCE_SCOPE_v3_0.md)) — workflow-demand analytics on document-management data; a consolidation candidate against DataVault's mart and AI layer. Backlog.
- **signalcore** ([boundary spec](project_scopes/CRUCIBLE/Shared_SignalCore_Boundary_Spec_v1_5.md)) — point-in-time-safe primitives shared by Crucible and AFC.
- **Cadence** ([scope](support_tools/CADENCE_CONTENT_SCOPE_v1_0_STAGE1.md)) — build-in-public content pipeline; tooling, not a portfolio project.

Superseded scope versions and review records live in [`project_scopes/revisions/`](project_scopes/revisions/).

---

## 🏗️ Production standard

Every repo, flagship or supporting:

- **Documentation** — README leads with Production · Cost · Architecture · Mermaid + C4 context (container on flagships), both generated from one Structurizr DSL model · numbered, immutable ADRs in `docs/adr/` · Dockerfile · eval-metrics table · 15–30 s demo GIF · "What I learned".
- **Quality gates** — evaluation thresholds block merges · **synthetic data only** in public repos · earned overlay: AI or ML ships only if it beats the deterministic baseline · no vibe coding — every line understood before merge.
- **Python & dependencies** — Python 3.14, standard GIL build, declared once in `requires-python`; ruff, mypy, the Docker base image and the CI matrix read from it, so a mismatch fails CI · `pyproject.toml` + `src/` + `py.typed` · uv with a committed `uv.lock`, `uv sync --frozen` in CI and Docker, no `requirements.txt`.
- **Data engines** — Polars for ingestion and bulk transforms; pandas only at the `openpyxl` template write and plotting hand-off; business logic in dbt. The reviewable artifact is the ADR plus a `.explain()` plan, not a small-data benchmark.
- **Observability & config** — `structlog` over stdlib via `ProcessorFormatter`, so third-party logs pass through the same chain and its **PII-redaction processor** · `run_id` bound per run · logs to stdout · `pydantic-settings` with `SecretStr` · capped, jittered retries with `stamina`.
- **Commit boundary** — a pinned `.pre-commit-config.yaml` that is a **strict subset of CI**: `ruff-check --fix` before `ruff-format`, `uv-lock`, `gitleaks`, `detect-private-key`, `nbstripout`, Conventional Commits on `commit-msg`. mypy runs in CI only — the mirrors-mypy hook silently degrades third-party types to `Any` (recorded as an ADR).
- **Workflow** — branch → PR → self-review → merge; no direct commits to `main`. Agent harness: OpenCode writes, Claude Code reviews read-only, both governed by one `AGENTS.md`; a guard hook blocks agent `git commit` / `push`, so every commit is made by a human.

---

## 🔗 More

[GitHub profile](https://github.com/manuel-reyes-ml) — the 60-second summary · [learning_journey](https://github.com/manuel-reyes-ml/learning_journey) — what I'm learning and why · [Roadmap v10.0](https://manuel-reyes-ml.github.io/learning_journey/roadmap.html) — every decision with its evidence · [LinkedIn](https://www.linkedin.com/in/mr410/) · [manuelreyesv410@gmail.com](mailto:manuelreyesv410@gmail.com)
