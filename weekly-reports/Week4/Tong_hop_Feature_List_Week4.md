# TỔNG HỢP FEATURE LIST — 4 THÀNH VIÊN (Week 4)

> **Dự án:** Anti-Scam Platform — Nền tảng kiểm tra và cảnh báo rủi ro lừa đảo trực tuyến
> **Kiến trúc:** Modular Monolith + Event-Driven Workers (chốt Week 3)
> **Nguyên tắc chia việc:** Vertical Slice — mỗi người làm trọn Frontend → API → Worker → DB → Security của slice mình
> **Nguồn:** `Hung_Feature.md` · `Khai_Feature.md` · `Kien_Feature.md` · `Thang_Feature.md` · `Template_Feature.md`
> **Cập nhật:** 09/08/2026

---

## 1. Bảng phân công tổng quan

| Slice | Tên slice | Người phụ trách | Trạng thái | Cập nhật file |
|-------|-----------|------------------|------------|----------------|
| **(1)** | Auth & User — RBAC, Profile, Audit log | **Hùng** | 🟢 Đang code — phần lớn đã xong | 01/08/2026 |
| **(2)** | Scan URL & SSRF guard | **Khải** | ⚪ Chưa bắt đầu code | 09/08/2026 |
| **(3)** | Text, Phone, QR & Rule Engine | **Kiên** + **Thắng** ⚠️ trùng | ⚪ Chưa bắt đầu code | 03/08 · 01/08 |
| **(4)** | Community Report & Admin Dashboard | ❌ **Chưa có người** | ⚪ Chưa có feature list | — |

<br>

### ⚠️ Hai vấn đề phân công cần giải quyết ngay

**1. Kiên và Thắng đang khai trùng gần như toàn bộ Slice (3)** — cả hai đều viết: Text Scan page, Phone Check, Bank Account Check, QR Scan, `POST /v1/scan/text`, `POST /v1/scan/phone`, `POST /v1/scan/bank-account`, `POST /v1/scan/qr`, Text Analyzer Worker, Phone/Bank Checker Worker, QR Parser Worker, bảng `risk_rules`, `rule_conditions`, `risk_entities`. Nếu không tách, hai người sẽ viết đè code của nhau.

**Đề xuất tách Slice (3) thành 2 slice con:**

| Slice con | Nội dung | Đề xuất người làm |
|-----------|----------|--------------------|
| **(3a) Rule Engine + Text Analyzer** | `risk_rules`, `rule_conditions`, Rule Cache Rebuild Worker, Text Analyzer Worker, `POST /v1/scan/text`, trang Admin quản lý rule, `GET /v1/rules/active` | Kiên *(đã viết chi tiết nhất về rule/condition/preview)* |
| **(3b) Entity Checker + QR Parser** | `risk_entities`, `entity_matches`, Phone/Bank Checker Worker, QR Parser Worker, `POST /v1/scan/phone`, `/bank-account`, `/qr`, `GET /v1/entities/search`, component `RiskResultCard` | Thắng *(đã viết chi tiết nhất về normalize/mask/RiskResultCard)* |

**2. Slice (4) chưa có người nhưng **3 slice khác đều phụ thuộc vào nó** — Community Report là nguồn dữ liệu chính cho `risk_entities`, mà `risk_entities` lại là đầu vào điểm rủi ro của cả URL, Phone và Bank Checker. Đây là rủi ro tiến độ lứn nhất hiện tại.

---

## 2. Mục tiêu và demo flow từng slice

