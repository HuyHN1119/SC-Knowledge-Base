# Naming Convention — Files

## Pattern

```
<YYYYMMDD>_<module>_<scope>_<version>.<ext>
```

| Component | Quy tắc |
|---|---|
| `YYYYMMDD` | Ngày tạo file (snapshot date) |
| `module` | demand / supply / inventory / master / report |
| `scope` | forecast / sales / po / soh / wos…(viết snake_case) |
| `version` | v1, v2, v_final, v_consensus |
| `ext` | xlsx / csv / pbix / pptx / md |

## Ví dụ

```
20260601_demand_forecast_v_consensus.xlsx
20260605_supply_po_master.xlsx
20260606_inventory_soh_snapshot.csv
20260608_demand_actual_vs_forecast_w23.xlsx
20260610_report_sop_deck_v1.pptx
```

## Quy tắc

1. **Date format ISO** — luôn `YYYYMMDD`, không có `_`, không có `/`, không có chữ tháng.
2. **Snake_case toàn bộ** — không có dấu cách, không có dấu tiếng Việt, không có chữ hoa.
3. **Version ở cuối** — dễ sort, dễ tìm latest.
4. **Một mục đích = một file** — không gộp forecast và sales vào cùng 1 sheet trừ khi là pivot tổng hợp.
5. **Không có "final"** — luôn dùng `v1`, `v2`… Nếu thật sự final → `v_locked` (chỉ admin update).

## Anti-patterns

❌ `Forecast tháng 6 cuối cùng (1).xlsx`
❌ `PO List - 06-2026 final FINAL.xlsx`
❌ `SOH 1/6/2026.csv`
❌ `Báo cáo S&OP T6.pptx`
