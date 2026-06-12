# Gold Spec — S&OP, Assortment, Allocation

## fact_otb (MỚI — v0.3)

- **Grain:** snapshot_period × otb_version × season × channel × division × category
- **Source:** stg_otb_plan (locked) + fact_po (committed/received at cost) + fact_sales (actual STD) + fact_inventory_snapshot (valued) + stg_sales_margin_plan (markdown budget)
- **Refresh:** monthly W3.5 + ad-hoc
- **Schema & validation:** theo `sop/otb_tracking_contract.md` (open_otb, commit_pct, forward_cover_wks, projected_eos_inv, otb_status, action_flag)
- **Logic mapping:**
  - `committed_value` = Σ (order_qty × unit_cost) fact_po, status ≠ cancelled, season match, group lên channel đích × category
  - `forward_cover` = ending_inv_cost / avg weekly planned sales cost 8 tuần tới (từ sales_margin_plan hoặc consensus forecast — ưu tiên consensus)
  - PO không gắn channel đích → phân bổ theo tỷ trọng sales plan channel (declare rõ trong cột `alloc_method`)

## fact_plan_vs_actual

- **Grain:** period_yyyymm × channel × division × category
- **Source:** stg_sales_margin_plan (BUDGET + RF) + fact_sales + markdown actual
- **Refresh:** weekly
- **Cột:** target_net_revenue (budget/rf), actual_net_revenue, attainment_pct, target_gm_pct, actual_gm_pct, markdown_budget, markdown_actual, markdown_used_pct
- **Dùng cho:** Financial Review (Tab 3 sop_otb_dashboard) + MBR slide 1

## fact_assortment_plan_vs_actual

- **Grain:** season × channel × category × sub_category
- **Source:** stg_assortment_plan + actual từ fact_po (options bought, buy value) + fact_sales (STR per category)
- **Refresh:** weekly in-season
- **Cột chính:** option_count plan/actual, buy_value plan/actual, newness plan/actual, str_target/actual, hit_rate

## fact_option_performance

- **Grain:** snapshot_date × style × color (option)
- **Source:** fact_sales + fact_inventory_health + stg_option_plan + stg_option_lifecycle
- **Refresh:** weekly
- **Cột:** str_pct, margin_pct, revenue_std, wos, lifecycle_stage, quadrant (winner / volume_driver / margin_driver / loser), markdown_candidate_flag

## fact_allocation

- **Grain:** alloc_id × SKU × to_location
- **Source:** stg_initial_allocation + stg_replen_allocation, đối chiếu inbound thực tế tại to_location
- **Refresh:** daily
- **KPI derive:** fulfillment_pct (received/requested), allocation_accuracy (sau 4 tuần: sales ratio thực vs alloc ratio)

## fact_transfer

- **Grain:** transfer_id × SKU
- **Source:** stg_transfer
- **Refresh:** daily
- **Derived:** transfer_cost_vnd, repeat_transfer_flag (SKU chuyển 2+ lần/30 ngày → allocation issue), transfer_cost_pct_revenue (monthly rollup)

## Views

### vw_otb_l1
- **Grain:** season × channel — tổng hợp cho MBR 1 slide: plan / committed / open / forward cover / # cells overbought

### vw_option_performance
- **Grain:** style × color, latest snapshot — alias view cho dashboard allocation_assortment (Tab 2)
