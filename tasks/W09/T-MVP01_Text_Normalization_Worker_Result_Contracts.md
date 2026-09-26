# T-MVP01 — Text Normalization + Worker / Result Contracts

| Thuộc tính | Giá trị |
|---|---|
| Week | **W09 · 27/09–05/10/2026** |
| Owner | **Thắng** |
| Source WP | `T-WP01` + `T-WP07` + `T-WP12 (contract)` |
| Upstream | Team contract freeze |
| Downstream chính | `T-MVP02`, `I-MVP02`, `I-MVP04`, `T-MVP04` |
| Mục tiêu tuần | Có normalization/evasion core + privacy rules + Text Worker/RiskResult contract ổn định |

## Tài liệu ràng buộc

Đặc tả này là **implementation slice cho Timeline V2.1**, không thay đổi danh sách tính năng hay kiến trúc. Khi có xung đột, áp dụng thứ tự nguồn hiện tại của dự án:

1. `architecture_v3.3.md`
2. `modules_specification_v1.3.md`
3. `anti_scam_schema_overall_v1.0.md` *(Draft)*
4. `Anti_Scam_Feature_Backlog_V2`
5. `Timeline_full_Anti_Scam_2026_2027_V2.1.md`

> Một task `*-MVPxx` là **MVP implementation slice** lấy từ một hoặc nhiều WP. Hoàn thành task **không đồng nghĩa toàn bộ source WP đã hoàn thành** nếu các feature nâng cao/P1/P2 được ghi là deferred.


## 1. Mô tả

Task này xây phần **deterministic text preprocessing** và chốt giao diện giữa Text Worker ↔ RabbitMQ ↔ Spring Boot/M12/M09/M06. W09 chưa cần public async endpoint chạy thật; trọng tâm là normalization có test, privacy boundary rõ và contract để W10 triển khai `POST /v1/scans/text` mà không đổi message shape.

## 2. Traceability / phạm vi feature

| Feature | Mức trong task | Ghi chú |
|---|---|---|
| `M03-F001`–`M03-F003` | **Required** | lowercase/remove-diacritic/space + leetspeak + dual-normalization fact |
| `M03-F004`, `M03-F005` | Recommended | homoglyph/zero-width basic handling nếu kịp |
| `M03-F006`, `M03-F007` | Deferred | teencode/language depth |
| `M03-F044` | **Required** | không đọc SMS/notification/call log/contacts/clipboard ngầm |
| `M03-F046`–`M03-F048` | **Required contract** | contentType, DerivedIndicator/pendingAiTasks, CCCD sensitive signal |
| `M06-F020` | **Required contract** | canonical RiskResult |
| `M06-F012`, `M06-F013` | Basic fixture/UI contract | render mocked RiskResult nhất quán |
| `M06-F011`, `M06-F014` | Deferred | UX detail nâng cao |

## 3. Input / Output

### Input

```text
text
contentType = MESSAGE | TRANSACTION_POST
optional source metadata
optional parentScanId/depth
```

### Preprocessing output

```text
NormalizedText
- originalText (chỉ trong processing scope cần thiết)
- canonicalText
- comparison/evasion view
- normalization facts, ví dụ LEET_BRAND
```

### Text WorkerResult contract

```text
AnalysisSignal[]
DerivedIndicator[]
  - type = URL | PHONE | BANK_ACCOUNT
  - normalizedValue
  - handling = REPUTATION_ONLY | CHILD_SCAN
Scenario metadata
pendingAiTasks[]
processor/version + event correlation metadata
```

### Final UI contract fixture

```text
RiskResult
- riskScore
- riskLevel
- degraded
- evidence[]
- explanations[]
- recommendations[]
- policyVersion
```

## 4. Business rules / invariants

