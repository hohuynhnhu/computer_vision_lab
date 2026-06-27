# Chương 4: So Sánh Sự Tương Đồng Của Các Hình Ảnh Sử Dụng Wavelet - Tính Năng

## Danh Sách Tính Năng

| STT | Tính Năng | Đầu Vào | Đầu Ra | Mô Tả |
|-----|-----------|---------|--------|-------|
| 1 | Chuẩn bị dữ liệu kiểm thử | Ảnh gốc | Tập hợp các cặp ảnh (tương tự / khác biệt) | Sinh dữ liệu kiểm thử bằng các phép biến đổi ảnh: xoay, đổi độ sáng, thêm nhiễu. |
| 2 | Trích xuất đặc trưng Wavelet | Ảnh xám đã resize | Ma trận hệ số DWT | Thực hiện biến đổi Wavelet rời rạc 2D (DWT) sử dụng PyWavelets (`pywt.wavedec2`). |
| 3 | Lượng tử hóa & Tạo mã băm (Slide) | Hệ số DWT | Chuỗi nhị phân (0/1) | Triển khai lượng tử hóa tùy chỉnh theo bước (step) và tính số dư (`bit % 2`) để băm toàn bộ hệ số. |
| 4 | Tạo mã băm wHash chuẩn | Hệ số DWT | Chuỗi nhị phân (0/1) | Tạo mã băm nhận thức chuẩn bằng cách so sánh hệ số xấp xỉ (`cA`) với giá trị trung vị (`median`). |
| 5 | Tính khoảng cách Hamming | Hai mã băm nhị phân | Số nguyên (khoảng cách) | So sánh hai mã băm có cùng độ dài và đếm số bit khác nhau. |
| 6 | Đánh giá phân loại & Vẽ ROC | Ma trận khoảng cách | Accuracy, Recall, Specificity và file `roc_curve.png` | Tìm ngưỡng tối ưu và đánh giá chất lượng phân loại của hai phương pháp bằng đồ thị ROC. |
| 7 | Tìm kiếm ảnh truy vấn | Ảnh truy vấn + database | Danh sách ảnh có khoảng cách nhỏ nhất | Tìm kiếm ảnh tương đồng trong cơ sở dữ liệu dựa trên mã băm. |

## Chi Tiết Kỹ Thuật

### 1. Chuẩn Bị Ảnh Biến Đổi
```python
# Tăng sáng
img_bright = np.clip(img.astype(np.int16) + 40, 0, 255).astype(np.uint8)
# Xoay ảnh
M = cv2.getRotationMatrix2D((w/2, h/2), 15, 1.0)
img_rotated = cv2.warpAffine(img, M, (w, h))
# Thêm nhiễu Gaussian
noise = np.random.normal(0, 15, img.shape).astype(np.int16)
img_noisy = np.clip(img.astype(np.int16) + noise, 0, 255).astype(np.uint8)
```

### 2. Trích Xuất & Lượng Tử Hóa Wavelet (Slide)
```python
def custom_quantize(c, step=2):
    if isinstance(c, tuple):
        return tuple(custom_quantize(sub_c, step) for sub_c in c)
    return np.round(c / step).astype(np.int64)

coeffs = pywt.wavedec2(img_resized.astype(np.float32), 'db4', level=1)
coeffs_quant = [custom_quantize(c, 2) for c in coeffs]
```

### 3. Tạo Mã Băm wHash Chuẩn
```python
coeffs = pywt.wavedec2(img_resized.astype(np.float32), 'db4', level=1)
cA = coeffs[0]
med = np.median(cA)
hash_code = (cA > med).astype(int).flatten().tolist()
```

### 4. Khoảng Cách Hamming
```python
def hamming_distance(hash1, hash2):
    return sum(abs(a - b) for a, b in zip(hash1, hash2))
```
