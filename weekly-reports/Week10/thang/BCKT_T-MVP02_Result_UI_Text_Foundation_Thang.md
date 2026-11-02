# T-MVP02 · Result UI và Text Scan Foundation (R02/S02) — Báo cáo kiểm thử

| Trường | Giá trị |
|---|---|
| Mã nhóm | T-MVP02 |
| Người kiểm thử | Thắng / bộ QA của nhánh; Kiên chưa xác nhận độc lập |
| Ngày chạy | Theo hồ sơ nguồn từng lần; báo cáo hồi cứu lập 29/10/2026 |
| Phiên bản code | PR/commit ở [BCTN](BCTN_T-MVP02_Result_UI_Text_Foundation_Thang.md); link source hiện tại ghim `0fe194976260` |
| Môi trường | Phân biệt unit/fixture/sandbox/broker/DB/browser trong bảng và nguồn |

## 1. Mục tiêu kiểm thử

Kiểm chứng đúng phạm vi: ResultDetailView với risky/low-risk/no-data/degraded fixtures; form MESSAGE/TRANSACTION_POST, validation và adapter intake. Đối chiếu T-MVP02 nhưng không nhận toàn bộ History/Export của timeline đã xong ở snapshot W10. Không suy từ bộ test thành chứng nhận an toàn tuyệt đối hoặc G4.

## 2. Chiến lược

| Tầng | Phạm vi và nguồn |
|---|---|
| Unit/contract | Assertions/fixtures của đúng snapshot trong [Spec triển khai](https://github.com/KLTN-2023-HCMSU/Scam-Risk-Detector/blob/0fe194976260bba5b7d82553ee0d51a5b644fa7c/docs/specs/R02_result_detail_W10.md) · [Hồ sơ kiểm chứng](https://github.com/KLTN-2023-HCMSU/Scam-Risk-Detector/blob/0fe194976260bba5b7d82553ee0d51a5b644fa7c/docs/specs/S02_text_scan_foundation_W10.md) |
| Tích hợp | Chỉ nhận kết quả HTTP/DB/broker nếu nguồn có mô tả chạy thật |
| Bảo mật | Validation, privacy, owner isolation/XSS theo từng ca được liệt kê |
| Hiệu năng | Chưa có benchmark kiểm soát; không báo latency/accuracy |

## 3. Bảng ca kiểm thử

| # | Ca | Đầu vào | Kỳ vọng | Nguồn kết quả thực tế | Đánh giá |
|---|---|---|---|---|---|
| 1 | Trạng thái kết quả | loading/failed/unavailable/no-data | có nhãn, không hiện SAFE giả | ResultDetailView tests | PASS theo hồ sơ nguồn; không phải lượt chạy lại ngày soạn |
| 2 | Payload không hợp lệ | thiếu score/level; evidence optional | từ chối verdict sai, không crash vì evidence | runtime validator/component tests | PASS theo hồ sơ nguồn; không phải lượt chạy lại ngày soạn |
| 3 | Safe rendering | HTML trong explanation | hiện chữ, không thực thi | component/browser fixtures | PASS theo hồ sơ nguồn; không phải lượt chạy lại ngày soạn |
| 4 | Form và responsive | MESSAGE/TRANSACTION_POST, chuỗi dài, mobile | validation và không overflow | S02 spec + ảnh browser local | PASS theo hồ sơ nguồn; không phải lượt chạy lại ngày soạn |

## 4. Cách chạy lại toàn bộ

Trong repo code đúng snapshot, đọc dependency/config trong spec trước khi chạy:

```bash
cd apps/web
npm ci
npm test
npm run lint
npm run typecheck
npm run build
```

R02 spec ghi 97/97 web tests ở lượt xác minh 13/10; scope fixture/UI. PR #12 và #13 đã merge ngày 20/10. Không lấy ngày merge làm ngày Thắng hoàn thành code hoặc nhận xong đúng hạn.

Lệnh cần service, credential hoặc opt-in không mặc nhiên chạy được trên máy chưa cấu hình. [Spec triển khai](https://github.com/KLTN-2023-HCMSU/Scam-Risk-Detector/blob/0fe194976260bba5b7d82553ee0d51a5b644fa7c/docs/specs/R02_result_detail_W10.md) · [Hồ sơ kiểm chứng](https://github.com/KLTN-2023-HCMSU/Scam-Risk-Detector/blob/0fe194976260bba5b7d82553ee0d51a5b644fa7c/docs/specs/S02_text_scan_foundation_W10.md). [Chi tiết phạm vi và PR](BCTN_T-MVP02_Result_UI_Text_Foundation_Thang.md).

## 5. Ca không đạt hoặc đạt một phần

Snapshot W10 là foundation/fixture. API result ownership, polling, History và Export đầy đủ được tích hợp tiếp ở các PR sau; không dùng kết quả W12 để nhận W10 đã hoàn thành E2E. Các ca chưa thực hiện được giữ pending, không đánh PASS dựa vào việc có code/schema. BCKT này tổng hợp nguồn cũ; không có lượt chạy ứng dụng mới trong repo document.

## 6. Số liệu chấm điểm / hiệu năng

Không áp dụng — không có benchmark detector/độ chính xác hoặc tập test độc lập được phép công bố từ bộ bằng chứng này. Test counts là số liệu regression của snapshot ghi trong nguồn, không phải số test mới của tuần.

## 7. Kết luận

Có bằng chứng tự kiểm theo bảng; trạng thái merge xem BCTN, trạng thái nghiệm thu cần Kiên/nhóm xác nhận. Không tự điền chữ ký Kiên, G4 hoặc review mà Thắng chưa trực tiếp thực hiện.
