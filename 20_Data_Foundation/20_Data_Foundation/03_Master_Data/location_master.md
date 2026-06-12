# Master Data — Location

## Mục đích
Dictionary chuẩn cho mọi điểm chứa hàng: kho, cửa hàng, 3PL, kho ảo platform. Phân biệt rõ **location** (nơi hàng nằm) vs **channel** (nơi hàng bán) — 1 kho có thể phục vụ nhiều channel.

## Schema

| Cột | Kiểu | Mô tả | Ví dụ |
|---|---|---|---|
| `location_id` | string (PK) | ID ổn định | "WH-HN-MAIN" |
| `location_name` | string | | "Kho tổng Hà Nội" |
| `location_type` | enum | warehouse / store / 3pl / virtual | "warehouse" |
| `region` | string | "HN", "HP", "DN", "VINH"… | "HN" |
| `address` | string | | |
| `serves_channels` | string | channel_id phục vụ (phân cách `;`) | "SHOPEE;TIKTOK;WEBSITE" |
| `capacity_pcs` | int | Sức chứa (ước) | 150000 |
| `is_sellable_stock` | bool | Tồn tại đây có available-to-sell không (kho QC/return = false) | true |
| `is_active` | bool | | true |
| `opened_date` | date | | |

## Cấu trúc location (seed — fashion omnichannel VN, cập nhật theo thực tế)

| location_id | location_name | type | serves_channels |
|---|---|---|---|
| WH-HN-MAIN | Kho tổng HN | warehouse | Tất cả ONL + replenish stores |
| WH-HN-ECOM | Kho ecom | warehouse | SHOPEE;TIKTOK;WEBSITE |
| FBS-SHOPEE | Kho Shopee FBS | virtual | SHOPEE |
| FBS-TIKTOK | Kho TikTok fulfillment | virtual | TIKTOK |
| WH-RETURN | Kho hàng return/QC | warehouse | — (is_sellable = false) |
| STORE_<CODE> | 25+ cửa hàng (HN/HP/DN/Vinh) | store | Chính store đó |

> Mỗi store là 1 location riêng `STORE_<CODE>` — list đầy đủ lấy từ file ERP store master, ID phải khớp `channel_hierarchy.md`.

## Rule reconcile

- SOH snapshot: mỗi row phải có `location_id` hợp lệ. Tồn "đang trung chuyển" → không gán store đích, dùng `intransit_qty_pcs` trên location nguồn.
- Tồn platform (FBS) là tồn của mình nằm kho sàn → vẫn count vào SOH tổng, tách được khi cần (`location_type = virtual`).
- `is_sellable_stock = false` (kho return/QC) → loại khỏi available_qty khi tính WOS.
- Đóng store: `is_active = false`, không xoá (history).

## Downstream
- `dim_location` ở silver
- `fact_inventory_snapshot` (grain có location)
- Allocation engine (initial/replen/transfer đều cần from/to location)
- Stock Imbalance Index (WOS across locations)
