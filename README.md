`# Computer Vision Lab - Phòng Thực Hành Thị Giác Máy Tính

## Giới Thiệu

Dự án bài tập thực hành môn **Thị giác Máy tính (Computer Vision)** dành cho sinh viên lớp **CN22H**. Dự án bao gồm các bài tập từ cơ bản đến nâng cao về xử lý ảnh sử dụng Python và OpenCV.

## Cấu Trúc

```
lab/
├── chapters/                              # Nội dung bài tập
│   ├── chapter01_image_fundamentals/      # Chương 1: Cơ bản về ảnh
│   ├── chapter02_image_filtering/         # Chương 2: Lọc ảnh
│   ├── chapter03_edge_detection/          # Chương 3: Phát hiện cạnh
│   ├── chapter04_wavelet_hashing/         # Chương 4: So sánh ảnh dùng Wavelet
│   └── chapter05_face_recognition/        # Chương 5: Nhận diện khuôn mặt
├── docs/                                  # Tài liệu dự án
├── commands/                              # Định nghĩa lệnh AI agent
├── knowledge_base/                        # Cơ sở tri thức
├── memory/                                # Bộ nhớ agent (bài học)
└── README.md                              # File này
```

## Chương Trình Học

| Chương | Chủ Đề | Nội Dung Chính |
|--------|--------|----------------|
| 1 | Cơ Bản Về Ảnh | Đọc/ghi ảnh, không gian màu, crop/resize, vẽ hình, văn bản |
| 2 | Lọc Ảnh | Biến đổi điểm ảnh, Median, Gaussian, Sharpen, Sobel, Emboss, Bilateral |
| 3 | Phát Hiện Cạnh | Canny (OpenCV & Scikit-image), Phân tích tham số, Đánh giá, Contour, Shape Detection |
| 4 | So Sánh Ảnh Dùng Wavelet | Biến đổi DWT (PyWavelets), Lượng tử hóa, wHash chuẩn, Khoảng cách Hamming, ROC Curve, Tìm kiếm ảnh |
| 5 | Nhận Diện Khuôn Mặt | MTCNN, FaceNet, Embedding, Cosine Similarity, Nhận diện thời gian thực qua Webcam |


## Cài Đặt

### Yêu Cầu
- Python 3.11+
- pip

### Cài Thư Viện
```bash
pip install opencv-python numpy matplotlib pillow scikit-image PyWavelets jupyter
```

Đối với Chương 5 (Face Recognition), cài thêm:
```bash
pip install facenet-pytorch torch
```


### Chạy Notebook
```bash
cd chapters/chapter01_image_fundamentals/notebook
jupyter notebook chapter01.ipynb
```

## Công Nghệ Sử Dụng

| Công Nghệ | Mục Đích |
|-----------|----------|
| **OpenCV** | Xử lý ảnh chính (đọc/ghi, lọc, Canny, contour, webcam) |
| **NumPy** | Xử lý mảng, tính toán số học |
| **Matplotlib** | Hiển thị ảnh và biểu đồ |
| **Pillow** | Hỗ trợ đọc/ghi ảnh đa định dạng |
| **Scikit-image** | Phát hiện cạnh Canny (thư viện thứ hai) |
| **Facenet-PyTorch** | MTCNN (phát hiện khuôn mặt) + FaceNet (trích xuất embedding) |
| **PyTorch** | Backend deep learning cho MTCNN và FaceNet |
| **Jupyter** | Môi trường thực hành tương tác |

## Cách Sử Dụng

1. **Clone repository** (hoặc mở thư mục dự án)
2. **Cài đặt thư viện**: `pip install -r requirements.txt`
3. **Mở notebook**: Di chuyển vào thư mục chương và chạy `jupyter notebook`
4. **Chạy từng cell**: Theo thứ tự từ trên xuống
5. **Kiểm tra kết quả**: Xem ảnh output trong thư mục `outputs/`

## Tài Liệu

Mỗi chương có đầy đủ tài liệu trong `documents/`:
- `requirement.md` - Yêu cầu người dùng
- `features.md` - Danh sách tính năng
- `tech_solution.md` - Giải pháp kỹ thuật
- `logic_ai.md` - Logic và nguyên lý thuật toán
- `implementation.md` - Hướng dẫn triển khai
- `testing.md` - Ma trận và kết quả kiểm thử

## Tác Giả

- **Lớp**: CN22H
- **Môn**: Thị giác Máy tính (Computer Vision)
- **Ngôn ngữ**: Tiếng Việt
