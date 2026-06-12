# Star Schema — SC Knowledge Base

## Nguyên tắc thiết kế

- Mỗi module (S&OP / Demand / Supply / Allocation / Assortment / Inventory) có **1 fact table chính** + một số fact phụ.
- Tất cả fact join về **bộ dimension chung** (dim_product, dim_channel, dim_calendar, dim_vendor, dim_location).
- **Grain** (mức chi tiết nhỏ nhất) của mỗi fact phải declare rõ ngay đầu contract.

---

## Dimensions (chung cho toàn bộ marts)

### dim_product
| Cột | Kiểu | Mô tả | Layer |
|---|---|---|---|
| `style_id` | string (PK) | ID style ổn định qua mùa | L2 |
| `style_code` | string | Mã hiển thị (vd "POL-NAVY-001") | L2 |
| `color_id` | string (PK) | ID color | L2 |
| `color_name` | string | Tên hiển thị | L2 |
| `size_id` | string (PK) | ID size | L2 |
| `size_label` | string | "S", "M", "L", "XL"… | L2 |
| `sub_category` | string | "Polo", "T-shirt", "Shirt"… | L1 |
| `category` | string | "Tops", "Bottoms", "Outerwear" | L1 |
| `division` | string | "Menswear", "Womenswear" | L1 |
| `season_launched` | string | "SS26", "FW26" | — |
| `lifecycle_stage` | enum | new / core / aging / clearance | — |

→ PK composite: `(style_id, color_id, size_id)`. Đây là **grain cao nhất** = SKU.

### dim_channel
| Cột | Kiểu | Mô tả | Layer |
|---|---|---|---|
| `channel_id` | string (PK) | ID ổn định | L1 |
| `channel_name` | string | "Shopee", "TikTok Shop", "Website", "Store A1"… | L1 |
| `channel_type` | enum | online / offline / b2b | L1 |
| `platform_group` | string | "Marketplace", "DTC Web", "Retail Store" | L1 |

### dim_calendar
| Cột | Kiểu | Mô tả |
|---|---|---|
| `date_id` | date (PK) | YYYY-MM-DD |
| `iso_week` | int | 1–53 |
| `month` | int | 1–12 |
| `quarter` | int | 1–4 |
| `year` | int | |
| `fiscal_week` | int | Theo lịch tài chính công ty |
| `season` | string | "SS26", "FW26" |
| `is_promo_day` | bool | Flag cho ngày sale (4.4, 5.5, 6.6, 9.9, 11.11…) |

### dim_vendor
| Cột | Kiểu | Mô tả |
|---|---|---|
| `vendor_id` | string (PK) | |
| `vendor_name` | string | |
| `country` | string | |
| `lead_time_days_avg` | int | |
| `otif_score_last_6m` | float | |

### dim_location
| Cột | Kiểu | Mô tả |
|---|---|---|
| `location_id` | string (PK) | |
| `location_name` | string | "WH HCM Main", "Store A1", "Shopee FBS"… |
| `location_type` | enum | warehouse / store / 3pl / virtual |

---

## Fact tables (gold layer)

### fact_sales (Demand input)
- **Grain:** 1 row = (date × channel × SKU)
- Cột chính: `date_id`, `channel_id`, `style_id`, `color_id`, `size_id`, `sales_qty_pcs`, `gross_revenue_vnd`, `net_revenue_vnd`, `discount_vnd`

### fact_forecast (Demand output)
- **Grain:** 1 row = (forecast_version × week × channel × style × color)
- Cột chính: `forecast_version`, `iso_week`, `channel_id`, `style_id`, `color_id`, `forecast_qty_pcs`, `confidence_level`

### fact_po (Supply)
- **Grain:** 1 row = (po_number × line_no × SKU)
- Cột chính: `po_number`, `po_line_no`, `vendor_id`, `style_id`, `color_id`, `size_id`, `order_qty_pcs`, `unit_cost_vnd`, `po_date`, `requested_delivery_date`, `confirmed_delivery_date`, `intake_date`, `inbound_date`, `status`

### fact_inventory_snapshot
- **Grain:** 1 row = (snapshot_date × location × SKU)
- Cột chính: `snapshot_date`, `location_id`, `style_id`, `color_id`, `size_id`, `soh_qty_pcs`, `intransit_qty_pcs`, `reserved_qty_pcs`, `available_qty_pcs`

### fact_inventory_health (derived)
- **Grain:** 1 row = (snapshot_date × channel × SKU)
- Cột chính: `snapshot_date`, `channel_id`, `style_id`, `color_id`, `size_id`, `wos`, `turnover_ytd`, `str_pct`, `aging_bucket`, `health_status`

---

## Fact tables bổ sung (v0.3 — full module coverage)

> Spec chi tiết từng fact: `21_Pipelines/gold/gold_<module>.md`

| Fact | Module | Grain | Spec |
|---|---|---|---|
| `fact_forecast_avf` | Demand | week × channel × style × color | `gold/gold_demand.md` |
| `fact_replenishment` | Demand | plan_date × location × SKU | `gold/gold_demand.md` |
| `fact_otb` | S&OP | period × otb_version × channel × division × category | `gold/gold_sop_assortment_allocation.md` |
| `fact_plan_vs_actual` | S&OP | period × channel × division × category | `gold/gold_sop_assortment_allocation.md` |
| `fact_assortment_plan_vs_actual` | Assortment | season × channel × category × sub_cat | `gold/gold_sop_assortment_allocation.md` |
| `fact_option_performance` | Assortment | snapshot × style × color | `gold/gold_sop_assortment_allocation.md` |
| `fact_allocation` | Allocation | alloc_id × SKU × to_location | `gold/gold_sop_assortment_allocation.md` |
| `fact_transfer` | Allocation | transfer_id × SKU | `gold/gold_sop_assortment_allocation.md` |

> **Note layer:** `fact_otb` và `fact_plan_vs_actual` là 2 fact duy nhất grain dừng ở L1 (category) — module S&OP không drill L2 (xem `02_Data_Contracts/sop/README.md`).
