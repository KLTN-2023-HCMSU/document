# K-WP10 — Platform Runtime, Containers, Core Topology & Readiness

**Owner:** Khải  
**Module:** M13 Platform Infrastructure  
**Cycle:** W10 · 06/10/2026 → 12/10/2026  
**Feature IDs:** M13-F003, F004, F006, F008, F030, F031, F032, F033, F034  
**Release target:** MVP v0.1.0

## 1. Outcome cần đạt

Một developer mới có thể chạy platform baseline bằng quy trình reproducible; core dependencies có health/readiness và đúng network/data ownership theo Architecture V3.3.

## 2. Scope / Non-scope

**IN:** Dockerfiles, local compose, PostgreSQL/Redis/RabbitMQ/MinIO/Nginx baseline, queue topology, health/readiness, network segmentation/egress rules ở mức dev/test.  
**OUT:** business schema ownership, CI (K-WP11), CD/release automation (K-WP12), production monitoring (K-WP13).

## 3. Input

- service inventory hiện tại;
- Architecture V3.3 queue/data/network boundaries;
- env/secret contract từ K-WP09.

## 4. Output

```text
docker compose local/dev
Dockerfile multi-stage cho service cần build
health/readiness endpoints/config
RabbitMQ exchange/queues/DLX bootstrap
MinIO buckets/config baseline
network definitions / egress rules theo môi trường
README: one-command local start
```

## 5. Runtime flow

```text
docker compose up
→ PostgreSQL ready
→ Redis ready
→ RabbitMQ + topology ready
→ MinIO ready
→ Spring Boot / workers start
→ health/readiness pass
→ Nginx chỉ expose public edge cần thiết
→ internal worker network không route DB/core ngoài boundary
```

## 6. Invariants

1. PostgreSQL là source of truth; Redis không giữ canonical business state duy nhất.
2. RabbitMQ là transport, không là business DB.
3. Scanner/AI worker không truy cập PostgreSQL/Redis/Spring Boot API trực tiếp.
4. URL worker chỉ có outbound public fetch theo SSRF-safe policy.
5. AI/notification worker chỉ egress provider được config.
6. Internal DB/Rabbit/Redis/MinIO ports không public Internet trong deploy topology.
7. Readiness chỉ xanh khi dependency bắt buộc cho service đã usable.

## 7. RabbitMQ baseline

```text
exchange: antiscan.topic
DLX:      antiscan.dlx
queues:
  q.scan.url
  q.scan.text
  q.scan.entity
  q.scan.qr
  q.ai.analyze
  q.scan.result
  q.report.export
  q.notification
  q.threat.ingest
```

Routing key semantics bám contract freeze; không tự đổi trong M13.

## 8. Failure behavior

| Case | Expected |
|---|---|
| PostgreSQL chưa ready | dependent service readiness DOWN |
| RabbitMQ down | scan acceptance không giả success nếu publish bắt buộc fail |
| Redis down | service degrade/fallback theo module policy, không mất canonical data |
| MinIO down | artifact/export path báo degraded/fail rõ |
| worker thử connect DB/core | network/config test phải phát hiện/chặn |

## 9. Test matrix

- fresh clone + env template + compose up;
- health/readiness của dependency/service;
- queue/DLX tồn tại đúng tên;
- restart Redis không mất canonical DB state;
- worker network không connect DB/core route bị cấm;
- MinIO upload/download smoke nếu scope đã dùng.

## 10. Acceptance criteria

- [ ] Local stack reproducible.
- [ ] Dockerfiles multi-stage cho service triển khai.
- [ ] Core topology đúng Architecture V3.3.
- [ ] Health/readiness rõ ràng.
- [ ] Queue/DLX bootstrap đúng contract.
- [ ] Network boundary cơ bản được test.
- [ ] README onboarding đủ để member khác chạy.
- [ ] Evidence dán tracker.

## 11. Evidence

```text
docker compose config
+ docker compose up log
+ health/readiness curl
+ RabbitMQ topology screenshot/export
+ network smoke tests
```
