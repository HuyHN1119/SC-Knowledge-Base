# Prompt template — Pre-Season OTB Build & Review

Sử dụng tại M-6 trước season launch (xem `21_Pipelines/orchestration/preseason_calendar.md`).

---

## Build OTB plan (M-6)

```
Huy: Cowork, build OTB plan cho mùa <season>.

Context:
- Sales & margin plan: đã drop file sales_margin_plan_<FY>_v<n>.xlsx vào data_inputs/
- Beginning inventory: lấy projection từ fact_inventory_snapshot mới nhất + open PO
- Forward cover target: 10w core / 8w seasonal (hoặc tôi sẽ chỉnh)
- Audience: CFO (approve)

Yêu cầu:
1. Validate sales_margin_plan theo contract
2. Tính OTB theo glossary, chain check begin/end inventory
3. Reconcile vs annual budget — flag lệch
4. Output Excel <season>_otb_plan_v1.xlsx + chat summary 5 bullet
5. List 3 assumption rủi ro nhất cần CFO confirm

Reference: skill `otb-plan-builder` mode=plan.
```

---

## Track OTB in-season (monthly W3.5)

```
Huy: Cowork, OTB tracking tháng <YYYYMM>.

Yêu cầu:
1. Dùng OTB version locked của season hiện tại
2. Tính committed / open / forward cover per channel × category
3. Flag overbought + đề xuất PO hold/cancel candidates
4. Output: bảng trong chat (nhanh) + HTML nếu cần present

Reference: skill `otb-plan-builder` mode=track.
```

---

## Variation ad-hoc

```
Huy: Còn room reorder cho Polo online không?
```

Cowork: check Open OTB cell (ALL_ONLINE × Tops/Polo) + forward cover → trả lời Yes/No kèm số, cite snapshot date. Nếu Open OTB < 0 → trả lời "Không — overbought X VND, cần CFO approval hoặc cancel PO khác trước".
