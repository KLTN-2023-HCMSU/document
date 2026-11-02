# Báo cáo tuần W12 — Thắng

| Trường | Giá trị |
|---|---|
| Tuần | W12 · 20/10–26/10/2026 |
| Sprint | Chưa xác nhận theo lịch nhóm |
| Người | Thắng |
| Giờ làm thực tế | Chưa xác nhận — không suy từ số commit |

## 1. Deadline tuần này — đối chiếu timeline

| Mã | Phạm vi kế hoạch / thực tế | Hạn kế hoạch | Trạng thái | PR |
|---|---|---|---|---|
| T-MVP04 | Text Scan, QR/VietQR, AI, History và Export | 26/10/2026 | Bổ sung hồi cứu; không tự nhận hoàn thành/merge đúng hạn | [#24](https://github.com/KLTN-2023-HCMSU/Scam-Risk-Detector/pull/24) |

[Timeline V2.1](../../../plan/v2.1/Timeline_full_Anti_Scam_2026_2027_V2.1.md); sai khác task/độ hoàn thiện từng tuần xem BCTN.

## 2. Đã làm gì — mô tả cho người khác hiểu

- Text Worker → AI Worker → Core được thử trong stack HTTP/Redis/PostgreSQL/Rabbit local thực với provider synthetic.
- QR URL/VietQR có parent-child scan, owner isolation và idempotent replay.
- History có filter/paging theo owner; result có HMAC report proof, verifier và bản in HTML/A4.

## 3. Đã đọc / review code của ai

Chưa ghi nhận bằng chứng review do Thắng trực tiếp thực hiện trong tuần. Cần Thắng bổ sung PR/kết luận; không lấy việc đọc code hoặc báo cáo tự động thành review/approve cá nhân.

## 4. Đang bị chặn / đang chặn người khác

Chờ review/handoff theo [BCTN](BCTN_T-MVP04_Text_QR_AI_History_Export_Thang.md); phần nghiệm thu cần Kiên xác nhận. Chưa xác nhận ai bị Thắng chặn hoặc ngày bắt đầu, không tự điền “không có”.

## 5. Số liệu tuần

Commit/test **thêm mới trong tuần**, giờ làm và P0 đã nghiệm thu: chưa xác nhận. PR liên quan và trạng thái hiện tại ghi trong BCTN; không coi đó là số PR mở/merge đúng kỳ. CI lịch sử cần đối chiếu từng PR/run; không dùng CI W13 thay số liệu của tuần này.

## 6. Kế hoạch tuần sau

Hoàn tất phần còn nợ trong BCTN, bàn giao theo timeline/task kế tiếp; đối chiếu task tuần sau trước khi nhận hoàn thành G3/G4.

## 7. Rủi ro tự nhận thấy

Lịch sử merge/QA không trùng tuần kế hoạch; fixture/sandbox dễ bị nhầm với E2E hoặc nguồn thực. Giữ riêng các snapshot và phần chưa xác nhận.

## 8. Báo cáo tính năng bổ sung

[BCTN](BCTN_T-MVP04_Text_QR_AI_History_Export_Thang.md) · [BCKT](BCKT_T-MVP04_Text_QR_AI_History_Export_Thang.md) · [Mục lục tuần](README.md).

Bổ sung ngày 29/10/2026; chưa xác nhận thời điểm nộp/phê duyệt. Không nhận đây là báo cáo đã nộp vào cuối tuần gốc.
