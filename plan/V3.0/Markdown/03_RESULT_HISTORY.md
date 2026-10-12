# Result, History & Export — Full-Flow Task Specifications (V3.0 Draft)

> Đây là mô tả các **task lớn có thể test thủ công độc lập** thuộc nhóm Result, History & Export. Không chia từng class/repository thành ticket riêng.

## Danh mục

| Task | Goal | Captain | Tester | Release | Week |
| --- | --- | --- | --- | --- | --- |
| R01 | Theo dõi trạng thái scan đang xử lý | Kiên | Hùng | v0.1.0 | W11 |
| R02 | Xem kết quả scan chi tiết | Thắng | Kiên | v0.1.0 | W11 |
| R03 | Xem, lọc và phân trang lịch sử scan | Thắng | Kiên | v0.1.0 | W12 |
| R04 | Xuất kết quả scan PDF/HTML | Thắng | Kiên | v0.1.0 | W12 |
| R05 | Chia sẻ kết quả scan theo chính sách quyền | Khải | Thắng | v0.3.0 | W17 |


---

## R01 — Theo dõi trạng thái scan đang xử lý


**Actor:** Guest/User · **Flow Captain:** Kiên · **Cross-tester:** Hùng · **Modules:** M06,M12 · **Release:** v0.1.0 · **Target:** W11 · **Estimate:** 5 SP (chờ đội xác nhận).

**User journey:** Sau submit có scanId → UI truy vấn trạng thái → PENDING/PROCESSING → terminal result/failed theo policy.

**Dependencies (để tích hợp):** E-CORE.

**Contract/Fixture để triển khai độc lập:** Delayed result fixture; guest access proof.

### Functional Requirements / Acceptance Criteria

| ID | Functional Requirement | Có thể xác nhận bằng |
| --- | --- | --- |
| R01-FR01 | Scan status endpoint | Dữ liệu scanId/status theo ownership proof. |
| R01-FR02 | Client polling | Dừng khi terminal; không polling vô hạn. |
| R01-FR03 | Error states | Không mất trạng thái FAILED/degraded. |


### Non-functional Requirements / Quality evidence

| ID | Quality | Verification |
| --- | --- | --- |
| R01-NFR01 | Authorization | Guest proof và user ownership chính xác. |
| R01-NFR02 | Resource use | Polling interval/backoff hợp lý; không quá tải backend. |


### Manual UAT — thành viên khác tự thực hiện

| Test ID | Scenario | Steps | Expected result |
| --- | --- | --- | --- |
| R01-TC01 | Happy | Dùng delayed worker fixture | Quan sát trạng thái chuyển đến terminal. |
| R01-TC02 | Failure | Worker timeout + invalid guest proof | Terminal hiện đúng và từ chối truy cập trái phép. |


### Implementation checklist (không tạo thêm task quản lý)

- Status API, polling hook, ownership checks + async tests.
- [ ] Contract fixtures/DTO/events tương thích specification; PR link đủ.
- [ ] Unit/contract tests + negative/security cases trong CI.
- [ ] Real integration trên staging; mock chỉ dùng trong lúc phát triển.
- [ ] Peer tester chạy UAT, ghi actual/evidence/defect.
- [ ] Không còn blocker hoặc NFR security/reliability critical chưa xử lý.

---

## R02 — Xem kết quả scan chi tiết


**Actor:** Guest/User · **Flow Captain:** Thắng · **Cross-tester:** Kiên · **Modules:** M06,M09 · **Release:** v0.1.0 · **Target:** W11 · **Estimate:** 5 SP (chờ đội xác nhận).

**User journey:** Từ scan/status chọn xem chi tiết → thấy score, level, evidence, explanation và recommendation.

**Dependencies (để tích hợp):** RiskResult contract W09;R01.

**Contract/Fixture để triển khai độc lập:** RiskResult valid+degraded fixture.

### Functional Requirements / Acceptance Criteria

