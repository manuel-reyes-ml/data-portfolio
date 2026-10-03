# AFC Project Review — October 2, 2026

**Scope reviewed:** `ATTENTION_FLOW_CATALYST_SCOPE_v9_0.md` · `AFC_EVAL_FIRST_CORE_SCOPE_v1_3.md` · `Shared_SignalCore_Boundary_Spec_v1_5.md` · `CRUCIBLE_SCOPE_v3_1_STAGE1.md` / `CRUCIBLE_SCOPE_v1_0_FULL_PRODUCTION.md` (cross-project items only) · `roadmap.html` (v10.0, the governing document).
**Standard applied:** the roadmap's v10.0 production standard and positioning, plus external verification (PyPI package metadata pulled 2026-10-02; SEC, FINRA and GDELT primary sources).
**Status of fixes (rev. 3, October 2, 2026):** all approved findings applied — see §0. *Rev. 2:* owner approved the backtest decision and the first batch of fixes — applied under roadmap **CORRECTION 46**; see §0. Originally: **none applied to existing files.** Each finding carries a proposed fix; the new Stage-1 build sheet (`ATTENTION_FLOW_CATALYST_SCOPE_v9_1_STAGE1.md`) already incorporates the ones that touch Stage 1. Edits to existing files wait for approval.

**Severity:** 🔴 blocking (wrong or unbuildable as written) · 🟠 high (materially weakens correctness or credibility) · 🟡 medium (inconsistency that will cause drift) · ⚪ low (hygiene).

---

## 0. Resolution log — rev. 2 (CORRECTION 46, owner-approved)

| Finding | Resolution | Where |
|---|---|---|
| A-01 | **Resolved — backtest kept by decision (D-0).** Roadmap positioning amended (struck-through original + new text); stage split S1 Phase 1 eval → S1 Phase 2 backtest v1 (T1/T4/T5) → S2 adds T2/T3/T6 | roadmap C46 · v9.0 alignment block, §16 · build sheet §0, §7B |
| A-02 | **Resolved — no move needed.** With the backtest kept, §5.6 is consistent inside AFC; T6 split deferred to S2 | v9.0 §5.6 note · build sheet §7B.6 |
| A-03 | **Applied** — FActScore protocol re-implementation (D-3 locked) | v9.0 eval layer · build sheet §6.2 |
| A-04 | **Applied** — T6 from FINRA files keyed to publication date; float from XBRL shares outstanding; T6 moved to S2 | v9.0 §4.7, §8.2 |
| A-05 | **Applied** — PandasAI replaced by validated text-to-SQL | v9.0 §1, §10, §12, §19, Mermaid, skills · build sheet §11 |
| A-06 | **Applied** — historical universe reconstruction, Form 25/15-seeded delistings, as-traded screening; delisted-price source = open spike | v9.0 §6.1–6.2 · build sheet §7B.3 |
| A-08 | **Applied** — rolling walk-forward + embargo + sealed final holdout | v9.0 §5.4 · build sheet §7B.5 |
| A-09 | **Applied** — base-rate lift + Benjamini–Hochberg FDR over the full family | v9.0 §5.5 · build sheet §7B.5 |
| A-10 | **Applied** — per-source knowledge-time rule | v9.0 §6.3 · build sheet §7B.2 |
| A-11 | **Applied** — T1 = code `P` only | v9.0 §4.2 |
| A-21 | **Applied** — label and entry defined precisely | v9.0 §5.1 · build sheet §7B.4 |
| A-22 | **Partly applied** — power estimate before running; universe widening is **D-9 (pending)** | v9.0 §5.5 · build sheet §7B.3 |
| S-07 | **Applied** — FActScore knowledge source SEC-only | v9.0 tests line |
| SC-01 | **Applied** — short-interest knowledge time = FINRA publication date (+ test, anti-pattern) | SignalCore v1.5 correction |
| SC-02, SC-03 | **Closed — no change needed** now that the backtest and §5.6 stay in AFC | — |

