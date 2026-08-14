# 📋 AUTONOMOUS DISTRIBUTION QA REVIEW AGENT — Pre-Scope Decision Brief v0.1

## Event-Driven Document Intake → Rule + LLM Review → Escalation or Sign-Off → NIGO Quality Mart

**Document Version:** 0.1 (PRE-SCOPE — not a scope document. This exists to lock the three rulings that must precede a Stage-1 and Full-Production scope pair, per the standing governance cycle: propose → gap-analyze → explicit approval → execute.)
**Last Updated:** August 14, 2026
**Status:** 🟡 AWAITING APPROVAL — no scope document written, no file changed, no roadmap correction drafted.
**Author:** Manuel Reyes (brief prepared by coach)
**Governing document:** Career Roadmap **v10.0 (2026 Market Realignment)**
**Reference scopes:** `POLICYPULSE_HR_RAG_SCOPE_v1_6_STAGE1.md` · `POLICYPULSE_HR_RAG_SCOPE_v2_0_FULL_PRODUCTION.md`

---

## 🎯 RULING BLOCK — READ FIRST

> **This block governs.** Everything below it is supporting evidence for these three rulings. Nothing is executed until each is approved, amended, or rejected.

| # | Ruling | Recommendation | Your call |
|---|--------|----------------|-----------|
| **R1** | **Identity** — is this a new project or an existing one? | ⭐ **Not new. This is FormSense v3.0.** | ☐ Approve ☐ Amend ☐ Reject |
| **R2** | **Portfolio tier** — flagship or supporting? | ⭐ **Qualifies on merit; promote on evidence, not on plan.** Ships as Supporting with a defined promotion gate. | ☐ Approve ☐ Amend ☐ Reject |
| **R3** | **Confidentiality** — what may enter the public repo? | ⭐ **Nothing from the Gmail attachments. Zero. Re-authored synthetic corpus only.** | ☐ Approve ☐ Amend ☐ Reject |

---

## 1. R1 — Identity: this is FormSense v3.0, not a new project

**Finding.** The roadmap already carries this project. FormSense is scoped as:

> *"Agentic workflow (Anthropic taxonomy — interview-defensible vocabulary), multimodal extraction on synthetic ERISA forms, GEval schema adherence. S1 → S3. Supporting."*

That is the described system's extraction half, already in the plan, already with a v2.0 scope document, already restructured onto the 3-stage scale in CORRECTION 27, already carrying `nbstripout` Tier-B pre-commit under CORRECTION 21.

**What the described system adds that FormSense v2.0 does not have:**

| Capability | FormSense v2.0 | Described system | Why it matters |
|---|---|---|---|
| Trigger | Invoked (notebook / CLI) | **Event-driven autonomous intake** — watches a folder, claims new work | Turns a script into a *running system*. The single biggest production-signal delta. |
| Decision boundary | Extract + validate schema | **Route: sign-off ↔ escalate to processor** | An escalation boundary is the HITL artifact FDE loops probe. Extraction alone is not. |
| Output | Structured record | **Review report artifact** written to a second folder | A durable, auditable deliverable a non-technical reviewer consumes. |
| Analytics | None | **IGO/NIGO quality mart** — first-pass rates, reason Pareto, drift | This is the entire Analytics-Engineer half. FormSense v2.0 has no AE surface at all. |
| Baseline | None | **An incumbent to beat** — the existing Copilot Chat agent | Rare and valuable. See §2. |

**Consequence if approved.** No 6th project is created. `FORMSENSE_SCOPE_v2_0` is superseded in place under additive-only governance (struck through with rationale, archive untouched), and a `v3.0` Full-Production + a Stage-1 build sheet are written against the enlarged system. Project-scope count stays at 14. Portfolio count stays at 5.

**⚠️ Falsifier for R1.** If FormSense v2.0's actual §4 business problem is *not* ERISA distribution forms — i.e. if it targets a different document class entirely — this ruling collapses and the correct answer is a separate 6th project. **I have not read `FORMSENSE_SCOPE_v2_0.md`.** Confirm or upload it before R1 is executed.

---

## 2. R2 — Portfolio tier: it qualifies, but promote on evidence

### 2.1 Why it qualifies on merit

**It is the only artifact in the portfolio that hits both target doors with one system.** The apply window targets *Analytics Engineer first door · Data Engineer parallel*, with Applied AI / FDE as Stage 3. Current coverage:

