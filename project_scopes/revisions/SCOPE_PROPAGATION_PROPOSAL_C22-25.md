# 📐 Scope-Document Propagation Proposal — Roadmap CORRECTIONS 22–25

**Status:** 🟡 PROPOSAL — awaiting approval. **No files edited.**
**Prepared:** August 10, 2026
**Scope:** 14 project scope documents vs. `roadmap.html` v10.0 (Corrections 1–25 applied)
**Governance:** propose → approve → edit. Additive-only. Archive untouched.

---

## 1. Verification Result

I read `roadmap.html` and all 14 scope documents and diffed the correction state.

| Check | Result |
|---|---|
| Roadmap version | **v10.0**, snapshot **Corrections 1–25 applied** |
| Highest correction propagated to scope docs | **CORRECTION 21** (pre-commit) — in **all 14** files |
| CORRECTIONS 22–25 propagated | ❌ **Zero of 14 files** |
| Files mentioning Vercel / AI SDK | **0 of 14** |
| Files mentioning TypeScript | **2 of 14** (PolicyPulse v2.0, StreamSmart v2.0 — both pre-existing, neither aligned) |
| Insertion anchor uniform across files | ✅ Yes — every file ends its alignment block with the CORRECTION 21 pre-commit blockquote |

**Bottom line:** your memory of the roadmap state was one correction behind (you had C22; the file is at C25), and the scope docs are **four corrections behind**. The propagation Correction 22 §8 deferred has not been executed. This proposal executes it.

**Corrections 24 and 25 do NOT propagate.** C24 (row 18 → row 19 split) and C25 (`<details>` collapse) are roadmap-internal presentation changes. Nothing in a scope document references roadmap row numbers. Propagating them would add noise with no truth content. **Recommend: log as "evaluated, no propagation required."**

---

## 2. Web Research — What Changed Since the Roadmap Was Written

⚠️ **Material finding: the roadmap's AI SDK framing is behind the release.**

**AI SDK 7 shipped June 25, 2026.** Corrections 22–23 were written in August 2026 but describe the SDK generically. AI SDK 7 is now at `ai@7.0.35`, with over 16 million weekly downloads (Vercel, primary source).

Five things in AI SDK 7 interact directly with invariants already written into your scope docs:

| AI SDK 7 feature | Interacts with | Verdict |
|---|---|---|
| **Tool approvals** (HITL, incl. HMAC-signed approvals to prevent forgery) | Crucible's mandatory human sign-off; PolicyPulse's approval-gated write tools | ⚠️ **Must be explicitly NOT adopted for Crucible** — see §4 Block C |
| **`@ai-sdk/otel` + `registerTelemetry()`**, OTel GenAI semantic conventions | Your S3 standard is Arize Phoenix (OTel-native) | ✅ **Adopt** — one trace backend across Python + TS, not two |
| **Selective telemetry allowlists** (`includeRuntimeContext`) | Your three-layer PII defense / `redact_pii` processor | ✅ **Adopt** — TS layer needs its own allowlist or it leaks tenant/user IDs by default |
| **MCP Apps** (app-only tools + sandboxed iframe UI) | PolicyPulse §8 MCP surface | 📋 **Note as candidate**, don't scope yet |
| **Timeouts** (total / step / chunk / tool) | "iteration cap = safety backstop" language | ✅ Mention in passing |

### Two evidence upgrades available

**(a) Your guardrail can stop resting on vendor-adjacent sources.** The C22 guardrail ("TS frameworks lack depth for multi-agent collaboration and long-horizon planning") currently cites the same 2026 sources that recommended the SDK — circular. An independent June 2026 practitioner review of the AI SDK 7 release draws the identical boundary unprompted: the SDK is strongest as a TypeScript-first *application layer*, and weaker for multi-hour orchestration, language-agnostic workflows, and deeply stateful agent planning. It goes further and describes your exact situation — teams deploying agents across Python services, queues and data pipelines should treat it as a good SDK layer, not an orchestration standard. **This is convergent, independently-sourced corroboration. Recommend recording it.**

**(b) The "62% of TypeScript projects" stat has a better replacement.** That figure is third-party and unverifiable. Vercel's own release post states **16M+ weekly downloads** — primary source, checkable, and stronger. **Recommend: keep 62% but add the primary-source figure alongside it**, consistent with your evidence-quality standard.

### One unexpected find worth your attention

