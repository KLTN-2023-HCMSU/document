# TEAM-C02 — Integration Checkpoint 1

**Owner:** Cả nhóm  
**Cycle:** W10 · 06/10/2026 → 12/10/2026  
**Goal:** chứng minh workstream ghép được bằng contract **và chạy qua CI/local platform baseline**.

## 1. Outcome cần đạt

Chạy được hai smoke flows bằng mock/fake:

```text
A. auth/session context → URL dispatch → URL worker fixture → mock RiskResult
B. auth/session context → TEXT dispatch → Text Worker → mock RiskEvaluation → mock RiskResult
```

Đồng thời mọi PR integration đi qua K-WP11 CI và local environment có thể chạy trên K-WP10 baseline.

## 2. Input

```text
H-WP03, H-WP01
K-WP09, K-WP11, K-WP10, K-WP01
I-WP11, I-WP12
T-WP01, T-WP02
TEAM-C01 contract freeze
```

## 3. Output

- integration PR/test environment;
- smoke-test script;
- CI xanh trên integration PR;
- local compose/readiness evidence;
- danh sách contract mismatch nếu có.

## 4. Integration flow A — URL

```text
AccessContext
→ POST /v1/scans/url
→ M12 create ScanRequest + FakeMessageBus/queue contract
→ K-WP01 URL normalization/basic signals fixture
→ worker result
→ M12 consume/dedupe
→ MockRiskEvaluationPort
→ RiskResult
```

**W10 chưa yêu cầu SSRF WP hoàn chỉnh**; K-WP02 bắt đầu W11. Integration này chỉ chứng minh contract/dispatch/result seam và URL intake fixture.

## 5. Integration flow B — Text

```text
AccessContext
→ POST /v1/scans/text
→ M12 dispatch
→ T-WP01 normalization
→ T-WP02 signals
→ result event
→ M12 dedupe
→ MockRiskEvaluationPort
→ RiskResult fixture
```

## 6. Platform / CI checkpoint

```text
PR → K-WP11 CI → required checks xanh
local compose → K-WP10 readiness xanh
secret/config → K-WP09 contract
```

Không merge integration bằng cách bypass required status check.

## 7. Must-not

- Không sửa contract silently chỉ để test pass.
- Không cho worker gọi DB/core API trực tiếp.
- Không hard-code final score ở worker.
- Không log raw token/OTP/provider secret.
- Không tắt CI gate vì integration đang lỗi.

## 8. Acceptance criteria

- [ ] URL mock smoke flow pass.
- [ ] TEXT smoke flow pass.
- [ ] duplicate event test pass.
- [ ] auth/access gate pass.
- [ ] local platform readiness pass.
- [ ] integration PR đi qua CI và required checks.
- [ ] không có contract mismatch chưa ghi issue.
- [ ] tracker cập nhật trước họp Thứ 2.

## 9. Evidence

```text
integration PR
+ CI run URL
+ smoke-test log
+ docker compose/readiness output
+ mismatch issue list
```
