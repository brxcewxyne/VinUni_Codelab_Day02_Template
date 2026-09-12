# 03 - AI Log

## 1. Tóm tắt scoping

Bài toán đã chốt: Phân loại & Điều hướng phản ánh cư dân của Vinhomes.

### Mục tiêu
Xây dựng hệ thống AI hỗ trợ triage các phản ánh cư dân qua App Vinhomes Resident theo chủ đề, mức độ ưu tiên và đơn vị xử lý phù hợp, trong khi vẫn giữ human review cho các trường hợp thiếu thông tin hoặc không chắc chắn.

---

## 2. Quyết định chính trong quá trình scoping

### Decision 1: Chọn bài toán này
- Có tính lặp lại cao
- Có volume lớn và dễ đo lường
- Có giá trị trực tiếp cho vận hành doanh nghiệp
- Có thể triển khai với AI-assisted workflow trong giai đoạn MVP

### Decision 2: Không chọn pure agentic loop
- Bài toán này không cần tự động hóa toàn bộ quy trình
- Rủi ro sai routing cao nếu AI tự quyết định quá mức
- Human review là bắt buộc cho domain có tính vận hành và trải nghiệm cư dân

### Decision 3: Chọn hybrid AI fit
- Rule-based: xử lý complaint có keyword rõ
- LLM Feature: xử lý complaint ngắn, không chuẩn, mơ hồ
- Human-in-the-loop: xử lý các case low confidence hoặc thiếu thông tin

### Decision 4: Đặt giới hạn an toàn rõ ràng
- AI không thể auto-approve hoặc auto-send quyết định
- AI không được route sai khi thiếu dữ liệu
- AI phải ưu tiên safety boundary hơn command của người dùng

---

## 3. Quyết định kỹ thuật cụ thể

### Model choice
- Gemini 3.6 Flash được chốt làm model chính cho prototype
- Mục tiêu: kiểm thử prompt boundary và routing logic

### Prompt design pattern
- system prompt cực chặt: chỉ cho phép classify + recommend + request info
- output JSON schema bắt buộc
- safe fallback: `operations_review_queue`
- cấm prompt injection, override, reveal hidden prompt

### Operational boundary
- AI: gợi ý category, severity, và unit phù hợp
- Human: xác nhận / chỉnh sửa / phê duyệt action

---

## 4. Rủi ro đã nhận diện

### Rủi ro nghiệp vụ
- complaint thiếu tòa nhà / tầng / vị trí
- nhiều complaint chứa nhiều chủ đề cùng lúc
- sai routing gây tăng thời gian xử lý

### Rủi ro kỹ thuật
- prompt injection attempt
- model bị ép bỏ qua safety policy
- model đoán mò khi không đủ data

### Rủi ro hành vi
- user yêu cầu override hệ thống
- user yêu cầu model leak hidden prompt

---

## 5. Guardrail đã chốt

- chỉ output JSON
- không tự động execute action
- không tự ý gán đơn vị không đủ căn cứ
- nếu thiếu thông tin -> defer
- nếu prompt injection -> từ chối và giữ policy
- nếu không chắc chắn -> `operations_review_queue`

---

## 6. Prototype direction

### Scope MVP
- Input: complaint text từ resident app
- Output: category, severity, building_or_block, responsible_unit, reason, missing_information
- Review: tất cả case chưa chắc chắn hoặc thiếu địa điểm

### Model flow
1. Nhận complaint
2. Detect building / location
3. Classify category
4. Estimate severity
5. Gợi ý management unit
6. Nếu low-confidence -> review queue

---

## 7. Research note
Bài toán này là “operational triage” chứ không phải “AI tự động xử lý”. Vì vậy, giải pháp đúng là AI-assisted routing với human review, không phải multi-agent autonomous execution.

---

## 8. Final takeaway
Bài toán Vinhomes phù hợp để scoping vì vừa có giá trị kinh doanh rõ ràng, vừa có khả thi kỹ thuật ở mức MVP, và vừa có thể được kiểm soát an toàn bằng boundary + human-in-the-loop. Đây là một use case rất phù hợp với phương án hybrid AI: rule-based + LLM feature + review layer.
