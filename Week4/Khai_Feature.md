# Khải - Danh sách tính năng (Feature List)

> Dựa trên [Template_Feature.md](./Template_Feature.md) · Tuần 4

---

## Thông tin người phụ trách

| Trường | Giá trị |
|--------|---------|
| **Người phụ trách** | Khải |
| **Vertical Slice** | (2) Scan URL & SSRF guard |
| **Mô tả mục tiêu** | Xây dựng pipeline kiểm tra URL bất đồng bộ: chuẩn hoá link, lần theo redirect, kiểm tra TLS/domain, phân tích HTML/form bằng Jsoup, rồi đưa đặc trưng sang Rule Engine để tính điểm rủi ro — tất cả chạy trong một lớp fetch an toàn có SSRF guard, vì đây là module duy nhất của hệ thống chủ động gửi request ra Internet. |
| **Demo flow độc lập** | Người dùng dán một URL giả mạo ngân hàng (`v1etcombank-verify.tk`) → API trả `scanId` ngay lập tức → worker lần theo 2 redirect, phát hiện không có HTTPS hợp lệ, domain mới 5 ngày, gần giống `vietcombank.com.vn`, form yêu cầu OTP → trả `riskScore 92 / DANGER` kèm danh sách bằng chứng → thử scan `http://169.254.169.254/latest/meta-data/` → hệ thống từ chối với lỗi `URL_TARGET_NOT_ALLOWED`. |

---

## Nhóm 1 · Frontend (UI/UX)

### Web (Next.js)

- [ ] **Trang Kiểm tra URL (Scan URL)**
  - Nền tảng: Web
  - Mô tả: Ô input dán link + nút "Kiểm tra". Validate sơ bộ ở client (phải là http/https, độ dài tối đa 2048 ký tự). Sau khi submit, hiển thị trạng thái `PROCESSING` với skeleton loading và tự động poll kết quả theo `scanId`. Cho phép huỷ chờ và xem lại sau trong lịch sử.
  - Phụ thuộc API: `POST /v1/scan/url`, `GET /v1/scan/{scanId}`

- [ ] **Trang Kết quả chi tiết URL (URL Result Detail)**
  - Nền tảng: Web
  - Mô tả: Dùng lại component `RiskResultCard` (của Thắng) và bổ sung phần chi tiết riêng cho URL: chuỗi redirect dạng timeline (`bit.ly → t.co → đích thật`), thông tin chứng chỉ TLS (CA, ngày hết hạn), tuổi domain từ WHOIS, danh sách field nhạy cảm phát hiện trong form, và domain thật mà nó đang nhái. Có nút "Gửi báo cáo" và "Không đồng ý với kết quả".
  - Phụ thuộc API: `GET /v1/scan/{scanId}`

- [ ] **Trang Lịch sử kiểm tra (Scan History)**
  - Nền tảng: Web
  - Mô tả: Bảng phân trang các lượt scan của chính user: thời gian, input đã chuẩn hoá, mức rủi ro (badge màu), nút xem lại chi tiết. Có filter theo `riskLevel` và khoảng thời gian. **Có nút xoá lịch sử** (yêu cầu privacy bắt buộc).
  - Phụ thuộc API: `GET /v1/scan/history`, `DELETE /v1/scan/history`

- [ ] **Trang Admin quản lý Domain Whitelist / Blacklist**
  - Nền tảng: Web
  - Mô tả: Danh sách `risk_entities` có `entityType = DOMAIN | URL`: thêm/sửa/xoá, gắn `riskLevel`, ghi rõ `source` (MANUAL / PHISHTANK / OPENPHISH / COMMUNITY), bật/tắt. Có ô test nhanh: nhập domain → xem sẽ khớp luật nào.
  - Phụ thuộc API: `GET|POST /v1/admin/entities`, `PUT /v1/admin/entities/{id}`

### Mobile (React Native)

- [ ] **Màn hình Scan URL + nhận link chia sẻ**
  - Nền tảng: Mobile
  - Mô tả: Tab nhập/dán URL, và **Share Target** để user chia sẻ link trực tiếp từ Zalo/Messenger/Chrome sang app. Tuyệt đối **không đọc clipboard tự động** — chỉ nhận dữ liệu khi user chủ động chia sẻ hoặc dán.
  - Phụ thuộc API: `POST /v1/scan/url`, `GET /v1/scan/{scanId}`

---

## Nhóm 2 · Backend API (Spring Boot)

