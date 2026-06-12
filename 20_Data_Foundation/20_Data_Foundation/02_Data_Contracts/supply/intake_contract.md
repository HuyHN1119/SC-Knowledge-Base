# Data Contract — Intake (Production Confirmation)

## Mục đích
Vendor xác nhận đã sản xuất xong, sẵn sàng ship. Stage trước Inbound.

## Source
- File raw: `21_Pipelines/bronze/supply/intake_log_<YYYYMM>.xlsx` (export từ vendor portal hoặc Excel vendor gửi)
- Owner: Procurement / Vendor Coordinator
- Tần suất: Daily

## Grain
1 row = (`intake_id`)

## Schema

| Cột | Kiểu | Mô tả |
|---|---|---|
| `intake_id` | string (PK) | "INT-2026-0512" |
| `po_number` | string (FK) | Join PO |
| `po_line_no` | int | |
| `vendor_id` | string | |
| `style_id`, `color_id`, `size_id` | string | |
| `qty_intake_pcs` | int | Số confirmed by vendor |
| `intake_date` | date | Ngày xác nhận |
| `qc_status` | enum | pending / passed / failed / rework |
| `expected_ship_date` | date | |
| `actual_ship_date` | date | |
| `shipping_method` | enum | air / sea / road / express |
| `tracking_no` | string | |

## KPI
- **Intake adherence:** `intake_date vs PO confirmed_delivery_date - lead_time_to_ship`
- **QC Pass Rate:** `qc_status = passed / total intakes`
