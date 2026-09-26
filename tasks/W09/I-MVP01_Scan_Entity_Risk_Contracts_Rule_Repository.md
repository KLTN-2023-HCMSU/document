# I-MVP01 — Scan / Entity / Risk Contracts + Rule Repository Baseline

| Thuộc tính | Giá trị |
|---|---|
| Week | **W09 · 27/09–05/10/2026** |
| Owner | **Kiên** |
| Source WP | `I-WP11` + `I-WP17` + `I-WP07 (basic)` |
| Upstream | Team contract freeze |
| Downstream chính | `K-MVP02`, `T-MVP02`, `I-MVP02`, `I-MVP03`, QR/Nested ở W11–W12 |
| Mục tiêu tuần | Chốt scan intake/routing, entity semantics, risk ports và có rule repository tối thiểu |

## Tài liệu ràng buộc

Đặc tả này là **implementation slice cho Timeline V2.1**, không thay đổi danh sách tính năng hay kiến trúc. Khi có xung đột, áp dụng thứ tự nguồn hiện tại của dự án:

1. `architecture_v3.3.md`
2. `modules_specification_v1.3.md`
3. `anti_scam_schema_overall_v1.0.md` *(Draft)*
4. `Anti_Scam_Feature_Backlog_V2`
5. `Timeline_full_Anti_Scam_2026_2027_V2.1.md`

> Một task `*-MVPxx` là **MVP implementation slice** lấy từ một hoặc nhiều WP. Hoàn thành task **không đồng nghĩa toàn bộ source WP đã hoàn thành** nếu các feature nâng cao/P1/P2 được ghi là deferred.


## 1. Mô tả

Task này tạo contract chung để **URL/TEXT/ENTITY/QR có thể phát triển song song** mà không cần chờ Orchestrator hoàn chỉnh. Kiên chịu trách nhiệm freeze `ValidatedScanCommand`, access/quota gate semantics, idempotency contract, `ScanTypeResolver/ProcessorRegistry`, entity endpoint semantics (`PHONE`/`BANK_ACCOUNT`), `NO_DATA`, `RiskEvaluationPort`, `ThreatQuery` seam và baseline repository cho `rules/rule_versions`.

W09 chưa phải full scan lifecycle; lifecycle/result consumption/retry thuộc `I-MVP02` ở W10.

## 2. Traceability / phạm vi feature

| Feature | Mức trong task | Ghi chú |
|---|---|---|
| `M12-F001`, `M12-F002` | **Required** | idempotency key + request hash/conflict |
| `M12-F004` | **Required** | ScanTypeResolver + JobRouter |
| `M12-F018`, `M12-F019` | **Required contract** | AccessContext/quota/ownership semantics |
| `M12-F003` | Contract/basic | 429 + Retry-After shape; full quota tuning later |
| `M04-F017`, `M04-F018` | **Required** | unified entity endpoint + NO_DATA semantics |
| `M09-F009`, `M09-F010` | **Required baseline** | versioned rule repository + DB-backed loading seam |
| `M09-F011`–`M09-F016` | Deferred | admin CRUD/UI/version management depth |

## 3. Input / Output

### Input

```text
ValidatedScanCommand
AccessContext
EntitlementContext / MVP policy guest|free
Idempotency-Key + request hash
scanType = URL | TEXT | ENTITY | QR
entityType = PHONE | BANK_ACCOUNT  // khi scanType=ENTITY
```

### Output

```text
DispatchDecision
- accepted / rejected
- scanType/entityType
- routingKey
- ownership context
- quota/inference profile metadata

Entity contract
- normalized entity semantics
- NO_DATA != SAFE

Internal ports
- RiskEvaluationPort.evaluate(...)
- ThreatQuery.lookup(...)

RuleRepository baseline
- active/versioned rule definitions có thể seed/read
```

## 4. Business rules / invariants

- Guest được scan public inputs; guest không có persistent account history.
- `scanId` không phải authorization token.
- Idempotency key phải gắn với request hash; same key + different hash → `409 IDEMPOTENCY_KEY_CONFLICT`.
- Routing phải map ổn định `URL/TEXT/ENTITY/QR` sang processor/routing key tương ứng.
- Phone/Bank dùng chung `POST /v1/scans/entity` + `entityType`.
- `NO_DATA` nghĩa là **không có dữ liệu uy tín**, không được diễn giải thành verified-safe.
- Scanner worker không gọi `ThreatQuery`; ThreatQuery là core-only boundary.
- Rule/Risk phải có port để scanner module không import implementation nội bộ.
- Rule definition phải versionable; baseline không hard-code toàn bộ logic vào controller.

