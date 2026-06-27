# Chương 4: So Sánh Sự Tương Đồng Của Các Hình Ảnh Sử Dụng Wavelet - Triển Khai

## 1. Cấu Trúc Code

Chương này được chia thành 3 file notebook chuyên biệt để thuận tiện quản lý và theo dõi:
1.  **`wavelet_hashing.ipynb`** (Phần 1: Khởi tạo mã băm): Thực hiện chuẩn bị dữ liệu (sinh các ảnh xoay, sáng, nhiễu), cài đặt các hàm băm Wavelet và tính khoảng cách Hamming chéo, sau đó lưu kết quả trung gian ra file JSON.
2.  **`model_evaluation.ipynb`** (Phần 2: Đánh giá hiệu năng bằng ROC): Đọc file kết quả trung gian, tính toán ma trận nhầm lẫn (Accuracy, Recall, Specificity) ứng với các ngưỡng khoảng cách khác nhau và vẽ đồ thị ROC.
3.  **`image_retrieval.ipynb`** (Phần 3: Hệ thống tìm kiếm ảnh nâng cao): Xây dựng CSDL chứa các ảnh sạch và thực hiện tìm kiếm so khớp ảnh bị nhiễu hạt để tìm ra ảnh gốc tương đồng nhất.

## 2. Chi Tiết Triển Khai Từng Bước

### Bước 1: Khai báo thư viện & Cấu hình
```python
import os
import cv2
import numpy as np
import pywt
import matplotlib.pyplot as plt

# Đảm bảo hiển thị ảnh đẹp mắt
%matplotlib inline
plt.rcParams['figure.figsize'] = [10, 6]
```

### Bước 2: Chuẩn bị dữ liệu kiểm thử
Học viên tạo các biến thể để tạo ra tập dữ liệu 10 ảnh:
*   4 ảnh gốc độc lập (`anh.jpg`, `anh1.jpg`, `anh2.jpg`, `anh3.jpg`).
*   Các ảnh biến thể tương tự của ảnh thứ 1 (`anh.jpg`):
    *   `anh_rotated.jpg` (Xoay 15 độ).
    *   `anh_bright.jpg` (Tăng độ sáng 40 đơn vị).
    *   `anh_noisy.jpg` (Thêm nhiễu Gaussian).
*   Ghi nhãn Ground Truth cho tất cả cặp đôi (1 nếu tương tự, 0 nếu không tương tự).

### Bước 3: Định nghĩa hàm băm Wavelet
*   **Hàm lượng tử hóa tùy chỉnh**:
```python
def custom_quantize(c, step=2):
    if isinstance(c, tuple):
        return tuple(custom_quantize(sub_c, step) for sub_c in c)
    return np.round(c / step).astype(np.int64)
```
*   **Hàm băm kiểu Slide**:
```python
def wavelet_hash_slide(image_path, wavelet='db4', level=1, step=2, size=(64, 64)):
    img = cv2.imread(image_path, cv2.IMREAD_GRAYSCALE)
    img_resized = cv2.resize(img, size)
    coeffs = pywt.wavedec2(img_resized.astype(np.float32), wavelet, level=level)
    coeffs_quant = [custom_quantize(c, step) for c in coeffs]
    
    # Flatten
    flat = []
    for c in coeffs_quant:
        if isinstance(c, tuple):
            for sub_c in c: flat.append(sub_c.flatten())
        else:
            flat.append(c.flatten())
    flattened = np.concatenate(flat)
    
    return [int(x % 2) for x in flattened]
```
*   **Hàm băm wHash chuẩn**:
```python
def wavelet_hash_standard(image_path, wavelet='db4', level=1, size=(64, 64)):
    img = cv2.imread(image_path, cv2.IMREAD_GRAYSCALE)
    img_resized = cv2.resize(img, size)
    coeffs = pywt.wavedec2(img_resized.astype(np.float32), wavelet, level=level)
    cA = coeffs[0]
    med = np.median(cA)
    return (cA > med).astype(int).flatten().tolist()
```

### Bước 4: Khoảng cách Hamming & Đánh giá phân loại
Đo khoảng cách nhị phân giữa các cặp mã băm:
```python
def hamming_distance(hash1, hash2):
    return sum(abs(a - b) for a, b in zip(hash1, hash2))
```
Thực hiện chạy so sánh chéo giữa tất cả các ảnh và đối chiếu với Ground Truth để thu thập True Positives, False Positives, True Negatives, False Negatives ở các ngưỡng khoảng cách khác nhau.

### Bước 5: Tính các chỉ số và vẽ ROC
Tính tỷ lệ TPR, FPR và vẽ đường cong biểu diễn hiệu năng phân loại.

### Bước 6: Ứng dụng tìm kiếm ảnh (Retrieval)
Nhận ảnh truy vấn (query) và tính khoảng cách Hamming với các ảnh trong cơ sở dữ liệu (database), trả về ảnh có khoảng cách nhỏ nhất.
