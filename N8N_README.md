# N8N Workflow 

```sql
[Schedule Trigger (Monthly)]
             │
             ▼
   [Scraper / Parser Subworkflows] ──┐
             │                       │
             ▼                       │
         [Merge Node] ◀──────────────┘  (merges all subworkflow outputs)
             │
             ▼
     [Advisor Agent (Python)]
   - Classifies stocks as BUY/HOLD/EXIT
   - Checks `_audit` table to skip previous buys
             │
             ▼
[Investment Splitter Agent (Python)]
   - Allocates ₹1000 across BUY stocks
   - Computes units per stock
             │
             ▼
          [IF Node]
Condition: investment_plan exists & length > 0 & Telegram sent
         /                    \
      True:                     False:
      │                           │
      ▼                           ▼
 [Telegram Node]           [Telegram Node]
Send investment plan      Send "No stocks this month"
      │
      ▼
  [Audit Node / DB]
   - Update `_audit` with newly bought stocks
   - Log portfolio: stock, units, invested amount
      │
      ▼
[Portfolio Tracker / DB]
```
