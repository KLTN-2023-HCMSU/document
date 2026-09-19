# Thắng - Danh sách tính năng (Feature List)

> Dựa trên [Template_Feature.md](./Template_Feature.md) · Tuần 4

---

## Thông tin người phụ trách

| Trường | Giá trị |
|--------|---------|
| **Người phụ trách** | Thắng |
| **Vertical Slice** | (3) Text Analyzer · Phone/Bank Checker · QR Parser · Rule Engine |
| **Mô tả mục tiêu** | Xây dựng lõi phân tích nội dung của hệ thống: phân tích tin nhắn/giao dịch đáng ngờ, tra cứu số điện thoại/tài khoản ngân hàng rủi ro, giải mã QR/VietQR và vận hành Rule Engine tính điểm rủi ro có thể giải thích được — làm nền tảng cho toàn bộ các scanner trong Anti-Scam Platform. |
| **Demo flow độc lập** | Người dùng dán tin nhắn SMS giả mạo ngân hàng → Text Analyzer trích xuất URL, số điện thoại, keyword → Cross-reference với Phone Checker → Rule Engine tổng hợp điểm → Trả về kết quả DANGER kèm danh sách evidence giải thích rõ từng lý do. |

---

## Nhóm 1 · Frontend (UI/UX)

### Web (Next.js)

- [ ] **Trang Scan nội dung tin nhắn (Text Scan)**
  - Nền tảng: Web
  - Mô tả: Textarea cho phép dán nội dung tin nhắn, đoạn chat, bài đăng giao dịch hoặc mô tả đáng ngờ. Hiển thị placeholder gợi ý ví dụ thực tế (SMS ngân hàng, Zalo tuyển dụng, bài đăng mua bán). Nút "Kiểm tra" gửi lên API. Hiển thị trạng thái loading và kết quả với risk score, level badge và danh sách evidence.
  - Phụ thuộc API: `POST /v1/scan/text`

- [ ] **Trang Kiểm tra số điện thoại (Phone Check)**
  - Nền tảng: Web
  - Mô tả: Form nhập số điện thoại Việt Nam (hỗ trợ định dạng 0xx và +84xx). Validate format trước khi gọi API. Hiển thị kết quả gồm risk score, số lượng báo cáo, loại scam phổ biến và thời điểm báo cáo gần nhất.
  - Phụ thuộc API: `POST /v1/scan/phone`

- [ ] **Trang Kiểm tra số tài khoản ngân hàng (Bank Account Check)**
  - Nền tảng: Web
  - Mô tả: Form nhập số tài khoản và tuỳ chọn chọn ngân hàng từ dropdown (VCB, TCB, MB, ACB...). Hiển thị kết quả rủi ro, lịch sử báo cáo và khuyến nghị. Cảnh báo nổi bật nếu tài khoản nằm trong blacklist đã xác minh.
  - Phụ thuộc API: `POST /v1/scan/bank-account`

- [ ] **Trang Scan QR Code (QR Scan)**
  - Nền tảng: Web
  - Mô tả: Cho phép upload ảnh QR hoặc dán raw QR data dạng text. Hiển thị thông tin parsed (loại QR, nội dung), sau đó tự động chuyển sang kết quả scan tương ứng (URL/text/VietQR). Với VietQR hiển thị thêm thông tin ngân hàng, số tài khoản và số tiền.
  - Phụ thuộc API: `POST /v1/scan/qr`

- [ ] **Component Risk Result Card (dùng chung)**
  - Nền tảng: Web + Mobile
  - Mô tả: Component hiển thị kết quả scan thống nhất: badge màu (xanh/vàng/đỏ) theo risk level, thanh điểm số 0–100, danh sách evidence có thể expand/collapse từng item, phần khuyến nghị hành động và nút "Gửi báo cáo". Dùng chung cho Text, Phone, Bank, QR scan.
  - Phụ thuộc API: Nhận `RiskResult` object từ bất kỳ API scan nào

