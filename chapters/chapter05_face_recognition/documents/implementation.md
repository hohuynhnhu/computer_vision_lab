# Chương 5: Nhận Diện Khuôn Mặt Thời Gian Thực với FaceNet & MTCNN - Triển Khai

## 1. Cấu Trúc Code

Chương 5 gồm 1 notebook duy nhất trong `notebook/`:

| Notebook | Nội Dung | Đầu Ra |
|----------|----------|--------|
| `chapter05_face_recognition.ipynb` | Toàn bộ bài tập: Webcam, MTCNN, FaceNet, Nhận diện | `outputs/recognition_result_*.png` |

Notebook được chia thành các section chính:
1. **Cài đặt và Import** — Cài thư viện, import, kiểm tra thiết bị
2. **Phần 1: Truy cập Webcam** — Mở webcam, chụp ảnh tham chiếu
3. **Phần 2: Phát hiện khuôn mặt với MTCNN** — Khởi tạo detector, test
4. **Phần 3: Trích xuất đặc trưng với FaceNet** — Khởi tạo model, extract embedding
5. **Phần 4: Đăng ký khuôn mặt tham chiếu** — Duyệt dataset, lưu embedding
6. **Phần 5: Nhận diện thời gian thực** — Vòng lặp webcam chính
7. **Tổng kết** — Bảng tổng kết các tác vụ

## 2. Import và Cài Đặt (Dùng Chung)

```python
# ============================================================
# Cell 1: Cài đặt thư viện (chạy 1 lần)
# ============================================================
# !pip install facenet-pytorch torch opencv-python numpy matplotlib Pillow

# ============================================================
# Cell 2: Import thư viện
# ============================================================
%matplotlib inline
import os
import cv2
import numpy as np
import matplotlib.pyplot as plt
import torch
from PIL import Image
from facenet_pytorch import MTCNN, InceptionResnetV1

# Kiểm tra thiết bị
device = torch.device('cuda' if torch.cuda.is_available() else 'cpu')
print(f"[*] Thiết bị: {device}")

# Đường dẫn
DATASET_DIR = '../dataset'
OUTPUT_DIR  = '../outputs'
os.makedirs(DATASET_DIR, exist_ok=True)
os.makedirs(OUTPUT_DIR, exist_ok=True)
```

## 3. Phần 1 — Truy Cập Webcam và Chụp Ảnh Tham Chiếu

```python
# ============================================================
# Cell 3: Hàm chụp ảnh từ webcam
# ============================================================
def capture_reference_images(save_dir=DATASET_DIR):
    """Mở webcam, nhấn 'c' để chụp ảnh, 'q' để thoát."""
    cap = cv2.VideoCapture(0)
    if not cap.isOpened():
        print("[!] Không thể mở webcam! Kiểm tra camera.")
        return

    print("[*] Webcam đã mở thành công!")
    print("[*] Nhấn 'c' để chụp ảnh tham chiếu")
    print("[*] Nhấn 'q' để thoát")
    print(f"[*] Ảnh sẽ được lưu vào: {save_dir}")

    count = 0
    while True:
        ret, frame = cap.read()
        if not ret:
            print("[!] Không đọc được frame!")
            break

        # Hiển thị hướng dẫn trên frame
        cv2.putText(frame, "Press 'c' to capture, 'q' to quit",
                    (10, 30), cv2.FONT_HERSHEY_SIMPLEX, 0.7, (0, 255, 0), 2)
        cv2.imshow('Capture Reference - Chapter 5', frame)

        key = cv2.waitKey(1) & 0xFF
        if key == ord('c'):
            count += 1
            name = input(f"Nhap ten nguoi cho anh {count}: ").strip().lower().replace(' ', '_')
            if not name:
                name = f"person_{count}"
            filepath = os.path.join(save_dir, f"{name}.jpg")
            cv2.imwrite(filepath, frame)
            print(f"[✓] Da luu: {filepath}")
        elif key == ord('q'):
            break

    cap.release()
    cv2.destroyAllWindows()
    print(f"[✓] Da chup {count} anh tham chieu.")

# Chạy cell này để chụp ảnh tham chiếu (chỉ cần chạy khi chưa có ảnh)
# capture_reference_images()
```

