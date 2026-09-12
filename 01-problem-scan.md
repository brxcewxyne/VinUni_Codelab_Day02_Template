# 01 - Problem Scan

## Mục tiêu
Khảo sát và chốt bài toán có giá trị kinh doanh rõ ràng, dễ đo lường và phù hợp để giải quyết bằng AI trong môi trường Vinhomes.

---

## 1. Bài toán được chọn

### Vinhomes — Phân loại & Điều hướng phản ánh cư dân

- Subsidiary: Vinhomes
- Problem title: Phân loại & điều hướng phản ánh cư dân
- Input channel: App Vinhomes Resident
- Output mong muốn: phân loại chủ đề, gán mức độ ưu tiên, gợi ý tòa nhà / khu vực, routing đến ban quản lý phù hợp

### Ví dụ phản ánh
- mất nước, rò rỉ nước ở tầng 12 tòa A
- đèn hỏng ở khu vực sân thượng tòa B
- ồn ào từ 10pm
- rác và vệ sinh ở lối đi chung
- thang máy dừng giữa tầng
- an ninh / cổng / truy cập bất hợp pháp

---

## 2. Vì sao bài toán này đáng chọn?

### 2.1 Lặp lại và có volume lớn
- Mỗi ngày có nhiều phản ánh tương tự nhưng viết theo cách khác nhau
- Dạng tác vụ này rất phù hợp với workflow AI-assisted triage

### 2.2 Dễ đo lường giá trị
- thời gian triage giảm
- tỷ lệ routing đúng tăng
- giảm khối lượng thao tác thủ công cho ban quản lý
- cải thiện trải nghiệm cư dân

### 2.3 Khả thi kỹ thuật ở MVP
- Có thể triển khai bằng Rule-based + LLM Feature
- Không cần dùng multi-agent hoặc agentic loop ở giai đoạn đầu
- Chỉ cần workflow rõ ràng và human review cho trường hợp thiếu thông tin

---

## 3. Problem Scan Matrix

| # | Bài toán | Pain point | Tần suất | AI-fit | Ghi chú |
|---|----------|------------|----------|--------|---------|
| 1 | Phân loại phản ánh cư dân | Triage thủ công, chậm, sai bộ phận | Cao | Cao | Phù hợp MVP |
| 2 | Trợ lý cư dân ảo hỗ trợ thủ tục hành chính | Người dân mất thời gian đi trực tiếp | Trung bình | Cao | Cần FAQ rõ |
| 3 | Review khách sạn / resort | Review không cấu trúc | Cao | Cao | Dùng NLP tốt |
| 4 | Booking / phòng trống | Quy trình thủ công | Cao | Trung bình | Phụ thuộc hệ thống ngoài |
| 5 | Chẩn đoán lỗi xe từ mô tả tiếng Việt | Mô tả phong phú, chuyên môn | Trung bình | Cao | Rủi ro kỹ thuật cao |

---

## 4. Đánh giá bài toán đã chọn

### Business value
- Giảm thời gian xử lý phản ánh từ khi nhận đến khi assign
- Tăng độ chính xác trong việc gán cho đơn vị đúng
- Giảm tải công việc cho ban quản lý và đội vận hành
- Cải thiện độ hài lòng của cư dân qua phản hồi nhanh hơn

### Technical feasibility
- Có thể xây dựng bằng Rule-based cho keyword rõ ràng
- Có thể dùng LLM để xử lý các complaint không chuẩn, mơ hồ, viết tắt
- Hệ thống phù hợp mô hình hybrid: AI suggest + human review

### Operational risk
- Nếu routing sai, phản ánh bị chậm trễ
- Nếu thiếu địa điểm hoặc tòa nhà, hệ thống dễ gán nhầm bộ phận
- Do đó cần ranh giới an toàn và Human-in-the-loop

---

## 5. Problem Statement chuẩn doanh nghiệp

"Vinhomes đang xử lý một lượng lớn phản ánh cư dân qua App Vinhomes Resident với nội dung không đồng nhất, viết ngắn và nhiều trường hợp thiếu thông tin về tòa nhà hoặc vị trí. Quy trình triage và routing hiện nay chủ yếu dựa vào thao tác thủ công của nhân sự, dẫn đến thời gian xử lý chậm, sai đơn vị xử lý và tăng tải công việc cho đội vận hành. Cần xây dựng một hệ thống AI hỗ trợ phân loại complaint theo chủ đề, xác định độ ưu tiên và đề xuất ban quản lý / bộ phận xử lý phù hợp, nhằm giảm thời gian triage, nâng hiệu suất vận hành và cải thiện trải nghiệm cư dân."

---

## 6. Metrics đề xuất

### Business metrics
- Time-to-triage
- Routing accuracy
- False routing rate
- Human override rate
- SLA compliance
- Reduction in manual workload

### Model / AI metrics
- Classification accuracy by category
- Precision / Recall cho các chủ đề phổ biến
- Deferral rate
- Confidence threshold satisfaction

### KPI gợi ý cho MVP
- Routing accuracy > 85%
- Deferral rate < 20%
- Time-to-triage giảm 30-50%
- Precision top categories > 90%

---

## 7. AI-fit assessment

### Rule-based
Phù hợp cho case có keyword rõ như:
- nước rò, đèn hỏng, ồn ào, thang máy, rác

### LLM Feature
Phù hợp cho complaint dạng tự do, viết ngắn, sai chính tả, không chuẩn hóa, nhiều biến thể ngôn ngữ

### Agentic Loop
Không nên chọn ở MVP vì:
- không cần tự động hóa toàn bộ quy trình
- rủi ro sai routing cao
- dễ vượt quá ranh giới vận hành nếu không có review

### Kết luận AI-fit
Hybrid approach: Rule-based + LLM Feature + Human-in-the-loop

---

## 8. Ranh giới an toàn cần giữ

AI chỉ được:
- phân loại chủ đề
- đánh giá mức ưu tiên
- gợi ý ban quản lý phù hợp
- yêu cầu thêm thông tin khi cần

AI không được:
- tự động quyết định routing khi thiếu dữ liệu
- bypass policy hoặc human review
- tự ý gửi/approve hành động mà không có người kiểm tra
- giả mạo tòa nhà / đơn vị không có căn cứ

---

## 9. Recommendation

- Bắt đầu với 1 classifier + routing engine
- Dùng dữ liệu historical complaint và danh sách ban quản lý tòa nhà
- Nếu thiếu thông tin -> chuyển `operations_review_queue`
- Khuyến khích mô hình kết hợp rule + LLM + review layer

---

## 10. Next step
- làm deep-dive report với workflow hiện tại và pain points
- xác định target metrics và model boundary
- xây dựng prompt prototype cho Gemini 3.6 Flash
- test prompt injection và fallback safety