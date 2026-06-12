# S&OP Meeting Deck — Template

## Mục đích
Template chuẩn cho Pre-S&OP và MBR deck. Mỗi tháng Cowork build deck mới từ template này qua skill `sop-deck-builder`.

## Audience
- Pre-S&OP: Head of Planning, Head of Merchandising, Head of Supply, CFO delegate
- MBR: CEO, CFO, Head of all functions

## Structure (15-20 slides)

### Section 1: Executive Summary (slide 1-2)
- **S1:** Title — "S&OP <YYYYMM> — Decisions Required"
- **S2:** 3-bullet summary
  - Demand: tổng net revenue vs forecast (%)
  - Supply: OTIF, PO at risk
  - Inventory: turnover annualized, dead stock value, gaps cần escalation

### Section 2: Demand Review (slide 3-6)
- **S3:** Forecast Accuracy — WMAPE 4w & 13w, Bias trend
- **S4:** Sales vs Plan — Channel × Category (L1)
- **S5:** Top 5 categories ahead/behind plan
- **S6:** Demand assumption changes for next 3 months

### Section 3: Supply Review (slide 7-9)
- **S7:** PO Pipeline — Open PO value, at-risk
- **S8:** Vendor performance — Top 5 + Bottom 5 OTIF
- **S9:** Supply constraints / capacity issue cần CEO biết

### Section 4: Inventory Review (slide 10-13)
- **S10:** Inventory health donut (L1)
- **S11:** WOS heatmap by Category × Channel
- **S12:** Aging & provision trend
- **S13:** STR season-to-date — styles critical cần markdown

### Section 5: Financial Alignment + OTB (slide 14-16)
- **S14:** Net revenue vs budget MTD/YTD (plan attainment % — `fact_plan_vs_actual`)
- **S15:** OTB position — plan / committed / **Open OTB** per channel; cells overbought + action (`vw_otb_l1`); markdown budget used % vs season elapsed %
- **S16:** Inventory value vs target + margin impact của decisions đề xuất

### Section 6: Decisions Required (slide 17-19)
- **S17:** Decision 1 — Reorder commit cho style X (value, vendor, timing)
- **S18:** Decision 2 — Markdown approval cho aging stock value Y
- **S19:** Decision 3 — Vendor switch / capacity expansion

### Section 7: Risks & Next Steps (slide 20)
- Top 3 risks
- Owners + due dates
- Next S&OP date

## Footer chuẩn mỗi slide
- Source: <fact table> | Last refresh: <timestamp>
- Layer: L1 (Channel × Cat) hoặc L2 (Style × Color)
- Owner: <function>
