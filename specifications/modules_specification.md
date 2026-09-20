# Anti-Scam Platform — End-to-End Module Specification

> Mỗi **Mxx** là một module có owner riêng. Owner chịu trách nhiệm xuyên suốt từ UI liên quan → API/event contract → backend use case → worker (nếu có) → data/storage → test. Các module dùng chung contract nhưng không chia ownership theo kiểu “frontend team / backend team”.

> **Đồng bộ kiến trúc:** tài liệu này khớp với `../architectures/architecture_v3.1.md` **revision V3.2 (Async AI Alignment)** và bộ sơ đồ trong `../architectures/diagrams/`. Hai thay đổi lớn so với bản trước:
>
> 1. **M11 AI/ML không còn là service được gọi đồng bộ** mà là worker tiêu thụ hàng đợi. M02/M03 publish AI task rồi trả kết quả ngay; M12 chịu trách nhiệm chờ và đóng AI task.
> 2. **Worker được cô lập hoàn toàn.** Không worker nào gọi ngược API của core hay chạm PostgreSQL/Redis. Chỉ dấu phát hiện giữa chừng được trả về qua `derivedIndicators[]` kèm `handling`; M12 tra uy tín rồi gắn signal hoặc tạo scan con. Xem `architecture_v3.1.md` mục 4.6.1.

## Module Catalog

| ID | Module | Type | Actor chính |
| --- | --- | --- | --- |
| **M01** | Account & Identity | End-to-End Feature | User, Admin |
| **M02** | URL & Website Risk Scan | End-to-End Feature | User |
| **M03** | Text & Transaction Scam Analysis | End-to-End Feature | User |
| **M04** | Phone & Bank Reputation Check | End-to-End Feature | User |
| **M05** | QR / VietQR Scan | End-to-End Feature | User |
| **M06** | Scan Result, History & Export | End-to-End Feature | User |
| **M07** | Community Report & Moderation | End-to-End Feature | User, Moderator/Admin |
| **M08** | Threat Intelligence Management | Admin/Internal Feature | Admin, Internal Scanner |
| **M09** | Rule, Risk Policy & Admin Operations | Shared/Admin Feature | Admin, All Scan Modules |
| **M10** | Notification & User Follow-up | End-to-End Feature | User, Admin |
| **M11** | AI/ML Inference | Shared Capability (async worker) | M02, M03 qua M12 |
| **M12** | Shared Scan Platform | Shared Platform | M02–M06, M08–M11 |
| **M13** | Platform Infrastructure | Infrastructure | Toàn hệ thống |

---

# M01. Account & Identity

## 1. Module summary

| Thuộc tính | Giá trị |
| --- | --- |
| Type | End-to-End Feature |
| Actor | User, Admin |
| UI | User Web, Mobile, Admin Dashboard |
| Backend owner | Auth & User domain |
| Data owner | `users`, `roles`, `user_roles`, session/refresh metadata |
| Phụ thuộc chính | PostgreSQL, Redis (nếu dùng session/revocation), Audit |

## 2. Mục đích

Cung cấp danh tính, session/token và quyền truy cập thống nhất cho User Web, Mobile và Admin. Các module khác chỉ nhận `userContext`/`adminContext` đã được xác thực, không tự triển khai logic authentication riêng.

## 3. Góc nhìn người dùng / actor journey

**User Web / Mobile**

```text
Mở ứng dụng
→ đăng ký hoặc đăng nhập
→ vào trang chính
→ thực hiện scan/report/history
→ xem/chỉnh profile
→ logout khi cần
```

**Admin**

```text
Đăng nhập Admin Dashboard
→ hệ thống xác minh role/permission
→ chỉ hiển thị và cho phép các chức năng quản trị được cấp quyền
```

## 4. Phạm vi nghiệp vụ

- register/login/logout nếu nằm trong MVP;
- refresh/session lifecycle;
- xem/chỉnh profile;
- xác định `AuthenticatedPrincipal`;
- RBAC cho User/Admin/Moderator;
- route guard ở client và authorization thật ở backend;
- revoke/expire session khi cần.

## 5. UI/UX ownership

**Web**
- Login/Register;
- Profile;
- Logout;
- authenticated route guard;
- hiển thị lỗi 401/403 hợp lý.

**Mobile**
- cùng core flow với Web;
- secure token/session storage phù hợp mobile;
- tự refresh session nếu contract hỗ trợ.

**Admin Dashboard**
- admin login;
- role-based navigation;
- unauthorized page/state.

## 6. API / event contracts

```text
POST  /v1/auth/register
POST  /v1/auth/login
POST  /v1/auth/refresh
POST  /v1/auth/logout
GET   /v1/users/me
PATCH /v1/users/me
```

Không có RabbitMQ event bắt buộc cho core auth flow. Security-sensitive admin/user actions có thể phát audit event.

## 7. Backend / worker responsibilities

**Backend**
- validate credentials;
- hash/verify password;
- issue/refresh/revoke token/session;
- load profile;
- enforce RBAC;
- propagate authenticated context cho downstream modules.

**Worker**
- không có worker riêng.

## 8. Input

```text
credentials
access/refresh token hoặc session
profile update command
request cần authorization
```

## 9. Output

```text
AuthResult
UserProfile
AuthenticatedPrincipal
Roles / Permissions
401 / 403 standardized errors
```

## 10. Data ownership

```text
users
roles
user_roles
sessions / refresh token metadata (nếu dùng)
```

## 11. Business rules / invariants

- UI guard không thay thế backend authorization;
- token hết hạn phải có behavior thống nhất giữa Web/Mobile;
- Admin không mặc định có toàn quyền nếu hệ thống dùng granular permissions;
- các module khác không tự đọc credential hay tự verify token.

## 12. Failure / degraded behavior

- sai credential → fail fast;
- token hết hạn → refresh hoặc yêu cầu login lại theo contract;
- Redis session cache unavailable → nếu kiến trúc cho phép, fallback theo source of truth thay vì phá toàn bộ hệ thống;
- không retry login mutation một cách mù quáng.

## 13. Security / privacy

- password dùng hash phù hợp;
- không log raw password/token;
- refresh token/session metadata phải có revoke/expiry;
- profile field nhạy cảm phải mask/authorize;
- rate limit login phù hợp.

## 14. Dependencies

- M13 Platform Infrastructure;
- M09 Audit/Admin Operations cho security-sensitive actions.

## 15. Phát triển độc lập / mock contract

Owner tạo trước:

```text
AuthApi
MockAuthApi
mock-user.json
mock-admin.json
mock-expired-session.json
```

Frontend có thể hoàn thiện login/profile/route guard trước khi backend auth xong.

## 16. Definition of Done

- Web + Mobile dùng cùng auth contract;
- Admin RBAC chạy đúng;
- integration test login/refresh/logout;
- không có credential/token trong log;
- mock adapter tồn tại để client dev độc lập.

---

# M02. URL & Website Risk Scan

## 1. Module summary

| Thuộc tính | Giá trị |
| --- | --- |
| Type | End-to-End Feature |
| Actor | User |
| UI | User Web, Mobile |
| API | `POST /v1/scans/url` |
| Worker | Web/URL Scanner Worker |
| Shared dependencies | M08 Threat Intelligence, M09 Risk Policy, M11 AI (async), M12 Scan Platform |

## 2. Mục đích

Cho phép user nhập hoặc share một URL đáng nghi và nhận đánh giá rủi ro dựa trên URL/domain, DNS, TLS, redirect, HTML/Form, reputation, rule và AI nếu được bật.

## 3. Góc nhìn người dùng / actor journey

**Web**

```text
Paste URL
→ bấm “Kiểm tra”
→ thấy trạng thái đang phân tích
→ nhận điểm rủi ro + mức cảnh báo
→ xem bằng chứng: domain/redirect/form/reputation/AI
→ đọc khuyến nghị
```

**Mobile**

```text
Paste URL hoặc Share URL từ browser/Zalo/Facebook/app khác
→ mở Anti-Scam
→ xác nhận scan
→ xem kết quả giống Web
```

User không cần biết worker/DNS/TLS hoạt động thế nào; họ chỉ cần hiểu **vì sao URL bị đánh giá rủi ro**.

## 4. Phạm vi nghiệp vụ

- scan URL/link;
- normalize/validate URL;
- lexical/domain analysis;
- DNS/domain metadata;
- redirect chain;
- TLS/HTTPS;
- typosquatting;
- safe fetch public content;
- HTML/Form analysis;
- reputation lookup;
- optional AI inference;
- derived indicators nếu phát hiện URL/domain liên quan.

`HTML/Form` là internal mode của URL scan, không phải public feature riêng.

## 5. UI/UX ownership

- URL input form;
- paste/share flow;
- validation feedback;
- processing state;
- URL-specific evidence groups;
- degraded state nếu AI/reputation unavailable;
- integration với shared result page M06.

Evidence group đề xuất:

```text
Domain / URL
Redirect
TLS / HTTPS
Form / Credential Collection
Reputation
AI Prediction (nếu có)
```

## 6. API / event contracts

HTTP:

```text
POST /v1/scans/url
GET  /v1/scans/{scanId}     (read qua M06)
```

Request:

```json
{
  "url": "https://example.com",
  "source": "MANUAL"
}
```

RabbitMQ:

```text
queue:       q.scan.url
request key: scan.url.requested
result key:  scan.url.analyzed
```

AI (tùy chọn, bất đồng bộ — xem M11):

```text
queue:       q.ai.analyze
request key: ai.analysis.requested     (worker publish, kind = URL_FEATURES | WEB_CONTENT)
result key:  ai.analysis.completed | ai.analysis.failed   (M11 publish vào q.scan.result)
```

Worker khai `taskId` vừa gửi vào `pendingAiTasks[]` của `scan.url.analyzed`; M12 dựa vào đó để biết còn phải chờ AI.

## 7. Backend / worker responsibilities

**Backend**

```text
URL request
→ validate
→ idempotency/cache check
→ create ScanRequest
→ pre-enrich known reputation context
→ publish scan.url.requested
→ consume worker result
→ shared Rule/Risk Evaluation
→ persist final RiskResult
```

**Web/URL Scanner Worker**

```text
analyzeUrl(url)
├── normalizeUrl()
├── validateUrl()
├── extractUrlParts()
├── analyzeLexicalFeatures()
├── resolveDnsAndDomainMetadata()
├── analyzeRedirectChain()
├── analyzeTlsHttps()
├── detectTyposquatting()
├── usePreEnrichedReputationContext()
├── collectNewHostsAsIndicators()
├── safeFetchPublicContent()
├── analyzeHtmlForm()
└── publishAiTaskIfEnabled()
```

