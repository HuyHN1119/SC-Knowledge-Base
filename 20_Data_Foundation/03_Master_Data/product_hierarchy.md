# Master Data — Product Hierarchy

## Cấu trúc 6 cấp

```
Division (L1)
 └── Category (L1)
      └── Sub-Category (L1)
           └── Style (L2)
                └── Color (L2)
                     └── Size (L2)  ← grain thấp nhất = SKU
```

## Ví dụ (fashion mid-size Vietnam)

| Division | Category | Sub-Category | Style code | Color | Size |
|---|---|---|---|---|---|
| Menswear | Tops | Polo | STY-00123 | Navy | S/M/L/XL/XXL |
| Menswear | Tops | T-shirt | STY-00214 | White | S/M/L/XL/XXL |
| Menswear | Bottoms | Chinos | STY-00301 | Khaki | 29/30/31/32/33/34/36 |
| Menswear | Outerwear | Jacket | STY-00402 | Black | M/L/XL/XXL |
| Womenswear | Tops | Blouse | STY-10101 | White | XS/S/M/L |
| Womenswear | Dresses | Mini Dress | STY-10220 | Floral | XS/S/M/L |

## ID convention

| Field | Format | Bắt buộc ổn định? |
|---|---|---|
| `division_id` | string 2 chars | Y — không đổi qua mùa |
| `category_id` | string 3 chars | Y |
| `sub_category_id` | string 4 chars | Y |
| `style_id` | "STY-NNNNN" | Y — không đổi qua mùa |
| `style_code` | merchandiser-friendly code | Có thể đổi |
| `color_id` | "COL-<NAME>" hoặc UUID | Y |
| `size_id` | size label trực tiếp | Y |

## Size groups chuẩn

| Size group | Sizes | Áp dụng cho |
|---|---|---|
| `apparel_unisex` | XS, S, M, L, XL, XXL, 3XL | Tops, Outerwear |
| `apparel_women` | XS, S, M, L | Womenswear-specific |
| `bottoms_waist` | 28, 29, 30, 31, 32, 33, 34, 36, 38 | Jeans, Chinos |
| `accessories_onesize` | OS | Belts, hats, scarves |
| `shoes_eu` | 35, 36, 37, 38, 39, 40, 41, 42, 43, 44 | Footwear |

## SKU code resolver

SKU = `{style_id}-{color_id}-{size_id}`

Ví dụ: `STY-00123-COL-NAVY-M`

## File expected
- `product_master.xlsx` — full list, refresh khi launch style mới
- Owner: Merchandiser
- Tần suất: Weekly append, không xoá history
