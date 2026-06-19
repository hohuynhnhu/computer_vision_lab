# Lệnh: Giải Bài Tập Lab

## Mục Đích
Hướng dẫn giải một bài tập lab cụ thể, hoặc tự động thực thi code để tạo kết quả.

## Quy Trình

### Bước 1: Xác Định Bài Tập
- Người dùng chỉ định: `chương`, `phần`, `bài tập`
- Đọc thông tin từ `documents/requirement.md` và `documents/features.md`

### Bước 2: Chuẩn Bị

```python
# Các import cần thiết
import cv2
import numpy as np
import matplotlib.pyplot as plt
import os
import time  # nếu cần đo hiệu năng

# Tạo thư mục output nếu chưa tồn tại
os.makedirs('outputs/', exist_ok=True)
```

### Bước 3: Đọc Dữ Liệu

```python
# Xác định đường dẫn ảnh
input_dir = 'data/input/'  # hoặc '../input/'
image_files = [f for f in os.listdir(input_dir) if f.endswith(('.jpg', '.png', '.bmp'))]

if not image_files:
    raise FileNotFoundError(f"Không tìm thấy ảnh trong {input_dir}")

img = cv2.imread(os.path.join(input_dir, image_files[0]))
img_rgb = cv2.cvtColor(img, cv2.COLOR_BGR2RGB)
```

### Bước 4: Thực Thi Theo Yêu Cầu

Với mỗi bài tập, xác định:
1. **Công thức / Hàm**: từ `documents/tech_solution.md`
2. **Tham số**: mặc định từ `documents/features.md`
3. **Output**: tên file và thư mục

### Bước 5: Kiểm Tra Kết Quả

```python
# Kiểm tra ảnh output
output_path = f'outputs/result.jpg'
if os.path.exists(output_path):
    check_img = cv2.imread(output_path)
    print(f"[OK] Output created: {output_path} ({check_img.shape})")
else:
    print(f"[ERR] Output not found: {output_path}")
```

## Các Bài Tập Cụ Thể

### Chương 1: Cơ Bản

| Bài | Mô Tả | Code Chính |
|-----|-------|------------|
| 1 | Cài đặt | `!pip install opencv-python Pillow` |
| 2 | Đọc/hiển thị | `cv2.imread()`, `cvtColor(BGR2RGB)` |
| 3 | Không gian màu | `cvtColor(BGR2GRAY/HSV/LAB)` |
| 4 | Crop/resize | `img[y1:y2,x1:x2]`, `cv2.resize()` |
| 5 | Vẽ hình | `cv2.line/rectangle/circle/putText` |

### Chương 2: Lọc Ảnh

| Bài | Mô Tả | Code Chính |
|-----|-------|------------|
| 1-2 | Độ sáng | `np.clip(img.float32 ± 50, 0, 255)` |
| 3-4 | Tương phản | `np.clip(img.float32 * factor, 0, 255)` |
| 5 | Âm bản | `255 - img` |
| 6 | Ngưỡng | `cv2.threshold(gray, 128, 255, THRESH_BINARY)` |
| 7 | Median | `cv2.medianBlur(img, 3)` |
| 8 | Gaussian | `cv2.GaussianBlur(img, (5,5), 0)` |
| 9 | Sharpen | `cv2.filter2D(img, -1, kernel)` |
| 10 | Sobel | `cv2.filter2D(gray, CV_32F, sobel_x/y)` |
| 11 | Emboss | `cv2.filter2D(img, -1, kernel)` |
| 12 | Bilateral | `cv2.bilateralFilter(img, 9, 75, 75)` |

### Chương 3: Canny

| Bài | Mô Tả | Code Chính |
|-----|-------|------------|
| 1 | Canny OpenCV | `cv2.Canny(gray, 100, 200)` |
| 2 | Canny Skimage | `canny(gray_f, sigma=2)` |
| 3 | So sánh | Đếm điểm + đo thời gian |
| 4-5 | Tham số | Loop qua các giá trị ngưỡng/sigma |
| 6-8 | Đánh giá | Gaussian/Canny, Equalize/Canny |
| 9-10 | Contour | `cv2.findContours()` |
| 11 | Shape | `cv2.approxPolyDP()` + phân loại |

## Template Trả Lời

```markdown
## [Tên bài tập]

### Yêu cầu
[Trích từ requirement.md]

### Cách giải
\`\`\`python
[code chính]
\`\`\`

### Kết quả
[Output image path / shape / kích thước]

### Giải thích
[Nguyên lý / công thức]

### Kiểm thử nhanh
[Test case cơ bản]
```
