# Phân tích UX tính năng Chatbot NEO - Vietnam Airlines

## Phần 1: Phân tích AI Marketing và Trải nghiệm thực tế (Test)

**AI Marketing (Kỳ vọng):**
- Triển khai đồng bộ trên Web, App, Facebook, Zalo.
- Lời hứa: Tra cứu chuyến bay, hỗ trợ khách hàng 24/7, trả lời nhanh, cá nhân hóa, tự động làm thủ tục.

**Trải nghiệm thực tế (Test thật):**
- **Hỏi danh sách chuyến bay đến một địa điểm:** Bot bắt buộc phải cung cấp mã số/số hiệu chuyến bay, không thể liệt kê danh sách dù người dùng đã cung cấp đầy đủ thông tin về địa điểm và ngày tháng.
- **Hỏi thủ tục:** Trả lời đầy đủ, có cung cấp đường link tham khảo.
- **Ngôn ngữ:** Hiểu được tiếng Việt không dấu.
- **Long context (Lưu giữ ngữ cảnh dài):** Kém, bot dễ quên thông tin và các truy vấn (queries) cũ.
- **Câu hỏi ngoài luồng (Out-of-domain):** Đôi lúc trả lời lộn xộn, tuy nhiên đôi lúc biết cách redirect lại sang chủ đề nằm trong domain.
- **Chất lượng phản hồi:** Reply thường bị trùng lặp, thiếu sự cá nhân hóa.

---

## Phần 2: Phân tích 4 Paths của User

### a. Trạng thái: AI đúng
- **Dạng câu hỏi:** Các truy vấn về chuyến bay (lưu ý phải có mã số, thời điểm) hoặc thông tin cơ bản về thủ tục.
- **Nhận xét:** 
  - Biết confirm (xác nhận) lại thông tin.
  - Câu trả lời rõ ràng, có đính kèm link tham khảo.
  - Có suggest (gợi ý) các việc cần làm tiếp theo.

### b. Trạng thái: AI chưa chắc chắn
- **Dạng câu hỏi:** Câu hỏi đơn giản, thiếu thông tin (info).
- **Nhận xét:** Bot có phản xạ xác nhận lại thông tin (ngày, mã chuyến bay) thay vì trả lời chung chung.
  - *Ví dụ:* Khi hỏi "Lịch bay ngày hôm kia", bot sẽ hỏi lại chính xác là ngày nào trong 3 ngày kể từ hiện tại để làm rõ.

### c. Trạng thái: AI sai
- **Lỗi phổ biến:** Đưa link không hoạt động do cú pháp đóng/mở ngoặc sai cách.
- **Nhận xét:**
  - Bot không thể tự phát hiện lỗi của chính mình. Khi user nhắc bot trả lời sai, bot chỉ biết escalate (chuyển hướng) tới tư vấn viên người thật.
  - 😞 Không có các nút Undo, Modify, hoặc Retry để người dùng điều chỉnh.
  - 😐 Có cung cấp nút "Nói chuyện với tư vấn viên".
  - 😞 Bắt user phải lặp lại câu hỏi từ đầu khi chuyển sang tư vấn viên.

### d. Trạng thái: User mất niềm tin
- **Quá trình:** Yêu cầu chuyển sang tư vấn viên -> Bot xác nhận việc chuyển (nhưng không có nút bấm tương tác) -> Thực chất vẫn là bot trả lời, nhưng có vẻ là dạng bot tự động hóa (không phải bản LLM) -> Quay lại yêu cầu người dùng tự tra cứu.
- **Kết quả:** Thiếu lối thoát (exit), thiếu nút liên hệ (contact button). Bot có xu hướng đẩy trách nhiệm trực tiếp lại cho người dùng.

---

## Phần 3: Sketch (Cải thiện User Flow)

### Hành trình hiện tại (As-is)
1. User chat với bot.
2. User yêu cầu gặp tư vấn viên (TVV).
3. Bot xác nhận: *"Vâng, chờ chút..."*
4. Không có nút CTA (Call-to-action) rõ ràng để hỗ trợ.
5. Kẹt trong luồng, user phải tự tra cứu lại từ đầu.

