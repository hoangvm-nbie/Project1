# Báo Cáo Nội Dung Đã Tìm Hiểu & Thực Hiện

---

## 1. Mô tả đề tài

- **Tên đề tài:** Xây dựng công cụ phân tích log đăng nhập Windows và phát hiện bất thường theo tập con luật Sigma.
- **Bối cảnh & Mục tiêu:**
  - Trên hệ thống Windows, các hành vi truy cập và xác thực đều được ghi nhận lại trong nhật ký bảo mật (Security Log). Mục tiêu của đề tài là xây dựng một công cụ bằng Python có khả năng tự động đọc các file log này, đối chiếu với các mẫu dấu hiệu tấn công (dựa theo định dạng luật Sigma) để đưa ra cảnh báo kịp thời.
  - Đề tài được thực hiện nhằm mục đích học tập và thực hành: hiểu sâu về cấu trúc log Windows, cách thức hoạt động của các hệ thống giám sát an ninh mạng (SIEM/SOC), và tự lập trình các thuật toán xử lý dữ liệu, phân tích cú pháp logic mà không phụ thuộc vào các thư viện chuyên dụng có sẵn.
- **Phạm vi & Các thành phần chính của đề tài:**
  - **Dữ liệu log:** Tập trung vào 2 mã sự kiện quan trọng nhất của Windows Security:
    - *Event ID 4624:* Đăng nhập thành công (theo dõi các đăng nhập bất thường, đăng nhập ngoài giờ, tài khoản đặc quyền).
    - *Event ID 4625:* Đăng nhập thất bại (theo dõi dấu hiệu dò mật khẩu, brute-force).
  - **Bộ luật phát hiện (Sigma Rules):** Đọc các file định dạng YAML theo chuẩn Sigma cơ bản, hỗ trợ so khớp trường (`exact`, `contains`, `endswith`, `re`) và biểu thức điều kiện logic (`and`, `or`, `not`).
  - **Cơ chế phát hiện:**
    - *Phát hiện tức thời (Stateless):* Kiểm tra từng sự kiện log độc lập xem có khớp với luật không.
    - *Tương quan thời gian (Stateful / Sliding Window):* Gom nhóm và đếm số lần đăng nhập thất bại theo từng IP hoặc User trong một khung thời gian nhất định (phát hiện tấn công vét cạn mật khẩu).
  - **Giao diện & Công cụ hỗ trợ:**
    - Chạy phân tích qua dòng lệnh (CLI).
    - Trang Web local nhỏ (HTML/CSS/JS thuần, server Python tích hợp) để người dùng xem trực quan danh sách log, cảnh báo được kích hoạt và chỉnh sửa luật.
    - Script sinh dữ liệu giả lập và module tính toán độ chính xác (đếm số lần phát hiện đúng, báo động nhầm, bỏ sót).

---

## 2. Những gì đã tìm hiểu & học được

- **Cấu trúc Log Windows Security (Event ID 4624 & 4625):**
  - Tìm hiểu ý nghĩa cơ bản của 2 sự kiện chính: 4624 (đăng nhập thành công) và 4625 (đăng nhập thất bại).
  - Nắm được các trường dữ liệu quan trọng khi đọc log: `TargetUserName` (tài khoản đăng nhập), `IpAddress` (IP nguồn), `LogonType` (loại đăng nhập: console, qua mạng, hay RDP từ xa), các mã trạng thái lỗi `Status` / `SubStatus`.
- **Cú pháp quy tắc Sigma (Sigma Rule):**
  - Đọc hiểu định dạng YAML cơ bản của Sigma: phần `selection` (các trường cần lọc giá trị) và phần `condition` (điều kiện kết hợp logic).
  - Tìm hiểu cách dùng các modifier so khớp chuỗi phổ biến: `exact`, `contains`, `endswith`, và regex cơ bản.
  - Biết sơ qua về cách gắn thẻ kỹ thuật theo khung MITRE ATT&CK (ví dụ kỹ thuật Brute Force T1110).
- **Kiến thức thuật toán & đánh giá:**
  - Tìm hiểu thuật toán Shunting-yard đơn giản để phân tích các biểu thức logic (`and`, `or`, `not`, dấu ngoặc `()`) thay vì dùng hàm `eval()` trực tiếp trong Python.
  - Khái niệm về các chỉ số đánh giá cơ bản: True Positive (phát hiện đúng), False Positive (báo động nhầm), False Negative (bỏ sót).

