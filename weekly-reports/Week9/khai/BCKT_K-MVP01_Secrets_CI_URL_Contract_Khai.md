# K-MVP01 · Secrets, CI và URL Contract — Báo cáo kiểm thử

| Trường | Giá trị |
|---|---|
| **Người thực hiện phần code/test** | Ninh Văn Khải |
| **Kỳ báo cáo / ngày lập hồ sơ** | W09 · 27/09–05/10/2026 / bổ sung hồ sơ 06/10/2026 |
| **Ngày chạy được dẫn lại** | 05/10/2026; ngày 06/10 đọc lại source và xác minh trạng thái run, không nhận là đã chạy lại toàn bộ test |
| **Phiên bản code** | `4e92abd645b72b4354821f5752e29f6146e2cb15`, [PR #9][pr9]; kế thừa `63cc335` từ #6 |
| **Môi trường local** | Java 17/H2; Python 3.14 venv; Node 22; Docker API/Python 3.12, PostgreSQL 16, Redis 7 |
| **GitHub Actions** | Ubuntu 24.04, Java 17, Python 3.12, Node 22; [run 37341308147][ci9] SUCCESS, 8/8 job |

## 1. Mục tiêu kiểm thử

Chứng minh URL fixture hợp lệ có thể serialize/validate offline và payload sai bị từ chối; worker result không mang final verdict/credential. Chứng minh gate không coi job thiếu/lỗi là success, cấu hình/log giảm nguy cơ lộ secret, route mock bị tắt không cấp token, và image API hoạt động với PostgreSQL thật kể cả DB outage/recovery. Các kết luận chỉ áp dụng cho implementation và môi trường đã kiểm tra.

## 2. Chiến lược

| Tầng | Nội dung | Công cụ / nguồn |
|---|---|---|
| Unit/contract offline | 5 URL fixture, field/semantic validation, CLI không echo payload | [`contracts/tests/test_url_contract.py`][urltests], `jsonschema`, `unittest` |
| Unit/policy | Gate result, CLI exit, tất cả job đều thuộc `needs`, quyền và pin Actions | [`scripts/tests/test_required_ci.py`][gatetests] |
| Java unit/MockMvc | JWT key, Logback redaction, dev account response và disabled routes | JUnit, H2; [cây test Java][javatests] |
| Runtime integration | Docker build, Flyway/Hibernate, HTTP→DB write, mock false, DB dừng/phục hồi | [`ci-runtime-smoke.py`][smoke], Compose/PostgreSQL thật |
| Pipeline | Secret scan, generated types, lint/build, production dependency audit | [Workflow][workflow], Gitleaks/actionlint/Redocly/npm |
| Regression ngoài slice | Python scoring legacy, web RiskResult UI, API baseline | Giữ test hiện có; không tính tất cả là test mới của Khải |

## 3. Bảng ca kiểm thử

Các dòng dưới gom theo tình huống, **không cộng số dòng thành số test**. Kết quả lấy từ evidence 05/10 và run CI đã xác minh.

| # | Ca / đầu vào | Kỳ vọng | Kết quả thực tế / điểm chạy lại |
|---|---|---|---|
| 1 | 5 fixture → JSON bytes → deserialize, cấm `socket.socket` | Payload giữ nguyên sau roundtrip, không mở socket trong tooling | Đạt · `test_producer_consumer_roundtrip_without_network` |
| 2 | Xóa từng field envelope của job/result | Reject thiếu trace/version/attempt | Đạt · `test_missing_envelope_fields_rejected` |
| 3 | Version 2.0; attempt 0/4; status COMPLETED; timestamp yesterday | Reject | Đạt · `test_wrong_version_attempt_status_and_timestamp` |
| 4 | Result thêm riskScore/riskLevel/verdict/userId/token/riskResult hoặc metadata.password | Reject business state/credential ngoài allowlist | Đạt · `test_worker_cannot_send_business_state_or_credentials` |
| 5 | Worker phát `REPUTATION_UNAVAILABLE` | Reject; signal lỗi lookup thuộc core | Đạt · `test_reputation_unavailable_belongs_to_core` |
| 6 | Indicator dùng handling `CALL_CORE` | Reject | Đạt · `test_invalid_handling` |
| 7 | Hai pending AI task cùng ID, khác kind | Reject duplicate task ID | Đạt · `test_duplicate_ai_task_rejected_even_with_different_kind` |
| 8 | Deadline bằng requestedAt; URL `file:///...` | Reject deadline/scheme | Đạt · `test_job_deadline_and_scheme`; không phải test SSRF network |
| 9 | Derived type hợp lệ; signal value NaN | Type hợp lệ pass, số non-finite reject | Đạt · `test_supported_derived_types_and_nonfinite_rejected` |
| 10 | Job chứa reputation signal do core cung cấp | Context giữ nguyên sau validate | Đạt · `test_pre_enrichment_is_serialized_in_job` |
| 11 | CLI nhận payload sai có secret tổng hợp | Exit 1, output không chứa secret | Đạt · `test_cli_failure_never_echoes_rejected_secret` |
| 12 | Đủ 7 job success; từng job fail/skip/cancel/pending/neutral/timed_out/unknown/null | Chỉ trường hợp đầy đủ success được pass | Đạt · `GateTest`; loop subtest không tính thành test method mới |
| 13 | Thiếu/thừa job; JSON malformed; result không phải object | Fail closed, CLI exit 1, không traceback với các input được test | Đạt · `test_missing_job_blocks`, `test_malformed_and_extra_jobs_block`, `test_cli_exit_status_matches_gate` |
| 14 | Đọc workflow YAML thực | Job set = gate needs = REQUIRED; always(), trigger, quyền read, SHA pin, timeout hợp lệ | Đạt · 3 `WorkflowPolicyTest` |
| 15 | JWT key null/trống/ngắn/toàn khoảng trắng/placeholder | Constructor từ chối; exception không echo key; properties che key | Đạt · `JwtSecretTest` (7 invocation, gồm parameterized cases) |
| 16 | Log phone/bank/email và credential, kể cả formatted argument/exception | Mask giá trị, giữ event hữu ích; layout không lỗi parser | Đạt · 3 `SensitiveDataRedactorTest` |
| 17 | Mock bật: GET accounts | Có accounts, không có data.password | Đạt · `AuthFlowTest.dev_accounts_do_not_expose_password` |
| 18 | Mock tắt: POST token role ADMIN / GET accounts | 404 `NOT_FOUND`, không trả access token | Đạt · 2 `MockAuthDisabledTest`; runtime còn kiểm tra POST trên image thật |
| 19 | Token giả sinh ngẫu nhiên + file/history scan | Token giả bị bắt, output redacted; repository không có finding theo config | Đạt · `test-secret-scanner.py`, `scan-secrets.sh`; exact synthetic-key allowlist |
| 20 | Regenerate types từ OpenAPI | Không khác artifact đã commit; lint/build/typecheck/test đạt | Đạt · job `web`, 68 web tests |
| 21 | Image API/Python + DB CI rỗng | Build được; health/ready UP, migration success, Hibernate validate | Đạt · runtime smoke; không có migration mới trong slice |
| 22 | HTTP đăng ký vào stack CI, query users | HTTP 201 và đúng một record được lưu PostgreSQL | Đạt · runtime smoke, không dùng H2 |
| 23 | Stop PostgreSQL rồi start lại | Ready 503, health 200; ready hồi phục 200, record còn | Đạt · runtime smoke; project test được cleanup |

Tình huống #1 chỉ cấm network của validator/fixture test; không chứng minh URL worker deployment bị cô lập. #7 chưa chứng minh AI publish thành công trước khi khai pending task. #14 kiểm tra policy file, không truy cập setting GitHub để chứng minh merge enforcement.

## 4. Cách chạy lại toàn bộ

Từ root checkout `Scam-Risk-Detector` tại `4e92abd`; cần Java 17, Node 22, Python 3.12+ và Docker Compose hỗ trợ `up --wait`. Các lệnh không cần `.env` thật; dùng venv riêng:

```bash
python3 -m venv .venv
source .venv/bin/activate
python -m pip install -r contracts/requirements.txt -r scripts/requirements-ci.txt
python -m pip install -r services/scan-engine/requirements.txt
(cd services/api && ./mvnw -B verify)
(cd services/scan-engine && python -m pytest tests/ -q)
python -m unittest discover -s contracts/tests -v
python -m unittest discover -s scripts/tests -v
bash scripts/scan-secrets.sh
npx --yes @redocly/cli@2.58.1 lint contracts/openapi/anti-scam-api.yaml
(cd apps/web && npm ci && npm audit --omit=dev --audit-level=high && npm run generate:types && git diff --exit-code -- src/types/api-generated.ts && npm run lint && npm run build && npm test)
python scripts/ci-runtime-smoke.py
```

Kiểm tra cú pháp workflow bằng installer pinned (Linux x86_64):

```bash
task_actionlint_dir=$(mktemp -d)
bash scripts/install-actionlint.sh "$task_actionlint_dir"
"$task_actionlint_dir/actionlint" -shellcheck="" -pyflakes=""
```

Negative control CLI (không sửa source): `CI_NEEDS='{}' python scripts/required-ci.py` phải exit **1** và in `Required checks failed or incomplete`.

Lần chạy tham chiếu: Java 40, Python legacy 25, web 68, URL contract 11, gate/workflow 8 — tổng **152**. Test mới riêng W09 là **32 ca**: 13 Java, 11 URL contract, 8 gate/workflow; scanner negative control và runtime smoke được báo riêng. Không cộng 152 và 32 vì 32 đã nằm trong tổng.

## 5. Ca không đạt hoặc đạt một phần

| Ca / giới hạn | Evidence / trạng thái | Xử lý tiếp |
|---|---|---|
| Disabled dev route từng trả 500 | Run local đầu tiên phát hiện catch-all handler; đã sửa thành 404 và 2 regression + smoke đạt ở `4e92abd` | Giữ test khi thay auth/error handling |
| Enforcement chặn merge đỏ | Owner báo đã bật; API ngày 05/10 trả 403, chưa có red-PR-blocked evidence độc lập | Xác minh rule/setting với quản trị repo; không suy ra từ gate xanh |
| Dependency dev tooling | Full audit 05/10 còn 8 high entries; production audit 0 finding | Theo dõi bản vá tương thích; không tự gọi full audit sạch |
| OpenAPI warnings | Valid nhưng còn 4 warning về metadata/server/probe response | Làm sạch riêng theo quy ước API, không sửa spec chỉ để tắt warning |
| Worker isolation, AMQP retry/dedupe/DLQ, AI ack/barrier | Chưa chạy integration vì runtime chưa có trong slice | Test cùng implementation W10–W12 |
| SSRF/redirect và độ chính xác URL detection | Chưa có fetch/detector thật | K-MVP02/K-MVP03; không dùng `file://` schema test thay attack test |
| Secret rotation/full privacy audit | Mới có validation/redaction/config baseline | Nghiệm thu riêng khi provider/session/runtime đầy đủ |

## 6. Số liệu phân loại và hiệu năng

Không áp dụng Precision/Recall/F1 hoặc confusion matrix: W09 chưa có URL detector/verdict thật để đánh giá. Chưa có coverage report hoặc benchmark latency; số test pass và thời gian CI không thay thế các chỉ số đó.

## 7. Kết luận

| Câu hỏi | Trả lời |
|---|---|
| Automated test pass | 152/152 tại evidence 05/10; Docker smoke và scanner negative control đạt riêng |
| CI | 8/8 job success ở [run PR #9][ci9] cho `4e92abd`; xác minh lại metadata ngày 06/10 |
| Đủ điều kiện nghiệm thu? | Đủ bằng chứng test cho phần code bàn giao; chưa đóng toàn bộ K-MVP01/WP vì #9 chờ review/merge, contract freeze và enforcement evidence còn thiếu |
| Kiến nghị | Review liên owner, merge #9 khi được duyệt; bổ sung integration theo runtime thật ở tuần sau |

Chi tiết phạm vi tại [BCTN](BCTN_K-MVP01_Secrets_CI_URL_Worker_Contract_Khai.md). Nguồn evidence local: [security/CI baseline tại commit][baseline]. Bổ sung báo cáo ngày 06/10 không làm thay đổi code đã được kiểm tra.

[pr9]: https://github.com/KLTN-2023-HCMSU/Scam-Risk-Detector/pull/9
[ci9]: https://github.com/KLTN-2023-HCMSU/Scam-Risk-Detector/actions/runs/37341308147
[urltests]: https://github.com/KLTN-2023-HCMSU/Scam-Risk-Detector/blob/4e92abd645b72b4354821f5752e29f6146e2cb15/contracts/tests/test_url_contract.py
[gatetests]: https://github.com/KLTN-2023-HCMSU/Scam-Risk-Detector/blob/4e92abd645b72b4354821f5752e29f6146e2cb15/scripts/tests/test_required_ci.py
[javatests]: https://github.com/KLTN-2023-HCMSU/Scam-Risk-Detector/tree/4e92abd645b72b4354821f5752e29f6146e2cb15/services/api/src/test/java/com/antiscam/api
[smoke]: https://github.com/KLTN-2023-HCMSU/Scam-Risk-Detector/blob/4e92abd645b72b4354821f5752e29f6146e2cb15/scripts/ci-runtime-smoke.py
[workflow]: https://github.com/KLTN-2023-HCMSU/Scam-Risk-Detector/blob/4e92abd645b72b4354821f5752e29f6146e2cb15/.github/workflows/ci.yml
[baseline]: https://github.com/KLTN-2023-HCMSU/Scam-Risk-Detector/blob/4e92abd645b72b4354821f5752e29f6146e2cb15/docs/security-ci-baseline.md
