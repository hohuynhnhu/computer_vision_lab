# Chapter 03: Phát Hiện Cạnh (Edge Detection) — Agent Context

## Tổng Quan
Chương này tập trung vào thuật toán **Canny Edge Detection** — một trong những phương pháp phát hiện cạnh phổ biến nhất. Sinh viên thực hành Canny qua OpenCV và Scikit-image, phân tích ảnh hưởng tham số, đánh giá trên nhiều loại ảnh, và kết hợp Canny với contour/shape detection.

## Cấu Trúc
- **4 Notebooks**: `canny_opencv.ipynb` (Phần 1), `parameter_analysis.ipynb` (Phần 2), `image_evaluation.ipynb` (Phần 3), `integration_experiment.ipynb` (Phần 4)
- **Documents**: `requirement.md`, `features.md`, `tech_solution.md`, `logic_ai.md`, `implementation.md`, `testing.md`
- **Notes**: `1.md` (Phần 1), `2.md` (Phần 2), `3.md` (Phần 3+4 intro), `4.md` (Phần 4 + Báo cáo)
- **Input**: `data/input/demo.jpg`
- **Output**: `outputs/01_gray.png` → `07_compare_default_value.png`

## 4 Phần — 12 Bài Tập

### Phần 1: Thực Hiện Canny Bằng Thư Viện
| # | Bài | Phương pháp |
|---|-----|-------------|
| 1 | Canny với OpenCV | `cv2.Canny(gray, 100, 200)` — ảnh nhị phân uint8 |
| 2 | Canny với Scikit-image | `skimage.feature.canny(gray_float, sigma=2)` — mảng Boolean |
| 3 | So sánh kết quả | Đếm số điểm cạnh, đo thời gian (ms), hiển thị grid 3 ảnh |

### Phần 2: Phân Tích Tham Số
| # | Bài | Tham số khảo sát |
|---|-----|-----------------|
| 4 | Thay đổi ngưỡng OpenCV | (50,150), (100,200), (150,250) |
| 5 | Thay đổi sigma Scikit-image | sigma = 1, 2, 3 |
| 6 | So sánh với mặc định | OpenCV(100,200) vs Skimage(sigma=2) |

### Phần 3: Đánh Giá Trên Nhiều Loại Ảnh
| # | Loại ảnh | Phương pháp so sánh |
|---|----------|---------------------|
| 7 | Ảnh nhiều nhiễu | Canny trực tiếp vs Gaussian Blur + Canny |
| 8 | Ảnh tương phản thấp | Canny trực tiếp vs Histogram Equalization + Canny |
| 9 | Ảnh nhiều chi tiết | Canny với nhiều bộ tham số khác nhau |

### Phần 4: Kết Hợp Với Kỹ Thuật Khác
| # | Bài | Công cụ |
|---|-----|---------|
| 10 | Canny + Phân đoạn ảnh | `cv2.findContours()` / watershed |
| 11 | Canny + Nhận dạng hình dạng | `cv2.findContours()` + `cv2.approxPolyDP()` + circularity |
| 12 | Báo cáo tổng kết | Kết luận các trường hợp nên/không nên dùng Canny |

## Thuật Toán Canny — 5 Bước

1. **Gaussian Smoothing**: Làm mịn ảnh để giảm nhiễu: $I_{smooth} = I * G_{\sigma}$
2. **Tính Gradient**: Đạo hàm theo X và Y (dùng Sobel): $G_x = \partial I/\partial x$, $G_y = \partial I/\partial y$
3. **Tính Magnitude & Hướng**: $G = \sqrt{G_x^2 + G_y^2}$, $\theta = \arctan(G_y/G_x)$
4. **Non-Maximum Suppression (NMS)**: Giữ pixel có gradient lớn nhất theo hướng gradient → cạnh mỏng 1px
5. **Hysteresis Thresholding**:
   - $> T_{high}$ → chắc chắn là cạnh
   - $< T_{low}$ → không phải cạnh
   - Giữa 2 ngưỡng → là cạnh nếu kết nối với pixel $> T_{high}$

## So Sánh OpenCV vs Scikit-image