| Slice | Mục tiêu | Demo flow độc lập |
|-------|----------|-------------------|
| **(1) Hùng** | Hệ thống định danh người dùng, phân quyền USER/ADMIN và ghi vết thao tác nhạy cảm — nền tảng bảo mật cho toàn bộ platform | Đăng ký → đăng nhập nhận JWT → gọi API profile → token hết hạn → refresh → Admin vào được route admin, User bị 403 |
| **(2) Khải** | Pipeline kiểm tra URL bất đồng bộ trong lớp fetch an toàn có SSRF guard | Dán `v1etcombank-verify.tk` → trả `scanId` → worker lần redirect, phát hiện không HTTPS + domain mới 5 ngày + typosquatting + form OTP → `92/DANGER` → thử scan `169.254.169.254` → bị chặn |
| **(3) Kiên** | Pipeline phân tích tin nhắn/giao dịch, phone, bank, QR bằng rule-based engine giải thích được | Dán tin nhắn giả mạo ngân hàng có link + số điện thoại + yêu cầu OTP → trích xuất entity → tra cứu → Rule Engine cộng điểm → DANGER kèm khuyến nghị |
| **(3) Thắng** | Lõi phân tích nội dung: Text Analyzer, Phone/Bank Checker, QR Parser, Rule Engine | Dán SMS giả mạo → trích xuất URL/phone/keyword → cross-reference Phone Checker → Rule Engine tổng hợp → DANGER kèm evidence |
| **(4)** | *(Chưa có)* Tiếp nhận báo cáo cộng đồng, kiểm duyệt, chuyển thành `risk_entities`, dashboard thống kê | *(Chưa có)* |

---

## 3. Nhóm 1 · Frontend (UI/UX) — tổng hợp

### Web (Next.js)

| Trạng thái | Màn hình | Chủ | API phụ thuộc |
|-----------|----------|-----|----------------|
| [x] | Trang Đăng ký (Register) | Hùng | `POST /v1/auth/register` |
| [x] | Trang Đăng nhập (Login) | Hùng | `POST /v1/auth/login` |
| [x] | Trang Hồ sơ cá nhân (Profile) | Hùng | `GET /v1/users/me` |
| [x] | Guard Route Admin (middleware Next.js) | Hùng | *(đọc role từ JWT)* |
| [ ] | Trang Kiểm tra URL (Scan URL) | Khải | `POST /v1/scan/url` |
| [ ] | Trang Kết quả chi tiết URL | Khải | `GET /v1/scan/{scanId}` |
| [ ] | Trang Lịch sử kiểm tra (Scan History) | Khải | `GET|DELETE /v1/scan/history` |
| [ ] | Trang Admin Domain Whitelist/Blacklist | Khải | `GET|POST /v1/admin/entities` |
| [ ] | Trang Scan nội dung tin nhắn / giao dịch | Kiên · Thắng ⚠️ | `POST /v1/scan/text` |
| [ ] | Trang kết quả phân tích nội dung | Kiên | `GET /v1/scan/{scanId}` |
| [ ] | Form tra cứu số điện thoại | Kiên · Thắng ⚠️ | `POST /v1/scan/phone` |
| [ ] | Form tra cứu số tài khoản ngân hàng | Kiên · Thắng ⚠️ | `POST /v1/scan/bank-account` |
| [ ] | Trang Scan QR Code (upload ảnh / dán raw data) | Thắng | `POST /v1/scan/qr` |
| [ ] | Trang Admin quản lý Rule Engine | Kiên | `GET|POST|PUT /v1/admin/rules` |
| [ ] | **Component `RiskResultCard`** (dùng chung toàn hệ thống) | Thắng | nhận `RiskResult` từ bất kỳ API scan |
| [ ] | Trang Tra cứu Risk Entity (Entity Lookup) | Thắng | `GET /v1/entities/search` |

### Mobile (React Native)

| Trạng thái | Màn hình | Chủ | Ghi chú |
|-----------|----------|-----|---------|
| [ ] | Màn hình Đăng nhập (Secure Storage, biometric sau) | Hùng | |
| [ ] | Màn hình Scan URL + nhận link chia sẻ | Khải | Share Target cho URL |
| [ ] | Màn hình Paste Text / Manual Input (4 tab) | Kiên · Thắng ⚠️ | |
| [ ] | Màn hình QR Scanner (camera, ZXing/AVFoundation) | Kiên · Thắng ⚠️ | decode trên thiết bị, chỉ gửi `qrData` |
| [ ] | Mobile Result Summary (3–5 lý do chính) | Kiên | |
| [ ] | Share Target nhận text/URL từ Zalo/Messenger/SMS | Thắng · Khải ⚠️ | cần gộp làm một |

