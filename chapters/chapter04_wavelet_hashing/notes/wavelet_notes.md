# Ghi Chú Lý Thuyết - So Sánh Sự Tương Đồng Của Các Hình Ảnh Sử Dụng Wavelet

## 1. Biến Đổi Wavelet Rời Rạc (Discrete Wavelet Transform - DWT)

Khác với **Biến đổi Fourier (Fourier Transform - FT)** chỉ phân tích tín hiệu từ miền thời gian/không gian sang miền tần số mà mất đi thông tin thời gian/không gian, **Biến đổi Wavelet (DWT)** cho phép phân tích tín hiệu đồng thời trên cả miền không gian và tần số (multi-resolution analysis).

### Phân tách ảnh 2D bằng Wavelet
Khi áp dụng DWT 2D lên một bức ảnh (tương đương với việc áp dụng bộ lọc thông thấp và thông cao trên các hàng và các cột):
1.  **cA (Approximation - Hệ số xấp xỉ)**: Được tạo ra từ việc lọc thông thấp trên cả hàng và cột. Đây là ảnh thu nhỏ chứa thông tin vĩ mô, cấu trúc nền tảng và năng lượng chính của bức ảnh.
2.  **cH (Horizontal - Chi tiết ngang)**: Lọc thông thấp trên hàng và thông cao trên cột. Lưu giữ thông tin biên cạnh nằm ngang.
3.  **cV (Vertical - Chi tiết đứng)**: Lọc thông cao trên hàng và thông thấp trên cột. Lưu giữ thông tin biên cạnh thẳng đứng.
4.  **cD (Diagonal - Chi tiết chéo)**: Lọc thông cao trên cả hàng và cột. Lưu giữ chi tiết nhiễu hoặc cạnh chéo.

$$\text{Ảnh ban đầu} \xrightarrow{\text{DWT Level 1}} \begin{array}{|c|c|} \hline cA & cH \\ \hline cV & cD \\ \hline \end{array}$$

Ở các cấp độ cao hơn (Level $L > 1$), ta tiếp tục phân tách ma trận $cA_{L-1}$ thành $cA_L, cH_L, cV_L, cD_L$.

---

## 2. Các Thuật Toán Băm Ảnh Nhận Thức (Image Perceptual Hashing)

Băm nhận thức sinh ra một biểu diễn nhị phân đại diện cho nội dung trực quan của ảnh. Hai ảnh tương đồng có khoảng cách giữa hai mã băm rất nhỏ.

### 2.1 Phương Pháp Lượng Tử Hóa Slide (Slide Method)
Phương pháp này lượng tử hóa toàn bộ hệ số wavelet (bao gồm cả các thành phần chi tiết tần số cao):
1.  Với mỗi ma trận hệ số $C$, thực hiện làm tròn theo một bước lượng tử $q$:
    $$Q(c) = \text{round}\left(\frac{c}{q}\right)$$
2.  Làm phẳng (flatten) tất cả các ma trận hệ số thành một mảng 1D duy nhất.
3.  Tạo chuỗi bit băm bằng phép toán lấy số dư modulo 2:
    $$\text{Bit}_i = Q(c_i) \pmod 2$$

*   **Đặc điểm**: Vì mã băm chứa cả các hệ số tần số cao (nhiễu và chi tiết nhỏ), phương pháp này rất nhạy cảm với các biến đổi nhỏ như nhiễu ngẫu nhiên, thay đổi độ sáng hay nén ảnh. Trong thực tế kiểm thử, sai số của phương pháp này khi ảnh bị nhiễu có thể lên tới **~49.5%** (gần như ngẫu nhiên).

### 2.2 Phương Pháp wHash Chuẩn (Standard wHash Method)
Phương pháp wHash tiêu chuẩn được thiết kế để tăng tính bất biến (robustness) bằng cách chỉ băm thành phần tần số thấp $cA$:
1.  Chỉ lấy ma trận hệ số xấp xỉ ở cấp độ cao nhất $cA_L$.
2.  Tính giá trị trung vị (median) của ma trận $cA_L$ để làm ngưỡng động phân ngưỡng nhị phân.
3.  Chuyển đổi từng phần tử trong $cA_L$ thành bit $1$ nếu lớn hơn trung vị, ngược lại nhận bit $0$.

*   **Đặc điểm**: Bỏ qua hoàn toàn các chi tiết tần số cao nên mã băm rất ổn định dưới tác động của nhiễu Gaussian, Salt & Pepper hay thay đổi ánh sáng. Sai số kiểm thử thực tế chỉ khoảng **0.16%** với ảnh sáng hơn và **3.27%** với ảnh nhiễu nặng.

---

## 3. Đánh Giá Chất Lượng Phân Loại (Classification Metrics)

Để đánh giá một ngưỡng khoảng cách Hamming $T$ xem nó phân loại cặp ảnh là "tương tự" hay "khác biệt" tốt như thế nào, ta dùng ma trận nhầm lẫn (Confusion Matrix):

*   **True Positive (TP)**: Cặp ảnh thực sự tương tự và thuật toán dự đoán là tương tự ($D_H \le T$).
*   **False Positive (FP)**: Cặp ảnh thực sự khác biệt nhưng thuật toán dự đoán là tương tự ($D_H \le T$).
*   **True Negative (TN)**: Cặp ảnh thực sự khác biệt và thuật toán dự đoán là khác biệt ($D_H > T$).
*   **False Negative (FN)**: Cặp ảnh thực sự tương tự nhưng thuật toán dự đoán là khác biệt ($D_H > T$).

Từ đó tính toán các chỉ số:
1.  **Độ chính xác (Accuracy)**:
    $$\text{Accuracy} = \frac{\text{TP} + \text{TN}}{\text{TP} + \text{TN} + \text{FP} + \text{FN}}$$
2.  **Độ nhạy / Thu hồi (Recall / Sensitivity)**: Tỷ lệ các cặp ảnh tương tự thực tế được phát hiện đúng.
    $$\text{Recall} = \frac{\text{TP}}{\text{TP} + \text{FN}}$$
3.  **Độ đặc hiệu (Specificity)**: Tỷ lệ các cặp ảnh khác biệt thực tế được nhận diện đúng.
    $$\text{Specificity} = \frac{\text{TN}}{\text{TN} + \text{FP}}$$

### Đường Cong ROC (Receiver Operating Characteristic)
Đường cong ROC được vẽ bằng cách thay đổi ngưỡng $T$ từ $0$ đến chiều dài tối đa của mã băm:
*   Trục tung: **True Positive Rate (TPR)** tương đương với Recall.
*   Trục hoành: **False Positive Rate (FPR)** tương đương với $1 - \text{Specificity}$.
*   Đường cong càng áp sát góc trên bên trái (diện tích dưới đường cong - AUC càng gần 1) thì thuật toán phân loại càng tối ưu.