The same review raises a **platform-gravity criticism**: if the best experience assumes AI Gateway + Vercel Workflows + Vercel Sandbox + Vercel Observability + Next.js, teams drift into a *platform* decision before making an *architecture* decision.

This independently validates your CORRECTION 23 gateway swap — but reframes it. **It is not only a privacy decision. It is an architecture-vs-platform decision.** That makes it a substantially stronger ADR, and a better interview answer. Recommend the ADR be written on both grounds.

### Verified unchanged (no edit needed)

- ✅ Vercel Academy "Builders Guide to the AI SDK" — free, vendor certificate, and confirmed **"uses the latest AI SDK v7."** C23's v7-current claim holds.
- ✅ Scrimba/Coursera course still shows **Free Trial** status → your "verify Coursera Plus at enrolment, else skip" caveat holds. No change.
- ✅ AI Hero tutorial (Matt Pocock) — live.

---

## 3. Gap Register

| # | Gap | Severity | Files |
|---|---|---|---|
| **G1** | **Factual contradiction.** PolicyPulse v2.0 line 477 reads `TypeScript + Zod \| S2 (Month-14 sprint)`. C22 re-timed this to immediately before the Q1 2027 apply window. The doc asserts a timing the roadmap retired. | 🔴 **High** | PolicyPulse v2.0 |
| **G2** | **The named deliverable has no home.** The roadmap commits to a streaming Claude UI over *PolicyPulse's* retrieval. PolicyPulse's Tech Stack, Project Structure and Skills table contain no such layer. A build target that lives only in the roadmap is exactly the drift this pass exists to close. | 🔴 **High** | PolicyPulse v2.0, v1.6 |
| **G3** | **Language ruling unrecorded.** C22's Python+SQL confirmation, PySpark-as-differentiator, and the Rust/Go/Java/Scala/JS declines-with-falsifiers appear in no scope doc. | 🟠 Med | All 13 project docs |
| **G4** | **Crucible safety ambiguity.** AI SDK 7 ships HITL tool approvals. Nothing in Crucible's docs says these do not satisfy its live-path invariant. A reader holding both documents could reasonably conclude they do. **They do not.** | 🔴 **High** | Crucible v1.0, v3.1 |
| **G5** | **Privacy-routing amendment unrecorded.** The gateway→`@ai-sdk/anthropic` swap and synthetic-data-only constraint exist only in the roadmap. | 🟠 Med | PolicyPulse v2.0, v1.6 |
| **G6** | **StreamSmart pre-existing TS.** v2.0 already specifies React + TypeScript frontend. Under "last mile only" this is *consistent* — a UI is the last mile — but unrecorded, it reads as contradiction. | 🟢 Low (backlog project) | StreamSmart v2.0 |
| **G7** | **AI SDK 7 observability opportunity unrecorded.** Without a note, the natural default is a second, parallel TS observability stack. | 🟠 Med | PolicyPulse v2.0 |

### Out-of-scope drift found (flagging, NOT fixing without separate approval)

These are real and predate this pass. I recommend a **separate correction** rather than folding them in:

1. **5-stage residue.** PolicyPulse §8 and FormSense §8 MCP tables label write tools **"Stage 4"** — the 3-stage governing block retired Stage 4. Direct contradiction with the block that says it governs.
2. **Python version.** DataVault skills table says **"Python 3.11+"**; roadmap unified on **3.12** across ruff/mypy/Dockerfile. Also `1099_reconciliation_pipeline` README carries a Python 3.11 badge.
3. **Correction 16 missing** from PolicyPulse v2.0 and StreamSmart v2.0 (present in the other 12). Possibly deliberate; flagging so it's a decision, not an accident.
4. **`attention-flow-catalyst` README is badly stale** — still titled "Trading Attention Tracker," Python 3.8+, framed as a Python-for-Everybody capstone. AFC scope is at v9.0. This is the largest public-facing drift I found and it is on a repo a recruiter can reach in one click. **Recommend prioritising this above the scope-doc pass.**

---

## 4. Proposed Text — Copy-Ready

All blocks are **additive**, inserted immediately after the existing CORRECTION 21 pre-commit blockquote (the `prek` falsifier paragraph), matching house voice: bold ruling, ⚠️ flags, explicit falsifiers.

---

### BLOCK A — Language & last-mile standard
**Applies to: all 13 project scope docs** (SignalCore optional — see §5)

