# Chapter 05: Nhận Diện Khuôn Mặt Thời Gian Thực với FaceNet & MTCNN — Agent Context

## Tổng Quan
Chương này tập trung vào **nhận diện khuôn mặt thời gian thực qua webcam** sử dụng hai mô hình deep learning hiện đại: **MTCNN** (Multi-task Cascaded Convolutional Networks) để phát hiện khuôn mặt và **FaceNet** (InceptionResnetV1) để trích xuất đặc trưng và so sánh khuôn mặt. Sinh viên thực hành truy cập webcam bằng OpenCV, tích hợp MTCNN phát hiện khuôn mặt, dùng FaceNet trích xuất embedding, và so sánh với embedding tham chiếu đã đăng ký trước để nhận diện theo thời gian thực.

## Cấu Trúc
- **1 Notebook**: `chapter05_face_recognition.ipynb` (toàn bộ bài tập trong 1 notebook)
- **Documents**: `requirement.md`, `features.md`, `tech_solution.md`, `logic_ai.md`, `implementation.md`, `testing.md`
- **Dataset**: `dataset/` — chứa ảnh tham chiếu dùng để đăng ký khuôn mặt
- **Models**: `models/` — thư mục lưu trữ pre-trained models (tự động tải về)
- **Output**: `outputs/` — ảnh chụp màn hình kết quả nhận diện

## 3 Phần Chính

### Phần 1: Truy Cập Webcam với OpenCV
| # | Bài | Phương pháp | Ghi chú |
|---|-----|-------------|---------|
| 1 | Mở webcam và hiển thị luồng video | `cv2.VideoCapture(0)` | Kiểm tra webcam hoạt động |
| 2 | Chụp ảnh từ webcam | Phím `c` để capture, `q` để thoát | Lưu ảnh tham chiếu vào `dataset/` |

### Phần 2: Phát Hiện Khuôn Mặt với MTCNN
| # | Bài | Phương pháp | Ghi chú |
|---|-----|-------------|---------|
| 3 | Khởi tạo MTCNN detector | `MTCNN(keep_all=True)` | Giữ tất cả khuôn mặt trong khung hình |
| 4 | Phát hiện khuôn mặt trên ảnh | `detector.detect_faces(img)` | Trả về bounding box + landmarks |
| 5 | Vẽ bounding box lên ảnh | `cv2.rectangle()` | Hiển thị khung xanh quanh khuôn mặt |

### Phần 3: Trích Xuất Đặc Trưng và Nhận Diện với FaceNet
| # | Bài | Phương pháp | Ghi chú |
|---|-----|-------------|---------|
| 6 | Khởi tạo FaceNet (InceptionResnetV1) | `InceptionResnetV1(pretrained='vggface2')` | Pre-trained trên VGGFace2 |
| 7 | Trích xuất embedding từ khuôn mặt | `model(face_tensor)` | Vector 512 chiều |
| 8 | Đăng ký khuôn mặt tham chiếu | Từ ảnh trong `dataset/` → embedding | Lưu vào biến `reference_embeddings` |
| 9 | So sánh embedding real-time | Cosine similarity | `> 0.7` → "Matched", `< 0.7` → "Unknown" |
| 10 | Nhận diện thời gian thực | Vòng lặp webcam + MTCNN + FaceNet | Hiển thị tên + similarity score |

## Công Nghệ Sử Dụng

| Công Nghệ | Vai Trò |
|-----------|---------|
| OpenCV (`cv2`) | Truy cập webcam, vẽ bounding box, hiển thị video |
| MTCNN (`facenet_pytorch`) | Phát hiện khuôn mặt với bounding box + 5 landmarks |
| FaceNet / InceptionResnetV1 | Trích xuất embedding 512 chiều từ khuôn mặt |
| PyTorch (`torch`) | Backend cho MTCNN và FaceNet |
| NumPy | Tính cosine similarity, xử lý mảng |
| Matplotlib | Hiển thị ảnh kết quả (chụp màn hình) |
| PIL (`PIL.Image`) | Chuyển đổi định dạng ảnh cho MTCNN |
| `scipy.spatial.distance.cosine` | Tính cosine distance (tùy chọn, có thể dùng numpy) |

## Kiến Trúc Xử Lý

