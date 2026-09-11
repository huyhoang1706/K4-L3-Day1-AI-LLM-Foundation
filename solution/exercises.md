# K4 — Ngày 1: Bài Tập & Phản Ánh
## Khám Phá LLM API | Phiếu Thực Hành

**Thời lượng:** 4 tiếng
**Cách làm:** Trả lời từng câu ngay sau khi hoàn thành block tương ứng —
đừng để dồn hết về cuối buổi. Thay dòng `*Câu trả lời của bạn*` bằng câu
trả lời thật (chấm tự động sẽ đếm số câu đã trả lời).

---

## Block 1 — API Cơ Bản (trả lời sau Checkpoint 1)

### Câu 1.1 — Độ nhạy của temperature
Gọi `call_openai` với temperature 0.0, 0.5, 1.0 và 1.5 dùng prompt
**"Hãy kể cho tôi một sự thật thú vị về Việt Nam."**

**Bạn nhận thấy quy luật gì qua bốn phản hồi?** (2–3 câu)
> Càng để temperature thấp, câu trả lời của model càng ổn định và giống nhau giữa các lần gọi. Temperature cao làm cách diễn đạt đa dạng, sáng tạo hơn, nhưng đôi lúc cũng lan man hoặc kém chính xác hơn.

### Câu 1.2 — Chọn temperature cho sản phẩm
**Bạn sẽ đặt temperature bao nhiêu cho chatbot hỗ trợ khách hàng, và tại sao?**
> Mình sẽ đặt khoảng 0.2–0.3. Chatbot hỗ trợ khách hàng cần trả lời nhất quán, đúng chính sách và ít bịa thêm thông tin; vẫn để một chút linh hoạt để câu văn không quá máy móc.

### Câu 1.3 — Đánh đổi chi phí
Kịch bản: 10.000 người dùng hoạt động mỗi ngày, mỗi người gọi API 3 lần,
mỗi lần trung bình ~350 token đầu ra.

**Ước tính GPT-4o đắt hơn GPT-4o-mini bao nhiêu lần cho workload này? Nêu một
trường hợp GPT-4o xứng đáng với chi phí và một trường hợp nên dùng mini:**
> Mỗi ngày có khoảng 10.500.000 token đầu ra. Theo bảng giá trong bài, GPT-4o tốn khoảng 105 USD/ngày, còn GPT-4o-mini khoảng 6,3 USD/ngày, tức GPT-4o đắt hơn gần 16,7 lần. GPT-4o đáng dùng khi xử lý khiếu nại phức tạp cần suy luận kỹ; mini phù hợp cho các câu hỏi thường gặp như tra cứu giờ làm việc hoặc hướng dẫn cơ bản.

---

## Block 2 — System Prompt & Token (trả lời sau Checkpoint 2)

### Câu 2.1 — Sức mạnh của persona
Gọi `chat_with_system_prompt` hai lần với cùng câu hỏi
**"Giải thích blockchain là gì?"** nhưng hai system prompt khác nhau:
- "Bạn là giáo viên tiểu học, giải thích thật đơn giản cho trẻ 8 tuổi."
- "Bạn là chuyên gia tài chính, trả lời chuyên sâu bằng thuật ngữ kỹ thuật."

**Hai phản hồi khác nhau như thế nào (độ dài, từ vựng, ví dụ)? System prompt
ảnh hưởng đến hành vi model ra sao?** (3–4 câu)
> Với prompt dành cho trẻ 8 tuổi, câu trả lời thường ngắn, dùng từ quen thuộc và ví dụ như “cuốn sổ ghi chép chung”. Với prompt chuyên gia tài chính, câu trả lời dài hơn, có các từ như phi tập trung, giao dịch, đồng thuận và có thể nói về rủi ro. System prompt đặt vai trò, đối tượng và phong cách nên nó định hướng model chọn mức độ chi tiết và cách nói phù hợp.

### Câu 2.2 — tiktoken vs đếm từ
Chọn một đoạn văn tiếng Việt ~100 từ. So sánh số token theo `count_tokens`
(tiktoken) với ước lượng `số từ / 0.75` mà Part 1 đã dùng.