`collectNewHostsAsIndicators()` gom host mới xuất hiện trong chuỗi redirect thành `DerivedIndicator` với `handling=REPUTATION_ONLY`. Worker **không tra uy tín cho chúng** và không gọi ngược core; M12 tra khi consume result.

`publishAiTaskIfEnabled()` chỉ sinh `taskId`, publish `ai.analysis.requested` và khai `taskId` vào `pendingAiTasks[]`. Worker **không chờ AI**: job của nó kết thúc ngay sau khi publish result. Nếu publish AI task thất bại thì không được khai `taskId` đó, nếu không scan sẽ chờ một task không bao giờ tới.

## 8. Input

```text
url
source = MANUAL | MOBILE_SHARE | NESTED_SCAN
optional parentScanId/depth
optional reputationContext
```

## 9. Output

Worker output:

```text
Technical AnalysisSignal[]
Web-content AnalysisSignal[]
DerivedIndicator[]      (host mới, handling=REPUTATION_ONLY)
pendingAiTasks[]        (taskId của AI task đã gửi, rỗng nếu không bật AI)
```

Reputation signal **không** nằm trong output của worker này: worker chỉ tiêu thụ `reputationContext` kèm trong job, còn chỉ dấu mới thì để M12 tra.

AI signal **không** nằm trong output của worker này. Nó về sau, qua `q.scan.result`, do M11 publish và M12 ghép theo `scanId` + `taskId`.

Final output:

```text
RiskResult
```

## 10. Data ownership

Module không sở hữu bảng riêng độc lập ngoài scan state/result dùng shared platform. URL-specific trace có thể nằm trong:

```text
scan_requests
scan_results
scan_signals (optional)
```

## 11. Business rules / invariants

- Worker chỉ tạo signal, không tự quyết định final verdict;
- known URL/domain reputation được pre-enrich trước khi publish job;
- redirect/domain mới phát sinh được trả về `derivedIndicators[]`, **không** tra tại worker;
- worker không gọi API core, không đọc PostgreSQL/Redis;
- AI là optional signal và **về bất đồng bộ**; worker không bao giờ chờ AI;
- HTML/Form chỉ là bước phân tích nội bộ;
- output phải explainable bằng evidence thật có.

## 12. Failure / degraded behavior

- AI task timeout hoặc `ai.analysis.failed` → M12 đóng task, scan finalize với `degraded=true` và renormalize trọng số, không coi AI = 0;
- publish AI task thất bại → không khai `taskId`, scan chạy tiếp như khi AI tắt;
- reputation lookup lỗi → lỗi này xảy ra **ở M12/M08, không ở worker**; scan nhận `REPUTATION_UNAVAILABLE` và finalize degraded;
- fetch website lỗi → vẫn dùng lexical/DNS/TLS/reputation signals còn lại;
- worker fail sau retry → shared scan platform xử lý fail/degraded theo policy.

## 13. Security / privacy

SSRF protection bắt buộc:

- chỉ `http/https`;
- chặn private/loopback/link-local/metadata ranges;
- re-check destination sau redirect;
- redirect limit ban đầu = 5;
- response body limit = 5 MB;
- connect/read timeout;
- không forward internal credential/header;
- worker không truy cập PostgreSQL/Redis và không gọi API của core; kết nối ra ngoài duy nhất ngoài RabbitMQ là fetch public content.

## 14. Dependencies

- M08 Threat Intelligence;
- M09 Rule/Risk Policy;
- M11 AI/ML optional, **chỉ qua RabbitMQ**, không gọi trực tiếp;
- M12 Shared Scan Platform;
- M13 Infrastructure.

## 15. Phát triển độc lập / mock contract

Fixtures/contracts:

```text
url-safe.json
url-phishing.json
url-redirect-suspicious.json
url-form-credential.json
url-reputation-unavailable.json
url-ai-unavailable.json
```

Frontend mock lifecycle:

```text
submit
→ fake 202 scanId
→ mock PROCESSING
→ mock RiskResult
```

Worker có thể test trên URL fixture riêng mà không chờ UI.

## 16. Definition of Done

- Web/Mobile URL scan dùng cùng contract;
- SSRF guard có unit/integration tests;
- result idempotent;
- degraded flow rõ ràng;
- E2E từ URL input đến RiskResult UI.

---

# M03. Text & Transaction Scam Analysis

## 1. Module summary

| Thuộc tính | Giá trị |
| --- | --- |
| Type | End-to-End Feature |
| Actor | User |
| UI | User Web, Mobile |
| API | `POST /v1/scans/text` |
| Worker | Text Analyzer Worker |
| Shared dependencies | M02/M04 nested scans, M09, M11, M12 |

## 2. Mục đích

Phân tích tin nhắn, hội thoại hoặc bài đăng giao dịch để phát hiện dấu hiệu lừa đảo như urgency, impersonation, yêu cầu chuyển tiền/OTP/credential/CCCD và trích xuất các indicator con như URL, số điện thoại, tài khoản ngân hàng.

## 3. Góc nhìn người dùng / actor journey

```text
User copy nội dung tin nhắn/bài đăng đáng nghi
→ paste vào Web/Mobile
→ chọn loại nội dung nếu cần (MESSAGE / TRANSACTION_POST)
→ bấm Scan
→ hệ thống phân tích nội dung và các URL/SĐT/STK xuất hiện bên trong
→ user nhận điểm rủi ro + các đoạn/dấu hiệu đáng ngờ + khuyến nghị
```

Ví dụ user không chỉ thấy “DANGER” mà thấy lý do kiểu:

```text
- thúc giục chuyển tiền gấp
- yêu cầu OTP
- mạo danh tổ chức
- chứa URL có reputation xấu
```

## 4. Phạm vi nghiệp vụ

- MESSAGE;
- TRANSACTION_POST;
- text normalization;
- detect language/encoding;
- extract URL/phone/bank indicators;
- detect amount/deadline;
- urgency/impersonation cues;
- payment/credential request;
- sensitive identity request (CCCD cue);
- scenario classification;
- optional AI model;
- nested scan orchestration.

## 5. UI/UX ownership

- text input/paste area;
- chọn `contentType` hoặc auto-detect assist;
- character limit feedback;
- processing state;
- highlight/nhóm suspicious cues;
- hiển thị nested findings URL/Phone/Bank;
- degraded state nếu child scan timeout;
- shared result rendering qua M06.

## 6. API / event contracts

HTTP:

```text
POST /v1/scans/text
```

Request:

```json
{
  "contentType": "MESSAGE",
  "text": "..."
}
```

hoặc:

```json
{
  "contentType": "TRANSACTION_POST",
  "text": "..."
}
```

RabbitMQ:

```text
queue:       q.scan.text
request key: scan.text.requested
result key:  scan.text.analyzed
```

AI (tùy chọn, bất đồng bộ — xem M11):

```text
queue:       q.ai.analyze
request key: ai.analysis.requested     (worker publish, kind = TEXT_CONTENT)
result key:  ai.analysis.completed | ai.analysis.failed   (M11 publish vào q.scan.result)
```

## 7. Backend / worker responsibilities

**Backend**
- validate text size;
- create scan;
- publish text job;
- nhận `DerivedIndicator[]` và `pendingAiTasks[]`;
- tạo child scans URL/Phone/Bank;
- đăng ký AI task đang chờ;
- completion barrier trên cả child scan lẫn AI task;
- finalize partial/degraded nếu child hoặc AI task fail/timeout.

**Text Worker**

```text
analyzeText(text, contentType)
├── normalizeText()
├── detectLanguageOrEncoding()
├── extractIndicators()
│   ├── URL
│   ├── phone
│   └── bank account
├── detectSensitiveIdentityRequest()
│   └── CCCD-like cue/value → signal only
├── extractAmountAndDeadline()
├── detectUrgencyAndImpersonationCues()
├── detectPaymentCredentialRequestCues()
├── classifyScamScenarioByRules()
├── publishAiTaskIfEnabled()
└── return signals + derivedIndicators + pendingAiTasks
```

Chỉ dấu Text Worker trích ra (URL, phone, bank account) đi thẳng vào `derivedIndicators[]` với `handling` phù hợp. Worker **không tra uy tín cho chúng** — đó là việc của M12.

Giống M02: worker publish AI task rồi kết thúc job ngay, khai `taskId` vào `pendingAiTasks[]` để M12 biết còn phải chờ.

## 8. Input

```text
text
contentType = MESSAGE | TRANSACTION_POST
optional source metadata
optional parentScanId/depth
```

## 9. Output

```text
Text/Content AnalysisSignal[]
DerivedIndicator[]
pendingAiTasks[]        (taskId của AI task đã gửi, rỗng nếu không bật AI)
Scenario metadata
Final RiskResult
```

AI prediction về sau qua `q.scan.result` do M11 publish, không nằm trong result event của Text Worker.

## 10. Data ownership

Dùng shared scan storage:

```text
scan_requests
scan_results
scan_relations
scan_signals (optional)
```

## 11. Business rules / invariants

Nested scan example:

```text
TEXT parent
├── URL child
├── ENTITY:PHONE child
└── ENTITY:BANK_ACCOUNT child
```

Rules:
- `maxDepth = 2`;
- parent deadline = 30 s;
- cycle detection;
- child fail không nhất thiết fail parent;
- CCCD không trở thành standalone lookup trong MVP;
- raw CCCD không được log/persist mặc định.

## 12. Failure / degraded behavior

- child scan timeout → parent có thể `COMPLETED + degraded=true`;
- AI task timeout hoặc `ai.analysis.failed` → đóng task, renormalize risk policy, không xem AI = 0;
- AI result về sau khi scan đã `COMPLETED` → ghi nhận late signal, không sửa `RiskResult` đã trả;
- malformed text/oversized input → fail fast;
- partial indicator extraction vẫn có thể tạo result nếu đủ signal.

## 13. Security / privacy

- text input tối đa initial 20,000 ký tự;
- worker không gọi API core, không đọc PostgreSQL/Redis;
- redact/mask CCCD-like value trong log/evidence;
- tránh lưu raw sensitive content lâu hơn cần thiết nếu privacy policy yêu cầu;
- nested indicators phải được normalize trước khi route.

## 14. Dependencies

