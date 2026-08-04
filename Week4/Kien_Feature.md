# Kiên - Danh sách tính năng (Feature List)

> Dựa trên [Template_Feature.md](./Template_Feature.md) · Tuần 4

---

## Thông tin người phụ trách

| Trường | Giá trị |
|--------|---------|
| **Người phụ trách** | Kiên |
| **Vertical Slice** | (3) Text, Phone, QR & Rule Engine — phân tích nội dung giao dịch, tra cứu thực thể rủi ro và chấm điểm giải thích được |
| **Mô tả mục tiêu** | Xây dựng pipeline phân tích tin nhắn/bài đăng giao dịch, số điện thoại, số tài khoản ngân hàng và QR/VietQR bằng rule-based engine để trả về điểm rủi ro, evidence và khuyến nghị dễ hiểu cho người dùng. |
| **Demo flow độc lập** | Người dùng dán một tin nhắn giả mạo ngân hàng có link + số điện thoại + yêu cầu OTP → hệ thống trích xuất entity → tra cứu phone/bank/domain trong dữ liệu rủi ro → Rule Engine cộng điểm → trả cảnh báo DANGER kèm lý do và khuyến nghị không bấm link/không cung cấp OTP/không chuyển tiền. |

---

## Nhóm 1 · Frontend (UI/UX)

### Web (Next.js)

- [ ] **Trang Scan nội dung tin nhắn / giao dịch**
  - Nền tảng: Web
  - Mô tả: Người dùng dán SMS, email, đoạn chat, bài đăng mua bán, thuê trọ hoặc tuyển dụng đáng nghi. Form cho chọn nguồn nội dung (`SMS`, `ZALO`, `MESSENGER`, `EMAIL`, `MARKETPLACE`, `OTHER`) và tùy chọn lưu lịch sử.
  - Phụ thuộc API: `POST /v1/scan/text`

- [ ] **Trang kết quả phân tích nội dung**
  - Nền tảng: Web
  - Mô tả: Hiển thị `riskScore`, `riskLevel`, entity đã trích xuất, keyword/pattern match, cross-reference check và danh sách evidence theo rule. Mỗi evidence cần giải thích ngắn gọn vì sao bị cộng điểm.
  - Phụ thuộc API: `GET /v1/scan/{scanId}`

- [ ] **Form tra cứu số điện thoại**
  - Nền tảng: Web
  - Mô tả: Người dùng nhập số điện thoại Việt Nam, hệ thống chuẩn hóa số và trả về số lượng báo cáo, trạng thái blacklist/whitelist, mức rủi ro và khuyến nghị xử lý.
  - Phụ thuộc API: `POST /v1/scan/phone`

- [ ] **Form tra cứu số tài khoản ngân hàng**
  - Nền tảng: Web
  - Mô tả: Người dùng nhập số tài khoản, ngân hàng và tên chủ tài khoản nếu có. Giao diện cảnh báo rõ nếu tài khoản đã được xác minh rủi ro hoặc có nhiều báo cáo gần đây.
  - Phụ thuộc API: `POST /v1/scan/bank-account`

- [ ] **Trang quản lý Rule Engine cơ bản cho admin**
  - Nền tảng: Web
  - Mô tả: Admin xem danh sách rule đang bật, lọc theo category (`TEXT`, `PHONE`, `BANK_ACCOUNT`, `QR`, `ENTITY`), chỉnh weight, bật/tắt rule và xem mô tả điều kiện.
  - Phụ thuộc API: `GET /v1/admin/rules`, `POST /v1/admin/rules`, `PUT /v1/admin/rules/{ruleId}`

### Mobile (React Native)

- [ ] **Màn hình Paste Text / Manual Input**
  - Nền tảng: Mobile
  - Mô tả: Người dùng chủ động paste nội dung tin nhắn hoặc nhập số điện thoại/số tài khoản để kiểm tra. Không đọc SMS, notification hoặc clipboard ngầm.
  - Phụ thuộc API: `POST /v1/scan/text`, `POST /v1/scan/phone`, `POST /v1/scan/bank-account`

