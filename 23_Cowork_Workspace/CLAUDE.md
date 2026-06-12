# CLAUDE.md — Cowork Operating Instructions

> File này được Cowork đọc **đầu tiên** mỗi lần mở repo. Mô tả: data ở đâu, dùng layer nào, output đi đâu, audience là ai.

## 1. Project context

- **Domain:** Fashion mid-size Vietnam, omnichannel (online marketplace + DTC web + retail stores).
- **Role của Huy:** Merchandise Planning & Logistics Manager.
- **Mục tiêu repo:** Operating system cho S&OP — từ raw data → dashboard → deck cho CEO/CFO.
- **Cycle:** IBP monthly (Oliver Wight 5-step).

## 2. Data location

| Cần data gì | Đọc ở đâu | Không bao giờ đọc |
|---|---|---|
| Dashboard, KPI tổng hợp | `21_Pipelines/gold/` | bronze |
| Ad-hoc analysis cấp SKU | `21_Pipelines/silver/` | bronze |
| Schema/contract | `20_Data_Foundation/02_Data_Contracts/` | — |
| Master data | `20_Data_Foundation/03_Master_Data/` | — |
| KPI definition | `20_Data_Foundation/05_Glossary/kpi_glossary.md` | — |
| File raw mới user drop | `23_Cowork_Workspace/data_inputs/` → ETL move sang bronze | — |

## 3. Layer rule

**Luôn hỏi user "Layer 1 hay Layer 2?"** nếu request không rõ.

- **L1** (Channel × Division × Category × Sub-cat) — cho deck S&OP, MBR, executive summary.
- **L2** (Style × Color × Size) — cho Planner reorder, replenishment, size curve, drill detail.
- **Ngoại lệ:** module S&OP/OTB chỉ có L1 (xem `02_Data_Contracts/sop/README.md`) — không hỏi layer khi user hỏi OTB.

## 3b. Quy tắc reorder in-season — MANDATORY

Trước khi recommend reorder bất kỳ mã nào: check **Open OTB** của cell (channel × category) trong `fact_otb`. Open OTB ≤ 0 → trả lời kèm cảnh báo overbought, yêu cầu CFO approval hoặc cancel PO khác trước.

## 4. KPI definition — MANDATORY

Trước khi tính bất kỳ KPI nào (WMAPE, OTIF, WOS, STR…), **PHẢI đọc** `20_Data_Foundation/05_Glossary/kpi_glossary.md`.

Không tự suy đoán công thức. Nếu công thức không có trong glossary → hỏi user thay vì đoán.

## 5. Output location

| Output | Save to |
|---|---|
| Deck S&OP/MBR | `D:\Claude\Planning\reports\<YYYYMM>\` |
| Ad-hoc analysis file | `D:\Claude\Planning\analysis\<YYYYMMDD>_<topic>.xlsx` |
| Memo/notes | `D:\Claude\Planning\notes\` |

Naming theo `20_Data_Foundation/04_Naming_Conventions/file_naming.md`.

## 6. Skills priority

| Khi user hỏi về… | Dùng skill |
|---|---|
| Demand giữa mùa, reorder, cancel | `demand-analysis-inseason` (skill chung) |
| IBP cycle, S&OP design | `ibp-monthly-cycle` (skill chung) |
| Pre-season buy plan | `merchandise-planning-preseason` (skill chung) |
| Supply tracking, vendor, logistics cost | `supply-execution-tracker` (skill chung) |
| Build Pre-S&OP/MBR deck từ data có sẵn | `sop-deck-builder` (PROJECT skill) |
| Tự động prep Demand Review deck monthly | `demand-review-pack` (PROJECT skill) |
| Tự động prep Supply Review deck monthly | `supply-review-pack` (PROJECT skill) |
| Health check inventory tuần | `inventory-health-check` (PROJECT skill) |
| Build assortment plan pre-season + Buy Plan deck | `assortment-plan-builder` (PROJECT skill) |
| Recommend allocation initial/replen/transfer | `allocation-engine` (PROJECT skill) |
| Tính OTB, track Open OTB, overbought, Financial Review prep | `otb-plan-builder` (PROJECT skill) |

**Project skill override skill chung** khi context match.

## 7. Communication style

- **Tiếng Việt** mặc định. Tiếng Anh khi user hỏi tiếng Anh hoặc khi là KPI term chuẩn quốc tế (OTIF, WMAPE…).
- Chuyên nghiệp, trực quan với tab/bảng.
- Ngắn gọn, cụ thể, có ví dụ.
- Reference framework chuẩn (Oliver Wight, SCOR, Lean) thay vì tự suy luận.

## 8. Anti-patterns — KHÔNG làm

- ❌ Tự suy đoán schema khi không có contract.
- ❌ Tính KPI khi chưa đọc glossary.
- ❌ Đọc bronze layer trực tiếp cho production output.
- ❌ Mix L1 và L2 trong cùng 1 bảng output mà không tách tab.
- ❌ Đưa lời khuyên "mơ hồ, không kiểm chứng, tự suy luận" (per Huy P2 priority).
- ❌ Generate file với tên không theo convention.

## 9. Verification

Mọi output cuối cùng phải:
- [ ] Cite source (fact table / contract file path)
- [ ] Ghi rõ layer (L1 / L2)
- [ ] Ghi rõ thời điểm data (snapshot date / period)
- [ ] Cross-check số liệu với glossary
