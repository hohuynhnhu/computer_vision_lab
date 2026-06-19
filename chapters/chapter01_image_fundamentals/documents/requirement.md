# Chương 1: Cơ Bản Về Ảnh - Yêu Cầu Người Dùng

## 1. Mục Tiêu

Sinh viên cần nắm vững các thao tác cơ bản khi làm việc với ảnh số bằng Python, bao gồm cài đặt thư viện, đọc/ghi ảnh, chuyển đổi không gian màu, cắt/resize ảnh và vẽ các hình cơ bản lên ảnh.

## 2. Yêu Cầu Chi Tiết

| STT | Yêu Cầu | Mô Tả |
|-----|---------|-------|
| 1 | Cài đặt thư viện OpenCV và Pillow | Cài đặt `opencv-python` và `Pillow`; kiểm tra phiên bản |
| 2 | Đọc và hiển thị ảnh | Đọc ảnh từ file `lab1.jpg`, chuyển đổi BGR→RGB, hiển thị bằng `matplotlib`, lưu ảnh với định dạng PNG và BMP |
| 3 | Chuyển đổi không gian màu | Chuyển đổi RGB sang Grayscale, HSV, LAB; hiển thị đồng thời 4 không gian màu trên một lưới |
| 4 | Cắt ảnh và thay đổi kích thước | Cắt vùng ảnh (crop), resize cố định và resize theo tỷ lệ; hiển thị kết quả |
| 5 | Vẽ hình cơ bản và thêm văn bản | Vẽ đường thẳng, hình chữ nhật, hình tròn; thêm văn bản lên ảnh; lưu kết quả |

## 3. Đầu Vào

- Ảnh mẫu `lab1.jpg` trong thư mục làm việc

## 4. Đầu Ra

- `lab1_saved.png`: Ảnh lưu định dạng PNG
- `lab1_pillow.bmp`: Ảnh lưu định dạng BMP bằng Pillow
- `result_drawing.png`: Ảnh kết quả sau khi vẽ hình và văn bản
- Hiển thị trực quan các bước xử lý bằng `matplotlib`

## 5. Ràng Buộc

- Sử dụng Python 3.11+
- Sử dụng OpenCV và Pillow làm thư viện chính
- Code có chú thích bằng tiếng Việt
