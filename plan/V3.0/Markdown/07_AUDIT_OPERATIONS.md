# Audit & Operations — Full-Flow Task Specifications (V3.0 Draft)

> Đây là mô tả các **task lớn có thể test thủ công độc lập** thuộc nhóm Audit & Operations. Không chia từng class/repository thành ticket riêng.

## Danh mục

| Task | Goal | Captain | Tester | Release | Week |
| --- | --- | --- | --- | --- | --- |
| O01 | Admin xem / lọc Audit Log | Khải | Thắng | v0.1.0 | W12 |
| O02 | Operator điều tra lỗi Scan/Worker/AI | Khải | Thắng | v0.2.0 | W15 |


---

## O01 — Admin xem / lọc Audit Log


**Actor:** Admin · **Flow Captain:** Khải · **Cross-tester:** Thắng · **Modules:** M14 · **Release:** v0.1.0 · **Target:** W12 · **Estimate:** 5 SP (chờ đội xác nhận).

**User journey:** Admin mở Audit → lọc actor/action/time/correlationId → xem action đã xảy ra và outcome.

**Dependencies (để tích hợp):** H-M14 audit backend; A02.

**Contract/Fixture để triển khai độc lập:** AuditEvent fixtures + test admin/user.

### Functional Requirements / Acceptance Criteria

| ID | Functional Requirement | Có thể xác nhận bằng |
| --- | --- | --- |
| O01-FR01 | Audit emit | Các thay đổi nhạy cảm phát audit event. |
| O01-FR02 | Query | Lọc và phân trang audit records. |
| O01-FR03 | Admin UI | Xem event và correlation metadata. |


### Non-functional Requirements / Quality evidence

| ID | Quality | Verification |
| --- | --- | --- |
| O01-NFR01 | Redaction | Không lộ token/password/OTP trong audit. |
| O01-NFR02 | Access | Chỉ role được cấp quyền xem. |


### Manual UAT — thành viên khác tự thực hiện

| Test ID | Scenario | Steps | Expected result |
| --- | --- | --- | --- |
| O01-TC01 | Happy | Thao tác login/report review rồi lọc audit | Thấy actor/action/outcome tương ứng. |
| O01-TC02 | Negative | User thường mở audit / scan secret patterns | Bị chặn và không leak secrets. |


### Implementation checklist (không tạo thêm task quản lý)

- Khải chủ trì Admin UI; Hùng backend AuditPort implementation.
- [ ] Contract fixtures/DTO/events tương thích specification; PR link đủ.
- [ ] Unit/contract tests + negative/security cases trong CI.
- [ ] Real integration trên staging; mock chỉ dùng trong lúc phát triển.
- [ ] Peer tester chạy UAT, ghi actual/evidence/defect.
- [ ] Không còn blocker hoặc NFR security/reliability critical chưa xử lý.

---

## O02 — Operator điều tra lỗi Scan/Worker/AI


**Actor:** Admin/Operator · **Flow Captain:** Khải · **Cross-tester:** Thắng · **Modules:** M14,M12,M13 · **Release:** v0.2.0 · **Target:** W15 · **Estimate:** 8 SP (chờ đội xác nhận).

**User journey:** Kích hoạt scan worker failure fixture → Operator tra scanId/correlationId → xem event timeline, retry/DLQ → xác định lỗi.

**Dependencies (để tích hợp):** O01,S01.

**Contract/Fixture để triển khai độc lập:** Injected failure, dead letter fixture.

### Functional Requirements / Acceptance Criteria

| ID | Functional Requirement | Có thể xác nhận bằng |
| --- | --- | --- |
| O02-FR01 | Search | Tra theo scanId/correlationId. |
| O02-FR02 | Timeline | Xem API/event/worker transitions và retry/DLQ state. |
| O02-FR03 | Access | Operator-only view. |


### Non-functional Requirements / Quality evidence

| ID | Quality | Verification |
| --- | --- | --- |
| O02-NFR01 | Traceability | Correlation consistent across API/queue/worker. |
| O02-NFR02 | Privacy | Redact input PII/tokens/secrets. |


### Manual UAT — thành viên khác tự thực hiện

| Test ID | Scenario | Steps | Expected result |
| --- | --- | --- | --- |
| O02-TC01 | Happy | Gây timeout fixture rồi truy vấn scan | Timeline và terminal outcome hiện đúng. |
| O02-TC02 | Negative | User non-admin mở page | 403; không leak. |


### Implementation checklist (không tạo thêm task quản lý)

- Trace propagation, query API, Operations UI, incident runbook.
- [ ] Contract fixtures/DTO/events tương thích specification; PR link đủ.
- [ ] Unit/contract tests + negative/security cases trong CI.
- [ ] Real integration trên staging; mock chỉ dùng trong lúc phát triển.
- [ ] Peer tester chạy UAT, ghi actual/evidence/defect.
- [ ] Không còn blocker hoặc NFR security/reliability critical chưa xử lý.
