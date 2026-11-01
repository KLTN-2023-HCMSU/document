# W13 — Kiên: Core Engine Stress-Test, Data Integrity & Scan Validation

**Owner:** Kiên
**Cross-tester:** Hùng
**Primary goal:** Fault-Tolerance & Data Integrity Across 5 Scan Flows
**Priority:** P0
**Milestone:** Release v0.1.0 (30/10/2026)

## Quy Tắc Tuần 13

TUYỆT ĐỐI KHÔNG THÊM TÍNH NĂNG MỚI. Tập trung kiểm thử chịu lỗi, kiểm tra tính toàn vẹn dữ liệu và đối soát kết quả chấm điểm rủi ro.

---

## CORE-REL01 — Kiểm Thử Khả Năng Chịu Lỗi & Phục Hồi (Fault Tolerance)

* Giả lập trường hợp Worker chết hoặc mất kết nối RabbitMQ đột ngột: Xác minh Core Engine tự động chuyển trạng thái scan sang FAILED hoặc DEGRADED, tuyệt đối không để scan bị treo vô hạn ở PROCESSING.
* Giả lập AI Worker phản hồi muộn hoặc lỗi mạng: Đảm bảo Completion Barrier kích hoạt fallback chấm điểm từ Rule Engine an toàn.

## CORE-REL02 — Kiểm Thử Hồi Quy 5 Luồng Quét (URL, Text, Phone, Bank, QR)

* Chạy bộ kiểm thử tự động và thủ công cho toàn bộ 5 luồng quét trên môi trường Staging.
* Đối soát tính nhất quán của bảng điểm Risk Score và công thức tính trọng số Risk Fusion V3.3.
* Đảm bảo mọi kết quả trả về đúng chuẩn contract OpenAPI và không thiếu sót trường dữ liệu.

## DATA-01 — Tính Idempotent & Chống Trùng Lặp Message

* Kiểm tra kịch bản RabbitMQ gửi lặp lại cùng một WorkerResult: Đảm bảo Core không bị cộng dồn điểm 2 lần và không ghi nhận duplicate analysis signals.
* Kiểm tra tính toàn vẹn của dữ liệu Threat Intelligence và Seed Data nạp vào hệ thống.

## DOC-02 — Tài Liệu ĐATN Phần Core Engine

* Chuẩn bị phần giải trình kỹ thuật cho buổi nghiệm thu sản phẩm 30/10.
* Viết chương báo cáo đồ án tốt nghiệp phần: Mô hình xử lý bất đồng bộ qua Message Queue, Thiết kế Completion Barrier và Thuật toán chấm điểm rủi ro đa nguồn (Risk Fusion).

## Definition of Done

* [ ] Không còn trường hợp scan bị kẹt vĩnh viễn ở PROCESSING.
* [ ] Toàn bộ 5 loại scan chạy ổn định 100% trên Staging.
* [ ] Idempotency test PASS không bị double counting điểm số.
* [ ] Hùng kiểm thử chéo và xác nhận phê duyệt.
