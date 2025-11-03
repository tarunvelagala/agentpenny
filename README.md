# 📈 Penny Stock Agentic AI

An agentic AI system that identifies potential multi-bagger penny stocks, allocates ₹1000 monthly across selected opportunities, sends Telegram notifications for manual approval, and logs all activity for performance tracking.

The goal is to minimize risk, capture early growth, and automate data analysis and tracking, with optional integration to Zerodha or Groww APIs for automated low-latency trading at market open.

## 🧭 Example Flow

- Lambda triggers at 9:00 AM (weekdays).
- Data Agent scrapes 3 Screener URLs.
- Analysis Agent classifies results (Buy / Exit / Avoid).
- Advisor Agent compiles actionable suggestions.
- Splitter Agent allocates ₹1000 evenly.
- Notifier Agent sends summary to Telegram.
- You confirm or reject suggestions.
- Audit Agent logs actions in S3.
- Tracker Agent updates ROI for next cycle.

## 🧠 System Overview

This system uses multiple collaborating agents, each focused on a specific function:

Agent	Purpose
Data Agent	Scrapes public Screener.in URLs and prepares stock data.
Analysis Agent	Applies buying criteria to identify potential multi-bagger stocks.
Risk Agent	Monitors risk and detects exit or overbought signals.
Advisor Agent	Combines analysis and risk data to decide BUY / HOLD / EXIT.
Splitter Agent	Allocates ₹1000 intelligently among shortlisted stocks.
Notifier Agent	Sends Telegram messages and waits for confirmation.
Audit Agent	Logs all actions to AWS S3 for audit and transparency.
Tracker Agent	Calculates monthly ROI and evaluates performance.
Trader Agent (Future)	Executes trades via Zerodha or Groww APIs (optional).
## ⚙️ Screener Queries

These queries are used as inputs to the Data Agent.

### Buy Query (Penny Stock — Careful + Profit Mindset):

```sql
Current price < 20 AND
Market Capitalization > 100 AND
Promoter holding > 30 AND
Debt to equity < 1.5 AND
Volume > 75000 AND
Current price > DMA 200 AND
DMA 50 > DMA 200
```


### Sell / Exit Watchlist Query (Momentum Broken):

```sql
Current price < DMA 200 OR
Current price < DMA 50
```

### Avoid / Warning Query (Overstretched / Risk Zone):

```sql
Current price > (1.5 * DMA 200)
```
---
## 🔄 System Architecture

```mermaid
flowchart LR
    %% Screener Data Inputs
    Screener1[Screener URL 1 - Buy Query]
    Screener2[Screener URL 2 - Sell/Exit Query]
    Screener3[Screener URL 3 - Avoid/Warning Query]

    %% Agents
    D[Data Agent - Scrape and Merge Data]
    A[Analysis Agent - Classify Stocks]
    AD[Advisor Agent - Final Decision Buy,Exit,Hold]
    SP[Splitter Agent - Distribute ₹1000]
    N[Notifier Agent - Telegram Summary + Confirmation]
    AU[Audit Agent - Store Logs to S3]
    T[Tracker Agent - Track ROI]

    %% External Components
    TG[Telegram Bot - User Interaction]
    AWS_S3[AWS S3 - Storage]
    Z[Zerodha/Groww - Future Auto-Trading Integration]

    %% Flow
    Screener1 --> D
    Screener2 --> D
    Screener3 --> D
    D --> A
    A --> AD
    AD --> SP
    SP --> N
    N --> TG
    N --> AU
    AU --> AWS_S3
    T --> AWS_S3
    TG --> AU
    SP --> T
    T --> AD
    Z --> SP
```

---

## 🧩 UML Sequence Diagram — Agent Flow

