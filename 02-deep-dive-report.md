# 02 - Deep Dive Report

## 1. Bối cảnh vấn đề
Vinhomes quản lý nhiều tòa nhà, khu căn hộ và tiện ích chung. Mỗi ngày có lượng lớn phản ánh cư dân được gửi qua App Vinhomes Resident về các sự cố như mất nước, hỏng đèn, ồn ào, rác, thang máy, cổng ra vào, v.v.

Phần lớn thông tin đầu vào có dạng văn bản ngắn, không chuẩn hóa, và thiếu dữ liệu về tòa nhà hoặc vị trí chính xác. Do đó, việc triage thủ công đang tạo ra nhiều điểm nghẽn cho bộ phận quản lý.

---

## 2. Current-state workflow

1. Cư dân gửi phản ánh qua App Vinhomes Resident
2. Ban quản lý nhận yêu cầu qua hệ thống nội bộ / ticket / giao tiếp trực tiếp
3. Nhân sự đọc nội dung, xác định loại sự cố và tòa nhà liên quan
4. Gán phản ánh cho bộ phận / đơn vị phù hợp
5. Theo dõi tiến độ xử lý và cập nhật cho cư dân
6. Nếu thiếu thông tin, cần liên hệ bổ sung trước khi gán đơn vị

### Pain points hiện tại
- phản ánh có cùng chủ đề nhưng mô tả khác nhau
- nhân sự phải đọc và gán thủ công nhiều lần
- khó routing khi không có tên tòa nhà / khu / tầng cụ thể
- sai đơn vị xử lý làm kéo dài thời gian giải quyết
- khó scale khi số lượng phản ánh tăng nhanh

---

## 3. Stakeholders

### Người dùng chính
- Ban quản lý tòa nhà
- Đội kỹ thuật / vận hành
- Nhân sự xử lý phản ánh cư dân

### Người dùng thứ cấp
- Cư dân
- Ban quản lý khu / khu đô thị
- Đội vận hành app / hệ thống nội bộ

---

## 4. Nhu cầu người dùng
- Tự động phân loại chủ đề sự cố
- Gợi ý bộ phận xử lý đúng
- Xác định mức ưu tiên / khẩn cấp
- Yêu cầu thêm thông tin nếu thiếu dữ liệu
- Giảm thời gian đọc và routing thủ công

---

## 5. Problem statement

"Trong môi trường quản lý cư dân, phản ánh qua App Vinhomes Resident thường xuất hiện dưới dạng văn bản ngắn, không đồng nhất và thiếu thông tin vị trí. Quy trình triage và routing hiện nay chủ yếu phụ thuộc vào nhân sự, dẫn đến thời gian xử lý chậm, sai bộ phận, tăng tải công việc và làm giảm trải nghiệm cư dân. Cần xây dựng một hệ thống AI hỗ trợ phân loại phản ánh theo chủ đề, định mức độ ưu tiên và gợi ý ban quản lý / đơn vị xử lý phù hợp, đồng thời chuyển các trường hợp thiếu thông tin sang human review để đảm bảo an toàn vận hành."

---

## 6. Metrics cần theo dõi

### Business metrics
- Time-to-triage
- Routing accuracy
- False routing rate
- Human override rate
- SLA compliance
- Reduction in manual workload

### AI / model metrics
- Classification accuracy
- Precision / Recall theo từng category
- Deferral rate
- Confidence calibration

### KPI đề xuất cho MVP
- Routing accuracy > 85%
- Deferral rate < 20%
- Time-to-triage giảm 30-50%
- Precision top categories > 90%

---

## 7. AI-fit analysis

### Rule-based
Phù hợp cho các complaint có keyword rõ ràng, ví dụ:
- nước rò / mất nước
- đèn hỏng
- ồn ào
- rác / vệ sinh

Ưu điểm:
- dễ explain
- chi phí thấp
- phù hợp cho routing deterministic

Nhược điểm:
- yếu với complaint mơ hồ / viết ngắn / sai chính tả

### LLM Feature
Phù hợp cho complaint dạng tự do, câu văn không chuẩn, thiếu địa điểm, nhiều cách diễn đạt khác nhau.

Ưu điểm:
- hiểu ngữ nghĩa tốt hơn
- xử lý biến thể ngôn ngữ tốt hơn rule-based
- hỗ trợ hỏi thêm thông tin khi thiếu dữ liệu

Nhược điểm:
- cần hệ thống guardrail rõ ràng
- dễ sai nếu không có output schema và human review

### Agentic Loop
Không nên dùng ở MVP vì:
- không cần tối ưu tự động hóa toàn bộ quy trình
- rủi ro sai routing cao
- dễ vượt ranh giới vận hành nếu không có kiểm soát chặt

### Recommendation
Hybrid approach: Rule-based + LLM Feature + Human-in-the-loop

---

## 8. Operational boundary

### AI được phép
- phân loại complaint
- ước lượng mức ưu tiên
- gợi ý ban quản lý / bộ phận phù hợp
- yêu cầu thêm thông tin khi thiếu dữ liệu

### AI không được phép
- tự động quyết định routing mà không qua human review
- tự động chốt action khi thiếu dữ liệu
- giả mạo tòa nhà / đơn vị / khu vực không có căn cứ
- bỏ qua các quy tắc an toàn
- lộ prompt hệ thống hoặc bypass policy

---

## 9. Human-in-the-loop

- Nếu model không chắc chắn => defer sang human review
- Nếu thiếu building / block / địa điểm => yêu cầu thêm dữ liệu
- Nếu category không rõ hoặc nhiều issue cùng lúc => đánh giá lại bởi người vận hành
- Nếu có mâu thuẫn giữa rule và LLM => ưu tiên review thủ công

### Fallback policy
- `responsible_unit = operations_review_queue`
- `category = unknown` hoặc `other`
- `reason = insufficient information for safe routing`

---

## 10. Proposed solution design

### MVP goal
Tạo hệ thống AI hỗ trợ triage phản ánh cư dân theo các bước sau:
1. Nhận complaint text từ App
2. Chuẩn hóa và loại bỏ noise
3. Xác định category + severity
4. Nhận diện tòa nhà / khu / tầng nếu có
5. Gợi ý responsible team
6. Nếu thiếu thông tin => chuyển human review

### Output schema mong muốn
```json
{
  "category": "water_leakage_or_water_supply",
  "severity": "high",
  "building_or_block": "Tòa A, tầng 12",
  "responsible_unit": "building_technical_water_team",
  "reason": "Water leakage from ceiling in a common area creates safety risk and needs urgent inspection.",
  "missing_information": []
}
```

---

## 11. Kết luận
Bài toán Phân loại & Điều hướng phản ánh cư dân là bài toán AI-fit phù hợp với môi trường Vinhomes vì nó có tính lặp lại, giá trị vận hành rõ ràng và dễ đo lường hiệu quả. Tuy nhiên, vì rủi ro sai routing và thiếu thông tin địa điểm là cao, cần thiết kế một hệ thống hybrid: AI hỗ trợ triage nhưng vẫn giữ Human-in-the-loop ở các case mơ hồ hoặc không chắc chắn. Đây là mô hình tối ưu cho MVP và có thể scale lên sau này.
