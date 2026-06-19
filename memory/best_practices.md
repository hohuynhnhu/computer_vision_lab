# Best Practices - Computer Vision Lab

## 1. Xử Lý Ảnh An Toàn

### Luôn Dùng np.clip
```python
# ✓ Đúng
result = np.clip(img.astype(np.float32) + 50, 0, 255).astype(np.uint8)

# ✗ Sai (có thể tràn số)
result = (img + 50).astype(np.uint8)
```

### Kiểm Tra File Tồn Tại
```python
# ✓ Đúng
if not os.path.exists(path):
    raise FileNotFoundError(f"Không tìm thấy: {path}")
img = cv2.imread(path)

# ✗ Sai (imread trả về None nếu lỗi, lỗi ở các hàm sau)
img = cv2.imread(path)
gray = cv2.cvtColor(img, cv2.COLOR_BGR2GRAY)  # crash nếu img=None
```

## 2. Định Dạng Màu

### Luôn Ghi Rõ BGR/RGB Trong Tên Biến
```python
# ✓ Đúng
img_bgr = cv2.imread('image.jpg')
img_rgb = cv2.cvtColor(img_bgr, cv2.COLOR_BGR2RGB)
img_gray = cv2.cvtColor(img_bgr, cv2.COLOR_BGR2GRAY)

# ✗ Sai (không biết định dạng gì)
img = cv2.imread('image.jpg')
img2 = cv2.cvtColor(img, cv2.COLOR_BGR2RGB)
```

### Hiển Thị Đúng Định Dạng
```python
# Ảnh màu
plt.imshow(img_rgb)            # ✓ OK
plt.imshow(img_bgr)            # ✗ Sai màu

# Ảnh xám
plt.imshow(gray, cmap='gray')  # ✓ OK
plt.imshow(gray)               # ✗ Màu giả (colormap mặc định)
```

## 3. Tổ Chức Code

### Mỗi Notebook Một File Input
```python
# ✓ Đúng - đọc 1 lần ở đầu
IMG = cv2.imread('../input/anh.jpg')
IMG_RGB = IMG[:, :, ::-1]

# Dùng IMG_RGB cho tất cả các cell sau
```

### Import Theo Thứ Tự
```python
# 1. Thư viện chuẩn
import os
import time

# 2. Thư viện bên thứ ba
import cv2
import numpy as np
import matplotlib.pyplot as plt

# 3. Thư viện tùy chọn
from skimage.feature import canny  # chỉ khi cần
```

## 4. Kiểm Tra Kết Quả

### Kiểm Tra Shape và Range
```python
print(f"Shape: {result.shape}")
print(f"Range: [{result.min()}, {result.max()}]")
print(f"Dtype: {result.dtype}")
```

### Hiển Thị Trước Khi Lưu
```python
# Luôn hiển thị để kiểm tra trực quan trước khi lưu
plt.imshow(result, cmap='gray')
plt.show()
cv2.imwrite('output.jpg', result)
```

## 5. Tham Số Mặc Định Tốt

| Thuật Toán | Tham Số Khuyến Nghị |
|------------|---------------------|
| Median Filter | k=3 hoặc k=5 |
| Gaussian Blur | k=5, σ=0 (tự động) |
| Sharpen | Kernel [[0,-1,0],[-1,6,-1],[0,-1,0]] |
| Canny OpenCV | low=100, high=200 |
| Canny Skimage | sigma=2 |
| Bilateral | d=9, sigma=75 |
| Binary Threshold | T=128 |

## 6. Xử Lý Lỗi Thường Gặp

| Vấn Đề | Giải Pháp |
|--------|-----------|
| `cv2.error: (-215:Assertion failed)` | Kiểm tra file tồn tại, đúng đường dẫn |
| Màu hiển thị sai | Chuyển BGR→RGB trước `plt.imshow` |
| Tràn số uint8 | Dùng `np.float32` + `np.clip` |
| Kernel chẵn | Đảm bảo kernel size là số lẻ |
| File output không được tạo | Kiểm tra thư mục tồn tại, `os.makedirs` |
