# Data Contract — Initial Allocation

## Mục đích
Quyết định first-wave phân bổ cho style+color mới sau khi PO đầu tiên về WH tổng. Đây là lần phân bổ quan trọng nhất — quyết định hàng có vào đúng store/channel hot hay không.

## Source
- File raw: `21_Pipelines/bronze/allocation/initial_alloc_<season>_<wave>.xlsx`
- Owner: Allocator / Planner
- Tần suất: Per PO inbound wave (thường 2-3 waves/season cho mỗi style)

## Grain
1 row = (`alloc_id`, `style_id`, `color_id`, `size_id`, `to_location_id`)

## Schema

| Cột | Kiểu | Bắt buộc | Mô tả |
|---|---|---|---|
| `alloc_id` | string (PK) | Y | "ALOC-2026-SS-W1-0001" |
| `alloc_date` | date | Y | Ngày run allocation |
| `wave` | int | Y | 1 = first wave, 2 = second… |
| `from_location_id` | string | Y | WH tổng |
| `to_location_id` | string | Y | Store/channel/marketplace FBS |
| `to_channel_id` | string | Y | Channel mà location phục vụ |
| `style_id`, `color_id`, `size_id` | string | Y | SKU |
| `qty_available_pcs` | int | Y | Tổng hàng có ở WH cho SKU |
| `qty_allocated_pcs` | int | Y | Phân về to_location |
| `qty_pack_unit` | int | N | Nếu allocate theo pre-pack |
| `is_breakpack` | bool | Y | Có break pack không |
| `allocation_logic` | enum | Y | even_split / by_velocity / by_forecast / by_store_grade / manual |
| `store_grade` | enum | N | A / B / C (cho retail) |
| `forecast_ratio` | float | N | % của to_location trong tổng forecast |
| `priority_score` | int | N | 1-100 dùng để sort khi capacity thiếu |
| `status` | enum | Y | draft / approved / picking / shipped / received |

## Allocation logic — mặc định cho fashion mid-size

### Logic 1: by_forecast (recommended cho launch mới)
```
qty_allocated = qty_available × (forecast_to_location / sum(forecast_all_locations))
```

### Logic 2: by_velocity (recommended cho replenish)
```
qty_allocated = qty_available × (avg_sales_4w_to_location / sum(avg_sales_4w))
```

### Logic 3: by_store_grade
| Grade | Multiplier |
|---|---|
| A (top 20% stores) | 1.5x |
| B (next 50%) | 1.0x |
| C (bottom 30%) | 0.5x |

### Logic 4: pack-based
Bắt đầu từ qty target theo logic 1/2, làm tròn xuống bội số pack size, dồn remainder vào store grade A.

## Downstream
- Trigger Transfer Order trong WMS
- Update `fact_inventory_snapshot.intransit_qty_pcs` cho `to_location`
- Feed vào `fact_allocation` ở gold layer

## Anti-pattern
- ❌ Allocate even_split khi launch new style (mất hot store opportunity)
- ❌ Allocate hết qty_available wave 1 (cần giữ 30-40% cho replen wave)
- ❌ Bỏ qua size curve store-specific (size curve khác giữa store HCM và HN)