- M02 URL Scan cho nested URL;
- M04 Entity Check cho Phone/Bank;
- M09 Risk Policy;
- M11 AI optional, **chỉ qua RabbitMQ**, không gọi trực tiếp;
- M12 Shared Scan Platform.

## 15. Phát triển độc lập / mock contract

Fixtures:

```text
safe-message.json
urgent-bank-transfer.json
fake-job-post.json
refund-asks-otp.json
asks-for-cccd.json
message-with-url-phone-bank.json
```

UI render từ mock `RiskResult`; worker phát triển parser/rules theo cùng fixtures.

## 16. Definition of Done

- MESSAGE/TRANSACTION_POST dùng cùng endpoint;
- nested indicators hoạt động;
- sensitive data được mask;
- AI optional/fallback;
- Web/Mobile cùng semantics;
- E2E text → nested scans → final result.

---

# M04. Phone & Bank Reputation Check

## 1. Module summary

| Thuộc tính | Giá trị |
| --- | --- |
| Type | End-to-End Feature |
| Actor | User |
| UI | User Web, Mobile |
| API | `POST /v1/scans/entity` |
| Worker | Entity/Reputation Checker Worker |
| Entity types | `PHONE`, `BANK_ACCOUNT` |

## 2. Mục đích

Cho phép user kiểm tra các tín hiệu rủi ro/reputation liên quan đến số điện thoại hoặc tài khoản ngân hàng dựa trên Threat Intelligence và Community Reports đã xác minh.

## 3. Góc nhìn người dùng / actor journey

```text
User nhận cuộc gọi / số tài khoản đáng nghi
→ mở chức năng kiểm tra
→ chọn Phone hoặc Bank Account
→ nhập giá trị
→ bấm Scan
→ xem hệ thống có dữ liệu/report/risk signal liên quan hay không
→ nhận khuyến nghị trước khi gọi lại/chuyển tiền
```

UI phải nói rõ **“không tìm thấy dữ liệu rủi ro” không đồng nghĩa “đã xác minh an toàn”**.

## 4. Phạm vi nghiệp vụ

- PHONE reputation check;
- BANK_ACCOUNT reputation check;
- optional bank code;
- normalization theo type;
- verified community match;
- external/source reputation;
- report statistics;
- source confidence;
- time-decay/association signals.

CCCD standalone lookup không thuộc MVP.

## 5. UI/UX ownership

- entity type selector;
- Phone form;
- Bank Account form + bank code nếu cần;
- validation feedback;
- processing state;
- no-data state;
- verified match state;
- source freshness/confidence explanation;
- result integration M06.

## 6. API / event contracts

HTTP:

```text
POST /v1/scans/entity
```

Phone:

```json
{
  "entityType": "PHONE",
  "value": "0901234567"
}
```

Bank:

```json
{
  "entityType": "BANK_ACCOUNT",
  "value": "123456789",
  "bankCode": "VCB"
}
```

RabbitMQ:

```text
queue:       q.scan.entity
request key: scan.entity.requested
result key:  scan.entity.analyzed
```

## 7. Backend / worker responsibilities

**Backend**
- validate/normalize input;
- pre-enrich reputation context;
- create scan;
- dispatch entity job;
- finalize qua shared Risk Evaluation.

**Entity Worker**

```text
checkEntity(entityType, value, context)
├── validateByType()
├── normalizeByType()
├── usePreEnrichedReputationContext()
├── calculateSourceConfidence()
├── calculateReportStatistics()
├── calculateTimeDecaySignal()
├── calculateAssociationSignals()
└── return EntityReputationSignal
```

## 8. Input

```text
entityType = PHONE | BANK_ACCOUNT
value
optional bankCode
reputationContext
```

## 9. Output

```text
VerifiedReportMatch signals
SourceConfidence
ReportStatistics
TimeDecaySignal
AssociationSignals
REPUTATION_UNAVAILABLE (nếu có)
Final RiskResult
```

## 10. Data ownership

Module đọc qua M08 Threat Query thay vì query trực tiếp DB. Shared data liên quan:

```text
risk_entities
risk_sources
community_reports (verified)
scan_requests
scan_results
```

## 11. Business rules / invariants

- `NO_DATA` ≠ `SAFE_VERIFIED`;
- Entity Worker chỉ tính toán trên `reputationContext` kèm trong job; nó **không** tự tra dữ liệu uy tín;
- unverified community report không tạo hard blacklist;
- Phone/Bank dùng chung pipeline nhưng validation strategy riêng;
- source confidence/freshness phải ảnh hưởng signal;
- CCCD không expose trong `ENTITY` MVP.

## 12. Failure / degraded behavior

- Threat Intelligence unavailable → M12/M08 phát `REPUTATION_UNAVAILABLE` lúc pre-enrich; job vẫn được giao và result degraded phù hợp;
- no match → hiển thị neutral wording;
- invalid phone/account → reject trước khi queue;
- stale source → evidence phải thể hiện freshness nếu có.

## 13. Security / privacy

- không log full bank account nếu không cần;
- masking khi hiển thị/audit;
- access to report/source details theo role;
- worker không truy cập trực tiếp PostgreSQL/Redis.

## 14. Dependencies

- M07 verified Community Reports;
- M08 Threat Intelligence;
- M09 Risk Policy;
- M12 Shared Scan Platform.

## 15. Phát triển độc lập / mock contract

Fixtures:

```text
entity-no-data.json
entity-verified-report.json
entity-multiple-reports.json
entity-stale-source.json
entity-reputation-unavailable.json
```

UI có thể implement tất cả state trước khi threat database có dữ liệu thật.

## 16. Definition of Done

- Phone/Bank chung endpoint/pipeline;
- normalization tests;
- no-data wording đúng;
- source confidence/freshness thể hiện rõ;
- Web/Mobile parity;
- E2E input → reputation result.

---

# M05. QR / VietQR Scan

## 1. Module summary

| Thuộc tính | Giá trị |
| --- | --- |
| Type | End-to-End Feature |
| Actor | User |
| UI | Mobile camera/gallery, Web upload |
| API | `POST /v1/scans/qr` |
| Worker | QR Parser Worker |
| Nested dependencies | M02 URL, M03 Text, M04 Entity |

## 2. Mục đích

Nhận QR từ camera hoặc ảnh upload, decode payload, phân loại nội dung và route các indicator phát sinh sang đúng scan module để tạo đánh giá rủi ro tổng hợp.

## 3. Góc nhìn người dùng / actor journey

**Mobile**

```text
User thấy QR đáng nghi
→ mở camera scan QR hoặc chọn ảnh từ gallery
→ hệ thống đọc QR nhưng không tự mở link
→ phân tích URL/Text/VietQR bên trong
→ trả final risk result
```

**Web**

```text
User upload ảnh QR
→ hệ thống decode
→ phân tích nội dung giống Mobile
→ xem kết quả
```

## 4. Phạm vi nghiệp vụ

- camera/gallery QR input;
- Web upload;
- decode image/payload;
- detect QR type;
- URL QR;
- VietQR;
- Text QR;
- unknown/unsupported payload;
- derived indicator routing;
- child scans;
- final aggregation.

## 5. UI/UX ownership

**Mobile**
- camera scanner;
- gallery picker;
- permission UX;
- preview/confirm scan nếu cần.

**Web**
- upload image;
- preview file;
- invalid QR feedback.

**Shared**
- processing state;
- nested findings;
- final RiskResult.

## 6. API / event contracts

HTTP:

```text
POST /v1/scans/qr
```

Input có thể dùng multipart image hoặc encoded payload theo API spec.

RabbitMQ:

```text
queue:       q.scan.qr
request key: scan.qr.requested
result key:  scan.qr.parsed
```

## 7. Backend / worker responsibilities

**QR Worker**

```text
parseQr(qrInput)
├── decodeIfImageProvided()
├── validatePayloadSize()
├── detectQrType()
│   ├── URL
│   ├── VIETQR
│   ├── TEXT
│   └── UNKNOWN
├── parseVietQrFields()
├── extractDerivedIndicators()
└── return ParsedQrResult
```

**Orchestrator**

```text
QR URL  → child URL scan
VietQR  → child BANK_ACCOUNT scan + optional text/content signal
QR Text → child TEXT scan
Unknown → low-information signal
```

QR Worker không tự gọi worker khác.

## 8. Input

```text
QR image hoặc encoded payload
source = CAMERA | GALLERY | WEB_UPLOAD | NESTED
optional parentScanId/depth
```

## 9. Output

```text
ParsedQrResult
DerivedIndicator[]
QR-specific AnalysisSignal[]
Final RiskResult sau child scans
```

## 10. Data ownership

Dùng shared scan storage và object upload tạm nếu implementation cần. Không cần bảng domain riêng bắt buộc.

## 11. Business rules / invariants

- QR chứa URL không được tự động mở link;
- URL phải đi qua M02;
- Bank Account phải đi qua M04;
- Text phải đi qua M03;
- nested `maxDepth = 2`;
- completion barrier dùng M12.

## 12. Failure / degraded behavior

- invalid/undecodable QR → clear error state;
- child scan fail/timeout → có thể final degraded result;
- unsupported payload → low-information evidence thay vì crash;
- camera permission denied → cho phép gallery/upload nếu khả dụng.

## 13. Security / privacy

- validate file type/size;
- decode limit;
- không execute embedded URL/content;
- file upload scan/sanitize theo policy;
- không giữ ảnh lâu hơn cần thiết nếu không phải evidence.

## 14. Dependencies

- M02 URL Scan;
- M03 Text Analysis;
- M04 Entity Check;
- M09 Risk Policy;
- M12 Shared Scan Platform.

## 15. Phát triển độc lập / mock contract

Fixtures:

```text
qr-url.png
vietqr-bank.png
qr-text.png
qr-unknown.png
invalid-qr.png
```

Mock parser output giúp UI/mobile camera flow hoàn thiện trước worker.

## 16. Definition of Done

- Mobile camera + gallery;
- Web upload;
- QR không bypass downstream scan modules;
- invalid QR có UX rõ;
- nested deadline/depth được enforce;
- E2E QR → child scans → RiskResult.

---

# M06. Scan Result, History & Export

## 1. Module summary

| Thuộc tính | Giá trị |
| --- | --- |
| Type | End-to-End Feature |
| Actor | User, Admin theo quyền |
| UI | User Web, Mobile |
| API | Scan read APIs + export APIs |
| Worker | Report Export Worker |
| Data | `scan_requests`, `scan_results`, export metadata, MinIO/S3 |

