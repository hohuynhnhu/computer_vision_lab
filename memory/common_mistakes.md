# Lỗi Thường Gặp

## Chương 1: Cơ Bản Về Ảnh

### 1.1 Quên Chuyển BGR→RGB
**Triệu chứng**: Ảnh hiển thị sai màu (trời xanh thành đỏ, da đỏ thành xanh).

**Nguyên nhân**: `cv2.imread` trả về BGR, `plt.imshow` hiển thị RGB.

**Sửa**:
```python
img_rgb = cv2.cvtColor(img_bgr, cv2.COLOR_BGR2RGB)
plt.imshow(img_rgb)
```

### 1.2 Nhầm Thứ Tự Tọa Độ Crop
**Triệu chứng**: Cắt sai vùng ảnh.

**Nguyên nhân**: `img[y1:y2, x1:x2]` - y là hàng (trên→dưới), x là cột (trái→phải).

**Sửa**: Nhớ thứ tự `[hàng_bắt_đầu:hàng_kết_thúc, cột_bắt_đầu:cột_kết_thúc]`

### 1.3 Quên cmap='gray'
**Triệu chứng**: Ảnh xám hiển thị với màu giả (xanh, đỏ).

**Sửa**:
```python
plt.imshow(gray_img, cmap='gray')
```

## Chương 2: Lọc Ảnh

### 2.1 Tràn Số Với uint8
**Triệu chứng**: Ảnh output bị sọc, vỡ, hoặc sai giá trị.

**Nguyên nhân**: `np.uint8` chỉ chứa giá trị 0-255. Khi cộng 50 vào pixel=220 → 270 → tràn thành 14.

**Sửa**:
```python
# Chuyển sang float32 → tính toán → clip → chuyển về uint8
result = np.clip(img.astype(np.float32) + 50, 0, 255).astype(np.uint8)
```

### 2.2 Kernel Size Chẵn
**Triệu chứng**: `cv2.error` khi dùng median hoặc Gaussian.

**Nguyên nhân**: Median và Gaussian yêu cầu kernel size là số lẻ.

**Sửa**: Luôn dùng 3, 5, 7, 9,...

### 2.3 GaussianBlur Nhận Tuple Lẻ
**Triệu chứng**: Lỗi Assertion failed.

**Nguyên nhân**: `cv2.GaussianBlur(img, (4, 4), 0)` - kernel chẵn.

**Sửa**:
```python
cv2.GaussianBlur(img, (5, 5), 0)  # ✓
```

## Chương 3: Canny

### 3.1 Quên Chuẩn Hóa Cho Skimage
**Triệu chứng**: Kết quả Canny Skimage không chính xác hoặc lỗi.

**Nguyên nhân**: `skimage.feature.canny` yêu cầu ảnh float trong khoảng [0, 1].

**Sửa**:
```python
gray_f = img_gray.astype(np.float64) / 255.0
edges = canny(gray_f, sigma=2)
```

### 3.2 Ngưỡng Canny Không Phù Hợp
**Triệu chứng**: Quá nhiều cạnh giả hoặc mất hết cạnh.

**Nguyên nhân**: Chọn low_threshold quá thấp (nhiều cạnh giả) hoặc quá cao (mất cạnh).

**Sửa**: 
- Khởi đầu với (100, 200)
- Điều chỉnh tăng nếu quá nhiều cạnh, giảm nếu quá ít
- Tỷ lệ khuyến nghị: high ≈ 2× low hoặc high ≈ 3× low

### 3.3 Không Dùng np.clip Cho Sobel
**Triệu chứng**: Ảnh Sobel bị tràn giá trị.

**Nguyên nhân**: Gradient magnitude có thể > 255.

**Sửa**:
```python
mag = np.sqrt(gx**2 + gy**2)
result = np.clip(mag, 0, 255).astype(np.uint8)
```

## Lỗi Chung

### Import Thư Viện
| Lỗi | Sửa |
|-----|-----|
| `ModuleNotFoundError: No module named 'cv2'` | `pip install opencv-python` |
| `ModuleNotFoundError: No module named 'skimage'` | `pip install scikit-image` |
| `No module named 'PIL'` | `pip install Pillow` |

### Đường Dẫn File
| Lỗi | Sửa |
|-----|-----|
| File ảnh không tồn tại | Kiểm tra `os.path.exists(path)` |
| Nhầm đường dẫn relative | Xác định working directory hiện tại |
| Viết sai tên file | Kiểm tra chính tả, phân biệt hoa/thường |

### Kiểu Dữ Liệu
| Lỗi | Sửa |
|-----|-----|
| `uint8` overflow | Dùng `float32` + `np.clip` |
| `int` với phép chia | Dùng `float32` hoặc `astype(float)` |
| Boolean vs uint8 | `edges.astype(np.uint8) * 255` |