- [ ] **Trang Tra cứu Risk Entity (Entity Lookup)**
  - Nền tảng: Web
  - Mô tả: Trang tra cứu nhanh cho phép tìm kiếm domain, số điện thoại, số tài khoản theo keyword. Hiển thị trạng thái (VERIFIED/PENDING), số lần báo cáo và nguồn xác minh. Dành cho người dùng muốn tra thủ công trước khi giao dịch.
  - Phụ thuộc API: `GET /v1/entities/search?q=...`

### Mobile (React Native)

- [ ] **Màn hình Manual Input (Nhập thủ công)**
  - Nền tảng: Mobile
  - Mô tả: Tab bar với 4 tab: Tin nhắn / Số điện thoại / Số tài khoản / QR. Mỗi tab có input tương ứng. Kết quả hiển thị dạng bottom sheet với Risk Result Card. Thiết kế tối giản, ưu tiên thao tác nhanh.
  - Phụ thuộc API: `POST /v1/scan/text`, `POST /v1/scan/phone`, `POST /v1/scan/bank-account`, `POST /v1/scan/qr`

- [ ] **Màn hình QR Scanner (Camera)**
  - Nền tảng: Mobile
  - Mô tả: Mở camera cho người dùng quét QR. Dùng ZXing (Android) hoặc AVFoundation (iOS) để decode. Sau khi decode, tự động gửi raw data lên API phân tích. Hiển thị kết quả ngay trên màn hình. Yêu cầu quyền CAMERA khi lần đầu sử dụng.
  - Phụ thuộc API: `POST /v1/scan/qr`

- [ ] **Share Target (Nhận link/text từ app khác)**
  - Nền tảng: Mobile
  - Mô tả: Đăng ký là share target để nhận text/URL từ Zalo, Messenger, SMS app... Khi user share link nghi ngờ vào app, tự động điền vào ô scan và gọi API tương ứng (URL hoặc text). Không đọc clipboard ngầm — chỉ xử lý khi user chủ động share.
  - Phụ thuộc API: `POST /v1/scan/url` hoặc `POST /v1/scan/text`

---

## Nhóm 2 · Backend API (Spring Boot)

- [ ] **POST /v1/scan/text**
  - Mô tả: Nhận nội dung text (tin nhắn, bài đăng, hội thoại). Validate độ dài tối đa 5000 ký tự. Chuẩn hóa text (lowercase, loại bỏ ký tự đặc biệt thừa). Xác định `inputType=TEXT`. Tạo `ScanRequest`, đẩy job vào queue `scan.text.requested` nếu cần async, hoặc xử lý sync nếu text ngắn. Trả về `scanId` với status `PROCESSING` hoặc kết quả ngay nếu sync.
  - Xử lý đặc biệt: Rate limit 200 req/giờ cho authenticated user. Idempotency key để tránh scan trùng cùng nội dung trong 5 phút.

- [ ] **POST /v1/scan/phone**
  - Mô tả: Nhận số điện thoại, normalize về dạng `+84xxxxxxxxx`. Kiểm tra format VN hợp lệ. Tra cứu `risk_entities` và `scam_reports`. Tính điểm dựa trên blacklist, số báo cáo và time-decay. Xử lý sync vì lookup nhanh.
  - Xử lý đặc biệt: Hash số điện thoại khi lưu log. Cache Redis TTL 15 phút theo normalized phone.

- [ ] **POST /v1/scan/bank-account**
  - Mô tả: Nhận số tài khoản và bank code (optional). Normalize account number (bỏ space, dash). Tra cứu blacklist và report history. Tính risk score với time-decay. Nếu có bank code, validate theo danh sách ngân hàng VN chuẩn.
  - Xử lý đặc biệt: Hash account number khi log. Cache Redis TTL 15 phút.

- [ ] **POST /v1/scan/qr**
  - Mô tả: Nhận `qrData` đã decode (string) và `qrType` hint. Phân loại raw data: nếu là URL gọi URL scan flow, nếu là VietQR parse thông tin chuyển khoản rồi gọi bank account checker và text analyzer, nếu là plain text gọi text analyzer. Trả kết quả tổng hợp.
  - Xử lý đặc biệt: Validate max length của qrData. Timeout nếu cần fetch thêm từ sub-scan.

