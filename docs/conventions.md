# Quy Ước Code

## 1. Ngôn Ngữ

- **Comment và docstring**: Tiếng Việt
- **Code (tên biến, tên hàm, keywords)**: Tiếng Anh / snake_case
- **Markdown**: Tiếng Việt

## 2. Đặt Tên

### 2.1 Biến
```python
# Tên biến mô tả rõ ràng bằng tiếng Anh
img_bgr = cv2.imread('image.jpg')   # ảnh định dạng BGR
img_rgb = cv2.cvtColor(img_bgr, cv2.COLOR_BGR2RGB)  # ảnh định dạng RGB
img_gray = cv2.cvtColor(img_bgr, cv2.COLOR_BGR2GRAY) # ảnh xám

# Bad
a = cv2.imread('image.jpg')
img = a
```

### 2.2 Hàm
```python
# snake_case, mô tả hành động
def median_filter(image, kernel_size=3):
def gaussian_filter(image, kernel_size=5):
def sobel_edge_detection(image):
```

### 2.3 File
- Notebook: mô tả nội dung chính (vd: `point_operations.ipynb`)
- Ảnh đầu vào: tên file gốc (vd: `anh.jpg`, `demo.jpg`)
- Ảnh đầu ra: mô tả kỹ thuật + tham số (vd: `brightness_dark.jpg`, `canny_opencv_default.png`)

## 3. Xử Lý Ảnh

### 3.1 Định Dạng Màu

```python
# Luôn ghi rõ định dạng màu trong tên biến
img_bgr    # OpenCV native (BGR)
img_rgb    # Dùng cho matplotlib
img_gray   # Ảnh xám 1 kênh
```

### 3.2 An Toàn Giá Trị Pixel

```python
# LUÔN luôn dùng np.clip khi biến đổi giá trị pixel
result = np.clip(image.astype(np.float32) + 50, 0, 255).astype(np.uint8)

# KHÔNG làm:
result = (image + 50).astype(np.uint8)  # có thể tràn số / sai giá trị
```

### 3.3 Hiển Thị

```python
# Ảnh màu: hiển thị RGB (không phải BGR)
plt.imshow(img_rgb)

# Ảnh xám: thêm cmap='gray'
plt.imshow(gray, cmap='gray')

# Ảnh nhị phân: cmap='gray'
plt.imshow(binary, cmap='gray')
```

## 4. Cấu Trúc Notebook

Mỗi notebook tuân theo cấu trúc:

1. **Header**: Tiêu đề, họ tên, MSSV, lớp (markdown cell)
2. **Import**: Tất cả imports ở cell đầu tiên
3. **Đọc dữ liệu**: Đọc ảnh từ thư mục `input/`
4. **Xử lý**: Chia thành các section nhỏ với markdown mô tả
5. **Lưu kết quả**: Lưu vào thư mục `outputs/`
6. **Tổng kết**: Bảng tổng kết các tác vụ

## 5. Quy Ước Git

- Commit message bằng tiếng Việt
- Không commit ảnh output, dữ liệu lớn
- Mỗi commit là một thay đổi logic độc lập

## 6. Thư Viện

### Import Chuẩn

```python
import cv2
import numpy as np
import matplotlib.pyplot as plt
from PIL import Image
import os
import time
# Skimage chỉ dùng khi cần
from skimage.feature import canny
```

### Phiên Bản Yêu Cầu

| Thư Viện | Phiên Bản Tối Thiểu |
|----------|---------------------|
| opencv-python | 4.11 |
| numpy | 1.26 |
| matplotlib | 3.5+ |
| Pillow | 12.0 |
| scikit-image | 0.19+ |
