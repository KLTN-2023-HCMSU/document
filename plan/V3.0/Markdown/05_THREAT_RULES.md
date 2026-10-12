# Threat Intelligence & Rules — Full-Flow Task Specifications (V3.0 Draft)

> Đây là mô tả các **task lớn có thể test thủ công độc lập** thuộc nhóm Threat Intelligence & Rules. Không chia từng class/repository thành ticket riêng.

## Danh mục

| Task | Goal | Captain | Tester | Release | Week |
| --- | --- | --- | --- | --- | --- |
| T01 | Admin xem danh sách nguồn Threat Intelligence | Khải | Thắng | v0.1.0 | W12 |
| T02 | Admin bật/tắt nguồn Threat Intelligence | Kiên | Hùng | v0.2.0 | W14 |
| T03 | Admin đồng bộ / import Threat Intelligence | Kiên | Hùng | v0.1.0 | W12 |
| T04 | Admin xem rules và lịch sử versions | Thắng | Kiên | v0.2.0 | W15 |
| T05 | Admin tạo rule version mới | Kiên | Hùng | v0.2.0 | W15 |
| T06 | Admin kích hoạt rule version → scan áp dụng rule mới | Kiên | Hùng | v0.2.0 | W16 |


---

## T01 — Admin xem danh sách nguồn Threat Intelligence


**Actor:** Admin · **Flow Captain:** Khải · **Cross-tester:** Thắng · **Modules:** M08 · **Release:** v0.1.0 · **Target:** W12 · **Estimate:** 3 SP (chờ đội xác nhận).

**User journey:** Admin đăng nhập → mở Threat Sources → xem source, status, last sync và số bản ghi.

**Dependencies (để tích hợp):** K07 ingestion source seed, A02.

**Contract/Fixture để triển khai độc lập:** Seeded risk_sources/risk_source_sync_runs.

### Functional Requirements / Acceptance Criteria

| ID | Functional Requirement | Có thể xác nhận bằng |
| --- | --- | --- |
| T01-FR01 | List source | Đọc nguồn configured và sync metadata. |
| T01-FR02 | Admin UI | Danh sách cơ bản hỗ trợ trạng thái trống/lỗi. |


### Non-functional Requirements / Quality evidence

| ID | Quality | Verification |
| --- | --- | --- |
| T01-NFR01 | RBAC | User không được truy cập nguồn admin. |
| T01-NFR02 | Consistency | Sync timestamp/count phản ánh data thực. |


### Manual UAT — thành viên khác tự thực hiện

| Test ID | Scenario | Steps | Expected result |
| --- | --- | --- | --- |
| T01-TC01 | Happy | Admin mở seeded threat source list | Có nguồn và last sync chính xác. |
| T01-TC02 | Negative | User mở admin page/API | Denied. |


### Implementation checklist (không tạo thêm task quản lý)

- Khải admin UI; Kiên cung cấp source query API theo contract.
- [ ] Contract fixtures/DTO/events tương thích specification; PR link đủ.
- [ ] Unit/contract tests + negative/security cases trong CI.
- [ ] Real integration trên staging; mock chỉ dùng trong lúc phát triển.
- [ ] Peer tester chạy UAT, ghi actual/evidence/defect.
- [ ] Không còn blocker hoặc NFR security/reliability critical chưa xử lý.

---

## T02 — Admin bật/tắt nguồn Threat Intelligence


**Actor:** Admin · **Flow Captain:** Kiên · **Cross-tester:** Hùng · **Modules:** M08 · **Release:** v0.2.0 · **Target:** W14 · **Estimate:** 3 SP (chờ đội xác nhận).

**User journey:** Admin chuyển trạng thái source enabled/disabled → ingestion policy sau đó tuân theo lựa chọn.

**Dependencies (để tích hợp):** T01.

**Contract/Fixture để triển khai độc lập:** Source states fixtures.

### Functional Requirements / Acceptance Criteria

| ID | Functional Requirement | Có thể xác nhận bằng |
| --- | --- | --- |
| T02-FR01 | Toggle source | Persist trạng thái theo policy. |
| T02-FR02 | Effect | Nguồn disabled không tạo sync mới nếu policy quy định. |


### Non-functional Requirements / Quality evidence

| ID | Quality | Verification |
| --- | --- | --- |
| T02-NFR01 | Audit | Ghi action người thay đổi. |
| T02-NFR02 | RBAC | User thường không được toggle. |


### Manual UAT — thành viên khác tự thực hiện

