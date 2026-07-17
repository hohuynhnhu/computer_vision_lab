# Chương 2: Lọc Ảnh - Kiểm Thử

## 1. Ma Trận Kiểm Thử

### Part I: Biến Đổi Điểm Ảnh

| Test Case | Dữ Liệu Vào | Kết Quả Mong Đợi | Trạng Thái |
|-----------|-------------|------------------|------------|
| Độ sáng -50 | `anh.jpg` | Ảnh tối hơn rõ rệt | ✓ Pass |
| Độ sáng +50 | `anh.jpg` | Ảnh sáng hơn rõ rệt | ✓ Pass |
| Tương phản ×0.5 | `anh.jpg` | Ảnh mờ, ít tương phản | ✓ Pass |
| Tương phản ×1.5 | `anh.jpg` | Ảnh tương phản cao, sắc nét hơn | ✓ Pass |
| Âm bản | `anh.jpg` | Màu sắc đảo ngược hoàn toàn | ✓ Pass |
| Cắt ngưỡng (T=128) | `anh.jpg` → grayscale | Ảnh nhị phân rõ ràng | ✓ Pass |

### Part II: Lọc Tuyến Tính

| Test Case | Dữ Liệu Vào | Tham Số | Kết Quả Mong Đợi | Trạng Thái |
|-----------|-------------|---------|------------------|------------|
| Mean filter | `anh.jpg` | k=3 | Ảnh mịn hơn, giảm nhiễu | ✓ Pass |
| Gaussian filter | `anh.jpg` | k=5, σ=0 | Ảnh mờ đều, mượt | ✓ Pass |
| Sharpen filter | `anh.jpg` | kernel 3×3 | Chi tiết nổi bật hơn | ✓ Pass |
| Mean ksize chẵn | - | k=4 | `ValueError` | ✓ Pass |

### Part III: Bộ Lọc Nâng Cao

| Test Case | Dữ Liệu Vào | Kết Quả Mong Đợi | Trạng Thái |
|-----------|-------------|------------------|------------|
| Sobel edge detect | `anh.jpg` | Cạnh được làm nổi bật, ảnh grayscale | ✓ Pass |
| Emboss filter | `anh.jpg` | Hiệu ứng nổi 3D | ✓ Pass |
| So sánh lọc | `anh.jpg` | Grid 4 ảnh rõ rệt | ✓ Pass |
| Bilateral filter | `anh.jpg` | Giảm nhiễu + giữ cạnh | ✓ Pass |

## 2. Kết Quả Đầu Ra

```
outputs/
├── point_operations/
│   ├── brightness_dark.jpg        (~210 KB)
│   ├── brightness_bright.jpg      (~230 KB)
│   ├── contrast_reduced.jpg       (~150 KB)
│   ├── contrast_increased.jpg     (~280 KB)
│   ├── negative.jpg               (~200 KB)
│   └── binary_threshold.jpg       (~30 KB)
├── linear_filters/
│   ├── mean_filtered.jpg            (~210 KB)
│   ├── gaussian_filtered.jpg      (~174 KB)
│   └── sharpened.jpg              (~358 KB)
└── advanced_filters/
    ├── sobel_edges.jpg            (~430 KB)
    ├── emboss.jpg                 (~485 KB)
    ├── filter_comparison.jpg      (~211 KB)
    ├── median_nonlinear.jpg       (~172 KB)
    └── bilateral_filtered.jpg     (~166 KB)
```

## 3. Các Lỗi Thường Gặp

| Lỗi | Nguyên Nhân | Cách Khắc Phục |
|-----|-------------|----------------|
| Ảnh quá sáng/tối sau biến đổi | Quên `np.clip` | Luôn dùng `np.clip(..., 0, 255)` |
| Tràn số (overflow) | Dùng `np.uint8` trực tiếp với phép cộng/nhân | Chuyển sang `np.float32` trước |
| `ValueError: Kernel size must be an odd integer` | Kernel chẵn cho mean filter | Dùng kernel size lẻ (3, 5, 7,...) |
| Cạnh sau Sobel không rõ | Không có `np.clip` hoặc sai kernel | Kiểm tra kernel và thêm `np.clip` |
