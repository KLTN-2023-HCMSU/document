# K-WP09 — Data Privacy & Secret Baseline

**Owner:** Khải  
**Module:** M13 Platform Infrastructure  
**Cycle:** W09 · 27/09/2026 → 05/10/2026  
**Feature IDs:** M13-F001, M13-F002, M13-F005, M13-F035  
**Release target:** MVP v0.1.0

> **Nguồn chuẩn:** Architecture V3.3 · Modules Specification V1.3 · Schema V1.0 Draft · Feature Backlog V2. Nếu task này mâu thuẫn tài liệu chuẩn, tài liệu chuẩn thắng.

## 1. Outcome cần đạt

Tạo baseline bảo mật dữ liệu/secret dùng chung để các module có thể code và CI/CD mà **không hard-code hoặc commit credential**, đồng thời có convention masking cho dữ liệu nhạy cảm.

## 2. Scope / Non-scope

**IN:** env templates, secret naming convention, local/dev secret injection, JWT/OIDC/AI/notification credentials, log masking baseline, secret scan/pre-commit/CI check tối thiểu.  
**OUT:** business auth logic; rotate key production hoàn chỉnh; vault/KMS enterprise; application-specific audit policy.

## 3. Input

```text
Architecture V3.3 secret boundaries
Schema V1.0 rule: secret/token only hash or external secret config
Service inventory: Spring Boot, scanner worker, AI worker, notification worker, web/admin
```

## 4. Output

```text
.env.example / config template
secret naming convention
local secret injection guide
masking helper/convention
CI secret-leak check baseline
README: secret nào thuộc service nào
```

Không commit `.env` thật, signing key, OAuth client secret, provider API key hoặc raw OTP/token.

## 5. Business / operational flow

```text
Developer clone repo
→ copy env template
→ điền local secret ngoài git
→ docker/app đọc config qua environment/secret mount
→ log filter redact field nhạy cảm
→ CI scan repository / config
→ phát hiện secret thật → pipeline fail
```

## 6. Logic / invariants

1. Không secret thật trong source, fixture public, Docker image layer hoặc business table.
2. `users`, `notification_jobs`, audit và DLQ không được chứa raw password/OTP/token/provider credential.
3. Refresh/API credential nếu cần persistence chỉ lưu hash theo owning module.
4. Log masking chạy trước sink; không “log rồi mới xóa”.
5. `.env.example` chỉ chứa placeholder.
6. Tên biến config phải ổn định để CI/CD dùng cùng contract.

## 7. Suggested config keys

```text
JWT_SIGNING_KEY / JWT_KEY_ID
GOOGLE_OIDC_CLIENT_ID
GOOGLE_OIDC_CLIENT_SECRET (nếu flow cần)
AI_PROVIDER_*_API_KEY
NOTIFICATION_PROVIDER_*_SECRET
DB_PASSWORD
REDIS_PASSWORD (nếu bật)
RABBITMQ_PASSWORD
MINIO_ROOT_PASSWORD
```

Tên cuối cùng phải theo convention repo, không bắt buộc y hệt danh sách trên.

## 8. Failure behavior

| Case | Expected |
|---|---|
| thiếu secret bắt buộc | service fail fast/readiness DOWN |
| placeholder được dùng ở prod | deploy fail validation |
| secret xuất hiện trong log | test/scan fail |
| secret bị commit | CI fail + rotate credential nếu là secret thật |

## 9. Mock / dependency boundary

Task này không cần business module hoàn chỉnh. Có thể dùng dummy local credentials và test container. Owner khác chỉ phụ thuộc vào **tên config + cách inject**, không phụ thuộc implementation nội bộ M13.

## 10. Test matrix tối thiểu

- repo secret scan với sample fake-secret pattern;
- `.env.example` không có credential thật;
- Spring Boot/worker đọc config từ env;
- missing required secret → startup/readiness fail rõ;
- masking test cho phone/account/token-like fields;
- build image không bake `.env` thật.

## 11. Acceptance criteria

- [ ] Có env/secret template dùng chung.
- [ ] Không có secret thật trong git/business DB/log fixture.
- [ ] Có validation fail-fast cho secret bắt buộc.
- [ ] Có masking baseline cho log nhạy cảm.
- [ ] CI có bước phát hiện secret leak tối thiểu.
- [ ] Owner M01/M11/M10 dùng được config contract mà không chờ Khải sửa code module họ.
- [ ] Dán PR/test evidence vào tracker.

## 12. Evidence để tick Done

```text
PR config/secret baseline
+ CI secret-scan output
+ grep/scan chứng minh không có secret thật
+ demo service start bằng env và fail khi thiếu secret
```