| Test ID | Scenario | Steps | Expected result |
| --- | --- | --- | --- |
| T02-TC01 | Happy | Disable seeded source, trigger sync | Sync bị chặn/skip theo policy. |
| T02-TC02 | Negative | User toggle | 403. |


### Implementation checklist (không tạo thêm task quản lý)

- Toggle API + admin switch UI + audit.
- [ ] Contract fixtures/DTO/events tương thích specification; PR link đủ.
- [ ] Unit/contract tests + negative/security cases trong CI.
- [ ] Real integration trên staging; mock chỉ dùng trong lúc phát triển.
- [ ] Peer tester chạy UAT, ghi actual/evidence/defect.
- [ ] Không còn blocker hoặc NFR security/reliability critical chưa xử lý.

---

## T03 — Admin đồng bộ / import Threat Intelligence


**Actor:** Admin · **Flow Captain:** Kiên · **Cross-tester:** Hùng · **Modules:** M08,M13 · **Release:** v0.1.0 · **Target:** W12 · **Estimate:** 8 SP (chờ đội xác nhận).

**User journey:** Admin chọn nguồn → import/sync fixture → xem result/counters → Scan PHONE/BANK sau thấy dữ liệu mới.

**Dependencies (để tích hợp):** E-RUNTIME Postgres; T01 source list.

**Contract/Fixture để triển khai độc lập:** Known threats JSON/CSV + malformed feeds.

### Functional Requirements / Acceptance Criteria

| ID | Functional Requirement | Có thể xác nhận bằng |
| --- | --- | --- |
| T03-FR01 | Ingest | Normalize + deduplicate + source provenance. |
| T03-FR02 | Sync status | Ghi run counters/error status. |
| T03-FR03 | Reputation effect | ThreatQuery sau sync trả dữ liệu mới. |


### Non-functional Requirements / Quality evidence

| ID | Quality | Verification |
| --- | --- | --- |
| T03-NFR01 | Reliability | Same feed twice không tạo duplicate observation. |
| T03-NFR02 | Security | Không chấp nhận nguồn không đáng tin/unsafe payload. |


### Manual UAT — thành viên khác tự thực hiện

| Test ID | Scenario | Steps | Expected result |
| --- | --- | --- | --- |
| T03-TC01 | Happy | Import known threats fixture rồi scan entity | Reputation thay đổi và nguồn được hiển thị. |
| T03-TC02 | Duplicate | Import fixture lần 2 | Không tăng duplicate. |
| T03-TC03 | Failure | Malformed feed | Sync failed, dữ liệu trước giữ ổn. |


### Implementation checklist (không tạo thêm task quản lý)

- Ingestion worker/service, source sync DB, API/import UI, cache invalidation.
- [ ] Contract fixtures/DTO/events tương thích specification; PR link đủ.
- [ ] Unit/contract tests + negative/security cases trong CI.
- [ ] Real integration trên staging; mock chỉ dùng trong lúc phát triển.
- [ ] Peer tester chạy UAT, ghi actual/evidence/defect.
- [ ] Không còn blocker hoặc NFR security/reliability critical chưa xử lý.

---

## T04 — Admin xem rules và lịch sử versions


**Actor:** Admin · **Flow Captain:** Thắng · **Cross-tester:** Kiên · **Modules:** M09 · **Release:** v0.2.0 · **Target:** W15 · **Estimate:** 3 SP (chờ đội xác nhận).

**User journey:** Admin vào Rules → xem danh sách, active version, previous versions và metadata.

**Dependencies (để tích hợp):** W09 rule repository.

**Contract/Fixture để triển khai độc lập:** Seeded rule versions.

### Functional Requirements / Acceptance Criteria

| ID | Functional Requirement | Có thể xác nhận bằng |
| --- | --- | --- |
| T04-FR01 | Rule list | Đọc versioned repository chính xác. |
| T04-FR02 | UI | Có list/detail, trạng thái active/disabled. |


### Non-functional Requirements / Quality evidence

| ID | Quality | Verification |
| --- | --- | --- |
| T04-NFR01 | RBAC | Chặn user non-admin. |
| T04-NFR02 | Integrity | Hiển thị rõ published vs draft. |


### Manual UAT — thành viên khác tự thực hiện

| Test ID | Scenario | Steps | Expected result |
| --- | --- | --- | --- |
| T04-TC01 | Happy | Mở seeded rules/version | Thấy active và history đúng. |
| T04-TC02 | Negative | User mở Rules admin | 403. |


### Implementation checklist (không tạo thêm task quản lý)

