# Chương 2: Lọc Ảnh - Logic & AI

## 1. Tích Chập (Convolution)

Tích chập là phép toán cơ bản của mọi bộ lọc tuyến tính:

$$g(x,y) = \sum_{i=-a}^{a}\sum_{j=-b}^{b} f(x+i, y+j) \cdot h(i,j)$$

Trong đó:
- $f(x,y)$: giá trị pixel gốc tại vị trí (x,y)
- $h(i,j)$: kernel (ma trận trọng số)
- $g(x,y)$: giá trị pixel mới sau lọc
- $a, b$: bán kính kernel (kernel size = 2a+1 × 2b+1)

### Minh Họa Tích Chập 3×3
```
Kernel 3x3                 Ảnh gốc (vùng 3x3)        Pixel mới
┌─────────────┐           ┌─────────────┐           ┌──────────┐
│ h₁₁ h₁₂ h₁₃ │           │ p₁₁ p₁₂ p₁₃ │           │          │
│ h₂₁ h₂₂ h₂₃ │    ⊗      │ p₂₁ p₂₂ p₂₃ │    =      │ g(x,y)   │
│ h₃₁ h₃₂ h₃₃ │           │ p₃₁ p₃₂ p₃₃ │           │          │
└─────────────┘           └─────────────┘           └──────────┘
         g(x,y) = h₁₁·p₁₁ + h₁₂·p₁₂ + ... + h₃₃·p₃₃
```

## 2. Các Loại Bộ Lọc

### 2.1 Lọc Trung Vị (Median Filter)

- **Nguyên lý**: Thay pixel tâm bằng giá trị trung vị của tất cả pixel trong cửa sổ lân cận
- **Ưu điểm**: Loại bỏ nhiễu xung (salt-and-pepper noise) mà không làm mờ cạnh
- **Lưu ý**: Là bộ lọc **phi tuyến** vì không dùng công thức tổng có trọng số

### 2.2 Lọc Gaussian

- **Nguyên lý**: Kernel có trọng số phân phối theo hàm Gauss:
  $$G(x,y) = \frac{1}{2\pi\sigma^2} e^{-\frac{x^2+y^2}{2\sigma^2}}$$
- **Tính chất**:
  - Pixel gần tâm có trọng số cao hơn → làm mờ mượt, tự nhiên
  - $\sigma$ càng lớn → mức độ mờ càng cao
- **Ứng dụng**: Tiền xử lý ảnh, giảm nhiễu Gauss

### 2.3 Lọc Làm Sắc Nét (Sharpen)

- **Nguyên lý**: Kernel tăng cường sự khác biệt giữa pixel tâm và các pixel lân cận:
  ```
  [[ 0, -1,  0],
   [-1,  6, -1],
   [ 0, -1,  0]]
  ```
- **Tổng trọng số kernel = 1**: giữ nguyên độ sáng trung bình
- **Hiệu ứng**: Làm nổi bật cạnh và chi tiết trong ảnh

## 3. Phát Hiện Cạnh Sobel

### 3.1 Nguyên Lý

Sobel sử dụng 2 kernel 3×3 để tính đạo hàm riêng phần theo hướng X và Y:

- **Sobel X** (phát hiện cạnh dọc):
  ```
  [[-1,  0,  1],
   [-2,  0,  2],
   [-1,  0,  1]]
  ```

- **Sobel Y** (phát hiện cạnh ngang):
  ```
  [[-1, -2, -1],
   [ 0,  0,  0],
   [ 1,  2,  1]]
  ```

### 3.2 Gradient Magnitude

Kết hợp Gx và Gy để tính cường độ cạnh:

$$G = \sqrt{G_x^2 + G_y^2}$$

## 4. Lọc Song Phương (Bilateral Filter)

### 4.1 Nguyên Lý

Lọc song phương kết hợp **2 metric** để tính trọng số:

1. **Khoảng cách không gian (Spatial distance)**: Pixel càng gần tâm → trọng số càng cao
2. **Khoảng cách cường độ (Intensity distance)**: Pixel có màu càng giống tâm → trọng số càng cao

### 4.2 Ưu Điểm

- Giảm nhiễu hiệu quả
- **Giữ được cạnh** (edge-preserving) - pixel khác màu ở bên kia cạnh sẽ có trọng số thấp

### 4.3 So Sánh Với Gaussian

| Tiêu Chí | Gaussian | Bilateral |
|----------|----------|-----------|
| Loại | Tuyến tính | Phi tuyến |
| Giữ cạnh | Không | Có |
| Tốc độ | Nhanh | Chậm hơn |
| Tham số | σ không gian | σ không gian + σ cường độ |

## 5. Biến Đổi Điểm Ảnh

### 5.1 Khái Niệm

Biến đổi điểm ảnh (point operation) là phép toán áp dụng lên **từng pixel độc lập**, không phụ thuộc vào các pixel lân cận:

$$g(x,y) = T[f(x,y)]$$

Trong đó $T$ là hàm biến đổi tác động lên một pixel duy nhất.

### 5.2 Các Phép Biến Đổi Cơ Bản

| Phép Biến Đổi | Công Thức | Ứng Dụng |
|---------------|-----------|----------|
| Độ sáng | `g = f + β` | Điều chỉnh độ sáng tổng thể |
| Tương phản | `g = α · f` | Điều chỉnh độ tương phản |
| Âm bản | `g = 255 - f` | Tạo ảnh âm bản (negative) |
| Ngưỡng nhị phân | `g = 255 if f > T else 0` | Phân tách đối tượng/nền |
