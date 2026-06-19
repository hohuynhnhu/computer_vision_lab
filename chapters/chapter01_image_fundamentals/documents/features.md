# Chương 1: Cơ Bản Về Ảnh - Tính Năng

## Danh Sách Tính Năng

| STT | Tính Năng | Đầu Vào | Đầu Ra | Mô Tả |
|-----|-----------|---------|--------|-------|
| 1 | Cài đặt thư viện | `pip install` | Xác nhận phiên bản OpenCV và Pillow | Tự động cài đặt và kiểm tra phiên bản thư viện |
| 2 | Đọc và hiển thị ảnh | File ảnh `lab1.jpg` | Ảnh hiển thị + file PNG/BMP | Đọc ảnh bằng OpenCV, chuyển BGR→RGB, hiển thị và lưu đa định dạng |
| 3 | Chuyển đổi không gian màu | Ảnh RGB | 4 ảnh: RGB, Gray, HSV, LAB | Chuyển đổi qua 3 không gian màu phổ biến, hiển thị lưới so sánh |
| 4 | Cắt và resize ảnh | Ảnh RGB | 3 ảnh: crop, resize cố định, resize tỷ lệ | Cắt vùng 100:350, resize 200×200, resize 50% |
| 5 | Vẽ hình và chữ | Ảnh RGB + tham số vẽ | Ảnh có hình và văn bản overlay | Vẽ line, rectangle, circle; thêm text "OpenCV Demo" |

## Chi Tiết Kỹ Thuật

### 1. Đọc/Hiển Thị Ảnh
```python
img_bgr = cv2.imread('lab1.jpg')
img_rgb = cv2.cvtColor(img_bgr, cv2.COLOR_BGR2RGB)
plt.imshow(img_rgb)
cv2.imwrite('lab1_saved.png', img_bgr)
pil_img = Image.fromarray(img_rgb).save('lab1_pillow.bmp')
```

### 2. Chuyển Đổi Không Gian Màu
```python
gray = cv2.cvtColor(img_bgr, cv2.COLOR_BGR2GRAY)
hsv  = cv2.cvtColor(img_bgr, cv2.COLOR_BGR2HSV)
lab  = cv2.cvtColor(img_bgr, cv2.COLOR_BGR2LAB)
```

### 3. Cắt và Resize
```python
crop = img_rgb[100:350, 100:350]            # crop vùng
resized_fixed = cv2.resize(img_rgb, (200, 200))  # resize cố định
resized_scale = cv2.resize(img_rgb, (int(w*0.5), int(h*0.5)))  # resize tỷ lệ
```

### 4. Vẽ Hình và Văn Bản
```python
cv2.line(canvas, (0,0), (512,512), (255,0,0), 3)
cv2.rectangle(canvas, (50,50), (200,200), (0,255,0), 3)
cv2.circle(canvas, (380,130), 80, (0,0,255), 3)
cv2.putText(canvas, 'OpenCV Demo', (120,460), cv2.FONT_HERSHEY_SIMPLEX, 1.2, (255,255,0), 2)
```