### Hành trình kỳ vọng (To-be)
1. User chat với bot.
2. Hệ thống kích hoạt trigger hỗ trợ (khi bot không xử lý được).
3. Hiển thị Menu trực quan với các lựa chọn: `[Gặp TVV]` hoặc `[Hotline]`.
4. Khi chọn gặp TVV: Bot tự động **tóm tắt (summarize) context** quá trình chat trước đó.
5. TVV nhận được thông tin tóm tắt và hỗ trợ khách hàng ngay lập tức.

### Kết luận sơ bộ về NEO
- Có khả năng cao NEO hiện tại đang là **Rule-based Bot** (Bot cấu trúc/luật), chưa phải là một **LLM Agent** hoàn chỉnh.
- Trải nghiệm UX còn khá tệ và nhiều lỗi:
  - Bot trả lời cứng ngắc, thiếu sự thân thiện.
  - Tốc độ phản hồi chậm (mất khoảng 20-30s).
  - Thiếu các nút tương tác trực quan (UI elements).
  - Đường link tham khảo thường bị lỗi.
  - Tin nhắn bị lặp lại nhiều lần.

---

## Phần 4: Đánh giá Feedback System & Tối ưu Model (Signals)

**Thực trạng:** Hiện tại, NEO mới chỉ thu nhận Explicit signals (Tín hiệu tường minh) từ người dùng qua chức năng đánh giá 1-5 sao.
- **Vấn đề:** Điều này là chưa đủ vì khách hàng thường lười/không muốn đánh giá sau mỗi lượt trả lời (gây phiền phức).
- **Giải pháp:** Cần bổ sung việc thu thập các **Implicit Signals** (Tín hiệu ngầm) và **Correction Signals** (Tín hiệu hiệu chỉnh).

### 1. Thu thập Implicit Signals (Tín hiệu ngầm)
- **Thời gian đọc:** Đo lường thời gian user dừng ở câu trả lời.
- **Tương tác đính kèm:** User có bấm mở link không? (Có -> Tích cực | Không -> Tiêu cực).
- **Hành vi hỏi tiếp:** 
  - Chuyển sang chủ đề mới -> Tích cực (Đã thỏa mãn với câu trả lời trước).
  - Vẫn lập lại câu hỏi cũ -> Tiêu cực (Câu trả lời chưa giải quyết được).
- **Drop-off:** User bỏ đi / đóng hội thoại trong lúc chờ bot -> Tiêu cực.
- **Tương tác đa hội thoại:** User bấm chọn vào các option bot gợi ý -> Tích cực.

**Quy trình thu thập:** 
> User hỏi -> NEO trả lời -> Theo dõi hành vi sau trả lời -> Suy luận chất lượng câu trả lời -> Lưu lại Signal -> Đóng góp vào cải thiện model.

### 2. Thu thập Correction Signals (Tín hiệu hiệu chỉnh)
- Bổ sung nút bấm `"Đúng"` / `"Không đúng"` sau mỗi câu trả lời của bot (Thumb up/down).
- Cho phép người dùng trực tiếp sửa lại câu hỏi hoặc clarify (làm rõ) ý ngay tại chỗ.
- Cung cấp tính năng Report nếu bot hoàn toàn hiểu sai intent hoặc trả lời thông tin không liên quan.

**Quy trình xử lý:**
- User chọn "Đúng" -> Ghi nhận Tích cực.
- User chọn "Không đúng" -> Hiển thị popup hỏi lý do (Sai chủ đề? Thiếu thông tin? Link hỏng? Giọng điệu không phù hợp?...) -> Cho phép user sửa lỗi -> Lưu trữ Correction Data (bao gồm: `query-answer gốc`, `nhãn lỗi`, `query sau sửa`, `intent đúng`...) -> Đóng góp vào cải thiện model.

### 3. Cải thiện Explicit Signals (Tín hiệu tường minh)
- **Gắn Tag thay vì Sao:** Thay vì chỉ chọn sao, cung cấp thêm các Quick-tags như: *"Rõ ràng"*, *"Đầy đủ"*, *"Chưa rõ"*, *"Sai lệch"*, v.v.
- **Thay đổi tần suất:** Không nên liên tục yêu cầu người dùng đánh giá gây khó chịu, chỉ kích hoạt khi kết thúc một flow hoặc một phiên giải quyết trọn vẹn.
