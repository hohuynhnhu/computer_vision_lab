# Chương 5: Nhận Diện Khuôn Mặt Thời Gian Thực với FaceNet & MTCNN - Tính Năng

## Phần 1: Truy Cập Webcam

| STT | Tính Năng | Đầu Vào | Đầu Ra | Thư Viện |
|-----|-----------|---------|--------|----------|
| 1 | Mở webcam | Camera index (0) | Đối tượng `VideoCapture` | `cv2.VideoCapture(0)` |
| 2 | Đọc frame liên tục | `VideoCapture` | Frame BGR (480×640) | `cap.read()` |
| 3 | Hiển thị video | Frame BGR | Cửa sổ OpenCV | `cv2.imshow()` |
| 4 | Chụp ảnh từ webcam | Phím `c` | File ảnh lưu vào `dataset/` | `cv2.imwrite()` |
| 5 | Thoát vòng lặp | Phím `q` | Thoát chương trình | `cv2.destroyAllWindows()` |

## Phần 2: Phát Hiện Khuôn Mặt với MTCNN

| STT | Tính Năng | Đầu Vào | Đầu Ra | Phương Pháp |
|-----|-----------|---------|--------|-------------|
| 6 | Khởi tạo MTCNN detector | Thiết bị (CPU/GPU) | Đối tượng `MTCNN` | `MTCNN(keep_all=True, device=device)` |
| 7 | Phát hiện khuôn mặt | Ảnh RGB (PIL hoặc numpy) | List bounding boxes `[x1, y1, x2, y2]` | `detector.detect_faces(img)` |
| 8 | Crop khuôn mặt | Ảnh gốc + bounding box | Ảnh khuôn mặt đã crop | `img[y1:y2, x1:x2]` |
| 9 | Vẽ bounding box | Frame + tọa độ | Frame có khung xanh quanh mặt | `cv2.rectangle(frame, (x1,y1), (x2,y2), (0,255,0), 2)` |
| 10 | Resize khuôn mặt | Ảnh khuôn mặt bất kỳ | Ảnh 160×160 pixels | `cv2.resize(face, (160, 160))` |

## Phần 3: Trích Xuất Đặc Trưng và Nhận Diện với FaceNet

| STT | Tính Năng | Đầu Vào | Đầu Ra | Phương Pháp |
|-----|-----------|---------|--------|-------------|
| 11 | Khởi tạo FaceNet | Pre-trained weights `'vggface2'` | Model `InceptionResnetV1` | `InceptionResnetV1(pretrained='vggface2').eval()` |
| 12 | Tiền xử lý khuôn mặt | Ảnh khuôn mặt numpy | Tensor chuẩn hóa `[-1, 1]` | `(face_tensor - 127.5) / 128.0` |
| 13 | Trích xuất embedding | Tensor khuôn mặt (1,3,160,160) | Vector numpy 512 chiều | `model(face_tensor).detach().numpy()` |
| 14 | Đăng ký tham chiếu | Ảnh từ `dataset/` | Dict `{tên: embedding}` | Lặp qua file, MTCNN → FaceNet → lưu |
| 15 | Tính cosine similarity | 2 vector embedding | Giá trị float [0, 1] | `np.dot(a,b) / (norm(a) * norm(b))` |
| 16 | Phân loại "Matched" | similarity > 0.7 | Nhãn "Matched: &lt;Tên&gt;" | So sánh ngưỡng |
| 17 | Phân loại "Unknown" | similarity ≤ 0.7 | Nhãn "Unknown" | Mặc định nếu không vượt ngưỡng |
| 18 | Hiển thị real-time | Frame + kết quả | Video có nhãn + similarity score | `cv2.putText()` + `cv2.imshow()` |

## Tổng Hợp Tham Số

### Tham Số MTCNN

| Tham Số | Giá Trị | Ý Nghĩa |
|---------|---------|---------|
| `keep_all` | `True` | Giữ tất cả khuôn mặt phát hiện được trong frame |
| `min_face_size` | `40` (mặc định) | Kích thước khuôn mặt tối thiểu (pixels) |
| `thresholds` | `[0.6, 0.7, 0.7]` (mặc định) | Ngưỡng cho 3 stage P-Net, R-Net, O-Net |
| `device` | `'cpu'` hoặc `'cuda'` | Thiết bị chạy suy luận |

### Tham Số FaceNet

| Tham Số | Giá Trị | Ý Nghĩa |
|---------|---------|---------|
| `pretrained` | `'vggface2'` | Bộ trọng số pre-trained (VGGFace2 dataset) |
| `classify` | `False` (mặc định) | Trả về embedding, không phân loại |
| `input_size` | `160` | Kích thước ảnh đầu vào |
| `embedding_dim` | `512` | Số chiều vector đặc trưng đầu ra |

### Tham Số Cosine Similarity

| Tham Số | Giá Trị | Ý Nghĩa |
|---------|---------|---------|
| `threshold` | `0.7` | Ngưỡng phân loại Matched / Unknown |
| `similarity > 0.9` | Rất chắc chắn cùng người | Điều kiện lý tưởng |
| `similarity 0.7–0.9` | Cùng người | Chấp nhận được |
| `similarity < 0.7` | Không cùng người | Hiển thị "Unknown" |

## So Sánh MTCNN và Haar Cascade

| Tiêu Chí | MTCNN | Haar Cascade (OpenCV) |
|----------|-------|----------------------|
| Độ chính xác | Cao, ít false positive | Trung bình, nhiều false positive |
| Phát hiện góc nghiêng | Tốt (đa góc) | Kém (chủ yếu chính diện) |
| Landmarks | 5 điểm (mắt, mũi, miệng) | Không có |
| Tốc độ | Chậm hơn (deep learning) | Nhanh (traditional CV) |
| Yêu cầu phần cứng | Cần GPU cho real-time mượt | Chạy tốt trên CPU |
| Thư viện | `facenet-pytorch` | `cv2.CascadeClassifier` |
