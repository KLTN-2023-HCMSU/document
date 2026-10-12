# W11 — Thắng: Result Integration, Text Scan & AI Worker

**Owner:** Thắng  
**Reviewer:** Kiên  
**Primary goal:** R02 dùng API thật, S02 có pipeline tích hợp  
**Priority:** P0/P1

## R02 — Xem kết quả scan chi tiết

### User Journey
Người dùng submit scan → theo dõi status → mở result → xem score, evidence, explanation, recommendation.

### FR
- FR01: Fetch Result API theo scanId/access proof.
- FR02: Hiển thị score/risk level.
- FR03: Hiển thị evidence và recommendation.
- FR04: UI có processing, failure, degraded states.
- FR05: Không hiển thị unavailable/NO_DATA như SAFE.
- FR06: Xử lý unauthorized/not-found result.

### NFR
- Safe rendering với dữ liệu không tin cậy.
- Responsive/mobile viewport.
- Loading/error states rõ ràng.
- Không lộ thông tin của scan ngoài quyền truy cập.

### Manual UAT
- [ ] URL Scan thật → Result UI.
- [ ] Risky/low-risk fixture.
- [ ] Degraded/worker failure.
- [ ] Unauthorized result.
- [ ] UI không lỗi khi evidence optional.

**Target: G3; hỗ trợ S01 đạt G4.**

---

## S02 — Scan Text Integration

### User Journey
User nhập MESSAGE/TRANSACTION_POST → submit → Text Worker phân tích → RiskResult.

### FR
- FR01: Web form hỗ trợ cả hai contentType.
- FR02: Text input validation.
- FR03: Scan request đi vào shared Scan Core.
- FR04: Text Worker nhận job thật qua AMQP.
- FR05: Worker trả AnalysisSignal theo contract.
- FR06: Derived indicators được xử lý theo approved policy.
- FR07: RiskResult được lưu và hiển thị.
- FR08: Có failure/timeout/degraded states.

### NFR
- Không gửi nội dung nhạy cảm vào logs ngoài policy.
- Không tự fetch URL trích xuất.
- Không đánh dấu SAFE khi analysis unavailable.
- Text normalization consistent/deterministic.

### Integration Tests
- [ ] MESSAGE input.
- [ ] TRANSACTION_POST input.
- [ ] Text chứa URL/phone indicator.
- [ ] Invalid input.
- [ ] Worker result message roundtrip.
- [ ] Timeout/worker failure.
- [ ] Render real result trên Web.

**Target: G3 cho deterministic text pipeline. AI enrichment phải được theo dõi riêng.**

---

## TE03 — Independent Async AI Worker

### System Flow
AiTask event → AI Worker → Provider/Model Adapter → AiPrediction event → Scan Core completion.

### FR
- FR01: AI Worker nhận job qua AMQP.
- FR02: Adapter gọi provider/test sandbox theo approved contract.
- FR03: Kết quả trả thành AiPrediction.
- FR04: Có timeout/retry/error semantics.
- FR05: Late prediction được xử lý đúng completion barrier.
- FR06: Tách AI Worker khỏi core API persistence.

### NFR
- Không expose provider secrets.
- Không giả định AI response luôn hợp lệ.
- Có output schema validation.
- AI failure không khiến parent scan treo vô hạn.
- Đo/ghi metadata cần thiết để đánh giá AI riêng khỏi integration test.

### Tests
- [ ] AMQP AiTask/AiPrediction roundtrip.
- [ ] Prediction schema validation.
- [ ] Provider unavailable.
- [ ] Timeout/late result.
- [ ] Duplicate response.
- [ ] Parent scan degraded theo policy.

**Target: G2, nâng G3 nếu provider + completion barrier đã tích hợp thật.**

## Dependencies
- Kiên: CORE-01, RiskResult, AI completion barrier.
- Khải: RabbitMQ runtime/staging.
- Hùng: guest/user access fixtures.

## Definition of Done
- [ ] R02 dùng API thật.
- [ ] S02 deterministic pipeline đạt G3 hoặc blocker được ghi rõ.
- [ ] TE03 có contract/AMQP tests.
- [ ] Kiên review event/risk semantics.
- [ ] Không nhầm AI stub thành inference production.
- [ ] Evidence PR, CI và demo được đưa vào tracker.