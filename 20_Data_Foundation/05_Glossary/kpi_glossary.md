# KPI Glossary

> Đây là **single source of truth** cho định nghĩa KPI. Mọi tranh luận về cách tính phải resolve về file này.

## Demand KPIs

### MAPE (Mean Absolute Percentage Error)
```
MAPE = mean( abs(actual - forecast) / actual × 100 )
```
- Bỏ qua row có `actual = 0` để tránh chia 0.
- Phù hợp khi forecast > 0 và actual ổn định.

### WMAPE (Weighted MAPE)
```
WMAPE = sum( abs(actual - forecast) ) / sum( actual ) × 100
```
- **Khuyến nghị dùng thay MAPE** vì stable hơn khi có row qty thấp.
- Đây là KPI chính trong Demand Review.

### Bias
```
Bias = sum(actual - forecast) / sum(forecast) × 100
```
- Dương → under-forecast (đoán thiếu, mất sale).
- Âm → over-forecast (đoán dư, gây tồn).
- Target: |Bias| < 10% cho horizon 4-13 tuần.

### Forecast Accuracy Hit Rate
% rows có `APE ≤ 20%`. Đo "bao nhiêu % SKU được forecast tốt".

---

## Supply KPIs

### OTIF (On-Time In-Full)
```
OTIF = % PO lines thoả:
  inbound_date <= requested_delivery_date  (On-Time)
  AND
  qty_inbound >= order_qty × 0.95          (In-Full, tolerance 5%)
```
- Đo cả time và quantity.
- Phổ biến: tách `OTD` (on-time) và `IF` (in-full) riêng để debug.

### PO Fill Rate
```
PO Fill Rate = sum(qty_inbound) / sum(qty_ordered)
```
Đo về quantity, không về time.

### Lead Time Variance
```
LT Variance = actual_inbound_date - po_date  (days)
```
So sánh với `dim_vendor.lead_time_days_avg` để đánh giá vendor.

### Late PO%
% PO có `inbound_date > requested_delivery_date`.

---

## Inventory KPIs

### SOH (Stock On Hand)
Tồn kho thực tế tại 1 thời điểm. Không gồm intransit.

### WOS (Weeks of Supply)
```
WOS = available_qty / avg_weekly_sales_4w
```
- `avg_weekly_sales_4w` = trung bình 4 tuần gần nhất.
- Cho phép tính 2 version: `wos_current` (chỉ SOH) và `wos_with_intransit` (cộng intransit).

### Turnover
```
Turnover_period = COGS_period / avg_inventory_value_period
Turnover_annualized = Turnover_period × (12 / months_in_period)
```

### DIO (Days Inventory Outstanding)
```
DIO = 365 / Turnover_annualized
```

### STR (Sell-Through Rate)
```
STR = qty_sold_since_launch / qty_intake_since_launch × 100
```
- Cumulative, không phải period.
- Compare với `str_target_pct` theo % season elapsed.

### Stock Health (composite)
Phân loại 1-trong-6: active_hot / active_healthy / slow / at_risk / dead / stockout. Logic xem `health_contract.md`.

### Aging Bucket
- 0-30 / 31-60 / 61-90 / 91-180 / 181-365 / 365+
- Đo days từ `first_inbound_date` của SKU tại location.

---

## S&OP / OTB KPIs

### OTB (Open-to-Buy)
```
OTB = Planned Sales (at cost) + Planned Markdown (at cost)
    + Target Ending Inventory − Beginning Inventory − On Order
```
- Tính **at cost** thống nhất (PO at cost). Retail method (at retail value) chỉ dùng khi có hệ thống retail accounting.
- Plan theo tháng × channel × category (L1). Xem `sop/otb_plan_contract.md`.

### Open OTB
```
Open OTB = OTB Plan − Committed (PO placed, chưa cancel)
```
- Âm = **overbought** → bắt buộc action (hold/cancel/push PO).
- Check Open OTB **trước mọi quyết định reorder in-season**.