- `MESSAGE` và `TRANSACTION_POST` dùng cùng text endpoint/worker contract.
- Normalization không được phá dữ liệu cần cho extraction ở W11; nên giữ original + canonical view riêng.
- Leetspeak conversion áp dụng theo token có chữ cái; tránh biến mọi chuỗi số thành chữ.
- Dual-normalization phải giữ khả năng sinh fact/evidence về hành vi né bộ lọc thay vì chỉ “sửa mất dấu vết”.
- CCCD trong MVP chỉ sinh sensitive-data signal; không tạo standalone CCCD lookup.
- Không background-read SMS, notification, call log, contacts hay clipboard; không dùng Accessibility Service.
- Text Worker không gọi M02/M04/M08/M11/core trực tiếp.
- AI result không nằm trực tiếp trong worker result; chỉ `pendingAiTasks[]`.
- Final business verdict không thuộc Text Worker.

## 5. Logic flow

```text
text + contentType
→ validate input contract
→ preserve original view
→ canonicalize lowercase / Vietnamese diacritics / whitespace
→ normalize leetspeak safely
→ compare raw vs normalized to emit evasion facts
→ optional homoglyph/zero-width handling
→ build WorkerResult fixture
   ├─ signals[]
   ├─ derivedIndicators[] (có thể empty ở W09)
   └─ pendingAiTasks[] (fixture/empty)
→ UI render mocked RiskResult
```

W10 mới thêm async dispatch + scam pattern matching.

## 6. API / event contract cần freeze

```text
POST /v1/scans/text          // semantics contract, implementation W10
queue: q.scan.text
request key: scan.text.requested
result key:  scan.text.analyzed
```

Worker/core boundary chỉ qua RabbitMQ. `WorkerResult` cần đủ trace metadata theo M12 event envelope.

## 7. Privacy / security

- Raw sensitive text không log mặc định.
- CCCD-like data không persist/log như standalone identifier.
- Không background capture user data.
- Worker message chỉ mang dữ liệu cần thiết cho analysis.
- Mọi evidence hiển thị sau này phải dựa trên signal/rule có thật; UI không tự suy diễn thêm.

## 8. Phát triển độc lập / fixture strategy

- `FakeMessageBus`.
- `MockRiskEvaluationPort`.
- Text job/result fixtures.
- `RiskResult` fixtures: SAFE / CAUTION / DANGER / degraded.
- DerivedIndicator fixture chứa URL/PHONE/BANK để Kiên test orchestration dù extraction implementation chưa xong.

## 9. Test plan

### Normalization unit tests

- chữ hoa/thường;
- tiếng Việt có dấu → canonical form;
- multiple whitespace;
- `vi3tc0mb4nk`-style leetspeak;
- input số thuần không bị chuyển sai;
- dual-normalization sinh evasion fact;
- zero-width/homoglyph nếu implement trong W09.

### Contract tests

- MESSAGE và TRANSACTION_POST deserialize cùng schema.
- WorkerResult có `signals`, `derivedIndicators`, `pendingAiTasks`.
- DerivedIndicator handling hợp lệ.
- RiskResult fixture render được qua shared component.

## 10. Acceptance Criteria

- [ ] Normalization P0 M03-F001–F003 có deterministic unit tests.
- [ ] Normalization giữ original/canonical views để không phá extraction sau này.
- [ ] Privacy invariant: không background-read dữ liệu thiết bị.
- [ ] contentType chỉ dùng MESSAGE | TRANSACTION_POST trong MVP.
- [ ] Text WorkerResult contract có signals + derivedIndicators + pendingAiTasks.
- [ ] CCCD chỉ là sensitive-data signal, không standalone lookup.
- [ ] Text Worker không gọi trực tiếp worker/core/DB/cache khác.
- [ ] Canonical RiskResult contract được freeze và mock UI render được.
- [ ] Task chưa implement async endpoint/pattern engine của T-MVP02.

## 11. Deliverables

- Text normalization library/service + fixtures/tests.
- Text job/result contract + JSON fixtures.
- Privacy guard/documented invariants.
- Shared RiskResult fixture/component contract.
- Evidence: test report + small demo normalize/render fixture.

## 12. Handoff sang W10

`T-MVP02` dùng normalization + contract này để triển khai async Text Scan. `I-MVP02` dùng WorkerResult fixture để test result consumer/risk core độc lập.
