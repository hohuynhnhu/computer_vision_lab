# Chương 3: Phát Hiện Cạnh - Tính Năng

## Phần 1: Canny Bằng Thư Viện

| STT | Tính Năng | Đầu Vào | Đầu Ra | Thư Viện |
|-----|-----------|---------|--------|----------|
| 1 | Đọc và chuẩn bị ảnh | `demo.jpg` | Ảnh xám 512×512 | OpenCV |
| 2 | Canny OpenCV | Ảnh xám | Ảnh cạnh nhị phân | `cv2.Canny(gray, 100, 200)` |
| 3 | Canny Scikit-image | Ảnh xám chuẩn hóa [0,1] | Ảnh cạnh Boolean | `skimage.feature.canny(gray, sigma=2)` |
| 4 | Đo hiệu năng | Kết quả 2 thư viện | Số điểm cạnh + thời gian (ms) | `time` module |
| 5 | Hiển thị so sánh | 3 ảnh | Grid: Gray, OpenCV Canny, Skimage Canny | Matplotlib |

## Phần 2: Phân Tích Tham Số

| STT | Tính Năng | Tham Số Khảo Sát | Mục Đích |
|-----|-----------|-----------------|----------|
| 6 | Thay đổi ngưỡng thấp/cao | (50,150), (100,200), (150,250) | Quan sát ảnh hưởng của ngưỡng đến số cạnh |
| 7 | Thay đổi sigma | sigma = 1, 2, 3 | Quan sát ảnh hưởng của làm mịn đến cạnh |
| 8 | So sánh mặc định | OpenCV(100,200) vs Skimage(sigma=2) | So sánh kết quả mặc định giữa 2 thư viện |

## Phần 3: Đánh Giá Trên Nhiều Loại Ảnh

| STT | Loại Ảnh | Phương Pháp So Sánh | Tiêu Chí Đánh Giá |
|-----|----------|--------------------|-------------------|
| 9 | Nhiều nhiễu | Canny trực tiếp vs Gaussian Blur + Canny | Số lượng biên giả, mức giảm nhiễu |
| 10 | Tương phản thấp | Canny trực tiếp vs Histogram Equalization + Canny | Khả năng phát hiện biên, độ rõ cạnh |
| 11 | Nhiều chi tiết | Canny với nhiều bộ tham số | Mức giữ chi tiết, hiện tượng quá nhiều cạnh |

## Phần 4: Kết Hợp Kỹ Thuật

| STT | Tính Năng | Công Cụ | Đầu Ra |
|-----|-----------|---------|--------|
| 12 | Phân đoạn ảnh | `cv2.findContours()` / watershed | Ảnh gốc + Edge map + Phân đoạn |
| 13 | Nhận dạng hình dạng | `cv2.findContours()` + `cv2.approxPolyDP()` | Contour + Nhãn hình dạng |
| 14 | Phân loại hình | 3 đỉnh→Tam giác, 4 đỉnh→Tứ giác, >4→Đa giác, Circularity→Tròn | Hình dạng được nhận diện |

## Tổng Hợp So Sánh

| Tiêu Chí | OpenCV `cv2.Canny` | Scikit-image `feature.canny` |
|----------|---------------------|------------------------------|
| Tham số chính | low_threshold, high_threshold | sigma |
| Cách kiểm soát | Trực tiếp qua ngưỡng | Qua mức làm mịn |
| Tốc độ | Nhanh hơn (code C/C++) | Chậm hơn (code Python) |
| Tính tùy biến | Ít (chỉ ngưỡng) | Nhiều (sigma + ngưỡng tự động) |
