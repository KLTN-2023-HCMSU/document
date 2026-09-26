# K-WP01 — URL Intake, Normalization, Cache & Basic Signals

**Owner:** Khải  
**Module:** M02 URL & Website Risk Scan  
**Cycle:** W10 · 06/10/2026 → 12/10/2026  
**Feature IDs:** M02-F001…F008  
**Release target:** MVP v0.1.0


> **Nguồn chuẩn:** Architecture V3.3 · Modules Specification V1.3 · Schema V1.0 Draft · Feature Backlog V2.  
> Nếu tài liệu task này mâu thuẫn với các tài liệu chuẩn trên, **tài liệu chuẩn thắng**. Các mục ghi “Working decision” là quyết định triển khai tạm thời cho sprint và phải được review nếu muốn đưa vào contract chuẩn.


## 1. Outcome cần đạt

User/consumer gửi URL qua `POST /v1/scans/url`. Hệ thống normalize + validate input, tính normalized-input hash/cache key, tạo scan job khi cache miss, và URL Worker có thể tạo các **basic lexical signals** mà không chạm DB/core API.

## 2. Scope / Non-scope

**IN:** intake contract, URL normalization, URL parts, result-cache lookup semantics, basic lexical signs, optional short-link identification, URL result fixture/UI contract.  
**OUT:** network fetch/SSRF (K-WP02), redirect analysis (K-WP03), TLS (K-WP04), HTML/Form (K-WP05), final Risk Fusion (M09).

## 3. Input

Public request:

```json
{
  "url": "https://example.com/path?a=1",
  "source": "MANUAL"
}
```

`source`: `MANUAL | MOBILE_SHARE | NESTED_SCAN` theo module contract.

Internal worker job cần tối thiểu:

```text
scanId / jobId / eventId / correlationId
scanType=URL
normalized URL
optional parentScanId/depth
optional reputationContext (core pre-enrich)
non-sensitive execution metadata
```

## 4. Output

### API

Cache miss:

```text
202 Accepted
{ scanId, status=PENDING|PROCESSING }
```

Cache hit: có thể trả cached `RiskResult` theo contract hiện hành.

### Worker result ở scope WP này

```text
AnalysisSignal[] (lexical/basic URL)
DerivedIndicator[] nếu phát hiện indicator mới phù hợp
pendingAiTasks[] = [] trong WP này nếu chưa nối AI
processor metadata
```

Không trả `riskScore`/`riskLevel` từ worker.

## 5. Normalization contract

Bắt buộc hỗ trợ:

```text
- bổ sung scheme khi input thiếu scheme
- lowercase host
- bỏ default port
- canonicalize/sắp xếp query theo policy hiện hành
- tách scheme/subdomain/domain/TLD/path/query/fragment
```

**Working decision cần team freeze trước code:** tài liệu hiện tại chưa nói scheme mặc định khi user nhập `example.com`. Khuyến nghị dùng `https` làm default UX, nhưng phải ghi rõ trong contract/test trước khi merge.

Output nội bộ đề xuất:

```text
NormalizedUrl
- originalInput
- normalizedUrl
- scheme
- host
- subdomain?
- registrableDomain?
- tld?
- port?
- path
- normalizedQuery
- fragment?
- inputHash
```

## 6. Business flow

```text
User nhập URL
→ M12 validate access/quota/idempotency
→ normalize URL
→ tạo normalized-input hash
→ shared result cache lookup
   ├─ HIT  → trả RiskResult cached theo policy
   └─ MISS → create ScanRequest
            → pre-enrich reputation ở core nếu có
            → publish scan.url.requested
            → 202 + scanId
```

URL Worker ở WP này:

```text
receive job
→ validate normalized URL contract
→ extract parts
→ lexical/basic analysis
→ return signals to q.scan.result
```

## 7. Basic signals tối thiểu

- host là IP thay vì domain;
- URL quá dài theo configurable threshold;
- ký tự bất thường/encoding đáng ngờ;
- `@` trong URL/authority theo rule;
- short-link service identification (P1).

Signal phải explainable, ví dụ:

```json
{
  "code": "URL_USES_IP_HOST",
  "severity": "MEDIUM",
  "value": 1,
  "source": "URL_LEXICAL"
}
```

Tên code cụ thể cần freeze trong fixture, nhưng semantics không đổi.

## 8. Cache rules

- Result cache TTL mặc định **10 phút**, cấu hình được.
- PostgreSQL vẫn là source of truth cho canonical scan result.
- Cache key dựa trên normalized-input hash + version/policy nếu cần để tránh stale semantics.
- WP này không tự sở hữu Redis; đi qua shared cache/port của M12/M13.

## 9. Worker isolation

URL Worker:

```text
MUST NOT: đọc PostgreSQL / Redis / gọi Spring Boot API
MAY: RabbitMQ
K-WP02 trở đi MAY outbound public Internet qua safe-fetch SSRF policy
```

WP này có thể chạy hoàn toàn bằng fixture, chưa cần fetch Internet.

## 10. Failure behavior

| Case | Expected |
|---|---|
| URL malformed | fail fast 4xx, không publish job |
| scheme không hỗ trợ | reject; chỉ http/https ở fetch stage |
| cache unavailable | fallback scan path theo platform policy; không coi là safe |
| duplicate idempotency request | M12 xử lý; worker không tự duplicate state |
| worker malformed result | M12 reject/DLQ theo contract |

## 11. Mock / dependency boundary

```text
FakeMessageBus
MockThreatQuery / fake reputationContext
RiskResult fixtures
url-safe.json
url-phishing.json
```

K-WP01 không chờ M08/M09/M11 thật.

## 12. Test matrix

- 15+ URL variants normalize deterministic.
- host lowercase/default port/query canonicalization.
- same semantic normalized URL → same input hash.
- cache hit/miss behavior.
- IP-host signal.
- `@`/length/abnormal-character signals.
- worker result không có final verdict.
- worker test pass không có DB/Redis/core API dependency.

## 13. Acceptance criteria

- [ ] `POST /v1/scans/url` contract đúng.
- [ ] normalization deterministic + unit test.
- [ ] URL parts extraction đúng.
- [ ] result-cache TTL mặc định 10 phút/configurable.
- [ ] basic lexical signals có evidence/code rõ.
- [ ] cache miss publish `scan.url.requested` và trả `202 + scanId`.
- [ ] worker chỉ trả signal/indicator, không verdict.
- [ ] có fixtures để M12/UI dev độc lập.
- [ ] PR/test/demo evidence dán tracker.
