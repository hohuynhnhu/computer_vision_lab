# Mô Tả Dự Án

## Tên Dự Án

**Computer Vision Lab** - Phòng Thực Hành Thị Giác Máy Tính

## Mục Tiêu

Dự án cung cấp một hệ thống bài tập thực hành toàn diện về Xử lý Ảnh và Thị giác Máy tính cho sinh viên, bao gồm:

1. **Nắm vững kiến thức nền tảng**: Từ thao tác cơ bản với ảnh số đến các thuật toán phức tạp
2. **Thực hành với thư viện thực tế**: OpenCV, Scikit-image, NumPy, Matplotlib
3. **Hiểu nguyên lý thuật toán**: Thông qua code và trực quan hóa
4. **So sánh phương pháp**: Đánh giá ưu/nhược điểm của từng kỹ thuật

## Đối Tượng

Sinh viên lớp **CN22H** - ngành Công nghệ Thông tin / Khoa học Máy tính.

## Nội Dung

| Chương | Chủ Đề | Số Bài | Kỹ Thuật Chính |
|--------|--------|--------|----------------|
| 1 | Cơ bản về ảnh | 5 | Đọc/ghi, không gian màu, crop/resize, vẽ hình |
| 2 | Lọc ảnh | 11 (3 phần) | Point ops, Median, Gaussian, Sharpen, Sobel, Emboss, Bilateral |
| 3 | Phát hiện cạnh | 12 (4 phần) | Canny OpenCV/Skimage, Parameter tuning, Evaluation, Contour/Shape |
| 4 | So sánh ảnh dùng Wavelet | 6 (2 phần) | Biến đổi DWT, Lượng tử hóa slide, wHash chuẩn, Hamming, Đánh giá ROC, Image Retrieval |

## Công Nghệ

| Công Nghệ | Phiên Bản |
|-----------|-----------|
| Python | 3.11 |
| OpenCV | 4.11 |
| NumPy | 1.26 |
| Matplotlib | 3.7+ |
| Pillow | 12.0 |
| Scikit-image | 0.22+ |
| PyWavelets | 1.9+ |
| Jupyter | Latest |

## Cấu Trúc

```
lab/
├── chapters/           # 4 chương học chính
├── docs/               # Tài liệu dự án
├── commands/           # Định nghĩa lệnh AI agent
├── knowledge_base/     # Cơ sở tri thức (thuật ngữ)
├── memory/             # Bộ nhớ agent (bài học)
└── README.md
```

Mỗi chương tuân theo mẫu:
- `data/input/` → ảnh đầu vào
- `notebook/` → code thực hành
- `documents/` → 6 file tài liệu (yêu cầu, tính năng, giải pháp, logic, triển khai, kiểm thử)
- `notes/` → ghi chú chi tiết
- `outputs/` → ảnh kết quả

## Cách Sử Dụng

1. Cài đặt thư viện: `pip install opencv-python numpy matplotlib pillow scikit-image jupyter`
2. Mở Jupyter: `jupyter notebook`
3. Chạy các notebook theo thứ tự từng chương
4. Kiểm tra kết quả trong thư mục `outputs/`

## Tác Giả

- **Lớp**: CN22H
- **Môn học**: Thị giác Máy tính (Computer Vision)
- **Ngôn ngữ**: Tiếng Việt
