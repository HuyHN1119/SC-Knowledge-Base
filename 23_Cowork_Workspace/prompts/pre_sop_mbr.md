# Prompt template — Pre-S&OP Consolidation + MBR

Sử dụng W3.5 (Pre-S&OP) và W4 (MBR) mỗi tháng. Đây là bước consolidate 3 review pack (Demand / Supply / Inventory) + Financial Review thành 1 deck.

---

## Pre-S&OP (W3.5)

```
Huy: Cowork, build Pre-S&OP deck tháng <YYYYMM>.

Context:
- Input: 3 deck đã build W1-W3 (demand / supply / inventory review) + OTB tracking mới nhất
- Audience: Planning Manager + Heads (pre-meeting trước MBR)
- Layer: L1 toàn bộ, L2 chỉ trong appendix

Yêu cầu:
1. Consolidate theo template `22_Dashboards_Reports/sop_meeting_deck_template.md`
2. Gap analysis: Demand plan vs Supply commit vs Financial target — bảng 1 trang
3. Decision list: mọi item cần MBR quyết (escalation từ 3 review + OTB overbought)
4. Mỗi decision: option A/B, impact VND, owner đề xuất
5. Output: D:\Claude\Planning\reports\<YYYYMM>\pre_sop_<YYYYMM>.pptx
6. Chat summary: 5 bullet — top gap, top decision

Reference: skill `sop-deck-builder`.
```

---

## MBR deck (W4)

```
Huy: Cowork, finalize MBR deck tháng <YYYYMM>.

Context:
- Input: pre_sop deck + kết quả Pre-S&OP meeting (tôi sẽ paste notes/decisions đã chốt)
- Audience: CEO + CFO
- Tối đa 12 slide, 3-second rule mỗi slide

Yêu cầu:
1. Update decision status từ Pre-S&OP notes
2. Slide 1 = Executive summary: RAG status 4 module (Demand/Supply/Inventory/Financial) + 3 quyết định cần CEO
3. Mọi số cite source + snapshot date
4. Output: D:\Claude\Planning\reports\<YYYYMM>\mbr_<YYYYMM>.pptx
```

---

## Checklist trước khi gửi deck

- [ ] WMAPE/Bias/OTIF/WOS/Open OTB đúng công thức glossary
- [ ] Mọi slide L1; L2 chỉ ở appendix
- [ ] Decision nào cũng có owner + deadline
- [ ] Số liệu 4 module cùng snapshot period (không slide dùng T5, slide dùng T6)
