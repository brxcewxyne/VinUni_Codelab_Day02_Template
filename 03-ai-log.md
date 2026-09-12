# 03-ai-log.md - Phase 6 REFLECTION

## 1. Tôi đã dùng AI để làm gì?

Tôi dùng AI như một thought-partner để biến ý tưởng **"Vinhomes - Phân loại & Điều hướng phản ánh cư dân"** thành một bài toán AI Product Scoping bao gồm các công việc: scan cơ hội, quick cards, deep-dive workflow, problem statement 6-field, future-state flow, operational boundary và prompt prototype bằng Python.

Tôi viết prompt cho AI thực hiện prototype có structured output JSON, test cases tấn công và boundary checks.

## 2. AI giúp gì tốt nhất?

AI giúp tốt nhất ở phần đặt **Operational Boundary**. Nếu chỉ nghĩ đơn giản, tôi có thể để AI tự phân loại và tự chuyển ticket. Nhưng sau khi trao đổi, tôi nhận ra rủi ro vận hành của Vinhomes nằm ở việc AI có thể:

- Chuyển ticket sai đội phụ trách.
- Tự hứa bồi thường/hoàn tiền.
- Đóng ticket khi chưa có người xử lý.
- Đoán tòa/khu khi cư dân ghi thiếu thông tin.
- Lộ dữ liệu cá nhân hoặc nội dung nội bộ.

Vì vậy prototype được giới hạn: AI chỉ tạo `[DRAFT_ONLY]`, luôn có `required_human_review=true`, confidence thấp thì fallback về triage trung tâm, case pháp lý/tài chính thì route cho human policy team, case khẩn cấp thì route hotline vận hành.

## 3. AI trả lời sai hoặc chưa phù hợp ở đâu?

Ban đầu file starter code trong repo dùng ví dụ **Xanh SM** với rule về pin xe và `dispatch_mobile_charger`, không khớp với đề tài tôi chọn là **Vinhomes**. Nếu giữ nguyên, bài code có thể pass một số check kỹ thuật nhưng không phản ánh đúng bài toán cá nhân.

Một điểm khác là khi chạy script bằng API key thật, model `gemini-2.5-flash` báo lỗi không còn khả dụng cho user mới. Output nhìn hơi lạ vì chương trình in warning từ SDK/API rồi mới fallback sang local simulator. Đây là lỗi cấu hình model, không phải lỗi logic của prompt. Cách sửa là cho phép cấu hình `GEMINI_MODEL` trong `.env` và đổi default sang model mới.

## 4. Tôi đã sửa prompt/ranh giới như thế nào?

Tôi sửa prompt theo hướng cụ thể hơn:

- Vai trò: `Vinhomes Resident Triage Copilot`, trợ lý nội bộ cho CSKH/BQL.
- Nhiệm vụ: đọc phản ánh, phân loại category, gán priority, route đúng đội, tạo summary.
- Output: JSON object duy nhất, có `draft_tag`, `category`, `priority`, `route_to`, `building`, `confidence`, `required_human_review`.
- Ranh giới: không tự gửi, không đóng ticket, không hứa bồi thường, không tiết lộ dữ liệu, không đoán tòa.
- Test tấn công: yêu cầu bỏ `[DRAFT_ONLY]`, tự bồi thường/đóng ticket, route tất cả tòa, tiết lộ system prompt.

## 5. Bài học cá nhân

Bài học lớn nhất là scoping sản phẩm AI phải bắt đầu từ workflow thật, metric thật và rủi ro vận hành, không bắt đầu từ việc "dùng AI cho hay". Với Vinhomes, AI có giá trị nhất khi giảm thời gian đọc/phân loại phản ánh lặp lại, nhưng quyền quyết định cuối vẫn phải nằm ở nhân viên CSKH/ban quản lý.

Prototype tốt không chỉ là prompt trả lời hay, mà phải có ranh giới: AI được làm gì, không được làm gì, khi nào cần fallback, và con người duyệt ở bước nào.
