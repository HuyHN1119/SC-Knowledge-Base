---
name: sop-deck-builder
description: Build consolidated Pre-S&OP / MBR HTML dashboard cho fashion mid-size từ data đã có trong gold layer. Gộp output từ Demand/Supply/Inventory/Assortment/Allocation review thành 1 HTML interactive (không phải pptx) — có tab cho từng review, drill L1 → L2, filter, export. LUÔN dùng skill này khi user nói "build pre-S&OP dashboard", "tạo MBR dashboard", "ghép review tháng này", "consolidate review", "S&OP meeting prep".
---

# sop-deck-builder

## When to trigger
- "Build deck S&OP cho tháng <YYYYMM>"
- "Consolidate review deck"
- "Tạo Pre-S&OP / MBR deck"
- "Prep meeting với CEO/CFO về planning"

## Inputs required
1. `period_yyyymm` — tháng deck (vd "2026-06")
2. `meeting_type` — pre_sop / mbr
3. Audience confirm (default: pre_sop = Heads; mbr = CEO+CFO)

## Steps

### Step 1: Verify data availability
Check các fact/view sau đã refresh đến `period_yyyymm`:
- `gold/fact_sales`
- `gold/fact_forecast_avf`
- `gold/fact_po`
- `gold/fact_inventory_health`
- `gold/vw_inventory_health_l1`

Nếu thiếu → STOP, ping user file nào missing trong `bronze/`.

### Step 2: Load template
Đọc `22_Dashboards_Reports/sop_meeting_deck_template.md` (structure 7 sections) và HTML template `22_Dashboards_Reports/_template/dashboard_template.html`.

### Step 3: Pull data per tab
| Tab | Data source | Aggregation level |
|---|---|---|
| Tab 1 — Executive | All facts aggregated | L1 |
| Tab 2 — Demand | fact_forecast_avf | L1 + L2 drill (top 10 worst) |
| Tab 3 — Supply | fact_po | L1 + vendor drill |
| Tab 4 — Inventory | fact_inventory_health, vw_str_by_season | L1 + L2 critical/overstock/dead |
| Tab 5 — Assortment | fact_assortment_plan_vs_actual | L1 (mix, OTB), L2 (option performance) |
| Tab 6 — Allocation | fact_allocation, fact_transfer | L1 (heatmap), L2 (drill) |
| Tab 7 — Financial | Aggregated KPIs | L1 |
| Tab 8 — Decisions | User input — Cowork phỏng vấn user 3 decisions lớn nhất | — |
| Tab 9 — Risks & Actions | Derived từ flagged data + owners | — |

### Step 4: Build HTML dashboard
Stack: Tailwind CDN + Chart.js + Grid.js + vanilla JS, single-file HTML.

**Cách build:**
1. Đọc HTML template `_template/dashboard_template.html`
2. Inject data JSON vào `<script id="data" type="application/json">...</script>` block
3. Tabs render từ data, không hardcode
4. Filter chip (period, channel, division) áp dụng cross-tab
5. Layer toggle (L1/L2) ở header
6. Export CSV button per table
7. Print-friendly CSS (cho ai vẫn muốn in)

Naming: `pre_sop_<YYYYMM>.html` hoặc `mbr_<YYYYMM>.html`

Save to: `D:\Claude\Planning\reports\<YYYYMM>\`

### Step 5: Footer mỗi tab
- Source: `<fact table>` | Last refresh: `<timestamp>`
- Layer: `L1` / `L2` badge

### Step 6: Verification
Trước khi present cho user:
- [ ] 9 tabs đầy đủ
- [ ] Mỗi KPI có cite glossary definition (tooltip on hover)
- [ ] Layer badge L1/L2 rõ ràng mỗi tab
- [ ] Decision tab có đủ ROI / cost impact + owner + date
- [ ] HTML mở được trong Chrome/Edge/Firefox không lỗi console

## Output
File `.html` + summary text 5 bullet trong chat.

## Optional PPTX export
Nếu user yêu cầu PPTX (vd. CEO không quen HTML), generate thêm 1-2 slide exec summary từ Tab 1 — KHÔNG full deck. Default = HTML only.

## Anti-pattern
- ❌ Build pptx full thay vì HTML (HTML cho phép drill, pptx static)
- ❌ Build dashboard mà chưa verify data fresh
- ❌ Tự suy 3 decisions — phải hỏi user
- ❌ Mix L1+L2 trong cùng tab không label rõ
- ❌ Hardcode data inline thay vì JSON block (sẽ khó refresh)
