# Weekly Plan & Release Train — V3.0 DRAFT

> Giữ nguyên **release day** của V2.1; chỉ điều chỉnh cách tổ chức work. Không tự dời 30/10/2026.

## Approved release milestones

| Version | Date | Goal | Release gate |
| --- | --- | --- | --- |
| v0.1.0 | 2026-10-30 | MVP basic / breadth; M01–M14 minimum working capability on staging | RC 29/10; feature freeze 26/10; G4 peer tests + critical NFR pass |
| v0.2.0 | 2026-11-20 | Depth Expansion 1 | Expanded account/security, admin, follow-up, URL/evidence |
| v0.3.0 | 2026-12-11 | Depth Expansion 2 | Rule/reputation/UX enhancements + reliability |
| v0.4.0 | 2027-01-08 | Beta candidate | Security/quality readiness + regression |
| v0.5.0-beta.1 | 2027-01-22 | Public beta | Approved public beta checklist |
| DATA-FREEZE | 2027-03-31 | Data/feedback freeze | Reproducible research dataset |
| v1.0.0-rc1 | 2027-05-28 | Final RC | Final regression/security/docs |
| v1.0.0 | 2027-06-04 | Final product baseline | Final project handoff |


## W10–W19 checkpoint

| Week | Start | End | Milestone / gate |
| --- | --- | --- | --- |
| W10 | 2026-10-06 | 2026-10-12 | Confirm merged W09 baseline, slice contracts, bootstrap staging; do not claim full E2E |
| W11 | 2026-10-13 | 2026-10-19 | URL walking skeleton + local auth + base result; parallel text/entity/report skeleton |
| W12 | 2026-10-20 | 2026-10-26 | Finish MVP breadth, verify all M01–M14 capability; feature freeze 26/10 |
| W13 | 2026-10-27 | 2026-11-02 | No new features; RC 29/10; v0.1.0 release 30/10; verification / rollback |
| W14 | 2026-11-03 | 2026-11-09 | Depth 1: account recovery/share/report status + source management |
| W15 | 2026-11-10 | 2026-11-16 | Depth 1: audit investigation/rule admin/notification center |
| W16 | 2026-11-17 | 2026-11-23 | 20/11 v0.2.0 and depth hardening; freeze and regression |
| W17 | 2026-11-24 | 2026-11-30 | Depth 2: security/UX/rule refinement |
| W18 | 2026-12-01 | 2026-12-07 | Depth 2 reliability / evaluator / release prep |
| W19 | 2026-12-08 | 2026-12-14 | 11/12 v0.3.0 and regression |


## MVP basic / v0.1.0 — target hoàn thiện trước freeze 26/10

| Flow | Title | Captain | Target end week | Peer |
| --- | --- | --- | --- | --- |
| A01 | Đăng ký tài khoản & xác thực email OTP | Hùng | W11 | Khải |
| A02 | Đăng nhập bằng email / mật khẩu | Hùng | W11 | Khải |
| A03 | Đăng nhập Google OIDC / liên kết identity an toàn | Hùng | W12 | Khải |
| A06 | Đăng xuất phiên hiện tại | Hùng | W11 | Khải |
| A08 | Xem / cập nhật hồ sơ cá nhân | Khải | W12 | Thắng |
| S01 | Guest Scan URL → RiskResult | Khải | W11 | Thắng |
| S02 | Scan tin nhắn / bài đăng giao dịch | Thắng | W12 | Kiên |
| S03 | Kiểm tra reputation số điện thoại | Kiên | W12 | Hùng |
| S04 | Kiểm tra reputation tài khoản ngân hàng | Kiên | W12 | Hùng |
| S05 | Quét/upload QR/VietQR → nested scan | Thắng | W12 | Kiên |
| R01 | Theo dõi trạng thái scan đang xử lý | Kiên | W11 | Hùng |
| R02 | Xem kết quả scan chi tiết | Thắng | W11 | Kiên |
| R03 | Xem, lọc và phân trang lịch sử scan | Thắng | W12 | Kiên |
| R04 | Xuất kết quả scan PDF/HTML | Thắng | W12 | Kiên |
| C01 | Gửi báo cáo đối tượng nghi lừa đảo | Hùng | W11 | Khải |
| C03 | Moderator xác minh / từ chối báo cáo | Hùng | W12 | Khải |
| T01 | Admin xem danh sách nguồn Threat Intelligence | Khải | W12 | Thắng |
| T03 | Admin đồng bộ / import Threat Intelligence | Kiên | W12 | Hùng |
| N01 | Event → gửi notification tối thiểu | Khải | W12 | Thắng |
| O01 | Admin xem / lọc Audit Log | Khải | W12 | Thắng |