## 2. Mục đích

Cung cấp read-side thống nhất cho mọi scan type: trạng thái xử lý, chi tiết kết quả, lịch sử và export/share. Module này giúp URL/Text/Entity/QR không tự xây result page khác nhau.

## 3. Góc nhìn người dùng / actor journey

```text
User gửi scan
→ được đưa đến trạng thái Processing
→ chờ hoặc quay lại sau
→ mở Result Detail
→ xem Score / SAFE-CAUTION-DANGER
→ xem evidence + explanation + recommendation
→ có thể xem lại trong History
→ export/share nếu cần
```

Nếu scan degraded, user phải thấy thông báo rằng một số nguồn/tác vụ không khả dụng thay vì tưởng kết quả là đầy đủ tuyệt đối.

## 4. Phạm vi nghiệp vụ

- scan status;
- result detail;
- shared risk presentation;
- evidence/explanation/recommendation;
- degraded state;
- history;
- filter/pagination;
- export PDF/HTML;
- share result theo platform.

## 5. UI/UX ownership

- processing/status page;
- shared Result Detail component;
- risk score/level visualization;
- evidence list/grouping;
- explanation/recommendation;
- degraded banner;
- history list/filter;
- export status/download;
- share action.

Scan-specific module có thể cung cấp extra evidence renderer nhưng không fork toàn result page.

## 6. API / event contracts

```text
GET  /v1/scans/{scanId}
GET  /v1/scans/history
POST /v1/scans/{scanId}/exports   (nếu expose riêng)
GET  /v1/exports/{exportId}
```

Export async event:

```text
queue: q.report.export
key:   report.export.requested
```

## 7. Backend / worker responsibilities

Backend:

```text
getScanStatus()
getScanDetail()
listScanHistory()
getScanSummary()
requestExport()
getExportStatus()
```

Export Worker:

```text
RiskResult + evidence + template
→ render PDF/HTML
→ store MinIO/S3
→ persist export metadata
```

## 8. Input

```text
scanId
userContext
filters/pagination
exportFormat
```

## 9. Output

```text
ScanSummary
RiskResult
HistoryPage
ExportJobStatus
Download/Object reference qua backend policy
```

## 10. Data ownership

```text
scan_requests (read ownership phối hợp M12)
scan_results
export metadata
MinIO/S3 exported objects
```

## 11. Business rules / invariants

- cùng một `RiskResult` contract cho mọi scan type;
- user chỉ xem scan có quyền truy cập;
- degraded phải hiển thị rõ;
- export lấy từ final persisted result, không tự recompute risk;
- result page không tự diễn giải vượt quá evidence.

## 12. Failure / degraded behavior

- scan còn PROCESSING → UI polling/status state;
- scan FAILED → clear error/retry action theo policy;
- export worker fail → scan result vẫn sử dụng bình thường;
- MinIO unavailable → export unavailable, không ảnh hưởng core scan;
- partial/degraded result vẫn xem được.

## 13. Security / privacy

- authorization theo `scanId`;
- signed/controlled download URL nếu dùng object storage;
- export phải mask sensitive fields theo policy;
- history không lộ scan của user khác.

## 14. Dependencies

- M01 Identity;
- M12 Shared Scan Platform;
- M13 Storage/DB;
- M10 Notification nếu báo export completed.

## 15. Phát triển độc lập / mock contract

Fixtures:

```text
url-danger-result.json
text-caution-result.json
entity-no-data-result.json
qr-degraded-result.json
scan-processing.json
scan-failed.json
```

UI module hoàn thiện được mà không cần chờ worker thật.

## 16. Definition of Done

- một result design dùng cho mọi scan type;
- history/filter/pagination;
- degraded state rõ ràng;
- export async;
- authorization test;
- Web/Mobile parity.

---

# M07. Community Report & Moderation

## 1. Module summary

| Thuộc tính | Giá trị |
| --- | --- |
| Type | End-to-End Feature |
| Actor | User, Moderator/Admin |
| UI | User Web/Mobile + Admin Dashboard |
| Storage | PostgreSQL + MinIO/S3 |
| Downstream | M08 Threat Intelligence, M09 Risk Policy |

## 2. Mục đích

Cho phép cộng đồng gửi báo cáo lừa đảo kèm evidence; moderator/admin review; chỉ report đủ độ tin cậy và đã xác minh mới trở thành reputation/threat signal.

## 3. Góc nhìn người dùng / actor journey

**User**

```text
Gặp URL/SĐT/STK/nội dung nghi lừa đảo
→ mở “Báo cáo”
→ chọn loại đối tượng
→ mô tả sự việc
→ đính kèm evidence
→ submit
→ nhận trạng thái report
```

**Moderator/Admin**

```text
Mở moderation queue
→ xem report + evidence
→ kiểm tra nguồn/thông tin
→ VERIFIED hoặc REJECTED
→ hệ thống ghi audit
→ verified report mới có thể ảnh hưởng reputation
```

## 4. Phạm vi nghiệp vụ

- submit report;
- report detail/status;
- evidence upload;
- moderation queue;
- review lifecycle;
- verified signal conversion;
- audit moderation action;
- optional reporter confidence.

## 5. UI/UX ownership

**User Web/Mobile**
- report form;
- indicator type;
- description;
- evidence uploader;
- submit/result/status.

**Admin**
- queue list;
- filters;
- report detail;
- evidence viewer;
- review action;
- moderation note.

## 6. API / event contracts

```text
POST /v1/reports
GET  /v1/reports/{id}
GET  /v1/admin/reports
GET  /v1/admin/reports/{id}
POST /v1/admin/reports/{id}/review
```

Có thể phát domain events:

```text
ReportSubmitted
ReportReviewed
```

## 7. Backend / worker responsibilities

Backend:
- validate report;
- persist report/evidence metadata;
- moderation lifecycle;
- verify permissions;
- convert VERIFIED → reputation/threat reference;
- publish audit/notification event.

Worker riêng không bắt buộc. File processing/virus scan có thể thêm sau nếu cần.

## 8. Input

```text
reported indicator/content
description
evidence attachment
reporterContext
admin review action
moderation note
```

## 9. Output

```text
ReportStatus
ReportDetail
VerifiedCommunitySignal
Optional RiskEntity update
Audit event
```

## 10. Data ownership

```text
community_reports
report_reviews
report evidence metadata
MinIO/S3 evidence binary
```

## 11. Business rules / invariants

```text
UNVERIFIED REPORT ≠ HARD BLACKLIST
```

- chỉ VERIFIED đủ policy/trust mới ảnh hưởng reputation;
- review phải trace được reviewer/time/reason;
- duplicate/spam report cần strategy nếu có;
- report không tự làm final scan verdict ngoài M09.

## 12. Failure / degraded behavior

- evidence upload fail → không tạo trạng thái “verified” giả;
- moderation service unavailable → report ở pending;
- reputation update fail → retry/eventual consistency, không mất review record;
- notification fail không rollback review.

## 13. Security / privacy

- evidence authorization;
- file type/size validation;
- redact sensitive data khi hiển thị/log;
- admin actions audit;
- chống spam/abuse report ở mức phù hợp.

## 14. Dependencies

- M01 Identity;
- M08 Threat Intelligence;
- M09 Risk Policy/Admin;
- M10 Notification;
- M13 Object Storage.

## 15. Phát triển độc lập / mock contract

Seed/mock reports:

```text
report-pending.json
report-under-review.json
report-verified.json
report-rejected.json
report-with-evidence.json
```

User submit UI và Admin moderation UI có thể chạy bằng mock repository trước backend thật.

## 16. Definition of Done

- user submit report được;
- admin review được;
- evidence stored/access-controlled;
- only VERIFIED affects reputation;
- review audit được;
- Web/Mobile submit parity.

---

# M08. Threat Intelligence Management

## 1. Module summary

| Thuộc tính | Giá trị |
| --- | --- |
| Type | Admin/Internal Feature |
| Actor | Admin, Internal Scan Modules |
| UI | Admin Dashboard |
| Worker | Threat Data Ingestion Worker |
| Data owner | `risk_entities`, `risk_sources`, import/sync metadata |
| Cache | Redis threat/reputation hot cache |

## 2. Mục đích

Quản lý các nguồn threat/reputation bên ngoài, ingest và chuẩn hóa dữ liệu vào hệ thống, đồng thời cung cấp lookup contract thống nhất cho URL/Entity scan mà không cho worker đọc DB trực tiếp.

## 3. Góc nhìn người dùng / actor journey

**Admin**

```text
Mở Threat Sources
→ xem nguồn nào đang bật/tắt
→ xem last sync / số record / lỗi
→ trigger sync thủ công khi cần
→ kiểm tra kết quả ingestion
```

**User cuối** không thao tác trực tiếp module này. Họ chỉ cảm nhận gián tiếp: scan URL/SĐT/STK trả reputation nhanh và cập nhật hơn.

## 4. Phạm vi nghiệp vụ

- threat source metadata;
- enable/disable source nếu scope có;
- ingestion CSV/JSON/API/feed;
- scheduled/manual ingestion;
- normalize/deduplicate;
- source confidence;
- source version/timestamp;
- PostgreSQL upsert;
- Redis refresh/invalidation;
- reputation query dùng nội bộ trong core (pre-enrich + deferred enrichment);
- ingestion status/metrics.

## 5. UI/UX ownership

Admin Dashboard:
- source list;
- source detail;
- enabled/status;
- last sync;
- record/import count;
- error summary;
- manual sync action;
- optional import/upload UI.

## 6. API / event contracts

Admin API ví dụ:

```text
GET  /v1/admin/threat-sources
GET  /v1/admin/threat-sources/{id}
POST /v1/admin/threat-sources/{id}/sync
PATCH /v1/admin/threat-sources/{id}
```

Lookup contract — **in-process, không phải HTTP endpoint**:

```text
ThreatQuery.lookup(indicator)      -> ReputationContext
ThreatQuery.lookupBatch(indicators) -> Map<indicator, ReputationContext>
```

Đây là interface Java gọi trong cùng process Spring Boot, dùng ở hai chỗ: M12 pre-enrich trước khi publish job, và M12 tra `derivedIndicators` khi consume worker result. **Không expose endpoint nào cho worker** — worker đã bị cô lập, xem `architecture_v3.1.md` mục 4.6.1.

RabbitMQ:

```text
queue: q.threat.ingest
key:   threat.ingest.requested
```

