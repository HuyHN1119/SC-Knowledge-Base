# Data Contract — PR (Purchase Request)

## Mục đích
Lưu request mua hàng (chưa phải PO chính thức). Gateway giữa Planning và Procurement.

## Source
- File raw: `21_Pipelines/bronze/supply/pr_master_<YYYYMM>.xlsx`
- Owner: Planner (raise) → Procurement (approve)
- Tần suất: Daily append, snapshot tuần

## Grain
1 row = (`pr_number`, `pr_line_no`)

## Schema

| Cột | Kiểu | Bắt buộc | Mô tả |
|---|---|---|---|
| `pr_number` | string | Y | "PR-2026-0612" |
| `pr_line_no` | int | Y | 1, 2, 3… |
| `pr_date` | date | Y | Ngày raise |
| `requested_by` | string | Y | Planner |
| `vendor_id` | string | N | Suggested vendor |
| `style_id`, `color_id`, `size_id` | string | Y | SKU |
| `qty_requested_pcs` | int | Y | |
| `target_intake_date` | date | Y | Khi cần hàng vào WH |
| `priority` | enum | Y | critical / high / med / low |
| `status` | enum | Y | draft / submitted / approved / converted_to_po / rejected / cancelled |
| `linked_po_number` | string | N | Khi đã convert |
| `reason_code` | enum | N | replenish / new_season / reorder / cancel_recovery |
| `notes` | string | N | |

## State machine
```
draft → submitted → approved → converted_to_po
                  ↓
              rejected
                  ↓
              cancelled
```

## Downstream
- Convert → `po_contract.md`
- KPI: PR-to-PO conversion time, PR rejection rate