- [ ] **GET /v1/entities/search**
  - Mô tả: Tìm kiếm risk entity theo keyword. Hỗ trợ filter theo `type` (DOMAIN, PHONE, BANK_ACCOUNT, KEYWORD) và `status` (VERIFIED, PENDING). Phân trang. Trả về danh sách entity với risk level và report count.
  - Xử lý đặc biệt: Cache kết quả search phổ biến TTL 5 phút. Không trả về thông tin PII đầy đủ (mask số điện thoại, số tài khoản).

- [ ] **GET /v1/rules/active**
  - Mô tả: Trả về danh sách rules đang active, dùng cho client hiển thị hoặc debug. Chỉ trả về `code`, `name`, `category`, `weight` — không trả cấu trúc condition nội bộ.
  - Xử lý đặc biệt: Cache Redis TTL 5 phút, invalidate khi admin cập nhật rule.

---

## Nhóm 3 · Background Workers (RabbitMQ / Async)

- [ ] **Text Analyzer Worker** — lắng nghe queue: `scan.text.requested`
  - Mô tả: Nhận message chứa `scanRequestId` và `normalizedText`. Thực hiện: (1) tiền xử lý tiếng Việt đơn giản, (2) extract entities (URL, phone, account, amount, OTP-like pattern, deadline), (3) keyword matching theo từng nhóm (khẩn cấp, giả mạo cơ quan, yêu cầu thông tin nhạy cảm, chuyển tiền, quá tốt để tin), (4) composite pattern matching (bank impersonation, fake job, fake shipper, hoàn tiền giả), (5) cross-reference URL/phone/account sang module tương ứng, (6) gọi Rule Engine tổng hợp điểm. Lưu `RiskResult` + `evidences_json` vào PostgreSQL, cập nhật cache.
  - Output: Cập nhật `scan_requests.status = COMPLETED`, lưu `risk_results` với JSONB `analysis_json` và `evidences_json`.
  - Retry / Dead-letter: Có — retry 3 lần với exponential backoff, sau đó đẩy dead-letter queue, cập nhật status = FAILED.

- [ ] **Phone/Bank Checker Worker** — lắng nghe queue: `scan.entity.requested`
  - Mô tả: Nhận message chứa `entityType` (PHONE/BANK_ACCOUNT) và `normalizedValue`. Lookup `risk_entities` theo hash. Đếm và aggregate `scam_reports`. Áp dụng time-decay scoring. Gọi Rule Engine với kết quả lookup. Lưu kết quả và cập nhật Redis cache.
  - Output: Lưu `risk_results`, cập nhật `scan_requests.status`.
  - Retry / Dead-letter: Có — retry 3 lần.

- [ ] **QR Parser Worker** — lắng nghe queue: `scan.qr.requested`
  - Mô tả: Nhận `qrData` đã decode. Phân loại: URL → publish `scan.url.requested`, VietQR → parse rồi publish `scan.entity.requested` cho bank account + `scan.text.requested` cho content, plain text → publish `scan.text.requested`. Sau khi các sub-scan hoàn tất, gom kết quả và tổng hợp `RiskResult` cuối cùng.
  - Output: Lưu `risk_results` tổng hợp từ tất cả sub-scan.
  - Retry / Dead-letter: Có.

---

## Nhóm 4 · Dữ liệu & Caching (PostgreSQL · Redis · MinIO)

### PostgreSQL

- [ ] **Bảng `scan_requests`** — Lưu mỗi lần user gửi yêu cầu scan. Quan hệ 1-1 với `risk_results`.
  - Cột chính: `id` (UUID), `user_id` (FK → users, nullable cho anonymous), `input_type` (enum: URL/TEXT/PHONE/BANK_ACCOUNT/QR), `raw_input`, `normalized_input`, `status` (enum: PENDING/PROCESSING/COMPLETED/FAILED), `idempotency_key`, `created_at`, `completed_at`.

