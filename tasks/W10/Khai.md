# W10 — Khải: Runtime & URL Scan Foundation

**Owner:** Khải  
**Reviewer:** Thắng  
**Target:** G2

## INFRA-01 — Reproducible Runtime & Staging Foundation

### System Flow
Clean checkout → cấu hình secrets → khởi động dependencies → chạy migration → API/worker kết nối → health checks.

### Functional Requirements
- FR01: Runtime có PostgreSQL, Redis, RabbitMQ và MinIO theo Architecture V3.3.
- FR02: Backend đọc cấu hình từ environment/secrets.
- FR03: Có queue/exchange/binding cần cho scan jobs và results.
- FR04: Có local smoke test và hướng dẫn khởi động.
- FR05: Có CI job kiểm tra build, test, migration/contract tối thiểu.
- FR06: Chuẩn bị cấu hình staging tách biệt development.

### Non-functional Requirements
- Không commit secrets.
- Restart service không làm mất dữ liệu ngoài chính sách.
- Có health/readiness và logs cơ bản.
- Các migrations chạy được trên PostgreSQL sạch.
- Retry/DLQ infrastructure có cấu hình rõ ràng.

### W10 Deliverables
- Docker Compose và environment template.
- Queue roundtrip smoke test.
- Database migration smoke test.
- CI workflow và quick-start instructions.

### Tests
- Clean boot toàn stack.
- Service restart/reconnect.
- Queue publish → consume.
- Missing secret/config báo lỗi rõ ràng.

**Target gate:** G2; staging G3 nếu đã deploy và smoke thật.

---

## S01 — Scan URL Foundation

### User Flow
Guest mở trang URL Scan → nhập URL → submit → request được tiếp nhận và job được chuyển cho worker.

### Functional Requirements
- FR01: Có URL form và client-side validation.
- FR02: Submit qua approved `/v1/scans/url` API contract.
- FR03: URL Worker nhận job fixture, trả AnalysisSignal đúng contract.
- FR04: URL lexical analysis cơ bản.
- FR05: Xử lý malformed/unsupported URL.
- FR06: Có fixture an toàn để kiểm thử URL hợp lệ/rủi ro.

### Non-functional Requirements
- Không fetch URL nội bộ/tùy ý trước khi SSRF guard đạt security tests.
- Không tự động mở URL nguy hiểm trên client.
- Worker có timeout/failure contract.
- Không log thông tin nhạy cảm trong URL ngoài policy.

### W10 Deliverables
- Web URL form.
- Worker job/result fixtures.
- URL lexical tests.
- OpenAPI/client contract tests.

### Tests
- URL hợp lệ.
- URL malformed.
- Worker job fixture.
- SSRF negative fixtures trước khi bật network fetch.

**Target gate:** G2.

## Dependencies
- Kiên: scan intake, lifecycle, risk/result contract.
- Thắng: shared Result UI.
- Hùng: Guest access/quota context.

## Definition of Done
- [ ] Runtime smoke PASS.
- [ ] Queue roundtrip PASS.
- [ ] URL worker contract tests PASS.
- [ ] Không có unsafe URL fetching.
- [ ] PR được Thắng review.
- [ ] W11 integration checklist và blockers đã ghi lại.