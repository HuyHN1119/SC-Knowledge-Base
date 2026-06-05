# ⚙️ THỰC THI (IN-SEASON EXECUTION) & WSSI
## Chuẩn SIOP Đa Quốc Gia (MNC Standard)

> [!NOTE]
> Khi SIOP Plan được phê duyệt ở Bước 5, nó đóng vai trò là "Ngân sách" và "Định hướng". Quá trình triển khai thực tế hàng ngày/hàng tuần (In-season execution) đòi hỏi mức độ chi tiết đến từng SKU, từng Cửa hàng. Công cụ quyền lực nhất để thực thi trong ngành thời trang chính là **WSSI (Weekly Sales & Stock Intake)**.

---

## 1. Hệ thống WSSI (Weekly Sales & Stock Intake)

Trong MNC Fashion Retail, Merchandise Planner không thể sống thiếu WSSI. Đây là bản đồ tích hợp Kế hoạch Bán, Kế hoạch Nhập hàng, Tồn kho và Markdown theo từng tuần.

### Cấu trúc cơ bản của một bảng WSSI (Cấp Style/Color)

**Tên Style:** Áo Thun Basic Nam (AT-2026-BLK) | **Lifecycle:** 12 Tuần | **Initial Buy:** 10,000 pcs

| Tuần (Week) | W1 | W2 | W3 | W4 | W5 | W6 | ... | W12 (Clearance) |
| :--- | ---: | ---: | ---: | ---: | ---: | ---: | :--- | ---: |
| **1. Sales Plan (Dự kiến Bán)** | 500 | 800 | 1,000 | 1,200 | 1,000 | 800 | | 200 |
| **2. Actual Sales (Thực Bán)** | 600 | 900 | 1,200 | 1,500 | *4,200* | | | |
| **3. Intake Plan (Dự kiến Nhập)** | 10,000| 0 | 0 | 2,000 | 0 | 0 | | 0 |
| **4. Actual Intake (Thực Nhập)** | 10,000| 0 | 0 | *Deloyed* | | | | |
| **5. EOW Stock (Tồn Cuối Tuần)** | 9,400 | 8,500 | 7,300 | 5,800 | | | | |
| **6. WOS (Số tuần Tồn kho)** | 18.8 | 9.4 | 6.0 | 3.8 | | | | |
| **7. Markdown/Promo Plan** | Full | Full | Full | 10% Off | Full | Full | | 50% Off |

> [!IMPORTANT]
> **Cách sử dụng WSSI để ra quyết định (Demand Sensing):**
> Nhìn vào Tuần 4, Actual Sales (1,500) > Sales Plan (1,200), nhưng Hàng Nhập (Actual Intake) bị trễ. Tồn kho rơi xuống mức WOS = 3.8 tuần (cảnh báo đỏ). 
> **Hành động ngay:** Kéo hàng từ kho Buffer ra cửa hàng; thúc giục Xưởng giao lô 2,000 pcs gấp; Hủy chương trình Promo 10% vì hàng đang bán quá tốt không cần giảm giá.

---

## 2. Chiến lược Phân bổ Tồn kho (Inventory Allocation & Replenishment)

MNC không đẩy 100% hàng ra cửa hàng ngay khi nhận từ xưởng. Chiến lược "nhỏ giọt" giúp tối ưu hóa rủi ro tồn kho chết ở các điểm bán lẻ.

### Thuật toán Phân bổ Chuẩn
1. **Initial Allocation (Cấp phát ban đầu):** Đẩy 60-70% tổng lượng hàng về Cửa hàng / Kênh Online. Giữ lại 30-40% tại Kho Tổng (Central Warehouse) làm Buffer Stock.
2. **Size Profiling:** Chia hàng theo Store Grade (A/B/C) và Size Ratio đặc thù của từng vùng. (Ví dụ: Miền Nam chuộng size nhỏ, Miền Bắc chuộng size to hơn).
3. **Replenishment (Bổ sung liên tục):** Hệ thống sẽ auto-run mỗi đêm. Cửa hàng bán 1 cái M, Kho Tổng sẽ gửi 1 cái M bù vào, miễn là Kho Tổng còn hàng.

---

## 3. Quản trị Luân chuyển & Thu hồi Hàng hóa (Reverse Logistics)

Thời trang là ngành có tính di chuyển nội bộ cao để chữa cháy các điểm nghẽn tồn kho.

| Phương thức | Khi nào áp dụng? | Chi phí | Hiệu quả |
| :--- | :--- | :--- | :--- |
| **Inter-store Transfer (Chuyển hàng giữa các cửa hàng)** | Cửa hàng A hết Size M (Nhu cầu cao), Cửa hàng B còn tồn Size M (Nhu cầu thấp). | Vận chuyển nội địa (Thấp) | Rất cao, cứu được "Lost Sales" ngay lập tức. |
| **Pull-back to DC (Thu hồi về Kho Tổng)** | Cuối mùa, cửa hàng bị phân mảnh size (Mỗi mẫu còn lắt nhắt 1-2 cái). | Vận chuyển hai chiều (Cao) | Gom về Kho Tổng để tạo thành Pack bán trên Online hoặc xả Outlet. |
| **Return to Vendor (Trả nhà cung cấp)** | Hàng bị lỗi QC (Sổ chỉ, ra màu) phát hiện khi bán. | Cước trả hàng (Rất Cao) | Chặn đứng trải nghiệm xấu của Khách hàng. Cấn trừ công nợ với NCC. |

---

## 4. Ma trận Leo thang & Giải quyết Sự cố Thực thi (Escalation Matrix)

Trong quá trình In-season, khi xảy ra sự cố đột biến, team thực thi cần biết rõ phải báo cáo cho ai và thời gian xử lý (SLA).

| Sự cố (Trigger) | Mức độ | Người chịu trách nhiệm (Owner) | Hành động (Action) | SLA (Thời hạn) |
| :--- | :---: | :--- | :--- | :---: |
| **Demand Surge:** Doanh thu 1 Style tăng vọt gấp 3 lần dự báo do Viral TikTok. | 🔴 Critical | Merch Planner + Buyer | Dùng OTB dự phòng để "Fast-reorder". Cắt allocation của Store Grade C dồn cho Online. | 24 giờ |
| **Supply Delay:** Lô hàng key của tháng bị kẹt ở Cảng hải quan thêm 2 tuần. | 🔴 Critical | Logistics + Marketing | Dừng chạy Ads ngay lập tức. Push một sản phẩm tương tự lên thay thế trên banner. | 12 giờ |
| **Dead Stock Alert:** STR của 1 Style < 15% sau 4 tuần lên kệ. | 🟠 High | Merch Planner | Đánh giá lại trưng bày (VM). Nếu không cải thiện, kích hoạt Markdown 30%. | 48 giờ |
| **Quality Issue:** Tỉ lệ trả hàng (Return rate) của 1 mã vượt 5%. | 🔴 Critical | QA / QC + Store Ops | Tạm dừng bán (Stop-sale) toàn hệ thống. Rút hàng về kho để kiểm tra diện rộng. | 8 giờ |
