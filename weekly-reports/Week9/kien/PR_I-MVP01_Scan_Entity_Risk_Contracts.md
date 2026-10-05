# Mô tả Pull Request — I-MVP01

## Mã nhóm tính năng

I-MVP01 · Scan / Entity / Risk Contracts + Rule Repository Baseline

## PR này làm gì

Freeze nền tảng W09 để URL, Text, Entity và QR có thể nhận request/dispatch song song mà không phụ thuộc scan lifecycle W10. PR thêm contract `/v1/scans/*`, idempotency scoped theo actor, routing registry, entity/threat/risk ports, fake bus/mock và baseline `rules`/`rule_versions`.

## Vì sao làm theo cách này

- Dùng `MessageBus`/FakeMessageBus thay RabbitMQ runtime để W09 chốt message boundary mà không triển khai retry, consumer hoặc DLQ sớm.
- Dùng request hash canonical SHA-256 để retry cùng semantic payload replay được dù thứ tự JSON khác nhau; key reuse với payload khác bị chặn 409.
- Không dùng `Optional` cho threat lookup: `NO_DATA` và `REPUTATION_UNAVAILABLE` có ý nghĩa nghiệp vụ khác nhau, và không được map thành SAFE.
- Rule condition lưu PostgreSQL `JSONB`; JPA dùng `SqlTypes.JSON` để Hibernate validation khớp Flyway migration.

## Loại thay đổi

- [x] `feat` — thêm feature/contract baseline
- [x] `fix` — bổ sung JSONB mapping và timestamp validation contract
- [x] `test` — thêm contract/integration tests
- [x] `docs` — spec, plan evidence, W09 reports

## Cách kiểm chứng

```bash
cd services/api && ./mvnw test
cd ../..
python -m unittest discover -s contracts/tests -v
npx @redocly/cli lint contracts/openapi/anti-scam-api.yaml
bash scripts/scan-secrets.sh
```

PostgreSQL smoke đã chạy local: Flyway V1–V3 từ DB rỗng, Hibernate validate schema JSONB và API start thành công.

## Ảnh hưởng tới người khác

| Ảnh hưởng | Ai | Họ cần làm gì |
|---|---|---|
| `/v1/scans/*` + `Idempotency-Key` | Khải, Thắng, UI | Không mở rộng legacy `/v1/scan/*`; retry giữ key/payload |
| Queue/routing key fixed | URL/Text/QR worker owner | Dùng mapping trong `ProcessorRegistry` |
| `NO_DATA` is not SAFE | UI/Result/Core | Không render/conclude safe từ thiếu reputation |
| Risk/Threat ports | I-MVP02 | Reuse interface/mock; thay implementation, không đổi contract |

## Checklist trước review

- [x] Maven tests pass tại evidence W09
- [x] Có đường đúng và sai: replay/conflict/quota deny/entity invalid/no data
- [x] Không commit secret; secret scan pass
- [x] OpenAPI được cập nhật và lint valid
- [x] Migration chạy từ PostgreSQL rỗng
- [x] Không thêm lifecycle, worker consumer, DLQ hay final verdict ngoài scope W09
- [ ] Chờ reviewer chéo approve

## Báo cáo tính năng

[`BCTN_I-MVP01_Scan_Entity_Risk_Contracts_Kien.md`](BCTN_I-MVP01_Scan_Entity_Risk_Contracts_Kien.md)
