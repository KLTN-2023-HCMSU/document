# Mô tả kiến trúc Anti-Scam Platform

**Phiên bản:** 2026-07-28  
**Phạm vi:** Gói trình bày kiến trúc tuần 3  
**Kiến trúc đề xuất:** Modular Monolith + Event-Driven Workers

## 1. Mục tiêu hệ thống

Anti-Scam Platform là nền tảng web và mobile giúp người dùng đánh giá rủi ro trước khi bấm link, nhập thông tin, quét QR hoặc chuyển khoản. Hệ thống tiếp nhận sáu nhóm đầu vào:

1. URL hoặc link đáng ngờ.
2. Nội dung tin nhắn, đoạn chat hoặc mô tả giao dịch.
3. Số điện thoại.
4. Số tài khoản ngân hàng.
5. QR hoặc VietQR.
6. Báo cáo lừa đảo từ cộng đồng.

Mọi scanner trả về một mô hình kết quả thống nhất:

- `riskScore` từ 0 đến 100.
- `riskLevel`: `SAFE`, `CAUTION` hoặc `DANGER`.
- Danh sách evidence cho biết rule nào đã khớp.
- Giải thích ngắn gọn và khuyến nghị hành động.

MVP ưu tiên rule-based kết hợp heuristic. Cách tiếp cận này minh bạch, kiểm thử được và không phụ thuộc dataset lớn. Machine learning chỉ được xem xét ở Phase 3 khi đã có dữ liệu gắn nhãn, baseline và metric đáng tin cậy.

## 2. Nguồn và thuật ngữ chuẩn

Thứ tự nguồn tham chiếu:

1. Feature spec và presentation contract quy định phạm vi đầu ra.
2. `document/Week3/01_Thiet_ke_kien_truc/Thiet_ke_kien_truc_he_thong.md` là nguồn kỹ thuật chính.
3. Tài liệu Week 1 và Week 2 cung cấp bối cảnh nghiệp vụ, quyền riêng tư và phạm vi MVP.

| Tên chuẩn | Trách nhiệm |
|---|---|
| Anti-Scam Platform | Toàn bộ nền tảng cảnh báo rủi ro |
| Modular Monolith + Event-Driven Workers | Kiến trúc được chọn |
| Web User | Client web cho người dùng |
| Admin Dashboard | Client web cho quản trị viên |
| Mobile App | Client React Native |
| Scan Orchestrator | Điều phối scan đồng bộ và bất đồng bộ |
| Rule Engine | Tính điểm, evidence và risk level |
| URL Scanner | Phân tích URL, redirect, TLS và HTML/form |
| Text Analyzer | Phân tích keyword, pattern và entity |
| Phone/Bank Checker | Tra cứu thực thể rủi ro |
| QR Parser | Decode và định tuyến URL, text hoặc VietQR |
| PostgreSQL | Nguồn dữ liệu nghiệp vụ chính |
| JSONB | Cấu trúc lưu kết quả scan đa dạng |
| Redis | Cache, rate limit, idempotency và lock |
| RabbitMQ | Job queue của MVP |
| MinIO | Object storage local tương thích S3 |

Kafka, full microservices và machine learning là hướng **Future**, không phải cam kết của MVP.

## 3. Quyết định kiến trúc

### 3.1. Phương án được chọn

Nhóm chọn **Modular Monolith + Event-Driven Workers**:

- Một Spring Boot application giữ API và nghiệp vụ cốt lõi, nhưng được chia thành module có ranh giới rõ.
- Các tác vụ chậm hoặc phụ thuộc network chạy qua RabbitMQ và worker riêng.
- PostgreSQL là nguồn dữ liệu chính; Redis phục vụ dữ liệu ngắn hạn và điều phối.
- Worker có thể chạy thành process/container độc lập dù vẫn dùng chung codebase và contract.

### 3.2. Vì sao phù hợp với nhóm 4 người

- Ít overhead deploy, tracing và versioning hơn full microservices.
- Debug end-to-end đơn giản hơn trong giai đoạn khóa luận.
- Ranh giới module vẫn rõ để chia việc theo vertical slice.
- Worker xử lý được URL fetch, export và ingestion mà không làm request chính chờ lâu.
- Có đường nâng cấp: tách scanner thành service khi tải, ownership hoặc chu kỳ release thực sự khác nhau.

### 3.3. Các phương án thay thế

