# Authentication & Account — Full-Flow Task Specifications (V3.0 Draft)

> Đây là mô tả các **task lớn có thể test thủ công độc lập** thuộc nhóm Authentication & Account. Không chia từng class/repository thành ticket riêng.

## Danh mục

| Task | Goal | Captain | Tester | Release | Week |
| --- | --- | --- | --- | --- | --- |
| A01 | Đăng ký tài khoản & xác thực email OTP | Hùng | Khải | v0.1.0 | W11 |
| A02 | Đăng nhập bằng email / mật khẩu | Hùng | Khải | v0.1.0 | W11 |
| A03 | Đăng nhập Google OIDC / liên kết identity an toàn | Hùng | Khải | v0.1.0 | W12 |
| A04 | Quên mật khẩu → OTP → đặt mật khẩu mới | Hùng | Khải | v0.2.0 | W14 |
| A05 | Đổi mật khẩu khi đã đăng nhập | Hùng | Khải | v0.2.0 | W14 |
| A06 | Đăng xuất phiên hiện tại | Hùng | Khải | v0.1.0 | W11 |
| A07 | Đăng xuất tất cả thiết bị | Kiên | Hùng | v0.2.0 | W15 |
| A08 | Xem / cập nhật hồ sơ cá nhân | Khải | Thắng | v0.1.0 | W12 |


---

## A01 — Đăng ký tài khoản & xác thực email OTP


**Actor:** Guest · **Flow Captain:** Hùng · **Cross-tester:** Khải · **Modules:** M01,M10 · **Release:** v0.1.0 · **Target:** W11 · **Estimate:** 5 SP (chờ đội xác nhận).

**User journey:** Mở trang đăng ký → nhập email/mật khẩu → nhận OTP tại test inbox → xác thực → đăng nhập bằng tài khoản vừa tạo.

**Dependencies (để tích hợp):** E-IDENTITY.

**Contract/Fixture để triển khai độc lập:** Email sandbox + user test fixture.

### Functional Requirements / Acceptance Criteria

| ID | Functional Requirement | Có thể xác nhận bằng |
| --- | --- | --- |
| A01-FR01 | Tạo tài khoản | Gửi đăng ký hợp lệ tạo user chưa xác minh; tuân theo uniqueness/email canonicalization. |
| A01-FR02 | Xác minh email | OTP đúng và còn hạn kích hoạt tài khoản; OTP sai/hết hạn không kích hoạt. |
| A01-FR03 | Truy cập UI | Có form đăng ký, nhập OTP, thông báo lỗi và hướng dẫn đăng nhập. |


### Non-functional Requirements / Quality evidence

| ID | Quality | Verification |
| --- | --- | --- |
| A01-NFR01 | Chống enumeration | Phản hồi request gửi lại OTP không tiết lộ email có tồn tại hay không. |
| A01-NFR02 | Bảo mật OTP | OTP được bảo vệ, hết hạn, giới hạn thử; không log plaintext OTP/token. |


### Manual UAT — thành viên khác tự thực hiện

| Test ID | Scenario | Steps | Expected result |
| --- | --- | --- | --- |
| A01-TC01 | Happy | Đăng ký email mới, lấy OTP sandbox và xác minh | Có account verified và đăng nhập được. |
| A01-TC02 | Negative | Nhập OTP sai, quá hạn hoặc email trùng | Không tạo quyền truy cập ngoài policy, có thông báo phù hợp. |


### Implementation checklist (không tạo thêm task quản lý)

- Auth API có thể tái sử dụng từ W09; bổ sung UI và OTP delivery sandbox; E2E + negative tests.
- [ ] Contract fixtures/DTO/events tương thích specification; PR link đủ.
- [ ] Unit/contract tests + negative/security cases trong CI.
- [ ] Real integration trên staging; mock chỉ dùng trong lúc phát triển.
- [ ] Peer tester chạy UAT, ghi actual/evidence/defect.
- [ ] Không còn blocker hoặc NFR security/reliability critical chưa xử lý.

