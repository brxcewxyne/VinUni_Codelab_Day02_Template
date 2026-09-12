# 01 — Problem Scan & Quick Cards

## Phase 1 — SCAN

Nhóm sử dụng ba lenses để tìm bài toán vận hành:

1. **Lặp lại (Repetitive):** Công việc được thực hiện nhiều lần theo cùng một quy trình.
2. **Tốn thời gian (Time-consuming):** Công việc thủ công chiếm nhiều thời gian của nhân viên.
3. **Pain từ người khác (Stakeholder Pain):** Bottleneck gây khó khăn cho khách hàng, cư dân hoặc nhân viên vận hành.

### Danh sách 5 bài toán

| # | Subsidiary | Lens | Mô tả ngắn bài toán |
|---:|---|---|---|
| 1 | **Vinhomes** | Lặp lại | Nhân viên CSKH phải đọc từng phản ánh của cư dân, xác định nhóm vấn đề và chuyển thủ công đến đúng bộ phận phụ trách tại từng tòa nhà. |
| 2 | **VinFast** | Lặp lại | Nhân viên tài chính phải đối chiếu dữ liệu của hàng loạt phiên sạc với hóa đơn do đối tác gửi và tìm các giao dịch bị thiếu hoặc chênh lệch. |
| 3 | **Vinpearl** | Tốn thời gian | Nhân viên đặt phòng phải đọc email group booking, trích xuất nhiều yêu cầu khác nhau, kiểm tra quỹ phòng và soạn bản xác nhận. |
| 4 | **Vinmec** | Tốn thời gian | Bác sĩ phải tổng hợp bệnh án, kết quả xét nghiệm và ghi chú điều trị để soạn bản tóm tắt xuất viện dễ hiểu cho bệnh nhân. |
| 5 | **Vinpearl** | Pain từ người khác | Quản lý khách sạn khó phát hiện sớm các review tiêu cực khẩn cấp khi phản hồi của khách nằm rải rác trên nhiều nền tảng. |

> **Lưu ý về số liệu:** Các baseline và mục tiêu dưới đây là giả định ban đầu để
> phục vụ scoping. Nhóm phải xác minh lại bằng log vận hành hoặc phỏng vấn
> stakeholder trước khi triển khai thực tế.

---

## Chọn 3 bài toán tiềm năng nhất

Nhóm chọn ba bài toán sau để thực hiện Quick-Assess:

1. **Problem #1 — Vinhomes:** Phân loại và điều hướng phản ánh cư dân.
2. **Problem #2 — VinFast:** Đối chiếu hóa đơn sạc điện đối tác.
3. **Problem #5 — Vinpearl:** Phát hiện review khách sạn cần xử lý khẩn cấp.

### Lý do chọn

| Bài toán | Giá trị vận hành | Khả năng đo lường | Khả năng kiểm soát rủi ro |
|---|---|---|---|
| Vinhomes — Điều hướng phản ánh | Giảm thao tác đọc và chuyển ticket lặp lại | Thời gian/ticket, accuracy phân loại, accuracy routing | AI chỉ đề xuất; CSKH phê duyệt trước khi chuyển |
| VinFast — Đối chiếu hóa đơn sạc | Giảm công việc so khớp dữ liệu thủ công | Thời gian/hóa đơn, tỷ lệ tự động khớp, số ngoại lệ | Không tự phê duyệt thanh toán; ngoại lệ do kế toán kiểm tra |
| Vinpearl — Phân tích review | Giúp phát hiện phản ánh khẩn cấp sớm hơn | Thời gian/review, precision/recall cảnh báo | AI chỉ tạo cảnh báo nháp; quản lý xác nhận |

Problem #3 chưa được chọn vì việc tự động kiểm tra group booking cần tích hợp dữ
liệu tồn phòng và quy tắc giá phức tạp. Problem #4 chưa được chọn vì dữ liệu y tế
nhạy cảm và hậu quả của việc tóm tắt sai cao hơn, đòi hỏi cơ chế quản trị dữ liệu
và bác sĩ phê duyệt nghiêm ngặt.

---

## Phase 2 — QUICK-ASSESS

## Quick Problem Card #1 — Phân loại và điều hướng phản ánh cư dân

| Trường | Nội dung |
|---|---|
| **Bài toán (1 câu)** | Nhân viên CSKH Vinhomes phải đọc và chuyển thủ công phản ánh của cư dân đến đúng bộ phận phụ trách tại từng tòa nhà. |
| **Công ty thành viên** | Vinhomes |
| **Actor** | Nhân viên CSKH tiếp nhận phản ánh; Ban quản lý và các đội vận hành là bên nhận ticket để xử lý. |
| **Workflow thủ công hiện tại** | 1. Cư dân gửi phản ánh qua ứng dụng.<br>2. CSKH đọc nội dung và kiểm tra tòa/căn hộ.<br>3. CSKH xác định nhóm vấn đề và mức độ ưu tiên.<br>4. CSKH tra cứu bộ phận phụ trách.<br>5. CSKH chuyển ticket đến đơn vị xử lý. |
| **Bottleneck** | Bước 3–4: nội dung là văn bản tự do và bảng phân công khác nhau theo địa điểm; thời gian baseline giả định là **5 phút/ticket**. |
| **AI hỗ trợ ở bước nào** | LLM trích xuất nhóm vấn đề, địa điểm và mức độ khẩn cấp. Rule-based routing tra bảng phân công để đề xuất bộ phận nhận. CSKH xác nhận trước khi chuyển. |
| **Metric có số** | Giảm thời gian phân loại và routing từ 5 phút xuống dưới 1 phút/ticket; accuracy phân loại ≥ 90%; accuracy routing ≥ 95%; 100% ticket khẩn cấp được đưa sang bước kiểm tra của con người. |
| **Quick Architecture** | **LLM Feature kết hợp Rule-based routing** |

