# Anti-Scam Platform — End-to-End Module Specification

> **Architecture alignment:** V3.2
> Mỗi **Mxx** là một module có owner riêng. Owner chịu trách nhiệm xuyên suốt từ UI liên quan → API/event contract → backend use case → worker (nếu có) → data/storage → test. Các module dùng chung contract nhưng không chia ownership theo kiểu “frontend team / backend team”.

## Module Catalog

| ID            | Module                               | Type                            | Actor chính                      |
| ------------- | ------------------------------------ | ------------------------------- | --------------------------------- |
| **M01** | Account & Identity                   | End-to-End Feature              | Guest, User, Admin                |
| **M02** | URL & Website Risk Scan              | End-to-End Feature              | Guest, User                       |
| **M03** | Text & Transaction Scam Analysis     | End-to-End Feature              | Guest, User                       |
| **M04** | Phone & Bank Reputation Check        | End-to-End Feature              | Guest, User                       |
| **M05** | QR / VietQR Scan                     | End-to-End Feature              | Guest, User                       |
| **M06** | Scan Result, History & Export        | End-to-End Feature              | Guest, User                       |
| **M07** | Community Report & Moderation        | End-to-End Feature              | User, Moderator/Admin             |
| **M08** | Threat Intelligence Management       | Admin/Internal Feature          | Admin, Spring Boot scan core      |
| **M09** | Rule, Risk Policy & Admin Operations | Shared/Admin Feature            | Admin, All Scan Modules           |
| **M10** | Notification & User Follow-up        | End-to-End Feature              | User, Admin                       |
| **M11** | AI/ML Inference                      | Shared Capability               | M02, M03                          |
| **M12** | Shared Scan Platform                 | Shared Platform                 | M02–M06, phối hợp M08–M11/M14 |
| **M13** | Platform Infrastructure              | Infrastructure                  | Toàn hệ thống                  |
| **M14** | Audit & Observability                | Shared Platform / Admin Feature | Admin, Internal Operations        |

### Release scope và hướng mở rộng tài khoản

**MVP / bản release đầu tiên:**

- người dùng **được phép scan ở chế độ Guest**, không bắt buộc tạo tài khoản trước khi kiểm tra URL/Text/Entity/QR;
- tài khoản Free được dùng để sở hữu **persistent scan history**, profile/session, quota theo account và các tính năng follow-up cần danh tính;
- Guest chỉ có access context tạm thời và quota thấp hơn; không có persistent account history;
- hệ thống **chưa triển khai thanh toán, subscription, ví credit, nạp credit hay user-facing API key** trong MVP;
- AI của MVP dùng một logical `inferenceProfile` mặc định do core resolve; M11 vẫn provider-agnostic và có thể gọi OpenAI/Gemini/OpenRouter/... hoặc local model theo config.

**Future Scope / thiết kế mở rộng:**

- plan/entitlement, usage metering, credit ledger/wallet, subscription, user API key và paid inference profile được xem là extension point đã dự trù;
- các khái niệm thương mại **không được làm điều kiện bắt buộc hoặc business dependency của MVP**;
- schema/API nội bộ phải giữ boundary để sau này thêm billing/credit mà không sửa scan contract, worker contract hay Risk Fusion;
- không lưu `credit_balance` trực tiếp trong `users`; khi triển khai thật nên dùng ledger/account riêng để trace giao dịch và tránh coupling Identity với Billing.

---

# M01. Account & Identity

## 1. Module summary

| Thuộc tính       | Giá trị                                                                                    |
| ------------------ | -------------------------------------------------------------------------------------------- |
| Type               | End-to-End Feature                                                                           |
| Actor              | Guest, User, Admin;`API_CLIENT` là Future Scope                                           |
| UI                 | User Web, Mobile, Admin Dashboard                                                            |
| Backend owner      | Auth, User & Access Context domain                                                           |
| Data owner         | `users`, `roles`, `user_roles`, session/refresh metadata                               |
| MVP identity modes | `GUEST`, `AUTHENTICATED_USER`, `ADMIN`                                                 |
| Future extension   | plan/entitlement, user API identity, subscription/credit/usage —**không thuộc MVP** |
| Phụ thuộc chính | PostgreSQL, Redis (session/revocation/quota), M14 Audit & Observability                      |

## 2. Mục đích

Cung cấp identity, session/token, RBAC và **AccessContext** thống nhất cho User Web, Mobile và Admin. M01 không được biến authentication thành điều kiện bắt buộc để scan: Guest vẫn có thể dùng các scan endpoint được policy cho phép. Tài khoản tồn tại để cung cấp persistent identity cho history, profile, quota theo account và các tính năng follow-up.

M01 đồng thời cung cấp extension seam cho entitlement/plan trong tương lai, nhưng **không sở hữu billing/credit business logic trong MVP**. Các module khác chỉ nhận `AccessContext` / `EntitlementContext` đã được resolve, không tự verify credential, tự suy ra plan hay tự đọc billing state.

## 3. Góc nhìn người dùng / actor journey

**Guest — MVP**

```text
Mở ứng dụng
→ scan URL/Text/Phone/Bank/QR ngay, không cần đăng ký
→ nhận scanId + xem kết quả trong guest access scope
→ bị áp guest quota/rate limit
→ không có persistent account history
→ có thể đăng ký/đăng nhập nếu muốn lưu và quản lý history lâu dài
```

**Authenticated User — MVP**

```text
Đăng ký hoặc đăng nhập
→ thực hiện scan
→ scan gắn với userId
→ xem persistent History
→ xem/chỉnh profile
→ nhận quota theo account
→ logout khi cần
```

**Admin**

```text
Đăng nhập Admin Dashboard
→ hệ thống xác minh role/permission
→ chỉ hiển thị và cho phép các chức năng quản trị được cấp quyền
```

**Future Scope — không thuộc release đầu tiên**

```text
Authenticated User / API Client
→ resolve plan/entitlement
→ có thể có quota cao hơn / inference profile khác
→ usage được meter
→ subscription/credit/API key được quản lý bởi capability/module thương mại riêng
```

## 4. Phạm vi nghiệp vụ

**MVP:**

- Guest access context cho các scan endpoint được phép;
- register/login/logout;
- refresh/session lifecycle;
- xem/chỉnh profile;
- xác định `AccessContext` / `AuthenticatedPrincipal`;
- RBAC cho User/Admin/Moderator;
- resolve quota/feature profile ở mức Free/Guest theo policy;
- route guard ở client và authorization thật ở backend;
- revoke/expire session khi cần.

**Future Scope — chỉ thiết kế extension point, chưa implement ở MVP:**

- plan/subscription;
- paid entitlement;
- credit wallet/ledger và nạp credit;
- user-facing API key / API client identity;
- usage metering cho billing;
- paid/premium inference profile.

## 5. UI/UX ownership

**Guest Web/Mobile**

- scan form phải dùng được trước khi login;
- CTA đăng ký/đăng nhập là optional enhancement để lưu history, không chặn scan;
- khi guest mở tính năng cần account như persistent History, UI mới yêu cầu login/register.

**Authenticated Web/Mobile**

- Login/Register;
- Profile;
- Logout;
- persistent History entry point;
- authenticated route guard cho account-only features;
- hiển thị lỗi 401/403 hợp lý.

**Admin Dashboard**

- admin login;
- role-based navigation;
- unauthorized page/state.

Không có UI billing/credit/subscription trong MVP.

## 6. API / event contracts

```text
POST  /v1/auth/register
POST  /v1/auth/login
POST  /v1/auth/refresh
POST  /v1/auth/logout
GET   /v1/users/me
PATCH /v1/users/me
```

Scan endpoint không nằm trong M01 và có thể nhận `GUEST` access context nếu policy cho phép. Không cần tạo `/guest/login`; guest identity nên là context tạm thời do edge/application resolve, ví dụ opaque guest session/token ngắn hạn.

Internal contract khuyến nghị:

```text
AccessContext
- actorType = GUEST | AUTHENTICATED_USER | ADMIN | API_CLIENT(future)
- subjectId = guestSessionId | userId | apiClientId(future)
- roles[]
- authenticated = true/false

EntitlementContext
- planKey = guest | free | <future extensible key>
- quotaProfile
- inferenceProfile
- featureFlags[]
- historyPolicy
```

`planKey`, `quotaProfile`, `inferenceProfile` là extensible config keys; không nên hard-code billing provider hoặc AI provider vào contract này.

Không có RabbitMQ event bắt buộc cho core auth flow. Security-sensitive admin/user actions có thể phát audit event.

## 7. Backend / worker responsibilities

**Backend**

- validate credentials;
- hash/verify password;
- issue/refresh/revoke token/session;
- tạo `GuestAccessContext` không cần account;
- resolve `AccessContext`;
- resolve `EntitlementContext` từ MVP policy (`guest`/`free`) hoặc future entitlement provider;
- load profile;
- enforce RBAC;
- propagate resolved access/entitlement context cho M12 và account-aware modules.

**Worker**

- không có worker riêng;
- scanner/AI worker không nhận raw token, password, plan/payment data hoặc credit balance.

## 8. Input

```text
optional credentials
access/refresh token hoặc session
guest session/access token nếu có
profile update command
request cần authorization
```

Future extension input có thể có API credential hoặc entitlement state, nhưng không thuộc MVP.

## 9. Output

```text
AuthResult
UserProfile
AccessContext
AuthenticatedPrincipal (khi authenticated)
EntitlementContext
Roles / Permissions
401 / 403 standardized errors
```

## 10. Data ownership

**MVP canonical data:**

```text
users
roles
user_roles
sessions / refresh token metadata (nếu dùng)
```

Guest identity là ephemeral; không tạo `users` record chỉ để cho phép một lần scan. Nếu cần guest ownership cho result, dùng opaque `guestSessionId`/short-lived access token và TTL/policy phù hợp.

**Future schema blueprint — chưa phải migration bắt buộc của MVP:**

```text
plans / entitlement_profiles
account_plan_assignments hoặc subscriptions
usage_records / usage_ledger
credit_accounts
credit_ledger / credit_transactions
api_credentials / user_api_keys
```

Các bảng future nên nằm trong capability/module thương mại riêng khi được kích hoạt. Không thêm `credit_balance` hoặc provider-specific field trực tiếp vào `users`; M01 chỉ giữ identity và reference cần thiết.

## 11. Business rules / invariants

- **Guest scan là hợp lệ trong MVP** đối với các public scan endpoint được policy cho phép;
- account không được là điều kiện bắt buộc chỉ để scan;
- persistent History gắn với authenticated `userId`; guest không có persistent account history;
- guest result access phải dùng opaque ownership/access proof, không coi biết `scanId` là đủ quyền;
- UI guard không thay thế backend authorization;
- token hết hạn phải có behavior thống nhất giữa Web/Mobile;
- Admin không mặc định có toàn quyền nếu hệ thống dùng granular permissions;
- các module khác không tự đọc credential hay tự verify token;
- entitlement được resolve ở core trước khi dispatch; worker không tự suy ra plan/quota/credit;
- MVP chỉ có guest/free policy; paid plan, credit, payment, subscription và user API key là Future Scope;
- future credit phải dùng ledger/audit-friendly model, không mutate một balance field thiếu transaction history.

