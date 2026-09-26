# H-MVP02 — Session, Guest, RBAC, Basic Profile & Quota

| Thuộc tính | Giá trị |
|---|---|
| Week | **W10 · 06/10–12/10/2026** |
| Owner | **Hùng** |
| Source WP | `H-WP03` + `H-WP02 (basic)` + `H-WP04 (basic)` + `H-WP05 (basic)` + `H-WP07 (basic)` |
| Upstream | **H-MVP01** |
| Downstream chính | M12 access/quota, M06 history ownership, Community/Admin flows |
| Mục tiêu tuần | Verified Local user → session/token lifecycle; Guest context; RBAC; profile/quota baseline |

## Tài liệu ràng buộc

Đặc tả này là **implementation slice cho Timeline V2.1**, không thay đổi danh sách tính năng hay kiến trúc. Khi có xung đột, áp dụng thứ tự nguồn hiện tại của dự án:

1. `architecture_v3.3.md`
2. `modules_specification_v1.3.md`
3. `anti_scam_schema_overall_v1.0.md` *(Draft)*
4. `Anti_Scam_Feature_Backlog_V2`
5. `Timeline_full_Anti_Scam_2026_2027_V2.1.md`

> Một task `*-MVPxx` là **MVP implementation slice** lấy từ một hoặc nhiều WP. Hoàn thành task **không đồng nghĩa toàn bộ source WP đã hoàn thành** nếu các feature nâng cao/P1/P2 được ghi là deferred.


## 1. Mô tả

W10 biến identity foundation thành **usable access model** cho toàn hệ thống. Local user đã verify từ W09 phải login được, nhận Anti-Scam access JWT + rotating opaque refresh token, logout/revoke được và phát hiện refresh-token reuse. Đồng thời cung cấp `GuestAccessContext`, RBAC `USER/MODERATOR/ADMIN`, profile read/update cơ bản và quota/rate-limit baseline để M12 có thể dispatch scan theo actor policy.

Google OIDC implementation đầy đủ được schedule ở W12; W10 chỉ giữ auth-method normalization seam.

## 2. Traceability / phạm vi feature

| Feature | Mức trong task | Ghi chú |
|---|---|---|
| `M01-F008`–`M01-F012`, `M01-F041` | **Required** | session/JWT/refresh rotation/reuse/logout/client storage |
| `M01-F013`, `M01-F014` | Deferred | logout-all/session-management UI depth |
| `M01-F007`, `M01-F019` | **Required basic** | Guest + normalized AuthResult/AccessContext |
| `M01-F006`, `M01-F020` | Deferred to W12/depth | Google OIDC/account linking |
| `M01-F015` | **Required** | profile read |
| `M01-F017` | Basic | profile update smoke path |
| `M01-F016`, `M01-F018` | Deferred depth | password-change/reset flow |
| `M01-F021`–`M01-F023` | **Required** | RBAC backend + UI guard |
| `M01-F034`–`M01-F036` | **Required basic** | login/register rate-limit + guest/free quota |

## 3. Input / Output

### Input

```text
POST /v1/auth/login: email + password
POST /v1/auth/refresh: refresh credential
POST /v1/auth/logout: current session context
GET/PATCH /v1/users/me
Authenticated request: JWT access token
Guest request: temporary guest context/session proof
```

### Output

```text
AuthResult
- accessToken (JWT short-lived)
- refreshCredential (opaque rotating; Web có thể qua HttpOnly cookie)
- sessionId / sid
- user / roles
- AccessContext

UserProfile
EntitlementContext (guest/free baseline)
401 / 403 / quota errors chuẩn hoá
```

## 4. Business rules / invariants

- Access JWT tối thiểu mang `iss`, `aud`, `sub`, `sid`, `roles[]`, `iat`, `exp`.
- Raw access token không persist; refresh DB chỉ lưu hash.
- Mỗi refresh thành công rotate token; old token reuse → revoke entire family + session và trả 401.
- Authenticated request verify JWT rồi resolve `sid`; revoked/expired session phải bị reject ngay cả khi JWT chưa hết `exp`.
- Web không lưu refresh token trong `localStorage`; Mobile không lưu refresh trong AsyncStorage thường.
- Guest được scan public inputs, quota thấp hơn, không persistent account history.
- Backend RBAC là authority; route guard UI chỉ là UX layer.
- `USER`, `MODERATOR`, `ADMIN` là role MVP.
- Quota check business nằm application/Redis; Nginx chỉ coarse IP/flood limit.

