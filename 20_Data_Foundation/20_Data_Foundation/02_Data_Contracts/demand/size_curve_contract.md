# Data Contract — Size Curve

## Mục đích
Phân bổ tỷ trọng theo size trong cùng style+color. Dùng để break forecast/buy xuống cấp SKU đầy đủ.

## Source
- File raw: `21_Pipelines/bronze/demand/size_curve_<season>.xlsx`
- Owner: Merchandiser + Planner
- Tần suất: 1 lần/mùa pre-season, review giữa mùa qua actual size sell-through

## Grain
1 row = (`season`, `category`, `style_id`, `color_id`, `size_id`)

> **Note:** Cho phép size curve ở cấp `category` (default) và override ở cấp `style+color` (cho style đặc biệt).

## Schema

| Cột | Kiểu | Mô tả | Ví dụ |
|---|---|---|---|
| `season` | string | | "SS26" |
| `category` | string | "Tops", "Bottoms"… | "Tops" |
| `style_id` | string nullable | Null = default cho category | "STY-00123" |
| `color_id` | string nullable | Null = default cho style | null |
| `size_id` | string | | "M" |
| `mix_pct` | float | % trong style+color | 28.0 |
| `is_active_size` | bool | Size có sản xuất không | true |
| `source_logic` | enum | historical / market_benchmark / override | "historical" |

## Validation
- `sum(mix_pct) = 100` trong cùng `(season, style_id or category, color_id or null)`
- Mỗi style+color phải resolve được về 1 size curve (theo priority: style+color > style > category default)

## Ví dụ default cho Tops

```
season | category | size_id | mix_pct
SS26   | Tops     | S       | 18
SS26   | Tops     | M       | 28
SS26   | Tops     | L       | 32
SS26   | Tops     | XL      | 17
SS26   | Tops     | XXL     | 5
```

## Downstream
- Apply vào forecast → SKU forecast đầy đủ size
- Apply vào buy plan → tạo PO theo size breakdown
- Dashboard: Demand Dashboard tab "Size Mix" so sánh planned vs actual
