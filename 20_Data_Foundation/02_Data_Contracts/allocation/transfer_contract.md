# Data Contract — Inter-Location Transfer

## Mục đích
Theo dõi việc chuyển hàng giữa các location (store ↔ store, WH ↔ store, WH ↔ marketplace FBS). Dùng để balance stock và rescue from overstock locations.

## Source
- File raw: `21_Pipelines/bronze/allocation/transfer_log_<YYYYMM>.csv`
- Owner: WH Ops / Allocator
- Tần suất: Daily append

## Grain
1 row = (`transfer_id`, `style_id`, `color_id`, `size_id`)

## Schema

| Cột | Kiểu | Mô tả |
|---|---|---|
| `transfer_id` | string PK | "TR-2026-0612-001" |
| `transfer_date` | date | Ngày tạo TO |
| `from_location_id` | string | |
| `to_location_id` | string | |
| `style_id`, `color_id`, `size_id` | string | |
| `qty_requested_pcs` | int | |
| `qty_shipped_pcs` | int | |
| `qty_received_pcs` | int | |
| `ship_date` | date | |
| `receive_date` | date | |
| `transfer_reason` | enum | rebalance / overstock_rescue / new_store_init / emergency_stockout / mistake_correction |
| `transfer_cost_vnd` | bigint | Cost ship + handling |
| `status` | enum | requested / picked / shipped / in_transit / received / cancelled |

## KPI

| KPI | Công thức |
|---|---|
| **Transfer cycle time** | `receive_date - transfer_date` (days) |
| **Transfer Cost % of Revenue** | `sum(transfer_cost) / sum(net_revenue) × 100` |
| **Rebalance Frequency** | # transfers cùng SKU trong 30 ngày (high = allocation logic kém) |
| **Transfer Fulfillment %** | `qty_received / qty_requested` |

## Anti-pattern (flag để debug)

- SKU transfer 2+ lần trong 30 ngày → allocation logic ban đầu sai
- Transfer cost > 3% revenue ở category → re-evaluate location strategy
- Transfer chỉ 1 chiều liên tục (vd HCM → HN) → demand forecast lệch
