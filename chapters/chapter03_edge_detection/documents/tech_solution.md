# Chương 3: Phát Hiện Cạnh - Giải Pháp Kỹ Thuật

## 1. Công Nghệ Sử Dụng

| Công Nghệ | Vai Trò |
|-----------|---------|
| OpenCV (`cv2`) | `cv2.Canny`, `cv2.GaussianBlur`, `cv2.equalizeHist`, `cv2.findContours`, `cv2.approxPolyDP` |
| Scikit-image (`skimage`) | `skimage.feature.canny` — phát hiện cạnh với tham số sigma tùy chỉnh |
| NumPy | Xử lý mảng, đếm điểm cạnh (`np.sum`), chuẩn hóa ảnh (`astype(np.float64) / 255`) |
| Matplotlib | Hiển thị kết quả so sánh dạng grid |
| `time` module | Đo thời gian xử lý (ms) |

## 2. Kiến Trúc Xử Lý

### Pipeline Canny Cơ Bản (Phần 1)

```
Ảnh màu (demo.jpg)
      │
      ▼
cv2.resize(512×512)
      │
      ▼
cv2.cvtColor(BGR→GRAY)
      │
      ├──────────────────────────────────────────┐
      ▼                                          ▼
cv2.Canny(gray, 100, 200)           canny(gray/255.0, sigma=2)
[uint8: 0 hoặc 255]                 [bool: True/False]
      │                                          │
      └──────────────────┬───────────────────────┘
                         ▼
              So sánh: số cạnh + thời gian (ms)
```

### Pipeline Đánh Giá Loại Ảnh (Phần 3)

```
Ảnh đầu vào
      │
      ├──► Canny trực tiếp ──────────────────► So sánh
      ├──► GaussianBlur(5,5) → Canny ────────►   kết quả
      └──► equalizeHist() → Canny ───────────►
```

### Pipeline Nhận Dạng Hình Dạng (Phần 4)

```
Ảnh → Canny → Edge Map → cv2.findContours(RETR_EXTERNAL, CHAIN_APPROX_SIMPLE)
                                │
                         lọc contour nhỏ (area < 100)
                                │
                         cv2.approxPolyDP(cnt, 0.04*peri, True)
                                │
              ┌─────────────────┼─────────────────┐
              ▼                 ▼                 ▼
       3 đỉnh → Tam giác  4 đỉnh → Tứ giác   >4 đỉnh → Circularity
                                                         │
                                         C = 4π·Area / Perimeter²
                                         C > 0.8 → Hình tròn
                                         C ≤ 0.8 → Đa giác
```

## 3. Các Hàm OpenCV và API Chính

| Hàm | Mục Đích | Tham Số Chính |
|-----|----------|---------------|
| `cv2.Canny(img, low, high)` | Phát hiện cạnh Canny | `low`: ngưỡng thấp, `high`: ngưỡng cao |
| `cv2.GaussianBlur(img, (k,k), 0)` | Làm mịn giảm nhiễu | `(k,k)`: kernel size (số lẻ), `0`: sigma tự tính |
| `cv2.equalizeHist(gray)` | Tăng tương phản ảnh xám | Không có tham số phụ |
| `cv2.findContours(edges, mode, method)` | Tìm contour từ edge map | `RETR_EXTERNAL`, `CHAIN_APPROX_SIMPLE` |
| `cv2.approxPolyDP(cnt, eps, True)` | Xấp xỉ đa giác | `eps = 0.04 * arcLength` |
| `skimage.feature.canny(gray_f, sigma)` | Phát hiện cạnh Canny | `sigma`: mức làm mịn Gaussian |

## 4. Chi Tiết Kỹ Thuật

### 4.1 Phát Hiện Cạnh OpenCV

```python
def canny_opencv(img_gray, low=100, high=200):
    """Phát hiện cạnh bằng OpenCV Canny.
    Đầu vào : ảnh xám uint8
    Đầu ra  : ảnh nhị phân uint8 (0 hoặc 255)
    """
    return cv2.Canny(img_gray, low, high)
```

**Lưu ý**: OpenCV tự áp dụng Gaussian blur nội bộ với σ=1.4 (không thay đổi được).

### 4.2 Phát Hiện Cạnh Scikit-image

```python
def canny_skimage(img_gray, sigma=2):
    """Phát hiện cạnh bằng Scikit-image Canny.
    Đầu vào : ảnh xám uint8
    Đầu ra  : mảng Boolean (True = cạnh)
    """
    gray_f = img_gray.astype(np.float64) / 255.0
    return skimage_canny(gray_f, sigma=sigma)
```

**Lưu ý**: Bắt buộc chuẩn hóa về [0, 1] trước khi truyền vào skimage.

### 4.3 Tiền Xử Lý Nâng Cao

```python
def canny_with_blur(img_gray, ksize=5, low=100, high=200):
    """Gaussian Blur trước Canny — giảm nhiễu."""
    blurred = cv2.GaussianBlur(img_gray, (ksize, ksize), 0)
    return cv2.Canny(blurred, low, high)

def canny_with_equalize(img_gray, low=100, high=200):
    """Histogram Equalization trước Canny — tăng tương phản."""
    equalized = cv2.equalizeHist(img_gray)
    return cv2.Canny(equalized, low, high)
```

### 4.4 Nhận Dạng Hình Dạng

```python
def classify_shape(cnt):
    """Phân loại hình dạng dựa vào số đỉnh và circularity."""
    peri     = cv2.arcLength(cnt, True)
    approx   = cv2.approxPolyDP(cnt, 0.04 * peri, True)
    vertices = len(approx)

    if vertices == 3:
        return "Tam giac"
    elif vertices == 4:
        return "Tu giac"
    elif vertices > 4:
        area        = cv2.contourArea(cnt)
        circularity = 4 * np.pi * area / (peri * peri) if peri > 0 else 0
        return "Hinh tron" if circularity > 0.8 else "Da giac"
    return "Khac"
```

**Công thức Circularity**: $C = \frac{4\pi \cdot \text{Area}}{\text{Perimeter}^2}$ — hình tròn hoàn hảo có $C = 1$.

## 5. So Sánh Phương Pháp Tiền Xử Lý

| Phương Pháp | Mục Đích | Khi Nào Dùng |
|-------------|----------|--------------|
| Canny trực tiếp | Baseline, không tiền xử lý | Ảnh chất lượng tốt, ít nhiễu |
| `GaussianBlur` + Canny | Giảm nhiễu trước khi phát hiện cạnh | Ảnh nhiều nhiễu muối tiêu / Gaussian |
| `equalizeHist` + Canny | Tăng tương phản trước khi phát hiện cạnh | Ảnh tương phản thấp, thiếu sáng |
