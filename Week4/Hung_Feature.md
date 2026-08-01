# Hùng - Danh sách tính năng (Feature List)

> Dựa trên [Template_Feature.md](./Template_Feature.md) · Tuần 4

---

## Thông tin người phụ trách

| Trường | Giá trị |
|--------|---------|
| **Người phụ trách** | Hùng |
| **Vertical Slice** | (1) Auth & User — RBAC, Profile, Audit log |
| **Mô tả mục tiêu** | Xây dựng hệ thống định danh người dùng, phân quyền vai trò (USER / ADMIN) và ghi vết thao tác nhạy cảm, làm nền tảng bảo mật cho toàn bộ Anti-Scam Platform. |
| **Demo flow độc lập** | Người dùng đăng ký tài khoản → đăng nhập nhận JWT → gọi API profile → token hết hạn → dùng refresh token lấy token mới → Admin truy cập route admin thành công, User bị chặn 403. |

---

## Nhóm 1 · Frontend (UI/UX)

### Web (Next.js)

- [x] **Trang Đăng ký (Register)**
  - Nền tảng: Web
  - Mô tả: Form nhập email, username, password và confirm password. Validate client-side (format email, độ dài mật khẩu). Hiển thị lỗi inline nếu backend trả validation error.
  - Phụ thuộc API: `POST /v1/auth/register`

- [x] **Trang Đăng nhập (Login)**
  - Nền tảng: Web
  - Mô tả: Form nhập username/email và password. Lưu access token + refresh token vào bộ nhớ an toàn sau khi đăng nhập thành công. Hiển thị thông báo lỗi nếu sai thông tin.
  - Phụ thuộc API: `POST /v1/auth/login`

- [x] **Trang Hồ sơ cá nhân (User Profile)**
  - Nền tảng: Web
  - Mô tả: Hiển thị thông tin tài khoản (username, email, ngày tham gia, role). Có nút đổi mật khẩu và xem lịch sử các lượt scan gần nhất. Chỉ hiển thị khi đã đăng nhập.
  - Phụ thuộc API: `GET /v1/users/me`

- [x] **Guard Route Admin (Route Protection)**
  - Nền tảng: Web
  - Mô tả: Middleware Next.js kiểm tra token và role trước khi cho vào các trang `/admin/*`. Nếu không có token → redirect về `/login`. Nếu có token nhưng không phải ADMIN → redirect về trang chủ với thông báo "Không có quyền truy cập".
  - Phụ thuộc API: Dùng role trong JWT payload (không cần gọi thêm API)

### Mobile (React Native)

- [ ] **Màn hình Đăng nhập (Mobile Login)**
  - Nền tảng: Mobile
  - Mô tả: Form đăng nhập đơn giản trên mobile. Lưu token vào Secure Storage (không dùng AsyncStorage thông thường). Cho phép biometric login ở phase sau.
  - Phụ thuộc API: `POST /v1/auth/login`

---

## Nhóm 2 · Backend API (Spring Boot)

- [x] **POST /v1/auth/register**
  - Mô tả: Tạo tài khoản mới. Validate email format, độ dài password, username không trùng. Hash mật khẩu bằng BCrypt trước khi lưu. Trả về 201 kèm thông tin user cơ bản (không trả password hash).
  - Xử lý đặc biệt: Rate limit 5 requests/phút/IP để chống bot tạo tài khoản hàng loạt.

- [x] **POST /v1/auth/login**
  - Mô tả: Xác thực thông tin đăng nhập. Sinh JWT Access Token (TTL 15 phút) và Refresh Token (TTL 7 ngày). Lưu Refresh Token vào DB. Trả về cả hai token cho client.
  - Xử lý đặc biệt: Rate limit 5 lần thử sai / IP / 15 phút. Sau đó block thêm 15 phút.

- [x] **POST /v1/auth/refresh**
  - Mô tả: Nhận Refresh Token còn hạn, xác minh tính hợp lệ, sinh Access Token mới. Xoay vòng (rotate) Refresh Token để tăng bảo mật (invalidate token cũ, cấp token mới).
  - Xử lý đặc biệt: Nếu Refresh Token đã bị dùng lần 2 (token reuse), thu hồi toàn bộ session của user đó.

- [x] **POST /v1/auth/logout**
  - Mô tả: Thu hồi Refresh Token của session hiện tại. Client xoá token khỏi bộ nhớ cục bộ.
  - Xử lý đặc biệt: Cần xác thực JWT hợp lệ trước khi xử lý.

- [x] **GET /v1/users/me**
  - Mô tả: Trả về thông tin profile người dùng hiện tại dựa trên JWT: username, email, role, created_at, số lượt scan. Không trả password hash hay refresh token.
  - Xử lý đặc biệt: Cần JWT hợp lệ (Bearer token trong header). Bảo vệ bằng Spring Security.

- [ ] **PATCH /v1/users/me/password**
  - Mô tả: Đổi mật khẩu — yêu cầu nhập mật khẩu cũ để xác nhận. Hash mật khẩu mới trước khi lưu. Sau khi đổi, thu hồi toàn bộ Refresh Token hiện có (force re-login).
  - Xử lý đặc biệt: Audit log ghi lại sự kiện đổi mật khẩu.

- [ ] **GET /v1/admin/users** _(Admin only)_
  - Mô tả: Liệt kê danh sách user với phân trang. Chỉ Admin mới gọi được. Hỗ trợ filter theo role, trạng thái.
  - Xử lý đặc biệt: RBAC — chặn USER role với 403 Forbidden.

---

## Nhóm 3 · Background Workers (RabbitMQ / Async)

