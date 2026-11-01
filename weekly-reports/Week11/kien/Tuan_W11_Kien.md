# Báo cáo tuần W11 — Kiên

| Trường | Giá trị |
|---|---|
| **Tuần** | W11 · 13/10–19/10/2026 |
| **Sprint** | MVP W11 · CORE-01/R01 G3; S03/S04 G2 có điều kiện |
| **Người** | Lê Trung Kiên |
| **Giờ làm thực tế** | Chưa có timesheet để xác nhận |
| **Ngày tổng hợp** | 19/10/2026 · từ snapshot `Scam-Risk-Detector(6).zip` |

## 1. Deadline tuần này — đối chiếu timeline

| Mã | Việc theo phân công | Hạn | Trạng thái tại snapshot | PR |
|---|---|---|---|---|
| CORE-01 | RabbitMQ job/result → signal → RiskResult + recovery | W11 | **PARTIAL G3**; URL fixture broker roundtrip đã được Agent báo PASS, production Worker chưa tích hợp |
| R01 | Status theo lifecycle/result thật, phân quyền & polling | W11 | **PARTIAL G3**; authenticated IDOR smoke có; guest UAT/polling chưa đầy đủ |
| S03 | PHONE normalize + reputation lookup + evidence/NO_DATA | W11 | **PARTIAL**; validation có, provider production chưa có |
| S04 | BANK_ACCOUNT bankCode + reputation lookup | W11 | **G1/PARTIAL**; chống collision có; provider chưa phê duyệt |

## 2. Đã làm gì — kết quả có thể kiểm tra

- Tạo V5 (`scan_results`, `scan_signals`, `processed_events`) và RabbitMQ outbox publisher/result consumer → Agent ghi nhận URL pipeline có broker roundtrip với fixture worker.
- Bổ sung AES-GCM, deadline recovery, inbox dedup và authenticated ownership/IDOR runtime cases; mã nguồn hiện diện tại `scan/privacy/`, `scan/messaging/`, `scan/result/`, `scan/application/`.
- TDD K1: sửa `ScanIntakeService` để refund quota khi cạnh tranh DB dẫn đến replay; 6 focused tests PASS **theo báo cáo Agent follow-up**.
- TDD K4: không kết luận SAFE khi chỉ có NO_DATA/unavailable; tuy nhiên **NO_DATA + LOW vẫn có thể trả SAFE**, nên chưa kết luận fix hoàn chỉnh.
- TDD K6: thêm `q.scan.text` và TEXT request/result bindings; 1 topology test PASS **theo Agent**, chưa có TEXT broker roundtrip.

## 3. Đã đọc / review code của ai

| PR đã review | Của ai | Kết luận | Phát hiện |
|---|---|---|---|
| Chưa có bằng chứng review PR của thành viên khác | — | Chưa xác nhận | Hùng được chỉ định reviewer W10/W11; cần review trước merge |

## 4. Đang bị chặn / đang chặn người khác

| Loại | Nội dung | Với ai | Từ ngày | Cách gỡ |
|---|---|---|---|---|
| Tôi đang bị chặn | URL/TEXT production Worker chưa bàn giao integration | Khải / Thắng (theo phân công W11) | Chưa xác nhận | Test contract và broker với Worker thật |
| Tôi đang bị chặn | Entitlement/Guest Proof production; approved PHONE/BANK reputation provider | Hùng / nhóm dữ liệu | Chưa xác nhận | Chốt nguồn dữ liệu và handoff API |
| Tôi đang chặn người khác | Timeout/result race và degraded Risk Fusion chưa hoàn chỉnh | R01/Result UI | Review 19/10 | Transactional finalization + tests; freeze Result semantics |

## 5. Số liệu tuần

| Chỉ số | Giá trị / nguồn |
|---|---|
| Commit/PR mới | **Chưa xác minh**; Agent báo không commit/push/merge trong đợt follow-up |
| Maven | **155 tests PASS** trong báo cáo completion trước follow-up; focused follow-up **11 PASS** (không phải full run sau sửa) |
| Contract | **21 tests PASS** theo báo cáo completion |
| RabbitMQ | URL fixture broker roundtrip **PASS theo báo cáo trước**; TEXT topology **đã có code**, broker test mới chưa xác minh |
| Secret scan | **Chưa xanh** do 2 synthetic fixtures bị scanner phát hiện; chưa có production secret được chứng minh |
| CORE-01/R01 G3 | **Chưa đạt gate**; không có Worker production/UAT và còn consistency risk |

## 6. Kế hoạch tuần sau

| Mã | Việc | Hạn | Ghi chú |
|---|---|---|---|
| CORE-01 | K3 result-timeout race; K1 quota token; K4 fail-safe mixed evidence | W12 | P0 độc lập, không chờ thành viên khác |
| CORE-01 | K5 active rule set; K6 TEXT broker roundtrip; K7 delivery recovery | W12 | Cần thống nhất fusion policy / handoff worker |
| R01 | HTTP guest-proof matrix và manual polling UAT | W12 | Hùng + Thắng |
| S03/S04 | Reputation fixtures/adapter + dữ liệu được phê duyệt | W12 | Không fake production provider |

## 7. Rủi ro tự nhận thấy

| Rủi ro | Mức | Dấu hiệu | Đề xuất |
|---|---|---|---|
| Scan FAILED vẫn có thể nhận `scan_results` do race | Cao | Consumer đọc status rồi insert result trước terminal update | Row lock/CAS cùng transaction và concurrent PostgreSQL tests |
| Fusion suy diễn SAFE từ evidence thiếu | Cao | `NO_DATA + LOW` vẫn ra `SAFE, degraded=true` | Regression mixed-evidence; chốt chính sách với reviewer |
| Thời gian chờ provider reputation | Trung bình | S03/S04 mới fixture/normalization | Ghi G1/PARTIAL; xin dữ liệu được duyệt |

## 8. Báo cáo tính năng đã nộp tuần này

| Mã | File |
|---|---|
| CORE-01/R01 | `BCTN_CORE-01_Async_Risk_Core_Kien.md`, `BCTN_R01_Status_Runtime_Kien.md` — **bản nháp, chưa đạt G3** |
| S03/S04 | `BCTN_S03_S04_Entity_Reputation_Kien.md` — **bản nháp tiến độ, chưa đóng** |

*Tổng hợp 19/10/2026 · Mẫu: Báo cáo tuần v1.0*