## 4. Phần 2 — Khởi Tạo MTCNN và FaceNet

```python
# ============================================================
# Cell 4: Khởi tạo MTCNN và FaceNet
# ============================================================
print("[*] Đang khởi tạo MTCNN...")
mtcnn = MTCNN(
    keep_all=True,
    device=device,
    min_face_size=40
)
print("[✓] MTCNN đã sẵn sàng!")

print("[*] Đang khởi tạo FaceNet (InceptionResnetV1)...")
model = InceptionResnetV1(
    pretrained='vggface2',
    classify=False,
    device=device
).eval()
print("[✓] FaceNet đã sẵn sàng!")
```

## 5. Phần 3 — Hàm Trích Xuất Embedding

```python
# ============================================================
# Cell 5: Hàm trích xuất embedding từ khuôn mặt
# ============================================================
def extract_embedding(face_img, model, device='cpu'):
    """Trích xuất embedding FaceNet từ ảnh khuôn mặt RGB 160x160.

    Args:
        face_img: numpy array (160, 160, 3) — ảnh RGB khuôn mặt
        model:    InceptionResnetV1 model
        device:   'cpu' hoặc 'cuda'

    Returns:
        numpy array (512,) đã chuẩn hóa L2
    """
    # Convert (H, W, C) → (C, H, W)
    face_tensor = torch.from_numpy(face_img).permute(2, 0, 1).float()

    # Chuẩn hóa về [-1, 1]
    face_tensor = (face_tensor - 127.5) / 128.0

    # Thêm batch dimension → (1, 3, 160, 160)
    face_tensor = face_tensor.unsqueeze(0).to(device)

    # Suy luận
    with torch.no_grad():
        embedding = model(face_tensor)

    # Chuyển về numpy và chuẩn hóa L2
    embedding = embedding.squeeze(0).cpu().numpy()
    embedding = embedding / np.linalg.norm(embedding)

    return embedding
```

## 6. Phần 4 — Đăng Ký Khuôn Mặt Tham Chiếu

```python
# ============================================================
# Cell 6: Đăng ký khuôn mặt tham chiếu từ dataset/
# ============================================================
def register_reference_faces(dataset_dir, mtcnn, model, device='cpu'):
    """Đăng ký khuôn mặt tham chiếu từ thư mục dataset.

    Args:
        dataset_dir: str — đường dẫn thư mục chứa ảnh tham chiếu
        mtcnn:       MTCNN detector
        model:       FaceNet model
        device:      'cpu' hoặc 'cuda'

    Returns:
        dict {tên_người: embedding_vector (512,)}
    """
    reference_embeddings = {}

    if not os.path.isdir(dataset_dir):
        print(f"[!] Thư mục '{dataset_dir}' không tồn tại!")
        return reference_embeddings

    files = [f for f in os.listdir(dataset_dir)
             if f.lower().endswith(('.jpg', '.jpeg', '.png', '.bmp'))]

    if not files:
        print("[!] Không tìm thấy ảnh nào trong dataset/!")
        print("[*] Hãy chạy cell capture_reference_images() để chụp ảnh tham chiếu.")
        return reference_embeddings

    for filename in files:
        filepath = os.path.join(dataset_dir, filename)
        person_name = os.path.splitext(filename)[0]

        # Đọc ảnh
        img_bgr = cv2.imread(filepath)
        if img_bgr is None:
            print(f"[!] Không đọc được: {filename}")
            continue

        img_rgb = cv2.cvtColor(img_bgr, cv2.COLOR_BGR2RGB)

        # Phát hiện khuôn mặt bằng MTCNN
        boxes, _ = mtcnn.detect(img_rgb)
        if boxes is None:
            print(f"[!] Không tìm thấy khuôn mặt: {filename}")
            continue

        # Lấy khuôn mặt đầu tiên
        x1, y1, x2, y2 = [max(0, int(v)) for v in boxes[0]]
        face = img_rgb[y1:y2, x1:x2]

        if face.size == 0:
            print(f"[!] Khuôn mặt rỗng: {filename}")
            continue

        # Resize về 160×160
        face = cv2.resize(face, (160, 160))

        # Trích xuất embedding
        embedding = extract_embedding(face, model, device)
        reference_embeddings[person_name] = embedding

        print(f"[✓] Đã đăng ký: {person_name} | Embedding shape: {embedding.shape}")

    print(f"\n[*] Tổng cộng: {len(reference_embeddings)} khuôn mặt đã đăng ký.")
    return reference_embeddings

# Đăng ký khuôn mặt
reference_embeddings = register_reference_faces(DATASET_DIR, mtcnn, model, device)

if len(reference_embeddings) == 0:
    print("\n[!!!] CHƯA CÓ KHUÔN MẶT THAM CHIẾU NÀO!")
    print("[>>>] Hãy chạy cell 'capture_reference_images()' trước.")
```

