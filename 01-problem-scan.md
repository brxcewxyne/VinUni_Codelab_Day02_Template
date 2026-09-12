# 01-problem-scan.md - Phase 1 SCAN & Phase 2 QUICK-ASSESS

## Phase 1 - SCAN: List bài toán của tôi

| # | Subsidiary (VinFast/Xanh SM...) | Lens | Mô tả ngắn bài toán |
|---|---|---|---|
| 1 | **Vinhomes** | **Lặp lại** | Phân loại tự động các phản ánh/khiếu nại như mất nước, hỏng đèn, ồn ào, vệ sinh, thang máy gửi qua App Vinhomes Resident đến đúng ban quản lý từng tòa nhà. |
| 2 | **Vinhomes** | **Tốn thời gian** | Nhân viên CSKH phải đọc từng phản ánh dài, tóm tắt nội dung, kiểm tra tòa/tầng/căn hộ và hỏi lại cư dân khi thiếu thông tin. |
| 3 | **Vinhomes** | **AI có thể tốt hơn** | Trợ lý cư dân hỗ trợ thủ tục hành chính như đăng ký thi công nội thất, thẻ xe, đặt tiện ích, tra cứu phí quản lý. |
| 4 | **Vinpearl** | **Pain từ người khác** | Tự động tổng hợp review 1-2 sao từ Booking/Agoda/Google Maps để phát hiện phàn nàn khẩn cấp về phòng bẩn, thái độ nhân viên, đồ ăn. |
| 5 | **VinFast** | **Lặp lại** | Đối chiếu hóa đơn sạc điện đối tác với log giao dịch tại trạm sạc để phát hiện sai lệch thanh toán. |
| 6 | **Xanh SM** | **Tốn thời gian** | Phân tích lý do khách hủy chuyến từ ghi chú tài xế/cuộc gọi để gom nhóm các nguyên nhân gây rò rỉ doanh thu. |

---

## Phase 2 - QUICK-ASSESS: 3 Quick Problem Cards

```text
┌─────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #1                                      │
│                                                             │
│ Bài toán (1 câu): Phân loại & route phản ánh cư dân         │
│ Vinhomes Resident đến đúng ban quản lý/tổ xử lý từng tòa.   │
│                                                             │
│ Công ty thành viên: [ ] VinFast  [ ] Xanh SM  [x] Vinhomes  │
│                     [ ] Vinmec   [ ] Khác ________________  │
│                                                             │
│ Ai đang đau (Actor)?                                       │
│ - Nhân viên CSKH/ban quản lý tòa phải đọc và chuyển ticket  │
│   thủ công.                                                 │
│ - Cư dân phải chờ lâu hoặc bị chuyển nhầm bộ phận.          │
│                                                             │
│ Workflow thủ công hiện tại (3-5 bước):                      │
│   1. Cư dân gửi phản ánh qua App Vinhomes Resident          │
│   ──> 2. CSKH đọc nội dung, ảnh, tòa/tầng/căn hộ            │
│   ──> 3. CSKH tự phân loại: mất nước/hỏng đèn/ồn ào/...     │
│   ──> 4. CSKH tra đội phụ trách và chuyển ticket            │
│   ──> 5. Đội xử lý nhận hoặc trả lại nếu route sai           │
│                                                             │
│ Bước nào tốn thời gian/lỗi nhất? Bước 2-4 (5-8 phút/lượt)  │
│ AI có thể nhảy vào hỗ trợ ở bước nào? Bước 2-4: tóm tắt,   │
│ phân loại, gán priority, đề xuất route_to dạng draft.       │
│                                                             │
│ Đo thành công bằng gì (Metric có số)?                       │
│ 85% ticket có draft phân loại/route trong <10 giây; giảm    │
│ ticket chuyển nhầm xuống <5%; 100% case nhạy cảm có HITL.   │
│                                                             │
│ Quick Architecture: [ ] No AI  [ ] Rule  [x] LLM  [ ] Agent │
└─────────────────────────────────────────────────────────────┘
```

