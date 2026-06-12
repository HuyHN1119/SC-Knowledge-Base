# Gold Spec — Supply (fact_po, vw_supply_otif_monthly)

## fact_po

- **Grain:** po_number × line_no × SKU — **bảng "1 PO line nhìn được cả pipeline"**
- **Source:** stg_po + stg_intake + stg_inbound (left join theo po line)
- **Refresh:** daily

| Nhóm cột | Cột |
|---|---|
| Key | po_number, po_line_no, vendor_id, style_id, color_id, size_id, season |
| Order | order_qty_pcs, unit_cost_vnd, po_date, requested_delivery_date, confirmed_delivery_date |
| Pipeline | intake_qty_pcs, intake_date, inbound_qty_pcs, inbound_date, status (fabric_ordered/in_production/partial/closed/cancelled) |
| Derived | days_late (inbound − requested), fill_rate_pct (inbound/order), is_otif (per glossary: on-time AND ≥95% full), open_qty_pcs (order − inbound, status ≠ cancelled), committed_value_vnd (order_qty × cost) |

**Rule:**
- `is_otif` chốt khi line closed; line đang mở không tính vào OTIF mẫu số
- `open_qty` của status cancelled = 0 (đã huỷ không còn là pipeline)
- Delay risk flag: chưa inbound + confirmed_date < today + 7 → `risk = late_risk`

## Views

### vw_supply_otif_monthly
- **Grain:** month × vendor × category
- **KPI:** otif_pct, otd_pct, if_pct, fill_rate, avg_days_late, late_po_pct, lead_time_actual_avg vs dim_vendor.lead_time_days_avg
- **Dùng cho:** vendor scorecard (W3 monthly) + dashboard supply
- **Rule:** chỉ tính line closed trong tháng; tối thiểu 3 PO lines/vendor/tháng mới hiện score (sample nhỏ → "n/a")

### vw_po_pipeline_weekly
- **Grain:** week (ETA) × channel-destination × category
- Tổng open_qty + committed_value theo tuần ETA — feed WOS-with-intransit và OTB tracking
