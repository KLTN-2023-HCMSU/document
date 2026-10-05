# Báo cáo tuần W09 — Kiên

| Trường | Giá trị |
|---|---|
| **Tuần** | W09 · 27/09–05/10/2026 |
| **Sprint** | MVP W09 |
| **Người** | Kiên |
| **Giờ làm thực tế** | Chưa ghi nhận độc lập; xem commit/test evidence |

## 1. Deadline tuần này

| Mã | Việc theo timeline | Hạn | Trạng thái | Evidence |
|---|---|---|---|---|
| I-MVP01 | Scan/entity/risk contracts + rule repository baseline | 05/10 | ✅ Hoàn thành implementation slice | `8d02692` – `ead99af` |

## 2. Đã làm gì

- Freeze request/dispatch contract URL, TEXT, ENTITY, QR; thêm OpenAPI plural namespace và `Idempotency-Key`.
- Thêm access/quota/ownership seam cho Guest và Free; quota deny không publish job.
- Implement canonical request hash, replay/conflict semantics và Redis/in-memory idempotency adapters.
- Freeze processor registry, queue/routing keys, entity PHONE/BANK normalization, `NO_DATA`/unavailable semantics, risk/threat mock ports.
- Tạo PostgreSQL migration V3 `rules`/`rule_versions`; smoke Flyway từ DB rỗng và Hibernate JSONB validation.

## 3. Đã review code của ai

| PR | Của ai | Kết luận | Phát hiện |
|---|---|---|---|
| Không có evidence review được ghi nhận | — | — | Cần thực hiện review chéo trước merge PR I-MVP01 |

## 4. Đang bị chặn / đang chặn người khác

| Loại | Nội dung | Với ai | Cách gỡ |
|---|---|---|---|
| Tôi đang bị chặn | Không có blocker kỹ thuật W09 | — | — |
| Tôi đang chặn người khác | Chưa có runtime consumer/Risk Core | Khải, Thắng, I-MVP02 | W10 thay FakeMessageBus/mock ports bằng implementation thật |

## 5. Số liệu tuần

| Chỉ số | Giá trị |
|---|---|
| Commit I-MVP01 | 12 commit implementation/docs/fix evidence |
| Test contract URL | 11/11 pass |
| Maven suite | pass tại thời điểm nghiệm thu W09 |
| OpenAPI | valid; 5 warning baseline ngoài scope |
| Migration smoke | PostgreSQL 16, Flyway V1–V3 pass |

## 6. Kế hoạch tuần sau

| Mã | Việc | Hạn | Ghi chú |
|---|---|---|---|
| I-MVP02 | Persist scan/lifecycle, worker-result consumer, risk core baseline | W10 | Reuse contracts W09; không redesign intake | 
| Team | Review chéo I-MVP01/contract freeze | Trước merge | Xác nhận owner URL/Text/QR consume đúng routing |

## 7. Rủi ro tự nhận thấy

| Rủi ro | Mức | Dấu hiệu | Đề xuất |
|---|---|---|---|
| Contract bị đổi khi W10 integration | Trung bình | Worker/UI tự tạo DTO/routing khác W09 | Bắt consumer dùng OpenAPI, fixture và port W09 |
| Redis atomic semantics chưa integration-test với Redis thật | Trung bình | Unit dùng in-memory; adapter có Lua script | Thêm Redis integration test khi I-MVP02 dựng runtime |

## 8. Báo cáo tính năng đã nộp

| Mã | File |
|---|---|
| I-MVP01 | [`BCTN_I-MVP01_Scan_Entity_Risk_Contracts_Kien.md`](BCTN_I-MVP01_Scan_Entity_Risk_Contracts_Kien.md) |

*Nộp: 05/10/2026 · Mẫu: Báo cáo tuần v1.0*
