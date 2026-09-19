# [Tên thành viên] - Danh sách tính năng (Feature List)

> **Mẫu dùng chung cho nhóm 4 người — Tuần 4**
> Bám sát kiến trúc **Modular Monolith + Event-Driven Workers** đã thống nhất ở Week 3.
> Mỗi thành viên copy file này, đổi tên thành `[Ten]_Feature.md` và điền theo vertical slice của mình.

---

## Thông tin người phụ trách

| Trường | Giá trị |
|--------|---------|
| **Người phụ trách** | [Tên của bạn] |
| **Vertical Slice** | [Chọn: (1) Auth & User · (2) Scan URL & SSRF · (3) Text, Phone, QR & Rule Engine · (4) Community Report & Admin Dashboard] |
| **Mô tả mục tiêu** | [Một câu mô tả mục tiêu chính của slice này là gì] |
| **Demo flow độc lập** | [Mô tả ngắn: người dùng làm gì → hệ thống phản hồi gì — đây là demo end-to-end bạn sẽ trình bày] |

---

## Nhóm 1 · Frontend (UI/UX)

> **Phạm vi:** Màn hình, form, component tương tác trên **Web (Next.js)** và/hoặc **Mobile (React Native)**.
> Mỗi item nên gắn với một luồng người dùng rõ ràng.

- [ ] **[Tên màn hình / tính năng UI]**
  - Nền tảng: Web / Mobile / Cả hai
  - Mô tả: [Người dùng làm gì trên màn hình này?]
  - Phụ thuộc API: [API endpoint mà màn hình này gọi]

- [ ] **[Tên màn hình / tính năng UI thứ 2]**
  - Nền tảng: ...
  - Mô tả: ...
  - Phụ thuộc API: ...

---

## Nhóm 2 · Backend API (Spring Boot)

> **Phạm vi:** Các REST endpoint xử lý **đồng bộ** — validate input, kiểm tra cache, tạo job, trả kết quả.
> Ghi rõ HTTP method, path và mô tả logic chính.

- [ ] **[HTTP_METHOD] [/v1/path]**
  - Mô tả: [Endpoint này làm gì? Input là gì? Output là gì?]
  - Xử lý đặc biệt: [Rate limit / Idempotency / RBAC / Cache check — nếu có]

- [ ] **[HTTP_METHOD] [/v1/path thứ 2]**
  - Mô tả: ...
  - Xử lý đặc biệt: ...

---

## Nhóm 3 · Background Workers (RabbitMQ / Async)

> **Phạm vi:** Tác vụ **bất đồng bộ** chạy qua RabbitMQ — fetch HTML, phân tích, xuất file, nạp dữ liệu.
> Nếu slice của bạn không có worker ở Phase 1 MVP, ghi: _(Không áp dụng ở Phase 1 MVP)_.

- [ ] **[Tên Worker]** — lắng nghe queue: `[tên.queue]`
  - Mô tả: [Worker này nhận message gì và xử lý gì?]
  - Output: [Lưu kết quả ở đâu? Cập nhật bảng nào?]
  - Retry / Dead-letter: [Có / Không]

---

## Nhóm 4 · Dữ liệu & Caching (PostgreSQL · Redis · MinIO)

> **Phạm vi:** Bảng CSDL, cấu trúc JSONB, key Redis và file storage liên quan đến slice.
> PostgreSQL là nguồn sự thật chính; Redis chỉ lưu dữ liệu ngắn hạn và điều phối.

### PostgreSQL

- [ ] **Bảng `[tên_bảng]`** — [Lưu gì? Quan hệ với bảng nào?]
- [ ] **Bảng `[tên_bảng thứ 2]`** — ...
- [ ] **JSONB column `[tên_column]`** trong bảng `[tên_bảng]` — [Lưu cấu trúc evidence dạng gì?]

### Redis

- [ ] **Cache `[mô_tả_key]`** — TTL: [? phút] — Mục đích: [cache kết quả / rate limit / idempotency / lock]

### MinIO (Object Storage)

- [ ] **[Loại file]** — Mục đích lưu: [evidence / report export / ...] — _(Nếu không dùng, bỏ section này)_

---

## Nhóm 5 · Security, Privacy & Trust-by-design

> **Phạm vi:** Các yêu cầu bảo mật và quyền riêng tư **bắt buộc** cho slice này.
> Đây không phải tùy chọn — phải được implement cùng với tính năng chính.

- [ ] **[Tên cơ chế bảo mật / quy tắc privacy]**
  - Mô tả: [Quy tắc này ngăn chặn điều gì? Áp dụng như thế nào trong slice của bạn?]

- [ ] **[Tên cơ chế thứ 2]**
  - Mô tả: ...

---

## Ghi chú & Dependencies

> Liệt kê các tính năng bạn **phụ thuộc** vào slice của thành viên khác.

| Phụ thuộc vào | Của thành viên | Lý do cần |
|---------------|----------------|-----------|
| [Tên tính năng] | [Thành viên số X] | [Bạn cần gì từ tính năng đó?] |

---

*Cập nhật lần cuối: [Ngày] · Phiên bản template: Week 4*
