# W10 — Hùng: Registration & Login Foundation

**Owner:** Hùng  
**Reviewer:** Khải  
**Target:** G2  
**Baseline:** Reuse W09 Identity Foundation.

## A01 — Đăng ký tài khoản và xác thực OTP

### User Flow
Mở Register → nhập thông tin → gửi đăng ký → nhận email OTP → nhập OTP → tài khoản được xác thực.

### Functional Requirements
- FR01: Web cung cấp form đăng ký theo Identity API.
- FR02: Validate dữ liệu và hiển thị lỗi theo contract.
- FR03: Backend tạo tài khoản/challenge theo chính sách đã duyệt.
- FR04: Gửi OTP thông qua notification/email sandbox.
- FR05: Verify OTP hợp lệ, cập nhật trạng thái tài khoản.

### Non-functional Requirements
- Không log OTP, password hoặc token.
- OTP có thời hạn, giới hạn số lần thử và chống replay.
- Không cho dữ liệu lỗi tạo duplicate account.
- Không làm lộ thông tin nhạy cảm qua error messages.

### W10 Deliverables
- Registration API và OTP contracts hoạt động.
- Registration/verify UI dùng backend thật hoặc API mock được ghi rõ.
- Test fixtures cho OTP hợp lệ, sai, hết hạn.
- CI PASS và PR được reviewer kiểm tra.

### Tests
- Đăng ký hợp lệ.
- Email/input không hợp lệ.
- OTP sai và hết hạn.
- Thử verify lặp lại cùng OTP.

**Target gate:** G2. G3/G4 chỉ khi Web–API–Email–DB đã chạy thật.

---

## A02 — Đăng nhập bằng Email/Password

### User Flow
Mở Login → nhập email/password → backend xác thực → nhận session → vào ứng dụng.

### Functional Requirements
- FR01: Form đăng nhập và input validation.
- FR02: Authenticate theo Identity service.
- FR03: Tạo và lưu session/token đúng chính sách.
- FR04: Hiển thị lỗi credentials/account state.
- FR05: Client duy trì authentication state sau navigation.

### Non-functional Requirements
- Không log credentials/token.
- Rate limiting hoặc authentication throttling theo policy.
- Không tiết lộ chi tiết nhạy cảm của tài khoản.
- Không bỏ qua authorization khi frontend chưa có route guard.

### W10 Deliverables
- Login UI + Auth API contract integration.
- Success/error session fixtures.
- Tests cho login đúng/sai và session behavior.
- CI PASS, reviewer approval.

### Tests
- Đăng nhập đúng.
- Sai password.
- Account chưa thỏa điều kiện đăng nhập.
- Refresh/navigation theo chính sách session.

**Target gate:** G2.

## Handoffs to W11
- Staging identity environment.
- Test email inbox/OTP sandbox.
- Web routes và API contracts ổn định.
- Cung cấp account fixtures cho cross-tester Khải.

## Definition of Done
- [ ] Cả A01 và A02 có PR/evidence.
- [ ] CI và các tests liên quan PASS.
- [ ] Security-sensitive changes được review.
- [ ] G2 được xác nhận, G3/G4 chỉ ghi khi test thật.
- [ ] Blockers được cập nhật.