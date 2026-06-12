# Orchestration — Monthly IBP Calendar

Gắn pipeline refresh với 5-step IBP cycle (Oliver Wight).

## Lịch chuẩn

| Tuần | Ngày trong tháng | IBP Step | Pipeline Action | Output |
|---|---|---|---|---|
| **W0** | Cuối tháng trước | — | Lock previous month — close `fact_sales`, `fact_po`, `fact_inventory_snapshot` cho tháng N-1 | Snapshot data locked |
| **W1 Mon-Wed** | 1-3 | **Product Review** | Refresh `stg_product` + `dim_product` (style mới launch/EOL) | Product change log |
| **W1 Thu-Fri** | 4-5 | **Demand Review** prep | Recompute `fact_forecast_avf` cho tháng N-1, build `pre_demand_review.pptx` qua skill `demand-review-pack` | Demand Review deck |
| **W2** | 6-12 | **Demand Review** meeting → **Supply Review** prep | Lock consensus forecast version "CONSENSUS_<YYYYMM>". Recompute `fact_replenishment`. Build `pre_supply_review.pptx` qua skill `supply-review-pack` | Consensus locked, Supply deck |
| **W3** | 13-19 | **Supply Review** → **Inventory check** | Run vendor scorecard. Build `pre_inventory_review.pptx` qua `inventory-health-check`. | Supply + Inventory deck |
| **W3.5** | 20-22 | **Financial Review** prep + Pre-S&OP | Recompute `vw_inventory_health_l1`, turnover, DIO at L1. **Refresh `fact_otb` + `fact_plan_vs_actual`** (skill `otb-plan-builder` mode=track). Build consolidated `pre_sop_<YYYYMM>.pptx` qua skill `sop-deck-builder` | Pre-S&OP deck + OTB tracking |
| **W4** | 23-30 | **MBR (Management Business Review)** | Final deck, financial alignment | MBR deck → CEO/CFO |

## Trigger map

| Trigger | Pipeline job | Skill |
|---|---|---|
| EoD daily | refresh fact_sales, fact_inventory_snapshot | — |
| Mon 6am weekly | refresh fact_forecast_avf, fact_inventory_health | — |
| 1st of month 6am | lock previous month, run vendor scorecard | — |
| Manual (Huy command) | rebuild any specific deck | `*-review-pack` |

## Failure handling

| Issue | Action |
|---|---|
| Bronze file thiếu (vd Shopee export chưa drop) | Quarantine; ping owner; freeze downstream |
| Master data conflict (SKU mới chưa có trong dim_product) | Quarantine row, ping merchandiser; không block pipeline |
| Forecast version chưa lock đến W1 Fri | Cowork dùng version mới nhất + flag warning |
