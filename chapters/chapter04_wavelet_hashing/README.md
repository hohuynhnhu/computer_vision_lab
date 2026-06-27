# Chương 4: So Sánh Sự Tương Đồng Của Các Hình Ảnh Sử Dụng Wavelet

## Giới Thiệu
Chương này thực hành về phương pháp **Băm hình ảnh Wavelet (Wavelet Hashing)** để so sánh độ tương đồng giữa các hình ảnh và xây dựng ứng dụng tìm kiếm ảnh đơn giản.

Bài thực hành giúp học viên hiểu rõ:
1. Cách áp dụng **Biến đổi Wavelet rời rạc (DWT)** trong xử lý ảnh bằng thư viện `PyWavelets`.
2. Cách xây dựng thuật toán băm nhận thức (Perceptual Hashing) bằng hai cách tiếp cận: phương pháp định lượng trong slide bài giảng và phương pháp wHash tiêu chuẩn.
3. Cách đánh giá chất lượng của thuật toán thông qua các chỉ số Accuracy, Recall, Specificity và vẽ đường cong ROC.

## Cấu Trúc Thư Mục
```
chapter04_wavelet_hashing/
├── data/
│   └── input/            # Ảnh đầu vào mẫu (anh.jpg, anh1.jpg...)
├── documents/            # 6 file tài liệu đặc tả thiết kế và kiểm thử
├── notebook/
│   ├── wavelet_hashing.ipynb     # Phần 1: Khởi tạo mã băm và so khớp
│   ├── model_evaluation.ipynb    # Phần 2: Đánh giá hiệu năng ROC
│   └── image_retrieval.ipynb     # Phần 3: Tìm kiếm ảnh nâng cao
├── notes/
│   └── wavelet_notes.md  # Ghi chú lý thuyết chi tiết
├── outputs/              # Nơi lưu kết quả đầu ra (ROC, ảnh tìm kiếm)
├── agent_context.md      # Ngữ cảnh học tập dành cho Agent
└── README.md             # File này
```

## Yêu Cầu Cài Đặt
Cần cài đặt thư viện `PyWavelets` để xử lý Wavelet:
```bash
pip install PyWavelets
```

Các thư viện nền tảng khác:
```bash
pip install opencv-python numpy matplotlib pillow scikit-image jupyter
```

## Hướng Dẫn Thực Hành
1. Đảm bảo đã sao chép hoặc tạo các ảnh mẫu trong thư mục `data/input/`.
2. Mở Jupyter Notebook:
   ```bash
   cd notebook
   jupyter notebook
   ```
3. Chạy các notebook theo đúng thứ tự:
   - Chạy `wavelet_hashing.ipynb` trước để khởi tạo băm và lưu kết quả trung gian.
   - Chạy `model_evaluation.ipynb` để vẽ đường cong ROC.
   - Chạy `image_retrieval.ipynb` để demo hệ thống tìm kiếm ảnh.
