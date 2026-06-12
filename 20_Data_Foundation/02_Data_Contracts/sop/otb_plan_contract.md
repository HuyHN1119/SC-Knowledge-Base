# Data Contract — OTB Plan

## Mục đích
Ngân sách mua hàng (Open-to-Buy) theo tháng × channel × category cho 1 season. Là cầu nối giữa Financial Plan (top-down) và Assortment Plan (bottom-up). Approved tại M-6, lock tại M-5 trước season launch.

## Source
- File raw: `21_Pipelines/bronze/sop/otb_plan_<season>_v<n>.xlsx`
- Owner: Planning Manager (build) + CFO (approve)
- Tần suất: 1 lần/mùa pre-season; re-forecast được phép tại mid-season review (tạo version mới, không sửa version cũ)

## Grain
1 row = (`otb_version` × `season` × `period_yyyymm` × `channel_id` × `division` × `category`)

> **Lý do dừng ở category (L1):** OTB là công cụ kiểm soát ngân sách, không phải công cụ chọn style. Drill xuống style/color do `assortment_plan_contract.md` + `option_plan_contract.md` quản.

## Schema

| Cột | Kiểu | Bắt buộc | Mô tả | Ví dụ |
|---|---|---|---|---|
| `otb_version` | string | Y | "OTB_SS27_v1" / "OTB_SS27_REFC_2026M08" | "OTB_SS27_v1" |
| `season` | string | Y | Season áp dụng | "SS27" |
| `period_yyyymm` | string | Y | Tháng plan | "202703" |
| `channel_id` | string (FK) | Y | Join dim_channel (cho phép group: "ALL_ONLINE", "ALL_STORE") | "ALL_ONLINE" |
| `division` | string | Y | | "Menswear" |
| `category` | string | Y | | "Tops" |
| `planned_sales_value_vnd` | bigint | Y | Doanh số plan (net, at retail) | 4500000000 |
| `planned_sales_qty_pcs` | int | Y | Quantity plan | 15000 |
| `planned_markdown_vnd` | bigint | Y | Markdown budget trong tháng | 350000000 |
| `target_ending_inv_value_vnd` | bigint | Y | Tồn cuối tháng target (at cost) | 6200000000 |
| `beginning_inv_value_vnd` | bigint | Y | Tồn đầu tháng (at cost; actual nếu đã có, plan nếu chưa) | 5800000000 |
| `on_order_value_vnd` | bigint | Y | PO đã đặt chưa nhận, ETA trong tháng (at cost) | 1200000000 |
| `otb_value_vnd` | bigint | Y | **= planned_sales_cogs + planned_markdown_cost + target_ending_inv − beginning_inv − on_order** | 2750000000 |
| `otb_qty_pcs` | int | Y | = otb_value / avg_cost_per_pc | 9170 |
| `avg_cost_per_pc_vnd` | int | Y | Cost trung bình per pc của category | 300000 |
| `target_intake_margin_pct` | float | Y | (RRP − cost) / RRP target | 62.0 |
| `forward_cover_target_wks` | float | Y | Target forward cover cuối tháng | 10.0 |
| `status` | enum | Y | draft / approved / locked | "approved" |
| `approved_by` | string | N | | "CFO" |
| `approved_date` | date | N | | 2026-09-15 |
| `notes` | string | N | | "Tết shift sang T1" |

## Primary Key
`(otb_version, season, period_yyyymm, channel_id, division, category)`

## Validation rules
- Công thức OTB phải khớp (±1%): `otb_value_vnd = planned_sales_value_vnd × (1 − target_intake_margin_pct/100) + planned_markdown_vnd × (1 − target_intake_margin_pct/100) + target_ending_inv_value_vnd − beginning_inv_value_vnd − on_order_value_vnd`
- `otb_value_vnd < 0` → **không drop**, flag `OVERBOUGHT` (đã commit quá ngân sách — cần cancel/push PO)
- `beginning_inv` tháng N phải = `target_ending_inv` tháng N-1 trong cùng version (chain check)
- Chỉ 1 version có `status = locked` per season tại 1 thời điểm
- Sum `otb_value_vnd` toàn season phải reconcile với `buy_value_total_vnd` của `assortment_plan_contract.md` (±2%)

## Ví dụ 3 row

```
otb_version | season | period | channel    | category | planned_sales (VND) | target_end_inv | begin_inv | on_order | otb_value
OTB_SS27_v1 | SS27   | 202703 | ALL_ONLINE | Tops     | 4.5B                | 6.2B           | 5.8B      | 1.2B     | 2.75B
OTB_SS27_v1 | SS27   | 202703 | ALL_STORE  | Tops     | 6.8B                | 9.5B           | 9.1B      | 1.8B     | 4.12B
OTB_SS27_v1 | SS27   | 202704 | ALL_ONLINE | Tops     | 5.2B                | 6.0B           | 6.2B      | 0.9B     | 2.93B
```

## Downstream
- `fact_otb` ở `21_Pipelines/gold/` (join với fact_po + fact_sales để track)
- Dashboard: `22_Dashboards_Reports/sop_otb_dashboard.md`
- Skill: `otb-plan-builder` (build), `sop-deck-builder` (Financial Review slide)
- Drive `assortment_plan_contract.md` (OTB là input bắt buộc trước khi build assortment)

## Anti-pattern
- ❌ Build assortment plan trước khi OTB approved → ngược quy trình
- ❌ Sửa trực tiếp version locked → tạo version REFC mới
- ❌ OTB tính at retail value nhưng inventory at cost trong cùng row → lệch đơn vị, phải thống nhất at cost
- ❌ Không trừ on_order → double-count ngân sách đã commit
