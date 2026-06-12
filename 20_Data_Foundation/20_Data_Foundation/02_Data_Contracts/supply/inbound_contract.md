# Data Contract — Inbound (WH Receipt)

## Mục đích
Lưu việc nhận hàng vào WH. Là điểm "available to sell" — quan trọng cho WOS calc.

## Source
- File raw: `21_Pipelines/bronze/supply/inbound_log_<YYYYMM>.csv` (từ WMS hoặc Excel WH log)
- Owner: WH Operations
- Tần suất: Daily

## Grain
1 row = (`grn_id`, `style_id`, `color_id`, `size_id`)

> GRN = Goods Received Note

## Schema

| Cột | Kiểu | Mô tả |
|---|---|---|
| `grn_id` | string | "GRN-2026-1101" |
| `grn_date` | date | Ngày nhận hàng vào WH |
| `po_number` | string (FK) | |
| `po_line_no` | int | |
| `intake_id` | string (FK) | |
| `location_id` | string (FK) | WH nhận |
| `style_id`, `color_id`, `size_id` | string | |
| `qty_inbound_pcs` | int | Số nhận thực |
| `qty_damaged_pcs` | int | Hàng lỗi |
| `qty_accepted_pcs` | int | qty_inbound - qty_damaged |
| `putaway_status` | enum | pending / completed |
| `lot_no` | string | |

## Derived
- **Receive accuracy:** `qty_accepted_pcs / qty_intake_pcs`
- **Putaway time:** time from `grn_date` to `available_to_sell_date`

## Downstream
- Trigger update `fact_inventory_snapshot.soh_qty_pcs += qty_accepted_pcs`
- Close PO line when `sum(qty_accepted_pcs) >= order_qty_pcs × tolerance`
