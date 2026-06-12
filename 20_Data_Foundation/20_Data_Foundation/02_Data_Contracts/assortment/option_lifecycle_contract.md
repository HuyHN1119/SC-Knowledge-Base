# Data Contract — Option Lifecycle

## Mục đích
Theo dõi lifecycle stage của từng option qua thời gian. Drive in-season action (reorder/markdown/clearance/EOL).

## Grain
1 row = (`snapshot_date`, `style_id`, `color_id`)

## Schema

| Cột | Kiểu | Mô tả |
|---|---|---|
| `snapshot_date` | date | |
| `style_id`, `color_id` | string | |
| `season` | string | Mùa launch |
| `weeks_in_market` | int | Số tuần đã bán |
| `lifecycle_stage` | enum | pre_launch / launch / ramp_up / peak / decline / clearance / eol |
| `current_str_pct` | float | STR season-to-date |
| `current_velocity_4w` | float | avg sales/week 4w |
| `velocity_trend` | enum | accelerating / steady / decelerating |
| `recommended_action` | enum | reorder / hold / promote / markdown / clearance / eol |
| `markdown_status` | enum | full_price / mkd_first / mkd_second / clearance |
| `markdown_pct` | float | Current discount % |

## Lifecycle stages

| Stage | Tuần in market | Action mặc định |
|---|---|---|
| **pre_launch** | -2 to 0 | Marketing prep |
| **launch** | 0-2 | Test, monitor velocity |
| **ramp_up** | 2-4 | Reorder nếu velocity > expected |
| **peak** | 4-12 (basic) / 4-8 (fashion) | Maintain stock |
| **decline** | 12+ (basic) / 8+ (fashion) | Promote, no reorder |
| **clearance** | str_critical OR end-of-season | Markdown deep |
| **eol** | sold out OR write-off | Remove from active |

## State transition rules

| Hiện tại | Điều kiện chuyển | Sang stage |
|---|---|---|
| launch | weeks_in_market > 2 | ramp_up |
| ramp_up | velocity_trend = accelerating + reorder triggered | peak |
| peak | velocity_trend = decelerating 2 tuần liên tiếp | decline |
| decline | str_critical OR weeks > category threshold | clearance |
| any | available_qty = 0 + no replen plan | eol |

## Downstream
- Drive skill `demand-analysis-inseason` actions
- Feed vào markdown calendar
- Update `dim_product.lifecycle_stage`
