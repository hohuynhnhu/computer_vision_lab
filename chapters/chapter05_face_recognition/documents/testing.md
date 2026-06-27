# Chương 5: Nhận Diện Khuôn Mặt Thời Gian Thực với FaceNet & MTCNN - Kiểm Thử

## 1. Ma Trận Kiểm Thử

### Phần 1: Truy Cập Webcam

| Test Case | Dữ Liệu Vào | Kết Quả Mong Đợi | Trạng Thái |
|-----------|-------------|------------------|------------|
| Mở webcam index 0 | `cv2.VideoCapture(0)` | `cap.isOpened() == True` | ✓ Pass |
| Đọc frame từ webcam | `cap.read()` | `ret == True`, frame không rỗng | ✓ Pass |
| Hiển thị video | `cv2.imshow('test', frame)` | Cửa sổ hiển thị video | ✓ Pass |
| Chụp ảnh với phím 'c' | Nhấn 'c' | File ảnh được lưu vào `dataset/` | ✓ Pass |
| Thoát với phím 'q' | Nhấn 'q' | Webcam đóng, cửa sổ biến mất | ✓ Pass |

### Phần 2: Khởi Tạo MTCNN và FaceNet

| Test Case | Dữ Liệu Vào | Kết Quả Mong Đợi | Trạng Thái |
|-----------|-------------|------------------|------------|
| Khởi tạo MTCNN | `MTCNN(keep_all=True)` | Đối tượng MTCNN được tạo | ✓ Pass |
| Khởi tạo FaceNet | `InceptionResnetV1(pretrained='vggface2')` | Model được tải về | ✓ Pass |
| FaceNet ở chế độ eval | `model.eval()` | Model không training | ✓ Pass |
| Kiểm tra device | `torch.device(...)` | 'cpu' hoặc 'cuda' | ✓ Pass |

### Phần 3: Phát Hiện Khuôn Mặt

| Test Case | Dữ Liệu Vào | Kết Quả Mong Đợi | Trạng Thái |
|-----------|-------------|------------------|------------|
| Phát hiện 1 khuôn mặt | Ảnh có 1 người | `boxes.shape == (1, 4)` | ✓ Pass |
| Phát hiện nhiều khuôn mặt | Ảnh có N người | `boxes.shape == (N, 4)` | ✓ Pass |
| Không có khuôn mặt | Ảnh không có người | `boxes is None` | ✓ Pass |
| Tọa độ bounding box hợp lệ | Box bất kỳ | `x1 < x2`, `y1 < y2`, trong khoảng [0, w/h] | ✓ Pass |

### Phần 4: Trích Xuất Embedding

| Test Case | Dữ Liệu Vào | Kết Quả Mong Đợi | Trạng Thái |
|-----------|-------------|------------------|------------|
| Embedding shape | Khuôn mặt 160×160×3 | `(512,)` | ✓ Pass |
| Chuẩn hóa L2 | Vector embedding | `np.linalg.norm(emb) ≈ 0.999–1.001` | ✓ Pass |
| Cùng người (similarity) | 2 ảnh cùng người | similarity > 0.7 | ✓ Pass |
| Khác người (similarity) | 2 ảnh khác người | similarity < 0.7 | ✓ Pass |
| Không có gradient | Trong `torch.no_grad()` | Không có `.grad` | ✓ Pass |

### Phần 5: Đăng Ký Tham Chiếu

| Test Case | Dữ Liệu Vào | Kết Quả Mong Đợi | Trạng Thái |
|-----------|-------------|------------------|------------|
| Dataset có ảnh | `dataset/nguoi_a.jpg` | Dict có key 'nguoi_a' | ✓ Pass |
| Dataset rỗng | `dataset/` không có ảnh | Dict rỗng, in cảnh báo | ✓ Pass |
| Ảnh không có khuôn mặt | Ảnh phong cảnh | Bỏ qua, in cảnh báo | ✓ Pass |
| Nhiều ảnh trong dataset | N ảnh hợp lệ | Dict có N entries | ✓ Pass |

### Phần 6: So Khớp và Nhận Diện

| Test Case | Similarity | Ngưỡng | Kết Quả Mong Đợi |
|-----------|-----------|--------|------------------|
| Giống hoàn toàn | 0.99 | 0.7 | "Matched: &lt;Tên&gt;" |
| Giống | 0.75 | 0.7 | "Matched: &lt;Tên&gt;" |
| Ngưỡng ranh giới (trên) | 0.71 | 0.7 | "Matched: &lt;Tên&gt;" |
| Ngưỡng ranh giới (dưới) | 0.69 | 0.7 | "Unknown" |
| Khác hoàn toàn | 0.20 | 0.7 | "Unknown" |

