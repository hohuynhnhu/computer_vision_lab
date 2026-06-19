# Lệnh: Tạo Test Case

## Mục Đích
Tạo các test case kiểm thử cho bài tập thực hành Computer Vision.

## Định Dạng

Mỗi test case bao gồm:

```markdown
| Test Case | Dữ Liệu Vào | Tham Số | Kết Quả Mong Đợi | Trạng Thái |
|-----------|-------------|---------|------------------|------------|
| Tên test  | File ảnh    | Tham số | Mô tả kết quả    | ✓ Pass / ✗ Fail |
```

## Các Bước Thực Hiện

### Bước 1: Xác Định Phạm Vi Test
- Đọc file `documents/requirement.md` và `documents/features.md` của chương cần test
- Xác định danh sách các tính năng cần kiểm thử

### Bước 2: Chuẩn Bị Dữ Liệu Test
- Kiểm tra ảnh đầu vào trong `data/input/`
- Nếu cần, tạo ảnh test nhân tạo (nhiễu, tương phản thấp, v.v.)

### Bước 3: Viết Test Case
- Mỗi tính năng có ít nhất 1 test case
- Với hàm có tham số, test ít nhất 2-3 bộ tham số:
  - Tham số mặc định
  - Tham số biên (giá trị cực đại/cực tiểu)
  - Tham số không hợp lệ (kiểm tra xử lý lỗi)

### Bước 4: Xác Định Kết Quả Mong Đợi
- Dựa trên tài liệu `documents/logic_ai.md` và `documents/tech_solution.md`
- Với ảnh output: mô tả định tính (sáng hơn, tối hơn, nhiều cạnh hơn...)

### Bước 5: Ghi Nhận Trạng Thái
- ✓ Pass: Đã test và kết quả đúng như mong đợi
- ✗ Fail: Kết quả không đúng
- ⚠ Pending: Chưa test

## Template

```markdown
# [Tên Chương] - Kiểm Thử

## 1. Ma Trận Kiểm Thử

| Test Case | Dữ Liệu Vào | Kết Quả Mong Đợi | Trạng Thái |
|-----------|-------------|------------------|------------|
|           |             |                  |            |

## 2. Kết Quả Đầu Ra

\`\`\`
outputs/
├── file1.jpg
├── file2.jpg
└── ...
\`\`\`

## 3. Các Lỗi Thường Gặp

| Lỗi | Nguyên Nhân | Cách Khắc Phục |
|-----|-------------|----------------|
|     |             |                |
```

## Loại Test Case Cơ Bản

| Loại Test | Mô Tả | Ví Dụ |
|-----------|-------|-------|
| Happy path | Đầu vào hợp lệ, tham số mặc định | Đọc `anh.jpg`, filter median k=3 |
| Edge case | Đầu vào hợp lệ, tham số biên | Median k=1 (không đổi), k=31 (rất mờ) |
| Error case | Đầu vào/Tham số không hợp lệ | Median k=4 (số chẵn), file không tồn tại |
| Performance | Đo thời gian xử lý | Đo ms cho Canny OpenCV vs Skimage |
| Comparison | So sánh 2 phương pháp | Canny OpenCV vs Skimage cùng 1 ảnh |
