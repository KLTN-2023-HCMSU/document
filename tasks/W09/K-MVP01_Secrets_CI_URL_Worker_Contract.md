# K-MVP01 — Secrets + CI + URL Worker Contract

| Thuộc tính | Giá trị |
|---|---|
| Week | **W09 · 27/09–05/10/2026** |
| Owner | **Khải** |
| Source WP | `K-WP09` + `K-WP11` + `K-WP08` |
| Upstream | Team contract freeze |
| Downstream chính | `K-MVP02`, `I-MVP02`, `I-MVP03`, `T-MVP04` |
| Mục tiêu tuần | Có security/config baseline, CI merge gate và URL worker/core boundary ổn định |

## Tài liệu ràng buộc

Đặc tả này là **implementation slice cho Timeline V2.1**, không thay đổi danh sách tính năng hay kiến trúc. Khi có xung đột, áp dụng thứ tự nguồn hiện tại của dự án:

1. `architecture_v3.3.md`
2. `modules_specification_v1.3.md`
3. `anti_scam_schema_overall_v1.0.md` *(Draft)*
4. `Anti_Scam_Feature_Backlog_V2`
5. `Timeline_full_Anti_Scam_2026_2027_V2.1.md`

> Một task `*-MVPxx` là **MVP implementation slice** lấy từ một hoặc nhiều WP. Hoàn thành task **không đồng nghĩa toàn bộ source WP đã hoàn thành** nếu các feature nâng cao/P1/P2 được ghi là deferred.


## 1. Mô tả

Task này tạo **engineering foundation** cho toàn team và chốt boundary của URL Worker. CI phải chạy từ PR và chặn merge khi test đỏ. Secret/PII không được hard-code hoặc leak qua log. URL Worker chỉ phát signal/indicator/AI-task metadata qua RabbitMQ; Spring Boot/M12 mới sở hữu business state, reputation lookup và final `RiskResult`.

W09 **không** yêu cầu URL scan end-to-end thật; implementation URL intake/runtime nằm ở W10.

## 2. Traceability / phạm vi feature

| Feature | Mức trong task | Ghi chú |
|---|---|---|
| `M13-F001`, `M13-F002`, `M13-F005`, `M13-F035` | **Required** | secret + sensitive logging baseline |
| `M13-F012`, `M13-F013` | **Required** | CI test trên PR + block merge |
| `M13-F014`, `M13-F017` | Recommended | lint/dependency check nếu pipeline ổn định |
| `M13-F015` | Deferred | coverage badge không phải gate W09 |
| `M02-F054` | **Required** | worker output boundary |
| `M02-F055` | **Required contract** | known reputation pre-enrich từ core; new host → `DerivedIndicator` |
| `M02-F056` | **Required contract** | deferred reputation qua core `ThreatQuery` |
| `M02-F053` | Deferred | UI 4 tab không phải W09 |

## 3. Input / Output

### Input

```text
Repository pull request
Environment/secret configuration
URL scan job fixture
- scanId
- job/event/correlation metadata
- normalized/raw URL theo contract
- optional reputationContext
```

### Output

```text
CI status: PASS / FAIL
Secret/config validation result
URL WorkerResult contract:
- AnalysisSignal[]
- DerivedIndicator[]
- pendingAiTasks[]
- processor/version metadata
- event/job/scan correlation metadata
```

Worker **không** output final `SAFE/CAUTION/DANGER` hoặc persist `RiskResult`.

## 4. Business rules / invariants

- PR test đỏ phải chặn merge.
- JWT signing key, OAuth secret, AI provider credential, notification provider credential không commit/hard-code.
- Log phải mask phone/bank/sensitive values theo policy.
- URL Worker không gọi Spring Boot API, PostgreSQL hoặc Redis.
- Known URL/domain reputation được core pre-enrich và đi kèm job.
- Host/domain mới trong redirect/content chỉ trở về `DerivedIndicator`.
- `pendingAiTasks[]` chỉ chứa task đã publish thành công.
- Final verdict thuộc Spring Boot/M09/M12.

