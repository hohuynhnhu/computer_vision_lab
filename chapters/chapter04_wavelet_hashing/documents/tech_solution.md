# Chương 4: So Sánh Sự Tương Đồng Của Các Hình Ảnh Sử Dụng Wavelet - Giải Pháp Kỹ Thuật

## 1. Công Nghệ Sử Dụng

| Công Nghệ | Phiên Bản | Vai Trò |
|-----------|-----------|---------|
| Python | 3.11+ | Ngôn ngữ lập trình chính |
| OpenCV (`cv2`) | 4.11 | Đọc/ghi ảnh, chuyển đổi không gian màu, tiền xử lý và biến đổi hình học |
| PyWavelets (`pywt`) | 1.9 | Biến đổi Wavelet rời rạc 2D (`pywt.wavedec2`) |
| NumPy | 1.26 | Lượng tử hóa, làm phẳng hệ số, tính toán khoảng cách Hamming và ma trận khoảng cách |
| Matplotlib | - | Hiển thị ảnh và vẽ biểu đồ ROC |

## 2. Kiến Trúc Giải Pháp

```
┌─────────────┐     ┌──────────────┐     ┌───────────────┐
│  Ảnh mẫu    │────▶│  cv2.resize  │────▶│ Chuyển Grayscale│
│  (.jpg)     │     │ (Đồng nhất)  │     │  (Ảnh xám)    │
└─────────────┘     └──────────────┘     └───────┬───────┘
                                                 │
                                                 ▼
                                         ┌───────────────┐
                                         │  pywt.wavedec2│
                                         │ (Biến đổi DWT)│
                                         └───────┬───────┘
                                                 │
                        ┌────────────────────────┴────────────────────────┐
                        ▼                                                 ▼
             [Phương pháp Slide]                                  [Phương pháp wHash]
             ┌──────────────────┐                                 ┌──────────────────┐
             │ Lượng tử hóa theo│                                 │ So sánh cA với   │
             │ bước chia step   │                                 │ giá trị trung vị │
             └────────┬─────────┘                                 └────────┬─────────┘
                      ▼                                                    ▼
             ┌──────────────────┐                                 ┌──────────────────┐
             │ Băm: bit % 2     │                                 │ Băm: cA >= med   │
             └────────┬─────────┘                                 └────────┬─────────┘
                      ▼                                                    ▼
             ┌─────────────────────────────────────────────────────────────┐
             │             Độ tương đồng = Khoảng cách Hamming            │
             └─────────────────────────────────────────────────────────────┘
```

## 3. Chi Tiết Triển Khai

### 3.1 Tiền Xử Lý Ảnh
Để mã băm có thể đối sánh trực tiếp, ta chuyển ảnh về dạng Grayscale và bắt buộc resize về kích thước chuẩn cố định (ví dụ $64 \times 64$):
```python
img_gray = cv2.imread(image_path, cv2.IMREAD_GRAYSCALE)
img_resized = cv2.resize(img_gray, (64, 64))
```

### 3.2 Biến Đổi Wavelet 2D Rời Rạc (DWT)
Sử dụng hàm `pywt.wavedec2` để phân tích tần số:
```python
# wavedec2 trả về: [cA, (cH_1, cV_1, cD_1), ...]
coeffs = pywt.wavedec2(img_resized.astype(np.float32), 'db4', level=1)
```

### 3.3 Tạo Mã Băm Nhị Phân

*   **Phương pháp Slide**: 
    1. Làm mịn và lượng tử hóa tất cả các hệ số thông qua một hàm đệ quy để xử lý cấu trúc kiểu list/tuple:
       $$\text{coeff\_quant} = \text{round}\left(\frac{\text{coeff}}{\text{step}}\right)$$
    2. Làm phẳng toàn bộ hệ số.
    3. Tính số dư modulo 2 (`bit % 2`) để nhận được mã băm $0$ hoặc $1$.
*   **Phương pháp wHash Chuẩn**:
    1. Chỉ trích xuất hệ số xấp xỉ `cA` (thành phần tần số thấp, ít nhiễu).
    2. Tính giá trị trung vị (median) của `cA`.
    3. Trả về $1$ nếu giá trị hệ số lớn hơn trung vị, và $0$ nếu nhỏ hơn hoặc bằng.

### 3.4 Đánh Giá Hiệu Năng Phân Loại
Khoảng cách Hamming được tính và so sánh với một loạt các ngưỡng (thresholds) khác nhau để tính ra:
*   **True Positive Rate (TPR / Recall)**: Tỷ số cặp tương tự được phân loại đúng.
*   **False Positive Rate (FPR)**: Tỷ số cặp không tương tự bị phân loại sai.
*   **Accuracy / Specificity**: Vẽ đường cong ROC biểu thị độ bao phủ và hiệu năng tổng quát.
