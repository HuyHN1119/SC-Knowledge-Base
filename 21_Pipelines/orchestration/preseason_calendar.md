# Orchestration — Pre-Season Calendar (OTB → Assortment → PO → Allocation)

Lịch pre-season 6 tháng trước season launch. Bổ trợ cho `monthly_calendar.md` (in-season IBP cycle).

## Timeline chuẩn

| Mốc | Việc | Input cần có | Cowork làm gì | Output |
|---|---|---|---|---|
| **M-7** | Finance build Sales & Margin Plan | LY actual + strategy direction | Validate file theo `sop/sales_margin_plan_contract.md` | Plan draft |
| **M-6** | **OTB build + approve** | Sales & margin plan approved; beginning inv projection | Skill `otb-plan-builder` mode=plan | `<season>_otb_plan_v1.xlsx` → CFO approve → lock |
| **M-5** | Build Assortment Plan | OTB locked | Skill `assortment-plan-builder` | Assortment plan + Buy Plan deck |
| **M-4** | Option Plan + Color/Size curve | Assortment plan approved | Skill `merchandise-planning-preseason` (chung) + validate curve contracts | Option plan, curves |
| **M-3** | **PO commitment cut Wave 1** | Option plan + vendor capacity | Skill `supply-execution-tracker`; check commit % ≤ 70-80% OTB | PO placed |
| **M-2** | Production tracking Wave 1 | fact_po refresh daily | OTIF risk flag weekly | Risk report |
| **M-1** | Wave 1 intake + Initial Allocation prep | Inbound confirmed + size curve | Skill `allocation-engine` mode=initial | Initial allocation plan |
| **M0** | Season launch | — | Switch sang in-season cycle (`monthly_calendar.md`) | — |

## Gate check — không qua mốc nếu thiếu

| Gate | Điều kiện pass |
|---|---|
| OTB lock (M-6) | Sales & margin plan approved; chain check pass; CFO sign-off |
| Assortment freeze (M-5) | Reconcile OTB ±2%; core_repeat ≥ 30% |
| PO cut (M-3) | Commit % ≤ 80%; mọi PO có vendor_id + unit_cost; Wave 1 ≤ 50-60% tổng buy |
| Allocation ready (M-1) | Size curve final; location master active; pack definition chốt |

## Season map (theo CLAUDE.md root)

| Season | Launch | OTB build | PO cut Wave 1 |
|---|---|---|---|
| SS (T3–T8) | đầu T3 | T9 năm trước | T12 năm trước |
| FW (T9–T2) | đầu T9 | T3 | T6 |
