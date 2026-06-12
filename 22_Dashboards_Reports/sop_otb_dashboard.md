# S&OP / OTB Dashboard — Spec

## Audience
Planning Manager (weekly), CFO + Head of Merchandising (monthly — Financial Review W3.5), CEO (MBR).

## Refresh
- OTB tracking: Monthly W3.5 (trước Financial Review) + ad-hoc khi cần quyết định reorder lớn
- Plan vs Actual: Weekly

## Source
- `gold/fact_otb`
- `gold/fact_plan_vs_actual`
- `gold/fact_po` (committed/received)
- `gold/fact_inventory_snapshot` (valued)

## Tabs

### Tab 1: OTB Position (L1) — "Are we within budget?"

| Visual | Metric |
|---|---|
| Big number | Total Open OTB season hiện tại (VND) — xanh nếu > 0, đỏ nếu overbought |
| Big number | Commit % vs benchmark (≤80% pre-launch) |
| Big number | Forward cover (wks) vs target |
| Waterfall | OTB plan → committed → received → open, per season |
| Heatmap | Open OTB per Channel × Category (đỏ = overbought cell) |

### Tab 2: OTB Tracking Detail (L1)

| Visual | Metric |
|---|---|
| Table | Full grid Channel × Category: plan / committed / open / commit% / fwd cover / status / action_flag |
| Trend | Cumulative commit % vs season timeline (so với pacing line chuẩn) |
| Flag list | Mọi cell overbought chưa có action — escalate MBR |

### Tab 3: Plan vs Actual (L1) — Financial Review view

| Visual | Metric |
|---|---|
| Big number | Plan attainment % MTD + STD (net revenue) |
| Bar | Actual vs Budget vs RF per Channel |
| Bar | Gross margin % actual vs target per Category |
| Trend | Markdown budget used % vs season elapsed % |
| Big number | GMROI rolling 12M |

### Tab 4: Buy Commitment vs Assortment (L1)

| Visual | Metric |
|---|---|
| Bar | Buy value: OTB plan vs assortment plan vs PO committed, per Category |
| Variance table | Cell nào lệch > ±2% — cần reconcile |
| Trend | Cumulative buy commitment theo wave (1/2/3) vs OTB phasing |

### Tab 5: Scenario (L1)

| Visual | Metric |
|---|---|
| Toggle | Best / Base / Worst sales scenario → projected EOS inventory + open OTB cần release/hold |
| Table | Action recommendation per category: chase / hold / cancel / release OTB |

## Decision rules hiển thị trên dashboard

| Điều kiện | Action flag |
|---|---|
| Open OTB < 0 | OVERBOUGHT — hold mọi PO mới, review cancel |
| Commit % > 80% và season chưa launch | WARN — hết room chase in-season |
| Forward cover > target × 1.5 | HOLD PO — đẩy ETA hoặc giảm qty |
| Attainment < 90% sau 2 tháng liên tiếp | Trigger re-forecast OTB (version REFC) |
