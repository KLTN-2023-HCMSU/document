# ADR-09 · PostgreSQL làm nguồn sự thật cho Scan + Transactional Outbox; Redis điều phối idempotency/quota

> **BẢN DỰ THẢO / CẦN REVIEW.** Số ADR-09 theo điểm bắt đầu được template gợi ý; **kiểm tra trùng số trong repo tài liệu trước khi nộp chính thức**. Đây là ghi nhận lựa chọn đang hiện diện trong code, **chưa phải quyết định nhóm đã approve**.

| Trường | Giá trị |
|---|---|
| **Số** | ADR-09 (dự kiến) |
| **Tiêu đề** | Durable scan/outbox tại PostgreSQL; Redis coordination cho idempotency và quota |
| **Người đề xuất** | Kiên |
| **Ngày** | 2026-10-19 |
| **Trạng thái** | ☒ Đề xuất · ☐ Đã chốt · ☐ Bị thay thế |
| **Liên quan** | W10 CORE-01, W11 CORE-01, R01 |
| **Ảnh hưởng** | Hùng, Khải, Thắng |

## 1. Bối cảnh

Scan được nhận qua HTTP nhưng xử lý bất đồng bộ bởi Worker. Backend phải cấp scanId và lưu trạng thái trước khi RabbitMQ hoặc Worker thành công. Nếu chỉ `INSERT scan_requests` rồi publish RabbitMQ trực tiếp, có thể tạo scan mà không có job (process crash sau commit). Redis cung cấp tốc độ cho idempotency/quota nhưng không nằm trong PostgreSQL transaction. W10 đã gặp các lỗi reservation stale/quota rollback; W11 cần outbox/inbox và recovery.

## 2. Ràng buộc

| # | Ràng buộc | Nguồn |
|---|---|---|
| 1 | Reuse W09 `ValidatedScanCommand`, routing, request hash | `document/tasks/W09/I-MVP01_...md` |
| 2 | `scan_requests`, `outbox_events`, `scan_results`, `scan_signals`, `processed_events` theo schema | Architecture V3.3 / Schema V1.0 / migration V4–V5 |
| 3 | WorkerResult có thể giao trùng, cần consumer idempotent | `document/tasks/W10/Kien.md`, `W11/Kien.md` |
| 4 | Không lộ input nhạy cảm trong DB display/outbox plaintext | W10 NFR privacy |

## 3. Các phương án

| Phương án | Mô tả | Ưu | Nhược |
|---|---|---|---|
| **A** | HTTP handler publish RabbitMQ sau DB commit | Đơn giản | Crash trước publish có thể mất job |
| **B** | PostgreSQL transaction scan + outbox; background publisher; Redis lease/quota | Recover được job, durable replay | Thêm publisher, retry, consistency Redis/PG |
| **C** | Redis là nguồn trạng thái chính | Nhanh và đơn giản lúc đầu | Không phù hợp durable lifecycle/history/ownership |

## 4. Quyết định đề xuất

**Đề xuất chọn B** vì phù hợp migration V4/V5 đang triển khai, bảo đảm scan và intent publish cùng commit và hỗ trợ RabbitMQ at-least-once delivery. PostgreSQL là nguồn chính cho scanId/status/result; Redis chỉ giữ coordination/quota. Quota phải được reserve bằng token cố định window, refund idempotent; không dựa vào `DECR` ở bucket tính tại thời điểm refund như snapshot `(6)`.

## 5. Hệ quả

| Loại | Nội dung |
|---|---|
| Tích cực | Dễ recovery từ PG, replay ổn định, worker decoupled |
| Tiêu cực | Phải có scheduler/claim lease, idempotent inbox, observability |
| Rủi ro | Race giữa result/timeout; stale Redis lease; duplicate broker delivery |
| Giảm rủi ro | CAS/row lock finalization, quota tokenized release, PG/Redis/RabbitMQ concurrent integration tests |

## 6. Ảnh hưởng tới người khác

| Ai | Ảnh hưởng | Đã báo chưa |
|---|---|---|
| Khải | Worker phải consume contract queue/result, retry-safe | Chưa có bằng chứng |
| Hùng | Duyệt quota/access semantics và degraded risk policy | Chưa có bằng chứng |
| Thắng | UI polling scanId/status, không giả định `202` đã có verdict | Chưa có bằng chứng |

## 7. Khi nào xem lại quyết định này

Khi đội thống nhất policy quota/guest proof và hoàn thành test: same-key concurrency (một scan/outbox/quota), cross-window refund, crash/restart publisher/consumer, concurrent timeout/result. Nếu không đạt thì ADR cần điều chỉnh trước khi chốt.

*Người đề xuất: Kiên · 19/10/2026 · Chưa có standup/approval evidence · Mẫu: ADR v1.0*
