# W12 — Kiên: Phone/Bank Reputation, Threat Sync & Risk Fusion

**Owner:** Kiên
**Cross-tester:** Hùng
**Primary goal:** S03, S04, T03 đạt G4, E-CORE barrier finalize
**Priority:** P0
**Target Release:** v0.1.0 (Feature Freeze 26/10)

## S03 — Kiểm Tra Reputation Số Điện Thoại (Phone Reputation Lookup)

### User Journey

Người dùng nhập số điện thoại cần kiểm tra → Hệ thống chuẩn hóa định dạng (+84/0x) → Core tra cứu dữ liệu Threat Intelligence và Community Reports đã duyệt → Tính điểm rủi ro → Trả về RiskResult chi tiết.

### FR

* FR01: API POST /v1/scans/phone (hoặc /v1/scans/entity type PHONE) xử lý request.
* FR02: Chuẩn hóa số điện thoại Việt Nam hợp lệ (E.164 / local format).
* FR03: Tra cứu dữ liệu blacklist, whitelist và phản ánh lừa đảo đã xác minh.
* FR04: Trả về RiskResult đầy đủ: điểm rủi ro, phân loại nguy cơ và danh sách bằng chứng.
* FR05: NGUYÊN TẮC BẮT BUỘC: Số điện thoại chưa có dữ liệu phải trả về kết quả NO_DATA, tuyệt đối không được đánh đồng thành SAFE.

### NFR

* Tốc độ tra cứu nhanh (< 200ms với Redis caching).
* Fallback trạng thái degraded nếu nguồn tra cứu gặp sự cố, không làm crash scan.

### Manual UAT

* [ ] Số điện thoại trong danh sách đen trả về RISKY/CRITICAL.
* [ ] Số điện thoại hoàn toàn mới chưa có lịch sử trả về NO_DATA.
* [ ] Nhập số điện thoại sai định dạng trả lỗi 400 rõ ràng.

**Target: G4 do Hùng kiểm thử.**

---

## S04 — Kiểm Tra Reputation Tài Khoản Ngân Hàng (Bank Account Reputation)

### User Journey

Người dùng nhập số tài khoản + mã ngân hàng (hoặc chọn tên ngân hàng) → Hệ thống chuẩn hóa STK và mã ngân hàng theo chuẩn NAPAS/VietQR → Tra cứu danh sách tài khoản lừa đảo / tài khoản rác → Trả về RiskResult.

### FR

* FR01: API POST /v1/scans/bank (type BANK_ACCOUNT) tiếp nhận yêu cầu.
* FR02: Chuẩn hóa và validate mã ngân hàng (bankCode/BIN) theo danh mục chuẩn NAPAS.
* FR03: Tra cứu chính xác theo cặp nhận diện (BankCode + AccountNumber).
* FR04: Trả về bằng chứng lừa đảo liên quan (nếu có trong dữ liệu xác minh).
* FR05: Tài khoản chưa có dữ liệu trả về NO_DATA kèm khuyến nghị thận trọng.

### NFR

* Tuyệt đối không nhầm lẫn giữa các ngân hàng có cùng số tài khoản.
* Tuân thủ bảo mật, không lộ dữ liệu tài chính nhạy cảm ngoài phạm vi cho phép.

### Manual UAT

* [ ] STK lừa đảo đã xác minh cảnh báo RISKY/CRITICAL kèm bằng chứng.
* [ ] STK hợp lệ chưa có phản ánh trả về NO_DATA đúng contract.
* [ ] Mã ngân hàng không hợp lệ bị từ chối ngay từ tầng validation.

**Target: G4 do Hùng kiểm thử.**

---

## T03 — Admin Đồng Bộ / Import Threat Intelligence

### User Journey

Admin đăng nhập → vào mục Quản trị Threat Intelligence → tải lên file danh sách đen (CSV/JSON) hoặc nhấn 'Đồng bộ nguồn ngoài' → Hệ thống batch insert/update vào kho dữ liệu Threat Indicators → cập nhật thống kê và thời gian đồng bộ.

### FR

* FR01: API POST /v1/admin/threat-sources/{id}/sync kích hoạt đồng bộ theo nguồn.
* FR02: API POST import file dữ liệu danh sách đen (Blacklist Domain, IP, Phone, Bank).
* FR03: Batch processing cập nhật bảng threat_indicators tối ưu hiệu năng.
* FR04: Ghi nhận lịch sử đồng bộ (thời gian, số bản ghi mới/cập nhật) và Audit Log.

### NFR

* Xử lý file lớn an toàn, không gây OutOfMemory hoặc khóa bảng database.
* Transaction an toàn: Rollback nếu file dữ liệu import bị hỏng cấu trúc.
* Phân quyền: Chỉ ADMIN mới được thực hiện.

### Manual UAT

* [ ] Admin import file CSV blacklist mẫu thành công và bản ghi tăng lên.
* [ ] Sau khi import, tra cứu số điện thoại/STK mới nạp lập tức nhận diện được.
* [ ] User thường hoặc Guest không thể gọi API import (403/401).

**Target: G3/G4 do Hùng kiểm thử.**

---

## E-CORE — Completion Barrier & Weighted Risk Fusion (Enabler)

* Hoàn thiện cơ chế Barrier gom đầy đủ kết quả từ URL Worker, Text Worker, AI Worker.
* Tính điểm tổng hợp (Weighted Risk Score) theo đúng công thức chuẩn Architecture V3.3.
* Outbox pattern và Event Deduplication bảo đảm không xử lý trùng lặp message từ RabbitMQ.

## Handoffs

* Cho Thắng: Endpoint Scan Phone & Bank và cấu trúc RiskResult để hiển thị trên UI.
* Cho Khải: Cập nhật seed database và migration dữ liệu threat indicators trên Staging.
* Cho Hùng: Danh sách test fixtures cho các số điện thoại và STK lừa đảo mẫu.

## Definition of Done

* [ ] S03 Phone Reputation đạt G4.
* [ ] S04 Bank Reputation đạt G4.
* [ ] T03 Threat Import/Sync đạt G4.
* [ ] E-CORE hoàn thành Barrier và Risk Fusion không lỗi treo.
* [ ] Hùng chạy UAT độc lập và approve trước freeze 26/10.
