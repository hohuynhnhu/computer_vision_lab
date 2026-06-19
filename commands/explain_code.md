# Lệnh: Giải Thích Code

## Mục Đích
Giải thích code trong notebook hoặc file Python một cách dễ hiểu cho sinh viên.

## Quy Trình

### Bước 1: Xác Định Ngữ Cảnh
- Đọc file notebook `.ipynb` cần giải thích
- Xác định chương, phần, bài tập cụ thể
- Đọc tài liệu liên quan trong `documents/`

### Bước 2: Phân Tích Code
Với mỗi đoạn code, giải thích theo cấu trúc:

```markdown
## [Tên đoạn code / hàm]

### Mục đích
[Mô tả ngắn gọn: đoạn code này làm gì]

### Code
\`\`\`python
[code]
\`\`\`

### Giải thích từng dòng
| Dòng | Code | Giải thích |
|------|------|------------|
| 1    | `img = cv2.imread(...)` | Đọc file ảnh từ đường dẫn, trả về mảng NumPy BGR |
| 2    | `gray = cv2.cvtColor(...)` | Chuyển đổi không gian màu BGR sang Grayscale |
| ...  | ...  | ... |

### Nguyên lý hoạt động
[Giải thích nguyên lý toán học / thuật toán đằng sau]

### Kết quả mong đợi
[Mô tả kết quả đầu ra]

### Lưu ý
- [Các điểm cần chú ý]
- [Lỗi thường gặp]
```

### Bước 3: Các Chủ Đề Cần Giải Thích Chi Tiết

#### Chương 1: Cơ Bản
- **BGR vs RGB**: Tại sao OpenCV dùng BGR, matplotlib dùng RGB
- **Không gian màu**: Khi nào dùng Grayscale, HSV, LAB
- **Resize nội suy**: INTER_LINEAR vs INTER_CUBIC vs INTER_AREA

#### Chương 2: Lọc Ảnh
- **Tích chập (Convolution)**: Công thức và minh họa từng bước
- **Kernel**: Ý nghĩa từng phần tử trong kernel
- **Median vs Gaussian**: Khác biệt tuyến tính/phi tuyến
- **Bilateral Filter**: Cơ chế giữ cạnh

#### Chương 3: Canny
- **5 bước của Canny**: Gaussian → Gradient → Magnitude → NMS → Hysteresis
- **Non-Maximum Suppression**: Cách hoạt động
- **Hysteresis Thresholding**: Vai trò của 2 ngưỡng
- **Sigma vs Threshold**: Ảnh hưởng khác nhau

## Template Trả Lời

```markdown
## [Tên hàm / đoạn code]

### Mục đích
...

### Giải thích
\`\`\`python
# [comment từng dòng]
code
\`\`\`

### Sơ đồ (nếu cần)
\`\`\`
┌──────┐    ┌──────┐    ┌──────┐
│ Input│───▶│ Xử lý│───▶│Output│
└──────┘    └──────┘    └──────┘
\`\`\`

### Câu hỏi thường gặp
- **Q:** Tại sao...?
- **A:** Bởi vì...
```