> **🆕 Language & AI last-mile standard (roadmap v10.0 CORRECTIONS 22–23, August 2026).** **Python and SQL are confirmed as the correct and sufficient primary languages** for this portfolio. **SQL is the single highest-signal language in DE postings**, and **PySpark is the capturable differentiator — reached through Python, not adopted as a separate language.** **Rust, Go, Java, Scala and standalone JavaScript were each evaluated and declined with recorded falsifiers**; JavaScript specifically as *redundant*, since TypeScript is a superset of it and the Stage-2 TypeScript sprint already covers that ground. **TypeScript is retained for the last mile only** — MCP protocol tooling and the AI application/UI layer. **This project stays Python-primary:** agent cores, retrieval, orchestration, evaluation and any long-horizon planning remain Python. ⚠️ **Falsifier:** revisit only if a target employer posts a JD naming a different primary language for the role being applied to.
>
> ⚠️ **Evidence note — guardrail independently corroborated (August 2026).** The last-mile guardrail no longer rests solely on the sources that recommended the SDK. An independent practitioner review of the **AI SDK 7** release (June 2026) draws the same boundary unprompted: the SDK is strongest as a **TypeScript-first application layer**, and weaker where the core problem is multi-hour orchestration, language-agnostic workflows or deeply stateful agent planning — with the explicit note that teams deploying agents across **Python services, queues and data pipelines** should treat it as *an SDK layer, not an orchestration standard*. That is this portfolio's exact shape. Convergent and independently sourced; recorded as **directional**, per the CORRECTIONS 18–19 evidence standard.

**Project-specific closing line** — append one:

- **DataVault (both), ODI:** `**No TypeScript layer in this project.** This is a Python/SQL data-platform project end to end; the last-mile TS layer belongs to PolicyPulse. Recorded so the absence is a decision, not an omission.`
- **AFC, ACF, FormSense (both):** `**No TypeScript layer scoped for this project at present.** ⚠️ **Falsifier:** revisit only if a UI deliverable is added to this project's scope by approval.`
- **Crucible (both):** *(omit — Block C is stronger and supersedes)*
- **PolicyPulse (both):** *(omit — Block B follows immediately)*
- **StreamSmart v2.0:** see Block D.

---

### BLOCK B — AI last-mile layer (Vercel AI SDK)
**Applies to: PolicyPulse v2.0 and v1.6 only**

> **🆕 AI last-mile layer — Vercel AI SDK (roadmap v10.0 CORRECTIONS 22–24, August 2026).** PolicyPulse is the **named build target** for the roadmap's AI-application-layer deliverable: a **streaming Claude-powered UI over this project's existing retrieval**, built with the **Vercel AI SDK** (free, open-source, TypeScript). Selected on adoption evidence — **16M+ weekly downloads** (Vercel, primary source; a separate 2026 analysis puts it in **62% of TypeScript projects started that year**, recorded as directional), the named industry standard for streaming AI UIs, and **model-agnostic across 25+ providers**, matching this portfolio's provider-agnostic architecture and Anthropic-primary routing.
>
> 🧱 **It ships as a sibling package, not a rewrite.** `web/` sits alongside `src/policypulse/` and consumes the retrieval surface over HTTP. **The Python core is untouched** — ingestion, retrieval, fusion, the evaluator-optimizer loop, eval, and the FastMCP server all remain Python. **Produces the AI last-mile artifact a Python-only portfolio cannot show.**
>
> ⏱️ **Timing.** The sprint sits **immediately before the compressed Q1 2027 apply window** — re-timed from the retired "~Month 14" placement, which was set against the pre-resignation calendar — so the evidence is fresh at interview. It is **subordinate to DataVault S2 and never competes with the evidence gate.**
>
> ⚠️ **Privacy-routing amendment (CORRECTION 23) — binding.** The official Vercel starter repo ships **Vercel AI Gateway**, which routes prompts through Vercel. Under this portfolio's privacy-first model-routing rule that is **not acceptable for anything but synthetic data**. The gateway is **swapped for the direct `@ai-sdk/anthropic` provider** — a one-line change the SDK's provider-agnostic design makes trivial — and the build runs **synthetic policy corpus only**, the same constraint every public repo here already carries. **Recorded as an ADR in `docs/adr/` on two grounds, not one:** (a) *privacy* — prompts must not transit a third-party gateway; (b) 🆕 *architecture over platform* — an independent 2026 review of AI SDK 7 warns that when the best experience assumes AI Gateway, Vercel Workflows, Vercel Sandbox, Vercel Observability and Next.js together, teams **drift into a platform decision before making an architecture decision**. Declining the gateway is how this project keeps the SDK as a library rather than inheriting a platform.
>
> 🔭 **Observability — one trace backend, not two (🆕 AI SDK 7, June 2026).** AI SDK 7 ships **`@ai-sdk/otel`** with a single application-startup `registerTelemetry(new OpenTelemetry())` call and optional **OpenTelemetry GenAI semantic conventions**. Because this project's S3 observability standard is **Arize Phoenix (OTel-native)**, the TypeScript last-mile layer **emits into the same trace backend as the Python core**. **No second observability stack is introduced.** ⚠️ **PII constraint carries across the language boundary:** AI SDK 7 telemetry is **allowlist-based** (`includeRuntimeContext` / `includeToolsContext`), and anything not explicitly allowlisted must stay out of spans. The three-layer PII defence and the `redact_pii` posture apply to the TS layer exactly as they do to `structlog` on the Python side — **the boundary is a language boundary, not a policy boundary.**

