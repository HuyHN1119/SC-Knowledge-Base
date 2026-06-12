# Naming Convention — Fields

## Pattern

```
<entity>_<attribute>_<unit?>
```

| Component | Quy tắc |
|---|---|
| `entity` | snake_case noun: style, color, size, channel, vendor, po, sales… |
| `attribute` | qty, value, date, id, code, name, pct, status… |
| `unit` | pcs, vnd, days, weeks, pct — bắt buộc khi có đơn vị |

## Ví dụ chuẩn

| Field | Mô tả |
|---|---|
| `sales_qty_pcs` | Số lượng bán (cái) |
| `gross_revenue_vnd` | Doanh thu gross (VND) |
| `net_revenue_vnd` | Doanh thu net sau discount |
| `discount_vnd` | Tổng discount (VND) |
| `unit_cost_vnd` | Giá vốn 1 đơn vị |
| `lead_time_days` | Lead time (ngày) |
| `wos_weeks` hoặc `wos` (default = weeks) | WOS |
| `str_pct` | Sell-through % |
| `forecast_qty_pcs` | Forecast (cái) |

## Anti-patterns

❌ `sales` — đơn vị gì? qty hay value?
❌ `qty` — qty của cái gì?
❌ `Doanh thu` — có dấu, không snake_case
❌ `salesAmount` — camelCase

## ID vs Code

| Loại | Mô tả | Ví dụ |
|---|---|---|
| `_id` | Surrogate/stable ID, dùng để JOIN | `style_id = "STY-00123"` |
| `_code` | Human-readable code, có thể đổi | `style_code = "POL-NAVY-CLASSIC"` |
| `_name` | Display name | `style_name = "Áo Polo Cổ Trụ Cotton"` |

→ JOIN luôn dùng `_id`, hiển thị dùng `_name`.

## Boolean

- Prefix `is_` hoặc `has_`
- Ví dụ: `is_active`, `is_promo_day`, `has_intransit`

## Date vs Datetime

| Suffix | Kiểu |
|---|---|
| `_date` | DATE only (vd `po_date`, `inbound_date`) |
| `_at` | TIMESTAMP (vd `created_at`, `updated_at`) |

## Enum

Giá trị enum: snake_case, không viết hoa.
Ví dụ: `status = "in_production"`, không phải `"In Production"`.
