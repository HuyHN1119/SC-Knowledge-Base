# 📊 BƯỚC 1: DATA GATHERING (Chuẩn hóa & Tổng hợp Dữ liệu)
## Chuẩn SIOP Đa Quốc Gia (MNC Standard)

> [!NOTE]
> Data Gathering là bước đầu tiên và quan trọng nhất trong quy trình hàng tháng. Dữ liệu rác (Garbage In) sẽ dẫn đến kế hoạch rác (Garbage Out).
> Ở bước này, nhóm Data / FP&A cần tổng hợp toàn bộ các dữ liệu liên quan từ quá khứ, hiện tại và các yếu tố mùa vụ để làm nền tảng cho Bước 2 (Demand Review).

---

## 1. Cấu Trúc Phân Cấp Sản Phẩm Chuẩn (7-Level Hierarchy)

Quy trình SIOP của MNC quy định phải nhóm dữ liệu thành các Product Family (Họ sản phẩm) để họp cấp điều hành. Chi tiết cấp Style/Color/Size (Level 5-7) sẽ được đẩy sang bộ phận thực thi.

```
┌─────────────────────────────────────────────────────────────────────────────────┐
│ CẤP ĐỘ STRATEGIC & SIOP (Phục vụ cho Dự báo & Lên ngân sách)                    │
├─────────────────────────────────────────────────────────────────────────────────┤
│ LEVEL 1 — TOTAL COMPANY REVENUE (Tổng doanh thu)                                │
│ LEVEL 2 — DIVISION (VD: Men, Women, Kids)                                       │
│ LEVEL 3 — CATEGORY (VD: Tops, Bottoms, Dresses, Accessories)                    │
│ LEVEL 4 — SUB-CATEGORY (VD: T-shirt, Shirt, Jacket, Jeans)                      │
├─────────────────────────────────────────────────────────────────────────────────┤
│ CẤP ĐỘ TACTICAL & EXECUTION (Phục vụ cho Lên lịch sx, WSSI, Phân bổ)            │
├─────────────────────────────────────────────────────────────────────────────────┤
│ LEVEL 5 — STYLE (Mã thiết kế, VD: AT-2026-SS-001)                               │
│ LEVEL 6 — COLOR (Mã màu, VD: BLK, WHT)                                          │
│ LEVEL 7 — SKU / SIZE (Đơn vị nhỏ nhất, VD: AT-2026-SS-001-BLK-M)                │
└─────────────────────────────────────────────────────────────────────────────────┘
```

---

## 2. Các Chỉ số Data Đầu vào Cốt lõi (Input Data & Metrics)

Bên cạnh các chỉ số bán hàng truyền thống, hệ thống cần theo dõi các nhóm dữ liệu nâng cao sau:

### 2.1 Seasonality Index (Chỉ số mùa vụ)

> [!IMPORTANT]
> Ngành thời trang phụ thuộc cực kỳ lớn vào tính mùa vụ. Demand Baseline (Dự báo cơ sở) phải nhân với **Seasonality Index** mới ra số dự báo thực.

**Cách tính:** `Doanh thu thực tế của tháng N / Doanh thu trung bình hàng tháng trong năm`

