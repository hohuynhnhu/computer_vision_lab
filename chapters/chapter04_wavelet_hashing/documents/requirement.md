# Chương 4: So Sánh Sự Tương Đồng Của Các Hình Ảnh Sử Dụng Wavelet - Yêu Cầu Người Dùng

## 1. Mục Tiêu
Sinh viên cần hiểu rõ và triển khai được phương pháp băm ảnh bằng biến đổi Wavelet (Wavelet Hashing) để so sánh độ tương đồng giữa các ảnh, đồng thời đánh giá hiệu năng phân loại thông qua các chỉ số thống kê và đường cong ROC.

## 2. Yêu Cầu Chi Tiết

| STT | Yêu Cầu | Mô Tả |
|-----|---------|-------|
| 1 | Chuẩn bị dữ liệu kiểm thử | Sinh tập hợp ảnh mẫu gồm các cặp ảnh tương tự (qua xoay ảnh, thêm nhiễu, thay đổi độ sáng) và các cặp ảnh khác biệt. |
| 2 | Trích xuất đặc trưng Wavelet | Áp dụng Discrete Wavelet Transform (DWT) dùng thư viện PyWavelets với các loại wavelet khác nhau (ví dụ: `db4`, `haar`). |
| 3 | Tạo mã băm nhị phân (Hash Code) | Định nghĩa hàm băm wavelet (wHash) thông qua lượng tử hóa hệ số wavelet thành chuỗi nhị phân cố định chiều dài. |
| 4 | Tính toán khoảng cách Hamming | Viết hàm tính khoảng cách Hamming để đo lường độ khác biệt giữa các mã băm ảnh. |
| 5 | Đánh giá hiệu suất | Tính toán Accuracy, Recall, Specificity và vẽ đường cong ROC dựa trên tập dữ liệu đã chuẩn bị. |
| 6 | Tìm kiếm ảnh truy vấn (Nâng cao) | Xây dựng chức năng tìm kiếm hình ảnh tương tự trong cơ sở dữ liệu dựa trên mã băm và khoảng cách Hamming tối thiểu. |

## 3. Đầu Vào
- Tập hợp ảnh mẫu gốc nằm trong thư mục `data/input/`.

## 4. Đầu Ra
- File Jupyter Notebook `wavelet_similarity.ipynb` hoàn chỉnh.
- Biểu đồ đường cong ROC (`roc_curve.png`) được lưu trong thư mục `outputs/`.
- Biểu đồ kết quả tìm kiếm hình ảnh truy vấn (`retrieval_result.png`) được lưu trong thư mục `outputs/`.

## 5. Ràng Buộc
- Sử dụng Python 3.11+ và thư viện PyWavelets (`pywt`).
- Đồng nhất kích thước ảnh trước khi biến đổi DWT để đảm bảo độ dài mã băm đồng bộ.
- Triển khai thuật toán lượng tử hóa tùy chỉnh tương thích với PyWavelets thực tế.
