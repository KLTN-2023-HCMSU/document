# PM Runbook — Cách điều hành Plan V3.0

## 1. Phương pháp sử dụng

**Scrumban + User Journey Mapping + Vertical Slicing + Contract-First Development + Risk-based verification**.

- **Scrumban**: hoạch định, kiểm tra và demo hằng tuần; Kanban để giới hạn WIP, unblock liên tục.
- **User Journey Mapping**: full task bắt đầu từ một hành động của Guest/User/Moderator/Admin và kết thúc ở kết quả người dùng kiểm chứng được.
- **Vertical Slicing**: nghiệm thu xuyên UI → API → worker → persistence → UI.
- **Contract First**: API schema/event/fixtures giúp thành viên code song song mà không xung đột trực tiếp.
- **Risk-based verification**: CI rộng; review kỹ Auth/SSRF/PII/rule/race; peer manual UAT cho mỗi flow.

## 2. Ownership và công bằng tải

| Member | Flow Captain | UAT peer assignments | Remaining flow SP (ước lượng) | Shared enabler SP | Total initial SP |
| --- | --- | --- | --- | --- | --- |
| Hùng | 8 | 8 | 44 | 14 | 58 |
| Kiên | 8 | 8 | 47 | 13 | 60 |
| Khải | 8 | 8 | 43 | 17 | 60 |
| Thắng | 8 | 8 | 53 | 8 | 61 |


**Lưu ý:** SP là relative effort ban đầu, KHÔNG được đổi trực tiếp thành ngày công. Giảm SP theo code đã merge W09 chỉ sau khi có evidence (CI, behavior). Flow Captain có thể không viết toàn bộ backend/module; owner module giữ quyền quyết định kỹ thuật trong phạm vi đã duyệt.

## 3. Cấu trúc task & một source of truth

- **Excel → `Flow Tasks`:** task trạng thái, priority, SP, owner, target, gate, evidence/defect; mỗi task một dòng.
- **Excel → `Weekly Board`:** theo dõi từ W10–W19; không tạo lại task theo tuần.
- **Excel → `Requirements`:** FR/NFR có trace về task ID, acceptance.
- **Excel → `Manual UAT`:** testcase, tester, expected/actual, evidence.
- **Excel → `Releases`:** ngày đã duyệt; RC/freeze/release gates.
- **Markdown → Spec theo functional area:** user journey, FR, NFR, UAT, fixture, dependencies.

## 4. Quy trình tuần và WIP

1. **Thứ 2:** 30 phút planning và cập nhật trạng thái flow/Gate, tuần mục tiêu, SP còn lại.
2. **Mỗi ngày:** 10–12 phút blocker/contract mismatch; không báo cáo từng file AI sinh.
3. **Thứ 5:** integration checkpoint; ưu tiên thông suốt queue/DB/API; unblock trước khi nhận task mới.
4. **Thứ 7:** peer manual UAT & demo staging, ghi actual/evidence, triage bug.
5. **Giới hạn WIP:** tối đa 2 full-flow có tích hợp chủ động cùng 1 enabler blocker; các task khác có thể code độc lập bằng contract fixtures.
6. **Escalation:** blocker tồn tại quá 1 ngày hoặc ảnh hưởng 26/10 freeze → Leader quyết định ưu tiên ngay; thiếu release gate → không tự đánh dấu Done.

## 5. Gates

| Gate | Ý nghĩa | Evidence |
| --- | --- | --- |
| G0 | Requirements Ready | Journey, FR, NFR, test fixtures, Owner, Tester, dependencies |
| G1 | Contract Ready | OpenAPI/event fixtures accepted by producer+consumer |
| G2 | Code Ready | CI xanh; unit/contract/security tests |
| G3 | Integrated | Real API/DB/queue/worker boundary, staging smoke; không phải mock-only |
| G4 | Peer UAT | Tester KHÁC owner chạy manual test, có actual + evidence |
| G5 | Accepted | PM kiểm FR/NFR critical, no blockers, sign-off |


**Một flow chỉ Accepted khi đạt G5.** Component-only green không tương đương full-flow.

## 6. Quy tắc review AI-generated code

- Bắt buộc CI lint/build/unit/contract/security; review các phần rủi ro cao: access/authorization, session, SSRF, quota/idempotency, schema migrations, event dedup/outbox, sensitive logs, AI prompt/provider.
- Không ép reviewer đọc toàn bộ file sinh bởi AI; ưu tiên diff summary, architecture invariants, boundary tests, rollback, failure cases.
- Tester tự thao tác staging; không chỉ xem video happy path do author chuẩn bị.

## 7. Quy định release 30/10

- 26/10 **feature freeze**; 27–28/10 regression/defect fix; 29/10 RC; 30/10 tag/deploy/demo v0.1.0.
- P0 là chức năng tối thiểu cần để đạt breadth-complete M01–M14; P1/P2 là chiều sâu đẩy sang v0.2/v0.3 **chỉ nơi bản spec cho phép**.
- Nếu có module M01–M14 chưa chứng minh capability tối thiểu, đánh dấu **Release Risk/Blocked**, xin quyết định phạm vi chính thức hoặc no-go; không ngầm đổi mốc đã duyệt.
- Web/Mobile parity: Web-first để dựng integration, nhưng theo dõi kết quả từng client riêng; Mobile chưa có implementation trong snapshot.
- AI accuracy là validation riêng, AI fixture pass chỉ xác nhận technical integration.

## 8. Mẫu buổi weekly review

1. Flow nào sang G3/G4/G5 từ tuần trước? Demo live bằng tài khoản test.
2. Flow P0 nào vẫn đang G0–G2? Ai đang block và ETA?
3. Các case UAT FAIL/Blocked có link defect chưa?
4. Thời gian còn lại đến 26/10 feature freeze & 29/10 RC?
5. SP effort còn lại của từng người đã lệch bao nhiêu? Có thể cân gói độc lập không?
6. Release gate và Security/Persistence/Queue/Mobile risks có owner/decision chưa?