| Project | AE/DE door | Applied-AI door |
|---|---|---|
| DataVault / 1099 | 🟢 Lead | 🟡 S3 layer only |
| PolicyPulse | 🟡 S2 layer only | 🟢 Lead |
| Crucible | 🟡 | 🟡 |
| **This system** | 🟢 **NIGO mart, dbt, contracts, grain design** | 🟢 **Agentic loop, escalation, three-layer eval** |

**It has an incumbent baseline — which almost no portfolio project ever has.** The Copilot Chat agent already runs this review. That permits a defensible before/after that is *not* a synthetic claim: *"the prompt-only version flagged N% false-NIGO; the governed version with deterministic pre-checks flags M%."* Reviewers discount portfolio metrics precisely because there is usually nothing to compare against. Here there is.

**It is the strongest available answer to the FDE discovery round.** The roadmap already records that ~60% of technically-passing candidates are filtered at the customer-conversation stage, and 73% of frontier-lab FDEs name discovery as their least-prepared skill. This project *is* a discovery artifact: an undocumented human review workflow, decomposed into rules, escalation thresholds, and an audit trail.

### 2.2 Why "promote on evidence" rather than promote now

- **Hours.** 25 hrs/week is frozen; last day of employment is 9 October 2026; the apply window is ~Q1 2027. Three flagships already owe S2/S3 layers. A 4th flagship-grade commitment is an unfunded schedule liability.
- **"Replace, not stack."** Promotion is a stacking move unless something is demoted, and nothing here deserves demotion.
- **Consistency with your own philosophy.** *Proof over keywords.* Promotion decided by a plan is a keyword. Promotion decided by a shipped eval number is proof.

### 2.3 The promotion gate (this is the falsifier, stated forward)

> **Promote FormSense v3.0 from Supporting to Flagship when all three hold:**
> 1. **S1 ships** — event-driven intake, deterministic rule layer, escalation routing, review-report artifact, all on a synthetic corpus.
> 2. **A measured false-NIGO rate exists** against a frozen golden set of ≥ 200 documents, and the deterministic layer demonstrably beats a prompt-only baseline on that same set.
> 3. **The NIGO mart is live** — dbt models tested and CI-gated, with at least one non-obvious operational finding (a reason Pareto, a drift signal) that a reviewer can interrogate.
>
> **If all three hold, this becomes the 4th flagship and the portfolio line becomes "4 Flagships + 1 Supporting (AFC)," requiring a roadmap correction.** If any fails, it stays Supporting and the correction is never written. Cost of being wrong in this direction: $0.

---

## 3. R3 — Confidentiality: a hard gate, not a caveat

**This is the one item that can sink the project, and it is worth being blunt about.**

The Gmail attachments are live employer work product containing plan-sponsor identifiers — five named school-district retirement plans appear in the PDF filenames alone. Under the roadmap's own ERISA rule, **client identifiers and any participant or plan data are 🚫 Prohibited** in public documents, and the deposition test applies to every figure.

### 3.1 What may and may not cross into the repo

| Artifact | Private spec input | Public repo | Rule |
|---|---|---|---|
| Distribution transaction reports (`.xlsx`) | ✅ Read to derive column schema | 🚫 Never — not a row, not a filename | Contains plan + participant data |
| the recordkeeper distribution forms (`.pdf`) | ✅ Read to derive field map | 🚫 Never — filenames alone name plan sponsors | Client identifiers |
| Distribution SOP (`.docx`) | ✅ Read to derive rule set | 🚫 Never — verbatim, paraphrased, or restructured | Employer IP; document version string is itself identifying |
| Copilot agent instructions (`.txt`) | ✅ Read as the incumbent baseline | ⚠️ **Re-authored only** — same intent, own words, against the synthetic schema | Arguably employer work product |
| Recordkeeper / employer name | — | 🚫 **Never appears** | Employer-name scrub is already standing policy |

### 3.2 The consequence, stated plainly

The public repo runs on a **synthetic ERISA distribution corpus you generate** — synthetic forms, a synthetic SOP, a re-authored rule pack, synthetic transaction reports. This is not a downgrade; it is the same posture DataVault already holds, and it is enforceable rather than aspirational because `nbstripout` + `gitleaks` are already in your Tier-A/B pre-commit set.

**Two things this constraint buys you, which are worth more than the real data would have been:**
1. **A synthetic-corpus generator is itself a portfolio artifact** — controlled NIGO injection (missing signature, gross/net mismatch, wrong distribution code, stale date) gives you a *labeled* golden set with known ground truth. Real documents would not. This is the same controlled-perturbation move AFC already uses.
2. **"The regulatory constraints that shaped the design" is explicitly named in your roadmap as the genuinely scarce signal** and an ADR topic in its own right.

