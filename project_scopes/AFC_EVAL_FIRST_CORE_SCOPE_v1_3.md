# 🧪 AFC — Eval-First Core Slice — Scope v1.3 (OFFICIAL)

## SEC-Grounded Faithfulness Benchmark for Small-Cap Filing Analysis

**Document Version:** 1.3 (🎯 **v10.0 ALIGNMENT** — parent AFC scope repointed to **v9.0** (now Supporting, 3-stage, Applied AI Engineer → FDE). This vertical slice = AFC's Stage-1 eval-first core; still additive, build scope unchanged. Prior v1.2 note archived below.)
**Last Updated:** June 10, 2026
**Status:** ✅ APPROVED
**Author:** Manuel Reyes
**Parent document:** `ATTENTION_FLOW_CATALYST_SCOPE_v9_0.md` (v9.0, Supporting) — **unchanged.** This slice is *additive*: it re-sequences a subset of AFC to build the AI/eval core first. It does not modify, replace, or contradict AFC v8.4.

---


## 🎯 v10.0 ROADMAP ALIGNMENT & STAGE-EVOLUTION ARC — AUTHORITATIVE

> **This block governs.** Where anything below it conflicts (old stage numbers, retired titles, pre-v10.0 portfolio lists), **this block wins.**

**Aligned to:** Career Roadmap **v10.0 (2026 Market Realignment)**.

**Governing model:** **3 stages, not 5.** The retired 14-month "ML Engineer" stage is now an **embedded ML-literacy module inside Stage 3** (earned-overlay — ships only if it beats the baseline). The destination title is **Applied AI Engineer → Forward Deployed Engineer (FDE)**; the retired "Senior LLM Engineer" title is dropped. **This project is ONE system that evolves across stages — never rebuilt per stage.**

**Portfolio role:** 🧩 **Supporting** — the **Stage-1 eval-first core** of AFC (itself Supporting). Portable SEC-grounded faithfulness benchmark. In v10.0, **flagship vs supporting = size & emphasis, not a quality tier — every project is production-grade.** Lead projects get new tooling first and are updated continuously as skills grow.

**Stage-evolution arc:**

| Stage | Theme | This project's layer |
|---|---|---|
| **S1** | Foundation (GenAI-first core) | Filing retrieval + LLM analyst + three-method eval + controlled-perturbation catalog → published benchmark repo (this slice). |
| **S2** | DE/AE hardening | Feeds AFC's S2 financial-data lakehouse (EDGAR ingest + signalcore + dbt models + contracts). |
| **S3** | Applied AI (RAG/agentic + eval) | Feeds AFC's S3 GraphRAG research loop; the eval harness becomes the **verifier** and the faithfulness-≥0.9 showcase. |

- **Every project's S2 adds:** ingestion → **dbt-tested models (CI-gated)** → **data contracts** (Great Expectations) → warehouse/lakehouse → **Airflow** (idempotent runs) → Docker/**ECS** → monitoring + written **postmortem** → **semantic/metrics layer**.
- **Every project's S3 adds:** RAG/GraphRAG/agentic layer + **three-layer eval** (per-query metrics · trajectory tracing · drift vs frozen golden set) + **observability (Arize Phoenix, OTel-native, free)** + MCP + **HITL** on irreversible actions.

**Production standard (non-negotiable, ALL projects):** business-outcome headline · Mermaid diagram · **C4 Context diagram (+ Container view on lead flagships)** 🆕 · **`docs/adr/` — numbered, immutable Architecture Decision Records (context → decision → consequences)** 🆕 · Dockerfile · eval-metrics table · 15–30s demo GIF · "What I Learned" · **synthetic data only in public repos** · `pyproject.toml` + `src/` + `py.typed` + ruff + mypy · Conventional Commits. *(🆕 C4 + ADR added per roadmap v10.0 CORRECTION 8, July 2026 — additive documentation discipline: the decision-and-defense artifacts Applied-AI/FDE interviews probe; same doc version, no structural change.)*

---

## Changelog

| Version | Change |
|---|---|
| v0.1 (DRAFT) | Initial eval-first slice: forward/defer split, research question, three-method eval design, controlled-perturbation methodology, structure, timeline. |
| **v1.0 (OFFICIAL)** | Draft approved. **Added §4.3 — Perturbation Error Catalog** (10 error types × 3 difficulty tiers, worked examples, per-detector expectations, labeling schema, composition target). Cross-referenced from §4.2 and §5 deliverable #8. |
| **v1.1 (OFFICIAL)** | **Added §14** — Future Extension: News & the "Faithful-but-Wrong" Problem (additive, future work). **Added §15** — Courses & Certifications (roadmap-aligned + researched 2026 additions, learn-while-building map). |
| **v1.2 (OFFICIAL)** | **Extended §14** — added the **verifiable-quantitative source category** (FINRA short interest): a non-authoritative carrying medium whose underlying quantity *is* checkable against an authoritative structured KB. Corrects the implicit "non-SEC = unverifiable" assumption; strengthens the extension taxonomy. Future-work only — **v1.x build scope unchanged.** |

---

## 0. What This Document Is (and Isn't)

This is a **vertical slice** of Attention-Flow Catalyst, not a new project. It pulls AFC's filing-retrieval + LLM-analyst + evaluation components *forward* and ships them first, as a standalone, publishable benchmark. The finance-research machinery (screener, backtest engine, dashboard) stays exactly where it is in AFC v8.4 and is built *after*.

- **It IS:** the production AI core of AFC, sequenced eval-first, packaged as a portable benchmark repo.
- **It IS NOT:** a throwaway demo, a fork of AFC, or a change to the AFC scope.

> **Design intent — zero rework.** Every module here (`edgar/`, `analyst/`, `eval/`) is written so it lifts directly into AFC later. You are building AFC's AI layer first, not building something you'll throw away.

---

## 1. Why This Slice (Gap Analysis)

| Question | Answer |
|---|---|
| Does the full AFC scope add fellowship value over this slice? | **No.** Phase 1A's backtest engine (screener, walk-forward, bootstrap CI, leaderboard) and Phase 1B's dashboard add ~zero signal for an AI-safety evaluation. ~6 weeks for ~0 fellowship return. |
| Can the eval study stand on a throwaway analyst? | **No.** An eval is only as credible as the system it scores, and a toy analyst violates the no-waste / production-from-day-1 principle. |
| What's the dependency that drives scoping? | AFC's analyst, as scoped, reads **backtest leaderboard stats** — verifiable only against your own DB. The *publishable* benchmark targets **SEC-filing claims** — verifiable against an external authoritative source (EDGAR) via FActScore. That analyst depends on **EDGAR retrieval**, **not** the backtest engine. |
| Why does it map to Anthropic's research areas? | A faithfulness/hallucination benchmark with controlled ground truth is a **dangerous-capability evaluation** — one of Anthropic's named areas. It also exercises honest-evaluation methodology (controlled perturbations, precision/recall against labels). |

---

## 2. Research Question

> **Primary:** On SEC-filing-grounded financial claim generation, how do three hallucination-detection methods — consistency-based (**SelfCheckGPT**), atomic-fact decomposition (**FActScore**), and LLM-judge faithfulness (**DeepEval**) — compare at catching unfaithful claims, and where does each method fail?

**Secondary questions**
1. On a controlled perturbation set (faithful summaries with injected factual errors), what is each method's detection precision/recall?
2. Where do the three methods *disagree*, and what does the disagreement reveal about each one's blind spots?
3. Which error types (wrong share count, wrong date, fabricated use-of-proceeds, misattributed insider, material omission) are hardest to catch, and by which method?
4. Does provider choice (Anthropic Claude vs. fallback) change the analyst's baseline faithfulness on filing summarization?

**Why this is a real research question (not a finance question):** the object of study is the *evaluation method*, not the stock. The finance domain supplies a high-stakes, externally-verifiable corpus — which is exactly what makes the benchmark meaningful.

---

## 3. The Analyst Task (What Gets Evaluated)

Given a retrieved SEC filing (or filing section), the analyst produces a **Pydantic-structured set of factual claims** plus a short narrative summary.

**Filing types in scope (the SEC-verifiable subset of AFC's triggers):**
- **T5 — Dilution state:** S-1, 424B5, 8-K (shares offered, offering price, use of proceeds, resulting dilution).
- **T1 — Insider activity:** Form 4 (insider, transaction type, shares, price, post-transaction holdings).

**Claim schema (illustrative — finalized in build):**
```
FilingClaimSet
├── filing_type: Literal["S-1","424B5","8-K","Form4"]
├── accession_no: str            # EDGAR provenance
├── claims: list[AtomicClaim]    # each: subject, predicate, value, source_span
└── narrative: str               # human-readable summary (the faithfulness target)
```

The **narrative** is the faithfulness target; the **atomic claims** feed FActScore.

---

## 4. Evaluation Design (The Core)

### 4.1 Three methods, one corpus

| Method | What it needs | What it catches |
|---|---|---|
| **DeepEval (faithfulness/hallucination)** | Retrieved filing context | Narrative claims unsupported by the source context |
| **SelfCheckGPT** | N=5 sampled generations, **no external KB** | Self-inconsistency across samples (subtle fabrications) |
| **FActScore** | Atomic-fact decomposition + EDGAR/filing text as KB | Per-fact unsupported/contradicted claims |

### 4.2 Ground truth via controlled perturbation (the empirical backbone)

This is what turns "run three scorers" into a real evaluation. **Key principle: the perturbation set measures the *detectors*, not the analyst.** The label is certain *by construction* — you author the error, so you already know the right answer; the detector's job is to agree.

1. Build a set of **human-verified faithful** filing summaries (the positives).
2. Programmatically inject **controlled factual errors** to create labeled negatives — the full taxonomy is in **§4.3**.
3. Run all three methods over the labeled set.
4. Report **precision, recall, F1** per method per error type, plus a **method-agreement matrix** and a **failure taxonomy**.

> **This is the publishable result.** Not "my analyst scored 0.9 faithfulness," but "here's how three detection methods perform against known-seeded errors in SEC-grounded financial claims, and here's where each one breaks."

**Method asymmetry (handled explicitly):** DeepEval and FActScore are *reference-based* — they score a given text against the filing, so the static perturbation set applies directly. SelfCheckGPT is *consistency-based* — it scores the generation process across N fresh samples, so it is measured on the **organic run** (real analyst, real filings) by correlating its consistency score against the reference-based verdicts and human spot-checks. That asymmetry is itself a reportable finding.

### 4.3 Perturbation Error Catalog

Each perturbation is a controlled edit applied to a faithful summary to create a labeled negative. Errors are organized by category and by **difficulty tier**, because the benchmark's credibility rests on including realistic, subtle errors — not just overt ones.

**Difficulty tiers**
- **Tier 1 — Overt:** unambiguous, single-token errors. Purpose: confirm every detector fires at all (sensitivity floor).
- **Tier 2 — Subtle:** plausible errors that mirror real LLM failure modes. Purpose: the actual discriminating test between methods.
- **Tier 3 — Structural:** errors (esp. omission) that reference-based scorers *cannot* catch by design. Purpose: map the ceiling of faithfulness scoring — a finding, not a bug.

**Catalog**

| ID | Tier | Perturbation | Applies to | Primarily stresses |
|---|---|---|---|---|
| **E1** | 1 | **Numeric value swap** — change a share count or dollar figure | T5, T1 | FActScore (numeric atom), DeepEval (contradiction) |
| **E2** | 2 | **Scale/unit error** — millions↔thousands, per-share↔aggregate proceeds | T5 | FActScore; DeepEval often misses (no arithmetic) |
| **E3** | 2 | **Derived-value miscalc** — correct inputs, wrong dilution % or post-offering share count | T5 | Detectors that verify *computed* values; known weak spot for both |
| **E4** | 1 | **Date shift** — move filing / offering / transaction date | T5, T1 | FActScore vs EDGAR metadata |
| **E5** | 2 | **Sequence error** — wrong ordering of filings/events (8-K before S-1, etc.) | T5, T1 | Temporal reasoning; both weak |
| **E6** | 2 | **Direction flip** — insider "buy" stated as "sell" (or vice versa) | T1 | FActScore (predicate atom), DeepEval |
| **E7** | 2 | **Misattribution** — right transaction, wrong insider/role; or wrong issuer / security class (common vs preferred, shares vs warrants) | T1, T5 | FActScore (entity atom) |
| **E8** | 2 | **Fabricated clause** — invent a use-of-proceeds / lock-up / over-allotment not in the filing | T5 | DeepEval (unsupported), FActScore (unverifiable); SelfCheckGPT on organic run |
| **E9** | 2 | **Fabricated qualifier** — add unwarranted certainty / characterization not in source | T5, T1 | DeepEval; subtle editorializing |
| **E10** | 3 | **Material omission** — drop a key qualifier (best-efforts, going-concern, conditionality) while stating only true things | T5 | **Designed to defeat reference-based scoring** — exposes a structural blind spot |

**Worked examples**
- **E1 (numeric):** "offering of **10,000,000** shares" → "offering of **15,000,000** shares."
- **E2 (scale):** "gross proceeds of approximately **$25 million**" → "**$2.5 million**" (per-share vs aggregate confusion).
- **E3 (derived):** inputs correct (10M new shares on a 40M pre-offering base) but summary states "**~33% dilution**" instead of "**~20%**." Every input verifies; the *conclusion* is wrong.
- **E6 (direction):** "the CFO **purchased** 50,000 shares" → "the CFO **sold** 50,000 shares."
- **E8 (fabrication):** filing says proceeds for "general corporate purposes"; summary adds "**primarily to repay outstanding debt**."
- **E10 (omission):** filing describes a **best-efforts** offering with a **going-concern** note; the summary accurately states shares, price, and date but **omits both qualifiers** — nothing stated is false, yet the summary materially misleads.

**Detector expectations (the hypotheses the benchmark tests)**
- **DeepEval (faithfulness vs context):** strong on E1, E6, E8, E9 (direct contradiction / unsupported addition); weak on E2, E3 (no arithmetic) and E10 (states nothing false).
- **FActScore (atomic verification):** strong on discrete-fact errors E1, E4, E6, E7; partial on E2/E3 only if atoms include computed values; weak on E10 (no false atom to flag).
- **SelfCheckGPT (consistency, organic run):** strong where the model can't reproduce a fabrication consistently (E8, E9); weak on confident, repeatable errors; inapplicable to static perturbations.

> **The E10 insight.** Omission is the class all three reference-based methods are structurally blind to, because "is every stated claim supported?" returns *yes*. Quantifying how often a faithful-scoring summary still misleads by omission is one of the most defensible findings this benchmark can produce — and it generalizes well beyond finance.

**Dataset labeling schema (per case)**
```
EvalCase
├── case_id: str
├── filing_type: Literal["S-1","424B5","8-K","Form4"]
├── accession_no: str                 # EDGAR provenance
├── gold_label: Literal["faithful","unfaithful"]
├── error_id: str | None              # E1..E10, or None for faithful positives
├── tier: Literal[1,2,3] | None
├── perturbed_field: str | None       # e.g., "shares_offered"
└── expected_detectable_by: list[str] # detectors hypothesized to catch it
```

**Composition target (≥30 cases, balanced):** ~40% faithful positives, ~60% perturbed negatives; negatives weighted toward **Tier 2** (the discriminating cases) with a deliberate handful of **Tier 3** omissions. Spread across both filing families (T5 dilution + T1 insider).

---

## 5. Scope — Deliverables That Move FORWARD

Pulled from AFC v8.4 (§ references are to that doc), built to production standard.

| # | Deliverable | Source in AFC v8.4 | Acceptance |
|---|---|---|---|
| 1 | Project setup (`src/` layout, `pyproject.toml` only, `py.typed`, pre-commit, Docker) | Phase 1A #1 | CI green, pre-commit working |
| 2 | SEC EDGAR async retrieval (httpx) — S-1, 424B5, 8-K, Form 4 | subset of Phase 1A #5 | Filings fetched + cached, provenance recorded |
| 3 | Filing parse + chunk (T1 insider, T5 dilution fields) | subset of Phase 1A #7 | Structured fields extracted from raw filings |
| 4 | Provider-agnostic LLM analyst (**Anthropic Claude primary**, Gemini fallback) | Phase 1B #7 | Produces structured `FilingClaimSet` |
| 5 | Pydantic response models | Phase 1B #8 | Type-safe, validated outputs |
| 6 | AI guardrails (read-only, governance-as-code, disclaimers) | Phase 1B #10, §11 | Testable guardrail unit tests |
| 7 | AI observability (token/cost/latency per call) | Phase 1B #11 | Per-call logs |
| 8 | **Claims dataset + controlled perturbation set (labeled)** — built per **§4.3** | NEW (extends `eval_dataset.json`, 30+ cases) | ≥30 cases per §4.3 composition target |
| 9 | **DeepEval harness** (faithfulness, hallucination) | §17 eval layer | Runs in CI |
| 10 | **SelfCheckGPT harness** (consistency, N=5; organic run) | v8.4 eval enhancement | Runs in CI |
| 11 | **FActScore harness** (atomic decomposition + EDGAR verification) | v8.4 eval enhancement | Runs in CI |
| 12 | **Benchmark runner + findings report** (precision/recall, agreement matrix, failure taxonomy) | NEW | Reproducible report + README writeup |
| 13 | Test suite >80% + Mermaid architecture diagram + demo GIF | Phase 1A #14/#15 | Coverage gate, diagram, GIF in README |
| 14 | `.cursor/rules/` (git-workflow, learning-mode, python-production-standards, evaluation) | AFC convention | Present |

---

## 6. Scope — Deliverables That DEFER (stay in AFC v8.4, built later)

These are **not cancelled** — they remain the rest of AFC and are built after this slice ships.

| Deferred | Source in AFC v8.4 | Why it can wait |
|---|---|---|
| Stock screener / universe snapshots / sector strength | Phase 1A #2, #3, #4 | Finance universe machinery; not needed to evaluate filing claims |
| Price pipeline (OHLCV, adjusted) | Phase 1A #6 | Claims are about filings, not prices |
| T2 (Wiki), T3 (news), T4 (volume) triggers | rest of Phase 1A #7 | Not SEC-verifiable; would weaken FActScore's authoritative-source property |
| DuckDB backtest schema, backtest engine, walk-forward, de-clustering | Phase 1A #8, #9 | Pure finance-research engine; zero eval value |
| Bootstrap CI, leaderboard, signal generator | Phase 1A #10, #11, #12 | Backtest outputs, not filing claims |
| Streamlit dashboard (all pages), PandasAI, Streamlit Cloud deploy, 60s demo video | Phase 1B #1–6, #9, #12, #13 | UI/serving layer; the benchmark report is the artifact instead |

---

## 7. Week-by-Week (~3–4 weeks @ 25 hrs/wk)

| Week | Focus |
|---|---|
| **1** | Project setup, CI, pre-commit, Docker, `.cursor/rules/`; EDGAR async client; filing fetch + cache + provenance |
| **2** | Filing parse (T1/T5 fields); provider-agnostic analyst; Pydantic claim schema; guardrails + observability; generate faithful summaries (the positives) |
| **3** | Build labeled perturbation set per §4.3; wire all three eval harnesses (DeepEval, SelfCheckGPT, FActScore); CI eval gate |
| **4** | Run benchmark; compute precision/recall + agreement matrix + failure taxonomy; write README + short report; record demo GIF |

---

## 8. Tech Stack (consistent with AFC v8.4)

- **Language:** Python 3.12, `src/` layout, `pyproject.toml`-only, `py.typed`
- **Retrieval:** `httpx` async → SEC EDGAR; local Parquet/JSON cache for filings
- **AI:** provider-agnostic SDK, **Anthropic Claude primary**, Gemini fallback; Pydantic v2 structured outputs
- **Eval:** DeepEval + SelfCheckGPT + FActScore (all pytest-compatible, open-source)
- **Quality:** pytest (>80%), ruff, mypy, pre-commit
- **CI/CD:** GitHub Actions — lint, type-check, test, **eval gate** on every PR
- **Container:** Dockerfile for reproducible runs

---

## 9. Proposed Project Structure

```
afc-eval-core/
├── pyproject.toml
├── Dockerfile
├── README.md                      # benchmark writeup + findings + demo GIF
├── .cursor/rules/
├── .github/workflows/ci.yml       # lint, type, test, eval gate
├── src/afc_eval_core/
│   ├── edgar/                     # → lifts into AFC collectors
│   │   ├── client.py              # async EDGAR retrieval
│   │   └── parse.py               # T1/T5 field extraction
│   ├── analyst/                   # → lifts into AFC AI layer
│   │   ├── provider.py            # provider-agnostic SDK
│   │   ├── schema.py              # Pydantic FilingClaimSet
│   │   ├── guardrails.py          # governance-as-code (testable)
│   │   └── observability.py
│   └── eval/                      # → lifts into AFC eval layer
│       ├── dataset.py             # faithful set + perturbation generator (§4.3)
│       ├── perturbations.py       # E1..E10 edit functions
│       ├── deepeval_suite.py
│       ├── selfcheckgpt_suite.py
│       ├── factscore_suite.py
│       └── benchmark.py           # runner → precision/recall, agreement, taxonomy
├── data/
│   ├── filings/                   # cached EDGAR filings (provenance)
│   └── eval_dataset.json          # labeled cases (faithful + seeded negatives)
└── tests/
    ├── test_edgar.py
    ├── test_analyst.py
    ├── test_ai_guardrails.py
    ├── test_perturbations.py      # each E1..E10 edit produces the intended label
    └── test_eval.py
```

---

## 10. Success Metrics

**Engineering gates (carried from AFC):**

| Metric | Target |
|---|---|
| Test coverage | >80% |
| Guardrail test coverage | >90% |
| CI eval gate | All three suites run + report on every PR |

**Benchmark/research outputs (the actual deliverable):**

| Output | Definition |
|---|---|
| Detection precision/recall/F1 | Per method, per error type (E1–E10), against the labeled set |
| Method-agreement matrix | Pairwise agreement of DeepEval / SelfCheckGPT / FActScore |
| Failure taxonomy | Which error types each method misses, with examples (incl. the E10 blind spot) |
| Provider baseline | Analyst faithfulness by provider on identical filings |

> Note: baseline analyst thresholds from AFC (faithfulness ≥0.9, hallucination <0.10, SelfCheckGPT >0.85, FActScore >0.80) are reported, but for a *benchmark* the headline is the **comparison**, not the gate.

---

## 11. Risk Mitigation

| Risk | Mitigation |
|---|---|
| Eval not credible (toy analyst) | Analyst is production-grade and SEC-grounded; folds into AFC unchanged |
| FActScore KB weak | Verify atomic facts against the filing text + EDGAR structured metadata (authoritative) |
| Perturbations too easy / unrealistic | Tiered catalog (§4.3) weights toward Tier 2 subtle errors that mirror real LLM failure modes; Tier 1 only as a sensitivity floor |
| Detector sensitivity doesn't transfer to real errors | Organic run + human spot-check complements the synthetic perturbation set (§4.2) |
| Scope creep back into full AFC | §6 defer-list is explicit; finance engine is out of bounds for this slice |
| Small dataset over-claims | Report confidence honestly; frame as a focused study, not a universal benchmark |

---

## 12. How It Folds Back Into AFC

When this slice ships, AFC resumes from Phase 1A with its AI core **already built and evaluated**:
- `edgar/` → AFC's T1/T5 collectors
- `analyst/` → AFC's Phase 1B LLM SDK layer (#7, #8, #10, #11)
- `eval/` → AFC's evaluation layer (§17)

The deferred backtest engine and dashboard then build *around* a proven, eval-gated AI core — which is the evaluation-first sequencing your roadmap already endorses.

---

## 13. Fellowship Mapping (why this is the artifact to publish)

- **Anthropic area:** dangerous-capability evaluations / faithfulness & honesty.
- **What it demonstrates:** taking an ambiguous question → controlled empirical design → reproducible public output (a benchmark with labeled ground truth, a tiered error catalog, and a failure taxonomy).
- **Paired narrative:** frame it with Crucible's control philosophy (the Wall, bounded LLM authority) as your "how I keep AI honest in high-stakes loops" thesis.

---

## 14. Future Extension: News & the "Faithful-but-Wrong" Problem

*(Future work — captured here so it's on record when AFC's T3 news trigger comes online. Not part of the v1.x build.)*

The eval methodology is deliberately **SEC-only**. News *can* be evaluated, but only partially, and understanding the split is the point:

- **Transfers to news — faithfulness-to-source.** DeepEval (summary-vs-context) and SelfCheckGPT (self-consistency) work on a news article as the source. Most of the §4.3 catalog applies as "faithful-to-article" edits.
- **Does NOT transfer — authoritative-truth verification.** FActScore needs an authoritative knowledge base. SEC EDGAR is authoritative; **news is not.** Pointed at the article itself, FActScore collapses into DeepEval; pointed at a news corpus, it measures reporter *consensus*, not truth.

**The conceptual crux:** with an SEC filing, *faithful-to-source* and *factually-true* are the same thing — the filing **is** the authority. With news they split: a summary can be perfectly faithful to an article that is itself wrong. (Note: many financial-news claims — raises, insider sales, earnings — are verifiable, but only by routing back to SEC primary sources, i.e., EDGAR again.)

**Why it's a strong *future* study, not part of this slice:** news isolates the **faithful-but-wrong** failure — "the analyst faithfully relayed the article, but the article was false; can any detector in this stack catch that?" The answer is **no** — all three measure grounding-to-source, not truth — and demonstrating that cleanly is itself a contribution. It generalizes well beyond finance.

**Implication for AFC proper:** when T3 (news) feeds the analyst, score it for *faithfulness-to-article only*, treat news as a **lower-trust input** with disclaimers, and never present news-derived claims as verified fact.

### 14.1 A third source category — verifiable quantitative claims (the FINRA / short-interest case)

*(Added v1.2. Future-work refinement of the taxonomy above; not part of the v1.x build.)*

The SEC-vs-news split above reads as a binary — authoritative prose vs. unverifiable prose — but short-interest data exposes a **third category** the taxonomy should name. When an LLM reports a short-interest fact, it is almost always reading it from **secondary prose** (a news item, a screener blurb, an analyst note: *"32% of float short, 6 days to cover, up from 28%"*). That carrying text is **non-authoritative** — so by the rule above it is a news-type source. But unlike a general news claim, the underlying **quantity** is **checkable against an authoritative structured source** (FINRA's bi-monthly short-interest record). That is exactly the condition under which FActScore does **not** collapse: there is a real external KB to decompose atomic facts against.

| Source category | Carrying medium | Faithful-to-source = true? | Authoritative KB to verify against? | FActScore behavior |
|---|---|---|---|---|
| SEC filing (the core) | authoritative prose | Yes — the filing *is* the authority | Yes — EDGAR | Works fully |
| **Verifiable quantitative (FINRA short interest)** | non-authoritative prose | No | **Yes — FINRA record** | **Works — the rare non-SEC case** |
| General news | non-authoritative prose | No | No | Collapses into DeepEval |

This makes short interest a **stronger extension example than general news**, and it corrects an implicit assumption in this section — that "non-SEC = unverifiable." The sharp future study is: *can the stack catch an analyst that faithfully relays a secondary article's short-interest figure when that figure is wrong, given FINRA as the ground-truth KB?* — the faithful-but-wrong problem, but this time **with a checkable answer**, which the news case lacks.

**Two caveats keep this honest:**
- **Thin corpus on its own.** Raw FINRA data is a number, not a document — almost no narrative faithfulness surface, which is exactly why it stays out of the SEC-grounded core. It earns a place *only* through the secondary-prose route above, never as a standalone SEC-style corpus.
- **"True" means as-reported.** The target is fidelity to the **reported** FINRA figure — not whether short interest perfectly reflects real positioning (naked shorts, settlement timing are known data limitations, out of scope for a *faithfulness* benchmark).

Still future work; the v1.x build scope is unchanged.

---

## ✅ Approval Status

Design approved (June 10, 2026):

- [x] Research question framed correctly (method comparison, not finance)
- [x] Filing scope correct (T5 dilution + T1 insider; SEC-verifiable only)
- [x] Analyst task + claim schema direction approved
- [x] Perturbation-set approach approved (controlled ground truth)
- [x] **Perturbation error catalog approved (§4.3)**
- [x] Forward/defer split matches intent
- [x] Standalone repo now, folds into AFC later — agreed
- [x] Timeline realistic (3–4 weeks @ 25 hrs/wk)

**Build-readiness (next, before/at coding):**
- [ ] Finalize `FilingClaimSet` / `AtomicClaim` schema fields
- [ ] Lock analyst prompt contract (summary + atomic-claim extraction)
- [ ] Confirm EDGAR rate-limit / caching policy
- [ ] Pick the initial filing set (tickers + accession numbers) for the dataset


---

## Skills Required (Roadmap Alignment — v10.0)

*Maps roadmap **v10.0** skills to how **this specific project** uses them. ✅ = already in hand / built at this stage. Skills escalate **within** the project (S1→S2→S3) — the system is never rebuilt.*

| Skill | Stage | How this project uses it |
|-------|-------|--------------------------|
| Python 3.11+, pandas | S1 ✅ | Slice tooling |
| SEC EDGAR retrieval | S1 ✅ | Filing corpus — the grounding source |
| LLM SDK (provider-agnostic) | S1 ✅ | The analyst under evaluation |
| Pydantic v2 | S1 ✅ | Structured analyst output (the eval target) |
| **RAGAS (RAG Triad)** | **S1 ✅** | **Faithfulness / answer-relevancy / context-precision per query** |
| **SelfCheckGPT** | **S1 ✅** | **Self-consistency hallucination detection** |
| **DeepEval / GEval** | **S1 ✅** | **CI-blocking eval gate** |
| **Controlled-perturbation catalog** | **S1 ✅** | **Injected-error detection — proves the harness actually works** |
| **Frozen golden set** | **S1 ✅** | **Drift baseline reused by every later stage** |
| Docker, pytest, ruff, mypy, GitHub Actions | S1 ✅ | Production standard |
| dbt, Airflow, `signalcore` | S2 *(via parent AFC)* | This slice feeds AFC's financial-data lakehouse |
| Neo4j + ChromaDB, LangGraph, MCP, Arize Phoenix | S3 *(via parent AFC)* | The harness becomes the **verifier** inside the GraphRAG research loop |


> **Why this slice exists:** it's the smallest publishable artifact that proves eval competence — the scarcest, most-cited skill in 2026 Applied-AI hiring. Ship it early; it folds back into AFC v9.0.

---

## 📚 Courses & Certifications — per Stage (v10.0 reference)

*Synced to roadmap **v10.0**. Names match the roadmap's stage tables; ordered by the stage in which this AFC eval-first slice needs them. ✅ = committed canon; conditional/platform certs are **take-ONE-only**, matched to a concrete apply-list. Employer-reimbursable certs noted. The shipped production-grade project is the primary hiring signal — certs are tiebreakers.*

### 🎓 Stage 1 — Foundation (GenAI-first core)
- **Courses:** Building with the Claude API · Building & Evaluating Advanced RAG (RAG Triad) · Improving the Accuracy of LLM Applications (eval-from-scratch) · Pre-processing Unstructured Data for LLM Applications
- **Certifications:** **AI-901** Azure AI Fundamentals (employer-reimbursed) · **AB-620** AI Agent Builder Associate (employer-reimbursed)

### 🎓 Stage 2 — DE/AE hardening
- **Courses:** PostgreSQL for Everybody · dbt Fundamentals — feeds AFC's S2 EDGAR/filings lakehouse + signalcore
- **Certifications:** **DP-700** Fabric Data Engineer (✅ committed · employer-reimbursed) · **AWS DEA-C01** Data Engineer Associate (✅ committed) *(shared with parent AFC scope)*

### 🎓 Stage 3 — Applied AI (RAG / agentic + eval)
- **Courses:** Neo4j GraphAcademy (GraphRAG) · HuggingFace NLP + LLM Course · NVIDIA DLI: Building RAG Agents with LLMs · Automated Testing for LLMOps
- **Certifications:** **NVIDIA NCA-GENL** ($125) · **Neo4j Certified Professional** (FREE) · **Anthropic CCA-F** ($125)

**Focus thread:** SEC filing retrieval → LLM analyst → three-method faithfulness eval + controlled-perturbation catalog → published benchmark repo.