---

### BLOCK C — AI SDK 7 tool approvals: evaluated, NOT adopted
**Applies to: Crucible v1.0 and v3.1 only** — ⭐ *this is the block I most recommend approving*

> **🆕 AI SDK 7 tool approvals — evaluated, explicitly NOT adopted on the live path (roadmap v10.0 CORRECTIONS 22–23, August 2026).** AI SDK 7 (June 2026) added agent-level **tool approvals**: per-tool `user-approval`, approval functions that can auto-approve / auto-deny / forward to a human, opt-in **HMAC-signed approvals to prevent forged approvals**, revalidation of tool inputs and policies before resuming, and **`WorkflowAgent` durability** so a run survives process restarts and deploys while waiting on a delayed approval. On paper this is a close match for this project's HITL gate. **It is deliberately not adopted as that gate.**
>
> 🛑 **The invariant is unchanged.** **The mandatory human sign-off and the kill-switch live in the Python core, behind the Wall, and are the authoritative control.** A TypeScript approval UI is a *presentation* of that gate — never the gate itself. **An approval mechanism reachable from the agent's own application layer is not a control that survives the threat model this project exists to demonstrate.** The claim being sold here is *"the agent can do everything except the one thing that matters, and I can prove the sign-off and kill-switch hold"* — that proof requires the gate to sit somewhere the agent's runtime cannot reach, which a same-process SDK approval callback does not satisfy. **Crucible remains Python-primary and TypeScript-free on the execution path.**
>
> 📌 **Corroboration.** The AI SDK 7 documentation and independent review are themselves explicit that **the SDK does not solve agent security** — tool allowlists, approval gates, prompt-injection defences, logging policy and rollback paths remain the implementer's responsibility. This decline is consistent with the vendor's own framing, not opposed to it.
>
> ⚠️ **Falsifier (two conditions, both required).** Revisit only if **(a)** the human sign-off and kill-switch remain fully enforced **server-side in Python**, with the TS layer strictly a display and request surface, **and (b)** the adversarial kill-switch suite is extended to demonstrate the TS layer **cannot bypass, forge or replay an approval** — with HMAC-signed approvals treated as *defence in depth, never as the primary control*. **Decided by ADR at that point, not by SDK capability now.**

---

### BLOCK D — StreamSmart TypeScript reconciliation
**Applies to: StreamSmart v2.0 only** *(backlog project — lowest priority)*

> **🆕 Pre-existing TypeScript frontend — reconciled, not retracted (roadmap v10.0 CORRECTION 22, August 2026).** This document already specifies a **React + TypeScript** web app and React Native mobile client. That specification **stands and is consistent with** the CORRECTION 22 last-mile ruling: the ruling restricts TypeScript to the **MCP tooling and AI application/UI layer**, and a product UI *is* the last mile. **The agent core, LangGraph orchestration, HITL checkpoints, browser automation and eval pipeline remain Python.** Recorded so the pre-existing TS stack is not later misread as a contradiction of the last-mile rule. ⚠️ **Status note:** this project is **backlog / positioned last** and outside the active lead set; nothing here authorises starting it ahead of the flagships.

---

### In-body targeted edits — PolicyPulse v2.0 (the only files needing surgical changes)

