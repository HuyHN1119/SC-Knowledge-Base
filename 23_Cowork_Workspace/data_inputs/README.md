# data_inputs/ — Drop file ở đây

Nơi Huy drop file raw khi không có ETL tự động. Cowork sẽ:
1. Verify schema theo contract trong `20_Data_Foundation/02_Data_Contracts/`
2. Move sang `21_Pipelines/bronze/<domain>/` với tên file đã chuẩn hoá
3. Trigger downstream processing

## File expected & frequency

| File | Drop khi nào | Map vào contract |
|---|---|---|
| `forecast_<YYYYMM>_v<n>.xlsx` | Mỗi tuần (rolling) + W1 monthly | `demand/forecast_contract.md` |
| `po_master_<YYYYMM>.xlsx` | Daily (hoặc weekly batch) | `supply/po_contract.md` |
| `intake_log_<YYYYMM>.xlsx` | Daily | `supply/intake_contract.md` |
| `inbound_log_<YYYYMM>.csv` | Daily | `supply/inbound_contract.md` |
| `soh_<YYYYMMDD>.csv` | Daily EoD | `inventory/soh_snapshot_contract.md` |
| `shopee_<dateRange>.csv` | Weekly | `sales/sales_contract.md` |
| `tiktok_<dateRange>.csv` | Weekly | `sales/sales_contract.md` |
| `website_<dateRange>.csv` | Weekly | `sales/sales_contract.md` |
| `pos_<dateRange>.csv` | Weekly | `sales/sales_contract.md` |
| `color_curve_<season>.xlsx` | Pre-season + revision | `demand/color_curve_contract.md` |
| `size_curve_<season>.xlsx` | Pre-season + revision | `demand/size_curve_contract.md` |
| `sales_margin_plan_<FY>_v<n>.xlsx` | Annual + quarterly RF | `sop/sales_margin_plan_contract.md` |
| `otb_plan_<season>_v<n>.xlsx` | Per season (M-6) | `sop/otb_plan_contract.md` |
| `assortment_plan_<season>.xlsx` | Per season (M-5) | `assortment/assortment_plan_contract.md` |
| `option_plan_<season>.xlsx` | Per season (M-4) | `assortment/option_plan_contract.md` |
| `initial_alloc_<season>_w<wave>.xlsx` | Per wave | `allocation/initial_allocation_contract.md` |
| `transfer_log_<YYYYMM>.csv` | Daily/Weekly | `allocation/transfer_contract.md` |

## Khi drop file:

Nói với Cowork:
```
Huy: Đã drop <file>. Validate + ingest giúp.
```

Cowork sẽ:
1. Đọc contract tương ứng
2. Validate schema, PK, validation rules
3. Báo lỗi nếu fail
4. Move sang bronze nếu pass
5. Confirm xong + recommend bước tiếp theo (rebuild dashboard? trigger skill?)