- [ ] **Bảng `risk_results`** — Lưu kết quả scan với JSONB cho phép mỗi loại scanner có cấu trúc evidence khác nhau.
  - Cột chính: `id`, `scan_request_id` (FK), `risk_score` (int 0-100), `risk_level` (enum: SAFE/CAUTION/DANGER), `confidence` (float), `summary` (text), `analysis_json` (JSONB — cấu trúc chi tiết từng scanner), `evidences_json` (JSONB — list evidence với ruleCode/score/severity), `recommendation_json` (JSONB — action/reasons/nextSteps), `created_at`.

- [ ] **Bảng `risk_entities`** — Blacklist/whitelist cho domain, phone, bank account, keyword, brand.
  - Cột chính: `id`, `entity_type` (enum: DOMAIN/PHONE/BANK_ACCOUNT/KEYWORD/BRAND), `value`, `value_hash` (SHA-256, dùng cho lookup), `risk_level`, `status` (enum: VERIFIED/PENDING/RESOLVED/WHITELIST), `source` (enum: ADMIN_MANUAL/COMMUNITY_REPORT/IMPORT), `report_count`, `verified_at`, `notes`.
  - Index: `(entity_type, value_hash)` để lookup nhanh.

- [ ] **Bảng `risk_rules`** — Bộ luật chấm điểm cấu hình được.
  - Cột chính: `id`, `code` (unique, VD: `TEXT_HAS_OTP_REQUEST`), `name`, `category` (enum: URL/TEXT/PHONE/BANK/QR/COMPOSITE), `weight` (int), `enabled` (boolean), `description`, `condition_type` (enum: SIMPLE/COMPOSITE/REGEX/KEYWORD_GROUP), `created_at`, `updated_at`.

- [ ] **Bảng `rule_conditions`** — Chi tiết điều kiện của từng rule composite.
  - Cột chính: `id`, `rule_id` (FK → risk_rules), `sequence` (int — thứ tự trong composite), `logical_op` (enum: AND/OR), `field_name`, `operator` (enum: CONTAINS/REGEX/EQUALS/GT/LT), `value`.

- [ ] **JSONB column `analysis_json`** trong bảng `risk_results` — Lưu cấu trúc phân tích theo từng loại scan:
  - Text scan: `{ extractedEntities: {...}, keywordMatches: [...], patternMatches: [...], crossReferenceChecks: {...} }`
  - Phone scan: `{ reportCount, verifiedReports, lastReportDate, reportCategories, timeDecayScore }`
  - QR scan: `{ qrType, parsedData, subScanResults: {...} }`

### Redis

- [ ] **Cache scan result** — key: `cache:scan:{normalizedInput_hash}` — TTL: 5 phút
  - Mục đích: Tránh scan lại cùng input trong thời gian ngắn. Giảm tải DB và worker.

- [ ] **Cache risk entity lookup** — key: `cache:entity:{type}:{value_hash}` — TTL: 15 phút
  - Mục đích: Cache kết quả tra cứu phone/account/domain hay được query. Invalidate khi admin cập nhật entity.

- [ ] **Cache active rule set** — key: `cache:rules:active` — TTL: 5 phút
  - Mục đích: Tránh query DB mỗi lần Rule Engine chạy. Invalidate ngay khi admin update/disable rule.

- [ ] **Rate limit scan** — key: `rate:{userId}:scan:{window}` — TTL: 1 giờ
  - Mục đích: Giới hạn 200 req/giờ cho authenticated user, 50 req/giờ cho anonymous.

- [ ] **Idempotency key** — key: `idempotency:{userId}:{key}` — TTL: 5 phút
  - Mục đích: Tránh scan trùng cùng nội dung trong thời gian ngắn, trả lại kết quả cũ nếu key trùng.

- [ ] **Distributed lock** — key: `lock:scan:{normalizedInput_hash}` — TTL: 30 giây
  - Mục đích: Tránh 2 worker xử lý cùng input cùng lúc khi có nhiều request trùng.

### MinIO

> _(Không áp dụng trực tiếp ở Phase 1 MVP cho Slice này. Evidence file nếu có sẽ do Slice 4 - Community Report quản lý.)_

