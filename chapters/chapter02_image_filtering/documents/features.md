# Chương 2: Lọc Ảnh - Tính Năng

## Part I: Biến Đổi Điểm Ảnh

| STT | Tính Năng | Đầu Vào | Đầu Ra | Công Thức |
|-----|-----------|---------|--------|-----------|
| 1 | Thay đổi độ sáng (tối) | Ảnh RGB | Ảnh tối hơn | `pixel - 50` |
| 2 | Thay đổi độ sáng (sáng) | Ảnh RGB | Ảnh sáng hơn | `pixel + 50` |
| 3 | Thay đổi tương phản (giảm) | Ảnh RGB | Ảnh ít tương phản | `pixel * 0.5` |
| 4 | Thay đổi tương phản (tăng) | Ảnh RGB | Ảnh tương phản cao | `pixel * 1.5` |
| 5 | Biến đổi âm bản | Ảnh RGB | Ảnh âm bản | `255 - pixel` |
| 6 | Cắt ngưỡng nhị phân | Ảnh Grayscale | Ảnh nhị phân | `255 nếu pixel > 128, 0 nếu không` |

## Part II: Lọc Tuyến Tính

| STT | Tính Năng | Đầu Vào | Đầu Ra | Hàm OpenCV |
|-----|-----------|---------|--------|------------|
| 7 | Lọc trung vị (Median) | Ảnh RGB | Ảnh sau lọc trung vị | `cv2.medianBlur()` |
| 8 | Lọc Gaussian | Ảnh RGB | Ảnh làm mờ mượt | `cv2.GaussianBlur()` |
| 9 | Lọc làm sắc nét (Sharpen) | Ảnh RGB | Ảnh sắc nét hơn | `cv2.filter2D()` + kernel tùy chỉnh |

## Part III: Bộ Lọc Nâng Cao

| STT | Tính Năng | Đầu Vào | Đầu Ra | Phương Pháp |
|-----|-----------|---------|--------|-------------|
| 10 | Phát hiện cạnh Sobel | Ảnh RGB | Ảnh biên (grayscale) | Kernel Sobel X/Y + Gradient magnitude |
| 11 | Kernel Emboss | Ảnh RGB | Ảnh hiệu ứng nổi | Kernel trọng số chéo 3×3 tùy chỉnh |
| 12 | So sánh bộ lọc | Ảnh RGB | Grid 4 ảnh so sánh | Original + Mean + Gaussian + Sharpen |
| 13 | Lọc song phương (Bilateral) | Ảnh RGB | Ảnh giảm nhiễu, giữ cạnh | `cv2.bilateralFilter()` |

## Chi Tiết Kernel

### Kernel Sharpen (3×3)
```
[[ 0, -1,  0],
 [-1,  6, -1],
 [ 0, -1,  0]]
```

### Kernel Emboss (3×3)
```
[[-2, -1, 0],
 [-1,  1, 1],
 [ 0,  1, 2]]
```

### Kernel Sobel X
```
[[-1, 0, 1],
 [-2, 0, 2],
 [-1, 0, 1]]
```

### Kernel Sobel Y
```
[[-1, -2, -1],
 [ 0,  0,  0],
 [ 1,  2,  1]]
```
