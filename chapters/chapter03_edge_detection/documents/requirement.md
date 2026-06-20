# Chương 3: Phát Hiện Cạnh - Yêu Cầu Người Dùng

## 1. Mục Tiêu

Sinh viên cần hiểu và thực hành thuật toán phát hiện cạnh Canny, so sánh kết quả giữa OpenCV và Scikit-image, phân tích ảnh hưởng của tham số, đánh giá trên nhiều loại ảnh, và kết hợp Canny với các kỹ thuật xử lý ảnh nâng cao.

## 2. Yêu Cầu Chi Tiết

### Phần 1: Thực Hiện Canny Bằng Thư Viện

| STT | Yêu Cầu | Mô Tả |
|-----|---------|-------|
| 1 | Canny với OpenCV | Dùng `cv2.Canny` với ngưỡng (100, 200) — kết quả ảnh nhị phân uint8 |
| 2 | Canny với Scikit-image | Dùng `skimage.feature.canny` với `sigma=2` — kết quả mảng Boolean |
| 3 | So sánh kết quả | So sánh số điểm cạnh, thời gian xử lý (ms), chất lượng cạnh trực quan |

### Phần 2: Phân Tích Tham Số

| STT | Yêu Cầu | Mô Tả |
|-----|---------|-------|
| 4 | Thay đổi ngưỡng OpenCV | Thử 3 cặp ngưỡng: (50,150), (100,200), (150,250) |
| 5 | Thay đổi sigma Scikit-image | Thử sigma = 1, 2, 3 |
| 6 | So sánh với mặc định | So sánh kết quả OpenCV(100,200) vs Skimage(sigma=2) |

### Phần 3: Đánh Giá Trên Nhiều Loại Ảnh

| STT | Yêu Cầu | Mô Tả |
|-----|---------|-------|
| 7 | Ảnh nhiều nhiễu | So sánh Canny trực tiếp vs Gaussian Blur + Canny |
| 8 | Ảnh tương phản thấp | So sánh Canny trực tiếp vs Histogram Equalization + Canny |
| 9 | Ảnh nhiều chi tiết | Áp dụng Canny với nhiều bộ tham số, quan sát hiện tượng quá nhiều cạnh |

### Phần 4: Kết Hợp Với Kỹ Thuật Khác

| STT | Yêu Cầu | Mô Tả |
|-----|---------|-------|
| 10 | Canny + Phân đoạn ảnh | Tìm contour (`cv2.findContours`) hoặc watershed để tách vùng |
| 11 | Canny + Nhận dạng hình dạng | Tìm contour, xấp xỉ đa giác (`cv2.approxPolyDP`), phân loại hình dạng |
| 12 | Báo cáo tổng kết | Kết luận về các trường hợp nên/không nên dùng Canny |

## 3. Đầu Vào

- `demo.jpg` trong thư mục `data/input/`

## 4. Đầu Ra

```
outputs/
├── 01_gray.png
├── 02_canny_opencv_default.png
├── 03_canny_skimage_default.png
├── 04_compare_opencv_skimage.png
├── 05_compare_threshold_opencv.png
├── 06_compare_sigma_skimage.png
└── 07_compare_default_value.png
```

## 5. Ràng Buộc

- Sử dụng OpenCV và Scikit-image
- Resize tất cả ảnh về `512 × 512` để so sánh đồng nhất
- Đo thời gian xử lý (ms) cho từng phương pháp: `time.time()` trước và sau
- Đếm số điểm cạnh: `np.sum(edges > 0)`
- Ảnh đầu vào cho Scikit-image phải chuẩn hóa về [0, 1] float: `gray.astype(np.float64) / 255.0`

## 6. Lỗi Thường Gặp

| Lỗi | Nguyên Nhân | Cách Khắc Phục |
|-----|-------------|----------------|
| `error: (-215) !_src.empty()` | Không tìm thấy ảnh đầu vào | Kiểm tra đường dẫn `../data/input/demo.jpg` |
| Skimage Canny không chạy | Ảnh chưa chuẩn hóa về [0,1] | Dùng `gray.astype(np.float64) / 255.0` |
| Canny phát hiện quá nhiều cạnh | Ngưỡng quá thấp hoặc ảnh nhiều nhiễu | Tăng ngưỡng hoặc dùng `cv2.GaussianBlur` trước |
| Canny mất quá nhiều cạnh | Ngưỡng quá cao | Giảm ngưỡng, thử (50, 150) |
| findContours không tìm được gì | Edge map rỗng hoặc dtype sai | Đảm bảo đầu vào là `uint8`, edges > 0 |
