# Data Contract — WOS (Weeks of Supply)

## Mục đích
Đo bao nhiêu tuần hàng đang có đủ bán theo velocity hiện tại. KPI sống còn để quyết định reorder.

## Source
Derived. Tính từ `fact_inventory_snapshot` + `fact_sales` 4-8 tuần gần nhất.

## Grain
Hỗ trợ cả 2 layer:
- **L1:** (`snapshot_date`, `channel_id`, `division`, `category`, `sub_category`)
- **L2:** (`snapshot_date`, `channel_id`, `style_id`, `color_id`)

## Schema (L2 view)

| Cột | Kiểu | Mô tả |
|---|---|---|
| `snapshot_date` | date | |
| `channel_id` | string | |
| `style_id`, `color_id` | string | |
| `total_available_qty_pcs` | int | sum across all locations serving channel |
| `intransit_qty_pcs` | int | |
| `avg_weekly_sales_4w` | float | mean(sales) 4 tuần gần nhất |
| `avg_weekly_sales_8w` | float | mean(sales) 8 tuần gần nhất |
| `wos_current` | float | available / avg_weekly_sales_4w |
| `wos_with_intransit` | float | (available + intransit) / avg_weekly_sales_4w |
| `wos_classification` | enum | overstock / healthy / low / critical / dead |

## Classification logic

| Class | Điều kiện | Action |
|---|---|---|
| **overstock** | `wos > 12` | Markdown/clearance |
| **healthy** | `4 <= wos <= 12` | Hold |
| **low** | `2 <= wos < 4` | Reorder |
| **critical** | `wos < 2` AND style = active/core | Chase vendor / express ship |
| **dead** | `wos = inf` (avg_weekly_sales_4w = 0) AND `days_since_last_sale > 90` | Clearance/write-off |

## Downstream
- Dashboard: Inventory Dashboard tab "WOS Heatmap"
- Skill: `inventory-health-check` — flag critical & overstock list mỗi tuần
- Feed vào: `replenishment_contract.md`