- [ ] **Màn hình QR Scanner**
  - Nền tảng: Mobile
  - Mô tả: Người dùng bấm quét QR, app xin quyền camera runtime, decode QR trên thiết bị rồi gửi `qrData` đã decode lên backend. QR chứa URL, text hoặc VietQR đều được route sang pipeline phù hợp.
  - Phụ thuộc API: `POST /v1/scan/qr`

- [ ] **Mobile Result Summary**
  - Nền tảng: Mobile
  - Mô tả: Màn hình kết quả tối giản cho người dùng phổ thông: mức cảnh báo, 3-5 lý do chính, hành động nên làm tiếp theo và nút gửi báo cáo nếu người dùng có thêm bằng chứng.
  - Phụ thuộc API: `GET /v1/scan/{scanId}`, `POST /v1/reports`

---

## Nhóm 2 · Backend API (Spring Boot)

- [ ] **POST /v1/scan/text**
  - Mô tả: Nhận nội dung text và `source`, validate độ dài, sanitize dữ liệu, tạo `scan_requests`, trích xuất entity cơ bản hoặc đẩy job async nếu nội dung dài. Output gồm `scanId`, điểm rủi ro, level, extracted entities, keyword matches, pattern matches, evidence và recommendation.
  - Xử lý đặc biệt: Rate limit theo user/IP, idempotency key cho retry, không lưu OTP/mật khẩu/số thẻ nguyên văn trong log.

- [ ] **POST /v1/scan/phone**
  - Mô tả: Nhận số điện thoại, chuẩn hóa về định dạng Việt Nam, ví dụ `0912345678` thành `+84912345678`, lookup trong `risk_entities` và `scam_reports`, tính score theo blacklist/report count/verified reports/time-decay.
  - Xử lý đặc biệt: Cache lookup trong Redis; hash số điện thoại khi lưu lookup/log; trả `SAFE` khi không có tín hiệu nhưng không khẳng định tuyệt đối.

- [ ] **POST /v1/scan/bank-account**
  - Mô tả: Nhận số tài khoản, `bankCode` và `accountName` nếu có; chuẩn hóa input; kiểm tra blacklist/whitelist và số báo cáo cộng đồng; trả khuyến nghị trước khi người dùng chuyển tiền.
  - Xử lý đặc biệt: Hash số tài khoản trong log/cache; rate limit cao hơn với anonymous/free user; không lưu thông tin giao dịch nhạy cảm ngoài mục đích scan.

- [ ] **POST /v1/scan/qr**
  - Mô tả: Nhận `qrData` đã decode và `qrType` (`AUTO`, `VIETQR`, `URL`, `TEXT`), phân loại payload, route sang URL Scanner/Text Analyzer/Bank Checker và gom kết quả thành một scan result thống nhất.
  - Xử lý đặc biệt: Validate kích thước payload; idempotency theo hash của QR data; nếu QR chứa URL thì phụ thuộc SSRF guard của URL Scanner.

- [ ] **GET /v1/rules/evaluate/preview** _(Admin only, optional cho demo nâng cao)_
  - Mô tả: Cho admin nhập payload mẫu để xem rule nào match, điểm cộng/trừ và level dự kiến trước khi bật rule thật.
  - Xử lý đặc biệt: RBAC `ADMIN`; không ghi kết quả preview vào lịch sử scan của user.

- [ ] **GET /v1/scan/{scanId}**
  - Mô tả: Trả chi tiết kết quả scan thuộc quyền user hiện tại, bao gồm `analysis_json`, `evidences_json`, `recommendation_json` cho text/phone/bank/QR.
  - Xử lý đặc biệt: Ownership check theo `userId`; admin có thể xem khi phục vụ kiểm duyệt/audit.

---

## Nhóm 3 · Background Workers (RabbitMQ / Async)

