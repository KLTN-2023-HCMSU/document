# K-MVP02 — Platform Runtime + URL Basic Vertical Slice

| Thuộc tính | Giá trị |
|---|---|
| Week | **W10 · 06/10–12/10/2026** |
| Owner | **Khải** |
| Source WP | `K-WP10` + `K-WP01` |
| Upstream | `K-MVP01` + `I-MVP01` contracts |
| Downstream chính | `K-MVP03`, `I-MVP02`, AI/QR integration |
| Mục tiêu tuần | `docker compose up` + URL intake/normalize/cache/basic signals chạy thành vertical slice |

## Tài liệu ràng buộc

Đặc tả này là **implementation slice cho Timeline V2.1**, không thay đổi danh sách tính năng hay kiến trúc. Khi có xung đột, áp dụng thứ tự nguồn hiện tại của dự án:

1. `architecture_v3.3.md`
2. `modules_specification_v1.3.md`
3. `anti_scam_schema_overall_v1.0.md` *(Draft)*
4. `Anti_Scam_Feature_Backlog_V2`
5. `Timeline_full_Anti_Scam_2026_2027_V2.1.md`

> Một task `*-MVPxx` là **MVP implementation slice** lấy từ một hoặc nhiều WP. Hoàn thành task **không đồng nghĩa toàn bộ source WP đã hoàn thành** nếu các feature nâng cao/P1/P2 được ghi là deferred.


## 1. Mô tả

Task này đưa foundation W09 lên **runtime thật** và tạo vertical slice đầu tiên cho URL. Local/dev stack phải có PostgreSQL, Redis, RabbitMQ, MinIO, Spring Boot, scanner workers, AI/notification/export/ingestion worker placeholders theo topology hiện hành và health/readiness đủ dùng. URL basic flow triển khai `POST /v1/scans/url`, normalization, cache và lexical/basic signals qua contract đã freeze.

SSRF-safe fetch đầy đủ nằm ở `K-MVP03` W11; W10 **không được** fetch tùy ý public URL nếu SSRF guard chưa sẵn sàng.

## 2. Traceability / phạm vi feature

| Feature | Mức trong task | Ghi chú |
|---|---|---|
| `M13-F003`, `F004`, `F006` | **Required** | compose, Dockerfile, health/readiness |
| `M13-F030`–`M13-F034` | **Required** | RabbitMQ topology, data boundaries, Nginx/business quota separation, MinIO, network isolation |
| `M13-F008` | Required runtime assumption | RabbitMQ + MinIO có ngay trong local stack |
| `M02-F001`–`M02-F005` | **Required** | URL endpoint, normalize/parts/cache/basic signals |
| `M02-F006`, `F007` | Deferred | shortener/result-specific UX depth |
| `M02-F008` | Deferred | batch scan |

## 3. Input / Output

### Input

```json
{
  "url": "https://example.com",
  "source": "MANUAL"
}
```

Runtime input:

```text
environment config
Docker images/services
RabbitMQ topology
PostgreSQL/Redis/MinIO connections
```

### Output

Cache hit:

```text
200 + cached RiskResult   // nếu policy cho phép
```

Cache miss:

```text
202 + scanId
scan.url.requested job
URL Worker basic AnalysisSignal[]
```

W10 result integration có thể dùng I-MVP02 real core hoặc fixture đúng contract.

## 4. Business rules / invariants

- PostgreSQL là source of truth; Redis là cache/coordination.
- Worker không có route tới PostgreSQL/Redis/Spring Boot API.
- URL Worker chỉ nói chuyện RabbitMQ; outbound public fetch chỉ được bật sau/qua SSRF-safe policy.
- Cache key dựa normalized-input hash; TTL default của backlog là 10 phút và configurable.
- URL normalization: thêm scheme nếu policy cho phép, lowercase host, bỏ default port, canonicalize/sort query theo contract.
- Basic signal gồm ít nhất: IP thay domain, URL quá dài, ký tự bất thường, `@` trong host.
- Final verdict thuộc M09/M12, không thuộc URL Worker.

## 5. Runtime topology

