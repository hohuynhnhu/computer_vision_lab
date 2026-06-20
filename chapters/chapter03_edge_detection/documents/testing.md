# Chương 3: Phát Hiện Cạnh - Kiểm Thử

## 1. Ma Trận Kiểm Thử

### Phần 1: Canny Bằng Thư Viện

| Test Case | Dữ Liệu Vào | Kết Quả Mong Đợi | Trạng Thái |
|-----------|-------------|------------------|------------|
| Đọc ảnh `demo.jpg` | File hợp lệ | Ảnh đọc được, resize 512×512 | ✓ Pass |
| Chuyển sang grayscale | Ảnh màu 512×512 | Ảnh xám 512×512 | ✓ Pass |
| Canny OpenCV (100, 200) | Ảnh xám uint8 | Ảnh cạnh nhị phân, `n_cv > 0` | ✓ Pass |
| Canny Skimage (sigma=2) | Ảnh float [0,1] | Mảng Boolean, `n_sk > 0` | ✓ Pass |
| Đếm điểm cạnh | Ảnh cạnh | Số nguyên > 0 | ✓ Pass |
| Đo thời gian | — | Giá trị ms > 0 | ✓ Pass |
| Hiển thị so sánh 3 ảnh | Kết quả | Grid 1×3 rõ ràng | ✓ Pass |

### Phần 2: Phân Tích Tham Số

| Test Case | Tham Số | Kết Quả Mong Đợi |
|-----------|---------|------------------|
| Ngưỡng thấp (50, 150) | Low=50, High=150 | Nhiều cạnh nhất, có cạnh giả |
| Ngưỡng trung bình (100, 200) | Low=100, High=200 | Cạnh cân bằng (mặc định) |
| Ngưỡng cao (150, 250) | Low=150, High=250 | Ít cạnh nhất, chỉ cạnh mạnh |
| Sigma thấp (1) | sigma=1 | Nhiều chi tiết, dễ có nhiễu |
| Sigma trung bình (2) | sigma=2 | Cân bằng giữa chi tiết và nhiễu |
| Sigma cao (3) | sigma=3 | Ít cạnh, ít nhiễu, mất cạnh nhỏ |

### Phần 3: Đánh Giá Loại Ảnh

| Test Case | Phương Pháp | Tiêu Chí Đánh Giá | Kết Quả Mong Đợi |
|-----------|-------------|-------------------|-----------------|
| Ảnh nhiễu — trực tiếp | Canny trực tiếp | Số biên giả | Nhiều cạnh giả, kém sạch |
| Ảnh nhiễu — Gaussian+Canny | `GaussianBlur(5)` + Canny | Mức giảm nhiễu | Ít cạnh giả hơn rõ rệt |
| Ảnh tương phản thấp — trực tiếp | Canny trực tiếp | Số cạnh phát hiện | Rất ít cạnh |
| Ảnh tương phản thấp — EqHist | `equalizeHist` + Canny | Độ rõ cạnh | Nhiều cạnh hơn, rõ hơn |
| Ảnh nhiều chi tiết | Nhiều bộ tham số | Số cạnh vs tham số | Ngưỡng cao → ít cạnh hơn |

### Phần 4: Kết Hợp Kỹ Thuật

| Test Case | Kết Quả Mong Đợi |
|-----------|-----------------|
| `cv2.findContours` trên edge map | Danh sách contour không rỗng |
| Lọc contour nhỏ (area < 100) | Chỉ còn contour có ý nghĩa |
| `cv2.approxPolyDP` | Đa giác xấp xỉ được tính |
| Phân loại 3 đỉnh | Nhãn "Tam giac" |
| Phân loại 4 đỉnh | Nhãn "Tu giac" |
| Phát hiện hình tròn (C > 0.8) | Nhãn "Hinh tron" |
| Vẽ nhãn lên ảnh | Nhãn xuất hiện tại vị trí centroid |

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
| Mức tùy biến | Qua 2 ngưỡng | Qua sigma (ngưỡng tự động) |

### Ảnh Hưởng Tham Số

| Tham Số | Ảnh Hưởng |
|---------|-----------|
| Ngưỡng thấp | Càng thấp → càng nhiều cạnh yếu được giữ lại |
| Ngưỡng cao | Càng cao → chỉ giữ cạnh mạnh nhất |
| Sigma | Càng cao → ảnh càng mờ → ít cạnh hơn |

### Kết Quả Trên Các Loại Ảnh

| Loại Ảnh | Kết Luận |
|----------|----------|
| Nhiễu | Cần `GaussianBlur` trước Canny |
| Tương phản thấp | Cần `equalizeHist` trước Canny |
| Nhiều chi tiết | Điều chỉnh ngưỡng phù hợp với nội dung ảnh |

## 4. Các Lỗi Thường Gặp

| Lỗi | Nguyên Nhân | Cách Khắc Phục |
|-----|-------------|----------------|
| `error: (-215) !_src.empty()` | Ảnh `demo.jpg` không tìm thấy | Kiểm tra đường dẫn `../data/input/demo.jpg` |
| `ValueError` trong skimage | Ảnh không phải float [0,1] | Dùng `gray.astype(np.float64) / 255.0` |
| `findContours` không tìm được gì | Edge map rỗng hoặc sai dtype | Đảm bảo edges là `uint8`, kiểm tra ngưỡng |
| Nhãn hình dạng vẽ sai vị trí | `M["m00"] == 0` (contour điểm) | Kiểm tra `if M["m00"] != 0` trước khi tính centroid |
| Quá nhiều cạnh giả | Ngưỡng quá thấp hoặc ảnh nhiễu | Tăng ngưỡng hoặc thêm `GaussianBlur` |
