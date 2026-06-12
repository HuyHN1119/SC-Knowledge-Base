# SC-Knowledge-Base — Restructure cho S&OP + Cowork

> Tài liệu này là **bản đề xuất bổ sung** vào repo `HuyHN1119/SC-Knowledge-Base` hiện có. Không xoá thư mục cũ (00–15 là knowledge base nền). Thêm 4 cụm thư mục mới (20–23) để biến repo từ "knowledge base đọc" thành **operating system** cho S&OP + Cowork.

---

## 1. Review hiện trạng

| Thư mục hiện có (00–15) | Vai trò | Đánh giá |
|---|---|---|
| `00_System_Thinking` → `15_Integrated_Business_Planning` | Knowledge base lý thuyết theo function (Demand, Supply, Inventory, Procurement, WH, Omni, Analytics, IBP…) | **Tốt** — đầy đủ pillar SCM. Phù hợp để học và tra cứu. |
| README.md | Chỉ có tiêu đề `# SC-Knowledge-Base` | **Thiếu** — chưa có index, chưa có hướng dẫn dùng với Cowork. |

**Gap chính khi muốn Cowork dùng để tạo dashboard/report S&OP:**

1. **Không có data contract** — Cowork không biết file Forecast, PO, SOH trông ra sao (cột nào, kiểu dữ liệu, key join).
2. **Không có master data chuẩn** — Channel/Division/Category/Sub-cat/Style/Color/Size mỗi nguồn gọi một kiểu → Cowork không reconcile được.
3. **Không tách Layer 1 vs Layer 2** — Knowledge base hiện gộp tất cả mức độ vào một, deck S&OP cần Layer 1 (CEO view), drill-down cần Layer 2 (Planner view).
4. **Không có pipeline** — Raw file (Shopee export, Haravan export, ERP export) đổ thẳng vào ngó → mỗi lần report là rework.
5. **Không có skill/prompt riêng cho project** — Cowork đang dùng skill chung (`demand-analysis-inseason`, `ibp-monthly-cycle`), chưa có skill được context hoá theo data của Huy.
6. **Không có nơi drop file hàng tháng** — Cowork không biết tìm input ở đâu khi Huy nói "review tháng này".

---

## 2. Đề xuất restructure — thêm 4 cụm mới

```
SC-Knowledge-Base/
├── 00_System_Thinking/         (giữ nguyên — lý thuyết)
├── 01_Business/                (giữ nguyên)
├── 02_Demand_Planning/         (giữ nguyên — knowledge)
├── 03_Supply_Planning/         (giữ nguyên)
├── 04_Inventory/               (giữ nguyên)
├── 05_Procurement/             (giữ nguyên)
├── …                           (giữ nguyên 06–15)
├── 15_Integrated_Business_Planning/
│
├── 20_Data_Foundation/         ← MỚI: hợp đồng dữ liệu (data contracts) + master data
│   ├── 01_Data_Model/          (conceptual / logical model — star schema)
│   ├── 02_Data_Contracts/      (schema từng bảng theo module)
│   │   ├── sop/                (OTB plan, sales & margin plan, OTB tracking)
│   │   ├── demand/
│   │   ├── supply/
│   │   ├── inventory/
│   │   ├── sales/
│   │   ├── assortment/
│   │   └── allocation/
│   ├── 03_Master_Data/         (product, channel, calendar, vendor, location)
│   ├── 04_Naming_Conventions/  (file, field, code)
│   └── 05_Glossary/            (KPI definitions)
│
├── 21_Pipelines/               ← MỚI: ETL theo medallion architecture
│   ├── bronze/                 (raw — file gốc từ Shopee/TikTok/ERP/POS)
│   ├── silver/                 (clean & conformed — đã chuẩn hoá master data)
│   ├── gold/                   (mart — fact tables đã aggregate theo Layer 1/2)
│   └── orchestration/          (lịch chạy gắn với IBP monthly calendar)
│
├── 22_Dashboards_Reports/      ← MỚI: spec dashboard + template deck
│   ├── demand_dashboard.md
│   ├── supply_dashboard.md
│   ├── inventory_dashboard.md
│   └── sop_meeting_deck_template.md
│
└── 23_Cowork_Workspace/        ← MỚI: Cowork operating layer
    ├── CLAUDE.md               (system prompt cho Cowork khi mở repo này)
    ├── skills/                 (skill được context hoá riêng cho project)
    │   ├── sop-deck-builder/
    │   ├── demand-review-pack/
    │   ├── supply-review-pack/
    │   └── inventory-health-check/
    ├── prompts/                (prompt template hàng tháng)
    └── data_inputs/            (nơi drop file raw mỗi tháng)
```

