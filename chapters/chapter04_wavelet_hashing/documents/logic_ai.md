# Chương 4: So Sánh Sự Tương Đồng Của Các Hình Ảnh Sử Dụng Wavelet - Logic & AI

## 1. Nguyên Lý Băm Ảnh Nhận Thức (Perceptual Hashing)
Khác với các hàm băm mật mã thông thường (như MD5, SHA-256) vốn nhạy cảm với từng bit thay đổi (hiệu ứng thác - avalanche effect), **Băm ảnh nhận thức** hướng tới việc tạo ra một mã đại diện nhỏ gọn phản ánh trực quan cấu trúc của ảnh.
*   **Mục tiêu**: Hai ảnh nhìn giống nhau (chỉ khác về độ sáng, xoay góc nhỏ hoặc bị nhiễu) sẽ sinh ra mã băm có khoảng cách Hamming nhỏ. Hai ảnh có nội dung khác hẳn nhau sẽ có mã băm rất khác nhau (khoảng cách Hamming lớn).

## 2. Biến Đổi Wavelet Rời Rạc 2D (2D-DWT)
Biến đổi Wavelet chia nhỏ ảnh thành các dải tần số khác nhau. Trong thực tế, các cấu trúc vĩ mô của ảnh (nội dung chính) nằm ở dải tần thấp, trong khi chi tiết nhỏ, vân bề mặt và nhiễu nằm ở dải tần cao.

Khi áp dụng `pywt.wavedec2` ở mức phân tách (level) $L$:
*   **Hệ số xấp xỉ $cA_L$ (Approximation)**: Biểu diễn thành phần tần số thấp (ảnh thu nhỏ, trơn tru nhất).
*   **Hệ số chi tiết $cH_i, cV_i, cD_i$ (Detail)**: Biểu diễn thành phần tần số cao (các cạnh biên ngang, đứng, chéo ở mức phân tách $i$).

$$\text{Image} \xrightarrow{\text{DWT}} cA_L + \sum_{i=1}^{L} (cH_i + cV_i + cD_i)$$

## 3. Lượng Tử Hóa và Tạo Mã Băm

### 3.1 Phương Pháp Lượng Tử Hóa Trong Slide
Phương pháp trong slide thực hiện lượng tử hóa toàn bộ hệ số wavelet (cA và các detail) bằng bước lượng tử $q$:
$$Q(c) = \text{round}\left(\frac{c}{q}\right)$$
Mã băm nhị phân được rút trích bằng cách lấy số dư cho $2$ trên giá trị nguyên đã lượng tử hóa:
$$\text{Bit}_k = Q(c_k) \pmod 2$$

*   **Nhận xét**: Phương pháp này băm cả các hệ số chi tiết (tần số cao). Do các hệ số tần số cao cực kỳ nhạy cảm với nhiễu và các phép biến đổi hình học nhỏ, mã băm này sẽ có tính ổn định kém dưới tác động của nhiễu hoặc độ sáng.

### 3.2 Phương Pháp wHash Chuẩn (Perceptual Wavelet Hash)
Phương pháp chuẩn chỉ lấy phần hệ số tần số thấp $cA_L$:
1.  Tính giá trị trung vị $M = \text{median}(cA_L)$.
2.  Tạo bit băm:
    $$\text{Bit}_k = \begin{cases} 1 & \text{nếu } cA_{L, k} > M \\ 0 & \text{nếu } cA_{L, k} \le M \end{cases}$$
*   **Nhận xét**: Do chỉ tập trung vào $cA_L$, phương pháp này bỏ qua các dao động tần số cao, dẫn đến mã băm có tính bất biến rất cao dưới ảnh hưởng của nhiễu và thay đổi ánh sáng.

## 4. Khoảng Cách Hamming (Hamming Distance)
Độ đo sự khác biệt giữa hai mã băm nhị phân có cùng chiều dài $N$:
$$D_H(H_1, H_2) = \sum_{i=1}^{N} (H_{1, i} \oplus H_{2, i})$$
Trong đó $\oplus$ là phép toán XOR.
*   $D_H \approx 0$: Hai ảnh gần như giống hệt nhau.
*   $D_H \approx \frac{N}{2}$: Hai ảnh hoàn toàn độc lập (ngẫu nhiên).