## 7. Phần 5 — So Khớp và Nhận Diện

```python
# ============================================================
# Cell 7: Hàm so khớp khuôn mặt
# ============================================================
def match_face(live_embedding, reference_embeddings, threshold=0.7):
    """So sánh embedding thời gian thực với danh sách tham chiếu.

    Args:
        live_embedding:       numpy array (512,)
        reference_embeddings: dict {tên: embedding}
        threshold:            ngưỡng cosine similarity

    Returns:
        (label, max_similarity)
    """
    best_name = "Unknown"
    best_sim = 0.0

    for name, ref_emb in reference_embeddings.items():
        sim = np.dot(live_embedding, ref_emb)
        if sim > best_sim:
            best_sim = sim
            best_name = name

    if best_sim > threshold:
        return f"Matched: {best_name}", best_sim
    else:
        return "Unknown", best_sim
```

## 8. Phần 6 — Vòng Lặp Nhận Diện Thời Gian Thực

```python
# ============================================================
# Cell 8: Nhận diện khuôn mặt thời gian thực
# ============================================================
def run_recognition(mtcnn, model, reference_embeddings, device='cpu', threshold=0.7):
    """Chạy nhận diện khuôn mặt thời gian thực từ webcam.

    Args:
        mtcnn:                 MTCNN detector
        model:                 FaceNet model
        reference_embeddings:  dict {tên: embedding}
        device:                'cpu' hoặc 'cuda'
        threshold:             ngưỡng cosine similarity (mặc định 0.7)
    """
    if len(reference_embeddings) == 0:
        print("[!!!] Chưa có khuôn mặt tham chiếu! Hãy đăng ký trước.")
        return

    cap = cv2.VideoCapture(0)
    if not cap.isOpened():
        print("[!] Không thể mở webcam!")
        return

    print("[*] Đang chạy nhận diện thời gian thực...")
    print(f"[*] Ngưỡng similarity: {threshold}")
    print(f"[*] Số khuôn mặt đã đăng ký: {len(reference_embeddings)}")
    print("[*] Nhấn 'q' để thoát.\n")

    frame_count = 0

    while True:
        ret, frame_bgr = cap.read()
        if not ret:
            print("[!] Không đọc được frame!")
            break

        frame_rgb = cv2.cvtColor(frame_bgr, cv2.COLOR_BGR2RGB)
        h, w = frame_rgb.shape[:2]

        # Phát hiện khuôn mặt
        boxes, _ = mtcnn.detect(frame_rgb)

        if boxes is not None:
            for box in boxes:
                # Tọa độ an toàn
                x1, y1, x2, y2 = [max(0, int(v)) for v in box]
                x1, y1 = min(x1, w - 1), min(y1, h - 1)
                x2, y2 = min(x2, w), min(y2, h)

                if x2 <= x1 or y2 <= y1:
                    continue

                # Crop và resize khuôn mặt
                face = frame_rgb[y1:y2, x1:x2]
                if face.size == 0:
                    continue
                face = cv2.resize(face, (160, 160))

                # Trích xuất embedding
                live_emb = extract_embedding(face, model, device)

                # So khớp
                label, sim = match_face(live_emb, reference_embeddings, threshold)

                # Màu sắc: xanh lá cho Matched, đỏ cho Unknown
                color = (0, 255, 0) if "Matched" in label else (0, 0, 255)

                # Vẽ bounding box
                cv2.rectangle(frame_bgr, (x1, y1), (x2, y2), color, 2)

                # Vẽ nhãn
                text = f"{label} ({sim:.2f})"
                cv2.putText(frame_bgr, text, (x1, y1 - 10),
                            cv2.FONT_HERSHEY_SIMPLEX, 0.6, color, 2)

        # Hiển thị FPS
        frame_count += 1
        cv2.imshow('Face Recognition - Chapter 5', frame_bgr)

        key = cv2.waitKey(1) & 0xFF
        if key == ord('q'):
            break

    cap.release()
    cv2.destroyAllWindows()
    print(f"\n[✓] Đã thoát. Tổng số frame đã xử lý: {frame_count}")

# Chạy nhận diện
# run_recognition(mtcnn, model, reference_embeddings, device, threshold=0.7)
```