---

## 3. Hai layer phân tích — chuẩn hoá xuyên suốt

Mọi bảng fact trong `21_Pipelines/gold/` phải hỗ trợ **drill cả 2 layer**:

| Layer | Mức độ | Dùng cho | Audience |
|---|---|---|---|
| **L1** | `Channel × Division × Category × Sub-Category` | S&OP deck, MBR, monthly review | CEO/CFO/Head |
| **L2** | `Style × Color × Size` | Reorder/Cancel, replenishment, size curve | Planner/Buyer |

→ Mỗi fact table có cột đủ cho cả 2 layer, dashboard chuyển layer bằng filter chứ không build 2 bảng riêng.

---

## 4. Bản đồ module → file đầu ra

> **Cập nhật v0.3 (2026-06-12):** Bổ sung module **S&OP/OTB** (contracts + fact_otb + dashboard + skill). Đầy đủ 6 module: S&OP (OTB) → Assortment → Demand → Supply → Allocation → Inventory.

### 4.0 Module flow

```
Pre-season:   Sales & Margin Plan ─► OTB Plan ─► Assortment Plan ─► PO commitment
                                        │
In-season:    Demand Forecast ─┬─► Supply (PR/PO/Intake/Inbound) ─► Allocation ─► Inventory
                               │        │
                               │   OTB Tracking (monthly) ─► Financial Review ─► MBR
                               │
                               └─► Replenishment (gate: Open OTB > 0) ─────────► Inventory
```

### 4.0b S&OP / OTB (MỚI v0.3)
| Output | Layer | File trong repo |
|---|---|---|
| Sales & Margin Plan (Finance input) | L1 | `…/sop/sales_margin_plan_contract.md` |
| OTB Plan (pre-season, lock M-6) | L1 | `…/sop/otb_plan_contract.md` |
| OTB Tracking (in-season, monthly W3.5) | L1 | `…/sop/otb_tracking_contract.md` |
| Sales (order line, 4 nguồn) | L2 | `…/sales/sales_contract.md` |

### 4.1 Demand
| Output | Layer | File trong repo |
|---|---|---|
| Forecast (Statistical + Consensus) | L1 + L2 | `20_Data_Foundation/02_Data_Contracts/demand/forecast_contract.md` |
| Actual vs Forecast (AvF) — bias, MAPE, WMAPE | L1 + L2 | `…/demand/actual_vs_forecast_contract.md` |
| Replenishment plan | L2 | `…/demand/replenishment_contract.md` |
| Color curve | L2 | `…/demand/color_curve_contract.md` |
| Size curve | L2 (size detail) | `…/demand/size_curve_contract.md` |

### 4.2 Supply
| Output | Layer | File |
|---|---|---|
| PR (Purchase Request) | L1 + L2 | `…/supply/pr_contract.md` |
| PO (Purchase Order) | L2 | `…/supply/po_contract.md` |
| Intake (production confirmed) | L2 | `…/supply/intake_contract.md` |
| Inbound (received in WH) | L2 | `…/supply/inbound_contract.md` |

### 4.3 Allocation (MỚI)
| Output | Layer | File |
|---|---|---|
| Initial Allocation (Wave 1/2/3) | L2 | `…/allocation/initial_allocation_contract.md` |
| Replenishment Allocation | L2 | `…/allocation/replenishment_allocation_contract.md` |
| Pack / Breakpack | L2 | `…/allocation/pack_allocation_contract.md` |
| Inter-location Transfer | L2 | `…/allocation/transfer_contract.md` |