```text
┌─────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #2                                      │
│                                                             │
│ Bài toán (1 câu): Trợ lý cư dân draft câu trả lời cho các   │
│ thủ tục lặp lại như thẻ xe, thi công nội thất, đặt tiện ích.│
│                                                             │
│ Công ty thành viên: [ ] VinFast  [ ] Xanh SM  [x] Vinhomes  │
│                     [ ] Vinmec   [ ] Khác ________________  │
│                                                             │
│ Ai đang đau (Actor)?                                       │
│ - Cư dân phải đợi CSKH trả lời các câu hỏi thủ tục đơn giản.│
│ - CSKH trả lời lặp lại cùng một nhóm câu hỏi mỗi ngày.      │
│                                                             │
│ Workflow thủ công hiện tại (3-5 bước):                      │
│   1. Cư dân hỏi thủ tục qua app/hotline                     │
│   ──> 2. CSKH tìm quy định hoặc hỏi ban quản lý             │
│   ──> 3. CSKH soạn câu trả lời                              │
│   ──> 4. CSKH gửi checklist giấy tờ/biểu mẫu                │
│                                                             │
│ Bước nào tốn thời gian/lỗi nhất? Bước 2-3 (7-10 phút/lượt) │
│ AI có thể nhảy vào hỗ trợ ở bước nào? Tra FAQ đã duyệt,    │
│ draft câu trả lời và checklist hồ sơ để CSKH duyệt.         │
│                                                             │
│ Đo thành công bằng gì (Metric có số)?                       │
│ Giảm thời gian draft từ 8 phút xuống <1 phút; 90% câu hỏi   │
│ FAQ có câu trả lời đúng theo nguồn đã duyệt.                │
│                                                             │
│ Quick Architecture: [ ] No AI  [x] Rule  [x] LLM  [ ] Agent │
└─────────────────────────────────────────────────────────────┘
```

```text
┌─────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #3                                      │
│                                                             │
│ Bài toán (1 câu): Tổng hợp review xấu của Vinpearl để       │
│ phát hiện phàn nàn khẩn cấp và chuyển cho quản lý khách sạn.│
│                                                             │
│ Công ty thành viên: [ ] VinFast  [ ] Xanh SM  [ ] Vinhomes  │
│                     [ ] Vinmec   [x] Khác: Vinpearl         │
│                                                             │
│ Ai đang đau (Actor)?                                       │
│ - Quản lý khách sạn dễ bỏ sót review nghiêm trọng.          │
│ - Khách hàng không được xử lý nhanh sau trải nghiệm xấu.    │
│                                                             │
│ Workflow thủ công hiện tại (3-5 bước):                      │
│   1. Nhân viên mở từng nền tảng review                      │
│   ──> 2. Đọc review 1-2 sao                                 │
│   ──> 3. Copy/tóm tắt nội dung                              │
│   ──> 4. Phân loại phòng/nhân viên/vệ sinh/ẩm thực          │
│   ──> 5. Gửi quản lý bộ phận liên quan                      │
│                                                             │
│ Bước nào tốn thời gian/lỗi nhất? Bước 1-4 (30-45 phút/ngày)│
│ AI có thể nhảy vào hỗ trợ ở bước nào? Tổng hợp, sentiment, │
│ phân loại chủ đề và đánh dấu review cần xử lý trong ngày.   │
│                                                             │
│ Đo thành công bằng gì (Metric có số)?                       │
│ 95% review 1-2 sao được phát hiện trong ngày; giảm 50%      │
│ thời gian tổng hợp thủ công.                                │
│                                                             │
│ Quick Architecture: [ ] No AI  [ ] Rule  [x] LLM  [ ] Agent │
└─────────────────────────────────────────────────────────────┘
```

## Quyết định lựa chọn

Tôi chọn **Quick Problem Card #1 - Vinhomes Phân loại & Điều hướng phản ánh cư dân** để làm deep-dive vì bài toán lặp lại hằng ngày, có dữ liệu text tiếng Việt rõ ràng, có thể đo bằng SLA/tỉ lệ route đúng, và rủi ro có thể kiểm soát bằng `[DRAFT_ONLY]` cùng human-in-the-loop.
