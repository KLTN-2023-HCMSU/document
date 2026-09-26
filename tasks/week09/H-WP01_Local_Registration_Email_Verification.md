# H-WP01 — Local Registration & Email Verification

**Owner:** Hùng  
**Module:** M01 Account & Identity  
**Cycle:** W01 · 06/10/2026 → 12/10/2026  
**Feature IDs:** M01-F001…F005  
**Release target:** MVP v0.1.0


> **Nguồn chuẩn:** Architecture V3.3 · Modules Specification V1.3 · Schema V1.0 Draft · Feature Backlog V2.  
> Nếu tài liệu task này mâu thuẫn với các tài liệu chuẩn trên, **tài liệu chuẩn thắng**. Các mục ghi “Working decision” là quyết định triển khai tạm thời cho sprint và phải được review nếu muốn đưa vào contract chuẩn.


## 1. Outcome cần đạt

User đăng ký Local bằng email/password, credential được hash đúng boundary, email được canonicalize/unique, và user có thể xác minh email bằng OTP/challenge do **M01 sở hữu**. M10 chỉ deliver qua `MockNotificationPort` trong cycle này.

## 2. Scope / Non-scope

**IN:** register Local, email canonicalization, password hash, verification challenge request/confirm, resend policy/rate limit, verified state.  
**OUT:** login/session token issue (H-WP03 đã là dependency), Google OIDC, forgot/reset password, SMTP/provider thật.

## 3. Input

Register working contract:

```json
{
  "email": "User@Example.com ",
  "password": "<raw>",
  "fullName": "...",
  "displayName": "..."
}
```

`dateOfBirth`/`avatarUrl` có thể update profile sau; không bắt buộc cho registration tối thiểu trừ khi team đã freeze khác.

Verification confirm:

```json
{
  "challengeId": "...",
  "code": "123456"
}
```

## 4. Output

Register:

```text
user/account summary
emailVerified=false (ban đầu nếu Local mới)
verification challenge requested/delivered status
```

Confirm success:

```text
emailVerified=true / email_verified_at set
challenge consumed
```

Không trả password hash hoặc OTP hash.

## 5. Data ownership

```text
users
local_credentials
auth_verification_challenges
```

`users` không chứa `password_hash`.

Challenge canonical fields:

```text
id, user_id, purpose,
target_email, code_hash,
attempt_count, max_attempts=5,
expires_at, verified_at?, consumed_at?, created_at
```

## 6. Business flow

### Register

```text
POST /v1/auth/register
→ trim/lowercase email
→ validate email/password/profile minimum
→ check email unique case-insensitive
→ create users
→ hash password → local_credentials
→ create EMAIL_VERIFICATION challenge
→ hash OTP, persist challenge
→ NotificationPort.send(...)
→ neutral/safe response
```

### Confirm email

```text
POST /v1/auth/email-verification/confirm
→ load challenge
→ validate purpose/expiry/attempts/not consumed
→ compare OTP hash
→ incorrect: increment attempts
→ correct: mark verified + consumed atomically
→ set users.email_verified_at
```

### Resend

```text
request resend
→ rate-limit
→ invalidate/supersede prior active challenge
→ create new challenge/code
→ send via NotificationPort
```

## 7. Business rules

1. `users.email` canonicalize trước persistence/lookup và unique case-insensitive.
2. Không có `username` trong MVP.
3. Password hash chỉ ở `local_credentials`.
4. Raw password không persist/log.
5. Raw OTP không persist/log; chỉ `code_hash`.
6. Challenge có expiry, max attempts, single-use.
7. M10 delivery failure **không** được làm account tự verified.
8. Resend phải bounded/rate-limited và supersede code cũ theo policy.

## 8. Working decisions

- Password hashing: backlog yêu cầu BCrypt với cost phù hợp; cost phải config, không hard-code vào business code.
- OTP length/TTL chưa được canonical docs chốt. Đưa thành config và ghi rõ test values; không biến giá trị test thành business invariant.

## 9. Failure behavior

| Case | Expected |
|---|---|
| email duplicate | business validation error, không tạo credential mới |
| weak/invalid password | 4xx validation |
| wrong OTP | increment attempt, không verify |
| max attempts | challenge unusable |
| expired/consumed | reject; user request challenge mới |
| notification delivery fail | challenge/account state vẫn đúng, cho resend bounded |

## 10. Mock / independence

```text
MockNotificationPort
FakeClock
In-memory/fake repo nếu chưa nối DB
```

Không chờ M10 provider thật hoặc Google.

## 11. Test matrix

- email trim/lowercase + duplicate case-insensitive.
- password raw không log/persist.
- password hash verify thành công.
- challenge row chỉ có code_hash.
- wrong OTP attempt count.
- expiry/max attempts/single-use.
- resend làm old challenge unusable.
- delivery failure không set verified.
- confirm success set `email_verified_at`.

## 12. Acceptance criteria

- [ ] `POST /v1/auth/register` chạy.
- [ ] email canonicalize + unique.
- [ ] BCrypt/configurable work factor.
- [ ] password hash chỉ ở `local_credentials`.
- [ ] EMAIL_VERIFICATION challenge hash/expiry/attempt/single-use.
- [ ] request/confirm/resend flows có tests.
- [ ] MockNotificationPort contract pass.
- [ ] no raw password/OTP in logs.
- [ ] migration từ DB rỗng chạy được.
- [ ] PR/test/demo evidence dán tracker.
