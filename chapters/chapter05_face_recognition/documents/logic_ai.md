# Chương 5: Nhận Diện Khuôn Mặt Thời Gian Thực với FaceNet & MTCNN - Logic & AI

## 1. MTCNN — Multi-task Cascaded Convolutional Networks

### 1.1 Kiến Trúc 3 Tầng

MTCNN (Zhang et al., 2016) là mạng CNN 3 tầng xếp chồng (cascade) để phát hiện khuôn mặt, hoạt động từ thô đến tinh:

```
Ảnh đầu vào (bất kỳ kích thước)
      │
      ▼
┌─────────────────────────────────────────────┐
│ Stage 1: P-Net (Proposal Network)           │
│ - Input: Image pyramid (nhiều scale)        │
│ - Output: Candidate windows + confidence    │
│ - Mục đích: Đề xuất vùng ứng viên nhanh     │
│ - Kiến trúc: CNN nông (3 conv layers)       │
└──────────────────────┬──────────────────────┘
                       │
                       ▼ (NMS lọc candidate)
┌─────────────────────────────────────────────┐
│ Stage 2: R-Net (Refine Network)             │
│ - Input: Các candidate windows (resize 24×24)│
│ - Output: Refined windows + confidence      │
│ - Mục đích: Loại bỏ false positive          │
│ - Kiến trúc: CNN sâu hơn P-Net              │
└──────────────────────┬──────────────────────┘
                       │
                       ▼ (NMS lọc tiếp)
┌─────────────────────────────────────────────┐
│ Stage 3: O-Net (Output Network)             │
│ - Input: Refined windows (resize 48×48)     │
│ - Output:                                      │
│   · Bounding box (chính xác cao)              │
│   · 5 facial landmarks (2 mắt, mũi, 2 mép)   │
│   · Confidence score                          │
│ - Kiến trúc: CNN sâu nhất                    │
└─────────────────────────────────────────────┘
      │
      ▼
Kết quả: bounding box + 5 landmarks
```

### 1.2 Image Pyramid (Kim Tự Tháp Ảnh)

Để phát hiện khuôn mặt ở nhiều kích thước khác nhau, MTCNN tạo image pyramid — chuỗi ảnh với kích thước giảm dần theo scale factor:

$$I_k = \text{resize}(I, \text{scale} \times s^{-k})$$

Trong đó $s$ là scale factor (thường là 0.709), P-Net quét toàn bộ pyramid để tìm candidate.

### 1.3 Non-Maximum Suppression (NMS)

Sau mỗi stage, NMS loại bỏ các bounding box chồng lấp quá nhiều:

```
Input:  N bounding boxes với confidence scores
Output: Các box không chồng lấp (IoU < threshold)

Thuật toán:
1. Sắp xếp boxes theo confidence giảm dần
2. Chọn box có confidence cao nhất → giữ lại
3. Loại bỏ tất cả box có IoU > threshold với box đã chọn
4. Lặp lại cho đến khi hết box
```

## 2. FaceNet — Học Embedding Khuôn Mặt

### 2.1 Kiến Trúc InceptionResnetV1

FaceNet (Schroff et al., 2015) sử dụng kiến trúc InceptionResnetV1 làm backbone để ánh xạ khuôn mặt 160×160 thành vector embedding 512 chiều:

```
Ảnh khuôn mặt (160×160×3)
      │
      ▼
┌──────────────────────────────┐
│ Stem (Conv + Pooling)        │
└──────────┬───────────────────┘
           ▼
┌──────────────────────────────┐
│ Inception-ResNet Blocks      │
│ (5 blocks: A, B, C lặp lại)  │
│ - Inception: multi-scale     │
│ - Residual: shortcut         │
└──────────┬───────────────────┘
           ▼
┌──────────────────────────────┐
│ Average Pooling              │
└──────────┬───────────────────┘
           ▼
┌──────────────────────────────┐
│ Fully Connected (512)        │
│ + L2 Normalization           │
└──────────────────────────────┘
      │
      ▼
Embedding vector (512 chiều, ||v|| = 1)
```

### 2.2 Triplet Loss — Hàm Mất Mát

FaceNet được huấn luyện với Triplet Loss, mục tiêu: kéo gần các khuôn mặt cùng người, đẩy xa các khuôn mặt khác người:

$$\mathcal{L} = \sum_{i}^{N} \max\left( \|f(x_i^a) - f(x_i^p)\|_2^2 - \|f(x_i^a) - f(x_i^n)\|_2^2 + \alpha,\; 0 \right)$$

Trong đó:
- $x_i^a$: ảnh anchor (mẫu gốc)
- $x_i^p$: ảnh positive (cùng người với anchor)
- $x_i^n$: ảnh negative (khác người với anchor)
- $f(\cdot)$: hàm embedding của FaceNet
- $\alpha$: margin — khoảng cách tối thiểu giữa positive và negative (thường 0.2)

