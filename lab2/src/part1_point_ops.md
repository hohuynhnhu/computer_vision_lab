# Lab 2 - Part I: Biến đổi điểm ảnh (Point Operations)

---

## 1. Yêu cầu người dùng (User's Requirement)

- **Thay đổi độ sáng** (brightness): tăng/giảm giá trị pixel để ảnh sáng hơn hoặc tối hơn.
- **Thay đổi độ tương phản** (contrast): nhân giá trị pixel với hệ số scale để tăng/giảm độ tương phản.
- **Biến đổi âm bản** (negative): đảo ngược màu sắc, `new = 255 - old`.
- **Cắt ngưỡng nhị phân** (binary threshold): chuyển ảnh xám, pixel > threshold → 255, còn lại → 0.

## 2. Tính năng (Features)

| STT | Tác vụ | Input | Output |
|-----|--------|-------|--------|
| 1 | Tăng/Giảm độ sáng | Ảnh RGB | Ảnh sáng hơn / tối hơn |
| 2 | Tăng/Giảm tương phản | Ảnh RGB | Ảnh tương phản cao / thấp |
| 3 | Biến đổi âm bản | Ảnh RGB | Ảnh đảo màu (negative) |
| 4 | Cắt ngưỡng nhị phân | Ảnh RGB | Ảnh đen trắng (binary) |

## 3. Giải pháp kỹ thuật (Tech Solutions)

- **Python 3.11** + **OpenCV** (`cv2`): đọc/ghi ảnh, cắt ngưỡng.
- **NumPy**: broadcast phép toán trên toàn bộ mảng pixel, `np.clip()`.
- **Matplotlib**: hiển thị ảnh (`plt.imshow`).

## 4. Logic & AI

### Phép toán trên điểm ảnh

Các phép biến đổi áp dụng **độc lập trên từng pixel**, không phụ thuộc lân cận:

| Phép toán | Công thức |
|-----------|-----------|
| Độ sáng | `new = pixel + factor` |
| Tương phản | `new = pixel * factor` |
| Âm bản | `new = 255 - pixel` |
| Ngưỡng | `new = 255 nếu pixel > T, ngược lại 0` |

Dùng `np.float32` cho tính toán trung gian, `np.clip(..., 0, 255)` đảm bảo giá trị hợp lệ.

## 5. Triển khai (Implementation)

```python
def adjust_brightness(image, factor=50):
    return np.clip(image.astype(np.float32) + factor, 0, 255).astype(np.uint8)

def adjust_contrast(image, factor=1.5):
    return np.clip(image.astype(np.float32) * factor, 0, 255).astype(np.uint8)

def negative_transform(image):
    return 255 - image

def binary_threshold(image, threshold=128):
    gray = cv2.cvtColor(image, cv2.COLOR_RGB2GRAY)
    _, binary = cv2.threshold(gray, threshold, 255, cv2.THRESH_BINARY)
    return binary
```

## 6. Kiểm thử (Test)

| Test Case | Dữ liệu | Kết quả mong đợi | Trạng thái |
|-----------|---------|------------------|------------|
| Tăng độ sáng (+50) | `anh.jpg` | Ảnh sáng hơn rõ rệt | ✓ Pass |
| Giảm độ sáng (-50) | `anh.jpg` | Ảnh tối hơn | ✓ Pass |
| Tăng tương phản (x1.5) | `anh.jpg` | Màu sắc rực rỡ hơn | ✓ Pass |
| Giảm tương phản (x0.5) | `anh.jpg` | Ảnh xám nhạt hơn | ✓ Pass |
| Âm bản | `anh.jpg` | Màu bị đảo ngược | ✓ Pass |
| Cắt ngưỡng (T=128) | `anh.jpg` | Chỉ còn đen và trắng | ✓ Pass |

### Kết quả đầu ra

```
output/part1_point_ops/
├── brightness_bright.jpg       (~221 KB)
├── brightness_dark.jpg         (~218 KB)
├── contrast_increased.jpg      (~256 KB)
├── contrast_reduced.jpg        (~173 KB)
├── negative.jpg                (~221 KB)
└── binary_threshold.jpg        (~249 KB)
```