| Phương án | Điểm mạnh | Giới hạn | Kết luận |
|---|---|---|---|
| Modular Monolith thuần | Nhanh làm, dễ debug | Tác vụ chậm dễ ảnh hưởng request | Phù hợp prototype nhỏ |
| Modular Monolith + Workers | Cân bằng đơn giản và mở rộng | Cần quản lý queue, retry và trạng thái job | **Chọn cho MVP** |
| Full Microservices | Scale và release từng service | Nặng vận hành, test và observability | Future khi có bằng chứng |

### 3.4. Điều kiện xem xét lại

Chỉ tách service khi có ít nhất một điều kiện rõ:

- Một scanner cần scale độc lập thường xuyên.
- Module có owner và release cadence riêng.
- Failure domain cần cô lập.
- Dữ liệu hoặc yêu cầu bảo mật buộc phải tách.
- Lưu lượng event cần replay/analytics dài hạn, khi đó mới cân nhắc Kafka.

## 4. Kiến trúc tổng thể

```text
Web User ───────┐
Admin Dashboard ├─> Nginx/TLS ─> Spring Boot API
Mobile App ─────┘                    │
                                    ├─ Auth/User
                                    ├─ Scan Orchestrator ─> Rule Engine
                                    ├─ Report / Admin / Notification
                                    ├─ PostgreSQL / Redis / MinIO
                                    └─ RabbitMQ ─> Scanner & background workers
```

### Client

- **Web User - Next.js:** scan URL/text/phone/account, xem lịch sử và gửi report.
- **Admin Dashboard - Next.js:** duyệt report, quản lý rule/entity và xem audit/dashboard.
- **Mobile App - React Native:** share link, paste text, nhập phone/account và quét QR.

Mobile MVP chỉ xử lý dữ liệu người dùng chủ động gửi. Ứng dụng không đọc ngầm SMS, call log, notification, Accessibility hoặc clipboard nền.

### Backend Spring Boot

- **Auth/User:** đăng ký, đăng nhập, token, role và hồ sơ.
- **Scan Orchestrator:** chuẩn hóa input, kiểm tra cache, chọn sync/async và gom kết quả.
- **Rule Engine:** áp dụng rule, tính điểm, tạo evidence và recommendation.
- **Report:** tiếp nhận báo cáo cộng đồng và evidence.
- **Admin:** duyệt report, rule, entity và audit.
- **Notification:** thông báo trạng thái hoặc kết quả khi cần.

### Workers

- **URL Scanner:** redirect, TLS, domain, HTML/form và SSRF guard.
- **Text Analyzer:** keyword, entity, pattern và composite rule.
- **Phone/Bank Checker:** blacklist, whitelist và community evidence.
- **QR Parser:** decode rồi định tuyến URL, text hoặc VietQR.
- **Report Export:** tạo báo cáo HTML/PDF ở Phase 2.
- **Threat Data Ingestion:** nhập nguồn threat data ở Phase 2.

## 5. Luồng scan URL đồng bộ và bất đồng bộ

1. Client gửi URL kèm idempotency key.
2. API kiểm tra rate limit và idempotency trong Redis.
3. API chuẩn hóa URL và kiểm tra recent cache.
4. Nếu cache hit, trả `RiskResult` ngay.
5. Nếu cache miss, tạo `ScanRequest` trạng thái `PROCESSING`.
6. API publish job vào RabbitMQ và trả `scanId`.
7. URL Scanner kiểm tra redirect, TLS, domain và HTML/form trong giới hạn an toàn.
8. Rule Engine tính điểm và evidence.
9. Worker lưu `RiskResult` vào PostgreSQL/JSONB và cập nhật cache.
10. Client lấy kết quả hoàn tất bằng `scanId`.

Các lookup nhẹ như phone/account có thể xử lý đồng bộ nếu cache hoặc database trả nhanh. URL fetch, report export và ingestion mặc định là ứng viên bất đồng bộ.

## 6. Kiến trúc dữ liệu

### PostgreSQL và JSONB

PostgreSQL lưu user, scan request, report, rule, risk entity, audit và trạng thái nghiệp vụ. `JSONB` được dùng cho payload kết quả scan vì evidence của URL, text và QR có cấu trúc khác nhau nhưng vẫn cần transaction, ownership và lịch sử thống nhất.

### Redis

