# Bài Học Kinh Nghiệm

## 1. Về Thiết Kế Dự Án

### Cấu Trúc Chapter
- **Bài học**: Mỗi chapter cần có cấu trúc nhất quán (`data/`, `notebook/`, `documents/`, `notes/`, `outputs/`) giúp dễ quản lý và mở rộng.
- **Tài liệu**: Viết documents/ ngay từ đầu giúp định hình rõ yêu cầu trước khi code.

### Tổ Chức Notebook
- Mỗi notebook chỉ nên tập trung vào 1 chủ đề con (một Part).
- Tách nhỏ notebook giúp dễ debug và sinh viên dễ theo dõi.
- Luôn có cell đầu tiên để import, cell thứ hai để đọc dữ liệu.

## 2. Về Xử Lý Ảnh

### Định Dạng Màu
- **Bài học quan trọng nhất**: Luôn ghi rõ định dạng màu trong tên biến (`img_bgr`, `img_rgb`, `img_gray`).
- OpenCV dùng BGR là nguyên nhân gây ra nhiều lỗi nhất cho người mới học.

### An Toàn Giá Trị
- `np.uint8` là "con dao hai lưỡi": nhanh nhưng dễ tràn số.
- **Luôn dùng**: `float32` → tính toán → `np.clip` → `uint8`.

### Hiển Thị Ảnh
- Ảnh xám luôn cần `cmap='gray'` khi dùng `plt.imshow`.
- Nên hiển thị ảnh trước khi lưu để kiểm tra trực quan.

## 3. Về Thuật Toán

### Lọc Ảnh
- Median filter tốt cho nhiễu muối tiêu, không tốt cho nhiễu Gauss.
- Gaussian blur làm mờ toàn bộ ảnh, kể cả cạnh.
- Bilateral filter là lựa chọn tốt khi vừa muốn giảm nhiễu vừa giữ cạnh.

### Canny Edge Detection
- Ngưỡng là tham số quan trọng nhất, ảnh hưởng lớn đến kết quả.
- Tiền xử lý (Gaussian, EqualizeHist) quyết định chất lượng cạnh.
- Không có bộ tham số "tốt nhất" cho mọi ảnh - cần điều chỉnh theo từng loại ảnh.

### So Sánh Thư Viện
- OpenCV nhanh hơn cho ứng dụng thực tế.
- Scikit-image phù hợp cho nghiên cứu và học thuật.
- Kết quả giữa 2 thư viện có thể khác nhau do cách triển khai khác nhau.

## 4. Về Giảng Dạy

### Độ Khó Tăng Dần
- Chương 1 (cơ bản) → Chương 2 (lọc ảnh) → Chương 3 (phát hiện cạnh).
- Cách tổ chức này giúp sinh viên xây dựng kiến thức từng bước.

### Tài Liệu Tiếng Việt
- Comment và giải thích bằng tiếng Việt giúp sinh viên dễ tiếp thu.
- Kết hợp cả công thức toán học và code thực tế.

### Trực Quan Hóa
- `matplotlib` hiển thị so sánh trước/sau là cách tốt nhất để sinh viên hiểu tác động của thuật toán.
- Grid so sánh nhiều ảnh giúp dễ đánh giá.

## 5. Về AI Agent

### Cấu Trúc Commands/
- Mỗi command cần có mục đích rõ ràng và quy trình cụ thể.
- Template giúp agent tạo output nhất quán.

### Memory/
- Ghi lại best practices giúp agent tránh lặp lỗi.
- Common mistakes giúp agent nhanh chóng chẩn đoán vấn đề.

### Knowledge Base/
- Glossary dùng chung cho tất cả chương.
- Giúp agent giải thích thuật ngữ nhất quán.