- [ ] **Text Analyzer Worker** — lắng nghe queue: `scan.text.requested`
  - Mô tả: Nhận `scanId`, `userId`, `source`, nội dung đã sanitize; xử lý tiếng Việt mức heuristic, extract URL/số điện thoại/số tài khoản/số tiền/OTP-like/deadline; match keyword và composite pattern như giả mạo ngân hàng, tuyển dụng giả, shipper giả, hoàn tiền giả.
  - Output: Lưu `risk_results.analysis_json`, `risk_results.evidences_json`, `risk_results.recommendation_json`; cập nhật `scan_requests.status = COMPLETED` hoặc `FAILED`.
  - Retry / Dead-letter: Có — retry 3 lần, sau đó đẩy vào `scan.text.dead` và đánh dấu scan failed có lý do.

- [ ] **Phone/Bank Checker Worker** — lắng nghe queue: `scan.entity.requested`
  - Mô tả: Xử lý các lookup cần aggregate nhiều dữ liệu báo cáo: phone, bank account, domain/email trong text. Tính score dựa trên blacklist/whitelist, số report, report đã verify và time-decay.
  - Output: Ghi bảng `entity_matches`, cập nhật phần cross-reference trong `risk_results.analysis_json`, cache kết quả hot trong Redis.
  - Retry / Dead-letter: Có — retry 3 lần; nếu DB timeout thì cho phép API trả trạng thái `PROCESSING`.

- [ ] **QR Parser Worker** — lắng nghe queue: `scan.qr.requested`
  - Mô tả: Nhận `qrData`, phân loại URL/VietQR/plain text/unknown. Với VietQR, parse `bankCode`, `accountNumber`, `amount`, `content`; gọi Bank Checker và Text Analyzer để tính rủi ro tổng hợp.
  - Output: Lưu `parsedData`, `accountCheck`, `contentAnalysis`, evidence QR vào `risk_results`.
  - Retry / Dead-letter: Có — retry 3 lần; payload không decode được thì hoàn tất với level `CAUTION` và evidence `QR_UNKNOWN_FORMAT`.

- [ ] **Rule Cache Rebuild Worker** — lắng nghe queue: `rule.cache.rebuild`
  - Mô tả: Khi admin tạo/cập nhật/bật/tắt rule, worker nạp lại active rule set từ PostgreSQL và ghi vào Redis để API/worker dùng cùng một phiên bản rule.
  - Output: Redis key `cache:rules:active:{version}`, cập nhật `risk_rules.updated_at` và audit log qua slice Auth/Admin.
  - Retry / Dead-letter: Có — retry 3 lần; nếu rebuild fail thì giữ rule cache version cũ.

---

## Nhóm 4 · Dữ liệu & Caching (PostgreSQL · Redis · MinIO)

### PostgreSQL

- [ ] **Bảng `risk_entities`**
  - Lưu: Thực thể rủi ro cần lookup như `PHONE`, `BANK_ACCOUNT`, `DOMAIN`, `URL`, `KEYWORD`, `BRAND`, `MERCHANT`.
  - Các cột chính: `id`, `entity_type`, `value_hash`, `display_value_masked`, `risk_level`, `status`, `source`, `report_count`, `verified_count`, `last_reported_at`, `created_at`, `updated_at`.
  - Quan hệ: Được tạo/cập nhật từ `scam_reports` sau khi admin verify; được `entity_matches` tham chiếu khi scan.

- [ ] **Bảng `risk_rules`**
  - Lưu: Rule chấm điểm rủi ro có thể cấu hình.
  - Các cột chính: `id`, `code`, `name`, `category`, `weight`, `severity`, `enabled`, `description`, `explanation_template`, `recommendation_template`, `version`, `created_at`, `updated_at`.
  - Quan hệ: Có nhiều `rule_conditions`; được Rule Engine load để tạo evidence.

- [ ] **Bảng `rule_conditions`**
  - Lưu: Điều kiện của rule đơn giản hoặc composite.
  - Các cột chính: `id`, `rule_id`, `condition_group`, `field_name`, `operator`, `value`, `logical_operator`, `created_at`.
  - Quan hệ: FK đến `risk_rules`; hỗ trợ rule như text chứa keyword + có URL + có yêu cầu OTP.

