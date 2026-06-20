# Chapter 03: Phát Hiện Cạnh (Edge Detection) — Agent Context

## Tổng Quan
Chương này tập trung vào thuật toán **Canny Edge Detection** — phương pháp phát hiện cạnh đa bước tiêu chuẩn. Sinh viên thực hành qua OpenCV và Scikit-image, phân tích ảnh hưởng tham số, đánh giá trên nhiều loại ảnh, và kết hợp Canny với contour/shape detection.

## Cấu Trúc
- **4 Notebooks**: `canny_opencv.ipynb` (Phần 1), `parameter_analysis.ipynb` (Phần 2), `image_evaluation.ipynb` (Phần 3), `integration_experiment.ipynb` (Phần 4)
- **Documents**: `requirement.md`, `features.md`, `tech_solution.md`, `logic_ai.md`, `implementation.md`, `testing.md`
- **Notes**: `1.md` (Phần 1), `2.md` (Phần 2), `3.md` (Phần 3), `4.md` (Phần 4 + Báo cáo)
- **Input**: `data/input/demo.jpg`
- **Output**: `outputs/01_gray.png` → `07_compare_default_value.png`

## 4 Phần — 12 Bài Tập

### Phần 1: Thực Hiện Canny Bằng Thư Viện
| # | Bài | Phương pháp | Ghi chú |
|---|-----|-------------|---------|
| 1 | Canny với OpenCV | `cv2.Canny(gray, 100, 200)` | Kết quả uint8 (0/255) |
| 2 | Canny với Scikit-image | `skimage.feature.canny(gray_float, sigma=2)` | Kết quả Boolean, cần float [0,1] |
| 3 | So sánh kết quả | Số điểm cạnh + thời gian (ms) | Dùng `np.sum(edges > 0)` và `time.time()` |

### Phần 2: Phân Tích Tham Số
| # | Bài | Tham số khảo sát | Kết quả mong đợi |
|---|-----|-----------------|-----------------|
| 4 | Thay đổi ngưỡng OpenCV | (50,150), (100,200), (150,250) | Ngưỡng thấp → nhiều cạnh hơn |
| 5 | Thay đổi sigma Scikit-image | sigma = 1, 2, 3 | Sigma lớn → ít cạnh hơn |
| 6 | So sánh với mặc định | OpenCV(100,200) vs Skimage(sigma=2) | Kết quả khác nhau do cách kiểm soát |

### Phần 3: Đánh Giá Trên Nhiều Loại Ảnh
| # | Loại ảnh | Phương pháp so sánh | Kết luận |
|---|----------|---------------------|---------|
| 7 | Ảnh nhiều nhiễu | Canny trực tiếp vs `GaussianBlur(5,5)` + Canny | Blur giảm cạnh giả đáng kể |
| 8 | Ảnh tương phản thấp | Canny trực tiếp vs `equalizeHist` + Canny | EqualizeHist giúp phát hiện nhiều cạnh hơn |
| 9 | Ảnh nhiều chi tiết | Canny với nhiều bộ tham số | Tăng ngưỡng để lọc bớt cạnh yếu |

### Phần 4: Kết Hợp Với Kỹ Thuật Khác
| # | Bài | Công cụ | Ghi chú |
|---|-----|---------|---------|
| 10 | Canny + Phân đoạn ảnh | `cv2.findContours()` / watershed | Lọc contour nhỏ (area < 100) |
| 11 | Canny + Nhận dạng hình dạng | `cv2.approxPolyDP()` + circularity | C = 4π·Area/Perimeter² |
| 12 | Báo cáo tổng kết | Kết luận nên/không nên dùng Canny | Xem mục "Kết Luận" bên dưới |

## Thuật Toán Canny — 5 Bước

1. **Gaussian Smoothing**: $I_{smooth} = I * G_{\sigma}$ — làm mịn giảm nhiễu
2. **Tính Gradient**: Sobel X/Y → $G_x$, $G_y$
3. **Magnitude & Hướng**: $G = \sqrt{G_x^2 + G_y^2}$, $\theta = \arctan(G_y/G_x)$
4. **Non-Maximum Suppression**: Giữ pixel lớn nhất theo hướng gradient → cạnh mỏng 1px
5. **Hysteresis Thresholding**: $> T_{high}$ → cạnh mạnh; $< T_{low}$ → loại; ở giữa → cạnh nếu nối với cạnh mạnh