## 7. Backend / worker responsibilities

Backend:
- source config/metadata;
- enqueue ingestion;
- expose sync status;
- cache-aside ThreatQuery;
- batch lookup cho `derivedIndicators` để một result event chỉ tốn một lượt tra.

Ingestion Worker:

```text
ingestThreatSource(source)
├── fetchOrReadSource()
├── validateSourceRecord()
├── normalizeIndicator()
├── mapIndicatorType()
├── deduplicate()
├── attachSourceAndConfidence()
├── versionAndTimestamp()
├── upsertPostgreSql()
└── refreshOrInvalidateRedisCache()
```

## 8. Input

```text
CSV
JSON
external API/feed
admin upload/trigger
scheduled trigger
lookup indicator
```

## 9. Output

```text
normalized threat/reputation records
source confidence metadata
sync/import status
ReputationContext
REPUTATION_UNAVAILABLE
metrics/error metadata
```

## 10. Data ownership

```text
risk_entities
risk_sources
source sync metadata
optional source versions/import batches
```

PostgreSQL = source of truth; Redis = hot cache.

## 11. Business rules / invariants

- source phải có identity/version/freshness metadata;
- deduplicate ingestion;
- verified community data từ M07 có thể trở thành một source/reference;
- worker scan không query DB trực tiếp và không gọi ngược core;
- cache miss → PostgreSQL → cache result;
- mọi lookup phải bounded về thời gian và số lượng indicator mỗi lượt.

## 12. Failure / degraded behavior

- ingestion source unavailable → giữ dữ liệu cũ nếu policy cho phép, ghi stale/error;
- Redis unavailable → fallback PostgreSQL;
- reputation lookup timeout initial ~500 ms → trả `REPUTATION_UNAVAILABLE`, lỗi này phát sinh trong lõi chứ không ở worker;
- bad source record → skip/quarantine theo policy, không làm hỏng toàn batch.

## 13. Security / privacy

- external source credential không log;
- admin sync action audit;
- validate imported file/source;
- không ingest dữ liệu riêng tư/không hợp pháp;
- internal lookup endpoint không public.

## 14. Dependencies

- M07 verified community source;
- M12 messaging;
- M13 PostgreSQL/Redis;
- M09 admin audit.

## 15. Phát triển độc lập / mock contract

Fixtures:

```text
sample-threat-feed.csv
sample-threat-feed.json
source-sync-success.json
source-sync-failed.json
reputation-hit.json
reputation-miss.json
```

M02/M04 dùng `MockThreatQuery` nên không phải chờ ingestion thật.

## 16. Definition of Done

- import idempotent/deduplicate;
- PostgreSQL source of truth;
- Redis refresh/invalidate đúng;
- Admin thấy sync status;
- scan modules chỉ phụ thuộc lookup contract;
- failure/stale source được observability.

---

# M09. Rule, Risk Policy & Admin Operations

## 1. Module summary

| Thuộc tính | Giá trị |
| --- | --- |
| Type | Shared/Admin Feature |
| Actor | Admin, M02–M05 |
| UI | Admin Dashboard |
| Backend | Rule Engine + Risk Fusion + Result Composer |
| Data owner | `rules`, `rule_versions`, fusion policy/version, admin policy changes |

## 2. Mục đích

Cung cấp một nơi duy nhất để chuyển `AnalysisSignal[]` thành evidence, risk score/level và explanation/recommendation; đồng thời cho admin quản lý/version hóa rule và policy có kiểm soát.

## 3. Góc nhìn người dùng / actor journey

**User cuối** không cấu hình rule. Họ thấy kết quả nhất quán giữa URL/Text/Entity/QR:

```text
Signal từ scan
→ Rule + Risk Policy
→ Risk Score / Level
→ Evidence / Explanation / Recommendation
→ hiển thị trên Result Page
```

**Admin**

```text
Mở Rule/Policy Management
→ xem rule hiện hành
→ xem version/history
→ enable/disable/update theo quyền
→ publish/activate policy version
→ mọi thay đổi được audit
```

## 4. Phạm vi nghiệp vụ

- rule evaluation;
- rule version;
- rule priority/weight;
- hard rule/override;
- risk group score;
- weighted fusion;
- missing signal renormalization;
- risk thresholds;
- result composition;
- explanation/recommendation;
- Admin rule/policy management;
- audit config changes.

## 5. UI/UX ownership

Admin:
- rule list/detail;
- version/history;
- active/inactive state;
- fusion policy view/edit nếu scope có;
- rule hit statistics nếu scope có;
- publish/activate confirmation.

User-facing output owner ở M06, nhưng M09 chịu trách nhiệm semantics của evidence/explanation/recommendation.

## 6. API / event contracts

Internal port:

```text
RiskEvaluationPort.evaluate(signals, scanProfile)
```

Admin API ví dụ:

```text
GET  /v1/admin/rules
GET  /v1/admin/rules/{id}
POST /v1/admin/rules
PATCH /v1/admin/rules/{id}
POST /v1/admin/rule-versions/{id}/activate
GET  /v1/admin/fusion-policies
```

## 7. Backend / worker responsibilities

Rule Engine:

```text
evaluateRules(UnifiedSignalSet, ActiveRuleSet)
→ RuleMatch[]
```

Risk Fusion:

```text
fuseRisk(signals, ruleMatches, fusionPolicy)
→ riskScore + riskLevel + metadata
```

Result Composer:

```text
compose(fusionResult, evidence, scanMetadata)
→ RiskResult
```

Không cần worker riêng trong MVP.

## 8. Input

```text
UnifiedSignalSet
ScanProfile
ActiveRuleSet
FusionPolicy
scan/degraded metadata
```

## 9. Output

```text
RuleMatch[]
Evidence[]
RiskScore
RiskLevel
PolicyVersion
Explanation[]
Recommendation[]
RiskResult primitives
```

## 10. Data ownership

```text
rules
rule_versions
fusion policy metadata/version
admin policy changes
```

## 11. Business rules / invariants

Initial MVP weights:

| Profile | Technical | Content | Reputation/Community | AI |
| --- | ---: | ---: | ---: | ---: |
| URL | 35% | 15% | 35% | 15% |
| TEXT | 10% | 45% | 25% | 20% |
| ENTITY | 0% | 0% | 100% | 0% |
| QR | dùng child scan scores + QR-specific rules |  |  |  |

Missing group:

```text
renormalize available weights
```

không gán missing signal = 0.

Nhóm AI là nhóm **hay vắng nhất** vì nó về bất đồng bộ qua `q.ai.analyze` (xem M11). Ba trường hợp đều dẫn tới cùng một cách xử lý — renormalize ba nhóm còn lại và đánh `degraded=true`:

```text
AI tắt bằng cấu hình
ai.analysis.failed
AI task hết aiTaskDeadline
```

Do đó `fusionPolicy` không được giả định AI luôn có mặt, và điểm của một scan có AI với cùng scan không AI phải so sánh được với nhau.

Threshold initial:

```text
0-34   SAFE
35-69  CAUTION
70-100 DANGER
```

Hard override ví dụ:

```text
VERIFIED_BLACKLIST + trusted source
=> minimum DANGER
```

Mọi policy phải versioned/configurable.

## 12. Failure / degraded behavior

- AI missing → renormalize;
- reputation missing → renormalize/mark degraded theo policy;
- invalid active policy → fail safe, không silently use random default;
- config publish fail → giữ active version cũ;
- explanation không được vượt quá evidence có thật.

## 13. Security / privacy

- chỉ Admin có quyền mutation;
- mọi rule/policy change phải audit;
- activation nên có version/revision control;
- tránh arbitrary expression execution nếu rule engine hỗ trợ dynamic config.

## 14. Dependencies

- M01 RBAC;
- M07/M08 reputation/community signals;
- M11 AI signals (bất đồng bộ, có thể vắng);
- M12 Signal Aggregation;
- M13 DB.

## 15. Phát triển độc lập / mock contract

Các scan module phụ thuộc:

```text
RiskEvaluationPort
```

và có thể dùng `MockRiskEvaluationPort`. M09 dùng signal fixtures để develop/test engine độc lập.

## 16. Definition of Done

- rules/policy versioned;
- deterministic tests;
- missing signal renormalization;
- hard override explainable;
- admin changes audit;
- same input signals + same policy → same output.

---

# M10. Notification & User Follow-up

## 1. Module summary

| Thuộc tính | Giá trị |
| --- | --- |
| Type | End-to-End Feature |
| Actor | User, Admin |
| UI | Web/Mobile notification center/badge nếu scope có |
| Backend | Notification policy/job |
| Worker | Notification worker/provider adapter nếu async |
| Data | `notification_jobs`, status/preferences nếu dùng |

## 2. Mục đích

Thông báo cho user/admin khi có sự kiện cần follow-up mà không để notification logic rải rác trong từng scan/report/export module.

## 3. Góc nhìn người dùng / actor journey

```text
User submit scan/report/export
→ rời màn hình hoặc tiếp tục dùng app
→ khi tác vụ hoàn thành / report được review
→ nhận in-app notification hoặc push/email nếu bật
→ tap notification
→ deep-link tới đúng scan/report/export
```

## 4. Phạm vi nghiệp vụ

- ScanCompleted notification nếu cần;
- ExportCompleted;
- ReportReviewed;
- Admin/system alerts;
- in-app notification;
- optional email/push;
- read/unread state nếu scope có;
- deep-link target.

## 5. UI/UX ownership

Web/Mobile:
- notification badge/list;
- read/unread;
- deep-link;
- notification settings nếu scope có;
- permission UX cho push trên mobile/web nếu áp dụng.

## 6. API / event contracts

Input domain events:

```text
ScanCompleted
ExportCompleted
ReportReviewed
SystemAlert
```

API ví dụ:

```text
GET   /v1/notifications
PATCH /v1/notifications/{id}/read
GET   /v1/notification-preferences
PATCH /v1/notification-preferences
```

RabbitMQ:

```text
queue: q.notification
key:   notification.requested
```

## 7. Backend / worker responsibilities

```text
Domain Event
→ Notification Policy
→ NotificationJob
→ Provider Adapter / In-App Store
→ Delivery Status
```

Worker/provider chịu trách nhiệm gửi, retry giới hạn và ghi delivery result.

## 8. Input

```text
Domain Event
userId/admin target
related resource id
notification preferences
provider config
```

## 9. Output

```text
InAppNotification
Email/Push request
DeliveryStatus
DeepLink target
```