**⚠️ Blocking item.** I could not read the attachments — the Gmail connector exposes no attachment-download tool, and pulling the full message would have dumped 12 base64 blobs into context. **See §7 for what I need.**

---

## 4. Recommended architecture (the preferred design)

### 4.1 The trigger — the decision that carries the whole system

**Recommendation: filesystem event as a *hint*, polling as the guaranteed floor, content hash as the identity.**

Naïve `watchdog` → `on_created` → process is the failure mode this design exists to avoid, and it is well documented:

- **Large files raise multiple modified events during copy**, making it impossible to know from the event alone when a write is complete (watchdog issue #309 — open since 2015 and still the canonical report).
- **Duplicate events fire on the same modification** across platforms; Salt hit this in production on Windows, where multiple kernel events surface through watchdog for a single edit (saltstack #64684; watchdog #346).
- **Network shares break inotify/FSEvents entirely.** A corporate SMB/NFS folder — the realistic deployment target — often delivers no events at all.

**The design:**

```
┌─ watchdog observer ──┐
│  (hint, best-effort) ├──┐
└──────────────────────┘  │    ┌──────────────┐   ┌───────────────┐
                          ├───►│  candidate   │──►│ stability gate│
┌─ poll sweep (60s) ───┐  │    │    queue     │   │ size+mtime    │
│  (floor, guaranteed) ├──┘    └──────────────┘   │ stable × N    │
└──────────────────────┘                          └───────┬───────┘
                                                          ▼
                        ┌──────────────────────────────────────────┐
                        │  claim table (SQLite/Postgres)           │
                        │  key = sha256(content), NOT path         │
                        │  discovered→claimed→reviewing→igo/nigo   │
                        │  lease TTL → crashed worker auto-retries │
                        └──────────────────────────────────────────┘
```

Four properties this buys, each of which is an interview answer:

| Property | Mechanism | Failure it prevents |
|---|---|---|
| **Exactly-once** | Content-hash idempotency key | Duplicate events; re-drops; renames; the same file arriving twice under different names |
| **Never reads a partial write** | Size + mtime unchanged across N consecutive checks | The watchdog #309 class of bug |
| **Crash-safe** | Durable claim table + lease TTL | A killed worker silently swallowing a document |
| **Works on a network share** | Poll floor runs regardless of event support | Silent zero-throughput on SMB/NFS |

**Runner-up considered and declined: poll-only, no watchdog.** Simpler and more portable. Declined because the event path gives sub-second latency on a local/container volume at negligible cost, and the poll floor already covers the case where events do not arrive — so removing watchdog buys simplicity but loses responsiveness with no reliability gain. ⚠️ **Falsifier:** adopt poll-only if the deployment target is confirmed to be a network share *and* the watchdog observer is measured to add no detections the poll sweep misses.

### 4.2 The review loop — a workflow with an agentic verification step

Per the Anthropic taxonomy FormSense already uses, this is **not** an autonomous agent. It is a deterministic workflow with a bounded LLM verification step. That distinction is itself the differentiator: a 2026 IDP vendor analysis identifies five things falsely marketed as agentic, of which the first is *"LLM-powered single-pass extraction."* Naming the boundary correctly is what separates this from that.

```
intake → classify → extract (schema-bound) → LAYER 1 rules → LAYER 2 judge → route
                                                    │              │           │
                                          deterministic     only where     ├─ IGO  → review report → /reviewed
                                          zero LLM cost     code can't     └─ NIGO → escalation packet → processor
                                                            express "good"
```

**Layer 1 (code — target 65–75% of NIGO detections).** Schema/required-field validation, cross-document arithmetic (form gross vs report gross), date logic, distribution-code consistency, signature-block presence, duplicate detection. Deterministic, free, fast, and fully explainable.

**Layer 2 (LLM — the remainder).** Only the judgment calls: is this mark actually a signature; does a handwritten annotation contradict a keyed value; is the stated distribution reason consistent with the plan provision. Current guidance puts LLM-as-judge at **25–35% of the eval surface** — the dimensions where code cannot express "good."

**Per-field confidence, not per-document rejection.** Field-level confidence enables field-level escalation with a conservative safe-override policy, rather than rejecting an entire document because one field is uncertain. For a NIGO reviewer this is the difference between "this form is wrong" and "field 7 needs a human," and the second is what a processor can act on.

### 4.3 The metric that gates the merge — and it is not accuracy

**The expensive failure is a false NIGO, not a missed NIGO.**

Extraction LLMs are systematically **high-recall, low-precision**. A 2026 benchmark on structured extraction from technical documents found zero-shot extraction precision of roughly 20–32% across frontier models while recall sat at 94–98%; the best few-shot precision reached only ~36%. Translated into this workflow: the model will flag nearly every real error, *and* a great many non-errors.

A reviewer who is sent five bad escalations for every good one stops trusting the tool and stops using it. That failure is terminal and unrecoverable, whereas a missed NIGO is caught downstream by the processor who was doing the review anyway.

> **Therefore the CI-blocking gate is false-NIGO rate (precision on the NIGO class). NIGO recall is reported but does not block.** Every eval, every A/B, and every prompt change is judged against that gate first.

**Golden set sizing (evidence-based):** ~50 examples detects large regressions; **200 gives statistical confidence on a 3–5% quality change**; beyond 500 is diminishing returns absent highly varied sub-tasks. Target **200, stratified by NIGO reason**, frozen, versioned.

**Scoring framework — do not hand-roll.** Field-level precision / recall / F1 with explicit classification into **omissions, hallucinations, and mismatches** is exactly the shape this project needs, and `extract-eval` (FAIRmat-NFDI, open source, schema-driven, config-in-schema so there is no drift between schema and eval) implements it. `ExtractBench`'s evaluation infrastructure (ContextualAI, open source) covers array alignment and LLM-based semantic matching if nested extraction is needed.

### 4.4 The analytics half — and the one ruling that makes it an AE artifact

**The spreadsheet is a rendered export, never the source of truth.**

This is the single most important design call on the AE side, and it is the one an Analytics Engineer interview will probe. Writing metrics directly into a workbook produces a mutable, un-versioned, un-testable artifact with no grain, no contract, and no lineage.

```
review_events (append-only)  →  dbt staging  →  dbt marts  →  rendered .xlsx export
        source of truth                                        (regenerated, never edited)
```

| Model | Grain | Notes |
|---|---|---|
| `stg_review_events` | one row per review event | Append-only; nothing is ever updated in place |
| `fct_document_reviews` | **one row per (document_version, review_run)** | ⚠️ Grain is the load-bearing decision: *not* one row per document, because a re-review after correction must remain distinguishable from the first pass |
| `dim_nigo_reason` | one row per reason code | Reason taxonomy is versioned; reason-code changes are additive |
| `mart_first_pass_quality` | one row per (period, plan_cohort, reason) | The reporting surface |

**Metrics that earn their place:**
- First-pass IGO rate (the headline)
- NIGO reason Pareto + **reason-mix drift over time** — a shift in mix is an upstream process signal, not noise
- **Agent-vs-human agreement rate** — the audit metric; it is what makes the whole system defensible and it produces an ERISA-safe *relative* figure
- Escalation precision (the §4.3 gate, tracked in production, not just in CI)

**Contracts:** Great Expectations on the source boundary; dbt tests CI-gated. Both are already the standing S2 requirement.

### 4.5 What S3 adds (the feedback loop)

Human corrections on escalated documents feed back to refine review behaviour **without retraining a model** — the mechanism a 2026 multi-agent document-processing paper formalises as prompt fine-tuning with feedback inheritance, evaluated on 955 real production documents with a reported 97.0% full-pipeline automation rate and 98.5% document-level accuracy under human-in-the-loop supervision. That paper is also the closest published analogue to this system and should be cited in the scope's market-validation section.

Also S3: Arize Phoenix tracing (same OTel backend, no second observability stack), MCP tool surface (`get_review_status`, `list_escalations` read-only; any write tool approval-gated), and drift-vs-frozen-golden-set as the third eval layer.

---

## 5. Evidence base

| Source | What it establishes | Weight |
|---|---|---|
| watchdog issues #309, #346; saltstack #64684 | Multiple modified events on large-file copy; duplicate events across platforms | 🟢 Primary (maintainer issue trackers) |
| ACL 2026 demo — *IDP Accelerator* | Production agentic IDP reference architecture; MCP-compliant analytics module; LLM-driven rule validation; healthcare deployment at 98% classification accuracy | 🟢 Peer-reviewed |
| arXiv 2026 — *MADP* | Multi-agent + HITL; 955 real documents; 97.0% automation, 98.5% doc-level accuracy with HITL; feedback-inheritance mechanism | 🟢 Primary |
| arXiv 2026 — *SecGoal* | Extraction precision bottleneck: zero-shot 20–32%, best few-shot ~36%, against 94–98% recall | 🟢 Primary — grounds the §4.3 gate |
| arXiv 2026 — *PromptPort* | Per-field confidence enabling field-level override with conservative safe-override policy | 🟢 Primary |
| `FAIRmat-NFDI/extract-eval` · `ContextualAI/extract-bench` | Schema-driven field-level P/R/F1 with omission / hallucination / mismatch classification | 🟢 Open-source, runnable |
| Galtea LLM evaluation guide (2026) | Golden-set sizing: 50 / 200 / 500 thresholds; warning that doc-derived and synthetic sets are "too clean" | 🟡 Directional |
| Kunal Ganglani, *Evaluate AI Agents in Production* (2026) | Four-layer eval model; LLM-judge at 25–35% of eval surface; shadow evaluation before promotion | 🟡 Directional |
| IDP-Software agentic guide (2026) | Five vendor claims that are not agentic; the "98% accuracy still means 20 daily failures at 1,000 docs/day" reliability framing | 🟡 Directional (vendor-adjacent) |

⚠️ **Directional sources are marked as such per the CORRECTIONS 18–19 evidence standard** and are not load-bearing for any ruling in §1–§3.

---

## 6. Proposed naming

| Option | Rationale |
|---|---|
| ⭐ **FormSense v3.0** (project) + **"Intake Gate"** (the autonomous subsystem) | Preserves continuity with an existing scope document, an existing roadmap row, and an existing pre-commit tier assignment. Renaming would orphan all three. |
| *DistribGuard* / *NIGO Sentinel* (new name) | Cleaner marketing, but creates a 6th project name across 14 scope docs, roadmap.html, and 5 public surfaces. Not recommended. |

---

## 7. ⛔ Blocking inputs — what I need before writing either scope

| # | Item | Why it blocks | How to send |
|---|---|---|---|
| **B1** | `FORMSENSE_SCOPE_v2_0.md` | R1 cannot execute without confirming FormSense's actual business problem. If it is not ERISA distribution forms, the identity ruling inverts. | Upload |
| **B2** | `distrib_agent_instructions.txt` | It is the incumbent baseline and the rule source. I could not fetch it — the Gmail connector exposes no attachment-download tool. | Upload directly |
| **B3** | Column headers only from one review `.xlsx` (**headers, zero data rows**) | Needed to define the synthetic generator's schema and the extraction contract | Paste as text |
| **B4** | Field labels only from one the recordkeeper form (**labels, no values, no plan name**) | Same — defines the field map | Paste as text |
| **B5** | Your NIGO reason taxonomy — the actual reason codes used today | This is `dim_nigo_reason`. Without it the mart has no dimension. | Paste as a list |
| **B6** | Deployment target: local folder, container volume, or network share? | Determines whether the watchdog hint layer survives §4.1's falsifier | One line |
| **B7** | Rough monthly document volume, order of magnitude only | Sizes the golden set and determines whether the reliability framing is 20 failures/day or 2/month | One line |

> ⚠️ **B3 and B4 must be structure only.** Headers and labels are schema; values are participant and plan data. If in doubt, send fewer.

---

## ✅ Approval Checklist

- [ ] **R1** — FormSense v3.0 identity ruling approved (or B1 supplied and ruling revisited)
- [ ] **R2** — Supporting-with-promotion-gate approved; three-condition gate accepted as written
- [ ] **R3** — Confidentiality gate approved; synthetic-corpus-only confirmed; employer/recordkeeper name scrub confirmed
- [ ] §4.1 trigger design approved (event-as-hint + poll floor + content-hash claim table)
- [ ] §4.3 false-NIGO-rate-as-blocking-gate approved (recall reported, not blocking)
- [ ] §4.4 spreadsheet-as-rendered-export ruling approved; `fct_document_reviews` grain accepted
- [ ] Naming approved (FormSense v3.0 / Intake Gate)
- [ ] Blocking inputs B1–B7 supplied
- [ ] **Then, and only then:** Stage-1 build sheet + Full-Production scope written to the PolicyPulse template structure

---

## 📌 Deliverables on approval

1. **`FORMSENSE_SCOPE_v3_0_FULL_PRODUCTION.md`** — PolicyPulse v2.0 structure: authoritative v10.0 alignment block, 21 sections, 3-stage evolution table, skills-required mapping, per-stage courses/certs, README standard, approval checklist.
2. **`FORMSENSE_SCOPE_v3_1_STAGE1.md`** — PolicyPulse v1.6 structure: phase-by-phase build sheet, Pydantic schemas, project tree, guardrail tests, eval harness, synthetic-corpus strategy, timeline.
3. **Supersession note** for `FORMSENSE_SCOPE_v2_0.md` — struck through in place with rationale, additive-only, archive untouched.
4. **Draft roadmap CORRECTION** — held unwritten until the §2.3 promotion gate fires. Not produced now.

---

*No file was changed and no roadmap correction was drafted in producing this brief.*
