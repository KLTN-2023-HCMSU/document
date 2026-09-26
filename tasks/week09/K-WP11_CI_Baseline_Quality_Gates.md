# K-WP11 — CI Baseline & Quality Gates

**Owner:** Khải  
**Module:** M13 Platform Infrastructure  
**Cycle:** W09 · 27/09/2026 → 05/10/2026  
**Feature IDs:** M13-F012, M13-F013, M13-F014, M13-F015, M13-F017  
**Release target:** MVP v0.1.0  
**Critical milestone:** P0 merge gate càng sớm càng tốt trong W09

## 1. Outcome cần đạt

Mọi Pull Request chạy kiểm tra tự động; **test đỏ không được merge**. CI là nền tảng từ đầu dự án, không phải task sau MVP.

## 2. Scope / Non-scope

**IN:** GitHub Actions PR workflow, test jobs, branch protection/status check, lint baseline, dependency scan baseline, coverage report nếu toolchain sẵn.  
**OUT:** deploy production/CD (K-WP12), observability runtime (K-WP13), business tests do owner module viết.

## 3. Input

```text
repo mono-repo
service/build commands hiện hành
Java/Spring tests
Python tests
Web tests/build/lint nếu project đã có
contract/fixture validation
```

## 4. Output

```text
.github/workflows/ci.yml (hoặc nhiều reusable workflow)
required status checks
cache dependency hợp lý
artifact/test report khi cần
README cách chạy cùng command ở local
```

## 5. Logic flow

```text
PR opened / push to PR
→ checkout
→ setup runtimes
→ restore cache
→ backend test
→ Python test
→ web/mobile lint/build/test theo scope hiện có
→ contract/schema check
→ dependency/security baseline
→ PASS: status green
→ FAIL: status red, branch protection block merge
```

## 6. Business rules / invariants

1. CI command phải gần với command local; không tạo “CI-only behavior”.
2. Job fail phải fail pipeline; không `continue-on-error` cho P0 test gate.
3. Required check áp cho protected main branch.
4. Secret không in ra log.
5. CI không cần external paid provider thật; dùng mocks/fixtures.
6. Flaky test phải được xử lý/ghi issue, không disable âm thầm.

## 7. Gate theo mức ưu tiên

### P0 — phải có ngay
- PR trigger.
- test các service đang tồn tại.
- failing test → CI đỏ.
- branch protection → CI đỏ không merge.

### P1 — hoàn thiện trong WP
- Checkstyle/ruff/ESLint tương ứng.
- coverage report/badge nếu toolchain ổn.
- Dependabot/OWASP Dependency-Check hoặc tương đương.

## 8. Failure behavior

| Case | Expected |
|---|---|
| unit test fail | pipeline fail |
| lint fail | fail khi gate đã bật |
| dependency scanner unavailable | fail/warn theo policy đã ghi rõ; không silently skip |
| external service unavailable | test dùng mock; không làm CI phụ thuộc Internet không cần thiết |

## 9. Test matrix / demo bắt buộc

- PR xanh được merge.
- Cố tình thêm 1 failing test → workflow đỏ.
- Xác minh nút merge bị chặn bởi required check.
- Sửa test → workflow xanh → merge được.
- Cache miss/hit không làm thay đổi kết quả.

## 10. Acceptance criteria

- [ ] GitHub Actions chạy trên mỗi PR.
- [ ] Backend/Python/Web command hiện có được chạy phù hợp.
- [ ] Test đỏ thật sự chặn merge.
- [ ] Không expose secret trong action log.
- [ ] Lint/dependency baseline được cấu hình hoặc ghi rõ deferred P1 có issue.
- [ ] README local command khớp CI.
- [ ] CI evidence link được dán vào tracker.

## 11. Evidence để tick Done

```text
workflow file
+ screenshot/link PR xanh
+ screenshot/link PR cố tình đỏ và bị block merge
+ link branch protection / required check
```