## 12. Failure / degraded behavior

- guest access context lỗi → reject request rõ ràng hoặc cấp context mới theo policy; không tự tạo account;
- sai credential → fail fast;
- token hết hạn → refresh hoặc yêu cầu login lại theo contract;
- Redis session/quota cache unavailable → nếu kiến trúc cho phép, fallback theo source of truth/policy thay vì phá toàn bộ hệ thống;
- entitlement resolver future unavailable → không tự nâng quyền/premium; fallback về policy an toàn hoặc fail theo use case;
- không retry login/payment-like mutation một cách mù quáng.

## 13. Security / privacy

- password dùng hash phù hợp;
- không log raw password/token;
- guest session ID phải random/opaque, không dùng fingerprint ổn định làm identity chính;
- refresh token/session metadata phải có revoke/expiry;
- profile field nhạy cảm phải mask/authorize;
- rate limit login và guest scan phù hợp;
- future API key phải lưu hash/secret metadata phù hợp, không log plaintext;
- payment/credit credential nếu có sau này không thuộc M01 và không được đưa vào scan/AI message.

## 14. Dependencies

- M12 Shared Scan Platform nhận `AccessContext`/`EntitlementContext`;
- M13 Platform Infrastructure cho session/quota store;
- M14 Audit & Observability cho security-sensitive actions;
- Future billing/usage capability chỉ kết nối qua entitlement/usage port, không coupling trực tiếp với scanner/AI worker.

## 15. Phát triển độc lập / mock contract

Owner tạo trước:

```text
AccessContextResolver
EntitlementResolver
AuthApi
MockAuthApi
FakeGuestAccessContext
FakeFreeEntitlementContext
mock-user.json
mock-admin.json
mock-guest.json
mock-expired-session.json
```

Frontend có thể hoàn thiện guest scan, login/profile/history gate trước khi backend auth xong. M12 có thể test với fake `guest`/`free` entitlement; không cần billing implementation.

## 16. Definition of Done

- Guest có thể scan các public scan type mà không cần register/login;
- Web + Mobile dùng cùng auth/access contract;
- authenticated user có persistent History theo `userId`;
- guest không nhìn thấy history của account khác và không truy cập result chỉ bằng cách đoán `scanId`;
- Admin RBAC chạy đúng;
- integration test guest/authenticated access + login/refresh/logout;
- `AccessContext` + `EntitlementContext` có mock/contract test;
- MVP không có dependency bắt buộc vào billing/credit/subscription/user API key;
- không có credential/token trong log;
- mock adapter tồn tại để client/module dev độc lập.

---

# M02. URL & Website Risk Scan

## 1. Module summary

| Thuộc tính        | Giá trị                                                                               |
| ------------------- | --------------------------------------------------------------------------------------- |
| Type                | End-to-End Feature                                                                      |
| Actor               | Guest, User                                                                             |
| UI                  | User Web, Mobile                                                                        |
| API                 | `POST /v1/scans/url`                                                                  |
| Worker              | Web/URL Scanner Worker                                                                  |
| Shared dependencies | M08 Threat Intelligence, M09 Risk Policy, M11 AI, M12 Scan Platform, M13 Infrastructure |

## 2. Mục đích

Cho phép user nhập hoặc share một URL đáng nghi và nhận đánh giá rủi ro dựa trên URL/domain, DNS, TLS, redirect, HTML/Form, reputation, rule và AI nếu được bật. Worker URL chỉ phân tích và phát tín hiệu; **Spring Boot mới tổng hợp và tạo final `RiskResult`**.

## 3. Góc nhìn người dùng / actor journey

**Web**

```text
Paste URL
→ bấm “Kiểm tra”
→ nhận scanId và thấy trạng thái đang phân tích
→ hệ thống kiểm tra URL/domain/redirect/HTML/Form
→ có thể chạy AI ở nền như một tín hiệu bổ sung
→ nhận điểm rủi ro + mức cảnh báo
→ xem bằng chứng và khuyến nghị
```

**Mobile**

```text
Paste URL hoặc Share URL từ browser/Zalo/Facebook/app khác
→ mở Anti-Scam
→ xác nhận scan
→ xem kết quả giống Web
```

User không cần biết worker/DNS/TLS/AI chạy theo tiến trình nào; họ chỉ cần hiểu **vì sao URL bị đánh giá rủi ro** và khi nào kết quả bị `degraded`.

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
- sử dụng `reputationContext` đã được core pre-enrich;
- phát hiện host/domain mới và trả về `DerivedIndicator[]`;
- optional AI task bất đồng bộ qua RabbitMQ;
- result integration qua M09/M12.

`HTML/Form` là internal mode của URL scan, không phải public feature riêng.

## 5. UI/UX ownership

- URL input form;
- paste/share flow;
- validation feedback;
- processing state;
- URL-specific evidence groups;
- degraded state nếu AI/reputation/child scan không khả dụng;
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
scan job queue: q.scan.url
request key:    scan.url.requested
result key:     scan.url.analyzed

AI task queue:  q.ai.analyze
AI request key: ai.analysis.requested
AI result keys: ai.analysis.completed | ai.analysis.failed
```

## 7. Backend / worker responsibilities

**Backend / M12 + M08**

```text
URL request
→ validate
→ idempotency/cache check
→ create ScanRequest
→ pre-enrich known reputation context bằng ThreatQuery
→ publish scan.url.requested
→ consume worker result
→ xử lý DerivedIndicator theo handling
   ├── REPUTATION_ONLY → core tra Redis/PostgreSQL và gắn signal
   └── CHILD_SCAN      → core enrich + tạo scan con nếu policy cho phép
→ register pendingAiTasks[]
→ chờ completion barrier của child scan + AI task
→ M09 Rule Engine + Risk Fusion + Result Composer
→ persist/cache final RiskResult
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
├── publishAiTaskIfEnabled()
└── return signals + derivedIndicators + pendingAiTasks
```

Worker **không gọi API của Spring Boot, không đọc PostgreSQL/Redis và không chờ AI trả kết quả**. Bề mặt giao tiếp với hệ thống nội bộ là RabbitMQ.

## 8. Input

```text
url
source = MANUAL | MOBILE_SHARE | NESTED_SCAN
optional parentScanId/depth
optional reputationContext
```

## 9. Output

Worker result:

```text
Technical AnalysisSignal[]
Web-content AnalysisSignal[]
DerivedIndicator[]
  - handling = REPUTATION_ONLY | CHILD_SCAN
pendingAiTasks[]
processor metadata
```

AI prediction **không** nằm trực tiếp trong worker result. Nếu AI được bật, nó về độc lập qua `ai.analysis.completed` / `ai.analysis.failed`.

Final output cho user:

```text
RiskResult
```

## 10. Data ownership

Module không trực tiếp sở hữu business state. Scan state/result do M12/M06 sử dụng chung:

```text
scan_requests
scan_results
scan_signals (optional)
scan_relations (nếu phát sinh child scan)
scan_ai_tasks (nếu phát sinh AI task)
```

Worker không ghi các bảng trên.

## 11. Business rules / invariants

- Worker chỉ tạo signal/indicator, không tự quyết định final verdict;
- known URL/domain reputation được core pre-enrich trước khi publish job;
- redirect/domain mới phát sinh được trả về dưới dạng `DerivedIndicator`, worker **không tự tra**;
- `REPUTATION_ONLY` được core tra ngay khi consume result; `CHILD_SCAN` mới tạo scan con;
- AI là optional signal và được xử lý bất đồng bộ;
- worker chỉ khai `taskId` trong `pendingAiTasks[]` sau khi publish AI task thành công;
- HTML/Form chỉ là bước phân tích nội bộ;
- output phải explainable bằng evidence thật có.

## 12. Failure / degraded behavior

- AI task fail hoặc quá `aiTaskDeadline` → scan có thể hoàn thành `degraded=true`, Risk Fusion renormalize thay vì coi AI = 0;
- core không đọc được reputation → core sinh `REPUTATION_UNAVAILABLE`; worker không sinh signal này;
- fetch website lỗi → vẫn dùng lexical/DNS/TLS/reputation signals còn lại;
- worker fail sau retry → M12 xử lý fail/degraded theo completion policy;
- AI result về sau khi scan đã finalize → ghi late signal/audit, không sửa `RiskResult` đã trả.

## 13. Security / privacy

SSRF protection bắt buộc:

- chỉ `http/https`;
- chặn private/loopback/link-local/metadata ranges;
- re-check destination sau redirect;
- redirect limit ban đầu = 5;
- response body limit = 5 MB;
- connect/read timeout;
- không forward internal credential/header;
- worker không truy cập trực tiếp PostgreSQL/Redis/Spring Boot API;
- ngoài RabbitMQ, URL Worker chỉ được outbound tới public content cần fetch theo SSRF policy.

## 14. Dependencies

- M01 Identity / Access / Entitlement Context;
- M08 Threat Intelligence;
- M09 Rule/Risk Policy;
- M11 AI/ML optional;
- M12 Shared Scan Platform;
- M13 Infrastructure;
- M14 Audit & Observability cho trace/failure metadata.

## 15. Phát triển độc lập / mock contract

Fixtures/contracts:

```text
url-safe.json
url-phishing.json
url-redirect-suspicious.json
url-form-credential.json
url-reputation-unavailable.json
url-ai-completed.json
url-ai-failed.json
worker-result-with-pending-ai.json
```

Frontend mock lifecycle:

```text
submit
→ fake 202 scanId
→ mock PROCESSING
→ mock RiskResult
```

Worker có thể test trên URL fixture + fake message bus mà không chờ UI, M08 hoặc AI Worker thật.

## 16. Definition of Done

- Web/Mobile URL scan dùng cùng contract;
- SSRF guard có unit/integration tests;
- worker không gọi core/DB/cache;
- derived indicator có `handling` đúng;
- AI task publish bất đồng bộ và có `pendingAiTasks[]`;
- result/event idempotent;
- degraded/late-AI flow rõ ràng;
- E2E từ URL input đến RiskResult UI.

---

# M03. Text & Transaction Scam Analysis

## 1. Module summary

| Thuộc tính        | Giá trị                                |
| ------------------- | ---------------------------------------- |
| Type                | End-to-End Feature                       |
| Actor               | Guest, User                              |
| UI                  | User Web, Mobile                         |
| API                 | `POST /v1/scans/text`                  |
| Worker              | Text Analyzer Worker                     |
| Shared dependencies | M02/M04 nested scans, M09, M11, M12, M13 |

## 2. Mục đích

Phân tích tin nhắn, hội thoại hoặc bài đăng giao dịch để phát hiện dấu hiệu lừa đảo như urgency, impersonation, yêu cầu chuyển tiền/OTP/credential/CCCD và trích xuất các indicator con như URL, số điện thoại, tài khoản ngân hàng. Text Worker chỉ trả signal/indicator và có thể phát AI task; final verdict thuộc core.

## 3. Góc nhìn người dùng / actor journey

```text
User copy nội dung tin nhắn/bài đăng đáng nghi
→ paste vào Web/Mobile
→ chọn loại nội dung nếu cần (MESSAGE / TRANSACTION_POST)
→ bấm Scan
→ hệ thống phân tích nội dung và các URL/SĐT/STK xuất hiện bên trong
→ AI có thể chạy ở nền như một tín hiệu bổ sung
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