- [ ] **POST /v1/scan/url**
  - Mô tả: Nhận `{ url, idempotencyKey }`. Validate scheme (chỉ `http`/`https`), độ dài ≤ 2048. **Chuẩn hoá URL**: lowercase host, punycode/IDN, decode percent-encoding, bỏ fragment, bỏ tracking param (`utm_*`, `fbclid`), sắp xếp query. Tính `urlHash = SHA-256(normalizedUrl)` → check cache Redis. Cache hit → trả `RiskResult` ngay (200). Cache miss → tạo `scan_requests` trạng thái `PROCESSING`, publish job vào queue `scan.url.requested`, trả **202 kèm `scanId`**.
  - Xử lý đặc biệt: Rate limit 100 req/giờ/user và 300 req/giờ/IP · Idempotency key TTL 5 phút · Redis lock theo `urlHash` để 2 người scan cùng link không tạo 2 job · Cho phép gọi khi chưa đăng nhập nhưng hạn mức thấp hơn (20 req/giờ/IP).

- [ ] **GET /v1/scan/{scanId}**
  - Mô tả: Trả trạng thái + kết quả của một lượt scan: `PROCESSING` / `DONE` / `FAILED`, kèm `riskScore`, `riskLevel`, `evidence[]`, `recommendations[]` khi đã xong. Dùng cho polling từ web và mobile.
  - Xử lý đặc biệt: RBAC — chỉ chủ sở hữu `scanId` hoặc ADMIN được xem. Trả 404 (không phải 403) khi không có quyền để tránh lộ sự tồn tại của bản ghi.

- [ ] **GET /v1/scan/history**
  - Mô tả: Danh sách lượt scan của user hiện tại, phân trang cursor-based, filter theo `inputType`, `riskLevel`, khoảng ngày.
  - Xử lý đặc biệt: Cần JWT hợp lệ. Chỉ trả bản ghi của chính user.

- [ ] **DELETE /v1/scan/history**
  - Mô tả: Xoá toàn bộ (hoặc theo danh sách `scanId`) lịch sử scan của user. Xoá cả evidence file tương ứng trên MinIO.
  - Xử lý đặc biệt: Ghi `audit_logs` với action `SCAN_HISTORY_DELETED` (dùng bảng của Hùng). Xoá thật, không soft-delete — đây là cam kết privacy với người dùng.

- [ ] **GET|POST /v1/admin/entities** _(Admin only)_
  - Mô tả: Quản lý whitelist/blacklist domain & URL trong `risk_entities`. POST hỗ trợ thêm hàng loạt (bulk import từ file).
  - Xử lý đặc biệt: RBAC ADMIN · Audit log mọi thay đổi · Invalidate cache whitelist/blacklist trong Redis sau khi ghi.

---

## Nhóm 3 · Background Workers (RabbitMQ / Async)

- [ ] **URL Scanner Worker** — lắng nghe queue: `scan.url.requested`
  - Mô tả: Trái tim của slice. Nhận `{ scanId, normalizedUrl, urlHash }` rồi lần lượt:
    1. **SSRF Guard** — resolve DNS, kiểm tra IP đích trước khi mở kết nối (xem Nhóm 5)
    2. **Redirect chain** — đi tối đa 5 hop, ghi lại từng hop, **validate lại IP sau mỗi hop** (chống DNS rebinding)
    3. **TLS/Certificate** — có HTTPS không, CA, hạn dùng, hostname có khớp không, self-signed hay không
    4. **Domain intel** — tuổi domain (WHOIS), TLD rủi ro (`.tk`, `.xyz`, `.top`), số subdomain, IP thay cho domain
    5. **Typosquatting** — Levenshtein / Jaro-Winkler so với \~100 brand phổ biến tại VN (ngân hàng, sàn TMĐT, ví điện tử, cơ quan nhà nước)
    6. **Phân tích HTML/form bằng Jsoup** — đếm và phân loại input nhạy cảm (password, OTP, số thẻ, CCCD), form POST sang domain khác, iframe ẩn, form không HTTPS
    7. Gom toàn bộ đặc trưng thành `UrlFeatureSet` → gọi **Rule Engine** (của Kiên/Thắng) để chấm điểm
  - Output: Ghi `risk_results` (evidence dạng JSONB) + cập nhật `scan_requests.status = DONE` + set cache Redis theo `urlHash` + lưu HTML snapshot lên MinIO.
  - Retry / Dead-letter: Có — retry 3 lần với exponential backoff (2s/8s/30s). Lỗi mạng/timeout thì retry; lỗi SSRF-blocked hoặc URL không hợp lệ thì **fail ngay, không retry**. Quá 3 lần → DLQ `scan.url.dlq`, `scan_requests.status = FAILED` kèm `failureReason`.

