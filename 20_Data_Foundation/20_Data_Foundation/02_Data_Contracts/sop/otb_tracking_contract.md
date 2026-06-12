# Data Contract — OTB Tracking (In-Season)

## Mục đích
Track OTB thực thi hàng tháng trong mùa: đã commit bao nhiêu (PO placed), đã nhận bao nhiêu (inbound), còn lại bao nhiêu (Open OTB), và projection tồn cuối mùa. Là bảng chính của Financial Review (IBP W3.5) và slide OTB trong Pre-S&OP deck.

## Source
- **Derived** — không có file raw riêng. ETL tính từ: `otb_plan` (locked version) + `fact_po` (committed/received) + `fact_sales` (actual) + `fact_inventory_snapshot` (tồn thực).
- Owner: Planning Manager
- Tần suất: Refresh monthly tại W3.5 (trước Financial Review); cho phép refresh ad-hoc

## Grain
1 row = (`snapshot_period_yyyymm` × `season` × `channel_id` × `division` × `category`)

## Schema

| Cột | Kiểu | Bắt buộc | Mô tả | Ví dụ |
|---|---|---|---|---|
| `snapshot_period_yyyymm` | string | Y | Tháng chốt số | "202606" |
| `otb_version` | string (FK) | Y | Version OTB plan đang track (locked) | "OTB_SS26_v2" |
| `season` | string | Y | | "SS26" |
| `channel_id` | string (FK) | Y | | "ALL_ONLINE" |
| `division` | string | Y | | "Menswear" |
| `category` | string | Y | | "Tops" |
| `otb_plan_value_vnd` | bigint | Y | Ngân sách plan lũy kế season-to-date | 12500000000 |
| `committed_value_vnd` | bigint | Y | PO placed lũy kế (at cost, mọi status trừ cancelled) | 11800000000 |
| `received_value_vnd` | bigint | Y | Đã inbound lũy kế (at cost) | 9400000000 |
| `open_otb_value_vnd` | bigint | Y | **= otb_plan − committed** | 700000000 |
| `commit_pct` | float | Y | committed / plan × 100 | 94.4 |
| `actual_sales_value_vnd` | bigint | Y | Net sales lũy kế season-to-date | 8900000000 |
| `actual_markdown_vnd` | bigint | Y | Markdown đã dùng lũy kế | 410000000 |
| `markdown_budget_used_pct` | float | Y | actual / budget × 100 | 58.6 |
| `ending_inv_value_vnd` | bigint | Y | Tồn thực cuối period (at cost) | 7100000000 |
| `forward_cover_wks` | float | Y | ending_inv / avg weekly planned sales (cost) 8 tuần tới | 11.2 |
| `projected_eos_inv_value_vnd` | bigint | Y | Projection tồn end-of-season = ending_inv + open PO − planned sales còn lại | 3200000000 |
| `otb_status` | enum | Y | open / fully_committed / overbought | "open" |
| `action_flag` | enum | N | none / hold_po / cancel_po / chase / release_otb | "none" |

## Primary Key
`(snapshot_period_yyyymm, otb_version, season, channel_id, division, category)`

## Validation rules
- `open_otb_value_vnd = otb_plan_value_vnd − committed_value_vnd` (exact)
- `received_value_vnd <= committed_value_vnd`
- `open_otb < 0` → `otb_status = overbought`, bắt buộc `action_flag` ≠ none
- `forward_cover_wks > forward_cover_target_wks × 1.5` → suggest `action_flag = hold_po`
- Append-only: không sửa row của period đã chốt

## Ví dụ 3 row

```
period | season | channel    | category | plan  | committed | open_otb | commit% | fwd_cover | status
202606 | SS26   | ALL_ONLINE | Tops     | 12.5B | 11.8B     | 0.7B     | 94.4    | 11.2w     | open
202606 | SS26   | ALL_ONLINE | Bottoms  | 5.2B  | 5.6B      | -0.4B    | 107.7   | 16.8w     | overbought
202606 | SS26   | ALL_STORE  | Tops     | 18.0B | 15.1B     | 2.9B     | 83.9    | 9.5w      | open
```

## Downstream
- Dashboard: `22_Dashboards_Reports/sop_otb_dashboard.md` (Tab OTB Tracking)
- Skill: `sop-deck-builder` — slide Financial Review; `demand-analysis-inseason` — quyết định reorder phải check Open OTB trước
- MBR: escalate mọi cell `overbought` chưa có action

## Anti-pattern
- ❌ Reorder in-season khi `open_otb <= 0` mà không có CFO approval → vượt ngân sách âm thầm
- ❌ Track OTB at retail value trong khi PO at cost → so sánh sai
- ❌ Bỏ qua markdown đã dùng → cuối mùa thiếu ngân sách clear hàng
- ❌ Chỉ track commit %, không track forward cover → commit đúng budget nhưng tồn vẫn phình
