# Master Data — Channel Hierarchy

## Cấu trúc

```
Channel Type
 └── Platform Group
      └── Channel
           └── (Sub-channel / Store nếu cần)
```

## Bảng channels (gợi ý fashion VN)

| channel_type | platform_group | channel_id | channel_name |
|---|---|---|---|
| online | Marketplace | SHOPEE | Shopee Mall |
| online | Marketplace | TIKTOK | TikTok Shop |
| online | Marketplace | LAZADA | Lazada Mall |
| online | Marketplace | TIKI | Tiki |
| online | DTC Web | WEBSITE | Brand.com |
| online | DTC App | APP_IOS | Mobile App iOS |
| online | DTC App | APP_AND | Mobile App Android |
| offline | Retail Store | STORE_<CODE> | Flagship/Standard stores |
| offline | Retail Outlet | OUTLET_<CODE> | Outlet stores |
| b2b | Wholesale | B2B_<NAME> | B2B partners |

## Cột bổ sung

| Cột | Mô tả |
|---|---|
| `channel_id` | PK ổn định |
| `parent_channel_id` | Null cho top level; có giá trị cho store thuộc 1 platform group |
| `region` | "HCM", "HN", "DN"… (cho offline) |
| `fulfillment_model` | self_ship / marketplace_fbs / 3pl |
| `cost_to_serve_pct` | Tham chiếu cho margin calc |
| `is_active` | bool |
| `launched_date` | date |

## Rule reconcile

- Mỗi đơn hàng phải mapped về 1 `channel_id` chính.
- Nếu nhiều kho phục vụ cùng channel → `location_id` lưu kho, `channel_id` lưu kênh bán.
- Không tạo `channel_id` mới khi chỉ là campaign — campaign tracking dùng `dim_calendar.is_promo_day` + `promo_code`.