## 9. Phần 7 — Hiển Thị Kết Quả và Tổng Kết

```python
# ============================================================
# Cell 9: Hiển thị ảnh tham chiếu đã đăng ký
# ============================================================
def show_registered_faces(dataset_dir=DATASET_DIR):
    """Hiển thị các ảnh tham chiếu đã đăng ký."""
    files = [f for f in os.listdir(dataset_dir)
             if f.lower().endswith(('.jpg', '.jpeg', '.png', '.bmp'))]

    if not files:
        print("[!] Chưa có ảnh tham chiếu nào.")
        return

    n = len(files)
    cols = min(n, 4)
    rows = (n + cols - 1) // cols

    fig, axes = plt.subplots(rows, cols, figsize=(4 * cols, 4 * rows))
    if n == 1:
        axes = [axes]
    else:
        axes = axes.flatten()

    for i, fname in enumerate(files):
        img = cv2.imread(os.path.join(dataset_dir, fname))
        img_rgb = cv2.cvtColor(img, cv2.COLOR_BGR2RGB)
        name = os.path.splitext(fname)[0]
        axes[i].imshow(img_rgb)
        axes[i].set_title(f"Ten: {name}", fontsize=10)
        axes[i].axis('off')

    for j in range(i + 1, len(axes)):
        axes[j].axis('off')

    plt.suptitle('Khuon mat tham chieu da dang ky', fontsize=14)
    plt.tight_layout()
    plt.savefig(os.path.join(OUTPUT_DIR, 'reference_faces.png'), dpi=100, bbox_inches='tight')
    plt.show()

# show_registered_faces()
```

## 10. Thứ Tự Chạy

1. **Cell 1**: Cài đặt thư viện (chỉ chạy lần đầu)
2. **Cell 2**: Import thư viện, kiểm tra thiết bị
3. **Cell 3**: Chụp ảnh tham chiếu từ webcam (`capture_reference_images()`)
4. **Cell 4**: Khởi tạo MTCNN và FaceNet
5. **Cell 5**: Định nghĩa hàm `extract_embedding()`
6. **Cell 6**: Đăng ký khuôn mặt tham chiếu
7. **Cell 7**: Định nghĩa hàm `match_face()`
8. **Cell 8**: Chạy `run_recognition()` — vòng lặp nhận diện chính
9. **Cell 9**: Hiển thị ảnh tham chiếu và tổng kết

> **Lưu ý quan trọng**: Phải có ít nhất 1 ảnh trong `dataset/` trước khi chạy Cell 8 (nhận diện). Nếu chưa có, hãy chạy Cell 3 để chụp ảnh tham chiếu.
