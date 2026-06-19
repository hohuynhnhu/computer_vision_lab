# Lệnh: Tạo Báo Cáo

## Mục Đích
Tạo báo cáo tổng kết cho một chương hoặc toàn bộ bài tập.

## Quy Trình

### Bước 1: Thu Thập Dữ Liệu

Đọc các file trong chương tương ứng:
- `documents/requirement.md` → yêu cầu ban đầu
- `documents/features.md` → danh sách tính năng
- `documents/testing.md` → kết quả kiểm thử
- `notebook/*.ipynb` → code thực tế
- `outputs/` → ảnh kết quả

### Bước 2: Tạo Báo Cáo

Cấu trúc báo cáo:

```markdown
# Báo Cáo [Tên Chương]
**Lớp:** CN22H
**Ngày:** [date]

---

## 1. Tổng Quan
[Mô tả ngắn gọn nội dung chương]

## 2. Nội Dung Thực Hiện

| STT | Bài Tập | Phương Pháp | Output | Trạng Thái |
|-----|---------|-------------|--------|------------|
| 1   | ...     | ...         | ...    | ✓ / ✗      |

## 3. Kết Quả

### 3.1 Ảnh Output
[Hình ảnh minh họa hoặc mô tả]

### 3.2 Số Liệu
| Chỉ Số | Giá Trị |
|--------|---------|
| Số ảnh input  | N |
| Số ảnh output | M |
| Thời gian TB  | X ms |

## 4. Nhận Xét

### 4.1 Điểm Mạnh
- ...

### 4.2 Hạn Chế
- ...

### 4.3 So Sánh (nếu có)
| Tiêu Chí | Phương Pháp A | Phương Pháp B |
|----------|---------------|---------------|
| ...      | ...           | ...           |

## 5. Kết Luận
[Tổng kết và bài học rút ra]

## 6. Phụ Lục
- File notebook: ...
- File output: ...
```

### Bước 3: Báo Cáo Riêng Cho Từng Chương

#### Chương 1: Cơ Bản Về Ảnh
- Bảng tổng hợp không gian màu
- Kích thước ảnh trước/sau crop, resize
- Hình vẽ và văn bản đã thêm

#### Chương 2: Lọc Ảnh
- So sánh hiệu ứng các bộ lọc
- Bảng kernel và công dụng
- Đánh giá chất lượng ảnh sau lọc

#### Chương 3: Phát Hiện Cạnh
- So sánh OpenCV vs Scikit-image (số cạnh, thời gian)
- Ảnh hưởng của từng tham số (biểu đồ nếu có)
- Kết quả trên các loại ảnh khác nhau
- Bảng phân loại hình dạng

## Template Báo Cáo Tổng Kết

```markdown
# Báo Cáo Tổng Kết - Computer Vision Lab

## Chương 1: Cơ Bản Về Ảnh
- Số bài tập: 5
- Tất cả ✓ Pass

## Chương 2: Lọc Ảnh
- Số bài tập: 11
- Tất cả ✓ Pass

## Chương 3: Phát Hiện Cạnh
- Số bài tập: 12
- Tất cả ✓ Pass

## Tổng Kết
- Tổng số bài: 28
- Đạt: 28/28
- Kỹ thuật: OpenCV, NumPy, Matplotlib, Pillow, Scikit-image
```
