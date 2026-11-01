# Báo cáo tuần W10 — Kiên

| Trường | Giá trị |
|---|---|
| **Tuần** | W10 · 06/10–12/10/2026 |
| **Sprint** | MVP W10 · CORE-01/R01, mục tiêu G2 |
| **Người** | Lê Trung Kiên |
| **Giờ làm thực tế** | Chưa có timesheet để xác nhận |
| **Thời điểm tổng hợp** | 19/10/2026; bao gồm các bản sửa sau W10 từ 15–19/10, **không ghi nhận ngược là hoàn tất đúng hạn W10** |

## 1. Deadline tuần này — đối chiếu timeline

| Mã | Việc theo phân công | Hạn | Trạng thái tại thời điểm tổng hợp | PR |
|---|---|---|---|---|
| CORE-01 | HTTP Scan Intake, lưu ScanRequest + Outbox, lifecycle/idempotency/quota | W10 | **Đã có implementation và runtime smoke; cần đóng concurrency quota + review gate G2** | Chưa có URL/ID PR được cung cấp |
| R01 | `GET /v1/scans/{scanId}`, ownership, guest proof, failure/timeout | W10 | **Đã có API/owner-IDOR tests; guest-proof HTTP E2E còn thiếu** | Chưa có URL/ID PR được cung cấp |

## 2. Đã làm gì — kết quả có thể kiểm tra

- Kết nối bốn endpoint `POST /v1/scans/{url,text,entity,qr}` với `ValidatedScanCommand` và `ScanIntakeService` → trả `202`/`scanId`; code: `scan/api/ScanController.java`.
- Tạo Flyway V4, JDBC persistence cho `scan_requests` và `outbox_events` → lưu scan/outbox trong cùng giao dịch PostgreSQL; code: `scan/application/ScanPersistenceService.java`, `scan/persistence/*`.
- Bổ sung lifecycle PENDING/PROCESSING/COMPLETED/FAILED, API status và kiểm tra `ownerUserId`/guest proof → test domain và authenticated IDOR smoke.
- Hardening **sau tuần gốc**: scope idempotency thống nhất, DB `ON CONFLICT` chọn winner, release reservation khi rollback, AES-GCM và masking đầu vào → có regression/smoke trong source `(6)`.
- Phát hiện nợ kỹ thuật còn lại: `RedisQuotaGate.refund()` tính lại window tại thời điểm refund nên chưa an toàn khi đổi bucket/double-release.

## 3. Đã đọc / review code của ai

| PR đã review | Của ai | Kết luận | Phát hiện |
|---|---|---|---|
| Chưa có bằng chứng PR review chéo trong dữ liệu được cung cấp | — | **Chưa xác nhận** | Cần Hùng review business/security trước nghiệm thu |

## 4. Đang bị chặn / đang chặn người khác

| Loại | Nội dung | Với ai | Từ ngày | Cách gỡ |
|---|---|---|---|---|
| Tôi đang bị chặn | Guest proof issuer/entitlement resolver thật chưa có bằng chứng handoff | Hùng | Chưa xác nhận | Thống nhất proof format và test Guest POST/GET qua HTTP |
| Tôi đang chặn người khác | Quota cross-window/refund chưa có idempotent reservation token | API reviewers / người dùng Scan | Sau khi review 19/10 | Sửa `RedisQuotaGate`, bổ sung Redis+PostgreSQL concurrent tests |

## 5. Số liệu tuần

| Chỉ số | Giá trị / nguồn |
|---|---|
| Số commit W10 | **Chưa xác minh** — source ZIP không cung cấp thống kê commit cho riêng W10 |
| PR mở / merge | **Chưa xác minh** — branch feature ở trạng thái WIP |
| Số test thêm trong W10 | **Chưa tách được theo tuần** |
| Maven | Báo cáo Agent 19/10 trước follow-up: **155 tests PASS**; không phải test run độc lập sau thay đổi `(6)` |
| Contract tests | Báo cáo Agent: **21 PASS** |
| Runtime | Báo cáo Agent 19/10: PostgreSQL + Redis + RabbitMQ smoke PASS; follow-up `(6)` bị gián đoạn khi lấy exit code |
| G2 | **Chưa ký nghiệm thu**; quota reservation vẫn có edge case |

## 6. Kế hoạch tuần sau

| Mã | Việc | Hạn | Ghi chú |
|---|---|---|---|
| CORE-01 | Quota reservation token/idempotent refund; multi-thread DB+Redis tests | W11 (nợ W10) | Ưu tiên P0 |
| R01 | Guest proof HTTP E2E + review owner/IDOR | W11 (nợ W10) | Phối hợp Hùng |
| CORE-01 | Broker result pipeline/outbox/inbox | W11 | Không claim Worker production từ fixture |

## 7. Rủi ro tự nhận thấy

| Rủi ro | Mức | Dấu hiệu | Đề xuất |
|---|---|---|---|
| Quota sai khi race/đổi window | Cao | `RedisQuotaGate` refund tính lại `Instant.now()` | Token cố định bucket, release idempotent, Redis integration test |
| Thiếu chứng cứ review/DoD | Trung bình | Chưa có PR/approval/CI merge evidence | Mở Draft PR, mời Hùng review |

## 8. Báo cáo tính năng đã nộp tuần này

| Mã nhóm | File báo cáo |
|---|---|
| CORE-01 | `BCTN_CORE-01_Scan_Intake_Lifecycle_Kien.md` — **bản nháp, chưa đóng** |
| R01 | `BCTN_R01_Theo_doi_trang_thai_scan_Kien.md` — **bản nháp, chưa đóng** |

*Nội dung tổng hợp ngày 19/10/2026; **không giả định đã nộp 12/10/2026** · Mẫu: Báo cáo tuần v1.0*
