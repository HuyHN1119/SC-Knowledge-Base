# Allocation & Assortment Dashboard — Spec

## Audience
Merchandiser / Allocator (daily), Head of Merchandising (weekly), Planning Manager (review).

## Refresh
- Allocation: Daily
- Assortment: Weekly (in-season tracking vs plan)

## Source
- `gold/fact_allocation`
- `gold/fact_transfer`
- `gold/fact_assortment_plan_vs_actual`
- `gold/vw_option_performance`

## Tabs

### Tab 1: Assortment vs Plan (L1)

| Visual | Metric |
|---|---|
| Big number | Total options bought vs planned (count + value) |
| Big number | Newness % actual vs plan |
| Donut | Core / Fashion / Seasonal mix actual vs plan |
| Bar | Option count per category × channel: actual vs plan |
| Trend | Cumulative buy commitment vs OTB |

### Tab 2: Option Performance (L2)

| Visual | Metric |
|---|---|
| Quadrant scatter | STR % × Margin % per option (winners = top right) |
| Table | Top 20 hits (high STR × high margin) |
| Table | Top 20 misses (low STR × low margin) — markdown candidate |
| Pareto | Top 20% options = X% revenue |
| Filter | Lifecycle stage selector |

### Tab 3: Allocation Heatmap (L2)

| Visual | Metric |
|---|---|
| Heatmap | qty_allocated per Location × Category latest 30 days |
| Table | Initial allocation pipeline (Wave 1/2/3 status) |
| Big number | Allocation fulfillment % |
| Big number | Break-pack cost MTD |

### Tab 4: Transfer Activity

| Visual | Metric |
|---|---|
| Sankey | Transfer flow Location_A → Location_B |
| Table | Top 10 high-volume SKU transfers |
| Big number | Total transfer cost MTD |
| Flag | SKUs transferred 2+ times trong 30 days (allocation issue) |

### Tab 5: Channel Mix (L1)

| Visual | Metric |
|---|---|
| Stacked bar | Buy qty by Channel × Mix Type |
| Table | Channel-exclusive options + performance |
| Trend | Channel split MTD/QTD |
