---
name: demand-review-pack
description: Tự động prep Demand Review HTML dashboard monthly cho IBP cycle (W1 IBP). Pull data từ `fact_forecast_avf`, `fact_sales`, `fact_replenishment`, tính WMAPE/Bias/Hit Rate theo L1 và L2, flag top 10 underforecast & overforecast, build HTML interactive (không pptx) với tab drill-down. LUÔN dùng skill này khi user nói "prep demand review", "demand review dashboard tháng <YYYYMM>", "tính forecast accuracy tháng này", "review AvF tháng <YYYYMM>".
---

# demand-review-pack

## When to trigger
- "Prep Demand Review tháng <YYYYMM>"
- "Tính forecast accuracy tháng N-1"
- "Demand Review deck"
- "AvF review meeting"

## Inputs required
1. `period_yyyymm` — tháng review (thường là N-1 đã closed)
2. `consensus_version` (optional, default: latest CONSENSUS lock)

## Steps

### Step 1: Verify data
- `fact_sales` closed cho `period_yyyymm`
- `fact_forecast` có version CONSENSUS lock cho `period_yyyymm`

### Step 2: Compute KPIs

#### Layer 1 (cho CEO view)
Aggregate theo (`channel_id`, `division`, `category`):
- WMAPE
- Bias %
- Hit Rate (% rows APE ≤ 20%)
- Net Revenue actual vs forecast

#### Layer 2 (cho Planner drill)
Aggregate theo (`style_id`, `color_id`):
- Top 10 worst forecast (highest WMAPE)
- Top 10 over-forecast (most negative bias)
- Top 10 under-forecast (most positive bias, missed sale)
- Styles with critical replenishment trigger

Tham chiếu công thức ở `20_Data_Foundation/05_Glossary/kpi_glossary.md`.

### Step 3: Build HTML dashboard

Template: `22_Dashboards_Reports/_template/dashboard_template.html`

| Tab | Content | Layer | Visual type |
|---|---|---|---|
| 1. Overview | KPI cards: WMAPE 4w & 13w, Bias, Hit Rate. Title period | L1 | Big numbers + sparkline |
| 2. AvF L1 | Heatmap WMAPE & Bias by Channel × Category | L1 | Chart.js heatmap |
| 3. Trend 13w | Line chart Sales actual vs forecast 13 tuần | L1 | Chart.js line, dual axis |
| 4. Worst Forecast (L2) | Grid.js table Top 50 worst styles, sortable | L2 | Filter category, channel |
| 5. Under-forecast (L2) | Grid.js Top 50 under-forecast (missed sales opportunity) | L2 | Filter |
| 6. Replen Queue | Critical + high priority list with value | L2 | Grid + KPI cards |
| 7. Assumptions | Editable text block — user fill next 3-month assumptions | — | Form |
| 8. Decisions | Checklist + owner + date | — | Form |

### Step 4: Save
File: `D:\Claude\Planning\reports\<YYYYMM>\pre_demand_review_<YYYYMM>.html`

### Step 5: Output summary
5-bullet chat summary:
- Overall WMAPE: X% (vs target Y%)
- Bias: ±Z%
- Top problem category: <name>
- Replenishment criticals: <count> SKUs, <value> VND
- Recommended action: <1 line>

## Anti-pattern
- ❌ Build pptx static — phải HTML interactive cho phép drill
- ❌ Dùng forecast version chưa lock
- ❌ Tính MAPE thay WMAPE (per glossary, WMAPE là KPI chính)
- ❌ Drop row có actual = 0 cho WMAPE (chỉ drop cho MAPE)
- ❌ Top 10 worst chỉ rank by qty error — phải kết hợp với value impact
