# Lab 2: Xử Lý Ảnh Cơ Bản & Lọc Ảnh

Tài liệu báo cáo được chia thành 3 phần nhỏ. Xem chi tiết tại:

| Phần | Nội dung | Notebook | Báo cáo |
|------|----------|----------|---------|
| **Part I** | Biến đổi điểm ảnh (Point Operations) | [`src/part1_point_ops.ipynb`](src/part1_point_ops.ipynb) | [`src/part1_point_ops.md`](src/part1_point_ops.md) |
| **Part II** | Lọc tuyến tính (Linear Filtering) | [`src/part2_linear_filters.ipynb`](src/part2_linear_filters.ipynb) | [`src/part2_linear_filters.md`](src/part2_linear_filters.md) |
| **Part III** | Nâng cao (Advanced Filters) | [`src/part3_advanced.ipynb`](src/part3_advanced.ipynb) | [`src/part3_advanced.md`](src/part3_advanced.md) |

## Cấu trúc thư mục

```
lab2/
├── input/
│   └── anh.jpg                    # Ảnh test đầu vào
├── output/                        # Tự động tạo khi chạy
│   ├── part1_point_ops/           # 6 ảnh kết quả
│   ├── part2_linear_filters/      # 3 ảnh kết quả
│   └── part3_advanced/            # 5 ảnh kết quả
├── src/
│   ├── part1_point_ops.ipynb      # Notebook Part I (tự chứa code)
│   ├── part1_point_ops.md         # Báo cáo Part I
│   ├── part2_linear_filters.ipynb # Notebook Part II (tự chứa code)
│   ├── part2_linear_filters.md    # Báo cáo Part II
│   ├── part3_advanced.ipynb       # Notebook Part III (tự chứa code)
│   └── part3_advanced.md          # Báo cáo Part III
├── lab2.ipynb                     # Notebook gốc (đầy đủ 3 phần)
├── lab2_notebook.ipynb            # Notebook sạch (import from src)
├── lab2_report.md                 # File này - index tổng
├── logs/
│   └── agent_log.md               # Nhật ký phát triển
└── README.md                      # Hướng dẫn cài đặt & sử dụng
```

## Cách sử dụng

1. Mở từng notebook trong `src/` bằng Jupyter hoặc VS Code
2. Chạy **Run All Cells** để thực thi toàn bộ
3. Kết quả được lưu vào `output/part{1,2,3}_*/`
