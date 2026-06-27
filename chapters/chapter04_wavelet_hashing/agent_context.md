# Chapter 04: So Sánh Sự Tương Đồng Của Các Hình Ảnh Sử Dụng Wavelet — Agent Context

## Tổng Quan
Chương này giới thiệu kỹ thuật sử dụng Biến đổi Wavelet Rời rạc (DWT) để trích xuất đặc trưng ảnh dưới dạng mã băm nhị phân (Wavelet Hashing / wHash), từ đó đánh giá sự tương đồng giữa các bức ảnh bằng khoảng cách Hamming. Học viên sẽ được hướng dẫn lập ma trận kiểm thử, vẽ đường cong ROC và xây dựng ứng dụng tìm kiếm ảnh đơn giản.

## Cấu Trúc
- **Notebooks**:
  - `notebook/wavelet_hashing.ipynb` — Phần 1: Khởi tạo mã băm và tính khoảng cách.
  - `notebook/model_evaluation.ipynb` — Phần 2: Đánh giá hiệu năng và vẽ ROC.
  - `notebook/image_retrieval.ipynb` — Phần 3: Hệ thống tìm kiếm ảnh (Image Retrieval).
- **Documents**: `requirement.md`, `features.md`, `tech_solution.md`, `logic_ai.md`, `implementation.md`, `testing.md`
- **Input**: `data/input/` (chứa các ảnh: `anh.jpg`, `anh1.jpg`, `anh2.jpg`, `anh3.jpg`)
- **Output**: `outputs/` (chứa biểu đồ ROC `roc_curve.png` và kết quả tìm kiếm ảnh `retrieval_result.png`)

## Các Bài Tập (6 phần)

| # | Tên | Mô tả |
|---|-----|-------|
| 1 | Chuẩn bị dữ liệu | Tự động sinh tập dữ liệu gồm các cặp tương tự (xoay ảnh, thêm nhiễu Gaussian, thay đổi độ sáng) và các cặp khác biệt. |
| 2 | Trích xuất Wavelet & Lượng tử hóa | Triển khai biến đổi DWT (`pywt.wavedec2`), thực hiện lượng tử hóa hệ số theo 2 phương pháp (phương pháp slide và phương pháp wHash chuẩn). |
| 3 | Tạo mã băm (Wavelet Hash) | Chuyển đổi các hệ số đã lượng tử hóa thành một chuỗi nhị phân (hash code) có độ dài cố định. |
| 4 | Tính khoảng cách Hamming | Viết hàm tính số lượng bit khác biệt giữa hai mã băm để đánh giá mức độ tương thích. |
| 5 | Đánh giá mô hình | Tính toán các chỉ số: Accuracy, Recall, Specificity và vẽ đường cong ROC dựa trên các ngưỡng khoảng cách Hamming khác nhau. |
| 6 | Tìm kiếm hình ảnh nâng cao | Xây dựng hệ thống truy vấn ảnh: tìm kiếm trong cơ sở dữ liệu các ảnh có khoảng cách Hamming nhỏ nhất với ảnh đầu vào. |

## Công Nghệ
| Công nghệ | Vai trò |
|-----------|---------|
| OpenCV (`cv2`) | Đọc/ghi ảnh, chuyển màu, biến đổi hình học (xoay, tăng sáng), resize |
| PyWavelets (`pywt`) | Thực hiện biến đổi Wavelet rời rạc 2D (`pywt.wavedec2`) |
| NumPy | Xử lý mảng ảnh, tính trung vị, lượng tử hóa và tính toán vector |
| Matplotlib | Hiển thị ảnh và vẽ biểu đồ ROC |

## Khái Niệm Quan Trọng

### 1. Perceptual Hashing (Băm Nhận Thức) vs Cryptographic Hashing (Băm Mã Hóa)
- **Băm mã hóa (SHA-256, MD5)**: Một thay đổi cực kỳ nhỏ trên ảnh (ví dụ đổi 1 pixel) cũng làm thay đổi hoàn toàn mã băm.
- **Băm nhận thức (wHash)**: Cho các ảnh nhìn giống nhau (chỉ khác góc nhìn, nhiễu nhẹ) có mã băm tương tự nhau (khoảng cách Hamming nhỏ).

### 2. Biến đổi Wavelet 2D (DWT)
- Phân tích ảnh thành 4 thành phần: `cA` (xấp xỉ - tần số thấp), `cH` (chi tiết ngang - tần số cao), `cV` (chi tiết đứng), `cD` (chi tiết chéo).
- Kỹ thuật **wHash chuẩn** chỉ sử dụng `cA` vì nó lưu giữ cấu trúc chính của ảnh và loại bỏ nhiễu tần số cao.
- Đồng nhất kích thước ảnh trước khi biến đổi DWT là bắt buộc để các mã băm có cùng độ dài.

### 3. Đánh giá chất lượng phân loại
- **Accuracy** (Độ chính xác): Tỷ số giữa số cặp dự đoán đúng trên tổng số cặp.
- **Recall / Sensitivity** (Độ nhạy): Tỷ số giữa số cặp tương tự được phát hiện đúng trên tổng số cặp thực sự tương tự.
- **Specificity** (Độ đặc hiệu): Tỷ số giữa số cặp không tương tự được nhận diện đúng trên tổng số cặp thực sự không tương tự.
- **ROC Curve**: Đồ thị biểu diễn mối quan hệ giữa True Positive Rate (Recall) và False Positive Rate (1 - Specificity) ở các ngưỡng phân loại khác nhau.

## Thứ Tự Chạy
1. Cell 1: Khai báo thư viện và cấu hình chung.
2. Cell 2: Khai báo hàm sinh tập dữ liệu và tải ảnh mẫu.
3. Cell 3: Cài đặt hai thuật toán Wavelet Hash (phương pháp slide và wHash chuẩn).
4. Cell 4: Viết hàm khoảng cách Hamming và tính toán ma trận khoảng cách cho tập dữ liệu.
5. Cell 5: Vẽ biểu đồ ROC và in ra các chỉ số đánh giá.
6. Cell 6: Chạy mô phỏng hệ thống tìm kiếm ảnh.

## Lỗi Thường Gặp
| Lỗi | Nguyên nhân | Cách khắc phục |
|-----|-------------|----------------|
| `ModuleNotFoundError: No module named 'pywt'` | Chưa cài đặt thư viện `PyWavelets` | Chạy lệnh `pip install PyWavelets` |
| `AttributeError: module 'pywt' has no attribute 'quantize'` | Trong slide bài giảng ghi `pywt.quantize` nhưng thư viện chính thức không hỗ trợ | Sử dụng hàm lượng tử hóa tự định nghĩa bằng NumPy |
| `ValueError: Size mismatch / zip length issue` | Các ảnh đầu vào có kích thước khác nhau làm độ dài mã băm thay đổi | Bắt buộc sử dụng `cv2.resize` về một kích thước chuẩn (ví dụ $64 \times 64$) trước khi DWT |
| Lỗi hiển thị tiếng Việt trên đồ thị | Thiếu font hỗ trợ tiếng Việt trong Matplotlib | Sử dụng văn bản không dấu hoặc cài đặt cấu hình font sans-serif mặc định |