- `MESSAGE`;
- `TRANSACTION_POST`;
- text normalization;
- detect language/encoding;
- extract URL/phone/bank indicators;
- detect amount/deadline;
- urgency/impersonation cues;
- payment/credential request;
- sensitive identity request (CCCD cue);
- scenario classification;
- optional AI task bất đồng bộ;
- nested scan orchestration qua M12.

## 5. UI/UX ownership

- text input/paste area;
- chọn `contentType` hoặc auto-detect assist;
- character limit feedback;
- processing state;
- highlight/nhóm suspicious cues;
- hiển thị nested findings URL/Phone/Bank;
- degraded state nếu child scan/AI timeout;
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
scan job queue: q.scan.text
request key:    scan.text.requested
result key:     scan.text.analyzed

AI task queue:  q.ai.analyze
AI request key: ai.analysis.requested
AI result keys: ai.analysis.completed | ai.analysis.failed
```

## 7. Backend / worker responsibilities

**Backend / M12**

- validate text size;
- create scan;
- publish text job;
- consume `AnalysisSignal[]`, `DerivedIndicator[]`, `pendingAiTasks[]`;
- core pre-enrich và tạo child scans cho URL/Phone/Bank khi cần;
- theo dõi child scan + AI task trong completion barrier;
- finalize partial/degraded theo deadline;
- gọi M09 để tạo final RiskResult.

**Text Worker**

```text
analyzeText(text, contentType)
├── normalizeText()
├── detectLanguageOrEncoding()
├── extractIndicators()
│   ├── URL
│   ├── phone
│   ├── bank account
│   └── CCCD-like cue/value → sensitive-data signal only
├── extractAmountAndDeadline()
├── detectUrgencyAndImpersonationCues()
├── detectPaymentCredentialRequestCues()
├── classifyScamScenarioByRules()
├── publishAiTaskIfEnabled()
└── return signals + derivedIndicators + pendingAiTasks
```

Worker không gọi M02/M04/M08/M11 trực tiếp. Nó chỉ publish/consume message thuộc contract của mình; Orchestrator quyết định nested scan và AI Worker xử lý AI task riêng.

## 8. Input

```text
text
contentType = MESSAGE | TRANSACTION_POST
optional source metadata
optional parentScanId/depth
```

## 9. Output

Worker result:

```text
Text/Content AnalysisSignal[]
DerivedIndicator[]
  - URL / PHONE / BANK_ACCOUNT
  - handling = REPUTATION_ONLY | CHILD_SCAN theo contract
Scenario metadata
pendingAiTasks[]
```

AI prediction về riêng qua AI result event. Final output cho user là `RiskResult` sau khi M12/M09 hoàn tất.

## 10. Data ownership

Dùng shared scan storage:

```text
scan_requests
scan_results
scan_relations
scan_ai_tasks
scan_signals (optional)
```

Text Worker không ghi trực tiếp các bảng này.

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
- AI task deadline = 20 s;
- cycle detection;
- child fail không nhất thiết fail parent;
- AI fail không nhất thiết fail parent;
- worker chỉ khai `pendingAiTasks[]` sau khi AI task publish thành công;
- CCCD không trở thành standalone lookup trong MVP;
- raw CCCD không được log/persist mặc định.

## 12. Failure / degraded behavior

- child scan timeout/fail → parent có thể `COMPLETED + degraded=true`;
- AI fail/timeout → renormalize risk policy, không xem AI = 0;
- malformed text/oversized input → fail fast;
- partial indicator extraction vẫn có thể tạo result nếu đủ signal;
- late AI result → metadata/audit only, không recompute result đã trả.

## 13. Security / privacy

- text input tối đa initial 20.000 ký tự;
- redact/mask CCCD-like value trong log/evidence;
- tránh lưu raw sensitive content lâu hơn cần thiết nếu privacy policy yêu cầu;
- nested indicators phải được normalize trước khi route;
- Text Worker không truy cập PostgreSQL/Redis hay gọi Spring Boot API.

## 14. Dependencies

- M02 URL Scan cho nested URL;
- M04 Entity Check cho Phone/Bank;
- M09 Risk Policy;
- M11 AI optional;
- M12 Shared Scan Platform;
- M13 Infrastructure;
- M14 Audit & Observability cho trace/failure metadata.

## 15. Phát triển độc lập / mock contract

Fixtures:

```text
safe-message.json
urgent-bank-transfer.json
fake-job-post.json
refund-asks-otp.json
asks-for-cccd.json
message-with-url-phone-bank.json
text-worker-pending-ai.json
text-ai-failed.json
```

UI render từ mock `RiskResult`; worker phát triển parser/rules với `FakeMessageBus`; M12 có thể dùng worker-result fixture mà không cần Text Worker thật.

## 16. Definition of Done

- MESSAGE/TRANSACTION_POST dùng cùng endpoint;
- nested indicators hoạt động;
- sensitive data được mask;
- worker không gọi trực tiếp core/DB/cache/AI service;
- AI task bất đồng bộ + barrier hoạt động;
- Web/Mobile cùng semantics;
- E2E text → nested scans/AI → final result.

---

# M04. Phone & Bank Reputation Check

## 1. Module summary

| Thuộc tính        | Giá trị                        |
| ------------------- | -------------------------------- |
| Type                | End-to-End Feature               |
| Actor               | Guest, User                      |
| UI                  | User Web, Mobile                 |
| API                 | `POST /v1/scans/entity`        |
| Worker              | Entity/Reputation Checker Worker |
| Entity types        | `PHONE`, `BANK_ACCOUNT`      |
| Shared dependencies | M07, M08, M09, M12, M13          |

## 2. Mục đích

Cho phép user kiểm tra số điện thoại hoặc tài khoản ngân hàng trước khi gọi lại/chuyển tiền. Core tra dữ liệu threat/reputation và gắn `reputationContext` vào job; Entity Worker dùng context đó để tạo các signal có thể giải thích được.

## 3. Góc nhìn người dùng / actor journey

```text
User nhận cuộc gọi / thông tin chuyển khoản đáng nghi
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

**Backend / M08 + M12**

- validate/normalize input;
- ThreatQuery cache-aside ở core: Redis hit, miss thì PostgreSQL;
- nếu lookup thành công, gắn `reputationContext` vào entity job;
- nếu reputation source unavailable, core tạo `REPUTATION_UNAVAILABLE` metadata/signal theo policy;
- create scan và dispatch entity job;
- consume worker signal và finalize qua M09.

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

Entity Worker không tự lookup threat data và không gọi ngược core.

## 8. Input

```text
entityType = PHONE | BANK_ACCOUNT
value
optional bankCode
reputationContext đã pre-enrich (hoặc trạng thái lookup do core quản lý)
```

## 9. Output

Worker output:

```text
VerifiedReportMatch signals
SourceConfidence
ReportStatistics
TimeDecaySignal
AssociationSignals
```

Core có thể bổ sung `NO_DATA` / `REPUTATION_UNAVAILABLE` vào unified signal/metadata trước Risk Fusion. Final output cho user là `RiskResult`.

## 10. Data ownership

`ThreatQuery` nằm trong core/M08 và là boundary duy nhất đọc threat data cho scan path. Entity Worker không query trực tiếp DB/cache.

Shared data liên quan:

```text
risk_entities
risk_sources
community_reports (verified)
scan_requests
scan_results
```

## 11. Business rules / invariants

- `NO_DATA` ≠ `SAFE_VERIFIED`;
- unverified community report không tạo hard blacklist;
- Phone/Bank dùng chung pipeline nhưng validation strategy riêng;
- source confidence/freshness phải ảnh hưởng signal;
- CCCD không expose trong `ENTITY` MVP;
- reputation lookup thuộc core, không thuộc worker.

## 12. Failure / degraded behavior

- Threat Intelligence unavailable → core đánh dấu `REPUTATION_UNAVAILABLE`, scan có thể tiếp tục/degraded theo policy;
- no match → hiển thị neutral wording;
- invalid phone/account → reject trước khi queue;
- stale source → evidence phải thể hiện freshness nếu có;
- worker fail sau retry → M12 áp dụng completion/failure policy.

## 13. Security / privacy

- không log full bank account nếu không cần;
- masking khi hiển thị/audit;
- access to report/source details theo role;
- worker không truy cập trực tiếp PostgreSQL/Redis/Spring Boot API.

## 14. Dependencies

- M07 verified Community Reports;
- M08 Threat Intelligence;
- M09 Risk Policy;
- M12 Shared Scan Platform;
- M13 Infrastructure;
- M14 Audit & Observability cho trace/failure metadata.

## 15. Phát triển độc lập / mock contract

Fixtures:

```text
entity-no-data.json
entity-verified-report.json
entity-multiple-reports.json
entity-stale-source.json
entity-reputation-unavailable.json
```

UI có thể implement tất cả state trước khi threat database có dữ liệu thật. Entity Worker test với `reputationContext` fixture thay vì cần DB thật.

## 16. Definition of Done

- Phone/Bank chung endpoint/pipeline;
- normalization tests;
- no-data wording đúng;
- source confidence/freshness thể hiện rõ;
- worker isolation được test;
- Web/Mobile parity;
- E2E input → reputation result.

---

# M05. QR / VietQR Scan

## 1. Module summary

| Thuộc tính        | Giá trị                         |
| ------------------- | --------------------------------- |
| Type                | End-to-End Feature                |
| Actor               | Guest, User                       |
| UI                  | Mobile camera/gallery, Web upload |
| API                 | `POST /v1/scans/qr`             |
| Worker              | QR Parser Worker                  |
| Nested dependencies | M02 URL, M03 Text, M04 Entity     |

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

| Thuộc tính       | Giá trị                                                                                       |
| ------------------ | ----------------------------------------------------------------------------------------------- |
| Type               | End-to-End Feature                                                                              |
| Actor              | Guest, User; Admin theo quyền                                                                  |
| UI                 | User Web, Mobile                                                                                |
| API                | Scan read APIs + history/export APIs                                                            |
| Worker             | Report Export Worker                                                                            |
| Data               | read-side`scan_requests`/`scan_results`, export metadata, MinIO/S3                          |
| MVP history policy | Guest: không persistent account history; Authenticated User: persistent history theo`userId` |

## 2. Mục đích

Cung cấp read-side thống nhất cho mọi scan type: trạng thái xử lý, chi tiết kết quả, **persistent history cho authenticated user** và export/share. Guest vẫn được xem kết quả scan mà mình vừa tạo trong access scope hợp lệ nhưng không có account history lâu dài. Module này giúp URL/Text/Entity/QR không tự xây result page khác nhau.

## 3. Góc nhìn người dùng / actor journey

**Guest — MVP**

```text
Guest gửi scan
→ nhận scanId + guest access proof/session
→ xem Processing / Result Detail
→ xem evidence + explanation + recommendation
→ không có persistent account History
→ nếu muốn lưu/quản lý history lâu dài thì đăng ký/đăng nhập cho các scan tiếp theo
```

**Authenticated User — MVP**

```text
User gửi scan
→ scan gắn userId
→ xem Processing / Result Detail
→ xem Score / SAFE-CAUTION-DANGER
→ xem evidence + explanation + recommendation
→ xem lại trong persistent History
→ export/share nếu policy cho phép
```

