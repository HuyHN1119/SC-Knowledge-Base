# Data Contract — Inventory Aging

## Mục đích
Phân loại tồn kho theo tuổi (kể từ ngày inbound đầu tiên). Base cho markdown decision & write-off.

## Grain
1 row = (`snapshot_date`, `location_id`, `style_id`, `color_id`, `size_id`)

## Schema

| Cột | Kiểu | Mô tả |
|---|---|---|
| `snapshot_date` | date | |
| `location_id` | string | |
| `style_id`, `color_id`, `size_id` | string | |
| `first_inbound_date` | date | GRN date đầu tiên của SKU tại location này |
| `age_days` | int | snapshot_date - first_inbound_date |
| `aging_bucket` | enum | 0_30 / 31_60 / 61_90 / 91_180 / 181_365 / over_365 |
| `soh_qty_pcs` | int | |
| `soh_value_vnd` | bigint | |
| `provision_pct` | float | Tra theo policy (0% / 25% / 50% / 75% / 100%) |
| `provision_value_vnd` | bigint | soh_value × provision_pct |

## Aging policy (đề xuất fashion mid-size)

| Bucket | Range (days) | Provision | Action mặc định |
|---|---|---|---|
| 0_30 | 0-30 | 0% | Bán bình thường |
| 31_60 | 31-60 | 0% | Theo dõi STR |
| 61_90 | 61-90 | 10% | Promotion soft |
| 91_180 | 91-180 | 25% | Markdown 20-30% |
| 181_365 | 181-365 | 50% | Clearance event |
| over_365 | 365+ | 75-100% | Write-off / outlet |

## Downstream
- Dashboard: Inventory Dashboard tab "Aging Pyramid"
- Report: Monthly aging report cho Finance
- Skill: `inventory-health-check`
