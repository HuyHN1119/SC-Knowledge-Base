# Demand Dashboard — Spec

## Audience
Demand Planner (daily), Head of Merchandising (weekly), Planning Manager (Demand Review prep).

## Refresh
Daily (sales), Weekly (forecast accuracy).

## Source
- `gold/fact_sales`
- `gold/fact_forecast`
- `gold/fact_forecast_avf`
- `gold/fact_replenishment`

## Tabs

### Tab 1: Sales Performance (Landing)
**Layer 1** — luôn mặc định.

| Visual | Metric | Layout |
|---|---|---|
| Big number | Net Revenue MTD vs Forecast | Top left |
| Big number | Sales Qty MTD vs Forecast | Top mid |
| Big number | Bias MTD (%) | Top right |
| Line chart | Weekly sales actual vs forecast 13 tuần | Mid |
| Stacked bar | Sales by Channel × Category MTD | Bottom left |
| Table | Top 10 categories by Variance (act-fc) | Bottom right |

**Filters:** Period (MTD/QTD/YTD), Channel, Division.

### Tab 2: Forecast Accuracy
| Visual | Metric |
|---|---|
| Big number | WMAPE 4-week ahead |
| Big number | WMAPE 13-week ahead |
| Trend | WMAPE trend 12 tháng |
| Heatmap | Bias by Category × Forecast Horizon |
| Table | Top 10 worst-forecast styles (L2 drill) |

### Tab 3: Replenishment Queue
**Layer 2** — focus SKU level.

| Visual | Metric |
|---|---|
| Table | All critical SKUs (WOS < 2 + active) |
| Table | All high-priority replenishment with `replenish_qty_pcs` + `priority` + `expected_otd_date` |
| Stacked bar | Replenishment qty by Category × Channel |

### Tab 4: Size Mix (NEW)
| Visual | Metric |
|---|---|
| Bar | Planned size curve vs actual size sell-through (by Category) |
| Heatmap | Size shortage (WOS by Style × Size) |
| Table | Styles with size imbalance (1 size sold out, others overstock) |

### Tab 5: Color Performance
| Visual | Metric |
|---|---|
| Pareto | Sales by Color (top 20 colors = X% of sales) |
| Bar | Planned color mix vs actual by Style |
