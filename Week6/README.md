# Week 6 — Kiến trúc v2, phạm vi, kế hoạch và chuẩn làm việc

**Cập nhật:** 2026-09-07

Đây là tuần chốt toàn bộ nền tảng trước khi cả nhóm bắt đầu code. Năm thư mục dưới đây trả lời năm câu hỏi khác nhau — đọc theo đúng thứ tự này nếu là lần đầu.

| # | Thư mục | Trả lời câu hỏi | Đọc khi nào |
|---|---|---|---|
| 1 | [`01_Kien_truc_v2/`](01_Kien_truc_v2/Kien_truc_he_thong_v2.md) | Hệ thống có những thành phần nào, ranh giới ở đâu, vì sao chọn vậy | Trước khi viết dòng code đầu tiên |
| 2 | [`02_Danh_sach_tinh_nang/`](02_Danh_sach_tinh_nang/Danh_sach_tinh_nang_toan_he_thong.md) | Phải làm những gì — 48 nhóm, 353 tính năng, chia theo `P0`/`P1`/`P2` | Khi cần biết phạm vi của mình |
| 3 | [`03_Ke_hoach_phat_trien/`](03_Ke_hoach_phat_trien/Ke_hoach_phat_trien.md) | Làm theo **thứ tự nào** và vì sao — đồ thị phụ thuộc, đường găng, điểm phối hợp chéo | Khi lập kế hoạch, khi phải chọn việc nào làm trước |
| 4 | [`04_Timeline_chi_tiet/`](04_Timeline_chi_tiet/Timeline_chi_tiet_theo_tuan.md) | **Ai nộp cái gì, vào ngày nào** — 15 tuần, deadline theo từng người | Mỗi thứ 2 đầu tuần, và mỗi khi lỡ hạn |
| 5 | [`05_Chuan_bao_cao_code/`](05_Chuan_bao_cao_code/00_Quy_dinh_bao_cao_code.md) | Viết code xong thì báo cáo thế nào để người khác hiểu và dùng được | Trước khi mở PR đầu tiên |

---

## Ba việc phải làm ngay trong tuần này

| Hạn | Ai | Việc |
|---|---|---|
| **T2 08/09** | Cả nhóm | Chốt 3 giả định ở mục 0 của [timeline](04_Timeline_chi_tiet/Timeline_chi_tiet_theo_tuan.md): ngày bảo vệ, chuyển T04+T08 sang Kiên, số giờ làm được mỗi tuần |
| **T4 09/09** | Hùng | Mock auth — nút chặn lớn nhất của cả dự án (25/48 nhóm đang chờ) |
| **T5 10/09** | Cả nhóm | Chốt 12 hợp đồng API ở mục 7 của [kế hoạch](03_Ke_hoach_phat_trien/Ke_hoach_phat_trien.md), ghi vào `contracts/openapi/` |

---

## Khi bắt đầu code

1. Đọc [`00_Quy_dinh_bao_cao_code.md`](05_Chuan_bao_cao_code/00_Quy_dinh_bao_cao_code.md) — quy tắc đặt tên nhánh, commit, comment, và định nghĩa "xong".
2. Xem ví dụ mẫu [`Vi_du_da_dien_BCTN_H09.md`](05_Chuan_bao_cao_code/Vi_du_da_dien_BCTN_H09.md) để biết một báo cáo đạt chuẩn trông thế nào.
3. Mở PR — template mô tả tự hiện sẵn từ [`.github/pull_request_template.md`](../../.github/pull_request_template.md).
