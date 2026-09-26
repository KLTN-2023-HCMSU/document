# T-WP02 — Async Text Scan, Scam Patterns & User Result UX

**Owner:** Thắng  
**Module:** M03 Text & Transaction Scam Analysis  
**Cycle:** W10 · 06/10/2026 → 12/10/2026  
**Feature IDs:** M03-F008…F015  
**Release target:** MVP v0.1.0


> **Nguồn chuẩn:** Architecture V3.3 · Modules Specification V1.3 · Schema V1.0 Draft · Feature Backlog V2.  
> Nếu tài liệu task này mâu thuẫn với các tài liệu chuẩn trên, **tài liệu chuẩn thắng**. Các mục ghi “Working decision” là quyết định triển khai tạm thời cho sprint và phải được review nếu muốn đưa vào contract chuẩn.


## 1. Outcome cần đạt

User gửi `MESSAGE` hoặc `TRANSACTION_POST` qua `POST /v1/scans/text`, nhận `202 + scanId`, Text Worker dùng output T-WP01 để phát hiện scam cues/patterns và trả **signals/indicators**, còn UI render được processing + mock/final `RiskResult` mà không để worker tự chấm verdict.

## 2. Scope / Non-scope

**IN:** async text endpoint semantics, 20k input validation, 6 keyword groups, 5 scenario patterns, worker result contract, web result UX/highlight, optional source metadata.  
**OUT:** full entity extraction (T-WP03), taxonomy admin (T-WP04), OCR `.eml` P2 nếu không đủ thời gian MVP, AI real provider.

## 3. Input

```json
{
  "contentType": "MESSAGE | TRANSACTION_POST",
  "text": "..."
}
```

Initial max text length: **20.000 ký tự**.

Optional UX/source metadata có thể gồm SMS/Zalo/Messenger/Email/Facebook, nhưng không được thay đổi canonical `contentType` enum nếu contract chưa freeze.

## 4. API output

Accepted:

```text
202 + scanId
status=PENDING/PROCESSING
```

Client poll/read qua shared scan result contract.

Final user output thuộc M12/M09/M06:

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

Text Worker **không** tạo fields verdict trên.

## 5. Keyword groups cần hỗ trợ

P0 hiện có 6 nhóm:

```text
1. Khẩn cấp / urgency
2. Giả danh / impersonation
3. Yêu cầu thông tin nhạy cảm
4. Yêu cầu chuyển tiền / thanh toán
5. Quá tốt để tin / reward-prize
6. Mời việc làm / CTV đáng ngờ
```

## 6. Scenario patterns cần hỗ trợ

```text
- giả mạo ngân hàng
- giả danh cơ quan/tổ chức
- trúng thưởng
- tuyển cộng tác viên / việc làm
- shipper/giao hàng giả
```

Pattern output là signal/scenario metadata, không phải final `DANGER` hard-code.

## 7. Business flow

```text
User paste text
→ POST /v1/scans/text
→ M12 validates/access/quota/idempotency
→ publish scan.text.requested
→ 202 + scanId

Text Worker:
→ T-WP01 normalizeText()
→ detect keyword groups
→ detect scenario patterns
→ (T-WP03 sau này) extract URL/PHONE/BANK indicators
→ optional publish AI task nếu enabled (có thể mock trong cycle)
→ return AnalysisSignal[] + DerivedIndicator[] + pendingAiTasks[]

Core:
→ M12 aggregate/barrier
→ M09 Risk Evaluation
→ final RiskResult
→ UI render evidence/explanation
```

## 8. Worker output working shape

```json
{
  "signals": [
    {
      "code": "URGENCY_CUE",
      "severity": "MEDIUM",
      "value": 1,
      "source": "TEXT_RULE"
    }
  ],
  "derivedIndicators": [],
  "pendingAiTasks": [],
  "processorVersion": "text-worker-v1"
}
```

Worker không gọi M02/M04/M08/M11 trực tiếp và không đọc DB/Redis/core API.

## 9. UX behavior

Web/Mobile semantics giống nhau:

```text
submit
→ processing
→ result
→ show score/level from RiskResult
→ evidence groups
→ highlight suspicious text segments nếu mapping khả dụng
→ recommendation
```

Trong cycle này UI có thể render từ mock `RiskResult` trước khi M09 thật hoàn tất.

## 10. Failure/degraded behavior

- malformed/oversized text → fail fast.
- worker fail sau retry → M12 failure/degraded policy.
- AI chưa có → không block task; use mock/no-AI path.
- partial indicator extraction vẫn có thể trả signals.
- late AI không mutate final result (M12/M11 concern).

## 11. Privacy

- CCCD-like cue chỉ là sensitive-data request signal; standalone CCCD lookup OUT.
- raw sensitive value phải mask/redact trong log/evidence.
- Text Worker không persist raw input trực tiếp.

## 12. Independence / mocks

```text
T-WP01 TextNormalizer
FakeMessageBus
MockRiskEvaluationPort
text worker result fixtures
RiskResult SAFE/CAUTION/DANGER fixtures
```

Có thể hoàn thiện UI + worker rules mà không chờ M09/M11 thật.

## 13. Test matrix

- MESSAGE và TRANSACTION_POST cùng endpoint.
- >20k rejected.
- mỗi keyword group có positive + negative fixture.
- 5 scenario patterns có fixture.
- normalized evasion case từ T-WP01 vẫn detect.
- worker output không có riskScore/riskLevel.
- UI processing→result bằng mock lifecycle.
- evidence highlight không làm sai raw text.
- worker no DB/cache/core API dependency.

## 14. Acceptance criteria

- [ ] `POST /v1/scans/text` async, `202 + scanId`.
- [ ] MESSAGE/TRANSACTION_POST đúng contract.
- [ ] 20k validation.
- [ ] 6 keyword groups có deterministic tests.
- [ ] 5 scenario patterns có deterministic tests.
- [ ] Text Worker dùng T-WP01 normalization.
- [ ] chỉ trả signals/indicators/pendingAiTasks.
- [ ] UI render được mock RiskResult + processing state.
- [ ] privacy masking cho sensitive cue.
- [ ] FakeMessageBus/MockRiskEvaluationPort tests pass.
- [ ] PR/test/demo evidence dán tracker.
