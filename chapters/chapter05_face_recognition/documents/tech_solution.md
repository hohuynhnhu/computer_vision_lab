# Chương 5: Nhận Diện Khuôn Mặt Thời Gian Thực với FaceNet & MTCNN - Giải Pháp Kỹ Thuật

## 1. Công Nghệ Sử Dụng

| Công Nghệ | Vai Trò |
|-----------|---------|
| OpenCV (`cv2`) | Truy cập webcam (`VideoCapture`), vẽ hình (`rectangle`, `putText`), hiển thị video (`imshow`), lưu ảnh (`imwrite`) |
| MTCNN (`facenet_pytorch.MTCNN`) | Phát hiện khuôn mặt với 3-stage cascaded CNN: P-Net, R-Net, O-Net |
| FaceNet (`facenet_pytorch.InceptionResnetV1`) | Trích xuất embedding 512 chiều từ khuôn mặt 160×160 |
| PyTorch (`torch`) | Backend deep learning, quản lý tensor và GPU |
| NumPy | Tính cosine similarity, xử lý vector embedding |
| Matplotlib | Hiển thị ảnh kết quả chụp màn hình |
| PIL (`PIL.Image`) | Chuyển đổi định dạng ảnh OpenCV (numpy) sang PIL cho MTCNN |
| `os` | Duyệt file trong thư mục `dataset/` |

## 2. Kiến Trúc Xử Lý

### 2.1 Pipeline Đăng Ký Khuôn Mặt Tham Chiếu

```
Thư mục dataset/
  ├── nguoi_a.jpg
  ├── nguoi_b.jpg
  └── nguoi_c.jpg
       │
       ▼  (với mỗi file)
  cv2.imread(path) → img_bgr
       │
       ▼
  cv2.cvtColor(BGR→RGB) → img_rgb
       │
       ▼
  MTCNN.detect_faces(img_rgb)
       │
       ├── Không tìm thấy khuôn mặt → bỏ qua, in cảnh báo
       │
       └── Tìm thấy khuôn mặt
              │
              ▼
         Lấy bounding box đầu tiên [x1, y1, x2, y2]
              │
              ▼
         Crop khuôn mặt: face = img_rgb[y1:y2, x1:x2]
              │
              ▼
         Resize về 160×160: cv2.resize(face, (160, 160))
              │
              ▼
         Chuyển thành tensor: torch.from_numpy(face).permute(2,0,1).float()
              │
              ▼
         Chuẩn hóa về [-1, 1]: (tensor - 127.5) / 128.0
              │
              ▼
         FaceNet: embedding = model(tensor.unsqueeze(0))
              │
              ▼
         Detach → numpy → chuẩn hóa L2: emb / np.linalg.norm(emb)
              │
              ▼
         Lưu: reference_embeddings[tên] = embedding
```

### 2.2 Pipeline Nhận Diện Thời Gian Thực

```
Webcam: cap = cv2.VideoCapture(0)
       │
       ▼  (vòng lặp while True)
  ret, frame_bgr = cap.read()
       │
       ▼
  frame_rgb = cv2.cvtColor(frame_bgr, cv2.COLOR_BGR2RGB)
       │
       ▼
  boxes, _ = mtcnn.detect(frame_rgb)
       │
       ├── boxes is None → không có khuôn mặt → hiển thị frame gốc
       │
       └── boxes có dữ liệu → với mỗi box:
              │
              ▼
         x1, y1, x2, y2 = box (làm tròn về int, clamp về [0, w/h])
              │
              ▼
         Crop khuôn mặt: face = frame_rgb[y1:y2, x1:x2]
              │
              ▼
         Resize về 160×160, chuyển tensor, chuẩn hóa
              │
              ▼
         FaceNet: live_emb = model(face_tensor)
              │
              ▼
         Với mỗi (tên, ref_emb) trong reference_embeddings:
           similarity = np.dot(live_emb, ref_emb)
              │
              ▼
         best_match = max(similarities) theo tên
              │
              ├── best_sim > 0.7 → label = "Matched: {tên}" (màu xanh)
              └── best_sim ≤ 0.7 → label = "Unknown"    (màu đỏ)
              │
              ▼
         Vẽ lên frame_bgr:
           cv2.rectangle(frame_bgr, (x1,y1), (x2,y2), color, 2)
           cv2.putText(frame_bgr, f"{label} ({sim:.2f})", ...)
              │
              ▼
  cv2.imshow('Face Recognition', frame_bgr)
       │
       ▼
  Nhấn 'q' → break → cap.release() → cv2.destroyAllWindows()
```

## 3. Các Hàm và API Chính

