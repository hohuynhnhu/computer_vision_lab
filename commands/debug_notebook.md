# Lệnh: Gỡ Lỗi Notebook

## Mục Đích
Phát hiện và sửa lỗi trong Jupyter Notebook của bài tập Computer Vision.

## Quy Trình Debug

### Bước 1: Kiểm Tra Môi Trường
```bash
# Kiểm tra phiên bản Python và thư viện
python --version
pip list | grep -E "opencv|numpy|matplotlib|pillow|scikit"
```

Các phiên bản yêu cầu tối thiểu:
- opencv-python >= 4.11
- numpy >= 1.26
- matplotlib >= 3.5
- Pillow >= 12.0
- scikit-image >= 0.19 (chỉ Chapter 3)

### Bước 2: Kiểm Tra Cấu Trúc Cell
Đọc notebook `.ipynb` và kiểm tra:
- [ ] Cell 1: Import đầy đủ thư viện
- [ ] Cell 2: Đọc dữ liệu từ đúng đường dẫn (`../input/` hoặc `data/input/`)
- [ ] Các cell xử lý: code logic đúng theo `documents/implementation.md`
- [ ] Cell cuối: Lưu kết quả vào `outputs/`
- [ ] `plt.show()` hoặc `%matplotlib inline` cho hiển thị

### Bước 3: Kiểm Tra Lỗi Phổ Biến

#### 3.1 Lỗi Import
```
ModuleNotFoundError: No module named 'cv2'
→ pip install opencv-python

ModuleNotFoundError: No module named 'skimage'
→ pip install scikit-image
```

#### 3.2 Lỗi Đường Dẫn
```
cv2.error: (-215:Assertion failed) !_src.empty()
→ File ảnh không tồn tại, kiểm tra đường dẫn
→ os.path.exists('path/to/image.jpg')
```

#### 3.3 Lỗi Màu Sắc
```
# Triệu chứng: Ảnh hiển thị sai màu (xanh thành đỏ, đỏ thành xanh)
# Nguyên nhân: OpenCV BGR vs Matplotlib RGB
# Sửa: Thêm dòng chuyển đổi
img_rgb = cv2.cvtColor(img_bgr, cv2.COLOR_BGR2RGB)
```

#### 3.4 Lỗi Kiểu Dữ Liệu
```
# Triệu chứng: Ảnh output bị sọc, vỡ, hoặc sai giá trị
# Nguyên nhân: Tràn số với uint8
# Sửa: Dùng np.float32 trung gian
result = np.clip(img.astype(np.float32) * 1.5, 0, 255).astype(np.uint8)
```

#### 3.5 Lỗi Tham Số
```
# Median filter: kernel size phải là số lẻ
if kernel_size % 2 == 0:
    raise ValueError("Kernel size must be odd")

# GaussianBlur: kernel size phải là tuple (w, h) với số lẻ
cv2.GaussianBlur(img, (5, 5), 0)  # đúng
cv2.GaussianBlur(img, (6, 6), 0)  # lỗi
```

### Bước 4: Kiểm Tra Output
- [ ] File output được tạo trong đúng thư mục
- [ ] Kích thước ảnh output hợp lý (không quá nhỏ/quá to)
- [ ] Nội dung ảnh output khớp với mong đợi (dùng matplotlib để kiểm tra trực quan)

### Bước 5: Log Kết Quả
```python
# Thêm log vào notebook để dễ debug
print(f"[DEBUG] Image shape: {img.shape}")
print(f"[DEBUG] Min pixel: {img.min()}, Max pixel: {img.max()}")
print(f"[DEBUG] Output file: {output_path}")
```
