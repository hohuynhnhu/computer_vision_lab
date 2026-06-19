# Chương 3: Phát Hiện Cạnh - Giải Pháp Kỹ Thuật

## 1. Công Nghệ Sử Dụng

| Công Nghệ | Vai Trò |
|-----------|---------|
| OpenCV (`cv2`) | `cv2.Canny`, `cv2.GaussianBlur`, `cv2.equalizeHist`, `cv2.findContours`, `cv2.approxPolyDP` |
| Scikit-image (`skimage`) | `skimage.feature.canny` - phát hiện cạnh Canny theo cách tiếp cận khác |
| NumPy | Xử lý mảng, đếm điểm cạnh, chuẩn hóa ảnh |
| Matplotlib | Hiển thị kết quả so sánh |
| `time` module | Đo thời gian xử lý |

## 2. Kiến Trúc Xử Lý

### Pipeline Canny Cơ Bản
```
Ảnh màu → Resize (512×512) → Grayscale → Canny → Ảnh cạnh
                                              │
                                  ┌───────────┴───────────┐
                                  ▼                       ▼
                          cv2.Canny(low, high)    feature.canny(sigma)
```

### Pipeline Đánh Giá Loại Ảnh
```
Ảnh đầu vào ──┬──► Canny trực tiếp ──────► So sánh │
              ├──► Gaussian + Canny ──────►         │
              └──► EqualizeHist + Canny ──►─────────┘
```

## 3. Chi Tiết Hàm

### 3.1 Phát Hiện Cạnh OpenCV

```python
cv2.Canny(img_gray, low_threshold, high_threshold)
```

- `low_threshold`: Ngưỡng thấp (kết nối cạnh yếu vào cạnh mạnh)
- `high_threshold`: Ngưỡng cao (xác định cạnh mạnh)
- Kết quả: Ảnh nhị phân uint8 (0 hoặc 255)

### 3.2 Phát Hiện Cạnh Scikit-image

```python
from skimage.feature import canny
edges = canny(gray_float, sigma=2)
```

- `sigma`: Độ lệch chuẩn của Gaussian blur (làm mịn trước khi phát hiện cạnh)
- Ảnh đầu vào cần được chuẩn hóa về [0, 1] (float)
- Kết quả: Mảng Boolean

### 3.3 Tiền Xử Lý Nâng Cao

```python
# Gaussian Blur trước Canny (giảm nhiễu)
blurred = cv2.GaussianBlur(img_gray, (5, 5), 0)
edges = cv2.Canny(blurred, 100, 200)

# Histogram Equalization trước Canny (tăng tương phản)
equalized = cv2.equalizeHist(img_gray)
edges = cv2.Canny(equalized, 100, 200)
```

### 3.4 Tìm Contour và Phân Loại Hình Dạng

```python
# Tìm contour
contours, _ = cv2.findContours(edges, cv2.RETR_EXTERNAL, cv2.CHAIN_APPROX_SIMPLE)

for cnt in contours:
    # Xấp xỉ đa giác
    peri = cv2.arcLength(cnt, True)
    approx = cv2.approxPolyDP(cnt, 0.04 * peri, True)

    # Phân loại theo số đỉnh
    vertices = len(approx)
    if vertices == 3:
        shape = "Tam giác"
    elif vertices == 4:
        shape = "Tứ giác"
    elif vertices > 4:
        shape = "Đa giác"

    # Phát hiện hình tròn (dùng circularity)
    area = cv2.contourArea(cnt)
    circularity = 4 * np.pi * area / (peri * peri)
    if circularity > 0.8:
        shape = "Hình tròn"
```

## 4. So Sánh Phương Pháp Tiền Xử Lý

| Phương Pháp | Mục Đích | Hàm OpenCV |
|-------------|----------|------------|
| Canny trực tiếp | Baseline | `cv2.Canny(gray, 100, 200)` |
| Gaussian Blur + Canny | Giảm nhiễu trước khi phát hiện cạnh | `cv2.GaussianBlur()` → `cv2.Canny()` |
| Histogram Equalization + Canny | Tăng tương phản trước khi phát hiện cạnh | `cv2.equalizeHist()` → `cv2.Canny()` |
