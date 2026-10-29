# T-MVP04 · Text Scan, QR/VietQR, AI, History và Export — Báo cáo kiểm thử

| Trường | Giá trị |
|---|---|
| Mã nhóm | T-MVP04 |
| Người kiểm thử | Thắng / bộ QA của nhánh; Kiên chưa xác nhận độc lập |
| Ngày chạy | Theo hồ sơ nguồn từng lần; báo cáo hồi cứu lập 29/10/2026 |
| Phiên bản code | PR/commit ở [BCTN](BCTN_T-MVP04_Text_QR_AI_History_Export_Thang.md); link source hiện tại ghim `0fe194976260` |
| Môi trường | Phân biệt unit/fixture/sandbox/broker/DB/browser trong bảng và nguồn |

## 1. Mục tiêu kiểm thử

Kiểm chứng đúng phạm vi: Phân công W12 chủ sở hữu cung cấp: S02 Text E2E, S05 QR/VietQR nested, E-AI RabbitMQ, R03 History và R04 PDF/HTML. Dùng V3.3 document defaults và dữ liệu ngân hàng synthetic cho QA, không nhận source/provider thật. Không suy từ bộ test thành chứng nhận an toàn tuyệt đối hoặc G4.

## 2. Chiến lược

| Tầng | Phạm vi và nguồn |
|---|---|
| Unit/contract | Assertions/fixtures của đúng snapshot trong [Spec triển khai](https://github.com/KLTN-2023-HCMSU/Scam-Risk-Detector/blob/0fe194976260bba5b7d82553ee0d51a5b644fa7c/docs/specs/T-W12_text_qr_ai_history_export.md) · [Hồ sơ kiểm chứng](https://github.com/KLTN-2023-HCMSU/Scam-Risk-Detector/blob/0fe194976260bba5b7d82553ee0d51a5b644fa7c/docs/specs/T-W12_text_qr_ai_history_export.md) |
| Tích hợp | Chỉ nhận kết quả HTTP/DB/broker nếu nguồn có mô tả chạy thật |
| Bảo mật | Validation, privacy, owner isolation/XSS theo từng ca được liệt kê |
| Hiệu năng | Chưa có benchmark kiểm soát; không báo latency/accuracy |

## 3. Bảng ca kiểm thử

| # | Ca | Đầu vào | Kỳ vọng | Nguồn kết quả thực tế | Đánh giá |
|---|---|---|---|---|---|
| 1 | Text qua worker/Core | OTP và text thường; provider failure | terminal result, đúng policy, degraded khi lỗi | HTTP stack integration được ghi trong W12 spec | PASS theo hồ sơ nguồn; không phải lượt chạy lại ngày soạn |
| 2 | QR parent-child | QR URL/VietQR, CRC sai | link child đúng; invalid 400; không mở URL tự động | Core/worker integration + decoder tests | PASS theo hồ sơ nguồn; không phải lượt chạy lại ngày soạn |
| 3 | Ownership/history | hai USER; filter/page | chỉ thấy dữ liệu owner, foreign 404 | PostgreSQL/HTTP acceptance | PASS theo hồ sơ nguồn; không phải lượt chạy lại ngày soạn |
| 4 | Report proof | code đúng/bị sửa; scanId khác | verify đúng, từ chối tamper | server verifier tests/integration | PASS theo hồ sơ nguồn; không phải lượt chạy lại ngày soạn |

## 4. Cách chạy lại toàn bộ

Trong repo code đúng snapshot, đọc dependency/config trong spec trước khi chạy:

```bash
python scripts/qa-w12-stack.py
# Cần PostgreSQL/Redis/Rabbit local và Python dependencies theo W12 spec; chỉ dùng DB/vhost tạm
```

W12 spec ghi Java 256 tổng/0 failure/0 error/15 opt-in skipped ở lượt cuối; unit/fixture và PostgreSQL/broker E2E được phân biệt riêng. Không nhận các số đó là test count của commit W13 mới.

Lệnh cần service, credential hoặc opt-in không mặc nhiên chạy được trên máy chưa cấu hình. [Spec triển khai](https://github.com/KLTN-2023-HCMSU/Scam-Risk-Detector/blob/0fe194976260bba5b7d82553ee0d51a5b644fa7c/docs/specs/T-W12_text_qr_ai_history_export.md) · [Hồ sơ kiểm chứng](https://github.com/KLTN-2023-HCMSU/Scam-Risk-Detector/blob/0fe194976260bba5b7d82553ee0d51a5b644fa7c/docs/specs/T-W12_text_qr_ai_history_export.md). [Chi tiết phạm vi và PR](BCTN_T-MVP04_Text_QR_AI_History_Export_Thang.md).

## 5. Ca không đạt hoặc đạt một phần

PR #24 còn mở, chưa merge và chưa có Kiên sign-off/G4. Live provider/nguồn ngân hàng thực và shared staging không được chứng minh bằng QA synthetic. PHONE được bổ sung sau trong W13 theo yêu cầu riêng. Các ca chưa thực hiện được giữ pending, không đánh PASS dựa vào việc có code/schema. BCKT này tổng hợp nguồn cũ; không có lượt chạy ứng dụng mới trong repo document.

## 6. Số liệu chấm điểm / hiệu năng

Không áp dụng — không có benchmark detector/độ chính xác hoặc tập test độc lập được phép công bố từ bộ bằng chứng này. Test counts là số liệu regression của snapshot ghi trong nguồn, không phải số test mới của tuần.

## 7. Kết luận

Có bằng chứng tự kiểm theo bảng; trạng thái merge xem BCTN, trạng thái nghiệm thu cần Kiên/nhóm xác nhận. Không tự điền chữ ký Kiên, G4 hoặc review mà Thắng chưa trực tiếp thực hiện.