---

## Nhóm 5 · Security, Privacy & Trust-by-design

- [ ] **Không lưu nội dung nhạy cảm vào log**
  - Mô tả: Nội dung tin nhắn (raw_input) chứa thể có OTP, số thẻ, mật khẩu của người khác. Không ghi `raw_input` vào application log. Chỉ log `scanRequestId`, `inputType`, `inputLength` và `status`. Khi lưu DB, `raw_input` phải được xem xét có cần encrypt at rest không.

- [ ] **Hash phone/account trước khi dùng làm lookup key**
  - Mô tả: `value_hash = SHA-256(normalizedValue)` dùng làm index lookup trong `risk_entities`. Không dùng plain-text number làm cache key hoặc log. Người dùng cuối không bị lộ thông tin số tài khoản/điện thoại của người khác qua response.

- [ ] **Mask giá trị entity trong response public**
  - Mô tả: Khi trả kết quả lookup entity cho user, mask bớt số: `0912***678`, `1234****0`. Chỉ Admin mới xem full value. Ngăn chặn việc dùng hệ thống để enumerate thông tin cá nhân.

- [ ] **Giới hạn kích thước input**
  - Mô tả: Text scan tối đa 5000 ký tự. QR data tối đa 2000 ký tự. Phone/account tối đa 20 ký tự. Validate và reject ngay tại API layer với thông báo lỗi rõ ràng, không để input lớn ngấm vào worker.

- [ ] **Rule Engine chỉ đọc (immutable trong một scan)**
  - Mô tả: Khi một scan job bắt đầu, load rule set một lần từ cache. Không reload giữa chừng. Đảm bảo kết quả một scan nhất quán dù admin update rule trong lúc đang xử lý. Ghi lại `rule_snapshot_version` vào `risk_results` để truy vết sau này.

- [ ] **Kết quả là "đánh giá rủi ro", không phải kết luận pháp lý**
  - Mô tả: Mọi response phải có disclaimer rõ ràng: kết quả chỉ là đánh giá rủi ro dựa trên dữ liệu cộng đồng và rule, không có giá trị pháp lý. Không dùng ngôn từ kết tội như "kẻ lừa đảo" hay "chắc chắn scam". Dùng: "Có dấu hiệu rủi ro", "Đã có X báo cáo chưa được xác minh".

- [ ] **Chống abuse bằng rate limit và audit log**
  - Mô tả: Giới hạn số lần scan để tránh dùng hệ thống enumerate danh sách số điện thoại/tài khoản. Ghi `audit_logs` cho các truy vấn bất thường (scan > 100 số điện thoại trong 10 phút từ cùng user/IP).

---

## Ghi chú & Dependencies

| Phụ thuộc vào | Của thành viên | Lý do cần |
|---------------|----------------|-----------|
| Auth/User (JWT + userId) | Hùng (Slice 1) | Mọi scan request cần `userId` để lưu lịch sử và áp rate limit đúng. Rule Engine cần context user để phân quyền xem kết quả nội bộ. |
| Scan URL flow (URL Scanner Worker) | Thành viên 2 (Slice 2) | Text Analyzer và QR Parser cần gọi lại URL scan khi phát hiện URL trong tin nhắn hoặc QR chứa link. Cần thống nhất interface `ScanJobPublisher.publishUrlScanRequested(...)`. |
| Community Report (ScamReport → RiskEntity) | Thành viên 4 (Slice 4) | Khi report được admin duyệt, cần cập nhật `risk_entities` — ảnh hưởng trực tiếp đến kết quả Phone/Bank Checker. Cần thống nhất event `report.verified` để invalidate cache entity. |
| Admin Rule Management (CRUD rules) | Thành viên 4 (Slice 4) | Admin cập nhật rule qua Slice 4's Admin API → cần trigger `cache:rules:active` invalidation bên Slice 3. Hai bên cần thống nhất event hoặc interface invalidate cache. |

---

*Cập nhật lần cuối: 2026-08-01 · Vertical Slice 3 · Phiên bản: Week 4*
