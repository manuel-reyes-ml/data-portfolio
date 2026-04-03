# 🚀 OPERATIONS-DEMAND-INTELLIGENCE — Project Scope v2.3

## AI-Powered Workflow Demand Analysis for Retirement Plan Operations

**Document Version:** 2.4 (SDK-First AI Architecture + Evaluation & Docker + 2026 Production Patterns)  
**Last Updated:** February 14, 2026  
**Status:** 📋 DRAFT — Awaiting Approval  
**Author:** Manuel Reyes  
**Data Coverage:** June 02, 2025 — Present (~8 months)  

---

## 📋 Table of Contents

1. [Executive Summary](#1-executive-summary)
2. [Business Problem](#2-business-problem)
3. [Data Architecture](#3-data-architecture)
4. [Demand Analysis Framework](#4-demand-analysis-framework)
5. [Phase 1: Data Pipeline & Analytics](#5-phase-1-data-pipeline--analytics-weeks-1-3)
6. [Phase 2: AI-Powered Dashboard](#6-phase-2-ai-powered-dashboard-weeks-4-6)
7. [AI Guardrails](#7-ai-guardrails)
8. [Tech Stack](#8-tech-stack)
9. [Project Structure](#9-project-structure)
10. [Success Metrics](#10-success-metrics)
11. [Timeline Summary](#11-timeline-summary)

---

## 1. Executive Summary

**Operations-Demand-Intelligence** is a portfolio project that analyzes workflow demand patterns from OnBase document processing at Daybright Financial. It enables data-driven staffing decisions and capacity planning through interactive dashboards with AI-powered natural language queries.

### What Makes This Project Stand Out

| Dimension | Typical Tutorial Project | Operations-Demand-Intelligence |
|-----------|-------------------------|-------------------------------|
| **Data Source** | Kaggle datasets | Real production workflow data |
| **Business Context** | Generic analysis | Finance domain expertise (10+ years) |
| **AI Architecture** | Single provider, raw text | Provider-agnostic SDK (Gemini/OpenAI/Claude) |
| **AI Outputs** | Unstructured text responses | Pydantic-validated structured outputs |
| **AI Features** | Basic chatbot | LLM SDK + PandasAI with guardrails & observability |
| **Data Privacy** | None needed | PII handling, synthetic data for GitHub |
| **CI/CD** | None | GitHub Actions on every PR |

### Core Capabilities

- **Demand Intelligence:** Volume analysis by workflow type, temporal patterns
- **Business Metrics:** Plan-level analytics, product mix, payment trends
- **AI Integration:** Natural language queries via LLM SDK (Gemini primary) + PandasAI + Streamlit
- **Structured Outputs:** Pydantic-validated AI responses with type safety
- **AI Observability:** Token usage, cost tracking, latency monitoring per query
- **Production Practices:** GitHub Actions CI, type hints, testing

### Project Scope Boundaries

```yaml
in_scope:
  - Weekly/monthly demand volume analysis
  - Workflow type distribution (Distribution vs Loan)
  - Temporal patterns (day of week, time of day, seasonality)
  - Plan and product mix analysis
  - AI chat interface for data exploration (LLM SDK + PandasAI)
  - Interactive Streamlit dashboard

out_of_scope:
  - Cycle time analysis (timestamps not available)
  - Real-time processing
  - Predictive ML models (future enhancement)
  - Multi-stage pipeline evolution
```

---

## 2. Business Problem

### 2.1 Current State

At Daybright Financial, retirement plan requests flow through OnBase document management. Data is exported **weekly** for analysis.

### 2.2 Data Availability

| Data Element | Available | Use Case |
|--------------|-----------|----------|
| Document intake date/time | ✅ Yes | Demand timing analysis |
| Workflow type | ✅ Yes | Distribution vs Loan segmentation |
| Plan information | ✅ Yes | Client-level analysis |
| Product type | ✅ Yes | MBDI/MBDII/PLAT mix |
| Payment method | ✅ Yes | ACH/Wire/Check trends |
| Transaction amount | ✅ Yes | Amount tier analysis |
| Cycle time per stage | ❌ No | Cannot calculate |

### 2.3 Business Questions to Answer

1. What is the weekly/monthly demand volume trend?
2. What is the Distribution vs Loan split over time?
3. Which days/times show peak processing demand?
4. Which plans generate the highest request volumes?
5. How does product type (MBDI/MBDII/PLAT) mix vary?
6. What are the payment method preferences?

---

## 3. Data Architecture

### 3.1 Source Data Schema

**OnBase Export Columns (15 total):**

| Column | Data Type | Sample Values | Analytics Use |
|--------|-----------|---------------|---------------|
| `Document Name` | string | — | Deduplication |
| `Document Handle` | string | — | Primary key |
| `Document Type` | string | Various | Workflow category |
| `Document Date` | date | — | Document creation |
| `Date Stored` | date | — | **Intake date** |
| `Time Stored` | time | — | **Intake time** |
| `KMC Plan ID` | string | — | Plan identifier |
| `Plan Name` | string | — | Plan display name |
| `KMC Product Type` | string | MBDI, MBDII, PLAT | **Product mix** |
| `KMC SSN` | string | — | ⚠️ PII - Remove |
| `First Name` | string | — | ⚠️ PII - Remove |
| `Last Name` | string | — | ⚠️ PII - Remove |
| `AmountKMC` | decimal | — | Amount analysis |
| `Payment Method` | string | ACH, Wire, Check | **Payment trends** |
| `Date of Birth` | date | — | ⚠️ PII - Age bands only |
| `Distribution/Loan` | string | Distribution, Loan | **Core segmentation** |

### 3.2 Data Classification

```yaml
pii_columns_remove:
  - KMC SSN           # Delete entirely
  - First Name        # Delete entirely
  - Last Name         # Delete entirely

pii_columns_transform:
  - Date of Birth     # Convert to age bands (18-30, 31-45, 46-55, 56-65, 65+)
  - KMC Plan ID       # Hash for GitHub version
  - Plan Name         # Anonymize for GitHub version

analytics_columns:
  - Document Type
  - Date Stored
  - Time Stored
  - KMC Product Type  # MBDI, MBDII, PLAT
  - Payment Method    # ACH, Wire, Check
  - AmountKMC
  - Distribution/Loan # Distribution or Loan
```

### 3.3 Data Volume

```yaml
current_data:
  distributions: ~5,300 rows (largest workflow)
  estimated_total: ~8,000-12,000 rows across all workflows
  refresh_frequency: Weekly
  
  history:
    start_date: "June 02, 2025"
    end_date: "January 29, 2026"
    duration: "~8 months"
    
  analysis_capabilities:
    - Week-over-week comparison: ✅ Yes
    - Month-over-month comparison: ✅ Yes (8 months)
    - Quarter-over-quarter: ✅ Yes (Q3 2025, Q4 2025, Q1 2026 partial)
    - Year-over-year: ❌ No (need 12+ months)
    - Seasonality detection: ⚠️ Limited (need full year)
```

### 3.4 Storage Structure

```
data/
├── raw/                      # Original exports (NOT in Git)
│   └── onbase_export_YYYYMMDD.xlsx
├── processed/                # Cleaned data (gitignored)
│   └── demand_facts.parquet
├── synthetic/                # Synthetic data for GitHub
│   └── sample_demand.csv
└── outputs/                  # Analysis results

logs/                         # Application logs (gitignored)
├── pipeline.log              # ETL processing logs
├── analytics.log             # Metrics calculation logs
└── app.log                   # Streamlit app logs
```

### 3.5 Logging Strategy

```yaml
logging_config:
  library: "Python logging (standard library)"
  
  log_levels:
    development: DEBUG
    production: INFO
    
  log_files:
    pipeline.log: "Data ingestion, transformation, validation"
    analytics.log: "Metric calculations, aggregations"
    ai.log: "LLM queries, token usage, cost, latency, guardrail activations"
    app.log: "Streamlit interactions, AI queries"
    
  format: "%(asctime)s | %(levelname)s | %(module)s | %(message)s"
  
  rotation:
    max_size: "10 MB"
    backup_count: 5
    
  key_events_to_log:
    - File loaded (rows, columns, path)
    - PII removal completed
    - Validation results (pass/fail counts)
    - Metric calculation times
    - AI query requests and responses
    - AI token usage (input/output tokens per query)
    - AI cost tracking (cost per query, cumulative session cost)
    - AI latency (response time per LLM call)
    - AI provider used (Gemini/OpenAI/Claude per request)
    - Guardrail activations (blocked queries with reason)
    - Errors and exceptions with stack traces
```

**Example Logging Implementation:**

```python
# src/utils/logger.py
import logging
from pathlib import Path

def setup_logger(name: str, log_file: str, level=logging.INFO) -> logging.Logger:
    """Configure logger with file and console handlers."""
    
    # Create logs directory if needed
    log_path = Path("logs")
    log_path.mkdir(exist_ok=True)
    
    logger = logging.getLogger(name)
    logger.setLevel(level)
    
    # File handler
    file_handler = logging.FileHandler(log_path / log_file)
    file_handler.setFormatter(logging.Formatter(
        "%(asctime)s | %(levelname)s | %(module)s | %(message)s"
    ))
    
    # Console handler
    console_handler = logging.StreamHandler()
    console_handler.setFormatter(logging.Formatter(
        "%(levelname)s | %(message)s"
    ))
    
    logger.addHandler(file_handler)
    logger.addHandler(console_handler)
    
    return logger

# Usage in modules:
# from src.utils.logger import setup_logger
# logger = setup_logger("pipeline", "pipeline.log")
# logger.info(f"Loaded {len(df)} rows from {filepath}")
```

---

## 4. Demand Analysis Framework

### 4.1 Core Segmentation

**Primary Segmentation: Distribution vs Loan**

```yaml
workflow_segmentation:
  distribution:
    column: "Distribution/Loan"
    value: "Distribution"
    expected_volume: "~5,300 rows (largest)"
    
  loan:
    column: "Distribution/Loan"
    value: "Loan"
    expected_volume: "TBD"
```

**Secondary Segmentation: Product Type**

```yaml
product_types:
  - MBDI
  - MBDII
  - PLAT
```

**Payment Methods:**

```yaml
payment_methods:
  - ACH
  - Wire
  - Check
```

### 4.2 Demand Metrics

| ID | Metric | Calculation | Granularity |
|----|--------|-------------|-------------|
| **DM01** | Total Volume | COUNT(documents) | Daily/Weekly/Monthly |
| **DM02** | Distribution vs Loan Mix | COUNT by Distribution/Loan | Weekly |
| **DM03** | Product Type Mix | COUNT by KMC Product Type | Weekly |
| **DM04** | Payment Method Mix | COUNT by Payment Method | Weekly |
| **DM05** | Day-of-Week Pattern | AVG volume by weekday | Weekly |
| **DM06** | Hour-of-Day Pattern | COUNT by hour | Daily |
| **DM07** | Top Plans | Top 10 plans by volume | Weekly |
| **DM08** | Amount Distribution | Percentiles, tiers | Weekly |
| **DM09** | Week-over-Week Change | % change vs prior week | Weekly |
| **DM10** | Month-over-Month Trend | % change vs prior month | Monthly |

### 4.3 Amount Tiers

```yaml
amount_tiers:
  tier_1: "$0 - $5,000"
  tier_2: "$5,001 - $25,000"
  tier_3: "$25,001 - $100,000"
  tier_4: "$100,001+"
```

### 4.4 Age Bands (from DOB)

```yaml
age_bands:
  - "18-30"
  - "31-45"
  - "46-55"
  - "56-65"
  - "65+"
```

---

## 5. Phase 1: Data Pipeline & Analytics (Weeks 1-3)

### Week 1: Foundation & Data Ingestion

| Task | Deliverable | Acceptance Criteria |
|------|-------------|---------------------|
| Project setup | Repo, CI, structure | pytest passes, Ruff clean |
| Excel loader | Read OnBase exports | Handles weekly files |
| PII processor | Remove/transform sensitive data | Zero PII in output |
| Schema validation | Validate columns | >98% pass rate |
| Synthetic data generator | Create fake data for GitHub | Realistic patterns |

**Key Code:**

```python
# src/ingest/loader.py
class OnBaseLoader:
    def load_excel(self, filepath: Path) -> pd.DataFrame: ...
    def validate_schema(self, df: pd.DataFrame) -> bool: ...
    def remove_pii(self, df: pd.DataFrame) -> pd.DataFrame: ...
    def transform_dob_to_age_band(self, dob: date) -> str: ...
```

### Week 2: Data Transformation

| Task | Deliverable | Acceptance Criteria |
|------|-------------|---------------------|
| Clean data pipeline | End-to-end ETL | Parquet output |
| Derived columns | Hour, day of week, amount tier, age band | All calculated |
| Aggregation functions | Flexible groupby | All metrics work |
| Data quality checks | Validation report | Documented |

**Key Code:**

```python
# src/transform/processor.py
class DemandProcessor:
    def add_time_dimensions(self, df: pd.DataFrame) -> pd.DataFrame: ...
    def add_amount_tier(self, df: pd.DataFrame) -> pd.DataFrame: ...
    def add_age_band(self, df: pd.DataFrame) -> pd.DataFrame: ...
    def to_parquet(self, df: pd.DataFrame, path: Path) -> None: ...
```

### Week 3: Analytics Engine

| Task | Deliverable | Acceptance Criteria |
|------|-------------|---------------------|
| Metric calculators | DM01-DM10 | All metrics accurate |
| Aggregation views | Pre-built summaries | Query-ready |
| Export functions | CSV/Parquet outputs | Automated reports |
| Documentation | README, docstrings | Complete |

**Key Code:**

```python
# src/analytics/metrics.py
class DemandMetrics:
    def total_volume(self, df: pd.DataFrame, period: str) -> pd.Series: ...
    def workflow_mix(self, df: pd.DataFrame) -> pd.DataFrame: ...
    def product_mix(self, df: pd.DataFrame) -> pd.DataFrame: ...
    def payment_mix(self, df: pd.DataFrame) -> pd.DataFrame: ...
    def top_plans(self, df: pd.DataFrame, n: int = 10) -> pd.DataFrame: ...
    def time_patterns(self, df: pd.DataFrame) -> pd.DataFrame: ...
```

---

## 6. Phase 2: AI-Powered Dashboard (Weeks 4-6)

### 6.1 Dashboard Architecture

```
┌─────────────────────────────────────────────────────────────┐
│                    STREAMLIT APP                            │
├─────────────────────────────────────────────────────────────┤
│  SIDEBAR                    │  MAIN CONTENT                 │
│  • Date Range               │  Page 1: Overview (KPIs)      │
│  • Distribution/Loan Filter │  Page 2: Trends (Time Series) │
│  • Product Type Filter      │  Page 3: Breakdown (Mix)      │
│  • Plan Filter              │  Page 4: AI Analyst (Chat)    │
└─────────────────────────────────────────────────────────────┘
```

### 6.2 Page Specifications

#### Page 1: Overview

```yaml
page_1_overview:
  kpi_cards:
    - "This Week Volume"
    - "Distribution vs Loan Split"
    - "Week-over-Week Change"
    - "Top Product Type"
    
  charts:
    - type: "Line"
      title: "Weekly Volume Trend"
    - type: "Donut"
      title: "Distribution vs Loan Mix"
```

#### Page 2: Trends

```yaml
page_2_trends:
  charts:
    - type: "Time series"
      title: "Daily/Weekly Volume Over Time"
    - type: "Heatmap"
      title: "Hour × Day of Week Pattern"
    - type: "Bar"
      title: "Monthly Comparison"
```

#### Page 3: Breakdown

```yaml
page_3_breakdown:
  charts:
    - type: "Stacked bar"
      title: "Product Type Mix (MBDI/MBDII/PLAT)"
    - type: "Pie"
      title: "Payment Method Mix (ACH/Wire/Check)"
    - type: "Bar"
      title: "Top 10 Plans by Volume"
    - type: "Histogram"
      title: "Amount Distribution"
```

#### Page 4: AI Analyst

```yaml
page_4_ai_analyst:
  interface: "Chat"
  ai_architecture: "LLM SDK (Gemini primary) + PandasAI (supplementary)"
  
  example_queries:
    - "What was our busiest week last month?"
    - "Show the Distribution vs Loan trend"
    - "Which product type grew the most?"
    - "Compare ACH vs Wire vs Check usage"
    - "What's the average transaction amount for MBDI?"
    
  features:
    - Natural language queries via LLM SDK
    - Pydantic-validated structured responses
    - Auto-generated visualizations
    - SQL/pandas transparency (code shown for every response)
    - Provider-agnostic (swap Gemini → OpenAI → Claude via config)
    - Token/cost tracking per query (AI observability)
    - Export results
    
  structured_output_example:
    model: "DemandInsight"
    fields:
      answer: "str — natural language response"
      metric_value: "float — the calculated number"
      time_period: "str — date range analyzed"
      confidence: "str — high/medium/low"
      sql_query: "str — transparency (code that produced the result)"
      rows_analyzed: "int — data scope"
```

### 6.3 Week-by-Week Dashboard Development

#### Week 4: Streamlit Foundation

| Task | Deliverable |
|------|-------------|
| App structure | Multi-page layout |
| Data loading | Cached Parquet reads |
| Sidebar filters | Date, workflow, product, plan |
| Page 1: Overview | KPIs + basic charts |

#### Week 5: Visualizations & AI

| Task | Deliverable |
|------|-------------|
| Page 2: Trends | Time series, heatmap |
| Page 3: Breakdown | Mix charts, top plans |
| LLM SDK integration | Provider-agnostic AI layer (Gemini primary) |
| Pydantic response models | Structured outputs for all AI responses |
| PandasAI integration | Supplementary chat interface |
| AI guardrails | Read-only, transparency, governance as code |

#### Week 6: Polish & Deploy

| Task | Deliverable |
|------|-------------|
| Page 4: AI Analyst | Polished chat with structured outputs |
| AI observability | Token/cost dashboard in sidebar |
| UI/UX polish | Consistent styling |
| Streamlit Cloud deploy | Public URL |
| Demo recording | 3-5 min video |
| README finalize | Portfolio-ready |

---

## 7. AI Guardrails

### 7.1 Access Control

```yaml
ai_access:
  mode: "Read-only"
  allowed: "SELECT, aggregations, groupby"
  prohibited: "Modifications, file access, PII queries"
```

### 7.2 Governance as Code

```python
# src/ai/guardrails.py — Testable guardrail logic
class AIGuardrail:
    """Validates AI queries before execution.
    
    Unlike config-only guardrails, this approach is testable
    with pytest and enforced at runtime.
    """
    def validate_query(self, query: str) -> GuardrailResult:
        """Check query against security rules."""
        # Check for PII patterns (SSN, names, DOB)
        # Check for modification attempts (UPDATE, DELETE, DROP)
        # Check token limits (4000 max)
        # Return validated or rejected with reason
        
    def sanitize_response(self, response: BaseModel) -> BaseModel:
        """Ensure AI response contains no leaked PII."""
        # Scan structured output fields for PII patterns
        # Redact if found, log guardrail activation
```

### 7.3 Transparency

```yaml
transparency:
  show_code: "Display pandas/SQL code for every response"
  show_row_count: "Display number of rows analyzed"
  acknowledge_limits: "State when data insufficient"
```

### 7.4 Cost Controls

```yaml
cost_controls:
  caching: "1 hour TTL for identical queries"
  token_limits: "4000 tokens per request"
  rate_limits: "50 queries/session"
```

### 7.5 Disclaimer

```yaml
disclaimer:
  text: |
    ⚠️ AI insights are for informational purposes only.
    Data reflects completed documents from OnBase exports.
  location: "Footer of every AI response"
```

---

## 8. Tech Stack

### Data Pipeline

| Category | Technology |
|----------|------------|
| Language | Python 3.11+ |
| Data Processing | pandas, numpy |
| Excel Handling | openpyxl |
| Storage | Parquet |
| Synthetic Data | Faker |
| Testing | pytest |
| Linting | Ruff, mypy |
| CI/CD | GitHub Actions |

### Dashboard

| Category | Technology |
|----------|------------|
| Web Framework | Streamlit |
| Charts | Plotly |
| **AI (Primary)** | **LLM SDK (Gemini primary, OpenAI/Claude supported)** |
| **AI (Supplementary)** | **PandasAI (natural language data querying)** |
| **Structured Outputs** | **Pydantic v2 (response validation)** |
| **AI Observability** | **Python logging + token/cost tracking** |
| Hosting | Streamlit Cloud (FREE) |
| **AI Evaluation** | **DeepEval (answer relevancy for PandasAI analytics queries)** |
| **Containerization** | **Docker (Dockerfile for deployment)** |

---

## 9. Project Structure

```
operations-demand-intelligence/
├── .cursor/
│   └── rules/                  # Cursor AI assistant rules
│       ├── git-workflow.mdc    # Branch naming, commit conventions, PR standards
│       ├── learning-mode.mdc   # Learning patterns, documentation standards
│       └── python-data.mdc     # Pandas/numpy patterns, type hints, docstrings
├── .github/
│   └── workflows/ci.yml
├── config/
│   ├── settings.yaml
│   └── logging.yaml           # Logging configuration
├── data/
│   ├── raw/                    # gitignored
│   ├── processed/              # gitignored
│   └── synthetic/
│       └── sample_demand.csv
├── logs/                       # gitignored - application logs
│   ├── pipeline.log
│   ├── analytics.log
│   ├── ai.log                  # ⭐ AI observability (tokens, cost, latency)
│   └── app.log
├── src/
│   ├── __init__.py
│   ├── ingest/
│   │   ├── loader.py           # Excel loading
│   │   └── anonymizer.py       # PII handling
│   ├── transform/
│   │   └── processor.py        # Data transformation
│   ├── analytics/
│   │   └── metrics.py          # Demand metrics
│   ├── ai/                     # ⭐ AI integration layer (2026 patterns)
│   │   ├── __init__.py
│   │   ├── provider.py         # Provider-agnostic LLM abstraction
│   │   ├── schemas.py          # Pydantic response models (structured outputs)
│   │   ├── guardrails.py       # Governance as code (testable)
│   │   └── observability.py    # Token/cost/latency tracking
│   └── utils/
│       ├── helpers.py
│       └── logger.py           # Logging configuration
├── app/
│   ├── main.py                 # Streamlit entry
│   ├── pages/
│   │   ├── 1_📊_Overview.py
│   │   ├── 2_📈_Trends.py
│   │   ├── 3_🔄_Breakdown.py
│   │   └── 4_🤖_AI_Analyst.py
│   └── components/
│       ├── filters.py
│       └── charts.py
├── tests/
│   ├── test_ingest.py
│   ├── test_transform.py
│   ├── test_analytics.py
│   └── test_ai_guardrails.py   # ⭐ AI guardrails unit tests
├── scripts/
│   └── generate_synthetic_data.py
├── .gitignore
├── README.md
├── requirements.txt
└── requirements-dev.txt
```

---

## 10. Success Metrics

### Phase 1 (Pipeline)

| Metric | Target |
|--------|--------|
| Data loaded | All weekly exports |
| PII removed | 100% |
| Validation pass rate | >98% |
| All metrics working | DM01-DM10 |
| Test coverage | >80% |
| CI pipeline | Green |

### Phase 2 (Dashboard)

| Metric | Target |
|--------|--------|
| All pages working | 4/4 |
| Page load time | <3 seconds |
| AI transparency | 100% code shown |
| Structured outputs | 100% Pydantic-validated |
| Provider switching | Gemini ↔ OpenAI works via config |
| AI observability | Token/cost/latency logged per query |
| Guardrail test coverage | >90% |
| Deployment | Live on Streamlit Cloud |
| Demo video | 3-5 minutes |

### Portfolio Impact

| Platform | Goal |
|----------|------|
| GitHub | Clean README, live demo link |
| LinkedIn | Project post with screenshots |
| Upwork | Portfolio showcase |

---


### AI Evaluation Layer (2026 Production Requirement)

Every AI-powered feature includes measurable quality evaluation using DeepEval.

**Framework:** DeepEval (pytest-compatible, open-source)  
**Install:** `pip install deepeval`

| Metric | What It Measures | Target Score |
|--------|-----------------|-------------|
| Answer Relevancy | Does the AI response address the user's question? | > 0.8 |
| Faithfulness | Is the response grounded in provided context? | > 0.85 |
| Hallucination | Does the output contain fabricated info? | < 0.15 |

**Implementation:**
- Evaluation test cases live in `tests/test_eval.py`
- Run with: `deepeval test run tests/test_eval.py`
- Results logged to `logs/evaluation/` for README metrics
- CI pipeline includes evaluation gate (fail build if scores drop)

**Why This Matters for Portfolio:**
Hiring managers in 2026 specifically scan for evaluation-driven development.
Adding measurable AI quality metrics signals production maturity beyond typical junior portfolios.


### Docker Support (Containerization)

**Dockerfile** provided for reproducible local development and deployment.

```dockerfile
# Dockerfile
FROM python:3.11-slim
WORKDIR /app
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt
COPY . .
EXPOSE 8501
CMD ["streamlit", "run", "app/Home.py", "--server.port=8501"]
```

**Run locally:**
```bash
docker build -t operations-demand-intelligence .
docker run -p 8501:8501 --env-file .env operations-demand-intelligence
```

**Why This Matters for Portfolio:**
Docker appears in 60%+ of AI/ML job postings. Including a Dockerfile
shows deployment readiness — critical for Junior AI Engineer applications.


---

## 11. Timeline Summary

```
Week 1 ──── Week 2 ──── Week 3 ──── Week 4 ──── Week 5 ──── Week 6
  │           │           │           │           │           │
  ▼           ▼           ▼           ▼           ▼           ▼
Setup      Transform   Analytics   Dashboard   AI Layer   Deploy
Ingest       ETL       Metrics      Pages     LLM SDK    Polish
PII         Parquet    Reports     Filters   Structured  Demo
                                              Outputs

├─────────── Phase 1 ───────────┼─────────── Phase 2 ───────────┤
         Pipeline & Analytics            AI-Powered Dashboard
```

### Key Milestones

| Week | Milestone |
|------|-----------|
| **Week 3** | ✅ Pipeline complete, all metrics working |
| **Week 6** | ✅ Dashboard deployed, demo recorded |

---

## ✅ Approval Checklist

- [ ] Data columns correctly mapped
- [ ] Distribution/Loan segmentation confirmed
- [ ] Product types (MBDI/MBDII/PLAT) confirmed
- [ ] Payment methods (ACH/Wire/Check) confirmed
- [ ] Timeline realistic (6 weeks)
- [ ] Scope appropriately focused

---

## Quick Reference

```
┌─────────────────────────────────────────────────────────────┐
│         OPERATIONS-DEMAND-INTELLIGENCE v2.3                 │
│     Focused Demand Analysis + SDK-First AI Dashboard        │
├─────────────────────────────────────────────────────────────┤
│  📊 DATA                                                    │
│     • ~5,300+ distributions + loans weekly from OnBase      │
│     • History: June 2025 — January 2026 (~8 months)        │
│     • Distribution vs Loan segmentation                     │
│     • Product: MBDI, MBDII, PLAT                           │
│     • Payment: ACH, Wire, Check                            │
├─────────────────────────────────────────────────────────────┤
│  📈 ANALYTICS                                               │
│     • 10 demand metrics (DM01-DM10)                        │
│     • Week-over-week & month-over-month trends             │
│     • Time patterns (day, hour)                            │
│     • Plan and product mix                                  │
├─────────────────────────────────────────────────────────────┤
│  🤖 AI FEATURES                                             │
│     • LLM SDK (Gemini primary, OpenAI/Claude supported)     │
│     • Provider-agnostic abstraction layer                    │
│     • Pydantic-validated structured outputs                  │
│     • PandasAI supplementary chat interface                  │
│     • Natural language queries                               │
│     • Code transparency                                      │
│     • Governance as code (testable guardrails)               │
│     • AI observability (tokens, cost, latency)               │
├─────────────────────────────────────────────────────────────┤
│  🔧 ENGINEERING                                             │
│     • Python logging for debugging                          │
│     • AI observability logging (tokens, cost, latency)      │
│     • GitHub Actions CI                                     │
│     • Parquet storage                                       │
│     • Streamlit Cloud deployment                            │
├─────────────────────────────────────────────────────────────┤
│  ⏱️ TIMELINE                                                │
│     • 6 weeks total                                         │
│     • Phase 1: Pipeline (Weeks 1-3)                        │
│     • Phase 2: Dashboard (Weeks 4-6)                       │
└─────────────────────────────────────────────────────────────┘
```

---

**Document Status:** 📋 DRAFT (v2.3 — SDK-First AI Architecture + 2026 Production Patterns)  
**Date:** February 14, 2026  
**Data Coverage:** June 2025 — January 2026 (~8 months)  
**Total Timeline:** 6 weeks

*"Real business data + SDK-first AI architecture + Structured outputs + Production guardrails = Portfolio-ready project"* 🚀
