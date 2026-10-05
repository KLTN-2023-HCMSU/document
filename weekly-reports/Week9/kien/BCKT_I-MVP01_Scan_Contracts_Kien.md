# I-MVP01 · Scan / Entity / Risk Contracts — Báo cáo kiểm thử

| Trường | Giá trị |
|---|---|
| **Mã nhóm** | `I-MVP01` |
| **Người kiểm thử** | Kiên |
| **Ngày chạy** | 05/10/2026 |
| **Phiên bản code** | `ead99af` (kèm các commit implementation W09) |
| **Môi trường** | Local Java 17 target / H2 test; Docker PostgreSQL 16 smoke |

## 1. Mục tiêu kiểm thử

Chứng minh request scan công khai không bị duplicate khi retry, không dispatch khi quota bị deny hoặc idempotency conflict, luôn route đúng worker contract, và không suy diễn `SAFE` từ thiếu dữ liệu reputation. Đồng thời chứng minh migration rule baseline chạy được từ PostgreSQL rỗng.

## 2. Chiến lược

| Tầng | Kiểm cái gì | Công cụ |
|---|---|---|
| Unit/contract | Command invariants, hash, access/quota, routing, entity, risk/threat mocks | JUnit 5 |
| Integration H2 | Active published rule loading | Spring Boot Test + H2 |
| Contract | URL event schema/semantic validation | Python `unittest` + `jsonschema` |
| Migration smoke | Flyway V1–V3 + Hibernate schema validation JSONB | Docker PostgreSQL 16 + Spring Boot |
| Security hygiene | Secret không bị commit | `scripts/scan-secrets.sh` |

## 3. Bảng ca kiểm thử

| # | Ca | Đầu vào | Kỳ vọng | Kết quả | Đạt |
|---|---|---|---|---|---|
| 1 | Entity type sai cho URL | URL command + `PHONE` | Reject trước route | `ScanContractModelTest` pass | ✅ |
| 2 | ENTITY thiếu entity type | ENTITY command + null | Reject | `ScanContractModelTest` pass | ✅ |
| 3 | Hash semantic | Payload cùng field khác thứ tự | Cùng SHA-256 | `IdempotencyContractTest` pass | ✅ |
| 4 | Retry cùng key/hash | Same actor/key/hash | Replay, bus chỉ 1 message | `ScanRoutingContractTest` pass | ✅ |
| 5 | Key reuse payload khác | Same actor/key, hash khác | Conflict, không publish | `IdempotencyContractTest` pass | ✅ |
| 6 | Quota denied | Quota decision deny | 429 semantic, bus rỗng | `ScanRoutingContractTest` pass | ✅ |
| 7 | 4 routing cases | URL/TEXT/ENTITY/QR | Đúng queue và routing key | `ScanRoutingContractTest` pass | ✅ |
| 8 | Guest/free history | Guest/free contexts | `NONE`/`PERSISTENT` | `ScanAccessGateTest` pass | ✅ |
| 9 | Entity + no data | PHONE/BANK + unknown indicator | Normalize đúng; `NO_DATA` không verdict | `EntityContractTest`, `ThreatQueryContractTest` pass | ✅ |
| 10 | Rule loader | Enabled/disabled rules | Chỉ active published enabled rule được load | `RuleRepositoryTest` pass | ✅ |
| 11 | Worker timestamp sai | `processedAt="yesterday"` | Reject contract | 11/11 Python tests pass | ✅ |
| 12 | DB từ rỗng | PostgreSQL 16 | Flyway V1–V3, Hibernate validate, API start | Smoke pass | ✅ |

## 4. Cách chạy lại

```bash
cd Scam-Risk-Detector/services/api
./mvnw test

cd ../..
python -m unittest discover -s contracts/tests -v
npx @redocly/cli lint contracts/openapi/anti-scam-api.yaml
bash scripts/scan-secrets.sh
```

Smoke PostgreSQL dùng biến môi trường local tạm thời, không commit `.env`:

```bash
POSTGRES_PASSWORD=<local> POSTGRES_HOST_PORT=55432 docker compose -f infra/docker-compose.yml up -d postgres
DB_URL=jdbc:postgresql://localhost:55432/antiscam DB_USER=antiscam DB_PASSWORD=<local> \
JWT_SECRET=<32+ chars> OTP_HASH_SECRET=<32+ chars> timeout 45s ./services/api/mvnw -f services/api/pom.xml spring-boot:run
```

## 5. Ca đạt một phần / rủi ro còn lại

| Ca | Trạng thái | Lý do | Xử lý tiếp |
|---|---|---|---|
| Redis adapter với Redis server thật | Chưa chạy integration test | W09 chỉ freeze seam; unit dùng in-memory adapter | I-MVP02 thêm integration test atomic Lua/TTL |
| HTTP controller end-to-end | Chưa có runtime controller W09 | W09 only freeze request/acceptance contract | I-MVP02 implement lifecycle/consumer |

## 6. Số liệu phân loại

Không áp dụng — I-MVP01 không chạy Rule Engine/Risk Fusion và không trả final verdict. Không có Precision/Recall/F1 để báo cáo trung thực.

## 7. Kết luận

| Câu hỏi | Trả lời |
|---|---|
| Số ca được liệt kê | 12 |
| Số ca đạt | 12 trong phạm vi W09 |
| Đủ điều kiện nghiệm thu I-MVP01? | ☑ Rồi, ở mức implementation slice/contract baseline |
| Rủi ro còn lại | Runtime RabbitMQ/Redis/lifecycle/risk fusion thuộc W10 |
| Kiến nghị | Review chéo OpenAPI/routing với owner URL/Text/QR trước merge |

*Người kiểm thử: Kiên · Ngày: 05/10/2026 · Mẫu: BCKT v1.0*