<callout>
⚠️ **Share Target bị khai ở cả Khải (link) và Thắng (text/URL).** Về kỹ thuật chỉ có **một** Share Target đăng ký ở cấp OS — cần một người làm, sau đó route theo nội dung (là URL thì gọi `/scan/url`, không thì `/scan/text`).
</callout>

**Nguyên tắc mobile thống nhất (cả 4 người đều ghi):** chỉ nhận dữ liệu khi người dùng **chủ động** paste / nhập tay / share / bấm quét QR. Không đọc SMS, notification, call log, danh bạ, clipboard ngầm; không dùng Accessibility Service.

---

## 4. Nhóm 2 · Backend API (Spring Boot) — tổng hợp

| Trạng thái | Endpoint | Chủ | Xử lý đặc biệt |
|-----------|----------|-----|-----------------|
| [x] | `POST /v1/auth/register` | Hùng | BCrypt · rate limit 5 req/phút/IP |
| [x] | `POST /v1/auth/login` | Hùng | JWT access 15 phút + refresh 7 ngày · 5 lần sai/IP/15 phút → block 15 phút |
| [x] | `POST /v1/auth/refresh` | Hùng | Token rotation · phát hiện reuse → thu hồi toàn bộ session |
| [x] | `POST /v1/auth/logout` | Hùng | Thu hồi refresh token của session |
| [x] | `GET /v1/users/me` | Hùng | Cần JWT · không trả password hash |
| [ ] | `PATCH /v1/users/me/password` | Hùng | Yêu cầu mật khẩu cũ · thu hồi toàn bộ refresh token · audit log |
| [ ] | `GET /v1/admin/users` | Hùng | RBAC ADMIN · phân trang · filter role/status |
| [ ] | `POST /v1/scan/url` | Khải | Chuẩn hoá URL · cache theo `urlHash` · 202 + `scanId` · 100/h/user, 300/h/IP, 20/h khách |
| [ ] | `GET /v1/scan/{scanId}` | Khải · Kiên ⚠️ | Ownership check · trả 404 thay 403 |
| [ ] | `GET /v1/scan/history` | Khải | Cursor pagination · chỉ bản ghi của chính user |
| [ ] | `DELETE /v1/scan/history` | Khải | Xoá thật + xoá evidence MinIO · audit log |
| [ ] | `GET|POST /v1/admin/entities` | Khải | RBAC ADMIN · bulk import · invalidate cache |
| [ ] | `POST /v1/scan/text` | Kiên · Thắng ⚠️ | Max 5000 ký tự · queue `scan.text.requested` · 200 req/h auth |
| [ ] | `POST /v1/scan/phone` | Kiên · Thắng ⚠️ | Normalize `+84xxxxxxxxx` · time-decay · hash khi log · cache 15 phút |
| [ ] | `POST /v1/scan/bank-account` | Kiên · Thắng ⚠️ | Normalize · validate `bankCode` · hash khi log · cache 15 phút |
| [ ] | `POST /v1/scan/qr` | Kiên · Thắng ⚠️ | Route URL/VietQR/plain text · QR chứa URL phải qua SSRF guard |
| [ ] | `GET /v1/entities/search` | Thắng | Mask PII · cache 5 phút · filter type/status |
| [ ] | `GET /v1/rules/active` | Thắng | Chỉ trả `code/name/category/weight` · cache 5 phút |
| [ ] | `GET /v1/rules/evaluate/preview` | Kiên | RBAC ADMIN · không ghi vào lịch sử scan |
| [ ] | `GET|POST /v1/admin/rules`, `PUT /v1/admin/rules/{ruleId}` | Kiên *(Thắng ghi là của Slice 4)* ⚠️ | RBAC ADMIN · audit log · trigger rebuild rule cache |
| [ ] | `POST /v1/reports` | Slice 4 — **chưa có người** | Cả 4 slice đều cần nút "Gửi báo cáo" |

---

## 5. Nhóm 3 · Background Workers (RabbitMQ) — tổng hợp