| Hàm | Mục Đích | Tham Số Chính |
|-----|----------|---------------|
| `cv2.VideoCapture(0)` | Mở webcam | `0`: camera index mặc định |
| `cap.read()` | Đọc 1 frame từ webcam | Trả về `(ret, frame)` |
| `cv2.imshow(title, frame)` | Hiển thị ảnh/video trong cửa sổ | `title`: tên cửa sổ, `frame`: ảnh BGR |
| `cv2.waitKey(1) & 0xFF` | Chờ phím nhấn (1ms) | Trả về mã ASCII của phím |
| `cv2.imwrite(path, frame)` | Lưu ảnh ra file | `path`: đường dẫn, `frame`: ảnh BGR |
| `MTCNN(keep_all=True)` | Khởi tạo face detector | `keep_all=True`: giữ tất cả khuôn mặt |
| `mtcnn.detect(img)` | Phát hiện khuôn mặt | `img`: numpy RGB array |
| `InceptionResnetV1(pretrained='vggface2')` | Khởi tạo FaceNet | `pretrained='vggface2'`: weights pre-trained |
| `model(tensor)` | Trích xuất embedding | `tensor`: shape (B, 3, 160, 160) |
| `cv2.rectangle(img, pt1, pt2, color, thickness)` | Vẽ hình chữ nhật | `pt1, pt2`: tọa độ góc |
| `cv2.putText(img, text, org, font, scale, color, thickness)` | Viết chữ lên ảnh | `org`: vị trí, `font`: `FONT_HERSHEY_SIMPLEX` |
| `np.dot(a, b)` | Tích vô hướng 2 vector | `a, b`: vector 512 chiều đã chuẩn hóa |

## 4. Chi Tiết Kỹ Thuật

### 4.1 Khởi Tạo MTCNN và FaceNet

```python
from facenet_pytorch import MTCNN, InceptionResnetV1
import torch

device = torch.device('cuda' if torch.cuda.is_available() else 'cpu')

# Khởi tạo MTCNN face detector
mtcnn = MTCNN(
    keep_all=True,           # Giữ tất cả khuôn mặt trong frame
    device=device,
    min_face_size=40         # Kích thước mặt tối thiểu
)

# Khởi tạo FaceNet (InceptionResnetV1)
model = InceptionResnetV1(
    pretrained='vggface2',   # Pre-trained trên VGGFace2
    classify=False,          # Trả về embedding, không phân loại
    device=device
).eval()
```

### 4.2 Trích Xuất Embedding Từ Khuôn Mặt

```python
def extract_embedding(face_img, model, device='cpu'):
    """Trích xuất embedding FaceNet từ ảnh khuôn mặt.

    Args:
        face_img:  numpy array ảnh khuôn mặt RGB (160, 160, 3)
        model:     InceptionResnetV1 model
        device:    'cpu' hoặc 'cuda'

    Returns:
        numpy array embedding (512,) đã chuẩn hóa L2
    """
    # Chuyển numpy (H,W,C) → tensor (C,H,W)
    face_tensor = torch.from_numpy(face_img).permute(2, 0, 1).float()

    # Chuẩn hóa về [-1, 1]
    face_tensor = (face_tensor - 127.5) / 128.0

    # Thêm batch dimension → (1, 3, 160, 160)
    face_tensor = face_tensor.unsqueeze(0).to(device)

    # Suy luận (không tính gradient)
    with torch.no_grad():
        embedding = model(face_tensor)

    # Detach, chuyển về CPU, chuyển thành numpy
    embedding = embedding.squeeze(0).cpu().numpy()

    # Chuẩn hóa L2
    embedding = embedding / np.linalg.norm(embedding)

    return embedding
```

### 4.3 Đăng Ký Khuôn Mặt Tham Chiếu

```python
def register_reference_faces(dataset_dir, mtcnn, model, device='cpu'):
    """Đăng ký khuôn mặt tham chiếu từ thư mục dataset.

    Args:
        dataset_dir: đường dẫn đến thư mục chứa ảnh tham chiếu
        mtcnn:       MTCNN detector
        model:       FaceNet model
        device:      'cpu' hoặc 'cuda'

    Returns:
        dict {tên_người: embedding_vector (512,)}
    """
    reference_embeddings = {}

    for filename in os.listdir(dataset_dir):
        if not filename.lower().endswith(('.jpg', '.jpeg', '.png', '.bmp')):
            continue

        filepath = os.path.join(dataset_dir, filename)
        person_name = os.path.splitext(filename)[0]  # Tên người = tên file

        # Đọc ảnh
        img_bgr = cv2.imread(filepath)
        if img_bgr is None:
            print(f"[!] Không đọc được: {filename}")
            continue

        img_rgb = cv2.cvtColor(img_bgr, cv2.COLOR_BGR2RGB)

        # Phát hiện khuôn mặt
        boxes, _ = mtcnn.detect(img_rgb)
        if boxes is None:
            print(f"[!] Không tìm thấy khuôn mặt trong: {filename}")
            continue

        # Lấy khuôn mặt đầu tiên
        x1, y1, x2, y2 = [max(0, int(v)) for v in boxes[0]]
        face = img_rgb[y1:y2, x1:x2]

        # Resize về 160×160
        face = cv2.resize(face, (160, 160))

        # Trích xuất embedding
        embedding = extract_embedding(face, model, device)
        reference_embeddings[person_name] = embedding

        print(f"[✓] Đã đăng ký: {person_name}")

    return reference_embeddings
```