```
Minh họa Triplet Loss trong không gian embedding:

  Trước khi học:            Sau khi học:
  
    ●_p                      ●_p
   /                             \
  /                               \
 ●_a ─────────── ●_n             ●_a ─────────────────── ●_n
                                    \                   /
                                     \                 /
                                  ||a-p|| << ||a-n||
```

### 2.3 Chuẩn Hóa Đầu Vào

FaceNet yêu cầu ảnh đầu vào phải được chuẩn hóa về khoảng $[-1, 1]$:

$$\text{face\_norm} = \frac{\text{face} - 127.5}{128.0}$$

Với face là tensor giá trị pixel [0, 255]:

| Pixel Gốc | Sau Chuẩn Hóa |
|-----------|---------------|
| 0 (đen) | -0.996 |
| 127.5 (xám) | 0.0 |
| 255 (trắng) | 0.996 |

## 3. Cosine Similarity — So Sánh Embedding

### 3.1 Công Thức

$$\text{cosine\_similarity}(A, B) = \frac{A \cdot B}{\|A\| \cdot \|B\|} = \frac{\sum_{i=1}^{512} A_i B_i}{\sqrt{\sum A_i^2} \cdot \sqrt{\sum B_i^2}}$$

Vì FaceNet chuẩn hóa L2 đầu ra ($\|v\| = 1$), công thức đơn giản thành:

$$\text{similarity}(A, B) = A \cdot B = \sum_{i=1}^{512} A_i B_i$$

### 3.2 Logic Phân Loại

```
similarity = dot(live_emb, ref_emb)
      │
      ├── similarity > 0.7
      │       │
      │       └──► "Matched: <Tên>"   (màu xanh lá)
      │
      └── similarity ≤ 0.7
              │
              └──► "Unknown"          (màu đỏ)
```

### 3.3 Ý Nghĩa Ngưỡng Similarity

| Ngưỡng | Độ Tin Cậy | Ứng Dụng |
|--------|-----------|----------|
| 0.9 – 1.0 | Rất cao | Xác thực bảo mật (Face ID) |
| 0.7 – 0.9 | Cao | Nhận diện thông thường ✓ |
| 0.5 – 0.7 | Thấp | Cần kiểm tra thêm |
| 0.0 – 0.5 | Rất thấp | Chắc chắn khác người |

## 4. Logic Toàn Bộ Hệ Thống

### 4.1 Luồng Quyết Định

```
Khởi động chương trình
      │
      ▼
Kiểm tra dataset/ có ảnh không?
      │
      ├── KHÔNG → Cảnh báo → Mở webcam chụp ảnh tham chiếu
      │
      └── CÓ → Đăng ký embedding tham chiếu
              │
              ▼
         Mở webcam
              │
              ▼
         ┌─────────────────────────┐
         │ Vòng lặp nhận diện      │
         │                         │
         │  Đọc frame              │
         │       │                 │
         │       ▼                 │
         │  MTCNN detect           │
         │       │                 │
         │  ┌────┴────┐            │
         │  │ Có mặt? │            │
         │  └────┬────┘            │
         │   Có  │  Không          │
         │   ▼   │   ▼             │
         │  FaceNet  Hiển thị     │
         │  extract  frame gốc    │
         │   │                    │
         │   ▼                    │
         │  Match với             │
         │  references            │
         │   │                    │
         │   ├── sim > 0.7        │
         │   │   → "Matched: X"   │
         │   └── sim ≤ 0.7        │
         │       → "Unknown"      │
         │   │                    │
         │   ▼                    │
         │  Vẽ box + nhãn         │
         │  Hiển thị frame        │
         │       │                │
         │       ▼                │
         │  Nhấn 'q'? ── Không ──┘
         │       │
         │       Có
         │       ▼
         └────► Thoát
```

### 4.2 Xử Lý Nhiều Khuôn Mặt Trong Frame

Khi MTCNN phát hiện nhiều khuôn mặt trong cùng một frame, mỗi khuôn mặt được xử lý độc lập:

```
Frame có N khuôn mặt
      │
      ▼
  Với mỗi khuôn mặt i (1 → N):
    1. Crop face_i
    2. Resize 160×160
    3. FaceNet → emb_i
    4. So sánh emb_i với TẤT CẢ reference embeddings
    5. Chọn reference có similarity cao nhất
    6. Gán nhãn "Matched" hoặc "Unknown"
```

## 5. Tối Ưu Hiệu Năng

| Kỹ Thuật | Mô Tả | Tác Động |
|----------|-------|----------|
| `torch.no_grad()` | Không tính gradient khi suy luận | Giảm ~30% bộ nhớ, tăng tốc |
| `model.eval()` | Tắt batch norm/dropout | Ổn định kết quả |
| Resize frame đầu vào | Giảm kích thước frame trước khi detect | Tăng FPS đáng kể |
| `keep_all=True` | MTCNN trả về tất cả mặt trong 1 lần | Không cần gọi lại |
| CPU mode | `device='cpu'` khi không có GPU | Đảm bảo chạy được mọi máy |