Nếu scan degraded, user phải thấy thông báo rằng một số nguồn/tác vụ không khả dụng thay vì tưởng kết quả là đầy đủ tuyệt đối.

## 4. Phạm vi nghiệp vụ

- scan status;
- result detail;
- guest result access trong session/access scope;
- persistent authenticated history;
- shared risk presentation;
- evidence/explanation/recommendation;
- degraded state;
- history filter/pagination cho authenticated user;
- export PDF/HTML;
- share result theo platform.

Credit/billing/paid export không thuộc MVP.

## 5. UI/UX ownership

- processing/status page cho Guest/User;
- shared Result Detail component;
- risk score/level visualization;
- evidence list/grouping;
- explanation/recommendation;
- degraded banner;
- authenticated history list/filter;
- guest CTA đăng nhập/đăng ký nếu muốn persistent account features;
- export status/download;
- share action.

Scan-specific module có thể cung cấp extra evidence renderer nhưng không fork toàn result page.

## 6. API / event contracts

```text
GET  /v1/scans/{scanId}            Guest/User nếu có access proof hợp lệ
GET  /v1/scans/history             Authenticated User trong MVP
POST /v1/scans/{scanId}/exports    nếu expose riêng và policy cho phép
GET  /v1/exports/{exportId}
```

`GET /v1/scans/history` không phải guest endpoint trong MVP. Không coi `scanId` tự thân là authorization token. Guest access nên dựa trên opaque guest session hoặc short-lived signed access proof do core cấp.

Export async event:

```text
queue: q.report.export
key:   report.export.requested
```

## 7. Backend / worker responsibilities

Backend:

```text
getScanStatus(scanId, accessContext)
getScanDetail(scanId, accessContext)
listScanHistory(userId, filters, pagination)
getScanSummary(scanId)
requestExport(scanId, accessContext)
getExportStatus(exportId, accessContext)
```

Authorization policy:

```text
GUEST -> chỉ result thuộc guest access scope còn hợp lệ
USER  -> scan thuộc userId hoặc policy chia sẻ hợp lệ
ADMIN -> theo explicit admin permission/audit policy
```

Export Worker:

```text
RiskResult + evidence + template
→ render PDF/HTML
→ store artifact vào MinIO/S3
→ trả object reference/status theo export contract
```

Canonical export metadata/business state, nếu persist, thuộc Spring Boot/M06; worker không sở hữu business table.

## 8. Input

```text
scanId
AccessContext
userId nếu authenticated
guest access proof/session nếu guest
filters/pagination
exportFormat
```

## 9. Output

```text
ScanSummary
RiskResult
HistoryPage              // authenticated only trong MVP
ExportJobStatus
Download/Object reference qua backend policy
```

## 10. Data ownership

M06 sở hữu **read/query/export use cases**, không sở hữu quá trình tạo final risk verdict.

```text
scan_requests  -> read ownership; canonical lifecycle thuộc M12
scan_results   -> read ownership; canonical write/finalization thuộc M12 sau M09
export metadata -> M06/Spring Boot nếu feature persist trạng thái export
MinIO/S3 exported objects -> artifact do Report Export Worker tạo
```

Ownership fields phải hỗ trợ cả:

```text
ownerUserId       // nullable khi guest
guestAccessRef    // opaque/hashed reference hoặc session binding theo policy
createdAt
```

`guestAccessRef` không được dùng như permanent identity; TTL/retention theo privacy policy.

## 11. Business rules / invariants

- cùng một `RiskResult` contract cho mọi scan type;
- user/guest chỉ xem scan có quyền truy cập;
- biết `scanId` không đủ để đọc result;
- persistent History chỉ thuộc authenticated account trong MVP;
- guest scan không tự động xuất hiện trong account history sau khi user đăng ký, trừ khi sau này có explicit claim/migration feature; MVP không cần feature này;
- degraded phải hiển thị rõ;
- export lấy từ final persisted result, không tự recompute risk;
- result page không tự diễn giải vượt quá evidence.

## 12. Failure / degraded behavior

- scan còn PROCESSING → UI polling/status state;
- guest access proof hết hạn → yêu cầu scan lại hoặc login theo policy, không bypass ownership;
- scan FAILED → clear error/retry action theo policy;
- export worker fail → scan result vẫn sử dụng bình thường;
- MinIO unavailable → export unavailable, không ảnh hưởng core scan;
- partial/degraded result vẫn xem được.

## 13. Security / privacy

- authorization theo `scanId` + `AccessContext`;
- guest session/access proof phải opaque, short-lived và không dựa vào đoán được identifier;
- signed/controlled download URL nếu dùng object storage;
- export phải mask sensitive fields theo policy;
- history không lộ scan của user khác;
- guest không được enumerate history/result của guest khác.

## 14. Dependencies

- M01 Identity/Access Context;
- M12 Shared Scan Platform;
- M13 Storage/DB;
- M10 Notification nếu báo export completed;
- M14 Audit & Observability cho export/failure trace.

## 15. Phát triển độc lập / mock contract

Fixtures:

```text
url-danger-result.json
text-caution-result.json
entity-no-data-result.json
qr-degraded-result.json
scan-processing.json
scan-failed.json
guest-scan-access.json
user-history-page.json
```

UI module hoàn thiện được mà không cần chờ worker thật.

## 16. Definition of Done

- một result design dùng cho mọi scan type;
- Guest xem được result của chính scan trong access scope mà không cần account;
- authenticated user có history/filter/pagination;
- guest không có persistent account history;
- ownership/authorization test cho Guest/User/Admin;
- degraded state rõ ràng;
- export async;
- Web/Mobile parity.

---

# M07. Community Report & Moderation

## 1. Module summary

| Thuộc tính | Giá trị                                |
| ------------ | ---------------------------------------- |
| Type         | End-to-End Feature                       |
| Actor        | User, Moderator/Admin                    |
| UI           | User Web/Mobile + Admin Dashboard        |
| Storage      | PostgreSQL + MinIO/S3                    |
| Downstream   | M08 Threat Intelligence, M09 Risk Policy |

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
- M13 Object Storage;
- M14 Audit & Observability.

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

| Thuộc tính | Giá trị                                                 |
| ------------ | --------------------------------------------------------- |
| Type         | Admin/Internal Feature                                    |
| Actor        | Admin, Spring Boot scan core                              |
| UI           | Admin Dashboard                                           |
| Worker       | Threat Data Ingestion Worker                              |
| Data owner   | `risk_entities`, `risk_sources`, import/sync metadata |
| Cache        | Redis threat/reputation hot cache                         |

## 2. Mục đích

Quản lý nguồn threat/reputation bên ngoài, ingest và chuẩn hóa dữ liệu vào hệ thống, đồng thời cung cấp **core-only ThreatQuery** cho scan orchestration. Scanner worker không được đọc DB/cache hoặc gọi endpoint của module này.

## 3. Góc nhìn người dùng / actor journey

**Admin**

```text
Mở Threat Sources
→ xem nguồn nào đang bật/tắt
→ xem last sync / số record / lỗi
→ trigger sync thủ công khi cần
→ kiểm tra kết quả ingestion
```

**User cuối** không thao tác trực tiếp module này. Họ chỉ cảm nhận gián tiếp: scan URL/SĐT/STK được enrich bằng dữ liệu reputation có nguồn và freshness rõ ràng.

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
- core-only threat/reputation lookup;
- pre-enrichment trước khi publish scan job;
- deferred enrichment khi core nhận `DerivedIndicator[]`;
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

Admin API **đề xuất**:

```text
GET  /v1/admin/threat-sources
GET  /v1/admin/threat-sources/{id}
POST /v1/admin/threat-sources/{id}/sync
PATCH /v1/admin/threat-sources/{id}
```

Internal application contract — **in-process Spring Boot boundary, không phải endpoint cho worker**:

```text
ThreatQuery.lookup(indicator) -> ReputationContext | NO_DATA | REPUTATION_UNAVAILABLE
```

RabbitMQ ingestion:

```text
queue: q.threat.ingest
key:   threat.ingest.requested
```

## 7. Backend / worker responsibilities

**Backend / Threat Intelligence Query**

```text
lookup(indicator)
→ Redis HIT → ReputationContext
→ Redis MISS → PostgreSQL
→ cache result
→ return ReputationContext / NO_DATA / REPUTATION_UNAVAILABLE
```

Lookup được dùng ở hai thời điểm:

```text
1. Pre-enrichment
   trước khi core publish scan job
   → reputationContext đi kèm job

2. Deferred enrichment
   khi core consume worker result
   → DerivedIndicator(REPUTATION_ONLY) được tra tại core và gắn signal
   → DerivedIndicator(CHILD_SCAN) được enrich rồi tạo scan con nếu cần
```

**Threat Data Ingestion Worker**

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

Scanner worker không gọi `ThreatQuery` trực tiếp.

## 8. Input

```text
CSV
JSON
external API/feed
admin upload/trigger
scheduled trigger
lookup indicator từ Spring Boot core
DerivedIndicator từ worker result (được core chuyển cho ThreatQuery)
```

## 9. Output

