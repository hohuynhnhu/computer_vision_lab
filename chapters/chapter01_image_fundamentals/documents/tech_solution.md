# Chương 1: Cơ Bản Về Ảnh - Giải Pháp Kỹ Thuật

## 1. Công Nghệ Sử Dụng

| Công Nghệ | Phiên Bản | Vai Trò |
|-----------|-----------|---------|
| Python | 3.11 | Ngôn ngữ lập trình chính |
| OpenCV (`cv2`) | 4.11 | Đọc/ghi ảnh, chuyển đổi không gian màu, vẽ hình |
| Pillow (`PIL`) | 12.0 | Lưu ảnh định dạng BMP |
| NumPy | 1.26 | Xử lý mảng ảnh |
| Matplotlib | - | Hiển thị ảnh và biểu đồ |

## 2. Kiến Trúc Giải Pháp

```
┌─────────────┐    ┌──────────────┐    ┌─────────────┐
│  File ảnh   │───▶│  cv2.imread  │───▶│  Mảng NumPy │
│  (.jpg)     │    │  (đọc ảnh)   │    │  (BGR/u8)   │
└─────────────┘    └──────────────┘    └──────┬──────┘
                                              │
                    ┌─────────────────────────┼─────────────────────────┐
                    ▼                         ▼                         ▼
            ┌──────────────┐          ┌──────────────┐          ┌──────────────┐
            │ chuyển đổi   │          │   cắt/resize │          │  vẽ hình     │
            │ không gian   │          │   crop/      │          │  line/rect/  │
            │ màu          │          │   resize     │          │  circle/text │
            └──────┬───────┘          └──────┬───────┘          └──────┬───────┘
                   ▼                         ▼                         ▼
            ┌──────────────┐          ┌──────────────┐          ┌──────────────┐
            │ matplotlib   │          │  matplotlib  │          │  matplotlib  │
            │ hiển thị     │          │  hiển thị    │          │  hiển thị    │
            └──────────────┘          └──────────────┘          └──────────────┘
```

## 3. Chi Tiết Triển Khai

### 3.1 Xử Lý BGR ↔ RGB

OpenCV đọc ảnh ở định dạng **BGR**, trong khi `matplotlib` hiển thị ảnh ở định dạng **RGB**. Do đó cần chuyển đổi trước khi hiển thị:

```python
img_rgb = cv2.cvtColor(img_bgr, cv2.COLOR_BGR2RGB)
```

### 3.2 Các Không Gian Màu

| Không Gian | Ý Nghĩa | Hàm Chuyển Đổi |
|------------|---------|----------------|
| RGB | 3 kênh: Đỏ, Lục, Lam (gốc) | Gốc |
| Grayscale | 1 kênh: cường độ xám (0-255) | `cv2.COLOR_BGR2GRAY` |
| HSV | 3 kênh: Hue, Saturation, Value | `cv2.COLOR_BGR2HSV` |
| LAB | 3 kênh: L* (độ sáng), a* (xanh lục-đỏ), b* (xanh dương-vàng) | `cv2.COLOR_BGR2LAB` |

### 3.3 Cắt Ảnh và Resize

- **Crop**: `img[y1:y2, x1:x2]` - cắt theo tọa độ pixel
- **Resize cố định**: `cv2.resize(img, (width, height))` - ép về kích thước cố định
- **Resize tỷ lệ**: `cv2.resize(img, (int(w*scale), int(h*scale)))` - giữ nguyên tỉ lệ khung hình

### 3.4 Vẽ Hình và Văn Bản

| Hàm | Mục Đích | Tham Số Chính |
|-----|----------|---------------|
| `cv2.line()` | Vẽ đường thẳng | `pt1, pt2, color, thickness` |
| `cv2.rectangle()` | Vẽ hình chữ nhật | `pt1, pt2, color, thickness` |
| `cv2.circle()` | Vẽ hình tròn | `center, radius, color, thickness` |
| `cv2.putText()` | Thêm văn bản | `text, org, font, scale, color, thickness` |

**Lưu ý về màu sắc trong OpenCV**: Màu định dạng `(B, G, R)`, không phải `(R, G, B)`.
