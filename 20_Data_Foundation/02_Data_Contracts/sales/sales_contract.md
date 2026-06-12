# Data Contract — Sales (Order Line, All Channels)

## Mục đích
Schema chuẩn hoá cho sales từ 4 nguồn (Shopee, TikTok, Website, POS) sau khi union tại silver. Đây là contract cho `stg_sales` — input của `fact_sales`, nền của mọi KPI demand & inventory.

## Source
- File raw: `21_Pipelines/bronze/sales/<source>_<dateRange>.csv` với source ∈ {shopee, tiktok, website, pos}
- Owner: Ecom team (3 nguồn online) + Retail Ops (POS)
- Tần suất: Weekly (target: daily khi có ETL tự động)

## Grain
1 row = 1 order line (`order_id` × `line_no`) — 1 SKU trong 1 đơn

## Schema (sau chuẩn hoá — silver)

| Cột | Kiểu | Bắt buộc | Mô tả | Ví dụ |
|---|---|---|---|---|
| `order_id` | string | Y | ID đơn từ source, prefix source: "SPE-", "TTS-", "WEB-", "POS-" | "SPE-2406151234" |
| `line_no` | int | Y | Số dòng trong đơn | 1 |
| `order_date` | date | Y | Ngày đặt (online) / ngày bán (POS) | 2026-06-06 |
| `channel_id` | string (FK) | Y | Join dim_channel | "SHOPEE" |
| `location_id` | string (FK) | N | Store bán (POS) / warehouse fulfil (online) | "STORE_A1" |
| `style_id` | string (FK) | Y | Join dim_product | "STY-00123" |
| `color_id` | string (FK) | Y | | "COL-NAVY" |
| `size_id` | string (FK) | Y | | "SIZE-L" |
| `sales_qty_pcs` | int | Y | Âm nếu là return | 2 |
| `gross_revenue_vnd` | bigint | Y | Giá niêm yết × qty | 700000 |
| `discount_vnd` | bigint | Y | Tổng discount line (voucher + markdown) | 140000 |
| `net_revenue_vnd` | bigint | Y | gross − discount | 560000 |
| `platform_fee_vnd` | bigint | N | Phí sàn (Shopee/TikTok) | 45000 |
| `order_status` | enum | Y | completed / cancelled / returned | "completed" |
| `is_promo_order` | bool | N | Đơn trong campaign (6.6, 11.11…) | true |
| `_source` | string | Y | shopee / tiktok / website / pos | "shopee" |
| `_ingested_at` | timestamp | Y | Thời điểm ingest | 2026-06-08 06:00 |

## Primary Key
`(order_id, line_no)`

## Mapping từ source → chuẩn

| Cột chuẩn | Shopee | TikTok | Website (Haravan) | POS |
|---|---|---|---|---|
| `order_id` | Mã đơn hàng | Order ID | Order number | Số hoá đơn |
| `order_date` | Ngày đặt hàng | Created time | Created at | Ngày bán |
| `sales_qty_pcs` | Số lượng | Quantity | Quantity | SL |
| `net_revenue_vnd` | Tổng giá bán − voucher | Total after discount | Total line price | Thành tiền |
| SKU map | SKU phân loại hàng → tách style/color/size qua product_hierarchy | Seller SKU | Variant SKU | Mã SP |

> Chi tiết mapping từng cột nguồn xem `21_Pipelines/silver/silver_inventory_sales.md`.

## Validation rules
- `net_revenue_vnd = gross_revenue_vnd − discount_vnd` (±1000 VND làm tròn)
- `order_status = cancelled` → loại khỏi fact_sales (giữ ở silver để audit)
- `sales_qty_pcs < 0` chỉ hợp lệ khi `order_status = returned`
- SKU không match dim_product → quarantine, ping merchandiser
- Duplicate `(order_id, line_no)` giữa 2 lần drop file → keep latest `_ingested_at`

## Ví dụ 3 row

```
order_id        | line | channel | style_id  | color_id | size_id | qty | net_rev  | status
SPE-2406151234  | 1    | SHOPEE  | STY-00123 | COL-NAVY | SIZE-L  | 2   | 560,000  | completed
TTS-88421       | 1    | TIKTOK  | STY-00123 | COL-NAVY | SIZE-M  | 1   | 275,000  | completed
POS-A1-0612-045 | 2    | STORE_A1| STY-00087 | COL-WHITE| SIZE-XL | -1  | -420,000 | returned
```

## Downstream
- `fact_sales` ở gold (grain aggregate: date × channel × SKU)
- Mọi KPI: WMAPE (AvF), WOS, STR, Turnover, Health
- Dashboards: tất cả

## Anti-pattern
- ❌ Tính revenue từ gross thay vì net → phồng doanh thu 15-25% (discount fashion ecom lớn)
- ❌ Bỏ return ra khỏi data → STR/WOS sai
- ❌ Để order cancelled trong fact_sales
- ❌ Dùng "Tên sản phẩm" để map SKU thay vì mã → vỡ khi đổi tên