### Rev. 3 — review batch approved and applied (C46 addendum, October 2, 2026)

| Finding | Resolution | Where |
|---|---|---|
| A-07 | **Applied** — "Agentic Trading Assistant" → read-only research agent | v9.0 §1 loop spec |
| A-12 | **Applied** — skills row now DeepEval + FActScore protocol + SelfCheckGPT | v9.0 Skills |
| A-13 | **Applied** — thresholds written as ≥ / ≤ with direction; SelfCheckGPT stated as inconsistency ≤ 0.15 | v9.0 §18A |
| A-14 | **Applied** — tiered CI (D-7) | v9.0 §13, §18A · build sheet §12.2 |
| A-15 | **Applied** — "10–50× faster" claim removed; edgartools adapter (D-2) | v9.0 §1, §9.1, §12, quick reference, Mermaid |
| A-16 | **Applied** — hardened Dockerfile; `.dockerignore` keeps README | v9.0 §18B |
| A-17 | **Applied** — logging row → structlog | v9.0 §12 |
| A-18 | **Applied** — `src/afc/` package, single logging module, no `errors.log`, harness nesting | v9.0 §15 (+ path references) |
| A-19 | **Applied** — Anthropic ↔ Gemini (↔ OpenAI) | v9.0 §17 |
| A-20 | **Applied** — approval status and quick-reference version refreshed; eval layer → §18A, Docker → §18B | v9.0 |
| A-23 | **Applied** — "5+ years" | v9.0 §1 |
| S-01…S-08 | **Closed** — slice marked **superseded** (D-6); its content lives, corrected, in the Stage-1 build sheet; harness tree fixed in place | slice header + changelog |
| X-01 | **Applied** — Crucible Stage-1 §12 and slice §9 (AFC v9.0 under A-18) | — |
| X-02 | **Applied** — Wikipedia knowledge-time rule | Crucible Stage-1 §6.4 |
| D-1…D-9 | **Locked** | build sheet §20 |
| CORRECTION 45 | **Written** in the roadmap; snapshot 1–44 → 1–46 | roadmap.html |

### Rev. 4 — D-6 completed (October 2, 2026)

`ATTENTION_FLOW_CATALYST_SCOPE_v9_0.md` was promoted to `ATTENTION_FLOW_CATALYST_SCOPE_v9_2_FULL_PRODUCTION.md` (`git mv`). **Section numbers §1–§19 are preserved**, so every "v9.0 §x" reference in this review still points to the same content. Build-level sections (§9, §13–§15, §18B, §19) now hold design summaries + pointers; their code moved to the Stage-1 build sheet Appendix A. New: §1A–§1C, §7.4, §16A, §16B, stage-by-stage §12/§17, approval checklist.

**Still open:** (1) the matching Falsification List entry in `MARKET_ANALYSIS_v9_3.md` (file not provided); ~~(2) drafting the AFC Full-Production companion~~ — ✅ done as v9.2 (rev. 4).

---

## 1. Headline

1. **AFC v9.0 contradicts the roadmap's binding positioning.** The roadmap states that AFC is financial NLP/RAG with an eval spine, with *no returns, no factor construction, no backtest*. v9.0 is built around a +10%-in-3-days backtest, a trigger leaderboard and a dashboard. Its own alignment block lists S1 as the eval-first slice only, while its §16 lists S1 as "backtest engine + AI dashboard + eval-first core". This is the root decision (**D-0**).
2. **Two eval components cannot install on the portfolio's Python 3.14 floor:** `factscore` (pins `torch<2.0`, `openai<0.28`) and `pandasai` (requires Python `<3.12`, `numpy<2`).
3. **Several point-in-time assumptions would leak future information** if the backtest were kept (short interest, float, filing timestamps, pageviews) — and one of them sits in the shared `signalcore` contract, so it would affect Crucible too.
4. **The eval-first core is the strongest part of the project** — the E1–E10 catalog and the E10 omission insight are genuinely publishable. Its statistical design needs strengthening before the numbers are defensible.

