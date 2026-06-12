# Data Contract — Replenishment Plan

## Mục đích
Output đề xuất bổ hàng (qty + timing) cho từng SKU × location dựa trên forecast, SOH, intransit, lead time, safety stock.

## Source
Derived. Tính từ:
- `fact_forecast` (forecast 4-8 tuần tới)
- `fact_inventory_snapshot` (SOH + intransit hiện tại)
- `dim_product` (lead_time, safety_stock policy)

## Grain
1 row = (`plan_date`, `location_id`, `style_id`, `color_id`, `size_id`)

## Schema

| Cột | Kiểu | Mô tả |
|---|---|---|
| `plan_date` | date | Ngày chạy replenishment |
| `location_id` | string | Kho/cửa hàng cần bổ |
| `style_id`, `color_id`, `size_id` | string | SKU |
| `soh_qty_pcs` | int | Tồn hiện tại |
| `intransit_qty_pcs` | int | Đang về |
| `forecast_4w_qty_pcs` | int | Forecast 4 tuần tới |
| `safety_stock_pcs` | int | Theo policy |
| `target_stock_pcs` | int | forecast_4w + safety_stock |
| `replenish_qty_pcs` | int | max(0, target - soh - intransit) — làm tròn MOQ |
| `priority` | enum | critical / high / med / low |
| `expected_otd_date` | date | plan_date + lead_time |

## Logic phân loại priority

| Priority | Điều kiện |
|---|---|
| **critical** | WOS hiện tại ≤ 1 tuần + style đang core/active |
| **high** | WOS 1-2 tuần |
| **med** | WOS 2-4 tuần |
| **low** | WOS > 4 tuần nhưng dưới target |

## Downstream
- Trigger PR (Purchase Request) — xem `supply/pr_contract.md`
- Dashboard: Demand Dashboard tab "Replenishment Queue"
