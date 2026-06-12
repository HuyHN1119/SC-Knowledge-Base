# Silver Spec — Supply (stg_pr, stg_po, stg_intake, stg_inbound)

> Contract đích: `20_Data_Foundation/02_Data_Contracts/supply/`. 4 bảng theo pipeline stage: PR → PO → Intake → Inbound.

## stg_pr

- **Source:** `bronze/supply/pr_master_<YYYYMM>.xlsx` — **Contract:** `pr_contract.md`
- **Refresh:** weekly
- Transform: conform category/style keys; map status text tự do → enum chuẩn; link `pr_id` → `po_number` khi PO được tạo.

## stg_po

- **Source:** `bronze/supply/po_master_<YYYYMM>.xlsx` — **Contract:** `po_contract.md`
- **Refresh:** daily
- **Đây là bảng quan trọng nhất domain supply.**

| Transform | Logic |
|---|---|
| Vendor match | Tên vendor → `vendor_id` qua alias lookup (dim_vendor). Không match → quarantine |
| SKU explode | PO theo colorway + size breakdown → 1 row per (po × line × SKU) |
| Status map | "Done"/"Chưa về hết hàng"/"Đang sản xuất"/"Đơn đặt vải" → enum: `closed / partial / in_production / fabric_ordered` |
| Date conform | "Ngày DK về mới nhất" → `confirmed_delivery_date`; giữ `requested_delivery_date` gốc để tính OTIF |
| Dedup | PK (po_number, line_no, sku) — keep latest |

**Validation:** order_qty > 0; confirmed_date ≥ po_date; PO không vendor_id → quarantine.

## stg_intake

- **Source:** `bronze/supply/intake_log_<YYYYMM>.xlsx` — **Contract:** `intake_contract.md`
- **Refresh:** daily
- Transform: link `po_number + line` ; intake_qty cộng dồn nhiều đợt per line.

## stg_inbound

- **Source:** `bronze/supply/inbound_log_<YYYYMM>.csv` (WMS GRN) — **Contract:** `inbound_contract.md`
- **Refresh:** daily
- Transform: 1 row per (grn_id × SKU); link về PO line; `inbound_qty` ≤ intake_qty per line (tolerance QC reject).

## Quy tắc chung domain supply

1. **OTIF tính trên stg_inbound vs requested_delivery_date** (không phải confirmed — confirmed là số vendor xin dời).
2. PO cancelled giữ row, status `cancelled` — Open OTB cần loại chúng ra.
3. Mọi qty đơn vị pcs; value at cost (`unit_cost_vnd`).
