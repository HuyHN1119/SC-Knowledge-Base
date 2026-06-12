# Bronze Layer — Raw Data

## Nguyên tắc
- **Không sửa.** File gốc từ source, không format, không rename cột.
- **Không xoá.** Lưu để audit.
- **Timestamp + source.** Mỗi file phải có ngày trong tên file.

## Cấu trúc

```
bronze/
├── demand/
│   ├── forecast_20260601_v1.xlsx
│   ├── color_curve_ss26.xlsx
│   └── size_curve_ss26.xlsx
│
├── supply/
│   ├── pr_master_202606.xlsx
│   ├── po_master_202606.xlsx
│   ├── intake_log_202606.xlsx
│   └── inbound_log_202606.csv
│
├── inventory/
│   └── soh_20260601.csv     (daily snapshot)
│
├── sales/
│   ├── shopee_20260601_20260607.csv
│   ├── tiktok_20260601_20260607.csv
│   ├── website_20260601_20260607.csv
│   └── pos_20260601_20260607.csv
│
├── sop/
│   ├── sales_margin_plan_FY27_v1.xlsx
│   └── otb_plan_ss27_v1.xlsx
│
├── assortment/
│   ├── assortment_plan_ss27.xlsx
│   ├── option_plan_ss27.xlsx
│   └── channel_assortment_ss27.xlsx
│
├── allocation/
│   ├── initial_alloc_ss27_w1.xlsx
│   └── transfer_log_202606.csv
│
└── master/
    ├── product_master_20260601.xlsx
    ├── channel_master.xlsx
    ├── vendor_master.xlsx
    └── location_master.xlsx
```

## File expected (mỗi tháng)

| File | Nguồn | Tần suất | Owner |
|---|---|---|---|
| `forecast_<YYYYMM>_v<n>.xlsx` | Demand Planner | Tuần (rolling), Tháng (consensus) | Demand |
| `pr_master_<YYYYMM>.xlsx` | Planning/Procurement | Weekly | Planning |
| `po_master_<YYYYMM>.xlsx` | Procurement system | Daily refresh | Procurement |
| `intake_log_<YYYYMM>.xlsx` | Vendor portal | Daily | Vendor coord |
| `inbound_log_<YYYYMM>.csv` | WMS | Daily | WH |
| `soh_<YYYYMMDD>.csv` | WMS | Daily (EoD) | WH |
| `shopee_<dateRange>.csv` | Shopee Seller Center | Weekly | Ecom |
| `tiktok_<dateRange>.csv` | TikTok Shop | Weekly | Ecom |
| `website_<dateRange>.csv` | Shopify/Haravan | Weekly | Ecom |
| `pos_<dateRange>.csv` | POS | Weekly | Retail Ops |

## File expected (per season / per năm)

| File | Nguồn | Tần suất | Owner |
|---|---|---|---|
| `sales_margin_plan_<FY>_v<n>.xlsx` | Finance | Annual + quarterly RF | Finance |
| `otb_plan_<season>_v<n>.xlsx` | Planning | Per season (M-6) | Planning Manager |
| `assortment_plan_<season>.xlsx` | Merchandising | Per season (M-5) | Head of Buying |
| `option_plan_<season>.xlsx` | Merchandising | Per season (M-4) | Merchandiser |
| `channel_assortment_<season>.xlsx` | Merchandising | Per season | Merchandiser |
| `initial_alloc_<season>_w<wave>.xlsx` | Planning | Per wave | Allocator |
| `transfer_log_<YYYYMM>.csv` | WMS | Daily | WH |
| `location_master.xlsx` | ERP | Khi đổi | Retail Ops |
