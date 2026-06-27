# Chương 5: Nhận Diện Khuôn Mặt Thời Gian Thực với FaceNet & MTCNN - Yêu Cầu Người Dùng

## 1. Mục Tiêu

Sinh viên cần hiểu và thực hành nhận diện khuôn mặt thời gian thực qua webcam, sử dụng MTCNN để phát hiện khuôn mặt và FaceNet để trích xuất đặc trưng (embedding). Sinh viên sẽ xây dựng hệ thống có khả năng: đăng ký khuôn mặt tham chiếu, phát hiện khuôn mặt trong luồng video webcam, trích xuất embedding, và so sánh để nhận diện "Matched" hoặc "Unknown".

## 2. Yêu Cầu Chi Tiết

### Phần 1: Truy Cập và Thu Thập Dữ Liệu Webcam

| STT | Yêu Cầu | Mô Tả |
|-----|---------|-------|
| 1 | Mở webcam | Dùng `cv2.VideoCapture(0)` để mở webcam mặc định |
| 2 | Hiển thị luồng video | Hiển thị frame liên tục từ webcam đến khi nhấn `q` |
| 3 | Chụp ảnh tham chiếu | Nhấn `c` để chụp và lưu ảnh vào `dataset/` |

### Phần 2: Phát Hiện Khuôn Mặt với MTCNN

| STT | Yêu Cầu | Mô Tả |
|-----|---------|-------|
| 4 | Khởi tạo MTCNN | Sử dụng `facenet_pytorch.MTCNN(keep_all=True)` |
| 5 | Phát hiện khuôn mặt | Phát hiện tất cả khuôn mặt trong frame, trả về bounding boxes |
| 6 | Vẽ khung khuôn mặt | Vẽ rectangle xanh lá quanh mỗi khuôn mặt phát hiện được |

### Phần 3: Trích Xuất Đặc Trưng và Nhận Diện

| STT | Yêu Cầu | Mô Tả |
|-----|---------|-------|
| 7 | Khởi tạo FaceNet | `InceptionResnetV1(pretrained='vggface2').eval()` |
| 8 | Trích xuất embedding | Biến đổi khuôn mặt thành vector đặc trưng 512 chiều |
| 9 | Đăng ký khuôn mặt tham chiếu | Đọc ảnh từ `dataset/`, trích xuất embedding, lưu vào dictionary |
| 10 | Tính cosine similarity | So sánh embedding real-time với từng embedding tham chiếu |
| 11 | Phân loại kết quả | similarity > 0.7 → hiển thị "Matched: &lt;Tên&gt;", similarity ≤ 0.7 → hiển thị "Unknown" |
| 12 | Hiển thị thời gian thực | Vẽ bounding box + nhãn + similarity score lên frame webcam |

## 3. Đầu Vào

- **Webcam**: Camera mặc định của máy tính (index 0)
- **Ảnh tham chiếu**: Các file ảnh `.jpg` hoặc `.png` trong thư mục `dataset/`, mỗi ảnh chứa một khuôn mặt của một người. Tên file (không bao gồm phần mở rộng) được dùng làm tên người.

Ví dụ:
```
dataset/
├── nguyen_van_a.jpg
├── tran_thi_b.jpg
└── le_van_c.png
```

## 4. Đầu Ra

```
outputs/
├── recognition_result_01.png    (ảnh chụp màn hình kết quả nhận diện)
├── recognition_result_02.png    (ảnh chụp với "Matched")
└── recognition_result_03.png    (ảnh chụp với "Unknown")
```

## 5. Ràng Buộc

- Sử dụng `facenet-pytorch` cho MTCNN và FaceNet (InceptionResnetV1)
- FaceNet yêu cầu ảnh khuôn mặt đầu vào có kích thước **160×160** pixels
- MTCNN yêu cầu ảnh đầu vào là **RGB** (không phải BGR của OpenCV)
- Ngưỡng cosine similarity mặc định: **0.7**
- Embedding FaceNet là vector **512 chiều**, cần chuẩn hóa trước khi so sánh
- Ít nhất **1 ảnh tham chiếu** trong `dataset/` để hệ thống có thể so sánh
- Webcam phải hoạt động bình thường, đủ ánh sáng để MTCNN phát hiện khuôn mặt
- Sử dụng `torch.no_grad()` khi suy luận để tiết kiệm bộ nhớ và tăng tốc

## 6. Lỗi Thường Gặp

| Lỗi | Nguyên Nhân | Cách Khắc Phục |
|-----|-------------|----------------|
| `ModuleNotFoundError: No module named 'facenet_pytorch'` | Chưa cài đặt thư viện | `pip install facenet-pytorch torch` |
| `cv2.error: (-215) !_src.empty()` | Webcam không mở được | Kiểm tra camera, thử `cv2.VideoCapture(1)` |
| MTCNN không phát hiện khuôn mặt | Thiếu sáng hoặc góc mặt quá nghiêng | Đảm bảo ánh sáng tốt, nhìn thẳng camera |
| `RuntimeError: Expected 4-dimensional input` | Tensor sai shape khi đưa vào FaceNet | Thêm batch dimension: `face_tensor.unsqueeze(0)` |
| Màu hiển thị sai (da xanh) | Quên chuyển BGR sang RGB | Dùng `cv2.cvtColor(frame, cv2.COLOR_BGR2RGB)` |
| Similarity luôn rất thấp | Embedding chưa chuẩn hóa | Dùng `np.linalg.norm` chuẩn hóa vector |
| `dataset/` rỗng, không có ảnh tham chiếu | Chưa chụp ảnh tham chiếu | Chạy cell chụp ảnh từ webcam trước |
