# Data Contract — SOH Snapshot

## Mục đích
Snapshot tồn kho theo SKU × Location tại từng thời điểm. Base cho mọi inventory KPI.

## Source
- File raw: `21_Pipelines/bronze/inventory/soh_<YYYYMMDD>.csv`
- Owner: WMS / Inventory team
- Tần suất: Daily snapshot (cuối ngày), weekly cho dashboard tổng hợp

## Grain
1 row = (`snapshot_date`, `location_id`, `style_id`, `color_id`, `size_id`)

## Schema

| Cột | Kiểu | Mô tả |
|---|---|---|
| `snapshot_date` | date | EoD date |
| `location_id` | string (FK) | WH / Store / Marketplace FBS |
| `style_id`, `color_id`, `size_id` | string | |
| `soh_qty_pcs` | int | On-hand thực tế |
| `intransit_qty_pcs` | int | Đang về (POs đã ship chưa GRN) |
| `reserved_qty_pcs` | int | Đã allocate cho đơn |
| `available_qty_pcs` | int | soh - reserved |
| `unit_cost_vnd` | bigint | FIFO unit cost trung bình |
| `soh_value_vnd` | bigint | soh_qty × unit_cost |

## Validation
- `soh_qty_pcs >= 0`
- `available_qty_pcs = soh_qty_pcs - reserved_qty_pcs` (>= 0)
- Mỗi SKU × Location 1 row/snapshot