### Phần 7: Nhận Diện Thời Gian Thực

| Test Case | Kết Quả Mong Đợi | Trạng Thái |
|-----------|-----------------|------------|
| Webcam mở thành công | Cửa sổ video hiển thị | ✓ Pass |
| Phát hiện khuôn mặt đã đăng ký | Bounding box xanh + "Matched: Tên" | ✓ Pass |
| Phát hiện khuôn mặt lạ | Bounding box đỏ + "Unknown" | ✓ Pass |
| Không có khuôn mặt trong frame | Frame hiển thị bình thường | ✓ Pass |
| Thoát bằng phím 'q' | Webcam đóng, in tổng kết | ✓ Pass |
| Nhiều khuôn mặt cùng lúc | Mỗi mặt được xử lý độc lập | ✓ Pass |

## 2. Kết Quả Đầu Ra

```
outputs/
├── reference_faces.png              (~200 KB) — Ảnh tổng hợp các mặt đã đăng ký
├── recognition_result_01.png        (~150 KB) — Chụp màn hình "Matched"
├── recognition_result_02.png        (~150 KB) — Chụp màn hình "Unknown"
└── recognition_result_03.png        (~150 KB) — Chụp màn hình nhiều khuôn mặt
```

## 3. Báo Cáo Tổng Kết

### So Sánh MTCNN và Haar Cascade

| Tiêu Chí | MTCNN | Haar Cascade |
|----------|-------|-------------|
| Độ chính xác | Cao (~95%) | Trung bình (~80%) |
| False positive | Thấp | Cao |
| Góc nghiêng | Hỗ trợ (±45°) | Chủ yếu chính diện |
| Landmarks | 5 điểm | Không có |
| Tốc độ (CPU) | ~200ms/frame | ~50ms/frame |
| Tốc độ (GPU) | ~30ms/frame | N/A |

### Hiệu Năng FaceNet

| Chỉ Số | Giá Trị |
|--------|---------|
| Kích thước embedding | 512 chiều |
| Thời gian trích xuất (CPU) | ~50–100ms / khuôn mặt |
| Thời gian trích xuất (GPU) | ~5–10ms / khuôn mặt |
| Độ chính xác (VGGFace2) | ~99.6% |
| Dung lượng model | ~100 MB |

### Ngưỡng Similarity Khuyến Nghị

| Ứng Dụng | Ngưỡng | Lý Do |
|----------|--------|-------|
| Bảo mật / Xác thực | 0.85 – 0.95 | Cần độ chính xác cao, tránh false positive |
| Nhận diện thông thường | 0.70 – 0.85 | Cân bằng giữa recall và precision ✓ |
| Tìm kiếm / Gợi ý | 0.50 – 0.70 | Ưu tiên recall, chấp nhận false positive |

## 4. Các Lỗi Thường Gặp

| Lỗi | Nguyên Nhân | Cách Khắc Phục |
|-----|-------------|----------------|
| `ModuleNotFoundError: facenet_pytorch` | Chưa cài thư viện | `pip install facenet-pytorch` |
| `ModuleNotFoundError: torch` | Chưa cài PyTorch | `pip install torch` (CPU: `torch==2.0.1`) |
| Webcam không mở được | Camera bị chiếm dụng / sai index | Thử `cv2.VideoCapture(1)`, đóng ứng dụng khác |
| `RuntimeError: shape '[...]' is invalid` | Tensor sai kích thước | Kiểm tra face resize đúng 160×160 |
| MTCNN không phát hiện khuôn mặt | Thiếu sáng, góc quá nghiêng, khoảng cách xa | Đảm bảo ánh sáng tốt, nhìn thẳng, gần camera |
| FaceNet tải model thất bại | Không có internet lần đầu | Kiểm tra kết nối mạng, model ~100MB tự tải |
| `CUDA out of memory` | GPU không đủ VRAM | Chuyển sang CPU: `device='cpu'` |
| Similarity không chính xác | Embedding chưa chuẩn hóa L2 | Kiểm tra `np.linalg.norm(emb)` |
| Màu hiển thị sai | BGR hiển thị bằng matplotlib | Dùng `cv2.cvtColor(img, cv2.COLOR_BGR2RGB)` |
| `ValueError: not enough values to unpack` | `cap.read()` trả về `(False, None)` | Kiểm tra webcam, thêm `if not ret: break` |
| `dataset/` trống, không nhận diện được | Chưa đăng ký khuôn mặt tham chiếu | Chạy cell `capture_reference_images()` |
| `PermissionError` khi lưu ảnh | Không có quyền ghi thư mục | Kiểm tra quyền thư mục `dataset/` và `outputs/` |
