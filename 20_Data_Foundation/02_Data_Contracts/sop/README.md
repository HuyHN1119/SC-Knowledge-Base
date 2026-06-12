# Data Contracts — S&OP / OTB

Module S&OP cấp tài chính — kết nối Merchandise Planning (Assortment) với Financial Review (IBP step 4). OTB là cơ chế kiểm soát ngân sách mua hàng: plan trước mùa, track trong mùa, reconcile tại Financial Review hàng tháng.

## Vị trí trong module flow

```
Pre-season:   Sales & Margin Plan (Finance) ─► OTB Plan ─► Assortment Plan ─► PO commitment
                                                  │
In-season:                                   OTB Tracking (monthly)
                                                  │
                                             Financial Review (IBP W3.5) → MBR
```

## Contracts trong folder này

| Contract | Grain | Phase | Owner |
|---|---|---|---|
| `sales_margin_plan_contract.md` | plan_version × period × channel × category | Pre-season (input) | Finance + Planning |
| `otb_plan_contract.md` | season × period × channel × category | Pre-season (M-6, lock M-5) | Planning Manager |
| `otb_tracking_contract.md` | snapshot period × season × channel × category | In-season (monthly W3.5) | Planning Manager |

## Quan hệ với module khác

- **Assortment:** `assortment_plan_contract.md` phải reconcile `buy_value_total_vnd` về `otb_value_vnd` (±2%).
- **Supply:** `committed_value_vnd` trong OTB tracking = sum PO placed từ `po_contract.md`.
- **Inventory:** `beginning_inv_value_vnd` lấy từ `soh_snapshot_contract.md` (valued at cost).
- **Demand:** `planned_sales` cross-check với consensus forecast tại Demand Review.

## Layer

- OTB plan + tracking: **L1** (Channel × Division × Category) — đây là module duy nhất không drill xuống L2. Style/color/size do Assortment + Option Plan quản.
