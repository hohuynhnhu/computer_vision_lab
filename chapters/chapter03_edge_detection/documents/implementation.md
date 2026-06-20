# Chương 3: Phát Hiện Cạnh - Triển Khai

## 1. Cấu Trúc Code

Chương 3 gồm 4 notebook trong `notebook/`:

| Notebook | Nội Dung | Đầu Ra |
|----------|----------|--------|
| `canny_opencv.ipynb` | Phần 1: Canny với OpenCV và Scikit-image | `outputs/01_gray.png` → `04_compare.png` |
| `parameter_analysis.ipynb` | Phần 2: Phân tích tham số ngưỡng và sigma | `outputs/05_threshold.png` → `07_default.png` |
| `image_evaluation.ipynb` | Phần 3: Đánh giá trên nhiều loại ảnh | So sánh trực quan |
| `integration_experiment.ipynb` | Phần 4: Kết hợp contour và shape detection | Contour + nhãn hình dạng |

## 2. Import và Đọc Ảnh (Dùng Chung)

```python
%matplotlib inline
import cv2
import numpy as np
import matplotlib.pyplot as plt
import time
from skimage.feature import canny as skimage_canny

# Đọc và chuẩn bị ảnh
img_bgr  = cv2.imread('../data/input/demo.jpg')
if img_bgr is None:
    raise FileNotFoundError("Không tìm thấy ảnh! Kiểm tra đường dẫn ../data/input/demo.jpg")

img_bgr  = cv2.resize(img_bgr, (512, 512))
img_gray = cv2.cvtColor(img_bgr, cv2.COLOR_BGR2GRAY)
gray_f   = img_gray.astype(np.float64) / 255.0      # Scikit-image cần float [0,1]

print(f"Image loaded: {img_bgr.shape}, dtype={img_bgr.dtype}")
```

## 3. Phần 1 — Canny Bằng Thư Viện

```python
# --- Canny bằng OpenCV ---
t1       = time.time()
edges_cv = cv2.Canny(img_gray, 100, 200)
t_cv     = (time.time() - t1) * 1000
n_cv     = np.sum(edges_cv > 0)

# --- Canny bằng Scikit-image ---
t2       = time.time()
edges_sk = skimage_canny(gray_f, sigma=2)
t_sk     = (time.time() - t2) * 1000
n_sk     = np.sum(edges_sk > 0)

# --- Hiển thị so sánh ---
fig, axes = plt.subplots(1, 3, figsize=(15, 5))
axes[0].imshow(img_gray, cmap='gray')
axes[0].set_title('Ảnh xám gốc')
axes[1].imshow(edges_cv, cmap='gray')
axes[1].set_title(f'OpenCV Canny\n{n_cv} điểm | {t_cv:.2f} ms')
axes[2].imshow(edges_sk, cmap='gray')
axes[2].set_title(f'Skimage Canny\n{n_sk} điểm | {t_sk:.2f} ms')
for ax in axes:
    ax.axis('off')
plt.tight_layout()
plt.show()

print(f"OpenCV : {n_cv} edges, {t_cv:.2f} ms")
print(f"Skimage: {n_sk} edges, {t_sk:.2f} ms")
```

## 4. Phần 2 — Phân Tích Tham Số

```python
# --- Thay đổi ngưỡng OpenCV ---
thresholds = [(50, 150), (100, 200), (150, 250)]
labels     = ['Low: (50,150)', 'Medium: (100,200)', 'High: (150,250)']

fig, axes = plt.subplots(1, 3, figsize=(15, 5))
for i, ((low, high), label) in enumerate(zip(thresholds, labels)):
    edges = cv2.Canny(img_gray, low, high)
    n     = np.sum(edges > 0)
    axes[i].imshow(edges, cmap='gray')
    axes[i].set_title(f'{label}\n{n} edges')
    axes[i].axis('off')
plt.suptitle('Ảnh hưởng của ngưỡng (OpenCV Canny)')
plt.tight_layout()
plt.show()

# --- Thay đổi sigma Scikit-image ---
sigmas = [1, 2, 3]

fig, axes = plt.subplots(1, 3, figsize=(15, 5))
for i, s in enumerate(sigmas):
    edges = skimage_canny(gray_f, sigma=s)
    n     = np.sum(edges > 0)
    axes[i].imshow(edges, cmap='gray')
    axes[i].set_title(f'Sigma = {s}\n{n} edges')
    axes[i].axis('off')
plt.suptitle('Ảnh hưởng của sigma (Scikit-image Canny)')
plt.tight_layout()
plt.show()
```