**Hai con số chênh nhau bao nhiêu phần trăm? Vì sao tiếng Việt thường tốn
nhiều token hơn tiếng Anh cùng độ dài?**
> Mình thử một đoạn 114 từ về đời sống ở Việt Nam: `count_tokens` cho 143 token, còn ước lượng số từ / 0,75 cho khoảng 152 token. Hai số lệch khoảng 6% (ước lượng cao hơn). Kết quả thay đổi theo nội dung; tiếng Việt có dấu, nhiều âm tiết tách bằng khoảng trắng và cách tách token không luôn trùng với “từ”, nên thường cần đo bằng tiktoken thay vì chỉ đếm từ.

---

## Block 3 — Streaming & Độ Bền (trả lời sau Checkpoint 3)

### Câu 3.1 — Trải nghiệm người dùng với streaming
**Streaming quan trọng nhất trong trường hợp nào, và khi nào thì
non-streaming lại phù hợp hơn?** (1 đoạn văn)
> Streaming quan trọng khi câu trả lời dài hoặc người dùng đang chờ trực tiếp, vì họ thấy chữ xuất hiện ngay và cảm giác phản hồi nhanh hơn. Non-streaming phù hợp khi cần nhận toàn bộ kết quả rồi mới xử lý, ví dụ lưu vào cơ sở dữ liệu, kiểm tra định dạng JSON hoặc gửi một thông báo ngắn.

### Câu 3.2 — Vì sao backoff theo cấp số nhân?
**So với delay cố định (ví dụ luôn chờ 1 giây), exponential backoff có lợi
thế gì khi API bị quá tải? Điều gì xảy ra nếu hàng nghìn client cùng retry
với delay cố định giống nhau?**
> Exponential backoff tăng thời gian chờ sau mỗi lần lỗi, giúp API có thời gian hồi phục và giảm số yêu cầu dồn vào cùng lúc. Nếu hàng nghìn client đều chờ đúng 1 giây rồi retry, chúng có thể cùng gửi lại một đợt lớn, làm API càng quá tải. Có thể thêm một chút thời gian ngẫu nhiên để các lần retry lệch nhau.

---

## Block 4 — Mini-Project (trả lời sau Checkpoint 4)

### Câu 4.1 — Thiết kế persona
**Bạn chọn persona gì cho trợ lý của mình? Viết lại system prompt đó và giải
thích 1–2 lựa chọn từ ngữ quan trọng trong prompt (ví dụ: vì sao yêu cầu
"trả lời ngắn gọn", vì sao chỉ định ngôn ngữ...):**
> Mình chọn persona “trợ lý học tập AI thân thiện”. System prompt: “Bạn là trợ lý học tập AI thân thiện. Hãy trả lời bằng tiếng Việt đơn giản, ngắn gọn, giải thích từng bước khi cần và nói rõ khi bạn không chắc chắn.” Cụm “tiếng Việt đơn giản” giúp người mới dễ hiểu, còn “nói rõ khi không chắc chắn” hạn chế việc trả lời quá tự tin khi thông tin chưa đủ.

### Câu 4.2 — Hạn chế & cải thiện
**Trợ lý của bạn hiện có hạn chế lớn nhất là gì (ví dụ: history chỉ 3 lượt,
không có bộ nhớ dài hạn, không kiểm duyệt nội dung...)? Đề xuất một cải
thiện cụ thể và mô tả ngắn cách triển khai:**
> Hạn chế lớn nhất là trợ lý chỉ nhớ vài lượt chat gần đây nên dễ quên mục tiêu học của người dùng. Có thể thêm bộ nhớ dài hạn: sau mỗi cuộc trò chuyện, lưu sở thích và mục tiêu đã được người dùng đồng ý vào cơ sở dữ liệu; khi mở cuộc trò chuyện mới, lấy các thông tin liên quan đưa vào prompt.

---

## Danh Sách Kiểm Tra Nộp Bài

- [ ] `python grade.py` — xem điểm tự động, mục tiêu ≥ 75/100
- [ ] Cả 4 checkpoint pytest đều pass
- [ ] Tất cả 9 câu trong file này đã được trả lời
- [ ] Đã copy bài làm vào folder `solution/`, push lên fork và dán link trên trang bài Lab ở VLearn trước 23:59 ngày 11/09/2026