**Ghi chú:** W09 đã có RegistrationService/AuthController: không viết lại. Xác minh lại UI và email delivery.

---

## A02 — Đăng nhập bằng email / mật khẩu


**Actor:** Guest · **Flow Captain:** Hùng · **Cross-tester:** Khải · **Modules:** M01 · **Release:** v0.1.0 · **Target:** W11 · **Estimate:** 3 SP (chờ đội xác nhận).

**User journey:** Mở Login → nhập email/password → xác thực → tạo session → vào khu vực user.

**Dependencies (để tích hợp):** A01 (dùng seeded verified user cũng được).

**Contract/Fixture để triển khai độc lập:** Verified user seeded + wrong-password fixture.

### Functional Requirements / Acceptance Criteria

| ID | Functional Requirement | Có thể xác nhận bằng |
| --- | --- | --- |
| A02-FR01 | Local login | Verified user đăng nhập thành công và nhận session/token theo contract. |
| A02-FR02 | Sai credentials | Không tạo session; thông báo lỗi bảo mật phù hợp. |
| A02-FR03 | Session client | Refresh trang vẫn giữ session đúng policy. |


### Non-functional Requirements / Quality evidence

| ID | Quality | Verification |
| --- | --- | --- |
| A02-NFR01 | Brute-force defense | Giới hạn yêu cầu login theo policy; test rate-limit fixture. |
| A02-NFR02 | Token safety | Token lưu bằng cơ chế an toàn đã duyệt; không log password/token. |


### Manual UAT — thành viên khác tự thực hiện

| Test ID | Scenario | Steps | Expected result |
| --- | --- | --- | --- |
| A02-TC01 | Happy | Đăng nhập seeded verified account | Vào khu vực user và gọi API yêu cầu auth được. |
| A02-TC02 | Negative | Sai password nhiều lần | Không có session; rate-limit/lockout theo policy. |


### Implementation checklist (không tạo thêm task quản lý)

- AuthService W09, login UI, session client, test 401/403.
- [ ] Contract fixtures/DTO/events tương thích specification; PR link đủ.
- [ ] Unit/contract tests + negative/security cases trong CI.
- [ ] Real integration trên staging; mock chỉ dùng trong lúc phát triển.
- [ ] Peer tester chạy UAT, ghi actual/evidence/defect.
- [ ] Không còn blocker hoặc NFR security/reliability critical chưa xử lý.

---

## A03 — Đăng nhập Google OIDC / liên kết identity an toàn


**Actor:** Guest/User · **Flow Captain:** Hùng · **Cross-tester:** Khải · **Modules:** M01 · **Release:** v0.1.0 · **Target:** W12 · **Estimate:** 5 SP (chờ đội xác nhận).

**User journey:** Bấm Google Login → OIDC callback hợp lệ → chuẩn hóa identity → tạo/login account → về Web.

**Dependencies (để tích hợp):** A02; Google OIDC sandbox.

**Contract/Fixture để triển khai độc lập:** OIDC test tenant/sandbox identities.

### Functional Requirements / Acceptance Criteria

| ID | Functional Requirement | Có thể xác nhận bằng |
| --- | --- | --- |
| A03-FR01 | OIDC login | Exchange callback đúng state/nonce và chuẩn hóa session. |
| A03-FR02 | Liên kết identity | Không liên kết account chỉ dựa trên email chưa xác minh; chống duplicate takeover. |


### Non-functional Requirements / Quality evidence

| ID | Quality | Verification |
| --- | --- | --- |
| A03-NFR01 | OAuth security | State/nonce/redirect allowlist được test. |
| A03-NFR02 | Privacy | Không lưu access token provider không cần thiết; không đưa token ra URL/log. |


### Manual UAT — thành viên khác tự thực hiện

