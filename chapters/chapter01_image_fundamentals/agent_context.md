# Chapter 01: Cơ Bản Về Ảnh (Image Fundamentals) — Agent Context

## Tổng Quan
Chương này giới thiệu các thao tác cơ bản nhất khi làm việc với ảnh số bằng Python: cài đặt thư viện, đọc/ghi ảnh, chuyển đổi không gian màu, cắt/resize ảnh và vẽ hình + văn bản lên ảnh.

## Cấu Trúc
- **Notebook**: `notebook/chapter01.ipynb` — 1 notebook duy nhất, 5 cell tương ứng 5 bài tập.
- **Documents**: `requirement.md`, `features.md`, `tech_solution.md`, `logic_ai.md`, `implementation.md`, `testing.md`
- **Input**: `data/input` (file ảnh không đuôi), `images/drawing` (file ảnh không đuôi)
- **Output**: `images/lab1_saved.png`, `images/lab1_pillow.bmp`, `result_drawing.png`

## Các Bài Tập (5 bài)

| # | Tên | Mô tả |
|---|-----|-------|
| 1 | Cài đặt thư viện | `pip install opencv-python Pillow`, kiểm tra phiên bản |
| 2 | Đọc và hiển thị ảnh | Đọc `lab1.jpg` bằng OpenCV, chuyển BGR→RGB, hiển thị matplotlib, lưu PNG + BMP |
| 3 | Chuyển đổi không gian màu | BGR → Grayscale, HSV, LAB; hiển thị lưới 4 ảnh so sánh |
| 4 | Cắt và resize ảnh | Crop [100:350, 100:350], resize cố định 200×200, resize 50% |
| 5 | Vẽ hình và văn bản | line, rectangle, circle, putText; lưu kết quả |

## Công Nghệ
| Công nghệ | Vai trò |
|-----------|---------|
| OpenCV (`cv2`) | Đọc/ghi ảnh, chuyển màu, vẽ hình, resize |
| Pillow (`PIL`) | Lưu ảnh định dạng BMP |
| NumPy | Xử lý mảng ảnh |
| Matplotlib | Hiển thị ảnh |

## Khái Niệm Quan Trọng

### 1. BGR vs RGB
- OpenCV đọc ảnh ở định dạng **BGR** (kênh 0=Blue, 1=Green, 2=Red)
- Matplotlib hiển thị **RGB** → luôn phải chuyển `cv2.cvtColor(img_bgr, cv2.COLOR_BGR2RGB)` trước khi `plt.imshow()`
- Khi lưu bằng `cv2.imwrite()`, cần chuyển ngược từ RGB về BGR

### 2. Không Gian Màu
- **Grayscale**: `cv2.COLOR_BGR2GRAY` — 1 kênh, công thức Y = 0.299R + 0.587G + 0.114B
- **HSV**: `cv2.COLOR_BGR2HSV` — Hue (sắc độ), Saturation (bão hòa), Value (độ sáng)
- **LAB**: `cv2.COLOR_BGR2LAB` — L* (độ sáng), a* (xanh lục↔đỏ), b* (xanh dương↔vàng)

### 3. Hệ Tọa Độ
- Gốc (0,0) = góc trên bên trái
- Trục x tăng sang phải, trục y tăng xuống dưới
- Crop: `img[y1:y2, x1:x2]`

### 4. Nội Suy (Interpolation) Khi Resize
- `INTER_LINEAR` (mặc định): nội suy song tuyến, nhanh, chất lượng trung bình
- `INTER_NEAREST`: nhanh nhất, chất lượng thấp
- `INTER_CUBIC`: chậm, chất lượng cao
- `INTER_AREA`: tốt cho thu nhỏ

## Màu Sắc Trong OpenCV
Định dạng `(B, G, R)`, không phải `(R, G, B)`:
- `(255, 0, 0)` → Blue (xanh dương)
- `(0, 255, 0)` → Green (xanh lục)
- `(0, 0, 255)` → Red (đỏ)

## Thứ Tự Chạy
1. Cell 1 (cài đặt thư viện)
2. Cell 2 (đọc ảnh) — các cell sau phụ thuộc vào biến `img_bgr`, `img_rgb`
3. Cell 3, 4, 5 (thứ tự tùy ý)

## Lỗi Thường Gặp
| Lỗi | Nguyên nhân | Cách khắc phục |
|-----|-------------|----------------|
| `cv2.error: (-215:Assertion failed)` | File ảnh không tồn tại hoặc sai đường dẫn | Kiểm tra tên file và path |
| Màu hiển thị sai (xanh↔đỏ hoán đổi) | Quên chuyển BGR→RGB | Thêm `cv2.cvtColor(img, cv2.COLOR_BGR2RGB)` |
| `ModuleNotFoundError: No module named 'cv2'` | OpenCV chưa cài | `pip install opencv-python` |
| `ModuleNotFoundError: No module named 'PIL'` | Pillow chưa cài | `pip install Pillow` |

## Kích Thước Ảnh Tham Chiếu
| Ảnh | Kích thước |
|-----|-----------|
| Gốc `lab1.jpg` | 914 × 735 × 3 |
| Sau crop [100:350, 100:350] | 250 × 250 × 3 |
| Resize cố định 200×200 | 200 × 200 × 3 |
| Resize 50% | 457 × 367 × 3 |
