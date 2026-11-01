# Mô tả Pull Request — W10/W11 CORE-01, R01, S03, S04 (DRAFT)

> **Chưa có URL/ID PR được xác minh.** Nội dung này dùng để dán vào PR khi mở. Branch trong báo cáo Agent: `feat/CORE-01-R01-scan-lifecycle`.

## Mã nhóm tính năng

CORE-01 · Scan Intake & Async Lifecycle; R01 · Scan Status; S03 · Phone Reputation; S04 · Bank Reputation (partial).

## PR này làm gì

Bổ sung HTTP scan controller, durable PostgreSQL scan/outbox persistence, Redis idempotency/quota, AES-GCM masking, lifecycle/status API; tiếp tục W11 với V5 results/signals/inbox, RabbitMQ outbox publisher, worker result consumer, deadline recovery và TEXT topology. **S03/S04 mới có normalization/contract, chưa có reputation provider production.**

## Vì sao làm theo cách này

- Đưa ScanRequest + OutboxEvent vào một PostgreSQL transaction để giữ ý định dispatch khi publisher/worker tạm thời lỗi.
- RabbitMQ dùng at-least-once delivery và inbox `processed_events` để chống xử lý lại duplicate WorkerResult; không giả định exactly-once.
- PostgreSQL unique `(idempotency_scope,idempotency_key)` làm nguồn quyết định scan winner; Redis làm coordination/quota và cần recovery ngoài transaction.
- AES-GCM bảo vệ raw payload và mask display; không đưa input nhạy cảm vào outbox plaintext.
- Không coi `NO_DATA` hay reputation outage là evidence an toàn; Risk Fusion vẫn cần policy-approved mixed evidence handling.

## Loại thay đổi

- [x] `feat` — HTTP/lifecycle/outbox/broker foundation
- [x] `fix` — idempotency/quota/privacy/normalization
- [x] `test` — focused, contract, runtime smoke
- [x] `docs` — Superpowers plans/reports
- [x] `chore` — môi trường RabbitMQ, migration

## Cách kiểm chứng

```bash
cd services/api && ./mvnw -B test
cd ../..
python3 -m unittest discover -s contracts/tests -v
python3 scripts/ci-runtime-smoke.py
bash scripts/scan-secrets.sh

git diff --check
```

**Evidence:** completion report 19/10 ghi 155 Maven tests, 21 Python, URL broker fixture PASS. Follow-up báo 11 focused PASS, còn runtime rerun mất exit code. **Không đánh dấu CI xanh từ báo cáo Agent nếu chưa có CI URL ở PR.**

## Ảnh hưởng tới người khác

- [x] Có ảnh hưởng:

| Ảnh hưởng | Ai | Cần làm gì |
|---|---|---|
| `/v1/scans/*` + response scanId/status | Thắng | Dùng contracts mới, thực hiện polling/UI |
| `q.scan.url`, `q.scan.text`, `.analyzed` result events | Khải/Thắng | Xác minh worker queue/schema, production roundtrip |
| Guest proof/entitlement | Hùng | Duyệt issuer/authorization contract, test negative |
| Fusion semantic | Hùng + team | Duyệt active policy/NO_DATA/degraded |
| V4/V5 migrations | Backend team | Test bootstrap, không chỉnh migration đã apply |

## Checklist trước khi xin review

- [ ] CI xanh tại SHA được review (chưa cung cấp evidence)
- [x] Có tests cho happy path và nhiều đường sai (theo source/báo cáo Agent)
- [ ] Secret scan clean sau triage 2 synthetic fixtures
- [ ] Không log sensitive input (cần audit cuối)
- [x] Scan API có OpenAPI contract
- [ ] `.env.example` đã được reviewer xác nhận đủ cho môi trường triển khai
- [ ] Migration V1–V5 từ DB rỗng có log ở CI commit review
- [ ] Header comments/review chéo tất cả file mới được hoàn tất
- [ ] Quota token+idempotent release, timeout/result race, mixed NO_DATA hoàn thành
- [ ] TEXT fixture + real worker test qua RabbitMQ PASS

## Báo cáo tính năng

- W10: `document/weekly-reports/Week10/kien/BCTN_CORE-01_Scan_Intake_Lifecycle_Kien.md`; `BCTN_R01_Theo_doi_trang_thai_scan_Kien.md` — **bản nháp/chưa nghiệm thu**.
- W11: `document/weekly-reports/Week11/kien/BCTN_CORE-01_Async_Risk_Core_Kien.md`, `BCTN_R01_Status_Runtime_Kien.md`, `BCTN_S03_S04_Entity_Reputation_Kien.md` — **bản nháp/partial**.

## Ảnh chụp màn hình

Không áp dụng — PR này chưa sửa frontend. Thay bằng test log/smoke output đã ẩn secret nếu reviewer yêu cầu.

**Trạng thái đề xuất: Draft PR / chưa merge.**
