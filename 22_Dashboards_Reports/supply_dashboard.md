# Supply Dashboard — Spec

## Audience
Procurement (daily), Vendor Coord (daily), Planning Manager (Supply Review prep).

## Refresh
Daily.

## Source
- `gold/fact_po`
- `silver/stg_pr`, `stg_intake`, `stg_inbound`
- `silver/dim_vendor`

## Tabs

### Tab 1: Pipeline Status (Landing — L1)

| Visual | Metric |
|---|---|
| Funnel | PR → PO → Intake → Inbound (qty + count) cho tháng N |
| Big number | Open PO qty + value |
| Big number | At-risk PO count |
| Big number | OTIF MTD (%) |
| Stacked bar | PO qty by Vendor × Category — current pipeline |

### Tab 2: PO Detail (L2)

| Visual | Metric |
|---|---|
| Table | All open PO lines: po_number, vendor, SKU, qty, requested_date, confirmed_date, status, risk_flag |
| Filter | Status, vendor, category, risk_flag |
| Conditional formatting | Red row nếu `risk_flag = late` |

### Tab 3: Vendor Scorecard

| Visual | Metric |
|---|---|
| Table | Per vendor: OTIF%, Fill Rate%, Avg LT days, Avg LT variance, # late PO, total value YTD |
| Bar | OTIF trend 6 tháng per top 10 vendors |
| Heatmap | OTIF by Vendor × Month |

### Tab 4: Lead Time Analysis

| Visual | Metric |
|---|---|
| Histogram | LT distribution overall |
| Box plot | LT by Vendor |
| Trend | LT variance trend 12 months |

### Tab 5: PR Backlog

| Visual | Metric |
|---|---|
| Table | PR submitted but not yet PO — age days |
| Big number | PR-to-PO conversion time (median) |
| Big number | PR rejection rate |
