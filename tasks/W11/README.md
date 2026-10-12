# W11 — First Real End-to-End Flows

**Plan:** V3.0 Draft  
**Period:** 13/10–19/10/2026  
**Feature freeze:** 26/10/2026  
**Release:** v0.1.0 — 30/10/2026

## 1. Weekly Goal

Có ít nhất một chức năng Scan và các chức năng Authentication cơ bản chạy xuyên Web → Backend → Storage/Worker → Result, được thành viên khác trực tiếp kiểm thử.

Không chạy theo số lượng PR; đo tiến độ bằng full-flow gates.

## 2. Priority Outcomes

### P0 — Bắt buộc ưu tiên
1. S01 URL Scan: đạt G4 trên môi trường tích hợp.
2. A01 Registration: đạt G4.
3. A02 Login: đạt G4.
4. CORE-01 và R01: hỗ trợ lifecycle/result thật.
5. R02 Result Detail: sử dụng API thật.

### P1 — Mở rộng song song
1. S02 Text Scan: tích hợp đến G3.
2. S03 Phone Reputation: component/contract tests đến G2.
3. S04 Bank Reputation: component/contract tests đến G2.
4. A06 Logout: tích hợp đến G3/G4.
5. TE03 AI Worker: AMQP worker và error handling đến G2.

### Stretch
- A04 Reset Password: G2 nếu đủ capacity.

## 3. Assignment

| Member | Task IDs | Highest-priority outcome |
|---|---|---|
| Hùng | A01, A02, A06; A04 stretch | Registration/Login E2E |
| Kiên | CORE-01, R01, S03, S04 | Real scan lifecycle + RiskResult |
| Khải | S01, INFRA-01 | URL Scan E2E on staging |
| Thắng | R02, S02, TE03 | Real Result UI + Text integration |

## 4. Integration Sequence

1. Khải xác minh PostgreSQL, Redis, RabbitMQ, MinIO và staging.
2. Kiên nối HTTP intake → persistence → queue → result consumer → RiskResult.
3. Khải nối URL Worker thật.
4. Thắng nối Result UI với result/status API thật.
5. Thắng tích hợp Text Worker vào cùng pipeline.
6. Hùng hoàn thiện Registration/Login trên Web.
7. Người test chéo chạy UAT từ browser với test fixtures.

Các bước 2–6 có thể code song song bằng frozen contracts; thứ tự trên là thứ tự nghiệm thu integration.

## 5. Peer Review Matrix

| Implementer | Reviewer / Cross-tester |
|---|---|
| Hùng | Khải |
| Kiên | Hùng |
| Khải | Thắng |
| Thắng | Kiên |

## 6. Weekly Go/No-Go Checklist

- [ ] Staging có URL và tài khoản test cho peer testers.
- [ ] URL Scan chạy thật từ frontend đến final result.
- [ ] Registration + Login chạy thật.
- [ ] Không có critical authorization/SSRF/secrets failure.
- [ ] Duplicate events và worker failures có tests.
- [ ] Có logs/correlation ID để điều tra lỗi.
- [ ] CI, migration, message queue smoke PASS.
- [ ] Peer UAT có screenshot/log/evidence.
- [ ] Các flow chưa đạt G3/G4 được ghi rõ, không tự nhận DONE.
- [ ] Scope chuyển W12 có owner và mức ưu tiên.

## 7. PM Weekly Review

Mỗi người báo cáo:
- Flow/task đang phụ trách.
- Gate hiện tại và evidence.
- Điểm nghẽn ảnh hưởng người khác.
- Phần việc còn lại và effort estimate.
- Kịch bản UAT đã PASS/FAIL.

**Weekly acceptance:** URL Scan, Registration và Login là ba mục tiêu G4 quan trọng nhất. Các flow mở rộng chỉ được nhận DONE khi đạt tiêu chí nghiệm thu riêng.