### 4.4 Tính Cosine Similarity và Nhận Diện

```python
def match_face(live_embedding, reference_embeddings, threshold=0.7):
    """So sánh embedding thời gian thực với danh sách tham chiếu.

    Args:
        live_embedding:       numpy array (512,) — embedding từ webcam
        reference_embeddings: dict {tên: embedding} — danh sách tham chiếu
        threshold:            ngưỡng cosine similarity (mặc định 0.7)

    Returns:
        (label, max_similarity) — "Matched: <Tên>" hoặc "Unknown"
    """
    best_name = "Unknown"
    best_sim = 0.0

    for name, ref_emb in reference_embeddings.items():
        # Cosine similarity = dot product (vì embedding đã chuẩn hóa L2)
        sim = np.dot(live_embedding, ref_emb)
        if sim > best_sim:
            best_sim = sim
            best_name = name

    if best_sim > threshold:
        return f"Matched: {best_name}", best_sim
    else:
        return "Unknown", best_sim
```

### 4.5 Vòng Lặp Nhận Diện Thời Gian Thực

```python
def run_recognition(mtcnn, model, reference_embeddings, device='cpu', threshold=0.7):
    """Chạy nhận diện khuôn mặt thời gian thực từ webcam.

    Args:
        mtcnn:                 MTCNN detector
        model:                 FaceNet model
        reference_embeddings:  dict {tên: embedding}
        device:                'cpu' hoặc 'cuda'
        threshold:             ngưỡng cosine similarity
    """
    cap = cv2.VideoCapture(0)
    if not cap.isOpened():
        print("[!] Không thể mở webcam!")
        return

    print("[*] Nhấn 'q' để thoát.")

    while True:
        ret, frame_bgr = cap.read()
        if not ret:
            print("[!] Không đọc được frame từ webcam!")
            break

        frame_rgb = cv2.cvtColor(frame_bgr, cv2.COLOR_BGR2RGB)

        # Phát hiện khuôn mặt
        boxes, _ = mtcnn.detect(frame_rgb)

        if boxes is not None:
            for box in boxes:
                x1, y1, x2, y2 = [max(0, int(v)) for v in box]
                h, w = frame_rgb.shape[:2]
                x1, y1 = min(x1, w-1), min(y1, h-1)
                x2, y2 = min(x2, w),   min(y2, h)

                if x2 <= x1 or y2 <= y1:
                    continue

                # Crop và resize khuôn mặt
                face = frame_rgb[y1:y2, x1:x2]
                face = cv2.resize(face, (160, 160))

                # Trích xuất embedding
                live_emb = extract_embedding(face, model, device)

                # So khớp
                label, sim = match_face(live_emb, reference_embeddings, threshold)

                # Chọn màu
                color = (0, 255, 0) if "Matched" in label else (0, 0, 255)

                # Vẽ bounding box
                cv2.rectangle(frame_bgr, (x1, y1), (x2, y2), color, 2)

                # Vẽ nhãn
                text = f"{label} ({sim:.2f})"
                cv2.putText(frame_bgr, text, (x1, y1 - 10),
                            cv2.FONT_HERSHEY_SIMPLEX, 0.6, color, 2)

        # Hiển thị
        cv2.imshow('Face Recognition - Chapter 5', frame_bgr)

        if cv2.waitKey(1) & 0xFF == ord('q'):
            break

    cap.release()
    cv2.destroyAllWindows()
    print("[✓] Đã thoát nhận diện.")
```

## 5. So Sánh Cosine Similarity và Euclidean Distance

| Phương Pháp | Công Thức | Khoảng Giá Trị | Dùng Trong Bài |
|-------------|-----------|----------------|----------------|
| Cosine Similarity | $\frac{A \cdot B}{\|A\| \|B\|}$ | [-1, 1]; với embedding FaceNet: [0, 1] | ✓ (chính) |
| Euclidean Distance | $\sqrt{\sum (A_i - B_i)^2}$ | [0, ∞) | Có thể thay thế |

**Lưu ý**: FaceNet được huấn luyện với Triplet Loss, tối ưu hóa khoảng cách Euclidean trong không gian embedding. Với embedding đã chuẩn hóa L2, cosine similarity và Euclidean distance có mối liên hệ trực tiếp, và cosine similarity được chọn vì dễ hiểu và trực quan hơn.
