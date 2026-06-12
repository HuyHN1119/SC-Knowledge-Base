# Data Contract — Replenishment Allocation

## Mục đích
Phân bổ hàng từ WH tổng đến store/channel khi store đạt ngưỡng min stock (theo WOS).

Khác với `demand/replenishment_contract.md` (đề xuất re-order từ vendor), file này quyết định **lấy hàng có sẵn ở WH** phân về đâu.

## Source
- Derived. Tính từ:
  - `fact_inventory_snapshot` (WH tổng + locations)
  - `fact_sales` 4-8 tuần per location
  - `dim_product` (min_stock policy per location)

## Grain
1 row = (`plan_date`, `from_location_id`, `to_location_id`, `style_id`, `color_id`, `size_id`)

## Schema

| Cột | Kiểu | Mô tả |
|---|---|---|
| `plan_date` | date | Ngày chạy replen alloc |
| `from_location_id` | string | WH tổng |
| `to_location_id` | string | Store/channel cần bổ |
| `style_id`, `color_id`, `size_id` | string | SKU |
| `to_loc_soh_pcs` | int | SOH tại to_location |
| `to_loc_intransit_pcs` | int | Đang về to_location |
| `to_loc_avg_weekly_sales` | float | Velocity 4 tuần |
| `to_loc_wos_current` | float | WOS hiện tại |
| `wh_available_pcs` | int | Có sẵn ở WH tổng |
| `min_stock_target` | int | Target floor stock |
| `replen_qty_recommended` | int | Đề xuất chia về |
| `replen_qty_approved` | int | Số duyệt thực tế (sau capacity check) |
| `priority` | enum | critical / high / med / low |
| `pack_unit` | int | Đóng theo bao nhiêu pcs/pack |

## Priority logic

| Priority | Tiêu chí |
|---|---|
| **critical** | `wos_current < 1` AND style = active/core AND velocity > 0 |
| **high** | `wos_current` 1-2 weeks |
| **med** | `wos_current` 2-3 weeks |
| **low** | `wos_current` 3-4 weeks |

→ Sau khi sort theo priority, capacity ở WH chia theo `replen_qty_recommended × priority_weight`.

## KPI

- **Replen Cycle Time**: từ `plan_date` đến hàng available ở to_location
- **Replen Fulfillment %**: replen_qty_approved / replen_qty_recommended

## Downstream
- Generate Transfer Order trong WMS
- Update intransit ở to_location
