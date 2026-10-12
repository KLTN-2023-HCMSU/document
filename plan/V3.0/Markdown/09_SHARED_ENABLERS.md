# Shared Technical Enablers — Không nhân đôi task

Các enabler là các **gói năng lực kỹ thuật** để 32 flow độc lập trong lúc phát triển, nhưng tích hợp theo thiết kế đã duyệt.

| ID | Enabler | Owner | SP | Weeks | Scope | Supports |
| --- | --- | --- | --- | --- | --- | --- |
| E-CORE | Scan Orchestrator + Risk Core | Kiên | 13 | W10–W12 | Scan lifecycle / outbox / event dedupe / risk composer / completion barrier; tests with fake/real bus | S01,S02,S03,S04,S05,R01 |
| E-RUNTIME | Runtime / CI / Staging / Security | Khải | 17 | W10–W13 | PostgreSQL/Redis/RabbitMQ/MinIO, migrations, readiness, deploy rollback, SSRF/secret gates | ALL |
| E-AI | Async AI Worker | Thắng | 8 | W11–W12 | AMQP AiTask → AiPrediction, model/provider adapter, timeout/failure fixtures; no direct core DB | S02,S05 |
| E-IDENTITY | Identity Delivery + Community/Audit Core | Hùng | 14 | W10–W12 | Reuse W09 Auth/OTP contracts; session/API integration, report/moderation data, notification/audit backend | A01–A08,C01,C03,N01,O01 |


## Contract boundary & development isolation

| Contract seam | Producer/Owner | Consumers | Fixture / Acceptance |
| --- | --- | --- | --- |
| Auth Session + AccessContext | Hùng | Kiên/Khải/Thắng | Seeded guest/user/admin, expired+rotated token fixtures, 401/403 tests |
| HTTP Scan + OpenAPI | Kiên | Khải/Thắng | URL/TEXT/ENTITY/QR DTO+202/409/429 fixtures; generated type CI |
| ScanJob / WorkerResult | Kiên + Khải + Thắng | URL/Text/QR/Risk | AMQP schema + fixture; exactly one logical dispatch; real queue roundtrip |
| RiskResult | Kiên | Thắng/Khải | safe/risky/no_data/degraded deterministic fixtures |
| ThreatQuery/Entity | Kiên | Hùng/Khải | PHONE/BANK entity and verified observation fixtures |
| NotificationEvent/AuditEvent | Hùng | Khải/Thắng | Sandbox delivery and searchable correlation fixture |
| AiTask/AiPrediction | Thắng | Kiên | AMQP consumer and AI timeout/fail/success fixtures |


## Ownership rule

- **Flow Captain** tích hợp và demo; **Module Owner** chịu trách nhiệm contract và implementation trong module của mình.
- Nếu flow owner khác module owner: tạo interface/mocked adapter và link một shared delivery task, không copy implementation sang module khác.
- Enabler chỉ có một issue/capability ID; một flow có thể link nhiều enabler mà không cộng SP enabler nhiều lần.
- S01 URL và A01 Local Register là hai luồng cần chạy đầu tiên; thiếu queue/staging là blocker chung của toàn nhóm.

## Rủi ro chưa giải quyết từ code snapshot

- Next.js: trang `/` còn starter; `/demo/*` là mock UI, không phải production user flows.
- Expo Mobile: chỉ có README placeholder.
- `infra/docker-compose.yml`: còn dấu vết thiết kế cũ, thiếu RabbitMQ/MinIO; phải so lại V3.3.
- `services/scan-engine` là legacy FastAPI scoring, **không phải AI AMQP V3.3 worker**.
- Scan Intake service contract tồn tại; chưa thấy Scan HTTP Controller/lifecycle persistence hoàn chỉnh trong source snapshot.
- Mọi nhận định trên là **static inspection**, không phải CI run mới nhất trên main.