| Trạng thái | Worker | Queue | Chủ | Retry / DLQ |
|-----------|--------|-------|-----|-------------|
| [ ] | **URL Scanner Worker** | `scan.url.requested` | Khải | 3 lần backoff 2s/8s/30s → `scan.url.dlq`. SSRF-blocked thì fail ngay, không retry |
| [ ] | **Text Analyzer Worker** | `scan.text.requested` | Kiên · Thắng ⚠️ | 3 lần → `scan.text.dead`, status `FAILED` kèm lý do |
| [ ] | **Phone/Bank Checker Worker** | `scan.entity.requested` | Kiên · Thắng ⚠️ | 3 lần; DB timeout thì API trả `PROCESSING` |
| [ ] | **QR Parser Worker** | `scan.qr.requested` | Kiên · Thắng ⚠️ | 3 lần; không decode được → `CAUTION` + `QR_UNKNOWN_FORMAT` |
| [ ] | **Rule Cache Rebuild Worker** | `rule.cache.rebuild` | Kiên | 3 lần; fail thì giữ rule cache version cũ |
| [ ] | **Threat Feed Ingestion Worker** *(Phase 2)* | `threat.feed.sync` | Khải | 3 lần; feed lỗi thì bỏ qua vòng đó |
| [ ] | **Email Notification Worker** *(Phase 2)* | `notification.email` | Hùng | 3 lần → DLQ cho admin xử lý |
| [ ] | **Report Export Worker** *(Phase 2)* | *(chưa đặt tên)* | Slice 4 — chưa có người | — |

### Luồng event giữa các worker (rất quan trọng — cần chốt sớm)

```
POST /v1/scan/qr
   └─> scan.qr.requested (QR Parser)
         ├─ QR là URL      ──> scan.url.requested   (URL Scanner — Khải)
         ├─ QR là VietQR   ──> scan.entity.requested (Bank Checker)
         │                   └─> scan.text.requested  (nội dung chuyển khoản)
         └─ QR là text     ──> scan.text.requested  (Text Analyzer)

POST /v1/scan/text
   └─> scan.text.requested (Text Analyzer)
         ├─ tìm thấy URL trong tin nhắn ──> scan.url.requested (Khải)
         └─ tìm thấy phone/STK        ──> scan.entity.requested
```

**Hệ quả:** Slice (2) của Khải bị slice (3) gọi vào. Cả hai bên phải chốt interface `ScanJobPublisher.publishUrlScanRequested(...)` và cách gom kết quả sub-scan **trước khi viết code**.

---

## 6. Nhóm 4 · Dữ liệu & Caching — tổng hợp

### PostgreSQL — danh sách bảng và ai sở hữu

| Trạng thái | Bảng | Owner | Dùng bởi | Ghi chú |
|-----------|-------|-------|----------|---------|
| [x] | `users` | Hùng | tất cả | `id`, `username`, `email`, `password_hash`, `role`, `status` |
| [x] | `refresh_tokens` | Hùng | Hùng | lưu `token_hash`, không lưu plain text |
| [x] | `audit_logs` | Hùng | tất cả | append-only, không UPDATE/DELETE |
| [ ] | **`scan_requests`** | ⚠️ **3 người cùng khai** | tất cả scanner | Khải · Kiên · Thắng — cần **một** chủ duy nhất |
| [ ] | **`risk_results`** | ⚠️ **3 người cùng khai** | tất cả scanner | schema JSONB khác nhau giữa 3 file |
| [ ] | **`risk_entities`** | ⚠️ Khải · Kiên · Thắng | tất cả | enum `source`/`status` khác nhau giữa 3 file |
| [ ] | `risk_rules` | ⚠️ Kiên · Thắng | Rule Engine | |
| [ ] | `rule_conditions` | ⚠️ Kiên · Thắng | Rule Engine | điều kiện composite |
| [ ] | `entity_matches` | Kiên | slice 3 | entity tìm thấy trong một lần scan |
| [ ] | `url_scan_details` | Khải | slice 2 | redirect chain, TLS, WHOIS, form findings |
| [ ] | `scam_reports` | Slice 4 — **chưa có người** | phone/bank/URL scoring | **chặn tiến độ của cả slice 2 và 3** |
| [ ] | `feed_sync_logs` | Khải *(Phase 2)* | Threat Feed Worker | |

### Redis — danh sách key