---

## 3. Những việc đã làm & code thực tế

- **Xử lý dữ liệu log đầu vào (`flatten.py`):**
  - Viết code Python đọc log định dạng JSON/JSONL, chuyển đổi cấu trúc lồng nhau của Windows thành dictionary phẳng để dễ so khớp.
  - Đặt bí danh (alias) cho các trường hay dùng (ví dụ: `TargetUserName` -> `user`, `IpAddress` -> `src_ip`), chuẩn hóa kiểu dữ liệu số cho `EventID` và `LogonType`.
- **Viết bộ so khớp điều kiện (`selection.py`, `condition.py`, `rules.py`):**
  - Viết hàm kiểm tra các điều kiện trong `selection`: so khớp bằng, kiểm tra chuỗi con, đuôi chuỗi hoặc biểu thức chính quy.
  - Viết bộ phân tích cú pháp nhỏ bằng Shunting-yard để duyệt biểu thức `condition` và kiểm tra logic đúng/sai trên từng dòng log.
  - Viết hàm đọc file luật YAML và sinh ra cảnh báo (alert) nếu log thỏa mãn điều kiện.
- **Thử nghiệm tương quan thời gian cơ bản (`sliding.py`):**
  - Thử nghiệm cơ chế cửa sổ trượt (Sliding Window) đơn giản: gom các sự kiện đăng nhập thất bại theo IP/User trong một khoảng thời gian nhất định để phát hiện hành vi thử mật khẩu nhiều lần liên tiếp.
- **Tạo dữ liệu thử nghiệm & đánh giá (`generate_logs.py`, `metrics.py`):**
  - Viết một script nhỏ sinh khoảng 20 dòng log giả lập mô phỏng các tình huống (đăng nhập đúng, gõ sai mật khẩu liên tiếp, đăng nhập tài khoản lạ).
  - Chạy thử nghiệm đối soát kết quả cảnh báo với nhãn đáp án để tính thử số lượng TP, FP, FN.
- **Làm một giao diện Web local đơn giản (`web.py`, thư mục `frontend/`):**
  - Dùng thư viện có sẵn của Python (`http.server`) tạo một web server chạy local (không cần cài thêm framework nặng).
  - Viết trang web HTML/CSS/JS đơn giản để:
    - Bấm chạy bộ log mẫu hoặc tải file log lên để phân tích.
    - Xem bảng kết quả các cảnh báo, có ô tìm kiếm/lọc theo IP hoặc tên người dùng.
    - Xem và sửa nhanh nội dung luật YAML trực tiếp trên trình duyệt.
- **Viết kiểm thử (Unittest):**
  - Viết một số bài test tự động cơ bản để kiểm tra các hàm xử lý log và so khớp logic xem có chạy đúng như dự tính hay không.

---

## 4. Khó khăn gặp phải & Cách xử lý

- **Xử lý biểu thức logic lồng nhau:** Ban đầu cắt chuỗi bằng regex đơn giản thì bị lỗi khi biểu thức có ngoặc `()` hoặc có toán tử `not`. Sau đó phải tìm hiểu thuật toán Shunting-yard để chuyển đổi và duyệt cây logic thì mới chạy ổn định.
- **Dữ liệu log không đồng nhất:** Log trích xuất từ Windows có nhiều trường bị thiếu hoặc giá trị rỗng/None, lúc đầu code hay bị văng lỗi `KeyError` hoặc `TypeError`. Đã bổ sung hàm kiểm tra an toàn trước khi so sánh giá trị.
- **Giao diện web ban đầu khó nhìn:** Sau khi chạy thử, đã chỉnh lại bảng danh sách gọn hơn và sửa lại màu sắc cho dễ đọc.

---

## 5. Hướng phát triển tiếp theo (nếu có thời gian)

- Thử nghiệm đọc trực tiếp file log thực tế trích xuất từ máy Windows (`.evtx`).
- Bổ sung thêm luật để phát hiện các hành vi khác (như tạo tiến trình lạ EventID 4688).