- [ ] **Bảng `entity_matches`**
  - Lưu: Kết quả các entity được tìm thấy trong một lần scan.
  - Các cột chính: `id`, `scan_request_id`, `risk_entity_id`, `entity_type`, `matched_value_hash`, `matched_value_masked`, `match_source`, `score_contribution`, `created_at`.
  - Quan hệ: FK đến `scan_requests`, optional FK đến `risk_entities`.

- [ ] **Bảng `scan_requests`**
  - Lưu: Metadata mỗi lần scan text/phone/bank/QR.
  - Các cột chính: `id`, `user_id`, `input_type`, `raw_input_hash`, `normalized_input_hash`, `status`, `created_at`, `completed_at`.
  - Quan hệ: FK đến `users`; có một `risk_results`.

- [ ] **Bảng `risk_results`**
  - Lưu: Kết quả chấm điểm cuối cùng cho scan.
  - Các cột chính: `id`, `scan_request_id`, `risk_score`, `risk_level`, `confidence`, `summary`, `analysis_json`, `evidences_json`, `recommendation_json`, `created_at`.
  - Quan hệ: FK đến `scan_requests`; `analysis_json` và `evidences_json` lưu chi tiết khác nhau theo từng loại scan.

- [ ] **JSONB column `analysis_json` trong bảng `risk_results`**
  - Lưu cấu trúc phân tích theo loại input:
    - Text: `extractedEntities`, `keywordMatches`, `patternMatches`, `crossReferenceChecks`.
    - Phone/Bank: `inBlacklist`, `reportCount`, `verifiedReports`, `lastReportDate`, `reportCategories`, `timeDecayScore`.
    - QR: `qrType`, `parsedData`, `accountCheck`, `contentAnalysis`, `urlCheck`.

- [ ] **JSONB column `evidences_json` trong bảng `risk_results`**
  - Lưu danh sách evidence gồm `ruleCode`, `ruleName`, `score`, `severity`, `description`, `matchedValueMasked`, `source`.

- [ ] **JSONB column `recommendation_json` trong bảng `risk_results`**
  - Lưu khuyến nghị cho người dùng gồm `action`, `message`, `reasons`, `nextSteps`.

### Redis

- [ ] **Cache active rules** — key: `cache:rules:active:{version}` — TTL: 15 phút
  - Mục đích: Worker và API dùng cùng bộ rule đang bật; invalidate khi admin cập nhật rule.

- [ ] **Cache phone lookup** — key: `cache:entity:phone:{phoneHash}` — TTL: 15 phút
  - Mục đích: Cache kết quả tra cứu số điện thoại phổ biến, giảm query `risk_entities` và `scam_reports`.

- [ ] **Cache bank account lookup** — key: `cache:entity:bank:{bankCode}:{accountHash}` — TTL: 15 phút
  - Mục đích: Cache kết quả tra cứu tài khoản ngân hàng, nhất là các tài khoản bị nhiều người kiểm tra.

- [ ] **Cache text scan result** — key: `cache:scan:text:{normalizedTextHash}` — TTL: 5 phút
  - Mục đích: Trả nhanh khi user retry cùng nội dung hoặc app gửi lại request do mất mạng.

- [ ] **Idempotency scan** — key: `idempotency:{userId}:{idempotencyKey}` — TTL: 24 giờ
  - Mục đích: Tránh tạo nhiều `scan_requests` khi client retry cùng request.

- [ ] **Rate limit scan text/entity/QR** — key: `rate:{userId}:scan:{endpoint}:{window}` — TTL: 1 giờ
  - Mục đích: Giới hạn lạm dụng API scan; gợi ý authenticated user 200 request/giờ, anonymous/free user 50 request/giờ nếu có.

### MinIO (Object Storage)

> _(Không áp dụng trực tiếp cho Slice Text/Phone/QR & Rule Engine ở Phase 1 MVP. Evidence ảnh/chụp màn hình thuộc Slice Community Report; QR scanner MVP chỉ gửi `qrData` đã decode, không upload ảnh QR.)_