| Key | TTL | Chủ | Mục đích |
|-----|-----|-----|----------|
| `rate:login:{ip}:{window}` | 15 phút | Hùng | đếm đăng nhập sai |
| `rate:register:{ip}:{window}` | 1 phút | Hùng | 5 lượt đăng ký/phút/IP |
| `cache:user:{userId}` | 5 phút | Hùng | cache profile |
| `cache:scan:url:{urlHash}` | 30 phút | Khải | link scam lan theo nhóm chat |
| `lock:scan:url:{urlHash}` | 60 giây | Khải | 1 link → 1 job worker |
| `cache:entities:domain:{level}` | 10 phút | Khải | whitelist/blacklist domain |
| `cache:rules:active:{version}` | 15 phút *(Kiên)* / `cache:rules:active` 5 phút *(Thắng)* ⚠️ | Kiên · Thắng | active rule set |
| `cache:entity:phone:{phoneHash}` | 15 phút | Kiên | tra cứu phone |
| `cache:entity:bank:{bankCode}:{accountHash}` | 15 phút | Kiên | tra cứu STK |
| `cache:entity:{type}:{value_hash}` | 15 phút | Thắng | *(trùng ý với 2 key trên của Kiên)* ⚠️ |
| `cache:scan:text:{normalizedTextHash}` | 5 phút | Kiên | retry cùng nội dung |
| `cache:scan:{normalizedInput_hash}` | 5 phút | Thắng | *(trùng ý với key trên của Kiên)* ⚠️ |
| `idempotency:{userId}:{key}` | **24 giờ (Kiên)** vs **5 phút (Thắng)** ⚠️ | Kiên · Thắng | chống tạo trùng scan |
| `idem:scan:url:{userId}:{key}` | 5 phút | Khải | *(tên khác format của 2 bạn kia)* ⚠️ |
| `rate:{userId}:scan:{endpoint}:{window}` | 1 giờ | Kiên · Thắng | 200/h auth, 50/h anonymous |
| `rate:scan:url:{user hoặc ip}:{window}` | 1 giờ | Khải | 100/h user, 300/h IP, 20/h khách |
| `lock:scan:{normalizedInput_hash}` | 30 giây | Thắng | chống 2 worker cùng input |

### MinIO

| Trạng thái | Nội dung | Chủ |
|-----------|----------|-----|
| [ ] | `evidence/url/{scanId}/page.html` — HTML snapshot, ≤ 2 MB, retention 30 ngày | Khải |
| [ ] | `evidence/url/{scanId}/screenshot.png` *(Phase 2)* | Khải |
| — | *Không áp dụng Phase 1* | Hùng · Kiên · Thắng |
| [ ] | Evidence ảnh do người dùng gửi kèm báo cáo | Slice 4 — chưa có người |

---

## 7. Nhóm 5 · Security, Privacy & Trust-by-design — tổng hợp

### 7.1. Nguyên tắc chung toàn hệ thống (cả 4 người đều phải tuân thủ)

- **Chỉ xử lý dữ liệu người dùng chủ động gửi.** Không đọc ngầm SMS, notification, call log, danh bạ, clipboard. Không dùng Accessibility Service.
- **Hash trước khi lưu/lookup/log** số điện thoại, số tài khoản, URL. `value_hash = SHA-256(normalizedValue)`.
- **Mask khi hiển thị**: `+849***678`, `1234****7890`. Chỉ ADMIN xem full value.
- **Không lưu OTP, mật khẩu, CVV, số thẻ** — có thể phát hiện pattern để cộng điểm nhưng phải redact trước khi lưu.
- **Kết quả là "đánh giá rủi ro", không phải kết luận pháp lý.** Dùng "có dấu hiệu rủi ro", "đã có X báo cáo chưa được xác minh". Không dùng "kẻ lừa đảo", "chắc chắn scam".
- **Mọi cảnh báo phải giải thích được** — truy được về `ruleCode` + bằng chứng cụ thể. Không có "điểm bí ẩn".
- **Rate limit + idempotency** trên mọi endpoint scan, chống dùng hệ thống để enumerate dữ liệu cá nhân.
- **Audit log** mọi thay đỏi rule, risk entity và thao tác admin.
- **RBAC**: chỉ ADMIN được sửa rule/entity/duyệt báo cáo. USER chỉ xem kết quả của chính mình.
- **Cơ chế khiếu nại false positive** — tránh làm hại oan website/cá nhân hợp pháp.

