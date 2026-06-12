# Master Data — Vendor

## Mục đích
Dictionary chuẩn cho vendor/nhà máy. Mọi PR/PO/Intake phải map về `vendor_id` ổn định — không dùng tên tự do ("a Đức", "anh Đức 1.5", "Duc 1.5" phải về cùng 1 ID).

## Schema

| Cột | Kiểu | Mô tả | Ví dụ |
|---|---|---|---|
| `vendor_id` | string (PK) | ID ổn định, không đổi | "VND-VIETTHAI" |
| `vendor_name` | string | Tên chính thức | "Việt Thái" |
| `vendor_alias` | string | Các tên gọi khác (phân cách `;`) — dùng để fuzzy match khi ingest | "Viet Thai; VT" |
| `vendor_type` | enum | cmt / fob / fabric / trim / finished_goods | "cmt" |
| `country` | string | | "VN" |
| `region` | string | Tỉnh/khu vực | "Hưng Yên" |
| `main_categories` | string | Category chính vendor sản xuất | "Polo, T-shirt" |
| `moq_pcs` | int | MOQ tiêu chuẩn per style | 300 |
| `lead_time_days_avg` | int | Lead time trung bình (PO → inbound) | 45 |
| `lead_time_days_p90` | int | P90 lead time — dùng cho safety stock calc | 60 |
| `capacity_pcs_month` | int | Năng lực tháng (ước) | 25000 |
| `otif_score_last_6m` | float | Cập nhật monthly từ vendor scorecard | 87.5 |
| `quality_reject_pct_6m` | float | % reject QC 6 tháng | 1.2 |
| `payment_terms` | string | | "30% deposit, 70% on delivery" |
| `is_active` | bool | | true |
| `onboarded_date` | date | | 2023-04-01 |

## Danh sách vendor hiện tại (seed — cập nhật theo thực tế)

| vendor_id | vendor_name | vendor_type | main_categories |
|---|---|---|---|
| VND-VIETTHAI | Việt Thái | cmt | Polo, T-shirt |
| VND-MRQUE | MR Quế | cmt | Shirt |
| VND-HTBLUE | HT Blue | cmt | T-shirt |
| VND-ZIKABI | Zikabi | cmt | Knitwear |
| VND-HUONGSON | Hương Sơn | cmt | Quần âu |
| VND-DUNGJEANS | Dũng Jeans | cmt | Jeans |
| VND-PHUONGDONG | Phương Đông | cmt | Short, Chinos |
| VND-DUC15 | a Đức 1.5 | cmt | Basic tees |

> **Note:** `lead_time_days_avg`, `otif_score`, `capacity` cần điền từ data thực — chưa có thì để NULL, không ước bừa.

## Rule reconcile

- Ingest PO/intake: match `vendor_name` hoặc `vendor_alias` (case-insensitive, bỏ dấu) → `vendor_id`. Không match → quarantine, ping Procurement.
- `otif_score_last_6m` chỉ được update bởi vendor scorecard job (monthly, W3) — không sửa tay.
- Vendor ngừng hợp tác: set `is_active = false`, **không xoá row** (history PO cần FK).

## Downstream
- `dim_vendor` ở silver
- Vendor scorecard (`supply-execution-tracker` skill + `vw_supply_otif_monthly`)
- Safety stock / reorder point calc dùng `lead_time_days_p90`