**Edit B1 — §21 Skills table (fixes G1, the factual contradiction).**
Replace:
```
| TypeScript + Zod | S2 (Month-14 sprint) | TS MCP server variant; Zod = MCP SDK input validation |
```
With:
```
| TypeScript + Zod | S2 (sprint immediately before the Q1 2027 apply window) | TS MCP server variant; Zod = MCP SDK input validation — and carries directly into the AI SDK layer below, no new prerequisite |
| **Vercel AI SDK (TypeScript — last mile)** | **S2 sprint** | **Streaming Claude-powered UI over this project's retrieval; direct `@ai-sdk/anthropic` provider (no gateway); synthetic corpus only; `@ai-sdk/otel` → same Phoenix/OTel backend as the Python core** |
```

**Edit B2 — §11 Tech Stack table.** Append row:
```
| AI last-mile UI | — | Vercel AI SDK (TS) — streaming Claude UI over retrieval; direct `@ai-sdk/anthropic` provider, gateway declined (ADR); synthetic data only |
```

**Edit B3 — §16 Project Structure.** Insert after the `guardrails/` line:
```
  web/           # 🆕 TS last mile — Vercel AI SDK streaming UI over the retrieval API
                 #    sibling package, NOT a rewrite; Python core untouched
```

**Edit B4 — §8 MCP Server.** Append note:
> 🆕 **MCP Apps (AI SDK 7, June 2026) — noted as a candidate, not scoped.** MCP servers can now separate **model-visible tools from app-only tools**, preserve app metadata, and render an app UI inside a **sandboxed iframe** via a JSON-RPC bridge. That maps cleanly onto this project's approval-gated write tools — `propose_policy_update` could surface a reviewable diff in an app pane rather than as raw text for HR. **Recorded as a candidate for the S3 MCP surface; not scoped, and not a prerequisite for the Week-2(b) build.** ⚠️ **Falsifier:** scope it only after the Week-2(b) UI lands and only if the approval-review UX is demonstrably worse without it.

**Edit B5 — PolicyPulse v1.6 §11 Project Structure.** Same `web/` entry as B3, marked `(S2 sprint — not built at Stage 1)`.

---

## 5. Propagation Matrix

| # | File | Block A | Block B | Block C | Block D | In-body |
|---|---|:--:|:--:|:--:|:--:|:--:|
| 1 | POLICYPULSE v2.0 FULL | ✅ | ✅ | — | — | **B1–B4** |
| 2 | POLICYPULSE v1.6 STAGE1 | ✅ | ✅ | — | — | **B5** |
| 3 | CRUCIBLE v1.0 FULL | ✅ | — | ✅ | — | — |
| 4 | CRUCIBLE v3.1 STAGE1 | ✅ | — | ✅ | — | — |
| 5 | DATAVAULT v1.0 FULL | ✅ | — | — | — | — |
| 6 | DATAVAULT v2.1 STAGE1 | ✅ | — | — | — | — |
| 7 | AFC_EVAL_FIRST v1.3 | ✅ | — | — | — | — |
| 8 | ATTENTION_FLOW_CATALYST v9.0 | ✅ | — | — | — | — |
| 9 | FORMSENSE v2.0 FULL | ✅ | — | — | — | — |
| 10 | FORMSENSE v1.8 STAGE1 | ✅ | — | — | — | — |
| 11 | OPERATIONS_DEMAND_INTEL v3.0 | ✅ | — | — | — | — |
| 12 | STREAMSMART v2.0 FULL | ✅ | — | — | ✅ | — |
| 13 | STREAMSMART v1.6 STAGE1 | ✅ | — | — | — | — |
| 14 | Shared_SignalCore v1.3 | ⚠️ **ruling needed** | — | — | — | — |

**⚠️ SignalCore requires your ruling.** It is a boundary spec between AFC and Crucible, not a project scope. It carries no tech-stack or language section, and it is the only file without the `prek` paragraph. **My recommendation: no edit.** Adding a language ruling to an interface contract is noise. But it is your call — it is technically one of the 14.

**Version numbers:** additive-only under your governance, so **no scope-doc version bumps**. Each file's `Last Updated` line is the only metadata change. Confirm this is right.

---

## 6. What I Recommend NOT Doing

