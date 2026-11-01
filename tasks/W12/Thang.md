# W12 — Thắng: Text Scan E2E, QR/VietQR, AI Worker & Scan History

**Owner:** Thắng
**Cross-tester:** Kiên
**Primary goal:** S02, S05, R03, R04 đạt G4, E-AI worker integration
**Priority:** P0
**Target Release:** v0.1.0 (Feature Freeze 26/10)

## S02 — Scan Tin Nhắn / Bài Đăng Giao Dịch (Text Scan E2E)

### User Journey

Người dùng vào trang 'Quét tin nhắn/văn bản' → dán nội dung tin nhắn SMS, bài đăng MXH hoặc email nghi ngờ → nhấn Quét → Text Worker xử lý chuẩn hóa tiếng Việt, bóc tách thực thể (URL, số điện thoại, STK, từ khóa cảnh báo) → Risk Engine phân tích → hiển thị RiskResult đầy đủ trên giao diện Web.

### FR

* FR01: Web form nhập nội dung văn bản có kiểm tra giới hạn độ dài.
* FR02: API POST /v1/scans/text đẩy job qua RabbitMQ thật.
* FR03: Text Worker bóc tách Regex và trích xuất thực thể nghi vấn.
* FR04: Nhận diện mẫu kịch bản lừa đảo phổ biến (mạo danh ngân hàng, tuyển dụng việc nhẹ lương cao, trúng thưởng...).
* FR05: Web hiển thị kết quả phân tích thời gian thực từ API thật.

### NFR

* Safe rendering (chống XSS) khi hiển thị lại nội dung văn bản trên giao diện kết quả.
* Không log nội dung riêng tư của người dùng ra log file.

### Manual UAT

* [ ] Quét tin nhắn lừa đảo OTP ngân hàng trả về kết quả RISKY kèm bằng chứng.
* [ ] Quét đoạn chat thông thường trả về kết quả SAFE.
* [ ] Dán đoạn text chứa link lừa đảo trích xuất đúng URL và chấm điểm chuẩn.

**Target: G4 do Kiên kiểm thử.**

---

## S05 — Quét / Upload Mã QR & VietQR (Nested Scan)

### User Journey

Người dùng tải ảnh mã QR hoặc chụp từ camera → Worker giải mã chuỗi dữ liệu trong QR: nếu là link web thì kích hoạt Quét URL (nested S01), nếu là chuỗi VietQR chuyển khoản thì bóc tách STK + Ngân hàng để kích hoạt Quét tài khoản ngân hàng (nested S04) → trả về kết quả phân tích tổng hợp.

### FR

* FR01: Giao diện Web hỗ trợ kéo thả upload ảnh QR hoặc dán hình ảnh từ clipboard.
* FR02: Module giải mã mã QR từ ảnh với tỷ lệ nhận diện cao.
* FR03: Phân loại nội dung QR: Web Link vs VietQR Payment String.
* FR04: Kích hoạt Nested Scan tương ứng và liên kết kết quả vào scan cha.
* FR05: Hiển thị giao diện kết quả rõ ràng: mã QR giải mã ra gì và mức độ an toàn.

### NFR

* File ảnh hỏng hoặc không chứa mã QR phải thông báo lỗi thân thiện, không crash worker.
* Giới hạn kích thước ảnh tải lên (< 5MB).

### Manual UAT

* [ ] Upload ảnh QR chứa link phishing phát hiện đúng link độc hại.
* [ ] Upload ảnh VietQR trích xuất đúng số tài khoản và mã ngân hàng.
* [ ] Upload ảnh thông thường không có QR hiển thị thông báo lỗi phù hợp.

**Target: G3/G4 do Kiên kiểm thử.**

---

## E-AI (TE03) — Async AI Worker Integration via RabbitMQ

### System Flow

AiTask event → RabbitMQ → AI Worker (Python) → Model Adapter (Gemini / LLM Provider) → AiPrediction event → Scan Core Barrier.

### FR

* FR01: AI Worker tiêu thụ job bất đồng bộ qua hàng đợi AMQP.
* FR02: Phân tích ngữ nghĩa văn bản lừa đảo (Intent Detection & Persuasion Tactic).
* FR03: Trả về AiPrediction đúng JSON Schema cho Core Engine.
* FR04: Xử lý timeout an toàn: Nếu AI không phản hồi kịp, Core vẫn hoàn tất scan theo Rule Engine, không để người dùng chờ vô hạn.

### Manual UAT

* [ ] AI Worker phân tích văn bản và trả về dự đoán chính xác.
* [ ] Giả lập AI lỗi/mất mạng: Scan vẫn hoàn tất với nhãn Degraded/Rule-only.

**Target: G3 do Kiên kiểm thử.**

---

## R03 — Xem, Lọc & Phân Trang Lịch Sử Scan (Scan History)

### User Journey

User đăng nhập → vào mục 'Lịch sử quét' → xem danh sách các lần quét trước đây → lọc theo loại scan (URL, Text, Phone, Bank, QR) và phân loại nguy cơ (SAFE, CAUTION, RISKY) → bấm xem lại chi tiết kết quả.

### FR

* FR01: API GET /v1/scans/history hỗ trợ paging và filtering.
* FR02: Web UI bảng lịch sử hiển thị trực quan, phân trang mượt mà.
* FR03: Quyền riêng tư: Người dùng chỉ xem được lịch sử quét của chính mình.

**Target: G4 do Kiên kiểm thử.**

---

## R04 — Xuất Kết Quả Scan Ra PDF / HTML

### User Journey

Tại trang chi tiết kết quả quét (Result Detail) → người dùng nhấn nút 'Xuất báo cáo PDF' → Hệ thống xuất file PDF định dạng báo cáo chuẩn có: Thời gian, Điểm số, Bằng chứng chi tiết, Khuyến nghị và Mã băm (hash) xác thực.

### FR

* FR01: Chức năng xuất PDF/Print HTML trực tiếp từ trình duyệt.
* FR02: Bố cục văn bản in chuẩn khổ A4, không vỡ layout khi in.
* FR03: Đi kèm watermark và mã tra cứu chống làm giả kết quả.

**Target: G3/G4 do Kiên kiểm thử.**

---

## Handoffs

* Cho Kiên: Tích hợp Text Worker & AI Worker vào Completion Barrier của Core Engine.
* Cho Khải: Kiểm thử tương thích Web UI trên môi trường Staging.

## Definition of Done

* [ ] S02 Text Scan đạt G4.
* [ ] S05 QR Scan đạt G3/G4.
* [ ] E-AI Worker kết nối ổn định qua RabbitMQ.
* [ ] R03 Lịch sử và R04 Xuất PDF đạt G4.
* [ ] Kiên kiểm thử độc lập và approve trước freeze 26/10.
