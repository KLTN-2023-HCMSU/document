# T-MVP01 · Text Normalization và Worker/Result Contracts — Báo cáo tính năng

## Thông tin chung

| Trường | Giá trị |
|---|---|
| Mã nhóm tính năng | T-MVP01 |
| Tên nhóm | Text Normalization và Worker/Result Contracts |
| Người thực hiện | Thắng |
| Người review chéo | Kiên theo phân công; không thay xác nhận cá nhân |
| Thành phần | Web / Core / Worker theo phạm vi bên dưới |
| Tuần / Sprint | W09 · 27/09–05/10/2026; Sprint chưa xác nhận |
| Mốc kiến trúc | V3.3; không suy ra quyết định nhóm đã approve |
| Pull Request / Commit | Bảng truy vết ở dưới |
| Trạng thái | Báo cáo bổ sung hồi cứu; trạng thái code theo từng PR, chưa nhận nghiệm thu |
| Ngày nộp báo cáo | Soạn 29/10/2026; chờ review PR tài liệu |

---

## 1. Tóm tắt trong 30 giây

- **Tôi đã làm gì:** Chuẩn hóa tiếng Việt, leetspeak/zero-width cơ bản; giữ view gốc và view đối chiếu; privacy boundary; TextJob/TextWorkerResult và RiskResult fixtures.
- **Ai dùng ngay:** các thành viên đọc contract/runbook để tích hợp hoặc review theo phạm vi.
- **Chạy thử nhanh nhất:** xem mục 11; fixture không thay E2E.

---

## 2. Phạm vi đã làm

### 2.1. Đã hoàn thành ở mức có bằng chứng

