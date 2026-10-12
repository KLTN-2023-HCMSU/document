# W11 — Khải: URL Scan E2E & Staging Reliability

**Owner:** Khải  
**Cross-tester:** Thắng  
**Primary goal:** S01 đạt G4  
**Priority:** P0

## S01 — Scan URL Full Flow

### User Journey
Guest mở Web → nhập URL → nhấn Scan → nhận scanId → xem processing → nhận RiskResult.

### FR
- FR01: URL form hoạt động trên Web.
- FR02: Input validation và API error handling.
- FR03: POST URL scan dùng approved OpenAPI.
- FR04: URL job đi qua RabbitMQ thật.
- FR05: URL worker trả AnalysisSignal đúng contract.
- FR06: RiskResult được backend lưu và trả về.
- FR07: Web hiển thị kết quả từ API thật.
- FR08: Hỗ trợ trạng thái failed/degraded.

### NFR
- SSRF guard phải chặn private/internal destinations trước khi network fetch.
- Xử lý redirect/DNS theo security policy.
- Worker timeout không làm scan treo.
- Không tự động điều hướng tới URL nghi ngờ.
- Có correlation ID xuyên API/worker/result.
- Retry không tạo duplicate processing ngoài policy.

### Integration Dependencies
- Kiên: CORE-01, R01, RiskResult.
- Thắng: R02 Result UI.
- Hùng: Guest access/quota semantics.
- Khải: INFRA-01 và URL Worker.

### Manual UAT
- [ ] URL hợp lệ → RiskResult thật.
- [ ] URL sai → validation error.
- [ ] URL nguy hiểm/internal → SSRF blocked.
- [ ] Guest hết quota → không xử lý job.
- [ ] Same key retry → không duplicate.
- [ ] Worker timeout → terminal/degraded.
- [ ] Reload page theo policy vẫn đọc đúng kết quả khi có quyền.
- [ ] Bằng chứng: staging URL, scanId, logs và screenshots.

**Target: G4 do Thắng kiểm thử.**

---

## INFRA-01 — Staging Integration & Security Gate

### System Flow
Merge PR → CI → Deploy staging → Migrations → Services Ready → Test flows → Rollback if failed.

### FR
- FR01: Chạy PostgreSQL, Redis, RabbitMQ và MinIO.
- FR02: CI tự chạy tests tối thiểu và fail khi có lỗi.
- FR03: Backend/worker dùng đúng runtime configs.
- FR04: Staging có seed/test fixtures.
- FR05: Có readiness/health checks và logging.
- FR06: Có hướng dẫn deploy và rollback.
- FR07: Có test cho RabbitMQ routing/retry/DLQ baseline.

### NFR
- Secrets không vào repository/logs.
- Clean migration PASS.
- Worker/restart/recovery behavior được kiểm tra.
- Không dùng credentials development cho staging.
- Có correlation tracing và thông tin điều tra lỗi.

### Integration Tests
- [ ] Clean deploy.
- [ ] Postgres migration.
- [ ] RabbitMQ job/result roundtrip.
- [ ] Redis connection.
- [ ] MinIO connectivity.
- [ ] Worker restart/reconnect.
- [ ] Secrets scanning.
- [ ] Rollback rehearsal hoặc documented rollback procedure.

**Target: G3.**

## Handoffs
- Staging URL, credentials test riêng và runbook cho cả nhóm.
- URL fixtures cho Kiên/Thắng.
- Ghi rõ môi trường nào đang dùng real worker và môi trường nào dùng stub.

## Definition of Done
- [ ] S01 đạt G4.
- [ ] INFRA-01 đạt G3.
- [ ] Critical SSRF/security tests PASS.
- [ ] Thắng tự chạy UAT thay vì chỉ xem demo.
- [ ] CI/deploy evidence và blockers được lưu trong tracker.