| Rejected | Why |
|---|---|
| Propagating C24 / C25 | Roadmap-internal presentation. No scope doc references row numbers. Log as evaluated, no action. |
| Adding the Packt TypeScript course to scope docs | It is optional overflow and a **Tier 4/5 credential** — it belongs in the roadmap's course tables, not in a project scope. |
| Adding a TS layer to DataVault, AFC or FormSense | No UI deliverable is scoped. Block A's closing line records the absence as a **decision with a falsifier**, which is the correct treatment. |
| Adopting `WorkflowAgent` / `HarnessAgent` anywhere now | Genuinely interesting (HarnessAgent runs Claude Code and **OpenCode** — your harnesses). But **out of scope** and it would pull TypeScript past the last mile. Recommend logging as *watchlist*, not scope. |
| Fixing the "Stage 4" residue and Python 3.11 drift in this pass | Real bugs, but unrelated to C22–25. Mixing them makes the correction log dishonest about what this pass did. **Separate correction.** |

---

## 7. Roadmap-Side Change Required

Under additive-only governance this pass must itself be logged:

> **Proposed 🛠️ v10.0 CORRECTION 26** (August 2026 — scope-document propagation executed; same version): discharges the propagation deferred in CORRECTION 22 §8. **(1)** Blocks A–D appended to 13 of 14 project scope documents (SignalCore excluded — interface contract, no language surface). **(2)** Four in-body edits to PolicyPulse v2.0 + one to v1.6, including correction of a **factual contradiction**: the Skills table asserted a retired "Month-14 sprint" timing that CORRECTION 22 had already re-anchored to the pre-Q1-2027 window. **(3)** AI SDK framing updated to **AI SDK 7** (released June 25, 2026, after CORRECTIONS 22–23 were drafted): `@ai-sdk/otel` adopted for single-backend observability; telemetry allowlisting recorded as a PII constraint crossing the language boundary; MCP Apps noted as an S3 candidate; **tool approvals evaluated and explicitly declined on Crucible's live path with a two-condition falsifier.** **(4) Evidence upgraded:** the last-mile guardrail now carries independent corroboration rather than resting on the sources that recommended the SDK; the gateway-swap ADR now rests on architecture-vs-platform grounds in addition to privacy; primary-source adoption figure (16M+ weekly downloads) recorded alongside the third-party 62% figure. **(5)** CORRECTIONS 24–25 evaluated, **no propagation required** — roadmap-internal presentation. **(6)** Out-of-scope drift logged, not fixed: 5-stage "Stage 4" residue in two MCP tables; Python 3.11 vs 3.12; CORRECTION 16 absent from two files; stale `attention-flow-catalyst` README. **(7)** Snapshot 1–25 → 1–26. **Cost: $0.** Additive-only apart from the PolicyPulse timing correction; archive untouched; **no structural teardown; version stays v10.0.**

---

## 8. Decisions Needed Before I Edit

| # | Decision | My recommendation |
|---|---|---|
| 1 | Approve Blocks A, B, C, D? | Approve all four. **C is the highest-value block** — it closes a safety ambiguity, not just a documentation gap. |
| 2 | SignalCore — edit or exclude? | **Exclude.** Interface contract, no language surface. |
| 3 | Bump scope-doc version numbers? | **No** — additive-only. `Last Updated` line only. |
| 4 | Draft CORRECTION 26 for the roadmap too? | **Yes** — otherwise the roadmap won't record that propagation happened. |
| 5 | Out-of-scope drift (Stage 4 residue, Python 3.11, C16 gap) | **Separate correction.** Say the word and I'll scope it. |
| 6 | Stale `attention-flow-catalyst` README | ⭐ **Prioritise above this pass.** It is public, recruiter-reachable, and describes a beginner capstone rather than the AFC in your scope docs. Biggest gap between what you have built and what a reader sees. |

---

**On approval I will produce all 13 edited scope documents to `/mnt/user-data/outputs/`** — full files, verified for heading integrity, anchor resolution and byte delta, with a per-file change manifest. You apply and commit. No auto-push, as always.

## Sources

- Vercel — *AI SDK 7* (release post, June 25, 2026): https://vercel.com/blog/ai-sdk-7
- Developers Digest — *Vercel AI SDK 7: The Production Agent Upgrade* (June 28, 2026): https://www.developersdigest.tech/blog/vercel-ai-sdk-7-production-agents
- Vercel Academy — *Builders Guide to the AI SDK*: https://vercel.com/academy/ai-sdk
- Coursera/Scrimba — *Build a Support Agent with Vercel AI SDK* (status re-verified: Free Trial)
- GitHub — `manuel-reyes-ml` public repos (repo/README state verified August 10, 2026)
