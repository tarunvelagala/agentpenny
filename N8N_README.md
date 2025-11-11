# N8N Workflow 

```sql
+---------------------+
| Schedule Trigger    |  <-- Runs monthly
+---------------------+
           |
           v
+---------------------+
| Scraper Subworkflow |  <-- Run in parallel for 3 URLs
+---------------------+
           |
           v
+---------------------+
| Merge Node          |  <-- Combine all subworkflow outputs
+---------------------+
           |
           v
+---------------------+
| Advisor Agent       |  <-- Python
| - Classify stocks   |
| - Check audit table |
| - Output BUY/HOLD/EXIT
+---------------------+
           |
           v
+---------------------+
| Investment Splitter |  <-- Python
| - Allocate ₹1000    |
|   evenly across BUY |
+---------------------+
           |
           v
+---------------------+
| IF Node             |  <-- Check if actionable stocks exist
+---------------------+
        /   \
       /     \
 True /       \ False
     /         \
    v           v
+-----------+  +----------------------+
| Telegram  |  | Telegram             |
| Node      |  | Node                 |
| - Send    |  | - "No stocks this    |
|   investment| |   month" message     |
|   plan     |  +----------------------+
+-----------+
      |
      v
+---------------------+
| Audit Node          |  <-- Store actions in n8n Table / Supabase
+---------------------+
      |
      v
+---------------------+
| Portfolio Tracker / |
| DB                  |  <-- Log stock, units, invested amount
+---------------------+
```
