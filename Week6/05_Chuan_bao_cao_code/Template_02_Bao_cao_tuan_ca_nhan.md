# Báo cáo tuần W[NN] — [Tên]

> **Mẫu số 02 · Báo cáo tuần / sprint cá nhân** — nộp Chủ nhật 21:00 cuối mỗi chu kỳ.
> **Chu kỳ là 1 tuần** trong giai đoạn MVP (07/09 – 01/11/2026), **2 tuần** từ tháng 11/2026 trở đi. Trong 8 tuần nhịp chậm (28/12/2026 – 21/02/2027) thì không nộp.
> Copy file này, đổi tên thành `Tuan_W<NN>_<Ten>.md`, đặt vào `document/WeekNN/BaoCao/`.
> **Giữ trong 1 trang.** Báo cáo tuần dài là báo cáo tuần không ai đọc.
> Xoá các khối hướng dẫn `>` sau khi điền.

---

| Trường | Giá trị |
|---|---|
| **Tuần** | W[NN] · [DD/MM] – [DD/MM/2026] |
| **Sprint** | [S2] |
| **Người** | [Tên] |
| **Giờ làm thực tế** | [~16 giờ] |

---

## 1. Deadline tuần này — đối chiếu timeline

> Chép các dòng của mình từ [`Timeline_chi_tiet_theo_tuan.md`](../../Week6/04_Timeline_chi_tiet/Timeline_chi_tiet_theo_tuan.md) mục 3, rồi tự chấm.

| Mã | Việc theo timeline | Hạn | Trạng thái | PR |
|---|---|---|---|---|
| [H04] | [RBAC hai vai trò + chặn `/v1/admin/*`] | [02/10] | ✅ Xong đúng hạn | [#18] |
| [H04] | [Middleware Next.js chặn `/admin/*`] | [02/10] | ⏳ Trễ 2 ngày | [#19] |
| [H07] | [Rate limit đăng nhập] | [09/10] | 🔜 Tuần sau | — |

**Ký hiệu:** ✅ xong đúng hạn · ⏳ trễ (ghi rõ số ngày) · ❌ chưa làm · 🔜 chưa tới hạn

---

## 2. Đã làm gì — mô tả cho người khác hiểu

> 3–6 gạch đầu dòng. Mỗi dòng: **làm gì** → **kết quả kiểm chứng được**. Không viết "đã research", "đã tìm hiểu" mà không kèm kết quả cụ thể.

- [Thêm hai vai trò `USER`/`ADMIN` vào JWT payload và bộ lọc chặn `/v1/admin/*` → user thường gọi route admin nhận `403 FORBIDDEN_ROLE`, có test khoá lại.]
- [Viết middleware Next.js chặn `/admin/*` phía client → truy cập bằng tài khoản thường bị chuyển về trang chủ, không nháy giao diện admin.]
- [...]

---

## 3. Đã đọc / review code của ai

> Buộc phải điền. Mỗi tuần phải review ít nhất 1 PR của người được ghép cặp, để không ai chỉ biết phần của mình.

| PR đã review | Của ai | Kết luận | Phát hiện đáng chú ý |
|---|---|---|---|
| [#17] | [Khải] | [Approve] | [SSRF guard chưa kiểm tra IP ở bước redirect thứ 2 — đã báo, Khải sửa rồi] |

---

## 4. Đang bị chặn / đang chặn người khác

> Cột thứ hai quan trọng hơn cột thứ nhất. Biết mình đang chặn ai thì mới biết việc nào phải làm trước.

| Loại | Nội dung | Với ai | Từ ngày | Cách gỡ |
|---|---|---|---|---|
| Tôi đang bị chặn | [Chưa có bảng `risk_entities` nên chưa test được luồng duyệt báo cáo] | [Kiên] | [12/10] | [Dùng dữ liệu seed tạm, Kiên hạn 09/10] |
| Tôi đang chặn người khác | [H04 RBAC trễ 2 ngày] | [Kiên — R04, Thắng — T06] | [02/10] | [Xong trong ngày 04/10, đã báo trong nhóm] |

**Nếu không có gì:** ghi `Không có` — nhưng kiểm tra lại mục 5 phần "12 điểm phối hợp chéo" của kế hoạch trước khi ghi vậy.

---

## 5. Số liệu tuần

| Chỉ số | Giá trị |
|---|---|
| Số commit | [12] |
| Số PR mở / đã merge | [2 / 2] |
| Số test thêm mới | [9] |
| CI hiện tại | [xanh] |
| Số tính năng `P0` đóng được trong tuần | [3] |
| Tổng `P0` đã đóng / tổng `P0` được giao | [11 / 32] |

---

## 6. Kế hoạch tuần sau

> Chép từ timeline, cộng thêm phần nợ của tuần này.

| Mã | Việc | Hạn | Ghi chú |
|---|---|---|---|
| [H07] | [Rate limit + Idempotency-Key] | [09/10] | |
| [H04] | [Nợ tuần này: middleware Next.js] | [05/10] | Trả nợ trước, làm việc mới sau |

---

## 7. Rủi ro tự nhận thấy

> Cảnh báo sớm hiệu quả hơn báo cáo trễ. Không có thì ghi `Không có`.

| Rủi ro | Mức | Dấu hiệu | Đề xuất |
|---|---|---|---|
| [Idempotency phức tạp hơn ước lượng] | [Trung bình] | [Đã mất 4 giờ mới xong phần hash request body] | [Nếu tới thứ 4 chưa xong thì bỏ phần `409 conflict`, chỉ giữ chống trùng] |

---

## 8. Báo cáo tính năng đã nộp tuần này

| Mã nhóm | File báo cáo |
|---|---|
| [H04] | [`BCTN_H04_RBAC_Hung.md`](BCTN_H04_RBAC_Hung.md) |

**Nếu tuần này không đóng trọn nhóm nào:** ghi `Không có — đang làm dở [mã nhóm], dự kiến đóng ở W[NN]`.

---

*Nộp lúc: [DD/MM/2026 21:00] · Mẫu: Báo cáo tuần v1.0*
