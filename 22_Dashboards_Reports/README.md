# 22_Dashboards_Reports

Spec dashboard + template deck. Mỗi dashboard 1 file `.md` mô tả: audience, pages/tabs, visuals, filters, source fact tables.

## Cấu trúc

| File | Module | Audience |
|---|---|---|
| `demand_dashboard.md` | Demand | Demand Planner + Head of Merchandising |
| `supply_dashboard.md` | Supply | Procurement + Vendor Coord + Planning Manager |
| `inventory_dashboard.md` | Inventory | Planner + Finance + Merchandising |
| `allocation_assortment_dashboard.md` | Allocation + Assortment | Allocator + Merchandiser + Head of Buying |
| `sop_otb_dashboard.md` | S&OP / OTB / Financial Review | Planning Manager + CFO + CEO |
| `sop_meeting_deck_template.md` | All | CEO/CFO/Heads (MBR) |
| `_template/dashboard_template.html` | Template HTML chung (Tailwind + Chart.js + Grid.js, single-file, tab L1/L2) | — Cowork dùng khi generate dashboard |

## Nguyên tắc design

1. **L1 trên — L2 dưới.** Top tab/page là L1 view (Channel × Cat). Bottom tab/page là drill L2.
2. **3-second rule.** Trang đầu của mỗi dashboard phải trả lời 1 câu hỏi trong 3 giây ("Are we on track?").
3. **Colour code thống nhất.** green = healthy, yellow = watch, red = critical, grey = no data.
4. **Source label.** Mỗi visual ghi rõ source fact table + last refresh time.