```text
normalized threat/reputation records
source confidence metadata
sync/import status
ReputationContext
NO_DATA
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

PostgreSQL = source of truth; Redis = hot/cache copy.

## 11. Business rules / invariants

- source phải có identity/version/freshness metadata;
- ingestion phải deduplicate/idempotent;
- verified community data từ M07 có thể trở thành source/reference;
- scanner worker không query DB/cache và không gọi core API;
- cache miss → PostgreSQL → cache result;
- chỉ core/M08 được sinh `REPUTATION_UNAVAILABLE` cho scan path;
- `DerivedIndicator.handling` là hint; Orchestrator có quyền quyết định `REPUTATION_ONLY` hay `CHILD_SCAN` theo policy/depth.

## 12. Failure / degraded behavior

- ingestion source unavailable → giữ dữ liệu cũ nếu policy cho phép, ghi stale/error;
- Redis unavailable → core lookup fallback PostgreSQL;
- reputation lookup trong core có timeout engineering default 500 ms;
- Redis + PostgreSQL lookup đều không khả dụng → core trả `REPUTATION_UNAVAILABLE`;
- bad source record → skip/quarantine theo policy, không làm hỏng toàn batch.

## 13. Security / privacy

- external source credential không log;
- admin sync action audit;
- validate imported file/source;
- không ingest dữ liệu riêng tư/không hợp pháp;
- không expose threat lookup endpoint cho scanner worker;
- scanner worker network policy không cần route tới PostgreSQL/Redis/Spring Boot.

## 14. Dependencies

- M07 verified community source;
- M12 orchestration/messaging;
- M13 PostgreSQL/Redis/RabbitMQ;
- M14 Audit & Observability.

## 15. Phát triển độc lập / mock contract

Fixtures:

```text
sample-threat-feed.csv
sample-threat-feed.json
source-sync-success.json
source-sync-failed.json
reputation-hit.json
reputation-miss.json
reputation-unavailable.json
```

M02/M04/M12 dùng `MockThreatQuery` ở core boundary nên không phải chờ ingestion thật. Worker tests nhận `reputationContext` fixture thay vì kết nối DB/cache.

## 16. Definition of Done

- import idempotent/deduplicate;
- PostgreSQL source of truth;
- Redis refresh/invalidate đúng;
- Admin thấy sync status;
- pre-enrichment + deferred enrichment có contract test;
- scanner worker không thể gọi core/DB/cache;
- failure/stale source observable.

---

# M09. Rule, Risk Policy & Admin Operations

## 1. Module summary

| Thuộc tính | Giá trị                                                                 |
| ------------ | ------------------------------------------------------------------------- |
| Type         | Shared/Admin Feature                                                      |
| Actor        | Admin, M02–M05                                                           |
| UI           | Admin Dashboard                                                           |
| Backend      | Rule Engine + Risk Fusion + Result Composer                               |
| Data owner   | `rules`, `rule_versions`, fusion policy/version, admin policy changes |

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

| Profile |                                   Technical | Content | Reputation/Community |  AI |
| ------- | ------------------------------------------: | ------: | -------------------: | --: |
| URL     |                                         35% |     15% |                  35% | 15% |
| TEXT    |                                         10% |     45% |                  25% | 20% |
| ENTITY  |                                          0% |      0% |                 100% |  0% |
| QR      | dùng child scan scores + QR-specific rules |         |                      |     |

Missing group:

```text
renormalize available weights
```

không gán missing signal = 0.

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
- M11 AI signals;
- M12 Signal Aggregation;
- M13 DB;
- M14 Audit & Observability.

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

| Thuộc tính | Giá trị                                            |
| ------------ | ---------------------------------------------------- |
| Type         | End-to-End Feature                                   |
| Actor        | User, Admin                                          |
| UI           | Web/Mobile notification center/badge nếu scope có  |
| Backend      | Notification policy/job                              |
| Worker       | Notification worker/provider adapter nếu async      |
| Data         | `notification_jobs`, status/preferences nếu dùng |

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
- M13 infrastructure;
- M14 Audit & Observability cho delivery/failure trace.

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

| Thuộc tính       | Giá trị                                                                                                                          |
| ------------------ | ---------------------------------------------------------------------------------------------------------------------------------- |
| Type               | Shared Capability                                                                                                                  |
| Actor              | Không có end-user trực tiếp; consumers là M02/M03                                                                             |
| Runtime            | Python AI/ML Worker consume RabbitMQ; FastAPI chỉ cho health/readiness/diagnostics nội bộ nếu cần                             |
| Inference strategy | **Provider-agnostic**: MVP ưu tiên external LLM API; giai đoạn nghiên cứu có thể chuyển sang local/fine-tuned model |
| Provider examples  | `OPENAI`, `GEMINI`, `HUGGINGFACE_LOCAL`, `CUSTOM_LOCAL` (không phải enum đóng)                                         |
| Input              | AI task có`scanId`, `taskId`, `kind`, optional logical `inferenceProfile`, payload/features/content                       |
| Output             | `ai.analysis.completed` hoặc `ai.analysis.failed` với prediction đã normalize                                              |
| Business verdict   | Không thuộc module này                                                                                                          |

## 2. Mục đích

Cung cấp model/LLM inference dùng chung cho URL/Text/Web analysis mà **không giữ Web/Text Worker phải chờ model**. M02/M03 publish AI task vào RabbitMQ; Python AI/ML Worker xử lý độc lập và trả prediction signal về `q.scan.result`.

M11 phải **không phụ thuộc một nhà cung cấp/model cụ thể hoặc một đường truy cập API cụ thể**. Ở MVP, inference có thể gọi external LLM theo nhiều nguồn: gọi trực tiếp OpenAI/Gemini, đi qua gateway/aggregator như OpenRouter, hoặc một API provider khác có adapter tương thích. Ở giai đoạn nghiên cứu, cùng AI Worker có thể chuyển sang model local/fine-tuned từ Hugging Face hoặc implementation dựa trên paper. Việc đổi **provider route / model / runtime** phải thực hiện bằng config + adapter registry + model registry, **không làm thay đổi scan orchestration, RabbitMQ topology hay contract mà Spring Boot consume**. Final business verdict vẫn thuộc M09, còn việc chờ/ghép kết quả thuộc M12.

## 3. Góc nhìn người dùng / actor journey

User không gọi AI Worker trực tiếp. Dưới góc nhìn user:

```text
User scan URL/Text
→ scanner worker hoàn tất phần phân tích riêng
→ AI có thể tiếp tục chạy nền
→ M12 chờ AI trong một deadline giới hạn
→ user nhận một RiskResult thống nhất
→ nếu AI fail/quá hạn, result vẫn có thể hoàn thành degraded bằng các tín hiệu còn lại
```

AI không được làm UX trở thành “AI nói nguy hiểm” mà không có evidence liên quan.

## 4. Phạm vi nghiệp vụ

- consume `ai.analysis.requested`;
- validate AI task;
- idempotency theo `taskId`/`eventId`;
- model routing theo `kind` / model profile;
- model-specific preprocessing;
- **Inference Provider resolution** từ model config;
- provider adapter cho external LLM API qua nhiều nguồn: direct API (OpenAI/Gemini/...) hoặc gateway/aggregator (ví dụ OpenRouter);
- provider adapter cho API tương thích chuẩn phổ biến hoặc custom HTTP API khi cần;
- provider adapter cho local/fine-tuned model (Hugging Face/custom model/paper implementation);
- model loading/version registry;
- model/LLM inference;
- normalize output khác nhau về một `AiPrediction` contract thống nhất;
- timeout + bounded retry nội bộ;
- provider/model/version/probability/label/latency metadata;
- publish success/failure result;
- concurrency control bằng RabbitMQ `prefetch_count`;
- health/readiness/diagnostics nội bộ;
- optional URL/Text/Web models.

## 5. UI/UX ownership

Không có UI riêng cho user MVP.

User-facing AI evidence được render qua M06 dựa trên final result do M09 compose. Admin/model diagnostics UI nếu có là operational scope, không phải user feature cốt lõi.

## 6. API / event contracts

**Scan flow không dùng REST/gRPC inference.** Giao diện chính của M11 là RabbitMQ.

```text
consume queue: q.ai.analyze
request key:   ai.analysis.requested
result queue:  q.scan.result
result keys:   ai.analysis.completed | ai.analysis.failed
```

AI task tối thiểu:

```json
{
  "eventId": "evt_ai_req_1",
  "correlationId": "corr_123",
  "eventVersion": "1.0",
  "taskId": "aitask_abc",
  "scanId": "scan_123",
  "parentScanId": null,
  "kind": "URL_FEATURES",
  "inferenceProfile": "standard",
  "attempt": 1,
  "payload": {
    "normalizedUrl": "https://example.com/login",
    "features": {},
    "contentExcerpt": null
  },
  "requestedBy": "url-worker-v1",
  "requestedAt": "...",
  "deadlineAt": "..."
}
```

AI task **cố ý không chứa provider cụ thể**. Producer mô tả `kind` + normalized payload và có thể kèm logical `inferenceProfile` đã được Spring Boot/core policy resolve; M11 tự chọn `providerKey` + model từ config/registry. `providerKey` là khóa cấu hình mở, ví dụ `openai-direct`, `gemini-direct`, `openrouter`, `hf-local` hoặc một provider mới. Nó **không phải closed enum** trong contract. Nhờ vậy M02/M03 không bị coupling với OpenAI, Gemini, OpenRouter hay model local.

AI result tối thiểu:

```json
{
  "eventId": "evt_ai_res_1",
  "correlationId": "corr_123",
  "eventVersion": "1.0",
  "taskId": "aitask_abc",
  "scanId": "scan_123",
  "status": "COMPLETED",
  "prediction": {
    "label": "PHISHING",
    "probability": 0.87,
    "providerKey": "openrouter",
    "modelId": "openai/gpt-5",
    "modelVersion": "prompt-v1",
    "latencyMs": 420
  },
  "failureReason": null,
  "processorVersion": "ai-worker-v1",
  "processedAt": "..."
}
```

Khi fail: `status="FAILED"`, `prediction=null`, `failureReason` thuộc `TIMEOUT | MODEL_UNAVAILABLE | INVALID_PAYLOAD | INTERNAL_ERROR`.

FastAPI, nếu giữ, chỉ expose `/health`, `/ready` và diagnostics nội bộ; không phải đường inference của scan.

## 7. Backend / worker responsibilities

**Python AI/ML Worker**

```text
handleAiTask(task)
├── validateTask()                // scanId, taskId, kind, payload size
├── deduplicateByTaskId()
├── routeModel(kind, inferenceProfile) // chọn logical model/profile cho task
│   ├── URL_FEATURES
│   ├── TEXT_CONTENT
│   └── WEB_CONTENT
├── loadModelConfig()             // providerKey + modelId + modelVersion + params
├── resolveProviderAdapter(providerKey)
│   └── ProviderAdapterRegistry
│       ├── direct API adapter(s)          // OpenAI, Gemini, ...
│       ├── gateway/aggregator adapter(s)  // OpenRouter, ...
│       ├── compatible/custom HTTP adapter // provider mới nếu cần
│       └── local runtime adapter(s)       // Hugging Face / custom paper model
├── preprocess()
├── adapter.predictWithTimeout()
├── normalizePrediction()         // mọi provider -> AiPrediction chung
├── postprocess()
└── publishResult()
    ├── success → ai.analysis.completed
    └── failure → ai.analysis.failed