| ID | Functional Requirement | Có thể xác nhận bằng |
| --- | --- | --- |
| R02-FR01 | Details | Render RiskResult schema và error/degraded metadata. |
| R02-FR02 | Navigation | Đi từ scan submit hoặc history tới result chính xác. |


### Non-functional Requirements / Quality evidence

| ID | Quality | Verification |
| --- | --- | --- |
| R02-NFR01 | A11y | Kết quả có text label, không chỉ dùng màu. |
| R02-NFR02 | Privacy | Mask input nhạy cảm và chặn cross-user IDOR. |


### Manual UAT — thành viên khác tự thực hiện

| Test ID | Scenario | Steps | Expected result |
| --- | --- | --- | --- |
| R02-TC01 | Safe/risky fixture | Mở result fixtures | Score, explanation, evidence đúng. |
| R02-TC02 | Degraded | Mở result partial fixture | Không hiển thị sai thành SAFE. |


### Implementation checklist (không tạo thêm task quản lý)

- Tái sử dụng UI demo mock W09, nối real endpoint và tests.
- [ ] Contract fixtures/DTO/events tương thích specification; PR link đủ.
- [ ] Unit/contract tests + negative/security cases trong CI.
- [ ] Real integration trên staging; mock chỉ dùng trong lúc phát triển.
- [ ] Peer tester chạy UAT, ghi actual/evidence/defect.
- [ ] Không còn blocker hoặc NFR security/reliability critical chưa xử lý.

---

## R03 — Xem, lọc và phân trang lịch sử scan


**Actor:** Authenticated User · **Flow Captain:** Thắng · **Cross-tester:** Kiên · **Modules:** M06,M01,M12 · **Release:** v0.1.0 · **Target:** W12 · **Estimate:** 8 SP (chờ đội xác nhận).

**User journey:** Login → có các scan → mở History → lọc/phân trang → xem lại result; Guest không có account history.

**Dependencies (để tích hợp):** A02,R01,R02.

**Contract/Fixture để triển khai độc lập:** 2 seeded users + scan history rows.

### Functional Requirements / Acceptance Criteria

| ID | Functional Requirement | Có thể xác nhận bằng |
| --- | --- | --- |
| R03-FR01 | History API | Chỉ trả scan persistent theo owner; phân trang/filter. |
| R03-FR02 | History UI | Hiển thị danh sách và điều hướng detail. |
| R03-FR03 | Guest policy | Guest không tạo persistent account history. |


### Non-functional Requirements / Quality evidence

| ID | Quality | Verification |
| --- | --- | --- |
| R03-NFR01 | IDOR | User B không xem history User A. |
| R03-NFR02 | Performance | Pagination server-side tránh trả toàn bộ lịch sử. |


### Manual UAT — thành viên khác tự thực hiện

| Test ID | Scenario | Steps | Expected result |
| --- | --- | --- | --- |
| R03-TC01 | Happy | Seed 2 users nhiều scan, mở history | Chỉ thấy dữ liệu đúng owner. |
| R03-TC02 | Guest | Guest scan rồi mở history | Không có account history. |
| R03-TC03 | Paging | Page/filter fixture | Thứ tự và số item chính xác. |


### Implementation checklist (không tạo thêm task quản lý)

- History query API + UI + DB indexes/tests.
- [ ] Contract fixtures/DTO/events tương thích specification; PR link đủ.
- [ ] Unit/contract tests + negative/security cases trong CI.
- [ ] Real integration trên staging; mock chỉ dùng trong lúc phát triển.
- [ ] Peer tester chạy UAT, ghi actual/evidence/defect.
- [ ] Không còn blocker hoặc NFR security/reliability critical chưa xử lý.

---

## R04 — Xuất kết quả scan PDF/HTML


