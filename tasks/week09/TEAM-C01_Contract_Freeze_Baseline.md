# TEAM-C01 — Contract Freeze v1 + Baseline Audit

**Owner:** Cả nhóm (một người làm contract editor tại một thời điểm)  
**Cycle:** B0 · 27/09/2026 → 05/10/2026  
**Goal:** khóa các seam cần để 4 owner code song song mà không đoán contract.


> **Nguồn chuẩn:** Architecture V3.3 · Modules Specification V1.3 · Schema V1.0 Draft · Feature Backlog V2.  
> Nếu tài liệu task này mâu thuẫn với các tài liệu chuẩn trên, **tài liệu chuẩn thắng**. Các mục ghi “Working decision” là quyết định triển khai tạm thời cho sprint và phải được review nếu muốn đưa vào contract chuẩn.


## 1. Outcome cần đạt

Đến cuối task, repository phải có **một bộ contract/fixture được team cùng chấp nhận** cho các seam của 2 tuần đầu. Sau freeze, thay đổi breaking phải qua cross-owner review.

## 2. Contract phải freeze

### Auth / Access

```text
AccessContext
EntitlementContext
AuthResult
JWT claims: iss/aud/sub/sid/roles/iat/exp
refresh/session semantics
NotificationPort (mock đủ dùng)
```

### Scan platform

```text
HTTP scan endpoints URL/TEXT/ENTITY/QR
ScanType + EntityType
ScanJobEnvelope
WorkerAnalysisResult
AnalysisSignal
DerivedIndicator
RiskResult mock shape
Idempotency error contract
```

### Messaging

```text
exchange antiscan.topic
q.scan.url / q.scan.text / q.scan.entity / q.scan.qr / q.scan.result
routing keys scan.*.requested / analyzed|parsed
eventId/jobId/correlationId/eventVersion/attempt/scanId
```

## 3. Input

- Architecture V3.3.
- Modules Specification V1.3.
- Schema V1.0 Draft.
- Backlog V2/WP catalog.
- Code hiện có trong repo.

## 4. Output

```text
contracts/
  openapi/...
  events/...
  fixtures/...
  README.md
```

Tên thư mục chỉ là gợi ý; dùng layout repo hiện tại nếu đã có convention.

Bắt buộc có fixture tối thiểu:

```text
fake guest/free AccessContext
URL scan request + worker result
TEXT scan request + worker result
RiskResult SAFE/CAUTION/DANGER
invalid event envelope
idempotency conflict example
```

## 5. Business flow

```text
Mỗi owner đưa contract mình produce/consume
→ cả nhóm review conflict
→ một editor cập nhật canonical contract files
→ producer test fixture
→ consumer test cùng fixture
→ merge main
→ freeze v1 cho cycle
```

## 6. Baseline audit

Rà code đã có trước 27/09 và đánh dấu:

```text
DONE / PARTIAL / NOT STARTED / OBSOLETE
```

Không xoá code cũ chỉ vì architecture đổi; mở issue/migration task nếu cần.

Audit tối thiểu:
- auth/session implementation có khớp JWT+opaque-refresh+auth_sessions không;
- endpoint cũ `/v1/scan/*` vs canonical `/v1/scans/*`;
- worker nào còn gọi sync core/DB/cache;
- RabbitMQ topology/routing key;
- schema migration có khớp V1.0 Draft;
- tests/fixtures hiện có.

## 7. Change policy sau freeze

Breaking change gồm:
- rename/remove field;
- đổi queue/routing key;
- đổi status/enum semantics;
- đổi ownership/security rule;
- đổi schema field mà consumer phụ thuộc.

Phải có:
1. issue mô tả impact;
2. review owner producer + consumer;
3. update fixture/contract test trước implementation.

## 8. Acceptance criteria

- [ ] `AccessContext`/`EntitlementContext` freeze.
- [ ] auth session/token contract freeze.
- [ ] 4 scan endpoint + request shapes freeze.
- [ ] job/result event envelope freeze.
- [ ] `AnalysisSignal`, `DerivedIndicator`, `RiskResult` mock freeze.
- [ ] routing keys/queues cần cho 2 tuần đầu freeze.
- [ ] producer/consumer cùng chạy được ít nhất một contract fixture.
- [ ] baseline audit ghi rõ phần code cũ nào obsolete/partial.
- [ ] mọi thành viên xác nhận contract trên PR/biên bản.

## 9. Evidence

```text
Contract PR merged to main
+ contract test output
+ baseline audit markdown
+ meeting note xác nhận 4 owner
```