```

**Ranh giới abstraction:**

```text
Model Router             = task này nên dùng logical model/profile nào?
Provider Adapter Registry = `providerKey` nào được thực thi bằng adapter nào?
Inference Provider/Adapter = cách gọi direct API, gateway/aggregator, custom HTTP hoặc local runtime
Model Registry            = providerKey + modelId + modelVersion + supported kinds + config/artifact location
```

`InferenceProvider`/`ProviderAdapter` là contract nội bộ của M11. Mọi nguồn inference — direct API, gateway/aggregator, custom HTTP provider hay local model — đều phải implement cùng semantics `predict(request) -> AiPrediction`. Thêm một nguồn mới chủ yếu là đăng ký `providerKey` + adapter/config; không sửa producer, M12 hay M09. Chuyển từ OpenAI direct sang OpenRouter, Gemini direct, Hugging Face local hoặc custom paper model chỉ đổi config/adapter/model artifact.

**M12 / Spring Boot** chịu trách nhiệm consume AI result, ghép bằng `scanId + taskId`, biến prediction thành AI `AnalysisSignal`, quản lý barrier và gọi M09. M12 chỉ xem `providerKey/modelId/modelVersion` là trace metadata, **không branch business logic theo provider route**. AI Worker không tự tạo `RiskResult`.

## 8. Input

```text
AiAnalysisTask
scanId
taskId
kind = URL_FEATURES | TEXT_CONTENT | WEB_CONTENT
inferenceProfile = standard | <extensible future profile key>
normalized features/content
requestedBy / requestedAt / deadlineAt
```

## 9. Output

```text
AiAnalysisResult
status = COMPLETED | FAILED
prediction = label + probability + providerKey + modelId + modelVersion + latencyMs
failureReason nếu FAILED
processorVersion
```

## 10. Data ownership

- model artifacts cho local/fine-tuned model;
- model/provider adapter registry config và runtime metadata;
- `providerKey`, `modelId`, `modelVersion`, supported task kinds;
- external provider credentials thuộc secret/config management của M13, không nằm trong business data;
- canonical model version metadata có thể nằm trong PostgreSQL do core/platform quản lý;
- module không sở hữu `scan_requests`, `scan_results`, `scan_ai_tasks` hay final verdict;
- AI Worker không đọc/ghi PostgreSQL/Redis/business tables.

## 11. Business rules / invariants

- Python trả prediction, không `SAFE/CAUTION/DANGER` business verdict;
- mỗi task nhận được phải sinh **đúng một result**, kể cả khi fail;
- consumer/worker idempotent theo `eventId` và `taskId`;
- task lặp không được chạy inference lần hai;
- `scanId + taskId` phải có ở cả task và result;
- `deadlineAt` phải đi cùng task;
- `inferenceProfile` là logical capability/profile do business core resolve trước khi dispatch; MVP dùng profile mặc định như `standard`; paid/premium profile là Future Scope;
- M11 không nhận `planKey`, `creditBalance`, payment state hay user billing data; nó chỉ nhận logical `inferenceProfile`;
- `AiAnalysisTask` không hard-code provider; routing `providerKey`/model là trách nhiệm nội bộ M11;
- `providerKey` là extensible string/config key, không phải enum đóng trong public/event contract;
- direct API, gateway/aggregator, custom HTTP provider và local/fine-tuned model đều phải trả về cùng `AiPrediction` normalized contract;
- switch nguồn gọi API hoặc model bằng config + adapter registry + model registry, không đổi M02/M03/M12/M09 contract; `inferenceProfile` chỉ chọn logical model policy, không expose provider cho client;
- `providerKey + modelId + modelVersion` luôn traceable trong successful result;
- structured output từ external LLM phải được validate/normalize trước khi publish; local classifier/logits cũng phải normalize về `label + probability`;
- fallback provider route/model nếu cấu hình phải bounded bởi cùng `deadlineAt`, không được tạo nhiều terminal result cho một task; gateway có routing nội bộ cũng không làm thay đổi nguyên tắc một task chỉ có một terminal result;
- AI result không chứa `riskScore`/`riskLevel`;
- AI Worker không query user/business tables trực tiếp.

## 12. Failure / degraded behavior

- `aiTaskDeadline` ở M12 mặc định = 20 s;
- model/LLM call timeout trong AI Worker mặc định = 10 s, tính cả retry nội bộ;
- tổng xử lý task không được vượt `deadlineAt`; nếu quá hạn → publish `ai.analysis.failed(TIMEOUT)`;
- model unavailable → publish FAILED thay vì im lặng;
- message retry exhausted → `q.ai.analyze.dlq` và scan thoát chờ nhờ deadline;
- result về sau khi scan finalize → M12 ghi late signal/audit, không sửa RiskResult đã trả.

## 13. Security / privacy

- AI Worker không public Internet endpoint cho inference;
- không log raw sensitive text quá mức cần thiết;
- payload size limit;
- local/fine-tuned model artifact phải có integrity/version control;
- không truy cập PostgreSQL/Redis/Spring Boot API;
- nếu model/LLM provider ở ngoài hệ thống, outbound credential phải được quản lý bằng secret và payload phải được xử lý theo privacy policy;
- provider adapter không được ghi API key/token vào log, event hoặc result metadata;
- khi đổi từ external LLM sang local model, privacy policy có thể khác nhưng output contract không đổi.

## 14. Dependencies

- M02 URL Scan;
- M03 Text Analysis;
- M09 Risk Policy (consumer downstream);
- M12 Shared Scan Platform;
- M13 RabbitMQ/runtime;
- M14 Audit & Observability cho AI failure/DLQ/late-result metadata.

## 15. Phát triển độc lập / mock contract

Producer/consumer contract có thể test bằng fixtures:

```text
ai-url-request.json
ai-text-request.json
ai-completed.json
ai-timeout.json
ai-model-unavailable.json
ai-duplicate-task.json
ai-late-result.json
ai-openai-completed.json
ai-gemini-completed.json
ai-local-model-completed.json
```

M02/M03 chỉ cần `FakeMessageBus` để publish task; M12 có thể dùng AI result fixture mà không cần model thật; M11 có thể chạy contract test độc lập với RabbitMQ/Testcontainers. Provider adapter nên có fake implementation để test cùng một task qua external-LLM mode và local-model mode nhưng vẫn sinh cùng schema `AiAnalysisResult`.

## 16. Definition of Done

- không còn REST/gRPC inference trong scan flow;
- consume `q.ai.analyze` và publish result vào `q.scan.result`;
- mỗi task luôn có completed/failed result;
- idempotent theo `eventId` + `taskId`;
- có `InferenceProvider`/`ProviderAdapter` abstraction + Model Router + Provider Adapter Registry + Model Registry rõ ràng;
- switch được bằng config giữa direct API, gateway/aggregator và local/fake provider mà không đổi task/result contract;
- thêm provider mới không yêu cầu sửa M02/M03/M12/M09;
- `providerKey + modelId + modelVersion` traceable;
- provider outputs được normalize về cùng `AiPrediction`;
- timeout/deadline/retry/DLQ test;
- không trả final business verdict;
- không truy cập business DB/cache/core API.

---

# M12. Shared Scan Platform

## 1. Module summary

| Thuộc tính | Giá trị                                                                           |
| ------------ | ----------------------------------------------------------------------------------- |
| Type         | Shared Platform                                                                     |
| Actor        | Không có UI trực tiếp; primary consumers M02–M06, phối hợp M08–M11/M14      |
| Backend      | Scan Orchestrator, Processor Registry, Worker/AI Result Consumer, Signal Aggregator |
| Messaging    | RabbitMQ                                                                            |
| Coordination | PostgreSQL + Redis                                                                  |
| Ownership    | scan lifecycle + nested scan + AI task coordination + finalization                  |

## 2. Mục đích

Cung cấp lifecycle/orchestration dùng chung để URL/Text/Entity/QR không tự implement lại idempotency, queue dispatch, result consumption, nested scan, AI task tracking, completion barrier và finalization. M12 nhận `AccessContext` + `EntitlementContext` đã được M01/core resolve để áp quota/feature policy trước dispatch mà không đưa billing/credit logic vào worker. Trong MVP chỉ cần policy `guest` và `free`; paid/credit/API commercialization là Future Scope.

## 3. Góc nhìn người dùng / actor journey

User không thấy module này như một màn hình riêng. Họ cảm nhận nó qua hành vi nhất quán:

```text
Bấm Scan
→ nhận scanId nhanh
→ thấy PROCESSING
→ scanner worker chạy nền
→ child scan và AI task được theo dõi nếu phát sinh
→ hệ thống kết thúc khi đủ kết quả hoặc hết deadline
→ cuối cùng về COMPLETED / FAILED / COMPLETED+degraded
→ không treo PROCESSING vô hạn
```

## 4. Phạm vi nghiệp vụ

- scan intake lifecycle;
- access/ownership context cho Guest/User;
- quota/entitlement gate từ resolved policy (`guest`/`free` trong MVP);
- propagate logical `inferenceProfile` vào AI-capable job/task mà không expose provider/model business detail;
- idempotency;
- result cache;
- `ScanRequest` persistence;
- processor routing;
- reputation pre-enrichment coordination;
- RabbitMQ dispatch;
- worker result consumption;
- AI result consumption;
- event/task idempotency;
- signal aggregation;
- derived-indicator handling;
- nested scan tree;
- AI task registry;
- completion barrier;
- timeout/degraded finalization;
- late AI result handling;
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

M06 phải có khả năng giải thích degraded state khi còn child/AI source không hoàn tất.

## 6. API / event contracts

Feature endpoints được module tương ứng sở hữu semantics:

```text
URL    -> /v1/scans/url
TEXT   -> /v1/scans/text
ENTITY -> /v1/scans/entity
QR     -> /v1/scans/qr
```

RabbitMQ topology:

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
q.ai.analyze
q.scan.result
q.report.export
q.notification
q.threat.ingest
```

Core routing keys:

```text
scan.url.requested / scan.url.analyzed
scan.text.requested / scan.text.analyzed
scan.entity.requested / scan.entity.analyzed
scan.qr.requested / scan.qr.parsed
ai.analysis.requested / ai.analysis.completed / ai.analysis.failed
scan.completed / scan.failed
```

`q.report.export`, `q.notification`, `q.threat.ingest` thuộc semantics của M06/M10/M08; M12 chỉ dùng chung messaging conventions/topology với các module đó.

## 7. Backend / worker responsibilities

Core components:

```text
Input Validation / Scan Type Resolver
Processor Registry / Job Router
Scan Orchestrator
Worker Result Consumer
AI Result Consumer
Signal Aggregator
Completion Barrier
```

Primary flow:

```text
ValidatedScanCommand + AccessContext + EntitlementContext
→ authorize scan type / check quota policy
→ check idempotency/cache
→ create ScanRequest với ownerUserId hoặc guestAccessRef
→ resolve ScanExecutionPolicy (`quotaProfile`, `inferenceProfile`, feature flags)
→ pre-enrich reputation nếu cần
→ publish primary worker job kèm non-sensitive execution policy cần thiết
→ consume WorkerResult
→ deduplicate eventId
→ resolve REPUTATION_ONLY indicators tại core
→ create/enrich child scans cho CHILD_SCAN nếu cần
→ register pendingAiTasks[]
→ match parked AI results nếu AI về sớm
→ consume AI result theo scanId + taskId
→ check child + AI completion barrier
→ aggregate signals
→ call M09 Risk Evaluation
→ persist/cache final result
```

AI result flow:

```text
handleAiResult(aiResult)
→ validate scanId + taskId
→ deduplicate eventId/taskId
→ task đã biết: validate normalized prediction + trace metadata (`providerKey/modelId/modelVersion`), close task + convert prediction thành AnalysisSignal
→ M12 không chọn provider route/model và không branch Risk Fusion theo providerKey
→ task chưa biết: park theo scanId
→ scan đã finalize: record late signal only
→ check barrier / finalizeIfReady
```

## 8. Input

```text
ValidatedScanCommand
AccessContext
EntitlementContext / ScanExecutionPolicy
WorkerResult event
AiAnalysisResult event
DerivedIndicator[]
pendingAiTasks[]
timeout/deadline event
```

## 9. Output

```text
scanId
ScanStatus
resolved ownership/quota/inference profile metadata
worker jobs
AI coordination state
UnifiedSignalSet
child scan tree
finalization trigger
failure/degraded metadata
late-result metadata
```

## 10. Data ownership

PostgreSQL:

```text
scan_requests (ownerUserId nullable + guest access ownership metadata theo policy)
scan_relations
scan_ai_tasks
scan lifecycle status
processed event/idempotency metadata nếu persist
```

`scan_results` là canonical final result được M12 persist sau khi M09 compose; M06 sở hữu read/query/export use cases trên result đó.

Redis:

```text
idempotency key
guest/user quota counters
result cache
expected/completed/failed child counters
expected/completed AI task counters
parked-result/temporary coordination nếu implementation dùng
lightweight lock
```

PostgreSQL vẫn là source of truth; Redis chỉ là temporary coordination/cache.

## 11. Business rules / invariants

