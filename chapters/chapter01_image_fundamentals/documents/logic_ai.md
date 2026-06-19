# Chương 1: Cơ Bản Về Ảnh - Logic & AI

## 1. Nguyên Lý Biểu Diễn Ảnh Số

### 1.1 Ảnh Dưới Dạng Mảng Số

Ảnh số được biểu diễn trong máy tính dưới dạng **mảng đa chiều (tensor)**:

- **Ảnh xám (Grayscale)**: Mảng 2 chiều `(H × W)`, mỗi pixel là giá trị cường độ từ 0 (đen) đến 255 (trắng)
- **Ảnh màu RGB**: Mảng 3 chiều `(H × W × 3)`, mỗi pixel chứa 3 giá trị (R, G, B) ∈ [0, 255]

```
Ảnh xám (H × W):           Ảnh màu RGB (H × W × 3):
┌─────────────────┐        ┌─────────────────────────────┐
│ 128  45  200    │        │ (255,0,0)  (0,255,0)  ...   │
│  67  89   13    │        │ (0,0,255)  (128,128,128) .. │
│  ...            │        │ ...                         │
└─────────────────┘        └─────────────────────────────┘
```

### 1.2 Định Dạng BGR trong OpenCV

OpenCV sử dụng định dạng **BGR** thay vì **RGB** vì lý do lịch sử. Điều này có nghĩa là:

- `img[:,:,0]` → kênh Blue (xanh dương)
- `img[:,:,1]` → kênh Green (xanh lục)
- `img[:,:,2]` → kênh Red (đỏ)

Khi dùng `matplotlib` để hiển thị, cần chuyển đổi BGR→RGB.

## 2. Chuyển Đổi Không Gian Màu

### 2.1 Grayscale (Ảnh Xám)

Công thức chuyển đổi RGB → Grayscale của OpenCV:

$$Y = 0.299 \cdot R + 0.587 \cdot G + 0.114 \cdot B$$

Trọng số cao nhất cho kênh Green vì mắt người nhạy cảm nhất với ánh sáng xanh lục.

### 2.2 HSV (Hue - Saturation - Value)

- **H (Hue/Sắc độ)**: Góc trên bánh xe màu (0°-360°), biểu thị loại màu
- **S (Saturation/Độ bão hòa)**: Độ tinh khiết của màu (0 = xám, 255 = thuần)
- **V (Value/Độ sáng)**: Cường độ sáng (0 = đen, 255 = sáng nhất)

HSV tách biệt thông tin màu sắc và độ sáng, hữu ích cho các tác vụ như phát hiện đối tượng dựa trên màu.

### 2.3 LAB (CIELAB)

- **L*** (0-100): Độ sáng (Lightness)
- **a*** (-128 đến 127): Trục xanh lục ↔ đỏ
- **b*** (-128 đến 127): Trục xanh dương ↔ vàng

LAB được thiết kế để gần với cảm nhận thị giác của con người.

## 3. Nội Suy Khi Resize

Khi resize ảnh, OpenCV sử dụng nội suy (interpolation) để tính giá trị pixel mới:

| Phương Pháp | Mô Tả | Tốc Độ | Chất Lượng |
|-------------|-------|--------|------------|
| INTER_NEAREST | Láng giềng gần nhất | Nhanh nhất | Thấp |
| INTER_LINEAR | Nội suy song tuyến (mặc định) | Nhanh | Trung bình |
| INTER_CUBIC | Nội suy Bicubic | Chậm | Cao |
| INTER_AREA | Lấy mẫu lại vùng | - | Tốt cho thu nhỏ |

## 4. Tọa Độ Trong OpenCV

Hệ tọa độ ảnh trong OpenCV:
- **Gốc tọa độ (0, 0)**: góc trên bên trái
- **Trục x**: tăng dần sang phải (cột)
- **Trục y**: tăng dần xuống dưới (hàng)
- **Định dạng**: `img[y, x]` hoặc `img[y1:y2, x1:x2]`
