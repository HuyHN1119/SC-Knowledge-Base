# Data Contract — Assortment Plan

## Mục đích
Plan tổng option count + buy value + average depth per (season × channel × category × sub_category). Là output chính của pre-season buy meeting.

## Source
- File raw: `21_Pipelines/bronze/assortment/assortment_plan_<season>.xlsx`
- Owner: Merchandiser / Head of Buying
- Tần suất: 1 lần/mùa pre-season + review at mid-season

## Grain
1 row = (`season`, `channel_id`, `division`, `category`, `sub_category`)

## Schema

| Cột | Kiểu | Bắt buộc | Mô tả |
|---|---|---|---|
| `season` | string | Y | "SS26" |
| `channel_id` | string | Y | "ALL_ONLINE" / "STORE_FLAGSHIP" / "SHOPEE"… |
| `division` | string | Y | |
| `category` | string | Y | |
| `sub_category` | string | Y | |
| `option_count_target` | int | Y | Số options sẽ mua |
| `option_count_new` | int | Y | Trong đó bao nhiêu options mới (vs core repeat) |
| `option_count_core_repeat` | int | Y | Carry-over từ mùa trước |
| `avg_depth_pcs` | int | Y | qty trung bình per option |
| `buy_qty_total_pcs` | int | Y | option_count × avg_depth |
| `buy_value_total_vnd` | bigint | Y | OTB ngân sách |
| `width_vs_depth_strategy` | enum | Y | width_heavy / balanced / depth_heavy |
| `core_pct` | float | Y | % qty core options |
| `fashion_pct` | float | Y | % qty fashion-forward |
| `seasonal_pct` | float | Y | % qty seasonal-only |
| `markdown_provision_pct` | float | Y | % expected markdown budget |
| `target_str_pct` | float | Y | STR target end-of-season |

## Validation
- `option_count_new + option_count_core_repeat = option_count_target`
- `core_pct + fashion_pct + seasonal_pct = 100`
- `buy_qty_total = option_count × avg_depth` (approximate, ±5%)

## Ví dụ

```
season | channel       | category | sub_cat | option_target | new | core_repeat | avg_depth | buy_qty | buy_value (VND)
SS26   | STORE_FLAGSHIP| Tops     | Polo    | 25            | 12  | 13          | 350       | 8,750   | 1.31B
SS26   | SHOPEE        | Tops     | Polo    | 18            | 18  | 0           | 200       | 3,600   | 540M
SS26   | STORE_FLAGSHIP| Bottoms  | Chinos  | 15            | 6   | 9           | 280       | 4,200   | 1.05B
```

## Downstream
- Feed vào OTB calculation (`15_Integrated_Business_Planning` / `merchandise-planning-preseason` skill)
- Drive `option_plan_contract.md` chi tiết
- Compare actual options bought vs plan at PO commitment stage
- Mid-season review: actual STR per category vs target

## Anti-pattern
- ❌ Option count cao + avg depth thấp + STR target cao → mâu thuẫn, sẽ stockout sớm
- ❌ 100% new option = risk markdown lớn (cần core_repeat tối thiểu 30%)
- ❌ Channel SHOPEE và STORE có cùng assortment plan = không tối ưu (channel khác nhau)
