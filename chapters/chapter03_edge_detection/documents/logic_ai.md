# Chương 3: Phát Hiện Cạnh - Logic & AI

## 1. Thuật Toán Canny — 5 Bước Nền Tảng

Thuật toán Canny (John F. Canny, 1986) là phương pháp phát hiện cạnh đa bước tiêu chuẩn:

### Bước 1: Gaussian Smoothing (Làm mịn ảnh)

Loại bỏ nhiễu bằng bộ lọc Gaussian trước khi tính đạo hàm:

$$I_{smooth} = I * G_{\sigma}$$

### Bước 2: Tính Gradient (Sobel)

$$G_x = \frac{\partial I}{\partial x}, \quad G_y = \frac{\partial I}{\partial y}$$

### Bước 3: Tính Magnitude và Hướng

$$G = \sqrt{G_x^2 + G_y^2}, \quad \theta = \arctan\left(\frac{G_y}{G_x}\right)$$

### Bước 4: Non-Maximum Suppression (NMS)

Giữ lại pixel có gradient magnitude lớn nhất theo hướng gradient → cạnh mỏng 1 pixel, loại bỏ cạnh mờ.

### Bước 5: Hysteresis Thresholding

Sử dụng 2 ngưỡng để quyết định pixel nào là cạnh:

```
Cường độ │
    ▲    │    ████████████  ← Cạnh mạnh (> T_high) → chắc chắn là cạnh
         │   █            █
T_high ──┼───█────────────────█────────
         │  █                █
         │ █   ░░░░░░░░░░    █  ← Cạnh yếu (giữ nếu kết nối cạnh mạnh)
T_low  ──┼█───░──────────░───█────────
         │    ░          ░
         │                      ← Không phải cạnh (< T_low) → loại bỏ
         └──────────────────────────────────────► Vị trí
```

## 2. Ảnh Hưởng Của Tham Số

### 2.1 Ngưỡng ($T_{low}$, $T_{high}$)

| Tình Huống | Kết Quả |
|------------|---------|
| Cả hai thấp | Nhiều cạnh, nhiều cạnh giả (false edges) |
| Cả hai cao | Ít cạnh, có thể mất cạnh yếu quan trọng |
| Khoảng cách lớn ($T_{high} \gg T_{low}$) | Nhiều cạnh yếu được giữ lại |
| Khoảng cách nhỏ | Cạnh sạch, ít nhiễu |

> **Khuyến nghị**: $T_{high} \approx 2 \times T_{low}$ hoặc $3 \times T_{low}$

### 2.2 Sigma ($\sigma$)

| Giá Trị | Kết Quả | Nên Dùng Khi |
|---------|---------|--------------|
| $\sigma = 1$ (nhỏ) | Giữ nhiều chi tiết, dễ có nhiễu | Ảnh sạch, cần giữ cạnh mảnh |
| $\sigma = 2$ (vừa) | Cân bằng chi tiết và giảm nhiễu ✓ | Mặc định tốt cho hầu hết ảnh |
| $\sigma = 3+$ (lớn) | Ảnh mịn, giảm nhiễu tốt nhưng mất cạnh nhỏ | Ảnh nhiều nhiễu |

## 3. So Sánh OpenCV vs Scikit-image

### Minh Họa Sự Khác Biệt

```
OpenCV cv2.Canny:
  Input (uint8) → [Gaussian σ=1.4 nội bộ] → Gradient → NMS → Hysteresis(low, high)
  Người dùng kiểm soát: low_threshold, high_threshold

Scikit-image feature.canny:
  Input (float [0,1]) → [Gaussian σ tùy chỉnh] → Gradient → NMS → Hysteresis(auto)
  Người dùng kiểm soát: sigma (ngưỡng tự động từ histogram gradient)
```

### Bảng So Sánh Chi Tiết

| Tiêu Chí | OpenCV `cv2.Canny` | Scikit-image `feature.canny` |
|----------|---------------------|------------------------------|
| Tham số chính | `low_threshold`, `high_threshold` | `sigma` |
| Cách kiểm soát | Trực tiếp qua ngưỡng | Qua mức làm mịn |
| Làm mịn nội bộ | Gaussian σ=1.4 (cố định) | σ tùy chỉnh qua `sigma` |
| Ngưỡng | Người dùng đặt thủ công | Tự động từ histogram gradient |
| Kiểu đầu vào | `uint8` [0, 255] | `float64` [0, 1] |
| Kiểu đầu ra | `uint8` (0 hoặc 255) | `bool` (True/False) |
| Tốc độ | Nhanh hơn (C/C++) | Chậm hơn (Python) |
| Tính tùy biến | Ít (2 ngưỡng) | Nhiều (sigma + ngưỡng tự động) |

## 4. Logic Lựa Chọn Tiền Xử Lý

### Khi Nào Cần Tiền Xử Lý?

```
Ảnh đầu vào
      │
      ├── Nhiều nhiễu? ──── Có ──► GaussianBlur → Canny
      │                              (nhiễu làm cạnh giả)
      │
      ├── Tương phản thấp? ─ Có ──► equalizeHist → Canny
      │                              (không đủ gradient)
      │
      └── Ảnh bình thường ──────► Canny trực tiếp
```

| Loại Ảnh | Vấn Đề | Tiền Xử Lý | Lý Do |
|----------|---------|------------|-------|
| Nhiều nhiễu | Nhiều cạnh giả | `cv2.GaussianBlur(5,5)` | Làm mịn loại nhiễu tần số cao trước gradient |
| Tương phản thấp | Gradient quá nhỏ, Canny không thấy cạnh | `cv2.equalizeHist()` | Kéo giãn phân phối cường độ sáng |
| Nhiều chi tiết | Quá nhiều cạnh, khó phân biệt | Tăng ngưỡng hoặc tăng sigma | Lọc bớt cạnh yếu không quan trọng |

## 5. Logic Nhận Dạng Hình Dạng

### Circularity — Số Đo Độ Tròn

$$C = \frac{4\pi \cdot \text{Area}}{\text{Perimeter}^2}$$

```
Hình tròn hoàn hảo: C ≈ 1.0
Hình vuông        : C ≈ 0.785
Hình chữ nhật dài : C < 0.5
Đa giác bất kỳ    : C nhỏ hơn tùy hình dạng
```

**Ngưỡng phân loại trong bài**: $C > 0.8$ → Hình tròn

### Quy Tắc Phân Loại Theo Số Đỉnh

| Số Đỉnh (approxPolyDP) | Hình Dạng |
|------------------------|-----------|
| 3 | Tam giác |
| 4 | Tứ giác (hình chữ nhật / hình vuông) |
| > 4 và C > 0.8 | Hình tròn |
| > 4 và C ≤ 0.8 | Đa giác |

## 6. Khi Nào Nên / Không Nên Dùng Canny

| Nên Dùng Canny | Không Nên Dùng Canny |
|----------------|---------------------|
| Ảnh có độ tương phản tốt | Ảnh quá nhiễu (phải tiền xử lý trước) |
| Cần phát hiện biên rõ ràng, chính xác | Ảnh tương phản quá thấp |
| Làm đầu vào cho contour/shape detection | Cần kết quả real-time cực nhanh |
| Ứng dụng phân đoạn ảnh (segmentation) | Yêu cầu độ dày cạnh cụ thể (>1px) |
