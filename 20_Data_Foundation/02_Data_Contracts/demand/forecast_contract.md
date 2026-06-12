# Data Contract — Forecast

## Mục đích
Lưu kết quả forecast (Statistical baseline + Consensus sau Demand Review) cho từng tuần × channel × style × color.

## Source
- File raw: `21_Pipelines/bronze/demand/forecast_<YYYYMM>_v<n>.xlsx`
- Owner: Demand Planner
- Tần suất: Hàng tuần (rolling 13-26 tuần), refresh chính thức hàng tháng tại Demand Review (W1 IBP cycle)

## Grain
1 row = (`forecast_version` × `iso_week` × `channel_id` × `style_id` × `color_id`)

> **Lý do không đến size:** Forecast cấp style+color, size sẽ apply qua `size_curve` riêng (xem `size_curve_contract.md`).

## Schema

| Cột | Kiểu | Bắt buộc | Mô tả | Ví dụ |
|---|---|---|---|---|
| `forecast_version` | string | Y | "STAT_v1" / "CONSENSUS_2026M06" | "CONSENSUS_2026M06" |
| `forecast_date` | date | Y | Ngày tạo bản forecast này | 2026-06-05 |
| `iso_week` | int | Y | Tuần được forecast (1-53) | 25 |
| `iso_year` | int | Y | Năm của tuần đó | 2026 |
| `channel_id` | string (FK) | Y | Join dim_channel | "SHOPEE" |
| `style_id` | string (FK) | Y | Join dim_product | "STY-00123" |
| `color_id` | string (FK) | Y | Join dim_product | "COL-NAVY" |
| `forecast_qty_pcs` | int | Y | Số lượng forecast | 250 |
| `forecast_revenue_vnd` | bigint | N | Revenue forecast | 75000000 |
| `confidence_level` | enum | N | high / med / low | "med" |
| `forecast_method` | enum | N | stat / consensus / override | "consensus" |
| `notes` | string | N | Ghi chú override | "Push 6.6 promo" |

## Primary Key
`(forecast_version, iso_year, iso_week, channel_id, style_id, color_id)`

## Validation rules
- `forecast_qty_pcs >= 0`
- Mỗi `(channel_id, style_id, color_id, iso_year, iso_week)` chỉ có 1 row cho mỗi version
- `confidence_level` bắt buộc nếu `forecast_method = "override"`

## Ví dụ 3 row

```
forecast_version  | iso_week | channel_id | style_id   | color_id  | forecast_qty_pcs
CONSENSUS_2026M06 | 25       | SHOPEE     | STY-00123  | COL-NAVY  | 250
CONSENSUS_2026M06 | 25       | TIKTOK     | STY-00123  | COL-NAVY  | 180
CONSENSUS_2026M06 | 25       | WEBSITE    | STY-00123  | COL-NAVY  | 60
```

## Downstream
- `fact_forecast` ở `21_Pipelines/gold/`
- Dashboard: Demand Dashboard (xem `22_Dashboards_Reports/demand_dashboard.md`)
- Skill: `demand-review-pack`