Access/entitlement:

```text
MVP:
GUEST -> public scan allowed + guest quota + no persistent account history + standard inferenceProfile
FREE  -> account quota + persistent history + standard inferenceProfile

Future:
PREMIUM / API_CLIENT / CREDIT-BASED -> resolve ở business capability riêng
                                     -> chỉ trả EntitlementContext/ScanExecutionPolicy cho M12
```

- M12 không tự tính tiền và không sở hữu credit/subscription state;
- scanner/AI worker không được nhận `creditBalance`, payment info hay raw plan state;
- quota/entitlement phải được check trước khi chấp nhận/publish công việc tốn tài nguyên;
- future usage metering nên đi qua `UsageMeterPort`/domain event sau khi có execution metadata, không làm thay đổi scan result contract;
- nếu future paid profile được dùng, core chỉ propagate logical `inferenceProfile`; M11 tự map profile -> model/provider;
- `scanId` không phải authorization token; result/history access vẫn theo M01/M06 access policy.

Completion barrier:

```text
PostgreSQL scan_relations = source of truth cho child scan
PostgreSQL scan_ai_tasks  = source of truth cho AI task
Redis = temporary coordination
parent deadline = 30 s
AI task deadline = 20 s
maxDepth = 2
cycle detection = enabled
```

Finalize khi:

```text
1. mọi child terminal VÀ mọi AI task terminal; hoặc
2. AI task deadline hết → bỏ task còn treo, degraded=true, renormalize; hoặc
3. parent deadline hết → partial/degraded result
```

Messaging:

```text
at-least-once delivery
consumer idempotent by eventId
task idempotent by taskId
attempt đầu + tối đa 2 retry
→ DLQ
```

Race conditions bắt buộc xử lý:

- AI result về trước worker result → park theo `scanId`, ghép khi `pendingAiTasks[]` xuất hiện;
- AI result về sau `COMPLETED` → late signal/audit only, không sửa RiskResult;
- worker chỉ khai AI task sau khi publish thành công.

Scan không được treo `PROCESSING` vô hạn.

## 12. Failure / degraded behavior

- quota exceeded → reject trước dispatch với lỗi business rõ ràng (`429`/quota error theo API policy);
- entitlement/usage extension future unavailable → không tự nâng lên paid profile; dùng policy an toàn hoặc fail trước dispatch theo use case;
- child fail → parent vẫn có thể complete degraded;
- AI fail → đóng task ngay và parent vẫn có thể complete degraded;
- AI task quá 20 s → bỏ khỏi barrier, degraded=true;
- parent deadline 30 s → finalize signals hiện có;
- duplicate event/result → ACK/ignore, không double-count;
- worker result malformed → reject/DLQ;
- Redis mất → rebuild coordination từ PostgreSQL;
- `q.ai.analyze` message vào DLQ → scan thoát chờ bằng `aiTaskDeadline`, đồng thời cần observable alert;
- RabbitMQ unavailable → API không được giả accepted nếu job chưa publish an toàn.

## 13. Security / privacy

- sensitive fields không đưa vào message nếu không cần;
- event schema versioned;
- queue/internal runtime endpoints không public;
- correlation/event/job/task IDs để audit/trace;
- no raw CCCD in message;
- scanner/AI workers không cần route tới PostgreSQL/Redis/Spring Boot API;
- không đưa credit/payment/subscription state hoặc provider billing credential vào scan/AI message; chỉ propagate non-sensitive logical execution profile.

## 14. Dependencies

- M08 Threat Intelligence;
- M09 Risk Evaluation;
- M11 AI Worker;
- M13 PostgreSQL/Redis/RabbitMQ;
- feature workers M02–M05;
- M14 Audit & Observability.

## 15. Phát triển độc lập / mock contract

Shared test kit:

```text
FakeMessageBus
FakeAccessContextResolver
FakeEntitlementResolver
FakeUsageMeterPort (no-op trong MVP)
InMemoryScanRepository
InMemoryAiTaskRepository
FakeClock
MockThreatQuery
MockRiskEvaluationPort
worker-result fixtures
ai-result fixtures
nested-scan fixtures
race-condition fixtures
```

Các feature module có thể dev với fake scan platform adapter hoặc contract test. M12 có thể test toàn bộ orchestration mà không cần worker/model thật.

## 16. Definition of Done

- consistent lifecycle cho 4 scan types;
- Guest/User ownership + quota policy được enforce trước dispatch;
- MVP không phụ thuộc billing/credit/subscription implementation;
- logical `inferenceProfile` có thể truyền xuống AI path mà không hard-code provider;
- idempotent worker/AI consumers;
- retry/DLQ;
- `q.ai.analyze` + AI routing keys đúng contract;
- child + AI barrier có deadline/race tests;
- nested scan depth/deadline/cycle tests;
- late AI result không mutate final result;
- no infinite PROCESSING;
- contract test với M02–M05/M08/M09/M11.

---

# M13. Platform Infrastructure

## 1. Module summary

| Thuộc tính | Giá trị                                                          |
| ------------ | ------------------------------------------------------------------ |
| Type         | Infrastructure                                                     |
| Actor        | Không có direct user; toàn hệ thống phụ thuộc               |
| Components   | Nginx, PostgreSQL, Redis, RabbitMQ, MinIO/S3, deployment/CI        |
| Goal         | Cung cấp runtime ổn định, bảo mật và local dev reproducible |

## 2. Mục đích

Cung cấp nền tảng runtime chung để các module chạy nhất quán từ local development đến môi trường deploy, đồng thời enforce ranh giới V3.2: core sở hữu business state; scanner/AI worker giao tiếp nội bộ qua RabbitMQ và không chạm PostgreSQL/Redis/core API.

## 3. Góc nhìn người dùng / actor journey

User không thao tác trực tiếp module này. Họ cảm nhận qua:

```text
HTTPS hoạt động ổn định
request không bị lộ internal services
scan chạy nền và không treo vô hạn
file/evidence/export tải được
hệ thống không mất canonical data khi Redis restart
```

Developer cảm nhận qua:

```text
docker compose up
→ có DB/Redis/RabbitMQ/MinIO/Nginx/core/workers
→ chạy module độc lập bằng contract/mock
```

## 4. Phạm vi nghiệp vụ

- Nginx edge gateway;
- PostgreSQL source of truth;
- Redis cache/coordination/quota counters;
- RabbitMQ topic exchange + DLX/DLQ;
- MinIO/S3 object storage;
- network segmentation cho core/scanner worker/AI worker;
- Docker Compose local/dev;
- CI/CD;
- deployment config;
- environment/secrets management;
- health/readiness plumbing;
- observability hooks cho M14.

## 5. UI/UX ownership

Không có product UI trực tiếp.

Admin operational dashboard/metrics nếu triển khai thuộc M14/operations, không phải business UI mặc định của M13.

## 6. API / event contracts

Infrastructure không sở hữu business API.

Network contracts:

```text
Public:
443 -> Nginx

Internal:
Nginx -> Spring Boot
Spring Boot -> PostgreSQL / Redis / RabbitMQ / MinIO
Scanner Workers -> RabbitMQ
URL Worker -> public Internet fetch (SSRF-safe) + RabbitMQ
AI Worker -> RabbitMQ (+ model/LLM provider nếu cấu hình)
Threat Ingestion Worker -> source/feed + PostgreSQL/Redis
Report Export Worker -> RabbitMQ + MinIO/S3
```

DB/Redis/RabbitMQ/MinIO và diagnostics nội bộ không public Internet.

## 7. Backend / worker responsibilities

**Nginx**

- TLS termination;
- reverse proxy;
- routing;
- coarse IP/flood rate limit;
- request size guard.

**PostgreSQL**

- source of truth cho business + threat reference data.

**Redis**

- cache/rate limit/idempotency/temporary coordination only;
- quota counters cho guest session/user/account/API identity future;
- không là source of truth cho future credit/billing ledger.

**RabbitMQ**

- `antiscan.topic`;
- `antiscan.dlx`;
- async scan job/result;
- AI task/result;
- notification/export/ingestion transport;
- retry/DLQ.

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
worker isolation boundaries
object references
runtime/deployment environment
```

## 10. Data ownership

PostgreSQL source-of-truth groups:

```text
users/auth
scan state/result
scan_relations
scan_ai_tasks
rules/policies
reports
threat reference data
admin_actions / audit_records
notification metadata
model_versions metadata

Future Scope khi commercialization được bật:
plans / entitlement profiles
subscriptions / account plan assignment
usage ledger
credit accounts + credit transaction ledger
user/API credentials metadata
```

Redis:

```text
result cache
threat hot cache
business quota (guest/user/account; future API identity)
idempotency
locks
nested scan / AI task temporary barrier
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
- Spring Boot + Redis quota theo guest session/user/account; future có thể thêm API identity;
- future credit/subscription/usage canonical state phải ở PostgreSQL/ledger riêng, **không** lưu duy nhất trong Redis;
- RabbitMQ delivery được xem là at-least-once;
- scanner/AI workers không truy cập PostgreSQL/Redis/core API;
- URL Worker chỉ có thêm outbound public fetch theo SSRF policy;
- production config khác local nhưng contract giữ ổn định.

## 12. Failure / degraded behavior

- Redis down → degrade cache/coordination, không mất canonical data;
- MinIO down → report/export/evidence degrade, core scan vẫn chạy nếu không cần artifact;
- RabbitMQ down → async dispatch bị ảnh hưởng rõ ràng;
- PostgreSQL down → core business không thể đảm bảo correctness, fail fast/health not ready;
- Nginx upstream unavailable → gateway error hợp lý;
- worker/AI DLQ phải có metric/alert, không auto-retry vô hạn.

## 13. Security / privacy

- TLS;
- secrets không commit repo;
- future payment/provider credential và user API key secret phải tách khỏi scan payload/log;
- private network segmentation;
- least privilege DB/broker credentials;
- scanner/AI worker không được cấp DB/cache credential;
- object storage access policy;
- backups;
- log masking;
- SSRF URL Worker isolation;
- diagnostics/health endpoint nội bộ không expose vô kiểm soát.

## 14. Dependencies

Tất cả module phụ thuộc M13 ở mức runtime. M13 cung cấp hooks/telemetry substrate cho M14 nhưng không sở hữu business audit semantics.

## 15. Phát triển độc lập / mock contract

Local stack:

```text
Docker Compose
├── PostgreSQL
├── Redis
├── RabbitMQ
├── MinIO
├── Spring Boot
├── URL/Text/Entity/QR workers
├── Python AI Worker
├── Threat Ingestion Worker
├── Report Export Worker
├── Next.js
└── Nginx
```

Các module có thể dùng Testcontainers/fake adapters khi không muốn bật toàn stack.

## 16. Definition of Done

- local environment reproducible;
- only intended public ports exposed;
- worker network isolation test/config rõ ràng;
- health/readiness checks;
- persistent volumes/backups theo môi trường;
- RabbitMQ exchange/DLX/DLQ reproducible;
- CI build/test image flow;
- secrets/config management rõ ràng;
- infra failure behavior được document/test ở mức phù hợp.

---

# M14. Audit & Observability

## 1. Module summary

