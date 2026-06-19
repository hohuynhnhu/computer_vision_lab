# Chương 3: Phát Hiện Cạnh - Logic & AI

## 1. Thuật Toán Canny Edge Detection

Thuật toán Canny (John F. Canny, 1986) là một trong những phương pháp phát hiện cạnh phổ biến nhất, gồm 5 bước chính:

### Bước 1: Làm Mịn Ảnh (Gaussian Smoothing)
Loại bỏ nhiễu bằng bộ lọc Gaussian:
$$I_{smooth} = I * G_{\sigma}$$
Trong đó $G_{\sigma}$ là kernel Gaussian với độ lệch chuẩn $\sigma$.

### Bước 2: Tính Gradient
Tính đạo hàm theo hướng X và Y (thường dùng Sobel):
$$G_x = \frac{\partial I}{\partial x}, \quad G_y = \frac{\partial I}{\partial y}$$

### Bước 3: Tính Magnitude và Hướng
$$G = \sqrt{G_x^2 + G_y^2}, \quad \theta = \arctan\left(\frac{G_y}{G_x}\right)$$

### Bước 4: Non-Maximum Suppression (NMS)
Giữ lại pixel có gradient magnitude lớn nhất theo hướng gradient, loại bỏ các pixel còn lại → cạnh mỏng 1 pixel.

### Bước 5: Hysteresis Thresholding
Sử dụng 2 ngưỡng:
- **Ngưỡng cao ($T_{high}$)**: Pixel > $T_{high}$ → chắc chắn là cạnh
- **Ngưỡng thấp ($T_{low}$)**: Pixel < $T_{low}$ → không phải cạnh
- **Giữa 2 ngưỡng**: Là cạnh nếu kết nối với pixel > $T_{high}$

```
Cường độ │
    ▲    │    ████████████  ← Cạnh mạnh (> T_high)
         │   █            █
T_high ──┼───█──────────────█────────
         │  █              █
         │ █   ░░░░░░░░░░  █ ← Cạnh yếu (giữ lại nếu kết nối cạnh mạnh)
         │█   ░          ░ █
T_low  ──┼█───░──────────░─█────────
         │    ░          ░
         │                  ← Không phải cạnh (< T_low)
         └──────────────────────────► Vị trí
```

## 2. Ảnh Hưởng Của Tham Số

### 2.1 Ngưỡng Thấp ($T_{low}$) và Ngưỡng Cao ($T_{high}$)

| Tình Huống | Kết Quả |
|------------|---------|
| $T_{low}$ thấp, $T_{high}$ thấp | Nhiều cạnh, nhiều cạnh giả (false edges) |
| $T_{low}$ cao, $T_{high}$ cao | Ít cạnh, có thể mất cạnh yếu |
| Khoảng cách $T_{low}$ ↔ $T_{high}$ lớn | Nhiều cạnh yếu được giữ lại |
| Khoảng cách nhỏ | Cạnh sạch, ít nhiễu |

**Khuyến nghị**: $T_{high} \approx 2 \times T_{low}$ hoặc $T_{high} \approx 3 \times T_{low}$

### 2.2 Sigma ($\sigma$)

| Giá Trị Sigma | Kết Quả |
|---------------|---------|
| $\sigma$ nhỏ (1) | Giữ nhiều chi tiết, dễ có nhiễu |
| $\sigma$ vừa (2) | Cân bằng giữa chi tiết và giảm nhiễu |
| $\sigma$ lớn (3+) | Ảnh mịn, giảm nhiễu tốt nhưng mất cạnh nhỏ |

## 3. So Sánh OpenCV vs Scikit-image

### OpenCV `cv2.Canny`
- **Cách tiếp cận**: Kiểm soát trực tiếp 2 ngưỡng (low, high)
- **Làm mịn**: Tự động áp dụng Gaussian với $\sigma=1.4$ (không tùy chỉnh được)
- **Kết quả**: Ảnh nhị phân `uint8`

### Scikit-image `feature.canny`
- **Cách tiếp cận**: Kiểm soát qua sigma (làm mịn), ngưỡng tự động tính từ histogram gradient
- **Làm mịn**: Gọi rõ `sigma` làm tham số đầu vào
- **Kết quả**: Mảng Boolean

## 4. Pipeline Kết Hợp

### Canny + Phân Đoạn
```
Ảnh → Canny → Edge Map → findContours/watershed → Phân đoạn vùng
```

### Canny + Nhận Dạng Hình Dạng
```
Ảnh → Canny → Edge Map → findContours → approxPolyDP → Phân loại
                                                              │
                                              ┌───────────────┼───────────────┐
                                              ▼               ▼               ▼
                                           Tam giác       Tứ giác          Hình tròn
                                           (3 đỉnh)       (4 đỉnh)      (circularity > 0.8)
```

### Công Thức Circularity
$$C = \frac{4\pi \cdot \text{Area}}{\text{Perimeter}^2}$$

- $C \approx 1$: Hình tròn hoàn hảo
- $C < 1$: Hình dạng khác (càng nhỏ càng không tròn)

## 5. Các Trường Hợp Nên/Không Nên Dùng Canny

| Nên Dùng Canny | Không Nên Dùng Canny |
|----------------|---------------------|
| Ảnh có độ tương phản tốt | Ảnh quá nhiễu (cần tiền xử lý) |
| Cần phát hiện biên rõ ràng | Ảnh tương phản quá thấp |
| Làm đầu vào cho contour/shape detection | Cần kết quả thời gian thực cực nhanh |
| Ứng dụng phân đoạn ảnh | Yêu cầu độ dày cạnh cụ thể |