- [ ] **Email Notification Worker** — lắng nghe queue: `notification.email`
  - Mô tả: Gửi email xác thực tài khoản sau khi đăng ký (Phase 2). Xử lý bất đồng bộ để không làm chậm request đăng ký. Dùng template email tiếng Việt.
  - Output: Cập nhật trạng thái `email_verified` trong bảng `users` khi user click link xác thực.
  - Retry / Dead-letter: Có — retry 3 lần, sau đó đẩy vào dead-letter queue để admin xử lý.

> _(Phần lớn logic Auth/User xử lý đồng bộ — Worker chỉ cần thiết khi Phase 2 bổ sung email verification)_

---

## Nhóm 4 · Dữ liệu & Caching (PostgreSQL · Redis · MinIO)

### PostgreSQL

- [x] **Bảng `users`**
  - Lưu: Định danh người dùng.
  - Các cột chính: `id` (UUID), `username`, `email`, `password_hash`, `role` (enum: USER/ADMIN), `status` (enum: ACTIVE/SUSPENDED), `created_at`, `updated_at`.
  - Ràng buộc: `username` và `email` phải UNIQUE.

- [x] **Bảng `refresh_tokens`**
  - Lưu: Các Refresh Token còn hiệu lực, gắn với từng user.
  - Các cột chính: `id`, `user_id` (FK → users), `token_hash` (lưu hash, không lưu plain text), `expires_at`, `revoked` (boolean), `created_at`.
  - Mục đích: Cho phép thu hồi (revoke) token cụ thể khi đăng xuất hoặc phát hiện bất thường.

- [x] **Bảng `audit_logs`**
  - Lưu: Vết thao tác nhạy cảm của user và admin.
  - Các cột chính: `id`, `user_id`, `action` (ví dụ: `AUTH_LOGIN`, `PASSWORD_CHANGED`, `ADMIN_REVIEW_REPORT`), `ip_address`, `user_agent`, `created_at`, `metadata` (JSONB — chi tiết thêm nếu cần).
  - Ghi log cho: đăng nhập, đăng xuất, đổi mật khẩu, admin duyệt report, admin thay đổi rule.

### Redis

- [x] **Rate limit login** — key: `rate:login:{ip}:{window}` — TTL: 15 phút
  - Mục đích: Đếm số lần đăng nhập sai theo IP. Khi vượt ngưỡng (5 lần sai), block thêm 15 phút.

- [x] **Rate limit register** — key: `rate:register:{ip}:{window}` — TTL: 1 phút
  - Mục đích: Giới hạn 5 lượt đăng ký / phút / IP, chống bot.

- [ ] **Cache user profile** — key: `cache:user:{userId}` — TTL: 5 phút
  - Mục đích: Cache thông tin profile thường xuyên được đọc, giảm query DB cho `GET /v1/users/me`.

### MinIO

> _(Không áp dụng ở Phase 1 MVP cho Slice Auth/User)_

---

## Nhóm 5 · Security, Privacy & Trust-by-design

- [x] **Hash mật khẩu bằng BCrypt (strength ≥ 10)**
  - Mô tả: Tuyệt đối không lưu plain-text password. Sử dụng BCrypt hoặc Argon2 với cost factor đủ cao. Không ghi password vào log dù là dạng encoded.

- [x] **JWT Access Token có TTL ngắn (15 phút)**
  - Mô tả: Access Token sống ngắn để hạn chế thiệt hại nếu bị lộ. Refresh Token sống dài hơn (7 ngày) nhưng được lưu hash trong DB và có cơ chế revoke.

- [x] **Không đưa thông tin nhạy cảm vào JWT payload**
  - Mô tả: JWT payload chỉ chứa `userId`, `role`, `iat`, `exp`. Không chứa email, password, số điện thoại hay bất kỳ thông tin cá nhân nhạy cảm nào.

- [x] **Rate limit chống brute-force trên auth endpoints**
  - Mô tả: `POST /v1/auth/login` và `POST /v1/auth/register` phải có rate limit nghiêm ngặt (5 req/phút/IP). Trả về 429 Too Many Requests với thông báo thân thiện.

- [x] **RBAC phân quyền rõ ràng (USER vs ADMIN)**
  - Mô tả: Mọi route admin (`/v1/admin/*`) phải được bảo vệ bằng annotation hoặc filter kiểm tra role `ADMIN`. USER bình thường nhận 403 Forbidden với message rõ ràng, không leak thông tin hệ thống.

- [x] **Audit log bất biến (Append-only)**
  - Mô tả: Bảng `audit_logs` chỉ được INSERT, không UPDATE hoặc DELETE. Admin không thể xoá log. Phục vụ mục đích truy vết khi cần điều tra.

- [ ] **Refresh Token Rotation (Thu hồi khi phát hiện reuse)**
  - Mô tả: Mỗi lần refresh, token cũ bị invalidate và token mới được cấp. Nếu phát hiện token cũ được dùng lần 2 (có thể bị đánh cắp), thu hồi toàn bộ session của user đó và yêu cầu đăng nhập lại.

---

## Ghi chú & Dependencies

| Phụ thuộc vào | Của thành viên | Lý do cần |
|---------------|----------------|-----------|
| Rule Engine (đọc rule khi scan) | Thành viên 3 | Scan Orchestrator cần auth context (userId) để lưu lịch sử scan — Hùng expose `userId` qua JWT, thành viên 3 đọc từ Security Context |
| Community Report | Thành viên 4 | Report cần `userId` của người gửi và role `ADMIN` để duyệt — đã được Hùng xây dựng |

---

*Cập nhật lần cuối: 2026-08-01 · Vertical Slice 1 · Phiên bản: Week 4*
