# Notifications — Full-Flow Task Specifications (V3.0 Draft)

> Đây là mô tả các **task lớn có thể test thủ công độc lập** thuộc nhóm Notifications. Không chia từng class/repository thành ticket riêng.

## Danh mục

| Task | Goal | Captain | Tester | Release | Week |
| --- | --- | --- | --- | --- | --- |
| N01 | Event → gửi notification tối thiểu | Khải | Thắng | v0.1.0 | W12 |
| N02 | Người dùng xem notification center / read-unread | Thắng | Kiên | v0.2.0 | W15 |
| N03 | Người dùng quản lý preferences notification | Thắng | Kiên | v0.3.0 | W17 |


---

## N01 — Event → gửi notification tối thiểu


**Actor:** User · **Flow Captain:** Khải · **Cross-tester:** Thắng · **Modules:** M10,M01,M06,M07 · **Release:** v0.1.0 · **Target:** W12 · **Estimate:** 5 SP (chờ đội xác nhận).

**User journey:** Một business event (OTP/scan/report) phát ra → delivery adapter xử lý → test user thấy thông báo qua kênh MVP.

**Dependencies (để tích hợp):** H-M10 backend contract, C01,A01.

**Contract/Fixture để triển khai độc lập:** Email sandbox + notification event fixtures.

### Functional Requirements / Acceptance Criteria

| ID | Functional Requirement | Có thể xác nhận bằng |
| --- | --- | --- |
| N01-FR01 | Event handling | Event route tới template/channel phù hợp. |
| N01-FR02 | Delivery | Ít nhất một kênh thực/sandbox có thể chứng minh nhận thông báo. |
| N01-FR03 | Failure | Không làm thay đổi business truth nếu delivery failed. |


### Non-functional Requirements / Quality evidence

| ID | Quality | Verification |
| --- | --- | --- |
| N01-NFR01 | PII | Không log OTP/secret trong notification logs. |
| N01-NFR02 | Reliability | Delivery retry/idempotency theo policy. |


### Manual UAT — thành viên khác tự thực hiện

| Test ID | Scenario | Steps | Expected result |
| --- | --- | --- | --- |
| N01-TC01 | Happy | Tạo OTP/scan event qua fixture | Test mailbox/in-app nhận event phù hợp. |
| N01-TC02 | Failure | Ngắt provider fixture | Failure/retry state và không corrupt core. |


### Implementation checklist (không tạo thêm task quản lý)

- Khải dẫn UI/integration, Hùng phụ trách notification backend M10.
- [ ] Contract fixtures/DTO/events tương thích specification; PR link đủ.
- [ ] Unit/contract tests + negative/security cases trong CI.
- [ ] Real integration trên staging; mock chỉ dùng trong lúc phát triển.
- [ ] Peer tester chạy UAT, ghi actual/evidence/defect.
- [ ] Không còn blocker hoặc NFR security/reliability critical chưa xử lý.

---

## N02 — Người dùng xem notification center / read-unread


**Actor:** User · **Flow Captain:** Thắng · **Cross-tester:** Kiên · **Modules:** M10 · **Release:** v0.2.0 · **Target:** W15 · **Estimate:** 3 SP (chờ đội xác nhận).

**User journey:** User đăng nhập → mở Notifications → đọc tin → mark as read → lần sau trạng thái được giữ.

**Dependencies (để tích hợp):** N01.

**Contract/Fixture để triển khai độc lập:** Two-user notification fixtures.

### Functional Requirements / Acceptance Criteria

| ID | Functional Requirement | Có thể xác nhận bằng |
| --- | --- | --- |
| N02-FR01 | List | List notifications cho current owner. |
| N02-FR02 | Mark read | Read state được persist và hiển thị. |


### Non-functional Requirements / Quality evidence

| ID | Quality | Verification |
| --- | --- | --- |
| N02-NFR01 | IDOR | Không đọc/sửa notification của người khác. |
| N02-NFR02 | Performance | Pagination/bounds cho danh sách dài. |


### Manual UAT — thành viên khác tự thực hiện

| Test ID | Scenario | Steps | Expected result |
| --- | --- | --- | --- |
| N02-TC01 | Happy | Seed 2 unread, mark read | State thay đổi sau reload. |
| N02-TC02 | Negative | User B mở notification A | Denied. |


### Implementation checklist (không tạo thêm task quản lý)

- Notification center UI/API integration; Hùng giữ core ownership.
- [ ] Contract fixtures/DTO/events tương thích specification; PR link đủ.
- [ ] Unit/contract tests + negative/security cases trong CI.
- [ ] Real integration trên staging; mock chỉ dùng trong lúc phát triển.
- [ ] Peer tester chạy UAT, ghi actual/evidence/defect.
- [ ] Không còn blocker hoặc NFR security/reliability critical chưa xử lý.

---

## N03 — Người dùng quản lý preferences notification


**Actor:** User · **Flow Captain:** Thắng · **Cross-tester:** Kiên · **Modules:** M10 · **Release:** v0.3.0 · **Target:** W17 · **Estimate:** 3 SP (chờ đội xác nhận).

**User journey:** User mở Notification Settings → bật/tắt kênh sự kiện được phép → sau trigger áp dụng preference.

**Dependencies (để tích hợp):** N02.

**Contract/Fixture để triển khai độc lập:** Two preferences profiles.

### Functional Requirements / Acceptance Criteria

| ID | Functional Requirement | Có thể xác nhận bằng |
| --- | --- | --- |
| N03-FR01 | Preferences | Chỉ cho phép channel/event options hợp lệ. |
| N03-FR02 | Effect | Notification event sau đó dùng preferences mới. |


### Non-functional Requirements / Quality evidence

| ID | Quality | Verification |
| --- | --- | --- |
| N03-NFR01 | Security | Người khác không sửa settings của user. |
| N03-NFR02 | Consent | Tôn trọng unsubscribe và bắt buộc OTP security channel nếu policy. |


### Manual UAT — thành viên khác tự thực hiện

| Test ID | Scenario | Steps | Expected result |
| --- | --- | --- | --- |
| N03-TC01 | Happy | Tắt channel optional, trigger event | Không deliver kênh đã tắt. |
| N03-TC02 | Negative | Tắt channel bắt buộc/modify user B | Rejected. |


### Implementation checklist (không tạo thêm task quản lý)

- Settings UI + preference API + delivery integration.
- [ ] Contract fixtures/DTO/events tương thích specification; PR link đủ.
- [ ] Unit/contract tests + negative/security cases trong CI.
- [ ] Real integration trên staging; mock chỉ dùng trong lúc phát triển.
- [ ] Peer tester chạy UAT, ghi actual/evidence/defect.
- [ ] Không còn blocker hoặc NFR security/reliability critical chưa xử lý.
