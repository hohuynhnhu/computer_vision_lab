# Chương 1: Cơ Bản Về Ảnh - Kiểm Thử

## 1. Ma Trận Kiểm Thử

| Test Case | Dữ Liệu Vào | Kết Quả Mong Đợi | Trạng Thái |
|-----------|-------------|------------------|------------|
| Cài đặt thư viện | `pip install` | OpenCV và Pillow được cài thành công | ✓ Pass |
| Kiểm tra phiên bản | `cv2.__version__` | Hiển thị phiên bản OpenCV và Pillow | ✓ Pass |
| Đọc ảnh `lab1.jpg` | File ảnh hợp lệ | Trả về mảng NumPy, shape (H, W, 3) | ✓ Pass |
| Chuyển đổi BGR→RGB | Mảng ảnh BGR | Mảng ảnh RGB, hiển thị đúng màu | ✓ Pass |
| Lưu PNG | Ảnh đã xử lý | File `lab1_saved.png` được tạo | ✓ Pass |
| Lưu BMP | Ảnh đã xử lý | File `lab1_pillow.bmp` được tạo bằng Pillow | ✓ Pass |
| Chuyển sang Grayscale | Ảnh màu RGB | Ảnh xám 1 kênh, hiển thị đúng | ✓ Pass |
| Chuyển sang HSV | Ảnh màu RGB | Ảnh 3 kênh HSV, hiển thị đúng | ✓ Pass |
| Chuyển sang LAB | Ảnh màu RGB | Ảnh 3 kênh LAB, hiển thị đúng | ✓ Pass |
| Cắt ảnh [100:350, 100:350] | Ảnh RGB | Ảnh kích thước 250×250 | ✓ Pass |
| Resize 200×200 | Ảnh gốc (914×735) | Ảnh kích thước 200×200 | ✓ Pass |
| Resize 50% | Ảnh gốc (914×735) | Ảnh kích thước 457×367 | ✓ Pass |
| Vẽ đường thẳng | Canvas + tọa độ | Đường đỏ từ (0,0) đến (512,512) | ✓ Pass |
| Vẽ hình chữ nhật | Canvas + tọa độ | Hình chữ nhật xanh lá tại (50,50)-(200,200) | ✓ Pass |
| Vẽ hình tròn | Canvas + tâm+bán kính | Hình tròn xanh dương tại (380,130), bán kính 80 | ✓ Pass |
| Thêm văn bản | Canvas + text | Hiển thị text "OpenCV Demo" | ✓ Pass |
| Lưu ảnh kết quả vẽ | Canvas có hình + chữ | File `result_drawing.png` được tạo | ✓ Pass |

## 2. Kết Quả Đầu Ra

```
images/
├── lab1_pillow.bmp        (~1,500 KB, BMP)
├── lab1_saved.png         (~1,200 KB, PNG)
└── result_drawing.png     (~1,300 KB, PNG)
```

## 3. Các Lỗi Thường Gặp

| Lỗi | Nguyên Nhân | Cách Khắc Phục |
|-----|-------------|----------------|
| `cv2.error: (-215:Assertion failed)` | File ảnh không tồn tại hoặc đường dẫn sai | Kiểm tra tên file và đường dẫn |
| Màu sắc hiển thị sai (xanh-đỏ hoán đổi) | Không chuyển BGR→RGB trước khi `imshow` | Thêm `cv2.cvtColor(img, cv2.COLOR_BGR2RGB)` |
| `ModuleNotFoundError: No module named 'cv2'` | OpenCV chưa được cài | Chạy `pip install opencv-python` |
| `ModuleNotFoundError: No module named 'PIL'` | Pillow chưa được cài | Chạy `pip install Pillow` |

## 4. Kiểm Tra Kích Thước

| Ảnh | Kích Thước Mong Đợi |
|-----|---------------------|
| Ảnh gốc `lab1.jpg` | 914 × 735 × 3 |
| Sau crop [100:350, 100:350] | 250 × 250 × 3 |
| Sau resize cố định 200×200 | 200 × 200 × 3 |
| Sau resize 50% | 457 × 367 × 3 |