> *(rev. 2: superseded — the backtest was kept, so §5.6 stays in AFC; see A-02 in §0.)* **Owning a mistake:** CORRECTION 45 §5.6 (failed-signal follow-through), which I recommended adding to AFC, is itself backtest analysis. It conflicts with the roadmap positioning I should have checked. Proposed fix: relocate it to Crucible as a pre-step of `SAR-on-stop`, run on Crucible's own universe — which also removes the universe-mismatch caveat it carried (finding A-02).

---

## 2. Findings — AFC v9.0 (`ATTENTION_FLOW_CATALYST_SCOPE_v9_0.md`)

| ID | Sev | Section | Finding | Proposed fix |
|---|---|---|---|---|
| **A-01** | 🔴 | Alignment block vs §1, §2, §9–§10, §16, §19 | Backtest, returns and leaderboard framing conflict with the roadmap ("no returns, no factor construction, no backtest"). Internal conflict: alignment block S1 = slice only; §16 S1 = backtest + dashboard + eval. | **D-0:** retire Phase 1A/1B and the trigger leaderboard from AFC; keep filing facts (insider activity, dilution events) as analyst subjects and S3 KG entities. Create AFC Full-Production v1.0 for S2/S3 (D-6). |
| **A-02** | 🔴 | §2 Q8, §5.6, §9.1 #16, §17, §18 (CORRECTION 45) | Failed-signal follow-through is a backtest over price outcomes — same conflict as A-01. | Move to Crucible Stage-1 §4.1 as a descriptive pre-step on Crucible's universe; remove from AFC. Update SignalCore's `shortsale` consumer list (SC-03). |
| **A-03** | 🔴 | §17 eval layer, slice §4 | `factscore` 0.2.0 (last release Oct 2023) pins `torch<2.0` and `openai<0.28` — no install path on Python 3.14. | Re-implement the FActScore **protocol** (atomic fact generation → validation) against filing text; cite the paper; never compare absolute scores to published FActScore numbers (D-3). |
| **A-04** | 🔴 | §4.7 T6 | `yfinance` `sharesShort` / `floatShares` are current snapshots, not historical series → a T6 backtest would use today's short interest and float on past dates (look-ahead). | If T6 survives D-0: use FINRA's consolidated short-interest files (exchange-listed coverage begins June 2021) with **publication date** as knowledge time; derive shares outstanding from XBRL cover-page facts with acceptance datetime. Otherwise T6 survives only as the §14.1 eval study. |
| **A-05** | 🔴 | §1, §10 #9, §12, §19, Mermaid | `pandasai` 3.0.0 requires Python `<3.12`, `numpy<2`, pinned `scipy==1.10.1` — cannot install on 3.14. It also executes model-generated code, which the SQL-whitelist guardrails in §11 do not cover. | Remove PandasAI. If a natural-language query surface is ever needed: LLM → SQL validated by a parser (e.g. `sqlglot`) against the §11.3 whitelist, executed read-only in DuckDB. |
| **A-06** | 🟠 | §6.1, §3 | Weekly universe snapshots can only be taken going forward; they cannot reconstruct three past years. `yfinance` coverage of delisted tickers is unreliable. The `< $5` screen applied to **adjusted** prices misclassifies names after reverse splits (common in sub-$5 small caps). | Only relevant if D-0 keeps the backtest: EDGAR-seeded delisted list (Form 25/15) or a scoped-and-flagged universe (Crucible §5.1 pattern); screen on **as-traded** prices, compute returns on adjusted. |
| **A-07** | 🟠 | §1 Agentic Loop Spec | "S3 wraps this as the **Agentic Trading Assistant**" contradicts read-only identity and the roadmap's not-a-quant-project line. | Rename to "read-only research agent"; the S1 detectors become its verifier. |
| **A-08** | 🟠 | §5.4 | "Walk-forward" is a single split (Y1–2 train, Y3 test). Crucible's own standard forbids publishing single-split results. | If kept: rolling or anchored walk-forward, or rename to "holdout validation" and stop claiming walk-forward. |
| **A-09** | 🟠 | §4.8, §5.5 | ~155 scenarios tested with per-scenario bootstrap CIs only: no false-discovery control across scenarios, and **no base-rate comparison** — a hit rate means nothing without the unconditional rate of +10% moves in the same universe and period. | If kept: Benjamini–Hochberg FDR across scenarios; report lift over the base rate with CIs. |
| **A-10** | 🟠 | §6.3, §4.2–4.4 | "Data available by market close on D" is wrong for several sources: Form 4 filings accepted 5:30–10:00 p.m. ET keep that day's filing date (after the close); Wikipedia daily pageviews close on a **UTC** day boundary; RSS has no history; GDELT DOC article lists cover only the latest ~3 months (timeline/volume modes reach back to 2017). | Per-source `available_at` (knowledge time) column; an event is usable for an entry at session S only if `available_at < open(S)`. T3 history via GDELT timeline modes or GKG files, not RSS. |
| **A-11** | 🟠 | §4.2 T1 | Transaction code `A` (grant/award) counted as an insider **buy**. Grants are compensation, not open-market conviction. | `P` only for "purchase"; record `A`/`M`/`F` as their own event types. (Build sheet §4.2.) |
| **A-12** | 🟡 | Skills Required | Lists "RAGAS + SelfCheckGPT + DeepEval" as the three-method eval; everywhere else the third method is FActScore. | Replace RAGAS with FActScore (protocol). |
| **A-13** | 🟡 | §17 eval layer | SelfCheckGPT target "> 0.85" — SelfCheckGPT outputs an *inconsistency* score (higher = more likely hallucinated), so the pass direction is ambiguous. Faithfulness written "> 0.9" here and "≥ 0.9" elsewhere. | Normalize every detector to `p_unfaithful`; state thresholds as `≤`/`≥` consistently. |
| **A-14** | 🟡 | §17 ("CI pipeline includes evaluation gate — all three frameworks must pass"); slice §8, §10 | Live LLM evals on every PR are non-deterministic, cost money on every push, and make red builds ambiguous. | Tiered CI: deterministic replay on every PR; live golden-set run nightly / on label; full run manual (D-7). |
| **A-15** | 🟡 | §1 table ("Async httpx, 10–50× faster"), §12 | SEC fair access caps automated clients at 10 requests/second with a declared User-Agent; async concurrency cannot exceed that. | Drop the speed claim; rate-limited adapter (edgartools behind an interface) (D-2). |
| **A-16** | 🟡 | §18 Docker | `uv:latest` contradicts the "pinned binary" comment; single `uv sync` before `COPY . .` never installs the project; `.dockerignore` excludes `*.md`, which breaks builds when `pyproject.toml` declares a README; runs as root. | Hardened Dockerfile in build sheet §12.3. |
| **A-17** | 🟡 | §12 Tech Stack | Logging row says "logging (stdlib) + python-json-logger"; §14 says dropping `python-json-logger` is deliberate (structlog). | Replace row with `structlog` + `ProcessorFormatter`. |
| **A-18** | 🟡 | §15 tree | `agents/` and `commands/` nested under `hooks/guard.py` (they belong under `.opencode/`); `src/__init__.py` + `src/py.typed` at `src/` root is not a valid src-layout package; two logging modules (`src/observability/logging.py`, `src/utils/logging.py`); `logs/errors.log` contradicts "stdout is primary". | Corrected tree in build sheet §12. |
| **A-19** | 🟡 | §17 Phase 1B | "Provider switching: Gemini ↔ OpenAI" while Anthropic is primary everywhere else. | "Anthropic ↔ Gemini (↔ OpenAI)". |
| **A-20** | ⚪ | Approval Status, Quick Reference, §17 heading | "APPROVED — February 14, 2026 … v8.0", "v8.0 (FINAL)", "NEW v8.3" labels; the AI Evaluation Layer and Docker sections sit unnumbered under §18 Risk. | Refresh to v9.x; give eval layer and Docker their own numbered sections (or move to Full-Production per D-6). |
| **A-21** | ⚪ | §5.1, §5.3 | If kept: "+10% within 3 days" is ambiguous (close at day 3 vs intraday touch); the example measures open-to-close but is labeled close-to-close; a 50 bps spread proxy is likely optimistic for sub-$5 names. | Moot under D-0; otherwise define the label precisely and measure spreads from data. |
| **A-22** | ⚪ | §3, §5.5 | If kept: a ~50-stock universe with rare triggers and a 30-signal floor will exclude most of the 155 scenarios; per-trigger-type de-clustering lets overlapping windows inflate n. | Moot under D-0; otherwise state expected n per scenario before running. |
| **A-23** | ⚪ | §1, README | "6 years of trading knowledge" here vs "5+ years" in the learning_journey README. | Use one figure everywhere. |

