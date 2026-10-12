# Scam Detection — Full-Flow Task Specifications (V3.0 Draft)

> Đây là mô tả các **task lớn có thể test thủ công độc lập** thuộc nhóm Scam Detection. Không chia từng class/repository thành ticket riêng.

## Danh mục

| Task | Goal | Captain | Tester | Release | Week |
| --- | --- | --- | --- | --- | --- |
| S01 | Guest Scan URL → RiskResult | Khải | Thắng | v0.1.0 | W11 |
| S02 | Scan tin nhắn / bài đăng giao dịch | Thắng | Kiên | v0.1.0 | W12 |
| S03 | Kiểm tra reputation số điện thoại | Kiên | Hùng | v0.1.0 | W12 |
| S04 | Kiểm tra reputation tài khoản ngân hàng | Kiên | Hùng | v0.1.0 | W12 |
| S05 | Quét/upload QR/VietQR → nested scan | Thắng | Kiên | v0.1.0 | W12 |


---

## S01 — Guest Scan URL → RiskResult


**Actor:** Guest/User · **Flow Captain:** Khải · **Cross-tester:** Thắng · **Modules:** M02,M09,M12,M06,M13 · **Release:** v0.1.0 · **Target:** W11 · **Estimate:** 13 SP (chờ đội xác nhận).

**User journey:** Mở Scan URL → paste URL → submit → xem PENDING/PROCESSING → nhận score/evidence/recommendation hoặc degraded.

**Dependencies (để tích hợp):** E-RUNTIME,E-CORE,R02.

**Contract/Fixture để triển khai độc lập:** URL allowlist fixture + SSRF fixtures + queue spy.

### Functional Requirements / Acceptance Criteria

| ID | Functional Requirement | Có thể xác nhận bằng |
| --- | --- | --- |
| S01-FR01 | Intake | POST /v1/scans/url tạo scanId và không enqueue trùng khi replay hợp lệ. |
| S01-FR02 | URL worker | Phân tích URL an toàn, tạo AnalysisSignal đúng contract. |
| S01-FR03 | Kết quả | RiskResult nhất quán và UI hiển thị trạng thái, evidence. |


### Non-functional Requirements / Quality evidence

| ID | Quality | Verification |
| --- | --- | --- |
| S01-NFR01 | SSRF | Không fetch địa chỉ private/internal hoặc redirect vi phạm. |
| S01-NFR02 | Async reliability | Retry/duplicate/timeout kết thúc theo policy; không processing vô hạn. |


### Manual UAT — thành viên khác tự thực hiện

| Test ID | Scenario | Steps | Expected result |
| --- | --- | --- | --- |
| S01-TC01 | Happy | Dán URL fixture → Scan | Result có score/level/evidence đúng deterministic fixture. |
| S01-TC02 | Negative | URL lỗi/hết quota/SSRF fixture | Không fetch hoặc publish trái phép, thông báo đúng. |
| S01-TC03 | Failure | Worker timeout/queue retry fixture | FAILED/degraded, trace tìm được. |


### Implementation checklist (không tạo thêm task quản lý)

- URL input UI; ScanIntake HTTP adapter; URL worker; queue roundtrip; result UI.
- [ ] Contract fixtures/DTO/events tương thích specification; PR link đủ.
- [ ] Unit/contract tests + negative/security cases trong CI.
- [ ] Real integration trên staging; mock chỉ dùng trong lúc phát triển.
- [ ] Peer tester chạy UAT, ghi actual/evidence/defect.
- [ ] Không còn blocker hoặc NFR security/reliability critical chưa xử lý.

**Ghi chú:** Walking skeleton đầu tiên; scan-engine legacy không thay thế V3.3 URL worker.

---

## S02 — Scan tin nhắn / bài đăng giao dịch


**Actor:** Guest/User · **Flow Captain:** Thắng · **Cross-tester:** Kiên · **Modules:** M03,M11,M12,M09 · **Release:** v0.1.0 · **Target:** W12 · **Estimate:** 13 SP (chờ đội xác nhận).

**User journey:** Paste MESSAGE/TRANSACTION_POST → submit → text worker phân tích pattern, derived indicator, AI task → Final RiskResult.

**Dependencies (để tích hợp):** E-CORE,E-AI,S01 basic,R02.

**Contract/Fixture để triển khai độc lập:** MESSAGE + TRANSACTION_POST fixtures, AI fake and real worker sandbox.

### Functional Requirements / Acceptance Criteria

