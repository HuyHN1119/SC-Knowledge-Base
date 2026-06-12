# Data Contract — Stock Health (composite)

## Mục đích
Phân loại 1-trong-N status cho từng SKU dựa trên WOS + STR + Aging + Lifecycle. Là **composite KPI** dùng để colour-code dashboard và prioritize action.

## Grain
- **L1:** (`snapshot_date`, `channel_id`, `division`, `category`)
- **L2:** (`snapshot_date`, `channel_id`, `style_id`, `color_id`, `size_id`)

## Classification matrix

| Health Status | WOS | STR vs target | Aging | Lifecycle |
|---|---|---|---|---|
| **Active-Hot** | 2-4 weeks | Ahead | < 60d | new/core |
| **Active-Healthy** | 4-12 | On track | < 90d | core |
| **Slow** | > 12 | Behind | 90-180d | core/aging |
| **At Risk** | > 16 | Critical | 90-180d | aging |
| **Dead Stock** | inf (no sale 90d) | — | > 180d | clearance |
| **Stockout** | < 0.5 | — | — | core/active |

## Schema

| Cột | Kiểu | Mô tả |
|---|---|---|
| `snapshot_date` | date | |
| `channel_id` | string | |
| `style_id`, `color_id`, `size_id` | string | |
| `wos_current` | float | Từ wos_contract |
| `str_pct` | float | Từ str_contract |
| `aging_bucket` | enum | Từ aging_contract |
| `lifecycle_stage` | enum | Từ dim_product |
| `health_status` | enum | active_hot / active_healthy / slow / at_risk / dead / stockout |
| `recommended_action` | enum | reorder / hold / promote / markdown / clearance / writeoff |
| `priority_score` | int | 1-100, dùng để sort dashboard |

## Downstream
- Dashboard: Inventory Dashboard tab "Stock Health Pareto"
- Skill: `inventory-health-check` — output Top 20 critical + Top 20 overstock mỗi tuần
- Báo cáo: Inventory Review deck (W3 IBP cycle)
