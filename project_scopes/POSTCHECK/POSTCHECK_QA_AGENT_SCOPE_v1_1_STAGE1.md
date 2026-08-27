# 📋 POSTCHECK — Stage 1 Project Scope v1.3

## Autonomous Post-Posting QA Review Agent — Foundation Build
## Watched Folder → Deterministic Parse + Packet Vision → 15-Item Adjudication → IGO/NIGO → Report or Escalation

**Document Version:** 1.3 (⚠️ **CORRECTED against a validated matched pair** — see the Full-Production scope §0 for the seven-item correction log. Companion to `POSTCHECK_QA_AGENT_SCOPE_v1_0_FULL_PRODUCTION.md` **v1.2**, which governs on any conflict. Roadmap authority: v10.0 **CORRECTION 33**.)
**Last Updated:** August 14, 2026
**Status:** 📋 DRAFT — awaiting approval.
**Author:** Manuel Reyes
**Stage:** S1 (Foundation — GenAI-first core), first layer of a 3-stage arc
**Strategic Priority:** 🏁 DUAL-TARGET FLAGSHIP — Applied AI **and** Analytics Engineer / DE

---

## 🎯 v10.0 ROADMAP ALIGNMENT — AUTHORITATIVE

> **This block governs.** Where anything below conflicts, this block wins. The Full-Production scope's alignment block governs above this one.

**Portfolio role:** 🏁 **Flagship — dual-target lead.** **⚠️ Distinct from FormSense**: FormSense is pre-index intake quality (*is this form complete enough to file?*); PostCheck is post-posting keying verification (*does the posted transaction match the request and the SOP?*). Different position in the workflow, different source of truth, different subject, different decision, different escalation target.

**Production standard (non-negotiable):** business-outcome headline · Mermaid diagram · **C4 Context + Container** · **`docs/adr/`** · Dockerfile · eval-metrics table · 15–30s demo GIF · "What I Learned" · **synthetic data only** · `pyproject.toml` + `uv.lock` + `src/` + `py.typed` + ruff + mypy · `structlog` + `redact_pii` · `pydantic-settings` + `SecretStr` · `stamina` · Conventional Commits · `.pre-commit-config.yaml` **Tier A + Tier B (`nbstripout`)** · **Python 3.14+, standard GIL build only**.

---

## 🔒 SAFETY INVARIANT — BINDING FROM COMMIT ONE

> PostCheck is **read-only and advisory**. It never keys, posts, reverses, or corrects. It never contacts a participant, advisor, TPA, or plan sponsor. It never edits SSN, legal name, or date of birth. **An IGO verdict is a recommendation to a human, never an authorization.**
>
> This is tested (`tests/test_safety_invariant.py`), not merely documented. Any violation is a Sev-1.

---

## 📋 Table of Contents

