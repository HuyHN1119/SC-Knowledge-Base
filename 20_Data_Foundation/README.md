# 20_Data_Foundation

Tầng nền tảng dữ liệu. Đây là **single source of truth** cho schema, master data, naming convention. Mọi pipeline, dashboard, skill phải tham chiếu về đây.

## Cấu trúc

| Folder | Vai trò | Khi nào đọc |
|---|---|---|
| `01_Data_Model/` | Conceptual & logical model — star schema (fact + dim) | Khi thiết kế dashboard mới |
| `02_Data_Contracts/` | Schema chi tiết từng bảng: cột, kiểu, key, đơn vị | Khi parse file raw hoặc viết ETL |
| `03_Master_Data/` | Dictionary cho product hierarchy, channel, calendar, vendor, location | Khi reconcile dữ liệu giữa nguồn |
| `04_Naming_Conventions/` | Quy ước đặt tên file, field, code | Khi tạo file mới |
| `05_Glossary/` | Định nghĩa KPI (WOS, STR, OTIF, MAPE…) | Khi tranh luận về cách tính |

## Nguyên tắc

1. **Contract trước, data sau.** Không nhận file vào pipeline nếu chưa có `.md` contract tương ứng.
2. **ID ổn định.** `style_id`, `color_id`, `size_id` không đổi qua mùa.
3. **Snake_case toàn bộ.** Không có cột `Tháng 6`, chỉ có `month` (int) hoặc `period_yyyymm` (string).
4. **Đơn vị viết kèm tên cột.** `sales_qty_pcs`, `revenue_vnd`, không có cột chỉ tên `sales`.
5. **Layer 1 + Layer 2 hỗ trợ cùng lúc.** Mọi fact table có đủ key cho cả 2 mức drill.
