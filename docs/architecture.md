# Kiến Trúc Dự Án

## 1. Tổng Quan

Dự án **Computer Vision Lab** là hệ thống bài tập thực hành về Thị giác Máy tính (Computer Vision) dành cho sinh viên lớp CN22H. Dự án được tổ chức theo cấu trúc chapter (chương), mỗi chương tập trung vào một chủ đề chính của Xử lý Ảnh.

## 2. Cấu Trúc Thư Mục

```
lab/
├── README.md                          # Tổng quan dự án
├── chapters/                          # Nội dung bài tập (3 chương)
│   ├── chapter01_image_fundamentals/  # Chương 1: Cơ bản về ảnh
│   ├── chapter02_image_filtering/     # Chương 2: Lọc ảnh
│   └── chapter03_edge_detection/      # Chương 3: Phát hiện cạnh
├── docs/                              # Tài liệu dự án
│   ├── architecture.md               # Kiến trúc (file này)
│   ├── conventions.md                # Quy ước code
│   ├── development_plan.md           # Kế hoạch phát triển
│   ├── discussion.md                 # Thảo luận
│   └── project.md                    # Mô tả dự án
├── commands/                          # Định nghĩa lệnh agent
│   ├── create_testcase.md
│   ├── debug_notebook.md
│   ├── explain_code.md
│   ├── generate_report.md
│   └── solve_lab.md
├── knowledge_base/                    # Cơ sở tri thức
│   └── glossary.md                   # Thuật ngữ
├── memory/                            # Bộ nhớ agent (lessons learned)
│   ├── best_practices.md
│   ├── common_mistakes.md
│   ├── lab_history.md
│   └── lessons_learned.md
└── lab1/                              # Thư mục bài tập dự phòng
```

## 3. Cấu Trúc Mỗi Chapter

```
chapterXX_topic_name/
├── agent_context.md          # Ngữ cảnh cho AI agent
├── README.md                 # Mô tả chương
├── data/
│   └── input/                # Ảnh đầu vào
├── documents/                # Tài liệu thiết kế
│   ├── requirement.md        # Yêu cầu người dùng
│   ├── features.md           # Tính năng
│   ├── tech_solution.md      # Giải pháp kỹ thuật
│   ├── logic_ai.md           # Logic & AI
│   ├── implementation.md     # Triển khai
│   └── testing.md            # Kiểm thử
├── notebook/                 # Jupyter Notebooks
├── notes/                    # Ghi chú chi tiết (tiếng Việt)
├── images/                   # Ảnh kết quả (Chương 1)
└── outputs/                  # Ảnh đầu ra (Chương 2, 3)
```

## 4. Quy Trình Xử Lý

```
┌──────────┐    ┌──────────────┐    ┌──────────────┐    ┌──────────┐
│ Ảnh đầu  │───▶│ Tiền xử lý   │───▶│ Thuật toán   │───▶│ Kết quả  │
│ vào      │    │ (resize,     │    │ chính        │    │ + hiển thị│
│          │    │  color conv) │    │ (lọc, Canny) │    │          │
└──────────┘    └──────────────┘    └──────────────┘    └──────────┘
```

## 5. Công Nghệ Sử Dụng

| Công Nghệ | Mục Đích |
|-----------|----------|
| Python 3.11 | Ngôn ngữ chính |
| OpenCV 4.11 | Xử lý ảnh (đọc/ghi, lọc, Canny, contour) |
| NumPy 1.26 | Xử lý mảng, tính toán số học |
| Matplotlib | Hiển thị ảnh và biểu đồ |
| Pillow 12.0 | Hỗ trợ đọc/ghi ảnh đa định dạng |
| Scikit-image | Phát hiện cạnh Canny (thư viện thứ hai) |
| Jupyter Notebook | Môi trường thực hành |
