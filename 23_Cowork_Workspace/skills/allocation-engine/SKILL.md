---
name: allocation-engine
description: Recommend allocation cho fashion mid-size — initial allocation (first wave sau PO inbound), replenishment allocation (re-fill từ WH), inter-location transfer (rebalance). Logic theo size curve × store grade × velocity × forecast ratio. LUÔN dùng skill này khi user nói "allocation plan", "phân bổ hàng về store", "first wave allocation", "replenishment allocation", "transfer order", "rebalance stock", "hàng nào về store nào", "store A bao nhiêu hàng".
---

# allocation-engine

## When to trigger
- "Phân bổ wave 1 cho style X"
- "Replenishment allocation tuần này"
- "Rebalance stock giữa store HCM và HN"
- "Allocation cho marketplace FBS"
- "Hàng về kho rồi, chia thế nào"

## Inputs required
1. `mode` — initial / replenishment / transfer
2. `scope` — style_id list hoặc all available
3. `from_location_id` (default: WH tổng)
4. `to_locations` (list hoặc all eligible)
5. `wave_number` (cho initial mode)

## Steps

### Step 1: Pull data
- `fact_inventory_snapshot` (latest) — SOH per location
- `fact_sales` last 4-8 weeks per location
- `dim_product` — pack definition, lifecycle stage
- `assortment/channel_assortment_contract` — channel eligibility
- `demand/size_curve_contract` — size mix per location cluster

### Step 2a: Initial Allocation logic
Cho mỗi (style_id, color_id, size_id) với qty_available > 0:

```
1. Filter eligible locations (channel_assortment.is_eligible = true)
2. Compute forecast_ratio per location:
   ratio_i = forecast_qty_4w_i / sum(forecast_qty_4w_all_locations)
3. Apply store grade multiplier:
   adjusted_ratio_i = ratio_i × grade_multiplier_i (A=1.5, B=1.0, C=0.5)
4. Normalize: final_ratio_i = adjusted_ratio_i / sum(adjusted_ratio)
5. qty_alloc_i = qty_available × final_ratio_i × wave_pct (Wave 1 = 50%, Wave 2 = 30%, Wave 3 = 20%)
6. Round to pack_size_total nếu using pre-pack
7. Hold remainder cho replen wave
```

### Step 2b: Replenishment Allocation logic
Cho mỗi to_location đạt ngưỡng:
```
1. Calculate WOS at to_location
2. Filter: WOS < 4 (critical/high/med)
3. min_stock_needed = target_wos × avg_weekly_sales (target_wos = 4)
4. replen_qty = max(0, min_stock_needed - soh - intransit)
5. Cap by wh_available
6. Priority: critical > high > med > low
7. Allocate in priority order until WH depleted
```

### Step 2c: Transfer logic
Detect imbalance:
```
1. Find SKU where:
   - location_A has wos > 12 (overstock)
   - location_B has wos < 2 (stockout)
   - same channel hoặc compatible channels
2. transfer_qty = min(
     overstock_excess_at_A,  -- (soh - 8 × velocity) at A
     stockout_deficit_at_B   -- (4 × velocity - soh) at B
   )
3. Check transfer_cost < expected_margin_recovery
```

### Step 3: Output

#### File 1: Excel allocation plan (cho WH execute)
Path: `D:\Claude\Planning\allocation\<YYYYMMDD>_<mode>_allocation.xlsx`
Sheets:
- `Summary` — totals per location × category
- `Detail_SKU` — full SKU × location × qty
- `Pack_Breakpack` — flag pack vs break-pack cost impact
- `Approval` — qty pending approval (priority sort)

#### File 2: Allocation dashboard (HTML interactive)
Path: `D:\Claude\Planning\reports\<YYYYMM>\allocation_<mode>_<YYYYMMDD>.html`
Template: `22_Dashboards_Reports/_template/dashboard_template.html`

**Tabs:**
| Tab | Content | Layer | Interaction |
|---|---|---|---|
| 1. Overview | KPI cards: total SKU allocated, total qty, # locations, fulfillment %, break-pack cost | L1 | — |
| 2. Location Heatmap | Heatmap qty by Location × Category | L1 | Click cell → filter Tab 5 |
| 3. Wave Phasing (initial) | Bar Wave 1/2/3 split per category | L1 | — |
| 4. Replen Queue (replen) | Priority-sorted table critical/high/med/low | L2 | Filter priority |
| 5. SKU Drill | Grid.js: SKU × Location × qty × pack_type. Sortable, filterable | L2 | Search SKU/location |
| 6. Transfer Map | Sankey diagram from_location → to_location flow (transfer mode) | L1 | — |
| 7. Imbalance Detect | Scatter: WOS distribution across locations cho từng SKU. Highlight outliers | L2 | — |

→ Dashboard này dùng cho **Allocator daily review** và **Planning Manager weekly health check**.

### Step 4: Chat summary
- Mode: <initial/replen/transfer>
- # SKUs allocated: <N>
- Total qty: <V> pcs
- Top recipient location: <name> (<qty> pcs)
- Break-pack cost est: <VND>
- Top critical allocation: <SKU> → <location>

## Anti-pattern
- ❌ Allocate even_split cho launch mới (mất hot store opportunity)
- ❌ Allocate 100% qty wave 1 (giữ 30-40% cho replen)
- ❌ Ignore pack_definition → break-pack tốn cost
- ❌ Replen vào location đang overstock (kiểm tra WOS đầu)
- ❌ Transfer cùng SKU 2+ lần/tháng (sửa allocation logic gốc thay vì transfer fix)
