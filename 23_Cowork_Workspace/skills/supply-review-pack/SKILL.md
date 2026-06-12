---
name: supply-review-pack
description: Tự động prep Supply Review HTML dashboard monthly cho IBP cycle (W2 IBP). Pull data từ `fact_po`, tính OTIF/Fill Rate/Lead Time variance theo L1 và vendor, build vendor scorecard, flag at-risk PO, output HTML interactive (không pptx). LUÔN dùng skill này khi user nói "prep supply review", "supply review dashboard <YYYYMM>", "vendor scorecard tháng này", "PO pipeline review", "tính OTIF tháng <YYYYMM>".
---

# supply-review-pack

## When to trigger
- "Prep Supply Review <YYYYMM>"
- "Tính OTIF tháng N-1"
- "Vendor scorecard"
- "PO pipeline review"

## Inputs required
1. `period_yyyymm`
2. `vendor_focus` (optional — review chi tiết vendor nào)

## Steps

### Step 1: Verify data
- `fact_po` đã close cho PO lines có `inbound_date` trong `period_yyyymm`
- `dim_vendor` cập nhật

### Step 2: Compute KPIs

#### Layer 1
- OTIF % toàn công ty MTD
- Fill Rate % toàn công ty MTD
- Total PO value open + at-risk value
- Lead Time variance trung bình

#### Vendor level
- Per vendor: OTIF, Fill Rate, Avg LT days, # late PO, total value YTD
- Trend 6 tháng
- Top 5 + Bottom 5

### Step 3: At-risk PO list
Lọc fact_po với:
- `status` IN (open, in_production, intake)
- `requested_delivery_date < today + 14 days` AND `inbound_date IS NULL`
- Flag `risk_flag = at_risk` hoặc `late`

### Step 4: Build HTML dashboard

Template: `22_Dashboards_Reports/_template/dashboard_template.html`

| Tab | Content | Layer | Visual type |
|---|---|---|---|
| 1. Overview | KPI cards: OTIF MTD, Fill Rate, # at-risk PO, value at-risk | L1 | Big numbers |
| 2. Pipeline Funnel | PR → PO → Intake → Inbound (qty + value) | L1 | Chart.js funnel/bar |
| 3. Vendor Scorecard | Top 5 + Bottom 5 by OTIF, sortable table | Vendor | Grid.js |
| 4. OTIF Trend | Line chart 6 months by vendor (multi-line) | Vendor | Chart.js |
| 5. At-risk PO | Top 20 by value, status flag, expected vs actual | L2 | Grid.js, filter |
| 6. Lead Time Variance | Heatmap by Vendor × Category | Vendor × Cat | Chart.js heatmap |
| 7. Capacity Flag | Vendor utilization vs capacity, color-coded | Vendor | Bar |
| 8. Decisions | Checklist + owner | — | Form |

### Step 5: Save
`D:\Claude\Planning\reports\<YYYYMM>\pre_supply_review_<YYYYMM>.html`

### Step 6: Summary
- OTIF MTD: X% (target Y%)
- At-risk PO value: Z VND, count: N
- Worst vendor: <name>, OTIF X%
- Top mitigation: <1 line>

## Anti-pattern
- ❌ Build pptx static — phải HTML interactive
- ❌ Tính OTIF chỉ bằng on-time (thiếu in-full)
- ❌ Bỏ qua intake stage khi PO chưa inbound
- ❌ Vendor rank chỉ theo volume (phải kết hợp OTIF + value)
