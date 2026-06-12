# Data Contract — Color Curve

## Mục đích
Phân bổ tỷ trọng forecast/buy theo color trong cùng một style. Dùng để break forecast cấp style xuống cấp style+color.

## Source
- File raw: `21_Pipelines/bronze/demand/color_curve_<season>.xlsx`
- Owner: Merchandiser
- Tần suất: 1 lần/mùa (pre-season) + review giữa mùa nếu lệch

## Grain
1 row = (`season`, `style_id`, `color_id`)

## Schema

| Cột | Kiểu | Mô tả | Ví dụ |
|---|---|---|---|
| `season` | string | "SS26", "FW26" | "SS26" |
| `style_id` | string | | "STY-00123" |
| `color_id` | string | | "COL-NAVY" |
| `color_name` | string | | "Navy" |
| `mix_pct` | float | % trong tổng style (0-100) | 35.0 |
| `is_core_color` | bool | Color phải có hàng quanh năm | true |
| `rank` | int | 1 = bán nhất, 2, 3… | 1 |
| `source_logic` | enum | historical / merchandiser_judgment / market_trend | "historical" |

## Validation
- `sum(mix_pct) = 100` cho mỗi `(season, style_id)`
- Phải có ít nhất 1 color với `is_core_color = true`

## Ví dụ

```
season | style_id  | color_id  | mix_pct | rank
SS26   | STY-00123 | COL-NAVY  | 35.0    | 1
SS26   | STY-00123 | COL-WHITE | 30.0    | 2
SS26   | STY-00123 | COL-BLACK | 25.0    | 3
SS26   | STY-00123 | COL-RED   | 10.0    | 4
```

## Downstream
- Apply vào forecast cấp style → ra forecast cấp style+color
- Apply vào buy plan pre-season (xem skill `merchandise-planning-preseason`)