```
┌──────────────────────────────────────────────────────────────┐
│                    GIAI ĐOẠN 1: ĐĂNG KÝ                       │
│                                                              │
│  Ảnh tham chiếu (dataset/)                                   │
│       │                                                      │
│       ▼                                                      │
│  MTCNN → Phát hiện khuôn mặt → Crop khuôn mặt                │
│       │                                                      │
│       ▼                                                      │
│  FaceNet → Embedding (512 chiều)                              │
│       │                                                      │
│       ▼                                                      │
│  Lưu vào reference_embeddings dict { "Tên": embedding }       │
└──────────────────────────────────────────────────────────────┘

┌──────────────────────────────────────────────────────────────┐
│                  GIAI ĐOẠN 2: NHẬN DIỆN                       │
│                                                              │
│  Webcam (cv2.VideoCapture)                                   │
│       │                                                      │
│       ▼                                                      │
│  Đọc frame (BGR) → Chuyển sang RGB                           │
│       │                                                      │
│       ▼                                                      │
│  MTCNN → Phát hiện khuôn mặt → bounding boxes                │
│       │                                                      │
│       ▼                                                      │
│  Với mỗi khuôn mặt:                                          │
│    1. Crop khuôn mặt từ frame                                │
│    2. Resize về 160×160                                      │
│    3. FaceNet → embedding (512 chiều)                        │
│    4. Tính cosine similarity với từng reference_embedding    │
│    5. similarity > 0.7 → "Matched: <Tên>"                    │
│       similarity < 0.7 → "Unknown"                           │
│    6. Vẽ bounding box + nhãn lên frame                       │
│       │                                                      │
│       ▼                                                      │
│  Hiển thị frame với kết quả nhận diện                        │
│  Nhấn 'q' để thoát                                           │
└──────────────────────────────────────────────────────────────┘
```

## Công Thức Cosine Similarity

$$\text{similarity}(A, B) = \frac{A \cdot B}{\|A\| \cdot \|B\|}$$

Trong đó $A$, $B$ là các embedding vector 512 chiều.
- similarity = 1: hai vector giống hệt nhau
- similarity = 0: hai vector trực giao (không liên quan)
- similarity > 0.7: cùng một người (ngưỡng khuyến nghị cho FaceNet)

## Ngưỡng Nhận Diện

| Ngưỡng Cosine Similarity | Kết Quả | Ý Nghĩa |
|--------------------------|---------|---------|
| > 0.9 | Matched (rất chắc chắn) | Cùng người, điều kiện lý tưởng |
| 0.7 – 0.9 | Matched | Cùng người, chấp nhận được |
| 0.5 – 0.7 | Unknown (ranh giới) | Không đủ tin cậy để khẳng định |
| < 0.5 | Unknown (chắc chắn khác) | Khác người |

## Ràng Buộc Quan Trọng
- Cần webcam hoạt động (index 0 mặc định)
- Cần cài đặt `facenet-pytorch`, `torch`, `opencv-python`, `numpy`, `matplotlib`
- MTCNN yêu cầu ảnh đầu vào là RGB (không phải BGR)
- FaceNet yêu cầu khuôn mặt resize về 160×160 pixels
- FaceNet embedding là vector numpy 512 chiều
- Ít nhất 1 ảnh tham chiếu trong `dataset/` để so sánh
- Ngưỡng similarity mặc định: **0.7** (có thể điều chỉnh)

## Lỗi Thường Gặp
| Lỗi | Nguyên nhân | Cách khắc phục |
|-----|-------------|----------------|
| `ModuleNotFoundError: No module named 'facenet_pytorch'` | Chưa cài facenet-pytorch | `pip install facenet-pytorch` |
| Webcam không mở được | Camera bị chiếm dụng hoặc sai index | Thử `cv2.VideoCapture(1)` hoặc kiểm tra camera |
| MTCNN không phát hiện được khuôn mặt | Ảnh quá tối, góc nghiêng quá lớn | Đảm bảo đủ sáng, nhìn thẳng vào camera |
| FaceNet báo lỗi kích thước | Khuôn mặt chưa resize về 160×160 | Dùng `cv2.resize(face, (160, 160))` |
| Similarity luôn thấp với mọi người | Embedding chưa chuẩn hóa | Kiểm tra bước chuẩn hóa vector |
| Màu da xanh khi hiển thị | Quên chuyển BGR→RGB | Dùng `cv2.cvtColor(frame, cv2.COLOR_BGR2RGB)` |
| `CUDA out of memory` | GPU không đủ VRAM | Chạy trên CPU: thêm `device='cpu'` khi khởi tạo |

## Thứ Tự Chạy
1. Chạy các cell import và kiểm tra webcam (Phần 1)
2. Chạy cell đăng ký khuôn mặt tham chiếu (Phần 2)
3. Chạy cell nhận diện thời gian thực (Phần 3)
4. Nhấn `q` để thoát vòng lặp nhận diện
5. Xem kết quả tổng kết