```text
Nginx
  ↓
Spring Boot
  ├─ PostgreSQL
  ├─ Redis
  ├─ RabbitMQ
  └─ MinIO

RabbitMQ
  ├─ URL Worker
  ├─ Text Worker
  ├─ Entity Worker
  ├─ QR Worker
  ├─ AI Worker
  ├─ Notification Worker
  ├─ Export Worker
  └─ Threat Ingestion Worker
```

Queues cần tồn tại theo architecture:

```text
q.scan.url
q.scan.text
q.scan.entity
q.scan.qr
q.ai.analyze
q.scan.result
q.report.export
q.notification
q.threat.ingest
+ DLQ tương ứng
```

## 6. URL logic flow

```text
POST /v1/scans/url
→ validate URL input
→ normalize URL
→ compute normalized-input hash
→ check result cache
├─ hit  -> return cached RiskResult
└─ miss -> create/dispatch ScanRequest through M12 contract
          → publish scan.url.requested
          → URL worker extract parts + lexical/basic signals
          → publish scan.url.analyzed / WorkerResult
          → M12/M09 integration or fixture
          → GET /v1/scans/{scanId}
```

## 7. Health / readiness

Phải phân biệt:

```text
health = process sống
ready  = dependency thiết yếu đủ để nhận workload
```

Spring Boot và worker không nên báo ready nếu dependency bắt buộc cho operation của nó chưa sẵn sàng. Diagnostics nội bộ không public Internet.

## 8. Failure / security cases

- RabbitMQ unavailable → API không được giả `202 accepted` nếu job chưa publish an toàn.
- Redis down → cache miss/fallback; không mất canonical data.
- PostgreSQL down → core not ready cho stateful flow.
- Worker cố truy cập DB/core → network policy phải chặn hoặc test chứng minh không cần route.
- URL malformed/unsupported scheme → reject trước dispatch.
- Không làm full network fetch trước khi K-MVP03 SSRF guard có hiệu lực.

## 9. Phát triển độc lập / mock strategy

- URL Worker dùng `FakeMessageBus` trong unit test.
- `MockThreatQuery` / reputation fixture từ K-MVP01 boundary.
- `MockRiskEvaluationPort` hoặc I-MVP02 fixture để UI/endpoint flow chạy trong đầu tuần.
- Cuối W10 swap sang real I-MVP02 tại integration checkpoint nếu sẵn sàng.

## 10. Test plan

### Runtime

- `docker compose up` sạch từ máy mới/config mẫu.
- health/readiness kiểm tra dependency chính.
- RabbitMQ exchanges/queues/DLQ tồn tại.
- network boundary: scanner worker không connect DB/Redis/core.
- MinIO create/read test artifact tối thiểu.

### URL

- normalize scheme/host/default port/query.
- extract scheme/subdomain/domain/TLD/path/query/fragment.
- cache hit/miss.
- lexical signal fixtures: IP host, long URL, unusual chars, `@` host/path case theo rule.
- invalid scheme/input rejected.

## 11. Acceptance Criteria

- [ ] `docker compose up` dựng được runtime topology cần cho MVP.
- [ ] Health/readiness của core/workers/dependencies đủ để debug và gate integration.
- [ ] RabbitMQ topology + DLQ cơ bản đúng architecture.
- [ ] PostgreSQL/Redis/MinIO roles đúng boundary; Redis không là source of truth.
- [ ] Scanner/AI worker không có direct route tới PostgreSQL/Redis/core API.
- [ ] POST /v1/scans/url xử lý cache hit/miss đúng contract.
- [ ] URL normalization + extraction + basic lexical signals có unit tests.
- [ ] Cache key dựa normalized input; TTL configurable.
- [ ] Cache miss có thể đi tới worker result/RiskResult path bằng real core hoặc contract fixture.
- [ ] Không triển khai unsafe fetch trước K-MVP03 SSRF guard.

## 12. Deliverables

- Local/dev compose + Dockerfiles + topology config.
- Health/readiness endpoints/config.
- URL intake/normalization/cache/basic analyzer.
- URL fixtures/tests.
- Integration smoke script/documentation.

## 13. Handoff sang W11

`K-MVP03` bổ sung SSRF-safe fetch, redirect baseline và CD/migration/release automation trên runtime này.
