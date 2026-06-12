# Data Contract — PO (Purchase Order)

## Mục đích
Lưu PO chính thức đã đặt với vendor. Là core fact cho Supply tracking.

## Source
- File raw: `21_Pipelines/bronze/supply/po_master_<YYYYMM>.xlsx`
- Owner: Procurement
- Tần suất: Daily

## Grain
1 row = (`po_number`, `po_line_no`, `style_id`, `color_id`, `size_id`)

## Schema

| Cột | Kiểu | Bắt buộc | Mô tả |
|---|---|---|---|
| `po_number` | string | Y | "PO-2026-0099" |
| `po_line_no` | int | Y | |
| `po_date` | date | Y | Ngày đặt |
| `vendor_id` | string | Y | |
| `style_id`, `color_id`, `size_id` | string | Y | |
| `order_qty_pcs` | int | Y | |
| `unit_cost_vnd` | bigint | Y | |
| `total_cost_vnd` | bigint | Y | qty × unit_cost |
| `requested_delivery_date` | date | Y | Date yêu cầu |
| `confirmed_delivery_date` | date | N | Date vendor xác nhận |
| `revised_delivery_date` | date | N | Date sau revise (nếu có) |
| `intake_date` | date | N | Vendor báo xong production |
| `inbound_date` | date | N | Hàng vào WH thực tế |
| `qty_intake_pcs` | int | N | Số confirm intake |
| `qty_inbound_pcs` | int | N | Số nhận thực |
| `status` | enum | Y | open / in_production / intake / inbound / closed / cancelled |
| `linked_pr_number` | string | N | |
| `risk_flag` | enum | N | on_track / at_risk / late / cancelled |

## KPI derived

| KPI | Công thức |
|---|---|
| **OTIF** (On-Time In-Full) | `inbound_date <= requested_delivery_date AND qty_inbound_pcs >= order_qty_pcs × 0.95` |
| **PO Fill Rate** | `sum(qty_inbound_pcs) / sum(order_qty_pcs)` |
| **Lead Time Variance** | `(actual inbound - requested) trong days` |
| **Late PO%** | % PO có `inbound_date > requested_delivery_date` |

## Downstream
- `fact_po` ở gold
- Skill: `supply-review-pack`
- Dashboard: Supply Dashboard
- Feed vào: vendor scorecard
