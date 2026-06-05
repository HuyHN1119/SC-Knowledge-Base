# 🏭 BƯỚC 3: SUPPLY REVIEW (Hoạch định & Thống nhất Nguồn Cung)
## Chuẩn SIOP Đa Quốc Gia (MNC Standard)

> [!NOTE]
> Sau khi nhận được "Unconstrained Demand Plan" từ nhóm Sales/Marketing, nhóm Supply Chain & Merchandising sẽ đánh giá xem hệ thống có khả năng sản xuất/nhập hàng để đáp ứng con số đó không. Quá trình này sẽ chuyển đổi "Unconstrained Demand" thành **"Constrained Demand"** (Nhu cầu đã bị giới hạn bởi năng lực thực tế).

---

## 1. Rough-Cut Capacity Planning (RCCP - Lên Kế hoạch Năng lực Thô)

Khác với lịch sản xuất chi tiết (MPS) theo từng ngày và từng size, RCCP ở mức SIOP chỉ nhìn vào **Tổng Công Suất (Aggregate Capacity)** của các xưởng hoặc nhà cung cấp chiến lược.

| Đơn vị Đo lường | Mục tiêu | Hành động nếu thiếu |
| :--- | :--- | :--- |
| **Giờ may / Chuyền may** | Đánh giá xưởng nội bộ | Tăng ca, mở thêm chuyền, thuê ngoài (Outsource) |
| **Sản lượng (PCs/Tháng)** | Đánh giá nhà cung cấp (FOB) | Khai thác NCC mới, đàm phán lại MOQ/Leadtime |
| **Mét vải (Fabric Yardage)**| Đánh giá nguyên phụ liệu | Đặt trước vải mộc (Greige fabric booking) |
| **Sức chứa Kho (Pallets)** | Đánh giá Logistics | Thuê kho ngoài, tăng tần suất luân chuyển (Cross-docking) |

### Template Cân đối Năng lực Sản xuất (RCCP)

| Nhà Cung Cấp / Nhóm Xưởng | Category Phụ Trách | Năng lực Tối đa (Capa/Tháng) | Unconstrained Demand Cần | Chênh lệch (Gap) | Cảnh báo | Quyết định Khắc phục |
| :--- | :--- | ---: | ---: | ---: | :---: | :--- |
| **Xưởng Nội Bộ A** | Áo thun (T-shirt) | 50,000 pcs | 65,000 pcs | - 15,000 | 🔴 Quá tải | Chuyển 15k sang NCC ngoài (Xưởng B). Chấp nhận giá vốn tăng 3%. |
| **Nhà máy Gia công B** | Quần Jeans, Kaki | 30,000 pcs | 25,000 pcs | + 5,000 | 🟢 An toàn | Giữ nguyên dự phòng cho kịch bản Upside. |
| **Kho Tổng C** | Tất cả | 150,000 pcs | 180,000 pcs | - 30,000 | 🔴 Quá tải | Kích hoạt thuê kho ngắn hạn (3 tháng) phục vụ dịp Tết. |

---

## 2. Ngân sách Mua hàng - Open-To-Buy (OTB Budget)

> [!CAUTION]
> **OTB là chốt chặn tài chính quan trọng nhất của Merchandise Planner.** Nếu OTB bị âm, bạn KHÔNG ĐƯỢC PHÉP tạo thêm Purchase Order (PO) mới, trừ khi được CEO/CFO phê duyệt ngân sách khẩn cấp. OTB giúp ngăn chặn nạn "Tồn kho chết" do mua vô tội vạ.

### Công thức tính OTB Tiêu chuẩn
```
OTB = [Dự báo Sales (Giá vốn)] + [Mục tiêu Tồn kho Cuối kỳ] - [Tồn kho Đầu kỳ] - [Hàng Đang Về (On-Order/In-transit)]
```

### Bảng Kiểm soát OTB (Theo Category)

**Áp dụng cho Tháng:** N+1 | **Đơn vị:** Triệu VNĐ (Giá vốn - COGS)

| Category | [1] Sales Forecast (Constrained) | [2] Target EOM Stock | [3] BOM Stock (Hiện tại) | [4] Hàng đang Về (On-Order) | [5] **OTB Còn Lại** | Đánh giá Status |
| :--- | ---: | ---: | ---: | ---: | ---: | :--- |
| **Tops (Áo)** | 3,000 | 6,000 | 4,000 | 2,000 | **3,000** | 🟢 Được phép mua thêm 3 tỷ. |
| **Bottoms (Quần)** | 2,000 | 4,000 | 4,500 | 2,500 | **- 1,000** | 🔴 ÂM OTB. Phải dừng đặt hàng! Hàng tồn đang quá cao. |
| **Accessories** | 500 | 1,000 | 800 | 300 | **400** | 🟡 Chú ý. |

*Ghi chú: EOM = End of Month (Cuối tháng); BOM = Beginning of Month (Đầu tháng)*

---

## 3. Quản trị Tuổi thọ Tồn kho (Inventory Health & Target)

Thay vì chỉ nhìn vào số lượng tồn kho (On-hand), MNCs đánh giá Sức khỏe Tồn kho dựa trên mục tiêu **Weeks of Supply (WOS) - Số tuần tồn kho** và **Tỷ lệ Tồn kho sạch**.

| KPI | Ý nghĩa | Tiêu chuẩn Ngành Fashion | Báo cáo Tháng này | Trạng thái |
| :--- | :--- | :--- | :--- | :--- |
| **WOS Trung bình** | Hàng tồn đủ bán trong bao nhiêu tuần? | **6 - 8 Tuần** | 9 Tuần | 🟡 Cần giảm mua, đẩy bán. |
| **Tỉ lệ Tồn Cũ (Aging Stock %)** | % Hàng nằm trong kho quá 6 tháng (2 mùa) | **< 10%** tổng tồn kho | 15% | 🔴 Cần lập kế hoạch thanh lý (Liquidation). |
| **Inventory Turnover (Vòng quay Tồn kho)** | Số vòng luân chuyển hàng hóa mỗi năm. | **4 - 6 Vòng/năm** | 3.5 Vòng | 🟡 Tốc độ chậm. |

---

## 4. Checklist Bàn Giao (Handoff to Pre-SIOP)

Cuộc họp Supply Review (Chủ trì bởi Head of Supply Chain/Merchandising) cần hoàn thiện các thông tin sau để mang vào bước Pre-SIOP cấn trừ:

- [ ] Đã xây dựng xong **Constrained Demand Plan** (Dựa trên Năng lực Sản xuất & Ngân sách OTB thực tế).
- [ ] Ghi nhận toàn bộ các khoản thiếu hụt cung ứng (Supply Shortfalls).
- [ ] Tính toán xong **OTB (Ngân sách Mua hàng) còn lại** cho từng Category.
- [ ] Đã có phương án trình duyệt xử lý hàng Tồn kho Cũ (Aging Stock).
