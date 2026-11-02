# T-MVP03 · Text extraction, Result Integration và AI runtime handoff — Báo cáo kiểm thử

| Trường | Giá trị |
|---|---|
| Mã nhóm | T-MVP03 |
| Người kiểm thử | Thắng / bộ QA của nhánh; Kiên chưa xác nhận độc lập |
| Ngày chạy | Theo hồ sơ nguồn từng lần; báo cáo hồi cứu lập 29/10/2026 |
| Phiên bản code | PR/commit ở [BCTN](BCTN_T-MVP03_Text_Extraction_AI_Runtime_Thang.md); link source hiện tại ghim `0fe194976260` |
| Môi trường | Phân biệt unit/fixture/sandbox/broker/DB/browser trong bảng và nguồn |

## 1. Mục tiêu kiểm thử

Kiểm chứng đúng phạm vi: TextAnalyzer, entity extraction URL/phone/bank có cue; Java Text Worker AMQP, AI task/prediction handoff và adapter intake. Timeline T-MVP03 nhấn QR/VietQR; task W11 nhấn Text/Result/AI, nên ghi rõ phần QR nested được hoàn thiện ở PR W12. Không suy từ bộ test thành chứng nhận an toàn tuyệt đối hoặc G4.

## 2. Chiến lược

| Tầng | Phạm vi và nguồn |
|---|---|
| Unit/contract | Assertions/fixtures của đúng snapshot trong [Spec triển khai](https://github.com/KLTN-2023-HCMSU/Scam-Risk-Detector/blob/0fe194976260bba5b7d82553ee0d51a5b644fa7c/docs/specs/S02_text_analysis_integration.md) · [Hồ sơ kiểm chứng](https://github.com/KLTN-2023-HCMSU/Scam-Risk-Detector/blob/0fe194976260bba5b7d82553ee0d51a5b644fa7c/docs/specs/E-AI_text_worker.md) |
| Tích hợp | Chỉ nhận kết quả HTTP/DB/broker nếu nguồn có mô tả chạy thật |
| Bảo mật | Validation, privacy, owner isolation/XSS theo từng ca được liệt kê |
| Hiệu năng | Chưa có benchmark kiểm soát; không báo latency/accuracy |

## 3. Bảng ca kiểm thử

| # | Ca | Đầu vào | Kỳ vọng | Nguồn kết quả thực tế | Đánh giá |
|---|---|---|---|---|---|
| 1 | Entity extraction | URL/phone/STK có cue; số bất kỳ/CCCD | chỉ sinh indicator hợp lệ, không lookup/fetch ngầm | TextAnalyzer regression | PASS theo hồ sơ nguồn; không phải lượt chạy lại ngày soạn |
| 2 | AI publish lỗi | broker publish-confirm fail | không báo pending hoặc ACK thành công giả | TextRabbitWorker tests | PASS theo hồ sơ nguồn; không phải lượt chạy lại ngày soạn |
| 3 | Retry và deadline | cùng job phát lại, deadline cũ | giữ identity, không gia hạn vô hạn | broker regression/QA harness | PASS theo hồ sơ nguồn; không phải lượt chạy lại ngày soạn |
| 4 | Provider hỏng | AI timeout/mất mạng giả lập | prediction failure hợp schema; Core xử lý fallback | AI unit/HTTP sandbox, không live model | PASS theo hồ sơ nguồn; không phải lượt chạy lại ngày soạn |

## 4. Cách chạy lại toàn bộ

Trong repo code đúng snapshot, đọc dependency/config trong spec trước khi chạy:

```bash
cd services/api
./mvnw test
# AI worker: cài services/ai-worker/requirements.txt và chạy theo E-AI spec
```

Spec integration lịch sử ghi TextAnalyzer/TextRabbitWorker 11/11; AI sandbox 19 PASS, broker chạy riêng. Java tổng 117 discovered/115 PASS/2 opt-in skipped trong snapshot đó. Không đồng nhất sandbox với live provider.

Lệnh cần service, credential hoặc opt-in không mặc nhiên chạy được trên máy chưa cấu hình. [Spec triển khai](https://github.com/KLTN-2023-HCMSU/Scam-Risk-Detector/blob/0fe194976260bba5b7d82553ee0d51a5b644fa7c/docs/specs/S02_text_analysis_integration.md) · [Hồ sơ kiểm chứng](https://github.com/KLTN-2023-HCMSU/Scam-Risk-Detector/blob/0fe194976260bba5b7d82553ee0d51a5b644fa7c/docs/specs/E-AI_text_worker.md). [Chi tiết phạm vi và PR](BCTN_T-MVP03_Text_Extraction_AI_Runtime_Thang.md).

## 5. Ca không đạt hoặc đạt một phần

Không nhận QR parent-child E2E, result ownership API thật hoặc live model là đã nghiệm thu W11. Các spec đầu kỳ ghi rõ còn chờ Core read contract; phần tích hợp hoàn tất tiếp trong W12. Các ca chưa thực hiện được giữ pending, không đánh PASS dựa vào việc có code/schema. BCKT này tổng hợp nguồn cũ; không có lượt chạy ứng dụng mới trong repo document.

## 6. Số liệu chấm điểm / hiệu năng

Không áp dụng — không có benchmark detector/độ chính xác hoặc tập test độc lập được phép công bố từ bộ bằng chứng này. Test counts là số liệu regression của snapshot ghi trong nguồn, không phải số test mới của tuần.

## 7. Kết luận

Có bằng chứng tự kiểm theo bảng; trạng thái merge xem BCTN, trạng thái nghiệm thu cần Kiên/nhóm xác nhận. Không tự điền chữ ký Kiên, G4 hoặc review mà Thắng chưa trực tiếp thực hiện.