## 5. URL contract flow

```text
Core/M12
→ pre-enrich known URL/domain via ThreatQuery
→ publish scan.url.requested(job + reputationContext)

URL Worker
→ analyze using input + reputationContext
→ collect AnalysisSignal[]
→ collect new host/domain as DerivedIndicator[]
→ optionally publish AI task
→ publish scan.url.analyzed / WorkerResult

Core/M12
→ consume result
→ REPUTATION_ONLY indicator → ThreatQuery
→ CHILD_SCAN indicator → orchestration policy
→ later RiskEvaluationPort → final RiskResult
```

## 6. CI flow

```text
Pull Request
→ checkout
→ backend tests
→ Python/worker tests
→ Web tests
→ optional lint/dependency checks
→ required check result
→ FAIL: merge blocked
→ PASS: eligible to merge
```

## 7. Contract fields tối thiểu

Envelope phải đủ cho trace/idempotency theo architecture:

```text
eventId
jobId
correlationId
eventVersion
attempt
scanId
processorVersion
```

Payload URL result:

```text
signals[]
derivedIndicators[]
  - type
  - normalizedValue
  - handling = REPUTATION_ONLY | CHILD_SCAN
pendingAiTasks[]
```

Exact class/package/file naming là implementation detail của repo, không phải contract business.

## 8. Security / failure cases

- Secret scan phát hiện raw secret → CI fail.
- Test fail → required check fail.
- Contract malformed/version unsupported → consumer reject/DLQ ở giai đoạn integration.
- Worker cần reputation mới → không gọi DB/core; trả `DerivedIndicator`.
- ThreatQuery unavailable → core mới sinh `REPUTATION_UNAVAILABLE`; worker không tự tạo.

## 9. Phát triển độc lập / mock strategy

- `FakeMessageBus` để URL Worker contract test không cần RabbitMQ thật.
- `reputationContext` fixture thay cho M08 thật.
- `RiskResult` fixture cho UI/consumer.
- Core có thể dùng `worker-result-url-safe.json`, `worker-result-url-suspicious.json`, `worker-result-with-derived-indicator.json`.

## 10. Test plan

### CI proof

- [ ] Một PR cố tình làm test fail và bị merge gate chặn.
- [ ] Main branch chạy xanh sau khi fix.
- [ ] Secret/config scan không phát hiện credential bị commit.
- [ ] CI artifact/log không in raw secret.

### Contract tests

- Producer job fixture → worker deserialize được.
- Worker result → core fixture deserialize được.
- `DerivedIndicator.handling` chỉ nhận giá trị contract cho phép.
- Worker result không chứa final verdict/business persistence field.
- Mock network test chứng minh worker không cần DB/Redis/core API.

## 11. Acceptance Criteria

- [ ] CI chạy tự động trên PR và test đỏ chặn merge.
- [ ] Environment/secret baseline có template/config rõ ràng, không hard-code credential.
- [ ] Sensitive values trong log được mask theo policy.
- [ ] URL WorkerResult contract được freeze: signals + derivedIndicators + pendingAiTasks + trace metadata.
- [ ] Reputation boundary được freeze: pre-enrich ở core, deferred lookup cũng ở core.
- [ ] Worker không truy cập trực tiếp DB/Redis/Spring Boot API.
- [ ] Producer/consumer contract fixtures xanh.
- [ ] M02-F053 UI 4-tab không bị kéo vào W09.

## 12. Deliverables

- CI workflow + required checks configuration.
- Secret/env baseline + sample config without real secret.
- URL job/result contract schema + fixtures.
- Contract tests producer/consumer.
- Evidence: blocked red PR + green main run.

## 13. Handoff sang W10

`K-MVP02` dùng contract này để chạy URL basic vertical slice trên runtime thật. `I-MVP02` có thể consume URL WorkerResult bằng fixture ngay cả khi URL worker implementation chưa hoàn tất.
