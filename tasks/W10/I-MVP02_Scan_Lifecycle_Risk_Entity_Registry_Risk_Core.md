# I-MVP02 — Scan Lifecycle + Risk Entity Registry + Risk Core

| Thuộc tính | Giá trị |
|---|---|
| Week | **W10 · 06/10–12/10/2026** |
| Owner | **Kiên** |
| Source WP | `I-WP12` + `I-WP03 (basic)` + `I-WP05` + `I-WP06 (basic)` |
| Upstream | **I-MVP01** |
| Downstream chính | URL/Text W10, Phone/Bank W11, Nested/AI W12 |
| Mục tiêu tuần | Scan lifecycle + worker result consumption + deterministic Rule/Fusion/Composer chạy được |

## Tài liệu ràng buộc

Đặc tả này là **implementation slice cho Timeline V2.1**, không thay đổi danh sách tính năng hay kiến trúc. Khi có xung đột, áp dụng thứ tự nguồn hiện tại của dự án:

1. `architecture_v3.3.md`
2. `modules_specification_v1.3.md`
3. `anti_scam_schema_overall_v1.0.md` *(Draft)*
4. `Anti_Scam_Feature_Backlog_V2`
5. `Timeline_full_Anti_Scam_2026_2027_V2.1.md`

> Một task `*-MVPxx` là **MVP implementation slice** lấy từ một hoặc nhiều WP. Hoàn thành task **không đồng nghĩa toàn bộ source WP đã hoàn thành** nếu các feature nâng cao/P1/P2 được ghi là deferred.


## 1. Mô tả

Task này biến contract W09 thành **business core chạy được**. M12 quản lý lifecycle `PENDING → PROCESSING → COMPLETED/FAILED`, consume worker result qua `q.scan.result`, deduplicate event và retry/DLQ. Đồng thời dựng `risk_entities` baseline và triển khai M09 Rule Engine → Risk Fusion → Result Composer để signal fixture/worker result có thể thành final `RiskResult` deterministic.

ThreatQuery cache-aside đầy đủ và PHONE/BANK flow nằm W11; nested/AI barrier nằm W12.

## 2. Traceability / phạm vi feature

| Feature | Mức trong task | Ghi chú |
|---|---|---|
| `M12-F005`–`M12-F008`, `M12-F012` | **Required** | lifecycle, result consumer, envelope, idempotency, retry/DLQ |
| `M08-F016`, `M08-F017` | **Required** | risk_entities + status |
| `M08-F018` | Repository capability | upsert hook; actual moderation bridge W12 |
| `M08-F019`–`F024` | Deferred | admin/merge/confidence/offline depth |
| `M09-F001`, `F002`, `F004`, `F005`, `F037` | **Required** | rule core + evidence + deterministic + composer separation |
| `M09-F003`, `F038`, `F039` | **Required basic** | fusion, versioned policy, thresholds/missing-group behavior |
| `M09-F006`–`F008` | Deferred/depth | caching/confidence/profile extension |

## 3. Input / Output

### Input

```text
ScanRequest / scanId
WorkerResult event
- eventId
- jobId
- correlationId
- eventVersion
- attempt
- scanId
- processorVersion
- AnalysisSignal[]
- DerivedIndicator[]
- pendingAiTasks[]

ActiveRuleSet / FusionPolicy
Risk entity repository commands/fixtures
```

### Output

```text
ScanStatus
- PENDING
- PROCESSING
- COMPLETED
- FAILED

UnifiedSignalSet
RuleMatch[] / Evidence[]
riskScore [0..100]
riskLevel SAFE | CAUTION | DANGER
RiskResult
- degraded
- evidence
- explanations
- recommendations
- policyVersion
```

## 4. Business rules / invariants

- Scan không được treo `PROCESSING` vô hạn.
- RabbitMQ at-least-once → consumer idempotent theo `eventId`; duplicate không double-count signal.
- Worker delivery: attempt đầu + tối đa 2 retry; exhausted → DLQ.
- Rule Engine tạo `RuleMatch[]/evidence`; Risk Fusion tạo score/level; Result Composer tạo explanation/recommendation. Không gộp ba trách nhiệm thành một hàm opaque.
- Cùng `UnifiedSignalSet` + cùng `FusionPolicy` phải deterministic.
- `riskScore` không đơn giản bằng tổng evidence score.
- Missing signal group → renormalize available weights; không coi missing = 0.
- Initial thresholds: `0–34 SAFE`, `35–69 CAUTION`, `70–100 DANGER`.
- Hard override phải có evidence và thuộc policy version.
- AI không có quyền tự trả business verdict.
- `risk_entities` status baseline: `SUSPECTED`, `VERIFIED`, `CLEARED`.