| Test ID | Scenario | Steps | Expected result |
| --- | --- | --- | --- |
| A03-TC01 | Happy | Đăng nhập bằng test Google OIDC identity | Session hợp lệ và profile tương ứng. |
| A03-TC02 | Negative | Dùng forged callback/email collision | Không takeover hoặc link sai user. |


### Implementation checklist (không tạo thêm task quản lý)

- OIDC adapter, callback UI, tests state/nonce/linking.
- [ ] Contract fixtures/DTO/events tương thích specification; PR link đủ.
- [ ] Unit/contract tests + negative/security cases trong CI.
- [ ] Real integration trên staging; mock chỉ dùng trong lúc phát triển.
- [ ] Peer tester chạy UAT, ghi actual/evidence/defect.
- [ ] Không còn blocker hoặc NFR security/reliability critical chưa xử lý.

**Ghi chú:** P0 theo mốc V2.1; nếu sandbox credentials chưa có phải đưa vào blocker ngay.

---

## A04 — Quên mật khẩu → OTP → đặt mật khẩu mới


**Actor:** Guest · **Flow Captain:** Hùng · **Cross-tester:** Khải · **Modules:** M01,M10 · **Release:** v0.2.0 · **Target:** W14 · **Estimate:** 5 SP (chờ đội xác nhận).

**User journey:** Chọn quên mật khẩu → nhập email → nhận OTP → đặt mật khẩu mới → login lại.

**Dependencies (để tích hợp):** A01 OTP service.

**Contract/Fixture để triển khai độc lập:** Test inbox + verified user.

### Functional Requirements / Acceptance Criteria

| ID | Functional Requirement | Có thể xác nhận bằng |
| --- | --- | --- |
| A04-FR01 | Khởi tạo reset | Response trung tính bất kể email tồn tại. |
| A04-FR02 | Xác minh OTP | OTP đúng cho phép đặt password mới; cũ hết hiệu lực. |
| A04-FR03 | UI recovery | Flow không đòi mật khẩu cũ. |


### Non-functional Requirements / Quality evidence

| ID | Quality | Verification |
| --- | --- | --- |
| A04-NFR01 | Credential safety | Reset token/OTP one-time, expiry, rate-limit; hash password. |
| A04-NFR02 | Session revocation | Tuân thủ policy revoke session sau reset. |


### Manual UAT — thành viên khác tự thực hiện

| Test ID | Scenario | Steps | Expected result |
| --- | --- | --- | --- |
| A04-TC01 | Happy | Reset qua test inbox, login password mới | Login mới thành công, mật khẩu cũ thất bại. |
| A04-TC02 | Negative | Reset email lạ/OTP sai | Không rò trạng thái account. |


### Implementation checklist (không tạo thêm task quản lý)

- DTO/endpoint OTP RESET, reset UI, tests security.
- [ ] Contract fixtures/DTO/events tương thích specification; PR link đủ.
- [ ] Unit/contract tests + negative/security cases trong CI.
- [ ] Real integration trên staging; mock chỉ dùng trong lúc phát triển.
- [ ] Peer tester chạy UAT, ghi actual/evidence/defect.
- [ ] Không còn blocker hoặc NFR security/reliability critical chưa xử lý.

---

## A05 — Đổi mật khẩu khi đã đăng nhập


**Actor:** User · **Flow Captain:** Hùng · **Cross-tester:** Khải · **Modules:** M01,M10 · **Release:** v0.2.0 · **Target:** W14 · **Estimate:** 3 SP (chờ đội xác nhận).

**User journey:** Vào settings → xác thực mật khẩu hiện tại/OTP → nhập mật khẩu mới → lưu → sử dụng credential mới.

**Dependencies (để tích hợp):** A02,A01 OTP.

**Contract/Fixture để triển khai độc lập:** Current password + change OTP fixture.

### Functional Requirements / Acceptance Criteria

