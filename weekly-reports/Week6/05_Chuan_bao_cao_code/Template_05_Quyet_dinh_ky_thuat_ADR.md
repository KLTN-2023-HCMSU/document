# ADR-[số] · [Tên quyết định]

> **Mẫu số 05 · Quyết định kỹ thuật (Architecture Decision Record)** — viết khi:
> - Phải chọn giữa nhiều phương án và lựa chọn không hiển nhiên
> - Làm khác với [`Kien_truc_he_thong_v2.md`](../01_Kien_truc_v2/Kien_truc_he_thong_v2.md)
> - Quyết định ảnh hưởng tới phần của người khác
>
> Copy file này, đổi tên thành `ADR_<số>_<Ten_quyet_dinh>_<Nguoi>.md`.
> **Đánh số nối tiếp ADR-01…ADR-08 đã có trong tài liệu kiến trúc — bắt đầu từ ADR-09.**
> Giữ trong 1 trang. ADR dài là ADR không ai đọc lại.

---

| Trường | Giá trị |
|---|---|
| **Số** | ADR-[09] |
| **Tiêu đề** | [Chọn thư viện parse VietQR] |
| **Người đề xuất** | [Tên] |
| **Ngày** | [YYYY-MM-DD] |
| **Trạng thái** | ☐ Đề xuất ☐ Đã chốt ☐ Bị thay thế bởi ADR-[..] |
| **Liên quan tới nhóm** | [T03] |
| **Ảnh hưởng tới ai** | [Thắng, Kiên] |

---

## 1. Bối cảnh

> Chuyện gì đang xảy ra khiến phải quyết định? Nêu **sự kiện**, không nêu ý kiến. 3–6 câu.

[Ví dụ: "T03 cần đọc chuỗi VietQR để lấy mã ngân hàng, số tài khoản, số tiền và nội dung chuyển khoản. VietQR theo chuẩn EMVCo — dữ liệu là chuỗi TLV lồng nhau. Tự parse thì phải xử lý đúng thứ tự trường và checksum CRC16. Trong hệ sinh thái Java có 2 thư viện, cả hai đều ít người dùng và cập nhật lần cuối cách đây hơn 1 năm."]

---

## 2. Ràng buộc

> Những thứ **không đàm phán được**, quyết định phải nằm trong khuôn này.

| # | Ràng buộc | Nguồn |
|---|---|---|
| 1 | [Phải viết bằng Java — tra cứu tài khoản là truy cập DB] | [ADR-03] |
| 2 | [Không được gọi ra Internet từ `api`] | [ADR-03] |
| 3 | [Phải xong trước 13/11] | [Timeline W10] |

---

## 3. Các phương án

| Phương án | Mô tả | Ưu | Nhược |
|---|---|---|---|
| **A** | [Dùng thư viện X] | [Nhanh, có sẵn test] | [Cập nhật lần cuối 2024, không rõ có duy trì] |
| **B** | [Tự viết parser EMVCo TLV] | [Kiểm soát hoàn toàn, hiểu rõ để viết vào báo cáo] | [Mất ~1.5 ngày, phải tự viết CRC16] |
| **C** | [Gọi dịch vụ ngoài để giải mã] | [Không phải viết gì] | [Vi phạm ràng buộc 2; gửi số tài khoản ra ngoài — vi phạm H08] |

---

## 4. Quyết định

> Một câu dứt khoát. Không "có thể", không "chắc là".

**Chọn phương án [B] — tự viết parser EMVCo TLV.**

**Vì:** [Ba lý do. Bám vào ràng buộc ở mục 2, đừng nói chung chung.]

1. [Phương án C vi phạm hai ràng buộc cứng — loại ngay.]
2. [Cấu trúc TLV của VietQR chỉ có 8 trường cần dùng, không phải toàn bộ chuẩn EMVCo — khối lượng thực tế nhỏ hơn nhiều so với vẻ ngoài.]
3. [Tự viết thì phần "Phân tích cấu trúc VietQR" trở thành nội dung kỹ thuật có giá trị trong báo cáo, thay vì một dòng "dùng thư viện X".]

---

## 5. Hệ quả

| Loại | Nội dung |
|---|---|
| **Tích cực** | [Không thêm phụ thuộc; tự kiểm soát khi VietQR đổi chuẩn; có nội dung viết báo cáo] |
| **Tiêu cực** | [Mất thêm 1.5 ngày; phải tự viết test cho CRC16] |
| **Rủi ro** | [Bỏ sót trường hợp biên của chuẩn EMVCo → parse sai QR của một số ngân hàng] |
| **Giảm rủi ro** | [Test với QR thật từ ít nhất 5 ngân hàng khác nhau] |

---

## 6. Ảnh hưởng tới người khác

| Ai | Ảnh hưởng gì | Đã báo chưa |
|---|---|---|
| [Kiên] | [`VietQrParser` nằm trong module dùng chung, R02 có thể tái sử dụng để trích xuất số tài khoản từ văn bản] | ☐ Rồi, ngày [...] |

---

## 7. Khi nào xem lại quyết định này

> Điều kiện cụ thể, không phải "khi cần".

[Ví dụ: "Nếu phát hiện QR của trên 2 ngân hàng parse sai và không sửa được trong 1 ngày, chuyển sang phương án A."]

---

*Người đề xuất: [Tên] · Ngày: [YYYY-MM-DD] · Chốt trong standup ngày: [...] · Mẫu: ADR v1.0*