## 5. Logic flow

### Login

```text
email + password
→ canonicalize email
→ verify local credential
→ require account state hợp lệ
→ create auth_session
→ issue JWT access token(sid, roles)
→ generate opaque refresh token
→ persist refresh token hash/family metadata
→ return AuthResult
```

### Refresh

```text
refresh credential
→ hash lookup
→ validate session/family/token state
→ if consumed/replaced token appears again: REUSE
   → revoke family + auth_session
   → 401 + security notification request
→ else atomically consume old token
→ create replacement token same family
→ issue fresh access JWT
```

### Authenticated request

```text
JWT
→ verify signature/issuer/audience/expiry
→ resolve sid via Redis cache, fallback PostgreSQL
→ ACTIVE: build AccessContext
→ REVOKED/EXPIRED: 401
```

### Guest

```text
no account login
→ resolve ephemeral GuestAccessContext
→ planKey=guest / lower quota / standard inference profile
→ M12 may accept public scan
```

## 6. API / contract

```text
POST /v1/auth/login
POST /v1/auth/refresh
POST /v1/auth/logout
GET  /v1/users/me
PATCH /v1/users/me
```

Internal:

```text
AccessContext
AuthenticatedPrincipal
EntitlementContext
```

## 7. Data / cache

Canonical:

```text
users
local_credentials
auth_sessions
refresh_tokens
roles
user_roles
```

Redis:

```text
session/revocation lookup cache
login/register rate-limit counters
Guest/User quota counters
```

PostgreSQL vẫn là source of truth.

## 8. Failure / security cases

- wrong password → neutral auth failure; rate-limit count tăng.
- revoked/expired session → 401.
- refresh token reuse → revoke family + session.
- user role change cần hiệu lực ngay → revoke relevant session theo policy.
- guest vượt quota → reject trước scan dispatch.
- USER gọi admin route → 403.
- profile update không được đổi protected fields/roles qua user endpoint.

## 9. Phát triển độc lập

- M12 dùng `FakeAccessContext`/`FakeEntitlementContext` nếu H-MVP02 chưa merge.
- H-MVP02 không phụ thuộc scanner worker.
- Security notification có thể đi qua `MockNotificationPort` đã freeze W09.

## 10. Test plan

### E2E auth

```text
register(W09) → verify → login → authenticated /me
→ refresh → old refresh reuse attempt → 401 + session revoked
→ login again → logout → same sid rejected
```

### RBAC / Guest

- Guest context scan permission contract.
- USER → admin route = 403.
- MODERATOR/Admin role claims/context đúng.
- guest/free quota profile khác nhau.

### Profile

- GET /me đúng fields.
- PATCH basic profile update không mutate email/roles trái policy.

## 11. Acceptance Criteria

- [ ] Verified Local user login được và nhận session + short-lived JWT + opaque refresh credential.
- [ ] Refresh rotate atomically; raw refresh token không persist/log.
- [ ] Refresh reuse revoke token family + auth_session và trả 401.
- [ ] Logout revoke current session; access JWT cùng sid bị reject.
- [ ] Guest AccessContext chạy không cần account và có lower quota/no persistent history.
- [ ] RBAC USER/MODERATOR/ADMIN được enforce backend; USER bị chặn admin route.
- [ ] GET/PATCH basic profile smoke pass.
- [ ] Login/register rate limit + business quota baseline hoạt động.
- [ ] Web/Mobile token storage policy không dùng insecure persistent storage.
- [ ] Register→verify→login→refresh→logout integration test xanh.

## 12. Deliverables

- Session/token services + persistence/cache adapters.
- Auth endpoints + AccessContext/Entitlement resolver.
- RBAC guard + client route guard baseline.
- Profile read/update basic.
- Quota/rate-limit baseline.
- Security + integration tests.

## 13. Handoff

M12 nhận **resolved `AccessContext`/`EntitlementContext`**, không biết user đăng nhập bằng Local hay provider nào. M06 dùng ownership context để bảo vệ result/history.