| ID | Functional Requirement | Có thể xác nhận bằng |
| --- | --- | --- |
| S02-FR01 | Text input | Chọn MESSAGE hoặc TRANSACTION_POST và validate. |
| S02-FR02 | Analysis | Text worker trả signals/derived indicators; child/AI theo policy. |
| S02-FR03 | Completion | Deadline/timeout AI tạo degraded thay vì treo. |


### Non-functional Requirements / Quality evidence

| ID | Quality | Verification |
| --- | --- | --- |
| S02-NFR01 | Sensitive text | Không log raw nội dung nhạy cảm. |
| S02-NFR02 | AI boundary | AI worker qua AMQP, không direct DB/core; đo riêng accuracy. |


### Manual UAT — thành viên khác tự thực hiện

| Test ID | Scenario | Steps | Expected result |
| --- | --- | --- | --- |
| S02-TC01 | Happy | Tin nhắn fixture có pattern/indicator | Evidence & result phản ánh fixture. |
| S02-TC02 | Transaction | Bài đăng giao dịch fixture | Correct contentType. |
| S02-TC03 | AI failure | AI timeout fixture | Không kẹt processing, degraded rõ. |


### Implementation checklist (không tạo thêm task quản lý)

- Text normalization Worker + input UI + AI AMQP consumer + integration, tests.
- [ ] Contract fixtures/DTO/events tương thích specification; PR link đủ.
- [ ] Unit/contract tests + negative/security cases trong CI.
- [ ] Real integration trên staging; mock chỉ dùng trong lúc phát triển.
- [ ] Peer tester chạy UAT, ghi actual/evidence/defect.
- [ ] Không còn blocker hoặc NFR security/reliability critical chưa xử lý.

---

## S03 — Kiểm tra reputation số điện thoại


**Actor:** Guest/User · **Flow Captain:** Kiên · **Cross-tester:** Hùng · **Modules:** M04,M08,M09,M12 · **Release:** v0.1.0 · **Target:** W12 · **Estimate:** 8 SP (chờ đội xác nhận).

**User journey:** Nhập số điện thoại → normalize → tra reputation → hiển thị cảnh báo hoặc trạng thái chưa có dữ liệu.

**Dependencies (để tích hợp):** E-CORE,E-RUNTIME,R02.

**Contract/Fixture để triển khai độc lập:** Known/unknown/unavailable phone fixtures.

### Functional Requirements / Acceptance Criteria

| ID | Functional Requirement | Có thể xác nhận bằng |
| --- | --- | --- |
| S03-FR01 | PHONE normalize | Số điện thoại chuẩn hóa theo strategy đúng. |
| S03-FR02 | Threat lookup | Verified observations có evidence; NO_DATA biểu diễn riêng. |
| S03-FR03 | Result | Trả risk result/label consistent với source. |


### Non-functional Requirements / Quality evidence

| ID | Quality | Verification |
| --- | --- | --- |
| S03-NFR01 | False safety | NO_DATA/REPUTATION_UNAVAILABLE không map thành SAFE. |
| S03-NFR02 | Privacy | Không lộ PII không cần thiết trong log/UI. |


### Manual UAT — thành viên khác tự thực hiện

| Test ID | Scenario | Steps | Expected result |
| --- | --- | --- | --- |
| S03-TC01 | Known risky | Số seeded high-risk | Hiển thị nguồn/evidence hợp lệ. |
| S03-TC02 | Unknown | Số không có dữ liệu | Hiển thị NO_DATA, không SAFE. |
| S03-TC03 | Outage | Threat provider unavailable | Degraded/unavailable, không false safe. |


### Implementation checklist (không tạo thêm task quản lý)

- Entity API + normalizer + ThreatQuery + result UI integration.
- [ ] Contract fixtures/DTO/events tương thích specification; PR link đủ.
- [ ] Unit/contract tests + negative/security cases trong CI.
- [ ] Real integration trên staging; mock chỉ dùng trong lúc phát triển.
- [ ] Peer tester chạy UAT, ghi actual/evidence/defect.
- [ ] Không còn blocker hoặc NFR security/reliability critical chưa xử lý.

---

## S04 — Kiểm tra reputation tài khoản ngân hàng


**Actor:** Guest/User · **Flow Captain:** Kiên · **Cross-tester:** Hùng · **Modules:** M04,M08,M09,M12 · **Release:** v0.1.0 · **Target:** W12 · **Estimate:** 8 SP (chờ đội xác nhận).

