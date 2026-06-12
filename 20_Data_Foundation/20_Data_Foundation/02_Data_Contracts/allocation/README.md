# Allocation — Module

## Định nghĩa
**Allocation** = quyết định **hàng nào đi đâu, bao nhiêu** sau khi PO inbound vào WH tổng hoặc giữa các location.

## Phân biệt với module khác

| Module | Quyết định | Grain | Lifecycle |
|---|---|---|---|
| **Assortment** | Mua những option nào | Style × Color × Channel | Pre-season |
| **Demand (Replenishment)** | Cần bổ bao nhiêu | SKU × Location | In-season |
| **Supply (PO)** | Đặt vendor sản xuất bao nhiêu | SKU × Vendor | Pre/In-season |
| **Allocation** | Sau khi inbound, chia hàng đi đâu | SKU × Location × Channel | In-season |

## Loại allocation

### 1. Initial Allocation (First Wave Allocation)
- Trigger: PO inbound vào WH tổng lần đầu cho style+color mới
- Logic: phân bổ theo size curve × store cluster × forecast ratio
- File: `initial_allocation_contract.md`

### 2. Replenishment Allocation
- Trigger: hàng có sẵn ở WH tổng, store/channel đạt min stock
- Logic: ưu tiên store/channel có velocity cao và WOS thấp
- File: `replenishment_allocation_contract.md`

### 3. Pack / Breakpack
- Pre-pack: đóng gói theo bộ size định sẵn (vd 1S-2M-2L-1XL = 1 pack 6 cái)
- Break-pack: mở pack để chia lẻ → cost cao hơn
- File: `pack_allocation_contract.md`

### 4. Inter-location Transfer
- Trigger: balance stock giữa stores/channels — hàng dư ở A → thiếu ở B
- File: `transfer_contract.md`

## Layer

| Layer | Áp dụng | Dùng cho |
|---|---|---|
| L1 | Channel × Category — tổng hàng allocation theo % | Management review |
| L2 | SKU × Location — chi tiết phân bổ | Ops thực thi |

## KPI chính

| KPI | Công thức |
|---|---|
| **Allocation Fulfillment %** | Hàng allocate được / Hàng cần |
| **Allocation Accuracy** | % SKU allocate đúng nhu cầu thực sau 4 tuần |
| **Transfer Cost % of Sales** | Chi phí transfer / Revenue |
| **Stock Imbalance Index** | Std deviation of WOS across locations |