| Thuộc tính | Giá trị                                                               |
| ------------ | ----------------------------------------------------------------------- |
| Type         | Shared Platform / Admin Feature                                         |
| Actor        | Admin, Internal Operations                                              |
| UI           | Admin Dashboard / operational view nếu triển khai                     |
| Backend      | Audit & Observability component trong Spring Boot                       |
| Data owner   | `admin_actions`, `audit_records` và operational metadata phù hợp |
| Consumers    | M01, M07–M13 và các core scan flows                                  |

## 2. Mục đích

Tập trung audit trail và metadata vận hành để các hành động quản trị, lỗi scan, retry/DLQ, correlation IDs và sự kiện quan trọng có thể truy vết mà không để từng module tự định nghĩa log/audit theo một kiểu khác nhau.

## 3. Góc nhìn người dùng / actor journey

End-user không thao tác trực tiếp module này.

**Admin / người vận hành**:

```text
Mở Audit / Operations
→ lọc theo thời gian / actor / scanId / correlationId / eventId
→ xem admin action hoặc scan/worker failure
→ lần theo retry / DLQ / late AI result khi cần
```

## 4. Phạm vi nghiệp vụ

- audit admin actions;
- audit rule/policy changes;
- audit moderation actions;
- scan/worker/AI failure metadata;
- retry/DLQ metadata;
- correlation IDs / event IDs / job IDs / task IDs;
- late AI result metadata;
- operational log/metric hooks;
- query/audit view cho admin nếu scope triển khai.

## 5. UI/UX ownership

Nếu expose trong Admin Dashboard:

- audit list;
- filter theo actor/action/resource/time;
- filter theo `scanId`, `correlationId`, `eventId`, `taskId`;
- detail view;
- failure/retry/DLQ context.

Không phải user-facing feature.

## 6. API / event contracts

Architecture V3.2 chưa chốt public endpoint cụ thể cho audit. Nếu triển khai Admin API, phải coi đây là contract đề xuất và dùng RBAC.

Canonical correlation fields dùng xuyên pipeline:

```text
correlationId
eventId
jobId
taskId (AI)
scanId
processorVersion
eventVersion
```

## 7. Backend / worker responsibilities

**Spring Boot Audit & Observability component**

- nhận audit event/domain metadata từ các module;
- persist/query audit record phù hợp;
- liên kết scan/admin action với correlation identifiers;
- expose operational metadata cho admin/monitoring nếu có.

**Worker**

- phát đủ IDs/version/status trong message/log;
- không tự sở hữu business audit table.

## 8. Input

```text
admin action
report review
rule/policy change
scan failure
worker retry/DLQ metadata
AI task failure / late result
correlation/event/job/task identifiers
```

## 9. Output

```text
AuditRecord
AdminAction record
operational log/metric metadata
traceable correlation chain
```

## 10. Data ownership

```text
admin_actions
audit_records
```

Log/metric backend cụ thể chưa được Architecture V3.2 bắt buộc; M13 chỉ cung cấp runtime hooks.

## 11. Business rules / invariants

- audit record phải gắn actor/resource/time khi có;
- message-driven flow phải trace được bằng correlation/event/job/task IDs;
- audit không được chứa raw password/token/CCCD hoặc dữ liệu nhạy cảm không cần thiết;
- retry/duplicate event không được tạo business effect lần hai dù vẫn có thể được quan sát trong operational telemetry.

## 12. Failure / degraded behavior

Architecture V3.2 chưa chốt policy chi tiết cho trường hợp audit sink unavailable. Implementation phải xác định rõ hành động nào bắt buộc audit đồng bộ và hành động nào có thể ghi operational telemetry theo eventual/best-effort, tránh tự giả định trong từng module.

## 13. Security / privacy

- Admin/operations RBAC;
- mask/redact sensitive fields;
- audit access itself cần được kiểm soát;
- không log secret/token/raw CCCD;
- retention phải theo policy của hệ thống.

## 14. Dependencies

- M01 Identity/RBAC;
- M12 correlation/lifecycle metadata;
- M13 PostgreSQL/runtime/logging infrastructure.

## 15. Phát triển độc lập / mock contract

```text
AuditPort
InMemoryAuditPort
mock-admin-action.json
mock-worker-failure.json
mock-ai-late-result.json
```

Các module khác chỉ phụ thuộc `AuditPort`/event contract, không cần chờ màn hình audit hoàn thành.

## 16. Definition of Done

- admin/security-sensitive action traceable;
- scan/worker/AI failure có correlation metadata;
- retry/DLQ/late-AI có thể truy vết;
- sensitive fields được mask;
- các module không tự tạo schema audit riêng.

---

# Shared Contracts

## Access & entitlement context

```text
AccessContext
- actorType = GUEST | AUTHENTICATED_USER | ADMIN | API_CLIENT(future)
- subjectId = guestSessionId | userId | apiClientId(future)
- roles[]
- authenticated

EntitlementContext
- planKey = guest | free | <future extensible key>
- quotaProfile
- inferenceProfile
- featureFlags[]
- historyPolicy
```

MVP chỉ cần `guest` và `free`. `PREMIUM`, subscription, credit, usage billing và user-facing API key là **Future Scope**, nhưng contract giữ extension seam để M12 không phải đổi orchestration khi các capability này được thêm. Credit/payment state không đi vào worker message.

## Scan types

```text
URL
TEXT
ENTITY
QR

TEXT.contentType = MESSAGE | TRANSACTION_POST
ENTITY.entityType = PHONE | BANK_ACCOUNT
URL internal mode = WEB_CONTENT / HTML_FORM
DOMAIN = internal DerivedIndicator type, không phải public ENTITY
```

`CCCD` không phải standalone lookup trong MVP. Text/Web chỉ phát hiện yêu cầu/thu thập CCCD như sensitive-data signal.

## Scan lifecycle

```text
PENDING -> PROCESSING -> COMPLETED
                     \-> FAILED

COMPLETED + degraded=true
```

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
  "type": "DOMAIN",
  "normalizedValue": "login-vcb.example",
  "sourceScanId": "scan_parent",
  "depth": 1,
  "handling": "REPUTATION_ONLY"
}
```

`handling`:

```text
REPUTATION_ONLY -> core tra reputation và gắn signal, không tạo child scan
CHILD_SCAN      -> core enrich rồi tạo scan con nếu policy/depth cho phép
```

`handling` là hint của worker; Orchestrator có quyền điều chỉnh theo policy và `maxDepth`.

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

## Generic worker result contract

```json
{
  "eventId": "evt_123",
  "correlationId": "corr_123",
  "eventVersion": "1.0",
  "jobId": "job_123",
  "attempt": 1,
  "scanId": "scan_123",
  "parentScanId": null,
  "scanType": "URL",
  "status": "ANALYZED",
  "signals": [],
  "derivedIndicators": [
    {
      "type": "DOMAIN",
      "normalizedValue": "login-vcb.example",
      "handling": "REPUTATION_ONLY"
    }
  ],
  "pendingAiTasks": [
    {
      "taskId": "aitask_abc",
      "kind": "URL_FEATURES"
    }
  ],
  "processorVersion": "url-worker-v1",
  "processedAt": "..."
}
```

Worker không dùng AI để `pendingAiTasks=[]`. `modelPrediction` không còn nằm trong generic worker result của V3.2.

## AI task contract

```json
{
  "eventId": "evt_ai_req_1",
  "correlationId": "corr_123",
  "eventVersion": "1.0",
  "taskId": "aitask_abc",
  "scanId": "scan_123",
  "parentScanId": null,
  "kind": "URL_FEATURES",
  "inferenceProfile": "standard",
  "attempt": 1,
  "payload": {
    "normalizedUrl": "https://example.com/login",
    "features": {},
    "contentExcerpt": null
  },
  "requestedBy": "url-worker-v1",
  "requestedAt": "...",
  "deadlineAt": "..."
}
```

`inferenceProfile` là logical execution profile đã được core policy resolve. MVP dùng `standard`; future có thể thêm profile khác theo entitlement/credit policy. Field này **không phải provider/model name** và không chứa billing state. M11 map profile + task kind sang model/provider qua registry.

## AI result contract

```json
{
  "eventId": "evt_ai_res_1",
  "correlationId": "corr_123",
  "eventVersion": "1.0",
  "taskId": "aitask_abc",
  "scanId": "scan_123",
  "status": "COMPLETED",
  "prediction": {
    "label": "PHISHING",
    "probability": 0.87,
    "providerKey": "openrouter",
    "modelId": "openai/gpt-5",
    "modelVersion": "prompt-v1",
    "latencyMs": 420
  },
  "failureReason": null,
  "processorVersion": "ai-worker-v1",
  "processedAt": "..."
}
```

`providerKey`, `modelId`, `modelVersion` là **trace metadata** của đường inference/backend đã thực thi. `providerKey` có thể là direct provider (`openai-direct`, `gemini-direct`), gateway/aggregator (`openrouter`) hoặc local runtime (`hf-local`, `custom-local`). Consumer downstream phải dựa trên normalized prediction semantics, không được hard-code business rule theo tên provider/model. Với gateway/aggregator, metadata upstream cụ thể nếu có chỉ là optional diagnostics, không phải business contract.

Failure contract:

```text
status = FAILED
prediction = null
failureReason = TIMEOUT | MODEL_UNAVAILABLE | INVALID_PAYLOAD | INTERNAL_ERROR
```

## RabbitMQ topology

```text
Exchange: antiscan.topic
DLX: antiscan.dlx

q.scan.url
q.scan.text
q.scan.entity
q.scan.qr
q.ai.analyze
q.scan.result
q.report.export
q.notification
q.threat.ingest
```

Core AI routing:

```text
ai.analysis.requested
ai.analysis.completed
ai.analysis.failed
```

Delivery được xem là at-least-once. Consumer phải idempotent; worker retry tối đa 2 lần sau attempt đầu rồi DLQ.

## Completion barrier

```text
PostgreSQL scan_relations = source of truth cho child scan
PostgreSQL scan_ai_tasks  = source of truth cho AI task
Redis = temporary coordination

parent deadline = 30 s
AI task deadline = 20 s
maxDepth = 2
```

Finalize khi mọi child + AI task terminal hoặc khi deadline tương ứng hết. Missing AI/reputation signal được xử lý bằng degraded metadata + Risk Fusion renormalization, không coi missing = 0.

## Operational defaults của MVP

| Hạng mục                                                 |               Default ban đầu |
| ---------------------------------------------------------- | ------------------------------: |
| Parent/nested scan deadline                                |                            30 s |
| Max nested depth                                           |                               2 |
| Reputation lookup timeout trong core (Redis → PostgreSQL) |                          500 ms |
| AI task deadline — Orchestrator chờ                      |                            20 s |
| AI model/LLM call timeout — AI Worker                     | 10 s, đã tính retry nội bộ |
| AI Worker concurrency (`prefetch_count`)                 |             4 task đồng thời |
| Worker retry                                               |       2 retry sau attempt đầu |
| URL redirect limit                                         |                               5 |
| URL fetched body                                           |                            5 MB |
| Text input                                                 |                  20.000 ký tự |
| Result cache TTL                                           |                        10 phút |
| Threat cache TTL                                           | theo source; mặc định 1 giờ |

> Các giá trị trên là engineering defaults của MVP, chưa phải SLA cuối cùng.