- [ ] **Threat Feed Ingestion Worker** — lắng nghe queue: `threat.feed.sync` _(Phase 2)_
  - Mô tả: Chạy theo scheduler (mỗi 6 giờ), nạp dữ liệu link/domain độc hại từ các nguồn công khai: PhishTank, OpenPhish, URLhaus (abuse.ch), Google Safe Browsing Lookup API. Chuẩn hoá và upsert vào `risk_entities` với `source` tương ứng. Không ghi đè entry `source = MANUAL` do admin tạo.
  - Output: Upsert `risk_entities`, ghi log số bản ghi thêm/cập nhật vào `feed_sync_logs`.
  - Retry / Dead-letter: Có — retry 3 lần. Feed lỗi thì bỏ qua vòng đó, không làm hỏng dữ liệu đang có.

---

## Nhóm 4 · Dữ liệu & Caching (PostgreSQL · Redis · MinIO)

### PostgreSQL

- [ ] **Bảng `scan_requests`** _(dùng chung với slice 3 — mình chịu trách nhiệm phần `inputType = URL`)_
  - Các cột chính: `id` (UUID), `user_id` (nullable — cho phép scan ẩn danh), `input_type` (enum: URL/TEXT/PHONE/BANK_ACCOUNT/QR), `raw_input`, `normalized_input`, `input_hash`, `status` (enum: PROCESSING/DONE/FAILED), `failure_reason`, `created_at`, `completed_at`.
  - Index: `(user_id, created_at DESC)` cho trang lịch sử; `(input_hash, created_at DESC)` cho cache warm-up.

- [ ] **Bảng `risk_results`**
  - Các cột chính: `id`, `scan_request_id` (FK), `risk_score` (0–100), `risk_level` (enum: SAFE/CAUTION/DANGER), `evidence` (JSONB), `recommendations` (JSONB), `engine_version`, `created_at`.
  - Lý do lưu `engine_version`: khi rule thay đổi trọng số, vẫn giải thích được vì sao link này từng bị chấm 92 điểm.

- [ ] **Bảng `url_scan_details`**
  - Lưu: Chi tiết kỹ thuật riêng của URL scan, tách khỏi `risk_results` để trang chi tiết truy vấn nhanh.
  - Các cột chính: `scan_request_id` (FK), `final_url`, `redirect_chain` (JSONB — mảng hop kèm status code và IP), `http_status`, `tls_info` (JSONB), `domain_age_days`, `registrar`, `form_findings` (JSONB), `html_snapshot_key` (đường dẫn MinIO), `fetch_duration_ms`.

- [ ] **Bảng `risk_entities`** _(phần `entityType = DOMAIN | URL`)_
  - Các cột chính: `id`, `entity_type`, `value` (đã chuẩn hoá), `value_hash`, `risk_level`, `source` (MANUAL/PHISHTANK/OPENPHISH/URLHAUS/COMMUNITY), `status` (ACTIVE/INACTIVE), `report_count`, `last_seen_at`, `created_at`.
  - Ràng buộc: UNIQUE `(entity_type, value_hash)`.

- [ ] **JSONB column `evidence`** trong bảng `risk_results`
  - Cấu trúc: `[{ ruleCode, ruleName, weight, matched: true, detail: "Domain đăng ký 5 ngày trước", severity }]`
  - Lý do dùng JSONB: mỗi loại đầu vào sinh ra bằng chứng khác nhau; JSONB cho phép thêm rule mới không cần migration, vẫn query được bằng GIN index khi cần thống kê rule nào hay khớp nhất.

### Redis

- [ ] **Cache kết quả scan URL** — key: `cache:scan:url:{urlHash}` — TTL: **30 phút**
  - Mục đích: Cùng một link được nhiều người gửi (link scam thường lan theo nhóm chat) → trả kết quả tức thì, không fetch lại. TTL ngắn vì site phishing bị takedown rất nhanh, kết quả cũ dễ sai.

- [ ] **Idempotency key** — key: `idem:scan:url:{userId}:{idempotencyKey}` — TTL: 5 phút
  - Mục đích: Người dùng bấm "Kiểm tra" nhiều lần hoặc mobile retry khi mạng yếu → chỉ tạo 1 `scanId`.

