# Kế Hoạch Phát Triển

## 1. Giai Đoạn 1: Hoàn Thiện (Hiện Tại)

### Chương 1: Cơ Bản Về Ảnh ✓
- [x] Notebook `chapter01.ipynb` hoàn chỉnh
- [x] Output images: `lab1_saved.png`, `lab1_pillow.bmp`, `result_drawing.png`
- [x] Tài liệu documents/ đầy đủ 6 file

### Chương 2: Lọc Ảnh ✓
- [x] 3 notebook: point_operations, linear_filters, advanced_filters
- [x] 14 ảnh output trong 3 thư mục con
- [x] Tài liệu documents/ đầy đủ 6 file
- [x] Notes chi tiết cho Part II và Part III

### Chương 3: Phát Hiện Cạnh ✓
- [x] 4 notebook: canny_opencv, parameter_analysis, image_evaluation, integration_experiment
- [x] 7 ảnh output
- [x] Tài liệu documents/ đầy đủ 6 file
- [x] Notes chi tiết cho 4 phần

## 2. Giai Đoạn 2: Mở Rộng (Dự Kiến)

### Chương 4: Phát Hiện Đối Tượng
- [ ] Haar Cascade
- [ ] HOG + SVM
- [ ] Template Matching

### Chương 5: Trích Xuất Đặc Trưng
- [ ] SIFT, SURF, ORB
- [ ] Feature Matching
- [ ] Image Stitching

### Chương 6: Deep Learning trong Computer Vision
- [ ] CNN cơ bản
- [ ] Transfer Learning
- [ ] YOLO/SSD cho Object Detection

## 3. Cải Thiện Hạ Tầng

| Hạng Mục | Mức Độ Ưu Tiên | Trạng Thái |
|----------|---------------|------------|
| Điền đầy đủ documents/ cho tất cả chapter | Cao | ✓ Hoàn thành |
| Viết commands/ cho agent | Cao | Đang thực hiện |
| Viết docs/ (architecture, conventions, etc.) | Cao | Đang thực hiện |
| Viết knowledge_base/glossary.md | Trung bình | Đang thực hiện |
| Viết memory/ cho agent | Trung bình | Đang thực hiện |
| Thêm README.md cho từng chapter | Thấp | Cần làm |
| Tạo requirements.txt / environment.yml | Trung bình | Cần làm |
| Thêm unit test cho các hàm lọc | Thấp | Cần làm |

## 4. Quy Trình Phát Triển Chapter Mới

```
1. Tạo thư mục chapterXX_topic_name/
2. Tạo cấu trúc con:
   ├── agent_context.md
   ├── README.md
   ├── data/input/
   ├── documents/ (6 file mẫu)
   ├── notebook/
   ├── notes/
   └── outputs/
3. Viết notebook với markdown rõ ràng
4. Kiểm tra kết quả output
5. Viết tài liệu documents/
6. Viết notes/ (ghi chú chi tiết tiếng Việt)
7. Cập nhật README.md gốc và docs/
```

## 5. Lộ Trình Thời Gian

| Giai Đoạn | Thời Gian Dự Kiến | Nội Dung |
|-----------|-------------------|----------|
| GĐ1 | Q2/2025 | Hoàn thiện Chương 1-3 + hạ tầng tài liệu |
| GĐ2 | Q3/2025 | Chương 4-6 + cải thiện hạ tầng |
| GĐ3 | Q4/2025 | Unit test, CI/CD, tối ưu hóa |
