# Data Contract — Pack / Breakpack

## Mục đích
Định nghĩa cấu trúc pre-pack (đóng bộ size từ vendor) và rule break-pack (mở chia lẻ ở WH).

## Pre-pack vs Break-pack

| Loại | Đặc điểm | Cost | Khi nào dùng |
|---|---|---|---|
| **Pre-pack** | Vendor đóng theo bộ size định sẵn | Thấp (vendor đã đóng) | Style core/basic, size curve ổn định |
| **Break-pack** | WH mở pack chia lẻ theo size cần | Cao (labor + repack) | Fashion-forward, replen lẻ |

## Source — Pack Definition
- File: `21_Pipelines/bronze/master/pack_definition_<season>.xlsx`
- Owner: Merchandiser + Supply
- Tần suất: Pre-season

## Schema — Pack Definition

| Cột | Kiểu | Mô tả |
|---|---|---|
| `pack_id` | string PK | "PACK-SS26-TOPS-STD" |
| `pack_name` | string | "Standard Tops Pack" |
| `season` | string | "SS26" |
| `category` | string | "Tops" |
| `pack_size_total` | int | Tổng pcs trong 1 pack |
| `s_qty` / `m_qty` / `l_qty` / `xl_qty` / `xxl_qty` | int | Số pcs mỗi size trong pack |
| `is_active` | bool | |

## Ví dụ

```
pack_id            | total | S | M | L | XL | XXL
PACK-SS26-TOPS-STD | 12    | 2 | 3 | 4 | 2  | 1
PACK-SS26-TOPS-WMN | 10    | 2 | 3 | 3 | 2  | 0
PACK-SS26-BTM-CHI  | 18    | 0 | 3 | 4 | 4  | 4  (jeans theo waist)
```

## Break-pack rule

| Trigger | Action |
|---|---|
| Replen qty < pack_size_total | Break pack |
| Specific size stockout (chỉ 1 size cần) | Break pack |
| Store mới open + cần test market | Break pack |

## Cost tracking

Cost per break-pack = labor 30 phút × WH labor rate (vd 50,000 VND/hour) = ~25,000 VND/pack.
→ Track tổng break-pack cost monthly trong Inventory cost slide.

## Downstream
- Applied trong allocation logic: `is_breakpack = true` flag ở `initial_allocation_contract.md`
- Cost feed vào `vw_warehouse_cost_monthly` (skill `supply-execution-tracker`)
