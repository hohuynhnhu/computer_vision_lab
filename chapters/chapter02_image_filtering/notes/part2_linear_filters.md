# Lab 2 - Part II: Lọc tuyến tính (Linear Filtering)

---

## 1. Yêu cầu người dùng (User's Requirement)

- **Lọc trung vị** (median filter): giảm nhiễu muối tiêu bằng cách thay pixel bằng giá trị trung vị.
- **Lọc Gaussian** (Gaussian blur): làm mờ ảnh bằng kernel phân phối chuẩn.
- **Lọc làm sắc nét** (sharpen filter): tăng cường chi tiết và biên ảnh.

## 2. Tính năng (Features)

| STT | Tác vụ | Input | Output |
|-----|--------|-------|--------|
| 1 | Lọc trung vị (Median) | Ảnh RGB | Ảnh sau lọc trung vị |
| 2 | Lọc Gaussian | Ảnh RGB | Ảnh làm mờ mượt |
| 3 | Lọc làm sắc nét (Sharpen) | Ảnh RGB | Ảnh sắc nét hơn |

## 3. Giải pháp kỹ thuật (Tech Solutions)

- **OpenCV**: `cv2.medianBlur()`, `cv2.GaussianBlur()`, `cv2.filter2D()`.
- **NumPy**: tạo kernel `np.float32` cho sharpen filter.

## 4. Logic & AI

### Tích chập (Convolution)

$$g(x,y) = \sum_{i=-a}^{a}\sum_{j=-b}^{b} f(x+i, y+j) \cdot h(i,j)$$

- **Median filter**: Thay pixel tâm bằng **trung vị** của cửa sổ lân cận → hiệu quả với nhiễu xung.
- **Gaussian filter**: Trọng số kernel theo phân phối chuẩn $G(x,y) = \frac{1}{2\pi\sigma^2} e^{-\frac{x^2+y^2}{2\sigma^2}}$.
- **Sharpen filter**: Kernel tăng cường biên:
  ```
  [[ 0, -1,  0],
   [-1,  6, -1],
   [ 0, -1,  0]]
  ```

## 5. Triển khai (Implementation)

```python
def median_filter(image, kernel_size=3):
    if kernel_size % 2 == 0:
        raise ValueError("Kernel size must be an odd integer.")
    return cv2.medianBlur(image, kernel_size)

def gaussian_filter(image, kernel_size=5):
    return cv2.GaussianBlur(image, (kernel_size, kernel_size), 0)

def sharpen_filter(image):
    kernel = np.array([[0,-1,0],[-1,6,-1],[0,-1,0]], dtype=np.float32)
    return cv2.filter2D(image, -1, kernel)
```

## 6. Kiểm thử (Test)

| Test Case | Dữ liệu | Kết quả mong đợi | Trạng thái |
|-----------|---------|------------------|------------|
| Median filter (k=3) | `anh.jpg` | Ảnh mịn hơn | ✓ Pass |
| Gaussian filter (k=5) | `anh.jpg` | Ảnh mờ đều | ✓ Pass |
| Sharpen filter | `anh.jpg` | Chi tiết nổi bật hơn | ✓ Pass |

### Kết quả đầu ra

```
output/part2_linear_filters/
├── median_filtered.jpg       (~210 KB)
├── gaussian_filtered.jpg     (~174 KB)
└── sharpened.jpg             (~358 KB)
```
