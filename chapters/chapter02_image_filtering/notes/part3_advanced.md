# Lab 2 - Part III: Nâng cao (Advanced Filters)

---

## 1. Yêu cầu người dùng (User's Requirement)

- **Phát hiện cạnh Sobel**: dùng kernel đạo hàm Sobel X và Y để tính gradient magnitude.
- **Kernel tùy chỉnh (Emboss)**: thiết kế kernel riêng tạo hiệu ứng ảnh nổi.
- **So sánh bộ lọc**: hiển thị 4 ảnh (Original, Mean, Gaussian, Sharpen) trên cùng grid.
- **Lọc song phương (Bilateral)**: lọc phi tuyến giảm nhiễu nhưng giữ cạnh.

## 2. Tính năng (Features)

| STT | Tác vụ | Input | Output |
|-----|--------|-------|--------|
| 1 | Phát hiện cạnh Sobel | Ảnh RGB | Ảnh biên (grayscale) |
| 2 | Kernel Emboss | Ảnh RGB | Ảnh hiệu ứng nổi |
| 3 | So sánh bộ lọc | Ảnh RGB | Grid 4 ảnh so sánh |
| 4 | Lọc song phương | Ảnh RGB | Ảnh giảm nhiễu, giữ cạnh |

## 3. Giải pháp kỹ thuật (Tech Solutions)

- **OpenCV**: `cv2.filter2D()` với `CV_32F`, `cv2.blur()`, `cv2.GaussianBlur()`, `cv2.bilateralFilter()`.
- **NumPy**: `np.sqrt()` tính gradient magnitude, kernel tùy chỉnh, `np.frombuffer()`.
- **Matplotlib**: render grid 4 ảnh thành numpy array.

## 4. Logic & AI

### Sobel Edge Detection
Sobel sử dụng 2 kernel 3x3 cho đạo hàm theo X và Y:

- **Sobel X**: phát hiện cạnh dọc (gradient ngang)
- **Sobel Y**: phát hiện cạnh ngang (gradient dọc)

$$G = \sqrt{G_x^2 + G_y^2}$$

### Emboss Filter
Kernel trọng số chéo tạo hiệu ứng ánh sáng từ góc trên bên trái:
```
[[-2, -1, 0],
 [-1,  1, 1],
 [ 0,  1, 2]]
```

### Bilateral Filter
Lọc phi tuyến kết hợp 2 trọng số:
- **Khoảng cách không gian**: pixel càng gần, trọng số càng cao.
- **Khoảng cách cường độ**: pixel càng giống màu, trọng số càng cao.

→ **Giảm nhiễu nhưng giữ được cạnh** (edge-preserving).

## 5. Triển khai (Implementation)

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

## 6. Kiểm thử (Test)

| Test Case | Dữ liệu | Kết quả mong đợi | Trạng thái |
|-----------|---------|------------------|------------|
| Sobel edge detect | `anh.jpg` | Cạnh được làm nổi bật | ✓ Pass |
| Emboss filter | `anh.jpg` | Hiệu ứng nổi 3D | ✓ Pass |
| Compare filters | `anh.jpg` | Grid 4 ảnh rõ rệt | ✓ Pass |
| Bilateral filter | `anh.jpg` | Giảm nhiễu + giữ cạnh | ✓ Pass |

### Kết quả đầu ra

```
output/part3_advanced/
├── sobel_edges.jpg            (~430 KB)
├── emboss.jpg                 (~485 KB)
├── filter_comparison.jpg      (~211 KB)
├── median_nonlinear.jpg       (~172 KB)
└── bilateral_filtered.jpg     (~166 KB)
```
