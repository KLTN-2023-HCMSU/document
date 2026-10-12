# Community & Moderation — Full-Flow Task Specifications (V3.0 Draft)

> Đây là mô tả các **task lớn có thể test thủ công độc lập** thuộc nhóm Community & Moderation. Không chia từng class/repository thành ticket riêng.

## Danh mục

| Task | Goal | Captain | Tester | Release | Week |
| --- | --- | --- | --- | --- | --- |
| C01 | Gửi báo cáo đối tượng nghi lừa đảo | Hùng | Khải | v0.1.0 | W11 |
| C02 | Xem trạng thái báo cáo đã gửi | Khải | Thắng | v0.2.0 | W14 |
| C03 | Moderator xác minh / từ chối báo cáo | Hùng | Khải | v0.1.0 | W12 |


---

## C01 — Gửi báo cáo đối tượng nghi lừa đảo


**Actor:** User · **Flow Captain:** Hùng · **Cross-tester:** Khải · **Modules:** M07,M13 · **Release:** v0.1.0 · **Target:** W11 · **Estimate:** 8 SP (chờ đội xác nhận).

**User journey:** Login → điền đối tượng URL/PHONE/BANK/Text → mô tả, bằng chứng → gửi → nhận reportId.

**Dependencies (để tích hợp):** A02; object storage if evidence.

**Contract/Fixture để triển khai độc lập:** Reported entity + evidence fixture.

### Functional Requirements / Acceptance Criteria

| ID | Functional Requirement | Có thể xác nhận bằng |
| --- | --- | --- |
| C01-FR01 | Submit report | Validate report type/content; lưu trạng thái PENDING. |
| C01-FR02 | Evidence | Upload bằng chứng đúng giới hạn hoặc fixture. |
| C01-FR03 | Confirmation | User thấy reportId/ack. |


### Non-functional Requirements / Quality evidence

| ID | Quality | Verification |
| --- | --- | --- |
| C01-NFR01 | Abuse | Giới hạn spam/rate-limit, validate file uploads. |
| C01-NFR02 | Privacy | Không lộ reporter PII và evidence URL public. |


### Manual UAT — thành viên khác tự thực hiện

| Test ID | Scenario | Steps | Expected result |
| --- | --- | --- | --- |
| C01-TC01 | Happy | Submit report seeded entity+evidence | Report được lưu với trạng thái PENDING. |
| C01-TC02 | Negative | Invalid data/oversized evidence | Không accept sai policy. |


### Implementation checklist (không tạo thêm task quản lý)

- Community report API + simple form + persistence.
- [ ] Contract fixtures/DTO/events tương thích specification; PR link đủ.
- [ ] Unit/contract tests + negative/security cases trong CI.
- [ ] Real integration trên staging; mock chỉ dùng trong lúc phát triển.
- [ ] Peer tester chạy UAT, ghi actual/evidence/defect.
- [ ] Không còn blocker hoặc NFR security/reliability critical chưa xử lý.

---

## C02 — Xem trạng thái báo cáo đã gửi


**Actor:** User · **Flow Captain:** Khải · **Cross-tester:** Thắng · **Modules:** M07 · **Release:** v0.2.0 · **Target:** W14 · **Estimate:** 3 SP (chờ đội xác nhận).

**User journey:** User mở My Reports → chọn report → thấy trạng thái PENDING/VERIFIED/REJECTED và ghi chú được phép.

**Dependencies (để tích hợp):** C01.

**Contract/Fixture để triển khai độc lập:** Two-user report fixtures.

### Functional Requirements / Acceptance Criteria

| ID | Functional Requirement | Có thể xác nhận bằng |
| --- | --- | --- |
| C02-FR01 | Report list/detail | Chỉ report do user sở hữu được truy cập. |
| C02-FR02 | Status | Hiển thị trạng thái với timeline/timestamp. |


### Non-functional Requirements / Quality evidence

| ID | Quality | Verification |
| --- | --- | --- |
| C02-NFR01 | Authorization | Chặn IDOR và information leak. |
| C02-NFR02 | Usability | Refresh giữ trạng thái chính xác. |


### Manual UAT — thành viên khác tự thực hiện

| Test ID | Scenario | Steps | Expected result |
| --- | --- | --- | --- |
| C02-TC01 | Happy | Xem report đã seed | Status đúng database. |
| C02-TC02 | Negative | User B mở report của A | Forbidden. |


### Implementation checklist (không tạo thêm task quản lý)

- Report query endpoint + My Reports UI; Hùng giữ module backend.
- [ ] Contract fixtures/DTO/events tương thích specification; PR link đủ.
- [ ] Unit/contract tests + negative/security cases trong CI.
- [ ] Real integration trên staging; mock chỉ dùng trong lúc phát triển.
- [ ] Peer tester chạy UAT, ghi actual/evidence/defect.
- [ ] Không còn blocker hoặc NFR security/reliability critical chưa xử lý.

---

## C03 — Moderator xác minh / từ chối báo cáo


**Actor:** Moderator · **Flow Captain:** Hùng · **Cross-tester:** Khải · **Modules:** M07,M08,M14 · **Release:** v0.1.0 · **Target:** W12 · **Estimate:** 13 SP (chờ đội xác nhận).

**User journey:** Moderator mở queue → xem evidence → VERIFY/REJECT → audit → nếu verified tạo reputation signal → entity scan sau dùng được.

**Dependencies (để tích hợp):** C01; K04 reputation contract.

**Contract/Fixture để triển khai độc lập:** Report pending, seeded moderator, ThreatQuery fake.

### Functional Requirements / Acceptance Criteria

| ID | Functional Requirement | Có thể xác nhận bằng |
| --- | --- | --- |
| C03-FR01 | Review | Chỉ moderator có thể xử lý, ghi decision/actor/time. |
| C03-FR02 | Bridge | VERIFIED report tạo observation hợp lệ; REJECTED không tạo reputation. |
| C03-FR03 | UX | Queue + evidence + decision + trạng thái. |


### Non-functional Requirements / Quality evidence

| ID | Quality | Verification |
| --- | --- | --- |
| C03-NFR01 | Provenance | Verified signal giữ source/report proof, không tự coi reports chưa duyệt là sự thật. |
| C03-NFR02 | Concurrency | Không quyết định trùng/lost update; audit đầy đủ. |


### Manual UAT — thành viên khác tự thực hiện

| Test ID | Scenario | Steps | Expected result |
| --- | --- | --- | --- |
| C03-TC01 | Verified | Moderator xác minh report fixture, scan lại entity | Reputation cập nhật theo policy. |
| C03-TC02 | Rejected | Moderator reject, scan lại entity | Không tăng reputation xấu. |
| C03-TC03 | RBAC | User thường gọi review | Denied. |


### Implementation checklist (không tạo thêm task quản lý)

- Moderation API, UI integration with existing components, approved observation bridge.
- [ ] Contract fixtures/DTO/events tương thích specification; PR link đủ.
- [ ] Unit/contract tests + negative/security cases trong CI.
- [ ] Real integration trên staging; mock chỉ dùng trong lúc phát triển.
- [ ] Peer tester chạy UAT, ghi actual/evidence/defect.
- [ ] Không còn blocker hoặc NFR security/reliability critical chưa xử lý.