**User journey:** Nhập STK và bankCode nếu biết → chuẩn hóa → tra reputation → xem evidence hoặc NO_DATA.

**Dependencies (để tích hợp):** S03 shared pipeline.

**Contract/Fixture để triển khai độc lập:** Bank/account collision fixtures.

### Functional Requirements / Acceptance Criteria

| ID | Functional Requirement | Có thể xác nhận bằng |
| --- | --- | --- |
| S04-FR01 | BANK normalize | BANK_ACCOUNT entityType, value và optional bankCode chính xác. |
| S04-FR02 | Lookup | Không match nhầm cùng account number giữa các bank. |
| S04-FR03 | Result | Có risk/evidence hoặc NO_DATA rõ ràng. |


### Non-functional Requirements / Quality evidence

| ID | Quality | Verification |
| --- | --- | --- |
| S04-NFR01 | False positives | Chính sách bankCode ambiguous được xử lý rõ. |
| S04-NFR02 | PII | Mask dữ liệu tài khoản theo role/context. |


### Manual UAT — thành viên khác tự thực hiện

| Test ID | Scenario | Steps | Expected result |
| --- | --- | --- | --- |
| S04-TC01 | Known | STK + bankCode seeded | Evidence/reputation đúng bank. |
| S04-TC02 | Ambiguous | STK không bankCode có nhiều match | Trả trạng thái theo policy, không match tùy tiện. |
| S04-TC03 | Unknown | STK chưa biết | NO_DATA khác SAFE. |


### Implementation checklist (không tạo thêm task quản lý)

- Dùng chung Entity API; bank normalization/reputation policy; UI Bank flow.
- [ ] Contract fixtures/DTO/events tương thích specification; PR link đủ.
- [ ] Unit/contract tests + negative/security cases trong CI.
- [ ] Real integration trên staging; mock chỉ dùng trong lúc phát triển.
- [ ] Peer tester chạy UAT, ghi actual/evidence/defect.
- [ ] Không còn blocker hoặc NFR security/reliability critical chưa xử lý.

---

## S05 — Quét/upload QR/VietQR → nested scan


**Actor:** Guest/User · **Flow Captain:** Thắng · **Cross-tester:** Kiên · **Modules:** M05,M12,M04,M02,M03 · **Release:** v0.1.0 · **Target:** W12 · **Estimate:** 13 SP (chờ đội xác nhận).

**User journey:** Chủ động chọn file/camera QR → decode trên client theo policy → nhận diện URL/Text/VietQR → child scans → final result.

**Dependencies (để tích hợp):** S01,S02/S03,E-CORE.

**Contract/Fixture để triển khai độc lập:** QR URL/Text/VietQR PNG + child result fixtures.

### Functional Requirements / Acceptance Criteria

| ID | Functional Requirement | Có thể xác nhận bằng |
| --- | --- | --- |
| S05-FR01 | Decode/classify | QR data nhận diện URL/Text/VietQR, unsupported riêng. |
| S05-FR02 | Child routing | Chọn child scan theo payload, không tự mở link. |
| S05-FR03 | Final result | Parent kết thúc sau children/timeout/deadline. |


### Non-functional Requirements / Quality evidence

| ID | Quality | Verification |
| --- | --- | --- |
| S05-NFR01 | Privacy | Không đọc QR/clipboard ngầm; Mobile decode on device, chỉ gửi qrData. |
| S05-NFR02 | Reliability | Retry/duplicate/nested barrier chịu lỗi đúng policy. |


### Manual UAT — thành viên khác tự thực hiện

| Test ID | Scenario | Steps | Expected result |
| --- | --- | --- | --- |
| S05-TC01 | URL QR | Upload QR URL fixture | Không auto navigate, child URL result. |
| S05-TC02 | VietQR | QR bank fixture | Parsed account/bank chính xác. |
| S05-TC03 | Unsupported | QR loại không hỗ trợ | Không đánh giá SAFE. |
| S05-TC04 | Fail child | Child worker fail fixture | Parent terminal/degraded. |


### Implementation checklist (không tạo thêm task quản lý)

- Client QR decoder; worker/task contract; nested orchestrator integration; tests.
- [ ] Contract fixtures/DTO/events tương thích specification; PR link đủ.
- [ ] Unit/contract tests + negative/security cases trong CI.
- [ ] Real integration trên staging; mock chỉ dùng trong lúc phát triển.
- [ ] Peer tester chạy UAT, ghi actual/evidence/defect.
- [ ] Không còn blocker hoặc NFR security/reliability critical chưa xử lý.