**Actor:** Guest/User · **Flow Captain:** Thắng · **Cross-tester:** Kiên · **Modules:** M06,M13 · **Release:** v0.1.0 · **Target:** W12 · **Estimate:** 5 SP (chờ đội xác nhận).

**User journey:** Xem Result → chọn Export → tạo/tải file PDF/HTML → nội dung khớp kết quả và quyền truy cập.

**Dependencies (để tích hợp):** R02; object storage/export.

**Contract/Fixture để triển khai độc lập:** Stored scan result + MinIO fixture.

### Functional Requirements / Acceptance Criteria

| ID | Functional Requirement | Có thể xác nhận bằng |
| --- | --- | --- |
| R04-FR01 | Export request | Tạo artifact đúng format tối thiểu MVP. |
| R04-FR02 | Download | Download link/token kiểm tra ownership. |
| R04-FR03 | Content | Có score/evidence/time và disclaimer. |


### Non-functional Requirements / Quality evidence

| ID | Quality | Verification |
| --- | --- | --- |
| R04-NFR01 | Secure download | URL không public lộ dữ liệu người khác. |
| R04-NFR02 | Durability | Download hoặc failure state rõ; không mất file khi retry. |


### Manual UAT — thành viên khác tự thực hiện

| Test ID | Scenario | Steps | Expected result |
| --- | --- | --- | --- |
| R04-TC01 | Happy | Export fixture scan | File mở được, đúng result. |
| R04-TC02 | Security | User khác tải export | 403/deny theo policy. |


### Implementation checklist (không tạo thêm task quản lý)

- Export endpoint, renderer, secured download, basic UI.
- [ ] Contract fixtures/DTO/events tương thích specification; PR link đủ.
- [ ] Unit/contract tests + negative/security cases trong CI.
- [ ] Real integration trên staging; mock chỉ dùng trong lúc phát triển.
- [ ] Peer tester chạy UAT, ghi actual/evidence/defect.
- [ ] Không còn blocker hoặc NFR security/reliability critical chưa xử lý.

---

## R05 — Chia sẻ kết quả scan theo chính sách quyền


**Actor:** User · **Flow Captain:** Khải · **Cross-tester:** Thắng · **Modules:** M06,M01 · **Release:** v0.3.0 · **Target:** W17 · **Estimate:** 3 SP (chờ đội xác nhận).

**User journey:** User chọn Share result → tạo liên kết/artefact được cấp phép → người nhận xem được theo policy.

**Dependencies (để tích hợp):** R02,R04.

**Contract/Fixture để triển khai độc lập:** Public/private result fixtures.

### Functional Requirements / Acceptance Criteria

| ID | Functional Requirement | Có thể xác nhận bằng |
| --- | --- | --- |
| R05-FR01 | Share | Link/token có scope và expiry nếu policy yêu cầu. |
| R05-FR02 | Revoke | Quyền thu hồi hoặc giới hạn đúng policy. |


### Non-functional Requirements / Quality evidence

| ID | Quality | Verification |
| --- | --- | --- |
| R05-NFR01 | Privacy | Không tự public scan của Guest/User. |
| R05-NFR02 | Security | Không IDOR hoặc đoán share token. |


### Manual UAT — thành viên khác tự thực hiện

| Test ID | Scenario | Steps | Expected result |
| --- | --- | --- | --- |
| R05-TC01 | Happy | Share allowed result theo policy | Người nhận mở được đúng phạm vi. |
| R05-TC02 | Negative | Expired/revoked link | Denied. |


### Implementation checklist (không tạo thêm task quản lý)

- Share UI + share proof + authorization tests.
- [ ] Contract fixtures/DTO/events tương thích specification; PR link đủ.
- [ ] Unit/contract tests + negative/security cases trong CI.
- [ ] Real integration trên staging; mock chỉ dùng trong lúc phát triển.
- [ ] Peer tester chạy UAT, ghi actual/evidence/defect.
- [ ] Không còn blocker hoặc NFR security/reliability critical chưa xử lý.
