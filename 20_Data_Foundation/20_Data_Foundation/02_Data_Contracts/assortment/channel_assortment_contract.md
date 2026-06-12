# Data Contract — Channel-Specific Assortment

## Mục đích
Một option có thể xuất hiện ở 1 hoặc nhiều channel với buy qty và pricing khác nhau. Bảng này map option × channel với eligibility và allocation %.

## Grain
1 row = (`season`, `style_id`, `color_id`, `channel_id`)

## Schema

| Cột | Kiểu | Mô tả |
|---|---|---|
| `season` | string | |
| `style_id`, `color_id` | string | |
| `channel_id` | string | |
| `is_eligible` | bool | Option này có bán ở channel này không |
| `channel_buy_qty_pcs` | int | Phần qty allocate cho channel |
| `channel_buy_pct` | float | % của tổng buy qty option đó |
| `channel_retail_price_vnd` | int | Có thể khác giữa channel |
| `launch_date` | date | Ngày launch ở channel |
| `exit_date` | date | Ngày EOL ở channel |
| `is_exclusive` | bool | Chỉ bán channel này (vd Shopee Exclusive) |
| `channel_strategy` | enum | flagship_first / marketplace_only / dtc_exclusive / outlet_clearance |

## Strategy logic

| Strategy | Mô tả | Channel mix |
|---|---|---|
| **flagship_first** | Launch store trước 2-4 tuần, sau đó online | Store → Website → Marketplace |
| **marketplace_only** | Chỉ bán Shopee/TikTok (high-volume basics) | Shopee + TikTok |
| **dtc_exclusive** | Chỉ website + app (newsletter base) | Website + App |
| **outlet_clearance** | Hàng cũ chỉ vào outlet | Outlet |

## Pricing rule (gợi ý)

| Channel | Retail price multiplier |
|---|---|
| Store Flagship | 1.00 (RRP) |
| Website DTC | 1.00 (RRP) |
| Shopee / TikTok | 0.92-0.98 (campaign-friendly) |
| Outlet | 0.50-0.70 |

## Downstream
- Feed vào allocation logic
- Drive channel-specific pricing in POS / ecom integration
- Used cho channel margin analysis

## Anti-pattern
- ❌ Cùng option, cùng price ở tất cả channel → không tận dụng channel mix
- ❌ Launch tất cả channel cùng lúc → cannibalize flagship traffic
- ❌ Để option core ở outlet sớm → giảm brand perception