---

## Quick Problem Card #2 — Đối chiếu hóa đơn sạc điện đối tác

| Trường | Nội dung |
|---|---|
| **Bài toán (1 câu)** | Nhân viên tài chính VinFast phải so khớp thủ công dữ liệu phiên sạc với hóa đơn đối tác và tìm các khoản chênh lệch. |
| **Công ty thành viên** | VinFast |
| **Actor** | Nhân viên kế toán đối soát và người phụ trách quan hệ đối tác trạm sạc. |
| **Workflow thủ công hiện tại** | 1. Đối tác gửi hóa đơn và bảng kê.<br>2. Kế toán tải dữ liệu phiên sạc nội bộ.<br>3. Kế toán chuẩn hóa mã trạm, thời gian, sản lượng và số tiền.<br>4. Kế toán so khớp từng giao dịch.<br>5. Các chênh lệch được chuyển cho người phụ trách kiểm tra. |
| **Bottleneck** | Bước 3–4: dữ liệu khác định dạng, có thể thiếu mã giao dịch hoặc dùng cách ghi thời gian khác nhau; baseline giả định là **10 phút/hóa đơn**. |
| **AI hỗ trợ ở bước nào** | Công cụ trích xuất đọc bảng kê; rule-based code chuẩn hóa trường dữ liệu, so khớp giao dịch và tạo danh sách ngoại lệ cho kế toán. |
| **Metric có số** | Giảm thời gian đối soát từ 10 phút xuống dưới 2 phút/hóa đơn; tự động ghép đúng ≥ 95% giao dịch đủ dữ liệu; 0 giao dịch chênh lệch được tự động phê duyệt. |
| **Quick Architecture** | **Rule-based**; OCR/trích xuất chỉ hỗ trợ khâu nhập dữ liệu nếu hóa đơn không có cấu trúc. |

---

## Quick Problem Card #3 — Phát hiện review khách sạn cần xử lý khẩn cấp

| Trường | Nội dung |
|---|---|
| **Bài toán (1 câu)** | Quản lý Vinpearl khó phát hiện sớm các review tiêu cực cần xử lý khẩn cấp khi phải đọc thủ công nội dung từ nhiều nền tảng. |
| **Công ty thành viên** | Vinpearl |
| **Actor** | Nhân viên chăm sóc khách hàng và quản lý vận hành khách sạn. |
| **Workflow thủ công hiện tại** | 1. Nhân viên mở từng nguồn review.<br>2. Nhân viên đọc và xác định cơ sở liên quan.<br>3. Nhân viên phân loại chủ đề và mức độ nghiêm trọng.<br>4. Nhân viên đưa review cần ưu tiên vào báo cáo.<br>5. Báo cáo được chuyển cho quản lý cơ sở. |
| **Bottleneck** | Bước 2–4: khối lượng văn bản lớn, cách diễn đạt đa dạng và dữ liệu nằm ở nhiều nguồn; baseline giả định là **6 phút/review cần lập báo cáo**. |
| **AI hỗ trợ ở bước nào** | LLM tóm tắt review, phân loại chủ đề, nhận diện dấu hiệu khẩn cấp và tạo cảnh báo nháp để nhân viên kiểm tra. |
| **Metric có số** | Giảm thời gian đọc và lập bản ghi từ 6 phút xuống dưới 1 phút/review; recall phát hiện review khẩn cấp ≥ 95%; 100% cảnh báo được người phụ trách xác nhận trước khi chuyển tiếp hoặc phản hồi khách. |
| **Quick Architecture** | **LLM Feature** |

---

## Chọn 1 bài toán để Deep-Dive

### Quyết định

Nhóm chọn **Quick Problem Card #1 — Phân loại và điều hướng phản ánh cư dân
Vinhomes** để thực hiện `02-deep-dive-report.md` và
`04-workflow-diagram.png`/`.pdf`.

### Lý do chọn Card #1

- Bài toán có actor, workflow, handoff và bottleneck cụ thể.
- Đầu vào là ngôn ngữ tự nhiên nên LLM có vai trò phù hợp và rõ ràng.
- Rule-based routing vẫn được dùng cho quyết định cần tính ổn định và kiểm chứng.
- Có thể đo hiệu quả bằng thời gian xử lý, accuracy phân loại, accuracy routing và
  tỷ lệ phát hiện ticket khẩn cấp.
- Có thể kiểm soát rủi ro bằng Operational Boundary, Human-in-the-loop và fallback
  về quy trình xử lý thủ công.

### Vì sao chưa chọn Card #2 và Card #3

- **Card #2:** Phần lớn logic đối soát có thể giải quyết minh bạch bằng rule-based
  code; LLM không phải thành phần trung tâm của giải pháp.
- **Card #3:** Phù hợp với LLM nhưng việc thử nghiệm phụ thuộc vào quyền truy cập
  review từ nhiều nền tảng. Bài toán Vinhomes có quy trình nội bộ và scope
  prototype rõ hơn.