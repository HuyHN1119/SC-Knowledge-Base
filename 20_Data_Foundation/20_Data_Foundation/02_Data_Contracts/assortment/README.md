# Assortment — Module

## Định nghĩa
**Assortment Planning** = quyết định **mua những option nào, bao nhiêu chiều rộng (option count) vs chiều sâu (qty/option)** trong mỗi category × channel × season.

Đây là output cốt lõi của Merchandiser/Buyer ở pre-season.

## Phân biệt với module khác

| Module | Quyết định | Khi nào |
|---|---|---|
| **Assortment Plan** | Bao nhiêu OPTION (style+color) trong mỗi category | Pre-season (3-6 tháng trước) |
| **OTB (Open-to-Buy)** | Tổng ngân sách mua | Pre-season — kèm Assortment |
| **Color/Size Curve** | Tỷ trọng color & size trong option | Pre-season |
| **Forecast** | Số qty bán theo tuần | In-season (sau khi assortment lock) |
| **Allocation** | Hàng nào về đâu | In-season (sau PO inbound) |

## Trade-off cốt lõi: Width vs Depth

| Strategy | Width (option) | Depth (qty/option) | Phù hợp |
|---|---|---|---|
| **Width-heavy** | Nhiều option, ít depth | Newness lớn, ít stockout risk per option | Fashion-forward, marketplace |
| **Depth-heavy** | Ít option, nhiều depth | Tận dụng top-seller | Core/basic, store flagship |

## Layer

| Layer | Áp dụng | Dùng cho |
|---|---|---|
| L1 | Channel × Category — option count + buy value | Buy plan presentation CEO |
| L2 | Style × Color — option detail | Merchandiser execution |

## Files trong module

| File | Vai trò |
|---|---|
| `assortment_plan_contract.md` | Option count + buy qty/value per category |
| `option_plan_contract.md` | Detailed list options (style+color) |
| `core_seasonal_mix_contract.md` | % core / fashion / seasonal mix |
| `channel_assortment_contract.md` | Assortment khác nhau theo channel |
| `option_lifecycle_contract.md` | Phân loại option theo lifecycle stage |

## KPI chính

| KPI | Công thức | Benchmark fashion mid-size |
|---|---|---|
| **Option Count** | # options unique trong category | Phụ thuộc category |
| **Avg Depth** | sum(buy_qty) / count(options) | 200-500 pcs/option (basic), 100-200 (fashion) |
| **Hit Rate** | % options đạt STR target | ≥ 70% |
| **New Option %** | % options mới so với last season | 40-60% |
| **Core Repeat %** | % core options carry over | 60-80% |