## 10. Data ownership

```text
notification_jobs
notification delivery status
notification preferences (nếu có)
```

## 11. Business rules / invariants

- notification failure không rollback scan/report/export;
- không gửi duplicate cho cùng event/user nếu idempotency key trùng;
- content phải dựa trên business event thật;
- deep-link phải authorize lại khi user mở.

## 12. Failure / degraded behavior

- provider down → retry bounded;
- retry exhausted → failed status/DLQ;
- push denied → in-app vẫn có thể hoạt động;
- duplicate event → ignore/idempotent.

## 13. Security / privacy

- không đưa sensitive scan detail vào push preview nếu không cần;
- deep-link vẫn yêu cầu auth;
- provider credential không log;
- notification preferences theo user.

## 14. Dependencies

- M01 Identity;
- M06 export/result;
- M07 reports;
- M12 messaging;
- M13 infrastructure.

## 15. Phát triển độc lập / mock contract

```text
ConsoleNotificationProvider
InMemoryNotificationProvider
mock-scan-completed-event.json
mock-report-reviewed-event.json
```

UI notification center dùng seeded notifications.

## 16. Definition of Done

- in-app notification flow hoạt động;
- deep-link đúng;
- duplicate protection;
- provider failure không ảnh hưởng business transaction;
- delivery status observable.

---

# M11. AI/ML Inference

## 1. Module summary

| Thuộc tính | Giá trị |
| --- | --- |
| Type | Shared Capability — **async worker**, không phải service đồng bộ |
| Actor | Không có end-user trực tiếp; M02/M03 gửi việc, M12 nhận kết quả |
| Runtime | Python worker consume RabbitMQ (`pika` / `aio-pika`); FastAPI chỉ giữ cho health/diagnostics |
| Consume | `q.ai.analyze` — routing key `ai.analysis.requested` |
| Publish | `q.scan.result` — `ai.analysis.completed` / `ai.analysis.failed` |
| Input | AI task: `scanId`, `taskId`, `kind`, features/content |
| Output | prediction signal kèm `scanId` + `taskId` |
| Business verdict | Không thuộc module này |

## 2. Mục đích

Cung cấp model/LLM inference dùng chung cho URL/Text scan. Python worker trả prediction có version/latency; final business verdict vẫn thuộc M09.

**Lý do module này là worker chứ không phải service REST:** thời gian chờ model, nhất là LLM, dài và không đoán trước được. Nếu M02/M03 gọi đồng bộ, mỗi lần AI chậm là một Web/Text Worker thread bị giữ chỗ và throughput của cả pipeline scan tụt theo. Đưa việc chờ vào một hàng đợi riêng khiến độ trễ AI chỉ ảnh hưởng tới AI, và cho phép giới hạn đồng thời của model tách khỏi giới hạn đồng thời của scan.

## 3. Góc nhìn người dùng / actor journey

User không gọi AI service trực tiếp. Dưới góc nhìn user:

```text
User scan URL/Text
→ hệ thống có thể gửi một AI task chạy song song với phần phân tích còn lại
→ user vẫn nhận một RiskResult thống nhất
→ nếu AI chậm quá aiTaskDeadline hoặc unavailable,
  scan vẫn hoàn thành bằng rule/reputation với degraded=true
```

AI không được làm UX trở thành “AI nói nguy hiểm” mà không có evidence liên quan.

## 4. Phạm vi nghiệp vụ

- consume AI task từ `q.ai.analyze`;
- model routing theo `kind`;
- model-specific preprocessing;
- model loading/version registry;
- model / LLM inference;
- probability/label/latency metadata;
- giới hạn đồng thời (`prefetch_count`), timeout và retry nội bộ;
- publish kết quả thành công **và thất bại** vào `q.scan.result`;
- health/readiness;
- optional URL/Text/Web models.

## 5. UI/UX ownership

Không có UI riêng cho user MVP.

Admin/model diagnostics UI nếu sau này có phải là scope riêng. User-facing AI evidence được render qua M06 dựa trên M09-composed result.

## 6. API / event contracts

RabbitMQ (giao diện chính — luồng scan không dùng HTTP):

```text
consume queue: q.ai.analyze
request key:   ai.analysis.requested
result keys:   ai.analysis.completed | ai.analysis.failed  → q.scan.result
DLQ:           q.ai.analyze.dlq
```

AI task nhận vào:

```json
{
  "eventId": "evt_ai_req_1",
  "taskId": "aitask_abc",
  "scanId": "scan_123",
  "kind": "URL_FEATURES",
  "attempt": 1,
  "payload": { "normalizedUrl": "https://example.com/login", "features": {} },
  "requestedBy": "url-worker-v1",
  "deadlineAt": "2026-09-19T00:00:20Z"
}
```

`kind` thuộc `URL_FEATURES | TEXT_CONTENT | WEB_CONTENT`.

AI result publish ra:

```json
{
  "eventId": "evt_ai_res_1",
  "taskId": "aitask_abc",
  "scanId": "scan_123",
  "status": "COMPLETED",
  "prediction": {
    "label": "PHISHING",
    "probability": 0.87,
    "modelVersion": "url-model-v1",
    "latencyMs": 420
  },
  "failureReason": null,
  "processorVersion": "ai-worker-v1",
  "processedAt": "2026-09-19T00:00:00Z"
}
```

Khi thất bại: `status = "FAILED"`, `prediction = null`, `failureReason` thuộc `TIMEOUT | MODEL_UNAVAILABLE | INVALID_PAYLOAD | INTERNAL_ERROR`.

HTTP chỉ còn cho vận hành, **không** nằm trong luồng scan:

```text
GET /health
GET /ready
GET /internal/models     (metadata / diagnostics)
```

## 7. Backend / worker responsibilities

Python worker:

```text
consume ai.analysis.requested
→ validate task (scanId, taskId, kind, payload size)
→ deduplicate by taskId
→ select model theo kind
→ preprocess
→ inference with timeout (+ retry nội bộ, không vượt deadlineAt)
→ postprocess
→ publish ai.analysis.completed | ai.analysis.failed
```

M12 nhận result, ghép theo `scanId` + `taskId`, đóng AI task trong barrier và biến prediction thành `AnalysisSignal` cho M09.

## 8. Input

```text
AI task envelope
├── scanId + taskId       (bắt buộc)
├── kind                  (chọn model)
├── payload               (normalized features / content)
├── attempt
└── deadlineAt
```

## 9. Output

```text
ai.analysis.completed
├── scanId + taskId
├── label
├── probability
├── modelVersion
├── latencyMs
└── optional confidence/metadata

ai.analysis.failed
├── scanId + taskId
└── failureReason
```

## 10. Data ownership

- model artifacts;
- model version metadata (canonical metadata có thể lưu PostgreSQL ở platform);
- module không sở hữu final scan result.

## 11. Business rules / invariants

- Python trả prediction, không `SAFE/CAUTION/DANGER` business verdict;
- **mỗi task nhận được sinh đúng một result**, kể cả khi fail — im lặng là lỗi thiết kế, không phải degraded hợp lệ;
- **mọi result mang `scanId` + `taskId`**; thiếu khóa này thì M12 không ghép được và kết quả vô dụng;
- tổng thời gian xử lý một task, gồm retry nội bộ, phải nhỏ hơn `deadlineAt`;
- idempotent theo `taskId`: task lặp do at-least-once không được chạy inference lần hai;
- model version luôn traceable;
- output contract deterministic về shape;
- module không query user/business tables, không đọc PostgreSQL/Redis — mọi thứ cần dùng nằm trong payload.

## 12. Failure / degraded behavior

- model/LLM call timeout initial = 10 s (đã tính retry nội bộ) → publish `ai.analysis.failed` với `reason=TIMEOUT`;
- model unavailable → `ai.analysis.failed` với `reason=MODEL_UNAVAILABLE`, M12 đóng task và finalize degraded ngay thay vì chờ hết deadline;
- malformed payload → `ai.analysis.failed` với `reason=INVALID_PAYLOAD`, không retry;
- task hết `deadlineAt` trước khi chạy → bỏ, trả `TIMEOUT`, không tốn inference;
- quá retry → `q.ai.analyze.dlq`, phải có alert: mỗi message trong DLQ là một scan bị degraded vì AI;
- AI Worker chết hẳn → mọi scan có AI finalize degraded sau `aiTaskDeadline`; hệ thống vẫn chấm điểm bằng rule + reputation.

## 13. Security / privacy

- internal-only network access; chỉ nói chuyện với RabbitMQ, không mở inference endpoint public;
- không log raw sensitive text quá mức cần thiết; không log raw CCCD / thông tin tài khoản;
- payload size limit ở cả phía publish lẫn phía consume;
- model artifact integrity/version control;
- nếu dùng LLM bên thứ ba, phải nêu rõ dữ liệu nào rời hệ thống và có cơ chế tắt được bằng cấu hình.

## 14. Dependencies

- M02 URL Scan — nguồn phát AI task;
- M03 Text Analysis — nguồn phát AI task;
- M12 Shared Scan Platform — nơi nhận result và đóng AI task;
- M09 Risk Policy — nơi prediction thành điểm rủi ro;
- M13 infrastructure/runtime (RabbitMQ).

## 15. Phát triển độc lập / mock contract

M02/M03 dùng port publish, M12 dùng port nhận result:

```text
AiTaskPublisherPort
MockAiTaskPublisherPort      (ghi lại taskId đã publish, không gọi model)
FakeAiWorker                 (đọc q.ai.analyze, trả fixture theo kind)
```

Fixtures:

```text
url-ai-phishing.json
url-ai-safe.json
text-ai-scam.json
ai-failed-timeout.json
ai-failed-model-unavailable.json
ai-result-late.json          (về sau khi scan đã COMPLETED)
ai-result-orphan.json        (taskId chưa được khai trong pendingAiTasks)
```

M02/M03 có thể phát triển hoàn toàn với `MockAiTaskPublisherPort` và AI tắt; M12 test barrier bằng `FakeAiWorker`.

## 16. Definition of Done

- consume `q.ai.analyze` và publish `q.scan.result` ổn định theo contract;
- mọi task đều sinh đúng một result, có test cho cả đường fail;
- `scanId` + `taskId` có mặt trong mọi result;
- idempotent theo `taskId`, có test message lặp;
- modelVersion traceable;
- timeout/DLQ/fallback test;
- `prefetch_count` cấu hình được và có test giới hạn đồng thời;
- không trả final business verdict;
- consumers có mock adapter.

