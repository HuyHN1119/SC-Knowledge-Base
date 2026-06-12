# Inventory Dashboard — Spec

## Audience
Planner (daily), Merchandising (weekly), Finance (monthly).

## Refresh
Daily snapshot, monthly turnover.

## Source
- `gold/fact_inventory_snapshot`
- `gold/fact_inventory_health`
- `gold/vw_str_by_season`

## Tabs

### Tab 1: Inventory Overview (L1 — Landing)

| Visual | Metric |
|---|---|
| Big number | Total SOH value (VND) |
| Big number | Turnover annualized |
| Big number | DIO days |
| Big number | Dead stock value (VND) |
| Donut | SOH value by Health Status (active_hot/healthy/slow/at_risk/dead) |
| Stacked bar | SOH value by Channel × Category |

### Tab 2: WOS Heatmap (L1 + L2)

| Visual | Metric |
|---|---|
| Heatmap | WOS by Category × Channel (L1 default) |
| Drill | Click → WOS by Style × Size for selected cell |
| Table | All "critical" SKUs (WOS < 2 + active) |
| Table | All "overstock" SKUs (WOS > 12) |

### Tab 3: STR by Season (L2)

| Visual | Metric |
|---|---|
| Curve | STR actual vs target by % season elapsed |
| Table | Styles with `str_status = critical` |
| Bar | STR by Category for current season |
| Filter | Season selector |

### Tab 4: Aging Pyramid

| Visual | Metric |
|---|---|
| Pyramid bar | SOH value by aging bucket (0-30 / 31-60 / 61-90 / 91-180 / 181-365 / 365+) |
| Big number | Provision value (VND) |
| Table | Top 20 aged SKUs by value |
| Trend | Aging mix trend 6 months |

### Tab 5: Turnover & Margin

| Visual | Metric |
|---|---|
| Table | Turnover, DIO, Margin per Category × Channel |
| Quadrant | Margin × Turnover scatter (high margin & high turn = winners) |
