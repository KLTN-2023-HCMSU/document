# W10 — Foundation to Integration

**Plan:** V3.0 Draft  
**Period:** 06/10–12/10/2026  
**Release target:** v0.1.0 — 30/10/2026  
**Status:** Retrospective verification required

## 1. Weekly Goal

Tận dụng toàn bộ foundation đã merge trong W09 để chuẩn bị các user flow đầu tiên chạy qua nhiều thành phần thật.

W10 ưu tiên:
- Authentication frontend/API integration.
- Scan HTTP intake, lifecycle persistence và contracts.
- PostgreSQL, Redis, RabbitMQ, MinIO runtime.
- URL Worker, Text Worker và Result UI baseline.

Không tạo lại W09 contracts nếu đã có.

## 2. Assignment

| Member | Task IDs | Primary deliverables | Target |
|---|---|---|---|
| Hùng | A01, A02 | Registration + Login foundations | G2 |
| Kiên | CORE-01, R01 | Scan intake/lifecycle + status API | G2 |
| Khải | INFRA-01, S01 | Runtime + URL Scan foundation | G2 |
| Thắng | R02, S02 | Result UI + Text Scan foundation | G2 |

## 3. Definitions

- G0: User journey, FR/NFR và acceptance cases rõ ràng.
- G1: OpenAPI/event contracts và fixtures được thống nhất.
- G2: Code đã có, CI và component/contract tests PASS.
- G3: Các thành phần thật tích hợp được.
- G4: Người khác manual cross-test PASS.
- G5: PM nghiệm thu với đầy đủ bằng chứng.

**G2 không đồng nghĩa user flow hoàn thành.** Các chức năng chưa G4 cần tiếp tục sang W11/W12.

## 4. Code Review / Cross-Test

| Implementer | Reviewer |
|---|---|
| Hùng | Khải |
| Kiên | Hùng |
| Khải | Thắng |
| Thắng | Kiên |

Các phần Auth, authorization, SSRF, idempotency, migration và xử lý event phải được review code có trọng tâm, không chỉ kiểm tra UI.

## 5. Technical Constraints

- Architecture V3.3, Modules Specification V1.3 và Schema V1.0 là baseline đã duyệt.
- Shared scan endpoints theo OpenAPI V3.3.
- Guest được scan nhưng không có persistent account history.
- `PHONE` và `BANK_ACCOUNT` sử dụng chung scan type `ENTITY`.
- `NO_DATA` không được hiển thị thành `SAFE`.
- Mock/fixture phải được ghi rõ; không nhận là real integration.
- Không bổ sung payment, subscription hay credit trong MVP.

## 6. Weekly Exit Checklist

- [ ] W09 baseline được đối chiếu với main branch.
- [ ] Không có duplicate implementation của contracts đã duyệt.
- [ ] Auth API và Web integration có test/evidence.
- [ ] Scan contract + worker contract chạy qua automated tests.
- [ ] PostgreSQL migration smoke PASS.
- [ ] RabbitMQ message roundtrip PASS.
- [ ] Result UI hiển thị đúng contract fixtures.
- [ ] Mọi blocker và phần chuyển W11 được ghi vào tracker.

## 7. PM Evidence

Mỗi task cần cập nhật:
- GitHub issue/PR.
- Commit hoặc CI run.
- Test report.
- Current gate.
- Blocker/dependency.
- Remaining work cho W11.

**Weekly outcome:** Foundation sẵn sàng tích hợp, không tự nhận các flow đã full E2E nếu chưa có bằng chứng.