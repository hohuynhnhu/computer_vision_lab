# Chương 3: Phát Hiện Cạnh - Tính Năng

## Phần 1: Canny Bằng Thư Viện

| STT | Tính Năng | Đầu Vào | Đầu Ra | Thư Viện |
|-----|-----------|---------|--------|----------|
| 1 | Đọc và chuẩn bị ảnh | `demo.jpg` | Ảnh xám 512×512 | OpenCV |
| 2 | Canny OpenCV | Ảnh xám uint8 | Ảnh cạnh nhị phân uint8 (0/255) | `cv2.Canny(gray, 100, 200)` |
| 3 | Canny Scikit-image | Ảnh xám float [0,1] | Ảnh cạnh Boolean | `skimage.feature.canny(gray, sigma=2)` |
| 4 | Đo hiệu năng | Kết quả 2 thư viện | Số điểm cạnh + thời gian (ms) | `np.sum`, `time` module |
| 5 | Hiển thị so sánh | 3 ảnh | Grid 1×3: Gray / OpenCV Canny / Skimage Canny | Matplotlib |

## Phần 2: Phân Tích Tham Số

| STT | Tính Năng | Tham Số Khảo Sát | Mục Đích |
|-----|-----------|-----------------|----------|
| 6 | Thay đổi ngưỡng thấp/cao | (50,150), (100,200), (150,250) | Quan sát ảnh hưởng của ngưỡng đến số cạnh |
| 7 | Thay đổi sigma | sigma = 1, 2, 3 | Quan sát ảnh hưởng của mức làm mịn đến cạnh |
| 8 | So sánh mặc định | OpenCV(100,200) vs Skimage(sigma=2) | Thấy rõ sự khác biệt giữa 2 cách kiểm soát |

## Phần 3: Đánh Giá Trên Nhiều Loại Ảnh

| STT | Loại Ảnh | Phương Pháp So Sánh | Tiêu Chí Đánh Giá |
|-----|----------|--------------------|-------------------|
| 9 | Nhiều nhiễu | Canny trực tiếp vs `GaussianBlur(5,5)` + Canny | Số biên giả, mức giảm nhiễu |
| 10 | Tương phản thấp | Canny trực tiếp vs `equalizeHist` + Canny | Khả năng phát hiện biên, độ rõ cạnh |
| 11 | Nhiều chi tiết | Canny với nhiều bộ tham số | Mức giữ chi tiết, hiện tượng quá nhiều cạnh |

## Phần 4: Kết Hợp Kỹ Thuật

| STT | Tính Năng | Công Cụ | Đầu Ra |
|-----|-----------|---------|--------|
| 12 | Phân đoạn ảnh | `cv2.findContours()` / watershed | Ảnh gốc + Edge map + Vùng phân đoạn |
| 13 | Nhận dạng hình dạng | `cv2.findContours()` + `cv2.approxPolyDP()` | Contour + Nhãn hình dạng |
| 14 | Phân loại hình | 3 đỉnh→Tam giác, 4 đỉnh→Tứ giác, >4+C>0.8→Hình tròn, còn lại→Đa giác | Hình dạng được nhận diện và vẽ nhãn |

## Chi Tiết Tham Số Canny

### Tham Số OpenCV `cv2.Canny`

| Tham Số | Giá Trị Thử | Ý Nghĩa |
|---------|-------------|---------|
| `low_threshold` | 50, 100, 150 | Ngưỡng thấp — kết nối cạnh yếu với cạnh mạnh |
| `high_threshold` | 150, 200, 250 | Ngưỡng cao — xác định cạnh mạnh chắc chắn |

### Tham Số Scikit-image `feature.canny`

| Tham Số | Giá Trị Thử | Ý Nghĩa |
|---------|-------------|---------|
| `sigma` | 1, 2, 3 | Độ lệch chuẩn Gaussian blur — làm mịn trước khi phát hiện cạnh |

## Tổng Hợp So Sánh Hai Thư Viện

| Tiêu Chí | OpenCV `cv2.Canny` | Scikit-image `feature.canny` |
|----------|---------------------|------------------------------|
| Tham số chính | `low_threshold`, `high_threshold` | `sigma` |
| Cách kiểm soát | Trực tiếp qua ngưỡng | Qua mức làm mịn (ngưỡng tự động) |
| Làm mịn nội bộ | Gaussian σ=1.4 (cố định) | σ tùy chỉnh qua tham số `sigma` |
| Kiểu dữ liệu đầu ra | `uint8` (0 hoặc 255) | `bool` (True/False) |
| Tốc độ | Nhanh hơn (C/C++) | Chậm hơn (Python) |
| Tính tùy biến | Ít (chỉ 2 ngưỡng) | Nhiều (sigma + ngưỡng tự động từ histogram) |
