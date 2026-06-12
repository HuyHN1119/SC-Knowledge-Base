# Data Contract — Sales & Margin Plan

## Mục đích
Financial plan top-down từ Finance: target doanh thu, margin, markdown budget theo tháng × channel × category. Là input đầu tiên của OTB và là baseline so sánh tại Financial Review (IBP step 4).

## Source
- File raw: `21_Pipelines/bronze/sop/sales_margin_plan_<FY>_v<n>.xlsx`
- Owner: Finance (build) + CEO (approve annual budget)
- Tần suất: 1 lần/năm (annual budget) + re-forecast quarterly (version mới)

## Grain
1 row = (`plan_version` × `period_yyyymm` × `channel_id` × `division` × `category`)

## Schema

| Cột | Kiểu | Bắt buộc | Mô tả | Ví dụ |
|---|---|---|---|---|
| `plan_version` | string | Y | "BUDGET_FY27" / "RF_2026Q3" | "BUDGET_FY27" |
| `period_yyyymm` | string | Y | | "202703" |
| `channel_id` | string (FK) | Y | Cho phép group ALL_ONLINE / ALL_STORE | "ALL_ONLINE" |
| `division` | string | Y | | "Menswear" |
| `category` | string | Y | | "Tops" |
| `target_gross_revenue_vnd` | bigint | Y | Doanh thu gộp (trước discount) | 5200000000 |
| `target_net_revenue_vnd` | bigint | Y | Sau discount/return | 4500000000 |
| `target_cogs_vnd` | bigint | Y | | 1710000000 |
| `target_gross_margin_pct` | float | Y | (net_rev − cogs) / net_rev | 62.0 |
| `markdown_budget_vnd` | bigint | Y | Ngân sách markdown cho phép | 350000000 |
| `target_inventory_value_vnd` | bigint | N | Tồn target cuối period (at cost) — cho DIO/turnover target | 6200000000 |
| `target_turnover` | float | N | Turnover annualized target | 4.0 |
| `notes` | string | N | | "Gồm campaign 3.3" |

## Primary Key
`(plan_version, period_yyyymm, channel_id, division, category)`

## Validation rules
- `target_gross_margin_pct` khớp công thức từ net_revenue và cogs (±0.5 điểm %)
- `target_net_revenue_vnd <= target_gross_revenue_vnd`
- Sum 12 tháng của BUDGET version = annual budget đã approve (reconcile với Finance)
- Chỉ 1 version BUDGET active per fiscal year; RF không overwrite BUDGET

## Ví dụ 3 row

```
plan_version | period | channel    | category | gross_rev | net_rev | cogs  | gm_pct | markdown_budget
BUDGET_FY27  | 202703 | ALL_ONLINE | Tops     | 5.2B      | 4.5B    | 1.71B | 62.0   | 350M
BUDGET_FY27  | 202703 | ALL_STORE  | Tops     | 7.5B      | 6.8B    | 2.45B | 64.0   | 420M
BUDGET_FY27  | 202703 | ALL_ONLINE | Bottoms  | 2.1B      | 1.8B    | 0.72B | 60.0   | 150M
```

## Downstream
- Input bắt buộc cho `otb_plan_contract.md` (planned_sales lấy từ đây hoặc consensus forecast, lấy số đã align)
- `fact_plan_vs_actual` ở gold — Financial Review slide "Plan vs Actual vs Forecast"
- Skill: `sop-deck-builder` (Financial Review section), `otb-plan-builder`

## Anti-pattern
- ❌ OTB dùng số planned_sales khác với consensus forecast mà không ghi chú gap → 2 bộ số "song song", mất single source of truth
- ❌ Re-forecast đè lên BUDGET version → mất baseline đo attainment
- ❌ Plan chỉ có revenue, không có margin/markdown → không tính được OTB at cost
