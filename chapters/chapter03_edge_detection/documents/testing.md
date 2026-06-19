# Chương 3: Phát Hiện Cạnh - Kiểm Thử

## 1. Ma Trận Kiểm Thử

### Phần 1: Canny Bằng Thư Viện

| Test Case | Dữ Liệu Vào | Kết Quả Mong Đợi | Trạng Thái |
|-----------|-------------|------------------|------------|
| Đọc ảnh `demo.jpg` | File hợp lệ | Ảnh resize 512×512 | ✓ Pass |
| Chuyển sang grayscale | Ảnh màu 512×512 | Ảnh xám 512×512 | ✓ Pass |
| Canny OpenCV (100, 200) | Ảnh xám | Ảnh cạnh nhị phân | ✓ Pass |
| Canny Skimage (sigma=2) | Ảnh xám float [0,1] | Ảnh cạnh Boolean | ✓ Pass |
| Đếm điểm cạnh | Ảnh cạnh | Số nguyên > 0 | ✓ Pass |
| Đo thời gian | - | Giá trị ms | ✓ Pass |
| Hiển thị so sánh 3 ảnh | Kết quả | Grid: Gray + OpenCV + Skimage | ✓ Pass |

### Phần 2: Phân Tích Tham Số

| Test Case | Tham Số | Kết Quả Mong Đợi |
|-----------|---------|------------------|
| Ngưỡng thấp (50, 150) | Low=50, High=150 | Nhiều cạnh, có cạnh giả |
| Ngưỡng trung bình (100, 200) | Low=100, High=200 | Cạnh cân bằng |
| Ngưỡng cao (150, 250) | Low=150, High=250 | Ít cạnh, cạnh mạnh |
| Sigma thấp (1) | sigma=1 | Nhiễu nhiều, chi tiết rõ |
| Sigma trung bình (2) | sigma=2 | Cân bằng |
| Sigma cao (3) | sigma=3 | Ít nhiễu, mất cạnh nhỏ |

### Phần 3: Đánh Giá Loại Ảnh

| Test Case | Phương Pháp | Tiêu Chí Đánh Giá | Kết Quả |
|-----------|-------------|-------------------|---------|
| Ảnh nhiễu - trực tiếp | Canny trực tiếp | Số biên giả | Nhiều cạnh giả |
| Ảnh nhiễu - Gaussian + Canny | Gaussian(5) + Canny | Mức giảm nhiễu | Ít cạnh giả hơn |
| Ảnh tương phản thấp - trực tiếp | Canny trực tiếp | Phát hiện biên | Ít cạnh |
| Ảnh tương phản thấp - EqHist | EqualizeHist + Canny | Độ rõ cạnh | Nhiều cạnh hơn |
| Ảnh nhiều chi tiết | Nhiều bộ tham số | Mức giữ chi tiết | Tùy tham số |

### Phần 4: Kết Hợp Kỹ Thuật

| Test Case | Kết Quả Mong Đợi |
|-----------|------------------|
| Canny + findContours | Contour được tìm thấy |
| Canny + approxPolyDP | Đa giác xấp xỉ |
| Phân loại tam giác (3 đỉnh) | Nhãn "Tam giác" |
| Phân loại tứ giác (4 đỉnh) | Nhãn "Tứ giác" |
| Phát hiện hình tròn (circularity > 0.8) | Nhãn "Hình tròn" |

## 2. Kết Quả Đầu Ra

```
outputs/
├── 01_gray.png                          (~120 KB)
├── 02_canny_opencv_default.png          (~80 KB)
├── 03_canny_skimage_default.png         (~80 KB)
├── 04_compare_opencv_skimage.png        (~250 KB)
├── 05_compare_threshold_opencv.png      (~300 KB)
├── 06_compare_sigma_skimage.png         (~200 KB)
└── 07_compare_default_value.png         (~250 KB)
```

## 3. Báo Cáo Tổng Kết

### So Sánh OpenCV vs Scikit-image
| Tiêu Chí | OpenCV | Scikit-image |
|----------|--------|--------------|
| Chất lượng cạnh | Tốt, cạnh sạch | Tốt, có thể khác do sigma |
| Tốc độ xử lý | Nhanh hơn | Chậm hơn |
| Mức tùy biến | Qua ngưỡng | Qua sigma |

### Ảnh Hưởng Tham Số
| Tham Số | Ảnh Hưởng |
|---------|-----------|
| Ngưỡng thấp | Càng thấp → càng nhiều cạnh yếu được giữ |
| Ngưỡng cao | Càng cao → chỉ giữ cạnh mạnh nhất |
| Sigma | Càng cao → càng mờ → ít cạnh hơn |

### Kết Quả Trên Các Loại Ảnh
| Loại Ảnh | Kết Luận |
|----------|----------|
| Nhiễu | Cần Gaussian Blur trước Canny |
| Tương phản thấp | Cần Histogram Equalization trước Canny |
| Nhiều chi tiết | Điều chỉnh tham số phù hợp với từng ảnh |

### Kết Luận Tổng Quát
- **Nên dùng Canny**: Ảnh tương phản tốt, cần phát hiện biên cho contour/shape detection
- **Không nên dùng Canny**: Ảnh quá nhiễu chưa qua tiền xử lý, ảnh tương phản quá thấp
