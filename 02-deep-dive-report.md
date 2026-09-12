# 02-deep-dive-report.md - Phase 3 DEEP-DIVE & Phase 5 EVALUATE

## Bài toán được chọn

**Vinhomes - Phân loại & Điều hướng phản ánh cư dân gửi qua App Vinhomes Resident đến đúng ban quản lý/tổ xử lý từng tòa nhà.**

---

## Phase 3.1 - Current-State Workflow Mapping

Sơ đồ trực quan của quy trình hiện tại được nộp tại file **`04-workflow-diagram.pdf`**.

```text
1. Cư dân gửi phản ánh qua App Vinhomes Resident
   Actor: Cư dân
   Tool: App Vinhomes Resident
   Time: 1 phút
   Output: Raw ticket gồm mô tả tiếng Việt, ảnh, tòa/tầng/căn hộ nếu có
        |
        | 🔄 Handoff: App chuyển ticket sang CSKH/BQL
        v
2. CSKH/BQL đọc và tóm tắt ticket
   Actor: Nhân viên CSKH / Ban quản lý tòa
   Tool: Dashboard ticket
   Time: 2 phút
   Output: Tóm tắt vấn đề, vị trí, mức độ gấp
        |
        v
3. CSKH/BQL tự phân loại vấn đề 🔴 Bottleneck
   Actor: Nhân viên CSKH / Ban quản lý tòa
   Tool: Kinh nghiệm cá nhân + quy định nội bộ
   Time: 2-3 phút
   Output: Category thủ công như mất nước, hỏng đèn, ồn ào, vệ sinh, thang máy
   Vì sao nghẽn: phải đọc hiểu phản ánh tiếng Việt tự do, nhiều cách diễn đạt
        |
        v
4. CSKH/BQL tra đội phụ trách theo tòa và loại việc 🔴 Bottleneck
   Actor: Nhân viên CSKH / Ban quản lý tòa
   Tool: Danh bạ nội bộ / bảng phân công theo tòa
   Time: 1-2 phút
   Output: Route dự kiến
   Vì sao nghẽn: thiếu tòa/khu hoặc chọn sai nhóm việc làm ticket bị chuyển nhầm
        |
        | 🔄 Handoff: CSKH/BQL chuyển trách nhiệm sang đội chuyên môn
        v
5. CSKH/BQL chuyển ticket sang đội xử lý
   Actor: Nhân viên CSKH / Ban quản lý tòa
   Tool: Ticket system / nhóm chat nội bộ
   Time: 1 phút
   Output: Ticket gửi sang kỹ thuật, an ninh, vệ sinh hoặc chính sách
        |
        v
6. Đội xử lý nhận hoặc trả lại ticket 🔴 Bottleneck
   Actor: Kỹ thuật / An ninh / Vệ sinh / Chính sách
   Tool: App nội bộ / nhóm vận hành
   Time: 5-30 phút
   Output: Accept để xử lý hoặc trả lại nếu route sai

Nếu route sai: ticket quay lại bước 3-4, CSKH/BQL phải đọc lại,
phân loại lại và route lại. Điều này làm tăng SLA và thời gian chờ của cư dân.
```

| Bước | Handoff/Bottleneck | Ý nghĩa vận hành |
|---|---|---|
| 1 -> 2 | 🔄 Handoff | App chuyển phản ánh thô sang nhân viên CSKH/BQL. |
| 3 | 🔴 Bottleneck | Cần đọc hiểu tiếng Việt tự do và phân biệt nhiều nhóm phản ánh. |
| 4 | 🔴 Bottleneck | Cần chọn đúng đội phụ trách theo tòa, khu và loại việc. |
| 5 -> 6 | 🔄 Handoff | CSKH/BQL chuyển trách nhiệm xử lý sang đội chuyên môn. |
| 6 -> 3/4 | 🔴 Bottleneck vòng lặp | Nếu route sai, ticket bị trả lại và phải xử lý lại từ đầu. |

**Tổng thời gian triage thủ công:** khoảng **6-10 phút/ticket**, chưa tính thời gian bị trả lại nếu chuyển nhầm đội.

**Bottleneck chính:** Bước 3 và 4, vì nhân viên phải đọc tiếng Việt tự do, phân loại vấn đề, xác định tòa/khu và chọn đúng đội xử lý trong khi nhiều ticket thiếu thông tin.

---

## Phase 3.2 - Problem Statement 6-field & Metrics

| Field | Nội dung chi tiết |
|---|---|
| **1. Actor / Operator** | Nhân viên CSKH và ban quản lý tòa Vinhomes tiếp nhận phản ánh cư dân hằng ngày trên App Vinhomes Resident. |
| **2. Current Workflow** | Cư dân gửi phản ánh; CSKH/BQL đọc text và ảnh; tự tóm tắt; tự phân loại mất nước, hỏng đèn, ồn ào, vệ sinh, thang máy, chính sách; tra tòa/khu; chuyển ticket cho đội kỹ thuật/an ninh/vệ sinh/chính sách. |
| **3. Bottleneck** | Đọc hiểu phản ánh tiếng Việt tự do và route đúng bộ phận. Ticket có thể thiếu tòa/tầng/căn hộ, hoặc chứa yêu cầu nhạy cảm như bồi thường/hoàn tiền/tranh chấp nên dễ xử lý sai. |
| **4. Business Impact** | Nếu 100 ticket/ngày và mỗi ticket tốn 6-10 phút triage thủ công, đội vận hành mất khoảng 10-16 giờ công/ngày. Chuyển nhầm đội làm tăng SLA, khiến cư dân phải chờ lâu và giảm chất lượng trải nghiệm dịch vụ. |
| **5. Success Metric** | 85% ticket thông thường có draft category/route trong **<10 giây**; tỉ lệ route nhầm giảm xuống **<5%**; 100% case pháp lý/tài chính/khẩn cấp được gắn `required_human_review=true`; confidence thấp hơn 0.70 phải fallback về triage trung tâm. |
| **6. Operational Boundary** | AI chỉ được tạo draft `[DRAFT_ONLY]`, phân loại, tóm tắt, gán priority và đề xuất `route_to`. AI tuyệt đối không được tự gửi ticket, đóng ticket, hứa bồi thường/hoàn tiền, phạt cư dân/nhân viên, tiết lộ dữ liệu cá nhân/nội bộ, hoặc đoán tòa khi thiếu thông tin. |