---

# M12. Shared Scan Platform

## 1. Module summary

| Thuộc tính | Giá trị |
| --- | --- |
| Type | Shared Platform |
| Actor | Không có UI trực tiếp; phục vụ M02–M06 |
| Backend | Scan Orchestrator, Processor Registry, Result Consumer, Signal Aggregator |
| Messaging | RabbitMQ |
| Coordination | PostgreSQL + Redis |
| Ownership | scan lifecycle + nested scan coordination |

## 2. Mục đích

Cung cấp cơ chế scan lifecycle/orchestration dùng chung để URL/Text/Entity/QR không tự implement lại idempotency, queue dispatch, result consumption, nested scan, barrier và finalization.

## 3. Góc nhìn người dùng / actor journey

User không thấy module này như một màn hình riêng. Họ cảm nhận nó qua hành vi nhất quán:

```text
Bấm Scan
→ nhận scanId nhanh
→ thấy PROCESSING
→ hệ thống chạy background
→ nested scans được xử lý nếu có
→ cuối cùng luôn về COMPLETED hoặc FAILED
→ không treo PROCESSING vô hạn
```

Đây là module đảm bảo mọi loại scan có cùng lifecycle/behavior.

## 4. Phạm vi nghiệp vụ

- scan intake lifecycle;
- idempotency;
- result cache;
- ScanRequest persistence;
- processor routing;
- RabbitMQ dispatch;
- result consumption;
- event idempotency;
- signal aggregation;
- nested scan tree;
- completion barrier;
- timeout/degraded finalization;
- finalization hooks.

## 5. UI/UX ownership

Không sở hữu screen trực tiếp.

Cung cấp status semantics cho M06:

```text
PENDING
PROCESSING
COMPLETED
FAILED
COMPLETED + degraded=true
```

## 6. API / event contracts

Feature endpoints được module tương ứng sở hữu semantics:

```text
URL    -> /v1/scans/url
TEXT   -> /v1/scans/text
ENTITY -> /v1/scans/entity
QR     -> /v1/scans/qr
```

RabbitMQ:

```text
Exchange: antiscan.topic
Type: topic
DLX: antiscan.dlx
```

Queues:

```text
q.scan.url
q.scan.text
q.scan.entity
q.scan.qr
q.ai.analyze          (M02/M03 publish, M11 consume)
q.scan.result         (worker result + AI result)
q.report.export
q.notification
q.threat.ingest
```

Routing keys liên quan tới AI:

```text
ai.analysis.requested
ai.analysis.completed
ai.analysis.failed
```

`q.scan.result` nhận **hai loại message**: `scan.*.analyzed` từ worker phân tích và `ai.analysis.completed/failed` từ M11. Consumer phân biệt bằng routing key và bằng sự có mặt của `taskId`.

## 7. Backend / worker responsibilities

Core components:

```text
Input Validation / Scan Type Resolver
Processor Registry / Job Router
Scan Orchestrator
Worker Result Consumer
AI Task Registry & Result Adapter
Reputation Enricher        (pre-enrich + deferred enrichment qua M08)
Signal Aggregator
```

**Quy tắc cô lập worker:** M12 là ranh giới duy nhất giữa worker và dữ liệu. Không worker nào gọi ngược vào core; mọi dữ liệu uy tín worker cần đều do M12 nhét vào job, và mọi chỉ dấu worker phát hiện đều do M12 tra hộ.

Core flow:

```text
ValidatedScanCommand
→ create ScanRequest
→ publish worker job
→ consume result
→ deduplicate event
→ register derived indicators
→ resolve REPUTATION_ONLY indicators (tra tại chỗ qua M08, gắn signal vào scan cha)
→ register pendingAiTasks[]          (AI task worker vừa gửi)
→ match parked AI results            (AI result về trước worker result)
→ child jobs if needed          (kèm reputationContext đã enrich)
→ completion barrier (child scans + AI tasks)
→ aggregate signals
→ call M09 Risk Evaluation
→ persist/cache final result
```

Đường AI result đi riêng:

```text
consume ai.analysis.completed | ai.analysis.failed
→ validate (bắt buộc có scanId + taskId)
→ deduplicate by eventId
→ resolve AI task
   ├── task đã biết      → đóng task, giữ prediction làm AnalysisSignal
   ├── task chưa biết    → park theo scanId, chờ worker result khai pendingAiTasks
   └── scan đã finalize  → ghi late signal vào metadata, KHÔNG sửa RiskResult đã trả
→ completion barrier
→ finalize if ready
```

## 8. Input

```text
ValidatedScanCommand
WorkerResult event (kèm pendingAiTasks[])
AiResult event (ai.analysis.completed | ai.analysis.failed)
DerivedIndicator[]
timeout/deadline event (parent deadline + aiTaskDeadline)
```

## 9. Output

```text
scanId
ScanStatus
worker jobs
UnifiedSignalSet (gồm cả AI signal đã ghép theo taskId)
child scan tree
AI task state (pending / completed / failed / expired)
finalization trigger
failure/degraded metadata
```

## 10. Data ownership

```text
scan_requests
scan_relations
scan_ai_tasks          (scanId, taskId, kind, status, requestedAt)
scan lifecycle status
processed event/idempotency metadata nếu persist
```

Redis:

```text
idempotency key
result cache
nested-scan counters/barrier
AI task counters (expectedAiTasks / completedAiTasks)
parked AI results chờ ghép
lightweight lock
```

## 11. Business rules / invariants

Nested scan và AI task:

```text
PostgreSQL scan_relations  = source of truth cho parent-child
PostgreSQL scan_ai_tasks   = source of truth cho AI task đang chờ
Redis = temporary coordination, dựng lại được từ PostgreSQL

parent deadline  = 30 s
aiTaskDeadline   = 20 s          (luôn nhỏ hơn parent deadline)
maxDepth = 2
cycle detection = enabled
partial completion => degraded=true
```

Barrier đếm **cả hai loại việc đang chờ**:

```text
Finalize khi:
1) completedChildren + failedChildren == expectedChildren
   VÀ completedAiTasks == expectedAiTasks, hoặc
2) aiTaskDeadline hết -> bỏ AI task còn treo, degraded=true, renormalize trọng số, hoặc
3) parent deadline hết -> partial/degraded result
```

`expectedAiTasks` chỉ đến từ `pendingAiTasks[]` do worker khai. Worker không được khai một `taskId` mà nó chưa publish thành công.

Messaging:

```text
at-least-once delivery
consumer idempotent by eventId
attempt đầu + tối đa 2 retry
→ DLQ
```

Scan không được treo `PROCESSING` vô hạn.

## 12. Failure / degraded behavior

- child fail → parent vẫn có thể complete degraded;
- AI task fail (`ai.analysis.failed`) → đóng task ngay và finalize sớm, không phải chờ hết deadline;
- AI task hết `aiTaskDeadline` → bỏ task treo, `degraded=true`, renormalize trọng số, không coi AI = 0;
- AI result về trước worker result → park theo `scanId` rồi ghép khi worker khai `pendingAiTasks[]`;
- AI result về sau khi scan đã `COMPLETED` → ghi late signal, không sửa `RiskResult` đã trả;
- deadline hết → finalize signals hiện có;
- duplicate event → ACK/ignore, không double-count;
- worker result malformed → reject/DLQ;
- reputation lookup cho `derivedIndicators` lỗi → gắn `REPUTATION_UNAVAILABLE`, scan vẫn finalize degraded thay vì treo;
- Redis mất → business relation vẫn phục hồi từ PostgreSQL;
- RabbitMQ unavailable → API trả error/retry policy rõ ràng, không giả accepted nếu job chưa publish an toàn.

## 13. Security / privacy

- sensitive fields không đưa vào message nếu không cần;
- event schema versioned;
- queue/internal endpoints không public;
- correlation/event/job IDs để audit/trace;
- no raw CCCD in message.

## 14. Dependencies

- M09 Risk Evaluation;
- M11 AI/ML — M12 là nơi nhận AI result và đóng AI task;
- M13 PostgreSQL/Redis/RabbitMQ;
- các feature worker M02–M05.

## 15. Phát triển độc lập / mock contract

Shared test kit:

```text
FakeMessageBus
InMemoryScanRepository
FakeClock
MockRiskEvaluationPort
FakeAiWorker
worker-result fixtures
nested-scan fixtures
ai-task/ai-result fixtures (completed, failed, late, orphan)
```

Các feature module có thể dev với fake scan platform adapter hoặc contract test.

## 16. Definition of Done

- consistent lifecycle cho 4 scan types;
- idempotent consumer theo `eventId`;
- retry/DLQ;
- nested scan depth/deadline/cycle tests;
- **AI barrier tests:** AI về bình thường, AI fail, AI quá hạn, AI về trước worker result, AI về sau khi scan đã đóng;
- **isolation test:** worker chạy được khi bị chặn mọi đường mạng trừ RabbitMQ;
- `REPUTATION_ONLY` được tra tại chỗ, không sinh scan con thừa;
- no infinite PROCESSING kể cả khi M11 chết hẳn;
- contract test với feature modules.

---

# M13. Platform Infrastructure

## 1. Module summary

| Thuộc tính | Giá trị |
| --- | --- |
| Type | Infrastructure |
| Actor | Không có direct user; toàn hệ thống phụ thuộc |
| Components | Nginx, PostgreSQL, Redis, RabbitMQ, MinIO/S3, deployment/CI |
| Goal | Cung cấp runtime ổn định, bảo mật và local dev reproducible |

## 2. Mục đích

Cung cấp nền tảng runtime chung để các module chạy được nhất quán từ local development đến môi trường deploy, đồng thời phân định rõ gateway, persistent storage, cache, message broker và object storage.

## 3. Góc nhìn người dùng / actor journey

User không thao tác trực tiếp module này. Họ cảm nhận qua:

```text
HTTPS hoạt động ổn định
request không bị lộ internal services
scan nhanh nhờ cache/async worker
file/evidence/export tải được
hệ thống không mất dữ liệu chính khi Redis restart
```

Developer cảm nhận qua:

```text
docker compose up
→ có đầy đủ DB/Redis/RabbitMQ/MinIO/Nginx
→ chạy feature module độc lập
```

## 4. Phạm vi nghiệp vụ

- Nginx edge gateway;
- PostgreSQL source of truth;
- Redis cache/coordination;
- RabbitMQ messaging;
- MinIO/S3 object storage;
- Docker Compose local/dev;
- CI/CD;
- deployment config;
- environment/secrets management;
- basic observability hooks.

