# Từ Điển Thuật Ngữ - Computer Vision

## A

### Ảnh nhị phân (Binary Image)
Ảnh chỉ chứa 2 giá trị pixel: 0 (đen) và 255 (trắng). Tạo ra từ phép cắt ngưỡng (thresholding).

### Ảnh xám (Grayscale Image)
Ảnh 1 kênh, mỗi pixel biểu thị cường độ sáng từ 0 (đen) đến 255 (trắng).

### Âm bản (Negative)
Phép biến đổi đảo ngược giá trị pixel: `new = 255 - old`. Tạo hiệu ứng như phim âm bản.

## B

### BGR
Định dạng màu mặc định của OpenCV: Blue-Green-Red. Cần chuyển sang RGB trước khi hiển thị với matplotlib.

### Biến đổi điểm ảnh (Point Operation)
Phép toán áp dụng lên từng pixel độc lập, không phụ thuộc pixel lân cận. VD: độ sáng, tương phản.

### Bilateral Filter (Lọc song phương)
Bộ lọc phi tuyến giảm nhiễu nhưng giữ cạnh. Kết hợp trọng số không gian và trọng số cường độ.

## C

### Canny Edge Detection
Thuật toán phát hiện cạnh 5 bước: Gaussian → Gradient → Magnitude → NMS → Hysteresis. John Canny, 1986.

### Circularity (Độ tròn)
Công thức: $C = \frac{4\pi \cdot A}{P^2}$. C ≈ 1: hình tròn. C < 1: hình khác.

### Contour (Đường viền)
Đường cong nối các điểm liên tục có cùng màu/cường độ. Dùng `cv2.findContours()`.

### Convolution (Tích chập)
Phép toán cơ bản của lọc tuyến tính: $g(x,y) = \sum\sum f(x+i,y+j) \cdot h(i,j)$.

### Crop (Cắt ảnh)
Trích xuất một vùng ảnh: `img[y1:y2, x1:x2]`.

## E

### Edge (Cạnh/Biên)
Vùng trong ảnh có sự thay đổi đột ngột về cường độ sáng, thường là ranh giới giữa các đối tượng.

### Emboss
Hiệu ứng làm nổi ảnh bằng kernel có trọng số chéo, tạo cảm giác 3D.

### EqualizeHist (Cân bằng Histogram)
Kỹ thuật tăng độ tương phản bằng cách phân phối lại cường độ pixel.

## G

### Gaussian Blur (Làm mờ Gaussian)
Bộ lọc làm mờ dùng kernel phân phối chuẩn: $G(x,y) = \frac{1}{2\pi\sigma^2} e^{-\frac{x^2+y^2}{2\sigma^2}}$.

### Gradient
Đạo hàm của ảnh theo hướng X và Y, dùng để phát hiện cạnh.

## H

### Histogram Equalization
→ EqualizeHist.

### HSV (Hue-Saturation-Value)
Không gian màu tách biệt màu sắc (Hue), độ bão hòa (Saturation), và độ sáng (Value).

### Hysteresis Thresholding (Ngưỡng trễ)
Bước cuối của Canny: dùng 2 ngưỡng để quyết định pixel có phải cạnh không.

## K

### Kernel (Mặt nạ lọc)
Ma trận vuông (3×3, 5×5) chứa trọng số, dùng trong phép tích chập để lọc ảnh.

## L

### LAB (CIELAB)
Không gian màu: L (độ sáng), a (xanh lục-đỏ), b (xanh dương-vàng). Gần với cảm nhận thị giác con người.

### Lọc tuyến tính (Linear Filter)
Bộ lọc dùng phép tích chập với kernel cố định. VD: Gaussian, Mean, Sharpen.

### Lọc phi tuyến (Non-linear Filter)
Bộ lọc không dùng công thức tổng có trọng số. VD: Median, Bilateral.

## M

### Median Filter (Lọc trung vị)
Thay pixel tâm bằng trung vị của cửa sổ lân cận. Hiệu quả với nhiễu muối tiêu.

## N

### Non-Maximum Suppression (NMS)
Bước 4 của Canny: giữ pixel có gradient lớn nhất theo hướng, loại bỏ các pixel khác → cạnh mỏng 1 pixel.

### Nhiễu muối tiêu (Salt-and-Pepper Noise)
Nhiễu dạng điểm trắng/đen ngẫu nhiên. Xử lý tốt bằng Median Filter.

## O

### OpenCV
Thư viện mã nguồn mở cho Computer Vision (Open Source Computer Vision Library). Hỗ trợ Python, C++, Java.

## P

### Pixel (Điểm ảnh)
Đơn vị nhỏ nhất của ảnh số. Với ảnh 8-bit, giá trị pixel ∈ [0, 255].

### Pillow (PIL)
Thư viện xử lý ảnh cho Python, hỗ trợ đọc/ghi nhiều định dạng.

## R

### Resize
Thay đổi kích thước ảnh, dùng nội suy để tính giá trị pixel mới.

### RGB (Red-Green-Blue)
Định dạng màu phổ biến: 3 kênh Đỏ-Xanh lục-Xanh dương. Dùng cho hiển thị (matplotlib).

## S

### Scikit-image
Thư viện xử lý ảnh cho Python, tập trung vào nghiên cứu và giáo dục.

### Sharpen Filter (Lọc làm sắc nét)
Kernel tăng cường sự khác biệt pixel tâm và lân cận → làm nổi bật chi tiết.

### Sigma (σ)
Độ lệch chuẩn của phân phối Gaussian. σ càng lớn → mức làm mờ càng cao.

### Sobel Operator
Kernel 3×3 tính đạo hàm ảnh theo hướng X và Y để phát hiện cạnh.

## T

### Thresholding (Cắt ngưỡng)
Chuyển ảnh xám thành ảnh nhị phân dựa trên ngưỡng T: pixel > T → 255, pixel ≤ T → 0.

### Tích chập
→ Convolution.

## Số

### 3-Kênh (3-Channel)
Ảnh màu với 3 kênh: R, G, B (hoặc B, G, R trong OpenCV). Mảng 3D (H × W × 3).
