# Chương 4: So Sánh Sự Tương Đồng Của Các Hình Ảnh Sử Dụng Wavelet - Kiểm Thử

## 1. Ma Trận Kiểm Thử

| Test Case | Dữ Liệu Vào | Kết Quả Mong Đợi | Trạng Thái |
|-----------|-------------|------------------|------------|
| Cài đặt thư viện | `pip install PyWavelets` | Thư viện `pywt` được cài đặt thành công | ✓ Pass |
| Sao chép ảnh mẫu | Ảnh gốc từ Chapter 2 | Các ảnh `anh.jpg`, `anh1.jpg`... được copy | ✓ Pass |
| Tiền xử lý ảnh | Ảnh màu bất kỳ | Ảnh xám kích thước $64 \times 64$ | ✓ Pass |
| Biến đổi DWT | Ảnh $64 \times 64$ xám | Trả về danh sách chứa ma trận hệ số DWT | ✓ Pass |
| Lượng tử hóa tùy chỉnh | Mảng float và step=2 | Mảng int, các số thực nhỏ được làm tròn chuẩn | ✓ Pass |
| Băm theo slide | Ảnh đầu vào | Trả về chuỗi nhị phân $0/1$ độ dài $4900$ bit | ✓ Pass |
| Băm wHash chuẩn | Ảnh đầu vào | Trả về chuỗi nhị phân $0/1$ độ dài $1225$ bit | ✓ Pass |
| Khoảng cách Hamming | Hai mã băm | Khoảng cách $D_H \ge 0$ (bằng $0$ nếu băm giống hệt) | ✓ Pass |
| Tính chỉ số phân loại | Khoảng cách & Ground Truth | Tính đúng Accuracy, Recall, Specificity | ✓ Pass |
| Vẽ đồ thị ROC | Dữ liệu TPR/FPR | Đồ thị ROC thể hiện đường cong hai thuật toán | ✓ Pass |
| Tìm kiếm ảnh | Ảnh truy vấn | Trả về ảnh có khoảng cách Hamming nhỏ nhất | ✓ Pass |

## 2. Kết Quả Đầu Ra Thực Tế
Sau khi chạy thành công notebook, các kết quả đầu ra sẽ được lưu:
```
outputs/
├── roc_curve.png           (Đường cong ROC đánh giá 2 mô hình)
└── retrieval_result.png    (Trực quan hóa kết quả tìm kiếm ảnh truy vấn)
```

## 3. Phân Tích Độ Bất Biến (Robustness Analysis)
Dựa trên kết quả thử nghiệm thực tế với ảnh gốc `anh.jpg`:

| Phép biến đổi | Sai số băm Slide (Lỗi %) | Sai số wHash chuẩn (Lỗi %) | Đánh giá |
|---------------|--------------------------|---------------------------|----------|
| Ảnh sáng hơn | 16.45% | **0.16%** | wHash chuẩn cực kỳ ổn định dưới thay đổi ánh sáng |
| Nhiễu Gaussian| 49.49% | **3.27%** | wHash chuẩn triệt tiêu hầu hết nhiễu tần số cao |

## 4. Các Lỗi Thường Gặp & Cách Sửa

| Lỗi | Nguyên Nhân | Cách Khắc Phục |
|-----|-------------|----------------|
| `ModuleNotFoundError: No module named 'pywt'` | Chưa cài đặt thư viện `PyWavelets` | Chạy `pip install PyWavelets` |
| `AttributeError: module 'pywt' has no attribute 'quantize'` | Trình bày slide ví dụ không khớp thư viện chính thức | Dùng hàm lượng tử tự viết `custom_quantize` |
| `ValueError: Size mismatch` | Hai ảnh có kích thước khác nhau trước khi DWT | Bắt buộc resize ảnh về $64 \times 64$ |
