---
name: otb-plan-builder
description: Build và track OTB (Open-to-Buy) cho fashion mid-size — tính OTB plan theo tháng × channel × category từ sales & margin plan, track commit/open OTB in-season, reconcile với assortment plan và PO. LUÔN dùng skill này khi user nói "tính OTB", "OTB plan mùa <season>", "còn bao nhiêu open OTB", "có vượt budget không", "overbought", "commit % bao nhiêu", "OTB tracking", "release OTB", "chuẩn bị Financial Review".
---

# otb-plan-builder

## When to trigger
- "Tính OTB cho SS27" / "Build OTB plan FW26"
- "Open OTB còn bao nhiêu?" / "Category nào overbought?"
- "Commit % hiện tại?" / "Còn room reorder không?"
- "Prep Financial Review tháng này"

## Mode

| Mode | Khi nào | Output |
|---|---|---|
| `plan` | Pre-season M-6 | OTB plan Excel + summary |
| `track` | In-season monthly W3.5 | OTB tracking table + action flags |
| `reconcile` | Trước Financial Review | Plan vs Assortment vs PO variance |

## Inputs required

### Mode = plan
1. `season` — "SS27"
2. Sales & margin plan (contract `sop/sales_margin_plan_contract.md`) — bắt buộc, không có thì dừng và yêu cầu Finance
3. Beginning inventory projection (từ `fact_inventory_snapshot` + open PO)
4. `forward_cover_target_wks` per category (default: 10w core, 8w seasonal)

### Mode = track
1. OTB plan locked version
2. `fact_po` (committed = mọi PO trừ cancelled), `fact_sales`, `fact_inventory_snapshot` mới nhất

## Steps

### Mode = plan
1. Verify sales & margin plan đã approve (status check)
2. Per (month × channel × category), tính theo glossary:
   ```
   OTB = planned_sales_cost + planned_markdown_cost + target_ending_inv − beginning_inv − on_order
   ```
   với `planned_sales_cost = net_revenue × (1 − intake_margin%)`
3. Chain check: ending inv tháng N = beginning inv tháng N+1
4. Convert qty: `otb_qty = otb_value / avg_cost_per_pc`
5. Cross-check tổng OTB vs năng lực tài chính (CFO cap) — flag nếu vượt
6. Output theo `sop/otb_plan_contract.md`

### Mode = track
1. Đọc OTB plan locked version — KHÔNG tự chọn version, hỏi nếu có >1
2. Tính committed/received/open per cell theo `sop/otb_tracking_contract.md`
3. Apply decision rules (xem `22_Dashboards_Reports/sop_otb_dashboard.md` section Decision rules)
4. Mọi cell overbought → bắt buộc đề xuất action: hold / cancel / push ETA, kèm danh sách PO ứng viên (từ fact_po, sort by ETA xa nhất + STR thấp nhất)

## Output

### Mode = plan
- Excel: `D:\Claude\Planning\otb\<season>_otb_plan_v<n>.xlsx`
  - Sheets: `OTB_L1` (month × channel × category), `Chain_Check`, `Assumptions`, `Reconcile_vs_Budget`
- Chat summary: total OTB, phasing theo tháng, top 3 assumption cần CFO confirm

### Mode = track
- HTML dashboard theo template `22_Dashboards_Reports/_template/dashboard_template.html` hoặc bảng trong chat nếu user cần nhanh
- Path: `D:\Claude\Planning\reports\<YYYYMM>\otb_tracking_<YYYYMM>.html`
- Chat summary: Open OTB total, số cell overbought, action cần approve

## Cross-check bắt buộc
- [ ] Công thức đúng glossary (`05_Glossary/kpi_glossary.md` — section S&OP/OTB)
- [ ] At cost thống nhất (không mix retail value)
- [ ] OTB plan reconcile assortment plan ±2%
- [ ] Mọi số có cite: fact table + snapshot date

## Anti-pattern
- ❌ Tính OTB khi chưa có sales & margin plan approved
- ❌ Mix at-cost và at-retail trong cùng bảng
- ❌ Cho phép reorder khi Open OTB < 0 mà không flag CFO approval
- ❌ So commit % mà không so pacing (commit 80% ở M-3 ≠ commit 80% ở mid-season)
