# Chương 1: Cơ Bản Về Ảnh - Triển Khai

## 1. Cấu Trúc Code

File notebook: `notebook/chapter01.ipynb` gồm 5 cell chính, mỗi cell tương ứng với một bài tập.

## 2. Chi Tiết Triển Khai Từng Bài

### Bài 1: Cài Đặt Thư Viện

```python
!pip install opencv-python Pillow
import cv2
from PIL import Image
import numpy as np
import matplotlib.pyplot as plt

print('OpenCV version:', cv2.__version__)
import PIL; print('Pillow version:', PIL.__version__)
```

**Giải thích**: Sử dụng `!pip install` trong Jupyter để cài thư viện, sau đó import và kiểm tra phiên bản.

### Bài 2: Đọc và Hiển Thị Ảnh

```python
img_bgr = cv2.imread('lab1.jpg')
img_rgb = cv2.cvtColor(img_bgr, cv2.COLOR_BGR2RGB)

plt.figure(figsize=(5, 5))
plt.imshow(img_rgb)
plt.title('Ảnh gốc')
plt.axis('off')
plt.show()

print('Kích thước ảnh:', img_rgb.shape)

cv2.imwrite('lab1_saved.png', img_bgr)
print('Đã lưu ảnh dưới định dạng PNG')

pil_img = Image.fromarray(img_rgb)
pil_img.save('lab1_pillow.bmp')
print('Đã lưu ảnh dưới định dạng BMP bằng Pillow')
```

**Giải thích**:
- `cv2.imread()` trả về mảng NumPy định dạng BGR
- Cần `cv2.cvtColor` BGR→RGB trước khi dùng `plt.imshow()`
- OpenCV lưu với `cv2.imwrite()`, Pillow với `Image.save()`

### Bài 3: Chuyển Đổi Không Gian Màu

```python
gray = cv2.cvtColor(img_bgr, cv2.COLOR_BGR2GRAY)
hsv  = cv2.cvtColor(img_bgr, cv2.COLOR_BGR2HSV)
lab  = cv2.cvtColor(img_bgr, cv2.COLOR_BGR2LAB)

fig, axes = plt.subplots(1, 4, figsize=(16, 4))
axes[0].imshow(img_rgb);          axes[0].set_title('RGB (gốc)');    axes[0].axis('off')
axes[1].imshow(gray, cmap='gray');axes[1].set_title('Grayscale');    axes[1].axis('off')
axes[2].imshow(hsv);              axes[2].set_title('HSV');          axes[2].axis('off')
axes[3].imshow(lab);              axes[3].set_title('LAB');          axes[3].axis('off')
plt.tight_layout()
plt.show()
```

**Giải thích**: Mỗi lần gọi `cv2.cvtColor` với cờ phù hợp để chuyển sang không gian màu tương ứng.

### Bài 4: Cắt Ảnh và Resize

```python
# Cắt vùng ảnh: img[y1:y2, x1:x2]
crop = img_rgb[100:350, 100:350]

# Resize cố định
resized_fixed = cv2.resize(img_rgb, (200, 200))

# Resize theo tỷ lệ (50%)
scale = 0.5
h, w = img_rgb.shape[:2]
resized_scale = cv2.resize(img_rgb, (int(w * scale), int(h * scale)))

fig, axes = plt.subplots(1, 3, figsize=(14, 4))
axes[0].imshow(crop);          axes[0].set_title('Cắt vùng [100:350, 100:350]'); axes[0].axis('off')
axes[1].imshow(resized_fixed); axes[1].set_title('Resize cố định 200×200');      axes[1].axis('off')
axes[2].imshow(resized_scale); axes[2].set_title('Resize 50% tỷ lệ');            axes[2].axis('off')
plt.tight_layout()
plt.show()

print('Kích thước ảnh gốc:', img_rgb.shape[:2])
print('Kích thước sau crop:', crop.shape[:2])
print('Kích thước sau resize 50%:', resized_scale.shape[:2])
```

### Bài 5: Vẽ Hình và Văn Bản

```python
canvas = img_rgb.copy()

cv2.line(canvas, (0, 0), (512, 512), (255, 0, 0), 3)       # Đỏ
cv2.rectangle(canvas, (50, 50), (200, 200), (0, 255, 0), 3) # Xanh lá
cv2.circle(canvas, (380, 130), 80, (0, 0, 255), 3)          # Xanh dương
cv2.putText(canvas, 'OpenCV Demo', (120, 460),
            cv2.FONT_HERSHEY_SIMPLEX, 1.2, (255, 255, 0), 2)

plt.figure(figsize=(6, 6))
plt.imshow(canvas)
plt.title('Vẽ hình cơ bản + văn bản')
plt.axis('off')
plt.show()

cv2.imwrite('result_drawing.png', cv2.cvtColor(canvas, cv2.COLOR_RGB2BGR))
print('Đã lưu result_drawing.png')
```

**Lưu ý**: Do canvas ở dạng RGB, khi lưu bằng `cv2.imwrite` cần chuyển ngược về BGR.

## 3. Thứ Tự Chạy

1. Chạy cell 1 (cài đặt) trước tiên
2. Chạy cell 2 (đọc ảnh) - các cell sau phụ thuộc vào biến `img_bgr`, `img_rgb`
3. Chạy cell 3, 4, 5 theo thứ tự tùy ý
