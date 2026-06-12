# Data Contract — Core / Seasonal Mix

## Mục đích
Định nghĩa cấu trúc product portfolio theo lifecycle role. Drive markdown strategy + buy depth.

## Định nghĩa

| Type | Đặc điểm | Buy depth | Markdown strategy |
|---|---|---|---|
| **Core** | Bán quanh năm, repeat nhiều mùa (vd áo polo basic trắng) | Deep | Hiếm markdown, full price |
| **Seasonal Core** | Mùa cụ thể nhưng repeat (vd jacket FW) | Medium-deep | Markdown nhẹ end-of-season |
| **Fashion** | Trend mùa cụ thể, không repeat | Medium | Markdown 30-50% giữa mùa |
| **Fashion-forward** | Trendy, ngắn vòng đời | Shallow | Markdown sớm, clearance fast |
| **Seasonal Drop** | Limited drop / collab | Shallow | Full price only — clearance nếu remain |

## Grain
1 row = (`season`, `channel_id`, `category`, `mix_type`)

## Schema

| Cột | Kiểu | Mô tả |
|---|---|---|
| `season` | string | |
| `channel_id` | string | |
| `category` | string | |
| `mix_type` | enum | core / seasonal_core / fashion / fashion_forward / seasonal_drop |
| `qty_pct` | float | % qty trong category × channel |
| `value_pct` | float | % buy value |
| `option_count_pct` | float | % option count |
| `target_str_pct` | float | STR target cuối mùa |
| `target_markdown_pct` | float | % budget cho markdown |

## Mix recommendation (fashion mid-size VN)

| Channel | Core | Seasonal Core | Fashion | Fashion-fw | Seasonal Drop |
|---|---|---|---|---|---|
| Store Flagship | 35% | 25% | 25% | 10% | 5% |
| Shopee/TikTok | 25% | 15% | 35% | 20% | 5% |
| Website DTC | 30% | 20% | 30% | 15% | 5% |
| Outlet | 60% | 30% | 10% | 0% | 0% |

→ Tham khảo, mỗi brand có DNA khác.

## Downstream
- Drive option_plan classification
- Feed vào markdown calendar (skill: `inventory-health-check`)