## 5. UI/UX ownership

Không có product UI trực tiếp.

Admin operational dashboard/metrics nếu triển khai sau là scope observability, không phải business UI mặc định.

## 6. API / event contracts

Infrastructure không sở hữu business API.

Network contracts:

```text
Public: 443 -> Nginx
Internal: Spring Boot / Workers / AI Worker / RabbitMQ / DB / Redis / MinIO

Mọi worker (scan workers, AI Worker, export, ingestion) chỉ cần route tới
RabbitMQ. Không worker nào kết nối PostgreSQL/Redis hay gọi API Spring Boot.
Ngoại lệ duy nhất: URL Scanner Worker cần ra Internet công cộng để fetch
nội dung, và đó chính là lý do SSRF protection là biên phòng thủ chính.

Network policy phải kiểm chứng được quy tắc này; một worker xin mở thêm
kết nối tới core hay database là dấu hiệu thiết kế đã lệch.
```

Nginx forward tới approved upstreams; DB/Redis/RabbitMQ không public Internet.

## 7. Backend / worker responsibilities

**Nginx**
- TLS termination;
- reverse proxy;
- routing;
- coarse IP/flood rate limit;
- request size guard.

**PostgreSQL**
- source of truth.

**Redis**
- cache/coordination only.

**RabbitMQ**
- async job/result/event transport;
- mang cả scan job, AI task và result event;
- DLQ cho từng queue, riêng `q.ai.analyze.dlq` cần alert vì mỗi message là một scan bị degraded.

**MinIO/S3**
- evidence/export/binary artifact.

## 8. Input

```text
application network traffic
SQL operations
cache operations
RabbitMQ messages
object upload/download
CI/CD artifacts/config
```

## 9. Output

```text
reliable network routing
persistent data
cache/coordination state
message delivery
object references
runtime/deployment environment
```

## 10. Data ownership

PostgreSQL source-of-truth groups:

```text
users/auth
scan state/result
rules/policies
reports
threat reference data
audit/notification metadata
```

Redis:

```text
result cache
threat hot cache
business quota
idempotency
locks
nested scan barrier
```

MinIO/S3:

```text
community evidence
screenshots nếu có
generated PDF/HTML
binary artifacts
```

## 11. Business rules / invariants

- PostgreSQL là source of truth;
- Redis không phải source of truth;
- Nginx không xử lý business auth/risk;
- Nginx rate limit theo IP/flood;
- Spring Boot + Redis quota theo user/account;
- internal services không expose public nếu không cần;
- production config khác local nhưng contract giữ ổn định.

## 12. Failure / degraded behavior

- Redis down → degrade cache/coordination, không mất canonical data;
- MinIO down → report/export/evidence feature degrade, core scan vẫn chạy nếu không cần artifact;
- RabbitMQ down → async scan dispatch bị ảnh hưởng rõ ràng;
- PostgreSQL down → core business không thể đảm bảo correctness, fail fast/health not ready;
- Nginx upstream unavailable → gateway error hợp lý.

## 13. Security / privacy

- TLS;
- secrets không commit repo;
- private network segmentation;
- least privilege DB/broker credentials;
- object storage access policy;
- backups;
- log masking;
- SSRF worker network isolation nếu có thể.

## 14. Dependencies

Tất cả module phụ thuộc M13 ở mức runtime.

## 15. Phát triển độc lập / mock contract

Local stack:

```text
Docker Compose
├── PostgreSQL
├── Redis
├── RabbitMQ
├── MinIO
├── Spring Boot
├── workers
├── Python AI worker
├── Next.js
└── Nginx
```

Các module có thể dùng Testcontainers/fake adapters khi không muốn bật toàn stack.

## 16. Definition of Done

- local environment reproducible;
- only intended public ports exposed;
- health/readiness checks;
- persistent volumes/backups theo môi trường;
- CI build/test image flow;
- secrets/config management rõ ràng;
- infra failure behavior được document/test ở mức phù hợp.

---

# Shared Contracts

## Scan types

```text
URL
TEXT
ENTITY
QR

TEXT.contentType = MESSAGE | TRANSACTION_POST
ENTITY.entityType = PHONE | BANK_ACCOUNT
URL internal mode = WEB_CONTENT / HTML_FORM
```

`CCCD` không phải standalone lookup trong MVP. Text/Web chỉ phát hiện yêu cầu/thu thập CCCD như sensitive-data signal.

## Scan lifecycle

```text
PENDING -> PROCESSING -> COMPLETED
                     \-> FAILED

COMPLETED + degraded=true
```

`degraded=true` dùng cho mọi trường hợp finalize khi còn thiếu tín hiệu: child scan fail/timeout, AI task fail, hoặc AI task hết `aiTaskDeadline`. Thiếu AI **không** được tính là AI = 0; M09 renormalize trọng số trên các nhóm signal còn lại.

## AnalysisSignal

```json
{
  "code": "VERIFIED_REPORT_MATCH",
  "category": "REPUTATION",
  "severity": "HIGH",
  "value": 1,
  "source": "COMMUNITY_VERIFIED",
  "confidence": 0.95,
  "metadata": {}
}
```

## DerivedIndicator

```json
{
  "type": "URL",
  "normalizedValue": "https://example.com",
  "sourceScanId": "scan_parent",
  "depth": 1,
  "handling": "CHILD_SCAN"
}
```

`type = URL | PHONE | BANK_ACCOUNT | TEXT | DOMAIN`

`DOMAIN` chỉ sinh từ nội bộ (host mới trong chuỗi redirect), không phải `entityType` hợp lệ của `POST /v1/scans/entity`.

`handling` nói chỉ dấu này cần gì — worker **gợi ý**, M12 quyết định cuối:

```text
REPUTATION_ONLY   M12 tra uy tín tại chỗ khi consume result,
                  gắn signal vào scan cha, KHÔNG tạo scan con.

CHILD_SCAN        M12 tra uy tín, tạo scan con kèm reputationContext,
                  publish job như mọi scan con khác.
```

M12 được phép nâng `REPUTATION_ONLY` thành `CHILD_SCAN`, hoặc hạ `CHILD_SCAN` xuống `REPUTATION_ONLY` khi đã chạm `maxDepth`.

**Worker không bao giờ tự tra uy tín cho chỉ dấu nó phát hiện.** Đây là hệ quả trực tiếp của quyết định cô lập worker.

## RiskResult

```json
{
  "scanId": "scan_123",
  "status": "COMPLETED",
  "riskScore": 82,
  "riskLevel": "DANGER",
  "degraded": false,
  "evidence": [],
  "explanations": [],
  "recommendations": [],
  "policyVersion": "fusion-v1",
  "completedAt": "..."
}
```

## Async event envelope

Worker result (`scan.*.analyzed`, vào `q.scan.result`):

```json
{
  "eventId": "evt_123",
  "jobId": "job_123",
  "attempt": 1,
  "scanId": "scan_123",
  "parentScanId": null,
  "scanType": "URL",
  "status": "ANALYZED",
  "signals": [],
  "derivedIndicators": [],
  "pendingAiTasks": [],
  "processorVersion": "worker-v1",
  "processedAt": "..."
}
```

`pendingAiTasks[]` thay cho `modelPrediction` của bản trước. Worker không còn cầm prediction lúc trả result, nên nó khai những AI task vừa gửi đi để M12 biết phải chờ thêm:

```json
"pendingAiTasks": [
  { "taskId": "aitask_abc", "kind": "URL_FEATURES" }
]
```

Worker không dùng AI thì để mảng rỗng. Chỉ khai `taskId` **sau khi publish AI task thành công**.

## AI task

M02/M03 publish, routing key `ai.analysis.requested`, vào `q.ai.analyze`; M11 consume.

```json
{
  "eventId": "evt_ai_req_1",
  "taskId": "aitask_abc",
  "scanId": "scan_123",
  "parentScanId": null,
  "kind": "URL_FEATURES",
  "attempt": 1,
  "payload": {},
  "requestedBy": "url-worker-v1",
  "requestedAt": "...",
  "deadlineAt": "..."
}
```

`kind = URL_FEATURES | TEXT_CONTENT | WEB_CONTENT`

## AI result

M11 publish, routing key `ai.analysis.completed` hoặc `ai.analysis.failed`, vào `q.scan.result`; M12 consume.

```json
{
  "eventId": "evt_ai_res_1",
  "taskId": "aitask_abc",
  "scanId": "scan_123",
  "status": "COMPLETED",
  "prediction": {
    "label": "PHISHING",
    "probability": 0.87,
    "modelVersion": "url-model-v1",
    "latencyMs": 420
  },
  "failureReason": null,
  "processorVersion": "ai-worker-v1",
  "processedAt": "..."
}
```

Khi thất bại: `status = "FAILED"`, `prediction = null`, `failureReason = TIMEOUT | MODEL_UNAVAILABLE | INVALID_PAYLOAD | INTERNAL_ERROR`.

**Bốn quy tắc bắt buộc:**

| Quy tắc | Lý do |
| --- | --- |
| `scanId` + `taskId` có ở **cả task lẫn result** | Khóa duy nhất để M12 ghép kết quả và đóng đúng barrier |
| Mỗi task sinh **đúng một** result, kể cả khi fail | Không có result thì scan chỉ thoát treo nhờ deadline, làm chậm mọi scan có AI |
| AI result **không** chứa `riskScore`, `riskLevel` hay verdict | Business verdict thuộc M09 |
| Hai phía idempotent theo `eventId` và `taskId` | Delivery là at-least-once |

## Operational defaults của MVP

| Hạng mục | Default ban đầu |
| --- | ---: |
| Parent/nested scan deadline | 30 s |
| Max nested depth | 2 |
| Reputation lookup timeout (trong lõi) | 500 ms |
| AI task deadline (M12 chờ) | 20 s, luôn nhỏ hơn parent deadline |
| AI model/LLM call timeout (trong M11) | 10 s, đã tính retry nội bộ |
| AI Worker concurrency (`prefetch_count`) | 4 |
| Worker retry | 2 retry sau attempt đầu |
| URL redirect limit | 5 |
| URL fetched body | 5 MB |
| Text input | 20,000 ký tự |
| Result cache TTL | 10 phút |
| Threat cache TTL | theo source; mặc định 1 giờ |

> Các giá trị trên là engineering defaults của MVP, chưa phải SLA cuối cùng.
