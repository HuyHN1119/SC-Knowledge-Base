---
name: inventory-health-check
description: Tự động run inventory health check weekly + prep Inventory Review HTML dashboard monthly (W3 IBP). Pull `fact_inventory_health`, compute WOS/STR/Aging/Health classification, flag top critical + overstock + dead stock, output HTML interactive (không pptx) + Excel action list. LUÔN dùng skill này khi user nói "inventory health check", "review tồn kho tuần này", "tính WOS/STR/aging", "SKU nào critical", "dead stock list", "prep inventory review <YYYYMM>".
---

# inventory-health-check

## When to trigger
- "Inventory health check tuần <N>"
- "SKU nào WOS thấp/cao"
- "Dead stock report"
- "Prep Inventory Review <YYYYMM>"
- "Aging analysis"

## Inputs required
1. `snapshot_date` (mặc định: latest)
2. `scope` — weekly_check / monthly_review_deck
3. `channel_filter` (optional)

## Steps

### Step 1: Verify
- `fact_inventory_snapshot` có data cho `snapshot_date`
- `fact_inventory_health` computed cho `snapshot_date`
- `fact_sales` last 8 tuần available (cho WOS calc)

### Step 2: Compute classifications

Reference: `20_Data_Foundation/02_Data_Contracts/inventory/health_contract.md`

Phân loại mỗi SKU vào 1 trong 6:
- active_hot
- active_healthy
- slow
- at_risk
- dead
- stockout

### Step 3: Flag lists

| List | Tiêu chí | Action recommended |
|---|---|---|
| **Critical reorder** | WOS < 2 + active/core + sales > 0 last 4w | Trigger PR ngay |
| **Stockout** | available_qty = 0 + sales > 0 last 4w | Express ship hoặc transfer |
| **Overstock** | WOS > 12 + not new launch | Promotion / markdown |
| **Dead stock** | no sale 90+ days + age > 180d | Clearance / write-off |
| **STR critical** | str_variance_pct < -15% | Markdown trước end-of-season |

### Step 4a: Weekly check output (scope = weekly_check)
- Output file: `D:\Claude\Planning\analysis\<YYYYMMDD>_inventory_health_check.xlsx`
- Sheets:
  - `Critical_Reorder` (Top 50)
  - `Stockout` (Full)
  - `Overstock` (Top 50 by value)
  - `Dead_Stock` (Full)
  - `STR_Critical` (Full)
- Chat summary: counts + total value cho mỗi list

### Step 4b: Monthly review HTML dashboard (scope = monthly_review_deck)
File: `D:\Claude\Planning\reports\<YYYYMM>\pre_inventory_review_<YYYYMM>.html`
Template: `22_Dashboards_Reports/_template/dashboard_template.html`

| Tab | Content | Layer | Visual |
|---|---|---|---|
| 1. Overview | KPI cards: SOH value, Turnover, DIO, Dead stock value | L1 | Big numbers |
| 2. Health Donut | By Category × Channel, click slice → drill Tab 8 | L1 | Chart.js donut |
| 3. WOS Heatmap | Category × Channel grid, click cell → drill Tab 8 | L1 | Chart.js heatmap |
| 4. Turnover Trend | 6 months line by Category | L1 | Chart.js line |
| 5. Aging Pyramid | Stacked horizontal bar 6 buckets + provision value | L1 | Chart.js |
| 6. STR by Season | Curve actual vs target by % season elapsed | L2 | Chart.js |
| 7. Top 20 Critical & Overstock | Side-by-side tables | L2 | Grid.js |
| 8. SKU Drill | Full table SKU × Location, sort/filter on all KPIs | L2 | Grid.js |
| 9. Decisions | Markdown / clearance / write-off approval list với value | — | Form |

### Step 5: Save & summarize
File path theo convention, chat summary 5 bullet.

## Anti-pattern
- ❌ Build pptx static — phải HTML interactive cho weekly check + monthly review
- ❌ Tính WOS với `avg_weekly_sales = 0` mà không classify dead stock
- ❌ Compare actual STR với fixed target (phải compare với target curve theo % season elapsed)
- ❌ Dùng `soh_qty` thay vì `available_qty` cho WOS