## So Sánh OpenCV vs Scikit-image

| Tiêu chí | OpenCV `cv2.Canny` | Scikit-image `feature.canny` |
|----------|---------------------|------------------------------|
| Tham số | `low_threshold`, `high_threshold` | `sigma` |
| Kiểm soát | Trực tiếp qua ngưỡng | Qua mức làm mịn (ngưỡng tự động) |
| Làm mịn nội bộ | Gaussian σ=1.4 (cố định) | σ tùy chỉnh |
| Đầu vào | `uint8` [0, 255] | `float64` [0, 1] |
| Đầu ra | `uint8` (0 hoặc 255) | `bool` (True/False) |
| Tốc độ | Nhanh (C/C++) | Chậm hơn (Python) |

## Ảnh Hưởng Tham Số

### Ngưỡng (OpenCV)
| Tình huống | Kết quả |
|------------|---------|
| Cả hai thấp | Nhiều cạnh, nhiều cạnh giả |
| Cả hai cao | Ít cạnh, có thể mất cạnh yếu |
| **Khuyến nghị** | $T_{high} \approx 2 \times T_{low}$ hoặc $3 \times T_{low}$ |

### Sigma (Scikit-image)
| Giá trị | Kết quả |
|---------|---------|
| σ nhỏ (1) | Giữ nhiều chi tiết, dễ có nhiễu |
| σ vừa (2) | Cân bằng chi tiết và giảm nhiễu ✓ |
| σ lớn (3+) | Ảnh mịn, giảm nhiễu nhưng mất cạnh nhỏ |

## Pipeline Tiền Xử Lý

```
Canny trực tiếp:        gray → cv2.Canny(100, 200)
Gaussian + Canny:       gray → GaussianBlur(5,5) → cv2.Canny(100, 200)
EqualizeHist + Canny:   gray → equalizeHist() → cv2.Canny(100, 200)
```

## Phân Loại Hình Dạng (Circularity C = 4π·Area / Perimeter²)

```
approxPolyDP → đếm đỉnh
    3 đỉnh            → Tam giác
    4 đỉnh            → Tứ giác
    >4, C > 0.8       → Hình tròn
    >4, C ≤ 0.8       → Đa giác
```

## Ràng Buộc Quan Trọng
- Resize tất cả ảnh về **512 × 512** trước khi xử lý
- Đo thời gian: `t = time.time()` trước và sau, nhân 1000 để ra ms
- Đếm cạnh: `np.sum(edges > 0)` — works cho cả uint8 và Boolean
- Scikit-image: **bắt buộc** dùng `gray.astype(np.float64) / 255.0`

## Kết Luận Tổng Quát

### Nên dùng Canny
- Ảnh tương phản tốt, cần phát hiện biên chính xác
- Làm đầu vào cho contour detection / shape recognition
- Ứng dụng phân đoạn ảnh (image segmentation)

### Không nên dùng Canny (cần tiền xử lý trước)
- Ảnh quá nhiễu → cần `GaussianBlur` trước
- Ảnh tương phản quá thấp → cần `equalizeHist` trước
- Yêu cầu cạnh dày hơn 1 pixel

## Lỗi Thường Gặp
| Lỗi | Nguyên nhân | Cách khắc phục |
|-----|-------------|----------------|
| `error: (-215) !_src.empty()` | Ảnh không tìm thấy | Kiểm tra đường dẫn `../data/input/demo.jpg` |
| Skimage báo lỗi dtype | Ảnh chưa chuẩn hóa [0,1] | `gray.astype(np.float64) / 255.0` |
| Canny quá nhiều cạnh | Ngưỡng thấp hoặc ảnh nhiễu | Tăng ngưỡng hoặc thêm `GaussianBlur` |
| `findContours` trả về rỗng | Edge map rỗng hoặc sai dtype | Kiểm tra `edges` là `uint8` và có cạnh |
| Nhãn vẽ sai vị trí | `M["m00"] == 0` | Thêm điều kiện `if M["m00"] != 0` |

## Thứ Tự Chạy
1. `canny_opencv.ipynb` → Phần 1 (cơ bản, **chạy trước tiên**)
2. `parameter_analysis.ipynb` → Phần 2 (phân tích tham số)
3. `image_evaluation.ipynb` → Phần 3 (đánh giá loại ảnh)
4. `integration_experiment.ipynb` → Phần 4 (kết hợp + báo cáo)