### 4.4 Assortment (MỚI)
| Output | Layer | File |
|---|---|---|
| Assortment Plan (option count, width vs depth) | L1 + L2 | `…/assortment/assortment_plan_contract.md` |
| Option Plan (detail per style+color) | L2 | `…/assortment/option_plan_contract.md` |
| Core/Fashion/Seasonal Mix | L1 | `…/assortment/core_seasonal_mix_contract.md` |
| Channel-Specific Assortment | L1 + L2 | `…/assortment/channel_assortment_contract.md` |
| Option Lifecycle | L2 | `…/assortment/option_lifecycle_contract.md` |

### 4.5 Inventory
| Output | Layer | File |
|---|---|---|
| SOH snapshot | L1 + L2 | `…/inventory/soh_snapshot_contract.md` |
| WOS (Weeks of Supply) | L1 + L2 | `…/inventory/wos_contract.md` |
| Turnover | L1 + L2 | `…/inventory/turnover_contract.md` |
| STR (Sell-Through Rate) | L1 + L2 | `…/inventory/str_contract.md` |
| Aging (bucket 0-30/31-60/61-90/91-180/180+) | L2 | `…/inventory/aging_contract.md` |
| Stock Health (Active/Slow/Dead) | L1 + L2 | `…/inventory/health_contract.md` |

---

## 5. Checklist — Điểm cần để Cowork hoạt động tốt

Đây là phần quan trọng nhất. Cowork chỉ output tốt nếu **6 điều kiện** dưới đây thoả mãn:

### 5.1 Data contract phải tồn tại trước khi có data
- [ ] Mỗi file Huy expect đẩy vào (Forecast.xlsx, PO_master.xlsx, SOH_snapshot.csv…) **phải có contract .md tương ứng** trong `20_Data_Foundation/02_Data_Contracts/`.
- [ ] Contract liệt kê: tên cột (chuẩn snake_case), kiểu dữ liệu, primary key, foreign key, đơn vị, ví dụ 3 row.
- [ ] Cowork đọc contract trước khi parse file → không phải đoán schema.

### 5.2 Master data có ID ổn định
- [ ] `style_id`, `color_id`, `size_id`, `channel_id`, `category_id` phải **không đổi qua mùa**.
- [ ] Có file `product_hierarchy.md` map từ tên hiển thị (vd "Áo polo nam navy size L") → bộ ID chuẩn.
- [ ] Không trộn key kỹ thuật (SKU code) với key kinh doanh (Style code) trong cùng cột.

### 5.3 Naming convention thống nhất
- [ ] File: `YYYYMMDD_<module>_<scope>_<version>.<ext>` ví dụ `20260601_demand_forecast_v1.xlsx`.
- [ ] Field: snake_case, đơn vị viết kèm khi cần (`sales_qty_pcs`, `revenue_vnd`, `cogs_vnd`).
- [ ] Period: cột `year`, `month`, `iso_week`, `fiscal_week` — không dùng tên cột `Tháng 6` hay `T6`.

### 5.4 Pipeline có layer rõ ràng
- [ ] **Bronze** = file gốc, không sửa cột, không format. Chỉ lưu timestamp + nguồn.
- [ ] **Silver** = clean + apply master data + add surrogate keys. Cowork chỉ đọc Silver cho parse logic.
- [ ] **Gold** = fact table đã aggregate, mỗi dashboard 1 fact + nhiều dim.
- [ ] Không bao giờ để Cowork tính toán trên Bronze trực tiếp.

### 5.5 Skill được context hoá theo project
- [ ] `23_Cowork_Workspace/skills/` chứa skill **chỉ dùng cho repo này**, biết tên file thực tế, biết KPI definition của công ty, biết audience là ai.
- [ ] Skill chung (`demand-analysis-inseason`, `ibp-monthly-cycle`) vẫn dùng được — nhưng skill project sẽ override khi context match.

