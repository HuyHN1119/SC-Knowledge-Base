# Silver Spec — Dimensions

> Spec gộp theo module (quyết định 2026-06-12). Build 5 dim từ master data. Mọi stg_/fact_ join về đây.

## dim_product

- **Source:** `bronze/master/product_master_<YYYYMMDD>.xlsx` + `20_Data_Foundation/03_Master_Data/product_hierarchy.md`
- **Grain:** 1 row = 1 SKU (`style_id` × `color_id` × `size_id`)
- **Refresh:** Weekly (W1 mỗi tháng bắt buộc — style mới launch/EOL)

| Transform | Logic |
|---|---|
| Tách SKU code | Mã ERP (vd `GSTP05172PE04SB_WH-M`) → parse `style_id`, `color_id`, `size_id` theo product_hierarchy |
| Hierarchy | Map LEVEL0→brand, LEVEL3→category, LEVEL4→sub_category; thêm `division` |
| Lifecycle | Gán `lifecycle_stage` (new/core/aging/clearance) theo `season_launched` + PL hàng (Hàng phễu → core) |
| SCD | Type 1 (overwrite) cho attribute; `style_id/color_id/size_id` không bao giờ đổi |

**Validation:** SKU duplicate → fail build; SKU mới chưa có hierarchy → quarantine + ping merchandiser.

## dim_channel

- **Source:** `bronze/master/channel_master.xlsx` + `channel_hierarchy.md`
- **Grain:** 1 row = 1 channel. **Refresh:** khi đổi.
- Thêm row group ảo `ALL_ONLINE`, `ALL_STORE`, `ALL` cho L1 aggregate (flag `is_group = true`).

## dim_vendor

- **Source:** `bronze/master/vendor_master.xlsx` + `vendor_master.md`
- **Grain:** 1 row = 1 vendor. **Refresh:** khi đổi; `otif_score_last_6m` update monthly bởi scorecard job.
- Build alias lookup table (`vendor_alias` tách `;`) cho fuzzy match khi ingest PO.

## dim_calendar

- **Source:** generate bằng script + `calendar.md` (promo days, fiscal calendar, season map)
- **Grain:** 1 row = 1 ngày, build 3 năm (Y-1, Y, Y+1). **Refresh:** 1 lần/năm + khi thêm promo day.
- Season map: tháng 3-8 → SS, 9-2 → FW (theo CLAUDE.md root). Promo days: 4.4, 5.5, 6.6, 7.7, 8.8, 9.9, 10.10, 11.11, 12.12, Black Friday, Tết sale.

## dim_location

- **Source:** `bronze/master/location_master.xlsx` (ERP store master) + `location_master.md`
- **Grain:** 1 row = 1 location. **Refresh:** khi đổi.

## Output format

Mỗi dim lưu: `silver/dim_<name>.csv` (hoặc parquet khi có ETL) — naming theo `file_naming.md`.