```mermaid
sequenceDiagram
    autonumber
    participant U as User (Investor)
    participant L as AWS Lambda (Scheduler)
    participant D as Data Agent
    participant A as Analysis Agent
    participant R as Risk Agent
    participant AD as Advisor Agent
    participant SP as Splitter Agent
    participant N as Notifier Agent
    participant TG as Telegram Bot
    participant AU as Audit Agent
    participant S3 as AWS S3 (Audit Logs)
    participant T as Tracker Agent
    participant Z as Zerodha/Groww (Future)

    U->>L: Setup cron (9:00 AM, weekdays)
    L->>D: Trigger data collection (Screener URLs)
    D->>D: Scrape and parse data
    D->>A: Send structured stock data
    D->>R: Send risk data
    A->>AD: Send Buy signals
    R->>AD: Send Risk signals
    AD->>SP: Finalize BUY / EXIT decisions
    SP->>SP: Split ₹1000 into allocations
    SP->>N: Send allocation summary
    N->>TG: Notify user on Telegram
    TG-->>U: Ask for confirmation
    U-->>TG: Respond (YES / NO)
    TG->>N: Return confirmation
    N->>AU: Log action
    AU->>S3: Store audit log
    T->>S3: Fetch logs for tracking
    T->>AD: Send monthly performance insights
    AD->>Z: (Future) Trigger trade via API
```
---
## 🧱 UML Class Diagram — Agent Components

```mermaid
classDiagram
    class DataAgent {
        +scrape_screener_urls()
        +parse_stock_data()
    }

    class AnalysisAgent {
        +evaluate_buy_signals()
        +apply_queries()
    }

    class RiskAgent {
        +detect_risk_signals()
        +apply_exit_criteria()
    }

    class AdvisorAgent {
        +combine_signals()
        +decide_action()
    }

    class SplitterAgent {
        +allocate_funds(total_amount)
        +assign_to_stocks()
    }

    class NotifierAgent {
        +send_telegram_summary()
        +await_confirmation()
    }

    class AuditAgent {
        +store_logs_to_S3()
        +retrieve_logs()
    }

    class TrackerAgent {
        +calculate_monthly_ROI()
        +generate_report()
    }

    class TelegramBot {
        +send_message()
        +get_user_response()
    }

    class S3Storage {
        +upload_json()
        +fetch_logs()
    }

    DataAgent --> AnalysisAgent
    DataAgent --> RiskAgent
    AnalysisAgent --> AdvisorAgent
    RiskAgent --> AdvisorAgent
    AdvisorAgent --> SplitterAgent
    SplitterAgent --> NotifierAgent
    NotifierAgent --> AuditAgent
    AuditAgent --> S3Storage
    NotifierAgent --> TelegramBot
    SplitterAgent --> TrackerAgent
    TrackerAgent --> S3Storage
```
---
## 🔁 UML State Diagram — System Lifecycle

```mermaid
stateDiagram-v2
    [*] --> Idle
    Idle --> Running : Lambda Trigger - 9AM, Weekdays
    Running --> DataFetched : Screener Data Collected
    DataFetched --> AnalysisDone : Stocks Classified - Buy/Exit/Avoid
    AnalysisDone --> Advised : Advisor Suggests Final Actions
    Advised --> Notified : Telegram Summary Sent
    Notified --> AwaitingConfirmation : Waiting for User Approval
    AwaitingConfirmation --> Confirmed : User Approves Buy/Exit
    AwaitingConfirmation --> Idle : User Rejects
    Confirmed --> Audited : Logs Stored in S3
    Audited --> Tracked : ROI Updated for Month
    Tracked --> Idle : Cycle Complete
```
---

### ☁️ Deployment & Hosting
|Option|Description|Free Tier| 
|---|---|---|
AWS Lambda| Run workflow on cron schedule	| ✅ 1M free requests/month | 
AWS S3| Store audit logs	| ✅ 5 GB free | 
n8n (self-host)	| Local or EC2 orchestration	| ✅ Free on EC2 free tier | 
CrewAI (Python)	| Orchestrate agents programmatically	| ✅ Open source | 
Telegram Bot API	| For notifications	| ✅ Free | 
GitHub Actions	| Optional trigger / CI	| ✅ Free | 

### 📦 Future Enhancements

#### 🔌 Integrate Zerodha Kite Connect or Groww API for real trade execution.
#### 📊 Add real-time dashboards for ROI tracking.
#### 🧠 Fine-tune prompts with LLMs for adaptive query tuning.
#### 🧮 Build dynamic fund allocation models using historical performance data.
