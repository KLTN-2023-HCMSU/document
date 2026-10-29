# Thắng — Bộ báo cáo bổ sung W09–W13

Soạn 29/10/2026 theo templates 01/02/04 của nhóm, không nhận đã nộp đúng hạn hoặc đã được nghiệm thu. Bộ báo cáo này phân biệt **kế hoạch**, **code có bằng chứng**, **merge** và **nghiệm thu**. “All” được xử lý trước cho các phần W09–W13 trong phạm vi công việc đã truy vết; không tự nhận đã kiểm kê mọi việc trước W09.

| Tuần | Bộ báo cáo | Nội dung |
|---|---|---|
| W09 | [Thắng](Week9/thang/README.md) | Text Normalization và Worker/Result Contracts |
| W10 | [Thắng](Week10/thang/README.md) | Result UI và Text Scan Foundation (R02/S02) |
| W11 | [Thắng](Week11/thang/README.md) | Text extraction, Result Integration và AI runtime handoff |
| W12 | [Thắng](Week12/thang/README.md) | Text Scan, QR/VietQR, AI, History và Export |
| W13 | [Thắng](Week13/thang/README.md) | UI/UX Audit, XSS, PDF và Live Demo |

## Nguồn chuẩn và cách đọc

- [Quy định báo cáo](../templates/00_Quy_dinh_bao_cao_code.md), [Template 01](../templates/Template_01_Bao_cao_tinh_nang.md), [Template 02](../templates/Template_02_Bao_cao_tuan_ca_nhan.md), [Template 04](../templates/Template_04_Bao_cao_kiem_thu.md).
- [Timeline V2.1](../plan/v2.1/Timeline_full_Anti_Scam_2026_2027_V2.1.md); task W09/W10/W11 có trong repo, W12/W13 được chủ sở hữu cung cấp khi triển khai. W10/W11 có khác biệt giữa timeline và implementation slice, được giữ rõ trong từng báo cáo.
- Link source code/spec ghim tại `0fe194976260bba5b7d82553ee0d51a5b644fa7c`; đây là checkout tổng hợp mới, không chứng minh mọi file đã tồn tại tại hạn cuối từng tuần. BCKT chỉ trích kết quả lịch sử có trong spec/run, không nhận đã chạy lại ứng dụng khi viết document.
- [CI W13](https://github.com/KLTN-2023-HCMSU/Scam-Risk-Detector/actions/runs/40876606905): 11/11 success tại source `0fe1949`. PR #24/#26 đang mở; #7/#11/#12/#13 đã merge theo bảng riêng. Không dùng CI code để nhận CI repo document đã chạy.

## Thông tin cần Thắng/nhóm bổ sung

Giờ làm thực tế, Sprint, commit/test mới theo từng kỳ, PR đã trực tiếp review, thời điểm hoàn thành, tổng duyệt và chữ ký Kiên còn chưa xác nhận. Không có thống kê accuracy/latency/F1 hoặc P0 nghiệm thu được tự tạo từ test counts. Tài liệu chưa được lead/giảng viên approve.

## Nơi lưu và phạm vi bản sao

Theo cấu trúc đang dùng trong repo: `weekly-reports/WeekN/thang/`; tên file theo template. Báo cáo liên kết đến code/contract/business rules gốc, không duy trì một bản sao quy tắc nghiệp vụ. W13 có bản slide HTML tự chứa và bản thảo chương báo cáo; binary PDF/ảnh QA dẫn về source snapshot thay vì sao chép toàn bộ vào document. ADR chỉ bổ sung nếu có quyết định cần lead xét duyệt riêng, không tạo quyết định nhóm giả.