### Commit %
```
Commit % = Committed Value / OTB Plan × 100
```
Benchmark fashion: ≤ 70-80% commit trước season launch, giữ 20-30% open cho in-season chase (Zara model giữ ~50%).

### Forward Cover (weeks)
```
Forward Cover = Ending Inventory (cost) / avg weekly planned sales (cost) 8 tuần tới
```
Khác WOS: WOS nhìn lùi (trailing sales), Forward Cover nhìn tới (planned sales) — dùng cho OTB và buy decision.

### Intake Margin %
```
Intake Margin = (RRP − Unit Cost) / RRP × 100
```
Margin "trên giấy" lúc mua. Khác Gross Margin (sau discount/markdown thực tế).

### Markdown Budget Used %
```
= Actual Markdown STD / Markdown Budget × 100
```
So với % season elapsed: used % > elapsed % → đang đốt budget nhanh hơn kế hoạch.

### Plan Attainment %
```
= Actual Net Revenue / Target Net Revenue × 100
```
KPI chính của Financial Review. Đo theo period + season-to-date.

### GMROI
```
GMROI = Gross Margin (VND) / Avg Inventory Value at cost (VND)
```
Đo hiệu quả vốn tồn kho. Benchmark fashion retail: 2.0-3.5.

---

## Assortment KPIs

### Option Count
Số option (style × color) unique trong scope. Đo "width" của assortment.

### Avg Depth
```
Avg Depth = sum(buy_qty_pcs) / count(distinct options)
```
Đo độ "depth" trung bình per option. Cao = bet vào số ít hits; thấp = test newness nhiều.

### Hit Rate
% options đạt STR target end-of-season. Benchmark fashion mid-size: ≥ 70%.

### Newness %
```
Newness % = new_options / total_options × 100
```
Mix newness vs core repeat. Benchmark: 40-60% new cho fashion brand, 20-40% cho basic-heavy.

### Core Repeat %
% options carry-over từ mùa trước. Cao = portfolio ổn định, ít risk markdown.

### Channel Exclusivity %
% options chỉ bán 1 channel. Cao = differentiation tốt giữa channels.

---

## Allocation KPIs

### Allocation Fulfillment %
```
Allocation Fulfillment % = qty_received_at_destination / qty_requested × 100
```
Đo allocation thực thi đúng kế hoạch chưa.

### Allocation Accuracy
% SKU allocate trùng với nhu cầu thực (so sánh sau 4 tuần — sales actual vs initial allocation ratio).

### Transfer Cost % of Revenue
```
= sum(transfer_cost_vnd) / sum(net_revenue_vnd) × 100
```
Target: ≤ 1.5% cho omnichannel mid-size. Cao = allocation logic ban đầu sai.

### Stock Imbalance Index
```
= stddev(WOS_across_locations) / mean(WOS_across_locations)
```
Cao = stock không cân bằng giữa locations (1 nơi dư, nơi khác thiếu).

### Break-pack Rate %
% allocation phải break-pack. Cao → repack cost cao → re-evaluate pack definition.

---

## Layer reference

| KPI | L1 (Channel × Div/Cat/Sub-cat) | L2 (Style × Color × Size) |
|---|---|---|
| MAPE/WMAPE/Bias | ✓ | ✓ (đến style+color, ít khi đến size) |
| OTIF | ✓ | ✓ |
| WOS | ✓ | ✓ (đến SKU) |
| Turnover | ✓ | ✓ |
| STR | ✓ | ✓ |
| Aging | (sum) | ✓ |
| Stock Health | ✓ | ✓ |
| OTB / Open OTB / Commit % | ✓ | — (OTB không drill L2) |
| Forward Cover | ✓ | ✓ (đến style+color) |
| Plan Attainment / GMROI | ✓ | — |
| Option Count / Hit Rate / Newness | ✓ | ✓ |
| Allocation Fulfillment / Accuracy | (sum) | ✓ |
| Transfer Cost % | ✓ | ✓ |
| Stock Imbalance Index | ✓ | — |
