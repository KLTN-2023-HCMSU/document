# W11 — Kiên: Scan Orchestrator, Status, Phone & Bank

**Owner:** Kiên  
**Reviewer:** Hùng  
**Primary goal:** CORE-01 thực sự xử lý URL job/result và tạo RiskResult  
**Priority:** P0

## CORE-01 — Async Scan Lifecycle & Risk Core Integration

### System Flow
HTTP Intake → ScanRequest → Outbox/Job Publish → WorkerResult Consumer → Signal/Risk Evaluation → RiskResult Persistence.

### FR
- FR01: ScanRequest được lưu trước hoặc nhất quán với job publishing.
- FR02: Có dispatch qua RabbitMQ thật.
- FR03: Consumer đọc kết quả worker đúng contract.
- FR04: Persist analysis signals và final RiskResult.
- FR05: Risk evaluation áp dụng active rule set/fixtures.
- FR06: Lifecycle chuyển sang terminal state đúng policy.
- FR07: Idempotency/duplicate event không sinh double result.
- FR08: Timeout/worker failure có trạng thái kết thúc hoặc degraded hợp lệ.

### NFR
- Không để scan processing vô hạn.
- Có outbox/recovery strategy theo thiết kế.
- Idempotent event processing.
- Correlation ID xuyên pipeline.
- Không mutate dữ liệu scan trái ownership/policy.
- Migration PostgreSQL được kiểm tra trên DB sạch.

### Integration Tests
- [ ] HTTP → DB → RabbitMQ.
- [ ] RabbitMQ → WorkerResult consumer → RiskResult.
- [ ] Duplicate WorkerResult.
- [ ] Same Idempotency-Key retry.
- [ ] Worker failure/timeout.
- [ ] Result và status nhất quán.
- [ ] Consumer restart/retry.

**Target: G3, hỗ trợ S01 đạt G4.**

---

## R01 — Theo dõi trạng thái scan

### User Journey
User/Guest submit scan → nhận scanId → đọc tiến độ → thấy result hoặc failure.

### FR
- FR01: API status theo approved contract.
- FR02: Ownership/guest access proof.
- FR03: State được đọc từ persistence thật.
- FR04: Không có terminal state giả.
- FR05: Client có thể polling theo policy.

### Manual UAT
- [ ] PENDING/PROCESSING → COMPLETED.
- [ ] Failure/timeout → terminal outcome.
- [ ] Không xem được scan của người khác.
- [ ] Không tồn tại → lỗi phù hợp.

**Target: G3, peer test cùng S01.**

---

## S03 — Kiểm tra số điện thoại

### User Journey
Nhập số điện thoại → normalize → ThreatQuery/Reputation → Result.

### FR
- FR01: Normalize/validate PHONE.
- FR02: Lookup dữ liệu reputation.
- FR03: Trả evidence/source phù hợp.
- FR04: `NO_DATA` khác `SAFE`.
- FR05: Source outage có trạng thái unavailable/degraded.

### Tests
- [ ] Known risky phone.
- [ ] Unknown phone → NO_DATA.
- [ ] Invalid phone.
- [ ] Source outage.
- [ ] Deterministic RiskResult fixture.

**W11 target: G2; tích hợp Web E2E tiếp theo.**

---

## S04 — Kiểm tra tài khoản ngân hàng

### User Journey
Nhập STK và thông tin ngân hàng → normalize → lookup → Result.

### FR
- FR01: Dùng ENTITY scan contract, không tạo scan type BANK riêng.
- FR02: Chuẩn hóa STK theo policy.
- FR03: Xử lý bankCode và trường hợp ambiguous.
- FR04: Lookup theo đúng bank/entity identity.
- FR05: Unknown/outage không đánh giá nhầm thành SAFE.

### Tests
- [ ] Known bank account.
- [ ] Same account string ở hai bank.
- [ ] Missing/ambiguous bankCode.
- [ ] Unknown account.
- [ ] Unavailable source.

**W11 target: G2 nếu CORE-01 không còn blocker; nếu chậm, giữ S04 ở G1 và ưu tiên CORE-01.**

## Dependencies
- Khải: runtime, URL worker, HTTP integration.
- Thắng: Result UI và Text integration.
- Hùng: AccessContext/EntitlementContext và cross-test.

## Definition of Done
- [ ] Real RabbitMQ roundtrip PASS.
- [ ] Scan lifecycle và final result persistence PASS.
- [ ] Idempotency, failure, ownership tests PASS.
- [ ] RiskResult contract đúng fixture.
- [ ] Hùng review security/business semantics.
- [ ] S03/S04 ghi gate riêng, không đánh dấu full flow nếu chỉ có contract tests.