---

## 3. Findings — Eval-First Slice (`AFC_EVAL_FIRST_CORE_SCOPE_v1_3.md`)

| ID | Sev | Finding | Proposed fix |
|---|---|---|---|
| **S-01** | 🟡 | Nine references to "AFC v8.4" as the parent; the parent is v9.0. | Superseded by the build sheet (D-6); if kept, repoint. |
| **S-02** | 🟠 | "≥ 30 cases" with 10 error types gives ~2 negatives per type — per-type precision/recall cannot be estimated. | ≥ 60 human-verified bases, every applicable E-type applied (paired, ~250–300 negatives), Wilson CIs (D-5). |
| **S-03** | 🟠 | No calibration/test split: detector thresholds would be chosen on the same data that is reported. | Group split by base filing; thresholds frozen on calibration; test scored once after a pre-registration commit. |
| **S-04** | 🟠 | Judge model unspecified — a Claude judge scoring a Claude analyst invites self-preference bias. | Judge provider ≠ analyst provider; 20% cross-judge κ (D-8). |
| **S-05** | 🟡 | Separate `afc-eval-core` repo whose modules "lift into AFC" = copy-and-drift between two repos. | Single repo, package `afc` (D-1). |
| **S-06** | 🟡 | E3 "dilution %" convention undefined (pre- vs post-offering basis; ownership dilution vs prospectus NTBV dilution). | Fixed in build sheet §4.2: post-offering basis. |
| **S-07** | 🟡 | v9.0 §17 says FActScore verifies against "SEC EDGAR + cached Wikipedia"; the slice correctly says SEC-only. | SEC-only in S1 (build sheet principle 1). |
| **S-08** | ⚪ | Same harness-tree nesting bug as X-01. | Corrected tree. |

