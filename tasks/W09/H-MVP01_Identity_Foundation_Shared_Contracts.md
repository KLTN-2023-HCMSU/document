# H-MVP01 — Identity Foundation + Shared Delivery/Audit Contracts

| Thuộc tính | Giá trị |
|---|---|
| Week | **W09 · 27/09–05/10/2026** |
| Owner | **Hùng** |
| Source WP | `H-WP01` + `H-WP10 (contract)` + `H-WP16 (contract)` |
| Upstream | Team contract freeze |
| Downstream chính | `H-MVP02`, `I-MVP01/I-MVP02`, Community/Notification/Audit ở W11–W12 |
| Mục tiêu tuần | Có Local registration + email verification chạy thật và freeze contract Notification/Audit để team dev độc lập |

## Tài liệu ràng buộc

Đặc tả này là **implementation slice cho Timeline V2.1**, không thay đổi danh sách tính năng hay kiến trúc. Khi có xung đột, áp dụng thứ tự nguồn hiện tại của dự án:

1. `architecture_v3.3.md`
2. `modules_specification_v1.3.md`
3. `anti_scam_schema_overall_v1.0.md` *(Draft)*
4. `Anti_Scam_Feature_Backlog_V2`
5. `Timeline_full_Anti_Scam_2026_2027_V2.1.md`

> Một task `*-MVPxx` là **MVP implementation slice** lấy từ một hoặc nhiều WP. Hoàn thành task **không đồng nghĩa toàn bộ source WP đã hoàn thành** nếu các feature nâng cao/P1/P2 được ghi là deferred.


## 1. Mô tả

Task này tạo **identity foundation** cho MVP. W09 phải cho phép tạo Local account, chuẩn hoá email, hash password và xác thực email bằng challenge/OTP do M01 sở hữu. M10 chỉ làm delivery; việc verify OTP/challenge vẫn thuộc M01. Đồng thời freeze hai cross-module boundary tối thiểu là `NotificationPort` và `AuditPort` để các module khác có thể dùng mock/in-memory implementation mà không chờ notification/audit thật.

W09 **chưa cần** hoàn thiện login/session/refresh/RBAC; các phần đó thuộc `H-MVP02` ở W10.

## 2. Traceability / phạm vi feature

| Feature | Mức trong task | Ghi chú |
|---|---|---|
| `M01-F001` | **Required** | Local registration + validation |
| `M01-F002` | **Required** | BCrypt; password hash chỉ trong `local_credentials` |
| `M01-F003` | **Required** | canonical email `trim/lowercase`, unique |
| `M01-F004` | **Required** | email verification challenge: hash/expiry/attempt/single-use |
| `M01-F005` | Deferred | resend/rate-limit nâng cao có thể hoàn thiện sau |
| `M10-F008` | Contract only | chuẩn hoá producer event thành `notification.requested` |
| `M10-F010`–`M10-F012` | **Contract required** | auth notification types, `NotificationRequest`, channel/provider abstraction |
| `M14-F001`–`M14-F003`, `M14-F011` | Contract only | vocabulary + AuditPort boundary; persistence đầy đủ ở W12 |

## 3. Input / Output

### Input

**Register command**

```text
email
password
profile tối thiểu theo M01
```

**Email verification confirm**

```text
challenge reference / user reference
OTP/code do user nhập
```

**Internal delivery request**

```text
NotificationRequest
- eventId
- correlationId
- notificationType
- recipient
- channelHints[]
- templateKey
- templateData
- resource/deep-link
- priority
- idempotencyKey
- expiresAt
```

### Output

```text
Registration result
- account/user reference
- verification state

ChallengeResult / ChallengeStatus
- valid / expired / exhausted / consumed

NotificationPort contract result
AuditPort contract result
```

**Không output token/session ở task này.** Session/token bắt đầu ở W10.

## 4. Business rules / invariants

- Email phải canonicalize trước lookup/persist; MVP không có `username` unique.
- Password raw không persist và không log; chỉ lưu BCrypt hash trong `local_credentials`.
- Raw OTP không persist; lưu hash + expiry + attempt count + single-use state.
- Challenge thuộc M01; M10 không có quyền tự verify/consume challenge.
- Delivery fail **không được** làm challenge trở thành verified.
- Không log password, OTP, refresh/access token hay provider assertion.
- `NotificationPort` phải provider/channel agnostic; producer không hard-code email provider.
- `AuditPort` ở W09 chỉ cần boundary đủ để consumer gọi được; persistence/search UI chưa phải scope.