### 5.6 CLAUDE.md hướng dẫn Cowork mỗi lần mở repo
- [ ] `23_Cowork_Workspace/CLAUDE.md` mô tả: data ở đâu, layer nào để đọc, template deck, output đi đâu, audience là ai.
- [ ] Cowork đọc CLAUDE.md đầu tiên → không phải hỏi lại Huy mỗi lần.

---

## 6. Quy trình monthly (gắn IBP cycle)

| Tuần | Việc | Cowork làm gì |
|---|---|---|
| **W1** | Demand Review + Assortment in-season tracking | Skill `demand-review-pack` + check option performance vs assortment plan |
| **W2** | Supply Review + Allocation review | Skill `supply-review-pack` + skill `allocation-engine` (review fulfillment & transfer cost) |
| **W3** | Inventory Review | Skill `inventory-health-check` |
| **W3.5** | Pre-S&OP | Consolidate 3 deck → `sop-deck-builder` |
| **W4** | MBR | Trình CEO/CFO |

### Quy trình pre-season (3-6 tháng trước season launch)

| Tháng trước launch | Việc | Cowork làm gì |
|---|---|---|
| M-6 | OTB approval + Assortment direction | — (human only) |
| M-5 | Build Assortment Plan | Skill `assortment-plan-builder` → output Excel + Buy Plan deck |
| M-4 | Option Plan + Color/Size curve | Skill `merchandise-planning-preseason` (chung) |
| M-3 | PO commitment cut | Skill `supply-execution-tracker` |
| M-2 | Wave 1 production tracking | — |
| M-1 | Wave 1 intake + Initial Allocation prep | Skill `allocation-engine` mode = initial |

---

## 7. Roadmap triển khai

| Phase | Thời gian | Nội dung | Output |
|---|---|---|---|
| **P0 — Foundation** | 2 tuần | Viết hết data contract + master data + naming convention | Folder `20_Data_Foundation` đầy đủ |
| **P1 — Pipeline** | 2 tuần | Setup bronze/silver/gold, viết script ETL (Python/Power Query) | Folder `21_Pipelines` chạy được 1 chu kỳ |
| **P2 — Dashboard** | 2 tuần | Build dashboard Demand/Supply/Inventory (Power BI hoặc Looker) | Folder `22_Dashboards_Reports` có file `.pbix`/`.lookml` |
| **P3 — Cowork** | 1 tuần | Viết CLAUDE.md + 7 skill project | Folder `23_Cowork_Workspace` chạy được monthly cycle |
| **P4 — Pilot** | 1 chu kỳ tháng | Chạy thử 1 tháng, đo thời gian từ raw data → MBR deck | Báo cáo time saved |

---

## 8. File đã có sẵn trong bộ đề xuất này

Xem chi tiết trong từng folder:
- `20_Data_Foundation/` — data contracts 7 domain (sop/demand/supply/inventory/sales/assortment/allocation), master data 5 file (product, channel, calendar, vendor, location), star schema, naming, glossary
- `21_Pipelines/` — bronze (8 source folder) + silver/gold spec gộp theo module (9 file) + orchestration (monthly + preseason calendar)
- `22_Dashboards_Reports/` — spec 5 dashboard + template S&OP deck + `_template/dashboard_template.html`
- `23_Cowork_Workspace/` — CLAUDE.md + 7 project skill + 5 prompt template

### Trạng thái roadmap (cập nhật 2026-06-12)

| Phase | Status |
|---|---|
| **P0 — Foundation** | ✅ DONE — contracts đủ 6 module, master data đủ 5, glossary có OTB |
| **P1 — Pipeline** | 🟡 Spec done (silver 5 + gold 4 file) — còn script ETL khi có data thật |
| **P2 — Dashboard** | 🟡 Spec done 5 dashboard + HTML template — còn build với data thật |
| **P3 — Cowork** | ✅ DONE — CLAUDE.md + 7 skill + 5 prompt |
| **P4 — Pilot** | ⬜ Chạy thử 1 chu kỳ tháng |

---

**Phiên bản:** v0.3 — 2026-06-12 (bổ sung S&OP/OTB module + sales contract + pipeline specs + preseason calendar)
**Tác giả:** Huy (Merchandise Planning & Logistics Manager) + Claude Cowork
