# T-MVP02 — Async Text Scan + Result / History / Basic Export

| Thuộc tính | Giá trị |
|---|---|
| Week | **W10 · 06/10–12/10/2026** |
| Owner | **Thắng** |
| Source WP | `T-WP02` + `T-WP10 (basic)` + `T-WP12` + `T-WP14 (basic)` |
| Upstream | `T-MVP01` + `I-MVP01` RiskResult contract |
| Downstream chính | Entity extraction/QR W11, nested/AI W12 |
| Mục tiêu tuần | Text scan async chạy được tới RiskResult UI + history basic + export happy path |

## Tài liệu ràng buộc

Đặc tả này là **implementation slice cho Timeline V2.1**, không thay đổi danh sách tính năng hay kiến trúc. Khi có xung đột, áp dụng thứ tự nguồn hiện tại của dự án:

1. `architecture_v3.3.md`
2. `modules_specification_v1.3.md`
3. `anti_scam_schema_overall_v1.0.md` *(Draft)*
4. `Anti_Scam_Feature_Backlog_V2`
5. `Timeline_full_Anti_Scam_2026_2027_V2.1.md`

> Một task `*-MVPxx` là **MVP implementation slice** lấy từ một hoặc nhiều WP. Hoàn thành task **không đồng nghĩa toàn bộ source WP đã hoàn thành** nếu các feature nâng cao/P1/P2 được ghi là deferred.


## 1. Mô tả

Task này tạo **text vertical slice** đầu tiên của sản phẩm. User paste `MESSAGE` hoặc `TRANSACTION_POST`, backend tạo async scan, Text Worker dùng normalization W09 + basic scam patterns, core trả `RiskResult`, M06 hiển thị cùng result component và authenticated user xem lại trong history. Export chỉ cần một happy-path cơ bản đúng architecture; styling/format/reporting nâng cao để post-MVP.

Entity extraction đầy đủ nằm W11; AI async nằm W12.

## 2. Traceability / phạm vi feature

| Feature | Mức trong task | Ghi chú |
|---|---|---|
| `M03-F008`–`M03-F011` | **Required** | async endpoint, keyword groups, pattern combos, text result UI |
| `M03-F012`, `F013` | Deferred/depth | source weighting, rich highlighting |
| `M03-F014`, `F015` | Deferred | OCR/.eml |
| `M06-F003`–`M06-F005` | **Required basic** | authenticated history/detail/cursor pagination |
| `M06-F006` | Deferred | filters nâng cao |
| `M06-F012`, `M06-F013`, `M06-F020` | **Required** | shared result component + canonical RiskResult |
| `M06-F011`, `M06-F014` | Deferred | compact/expandable UX |
| `M06-F016`, `M06-F021` | Basic happy path | async PDF/HTML/export contract; polish sau MVP |
| `M06-F019` | Deferred | admin reporting |

## 3. Input / Output

### Scan input

```json
{
  "contentType": "MESSAGE",
  "text": "..."
}
```

`contentType` cũng có thể là `TRANSACTION_POST`.

### Scan API output

```text
202 + scanId
```

Sau đó:

```text
GET /v1/scans/{scanId}
→ PROCESSING | COMPLETED | FAILED
→ khi complete: RiskResult
```

### History output

```text
HistoryPage
- scan summaries
- cursor / next cursor
```

### Export output

```text
ExportJobStatus
→ artifact/object reference qua backend policy
```

## 4. Business rules / invariants

- Text length MVP tối đa 20,000 chars theo backlog.
- `MESSAGE` và `TRANSACTION_POST` cùng endpoint.
- Basic keyword groups: urgency, impersonation, sensitive info, money transfer, too-good-to-be-true, job offer.
- Basic combo scenarios: fake bank/authority, prize, CTV recruitment, fake shipper.
- Text Worker trả signals; final verdict thuộc M09/M12.
- Một `RiskResult` contract dùng cho mọi scan type.
- Guest có thể xem result của chính scan trong guest access scope nhưng **không** có persistent account history.
- Authenticated user history chỉ trả scan thuộc user có quyền.
- Biết `scanId` không đủ để authorize result.
- Export dùng persisted final result; không recompute risk.
- Export worker fail không làm core scan fail.

## 5. Logic flow