## 5. Phần 3 — Đánh Giá Nhiều Loại Ảnh

```python
# --- Ảnh nhiều nhiễu ---
edges_noise_direct = cv2.Canny(img_noise, 100, 200)

blurred      = cv2.GaussianBlur(img_noise, (5, 5), 0)
edges_noise_blur   = cv2.Canny(blurred, 100, 200)

# --- Ảnh tương phản thấp ---
edges_lc_direct  = cv2.Canny(img_low_contrast, 100, 200)

equalized       = cv2.equalizeHist(img_low_contrast)
edges_lc_eq     = cv2.Canny(equalized, 100, 200)

# --- Ảnh nhiều chi tiết ---
for low, high in [(50,150), (100,200), (150,250)]:
    edges = cv2.Canny(img_detail, low, high)
    n     = np.sum(edges > 0)
    print(f"Low={low}, High={high}: {n} edges")
```

## 6. Phần 4 — Kết Hợp Kỹ Thuật

```python
def classify_shape(cnt):
    """Phân loại hình dạng: Tam giác / Tứ giác / Hình tròn / Đa giác."""
    peri     = cv2.arcLength(cnt, True)
    approx   = cv2.approxPolyDP(cnt, 0.04 * peri, True)
    vertices = len(approx)
    if vertices == 3:
        return "Tam giac", approx
    elif vertices == 4:
        return "Tu giac", approx
    elif vertices > 4:
        area   = cv2.contourArea(cnt)
        circ   = 4 * np.pi * area / (peri * peri) if peri > 0 else 0
        name   = "Hinh tron" if circ > 0.8 else "Da giac"
        return name, approx
    return "Khac", approx

# --- Phát hiện cạnh và tìm contour ---
edges    = cv2.Canny(img_gray, 100, 200)
contours, _ = cv2.findContours(edges, cv2.RETR_EXTERNAL, cv2.CHAIN_APPROX_SIMPLE)
img_result  = cv2.cvtColor(img_gray, cv2.COLOR_GRAY2BGR)

for cnt in contours:
    if cv2.contourArea(cnt) < 100:      # Lọc contour quá nhỏ
        continue

    name, approx = classify_shape(cnt)

    # Vẽ contour và nhãn
    cv2.drawContours(img_result, [approx], -1, (0, 255, 0), 2)
    M = cv2.moments(cnt)
    if M["m00"] != 0:
        cx = int(M["m10"] / M["m00"])
        cy = int(M["m01"] / M["m00"])
        cv2.putText(img_result, name, (cx - 30, cy),
                    cv2.FONT_HERSHEY_SIMPLEX, 0.5, (255, 0, 0), 1)

# --- Hiển thị kết quả ---
fig, axes = plt.subplots(1, 3, figsize=(15, 5))
axes[0].imshow(img_gray, cmap='gray');        axes[0].set_title('Ảnh gốc (gray)')
axes[1].imshow(edges, cmap='gray');           axes[1].set_title('Edge Map (Canny)')
axes[2].imshow(img_result[:,:,::-1]);         axes[2].set_title('Shape Detection')
for ax in axes: ax.axis('off')
plt.tight_layout()
plt.show()
```

## 7. Thứ Tự Chạy

1. `canny_opencv.ipynb` → Phần 1 (cơ bản, **chạy trước tiên**)
2. `parameter_analysis.ipynb` → Phần 2 (phân tích tham số)
3. `image_evaluation.ipynb` → Phần 3 (đánh giá loại ảnh)
4. `integration_experiment.ipynb` → Phần 4 (kết hợp kỹ thuật + báo cáo)

Tất cả notebook đều đọc ảnh từ `../data/input/demo.jpg`.
