# Silver Spec — Inventory & Sales (stg_inventory, stg_sales)

## stg_inventory

- **Source:** `bronze/inventory/soh_<YYYYMMDD>.csv` (WMS/ERP EoD) — **Contract:** `inventory/soh_snapshot_contract.md`
- **Refresh:** daily EoD
- **Grain:** snapshot_date × location × SKU

| Transform | Logic |
|---|---|
| SKU parse | Mã ERP → style/color/size qua product_hierarchy (giống dim_product) |
| Location map | Tên kho/cửa hàng → `location_id` (dim_location) |
| Sellable split | `available_qty = soh − reserved` ; location `is_sellable_stock = false` → available = 0 |
| Value | `soh_value_vnd = soh_qty × unit_cost` (cost từ PO gần nhất hoặc standard cost) — cần cho OTB/turnover |
| Append | Append-only theo snapshot_date — không update row cũ |

**Validation:** soh ≥ 0 (âm → quarantine, báo WH); SKU lạ → quarantine; thiếu snapshot 1 ngày → flag gap, không interpolate.

## stg_sales

- **Source:** `bronze/sales/{shopee|tiktok|website|pos}_<dateRange>.csv` — **Contract:** `sales/sales_contract.md`
- **Refresh:** weekly (target daily)
- **Grain:** order line

| Transform | Logic |
|---|---|
| Union 4 nguồn | Map cột theo bảng mapping trong sales_contract → schema chuẩn duy nhất |
| Prefix order_id | SPE- / TTS- / WEB- / POS- tránh trùng ID giữa sàn |
| SKU map | Seller SKU / mã SP → style/color/size; KHÔNG map bằng tên sản phẩm |
| Net revenue | Tính lại `net = gross − discount`; với Shopee/TikTok lấy số sau voucher sàn nếu file có |
| Returns | qty âm + status returned; giữ nguyên trong stg |
| Cancelled | Giữ ở silver (audit), flag để gold loại ra |
| Promo flag | `is_promo_order` từ dim_calendar.is_promo_day theo order_date |

**Validation:** theo sales_contract (net = gross − discount ±1000; duplicate keep latest; SKU lạ quarantine).

## Quy tắc chung

1. **stg_inventory là snapshot, stg_sales là transaction** — không bao giờ join trực tiếp 2 bảng này ở silver; mọi derive (WOS, STR…) làm ở gold.
2. Inventory value at cost; sales value at net revenue — không mix.
3. Quarantine rate > 5% rows của 1 file → dừng pipeline file đó, báo Huy thay vì âm thầm drop.
