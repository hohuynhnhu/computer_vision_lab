# WORKFLOW TỔNG QUÁT: LAB 3 - CANNY EDGE DETECTION

## Ngữ cảnh
Đây là thư mục chứa bài thực hành Lab 3 về thuật toán Canny Edge Detection. 
Toàn bộ cấu trúc yêu cầu (workflow) và mã nguồn được đặt trong thư mục `src/`.

---

## 🌳 Cấu trúc dự án
Agent cần nắm rõ cấu trúc cây thư mục dưới đây để tương tác đúng với các tệp tin, đọc dữ liệu đầu vào và ghi dữ liệu đầu ra:

```text
lab3/
│
├── input/                  # Chứa các ảnh đầu vào để chạy thuật toán (VD: demo.jpg)
├── output/                 # Thư mục đích để Agent lưu các ảnh kết quả và biểu đồ
│
├── src/                    # Thư mục lõi chứa toàn bộ Workflow và Code
│   ├── 1.md                # Workflow Phần 1: Canny bằng các thư viện
│   ├── 1.ipynb             # (Agent tạo/sửa) Code thực thi Phần 1
│   ├── 2.md                # Workflow Phần 2: Khảo sát Tham số Canny
│   ├── 2.ipynb             # (Agent tạo/sửa) Code thực thi Phần 2
│   ├── 3.md                # Workflow Phần 3: Áp dụng trên nhiều loại ảnh
│   ├── 3.ipynb             # (Agent tạo/sửa) Code thực thi Phần 3
│   ├── 4.md                # Workflow Phần 4: Kết hợp kỹ thuật khác
│   └── 4.ipynb             # (Agent tạo/sửa) Code thực thi Phần 4
│
├── requirements.txt        # Danh sách thư viện Python cần thiết
├── question.txt            # Câu hỏi gợi ý từ bài tập (nếu có)
└── README.md               # Tệp chỉ dẫn tổng quát này (Agent đang đọc)
```

---

## 📋 Nhiệm vụ và Các bước Thực thi chi tiết của Agent

Agent có nhiệm vụ đọc hiểu các tệp hướng dẫn (`.md`) trong thư mục `src/`, từ đó viết code triển khai vào các tệp Jupyter Notebook (`.ipynb`), thực thi, và sinh ra các báo cáo đánh giá tương ứng.

Dưới đây là **Quy trình 6 bước** chi tiết Agent phải tuân thủ:

### Bước 1: Khởi tạo và Phân tích môi trường
* Đọc tệp `requirements.txt` và đảm bảo môi trường Python đã cài đặt đủ các thư viện (`opencv-python`, `numpy`, `matplotlib`, `scikit-image`, `ipykernel`).
* Kiểm tra sự tồn tại của thư mục `input/` và `output/`. Nếu thư mục `output/` chưa có, Agent cần viết code tự động tạo thư mục này trong quá trình chạy.

### Bước 2: Đọc và Hiểu Workflow (Tệp Markdown)
* Agent bắt đầu bằng việc đọc tệp cấu hình của phần tương ứng, ví dụ: `src/1.md`.
* Trích xuất các yêu cầu cụ thể: cần so sánh gì, dùng hàm nào, cần đo lường thông số nào (thời gian, số pixel cạnh...).

### Bước 3: Triển khai Mã nguồn (Tệp Jupyter Notebook)
* Agent mở hoặc tạo tệp notebook tương ứng (ví dụ: `src/1.ipynb`).
* Lập trình các bước theo đúng yêu cầu từ Bước 2.
* **Quy tắc đọc ảnh:** Phải sử dụng đường dẫn tương đối tới thư mục `input/` (ví dụ: `cv2.imread("../input/demo.jpg")`).

### Bước 4: Lưu trữ Kết quả và Trực quan hóa
* Hiển thị kết quả bằng `matplotlib` (vẽ biểu đồ, hiện nhiều ảnh cạnh nhau).
* In ra các chỉ số định lượng trực tiếp trên Notebook (để lại output trong tệp `.ipynb`).
* **Quy tắc xuất ảnh:** Các ảnh kết quả quan trọng hoặc biểu đồ cần được lưu ra định dạng ảnh bằng `cv2.imwrite` hoặc `plt.savefig` vào thư mục `output/` (ví dụ: `../output/01_canny_result.png`).

### Bước 5: Lặp lại cho các Phần tiếp theo
* Sau khi hoàn tất `src/1.md` và `1.ipynb`, Agent tiếp tục chuyển sang xử lý `src/2.md`, `src/3.md` và `src/4.md` theo chu trình tương tự.

### Bước 6: Tổng hợp Báo cáo Cuối cùng
* Khi hoàn thành cả 4 phần, Agent có trách nhiệm đọc lại kết quả chạy thực tế từ 4 Notebook.
* Tổng hợp các nhận xét, đánh giá toàn diện ưu/nhược điểm, phân tích sự thay đổi tham số, và ứng dụng của thuật toán Canny vào một **Báo cáo Kết luận Chung** (có thể xuất dưới dạng Markdown cell ở cuối tệp `4.ipynb` hoặc một tệp `final_report.md` riêng).
