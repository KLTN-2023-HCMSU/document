# T-MVP01 · Text Normalization và Worker/Result Contracts — Báo cáo kiểm thử

| Trường | Giá trị |
|---|---|
| Mã nhóm | T-MVP01 |
| Người kiểm thử | Thắng / bộ QA của nhánh; Kiên chưa xác nhận độc lập |
| Ngày chạy | Theo hồ sơ nguồn từng lần; báo cáo hồi cứu lập 29/10/2026 |
| Phiên bản code | PR/commit ở [BCTN](BCTN_T-MVP01_Text_Normalization_Contracts_Thang.md); link source hiện tại ghim `0fe194976260` |
| Môi trường | Phân biệt unit/fixture/sandbox/broker/DB/browser trong bảng và nguồn |

## 1. Mục tiêu kiểm thử

Kiểm chứng đúng phạm vi: Chuẩn hóa tiếng Việt, leetspeak/zero-width cơ bản; giữ view gốc và view đối chiếu; privacy boundary; TextJob/TextWorkerResult và RiskResult fixtures. Không suy từ bộ test thành chứng nhận an toàn tuyệt đối hoặc G4.

## 2. Chiến lược

| Tầng | Phạm vi và nguồn |
|---|---|
| Unit/contract | Assertions/fixtures của đúng snapshot trong [Spec triển khai](https://github.com/KLTN-2023-HCMSU/Scam-Risk-Detector/blob/0fe194976260bba5b7d82553ee0d51a5b644fa7c/docs/specs/T-MVP01_text_worker_contracts.md) · [Hồ sơ kiểm chứng](https://github.com/KLTN-2023-HCMSU/Scam-Risk-Detector/blob/0fe194976260bba5b7d82553ee0d51a5b644fa7c/docs/specs/T-MVP01_verification.md) |
| Tích hợp | Chỉ nhận kết quả HTTP/DB/broker nếu nguồn có mô tả chạy thật |
| Bảo mật | Validation, privacy, owner isolation/XSS theo từng ca được liệt kê |
| Hiệu năng | Chưa có benchmark kiểm soát; không báo latency/accuracy |

## 3. Bảng ca kiểm thử

| # | Ca | Đầu vào | Kỳ vọng | Nguồn kết quả thực tế | Đánh giá |
|---|---|---|---|---|---|
| 1 | Tiếng Việt và evasion | text có dấu/leet/zero-width | signals normalization ổn định | Bộ normalization trong spec xác minh lịch sử | PASS theo hồ sơ nguồn; không phải lượt chạy lại ngày soạn |
| 2 | Bảo toàn thực thể | số sau nhãn ngân hàng/điện thoại | không làm đổi chữ số | fixture/regression trong hồ sơ nguồn | PASS theo hồ sơ nguồn; không phải lượt chạy lại ngày soạn |
| 3 | Contract worker | result có score/verdict hoặc field sai | validator từ chối | contract negative tests | PASS theo hồ sơ nguồn; không phải lượt chạy lại ngày soạn |
| 4 | UI mock | RiskResult degraded/không có dữ liệu | không suy ra SAFE | fixture UI; chưa phải Core E2E | PASS theo hồ sơ nguồn; không phải lượt chạy lại ngày soạn |

## 4. Cách chạy lại toàn bộ

Trong repo code đúng snapshot, đọc dependency/config trong spec trước khi chạy:

```bash
python -m pip install -r contracts/requirements.txt
python -m unittest discover -s contracts/tests -v
```

Hồ sơ xác minh lịch sử ghi contracts 21 tests PASS và web 82 tests PASS; Java dùng unit/H2. Đây là số liệu ở snapshot ghi trong spec, không phải lần chạy lại ngày nộp báo cáo.

Lệnh cần service, credential hoặc opt-in không mặc nhiên chạy được trên máy chưa cấu hình. [Spec triển khai](https://github.com/KLTN-2023-HCMSU/Scam-Risk-Detector/blob/0fe194976260bba5b7d82553ee0d51a5b644fa7c/docs/specs/T-MVP01_text_worker_contracts.md) · [Hồ sơ kiểm chứng](https://github.com/KLTN-2023-HCMSU/Scam-Risk-Detector/blob/0fe194976260bba5b7d82553ee0d51a5b644fa7c/docs/specs/T-MVP01_verification.md). [Chi tiết phạm vi và PR](BCTN_T-MVP01_Text_Normalization_Contracts_Thang.md).

## 5. Ca không đạt hoặc đạt một phần

Không nhận public async HTTP, AMQP runtime, live AI hoặc toàn bộ source WP là đã hoàn thành trong W09. Các ca chưa thực hiện được giữ pending, không đánh PASS dựa vào việc có code/schema. BCKT này tổng hợp nguồn cũ; không có lượt chạy ứng dụng mới trong repo document.

## 6. Số liệu chấm điểm / hiệu năng

Không áp dụng — không có benchmark detector/độ chính xác hoặc tập test độc lập được phép công bố từ bộ bằng chứng này. Test counts là số liệu regression của snapshot ghi trong nguồn, không phải số test mới của tuần.

## 7. Kết luận

Có bằng chứng tự kiểm theo bảng; trạng thái merge xem BCTN, trạng thái nghiệm thu cần Kiên/nhóm xác nhận. Không tự điền chữ ký Kiên, G4 hoặc review mà Thắng chưa trực tiếp thực hiện.
