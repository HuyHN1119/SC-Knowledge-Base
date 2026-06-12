# Gold Spec — Demand (fact_sales, fact_forecast, fact_forecast_avf, fact_replenishment)

## fact_sales

- **Grain:** date × channel × SKU (aggregate từ order line)
- **Source:** stg_sales — **Refresh:** daily
- **Logic:** loại `cancelled`; return tính âm vào net; group by (order_date, channel_id, style_id, color_id, size_id)
- **Cột:** date_id, channel_id, style_id, color_id, size_id, sales_qty_pcs, gross_revenue_vnd, discount_vnd, net_revenue_vnd, platform_fee_vnd, return_qty_pcs, is_promo_day
- **L1 path:** join dim_product (division/category/sub_cat) + dim_channel — không build bảng L1 riêng, dùng `vw_sales_l1_weekly`

## fact_forecast

- **Grain:** forecast_version × week × channel × style × color
- **Source:** stg_forecast (+ stg_color_curve cho style mới chưa có forecast color-level)
- **Refresh:** khi forecast mới lock

## fact_forecast_avf

- **Grain:** week × channel × style × color (so version CONSENSUS locked vs actual)
- **Source:** fact_sales (aggregate lên week × style × color) + fact_forecast
- **Refresh:** weekly (Mon 6am)
- **Cột:** iso_year, iso_week, channel_id, style_id, color_id, forecast_version, forecast_qty_pcs, actual_qty_pcs, abs_error, ape_pct
- **KPI derive (đúng glossary):** WMAPE = Σ|a−f| / Σa; Bias = Σ(a−f) / Σf; Hit rate = % rows APE ≤ 20%
- **Rule:** actual aggregate từ net qty (trừ return); tuần chưa close không tính

## fact_replenishment

- **Grain:** plan_date × location × SKU
- **Source:** fact_forecast (explode size qua stg_size_curve) + fact_inventory_snapshot + dim_vendor (lead time) + dim_product
- **Refresh:** daily
- **Logic chuẩn (min-max với forecast-driven demand):**
  ```
  demand_during_lt_qty = forecast_weekly_avg × (lead_time_days_p90 / 7) [explode size curve]
  safety_stock_qty     = z × σ_demand × sqrt(lead_time_weeks)   (z theo service level target 95%)
  reorder_point_qty    = demand_during_lt + safety_stock
  replen_qty           = max(0, reorder_point − available − intransit − on_order)
  ```
- **Cột:** plan_date, location_id, style_id, color_id, size_id, forecast_weekly_qty, available_qty, intransit_qty, on_order_qty, reorder_point_qty, replen_qty_suggested, curve_source (actual/fallback), action (none/replen/urgent)
- **Rule:** Hàng phễu (lifecycle = core) luôn có row; seasonal chỉ replen khi STR < target và còn Open OTB (check fact_otb trước khi suggest)

## Views

### vw_sales_l1_weekly
- **Grain:** week × channel × division × category × sub_category — cho Demand dashboard L1 + S&OP deck
- Gồm cả: qty, net_revenue, % vs LY (khi có 2 năm data), % vs forecast
