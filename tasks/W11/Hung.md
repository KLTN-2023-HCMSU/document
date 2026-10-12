# W11 — Hùng: Registration, Login & Session Flows

**Owner:** Hùng  
**Cross-tester:** Khải  
**Primary goal:** A01 + A02 đạt G4  
**Priority:** P0

## A01 — Đăng ký tài khoản và xác thực email

### User Journey
Mở trang đăng ký → nhập thông tin → submit → nhận OTP trên test mailbox → verify → tài khoản được xác thực.

### FR
- FR01: Registration Web form kết nối backend thật.
- FR02: OTP challenge được tạo/gửi theo policy.
- FR03: Verify OTP hợp lệ cập nhật account.
- FR04: Có thể tiếp tục đến login sau khi verified.
- FR05: Hiển thị lỗi rõ ràng cho invalid/expired OTP.

### NFR
- Rate-limit OTP, không leak OTP hoặc secrets.
- Không tạo duplicate account/challenge ngoài policy.
- Thao tác verify phải an toàn khi replay/concurrent requests.

### Integration & Evidence
- Web + Backend + PostgreSQL + email sandbox.
- Test account và test mailbox.
- PR, CI, logs và browser demo.

### Manual UAT
- [ ] Đăng ký thành công và verify.
- [ ] Email/input sai được xử lý.
- [ ] OTP sai/hết hạn bị từ chối.
- [ ] OTP reuse không tạo hiệu ứng sai.
- [ ] Dữ liệu account vẫn đúng sau reload.

**Target: G4.**

---

## A02 — Đăng nhập Email/Password

### User Journey
Nhập credentials → xác thực → tạo session → vào protected page.

### FR
- FR01: Login form dùng API thật.
- FR02: Session/token được tạo theo approved policy.
- FR03: Client giữ authentication state.
- FR04: Backend từ chối credentials không hợp lệ.
- FR05: Route/API authorization hoạt động.

### NFR
- Password/token không xuất hiện trong logs.
- Throttle login attempts.
- Không tiết lộ thông tin nhạy cảm về account.
- Refresh-token/session policy phải được test.

### Manual UAT
- [ ] Login đúng.
- [ ] Login sai password.
- [ ] User chưa verify không bypass policy.
- [ ] Reload và refresh session hợp lệ.
- [ ] User thường không truy cập admin API.

**Target: G4.**

---

## A06 — Đăng xuất phiên hiện tại

### User Journey
User đang đăng nhập → chọn Logout → session bị thu hồi/invalid theo policy → trở lại guest.

### FR/NFR
- FR01: Logout action và API integration.
- FR02: Xóa client authentication state.
- FR03: Token/session bị thu hồi theo contract.
- NFR01: Không thể sử dụng lại phiên đã bị invalidate.
- NFR02: Logout lặp lại không gây lỗi nguy hiểm.

### Manual UAT
- [ ] Logout thành công.
- [ ] Protected page/API bị từ chối sau logout.
- [ ] Replay token đã revoke bị xử lý đúng policy.

**Target: G3, nâng G4 khi peer test.**

---

## A04 — Quên/Reset mật khẩu (Stretch)

### User Journey
Forgot Password → nhận challenge/OTP → verify → đặt mật khẩu mới → login lại.

### Scope
- Recovery API + Web form.
- OTP/token expiry.
- Không tiết lộ email có tồn tại.
- Session invalidation theo policy.

### Tests
- Email tồn tại/không tồn tại.
- OTP sai/hết hạn.
- Password mới hợp lệ.
- Password cũ không còn hiệu lực theo policy.

**Target: G2 nếu còn capacity. Không được ảnh hưởng A01/A02.**

## Handoffs
- Cho Khải: staging URL, test mailbox, account fixtures.
- Cho Kiên: AccessContext/EntitlementContext và account session behavior.
- Cho Thắng: authenticated/guest UI test accounts.

## Definition of Done
- [ ] A01 G4.
- [ ] A02 G4.
- [ ] A06 đạt gate có evidence.
- [ ] Auth security checks PASS.
- [ ] Khải kiểm thử độc lập và approve.
- [ ] Stretch task chưa xong được giữ riêng, không làm giảm trạng thái A01/A02.