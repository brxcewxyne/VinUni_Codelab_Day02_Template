# Phase 3 — DEEP-DIVE

## 3.1. Current-State Workflow Mapping
Quy trình xử lý sự cố hết pin thực địa hiện tại của điều phối viên Xanh SM:

```text
┌──────────────┐     ┌──────────────┐     ┌──────────────┐     ┌──────────────┐
│ Bước 1       │     │ Bước 2       │     │ Bước 3       │     │ Bước 4       │
│ Cư dân gửi   │ ──→ │ Nhân viên    │ ──→ │ Phân loại    │ ──→ │ Route theo   │
│ phản ánh     │     │ tiếp nhận và │     │ nội dung     │     │ bộ phận      │
│ qua app      │     │ đọc tin nhắn │     │ (đèn, nước,  │     │ (kỹ thuật,   │
│ / hotline    │     │ thủ công     │     │ thang máy,   │     │ bảo vệ, vệ   │
│              │     │              │     │ vệ sinh...)  │     │ sinh, quản lý)│
│ Ai: CSKH /   │     │ Ai: Ban quản │     │ Ai: Ban quản │     │ Ai: Ban quản │
│ Nhân viên    │     │ lý / nhân sự │     │ lý / nhân sự │     │ lý / nhân sự │
│ ⏱ 2–3 phút  │     │ ⏱ 4–6 phút  │     │ ⏱ 4–6 phút 🔴│     │ ⏱ 3–5 phút 🔴│
│ In: Nội dung │     │ In: Ticket   │     │ In: Ticket   │     │ In: Category │
│ Out: Ticket  │     │ Out: Nội dung│     │ Out: Label   │     │ Out: Team    │
└──────────────┘     └──────────────┘     └──────────────┘     └──────────────┘
                                                                      │
                                                                      ▼
                                                               ┌──────────────┐
                                                               │ Bước 5       │
                                                               │ Theo dõi,    │
                                                               │ phản hồi và  │
                                                               │ cập nhật tình│
                                                               │ trạng xử lý  │
                                                               │ Ai: Ban quản │
                                                               │ lý / kỹ thuật│
                                                               │ ⏱ 3–5 phút  │
                                                               └──────────────┘
🔴 = Bottleneck
⏱ Tổng thời gian xử lý thủ công trung bình: 10–15 phút/lượt.
```

---

## 3.2. Problem Statement & Metrics

| Field | Nội dung |
|---|---|
| **1. Actor / Operator** | Nhân viên quản lý tòa nhà, bộ phận CSKH, và đội kỹ thuật Vinhomes đang xử lý phản ánh cư dân hằng ngày qua App Vinhomes Resident hoặc hotline. |
| **2. Current Workflow** | Cư dân gửi khiếu nại dưới dạng tin nhắn tự do (ví dụ: “đèn hành lang tầng 12 hỏng”, “nước tòa B yếu”, “thang máy kẹt khách”), nhân viên đọc tin nhắn, gán nhãn theo loại sự cố, sau đó chuyển cho đúng bộ phận xử lý và phản hồi lại cư dân. Quy trình hoàn toàn thủ công, chủ yếu dựa trên việc đọc văn bản và routing bằng tay. |
| **3. Bottleneck** | Bước đọc nội dung và phân loại ticket là điểm choke point: một ticket có thể không rõ danh mục, có nhiều cách mô tả khác nhau cho cùng một sự cố, đồng thời cần xác định bộ phận phù hợp nhanh để tránh chậm phản hồi. Đây là bước cần xử lý ngôn ngữ tự nhiên nhiều nhất. |
| **4. Business Impact** | Mỗi ngày có khoảng 200–300 phản ánh cư dân cần xử lý trên các tòa nhà lớn. Nếu triage chậm, SLA phản hồi bị trễ, cư dân mất niềm tin, bộ phận kỹ thuật nhận ticket sai nhánh, gây lãng phí thời gian và tăng nguy cơ khiếu nại. Tổn thất đo được bằng thời gian xử lý, mức độ hài lòng cư dân và tỉ lệ ticket route sai. |
| **5. Success Metric** | 1. Giảm thời gian triage từ 10–15 phút/lượt xuống dưới 3 phút.<br>2. Tăng tỷ lệ phân loại đúng danh mục lên trên 85–90%.<br>3. Giảm tỷ lệ route sai từ khoảng 25% xuống dưới 10%. |
| **6. Operational Boundary** | AI được phép: phân loại phản ánh, gợi nhãn, gợi mức độ ưu tiên, tóm tắt nội dung, và đề xuất bộ phận phù hợp. AI không được: tự ý đóng ticket, tự động chuyển trực tiếp cho đội kỹ thuật mà không có người duyệt, hoặc tự ra quyết định cho các sự cố an toàn cấp độ khẩn cấp như cháy, điện giật, thang máy kẹt người, ngập nước lớn, đe dọa an ninh. Mọi trường hợp nguy hiểm cần escalate lên nhân sự / quản lý / bảo vệ. |

---

## 3.3. Future-State Flow & AI Fit

* **AI Fit:** Chọn **LLM Feature** (bài toán này dựa trên văn bản tự do của cư dân, cần hiểu ngôn ngữ tự nhiên, nhưng quy trình vẫn có cấu trúc rõ: phân loại → ưu tiên → route → review. Không cần Agentic Loop vì không cần AI tự hành động trên nhiều hệ thống hoặc ra quyết định hoàn toàn độc lập.).
* **Quy trình tương lai (Future-State):**

```text
┌──────────────┐     ┌──────────────┐     ┌──────────────┐     ┌──────────────┐
│ Bước 1       │     │ Bước 2       │     │ Bước 3       │     │ Bước 4       │
│ Cư dân gửi   │ ──→ │ 🔵 AI parse  │ ──→ │ 🔵 AI gợi    │ ──→ │ 🟢 Nhân viên │
│ phản ánh     │     │ & phân loại  │     │ category +   │     │ duyệt & xác │
│              │     │ ngôn ngữ     │     │ ưu tiên +    │     │ nhận route  │
│              │     │ tự do        │     │ team route   │     │             │
└──────────────┘     └──────────────┘     └──────────────┘     └──────────────┘
                                                                      │
                                                                      ▼
                                                               ┌──────────────┐
                                                               │ Bước 5       │
                                                               │ ↩️ Fallback:  │
                                                               │ Nếu AI không │
                                                               │ chắc chắn,   │
                                                               │ chuyển về    │
                                                               │ manual triage│
                                                               └──────────────┘
```

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
Dự án nên bắt đầu prototype vì scope hẹp, metric rõ, dữ liệu đầu vào là text phản ánh cư dân phù hợp với LLM, và rủi ro được kiểm soát bằng `[DRAFT_ONLY]`, human-in-the-loop, confidence threshold và fallback.