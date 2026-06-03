# 🔥 CRUCIBLE — Scope Revision v1.2 (CONFIRMED — Swing-First, Free-First Data Layer)

**Status:** ✅ **CONFIRMED** — decisions locked by owner. Supersedes the v1.1 *proposal*; carries forward v1.0 approved architecture.
**Purpose:** Record the confirmed Phase 1 direction and lock the data layer to a $0 free-tier + self-built stack.
**Reviewed by:** Lead Risk & Strategy Analyst
**Date:** June 3, 2026

---

## 0. Confirmed decisions (locked)

| # | Decision | Confirmed value |
|---|---|---|
| A | **Phase 1 timeframe** | **Swing first.** Phase 1 = **SW-A v3** + **SW-B** (the swing pair that proves the plugin abstraction). Intraday (IT-1, VWAP, Trap, AVWAP) becomes later plugins. *(Reopens v1.0 Decision #6.)* |
| B | **PIT GICS sector map** | **Built**, not licensed — from Select Sector SPDR ETF holdings snapshots. |
| C | **Daily price data** | **Free tiers** (no paid data in Phase 1). |
| D | **Earnings data** | **Free-first stack**: Finnhub (forward calendar) + Alpha Vantage `EARNINGS` (historical backfill) + EDGAR 8-K cross-check. |
| E | **Dilution filings** | **Built on SEC EDGAR** (free, authoritative). |
| F | **Paid fundamentals** | **Deferred** — separate, later dependency (margins, balance-sheet runway), not required for Phase 1. |

> **Net effect:** the entire Phase 1 data layer is **$0 in subscription cost** — free APIs + self-built pipelines. The cost moves from dollars to engineering labor, which is the trade you chose, and which doubles as roadmap-relevant data-engineering work.

---

## 1. Updated locked-decisions table (v1.2)

| # | Decision | v1.0 | v1.2 (confirmed) |
|---|---|---|---|
| 1 | Codename | Crucible | Crucible |
| 2 | AFC relationship | timeframe split | **liquidity/execution split** (AFC = illiquid sub-$5 research-only; Crucible = liquid, executes, swing+intraday) |
| 3 | AI providers | Local → Gemini → Anthropic → OpenAI | unchanged |
| 4 | Brokers | Alpaca paper+live; Schwab/TOS live | unchanged (PDT moot for swing) |
| 5 | Engine | Own harness P1; Nautilus P2–3 | + **cross-sectional/universe stage** and **holding_model** added to harness |
| 6 | **Phase 1 strategies** | IT-1 + VWAP Reclaim | ✅ **SW-A v3 + SW-B (swing)** |
| 7 | **Platform identity** | (implicit intraday) | ✅ **Multi-timeframe (swing + intraday)** |
| 8 | **Primary data resolution** | 1m-primary | ✅ **Daily/weekly-primary**; 1m deferred to intraday plugins |
| 9 | **Data layer cost** *(new)* | (paid 1m implied) | ✅ **$0 free-tier + self-built** for Phase 1 |
| 10 | **PIT GICS** *(new)* | not specified | ✅ **Built from SPDR ETF holdings snapshots** |

---

## 2. Phase 1 Data Layer — LOCKED (free-first)

Every data need for SW-A v3 + SW-B, with confirmed source, cost, build/buy, and how point-in-time (PIT) correctness is handled:

| Data need | Source (confirmed) | Cost | Build / Buy | PIT handling |
|---|---|---|---|---|
| Daily OHLCV — stocks, **SPY**, 11 SPDR sector ETFs | Stooq bulk CSV (backfill) + Alpha Vantage `TIME_SERIES_DAILY_ADJUSTED` / Finnhub (top-ups) | Free | Buy (free API) | Adjusted series; store as-of; **survivorship caveat — see §4** |
| Weekly bars | Resampled from daily | Free | Build | Derived |
| Forward earnings calendar (live gate) | Finnhub `/calendar/earnings` | Free | Buy (free) | Naturally PIT (announcement dates) |
| Historical earnings dates (backtest) | Alpha Vantage `EARNINGS` (`reportedDate`) | Free | Buy (free) | Naturally PIT; **cache once** |
| Earnings cross-check | SEC EDGAR 8-K (Item 2.02) | Free | Build | PIT by filing date |
| Dilution filings (424B5 / S-3 / S-1) | SEC EDGAR full-text + submissions API | Free | Build | PIT by filing date |
| Sector membership (PIT GICS proxy) | SPDR 11 sector ETF daily holdings (State Street) | Free | Build | Forward-snapshot + hard-coded discontinuities — **see §3** |
| RS percentile, RVOL, MAs, slopes | Computed from daily OHLCV | Free | Build | Recompute as-of, past-only (The Wall) |

All raw pulls land once in the Parquet/DuckDB lakehouse and are never re-pulled; rate limits only bite on initial backfill.

---

## 3. PIT GICS — Build approach (confirmed: Built)

**Principle:** you are not licensing GICS (proprietary to S&P/MSCI). You are building **point-in-time sector membership consistent with how you trade it** — and you trade the 11 Select Sector SPDR ETFs, so the ETF holdings *are* the ground-truth sector definition.

**Pipeline (the build):**
1. **Forward snapshots (primary).** Daily, download each of the 11 SPDR sector ETF holdings files (State Street publishes current holdings). Append to a `sector_membership(date, symbol, sector_etf)` table. This accumulates a true PIT history from day one of the project.
2. **Historical reconstruction (approximation).** State Street offers **no historical-holdings download**, so for the pre-snapshot backtest window you approximate: use coarse periodic membership (quarterly), and **hard-code the three known reclassification discontinuities** so the backtest doesn't misclassify across them:
   - 2015 — Real Estate split out of Financials.
   - 2018 — Communication Services created (pulled GOOGL/META/NFLX from Tech/Consumer Disc).
   - 2023 — payments (V/MA/PYPL) and retail (TGT/DG) reclassifications.
3. **Validate.** Reconcile a sample of reconstructed membership against current holdings to bound the error.

> **Known limitation (document it):** forward snapshots are PIT-perfect from project start; the historical window is an *approximation*. This is acceptable for v1 because (a) sector membership changes slowly, and (b) the **parallel-tag attribution test measures tier-level expectancy (top/middle/bottom-third), not single-name precision** — tier assignment is robust to minor membership noise. Record it as a known limitation in the results metadata, per the scope's own integrity discipline.

> **Roadmap value:** "survivorship- and PIT-aware sector membership built from primary ETF holdings" is a genuine Stage 1–2 data-engineering portfolio artifact — directly on your data-analyst/engineer track. Licensing a black-box GICS history would teach nothing.

---

## 4. Daily data — free-tier specifics and the one real landmine

**Free sources, ranked for this use:**
- **Stooq** — free **bulk** daily CSV; fastest way to backfill a multi-year universe in one pass (no per-call cap).
- **Alpha Vantage** — `TIME_SERIES_DAILY_ADJUSTED`, 20+ years history; free tier **25 requests/day, 5/min** → fine for top-ups and the `EARNINGS` pulls, too slow for a from-scratch universe backfill (cache once, `sleep` between calls).
- **Finnhub** — free **60 calls/min**, US daily + quotes; good for ongoing top-ups.
- **Not** yfinance for production — it's scraping, not an API; fine for a one-off sanity check only.

> **⚠️ The landmine — survivorship bias can sneak back in through free daily data.** Most free price sources only serve **currently-listed** tickers; delisted/merged names are silently missing. After the work we put into a survivorship-correct universe, an all-free daily feed can quietly reintroduce the exact bias. **Mitigation:** source a delisted-ticker list separately (your EDGAR pipeline covers *filers* survivorship-free and can seed it), OR explicitly scope Phase 1 backtests to a survivorship-limited universe and **flag it in results metadata**. Do not let "free daily" smuggle the bias back in unannounced — this is the single biggest integrity risk of going all-free.

Free daily data is genuinely fine for swing (EOD cadence, no microstructure needs) — unlike intraday 1m, where free does not stretch. That asymmetry is exactly why swing-first lowers the Phase 1 data bill.

---

## 5. Earnings — confirmed free stack

Two free providers for two jobs, with a free validation layer:

- **Forward calendar (live "no earnings in 3–5 sessions" gate)** → **Finnhub** `/calendar/earnings?from=&to=` (free, US, 60/min).
- **Historical dates (backtest)** → **Alpha Vantage** `EARNINGS` — one call per symbol returns full quarterly `reportedDate` history; backfill the universe across a few sessions under the 25/day cap and cache permanently.
- **Cross-check** → **EDGAR 8-K (Item 2.02)** filing dates, reusing the dilution pipeline, to confirm free coverage is trustworthy for the backtest window before relying on it.

> **Why free is legitimate here (not a corner cut):** an earnings *date* is naturally point-in-time — the announcement happened when it happened and is not restated like revenue. The free-tier risk is **completeness** (a missing date), not **leakage**. The EDGAR cross-check directly addresses completeness. Validate historical depth before fixing the backtest range.

---

## 6. Dilution filings — EDGAR build (confirmed)

Build on the free SEC EDGAR APIs: no key, ~10 req/sec, full-text search since 2001, submissions API, survivorship-free filer coverage. Query by form type (424B5 / S-3 / S-1); the filing date is the PIT timestamp for the "no dilution in last 60 days" disqualifier. Authoritative primary source — no provider does this better for this purpose.

---

## 7. Total cost and when to revisit paid

- **Phase 1 data subscription cost: $0.** Free APIs + self-built EDGAR and SPDR pipelines.
- **Revisit a ~$30/mo tier (e.g., Tiingo) only when one of these triggers fires:**
  1. Free earnings/price **historical depth** proves too thin for the backtest window you want.
  2. The **survivorship gap** in free daily data materially distorts results and a clean delisted universe isn't reconstructable for free.
  3. You need **deep fundamentals** (margins, cash-vs-burn runway) for SW-A v3's fundamental gate — a separate, genuinely-buy need from earnings dates.
- Until a trigger fires, stay free. Let the crucible's own results tell you when the free data is the binding constraint.

---

## 8. Carried-forward confirmed architecture (summary — full detail in v1.1)

- **Cross-sectional / universe stage (confirmed build).** The engine computes universe-wide PIT rankings (sector rank, RS percentile) each timestamp and exposes them via a `CrossSection` context; strategies gain an optional `universe_filter()` hook (funnel Stages 1–2) plus `on_bar()` (Stage 3). Intraday plugins are unaffected (both additions optional).
- **Phase 1 strategy pair (confirmed).** SW-A v3 (pullback reclaim) + SW-B (VCP breakout) — shared cross-sectional spine, different entry mechanic; proves the abstraction and exercises the new stage twice.
- **`holding_model` switch (confirmed).** `multiday_hold` for swing (carry overnight, no EOD flatten); `intraday_flat` retained for later intraday plugins.
- **Integrity machinery (unchanged — keep all).** The Wall, sealed OOS vault, overfitting budget, walk-forward CV, parity gates, three-gate backtest→paper→live pipeline. These map 1:1 onto SW-A v3's paper-to-live protocol.

---

## 9. Honest limitations of an all-free Phase 1 (consolidated)

1. **Survivorship** — the top risk; free daily feeds omit delisted names (§4). Mitigate or scope-and-flag.
2. **PIT GICS history** — forward-perfect, historically approximated (§3). Tolerable for tier-level attribution; documented.
3. **Free-tier completeness/rate limits** — historical depth must be validated against EDGAR; initial backfills are slow under daily caps (cache once).
4. **No deep fundamentals yet** — SW-A v3's fundamental gate (margins, runway) is thinner on free data; treat as confirmation-only until/unless paid fundamentals are added.

None of these block Phase 1. All must be **recorded in results metadata** so the eventual OOS verdict is read with the data's limits in view — which is the same discipline the scope already enforces for everything else.

---

## 10. Next steps

1. Fold v1.1 + v1.2 into a single consolidated **`CRUCIBLE_SCOPE_v1.2.md`** living document (replacing the v1.0 approved file), if you want one canonical doc rather than a base + two revisions.
2. Decide the **backtest window depth** (how many years), which determines whether free historical earnings/price coverage suffices or trips a paid trigger from §7.
3. Build order for Phase 1 data: EDGAR filings pipeline → SPDR holdings snapshotter → Stooq daily backfill → AV/Finnhub earnings → cross-sectional ranking stage.

---

*Confirmed revision. Not investment, financial, or legal advice. This document fixes process/architecture and data-sourcing decisions; it makes no claim that any strategy has positive expectancy — that is what the crucible is built to determine.*