1. [Executive Summary](#1-executive-summary)
2. [What Ships in Stage 1](#2-what-ships-in-stage-1)
3. [Data Contracts & Schemas](#3-data-contracts--schemas)
4. [Phase 1 — Intake & Deterministic Parse (Weeks 1–2)](#4-phase-1--intake--deterministic-parse-weeks-12)
5. [Phase 2 — Packet Vision & Pairing (Weeks 3–4)](#5-phase-2--packet-vision--pairing-weeks-34)
6. [Phase 3 — Rule Engine & Adjudication (Weeks 5–6)](#6-phase-3--rule-engine--adjudication-weeks-56)
7. [Phase 4 — Reporting, Events & Eval Gate (Weeks 7–8)](#7-phase-4--reporting-events--eval-gate-weeks-78)
8. [The Synthetic Corpus Generator](#8-the-synthetic-corpus-generator)
9. [Evaluation Strategy](#9-evaluation-strategy)
10. [Guardrail Tests](#10-guardrail-tests)
11. [Tech Stack](#11-tech-stack)
12. [Project Structure](#12-project-structure)
13. [Success Metrics](#13-success-metrics)
14. [Risk Mitigation](#14-risk-mitigation)
15. [Timeline](#15-timeline)
16. [Approval Checklist](#16-approval-checklist)

---

## 1. Executive Summary

Stage 1 ships a **complete, runnable, evaluated system** — not a prototype. It watches a folder, claims each packet exactly once, parses the posted-transaction export deterministically, extracts the request form with a multimodal model, adjudicates the pair against a versioned rule pack across a fixed 15-item surface, and emits either a review report (IGO) or an escalation with the specific field, form value, export value, SOP citation, and fix (NIGO). Every adjudication is appended to an event log.

**Stage 1 is independently evidence-bearing.** It produces a defensible headline metric on its own, with no S2 or S3 dependency:

> **The deterministic block parser measurably reduces false-CRITICAL findings versus a flattened-text baseline running the identical rule pack on the identical golden set.**

That is a real before/after against a real incumbent approach, on labeled synthetic data with ground truth known by construction. Very few portfolio projects can produce one.

---

## 2. What Ships in Stage 1

| ✅ In scope | ❌ Deferred |
|---|---|
| Watched-folder intake with exactly-once claiming | Airflow orchestration (S2) |
| Deterministic block parser + schema-drift contract | dbt marts, semantic layer (S2) |
| Packet segmentation + multimodal form extraction | ECS/Fargate, Terraform (S2) |
| Retry → unreadable discipline | Postgres (SQLite in S1) |
| Pairing on participant + plan ID | SOP retrieval / clause citation (S3) |
| Live/dead payee-leg resolution | Evaluator-optimizer loop (S3) |
| Layer-1 rule engine (all families) | Arize Phoenix tracing (S3) |
| Bounded Layer-2 LLM judgment on the residual | Shadow evaluation (S3) |
| 15-item adjudication + 4 statuses + 3 verdicts | Feedback inheritance (S3) |
| Review report `.docx` + intake table + roll-up | Approval-gated MCP draft tools (S3) |
| Append-only event log (JSONL → SQLite) | BI surface (S2) |
| **Synthetic corpus generator with controlled NIGO injection** | |
| **Frozen golden set (200) + blocking false-NIGO gate** | |
| FastMCP read tools (3) | |
| Docker Compose, CI, ADRs, C4 | |

---

## 3. Data Contracts & Schemas

### 3.1 The export — block dispatch

```python
# src/postcheck/parsing/ledger_export.py
from decimal import Decimal
from enum import StrEnum

class RowType(StrEnum):
    """The first column is a row-type discriminator, not data."""
    BLOCK1_TITLE  = "1.1"
    BLOCK1_HEADER = "1.2"
    BLOCK1_DATA   = "1.3"   # grain: one row per TRANSACTION
    SPACER        = "2"
    BLOCK2_TITLE  = "2.1"
    BLOCK2_HEADER = "2.2"
    BLOCK2_DATA   = "2.3"   # grain: one row per PAYEE LEG

def normalise_discriminator(cell: object) -> str:
    """⚠️ v1.3 CORRECTION C-1 — the discriminator is NOT reliably a string.

    Observed in validated exports: float (1.1, 2.1), int (2), and str.
    Float comparison is unsafe and `1.1` would collide with `1.10`.
    Canonicalise via Decimal(str(...)) and normalize() before dispatch.
    """
    if cell is None:
        raise MalformedExportError("null row discriminator")
    if isinstance(cell, str):
        cell = cell.strip()
    return str(Decimal(str(cell)).normalize())
```

> ⚠️ **This function is the parser's single point of failure and carries its own test class.** A naive `str(cell) == "1.3"` passes on one export and silently drops every data row on the next.

Parse contract:
1. `openpyxl` read-only. **Never `pandas.read_excel` on the raw sheet** — it will treat the first header row as the frame's columns and destroy both blocks.
2. Dispatch every row on its `SORTORDER` prefix.
3. **Bind each block's data rows positionally to that block's own header row.** Never to physical column names (`COLUMN2`…`COLUMN31` carry no meaning).
4. Validate the header label sequence against the expected sequence. **A mismatch fails the run and escalates.**
5. 🆕 **Coerce every field at the boundary via the Pydantic schema (C-2).** Value types drift *within a block*: in the validated pair, payee leg 1 carried native `int`/`str` while its sibling leg carried all-`str` — same columns, same file. Two legs differing only in cell type must produce identical models.
6. Emit `TransactionLedger` (1) + `PayeeLeg[]` (n).

> ⚠️ **Source-system header typos are preserved verbatim in `column_map.py`** and normalised only at that boundary. Several upstream labels are misspelled. Correcting them in the map would break silently on the next export; nothing downstream ever sees a misspelled name.

### 3.2 Pydantic models

```python
class TransactionLedger(BaseModel):
    plan_id: str
    transaction_id: str
    post_date: datetime
    participant_name: str
    ssn: SecretStr                      # never logged, never rendered in full
    participant_age: int
    distribution_type: str
    distribution_template: str
    total_transaction_amt: Decimal      # Decimal — never float on money
    distribution_amt: Decimal
    fee_amt: Decimal | None
    activity_fee_amt: Decimal | None
    activity_fee_method_code: str | None
    non_roth_source_amt: Decimal | None
    roth_source_amt: Decimal | None

class PayeeLeg(BaseModel):
    plan_id: str
    transaction_id: str
    payee_seq: int
    payment_method: str | None
    payee_name: str | None
    payee_company: str | None
    address: Address | None
    check_reference_line: str | None    # CRITICAL surface for rollover-out legs
    bank_account_name: str | None
    aba_routing: str | None             # must be exactly 9 digits when present
    account_number: SecretStr | None
    payment_fee_amt: Decimal | None
    source_type: SourceType             # Traditional | Roth — never swapped
    fed_wh_method: WithholdingMethod | None
    fed_wh_amt: Decimal | None
    fed_tax_amt: Decimal | None
    manual_fed_tax_amt: Decimal | None
    distr_code_1: str | None            # None/blank/0/- ⇒ DEAD LEG
    distr_code_1_desc: str | None
    distr_code_2: str | None
    distr_code_2_desc: str | None
    state_wh_amt: Decimal | None
    state_tax_amt: Decimal | None
    state_wh_method: WithholdingMethod | None
    manual_state_tax_amt: Decimal | None

    @property
    def is_live(self) -> bool:
        """A leg is live iff it carries a real distribution code."""
        return self.distr_code_1 not in (None, "", "0", "-", "None")
```

> 💰 **`Decimal` everywhere on money, never `float`.** The gross-up reconciles at ±$0.01. Float drift would manufacture CRITICAL findings — the exact failure this system exists to avoid.

### 3.3 The form

```python
class SourceElection(StrEnum):
    PRE_TAX = "pre_tax"      # → non-Roth legs are live
    ROTH    = "roth"         # → Roth legs are live
    BOTH    = "both"

class FormFamily(StrEnum):
    """🆕 v1.3 CORRECTION C-3 — routes to a DIFFERENT rule path. Classified FIRST."""
    OUTGOING_MOVEMENT = "outgoing_movement"  # Exchange/Transfer/Rollover Request
    ACCOUNT_DISTRIB   = "account_distrib"    # Account Distribution Form
    UNKNOWN           = "unknown"            # ⇒ FLAG, never guess

class MovementType(StrEnum):
    """Family A only. GOVERNS THE CODE — not plan type, not template."""
    EXCHANGE = "exchange"    # vendor change within current employer's plan → code 6
    TRANSFER = "transfer"    # to another employer's plan            → code 6
    ROLLOVER = "rollover"    # removal from the plan, needs a qualifying event

class AmountBasis(StrEnum):
    """🆕 C-4 — the form elects this explicitly. GROSS ⇒ gross-up item is N/A."""
    GROSS = "gross"
    NET   = "net"

class RequestForm(BaseModel):
    form_family: FormFamily              # 🆕 C-3 — classified before any rule runs
    amount_basis: AmountBasis | None     # 🆕 C-4 — GROSS ⇒ item 11 is N/A, not PASS
    distribution_reason: str | None      # Family B path (→ SOP code tables by age)
    participant_name: str
    ssn_visible_digits: str              # visible digits only — never the full number
    date_of_birth: date | None
    address: Address
    from_account_type: SourceElection    # THE source determinant
    plan_type: PlanType
    movement_type: MovementType | None   # THE code determinant
    receiving_institution: str | None
    receiving_account_number: str | None
    fbo_line: str | None
    amount_election: AmountElection      # full | partial(Decimal)
    designated_funds: list[str]          # empty ⇒ pro-rata
    reason: str | None
    signature: SignatureEvidence         # wet | docusign | absent (+ date)
    plan_admin_block_signed: bool
    approval_certificate_present: bool
    annotations: list[Annotation]        # handwritten marks + page + location
    confidence: dict[str, float]         # PER-FIELD — enables field-level escalation


class ApprovalCertificate(BaseModel):
    """🆕 C-5 — supplies plan-level auth AND two reconciliation surfaces."""
    confirmation_number: str
    transaction_type: str
    approved_amount: Decimal | None      # must reconcile to the posted amount
    approved_cap: Decimal | None         # posting above the cap ⇒ CRITICAL
    effective_date: date
    validity_days: int                   # post date outside window ⇒ CRITICAL
    plan_sponsor: str
    signed: bool

class BankingDocument(BaseModel):
    """🆕 ACH ownership verification. Pre-printed name is the whole point."""
    preprinted_name: str | None          # absent/handwritten ⇒ unverified ⇒ CRITICAL
    routing: str | None                  # cross-check vs export ABA
    account_number: SecretStr | None
    institution: str | None

class SignatureCertificate(BaseModel):
    """🆕 Electronic envelope. Status governs; sub-question failures do NOT."""
    envelope_id: str
    status: str                          # anything but Completed ⇒ CRITICAL
    signer_name: str
    signer_email: str
    signed_at: datetime
    adoption_method: str                 # typed/pasted ⇒ invalid
    identity_check_overall: str          # ⚠️ THIS governs
    identity_check_failed_items: list[str]  # ⚠️ NEVER a finding on its own
```

### 3.4 Adjudication

```python
class ItemStatus(StrEnum):
    PASS = "PASS"; EXCEPTION = "EXCEPTION"; CRITICAL = "CRITICAL"; NA = "N/A"

class Verdict(StrEnum):
    IGO_READY          = "READY TO PROCESS (IGO)"
    NIGO_CLARIFICATION = "NEEDS CLARIFICATION (NIGO)"
    NIGO_DO_NOT        = "DO NOT PROCESS (NIGO)"

VERIFICATION_ITEMS: Final = (   # FIXED ORDER. NEVER REORDERED. NEVER OMITTED.
    "source_and_live_rows", "name_and_plan", "ssn", "reason_and_scope",
    "date_and_event", "fund_and_source", "fee", "type_and_code",       # per live leg
    "federal_withholding", "state_withholding", "gross_up",
    "payment_and_reference_line",                                       # per live leg
    "participant_auth", "plan_auth", "documentation",
)
assert len(VERIFICATION_ITEMS) == 15
```

---

## 4. Phase 1 — Intake & Deterministic Parse (Weeks 1–2)

### Week 1 — Exactly-once intake

**Build:**
- `watchdog` observer (hint) + 60 s poll sweep (floor) feeding one candidate queue
- Stability gate: size + mtime unchanged across N consecutive checks
- Content-hash (SHA-256) idempotency key — **not path**
- SQLite claim store: `discovered → claimed → reviewing → igo | nigo | flagged | failed`, with **lease TTL** so a dead worker's claim expires and retries
- Batch bound 1–10 packets per run
- `structlog` + `redact_pii` from the first commit; `pydantic-settings` as sole config entrypoint

**Why this shape:** the watcher library's own tracker documents multiple modified events during large-file copies, duplicate events for a single modification across platforms, and silence on network shares. The event is a hint; the sweep is the guarantee; the hash is the identity.

**Exit:** kill the worker mid-processing → the packet is re-claimed and completed exactly once, verified by test. Drop the same packet twice under two names → processed once.

**→ ADR-003: Event-as-hint with a poll floor.**

### Week 2 — The block parser

**Build:**
- `RowType` dispatch, positional header binding, `column_map.py` with source typos preserved
- Header-sequence contract; failure escalates, never mis-binds
- `TransactionLedger` + `PayeeLeg[]` emission with `Decimal` money
- **`is_live` resolution as a separately tested module**
- **The flattened-text baseline harness** — renders the sheet as flat text the way a chat assistant would, so the same rule pack can run against both and the delta is measurable

**Exit:** parser round-trips every synthetic export with exact header→value binding; a deliberately reordered header column fails the run loudly; the baseline harness runs and the two paths are comparable.

**→ ADR-001: Deterministic block parsing over LLM table reading.** *Rejected:* feeding the sheet to a vision or text model — which is what the incumbent does, and which is why the incumbent needs a defensive rule about columns shifting left. **That entire hazard is an artifact of flattening, not a property of the file.**

---

## 5. Phase 2 — Packet Vision & Pairing (Weeks 3–4)

### Week 3 — Packet segmentation & extraction

**Build:**
- PDF content inventory; text-layer detection. **Scanned faxes have no usable text layer — go straight to page rasterization.**
- Page classification → document types: transmittal, request form, letter of acceptance, statement, approval certificate, ticket screenshot, blank, rotated
- Contiguous same-type pages → one document; each extracted against its own schema
- Multimodal extraction → `RequestForm` with **per-field confidence**
- Rotation and blank-page handling

**Exit:** every synthetic packet segments correctly; the form extracts with per-field confidence; rotated and blank pages are handled without error.

### Week 4 — Retry discipline & pairing

**Build:**
- **Retry ≠ Unreadable.** Garbled, empty, or partial text is `Retry`, *not* a finding. Finish the other pairs first, then re-open each `Retry` alone in extended mode, scanned pages rasterized page by page. **Only a failed retry is `Unreadable`.**
- Pairing: **key = participant name + plan ID**, corroborated by visible SSN digits. Case, initials, and file order never break a match. **Filenames are generic and never used.** No participant is reported unknown before both files are read.
- Intake table: pair, participant, plan ID, form, export, status ∈ {Paired, Missing export, Missing form, Retry, Unreadable}
- **One flag never stops the rest of the run.**

**Exit:** a corrupted file produces `Retry` then `Unreadable`, and the other pairs in the batch complete normally.

---

## 6. Phase 3 — Rule Engine & Adjudication (Weeks 5–6)

### Week 5 — Layer 1

**Build the rule families**, each with its own module and YAML table where the SOP is tabular:

| Module | Implements |
|---|---|
| `identity.py` | Name, plan/sponsor, **visible SSN digits only** — match is `PASS` and never an exception; conflict is `CRITICAL` |
| `movement_code.py` | **The form's type step governs.** Exchange or Transfer → code 6, not a distribution, 0% withholding, **no gross-up**. Rollover → pre-tax vs designated-Roth code by source. **A Roth source alone does not imply the Roth rollover code — a Roth contract exchange is code 6.** Otherwise SOP code tables by plan type, source, age, reason. Cite code keyed, code required, leg used. |
| `withholding_limits.py` | Mandatory minimum on eligible rollover distributions paid to the participant; defaults on required distributions, hardships, IRAs; **0% and no gross-up** on direct rollovers, exchanges, transfers, charitable distributions; fed + state ≤ 100% |
| `withholding_pattern.py` | **Read the method, not the dollar field — exactly one is ever populated.** Pre-built percentage method ⇒ dollar fields correctly `$0`; **`$0` there is NEVER a finding.** Method `None` + manual amount ⇒ correct; **`None` ≠ declined.** **Findings: both populated, or `None` + `$0` where the form elected withholding.** Same for state. |
| `grossup.py` | 🆕 **(C-4) Read the form's Gross/Net election first — it governs.** Gross elected ⇒ item 11 is **`N/A` with reason**, not `PASS`; there is nothing to reconcile. **Never infer the basis from the presence of a net amount.** Net requests only: `r = fed% + state%`; **`expected_gross = (net + fee) / (1 - r)`**; flat-dollar: `net + manual_fed + state + fee`. Compare at **±$0.01**, **show the arithmetic**, mismatch `CRITICAL`. Total = expected ⇒ applied. Total at/near net ⇒ not applied ⇒ `CRITICAL`. |
| `fee.py` | Fee vs SOP fee table by distribution type and payment method |
| `payment.py` | Method and address match the form; payee complete — institution, account number, FBO line, address — vs the letter of acceptance; routing exactly 9 digits; **check reference line on every exchange/transfer/rollover-out leg = that leg's receiving account number + participant name per its LOA; missing or mismatched is `CRITICAL`** |
| `authorization.py` | Wet signature **or** completed envelope + certificate — **either alone suffices.** IRA: that alone is the only auth. Employer plans: **plus** plan-level auth — approval-block signature **or** separate certificate; **a blank approval block is NOT a finding when a certificate is attached.** Death adds beneficiary auth. |
| 🆕 `form_family.py` | **Runs first (C-3).** Registry keyed on form identifier → selects the code path, gross-up applicability, companion-document requirement, and reference-line applicability. **Unrecognised family ⇒ flag, never guess.** |
| 🆕 `approval_cert.py` | **(C-5)** Certificate amount reconciles to the posted amount and to any stated cap; **post date must fall inside the validity window**. Expired approval or posting above cap ⇒ `CRITICAL`. |
| 🆕 `template_consistency.py` | **(C-6)** The template string encodes reason + gross/net + payment method. Cross-check all three against the form. `Net` template on a `Gross` request, or `Check` template on an ACH election ⇒ `EXCEPTION`. Free, deterministic, zero LLM. |
| 🆕 `age.py` | Derive age from DOB as of the post date; cross-check the export's age field. Disagreement ⇒ `EXCEPTION`; disagreement crossing a code band ⇒ `CRITICAL`. |
| 🆕 `address.py` | Three-way reconcile: form / banking document / export. ⚠️ **Conditional on payment method** — for ACH the routing+account are the destination, so a discrepancy is `EXCEPTION` and never blocks; for check payments the same discrepancy blocks. |
| 🆕 `signature_cert.py` | Envelope status must be `Completed`; signer name+email match the participant; applied signature with audit trail; signed date not before the permitting event. **A failed identity sub-question is NEVER a finding when the overall result passes.** |
| `anti_false_positive.py` | 🚫 **A medallion is NEVER required.** Pre-printed threshold language on the form creates no requirement. **Expired or under-covered stamps are never findings when a valid signature is present.** 🆕 A blank signature-guarantee section below the threshold is `N/A`, not a gap. 🆕 A failed identity sub-question under an overall pass is never a finding. |

**Target: ≥ 65% of all findings originate in Layer 1.** Tracked as a metric with an alarm.

**→ ADR-002: Deterministic rules first; LLM confined to the residual.** *Rejected:* LLM-driven rule validation (the direction the 2026 reference architecture takes), on the grounds that non-determinism in an arithmetic check is a defect, not a feature.

### Week 6 — Layer 2, the re-read rule, and adjudication

**Build:**
- Layer 2, strictly bounded to what code cannot express: is this mark a signature; does a handwritten annotation contradict a keyed value; is the reason consistent with the transaction and the event date; was the document altered
- **The re-read rule — the single most important false-positive control:**
  > Before emitting any `CRITICAL`, re-read the cell and its bound header. **A finding raised on a blank or mis-bound cell is not a finding.** If the export contradicts the form in a way suggesting a bad read, **call that field unreadable and request a re-export — never report a processor error.**
- Adjudicator: 15 items in fixed order, items 8 and 12 repeated per live leg, four statuses, three verdicts. **Any `CRITICAL` ⇒ NIGO.**
- **Per-field escalation**, not whole-packet rejection — *"item 9 needs a human"*, not *"this packet is wrong"*

**Exit:** full adjudication on the synthetic corpus; no item ever omitted; dead-leg cases produce zero findings.

---

## 7. Phase 4 — Reporting, Events & Eval Gate (Weeks 7–8)

### Week 7 — Artifacts & events

**Build:**
- **A. Intake table** first, always
- **B. Per-transaction block** — verification summary table (15 rows, no prose above it) → details for failed items only, criticals first (item, field, form value, export value, SOP section, fix; all clean ⇒ `No failed items.`) → one-line recommendation
- **C. Roll-up** for multi-pair runs + counts
- Per-pair `.docx`: `Distribution_QA_Review_[PARTICIPANT]_[YYYY-MM-DD].docx`, header carrying run date, SOP version, source files, **and 🆕 agent version, rule-pack version, prompt hash** — without these a report is unreproducible and therefore not audit evidence
- Flagged pairs: one roll-up line, no report
- Append-only event log → JSONL + SQLite, emitting `stg_review_events` and `stg_verification_items` shapes **so S2's dbt layer is a lift, not a redesign**
- **A rendered `.xlsx` export from the event log — regenerated, never hand-edited** (the S2 mart ruling, honoured from day one)
- FastMCP: `list_pending_packets`, `get_review_report`, `get_verification_detail`

### Week 8 — The gate

**Build:**
- Freeze the 200-packet golden set, stratified by NIGO reason
- Field-level P/R/F1 via `extract-eval` with omission / hallucination / mismatch classification
- **CI eval gate — blocking:** false-NIGO rate · false-CRITICAL rate · **false-CRITICAL vs the flattened baseline** · report-format conformance
- 🆕 **Substrate matrix (ADR-009):** the same eval suite run across **Anthropic · Azure OpenAI (Foundry) · Ollama**. Agreement rate, false-NIGO delta, latency and cost-per-packet per substrate ship in the README **Cost** section. **Gates run per substrate — parity is required, not assumed.**
- Docker Compose; ADRs; Structurizr DSL → Mermaid + C4 L1/L2; README to the ①②③ order; demo GIF

**Exit:** `docker compose up` runs the full pipeline on the synthetic corpus from a clean clone; the gate is green and blocks on regression.

---

## 8. The Synthetic Corpus Generator

> **This is an artifact in its own right, not scaffolding — and under the ERISA constraint it is the *only* legal source of evaluation data.**

### 8.1 Why synthetic is better here, not merely permitted

Real packets carry no ground-truth labels. A generator with **controlled NIGO injection** produces a labeled set where the correct verdict is known **by construction** — which is what makes a precision gate meaningful at all. Same controlled-perturbation approach AFC already uses.

⚠️ **A set built only from clean synthesis is "too clean."** Two counters: the perturbation catalog below deliberately manufactures the *hard* cases, and golden-set bucket four grows monotonically from real production failures and processor corrections once S3 feedback exists.

### 8.2 The perturbation catalog

| Class | Injected defect | Expected verdict |
|---|---|---|
| **Code** | Wrong code on a live leg; rollover code applied to a Roth *exchange*; distribution code applied where the form marks Exchange | `CRITICAL` |
| **Gross-up** | Total at/near net on a net request; total off by more than $0.01 | `CRITICAL` |
| **Withholding** | Below mandatory minimum; both method and manual amount populated; method `None` + `$0` where the form elected withholding; fed + state > 100% | `CRITICAL` / `EXCEPTION` |
| **Payment** | Missing or mismatched check reference line; routing ≠ 9 digits; account held in a non-participant name; incomplete FBO line | `CRITICAL` |
| **Authorization** | Missing signature; envelope status other than Completed; employer plan with neither approval-block signature nor certificate | `CRITICAL` |
| **Fee** | Fee not matching the SOP table for the type and method | `EXCEPTION` |
| 🎯 **Anti-false-positive** | **Dead leg carrying stale defaults** (payment method, address, withholding method) · pre-built percentage method with `$0` in dollar fields · method `None` with a valid manual amount · **absent medallion** · expired or under-covered medallion stamp beside a valid signature · **blank approval block with a certificate attached** · a dead leg paying the participant while a live leg pays an institution · different codes on two live legs of one transaction | **`PASS` — every one of these must produce NO finding** |
| **Readability** | Garbled page; rotated page; blank page; partial scan | `Retry` → `Unreadable` |
| **Intake** | Form with no export; export with no form; duplicate packet under a different filename | Flag / dedupe |

> 🎯 **The anti-false-positive row is the most valuable in the table.** It is where the §12.2 gate is actually earned, and it is stratified into the golden set at no less than **30%** of cases. A system that catches every error and cries wolf on clean packets has failed.

### 8.3 Constraints

Faker-generated identities · fictitious plan sponsors and institutions · **no real employer, recordkeeper, sponsor, or vendor names** (aliased) · scans synthesised at fax-like resolution with realistic degradation, rotation, and handwritten-style annotation overlays · every generated packet carries its ground-truth label file.

---

## 9. Evaluation Strategy

### 9.1 The gate — and it is not accuracy

> **The expensive failure is a false NIGO, not a missed NIGO.**

2026 benchmark evidence puts structured-extraction precision at roughly **20–32% zero-shot** against **94–98% recall**, with the best few-shot precision reaching ~36%. The model will flag nearly every real error *and* a great many non-errors. **A processor who gets five bad escalations per good one stops using the tool — that failure is terminal.** A missed NIGO is caught downstream by the human who was reviewing anyway.

| Gate | Blocks merge? |
|---|---|
| **False-NIGO rate** (precision on the NIGO class) | ✅ |
| **False-CRITICAL rate** on the 15-item surface | ✅ |
| **False-CRITICAL vs flattened baseline** — must be lower | ✅ **the headline** |
| **Report-format conformance** (15 rows, fixed order, none omitted) | ✅ |
| 🆕 **Per-substrate gate parity** — gates run on **each** named substrate, not only the primary (ADR-009) | ✅ |
| NIGO recall | Reported; trend non-decreasing |
| Field-level F1 per field | Reported |
| Layer-1 share of findings | Reported; alarms below 65% |

### 9.2 Golden set

**200 packets, frozen, versioned, stratified by NIGO reason, ≥30% anti-false-positive cases.** ~50 would only catch large regressions; 200 gives confidence on a 3–5% change; beyond 500 is diminishing returns. Cache evaluator decisions by `(input hash, output hash, prompt version)`; a stratified subset runs at PR time, the full set nightly.

### 9.3 Tooling

`extract-eval` (schema-driven field-level P/R/F1 with omission / hallucination / mismatch, eval config living in the schema so there is no drift) · `DeepEval` + `GEval` for the Layer-2 surface, calibrated against human verdicts · `ExtractBench` infrastructure if nested extraction is needed. **Adopted, not hand-rolled.**

---

## 10. Guardrail Tests

```python
# tests/test_safety_invariant.py  — Sev-1 if any fails
def test_no_write_path_to_system_of_record(): ...
def test_no_outbound_contact_capability(): ...
def test_identity_fields_are_read_only(): ...          # SSN, name, DOB
def test_igo_verdict_is_advisory_not_authorization(): ...

# tests/test_liveness.py — the dead-leg trap
def test_dead_leg_stale_defaults_produce_no_finding(): ...
def test_dead_leg_paying_participant_while_live_leg_pays_carrier_is_clean(): ...
def test_two_live_legs_with_different_codes_is_normal(): ...
def test_source_comes_from_form_never_from_amount_columns(): ...

# tests/test_form_family.py — C-3, runs before everything
def test_family_a_routes_to_movement_code_path(): ...
def test_family_b_routes_to_reason_and_age_code_path(): ...
def test_unrecognised_family_flags_and_never_guesses(): ...
def test_reference_line_is_na_for_family_b(): ...

# tests/test_approval_cert.py — C-5
def test_post_date_outside_validity_window_is_critical(): ...
def test_posted_amount_above_approved_cap_is_critical(): ...

# tests/test_address.py — the conditional that defines the project
def test_address_mismatch_on_ach_is_exception_not_critical(): ...
def test_address_mismatch_on_check_payment_blocks(): ...

# tests/test_anti_false_positive.py — the trust surface
def test_absent_medallion_is_never_a_finding(): ...
def test_expired_medallion_beside_valid_signature_is_not_a_finding(): ...
def test_preprinted_threshold_language_creates_no_requirement(): ...
def test_blank_approval_block_with_certificate_is_not_a_finding(): ...
def test_zero_dollar_under_prebuilt_percentage_method_is_not_a_finding(): ...
def test_method_none_with_manual_amount_is_not_declined(): ...
def test_ssn_visible_digit_match_is_pass_never_exception(): ...
def test_failed_identity_subquestion_under_overall_pass_is_not_a_finding(): ...
def test_blank_signature_guarantee_below_threshold_is_na(): ...
def test_gross_election_makes_grossup_item_na_not_pass(): ...

# tests/test_reread_rule.py
def test_critical_on_null_field_is_suppressed_and_reread(): ...
def test_export_form_contradiction_requests_reexport_not_processor_error(): ...

# tests/test_intake.py
def test_exactly_once_under_kill_during_processing(): ...
def test_duplicate_content_under_different_filename_processed_once(): ...
def test_partial_write_never_read(): ...
def test_expired_lease_is_reclaimed(): ...

# tests/test_provider.py — ADR-009
def test_no_provider_sdk_imported_outside_ai_module(): ...
def test_all_three_substrates_satisfy_same_interface(): ...
def test_non_synthetic_run_is_pinned_to_local_substrate(): ...

# tests/test_parsing.py
def test_reordered_header_column_fails_run_loudly(): ...
def test_block_binding_is_positional_not_by_physical_column_name(): ...
def test_money_uses_decimal_never_float(): ...

# tests/test_format.py
def test_fifteen_items_always_present_in_fixed_order(): ...
def test_no_prose_above_verification_table(): ...
def test_flagged_pair_produces_no_report(): ...
def test_report_header_carries_agent_rulepack_and_prompt_version(): ...
```

---

## 11. Tech Stack

| Layer | S1 choice |
|---|---|
| Language | **Python 3.14+**, standard GIL build (`requires-python` single source; ruff `py314`, mypy `3.14`, Dockerfile, CI matrix all read from it — mismatch is a CI failure) |
| Packaging | `uv` + committed `uv.lock`, `pyproject.toml`, `src/`, `py.typed` |
| Intake | `watchdog` + poll sweep; SQLite claim store with lease TTL |
| Excel | `openpyxl` read-only + `SORTORDER` dispatch |
| PDF | `pypdf` inventory; page rasterization for scans |
| Vision / LLM | **Provider-agnostic interface** (`ai/provider.py`, sole boundary — no provider SDK imported elsewhere). 🆕 **Three named substrates (ADR-009):** **Anthropic SDK** (primary) · **Azure OpenAI via Microsoft Foundry** (OpenAI-compatible client — a config change, not a rewrite) · **Ollama** (local, **mandatory for any non-synthetic run**). Gemini and OpenAI-direct are interface-supported but **not benchmarked**. |
| Schemas / config | `pydantic`, `pydantic-settings`, `SecretStr` |
| Dataframes | ⬆️ **S2 — Polars** (default engine) for event-log frames, `render_xlsx` inputs, eval-result tables and the dbt-facing Parquet layer. ⚠️ **Does not touch the parse path**: `openpyxl` + `SORTORDER` dispatch stays deterministic and row-wise, and `pandas.read_excel` on the raw sheet remains banned (§3.1). Polars begins *after* the export is parsed. Reviewable artifact = **ADR-0010** + a `.explain()` plan (CORRECTION 35) |
| Money | `decimal.Decimal` — never `float`. ⚠️ **Engine caveat:** `Decimal` survives the Polars boundary only via an explicit `Decimal128` dtype or by keeping money as string→`Decimal` at the edge — money is **never** materialized as `Float64` for convenience |
| Retries | `stamina` |
| Logging | `structlog` + `ProcessorFormatter` + `redact_pii` |
| Report | `python-docx` |
| MCP | **FastMCP** |
| Eval | `extract-eval`, `DeepEval`, `GEval` |
| Quality | `ruff`, `mypy` (**CI-only, ADR-007**), `pytest`, `pre-commit` Tier A + B |
| Container | Docker + Docker Compose |
| CI | GitHub Actions — ruff → mypy → pytest → **eval gate** → contract check → build |

---

## 12. Project Structure

```
postcheck/
├── pyproject.toml · uv.lock · .pre-commit-config.yaml
├── Dockerfile · docker-compose.yml · .github/workflows/ci.yml
├── AGENTS.md                       # portable behavioural contract — one source for both harnesses
├── .opencode/ · opencode.jsonc · .claude/ · .cursor/rules/   # dual harness: OpenCode + Claude Code
├── hooks/guard.py                  # PreToolUse — blocks git commit/push + sensitive paths
├── .env.example                    # pydantic-settings surface, no real values
├── README.md                       # ① Production ② Cost ③ Architecture
├── docs/
│   ├── adr/
│   │   ├── 0001-deterministic-block-parsing.md
│   │   ├── 0002-rules-first-llm-residual.md
│   │   ├── 0003-event-as-hint-poll-floor.md
│   │   ├── 0004-false-nigo-rate-as-blocking-gate.md
│   │   ├── 0005-synthetic-corpus-only.md
│   │   ├── 0006-read-only-advisory-boundary.md
│   │   ├── 0007-mypy-ci-only.md
│   │   ├── 0008-no-typescript-layer.md
│   │   ├── 0009-provider-strategy-three-substrates.md
│   │   └── 0010-dataframe-engine-boundary.md      # 🆕 Polars default / pandas boundaries / parse path excluded
│   ├── architecture.dsl · rule_catalog.md · nigo_taxonomy.md · data_dictionary.md
├── src/postcheck/
│   ├── py.typed
│   ├── config.py
│   ├── intake/       watcher.py · poller.py · stability.py · claims.py · leases.py
│   ├── parsing/      ledger_export.py · column_map.py · contracts.py · flat_baseline.py
│   ├── packet/       inventory.py · segment.py · classify.py · rasterize.py · retry.py
│   ├── extraction/   form.py · loa.py · certificate.py · schemas.py
│   ├── pairing/      key.py · intake_table.py
│   ├── liveness/     resolve.py
│   ├── rules/        identity.py · movement_code.py · withholding_limits.py
│   │                 withholding_pattern.py · grossup.py · fee.py · payment.py
│   │                 authorization.py · anti_false_positive.py
│   │                 tables/ fee.yaml · codes.yaml · wh_limits.yaml
│   ├── judgment/     signature.py · annotation.py · consistency.py   # Layer 2
│   ├── adjudication/ items.py · adjudicator.py · reread.py · verdict.py
│   ├── reporting/    intake_table.py · block.py · rollup.py · docx_render.py
│   ├── events/       emitter.py · schema.py · render_xlsx.py
│   ├── mcp_server/   server.py
│   ├── observability/ logging.py · redact.py
│   └── ai/           provider.py · substrates/ anthropic.py · azure_openai.py · ollama.py
├── evals/
│   ├── generator/    packets.py · perturbations.py · scans.py
│   ├── golden/       v1/ (200 packets + labels, frozen)
│   └── suites/       field_f1.py · false_nigo_gate.py · baseline_delta.py · format.py
├── docs/demo/        postcheck_run.gif          # 15–30s: watched folder → verdict → escalation
├── notebooks/        01_export_structure.ipynb · 02_rule_derivation.ipynb
│                     03_eval_analysis.ipynb            # nbstripout-enforced
└── tests/
```

---

## 13. Success Metrics

| Metric | S1 exit target |
|---|---|
| **False-CRITICAL vs flattened baseline** | **Measurably lower** — the headline claim |
| False-NIGO rate | Measured; gate set; CI-blocking |
| NIGO recall | Measured and reported |
| Layer-1 share of findings | ≥ 65% |
| Report-format conformance | 100%, CI-enforced |
| Exactly-once intake | Verified under kill-during-processing |
| Schema-drift detection | Reordered column fails the run loudly |
| Anti-false-positive suite | **100% pass — non-negotiable** |
| Safety-invariant suite | **100% pass — Sev-1 otherwise** |
| Clean-clone reproducibility | `docker compose up` runs end-to-end on the synthetic corpus |

---

## 14. Risk Mitigation

| Risk | Mitigation |
|---|---|
| False escalations destroy processor trust | The gate is precision, not accuracy; mandatory re-read before any `CRITICAL`; anti-false-positive suite must be 100%; per-field escalation |
| Confidentiality breach | Synthetic-only; three-layer PII defence; `nbstripout` + `gitleaks`; `SecretStr`; no employer, sponsor, or vendor names |
| Scan quality defeats extraction | Retry-then-unreadable is a rule, not a heuristic; page-level rasterization; a flag never stops the batch |
| Dead-leg findings | Liveness is a separate module with dedicated regression cases designed to fail loudly |
| Float drift manufactures findings | `Decimal` everywhere on money, enforced by test |
| Rule drift vs SOP | Rules are versioned YAML + Python; every finding records rule-pack and SOP version |
| Scope creep into a write path | Safety invariant tested from commit one |
| 8-week plan vs 25 hrs/week | Phases are independently shippable; the gate can be set on a smaller golden set and raised as the set grows |

---

## 15. Timeline

| Weeks | Phase | Deliverable |
|---|---|---|
| 1–2 | Intake & parse | Exactly-once claiming; block parser; **flattened baseline harness**; ADR-001, ADR-003 |
| 3–4 | Packet vision | Segmentation; multimodal extraction with per-field confidence; retry discipline; pairing; intake table |
| 5–6 | Rules & adjudication | All nine rule modules + tables; Layer 2; **the re-read rule**; 15-item adjudicator; ADR-002 |
| 7–8 | Artifacts & gate | Reports; event log; rendered export; FastMCP; golden set; **blocking CI gate**; Docker; C4 + README + GIF |

**Milestones:** M1 end of W2 — a packet is claimed exactly once and parsed with exact binding. M2 end of W4 — a scanned packet is segmented, extracted, and paired. M3 end of W6 — a full 15-item adjudication with a verdict. **M4 end of W8 — the gate is green, the baseline is beaten, and the repo runs from a clean clone.**

---

## 16. Approval Checklist

- [ ] **Distinct from FormSense** confirmed (pre-index vs post-posting)
- [ ] **Flagship — dual-target lead** approved; roadmap correction to follow separately
- [ ] **Safety invariant** approved as binding and tested from commit one
- [ ] `SORTORDER` block-dispatch parse ruling approved (§3.1, ADR-001)
- [ ] Flattened-baseline comparison approved as the **headline metric**
- [ ] Rules-first inversion approved, ≥ 65% Layer-1 target (ADR-002)
- [ ] Intake design approved (event-as-hint + poll floor + content-hash + leases, ADR-003)
- [ ] **False-NIGO rate as the blocking gate** approved; recall reported not blocking (ADR-004)
- [ ] Anti-false-positive suite at ≥ 30% of the golden set approved
- [ ] Synthetic-corpus-only approved; name scrub confirmed (ADR-005)
- [ ] `Decimal`-only money rule approved
- [ ] Report header carrying agent / rule-pack / SOP / prompt version approved
- [x] Project name **PostCheck** approved
- [x] **ADR-009 provider strategy approved** — three named substrates; Copilot Studio and M365 Agents SDK declined with recorded falsifiers
- [ ] ⚖️ **Open ruling:** whether tenant-isolated Azure OpenAI is a third privacy category. **The binary rule (proprietary → local) stands until explicitly amended.**
- [ ] 🆕 **Dataframe engine boundary approved (ADR-0010)** — Polars default from S2, parse path explicitly excluded, `Decimal` never widened to float
- [ ] 🆕 **Courses & Certifications section approved** (§17) — this scope was the only one of the fifteen without one
- [ ] 8-week phasing realistic against the 25 hrs/week baseline

---

## 📚 Courses & Certifications — take-order table (v10.0 reference)

*Synced to roadmap **v10.0** (through CORRECTION 43). **The table is ordered: take them top to bottom.** Numbering is continuous across all three stages — #1 is the next thing to start, not the first item of an unordered list. Names match the roadmap's stage tables. 🎖️ = committed certification; ⏸️ = conditional, taken only on a named trigger and **never stacked**. **All certifications are self-funded** — the prior employer track ended, and CORRECTION 37 moved AB-620 to conditional: **eight committed ≈ $1,029**, ≈ **$1,594** if every conditional is taken. The shipped production-grade project is the primary hiring signal — certs are tiebreakers.*

### 🎓 Take-order — PostCheck (dual-target flagship)

| # | Course / Certification | Source | Cost | Stage | Why here, in this position |
|---|---|---|---|---|---|
| 1 | uv — Python Packaging & Environments | Astral docs + Sweigart quickstart | Free | S1 | Before the first commit. |
| 2 | Pre-Commit Hooks — Molin four-part series | Blog series | Free | S1 | Hooks before history — the synthetic-only guarantee is mechanical, not remembered. |
| 3 | Introduction to Git and GitHub | Coursera · Google | Free (audit) | S1 | Branch → PR → self-review. |
| 4 | Architecture Documentation: C4 + ADR | c4model.com + AWS Prescriptive Guidance | Free | S1 | **Before ADR-001** — this project carries ten ADRs and they are the reviewable artifact. |
| 5 | Building with the Claude API | Anthropic Academy | Free | S1 | Structured outputs — every adjudication returns a typed verdict object. |
| 6 | IBM Generative AI Engineering PC (16 courses) | Coursera · IBM | Coursera Plus | S1 | The S1 spine; long-running, start early. |
| 7 | Document AI: From OCR to Agentic Doc Extraction | DeepLearning.AI | Free | S1/S3 | **The most on-target S1 course for the packet-vision layer.** |
| 8 | Pre-processing Unstructured Data for LLM Apps | DeepLearning.AI | Free | S1 | Directly load-bearing — the packet is a scanned multi-document bundle. |
| 9 | Improving Accuracy of LLM Applications | DeepLearning.AI | Free | S1 | Eval-from-scratch, before the blocking gate is designed. |
| 10 | MCP: Build Rich-Context AI Apps — primer | DeepLearning.AI / Anthropic Academy | Free | S1 | *Before* the FastMCP build. |
| 11 | Docker for Beginners with Hands-on Labs | KodeKloud | Free | S1 | Containerize; Compose for the local stack. |
| 12 | CS50P — Introduction to Programming with Python | HarvardX | Free | S1 | Testing discipline — the safety invariant is a tested assertion. |
| 13 | MITx 6.00.1x — CS & Programming with Python | edX · MIT | Free (audit) | S1 | CS foundations; background track. |
| 14 | 🎖️ **AI-901** Azure AI Fundamentals | Microsoft · Pearson VUE | **$99** ✅ | S1 | Take once S1 build work is underway. |
| 15 | ⏸️ **AB-620** AI Agent Builder Associate | Microsoft | ~$165 — **CONDITIONAL** | S1–S2 | **Not by default.** ⚠️ ADR-009 was written when this was committed; the provider-agnostic ruling is unaffected — the Azure credential backing Substrate 2 is **AI-103**. |
| 16 | PostgreSQL for Everybody + use-the-index-luke.com | Coursera · U. Michigan + web | Free (audit) | S2 | Opens S2 — the claim store moves SQLite → PostgreSQL here. |
| 17 | dbt Fundamentals | dbt Labs | Free | S2 | **The `transform/` project and the declared grain** — the AE half of this flagship. |
| 18 | dbt Advanced Learning Paths (Analytics Engineering) | dbt Labs | Free | S2 | AE depth on the quality mart. |
| 19 | ⚡ Dataframe Engine Boundary — Polars-first pipelines | Polars User Guide (roadmap S2 row 6.5) | Free | S2 | Before the event-log and eval frames are built. **The course behind ADR-0010.** |
| 20 | Astronomer Academy — Airflow 101 + DAG Authoring | Astronomer | Free | S2 | Scheduling the review runs. |
| 21 | Terraform Fundamentals | HashiCorp Developer | Free | S2 | Infrastructure for the ECS deploy. |
| 22 | 🆕 IBM AI-Native Data Engineering PC | Coursera · IBM (CORRECTION 43) | Coursera Plus | S2 | Two of three land here: ***Unstructured Data Engineering*** (PII-safe corpus prep, OCR-aware processing — the synthetic packet corpus) and ***Reproducible Training Data*** (dataset versioning and release control — the frozen golden set). *The vector course is **not** claimed: §5 rules retrieval is the wrong tool for a keyed-transaction check.* |
| 23 | 🎖️ **DP-700** Fabric Data Engineer | Microsoft | **$165** ✅ | S2 | After the dbt + orchestration work exists. |
| 24 | 🎖️ **AWS DEA-C01** Data Engineer Associate | AWS | **$150** ✅ | S2 | Deploy-target credential. |
| 25 | ⏸️ Lakehouse slot — **take exactly ONE**: DP-750 / SnowPro Core / Databricks DE | Microsoft / Snowflake / Databricks | $165–200 — **CONDITIONAL** | S2 | Deferred — target-employer stack decides. |
| 26 | ⏸️ **dbt Analytics Engineering** | dbt Labs | ~$200 — **CONDITIONAL** | S2 | Only if the apply-list names it. |
| 27 | Evaluating AI Agents | DeepLearning.AI | Free | S3 | **Opens S3 — carries the Layer-2 adjudication loop.** |
| 28 | Automated Testing for LLMOps | DeepLearning.AI | Free | S3 | The four eval layers as merge conditions. |
| 29 | AI Agents in LangGraph | DeepLearning.AI | Free | S3 | HITL pattern behind the escalation boundary. |
| 30 | LangChain Academy (LangGraph + LangSmith) | LangChain | Free | S3 | Tracing the adjudication loop. |
| 31 | Agent Skills with Anthropic | Anthropic Academy | Free | S3 | Reusable adjudication capability. |
| 32 | MCP — Advanced Topics (full) | Anthropic Academy | Free | S3 | Read tools now; approval-gated writes never. |
| 33 | AI-103 Learning Path — Foundry & Azure AI Agents | Microsoft Learn | Free | S3 | Free path backing AI-103 and **Substrate 2**. |
| 34 | M365 Agents SDK — Custom Engine Agents | Microsoft Learn | Free | S3 | **S3 channel layer only.** ADR-009 survives intact — Agent 365 works with agents built on any SDK, so the Anthropic SDK stays primary. |
| 35 | NVIDIA DLI: Building RAG Agents with LLMs | NVIDIA | Free | S3 | RAG agents; breadth. |
| 36 | 🎖️ **Anthropic CCA-F** | Anthropic · Pearson VUE | **~$125** ✅ ⚠️ | S3 | Agentic orchestration source-of-truth. |
| 37 | 🎖️ **AI-103** Azure AI Apps & Agents Developer | Microsoft | **$165** ✅ | S3 | The code-first credential behind Substrate 2. |
| 38 | 🎖️ **NVIDIA NCA-GENL** | NVIDIA | **$125** ✅ | S3 | GenAI credential. |
| 39 | 🎖️ **Databricks GenAI Engineer Associate** | Databricks | **$200** ✅ | S3 | Optional. |

> **🎯 Stage 3 deliverable (CORRECTION 8):** ADR set + C4 + **architecture-defence rehearsal**, mirroring the FDE panel format. **This project is the recommended subject** — the `SORTORDER` parse ruling, the rules-first inversion, the false-NIGO gate and the read-only boundary are four decisions with named rejected alternatives.

**Focus thread:** watched folder → exactly-once claim → deterministic `SORTORDER` parse → packet segmentation + multimodal extraction → pairing → liveness → 15-item adjudication vs a versioned SOP → IGO/NIGO → report + escalation → append-only event log → **dbt quality mart**.

> **Cert discipline (v10.0):** the shipped, production-grade project is the primary hiring signal; certs are tiebreakers. Platform certs are a **conditional menu — take exactly ONE**, matched to a concrete apply-list's stack. Keyword-density is a negative signal.

---

## Quick Reference

```
┌─────────────────────────────────────────────────────────────────┐
│   POSTCHECK — STAGE 1 (Foundation)                              │
│   Watched folder → parse → read → adjudicate → report/escalate  │
├─────────────────────────────────────────────────────────────────┤
│  W1–2  exactly-once intake · SORTORDER parser · baseline harness│
│  W3–4  packet segmentation · vision extraction · retry · pairing│
│  W5–6  9 rule modules · Layer 2 · re-read rule · 15-item verdict│
│  W7–8  reports · events · MCP · golden set · BLOCKING GATE      │
├─────────────────────────────────────────────────────────────────┤
│  HEADLINE: deterministic parser beats flattened baseline on     │
│            false-CRITICAL rate, same rules, same golden set     │
├─────────────────────────────────────────────────────────────────┤
│  🔒 never keys · never posts · never contacts · never authorizes│
└─────────────────────────────────────────────────────────────────┘
```