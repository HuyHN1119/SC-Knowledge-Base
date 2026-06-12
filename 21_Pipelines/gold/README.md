# Gold Layer — Marts (Dashboard-Ready)

## Nguyên tắc
- **Star schema.** Mỗi fact join về dim_product, dim_channel, dim_calendar, dim_vendor, dim_location.
- **Append-only snapshot.** fact_inventory_snapshot daily không xoá → trace được history.
- **Hỗ trợ cả L1 và L2 cùng lúc** — qua join dim chứ không build 2 fact riêng.

## Fact tables

| Fact | Grain | Sources | Refresh |
|---|---|---|---|
| `fact_sales` | date × channel × SKU | stg_sales | Daily |
| `fact_forecast` | forecast_version × week × channel × style × color | stg_forecast + stg_color_curve | Khi có forecast mới |
| `fact_forecast_avf` | week × channel × style × color | fact_sales + fact_forecast | Weekly |
| `fact_po` | po × line × SKU | stg_po + stg_intake + stg_inbound | Daily |
| `fact_inventory_snapshot` | date × location × SKU | stg_inventory | Daily |
| `fact_inventory_health` | date × channel × SKU | fact_inventory_snapshot + fact_sales (derived WOS/STR/aging/health) | Daily |
| `fact_replenishment` | plan_date × location × SKU | fact_forecast + fact_inventory_snapshot + dim_product (lead_time) | Daily |
| `fact_assortment_plan_vs_actual` | season × channel × category × sub_cat | stg_assortment_plan + actuals from fact_po + fact_sales | Weekly in-season |
| `fact_allocation` | alloc_id × SKU × to_location | stg_initial_allocation + stg_replen_allocation | Daily |
| `fact_transfer` | transfer_id × SKU | stg_transfer | Daily |
| `fact_option_performance` | snapshot_date × style × color | fact_sales + fact_inventory_health + option_lifecycle | Weekly |
| `fact_otb` | period × otb_version × channel × division × category | stg_otb_plan + fact_po + fact_sales + fact_inventory_snapshot | Monthly W3.5 |
| `fact_plan_vs_actual` | period × channel × division × category | stg_sales_margin_plan + fact_sales | Weekly |

## Aggregation views (cho dashboard tốc độ cao)

| View | Grain | Dùng cho |
|---|---|---|
| `vw_sales_l1_weekly` | week × channel × division × category × sub_category | Demand dashboard L1 |
| `vw_inventory_health_l1` | snapshot_date × channel × category | Inventory dashboard L1 |
| `vw_supply_otif_monthly` | month × vendor × category | Vendor scorecard |
| `vw_str_by_season` | season × style × color | Inventory STR tab |
| `vw_otb_l1` | season × channel | MBR OTB slide |
| `vw_po_pipeline_weekly` | week ETA × channel × category | WOS-with-intransit, OTB tracking |
| `vw_option_performance` | style × color (latest) | Allocation/Assortment dashboard Tab 2 |

## Spec chi tiết (gộp theo module)

| File | Fact/view cover |
|---|---|
| `gold_demand.md` | fact_sales, fact_forecast, fact_forecast_avf, fact_replenishment, vw_sales_l1_weekly |
| `gold_supply.md` | fact_po, vw_supply_otif_monthly, vw_po_pipeline_weekly |
| `gold_inventory.md` | fact_inventory_snapshot, fact_inventory_health, vw_inventory_health_l1, vw_str_by_season |
| `gold_sop_assortment_allocation.md` | fact_otb, fact_plan_vs_actual, fact_assortment_plan_vs_actual, fact_option_performance, fact_allocation, fact_transfer, vw_otb_l1, vw_option_performance |

## Cowork access

- Skills được phép đọc fact_* và vw_*.
- Khi user hỏi L1 (CEO view) → ưu tiên `vw_*_l1_*` để nhanh.
- Khi user hỏi L2 (SKU drill) → đọc fact_* trực tiếp.