| Tiêu chí | OpenCV `cv2.Canny` | Scikit-image `feature.canny` |
|----------|---------------------|------------------------------|
| Tham số chính | `low_threshold`, `high_threshold` | `sigma` |
| Cách kiểm soát | Trực tiếp qua ngưỡng | Qua mức làm mịn (ngưỡng tự động từ histogram gradient) |
| Làm mịn | Tự động Gaussian σ=1.4 (không đổi được) | σ tùy chỉnh qua tham số `sigma` |
| Kết quả | Ảnh nhị phân uint8 (0 hoặc 255) | Mảng Boolean |
| Tốc độ | Nhanh hơn (C/C++) | Chậm hơn (Python) |
| Tính tùy biến | Ít (chỉ 2 ngưỡng) | Nhiều (sigma + ngưỡng tự động) |

## Ảnh Hưởng Của Tham Số

### Ngưỡng (low_threshold, high_threshold)
| Tình huống | Kết quả |
|------------|---------|
| Cả hai thấp | Nhiều cạnh, nhiều cạnh giả |
| Cả hai cao | Ít cạnh, có thể mất cạnh yếu |
| Khoảng cách lớn | Nhiều cạnh yếu được giữ |
| Khoảng cách nhỏ | Cạnh sạch, ít nhiễu |
| **Khuyến nghị** | $T_{high} \approx 2 \times T_{low}$ hoặc $3 \times T_{low}$ |

### Sigma
| Giá trị | Kết quả |
|---------|---------|
| σ nhỏ (1) | Giữ nhiều chi tiết, dễ có nhiễu |
| σ vừa (2) | Cân bằng chi tiết và giảm nhiễu |
| σ lớn (3+) | Ảnh mịn, giảm nhiễu tốt nhưng mất cạnh nhỏ |

## Pipeline Tiền Xử Lý

```
Canny trực tiếp:        gray → cv2.Canny(100, 200)
Gaussian + Canny:       gray → cv2.GaussianBlur(5,5) → cv2.Canny(100, 200)
EqualizeHist + Canny:   gray → cv2.equalizeHist() → cv2.Canny(100, 200)
```

## Pipeline Nhận Dạng Hình Dạng

```
Ảnh → Canny → Edge Map → cv2.findContours(RETR_EXTERNAL, CHAIN_APPROX_SIMPLE)
                               │
                               ▼
                        cv2.approxPolyDP(cnt, 0.04*peri, True)
                               │
                ┌──────────────┼──────────────┐
                ▼              ▼              ▼
          3 đỉnh→Tam giác  4 đỉnh→Tứ giác   >4 đỉnh → kiểm tra circularity
                                                          │
                                             C = 4π·Area/Perimeter²
                                             C > 0.8 → Hình tròn
                                             C ≤ 0.8 → Đa giác
```

## Ràng Buộc Quan Trọng
- Resize tất cả ảnh về **512 × 512** để so sánh đồng nhất
- Đo thời gian xử lý (ms) cho từng phương pháp
- Đếm số điểm cạnh phát hiện được (`np.sum(edges > 0)`)
- Ảnh cho Scikit-image cần chuẩn hóa về [0, 1] float: `gray.astype(np.float64) / 255.0`

## Kết Luận Tổng Quát

### Nên dùng Canny
- Ảnh có độ tương phản tốt
- Cần phát hiện biên rõ ràng
- Làm đầu vào cho contour/shape detection
- Ứng dụng phân đoạn ảnh

### Không nên dùng Canny (cần tiền xử lý)
- Ảnh quá nhiễu → cần Gaussian Blur trước
- Ảnh tương phản quá thấp → cần Histogram Equalization trước
- Cần kết quả thời gian thực cực nhanh
- Yêu cầu độ dày cạnh cụ thể

## Thứ Tự Chạy
1. `canny_opencv.ipynb` → Phần 1 (cơ bản)
2. `parameter_analysis.ipynb` → Phần 2 (phân tích tham số)
3. `image_evaluation.ipynb` → Phần 3 (đánh giá loại ảnh)
4. `integration_experiment.ipynb` → Phần 4 (kết hợp kỹ thuật + báo cáo)