---

## Nhóm 5 · Security, Privacy & Trust-by-design

- [ ] **Không đọc dữ liệu ngầm trên mobile**
  - Mô tả: Mobile chỉ nhận dữ liệu khi người dùng chủ động paste, nhập tay, share hoặc bấm quét QR. Không đọc SMS, notification, call log, danh bạ, clipboard nền hoặc dùng Accessibility Service.

- [ ] **Data minimization cho nội dung scan**
  - Mô tả: Chỉ lưu hash/normalized metadata khi không cần raw input. Nếu cần lưu lịch sử, che/mask số điện thoại, số tài khoản, OTP-like pattern và thông tin nhạy cảm trong phần hiển thị/log.

- [ ] **Hash phone/account khi lookup và log**
  - Mô tả: Số điện thoại và số tài khoản được chuẩn hóa rồi hash để lookup/cache/log. UI chỉ hiển thị dạng masked như `+849***678` hoặc `1234****7890`.

- [ ] **Không lưu OTP, mật khẩu, CVV, số thẻ**
  - Mô tả: Text Analyzer có thể phát hiện pattern nhạy cảm để cộng điểm nhưng phải redact trước khi lưu `analysis_json`, log hoặc audit event.

- [ ] **Rule Engine giải thích được**
  - Mô tả: Mọi điểm cộng/trừ phải sinh evidence có `ruleCode`, tên rule, score và mô tả. Hệ thống chỉ đưa ra đánh giá rủi ro, không khẳng định pháp lý rằng cá nhân/tài khoản chắc chắn lừa đảo khi chưa có kiểm duyệt.

- [ ] **Rate limit và idempotency cho scan**
  - Mô tả: Ngăn spam API scan và tránh tạo trùng lịch sử khi client retry. Nếu cùng idempotency key nhưng request hash khác, trả lỗi conflict.

- [ ] **RBAC cho rule management**
  - Mô tả: Chỉ admin được tạo/sửa/bật/tắt rule. User chỉ được scan và xem kết quả thuộc mình.

- [ ] **Audit thay đổi rule và entity rủi ro**
  - Mô tả: Mọi thay đổi rule weight, trạng thái rule hoặc risk entity phải ghi audit log để truy vết khi kết quả cảnh báo thay đổi.

- [ ] **Giới hạn payload và timeout xử lý**
  - Mô tả: Giới hạn độ dài text, kích thước QR payload và thời gian worker xử lý để tránh lạm dụng tài nguyên. QR chứa URL phải đi qua SSRF guard của URL Scanner.

---

## Ghi chú & Dependencies

| Phụ thuộc vào | Của thành viên | Lý do cần |
|---------------|----------------|-----------|
| Auth/JWT + Security Context | Hùng - Slice 1 | API scan cần `userId` để lưu lịch sử, kiểm tra ownership và áp dụng rate limit theo user. |
| RBAC Admin | Hùng - Slice 1 | Trang quản lý rule chỉ cho role `ADMIN`; user thường không được sửa rule/weight. |
| URL Scanner + SSRF Guard | Thành viên 2 - Slice 2 | Text/QR có thể chứa URL; slice Kiên cần gọi URL Scanner để cross-reference rủi ro và đảm bảo URL không truy cập private/internal network. |
| `scan_requests` / `risk_results` contract chung | Thành viên 2 và Kiên | URL/text/phone/bank/QR phải trả response thống nhất để frontend hiển thị một kiểu kết quả. |
| Community Report verify flow | Thành viên 4 - Slice 4 | Phone/bank/domain risk score phụ thuộc `scam_reports` đã verify và dữ liệu được admin chuyển thành `risk_entities`. |
| Admin Dashboard thống kê | Thành viên 4 - Slice 4 | Dashboard cần đọc kết quả scan/risk level/top entity từ dữ liệu do Rule Engine và entity checker tạo ra. |

---

*Cập nhật lần cuối: 2026-08-03 · Vertical Slice 3 · Phiên bản: Week 4*