### 7.2. Yêu cầu riêng theo slice

| Slice | Yêu cầu bảo mật đặc thù |
|-------|--------------------------|
| **(1) Hùng** | BCrypt strength ≥ 10 · JWT payload chỉ `userId`/`role`/`iat`/`exp` · access token 15 phút · refresh token lưu hash + rotation, reuse → thu hồi toàn bộ session · audit log append-only · rate limit chống brute-force |
| **(2) Khải** | **SSRF Guard**: chỉ http/https, chỉ port 80/443, resolve DNS trước + pin IP, chặn `10/8` `172.16/12` `192.168/16` `127/8` `0.0.0.0/8` `169.254/16` `::1` `fc00::/7` `fe80::/10`, **validate lại IP sau mỗi redirect** (DNS rebinding), ≤ 5 hop, timeout 5s/hop, response ≤ 2 MB, egress qua network riêng · không thực thi JS (chỉ Jsoup) · chỉ GET, không submit form · User-Agent minh bạch · chỉ lưu cấu trúc form, không lưu giá trị |
| **(3) Kiên** | Data minimization cho nội dung scan · hash phone/account khi lookup và log · redact OTP/thẻ trước khi lưu `analysis_json` · RBAC rule management · audit thay đổi rule/entity · giới hạn payload và timeout worker |
| **(3) Thắng** | Không ghi `raw_input` vào log (chỉ `scanRequestId`/`inputType`/`inputLength`/`status`), cân nhắc encrypt at rest · mask entity trong response public · giới hạn input: text 5000, QR 2000, phone/account 20 ký tự · **Rule Engine immutable trong một scan** + ghi `rule_snapshot_version` · audit truy vấn bất thường (>100 số điện thoại/10 phút) |
| **(4)** | *(Chưa có)* — cần: chống spam báo cáo, chống tố cáo sai, quy trình kiểm duyệt, quyền xem evidence do người khác gửi |

---

## 8. Ma trận phụ thuộc giữa các slice

| Slice cần | Phụ thuộc vào | Của | Lý do |
|-----------|---------------|-----|-------|
| (2) Khải | Auth/JWT, RBAC, `audit_logs` | Hùng (1) | gắn `user_id` vào scan, route admin, log xoá lịch sử |
| (2) Khải | Rule Engine (chấm điểm) | Kiên/Thắng (3) | worker chỉ trích đặc trưng, không tự chấm điểm |
| (2) Khải | `RiskResultCard` | Thắng (3) | giao diện kết quả nhất quán |
| (2) Khải | `scam_reports` | Slice 4 | số lần bị báo cáo là đặc trưng điểm rủi ro |
| (3) Kiên/Thắng | Auth/JWT + Security Context | Hùng (1) | lưu lịch sử, ownership, rate limit theo user |
| (3) Kiên/Thắng | URL Scanner + SSRF guard | Khải (2) | text/QR chứa URL phải cross-reference qua URL scan |
| (3) Kiên/Thắng | `scam_reports` → `risk_entities` verify flow | Slice 4 | điểm phone/bank phụ thuộc report đã verify |
| (3) Thắng | Admin Rule Management CRUD | Slice 4 *(hoặc Kiên)* ⚠️ | cần chốt ai làm: Kiên khai là của mình, Thắng khai là của Slice 4 |
| (1) Hùng | — | — | độc lập hoàn toàn, là lý do slice này xong trước |
| (4) | `userId` + role ADMIN | Hùng (1) | duyệt báo cáo cần danh tính và quyền |
| (4) | Kết quả scan / risk level / top entity | Kiên (3) | dashboard thống kê |

**Thứ tự phụ thuộc:** Slice (1) → nền tảng cho tất cả · Slice (4) → nguồn dữ liệu cho (2) và (3) · Slice (2) ↔ (3) gọi qua lại lẫn nhau.

---