- [ ] **Rate limit scan** — key: `rate:scan:url:{userId|ip}:{window}` — TTL: 1 giờ
  - Mục đích: 100 req/giờ/user, 300 req/giờ/IP, 20 req/giờ cho khách chưa đăng nhập. Chống lạm dụng hệ thống làm công cụ quét/dò cho mục đích khác.

- [ ] **Distributed lock theo URL** — key: `lock:scan:url:{urlHash}` — TTL: 60 giây
  - Mục đích: Nhiều request cùng lúc cho một link chỉ sinh **một** job worker; các request còn lại chờ và dùng chung kết quả.

- [ ] **Cache whitelist/blacklist domain** — key: `cache:entities:domain:{level}` — TTL: 10 phút
  - Mục đích: Worker tra whitelist/blacklist mỗi lần scan; cache để không đánh vào PostgreSQL liên tục. Invalidate ngay khi admin sửa entity.

### MinIO (Object Storage)

- [ ] **HTML snapshot của trang bị scan** — key: `evidence/url/{scanId}/page.html`
  - Mục đích: Lưu bằng chứng để bảo vệ kết quả cảnh báo khi trang đã bị takedown hoặc chủ trang khiếu nại. Chỉ lưu HTML tĩnh (không JS, không tài nguyên ngoài), giới hạn 2 MB, **retention 30 ngày** rồi tự xoá.

- [ ] **Ảnh chụp trang (screenshot)** — key: `evidence/url/{scanId}/screenshot.png` _(Phase 2)_
  - Mục đích: Hiển thị trực quan cho admin khi kiểm duyệt báo cáo, người dùng không cần mở link thật. Render trong sandbox headless tách biệt hoàn toàn khỏi mạng nội bộ.

---

## Nhóm 5 · Security, Privacy & Trust-by-design

- [ ] **SSRF Guard — lớp bảo vệ quan trọng nhất của slice**
  - Mô tả: Worker của mình là thành phần duy nhất chủ động gửi HTTP request tới địa chỉ do **người dùng nhập**, nên nếu không chặn đúng, hệ thống trở thành công cụ để kẻ tấn công truy cập nội bộ. Quy tắc bắt buộc:
    - Chỉ cho phép scheme `http` / `https`; chặn `file://`, `gopher://`, `ftp://`, `data:`
    - Chỉ cho phép port 80 và 443
    - Resolve DNS **trước** khi mở kết nối, và chỉ kết nối tới đúng IP đã kiểm tra (pinning)
    - Chặn IP private/reserved: `10/8`, `172.16/12`, `192.168/16`, `127/8`, `0.0.0.0/8`, `169.254/16` (metadata endpoint của cloud), `::1`, `fc00::/7`, `fe80::/10`
    - **Validate lại IP sau mỗi redirect** để chống DNS rebinding; tối đa 5 hop
    - Timeout 5 giây/hop, tổng tối đa 15 giây; giới hạn response 2 MB
    - Egress đi qua network/proxy riêng, không có route tới subnet nội bộ

- [ ] **Không thực thi JavaScript, chỉ parse HTML tĩnh**
  - Mô tả: Dùng Jsoup để parse, **không** dùng headless browser ở Phase 1 MVP. Tránh rủi ro bị khai thác qua JS độc hại và tiết kiệm tài nguyên. Nếu Phase 2 cần screenshot thì phải chạy trong sandbox riêng, không chung network với API.

- [ ] **Không tương tác với trang đích ngoài một request GET**
  - Mô tả: Chỉ GET, không submit form, không tải file về, không thực hiện thao tác nào có thể gây tác động lên hệ thống của người khác. Gửi `User-Agent` minh bạch có ghi mục đích và địa chỉ liên hệ — đây là chuẩn mực đạo đức khi công cụ tự động truy cập web của người khác.

- [ ] **Không lưu dữ liệu nhạy cảm từ trang đã fetch**
  - Mô tả: Chỉ lưu **cấu trúc** form (có field tên `otp`, `password`...), không lưu giá trị. URL đầy đủ được lưu cho user xem lại, nhưng trong log hệ thống chỉ ghi `urlHash` để tránh rò rỉ link chứa token trong query string.

- [ ] **Chống lạm dụng hệ thống (abuse prevention)**
  - Mô tả: Rate limit nhiều tầng + chặn scan cùng một domain quá nhiều lần trong thời gian ngắn, để hệ thống không bị dùng như công cụ dò quét hay khuếch đại tấn công vào bên thứ ba. Vượt ngưỡng bất thường → ghi `audit_logs` và cảnh báo admin.

