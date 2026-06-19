# Chương 3: Phát Hiện Cạnh - Triển Khai

## 1. Cấu Trúc Code

Chương 3 gồm 4 notebook trong `notebook/`:

| Notebook | Nội Dung | Đầu Ra |
|----------|----------|--------|
| `canny_opencv.ipynb` | Phần 1: Canny với OpenCV và Scikit-image | `outputs/01-04` |
| `parameter_analysis.ipynb` | Phần 2: Phân tích tham số | `outputs/05-07` |
| `image_evaluation.ipynb` | Phần 3: Đánh giá trên nhiều loại ảnh | Kết quả so sánh |
| `integration_experiment.ipynb` | Phần 4: Kết hợp contour/shape detection | Contour + nhận dạng |

## 2. Phần 1: Canny Bằng Thư Viện

```python
import cv2
import numpy as np
import matplotlib.pyplot as plt
import time
from skimage.feature import canny

# Đọc và chuẩn bị ảnh
img_bgr = cv2.imread('../input/demo.jpg')
img_bgr = cv2.resize(img_bgr, (512, 512))
img_gray = cv2.cvtColor(img_bgr, cv2.COLOR_BGR2GRAY)

# Canny bằng OpenCV
t1 = time.time()
edges_cv = cv2.Canny(img_gray, 100, 200)
t_cv = (time.time() - t1) * 1000
n_cv = np.sum(edges_cv > 0)

# Canny bằng Scikit-image
gray_f = img_gray.astype(np.float64) / 255.0
t2 = time.time()
edges_sk = canny(gray_f, sigma=2)
t_sk = (time.time() - t2) * 1000
n_sk = np.sum(edges_sk > 0)

# Hiển thị so sánh
fig, axes = plt.subplots(1, 3, figsize=(15, 5))
axes[0].imshow(img_gray, cmap='gray')
axes[0].set_title('Ảnh xám gốc')
axes[1].imshow(edges_cv, cmap='gray')
axes[1].set_title(f'OpenCV Canny\n{n_cv} điểm, {t_cv:.2f}ms')
axes[2].imshow(edges_sk, cmap='gray')
axes[2].set_title(f'Skimage Canny\n{n_sk} điểm, {t_sk:.2f}ms')

for ax in axes: ax.axis('off')
plt.tight_layout()
plt.show()

print(f'OpenCV: {n_cv} edges, {t_cv:.2f} ms')
print(f'Skimage: {n_sk} edges, {t_sk:.2f} ms')
```

## 3. Phần 2: Phân Tích Tham Số

```python
# Thay đổi ngưỡng OpenCV
thresholds = [(50, 150), (100, 200), (150, 250)]

fig, axes = plt.subplots(1, 3, figsize=(15, 5))
for i, (low, high) in enumerate(thresholds):
    edges = cv2.Canny(img_gray, low, high)
    n = np.sum(edges > 0)
    axes[i].imshow(edges, cmap='gray')
    axes[i].set_title(f'Low={low}, High={high}\n{n} edges')
    axes[i].axis('off')
plt.tight_layout()
plt.show()

# Thay đổi sigma Scikit-image
sigmas = [1, 2, 3]

fig, axes = plt.subplots(1, 3, figsize=(15, 5))
for i, s in enumerate(sigmas):
    edges = canny(gray_f, sigma=s)
    n = np.sum(edges > 0)
    axes[i].imshow(edges, cmap='gray')
    axes[i].set_title(f'Sigma={s}\n{n} edges')
    axes[i].axis('off')
plt.tight_layout()
plt.show()
```

## 4. Phần 3: Đánh Giá Nhiều Loại Ảnh

```python
# --- Ảnh nhiễu ---
# Canny trực tiếp
edges_direct = cv2.Canny(img_noise, 100, 200)

# Gaussian + Canny
blurred = cv2.GaussianBlur(img_noise, (5, 5), 0)
edges_gauss = cv2.Canny(blurred, 100, 200)

# --- Ảnh tương phản thấp ---
# Canny trực tiếp
edges_lc = cv2.Canny(img_low_contrast, 100, 200)

# Histogram Equalization + Canny
equalized = cv2.equalizeHist(img_low_contrast)
edges_eq = cv2.Canny(equalized, 100, 200)

# --- Ảnh nhiều chi tiết ---
# Thử nhiều bộ tham số
params = [(50,150), (100,200), (150,250)]
for low, high in params:
    edges = cv2.Canny(img_detail, low, high)
```

## 5. Phần 4: Kết Hợp Kỹ Thuật

```python
# Canny + Contour + Nhận dạng hình dạng
edges = cv2.Canny(img_gray, 100, 200)
contours, _ = cv2.findContours(edges, cv2.RETR_EXTERNAL, cv2.CHAIN_APPROX_SIMPLE)

for cnt in contours:
    # Lọc contour nhỏ
    if cv2.contourArea(cnt) < 100:
        continue

    # Xấp xỉ đa giác
    peri = cv2.arcLength(cnt, True)
    approx = cv2.approxPolyDP(cnt, 0.04 * peri, True)

    # Phân loại
    vertices = len(approx)
    if vertices == 3:
        name = "Tam giac"
    elif vertices == 4:
        name = "Tu giac"
    elif vertices > 4:
        # Kiểm tra hình tròn
        area = cv2.contourArea(cnt)
        circ = 4 * np.pi * area / (peri * peri) if peri > 0 else 0
        name = "Hinh tron" if circ > 0.8 else "Da giac"

    # Vẽ contour và nhãn
    cv2.drawContours(img_result, [approx], -1, (0, 255, 0), 2)
    M = cv2.moments(cnt)
    if M["m00"] != 0:
        cx, cy = int(M["m10"]/M["m00"]), int(M["m01"]/M["m00"])
        cv2.putText(img_result, name, (cx-20, cy), cv2.FONT_HERSHEY_SIMPLEX, 0.5, (255,0,0), 1)
```

## 6. Thứ Tự Chạy

1. `canny_opencv.ipynb` → Phần 1 (cơ bản)
2. `parameter_analysis.ipynb` → Phần 2 (phân tích)
3. `image_evaluation.ipynb` → Phần 3 (đánh giá)
4. `integration_experiment.ipynb` → Phần 4 (kết hợp)