## 9. ⚠️ Danh sách xung đột kỹ thuật cần chốt trước khi code

Đây là các chỗ 4 file mô tả **khác nhau về cùng một thứ**. Nếu không chốt, khi merge code sẽ vỡ.

| # | Hạng mục | Các phiên bản đang có | Đề xuất chốt |
|---|---------|----------------------|---------------|
| 1 | **`scan_requests.status` enum** | Khải: `PROCESSING/DONE/FAILED` · Kiên: `...COMPLETED` · Thắng: `PENDING/PROCESSING/COMPLETED/FAILED` | Dùng bản đầy đủ của Thắng: `PENDING/PROCESSING/COMPLETED/FAILED` |
| 2 | **Lưu raw input hay hash?** | Khải & Thắng: lưu `raw_input` · Kiên: chỉ lưu `raw_input_hash` | Lưu raw có điều kiện: URL lưu raw (user cần xem lại), text lưu raw nhưng redact, phone/STK chỉ lưu hash + masked |
| 3 | **`risk_entities.status` enum** | Khải: `ACTIVE/INACTIVE` · Thắng: `VERIFIED/PENDING/RESOLVED/WHITELIST` · Kiên: không liệt kê | Dùng bản của Thắng (diễn đạt đúng quy trình kiểm duyệt) |
| 4 | **`risk_entities.source` enum** | Khải: `MANUAL/PHISHTANK/OPENPHISH/URLHAUS/COMMUNITY` · Thắng: `ADMIN_MANUAL/COMMUNITY_REPORT/IMPORT` | Gộp: `ADMIN_MANUAL / COMMUNITY_REPORT / FEED_IMPORT` + cột `feed_name` cho biết feed nào |
| 5 | **Idempotency TTL** | Kiên: 24 giờ · Thắng: 5 phút · Khải: 5 phút | 5 phút (2/3 phiếu, và 24h quá dài cho kết quả scan dễ lỗi thời) |
| 6 | **Rule cache key/TTL** | Kiên: `cache:rules:active:{version}` 15 phút · Thắng: `cache:rules:active` 5 phút | Dùng bản có `{version}` của Kiên (cần cho `rule_snapshot_version` của Thắng), TTL 5 phút |
| 7 | **Format cache/rate-limit key** | 3 kiểu đặt tên khác nhau | Chuẩn hoá: `cache:{domain}:{...}`, `rate:{scope}:{...}`, `lock:{...}`, `idem:{...}` |
| 8 | **Ai quản lý CRUD rules?** | Kiên khai là của mình · Thắng khai là của Slice 4 | Kiên làm (gắn với Rule Engine), Slice 4 chỉ hiển thị thống kê |
| 9 | **Rate limit scan** | Kiên/Thắng: 200/h auth, 50/h anon · Khải: 100/h user, 300/h IP, 20/h khách | Thống nhất một bảng hạn mức chung cho mọi endpoint scan |
| 10 | **`risk_rules.category` enum** | Kiên: `TEXT/PHONE/BANK_ACCOUNT/QR/ENTITY` · Thắng: `URL/TEXT/PHONE/BANK/QR/COMPOSITE` | Gộp: `URL/TEXT/PHONE/BANK_ACCOUNT/QR/ENTITY/COMPOSITE` |
| 11 | **Share Target mobile** | Khải (link) · Thắng (text/URL) | Một người làm, route theo nội dung |
| 12 | **`GET /v1/scan/{scanId}`** | Khải và Kiên đều khai | Một endpoint chung, trả theo `input_type` — gợi ý Khải làm vì gắn với `scan_requests` |

---

## 10. Mô hình kết quả thống nhất (tất cả scanner phải tuân theo)

