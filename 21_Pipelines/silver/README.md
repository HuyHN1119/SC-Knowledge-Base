# Silver Layer — Conformed Data

## Nguyên tắc
- **Snake_case cột** áp dụng theo `20_Data_Foundation/04_Naming_Conventions/field_naming.md`.
- **Master data conformed** — apply `style_id`, `color_id`, `channel_id` từ master.
- **Validation pass** — drop hoặc flag row vi phạm contract.
- **Một entity → một file/table.**

## Bảng silver chuẩn

| Bảng | Source bronze | Grain | Khi nào refresh |
|---|---|---|---|
| `stg_forecast` | demand/forecast_*.xlsx | week × channel × style × color | Khi có forecast mới |
| `stg_color_curve` | demand/color_curve_*.xlsx | season × style × color | Pre-season + revise |
| `stg_size_curve` | demand/size_curve_*.xlsx | season × style × color × size | Pre-season + revise |
| `stg_pr` | supply/pr_master_*.xlsx | pr_id × line | Weekly |
| `stg_po` | supply/po_master_*.xlsx | po × line × SKU | Daily |
| `stg_intake` | supply/intake_log_*.xlsx | intake_id | Daily |
| `stg_inbound` | supply/inbound_log_*.csv | grn_id × SKU | Daily |
| `stg_inventory` | inventory/soh_*.csv | snapshot × location × SKU | Daily |
| `stg_sales` | sales/* (union all sources) | order_line | Daily/Weekly |
| `dim_product` | master/product_master.xlsx | SKU | Weekly |
| `dim_channel` | master/channel_master.xlsx | channel | Khi đổi |
| `dim_vendor` | master/vendor_master.xlsx | vendor | Khi đổi |
| `dim_calendar` | master/calendar_master.xlsx | date | 1 lần/năm |
| `stg_assortment_plan` | assortment/assortment_plan_*.xlsx | season × channel × category × sub_cat | Pre-season + mid-season review |
| `stg_option_plan` | assortment/option_plan_*.xlsx | season × style × color | Pre-season |
| `stg_channel_assortment` | assortment/channel_assortment_*.xlsx | season × style × color × channel | Pre-season |
| `stg_initial_allocation` | allocation/initial_alloc_*.xlsx | alloc_id × SKU × to_location | Per wave |
| `stg_replen_allocation` | (derived) | plan_date × SKU × to_location | Daily/Weekly |
| `stg_transfer` | allocation/transfer_log_*.csv | transfer_id × SKU | Daily |
| `stg_sales_margin_plan` | sop/sales_margin_plan_*.xlsx | plan_version × period × channel × category | Annual + RF |
| `stg_otb_plan` | sop/otb_plan_*.xlsx | otb_version × season × period × channel × category | Per season |
| `stg_option_lifecycle` | (derived) | snapshot × style × color | Weekly |
| `dim_location` | master/location_master.xlsx | location | Khi đổi |

## Transform spec chi tiết (gộp theo module)

| File | Bảng cover |
|---|---|
| `silver_dimensions.md` | dim_product, dim_channel, dim_vendor, dim_calendar, dim_location |
| `silver_demand.md` | stg_forecast, stg_color_curve, stg_size_curve |
| `silver_supply.md` | stg_pr, stg_po, stg_intake, stg_inbound |
| `silver_inventory_sales.md` | stg_inventory, stg_sales |
| `silver_sop_assortment_allocation.md` | stg_otb_plan, stg_sales_margin_plan, stg_assortment_*, stg_*_allocation, stg_transfer |

## Validation rules apply ở silver

| Vi phạm | Action |
|---|---|
| NULL ở PK | Drop row, log |
| SKU không có trong dim_product | Quarantine → ping merchandiser |
| channel_id không match | Quarantine |
| qty < 0 ở sales (return) | Flag, không drop |
| Duplicate PK | Keep latest by `_at`, log |
