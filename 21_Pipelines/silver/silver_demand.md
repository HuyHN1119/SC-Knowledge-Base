# Silver Spec — Demand (stg_forecast, stg_color_curve, stg_size_curve)

> Contract đích: `20_Data_Foundation/02_Data_Contracts/demand/`. Silver = bronze đã clean + conform theo contract. Spec này mô tả **transform logic**, schema xem contract.

## stg_forecast

- **Source:** `bronze/demand/forecast_<YYYYMM>_v<n>.xlsx`
- **Contract:** `forecast_contract.md`
- **Refresh:** khi có forecast mới (weekly rolling, monthly consensus)

| Transform | Logic |
|---|---|
| Header | Bỏ header trang trí nếu có, detect data row đầu |
| Conform keys | `channel_id` upper-case match dim_channel; style/color match dim_product qua product_hierarchy |
| Version | Gán `forecast_version` từ tên file nếu thiếu trong cột |
| Dedup | PK (version, year, week, channel, style, color) — keep latest `_ingested_at` |

**Validation (theo contract):** qty ≥ 0; 1 row/PK/version; quarantine SKU lạ.

## stg_color_curve

- **Source:** `bronze/demand/color_curve_<season>.xlsx` — **Contract:** `color_curve_contract.md`
- **Refresh:** pre-season + revision
- Transform chính: validate sum % các color trong 1 style = 100 (±0.5); flag style thiếu curve.

## stg_size_curve

- **Source:** `bronze/demand/size_curve_<season>.xlsx` — **Contract:** `size_curve_contract.md`
- **Refresh:** pre-season + revision (in-season nếu actual lệch curve > threshold)
- Transform chính: validate sum % size trong 1 (style × color) = 100 (±0.5); map `size_label` → `size_id`.

## Quy tắc chung domain demand

1. Forecast cấp style+color; explode xuống size **chỉ ở gold** (fact_replenishment) qua size_curve — không explode ở silver.
2. Mọi version forecast giữ nguyên — không overwrite (đo WMAPE cần version lock tại thời điểm).
3. File curve thiếu cho style mới → dùng curve của sub_category (fallback declare trong gold spec), flag `curve_source = fallback`.
