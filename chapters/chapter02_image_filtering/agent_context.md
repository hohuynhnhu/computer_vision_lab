# Chapter 02: Lọc Ảnh (Image Filtering) — Agent Context

## Tổng Quan
Chương này bao quát các kỹ thuật lọc ảnh từ cơ bản đến nâng cao: biến đổi điểm ảnh (point operations), lọc tuyến tính (linear filters) dùng kernel tích chập, và các bộ lọc nâng cao (advanced filters) như Sobel, Emboss, Bilateral.

## Cấu Trúc
- **3 Notebooks**: `point_operations.ipynb` (Part I), `linear_filters.ipynb` (Part II), `advanced_filters.ipynb` (Part III)
- **Documents**: `requirement.md`, `features.md`, `tech_solution.md`, `logic_ai.md`, `implementation.md`, `testing.md`
- **Notes**: `part2_linear_filters.md`, `part3_advanced.md`
- **Input**: `data/input/anh.jpg`, `anh1.jpg`, `anh2.jpg`, `anh3.jpg`
- **Output**: `outputs/point_operations/`, `outputs/linear_filters/`, `outputs/advanced_filters/`

## 3 Phần — 11 Bài Tập

### Part I: Biến Đổi Điểm Ảnh (Point Operations)
| # | Bài | Công thức | Hàm/Phương pháp |
|---|-----|-----------|-----------------|
| 1 | Độ sáng (tối) | `pixel - 50` | `np.clip(image.float32 + (-50), 0, 255).astype(np.uint8)` |
| 2 | Độ sáng (sáng) | `pixel + 50` | `np.clip(image.float32 + 50, 0, 255).astype(np.uint8)` |
| 3 | Tương phản (giảm) | `pixel * 0.5` | `np.clip(image.float32 * 0.5, 0, 255).astype(np.uint8)` |
| 4 | Tương phản (tăng) | `pixel * 1.5` | `np.clip(image.float32 * 1.5, 0, 255).astype(np.uint8)` |
| 5 | Âm bản (Negative) | `255 - pixel` | `255 - image` (uint8 tự động wrap?) → nên dùng float32 |
| 6 | Ngưỡng nhị phân | `255 if pixel > 128 else 0` | `cv2.threshold(gray, 128, 255, cv2.THRESH_BINARY)` |

### Part II: Lọc Tuyến Tính (Linear Filters)
| # | Bài | Hàm OpenCV | Ghi chú |
|---|-----|------------|---------|
| 7 | Lọc trung bình (Mean) | `cv2.blur(image, ksize)` | ksize phải là số lẻ, lọc tuyến tính, hiệu quả với nhiễu Gauss |
| 8 | Lọc Gaussian | `cv2.GaussianBlur(image, (k,k), sigmaX)` | sigma=0 → OpenCV tự tính, làm mờ mượt tự nhiên |
| 9 | Lọc sắc nét (Sharpen) | `cv2.filter2D(image, -1, kernel)` | Kernel `[[0,-1,0],[-1,6,-1],[0,-1,0]]`, tổng trọng số = 1 |

### Part III: Bộ Lọc Nâng Cao (Advanced Filters)
| # | Bài | Hàm/Phương pháp | Ghi chú |
|---|-----|-----------------|---------|
| 10 | Phát hiện cạnh Sobel | Kernel Sobel X/Y + `cv2.filter2D(gray, cv2.CV_32F, kernel)` + `np.sqrt(gx²+gy²)` | Kết quả grayscale, cần `np.clip` |
| 11 | Kernel Emboss | `cv2.filter2D(image, -1, kernel)` | Kernel `[[-2,-1,0],[-1,1,1],[0,1,2]]` tạo hiệu ứng nổi |
| 12 | So sánh bộ lọc | Grid 4 ảnh: Original + Mean + Gaussian + Sharpen | Dùng `cv2.blur()` cho Mean filter |
| 13 | Lọc song phương (Bilateral) | `cv2.bilateralFilter(image, d, sigma, sigma)` | Phi tuyến, giảm nhiễu + giữ cạnh |

## Các Kernel Quan Trọng

```
Sharpen (3×3):          Emboss (3×3):           Sobel X:              Sobel Y:
[[ 0, -1,  0],         [[-2, -1, 0],           [[-1, 0, 1],          [[-1,-2,-1],
 [-1,  6, -1],          [-1,  1, 1],            [-2, 0, 2],           [ 0, 0, 0],
 [ 0, -1,  0]]          [ 0,  1, 2]]            [-1, 0, 1]]           [ 1, 2, 1]]
```

## Khái Niệm Cốt Lõi

### 1. Tích Chập (Convolution)
$$g(x,y) = \sum_{i=-a}^{a}\sum_{j=-b}^{b} f(x+i, y+j) \cdot h(i,j)$$
- $f$: ảnh gốc, $h$: kernel, $g$: ảnh kết quả
- Kernel trượt qua từng pixel, nhân chập và cộng dồn

### 2. Biến Đổi Điểm Ảnh vs Lọc Tích Chập
- **Point operation**: Mỗi pixel biến đổi độc lập, không phụ thuộc lân cận: $g(x,y) = T[f(x,y)]$
- **Convolution filter**: Mỗi pixel mới phụ thuộc vào cửa sổ lân cận quanh nó

### 3. Xử Lý Tràn Số (Overflow)
- `uint8` chỉ chứa giá trị [0, 255] → phép cộng/nhân dễ gây tràn
- **Luôn dùng**: `np.float32` làm trung gian → `np.clip(..., 0, 255)` → `.astype(np.uint8)`

### 4. Lọc Tuyến Tính vs Phi Tuyến
| Tuyến tính | Phi tuyến |
|------------|-----------|
| Gaussian, Mean, Sharpen, Sobel | Bilateral |
| Dùng công thức tổng có trọng số | Không theo công thức tổng trọng số |

### 5. Gaussian vs Bilateral
| Tiêu chí | Gaussian | Bilateral |
|----------|----------|-----------|
| Loại | Tuyến tính | Phi tuyến |
| Giữ cạnh | Không | Có |
| Tốc độ | Nhanh | Chậm hơn |
| Trọng số | Chỉ khoảng cách không gian | Không gian + cường độ |

## Thứ Tự Chạy
1. `point_operations.ipynb` → Part I
2. `linear_filters.ipynb` → Part II
3. `advanced_filters.ipynb` → Part III

Tất cả notebook đọc ảnh từ `../input/anh.jpg` và độc lập với nhau.

## Lỗi Thường Gặp
| Lỗi | Nguyên nhân | Cách khắc phục |
|-----|-------------|----------------|
| Ảnh quá sáng/tối sau biến đổi | Quên `np.clip` | Luôn `np.clip(..., 0, 255)` |
| Tràn số (overflow) | Tính trực tiếp trên `uint8` | Chuyển `np.float32` trước khi tính |
| `ValueError: Kernel size must be an odd integer` | Kernel chẵn cho mean | Dùng ksize lẻ: 3, 5, 7... |
| Cạnh Sobel không rõ | Thiếu `np.clip` hoặc sai kernel | Kiểm tra kernel và thêm `np.clip` |
