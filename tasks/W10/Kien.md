# W10 — Kiên: Shared Scan Lifecycle Foundation

**Owner:** Kiên  
**Reviewer:** Hùng  
**Target:** G2  
**Baseline:** Reuse W09 Scan Contracts, Intake, Risk/Threat Ports và Rule Repository.

## CORE-01 — Scan Intake & Async Lifecycle Foundation

### System Flow
HTTP scan request → Access/Quota/Idempotency → ScanRequest persistence → Job dispatch → WorkerResult contract.

### Functional Requirements
- FR01: Dùng lại `ValidatedScanCommand`, `ScanPayload`, AccessContext và EntitlementContext.
- FR02: Kết nối HTTP DTO với Scan Intake.
- FR03: Có persistence cho scan request theo schema đã duyệt.
- FR04: Chuẩn bị publish/consume job/result theo RabbitMQ contract.
- FR05: Định nghĩa lifecycle transition và terminal/error semantics.
- FR06: Idempotency replay không tạo job xử lý trùng ngoài policy.

### Non-functional Requirements
- Queue delivery có thể lặp nên consumer phải idempotent.
- Tránh lost-job giữa DB commit và publish theo outbox/recovery policy.
- Quota và idempotency phải có thứ tự xử lý nhất quán.
- Correlation ID xuyên request/event.
- Không log payload nhạy cảm ngoài policy.

### W10 Deliverables
- HTTP/controller binding hoặc adapter theo OpenAPI.
- ScanRequest migration/repository và persistence tests.
- FakeMessageBus contract tests, real bus smoke khi runtime sẵn sàng.
- Design/test cho lifecycle và duplicate event.
- CI + PostgreSQL migration smoke PASS.

### Tests
- Guest và Free submit đúng.
- Invalid payload bị reject.
- Same idempotency key + same request không sinh trùng.
- Same key + different payload trả conflict.
- Quota reject không publish job.
- DB transaction/publish failure có hành vi khôi phục xác định.

**Target gate:** G2, chưa yêu cầu Risk Fusion hoàn chỉnh.

---

## R01 — Theo dõi trạng thái scan

### User Flow
Submit scan → nhận scanId → mở/truy vấn trạng thái → thấy PENDING/PROCESSING/terminal outcome.

### Functional Requirements
- FR01: API đọc scan status theo approved OpenAPI.
- FR02: Guest/User truy cập đúng scan của mình.
- FR03: Status phản ánh trạng thái đã lưu.
- FR04: Có failure/timeout semantics.
- FR05: Không tiết lộ scan khác khi đoán scanId.

### W10 Deliverables
- Status API contract + stub/DB fixture.
- Ownership/access tests.
- Lifecycle transition tests.
- Tài liệu status mapping cho frontend.

### Tests
- Đọc scan thuộc quyền.
- Đọc scan không thuộc quyền.
- Scan chưa tồn tại.
- State transition không hợp lệ.

**Target gate:** G2.

## Dependencies
- Hùng: AccessContext/EntitlementContext.
- Khải: PostgreSQL/RabbitMQ runtime.
- Thắng: UI và Result contract fixtures.

## Definition of Done
- [ ] Không rewrite W09 shared contracts.
- [ ] Migration và repositories được test.
- [ ] Lifecycle/Status contract tests PASS.
- [ ] Idempotency và access negative tests PASS.
- [ ] PR được Hùng review.
- [ ] Remaining integration scope được ghi rõ cho W11.