```text
POST /v1/scans/text
→ validate contentType + text size
→ M12 create ScanRequest
→ publish scan.text.requested
→ Text Worker
   → T-MVP01 normalization
   → keyword groups
   → combination/scenario rules
   → publish scan.text.analyzed(signals, derivedIndicators?, pendingAiTasks?)
→ I-MVP02 consume/aggregate/risk-evaluate
→ persist final RiskResult
→ M06 GET detail/render shared RiskResultCard
→ authenticated history list/detail
```

Export basic:

```text
final persisted RiskResult
→ request export
→ q.report.export / report.export.requested
→ Export Worker render HTML/PDF basic
→ store artifact MinIO/S3
→ backend returns status/download reference
```

Nếu Export Worker chưa hoàn chỉnh trong đầu W10, dùng contract-compatible stub worker để integration; không thay đổi queue/storage contract.

## 6. API / event contracts

### Text

```text
POST /v1/scans/text
queue: q.scan.text
request key: scan.text.requested
result key:  scan.text.analyzed
```

### Result / history

```text
GET /v1/scans/{scanId}
GET /v1/scans/history
```

### Export

```text
POST /v1/scans/{scanId}/exports   // nếu expose riêng theo implementation
GET  /v1/exports/{exportId}
queue: q.report.export
key: report.export.requested
```

## 7. UI expectations

Shared result component hiển thị tối thiểu:

```text
riskScore
riskLevel + text/icon (không chỉ màu)
degraded state
evidence[]
explanations[]
recommendations[]
policyVersion
```

Text page có input, processing state và result; không cần rich highlight từng đoạn trong W10.

## 8. Failure / security cases

- text > limit → validation error, không dispatch.
- queue publish fail → không giả accepted.
- worker fail/retry → M12 lifecycle phản ánh đúng.
- Guest dùng scanId của người khác → deny.
- Authenticated user history không lộ scan user khác.
- export worker/MinIO fail → scan result vẫn xem bình thường.
- result `degraded=true` phải hiển thị rõ.
- sensitive raw text không log tùy tiện.

## 9. Phát triển độc lập / mock strategy

- `FakeMessageBus` + Text WorkerResult fixtures.
- `MockRiskEvaluationPort` trong đầu tuần; switch I-MVP02 cuối tuần.
- `RiskResult` fixtures cho UI/history.
- Export worker có thể dùng stub contract-compatible happy path trước khi full renderer.

## 10. Test plan

### Text scan

- MESSAGE / TRANSACTION_POST happy path.
- input length boundary.
- 6 keyword groups có fixture.
- combination patterns có fixture benign + scam.
- async 202→PROCESSING→COMPLETED flow.

### Result/history auth

- Guest xem scan của chính guest scope.
- Guest không có `/history` persistent account history.
- User chỉ thấy own history.
- cursor pagination.
- unauthorized scanId rejected.

### Export

- completed result → export request → artifact/status.
- export failure không mutate RiskResult.

## 11. Acceptance Criteria

- [ ] POST /v1/scans/text nhận MESSAGE/TRANSACTION_POST và trả 202 + scanId trên async path.
- [ ] Text input limit/validation hoạt động.
- [ ] Basic keyword groups + scam-combination patterns có test benign/scam.
- [ ] Text Worker sử dụng normalization W09 và publish đúng WorkerResult contract.
- [ ] Result page dùng canonical RiskResultCard chung, hiển thị score/level/evidence/recommendation/degraded.
- [ ] GET /v1/scans/{{scanId}} enforce ownership; scanId không phải auth token.
- [ ] Authenticated history + cursor pagination hoạt động; Guest không có persistent history.
- [ ] Một basic async export happy path theo q.report.export/MinIO contract chạy được hoặc contract-compatible stub được chứng minh.
- [ ] Export fail không ảnh hưởng persisted RiskResult.
- [ ] Text submit→worker→risk core→result UI integration smoke xanh.

## 12. Deliverables

- Text scan endpoint/worker basic patterns.
- Shared result UI/component integration.
- Result detail + authenticated history basic.
- Export happy-path contract/worker/stub + object storage integration.
- Tests + fixtures + demo evidence.

## 13. Handoff sang W11

`T-MVP03` thêm entity extraction URL/Phone/Bank và QR/VietQR basic. Các `DerivedIndicator[]` sẽ đi vào nested/reputation flow ở Kiên W11/W12.