---

## 4. Findings — Shared SignalCore Boundary Spec (`Shared_SignalCore_Boundary_Spec_v1_5.md`)

| ID | Sev | Finding | Proposed fix |
|---|---|---|---|
| **SC-01** | 🟠 | The short-interest accessor test asserts it "never returns a position whose FINRA **effective date** is > t". FINRA publishes each cycle several business days **after** the settlement date, so a record with settlement ≤ t can still be unknown at t → up to a week of look-ahead, in the one contract both projects trust. | Make **publication date** the record's transaction time (the bitemporal model already supports this); the leakage test asserts `publication_date ≤ t`. Semver-visible → ADR. |
| **SC-02** | 🟡 | §3 rows (T6 squeeze-trigger thresholds, +10% labeling, leaderboard) assume v9.0's backtest. | Revise after D-0. |
| **SC-03** | 🟡 | `signalcore.shortsale` lists "AFC §5.6" as a consumer. | If A-02 is approved, consumers become Crucible only (SAR-on-stop and its pre-step). The primitive stays shared-ready; the asymmetry note changes. |

---

## 5. Cross-project findings

| ID | Sev | Files | Finding | Proposed fix |
|---|---|---|---|---|
| **X-01** | 🟡 | Crucible Stage-1 §12, AFC v9.0 §15, slice §9 | Harness `agents/` + `commands/` nested under `hooks/guard.py` in all three trees. | Place under `.opencode/`; `hooks/guard.py` stands alone. |
| **X-02** | 🟡 | Crucible Stage-1 §6 | Wikipedia pageviews used as a PIT attention tag — daily counts close at the UTC day boundary, and availability lags. | Record `available_at` per source (same rule as A-10). |

