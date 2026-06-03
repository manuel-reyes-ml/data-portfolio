# 🔥 CRUCIBLE — Scope Revision v1.0 → v1.1 (Swing-First & Multi-Timeframe)

**Purpose:** Make the approved v1.0 scope suitable for (a) **starting with SW-A v3** (a swing strategy) and (b) running **both swing and intraday** strategies through backtest → paper → live.
**Status:** Proposed revision — requires reopening two v1.0 locked items (Decision #6 and the "intraday" identity).
**Reviewed by:** Lead Risk & Strategy Analyst
**Date:** June 2, 2026

---

## 0. Verdict (read this first)

**The integrity machinery is the right machinery — keep all of it.** The Wall, the sealed OOS vault, the logged overfitting budget, point-in-time data, walk-forward CV, survivorship correctness, the engine-parity gate, and the three-gate backtest→paper→live pipeline are *exactly* what SW-A v3 needs. In fact they map almost 1:1 onto the "backtest integrity checklist" already written into the SW-A v3 plan (§14). On that axis the scope is not just suitable — it's aligned.

**But v1.0 is intraday-*first* in its bones, in five places that must change for a swing-first start:**

1. **Phase 1 strategies are IT-1 + VWAP Reclaim — both intraday.** Starting with SW-A v3 directly contradicts **locked Decision #6**.
2. **The `Strategy` Protocol is single-symbol, per-bar (`on_bar`).** SW-A v3 needs a *cross-sectional* stage (rank sectors, rank stock RS vs the universe) that the current abstraction has no concept of. **This is the one real architectural gap.**
3. **The data layer is 1-minute-primary, QQQ-aligned.** Swing is daily/weekly-primary, **SPY**-benchmarked, needs the **11 SPDR sector ETFs**, plus **earnings-calendar and SEC-filings** feeds (SW-A v3 gates on both) and **point-in-time GICS sector membership** (not currently called out).
4. **The execution model assumes sub-second fast loop + EOD flatten.** Swing holds for days/weeks: EOD decision cadence, overnight risk, no flatten.
5. **The identity says "Intraday platform."** If it does both, it's a *multi-timeframe* platform, and the AFC differentiator can no longer be "timeframe."

**Bonus finding — starting with swing actually de-risks the build.** Daily data is cheaper and can start near-free; the cadence is EOD not sub-second (far easier to build correctly); and **the PDT rule is largely moot for swing**, removing a Phase 3 regulatory complication. Your instinct to start with swing is the lower-risk on-ramp, not a detour. The cost you take on instead is the new cross-sectional stage (item 2).

---

## 1. What already fits — do not touch

These v1.0 elements serve SW-A v3 directly. Map of SW-A v3 requirement → existing Crucible control:

| SW-A v3 requirement (from its §11/§14) | Crucible v1.0 control that already covers it |
|---|---|
| Point-in-time data, no leakage | §2 principle #1 (The Wall) + §5 point-in-time recompute |
| Survivorship-correct universe | §5 survivorship + §12 risk row |
| Parallel-tag attribution (prove the sector gate adds edge) | The leaderboard/comparison engine (§4) + aggregates-only results store (§3) |
| 25% paper-to-live haircut, +0.20R gate, micro-sizing | §6 "validated" gate (+0.20R OOS), §7 paper parity, §8 promotion rule — *verbatim* alignment |
| Each playbook earns live independently | §4 "clear the crucible independently" — *verbatim* alignment |
| Walk-forward, bootstrap CI, multiple-comparison discipline | §3 walk-forward, §6 ≥30 trades + bootstrap CI, §2 #4 overfitting budget |
| Backtest tests a mechanized proxy; forward validates the read | §7 paper-parity gate + §12 backtest↔live skew controls |

**Implication:** the revision is additive and orientational, not a teardown. ~70% of the scope stands.

---

## 2. The one real architectural change — a cross-sectional / universe stage

**The problem.** v1.0's abstraction is per-symbol, per-bar:

```python
def on_bar(self, bar: Bar, ctx: StrategyContext) -> Signal | None: ...
```

That is fine for IT-1 (each symbol's ORB is self-contained). It **cannot express** SW-A v3's top-down funnel, because "rank the 11 sectors by RS vs SPY" and "is this stock's RS percentile ≥ 70 vs the universe" are **cross-sectional** operations — they compare a symbol against *other symbols at the same timestamp*. `on_bar` sees one symbol at a time and has no universe view.

**The fix (additive — does not break intraday plugins).** Add a universe/cross-sectional pre-stage to the engine and expose its output to `ctx`, plus an optional funnel hook:

```python
# src/crucible/types.py  — extend StrategyContext
class CrossSection(Protocol):
    """Universe-wide, point-in-time rankings as of ctx.t (past data only)."""
    def sector_rank(self, symbol: str) -> int: ...      # 1..11, PIT GICS-mapped
    def sector_rs_rising(self, symbol: str) -> bool: ...
    def rs_percentile(self, symbol: str) -> float: ...   # vs SPY, 0..100
    def adv(self, symbol: str) -> float: ...

# src/crucible/strategies/base.py — extend the Protocol (both optional → intraday unaffected)
class Strategy(Protocol):
    name: str
    timeframe: str                  # "1m" | "1d" | "1w"  (NEW — drives the data + fill model)
    holding_model: str              # "intraday_flat" | "multiday_hold"  (NEW)
    def param_space(self) -> ParamSpace: ...
    def universe_filter(self, ctx: StrategyContext) -> set[str] | None:   # NEW, optional
        """Funnel Stages 1–2: return eligible symbols (regime+sector+stock RS).
        Return None to consider the full universe (intraday plugins do this)."""
        ...
    def on_bar(self, bar: Bar, ctx: StrategyContext) -> Signal | None: ...
```

**How SW-A v3 maps on:** the staged screener already written for SW-A v3 (`rank_sectors → strong_sectors → stocks_in_strong_sectors → detect_setups`) drops straight in — `universe_filter()` is Stages 1–2, `on_bar()` is Stage 3 (the pullback-reclaim / base-breakout trigger). The engine computes `CrossSection` once per timestamp from past-only data; the leaderboard's **parallel-tag** mode runs `on_bar` on the *full* universe with sector tier tagged (no `universe_filter`), which is exactly the attribution test SW-A v3 requires.

**Engine change:** the backtest loop gains a per-timestamp cross-sectional compute, and a `holding_model` switch (`intraday_flat` keeps the EOD flatten; `multiday_hold` carries positions overnight). Both are config-driven and leave the intraday path intact.

---

## 3. Data-layer additions for swing

| Concern | v1.0 (intraday) | v1.1 addition for swing |
|---|---|---|
| Primary resolution | 1-minute + daily | **Daily primary + weekly** (1m only loaded for intraday plugins) |
| Benchmark | QQQ-aligned | **SPY** primary for RS/sector ranking; QQQ kept for intraday. Multi-benchmark. |
| ETF universe | (implicit) | **11 SPDR sector ETFs** (XLK/XLF/XLE/XLV/XLY/XLP/XLI/XLB/XLU/XLRE/XLC) as first-class series for sector ranking |
| Sector membership | not mentioned | **Point-in-time GICS sector map** — required, or the sector-first backtest leaks (2018 Comm-Services reshuffle). This is a *new* PIT dimension beyond price/RVOL. |
| Fundamentals/events | none (OHLCV only) | **Earnings calendar** (the "no earnings in 3–5 sessions" gate) + **SEC filings feed** for 424B5/S-3/S-1 dilution disqualifier. New data sources. |
| Cost path | Alpaca 1m (real cost) | Daily swing data can start **near-free** (Stooq/Alpaca daily); paid 1m deferred until intraday plugins. Swing lowers the Phase 1 data bill. |

> **Honest gap:** earnings dates and SEC dilution filings are not OHLCV and not in the current data plan. For a faithful SW-A v3 backtest you need point-in-time earnings dates and filing dates. Options: a fundamentals provider (e.g., a paid tier) or an SEC EDGAR ingest for filings + a calendar source for earnings. Flag this as a data dependency before Phase 1, the same way the scope flags the 1m data cost.

---

## 4. Phase 1 strategy set — the swing pair that proves the abstraction

v1.0 ships **IT-1 + VWAP Reclaim** together specifically to prove the plugin abstraction is real. Keep that logic, swap the pair:

| # | v1.0 (intraday-first) | v1.1 (swing-first) |
|---|---|---|
| Phase 1 #1 | IT-1 ORB | **SW-A v3 (Trend Pullback & Base Breakout)** |
| Phase 1 #2 (proves abstraction) | VWAP Reclaim | **SW-B (Trend Continuation / VCP)** |
| Later plugins | VWAP Rejection, Trap, AVWAP | **IT-1, VWAP Reclaim/Rejection, Trap, AVWAP** (intraday) |

**Why SW-A v3 + SW-B as the pair:** they share the *entire* cross-sectional spine (regime → sector rank → RS percentile) but differ in entry mechanic — SW-A v3 buys the **pullback reclaim**, SW-B buys the **VCP pivot breakout**. If both register and run with zero engine changes, the abstraction is proven *and* the cross-sectional stage (the new work) is exercised twice. This is the cleanest possible proof for the swing-first build, and it directly serves the anti-drift testing already specified in the SW-A v3 / SW-B docs.

> **Locked-decision conflict (must reopen):** Decision #6 is "Phase 1 strategies: IT-1 + VWAP Reclaim." Starting with SW-A v3 overrides it. You are the author and can reopen it — but the doc is marked OFFICIAL/APPROVED, so this should be a *conscious* re-decision logged in the changelog, not a silent edit.

---

## 5. Execution model for swing (Phase 2/3)

| Element | v1.0 (intraday) | v1.1 (swing) |
|---|---|---|
| Fast loop | Deterministic, sub-second/per-bar | **EOD batch** — signals generated on the daily close; no sub-second engineering in the swing path |
| Position lifetime | EOD flatten | **Multi-day / multi-week hold**; overnight + weekend gap risk is real and must be in the risk gate |
| Slow loop (agents) | Pre-market regime pick, per-trade veto | Same crew, **daily/weekly cadence**; regime + sector re-rank is a weekend job (matches SW-A v3 §14 cadence) |
| Two-speed rationale | "Intraday can't wait on an LLM" | Still holds, but the deterministic core runs once daily — *easier*, not harder |
| PDT rule | Central concern (§8) | **Largely moot** — swing trades aren't day trades. Note it, but it stops gating the swing path. |

NautilusTrader supports daily bars and multi-day holds, so Phase 2–3 engine choice is unaffected; only the cadence and the flatten assumption change.

---

## 6. Identity & relationship to AFC

- **Retitle:** "Strategy-Agnostic **Intraday** Research-to-Execution Platform" → "Strategy-Agnostic **Multi-Timeframe** Research-to-Execution Platform." Executive summary: "any **rules-based** strategy (swing or intraday)."
- **AFC differentiator can no longer be timeframe.** v1.0 §15 splits them as "AFC = illiquid sub-$5 swing research (read-only); Crucible = liquid intraday execution." With Crucible doing swing too, redraw the line on **liquidity + execution**, not timeframe:
  - **AFC** = illiquid, sub-$5, **research-only / read-only**.
  - **Crucible** = liquid (ADV ≥ 1M, price filters), **executes** (paper→live), **swing + intraday**.
  - The shared ~70% engineering spine argument is unchanged and still favors the eventual shared internal library.

---

## 7. Revised locked-decisions table (v1.1)

| # | Decision | v1.0 | v1.1 |
|---|---|---|---|
| 1 | Codename | Crucible | Crucible (unchanged) |
| 2 | AFC relationship | Sequence; timeframe split | Sequence; **liquidity/execution split** (not timeframe) |
| 3 | AI providers | Local Qwen3 → Gemini → Anthropic → OpenAI | unchanged |
| 4 | Brokers | Alpaca paper+live; Schwab/TOS live | unchanged (note: swing makes TOS live more natural; PDT moot) |
| 5 | Engine | Own harness P1; Nautilus P2–3 | unchanged + **cross-sectional stage** and **holding_model** added to the harness |
| 6 | **Phase 1 strategies** | **IT-1 + VWAP Reclaim** | **SW-A v3 + SW-B (swing pair)**; intraday becomes later plugins |
| 7 | **Platform identity** *(new)* | (implicit: intraday) | **Multi-timeframe (swing + intraday)** |
| 8 | **Primary data resolution** *(new)* | 1m-primary | **Daily/weekly-primary**; 1m loaded per intraday plugin |

---

## 8. Sequencing & honest trade-offs

- **Net new work vs v1.0:** the cross-sectional/universe stage (§2), the PIT GICS map + earnings/filings feeds (§3), and the `multiday_hold` fill path (§5). Everything else is reuse.
- **What gets easier:** daily data (cheaper, near-free start), EOD cadence (no sub-second loop in Phase 1), PDT removed from the swing path. Realistically this makes the Phase 1 *engineering* lighter than the intraday-first plan, even after adding the cross-sectional stage.
- **What gets harder:** the cross-sectional stage is genuinely new architecture, and the fundamentals/event data is a new dependency with cost. Don't underestimate the PIT sector-map work — it's the single most common place a sector-first backtest silently leaks.
- **Recommended path:** Phase 1 = SW-A v3 + SW-B on daily data with the cross-sectional stage and parallel-tag attribution. Add intraday plugins (IT-1, VWAP) only after the swing pair has a documented OOS verdict — the same "prove it, then expand" discipline the original scope already enforces.

---

## 9. Decisions I need from you

1. **Reopen Decision #6?** Confirm Phase 1 starts with **SW-A v3 + SW-B** (swing) and intraday strategies move to "later plugins." (Required for everything above.)
2. **Fundamentals/event data source** for the earnings + dilution gates — pay for a fundamentals tier, or build an EDGAR + earnings-calendar ingest? (Affects Phase 1 data cost and timeline.)
3. **Daily data source** for the near-free start (Stooq / Alpaca daily / paid) — and whether you want the PIT GICS sector map sourced or built.
4. Do you want me to fold this revision into a **consolidated CRUCIBLE_SCOPE_v1.1.md** (full living document), or keep it as this change-set against your approved v1.0?

---

*Revision proposal only. Not investment, financial, or legal advice. This document changes process/architecture orientation; it makes no claim that SW-A v3 or any strategy has positive expectancy — that is what the crucible is for.*