```json
{
  "scanId": "uuid",
  "inputType": "URL | TEXT | PHONE | BANK_ACCOUNT | QR",
  "status": "PENDING | PROCESSING | COMPLETED | FAILED",
  "riskScore": 0,
  "riskLevel": "SAFE | CAUTION | DANGER",
  "confidence": 0.0,
  "summary": "Mô tả ngắn cho người dùng phổ thông",
  "evidences": [
    {
      "ruleCode": "DOMAIN_TYPOSQUATTING",
      "ruleName": "Domain gần giống thương hiệu thật",
      "score": 30,
      "severity": "HIGH",
      "description": "Domain gần giống vietcombank.com.vn",
      "matchedValueMasked": "v1etcombank***.tk",
      "source": "RULE_ENGINE"
    }
  ],
  "recommendation": {
    "action": "DO_NOT_PROCEED",
    "message": "Không nhập thông tin vào trang này",
    "reasons": ["..."],
    "nextSteps": ["..."]
  },
  "ruleSnapshotVersion": "v12"
}
```

**Ngưỡng điểm:** `0–29 SAFE` (xanh) · `30–59 CAUTION` (vàng) · `60–100 DANGER` (đỏ)

---

## 11. Tiến độ tổng — đếm theo trạng thái

| Slice | Người | Đã xong `[x]` | Chưa làm `[ ]` | Ghi chú |
|-------|--------|---------------|-----------------|---------|
| (1) Auth & User | Hùng | ~16 | ~5 | Backend auth + web UI đã chạy; còn mobile login, đổi mật khẩu, admin users, email worker |
| (2) Scan URL & SSRF | Khải | 0 | ~26 | Mới có feature list |
| (3) Text/Phone/QR/Rule | Kiên | 0 | ~30 | Mới có feature list |
| (3) Text/Phone/QR/Rule | Thắng | 0 | ~32 | Mới có feature list, trùng phần lớn với Kiên |
| (4) Report & Admin | — | 0 | — | Chưa có feature list |

---

## 12. Việc cần làm ngay (trước khi viết thêm code)

- [ ] **Họp chốt tách Slice (3)** thành (3a) Rule Engine + Text và (3b) Entity + QR — tránh Kiên và Thắng viết đè nhau
- [ ] **Chốt người cho Slice (4)** hoặc chia Slice (4) cho cả nhóm — vì 3 slice còn lại đều phụ thuộc `scam_reports`
- [ ] **Chốt 12 xung đột kỹ thuật ở mục 9** — đặc biệt là schema `scan_requests` / `risk_results` / `risk_entities`
- [ ] **Viết file `Database_Schema.md` dùng chung** — một nguồn sự thật duy nhất cho DDL, không để 3 người tự khai bảng
- [ ] **Chốt interface `ScanJobPublisher`** để slice (3) gọi được URL Scanner của slice (2)
- [ ] **Chốt interface `RuleEngine.evaluate(FeatureSet) → RiskResult`** để URL Scanner không tự viết logic chấm điểm
- [ ] **Chốt `RiskResultCard` props** để 4 loại scan dùng chung một component
- [ ] **Chuẩn bị dữ liệu mẫu tiếng Việt cho demo**: link giả, SMS giả mạo, phone rủi ro, STK bị báo cáo, QR chuyển khoản
- [ ] **Từ điển keyword tiếng Việt** theo nhóm rủi ro: khẩn cấp, giả danh, chuyển tiền, nhận thưởng, khóa tài khoản
- [ ] **Whitelist ~100 brand VN** — ai tạo, lấy từ đâu, ai duy trì

---

## 13. Câu hỏi cần chốt với giảng viên

- [ ] MVP có cần mobile app thật hay web responsive + mô phỏng mobile flow là đủ?
- [ ] Có tích hợp API thật (Google Safe Browsing / PhishTank / VirusTotal) hay dữ liệu mẫu là đủ?
- [ ] Có đưa AI/LLM vào demo hay chỉ trình bày ở hướng phát triển?
- [ ] Phần chạy ngầm mobile nên làm đến mức nào để vừa thực tế vừa không vượt phạm vi?
- [ ] Bổ sung Browser Extension / Zalo Bot có được tính là điểm mới không?
- [ ] Nhóm 4 người nhưng 4 slice — có được phép thu hỡp Slice (4) về mức tối thiểu để đủ người không?

---

*Tổng hợp từ `Hung_Feature.md` (01/08) · `Khai_Feature.md` (09/08) · `Kien_Feature.md` (03/08) · `Thang_Feature.md` (01/08) · `Template_Feature.md` — Week 4*