## Depth expansion sau v0.1.0

| Flow | Title | Target release | Target week |
| --- | --- | --- | --- |
| A04 | Quên mật khẩu → OTP → đặt mật khẩu mới | v0.2.0 | W14 |
| A05 | Đổi mật khẩu khi đã đăng nhập | v0.2.0 | W14 |
| A07 | Đăng xuất tất cả thiết bị | v0.2.0 | W15 |
| R05 | Chia sẻ kết quả scan theo chính sách quyền | v0.3.0 | W17 |
| C02 | Xem trạng thái báo cáo đã gửi | v0.2.0 | W14 |
| T02 | Admin bật/tắt nguồn Threat Intelligence | v0.2.0 | W14 |
| T04 | Admin xem rules và lịch sử versions | v0.2.0 | W15 |
| T05 | Admin tạo rule version mới | v0.2.0 | W15 |
| T06 | Admin kích hoạt rule version → scan áp dụng rule mới | v0.2.0 | W16 |
| N02 | Người dùng xem notification center / read-unread | v0.2.0 | W15 |
| N03 | Người dùng quản lý preferences notification | v0.3.0 | W17 |
| O02 | Operator điều tra lỗi Scan/Worker/AI | v0.2.0 | W15 |


## Critical path: dependency path và thực tế snapshot

- W10: W09 contracts đã merge nhưng client Web còn trang khởi tạo, Mobile placeholder; Docker compose hiện thiếu RabbitMQ và MinIO. **Không ghi real integration là PASS khi chỉ có FakeMessageBus**.
- W11: cần vòng thực `API → Postgres → outbox/queue → worker → WorkerResult → RiskResult → Web` (S01 + R01 + R02). Auth local (A01/A02/A06) có fixture dùng riêng.
- W12: hoàn thành P0 flows M01–M14, đặc biệt Text/Phone/Bank/QR, moderation/reputation, AI worker, notifications, basic audit; risk cao, tập trung basic capability.
- W13: chỉ xử lý blocker/regression; 29/10 RC, **30/10 release**.

## Definition of v0.1.0 release readiness

- [ ] Không có blocker security critical/open CVE high/critical chưa có quyết định.
- [ ] `docker compose`/staging reproducible, Flyway PostgreSQL migration và RabbitMQ roundtrip smoke PASS.
- [ ] Scan URL/TEXT/PHONE/BANK/QR tạo final RiskResult hoặc degraded đúng contract.
- [ ] Local auth register/verify/login/refresh/logout; basic guest/RBAC/quota; Google basic theo approved plan.
- [ ] Community report submit → moderation VERIFIED → reputation observation; source/threat basic list/import.
- [ ] Seeded rule evaluation, result/history/export basic; notification tối thiểu; audit correlation trace.
- [ ] Peer UAT evidence cho các MVP basic flows; coverage cả happy/negative.
- [ ] Web/Mobile parity gap được báo cáo chính xác và có quyết định scope, không mặc định PASS khi mobile placeholder.
- [ ] Rehearsal rollback/redeploy, release notes và go/no-go meeting được ghi nhận.

## Capacity / cảnh báo

- Có **20 P0 flows** dự kiến cần basic demo trước 26/10. Đây là lịch rất căng nếu runtime/client mới ở mức baseline W09.
- Việc chia P0 flow không đảm bảo chắc chắn đạt ngày 30/10. PM nên tạo risk/mitigation ngay, không đánh đổi an toàn (SSRF/IDOR/token).
- P1/P2 không đồng nghĩa vĩnh viễn bỏ; vẫn trong release train theo bản gốc.
