# Data Contract — Option Plan (Detail)

## Mục đích
Chi tiết list từng option (style+color) sẽ mua trong mùa. Output cuối cùng của assortment process trước khi cut PO.

## Grain
1 row = (`season`, `style_id`, `color_id`)

## Schema

| Cột | Kiểu | Bắt buộc | Mô tả |
|---|---|---|---|
| `season` | string | Y | |
| `style_id` | string | Y | |
| `color_id` | string | Y | |
| `style_code` | string | N | Display |
| `style_name` | string | N | |
| `color_name` | string | N | |
| `division`, `category`, `sub_category` | string | Y | |
| `option_type` | enum | Y | core_repeat / new_basic / new_fashion / seasonal |
| `is_carry_over` | bool | Y | Repeat từ mùa trước |
| `prior_season_str_pct` | float | N | STR mùa trước (nếu carry over) |
| `target_channels` | string list | Y | Channels sẽ bán (vd "STORE,SHOPEE,WEBSITE") |
| `buy_qty_pcs` | int | Y | Tổng depth |
| `buy_value_vnd` | bigint | Y | qty × cost |
| `target_retail_price_vnd` | int | Y | |
| `target_margin_pct` | float | Y | |
| `target_str_pct` | float | Y | |
| `phasing` | enum | Y | wave_1 / wave_2 / wave_3 |
| `intake_target_week` | int | Y | iso_week vendor giao về |
| `markdown_strategy` | enum | N | full_price_only / planned_50_50 / clearance_early |

## Validation
- Sum(`buy_qty`) per category match `assortment_plan_contract.buy_qty_total_pcs` (±5%)
- `target_margin_pct >= category floor margin`
- `intake_target_week` không trễ hơn 50% mùa

## Phasing logic

| Wave | % buy | Khi nào về |
|---|---|---|
| Wave 1 | 40-50% | Tuần 1-3 mùa (launch hot) |
| Wave 2 | 30-40% | Tuần 4-8 (peak) |
| Wave 3 | 15-25% | Tuần 9-14 (replen/sustain) |

→ Quá phụ thuộc Wave 1 = risk stockout. Quá phụ thuộc Wave 3 = trễ mùa.

## Downstream
- Cut PO: option × color_curve × size_curve → SKU-level PO
- Feed vào `forecast_contract.md` (forecast cấp style+color)