## 5. Logic flow

```text
HTTP request từ module scan
→ validate request
→ resolve AccessContext / EntitlementContext
→ authorize scan type + quota policy
→ validate Idempotency-Key/request hash
→ ScanTypeResolver
   ├─ URL
   ├─ TEXT
   ├─ ENTITY -> PHONE | BANK_ACCOUNT
   └─ QR
→ ProcessorRegistry / JobRouter
→ produce DispatchDecision / fake bus publish
```

Entity contract:

```text
POST /v1/scans/entity
→ entityType strategy
→ validate/normalize input
→ later M08 ThreatQuery/reputation processing
→ result may contain NO_DATA
→ NO_DATA never mapped to SAFE automatically
```

Risk seam:

```text
UnifiedSignalSet
→ RiskEvaluationPort
→ Rule Engine / Fusion / Composer implementation ở I-MVP02
```

## 6. API / event / port contract cần chốt

### Public scan endpoints

```text
POST /v1/scans/url
POST /v1/scans/text
POST /v1/scans/entity
POST /v1/scans/qr
```

### Routing semantics

```text
URL    -> scan.url.requested / q.scan.url
TEXT   -> scan.text.requested / q.scan.text
ENTITY -> scan.entity.requested / q.scan.entity
QR     -> scan.qr.requested / q.scan.qr
```

### Internal ports

```text
RiskEvaluationPort.evaluate(signals, scanProfile)
ThreatQuery.lookup(indicator)
```

## 7. Data / repository baseline

W09 tối thiểu phải có design/migration/repository seam cho:

```text
rules
rule_versions
```

Có thể seed một active rule set nhỏ để W10 Risk Core chạy deterministic tests. Admin CRUD sâu chưa cần.

Idempotency storage dùng Redis theo M12/M13 contract; exact key format là implementation detail nhưng phải include request hash và TTL configurable.

## 8. Failure / security cases

- Idempotency key reuse với payload khác → 409.
- Unsupported scan/entity type → validation error, không publish job.
- Quota denied → reject trước dispatch; không enqueue tốn tài nguyên.
- Guest/user không có ownership hợp lệ → không cấp quyền đọc result chỉ vì biết `scanId`.
- Rule repository unavailable ở W10 integration → fail safe rõ ràng; không silently dùng random rule set.

## 9. Phát triển độc lập / mocks

- `FakeMessageBus` cho dispatch.
- `FakeAccessContext`/`FakeEntitlementContext`.
- `MockThreatQuery`.
- `MockRiskEvaluationPort`.
- Seed rule fixtures.
- ENTITY fixtures cho PHONE/BANK/NO_DATA.

## 10. Test plan

### Contract tests

- 4 public scan types route đúng.
- ENTITY PHONE/BANK chọn đúng strategy contract.
- same idempotency key + same hash → replay/return same semantic result theo policy.
- same key + different hash → 409.
- Guest accepted cho public scan nhưng persistent history policy = false.
- `NO_DATA` serialization khác với SAFE result.
- Seed rule version load được qua repository.

## 11. Acceptance Criteria

- [ ] AccessContext/EntitlementContext contract dùng được cho Guest và authenticated/free.
- [ ] Idempotency-Key gắn request hash; conflict trả 409.
- [ ] ScanTypeResolver/ProcessorRegistry route đủ URL/TEXT/ENTITY/QR.
- [ ] Unified entity endpoint PHONE/BANK được chốt.
- [ ] NO_DATA semantics được test và không map thành SAFE.
- [ ] RiskEvaluationPort và ThreatQuery seam được freeze để team dùng mock.
- [ ] rules/rule_versions baseline có migration/repository/seed đọc được.
- [ ] Contract tests cho 4 scan types xanh.
- [ ] Không implement lifecycle/result consumer trùng trách nhiệm I-MVP02.

## 12. Deliverables

- Scan/entity contract models + fixtures.
- Idempotency/authorization contract tests.
- Job routing registry seam + FakeMessageBus adapter.
- RiskEvaluationPort + ThreatQuery interface/mock.
- `rules/rule_versions` baseline repository/migration + seed.
- Evidence: contract test report + demo dispatch bằng fake bus.

## 13. Handoff sang W10

`I-MVP02` dùng các seam này để triển khai lifecycle + worker result consumer + risk core. Khải/Thắng có thể integrate URL/Text vào fake scan platform mà không import code nội bộ của Kiên.
