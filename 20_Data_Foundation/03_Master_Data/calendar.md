# Master Data — Calendar

## Mục đích
Mọi thời gian đều join về `dim_calendar`. Không tự generate week/month trong dashboard.

## Phạm vi
- Range: 2023-01-01 đến 2030-12-31 (dài để cover plan dài hạn)
- Grain: 1 row = 1 date

## Schema

| Cột | Kiểu | Mô tả |
|---|---|---|
| `date_id` | date PK | YYYY-MM-DD |
| `day_of_week` | int | 1=Mon..7=Sun |
| `iso_week` | int | 1-53 |
| `iso_year` | int | |
| `month` | int | 1-12 |
| `quarter` | int | 1-4 |
| `year` | int | |
| `fiscal_week` | int | Theo lịch FY công ty |
| `fiscal_month` | int | |
| `fiscal_quarter` | int | |
| `fiscal_year` | int | |
| `season` | string | "SS<YY>", "FW<YY>" |
| `season_week` | int | Tuần thứ N trong mùa |
| `is_weekend` | bool | |
| `is_holiday` | bool | |
| `holiday_name` | string nullable | "Tết", "Quốc khánh"… |
| `is_promo_day` | bool | 4.4, 5.5, 6.6, 7.7, 8.8, 9.9, 10.10, 11.11, 12.12 |
| `promo_event` | string nullable | "6.6 Sale", "11.11 Mega"… |
| `is_payday` | bool | 25, 30/31 hàng tháng |

## Season mapping (fashion VN)

| Season | Tháng |
|---|---|
| SS (Spring/Summer) | T3 → T8 |
| FW (Fall/Winter) | T9 → T2 năm sau |

## Promo events VN-specific

| Date | Event |
|---|---|
| Mùng 1 Tết → Mùng 7 | Tết sale + low demand week |
| 4.4 | Marketplace Sale |
| 5.5 | Marketplace Sale |
| 6.6 | Big sale (high traffic) |
| 8.3 | Quốc tế Phụ nữ — Womenswear push |
| 11.11 | Mega Sale — biggest of year |
| 12.12 | Year-end Sale |
| Black Friday | Western influence |

## File
- `calendar_master.xlsx` — generate 1 lần, edit chỉ khi thay holiday/promo
- Owner: Planning Manager
