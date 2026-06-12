# Silver Spec — S&OP, Assortment, Allocation

> Contract đích: `02_Data_Contracts/sop/`, `assortment/`, `allocation/`.

## Domain S&OP (MỚI — v0.3)

### stg_sales_margin_plan
- **Source:** `bronze/sop/sales_margin_plan_<FY>_v<n>.xlsx` — **Contract:** `sop/sales_margin_plan_contract.md`
- **Refresh:** annual + quarterly RF
- Transform: conform channel group (ALL_ONLINE/ALL_STORE phải tồn tại trong dim_channel dạng group); validate margin formula.

### stg_otb_plan
- **Source:** `bronze/sop/otb_plan_<season>_v<n>.xlsx` — **Contract:** `sop/otb_plan_contract.md`
- **Refresh:** per season + REFC version
- Transform: validate công thức OTB (±1%); chain check begin/end inventory giữa các tháng; chỉ 1 version `locked` per season.

> OTB tracking KHÔNG có stg — là derived fact, build trực tiếp ở gold (`gold_sop_assortment_allocation.md`).

## Domain Assortment

### stg_assortment_plan
- **Source:** `bronze/assortment/assortment_plan_<season>.xlsx` — **Contract:** `assortment/assortment_plan_contract.md`
- Transform: validate new + core_repeat = total; core+fashion+seasonal = 100%; reconcile buy_value vs OTB plan (±2%) — fail thì flag, không block.

### stg_option_plan
- **Source:** `bronze/assortment/option_plan_<season>.xlsx` — **Contract:** `assortment/option_plan_contract.md`
- Transform: mỗi option (style × color) map về assortment plan cell cha; option chưa có style_id (style mới chưa tạo mã) → giữ `style_id_tbd`, flag.

### stg_channel_assortment
- **Source:** `bronze/assortment/channel_assortment_<season>.xlsx` — **Contract:** `assortment/channel_assortment_contract.md`

### stg_option_lifecycle
- **Source:** derived từ option_plan + dim_product + sales actual — **Contract:** `assortment/option_lifecycle_contract.md`
- **Refresh:** weekly in-season

## Domain Allocation

### stg_initial_allocation
- **Source:** `bronze/allocation/initial_alloc_<season>_w<wave>.xlsx` — **Contract:** `allocation/initial_allocation_contract.md`
- Transform: validate to_location active + sellable; sum alloc qty ≤ inbound qty của wave.

### stg_replen_allocation
- **Source:** derived (output của allocation-engine skill, lưu lại làm audit) — **Contract:** `allocation/replenishment_allocation_contract.md`
- **Refresh:** daily/weekly

### stg_transfer
- **Source:** `bronze/allocation/transfer_log_<YYYYMM>.csv` — **Contract:** `allocation/transfer_contract.md`
- Transform: from/to location map; transfer_cost từ phí vận chuyển nội bộ nếu có, else standard cost per pc-km.

## Quy tắc chung

1. Mọi bảng plan (OTB, assortment, option) **giữ mọi version** — đo plan vs actual cần version gốc.
2. Pre-season data thưa (1 lần/mùa) — pipeline không chạy daily cho domain này, chỉ chạy khi có file mới.
