# 📊 Data Analysis Portfolio

**Manuel Reyes** | Business Ops Professional → Senior LLM Engineer  
Production Systems • Real Business Impact • Building in Public

[![LinkedIn](https://img.shields.io/badge/LinkedIn-mr410-blue)](https://linkedin.com/in/mr410) 
[![GitHub](https://img.shields.io/badge/GitHub-manuel--reyes--ml-181717)](https://github.com/manuel-reyes-ml) 
[![Email](https://img.shields.io/badge/Email-Contact-red)](mailto:manuelreyesv410@gmail.com)

---

## 🎯 Portfolio Overview

**Current Stage:** Data Analyst (Stage 1 of 5)  
**Focus:** Production systems + measurable business impact + systematic skill progression  
**Trajectory:** 37-month structured path to Senior LLM Engineer

**What Makes This Portfolio Different:**
- ✅ **Production code** with real business ROI (not just tutorials)
- ✅ **Flagship project** evolving through all 5 career stages
- ✅ **Public repositories** demonstrating professional practices
- ✅ **Domain expertise** in finance, trading, and business operations
- ✅ **Systematic progression** with measurable outcomes

---

## 🏆 Production Systems

### 1. 🧾 [1099 Reconciliation ETL Pipeline](https://github.com/manuel-reyes-ml/1099_reconciliation_pipeline)
**Status:** ✅ Live in Production | 🌐 Public Repository

Automated ETL pipeline reconciling retirement plan distribution data between Relius and Matrix financial systems at Daybright Financial.

**Business Problem:**  
Manual reconciliation took 4-6 hours weekly, was error-prone, and blocked critical 1099-R tax reporting deadlines.

**Solution Architecture:**
- Multi-stage ETL pipeline with comprehensive validation
- Automated data quality checks across systems
- Professional error handling and audit logging
- Uses synthetic test data (`faker`) for public demo
- Production-ready code with documentation

**Measurable Business Impact:**
- ⚡ **95% time reduction** (4-6 hours → 15 minutes weekly)
- 💰 **$15,000+ annual savings** in labor costs
- 📊 **10x scalability** (300+ accounts vs. 30 manual capacity)
- ✅ **Zero errors** since deployment
- 🎯 **Stakeholder trust** through reliable automation

**Why This Matters for Portfolio:**
- Demonstrates production deployment capability
- Shows business value thinking (not just technical skills)
- Professional data governance (public code, private data)
- Real system integration experience
- Measurable ROI

**Tech Stack:** Python • pandas • openpyxl • Excel • data validation • Matplotlib • faker  
**Key Skills:** ETL architecture, data reconciliation, production deployment, business impact measurement

[**→ View Code & Technical Documentation**](https://github.com/manuel-reyes-ml/1099_reconciliation_pipeline)

---

## 🚀 Flagship Project

### 2. 📈 [Attention-Flow Catalyst](https://github.com/manuel-reyes-ml/attention-flow-catalyst)
**Status:** 🚧 Phase 1A Active | 🌐 Public Repository

> **Research Question:** Which trigger or combination best predicts +10% price moves within 3 trading days?

**One project, five stages, complete career demonstration.**

This is my **flagship project** — a predictive trigger analysis system for small-cap stocks that evolves through all 5 stages of my career transition, from Data Analyst to Senior LLM Engineer.

**System Architecture:**
```
┌─────────────────┐     ┌─────────────────┐     ┌─────────────────┐
│  Stock Screener │ ──► │  Data Pipeline  │ ──► │ Trigger Detection│
│  50 small-caps  │     │  3yr history    │     │  T1-T4 signals   │
│  (<$5, listed)  │     │  per ticker     │     │  + combinations  │
└─────────────────┘     └─────────────────┘     └─────────────────┘
                                                        │
                                                        ▼
┌─────────────────┐     ┌─────────────────┐     ┌─────────────────┐
│ Signal Generator│ ◄── │   Leaderboard   │ ◄── │ Backtest Engine │
│  daily watchlist│     │  rank by hit    │     │  test all combos│
└─────────────────┘     └─────────────────┘     └─────────────────┘
```

**Stock Screening Criteria:**
- Price < $5 (bigger % move potential)
- Listed exchanges only (NYSE, NASDAQ, AMEX — NO OTC)
- Small float (limited shares = faster moves)
- Strong sector (auto-detected by relative strength)

**Triggers Being Tested:**

| ID | Trigger | Data Source | What It Detects |
|----|---------|-------------|-----------------|
| **T1** | SEC Form 4 Insider Buy | edgartools | Smart money activity |
| **T2** | Wikipedia Attention Spike | Wikipedia API | Public attention surge |
| **T3** | News Mention Spike | RSS/GDELT | Media coverage |
| **T4** | Volume Accumulation | yfinance | Institutional buying patterns |

**Volume Signals (T4 Breakdown):**
- **T4a:** Relative Volume (RVOL ≥ 1.5x) — Unusual activity
- **T4b:** Accumulation Score Rising — Sustained buying pressure
- **T4c:** OBV Breakout (20-day high) — Cumulative buying strength
- **T4d:** Quiet Accumulation — Stealth institutional buying
- **T4e:** Volume Dry-Up — Sellers exhausted (pre-breakout)

**Combination Testing:**
- All individual triggers (T1, T2, T3, T4)
- All 2-trigger combinations (T1+T2, T1+T4, etc.)
- All 3-trigger combinations (T1+T2+T3, T1+T2+T4, etc.)
- All 4-trigger combination (T1+T2+T3+T4)
- Each tested with context filters (sector strength, index trend)

**Project Evolution Through 5 Career Stages:**

| Stage | Version | Focus | Key Deliverable |
|-------|---------|-------|-----------------|
| **1 (DA)** | v2.0-v3.0 | Backtest engine | Trigger leaderboard, signal generator, dashboard |
| **2 (DE)** | v4.0 | Cloud scale | 500+ tickers, Airflow automation, AWS |
| **3 (ML)** | v5.0 | Predictions | ML ensemble, XGBoost/LSTM, auto-optimization |
| **4 (LLM)** | v6.0 | AI Assistant | Natural language interface, RAG system |
| **5 (Sr)** | v7.0 | Production | Monetization platform ($2-50K/mo potential) |

**Why This Project Stands Out:**
- ✅ **Dynamic screening** — Program finds stocks automatically (not manual list)
- ✅ **Real SEC data** — Form 4 insider transactions via edgartools
- ✅ **Statistical rigor** — Proper backtesting with combination testing
- ✅ **Volume analysis** — RVOL, OBV, accumulation patterns (institutional footprint)
- ✅ **Domain expertise** — 6 years trading knowledge codified into algorithms
- ✅ **Progressive architecture** — Evolves through all 5 career stages
- ✅ **Clear path to monetization** — Not just a learning project

**Current Phase (1A) Deliverables:**
- Dynamic stock screener with auto-detection of strong sectors
- 3+ years historical data collection for 50 stocks
- All trigger detection (T1-T4) with context analysis
- Backtest engine testing all combinations
- Trigger leaderboard ranked by +10% hit rate
- Forward signal generator using winning triggers

**Tech Stack:** Python • SQLite • pandas • edgartools • yfinance • Wikipedia API • RSS/GDELT • matplotlib  
**Key Skills:** Dynamic screening, API integration, database design, statistical backtesting, feature engineering, volume analysis

[**→ View Code & Documentation**](https://github.com/manuel-reyes-ml/attention-flow-catalyst)

---

## 📋 Planned Projects (Stage 1)

### 3. 📊 OnBase Workflow Intelligence System
**Status:** 📅 Next Major Project

Enterprise workflow analytics for retirement plan operations at Daybright Financial.

**Business Challenge:**  
No visibility into demand patterns or processing bottlenecks across workflows (distributions, contributions, enrollments, transfers), making resource allocation suboptimal.

**Planned Solution:**
- Data extraction from OnBase document management system
- Demand analysis per workflow type
- Processing time analysis per stage and queue
- Interactive dashboards for stakeholder visibility
- Actionable recommendations with ROI estimates

**Expected Impact:**
- Identify processing bottlenecks
- Enable data-driven resource allocation
- Reduce average processing times
- Improve capacity planning

**Skills to Demonstrate:**
- Enterprise system integration
- Business intelligence
- Stakeholder communication
- Process optimization
- Data-driven recommendations

**Tech Stack:** Python • SQL • pandas • data visualization • business intelligence

---

### 4. 📊 Trading Dashboard (Phase 1B Component)
**Status:** 📅 Planned (Attention-Flow Catalyst Phase 1B)

Interactive Streamlit dashboard built on Attention-Flow Catalyst data with real-time filtering, trigger visualization, and signal tracking.

**Features:**
- Interactive trigger leaderboard visualization
- Stock screener results with filtering
- Active signals watchlist
- Backtest performance charts
- Export capabilities

**New Skills:** Streamlit, Plotly (interactive charts), UI/UX design  
**Relationship:** Component within flagship project (not separate)

---

### 5. 🤖 ML Trading Models (Stage 3 Evolution)
**Status:** 📅 Planned (Attention-Flow Catalyst v5.0)

Machine learning models predicting price movements using triggers and features from Phase 1.

**Planned Approach:**
- Feature engineering from trigger combinations
- XGBoost for tabular signal data
- LSTM for time-series patterns
- Ensemble strategies combining models
- Comprehensive backtesting framework

**New Skills:** scikit-learn, XGBoost, LSTM, feature engineering, ML backtesting  
**Relationship:** Stage 3 evolution of flagship project

---

## 📖 Learning Documentation

### [Learning Journey Repository](https://github.com/manuel-reyes-ml/learning_journey)
**37-month public documentation** of career transition from financial services to Senior LLM Engineer.

**Includes:**
- 📚 Course materials & comprehensive notes (CS50, Python, IBM, Google)
- 📝 Progress summaries and insights
- 🛠️ Complete development setup guides
- 📋 Structured learning activation plans
- 🎓 Certification tracking
- 🧪 Enhanced practice exercises (beyond course requirements)

[**→ View Learning Journey**](https://github.com/manuel-reyes-ml/learning_journey)

---

### [37-Month Interactive Roadmap](https://manuel-reyes-ml.github.io/learning_journey/roadmap.html)
Visual progression through 5 career stages with detailed course planning, community engagement strategy, and project milestones.

**Stages:**
1. **Data Analyst** (Months 1-5) - Current
2. **Data Engineer** (Months 6-15) - Cloud, pipelines, big data
3. **ML Engineer** (Months 16-29) - Machine learning, MLOps
4. **LLM Specialist** (Months 30-34) - RAG, vector DBs, fine-tuning
5. **Senior LLM Engineer** (Months 35-37) - Production AI systems

[**→ View Interactive Roadmap**](https://manuel-reyes-ml.github.io/learning_journey/roadmap.html)

---

## 🛠️ Technical Skills

### **Current Proficiency (Stage 1)**
```
Languages:     Python 3.11+, SQL
Data Analysis: pandas, NumPy, Matplotlib, Seaborn, Plotly
Databases:     SQLite, PostgreSQL (learning)
Tools:         Jupyter, Git/GitHub, VS Code, Excel (advanced)
APIs & Web:    REST APIs, JSON/XML parsing, edgartools, yfinance, BeautifulSoup
Production:    ETL pipelines, data validation, error handling, backtesting
```

### **Domain Expertise**
```
Data Operations: 15+ years (manufacturing, bookkeeping, financial services)
Financial Services: 8 years (retirement plans, compliance, reconciliation)
Trading: 6 years active trading (technical analysis, volume patterns, market psychology)
Business Operations: Process automation, ROI analysis, stakeholder communication
```

### **Skill Progression by Stage**

| Stage | Role | Core Technologies |
|-------|------|-------------------|
| **1** (Current) | Data Analyst | Python, SQL, Visualization, Statistics, Backtesting |
| **2** | Data Engineer | AWS, Airflow, PySpark, Redshift, Docker |
| **3** | ML Engineer | scikit-learn, XGBoost, TensorFlow, PyTorch, MLOps |
| **4** | LLM Specialist | LangChain, RAG, Vector DBs, Fine-tuning |
| **5** | Senior LLM Engineer | Production AI, Architecture, Leadership |

---

## 🎯 Project Philosophy

### **Production-First Mindset**

Every project follows these principles:
- ✅ **Solves real problems** (business or personal)
- ✅ **Production-quality code** from Day 1
- ✅ **Comprehensive documentation** (README, inline comments, architecture)
- ✅ **Measurable outcomes** (business impact or clear learning objectives)
- ✅ **Professional data governance** (synthetic test data for sensitive projects)
- ✅ **Public when possible** (transparency and accountability)

### **Flagship Project Strategy**

Rather than building many disconnected projects, I'm building **one flagship project** that evolves through all 5 career stages:

```
Attention-Flow Catalyst Evolution:

Stage 1 (DA):  SQLite → Backtest engine → Trigger leaderboard → Signals
                                    │
Stage 2 (DE):  AWS → Airflow → 500+ tickers → Real-time pipeline
                                    │
Stage 3 (ML):  XGBoost → LSTM → Ensemble → Auto-optimization
                                    │
Stage 4 (LLM): RAG → Natural language → AI Trading Assistant
                                    │
Stage 5 (Sr):  Production → Monitoring → Monetization ($2-50K/mo)
```

**Why This Approach:**
- **Depth over breadth** — Deep expertise in one domain
- **Progressive complexity** — Each stage builds on previous work
- **Portfolio coherence** — One strong story, not scattered projects
- **Real-world relevance** — Combines trading expertise with tech skills
- **Monetization potential** — Path to revenue-generating system

### **Building in Public**

**Why share everything openly?**
- **Transparency:** Real progress, real struggles, real growth (not just polished results)
- **Accountability:** Public commits = public commitment (can't fake consistency)
- **Community:** Learn from others, contribute to the ecosystem
- **Portfolio:** The repos themselves ARE the proof of capability
- **Professional Practice:** Proper data governance even with public code

---

## 💼 About Me

### **Current Position**
Financial Administrator at Daybright Financial (retirement plan operations)

### **Career Trajectory**
37-month structured progression from Data Analyst to Senior LLM Engineer specializing in AI-powered trading systems

### **What Makes Me Different**

**Typical Entry-Level Candidate:**
```
✓ Tutorial projects
✓ Online course completions
✓ Basic Python/SQL knowledge
✗ No production experience
✗ No domain expertise
✗ No measurable business impact
```

**What I Bring:**
```
✓ Tutorial projects (enhanced beyond requirements)
✓ Multiple certifications (in progress)
✓ Advanced Python/SQL proficiency
✓ Production ETL system (live, measurable ROI)
✓ Flagship trading project with statistical rigor
✓ 15+ years data + 8 years finance + 6 years trading
✓ Real business value ($15K savings, 95% efficiency gain)
✓ Clear growth trajectory (systematic 37-month plan)
✓ Public code portfolio (transparent, accountable)
```

### **The Value Proposition**

> "I don't just learn from tutorials—I deploy production systems with measurable business impact. My flagship project uses real SEC data, proper statistical backtesting, and volume analysis to find predictive trading signals. I bring deep domain expertise in finance and trading, think quantitatively, and I'm systematically building toward advanced AI/ML capabilities. **The code is public, the impact is measurable, and the trajectory is clear.**"

---

## 📊 Current Focus & Next Milestones

**Current Stage:** Data Analyst (1 of 5)  
**Study Commitment:** 25 hours/week systematic learning 
**Certifications in Progress:** CS50, Python for Everybody, IBM Data Analyst, Google Data Analytics, Statistics with Python

**Active Work:**
- 🚧 Attention-Flow Catalyst Phase 1A (backtest engine, trigger leaderboard)
- 📅 OnBase Workflow Intelligence System (starting soon)
- 🎓 5 certifications in progress

**Stage 1 Goals:**
- Complete Attention-Flow Catalyst Phase 1A & 1B
- Build enterprise system integration experience (OnBase project)
- Secure Data Analyst position in business operations or finance sector

**Next Stage Milestones:**
- **Stage 2 Complete:** Data Engineer role, AWS certified, Attention-Flow Catalyst v4.0
- **Stage 3 Complete:** ML Engineer role, Attention-Flow Catalyst v5.0 with ML models
- **Stage 4 Complete:** LLM Specialist role, AI Trading Assistant v6.0
- **Stage 5 Complete:** Senior LLM Engineer, Production platform v7.0 with revenue

---

## 🔗 Connect & Collaborate

**Professional Links:**
- **LinkedIn:** [linkedin.com/in/mr410](https://linkedin.com/in/mr410)
- **GitHub:** [github.com/manuel-reyes-ml](https://github.com/manuel-reyes-ml)
- **Portfolio Site:** [manuel-reyes-ml.github.io/learning_journey](https://manuel-reyes-ml.github.io/learning_journey/)
- **Email:** [manuelreyesv410@gmail.com](mailto:manuelreyesv410@gmail.com)

**I'm Open To:**
- 💼 **Data Analyst opportunities** (remote, business operations or finance/trading sectors)
- 🤝 **Professional networking** with data professionals and traders
- 💻 **Code reviews** and technical discussions
- 🤖 **Collaborations** on trading + tech projects
- 🎓 **Mentorship** (giving or receiving)
- 💬 **Career transition** conversations

**Let's Connect If You:**
- Value production experience over tutorial completions
- Are building data-driven trading systems
- Believe in transparent career development
- Want to discuss data + AI + finance intersection
- Are on a similar learning journey
- Are hiring Data Analysts with proven delivery capability

---

## 📈 Project Progression Map
```
✅ DEPLOYED
└─ 1099 Reconciliation ETL Pipeline (Production, Live, $15K savings)

🚧 ACTIVE DEVELOPMENT  
└─ Attention-Flow Catalyst — Phase 1A
   ├─ Dynamic stock screener (small-caps < $5, strong sectors)
   ├─ Alternative data collection (Form 4, Wiki, News)
   ├─ Volume signals (RVOL, OBV, accumulation patterns)
   ├─ Backtest engine (3yr, all trigger combinations)
   └─ Trigger leaderboard & signal generator

📅 NEXT (Stage 1)
├─ Attention-Flow Catalyst Phase 1B (VSA metrics, dashboard)
└─ OnBase Workflow Intelligence System

📅 PLANNED (Stage 2-5)
├─ Attention-Flow Catalyst v4.0 (Cloud, Airflow, 500+ tickers)
├─ Attention-Flow Catalyst v5.0 (ML models, ensemble)
├─ Attention-Flow Catalyst v6.0 (AI Assistant, RAG)
└─ Attention-Flow Catalyst v7.0 (Production, monetization)

🎯 ULTIMATE GOAL (Stage 5)
└─ Production AI Trading Platform ($2-50K/mo potential)
```

---

<div align="center">

### 💡 **"From financial operations to LLM engineering—production systems + domain expertise + systematic learning = career transformation"**

⭐ **Star this portfolio** to follow the journey!  
🔔 **Watch repos** for project updates!  
💬 **Connect** to collaborate or discuss data + trading + tech!

---

**Current Stage:** Data Analyst (Stage 1) | Building Production Systems  
**Status:** 🟢 Active • Learning in Public • Deploying Real Solutions

**[→ View Complete Learning Journey](https://github.com/manuel-reyes-ml/learning_journey)**

</div>