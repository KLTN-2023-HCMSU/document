# T-W13 · UI/UX Audit, XSS, PDF và Live Demo — Báo cáo kiểm thử

| Trường | Giá trị |
|---|---|
| Mã nhóm | T-W13 |
| Người kiểm thử | Thắng / bộ QA của nhánh; Kiên chưa xác nhận độc lập |
| Ngày chạy | Theo hồ sơ nguồn từng lần; báo cáo hồi cứu lập 29/10/2026 |
| Phiên bản code | PR/commit ở [BCTN](BCTN_T-W13_UI_XSS_PDF_Demo_Thang.md); link source hiện tại ghim `0fe194976260` |
| Môi trường | Phân biệt unit/fixture/sandbox/broker/DB/browser trong bảng và nguồn |

## 1. Mục tiêu kiểm thử

Kiểm chứng đúng phạm vi: UI-REG01, SEC-UI01, EXP-01, DEMO-RUN theo yêu cầu W13 chủ sở hữu cung cấp. PHONE và Docker HTTPS local là phần bổ sung được owner yêu cầu sau freeze; không biến VPS/public staging thành DoD W13. Không suy từ bộ test thành chứng nhận an toàn tuyệt đối hoặc G4.

## 2. Chiến lược

| Tầng | Phạm vi và nguồn |
|---|---|
| Unit/contract | Assertions/fixtures của đúng snapshot trong [Spec triển khai](https://github.com/KLTN-2023-HCMSU/Scam-Risk-Detector/blob/0fe194976260bba5b7d82553ee0d51a5b644fa7c/docs/specs/T-W13_ui_xss_pdf_demo.md) · [Hồ sơ kiểm chứng](https://github.com/KLTN-2023-HCMSU/Scam-Risk-Detector/blob/0fe194976260bba5b7d82553ee0d51a5b644fa7c/docs/specs/T-W13_phone_https_acceptance.md) |
| Tích hợp | Chỉ nhận kết quả HTTP/DB/broker nếu nguồn có mô tả chạy thật |
| Bảo mật | Validation, privacy, owner isolation/XSS theo từng ca được liệt kê |
| Hiệu năng | Chưa có benchmark kiểm soát; không báo latency/accuracy |

## 3. Bảng ca kiểm thử

| # | Ca | Đầu vào | Kỳ vọng | Nguồn kết quả thực tế | Đánh giá |
|---|---|---|---|---|---|
| 1 | Skeleton/accessibility | loading → failed/completed, reduced motion | có busy status, không fake score; bỏ skeleton khi kết thúc | ResultDetailView regression + preview desktop/mobile | PASS theo hồ sơ nguồn; không phải lượt chạy lại ngày soạn |
| 2 | Stored-result XSS | HTML/SVG/script trong trường DB QA | render thành text, không active DOM/JS | 3 engine local; Firefox HTTPS với CA profile tạm | PASS theo hồ sơ nguồn; không phải lượt chạy lại ngày soạn |
| 3 | PDF 5 loại | URL/Text/Phone/Bank/QR và evidence dài | A4 không tràn/trắng; có hash/proof/nhãn nguồn | Chrome/Firefox/Safari PDFs + manifest | PASS theo hồ sơ nguồn; không phải lượt chạy lại ngày soạn |
| 4 | TLS/RBAC/SSRF | CA lạ, hostname sai, foreign USER, loopback URL | TLS reject, 403/404/400 tùy ca | HTTPS local security checks | PASS theo hồ sơ nguồn; không phải lượt chạy lại ngày soạn |
| 5 | PR tích hợp dev | source head 0fe1949 | required-ci đủ job thành công | GitHub Actions 11/11 success | PASS theo hồ sơ nguồn; không phải lượt chạy lại ngày soạn |

## 4. Cách chạy lại toàn bộ

Trong repo code đúng snapshot, đọc dependency/config trong spec trước khi chạy:

```bash
cd apps/web
npm ci
npm test -- --reporter=dot
npm run lint
npm run typecheck
npm run build
# Root: python3 -m unittest discover -s scripts/tests -v
```

Sau merge dev: Web 219/219 PASS, scripts 20/20 PASS. GitHub CI của 0fe1949: 11/11 checks success, gồm required-ci và Docker/PostgreSQL smoke. Hồ sơ trước merge ghi web 211; giữ đúng source/run, không trộn thành một lần chạy.

Lệnh cần service, credential hoặc opt-in không mặc nhiên chạy được trên máy chưa cấu hình. [Spec triển khai](https://github.com/KLTN-2023-HCMSU/Scam-Risk-Detector/blob/0fe194976260bba5b7d82553ee0d51a5b644fa7c/docs/specs/T-W13_ui_xss_pdf_demo.md) · [Hồ sơ kiểm chứng](https://github.com/KLTN-2023-HCMSU/Scam-Risk-Detector/blob/0fe194976260bba5b7d82553ee0d51a5b644fa7c/docs/specs/T-W13_phone_https_acceptance.md). [Chi tiết phạm vi và PR](BCTN_T-W13_UI_XSS_PDF_Demo_Thang.md).

## 5. Ca không đạt hoặc đạt một phần

Thắng chưa có biên bản tổng duyệt người thật; Kiên chưa ký cross-test; chưa trình diễn/nghiệm thu release. HTTPS local trước đó rule-only, không nhận paid/live AI hay SMTP từ bằng chứng đó. Runtime snapshot sau merge dev có SMTP code, chưa có phép đo live delivery riêng. Các ca chưa thực hiện được giữ pending, không đánh PASS dựa vào việc có code/schema. BCKT này tổng hợp nguồn cũ; không có lượt chạy ứng dụng mới trong repo document.

## 6. Số liệu chấm điểm / hiệu năng

Không áp dụng — không có benchmark detector/độ chính xác hoặc tập test độc lập được phép công bố từ bộ bằng chứng này. Test counts là số liệu regression của snapshot ghi trong nguồn, không phải số test mới của tuần.

## 7. Kết luận

Có bằng chứng tự kiểm theo bảng; trạng thái merge xem BCTN, trạng thái nghiệm thu cần Kiên/nhóm xác nhận. Không tự điền chữ ký Kiên, G4 hoặc review mà Thắng chưa trực tiếp thực hiện.