| ID | Functional Requirement | Có thể xác nhận bằng |
| --- | --- | --- |
| A05-FR01 | Đổi mật khẩu | Chỉ current user đổi, yêu cầu proof đúng chính sách. |
| A05-FR02 | Cập nhật credential | Mật khẩu mới login được, cũ không còn được chấp nhận. |


### Non-functional Requirements / Quality evidence

| ID | Quality | Verification |
| --- | --- | --- |
| A05-NFR01 | Session hygiene | Revoke/rotate session đúng policy; audit không lưu secrets. |
| A05-NFR02 | Access | Account khác không thể đổi mật khẩu mục tiêu. |


### Manual UAT — thành viên khác tự thực hiện

| Test ID | Scenario | Steps | Expected result |
| --- | --- | --- | --- |
| A05-TC01 | Happy | Đổi mật khẩu cho user test | Credential đổi và thông báo thành công. |
| A05-TC02 | Negative | Sai proof/đổi password user khác | Không cập nhật credential. |


### Implementation checklist (không tạo thêm task quản lý)

- Settings UI + backend password change + tests.
- [ ] Contract fixtures/DTO/events tương thích specification; PR link đủ.
- [ ] Unit/contract tests + negative/security cases trong CI.
- [ ] Real integration trên staging; mock chỉ dùng trong lúc phát triển.
- [ ] Peer tester chạy UAT, ghi actual/evidence/defect.
- [ ] Không còn blocker hoặc NFR security/reliability critical chưa xử lý.

---

## A06 — Đăng xuất phiên hiện tại


**Actor:** User · **Flow Captain:** Hùng · **Cross-tester:** Khải · **Modules:** M01 · **Release:** v0.1.0 · **Target:** W11 · **Estimate:** 2 SP (chờ đội xác nhận).

**User journey:** User chọn Logout → session hiện tại bị revoke → chuyển về Guest → protected route yêu cầu login.

**Dependencies (để tích hợp):** A02.

**Contract/Fixture để triển khai độc lập:** Active session fixture.

### Functional Requirements / Acceptance Criteria

| ID | Functional Requirement | Có thể xác nhận bằng |
| --- | --- | --- |
| A06-FR01 | Logout | Session refresh không được sử dụng tiếp sau logout. |
| A06-FR02 | UI state | Clear client session, cập nhật menu/route. |


### Non-functional Requirements / Quality evidence

| ID | Quality | Verification |
| --- | --- | --- |
| A06-NFR01 | Session security | Không để refresh token reuse tạo lại session. |
| A06-NFR02 | Idempotence | Logout lặp lại không gây lỗi nghiêm trọng. |


### Manual UAT — thành viên khác tự thực hiện

| Test ID | Scenario | Steps | Expected result |
| --- | --- | --- | --- |
| A06-TC01 | Happy | Login rồi Logout, thử lại protected API | 401 và UI về Guest. |
| A06-TC02 | Negative | Sử dụng refresh token vừa logout | Không cấp access token mới. |


### Implementation checklist (không tạo thêm task quản lý)

- Reuse logout service từ W09 nếu có, UI, session invalidation tests.
- [ ] Contract fixtures/DTO/events tương thích specification; PR link đủ.
- [ ] Unit/contract tests + negative/security cases trong CI.
- [ ] Real integration trên staging; mock chỉ dùng trong lúc phát triển.
- [ ] Peer tester chạy UAT, ghi actual/evidence/defect.
- [ ] Không còn blocker hoặc NFR security/reliability critical chưa xử lý.

---

## A07 — Đăng xuất tất cả thiết bị


**Actor:** User · **Flow Captain:** Kiên · **Cross-tester:** Hùng · **Modules:** M01 · **Release:** v0.2.0 · **Target:** W15 · **Estimate:** 2 SP (chờ đội xác nhận).

**User journey:** User trong Security chọn Logout all devices → tất cả refresh/session bị revoke → các thiết bị phải login lại.

**Dependencies (để tích hợp):** A02; phối hợp Hùng M01.