## 5. Logic flow

```text
Scan accepted (I-MVP01)
→ persist ScanRequest(PENDING/PROCESSING)
→ worker job dispatched
→ q.scan.result receives WorkerResult
→ validate event schema/version
→ dedupe eventId
→ aggregate signals
→ apply retry/failure policy if worker failed
→ Rule Engine
→ Risk Fusion
→ Result Composer
→ persist scan_results
→ status COMPLETED
```

Worker fail:

```text
attempt 1
→ retry 1
→ retry 2
→ exhausted
→ DLQ
→ terminal FAILED/degraded policy theo loại flow
```

`DerivedIndicator` ở W10 có thể được giữ/forward theo contract; ThreatQuery/nested handling sâu bắt đầu W11/W12.

## 6. Risk evaluation flow

```text
UnifiedSignalSet
→ evaluateRules(ActiveRuleSet)
→ RuleMatch[] + Evidence[]
→ fuseRisk(FusionPolicy)
→ riskScore + riskLevel + metadata
→ compose(...)
→ RiskResult
```

Initial profile weights lấy từ Module Spec; implementation phải versioned/configurable, không hard-code rải rác.

## 7. Data ownership

Canonical PostgreSQL baseline:

```text
scan_requests
scan_results
risk_entities
rules
rule_versions
fusion policy/version metadata theo implementation hiện hành
```

Redis chỉ dùng cache/idempotency/coordination theo M12/M13 contract.

W10 chưa cần `scan_relations`/`scan_ai_tasks` behavior đầy đủ; W12 sẽ dùng cho nested/AI barrier.

## 8. Failure / degraded cases

- duplicate event → ACK/ignore, không double state transition/signal.
- malformed event → reject/DLQ.
- retry exhausted → terminal state/observable failure.
- invalid active fusion/rule config → fail safe; không silently chọn random defaults.
- missing AI/reputation group trong fixture → renormalize + degraded metadata nếu policy yêu cầu.
- result composition không được tạo explanation ngoài evidence có thật.

## 9. Phát triển độc lập / mocks

- WorkerResult fixtures từ Khải/Thắng W09.
- `MockThreatQuery` cho reputation dependency chưa thật.
- `FakeClock` để test retry/deadline/state transitions.
- In-memory scan/risk repositories cho domain tests.
- URL/Text team có thể consume `MockRiskEvaluationPort` đầu tuần rồi switch sang real port cuối tuần.

## 10. Test plan

### Lifecycle / messaging

- PENDING→PROCESSING→COMPLETED happy path.
- FAILED terminal path.
- duplicate event same `eventId` không double-count.
- retry 2 lần rồi DLQ.
- event envelope thiếu required field bị reject.

### Risk core

- same input + same policy → same output.
- threshold boundaries 34/35/69/70.
- missing group renormalization.
- hard override có evidence.
- Result Composer chỉ dùng evidence thực.

### Registry

- create/update risk entity baseline.
- state SUSPECTED/VERIFIED/CLEARED valid.
- moderation-triggered upsert dùng repository hook fixture, chưa cần M07 thật.

## 11. Acceptance Criteria

- [ ] Lifecycle PENDING→PROCESSING→COMPLETED/FAILED được persist và test.
- [ ] Worker Result Consumer validate event + dedupe theo eventId.
- [ ] Duplicate event không double-count signal hoặc transition.
- [ ] Retry policy attempt đầu + tối đa 2 retry; exhausted vào DLQ.
- [ ] Risk Entity Registry baseline có entity/status/repository tests.
- [ ] Rule Engine tạo RuleMatch/Evidence độc lập với Fusion/Composer.
- [ ] Risk Fusion score 0–100 + SAFE/CAUTION/DANGER đúng threshold.
- [ ] Missing signal group được renormalize, không mặc định bằng 0.
- [ ] RiskResult có evidence/explanation/recommendation/policyVersion và deterministic test.
- [ ] URL/Text worker fixtures có thể đi end-to-end tới RiskResult.

## 12. Deliverables

- Scan lifecycle state machine/repository.
- Worker result consumer + idempotency/retry/DLQ tests.
- Risk entity repository baseline.
- Rule Engine + Risk Fusion + Result Composer implementation.
- Seeded rule/policy fixtures.
- End-to-end test từ worker fixture → persisted RiskResult.

## 13. Handoff sang W11

`I-MVP03` bổ sung `ThreatQuery`, Phone/Bank reputation và basic threat data trên registry/core này. W12 mới thêm child scan + AI completion barrier.
