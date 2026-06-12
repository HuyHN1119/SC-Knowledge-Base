# Prompt template — Monthly Inventory Review

W3 IBP cycle.

```
Huy: Cowork, prep Inventory Review cho tháng <YYYYMM>.

Context:
- Cycle: IBP W3 — Inventory Review prep
- Audience: Merchandising + Finance
- Layer: L1 cho health donut/heatmap, L2 cho Top 20 lists

Yêu cầu:
1. Snapshot date: cuối tháng N-1
2. Compute WOS/Turnover/DIO/STR/Aging/Health classification
3. Flag Critical/Stockout/Overstock/Dead/STR-critical lists
4. Build 9-slide deck
5. Identify markdown/write-off decisions cần CEO approval (value VND)
6. Output: D:\Claude\Planning\reports\<YYYYMM>\pre_inventory_review_<YYYYMM>.pptx

Reference: skill `inventory-health-check` với scope = monthly_review_deck.
```

## Weekly check variation

```
Huy: Cowork, inventory health check tuần này.

Yêu cầu:
1. Latest snapshot
2. Output Excel với 5 sheets: Critical_Reorder, Stockout, Overstock, Dead_Stock, STR_Critical
3. Chat summary: counts + total value

Reference: skill `inventory-health-check` với scope = weekly_check.
```