- Thắng làm read-only Admin UI; Kiên cung cấp contract/API.
- [ ] Contract fixtures/DTO/events tương thích specification; PR link đủ.
- [ ] Unit/contract tests + negative/security cases trong CI.
- [ ] Real integration trên staging; mock chỉ dùng trong lúc phát triển.
- [ ] Peer tester chạy UAT, ghi actual/evidence/defect.
- [ ] Không còn blocker hoặc NFR security/reliability critical chưa xử lý.

---

## T05 — Admin tạo rule version mới


**Actor:** Admin · **Flow Captain:** Kiên · **Cross-tester:** Hùng · **Modules:** M09 · **Release:** v0.2.0 · **Target:** W15 · **Estimate:** 5 SP (chờ đội xác nhận).

**User journey:** Admin chọn rule → tạo version mới từ condition/weight/severity/templates → lưu draft.

**Dependencies (để tích hợp):** T04.

**Contract/Fixture để triển khai độc lập:** Rule seed + invalid JSON conditions.

### Functional Requirements / Acceptance Criteria

| ID | Functional Requirement | Có thể xác nhận bằng |
| --- | --- | --- |
| T05-FR01 | Version create | version_no tăng, không mutate active version. |
| T05-FR02 | Validation | Từ chối condition JSON không hợp lệ. |


### Non-functional Requirements / Quality evidence

| ID | Quality | Verification |
| --- | --- | --- |
| T05-NFR01 | Audit | Ghi actor/change summary. |
| T05-NFR02 | Consistency | Unique (rule_id,version_no), optimistic concurrency. |


### Manual UAT — thành viên khác tự thực hiện

| Test ID | Scenario | Steps | Expected result |
| --- | --- | --- | --- |
| T05-TC01 | Happy | Create valid new rule version | Version mới được lưu nhưng chưa active. |
| T05-TC02 | Negative | Edit ACTIVE in-place | Bị từ chối. |


### Implementation checklist (không tạo thêm task quản lý)

- Rule version API+editor form+tests. Reuse W09 repository.
- [ ] Contract fixtures/DTO/events tương thích specification; PR link đủ.
- [ ] Unit/contract tests + negative/security cases trong CI.
- [ ] Real integration trên staging; mock chỉ dùng trong lúc phát triển.
- [ ] Peer tester chạy UAT, ghi actual/evidence/defect.
- [ ] Không còn blocker hoặc NFR security/reliability critical chưa xử lý.

---

## T06 — Admin kích hoạt rule version → scan áp dụng rule mới


**Actor:** Admin · **Flow Captain:** Kiên · **Cross-tester:** Hùng · **Modules:** M09,M12 · **Release:** v0.2.0 · **Target:** W16 · **Estimate:** 8 SP (chờ đội xác nhận).

**User journey:** Admin activate version → Run deterministic scan fixture → RiskResult sử dụng active version mới.

**Dependencies (để tích hợp):** T05; Risk core.

**Contract/Fixture để triển khai độc lập:** Two deterministic rule fixtures producing different scores.

### Functional Requirements / Acceptance Criteria

| ID | Functional Requirement | Có thể xác nhận bằng |
| --- | --- | --- |
| T06-FR01 | Activate | Chỉ active version phù hợp rule/policy; ghi audit. |
| T06-FR02 | Runtime effect | Rule loader và cache cập nhật, scan sau dùng version được activate. |


### Non-functional Requirements / Quality evidence

| ID | Quality | Verification |
| --- | --- | --- |
| T06-NFR01 | Consistency | Không active version của rule khác. |
| T06-NFR02 | Recovery | Có đường rollback/re-activate version trước theo policy. |


### Manual UAT — thành viên khác tự thực hiện

| Test ID | Scenario | Steps | Expected result |
| --- | --- | --- | --- |
| T06-TC01 | Happy | Activate version mới, re-scan fixture | RiskResult/evidence đổi dự kiến. |
| T06-TC02 | Negative | Activation invalid/version from another rule | Rejected, active không đổi. |


### Implementation checklist (không tạo thêm task quản lý)

- Activation API, cache invalidate, admin UI hook, integration test.
- [ ] Contract fixtures/DTO/events tương thích specification; PR link đủ.
- [ ] Unit/contract tests + negative/security cases trong CI.
- [ ] Real integration trên staging; mock chỉ dùng trong lúc phát triển.
- [ ] Peer tester chạy UAT, ghi actual/evidence/defect.
- [ ] Không còn blocker hoặc NFR security/reliability critical chưa xử lý.
