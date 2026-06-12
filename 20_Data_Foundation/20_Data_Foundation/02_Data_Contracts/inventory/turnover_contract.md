# Data Contract — Inventory Turnover

## Mục đích
Đo tốc độ luân chuyển tồn kho. KPI tài chính chính cho Inventory Review.

## Grain
- **L1:** (`period_yyyymm`, `channel_id`, `division`, `category`)
- **L2:** (`period_yyyymm`, `channel_id`, `style_id`)

## Công thức

| Metric | Formula |
|---|---|
| **Turnover (period)** | `COGS_period / avg_inventory_value_period` |
| **Turnover annualized** | `Turnover × (12 / months_in_period)` |
| **DIO (Days Inventory Outstanding)** | `365 / Turnover annualized` |

`avg_inventory_value` = trung bình `soh_value_vnd` đầu kỳ + cuối kỳ.

## Schema

| Cột | Kiểu | Mô tả |
|---|---|---|
| `period_yyyymm` | string | "2026-05" |
| `channel_id`, `division`, `category` (L1) hoặc `style_id` (L2) | string | |
| `cogs_period_vnd` | bigint | |
| `avg_inventory_value_vnd` | bigint | |
| `turnover_period` | float | |
| `turnover_annualized` | float | |
| `dio_days` | float | |

## Benchmark fashion mid-size (Vietnam ecom-heavy)

| Tier | Turnover annualized | DIO |
|---|---|---|
| **Best-in-class** | ≥ 6 | ≤ 60 days |
| **Healthy** | 4-6 | 60-90 days |
| **Watch** | 2-4 | 90-180 days |
| **Poor** | < 2 | > 180 days |

> Note: Benchmark tham khảo, tuỳ category — basic/core có thể 8-10, fashion-forward 3-5.

## Downstream
- Dashboard: Inventory Dashboard tab "Turnover Trends"
- Báo cáo: MBR financial slide
