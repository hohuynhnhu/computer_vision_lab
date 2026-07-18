# Chương 2: Lọc Ảnh - Giải Pháp Kỹ Thuật

## 1. Công Nghệ Sử Dụng

| Công Nghệ | Vai Trò |
|-----------|---------|
| OpenCV (`cv2`) | Các hàm lọc: `blur`, `GaussianBlur`, `filter2D`, `bilateralFilter`, `threshold` |
| NumPy | Tạo kernel (`np.float32`), tính gradient magnitude (`np.sqrt`), giới hạn giá trị (`np.clip`) |
| Matplotlib | Hiển thị ảnh và grid so sánh |

## 2. Kiến Trúc Xử Lý

### Part I: Biến Đổi Điểm Ảnh

```
Ảnh đầu vào (RGB, uint8)
      │
      ▼
convert to float32
      │
      ├──┬──┬──┬──┐
      ▼  ▼  ▼  ▼  ▼
    +/-  *  -128    threshold
      │  │  │       │
      ▼  ▼  ▼       ▼
   clip(0,255)   binary
      │
      ▼
convert to uint8
      │
      ▼
Ảnh đầu ra
```

### Part II & III: Lọc Tích Chập

```
Ảnh đầu vào
      │
      ▼
Kernel (h) ──► Tích chập (convolution)
      │           g(x,y) = ΣΣ f(x+i,y+j)·h(i,j)
      ▼
Ảnh đã lọc
```

## 3. Các Hàm OpenCV Chính

| Hàm | Mục Đích | Tham Số Chính |
|-----|----------|---------------|
| `cv2.blur(image, ksize)` | Lọc trung bình | `ksize`: kích thước kernel (số lẻ) |
| `cv2.GaussianBlur(image, (kw,kh), sigmaX)` | Lọc Gaussian | Kích thước kernel, sigma (0 = tự động) |
| `cv2.filter2D(image, ddepth, kernel)` | Tích chập với kernel tùy chỉnh | `ddepth=-1`, kernel (float32) |
| `cv2.bilateralFilter(image, d, sigmaColor, sigmaSpace)` | Lọc song phương | `d`: đường kính, `sigma`: độ lệch chuẩn |
| `cv2.blur(image, ksize)` | Lọc trung bình (Mean filter) | Kích thước kernel |

## 4. Chi Tiết Kỹ Thuật

### 4.1 Biến Đổi Độ Sáng (Brightness)

```python
# Cộng/trừ hằng số vào tất cả pixel
bright = np.clip(image.astype(np.float32) + 50, 0, 255).astype(np.uint8)
dark   = np.clip(image.astype(np.float32) - 50, 0, 255).astype(np.uint8)
```

**Lưu ý**: Dùng `np.float32` trung gian để tránh tràn số (overflow), sau đó `np.clip` để giới hạn trong [0,255].

### 4.2 Biến Đổi Tương Phản (Contrast)

```python
# Nhân hệ số vào tất cả pixel
low_contrast  = np.clip(image.astype(np.float32) * 0.5, 0, 255).astype(np.uint8)
high_contrast = np.clip(image.astype(np.float32) * 1.5, 0, 255).astype(np.uint8)
```

### 4.3 Lọc Trung Bình (Mean Filter)

```python
def mean_filter(image, kernel_size=3):
    if kernel_size % 2 == 0:
        raise ValueError("Kernel size must be an odd integer.")
    return cv2.blur(image, (kernel_size, kernel_size))
```

- Thay pixel tâm bằng **trung bình** của cửa sổ lân cận
- Hiệu quả với nhiễu Gauss

### 4.4 Lọc Gaussian

```python
def gaussian_filter(image, kernel_size=5):
    return cv2.GaussianBlur(image, (kernel_size, kernel_size), 0)
```

- Trọng số kernel theo phân phối chuẩn: $G(x,y) = \frac{1}{2\pi\sigma^2} e^{-\frac{x^2+y^2}{2\sigma^2}}$
- `sigma=0` → OpenCV tự tính sigma từ kernel size

### 4.5 Phát Hiện Cạnh Sobel

```python
def sobel_edge_detection(image):
    gray = cv2.cvtColor(image, cv2.COLOR_RGB2GRAY)
    sobel_x = np.array([[-1,0,1],[-2,0,2],[-1,0,1]], dtype=np.float32)
    sobel_y = np.array([[-1,-2,-1],[0,0,0],[1,2,1]], dtype=np.float32)
    gx = cv2.filter2D(gray, cv2.CV_32F, sobel_x)
    gy = cv2.filter2D(gray, cv2.CV_32F, sobel_y)
    return np.clip(np.sqrt(gx**2 + gy**2), 0, 255).astype(np.uint8)
```

Gradient magnitude: $G = \sqrt{G_x^2 + G_y^2}$

### 4.6 Lọc Song Phương (Bilateral Filter)

```python
def bilateral_filter(image, d=9, sigma=75):
    return cv2.bilateralFilter(image, d, sigma, sigma)
```

- Kết hợp 2 trọng số: khoảng cách không gian + khoảng cách cường độ
- **Edge-preserving**: giảm nhiễu nhưng giữ được cạnh