| Ưu tiên | Tính năng | Bằng chứng |
|---|---|---|
| P0/P1 theo task | Có deterministic normalization và fixtures cho tiếng Việt/evasion. | [Spec triển khai](https://github.com/KLTN-2023-HCMSU/Scam-Risk-Detector/blob/0fe194976260bba5b7d82553ee0d51a5b644fa7c/docs/specs/T-MVP01_text_worker_contracts.md) · [Hồ sơ kiểm chứng](https://github.com/KLTN-2023-HCMSU/Scam-Risk-Detector/blob/0fe194976260bba5b7d82553ee0d51a5b644fa7c/docs/specs/T-MVP01_verification.md) |
| P0/P1 theo task | Có contract, validator/fixtures WorkerResult và UI demo RiskResult. | [Spec triển khai](https://github.com/KLTN-2023-HCMSU/Scam-Risk-Detector/blob/0fe194976260bba5b7d82553ee0d51a5b644fa7c/docs/specs/T-MVP01_text_worker_contracts.md) · [Hồ sơ kiểm chứng](https://github.com/KLTN-2023-HCMSU/Scam-Risk-Detector/blob/0fe194976260bba5b7d82553ee0d51a5b644fa7c/docs/specs/T-MVP01_verification.md) |
| P0/P1 theo task | Không để worker quyết định score/verdict hoặc đọc clipboard/SMS ngầm. | [Spec triển khai](https://github.com/KLTN-2023-HCMSU/Scam-Risk-Detector/blob/0fe194976260bba5b7d82553ee0d51a5b644fa7c/docs/specs/T-MVP01_text_worker_contracts.md) · [Hồ sơ kiểm chứng](https://github.com/KLTN-2023-HCMSU/Scam-Risk-Detector/blob/0fe194976260bba5b7d82553ee0d51a5b644fa7c/docs/specs/T-MVP01_verification.md) |

### 2.2. Chưa hoàn tất / không thuộc snapshot

Không nhận public async HTTP, AMQP runtime, live AI hoặc toàn bộ source WP là đã hoàn thành trong W09.

[Task tuần](../../../tasks/W09/T-MVP01_Text_Normalization_Worker_Result_Contracts.md)

---

## 3. Hợp đồng API

Không có endpoint HTTP runtime thuộc slice W09. Hợp đồng chính là TextJob/TextWorkerResult v1.0 và CanonicalRiskResult; fixture không chứng minh có Core đang chạy.

**Input minh họa, không có dữ liệu người dùng thật:**

```json
{"text":"Tin nhan minh hoa","contentType":"MESSAGE"}
```

**Output/đường lỗi:** Worker trả signals/derivedIndicators/pendingAiTasks theo schema; score/riskLevel thuộc Core, không có trong WorkerResult. Validation/quyền/phụ thuộc phải đọc theo OpenAPI của đúng snapshot, không suy từ ví dụ thành contract mới.

[OpenAPI hiện tại](https://github.com/KLTN-2023-HCMSU/Scam-Risk-Detector/blob/0fe194976260bba5b7d82553ee0d51a5b644fa7c/contracts/openapi/anti-scam-api.yaml); đối chiếu spec lịch sử nếu chạy PR cũ.

---

## 4. Mô hình dữ liệu

Không áp dụng — slice W09 không tạo DB schema/cache hoặc lưu raw text.

---

## 5. Luồng xử lý

```mermaid
flowchart LR
Text input --> Normalize[Original và canonical views] --> Signals[Normalization signals] --> Contract[WorkerResult contract] --> Core[Core nhận signals]
```

Nhánh lỗi và giới hạn: Không nhận public async HTTP, AMQP runtime, live AI hoặc toàn bộ source WP là đã hoàn thành trong W09.

---

## 6. Cấu trúc mã nguồn

| Điểm vào / File | Trách nhiệm |
|---|---|
| [docs/specs/T-MVP01_text_worker_contracts.md](https://github.com/KLTN-2023-HCMSU/Scam-Risk-Detector/blob/0fe194976260bba5b7d82553ee0d51a5b644fa7c/docs/specs/T-MVP01_text_worker_contracts.md) | Ranh giới normalization, privacy và hợp đồng worker/result W09 |
| [contracts/events/text-worker-v1.schema.json](https://github.com/KLTN-2023-HCMSU/Scam-Risk-Detector/blob/0fe194976260bba5b7d82553ee0d51a5b644fa7c/contracts/events/text-worker-v1.schema.json) | JSON Schema để validate TextJob/TextWorkerResult |

Link code ở báo cáo này ghim tại `0fe194976260` để không thay đổi âm thầm. Spec lịch sử ghi snapshot cũ riêng; không nhận file hiện tại đã tồn tại nguyên trạng tại mọi tuần.

---

## 7. Quyết định kỹ thuật và đánh đổi

Giữ original view để bảo toàn chữ số/nhãn và canonical view để phát hiện evasion; đổi lại phải kiểm soát lifecycle dữ liệu nhạy cảm.

Không có biên bản bổ sung phê duyệt DEC của cả nhóm/giảng viên; chỉ ghi lý do implementation và yêu cầu owner đã có. Xem nguồn kiến trúc thay vì tạo bản sao business rules.

---

## 8. Cấu hình và biến môi trường

Không có secret bắt buộc cho normalization/offline contract. Java dùng Maven wrapper; Python theo contracts/requirements.txt.

Cài dependency theo Maven wrapper/npm lockfile/requirements của repo code. Không chép .env, token, key hoặc thông tin đăng nhập vào document.

---

## 9. Bảo mật và quyền riêng tư

| Rủi ro | Xử lý / giới hạn |
|---|---|
| Nội dung riêng tư | Không log raw text/định danh/token; dùng dữ liệu thử có kiểm soát |
| Worker vượt quyền | Worker trả facts/signals, Core quyết định score/owner/lifecycle |
| UI/XSS và kết luận an toàn | Text binding, không suy ra SAFE khi thiếu verdict; phạm vi kiểm thử theo BCKT |

[Business Rules chuẩn](https://github.com/KLTN-2023-HCMSU/Scam-Risk-Detector/blob/0fe194976260bba5b7d82553ee0d51a5b644fa7c/docs/business-rules.md)

---

## 10. Kiểm thử

Hồ sơ xác minh lịch sử ghi contracts 21 tests PASS và web 82 tests PASS; Java dùng unit/H2. Đây là số liệu ở snapshot ghi trong spec, không phải lần chạy lại ngày nộp báo cáo.

[Báo cáo kiểm thử theo Template 04](BCKT_T-MVP01_Text_Normalization_Contracts_Thang.md) · [Spec triển khai](https://github.com/KLTN-2023-HCMSU/Scam-Risk-Detector/blob/0fe194976260bba5b7d82553ee0d51a5b644fa7c/docs/specs/T-MVP01_text_worker_contracts.md) · [Hồ sơ kiểm chứng](https://github.com/KLTN-2023-HCMSU/Scam-Risk-Detector/blob/0fe194976260bba5b7d82553ee0d51a5b644fa7c/docs/specs/T-MVP01_verification.md)

---

## 11. Cách chạy thử / demo

Tại checkout **repo code** phù hợp với PR/snapshot, không chạy lệnh ứng dụng trong repo document:

```bash
python -m pip install -r contracts/requirements.txt
python -m unittest discover -s contracts/tests -v
```

![Ảnh minh họa từ repo code](https://github.com/KLTN-2023-HCMSU/Scam-Risk-Detector/blob/0fe194976260bba5b7d82553ee0d51a5b644fa7c/docs/specs/assets/T-MVP01_text_contract_demo.png?raw=true)

Ảnh/fixture lịch sử không chứng minh mọi ca backend hoặc môi trường phát hành đang hoạt động.

---

## 12. Ảnh hưởng tới người khác

| Với ai | Nội dung cần bàn giao | Xác nhận |
|---|---|---|
| Kiên | Review contract/Core/quyền và kiểm thử chéo đúng snapshot | Chưa tự điền approve |
| Khải / nhóm runtime | Queue/schema/config/browser theo phần có ảnh hưởng | Xem handoff trong spec, chưa tự nhận đã thông báo |
| Nhóm / giảng viên | Phân biệt implemented, merged và accepted | Báo cáo này không phải biên bản nghiệm thu |

---

## 13. Hạn chế đã biết và việc còn nợ

Không nhận public async HTTP, AMQP runtime, live AI hoặc toàn bộ source WP là đã hoàn thành trong W09.

Chưa ghi nhận giờ làm thực tế, review cá nhân, thời điểm hoàn thành từng hạng mục hoặc chữ ký UAT. Không công bố accuracy/latency/F1 từ unit test.

---

## 14. Nhật ký thay đổi

| Ngày | Nội dung |
|---|---|
| 29/10/2026 | Bổ sung hồi cứu theo Template 01, không nhận đã nộp đúng hạn |

| PR | Trạng thái GitHub tại lúc kiểm tra | Thời điểm merge UTC | Head snapshot |
|---|---|---|---|
| [#7](https://github.com/KLTN-2023-HCMSU/Scam-Risk-Detector/pull/7) | MERGED | 2026-10-11T06:36:12Z | `69640668bd78` |

Ngày merge không phải ngày code hoàn thành. Báo cáo tuần không lấy trạng thái hiện tại gán ngược cho hạn cuối tuần.