- [ ] **Ngôn ngữ cảnh báo thận trọng, không khẳng định pháp lý**
  - Mô tả: Kết quả dùng cách diễn đạt "có dấu hiệu rủi ro cao", "cần xác minh trước khi nhập thông tin", "đã có báo cáo từ cộng đồng". **Không** viết "đây là trang lừa đảo" — hệ thống đưa ra đánh giá tự động, không phải kết luận điều tra.

- [ ] **Giải thích được mọi cảnh báo (explainability)**
  - Mô tả: Mỗi điểm rủi ro phải truy được về rule cụ thể và bằng chứng cụ thể. Không có "điểm bí ẩn". Đây là lý do MVP chọn rule-based thay vì ML, và cũng là yếu tố để người dùng tin hệ thống.

- [ ] **Cơ chế khiếu nại kết quả sai (false positive)**
  - Mô tả: Trang kết quả có nút "Không đồng ý với kết quả" → tạo bản ghi để admin xem lại, có thể thêm domain vào whitelist. Tránh việc hệ thống làm hại oan một website hợp pháp.

---

## Ghi chú & Dependencies

| Phụ thuộc vào | Của thành viên | Lý do cần |
|---------------|----------------|-----------|
| Rule Engine core (`risk_rules`, API chấm điểm) | Kiên / Thắng (slice 3) | Worker của mình chỉ **trích xuất đặc trưng** URL; việc chấm điểm và tổng hợp evidence do Rule Engine làm. Cần chốt sớm interface `UrlFeatureSet` → `RiskResult`. |
| Component `RiskResultCard` | Thắng (slice 3) | Trang kết quả URL dùng lại card này để giao diện toàn hệ thống nhất quán; mình chỉ bổ sung phần chi tiết riêng của URL. |
| Auth / JWT / RBAC / `audit_logs` | Hùng (slice 1) | Cần JWT để gắn `scan_requests.user_id`, cần RBAC cho route admin entities, cần bảng audit log để ghi thao tác xoá lịch sử và sửa whitelist. |
| Module Community Report | Slice 4 | Nút "Gửi báo cáo" và nút khiếu nại false positive trên trang kết quả cần API report; số lần bị báo cáo cũng là một đặc trưng đầu vào cho điểm rủi ro. |

### Bộ luật URL dự kiến (đề xuất cho Rule Engine)

| Mã luật | Điều kiện | Trọng số |
|---------|-----------|----------|
| `URL_IN_BLACKLIST` | Khớp `risk_entities` blacklist | +50 |
| `DOMAIN_TYPOSQUATTING` | Levenshtein ≤ 2 với brand VN phổ biến | +30 |
| `URL_USES_IP` | Dùng IP thay vì domain | +25 |
| `FORM_ASKS_SENSITIVE` | Form có field OTP/password/CCCD/số thẻ | +25 |
| `NO_HTTPS` | Không HTTPS hoặc lỗi chứng chỉ | +20 |
| `DOMAIN_TOO_NEW` | Tuổi domain < 30 ngày | +20 |
| `SUSPICIOUS_CHARS` | Có `@` hoặc `//` bất thường trong URL | +15 |
| `TOO_MANY_SUBDOMAINS` | Số subdomain > 3 | +15 |
| `MULTI_DOMAIN_REDIRECT` | Redirect qua nhiều domain khác nhau | +10/hop |
| `URL_TOO_LONG` | Độ dài > 75 ký tự | +10 |
| `DOMAIN_IN_WHITELIST` | Khớp whitelist | −30 |

### Câu hỏi cần chốt với nhóm / giảng viên

- [ ] Interface giữa URL Scanner và Rule Engine: gọi trực tiếp trong monolith hay qua message? (đề xuất: gọi trực tiếp ở Phase 1)
- [ ] Có tích hợp Google Safe Browsing / PhishTank thật ở MVP hay để Phase 2? (ảnh hưởng tới việc có cần Threat Feed Worker ngay hay không)
- [ ] Có cần headless browser screenshot cho demo bảo vệ khoá luận hay HTML snapshot là đủ?
- [ ] Whitelist \~100 brand VN lấy từ đâu và ai duy trì?

---

*Cập nhật lần cuối: 09/08/2026 · Phiên bản template: Week 4*
