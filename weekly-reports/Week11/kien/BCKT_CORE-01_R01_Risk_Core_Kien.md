# CORE-01/R01 · RabbitMQ, WorkerResult, Risk Fusion, Status — Báo cáo kiểm thử

| Trường | Giá trị |
|---|---|
| **Mã nhóm** | `CORE-01`, `R01` (W11); S03/S04 boundary liên quan |
| **Người kiểm thử** | Kiên / AI Agent (kết quả theo Agent; bổ sung review source `(6)`) |
| **Ngày** | 19/10/2026 |
| **Phiên bản code** | Snapshot `(6)`; branch `feat/CORE-01-R01-scan-lifecycle`, SHA chưa xác minh |
| **Môi trường** | Maven/JUnit, Python JSON Schema, RabbitMQ+PG+Redis Docker smoke (Agent) |

## 1. Mục tiêu kiểm thử

Chứng minh HTTP → outbox → RabbitMQ → WorkerResult → `scan_signals`/`scan_results` → trạng thái terminal; idempotent khi duplicate event; không trả SAFE từ dữ liệu thiếu; không ghi result trái terminal state khi timeout đến đồng thời.

## 2. Chiến lược

| Tầng | Kiểm gì | Công cụ |
|---|---|---|
| Unit | Risk fusion và topology | `RiskFusionEvaluatorTest`, `RabbitScanMessagingConfigurationTest` |
| Event contract | URL job/result fixture theo schema | `contracts/validate_events.py` |
| Integration | broker URL fixture → result persisted | `scripts/ci-runtime-smoke.py` |
| Concurrency | Result vs timeout; duplicate/restart | **Chưa đủ test** |
| Security | Ownership/IDOR status | Smoke + `ScanW10DomainTest` |

## 3. Bảng ca kiểm thử

| # | Ca | Đầu vào | Kỳ vọng | Kết quả / evidence | Đạt |
|---|---|---|---|---|---|
| 1 | URL broker roundtrip | POST URL, fixture worker URL | COMPLETED + result | Smoke PASS theo Agent trước follow-up | ✅* |
| 2 | Duplicate WorkerResult | Cùng eventId gửi lại | Không double result | Smoke PASS theo Agent | ✅* |
| 3 | Timeout sau deadline | Scan không có result | FAILED/TIMEOUT | Smoke PASS theo Agent | ✅* |
| 4 | Late result sau FAILED | Event gửi sau terminal | Không overwrite | Smoke PASS theo Agent **tuần tự** | ✅* |
| 5 | Concurrent WorkerResult/timeout | Event và scheduler cùng lúc | Không có FAILED + `scan_results` | **Chưa chứng minh; source có SELECT→INSERT→UPDATE race** | ❌ |
| 6 | `NO_DATA` đơn lẻ | Signal NO_DATA | CAUTION/degraded | Unit focused PASS theo Agent | ✅* |
| 7 | `NO_DATA + LOW` | Thiếu reputation + 1 LOW | Không khẳng định SAFE khi chưa đủ policy | Current evaluator có thể trả `SAFE,degraded=true` | ❌ |
| 8 | `REPUTATION_UNAVAILABLE + LOW` | Source outage + LOW | Fail-safe theo policy được duyệt | Current evaluator có thể trả `SAFE,degraded=true` | ❌ |
| 9 | Active published rule set | Rule version active | Evaluator dùng rule DB | Chưa có integration, hardcode weights | ❌ |
| 10 | TEXT topology | Queue + bindings | `q.scan.text`, 2 routing keys | Focused topology test PASS theo Agent | ✅* |
| 11 | TEXT broker roundtrip | POST TEXT → fixture | COMPLETED | **Chưa chạy có exit code** | ⚠ |
| 12 | Real URL/TEXT Worker | Worker Khải/Thắng | Worker production G3 | Fixture only | ⚠ |
| 13 | Provider PHONE/BANK | FOUND/NO_DATA/unavailable | Evidence chính thức | Chưa có approved provider | ⚠ |
| 14 | Secret hygiene | Repository fixtures | Không credential thật, scanner policy rõ | Secret scan báo 2 synthetic fixtures, chưa green | ⚠ |

`✅*`: bằng chứng Agent báo cáo; snapshot `(6)` **chưa được chạy lại toàn bộ** trong phiên tạo báo cáo này.

## 4. Cách chạy lại toàn bộ

```bash
cd Scam-Risk-Detector/services/api
./mvnw -B test
cd ../..
python3 -m unittest discover -s contracts/tests -v
python3 contracts/validate_events.py job contracts/events/fixtures/url-job.json
python3 contracts/validate_events.py result contracts/events/fixtures/worker-result-url-suspicious.json
python3 scripts/ci-runtime-smoke.py
bash scripts/scan-secrets.sh
```

Dự kiến mở rộng smoke script cho TEXT fixture, simultaneous timeout/result và consumer restart; giữ log và exit code CI.

## 5. Ca không đạt hoặc đạt một phần

| # | Ca | Sai / thiếu | Nguyên nhân | Xử lý | Hạn |
|---|---|---|---|---|---|
| 5 | Race terminal/result | Có thể ghi result trước UPDATE state thất bại | Không row lock/CAS đủ | Transactional finalization + Postgres concurrency | W12 |
| 7–8 | Mixed evidence false SAFE | Low score vượt guard `score == 0` | Incomplete policy chưa chi phối riskLevel | Thêm tests, chốt fail-safe với Hùng | W12 |
| 9 | Active rules | Hardcoded 40/30/18/8 | Chưa tích hợp rule loader/version | Chốt policy, tích hợp rule version | Sau policy |
| 11–12 | TEXT & real Worker | Mới unit topology | Thiếu runtime contract handoff | Fixture TEXT rồi Worker thật | W12 |
| 13 | Reputation | Không có approved data source | Nguồn ngoài phụ thuộc nhóm | Giữ G1/PARTIAL, duyệt provider | Sau handoff |
| 14 | Secret scan | 2 synthetic fixtures | Scanner rule/fixtures chưa thống nhất | Triage có bằng chứng, không xóa mù | Trước merge |

## 6. Số liệu (Risk Fusion)

Không có dữ liệu đánh giá gán nhãn/ma trận nhầm lẫn để tính Precision, Recall, F1. Severity weights trong evaluator hiện là baseline hardcoded, chưa phải production-calibrated policy. **Không tự điền số liệu chất lượng phân loại.**

## 7. Kết luận

| Câu hỏi | Trả lời |
|---|---|
| Số ca liệt kê | 14 |
| Broker URL fixture | PASS theo Agent trước follow-up |
| TEXT result / worker real | Chưa kiểm chứng |
| Risk + timeout consistency | Chưa đạt |
| Nhóm tính năng đủ nghiệm thu G3 chưa? | **Chưa** |
| Kiến nghị | K3/K4/K5 P0 trước; K6 TEXT smoke; Khải/Thắng integration; Hùng review |

*Người tổng hợp: Kiên · Ngày 19/10/2026 · Mẫu: BCKT v1.0*
