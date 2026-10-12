# Anti-Scam — Delivery Plan V3.0 (DRAFT)

> **Lập ngày:** 12/10/2026 · **Status:** DRAFT, chờ 4 thành viên ước lượng/duyệt; chưa thay đổi baseline kiến trúc, module, schema và release dates.

## Mở tài liệu theo vai trò

- [PM Runbook & weekly operating model](00_PM_RUNBOOK.md)
- [Release & weekly delivery plan](08_RELEASE_ROADMAP.md)
- [Shared enablers / integration contracts](09_SHARED_ENABLERS.md)
- [Source snapshot & traceability](10_SOURCE_BASELINE.md)
- [Authentication & Account](01_AUTH_ACCOUNT.md)
- [Scam Detection](02_SCAN_DETECTION.md)
- [Result, History & Export](03_RESULT_HISTORY.md)
- [Community & Moderation](04_COMMUNITY.md)
- [Threat Intelligence & Rules](05_THREAT_RULES.md)
- [Notifications](06_NOTIFICATIONS.md)
- [Audit & Operations](07_AUDIT_OPERATIONS.md)

## Bảng tổng hợp 32 full-flow tasks

| ID | Flow | Owner/Flow Captain | Peer Tester | Release target | Target week | SP |
| --- | --- | --- | --- | --- | --- | --- |
| A01 | Đăng ký tài khoản & xác thực email OTP | Hùng | Khải | v0.1.0 | W11 | 5 |
| A02 | Đăng nhập bằng email / mật khẩu | Hùng | Khải | v0.1.0 | W11 | 3 |
| A03 | Đăng nhập Google OIDC / liên kết identity an toàn | Hùng | Khải | v0.1.0 | W12 | 5 |
| A04 | Quên mật khẩu → OTP → đặt mật khẩu mới | Hùng | Khải | v0.2.0 | W14 | 5 |
| A05 | Đổi mật khẩu khi đã đăng nhập | Hùng | Khải | v0.2.0 | W14 | 3 |
| A06 | Đăng xuất phiên hiện tại | Hùng | Khải | v0.1.0 | W11 | 2 |
| A07 | Đăng xuất tất cả thiết bị | Kiên | Hùng | v0.2.0 | W15 | 2 |
| A08 | Xem / cập nhật hồ sơ cá nhân | Khải | Thắng | v0.1.0 | W12 | 3 |
| S01 | Guest Scan URL → RiskResult | Khải | Thắng | v0.1.0 | W11 | 13 |
| S02 | Scan tin nhắn / bài đăng giao dịch | Thắng | Kiên | v0.1.0 | W12 | 13 |
| S03 | Kiểm tra reputation số điện thoại | Kiên | Hùng | v0.1.0 | W12 | 8 |
| S04 | Kiểm tra reputation tài khoản ngân hàng | Kiên | Hùng | v0.1.0 | W12 | 8 |
| S05 | Quét/upload QR/VietQR → nested scan | Thắng | Kiên | v0.1.0 | W12 | 13 |
| R01 | Theo dõi trạng thái scan đang xử lý | Kiên | Hùng | v0.1.0 | W11 | 5 |
| R02 | Xem kết quả scan chi tiết | Thắng | Kiên | v0.1.0 | W11 | 5 |
| R03 | Xem, lọc và phân trang lịch sử scan | Thắng | Kiên | v0.1.0 | W12 | 8 |
| R04 | Xuất kết quả scan PDF/HTML | Thắng | Kiên | v0.1.0 | W12 | 5 |
| R05 | Chia sẻ kết quả scan theo chính sách quyền | Khải | Thắng | v0.3.0 | W17 | 3 |
| C01 | Gửi báo cáo đối tượng nghi lừa đảo | Hùng | Khải | v0.1.0 | W11 | 8 |
| C02 | Xem trạng thái báo cáo đã gửi | Khải | Thắng | v0.2.0 | W14 | 3 |
| C03 | Moderator xác minh / từ chối báo cáo | Hùng | Khải | v0.1.0 | W12 | 13 |
| T01 | Admin xem danh sách nguồn Threat Intelligence | Khải | Thắng | v0.1.0 | W12 | 3 |
| T02 | Admin bật/tắt nguồn Threat Intelligence | Kiên | Hùng | v0.2.0 | W14 | 3 |
| T03 | Admin đồng bộ / import Threat Intelligence | Kiên | Hùng | v0.1.0 | W12 | 8 |
| T04 | Admin xem rules và lịch sử versions | Thắng | Kiên | v0.2.0 | W15 | 3 |
| T05 | Admin tạo rule version mới | Kiên | Hùng | v0.2.0 | W15 | 5 |
| T06 | Admin kích hoạt rule version → scan áp dụng rule mới | Kiên | Hùng | v0.2.0 | W16 | 8 |
| N01 | Event → gửi notification tối thiểu | Khải | Thắng | v0.1.0 | W12 | 5 |
| N02 | Người dùng xem notification center / read-unread | Thắng | Kiên | v0.2.0 | W15 | 3 |
| N03 | Người dùng quản lý preferences notification | Thắng | Kiên | v0.3.0 | W17 | 3 |
| O01 | Admin xem / lọc Audit Log | Khải | Thắng | v0.1.0 | W12 | 5 |
| O02 | Operator điều tra lỗi Scan/Worker/AI | Khải | Thắng | v0.2.0 | W15 | 8 |


## Ranh giới

- 32 **full user/admin flows** độc lập về nghiệm thu (không mặc định độc lập runtime).
- 4 **shared enablers** dùng chung, không nhân đôi dưới từng flow.
- Một task được **Done** khi owner có evidence + CI + real integration + peer UAT đạt; không dựa vào mock/demo đơn lẻ.
- Phân bổ 8 Flow Captain/người chỉ là cân số lượng; phải xem **Workload** trên tracker và điều chỉnh story points theo remaining effort.
- Release v0.1.0 **30/10/2026**, ngày RC **29/10**, feature freeze **26/10**; W13 không nhận feature mới.
- Web/Mobile parity là requirement đã duyệt. Tracker phải lưu trạng thái Web và Mobile riêng. Nếu thiếu Mobile, phải nêu release risk và xin quyết định chính thức, không tự bỏ scope.
