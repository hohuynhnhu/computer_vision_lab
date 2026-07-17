# Chương 2: Lọc Ảnh - Triển Khai

## 1. Cấu Trúc Code

Chương 2 gồm 3 notebook trong `notebook/`:

| Notebook | Nội Dung | File Đầu Ra |
|----------|----------|-------------|
| `point_operations.ipynb` | Part I: Biến đổi điểm ảnh | `outputs/point_operations/` |
| `linear_filters.ipynb` | Part II: Lọc tuyến tính | `outputs/linear_filters/` |
| `advanced_filters.ipynb` | Part III: Bộ lọc nâng cao | `outputs/advanced_filters/` |

## 2. Part I: Biến Đổi Điểm Ảnh

### Import và Đọc Ảnh

```python
%matplotlib inline
import cv2
import numpy as np
import matplotlib.pyplot as plt
import os

image = cv2.imread('../input/anh.jpg')
image = image[:, :, ::-1]  # BGR -> RGB
print(f'Image loaded: {image.shape}, dtype={image.dtype}')
```

### 2.1 Thay Đổi Độ Sáng

```python
# factor = -50 (tối) và factor = +50 (sáng)
bright = np.clip(image.astype(np.float32) + 50, 0, 255).astype(np.uint8)
dark   = np.clip(image.astype(np.float32) - 50, 0, 255).astype(np.uint8)

fig, axes = plt.subplots(1, 3, figsize=(12, 4))
axes[0].imshow(dark)
axes[0].set_title('Factor = -50 (Tối hơn)')
axes[1].imshow(image)
axes[1].set_title('Original')
axes[2].imshow(bright)
axes[2].set_title('Factor = +50 (Sáng hơn)')
plt.tight_layout()
plt.show()

cv2.imwrite('output/part1_point_ops/brightness_dark.jpg', cv2.cvtColor(dark, cv2.COLOR_RGB2BGR))
cv2.imwrite('output/part1_point_ops/brightness_bright.jpg', cv2.cvtColor(bright, cv2.COLOR_RGB2BGR))
```

### 2.2 Thay Đổi Tương Phản

```python
# factor = 0.5 (giảm) và factor = 1.5 (tăng)
low_contrast  = np.clip(image.astype(np.float32) * 0.5, 0, 255).astype(np.uint8)
high_contrast = np.clip(image.astype(np.float32) * 1.5, 0, 255).astype(np.uint8)

fig, axes = plt.subplots(1, 3, figsize=(12, 4))
axes[0].imshow(low_contrast)
axes[0].set_title('Factor = 0.5 (Giảm)')
axes[1].imshow(image)
axes[1].set_title('Original')
axes[2].imshow(high_contrast)
axes[2].set_title('Factor = 1.5 (Tăng)')
plt.tight_layout()
plt.show()
```

### 2.3 Biến Đổi Âm Bản

```python
negative = 255 - image

fig, axes = plt.subplots(1, 2, figsize=(10, 4))
axes[0].imshow(image)
axes[0].set_title('Original')
axes[1].imshow(negative)
axes[1].set_title('Negative (255 - pixel)')
plt.tight_layout()
plt.show()
```

### 2.4 Cắt Ngưỡng Nhị Phân

```python
gray = cv2.cvtColor(image, cv2.COLOR_RGB2GRAY)
_, binary = cv2.threshold(gray, 128, 255, cv2.THRESH_BINARY)

fig, axes = plt.subplots(1, 2, figsize=(10, 4))
axes[0].imshow(image)
axes[0].set_title('Original')
axes[1].imshow(binary, cmap='gray')
axes[1].set_title('Binary (threshold = 128)')
plt.tight_layout()
plt.show()
```

## 3. Part II: Lọc Tuyến Tính

### Các Hàm Lọc

```python
def mean_filter(image, kernel_size=3):
    if kernel_size % 2 == 0:
        raise ValueError("Kernel size must be an odd integer.")
    return cv2.blur(image, (kernel_size, kernel_size))

def gaussian_filter(image, kernel_size=5):
    return cv2.GaussianBlur(image, (kernel_size, kernel_size), 0)

def sharpen_filter(image):
    kernel = np.array([[0,-1,0],[-1,6,-1],[0,-1,0]], dtype=np.float32)
    return cv2.filter2D(image, -1, kernel)
```

## 4. Part III: Bộ Lọc Nâng Cao

```python
def sobel_edge_detection(image):
    gray = cv2.cvtColor(image, cv2.COLOR_RGB2GRAY)
    sobel_x = np.array([[-1,0,1],[-2,0,2],[-1,0,1]], dtype=np.float32)
    sobel_y = np.array([[-1,-2,-1],[0,0,0],[1,2,1]], dtype=np.float32)
    gx = cv2.filter2D(gray, cv2.CV_32F, sobel_x)
    gy = cv2.filter2D(gray, cv2.CV_32F, sobel_y)
    return np.clip(np.sqrt(gx**2 + gy**2), 0, 255).astype(np.uint8)

def emboss_filter(image):
    kernel = np.array([[-2,-1,0],[-1,1,1],[0,1,2]], dtype=np.float32)
    return cv2.filter2D(image, -1, kernel)

def bilateral_filter(image, d=9, sigma=75):
    return cv2.bilateralFilter(image, d, sigma, sigma)
```

## 5. Thứ Tự Chạy

1. Chạy `point_operations.ipynb` → Part I
2. Chạy `linear_filters.ipynb` → Part II
3. Chạy `advanced_filters.ipynb` → Part III

Tất cả notebook đều độc lập và đọc ảnh từ `../input/anh.jpg`.
