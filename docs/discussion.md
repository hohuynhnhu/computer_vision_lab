# Thảo Luận

## 1. Tại Sao Chọn OpenCV?

### Ưu Điểm
- **Hiệu năng cao**: Viết bằng C/C++, tối ưu cho xử lý thời gian thực
- **Đầy đủ tính năng**: Từ xử lý ảnh cơ bản đến Deep Learning (DNN module)
- **Cộng đồng lớn**: Tài liệu phong phú, nhiều tutorial
- **Đa nền tảng**: Windows, Linux, macOS, Android, iOS

### Nhược Điểm
- **API hơi phức tạp**: Một số hàm yêu cầu nhiều tham số
- **Định dạng BGR**: Không giống các thư viện khác (RGB), dễ gây nhầm lẫn
- **Ít hỗ trợ xử lý ảnh khoa học**: Không tốt bằng scikit-image cho các tác vụ học thuật

## 2. So Sánh OpenCV vs Scikit-image (Chương 3)

| Tiêu Chí | OpenCV | Scikit-image |
|----------|--------|--------------|
| Mục đích chính | Ứng dụng thực tế, real-time | Nghiên cứu, giáo dục |
| Ngôn ngữ lõi | C/C++ | Python (Cython) |
| Tốc độ | Nhanh | Chậm hơn |
| API phong cách | Hướng đối tượng (C++) | Hàm thuần Python |
| Tài liệu | Rất nhiều | Ít hơn |
| Canny | Kiểm soát qua ngưỡng | Kiểm soát qua sigma |

**Kết luận**: Dùng OpenCV cho ứng dụng thực tế, scikit-image để học nguyên lý và thử nghiệm.

## 3. Lựa Chọn Tham Số Cho Canny

### Công Thức Kinh Nghiệm

- **Tỷ lệ ngưỡng**: $T_{high} = 2 \times T_{low}$ hoặc $T_{high} = 3 \times T_{low}$
- **Giá trị khởi đầu tốt**: (100, 200)
- **Ảnh ít nhiễu**: Giảm cả 2 ngưỡng
- **Ảnh nhiều nhiễu**: Tăng cả 2 ngưỡng + Gaussian blur trước

### Tiền Xử Lý Trước Canny

| Tình Huống | Giải Pháp |
|------------|-----------|
| Ảnh nhiễu cao | Gaussian Blur (kernel 5×5) |
| Ảnh tương phản thấp | Histogram Equalization |
| Ảnh quá chi tiết | Tăng sigma hoặc ngưỡng cao |

## 4. Khi Nào Dùng Lọc Nào?

| Mục Đích | Bộ Lọc Khuyên Dùng |
|----------|-------------------|
| Giảm nhiễu muối tiêu | Median Filter |
| Giảm nhiễu Gauss | Gaussian Filter |
| Giảm nhiễu + giữ cạnh | Bilateral Filter |
| Tăng cường cạnh | Sharpen Filter |
| Phát hiện cạnh nhanh | Sobel |
| Phát hiện cạnh chất lượng cao | Canny |
| Làm mờ đơn giản | Mean Filter (Box Blur) |

## 5. Hướng Phát Triển Tương Lai

### 5.1 Mở Rộng Chương Học
- Thêm phần về xử lý video
- Thêm phần về ảnh 3D / depth map
- Thêm các bài toán thực tế (nhận diện biển số, khuôn mặt)

### 5.2 Cải Thiện Hạ Tầng
- Tự động chấm điểm bằng so sánh output image
- CI/CD pipeline kiểm tra notebook tự động
- Web interface cho sinh viên nộp bài

### 5.3 Deep Learning
- Tích hợp PyTorch/TensorFlow
- Bài tập về CNN, Object Detection
- Transfer learning với pre-trained models
