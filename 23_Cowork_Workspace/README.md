# 23_Cowork_Workspace

Tầng vận hành Cowork. Mỗi lần Huy mở repo này, Cowork đọc `CLAUDE.md` đầu tiên để biết cách làm việc.

## Cấu trúc

```
23_Cowork_Workspace/
├── CLAUDE.md              ← Operating instructions cho Cowork (đọc đầu tiên)
├── skills/                ← Project-specific skills
│   ├── sop-deck-builder/SKILL.md
│   ├── demand-review-pack/SKILL.md
│   ├── supply-review-pack/SKILL.md
│   ├── inventory-health-check/SKILL.md
│   ├── assortment-plan-builder/SKILL.md   (pre-season)
│   ├── otb-plan-builder/SKILL.md          (pre-season build + in-season track)
│   └── allocation-engine/SKILL.md         (in-season)
├── prompts/               ← Prompt template cho monthly + pre-season cycle
│   ├── monthly_demand_review.md
│   ├── monthly_supply_review.md
│   ├── monthly_inventory_review.md
│   ├── preseason_otb_review.md
│   └── pre_sop_mbr.md
└── data_inputs/           ← Drop file raw ở đây để Cowork ingest
    └── README.md
```

## Cách dùng

### Setup 1 lần
1. Clone repo về máy
2. Mở Cowork → connect folder repo này
3. Cowork sẽ tự đọc `CLAUDE.md`

### Monthly routine (theo IBP cycle)

| Tuần | Việc | Prompt |
|---|---|---|
| W1 | Demand Review prep | `prompts/monthly_demand_review.md` |
| W2 | Supply Review prep | `prompts/monthly_supply_review.md` |
| W3 | Inventory Review prep | `prompts/monthly_inventory_review.md` |
| W3.5 | OTB tracking + Pre-S&OP deck | `prompts/pre_sop_mbr.md` (+ skill `otb-plan-builder` mode=track) |
| W4 | MBR final | `prompts/pre_sop_mbr.md` (section MBR) |

### Pre-season routine (M-6 → M-1)

Xem `21_Pipelines/orchestration/preseason_calendar.md`. Prompt: `prompts/preseason_otb_review.md` (OTB build M-6) → skill `assortment-plan-builder` (M-5) → `allocation-engine` mode=initial (M-1).

### Daily ad-hoc
Bất kỳ câu hỏi nào về demand/supply/inventory → Cowork sẽ:
1. Đọc CLAUDE.md (đã trong context)
2. Identify layer (L1 hay L2)
3. Pull data từ gold/silver
4. Apply KPI definition từ glossary
5. Output theo style chuẩn

### Khi cần ingest file mới
Drop file vào `data_inputs/`, nói:
```
Cowork, validate + ingest <file>.
```

## Skills priority

Project skills (trong folder này) override skills chung khi context match. Skills chung được giữ để dùng cho ad-hoc:
- `demand-analysis-inseason` — phân tích giữa mùa
- `ibp-monthly-cycle` — design quy trình
- `merchandise-planning-preseason` — buy plan
- `supply-execution-tracker` — vendor + logistics cost