**Contract/Fixture để triển khai độc lập:** 2 sessions seeded; AuthSession fixture.

### Functional Requirements / Acceptance Criteria

| ID | Functional Requirement | Có thể xác nhận bằng |
| --- | --- | --- |
| A07-FR01 | Revoke all | Thu hồi family/session các thiết bị của chính user. |
| A07-FR02 | UX | Hiển thị cảnh báo và xác nhận. |


### Non-functional Requirements / Quality evidence

| ID | Quality | Verification |
| --- | --- | --- |
| A07-NFR01 | Authorization | Không revoke session của người khác. |
| A07-NFR02 | Race safety | Concurrent refresh không hồi sinh session đã revoke. |


### Manual UAT — thành viên khác tự thực hiện

| Test ID | Scenario | Steps | Expected result |
| --- | --- | --- | --- |
| A07-TC01 | Happy | Login 2 thiết bị test, revoke all | Cả 2 không refresh lại được. |
| A07-TC02 | Negative | Revoke all của User A, xem User B | User B không ảnh hưởng. |


### Implementation checklist (không tạo thêm task quản lý)

- Flow Captain Kiên; Hùng giữ ownership backend M01; Kiên tích hợp client/QA với contract.
- [ ] Contract fixtures/DTO/events tương thích specification; PR link đủ.
- [ ] Unit/contract tests + negative/security cases trong CI.
- [ ] Real integration trên staging; mock chỉ dùng trong lúc phát triển.
- [ ] Peer tester chạy UAT, ghi actual/evidence/defect.
- [ ] Không còn blocker hoặc NFR security/reliability critical chưa xử lý.

**Ghi chú:** Assignment theo Flow Captain, không chuyển ownership Auth backend khỏi Hùng.

---

## A08 — Xem / cập nhật hồ sơ cá nhân


**Actor:** User · **Flow Captain:** Khải · **Cross-tester:** Thắng · **Modules:** M01 · **Release:** v0.1.0 · **Target:** W12 · **Estimate:** 3 SP (chờ đội xác nhận).

**User journey:** Đăng nhập → mở Profile → xem dữ liệu → chỉnh displayName/fields được phép → reload vẫn thấy cập nhật.

**Dependencies (để tích hợp):** A02.

**Contract/Fixture để triển khai độc lập:** Seeded verified user + another user.

### Functional Requirements / Acceptance Criteria

| ID | Functional Requirement | Có thể xác nhận bằng |
| --- | --- | --- |
| A08-FR01 | Xem hồ sơ | GET current profile trả đúng current user. |
| A08-FR02 | Cập nhật | Chỉ field được phép, persist và hiển thị lại. |


### Non-functional Requirements / Quality evidence

| ID | Quality | Verification |
| --- | --- | --- |
| A08-NFR01 | Access control | Chặn chỉnh sửa profile user khác. |
| A08-NFR02 | PII | Không render/log dữ liệu nhạy cảm không cần. |


### Manual UAT — thành viên khác tự thực hiện

| Test ID | Scenario | Steps | Expected result |
| --- | --- | --- | --- |
| A08-TC01 | Happy | Đổi displayName, reload | Dữ liệu được lưu. |
| A08-TC02 | Negative | PATCH profile user khác/field cấm | 403/validation theo policy. |


### Implementation checklist (không tạo thêm task quản lý)

- Khải phụ trách Profile UI/integration; Hùng giữ API ownership.
- [ ] Contract fixtures/DTO/events tương thích specification; PR link đủ.
- [ ] Unit/contract tests + negative/security cases trong CI.
- [ ] Real integration trên staging; mock chỉ dùng trong lúc phát triển.
- [ ] Peer tester chạy UAT, ghi actual/evidence/defect.
- [ ] Không còn blocker hoặc NFR security/reliability critical chưa xử lý.

**Ghi chú:** Không nhân đôi việc đã có trong UserController W09.