---

## 6. What is strong (keep as-is)

- The **E1–E10 perturbation catalog** with tiers, and the **E10 omission** insight — a finding that generalizes beyond finance.
- The **method-asymmetry** framing (reference-based vs consistency-based) — reportable in its own right.
- The **§14 / §14.1 source taxonomy** (SEC vs FINRA prose vs news) — a clean basis for S3 studies.
- The **pre-commit, Python-pin, logging and harness standards** — consistent and well-reasoned; they just need the tree fixes.
- The **signalcore boundary discipline** — primitives in, decisions out — held up under CORRECTION 45.

---

## 7. Evidence

| Claim | Source |
|---|---|
| `factscore` 0.2.0 pins `torch<2.0`, `openai<0.28`; released 2023-10-14 | PyPI JSON metadata, pulled 2026-10-02 |
| `pandasai` 3.0.0 requires Python `<3.12`, `numpy<2`, `scipy==1.10.1` | PyPI JSON metadata, pulled 2026-10-02 |
| `selfcheckgpt` 0.1.7 (2024-03-10), sdist only, unpinned torch/transformers/spaCy | PyPI + project `setup.py` on GitHub |
| `deepeval` 4.2.8, `edgartools` 5.60.0, ruff 0.16.10, uv 0.12.22 support 3.14 | PyPI JSON metadata, pulled 2026-10-02 |
| FActScore = atomic fact generation + validation; OpenFActScore shows absolute scores shift with the model while rankings hold | Min et al., arXiv 2305.14251; Lage & Ostermann, arXiv 2507.05965 |
| SEC fair access: 10 requests/second, declared User-Agent | sec.gov — Accessing EDGAR Data |
| Section 16 filings accepted after 5:30 p.m. ET (until 10:00 p.m.) keep that day's filing date | sec.gov — Determine the Status of My Filing; SEC 2003 Forms 3/4/5 release |
| FINRA consolidated short-interest files include exchange-listed securities only from June 2021; archives back to 2014 | finra.org — Equity Short Interest Files |
| FINRA publishes short interest on the 7th business day after the settlement date | FINRA short-interest description (via OpenBB data-model docs) |
| GDELT DOC 2.0: timeline modes search from 2017; article-list modes return only the latest ~3 months of the window | blog.gdeltproject.org — DOC 2.0 updates |

---

## 8. Approval checklist — edits I would make once approved

- [ ] **D-0** — retire backtest/dashboard/trigger leaderboard from AFC v9.0 (or amend the roadmap instead).
- [ ] **A-02** — move §5.6 from AFC v9.0 to Crucible Stage-1 §4.1; update SignalCore §2/§3 consumers (SC-03).
- [ ] **D-6** — mark slice v1.3 superseded by `v9_1_STAGE1`; draft `ATTENTION_FLOW_CATALYST_SCOPE_v1_0_FULL_PRODUCTION.md` from v9.0's S2/S3 content.
- [ ] **SC-01** — publication-date knowledge time for the short-interest accessor (+ ADR).
- [ ] **Mechanical fixes** (no decision needed beyond "go"): A-12, A-13, A-17, A-18, A-19, A-20, A-23, S-01, X-01.
- [ ] **Approve D-1 … D-5, D-7, D-8** in the build sheet §20, or choose the alternatives.
- [ ] **Roadmap** — CORRECTION 45 entry still unwritten; D-0 and A-02 would amend it.
