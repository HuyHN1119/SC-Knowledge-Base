---
name: assortment-plan-builder
description: Build assortment plan pre-season cho fashion mid-size — tính option count + width vs depth + core/seasonal mix per (season × channel × category). Cross-check với OTB budget, last season hit rate, channel strategy. Output Excel assortment plan + deck buy plan presentation. LUÔN dùng skill này khi user nói "build assortment plan", "option count cho mùa <YYYY>", "width vs depth", "core seasonal mix", "channel assortment", "buy plan presentation", "pre-season planning", "SS<YY> assortment", "FW<YY> assortment".
---

# assortment-plan-builder

## When to trigger
- "Build assortment plan SS27/FW26"
- "Option count cho mùa tới"
- "Width vs depth strategy"
- "Core vs fashion mix cho channel X"
- "Buy plan presentation"

## Inputs required
1. `season` — "SS27" / "FW26"
2. `channels` — list channels include
3. `total_buy_budget_vnd` (OTB)
4. `prior_season_performance` (auto-pull nếu có data, hoặc user upload)

## Steps

### Step 1: Verify inputs
- Có OTB approved chưa (cross-check với Financial Review)
- Có last season actual STR + sell-through performance không
- Brand strategy direction (more newness vs more core)?

### Step 2: Compute per category × channel

#### Width vs Depth recommendation
Dựa trên category × channel benchmark:

| Channel | Category | Recommended strategy |
|---|---|---|
| Store Flagship | Tops (basic) | depth_heavy (200-500 pcs/option) |
| Store Flagship | Outerwear | balanced (150-300) |
| Shopee/TikTok | Tops (fashion) | width_heavy (50-150 pcs/option, nhiều option) |
| Website DTC | Drops/Collab | balanced |
| Outlet | All | depth_heavy (1000+ pcs/option, fewer) |

#### Option count calculation
```
option_count = (buy_budget × category_share) / (avg_depth × avg_cost_per_pc)
```

#### Core/Fashion/Seasonal split
Reference: `core_seasonal_mix_contract.md` — table 5 channel × 5 mix_type.

### Step 3: Cross-check
- [ ] Total buy value match OTB? (±2%)
- [ ] Option count realistic vs supplier capacity?
- [ ] core_repeat ≥ 30% (anti-pattern check)
- [ ] Markdown provision ≥ 8-12% of buy value (industry standard)
- [ ] Phasing reasonable (Wave 1 ≤ 50% to avoid early sellout)

### Step 4: Output

#### File 1: Excel assortment plan
Path: `D:\Claude\Planning\assortment\<season>_assortment_plan.xlsx`
Sheets:
- `Summary_L1` — option count + value per channel × category
- `Mix_Detail` — core/seasonal % per cell
- `Width_Depth` — strategy per category
- `OTB_Reconciliation` — match vs total budget
- `Phasing` — wave 1/2/3 split

#### File 2: Buy plan dashboard (HTML interactive)
Path: `D:\Claude\Planning\reports\<season>\buy_plan_<season>.html`
Template: `22_Dashboards_Reports/_template/dashboard_template.html`

**Tabs (single-file HTML, mở trong browser, drill-down được):**
| Tab | Content | Layer | Interaction |
|---|---|---|---|
| 1. Exec Summary | KPI cards: total OTB, option count, mix %, newness % | L1 | Click → jump tab tương ứng |
| 2. Channel Strategy | Stacked bar option count by Channel × Category | L1 | Click bar → filter Tab 6 |
| 3. Width vs Depth | Scatter option_count × avg_depth per category | L1 | Hover → category detail |
| 4. Core/Fashion/Seasonal Mix | Donut + variance bar (actual vs target benchmark) | L1 | — |
| 5. Phasing | Gantt-style timeline Wave 1/2/3 per category | L1 | — |
| 6. Option Drill | Grid.js sortable/filterable table — all options L2 | L2 | Search style_id, color, classification filter |
| 7. Risk & Scenario | Side-by-side: best/worst STR scenario impact on inventory value | L1 | Toggle scenario |
| 8. Decisions | Checklist: approval items với owner + date | — | — |

**Stack:** Tailwind CDN + Chart.js + Grid.js + vanilla JS. Single file, không cần server.

→ Đọc `merchandise-planning-preseason` skill chung để align framework chuẩn.

→ Optional: vẫn export PPTX summary (1-2 slide exec) nếu cần present executive. Default = HTML.

### Step 5: Chat summary
- Total OTB: <value>
- Total option count: <number>
- Mix: core <%> / fashion <%> / seasonal <%>
- Newness: <% new vs carry-over>
- Top decision needed: <1 line>

## Anti-pattern
- ❌ Build assortment plan trước khi OTB approved
- ❌ Same assortment cho Store và Marketplace
- ❌ 100% new option (no core repeat)
- ❌ Wave 1 > 60% (sellout risk)
- ❌ Avg depth < 50 pcs (production cost ineffective)
