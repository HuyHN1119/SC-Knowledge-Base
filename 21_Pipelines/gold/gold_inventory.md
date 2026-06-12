# Gold Spec — Inventory (fact_inventory_snapshot, fact_inventory_health + views)

## fact_inventory_snapshot

- **Grain:** snapshot_date × location × SKU — **append-only, không xoá history**
- **Source:** stg_inventory — **Refresh:** daily EoD
- **Cột:** snapshot_date, location_id, style_id, color_id, size_id, soh_qty_pcs, reserved_qty_pcs, available_qty_pcs, intransit_qty_pcs, soh_value_vnd (at cost)

## fact_inventory_health

- **Grain:** snapshot_date × channel × SKU (location aggregate lên channel theo serves_channels)
- **Source:** fact_inventory_snapshot + fact_sales (trailing) + fact_po (open) + dim_product
- **Refresh:** daily

| KPI cột | Logic (đúng glossary) |
|---|---|
| `wos_current` | available / avg_weekly_sales_4w |
| `wos_with_intransit` | (available + intransit + open_po_eta_4w) / avg_weekly_sales_4w |
| `str_pct` | qty_sold_since_launch / qty_intake_since_launch × 100 (cumulative) |
| `aging_bucket` | days từ first_inbound_date: 0-30/31-60/61-90/91-180/181-365/365+ |
| `turnover_ytd` | COGS_ytd / avg_inventory_value_ytd |
| `health_status` | Composite per `health_contract.md`: active_hot / active_healthy / slow / at_risk / dead / stockout |

**WOS threshold (theo CLAUDE.md root — giữ nguyên đã kiểm chứng):**

| Status | WOS | Action |
|---|---|---|
| CRITICAL | < 4w | Chase vendor / confirm PO khẩn |
| THẤP | 4–8w | Monitor + check PO pipeline |
| OK | 8–26w | On track |
| DƯ | > 26w | Markdown candidate / giảm reorder |
| NGƯNG BÁN | vol = 0 | Dead stock review |

> Hàng phễu (core): OK đến 26–52w. Seasonal cuối mùa: ngưỡng DƯ co lại còn 8w.

**Rule:**
- `avg_weekly_sales_4w` = net qty (trừ return), 4 tuần đầy đủ gần nhất
- SKU mới < 4 tuần data → dùng số tuần có thực, flag `wos_basis = partial`
- Sales = 0 cả 4 tuần + SOH > 0 → wos = NULL (không chia 0), status theo aging

## Views

### vw_inventory_health_l1
- **Grain:** snapshot_date × channel × category
- soh_value, wos (weighted by value), %value theo health_status, %value theo aging_bucket — cho Inventory dashboard L1 + S&OP deck

### vw_str_by_season
- **Grain:** season × style × color
- str_pct, str_target_pct (từ assortment plan), gap, % season elapsed — Inventory STR tab + option performance