## 5. Logic flow

```text
POST /v1/auth/register
→ validate email/password/profile
→ canonicalize email
→ check duplicate account
→ create users row (unverified)
→ create local_credentials(password_hash)
→ create EMAIL_VERIFICATION challenge(code_hash, expiry, attempts)
→ call NotificationPort
→ return neutral registration/verification state

POST /v1/auth/email-verification/confirm
→ load active challenge
→ reject expired / exhausted / consumed
→ compare OTP with hash
→ increment attempt nếu sai
→ nếu đúng: consume challenge atomically
→ set emailVerifiedAt
→ return verified state
```

## 6. API / port / data contract cần chốt

### HTTP

```text
POST /v1/auth/register
POST /v1/auth/email-verification/request
POST /v1/auth/email-verification/confirm
```

### Internal ports

```text
NotificationPort.request(NotificationRequest)
AuditPort.append(SecurityOrBusinessAuditEvent)
```

### Canonical data chạm tới

```text
users
local_credentials
auth_verification_challenges
```

`auth_sessions` và `refresh_tokens` **không phải deliverable W09**.

## 7. Failure / security cases

- Email đã tồn tại → error chuẩn hoá, không tạo duplicate.
- OTP sai → tăng attempt; không verify account.
- OTP hết hạn / consumed → reject.
- Notification provider unavailable → challenge vẫn tồn tại theo policy nhưng không tự verified; response phải phản ánh delivery failure theo contract.
- DB transaction fail giữa account/challenge → không để trạng thái nửa vời.
- Sensitive value tuyệt đối không xuất hiện trong log/audit/event payload dài hạn.

## 8. Phát triển độc lập / mock strategy

- Dùng `MockNotificationPort` để test register/verify trước khi M10 implementation hoàn chỉnh.
- Dùng `InMemoryAuditPort`/no-op audit adapter để consumer compile/test.
- Frontend có thể dùng fixture `pending verification` / `verified account` mà không chờ W10 session.
- Không yêu cầu bất kỳ module scan nào gọi Auth internals; downstream chỉ consume `AccessContext` contract từ W10.

## 9. Test plan

### Unit

- email canonicalization;
- password validation + BCrypt verify;
- challenge hash compare;
- expiry / max-attempt / single-use;
- duplicate email.

### Integration

```text
register → challenge created → notification request emitted
register → confirm correct OTP → emailVerifiedAt set
confirm same OTP lần 2 → rejected
expired OTP → rejected
notification failure → account không tự verified
```

### Contract

- `NotificationRequest` fixture deserialize/serialize được;
- M01↔M10 contract không cho M10 verify challenge;
- Audit event không chứa raw credential.

## 10. Acceptance Criteria

- [ ] Local account đăng ký được bằng email + password + profile tối thiểu.
- [ ] Email được canonicalize và uniqueness được enforce.
- [ ] Password chỉ lưu BCrypt hash; test/log không lộ raw password.
- [ ] EMAIL_VERIFICATION challenge có hash, expiry, attempt và single-use semantics.
- [ ] Confirm OTP đúng set email verified; OTP sai/hết hạn/đã consume bị reject.
- [ ] NotificationPort contract được freeze và có mock adapter.
- [ ] AuditPort contract tối thiểu được freeze và không chứa sensitive credentials.
- [ ] Register/verify integration tests xanh.
- [ ] Task không tạo session/token sớm hơn H-MVP02.

## 11. Deliverables

- Register + email verification implementation.
- Migration/repository cần thiết cho user/local credential/challenge theo schema hiện hành.
- Notification/Audit contract + fixtures/mocks.
- Unit/integration/contract tests.
- Evidence: PR + test result + demo register/verify.

## 12. Handoff sang W10

`H-MVP02` phải nhận được **verified Local user** làm input để test login/session/refresh. Các module khác chưa cần biết Local/Google; W10 chỉ expose `AccessContext` chuẩn hoá.