*Bảng ví dụ Seasonality Index cho Sub-category "Áo khoác nam" (Men's Jacket)*:

| Tháng | 1 (Pre-Tết) | 2 | 3 | 4 | 5 | 6 | 7 | 8 | 9 | 10 | 11 | 12 (Sale) |
| :--- | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| **Index** | 1.8 | 0.8 | 0.6 | 0.4 | 0.3 | 0.3 | 0.4 | 0.6 | 0.9 | 1.5 | 1.9 | 2.5 |

*(Tháng 12 có Index = 2.5 nghĩa là doanh thu dự kiến gấp 2.5 lần trung bình các tháng).*

### 2.2 Demand Signal Data (Dữ liệu tín hiệu nhu cầu)

Để phát hiện sớm các thay đổi của thị trường trước khi nó thể hiện qua doanh thu thực, cần thu thập:
*   **Web/App Traffic:** Lượng truy cập trang danh mục sản phẩm (Category Page Views).
*   **Add-to-cart Rate (ATCR):** Tỉ lệ thêm vào giỏ hàng (chỉ báo mạnh mẽ về sự quan tâm).
*   **Wishlist Adds:** Số lượt lưu vào mục yêu thích (sử dụng để dự báo cho các chương trình Mega Sale sắp tới).
*   **Social Listening / Sentiment:** Trending keywords liên quan đến chất liệu, màu sắc.

### 2.3 Channel Cannibalization (Hiệu ứng triệt tiêu kênh)

Khi mở một kênh mới (như TikTok Shop), một phần doanh thu không phải là nhu cầu mới sinh ra mà là dịch chuyển từ kênh cũ (Shopee, Offline).

* **Metric cần đo:** `Cross-Channel Switch Rate %`
* **Hành động:** Trừ % Cannibalization vào Baseline forecast của các kênh bị ảnh hưởng trước khi chốt tổng Demand.

---

## 3. Bảng Theo dõi Độ Chính xác & Thiên lệch (Accuracy & Bias)

SIOP đo lường chất lượng dữ liệu quá khứ bằng 2 chỉ số đi đôi với nhau:
1. **Forecast Accuracy (FA):** Đo biên độ sai lệch (sai bao nhiêu phần trăm).
2. **Forecast Bias:** Đo thiên hướng sai lệch (luôn dự báo cao hơn thực tế hay thấp hơn thực tế).

| KPI | Công thức | Tiêu chuẩn | Nếu vi phạm |
| :--- | :--- | :---: | :--- |
| **Forecast Accuracy (FA)** | `1 - |Actual - Forecast| / Actual` | **≥ 70%** (Cấp Category) | Cần phân tích nguyên nhân gốc rễ (RCA - xem file 04) |
| **Forecast Bias** | `(Actual - Forecast) / Actual` | **± 5%** | **Bias > 5%**: Dự báo thấp (Under-forecast) ➔ Nguy cơ mất doanh thu.<br>**Bias < -5%**: Dự báo cao (Over-forecast) ➔ Tồn kho chết. |

*Báo cáo Mẫu (Category Level):*
| Category | Actual Rev (Tỷ) | Forecast Rev (Tỷ) | Variance | FA (%) | Bias (%) | Đánh giá |
| :--- | ---: | ---: | ---: | ---: | ---: | :--- |
| Men Tops | 10.5 | 10.0 | +0.5 | 95.0% | +5.0% | 🟢 Tốt |
| Women Dresses | 8.0 | 12.0 | -4.0 | 66.7% | -33.3% | 🔴 Kém, over-forecast nghiêm trọng |

---

## 4. Size Ratio (Tỉ lệ Phân bổ Size)

> [!TIP]
> Tỷ lệ size là nguyên nhân số 1 gây ra **Broken Size** (hết size M/L) và **Dead Stock** (tồn XS/XXL) trong Fashion. Dữ liệu này cần cập nhật mỗi 6-12 tháng dựa trên `Rolling 12-Month Data`.

**Lưu ý khi xuất Data Size Ratio:**
- Không dùng chung 1 tỷ lệ size cho toàn bộ công ty. Phải chia nhỏ theo **Division** (Nam/Nữ) và **Sub-category** (Áo/Quần).
- Phải chia theo **Kênh bán**. (Ví dụ: Kênh Online thường bán size to tốt hơn Kênh Offline).

*Ví dụ Dataset chuẩn cho Size Ratio T-shirt Nam:*
| Size | XS | S | M | L | XL | XXL |
| :--- | :---: | :---: | :---: | :---: | :---: | :---: |
| **Standard Ratio** | 5% | 15% | 30% | 28% | 15% | 7% |

---

## 5. Checklist Bàn Giao (Handoff to Demand Review)

Nhóm Data/FP&A cần đảm bảo các hạng mục sau trước ngày họp Demand Review (Ngày 6 hàng tháng):

- [ ] Đã khóa sổ (Lock data) doanh thu tháng trước (POS + Online + B2B).
- [ ] Chạy mô hình Statistical Baseline Forecast (sử dụng Seasonality Index) cho 18 tháng tới.
- [ ] Tính toán Forecast Accuracy và Bias của tháng vừa qua.
- [ ] Trích xuất tồn kho đầu kỳ (BOM Inventory) từ hệ thống ERP/WMS.
- [ ] Nộp toàn bộ gói dữ liệu (Data Packet) cho Chủ trì cuộc họp Demand (VP of Sales/Marketing).