Redis không phải nguồn dữ liệu chính. Redis phục vụ:

- Cache kết quả scan gần đây.
- Cache active rule và risk entity.
- Rate limit theo user/IP.
- Idempotency key cho scan/report.
- Lock nhẹ để tránh scan trùng hoặc import trùng.

### RabbitMQ và MinIO

RabbitMQ là job queue của MVP, có ack, retry và dead-letter queue. MinIO lưu evidence file hoặc report export; production có thể thay bằng storage tương thích S3.

## 7. Security, privacy và trust-by-design

Nguyên tắc đầu tiên là **user chủ động gửi gì thì hệ thống chỉ scan dữ liệu đó**:

- Không đọc ngầm SMS, call log hoặc notification.
- Không yêu cầu OTP, mật khẩu, PIN hoặc số thẻ.
- Hash phone/account khi dùng cho lookup hoặc log phù hợp.
- Cho phép user xem và xóa lịch sử của mình.

Các lớp bảo vệ:

- HTTPS ở edge; JWT access token ngắn hạn và refresh token.
- RBAC cho `USER` và `ADMIN`.
- Rate limit và idempotency chống abuse/retry trùng.
- Audit log cho thao tác quản trị.
- Validate input, giới hạn kích thước evidence và không ghi secret vào log.

URL Scanner phải chống SSRF:

- Chỉ cho phép HTTP/HTTPS.
- Chặn localhost, private IP, link-local và metadata endpoint.
- Resolve DNS và kiểm tra lại sau mỗi redirect.
- Giới hạn port, redirect count, timeout và response size.

## 8. Triển khai

### Local

Docker Compose chạy PostgreSQL, Redis, RabbitMQ, MinIO cùng API, web và worker. Nhóm có thể chỉ chạy infrastructure trong giai đoạn đầu.

### Production

Luồng public:

```text
Internet -> Cloudflare/Tunnel -> Nginx/TLS -> Web hoặc API
```

API, worker và data services nằm trong network nội bộ. PostgreSQL, Redis, RabbitMQ và MinIO không được public trực tiếp. Production có thể chạy trên AWS EC2 hoặc VPS bằng Docker Compose profile riêng, backup PostgreSQL/MinIO và log rotation.

## 9. Chia việc nhóm 4 người theo vertical slice

| Thành viên | Vertical slice | Demo độc lập |
|---|---|---|
| 1 | Auth/User, RBAC, profile và audit | Login, token hết hạn, chặn route admin |
| 2 | Scan URL, HTML/Form và SSRF guard | Safe URL, risky URL, redirect và private IP |
| 3 | Text, phone/bank, QR và Rule Engine | Scam text, entity match và QR routing |
| 4 | Community Report, Admin và dashboard | Gửi report, duyệt/từ chối và cập nhật dashboard |

Mỗi người sở hữu UI, API, data/cache/event, test và demo flow của slice. Các contract chung như error format, naming, migration, event và component risk result được cả nhóm thống nhất trước.

## 10. Roadmap

### Phase 1 - MVP

- Auth và role.
- Rule Engine giải thích được.
- Scan URL, text, phone/bank và QR routing.
- Community report và admin review.
- Web/mobile input chủ động.
- PostgreSQL, Redis và Docker Compose local.

### Phase 2 - Async, report và export

- Worker riêng và trạng thái async hoàn chỉnh.
- RabbitMQ retry/dead-letter.
- Report export, threat data ingestion.
- Notification khi scan hoàn tất.

### Phase 3 - Microservice, hybrid và ML

- Tách service khi có nhu cầu scale/ownership thật.
- Kafka khi cần replay và analytics event.
- ML prototype sau khi có dataset, baseline và metric.
- Hybrid scoring kết hợp rule evidence với model confidence.

## 11. Kết luận

Modular Monolith + Event-Driven Workers cân bằng ba mục tiêu:

1. **Khả thi:** nhóm 4 người có thể phát triển, test và deploy trong phạm vi khóa luận.
2. **Đáng tin:** kết quả giải thích được, privacy rõ và URL scanning có guard.
3. **Mở rộng được:** queue và module boundary tạo đường tách service khi có bằng chứng.

Kiến trúc không cố dự đoán mọi nhu cầu tương lai. Nó giữ MVP gọn, nhưng đặt đúng ranh giới để hệ thống có thể lớn lên mà không phải viết lại toàn bộ.
