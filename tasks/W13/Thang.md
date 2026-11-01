# W13 — Thắng: UI/UX Audit, Export PDF Validation & Live Demo Script

**Owner:** Thắng
**Cross-tester:** Kiên
**Primary goal:** Web UI Zero Defect, PDF Export Validated, Live Demo Script Ready
**Priority:** P0
**Milestone:** Release v0.1.0 (30/10/2026)

## Quy Tắc Tuần 13

TUYỆT ĐỐI KHÔNG THÊM TÍNH NĂNG MỚI. Rà soát trải nghiệm người dùng, kiểm định tính an toàn giao diện, kiểm thử xuất PDF và chuẩn bị kịch bản trình chiếu Live Demo.

---

## UI-REG01 — Rà Soát Giao Diện & Trải Nghiệm Người Dùng (UI/UX Audit)

* Kiểm tra độ tương thích giao diện trên Desktop và Mobile Viewport.
* Đảm bảo đồng bộ hệ thống Design System: Màu sắc cảnh báo rủi ro (Xanh - SAFE, Vàng - CAUTION, Đỏ - RISKY/CRITICAL).
* Rà soát các trạng thái biên: Loading skeleton khi đang quét, thông báo lỗi mạng, giao diện khi worker trả về kết quả degraded.

## SEC-UI01 — Kiểm Định An Toàn Dữ Liệu Hiển Thị (Safe Rendering & XSS)

* Kiểm tra bảo mật đầu ra: Nội dung tin nhắn lừa đảo hoặc URL độc hại hiển thị trên trang Result Detail và Report Detail phải được escape/sanitize an toàn, ngăn chặn 100% tấn công Stored XSS.

## EXP-01 — Kiểm Định Tính Năng Xuất Báo Cáo PDF / HTML

* Kiểm thử tính năng xuất PDF cho cả 5 loại hình quét (URL, Text, Phone, Bank, QR).
* Đảm bảo file PDF xuất ra có định dạng đẹp, không bị tràn lề, đầy đủ thông tin bằng chứng, thời gian quét và mã băm xác thực.

## DEMO-RUN — Kịch Bản Thuyết Trình & Live Demo Release v0.1.0 (30/10)

* Soạn thảo Slide thuyết trình tổng quan dự án và kiến trúc đạt được ở mốc v0.1.0.
* Soạn Demo Script từng phút: Thao tác mẫu quét URL lừa đảo thật, quét tin nhắn giả mạo trúng thưởng, quét mã VietQR mạo danh.
* Trực tiếp trình diễn giao diện Web trong buổi nghiệm thu phát hành 30/10.
* Viết chương báo cáo đồ án tốt nghiệp phần: Kiến trúc Ứng dụng Web Client, Tích hợp Async AI Worker và Thiết kế Trải nghiệm Người dùng (UI/UX).

## Definition of Done

* [ ] Toàn bộ màn hình Web UI không có lỗi layout hoặc console errors.
* [ ] Kiểm thử chống XSS an toàn tuyệt đối.
* [ ] Xuất PDF hoạt động mượt mà trên các trình duyệt Chrome/Firefox/Safari.
* [ ] Slide và Kịch bản Demo sẵn sàng trước 29/10.
* [ ] Kiên kiểm thử chéo và xác nhận phê duyệt.
