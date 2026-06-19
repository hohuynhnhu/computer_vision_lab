# Chương 2: Lọc Ảnh - Yêu Cầu Người Dùng

## 1. Mục Tiêu

Sinh viên cần nắm vững các kỹ thuật lọc ảnh, từ biến đổi điểm ảnh đơn giản đến các bộ lọc tuyến tính và bộ lọc nâng cao, sử dụng các kernel tích chập và thư viện OpenCV.

## 2. Yêu Cầu Chi Tiết

### Part I: Biến Đổi Điểm Ảnh (Point Operations)

| STT | Yêu Cầu | Mô Tả |
|-----|---------|-------|
| 1 | Thay đổi độ sáng | Tăng/giảm độ sáng ảnh bằng cách cộng/trừ hằng số vào từng pixel |
| 2 | Thay đổi độ tương phản | Tăng/giảm tương phản bằng cách nhân từng pixel với hệ số |
| 3 | Biến đổi âm bản | Đảo ngược màu sắc ảnh (negative transform) |
| 4 | Cắt ngưỡng nhị phân | Chuyển ảnh xám thành ảnh nhị phân dựa trên ngưỡng cố định |

### Part II: Lọc Tuyến Tính (Linear Filters)

| STT | Yêu Cầu | Mô Tả |
|-----|---------|-------|
| 5 | Lọc trung vị (Median) | Giảm nhiễu muối tiêu bằng cách thay pixel bằng giá trị trung vị của cửa sổ lân cận |
| 6 | Lọc Gaussian | Làm mờ ảnh bằng kernel phân phối chuẩn |
| 7 | Lọc làm sắc nét (Sharpen) | Tăng cường chi tiết và biên ảnh bằng kernel tăng cường |

### Part III: Bộ Lọc Nâng Cao (Advanced Filters)

| STT | Yêu Cầu | Mô Tả |
|-----|---------|-------|
| 8 | Phát hiện cạnh Sobel | Dùng kernel đạo hàm Sobel X và Y để tính gradient magnitude |
| 9 | Kernel tùy chỉnh (Emboss) | Thiết kế kernel riêng tạo hiệu ứng ảnh nổi |
| 10 | So sánh bộ lọc | Hiển thị 4 ảnh (Original, Mean, Gaussian, Sharpen) trên cùng grid |
| 11 | Lọc song phương (Bilateral) | Lọc phi tuyến giảm nhiễu nhưng giữ cạnh |

## 3. Đầu Vào

- `anh.jpg`, `anh1.jpg`, `anh2.jpg`, `anh3.jpg` trong thư mục `data/input/`

## 4. Đầu Ra

```
outputs/
├── point_operations/
│   ├── brightness_dark.jpg
│   ├── brightness_bright.jpg
│   ├── contrast_reduced.jpg
│   ├── contrast_increased.jpg
│   ├── negative.jpg
│   └── binary_threshold.jpg
├── linear_filters/
│   ├── median_filtered.jpg
│   ├── gaussian_filtered.jpg
│   └── sharpened.jpg
└── advanced_filters/
    ├── sobel_edges.jpg
    ├── emboss.jpg
    ├── filter_comparison.jpg
    ├── median_nonlinear.jpg
    └── bilateral_filtered.jpg
```

## 5. Ràng Buộc

- Sử dụng OpenCV và NumPy
- Kernel size của median và gaussian phải là số lẻ
- Giá trị pixel phải nằm trong khoảng [0, 255] (dùng `np.clip` hoặc `np.uint8`)
