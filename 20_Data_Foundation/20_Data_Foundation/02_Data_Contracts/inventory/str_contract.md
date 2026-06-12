# Data Contract — STR (Sell-Through Rate)

## Mục đích
Đo % hàng đã bán so với hàng đã nhập trong cùng kỳ. KPI quan trọng nhất cho fashion theo mùa.

## Grain
- **L1:** (`season`, `channel_id`, `category`)
- **L2:** (`season`, `style_id`, `color_id`) hoặc xuống size

## Công thức

**STR season-to-date:**
```
STR = total_sales_qty_since_launch / total_intake_qty_since_launch × 100
```

**STR target curve theo % vào mùa:**

| % tuần đã vào mùa | STR target (fashion thường) | STR target (fashion-forward) |
|---|---|---|
| 25% | 20-25% | 25-30% |
| 50% | 45-55% | 55-65% |
| 75% | 70-80% | 80-90% |
| 100% (end of season) | 85-90% | 90-95% |

## Schema

| Cột | Kiểu | Mô tả |
|---|---|---|
| `season` | string | "SS26" |
| `snapshot_date` | date | |
| `style_id`, `color_id` | string | |
| `weeks_in_season` | int | Số tuần đã vào mùa |
| `total_in_season_weeks` | int | Tổng số tuần mùa (vd 26) |
| `pct_season_elapsed` | float | weeks_in_season / total × 100 |
| `qty_intake_total` | int | Tổng hàng đã nhập (cộng dồn từ launch) |
| `qty_sold_total` | int | Tổng bán cộng dồn |
| `str_pct` | float | qty_sold / qty_intake × 100 |
| `str_target_pct` | float | Tra từ STR target curve |
| `str_variance_pct` | float | str_pct - str_target_pct |
| `str_status` | enum | ahead / on_track / behind / critical |

## Classification

| Status | Điều kiện |
|---|---|
| **ahead** | `str_variance_pct >= +10%` |
| **on_track** | `-5% <= str_variance_pct < +10%` |
| **behind** | `-15% <= str_variance_pct < -5%` |
| **critical** | `str_variance_pct < -15%` |

## Downstream
- Dashboard: Inventory Dashboard tab "Season STR"
- Skill: `demand-analysis-inseason` (sẵn có) — flag style nào critical để markdown sớm
