# 📊 BƯỚC 2: DEMAND REVIEW (Hoạch định & Thống nhất Nhu cầu)
## Chuẩn SIOP Đa Quốc Gia (MNC Standard)

> [!NOTE]
> Mục tiêu của bước này là tạo ra bản **Unconstrained Demand Plan** (Dự báo Nhu cầu Không Giới hạn). Đây là con số Sales tin rằng thị trường sẽ tiêu thụ nếu có vô hạn nguồn cung, KHÔNG bị rào cản bởi năng lực sản xuất hay tài chính.

---

## 1. Mở Rộng Tầm Nhìn Dự Báo (18-Month Rolling Horizon)

SIOP phải đủ xa để định hướng chuỗi cung ứng. Ngành thời trang yêu cầu chu kỳ 18 tháng do quá trình R&D vải và phát triển mẫu mã tốn 6-12 tháng.

| Giai đoạn (Horizon) | Cấp độ Chi tiết (Granularity) | Mục đích Quyết định | Hành động chính |
| :--- | :--- | :--- | :--- |
| **0 - 3 Tháng** (Tactical) | Level 4 & 5 (Sub-cat / Style) | Cứu vãn / Đẩy hàng (In-season) | Chốt đơn Replenishment, Chốt Markdown |
| **4 - 6 Tháng** (Planning) | Level 4 (Sub-category) | Phát PO / Lịch sản xuất | Booking xưởng, Mua nguyên phụ liệu |
| **7 - 18 Tháng** (Strategic) | Level 3 (Category) | Lên ngân sách, Mở rộng quy mô | Phê duyệt thiết kế BST mới, Quy hoạch vốn |

---

## 2. Ma Trận Kịch Bản Dự Báo (Scenario Planning)

Trong thế giới VUCA, không bao giờ dùng 1 con số dự báo duy nhất. Bắt buộc sử dụng 3 Kịch bản (Scenarios) để xây dựng rủi ro và bộ đệm (buffer).

### Bảng Kịch bản chuẩn SIOP cho Ngành Thời trang

| Loại Kịch Bản | % Xác Suất | Mức độ Impact (Doanh thu) | Tác động Tồn kho | Trigger (Khi nào kích hoạt) | Kế hoạch Hành động (Contingency Plan) |
| :--- | :---: | :---: | :--- | :--- | :--- |
| **Base Case (Cơ sở)** | 60% | Giữ nguyên (Theo forecast) | Target WOS: 6 tuần | Mặc định | Chạy theo lịch sản xuất chuẩn. |
| **Upside (Lạc quan)** | 20% | +15% đến +20% | Đòi hỏi tăng PO ngay lập tức | Khi STR của 1 Category > 80% ở Tuần thứ 4. | Đàm phán Fast-reorder (leadtime ngắn) với xưởng nội địa thay vì outsource nước ngoài. Chấp nhận giá vốn (COGS) tăng 5%. |
| **Downside (Bi quan)** | 20% | -15% đến -30% | Nguy cơ Overstock > 10 tuần | Khi STR của 1 Category < 40% ở Tuần thứ 6. | Dừng ngay các đợt Reorder (cắt PO). Đẩy hàng ra kênh Outlet / Wholesale để thu hồi vốn sớm. Kích hoạt Flash Sale 30%. |
| *Weather Alert* | - | Đảo lộn Category | Lệch pha giữa áo len và áo phông | Mùa đông ấm hơn 2 độ so với dự báo. | Điều chỉnh Allocation (Đưa áo khoác lên vùng núi/Bắc sớm hơn, giữ lại áo phông ở miền Nam). |

---

## 3. Kế hoạch Ra mắt Sản phẩm mới (NPI / BST Pipeline)

Hàng mới (New Product Introduction - NPI) đóng góp 40-60% doanh thu mùa mới của thời trang. Việc dự báo NPI rất khó vì chưa có dữ liệu lịch sử (Historical data).

### Template Phê duyệt NPI Pipeline (Cấp Category)

| Tên Bộ Sưu Tập / Concept | Tháng Launch | Category Target | Dự kiến số Styles | Tỉ trọng Doanh thu Mùa | Lịch sử BST tương tự | Ngân sách MKT (Đề xuất) | Rủi ro Cannibalization (Triệt tiêu) |
| :--- | :---: | :--- | :---: | :---: | :--- | :--- | :--- |
| Spring Summer 2026 "Breeze" | Mar 2026 | Shirts, Shorts, T-shirts | 45 | 40% | SS2025 "Ocean" (Đã vượt 110% target) | 200M | Triệt tiêu nhẹ nhóm áo thun Basic đang bán. |
| Fall Winter 2026 "Cozy" | Sep 2026 | Jackets, Sweaters | 30 | 50% | FW2025 "Warmth" (Đạt 95% target) | 300M | Không có. |

> [!WARNING]
> Mọi BST mới phải đính kèm **Target STR (Sell-through Rate)** và **Lifecycle Plan** (Sau bao nhiêu tuần thì bắt đầu clear hàng).

---

## 4. Biên Bản / Checklist Trước Khi Rời Khỏi Demand Review

Cuộc họp Demand Review (chủ trì bởi Head of Sales/MKT) phải chốt được các điểm sau trước khi chuyển sang Supply:

- [ ] Đã khóa chốt **Unconstrained Demand Plan** cho 18 tháng tới (Aggregate theo Category/Sub-category).
- [ ] Đã thống nhất **Base / Upside / Downside Scenarios**.
- [ ] Đã phê duyệt khung lịch ra mắt **NPI/BST** và chỉ tiêu doanh thu cho hàng mới.
- [ ] Có kế hoạch bù đắp khoảng trống (Gap-closing plan) nếu Demand Plan không đạt chỉ tiêu (Budget/AOP) của tài chính.
