# W10 — Thắng: Result UI & Text Scan Foundation

**Owner:** Thắng  
**Reviewer:** Kiên  
**Target:** G2

## R02 — Xem kết quả scan chi tiết

### User Flow
Người dùng mở kết quả scan → xem mức rủi ro, bằng chứng, giải thích và khuyến nghị.

### Functional Requirements
- FR01: Hiển thị score/level theo RiskResult contract.
- FR02: Hiển thị evidence và explanation.
- FR03: Hiển thị recommendation.
- FR04: Có loading, unavailable, failed và degraded states.
- FR05: Không hiển thị `NO_DATA` hoặc unavailable như `SAFE`.
- FR06: Có responsive layout và đường điều hướng từ scan status.

### Non-functional Requirements
- Không render nội dung không tin cậy dưới dạng executable HTML.
- Không để UI lỗi khi optional evidence thiếu.
- Các trạng thái có nhãn rõ ràng, không dựa vào màu sắc duy nhất.
- Không đưa thông tin cá nhân nhạy cảm lên UI ngoài policy.

### W10 Deliverables
- Shared Result UI.
- RiskResult fixtures: risky, low-risk, no-data, degraded.
- UI tests cho loading/error/evidence.
- Giao diện có thể tích hợp với scanId khi API sẵn sàng.

### Tests
- Render từng loại fixture.
- Missing evidence.
- Degraded result.
- Responsive/mobile viewport.
- Không render kết quả sai thành an toàn.

**Target gate:** G2.

---

## S02 — Scan Text Foundation

### User Flow
Người dùng chọn MESSAGE hoặc TRANSACTION_POST → nhập nội dung → submit yêu cầu phân tích.

### Functional Requirements
- FR01: Text form theo approved input contract.
- FR02: Chọn contentType đúng giá trị hỗ trợ.
- FR03: Input validation, length limit theo spec.
- FR04: Normalization/worker fixtures.
- FR05: Text Worker tạo output đúng AnalysisSignal contract.
- FR06: Chuẩn bị derived indicators cho URL/phone/bank khi có policy.

### Non-functional Requirements
- Không tự thực hiện network fetch cho URL trích xuất từ text.
- Không log nguyên văn nội dung nhạy cảm ngoài policy.
- Không coi AI provider error là kết quả `SAFE`.
- Worker output phải deterministic trong contract tests.

### W10 Deliverables
- Web Text form.
- Text normalization fixtures.
- Text Worker contract tests.
- Tài liệu interface với Scan Core.

### Tests
- MESSAGE hợp lệ.
- TRANSACTION_POST hợp lệ.
- Empty/invalid text.
- Text chứa URL.
- Worker error fixture.

**Target gate:** G2.

## Dependencies
- Kiên: Scan API/RiskResult contract.
- Khải: RabbitMQ/runtime.
- AI Provider chưa bắt buộc hoạt động thật trong W10.

## Definition of Done
- [ ] Result UI fixture tests PASS.
- [ ] Text worker contract tests PASS.
- [ ] UI dùng đúng approved schemas.
- [ ] PR được Kiên review.
- [ ] Ghi rõ mock và phần cần tích hợp ở W11.