# Data Contract — Actual vs Forecast (AvF)

## Mục đích
So sánh thực tế bán (`fact_sales`) với forecast (`fact_forecast`) → tính bias, MAPE, WMAPE theo tuần / tháng / mùa.

## Source
Derived. Không nhận file raw — là **view** trong `21_Pipelines/silver/` join từ:
- `fact_sales` (đã aggregate lên cấp style+color+week+channel)
- `fact_forecast` (consensus version mới nhất tại thời điểm tuần đó)

## Grain
1 row = (`iso_year`, `iso_week`, `channel_id`, `style_id`, `color_id`)

## Schema

| Cột | Kiểu | Mô tả |
|---|---|---|
| `iso_year` | int | |
| `iso_week` | int | |
| `channel_id` | string | |
| `style_id` | string | |
| `color_id` | string | |
| `actual_qty_pcs` | int | Từ fact_sales |
| `forecast_qty_pcs` | int | Từ fact_forecast (consensus version locked đầu tháng) |
| `forecast_version_used` | string | "CONSENSUS_2026M05" — phiên bản dùng để so sánh |
| `error_qty_pcs` | int | actual - forecast |
| `abs_error_qty_pcs` | int | abs(error) |
| `ape_pct` | float | abs_error / actual × 100 (chỉ tính khi actual > 0) |
| `bias_pct` | float | (actual - forecast) / forecast × 100 |

## KPI tổng hợp (tính ở gold)

| KPI | Công thức | Layer |
|---|---|---|
| **MAPE** | `mean(ape_pct)` trong cùng grain | L1 + L2 |
| **WMAPE** | `sum(abs_error_qty_pcs) / sum(actual_qty_pcs) × 100` | L1 + L2 |
| **Bias** | `mean(bias_pct)` — dương = under-forecast, âm = over-forecast | L1 + L2 |
| **Hit Rate** | % rows có `ape_pct <= 20%` | L1 |

## Benchmark fashion mid-size

| Time horizon | WMAPE target | Bias target |
|---|---|---|
| 1-4 tuần ahead | ≤ 20% | ±5% |
| 5-13 tuần ahead | ≤ 35% | ±10% |
| 14+ tuần ahead | ≤ 50% | ±15% |

## Downstream
- Dashboard: Demand Dashboard tab "Forecast Performance"
- Skill: `demand-review-pack` — tự động flag style nào WMAPE > target → đưa vào Demand Review deck.
