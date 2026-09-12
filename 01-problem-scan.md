# Phase 1 — SCAN

### List bài toán của tôi:
| # | Subsidiary (VinFast/Xanh SM...) | Lens | Mô tả ngắn bài toán |
|---|----------------------------------|------|---------------------|
| 1 | **Vinhomes** | Lặp lại | Phân loại phản ánh cư dân từ App Vinhomes Resident (mất nước, hỏng đèn, ồn ào, thang máy, vệ sinh, an ninh…) |
| 2 | **Vinhomes** | Tốn thời gian | Việc nhân viên quản lý tòa nhà đọc từng phản ánh và phân loại thủ công mất nhiều thời gian, đặc biệt khi số lượng lớn vào cuối ngày |
| 3 | **Vinpearl** | Pain from stakeholder | Review khách sạn từ Booking/Agoda/Google Map bị trễ, không được lọc theo mức độ khẩn cấp và đang chảy rất nhiều thông tin không có cấu trúc |
| 4 | **Xanh SM** | AI upgrade | Tài xế gửi tin nhắn hoặc cảnh báo sự cố sạc/pin, điều phối viên phải đọc rồi manual route thông tin |
| 5 | **Vinmec** | Tốn thời gian | Nhân viên tiếp nhận bệnh nhân mô tả triệu chứng qua chat, cần route đúng chuyên khoa nhanh hơn |

---

# Phase 2 — QUICK-ASSESS

```
┌─────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #1                                       │
│                                                             │
│ Bài toán (1 câu): Cư dân gửi phản ánh qua App Vinhomes      |
| Resident, nhân viên phải đọc, phân loại và chuyển đến đúng  |
| bộ phận xử lý.                                              │
│ Công ty thành viên: [ ] VinFast  [ ] Xanh SM  [x] Vinhomes  │
│                     [ ] Vinmec   [ ] Khác (Ghi rõ)________  │
│                                                             │
│ Ai đang đau (Actor)? Cư dân (chờ phản hồi lâu), Ban quản lý |
| tòa nhà và đội kỹ thuật (phải xử lý thủ công từng ticket).  │
│                                                             │
│ Workflow thủ công hiện tại (5 bước):                        │
│   1. Cư dân gửi khiếu nại qua app / hotline                 |
|   → 2. Nhân viên tiếp nhận đọc nội dung                     |
|   → 3. Phân loại theo danh mục (đèn, nước, máy giặt, ...)   |
|   → 4. Chuyển đến bộ phận phù hợp                           |
|   → 5. Theo dõi phản hồi và báo lại cho cư dân              │
│                                                             │
│ Bước nào tốn thời gian/lỗi nhất? 2–4 (⏱ 10–15 phút/lượt)    │
│ AI có thể nhảy vào hỗ trợ ở bước nào? Bước 2–4              |
│ (phân loại, gợi bộ phận, tóm tắt, ưu tiên mức độ khẩn cấp)  |
│                                                             │
│ Đo thành công bằng gì (Metric có số)?                       │
│ Giảm thời gian triage từ 10–15 phút ──> dưới 3 phút, giảm   |
| tỷ lệ route sai từ ~25% ──> dưới 10%, tăng tỷ lệ phản hồi   |
| cư dân trong SLA từ 70% ──> 90%.                            │
│                                                             │
│ Quick Architecture: [ ] No AI  [ ] Rule  [x] LLM  [ ] Agent │
└─────────────────────────────────────────────────────────────┘
```

```
┌─────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #3                                       │
│                                                             │
│ Bài toán (1 câu): Review từ Booking, Agoda, Google Maps     │
│ chảy rất nhiều và manager khó lọc các phàn nàn khẩn cấp.    │
│ Công ty thành viên: [ ] VinFast  [ ] Xanh SM  [ ] Vinhomes  │
│                     [x] Vinpearl [ ] Vinmec   [ ] Khác      │
│                                                             │
│ Ai đang đau (Actor)? Manager khách sạn, bộ phận vận hành,   │
│ phòng QC và đội ngũ chăm sóc khách hàng.                    │
│                                                             │
│ Workflow thủ công hiện tại (5 bước):                        │
│   1. Thu thập review từ các nền tảng thuê phòng             │
│   → 2. Đọc từng review                                      │
│   → 3. Lọc phàn nàn khẩn cấp và mức độ nghiêm trọng         │
│   → 4. Gửi báo cáo cho quản lý và bộ phận xử lý             │
│   → 5. Theo dõi phản hồi và hành động khắc phục             │
│                                                             │
│ Bước nào tốn thời gian/lỗi nhất? Bước 2–4 (⏱ 20–30 phút/    │
│ lượt/ngày)                                                  │
│ AI có thể nhảy vào hỗ trợ ở bước nào? Bước 2–4              │
│ (tổng hợp, phân loại, ưu tiên mức độ khẩn cấp)              │
│                                                             │
│ Đo thành công bằng gì (Metric có số)?                       │
│ Giảm thời gian lọc review từ 30 phút ──> dưới 8 phút; tăng  │
│ tỷ lệ phát hiện issue khẩn cấp lên > 90%.                   │
│                                                             │
│ Quick Architecture: [ ] No AI  [ ] Rule  [x] LLM  [ ] Agent │
└─────────────────────────────────────────────────────────────┘
```

```
┌─────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #4                                       │
│                                                             │
│ Bài toán (1 câu): Tài xế báo sự cố pin giữa đường, điều     │
│ phối viên phải tra cứu vị trí xe, trạm sạc và soạn tin nhắn │
│ chỉ đường.                                                  │
│ Công ty thành viên: [ ] VinFast  [x] Xanh SM  [ ] Vinhomes  │
│                     [ ] Vinmec   [ ] Khác (Ghi rõ)________  │
│                                                             │
│ Ai đang đau (Actor)? Tài xế đang chờ cứu hộ, điều phối viên │
│ quá tải trong giờ cao điểm.                                 │
│                                                             │
│ Workflow thủ công hiện tại (5 bước):                        │
│   1. Tài xế báo pin yếu / hết pin                           │
│   → 2. Điều phối viên tra cứu vị trí xe                     │
│   → 3. Tìm trạm sạc gần nhất / phù hợp                      │
│   → 4. Soạn tin nhắn hướng dẫn cho tài xế                   │
│   → 5. Gọi xe cứu hộ nếu cần                                │
│                                                             │
│ Bước nào tốn thời gian/lỗi nhất? Bước 3–4 (⏱ 12–15 phút/    │
│ lượt)                                                       │
│ AI có thể nhảy vào hỗ trợ ở bước nào? Bước 3–4              │
│ (gợi trạm, soạn draft, ưu tiên an toàn)                     │
│                                                             │
│ Đo thành công bằng gì (Metric có số)?                       │
│ Giảm thời gian xử lý sự cố từ 15 phút ──> dưới 3 phút; giảm │
│ tỷ lệ đề xuất trạm không an toàn và lỗi route.              │
│                                                             │
│ Quick Architecture: [ ] No AI  [ ] Rule  [x] LLM  [ ] Agent │
└─────────────────────────────────────────────────────────────┘
```

---

# 🗳️ Quyết định lựa chọn của nhóm:
Nhóm quyết định chọn bài toán **"Card #1 — Phân loại & điều hướng phản ánh cư dân"** để thực hiện Deep-Dive vì bài toán lặp lại hằng ngày, có dữ liệu text tiếng Việt rõ ràng, có thể đo bằng SLA/tỉ lệ route đúng, và rủi ro có thể kiểm soát bằng [DRAFT_ONLY] cùng human-in-the-loop.

---