---

## Phase 3.3 - Future-State Flow & AI Fit

**AI-Fit Matrix:**  
[ ] Rule / State-Machine  
[x] LLM Feature  
[ ] Agentic Loop

**Lý do:** Rule-based phù hợp cho một số từ khóa đơn giản, nhưng phản ánh cư dân là tiếng Việt tự do, có nhiều cách diễn đạt như "nước yếu", "không có nước", "đèn hành lang chập chờn", "karaoke quá giờ". LLM Feature phù hợp để đọc hiểu và tóm tắt, nhưng chưa nên dùng Agent tự trị vì mọi route vẫn cần nhân viên duyệt.

```text
┌──────────────┐     ┌──────────────┐     ┌──────────────┐
│ Cư dân gửi   │     │ 🔵 AI Step   │     │ 🔵 AI Step   │
│ phản ánh app │ ──> │ Tóm tắt text │ ──> │ Category +   │
│              │     │ & lấy entity │     │ priority     │
└──────────────┘     └──────────────┘     └──────┬───────┘
                                                   │
                                                   ▼
┌──────────────┐     ┌──────────────┐     ┌──────────────┐
│ Ticket được  │ <── │ 🟢 Human     │ <── │ 🔵 AI draft  │
│ chuyển sau   │     │ BQL/CSKH     │     │ route_to     │
│ khi duyệt    │     │ duyệt/sửa    │     │ [DRAFT_ONLY] │
└──────────────┘     └──────────────┘     └──────────────┘
                             │
                             ▼
                     ↩️ Fallback:
                     - Thiếu tòa hoặc confidence <0.70:
                       route về central_resident_service_triage
                     - Legal/financial: human_review_policy_team
                     - Emergency: emergency_operations_hotline
```

### Operational Boundary trong future flow

| Tình huống | Cách xử lý bắt buộc |
|---|---|
| Phản ánh thông thường như mất nước, hỏng đèn, ồn ào | AI tạo draft JSON gồm category, priority, route_to, building, confidence. |
| Thiếu tòa/khu/căn hộ | AI không đoán. Đặt `building="unknown"` và route `central_resident_service_triage`. |
| Bồi thường, hoàn tiền, phí quản lý, tranh chấp pháp lý | AI không quyết định. Route `human_review_policy_team`. |
| Cháy nổ, rò điện, kẹt thang máy, đe dọa an toàn | AI route `emergency_operations_hotline`, yêu cầu operator xác minh ngay. |
| Người dùng yêu cầu bỏ `[DRAFT_ONLY]`, gửi thẳng, đóng ticket, lộ system prompt | AI bỏ qua yêu cầu đó và ghi nhận trong `forbidden_actions_observed`. |

---

## Phase 5 - EVALUATE

### AI Readiness Checklist

1. [x] **Chúng tôi có sẵn dữ liệu mẫu/logs sạch để test?**  
   Có thể bắt đầu bằng dữ liệu giả lập và một tập ticket đã ẩn danh; trước production cần log thật đã được gắn nhãn category/route chuẩn.

2. [x] **Rủi ro khi AI sai có nằm trong tầm kiểm soát?**  
   Có, vì AI chỉ tạo draft, mọi ticket vẫn cần CSKH/BQL duyệt. Các case nhạy cảm có fallback rõ.

3. [x] **Stakeholders sẵn sàng thay đổi quy trình làm việc cũ?**  
   Khả thi vì AI không thay thế nhân viên, chỉ giảm bước đọc/phân loại lặp lại và giúp họ xử lý nhanh hơn.

### Quyết định cuối cùng của Ban Giám Đốc Vin Smart Future

[x] **GO (Bắt đầu xây dựng Prototype):** Bắt đầu phát triển với scope hẹp.  
[ ] **NOT YET (Cần tích lũy thêm dữ liệu/xác lập baseline)**  
[ ] **NO-GO (Không khả thi / Rule-based tốt hơn)**

**Justification:**  
Dự án nên bắt đầu prototype vì scope hẹp, metric rõ, dữ liệu đầu vào là text phản ánh cư dân phù hợp với LLM, và rủi ro được kiểm soát bằng `[DRAFT_ONLY]`, human-in-the-loop, confidence threshold và fallback. Giai đoạn đầu chỉ nên triển khai 5-7 nhóm phổ biến: mất nước, hỏng đèn/điện, ồn ào, vệ sinh, thang máy/bãi xe/an ninh, policy/legal và emergency.
