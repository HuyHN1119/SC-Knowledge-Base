# 21_Pipelines — ETL theo Medallion Architecture

Tầng vận hành dữ liệu. Phân thành 3 layer chuẩn để Cowork đọc đúng nơi mỗi lần.

## Medallion (Bronze / Silver / Gold)

| Layer | Trạng thái | Ai write | Ai read |
|---|---|---|---|
| **bronze/** | Raw — y nguyên file gốc từ source | ETL ingest (hoặc Huy drop tay) | ETL silver — **Cowork không đọc trực tiếp** |
| **silver/** | Clean — đã chuẩn hoá master data, snake_case, ID conformed | ETL transform | ETL gold, ad-hoc analysis |
| **gold/** | Mart — fact + dim, đã aggregate theo grain dashboard | ETL aggregate | Dashboards, Cowork skills |

## Sơ đồ

```
Sources (Shopee/TikTok/POS/ERP/WMS export)
        │
        ▼
   bronze/<source>/<file>           ← Raw, immutable
        │
        ▼   [clean, join master, conform]
   silver/<domain>/<table>          ← Conformed, snake_case, validated
        │
        ▼   [aggregate, derive KPI]
   gold/<mart>/<fact_or_view>       ← Dashboard-ready
        │
        ▼
   Dashboards / Cowork Skills
```

## Folder structure

```
21_Pipelines/
├── bronze/
│   ├── demand/         (forecast files, color/size curves)
│   ├── supply/         (PR, PO, intake, inbound logs)
│   ├── inventory/      (SOH snapshots)
│   ├── sales/          (Shopee, TikTok, POS, Website exports)
│   ├── sop/            (sales & margin plan, OTB plan)
│   ├── assortment/     (assortment/option/channel plan)
│   ├── allocation/     (initial alloc, transfer log)
│   └── master/         (product, channel, vendor, location master)
│
├── silver/             (spec gộp theo module)
│   ├── silver_dimensions.md
│   ├── silver_demand.md
│   ├── silver_supply.md
│   ├── silver_inventory_sales.md
│   └── silver_sop_assortment_allocation.md
│
├── gold/               (spec gộp theo module)
│   ├── gold_demand.md
│   ├── gold_supply.md
│   ├── gold_inventory.md
│   └── gold_sop_assortment_allocation.md
│
└── orchestration/
    ├── monthly_calendar.md      (in-season IBP cycle)
    └── preseason_calendar.md    (OTB → Assortment → PO → Allocation)
```

## Rule

1. **Bronze immutable.** Không sửa file trong bronze. Replace = thêm file mới với version suffix.
2. **Silver mỗi table 1 file data.** Naming `stg_<entity>.<ext>`. (Spec docs thì gộp theo module — `silver_<module>.md`.)
3. **Gold append-only.** Snapshot daily → không xoá history.
4. **Cowork đọc gold cho dashboard, đọc silver cho ad-hoc analysis.** Tuyệt đối không đọc bronze trực tiếp (trừ khi Huy yêu cầu debug).
