# Báo cáo tuần W07 — Khải

| Trường | Giá trị |
|---|---|
| **Tuần** | W07 (thư mục tài liệu) · **14/09 – 20/09/2026** · tương ứng **W02** của [timeline](../../Week6/04_Timeline_chi_tiet/Timeline_chi_tiet_theo_tuan.md) |
| **Sprint** | Giai đoạn 1 · MVP · nhịp tuần |
| **Người** | Khải — URL, tên miền, mạng, threat intel, hạ tầng & vận hành (K01–K12) |
| **Giờ làm thực tế** | _(Khải tự điền)_ |

---

## 1. Deadline tuần này — đối chiếu timeline

| Mã | Việc theo timeline | Hạn | Trạng thái | PR |
|---|---|---|---|---|
| K11 | Dockerfile `api` | T5 17/09 | ✅ Xong — đã có sẵn từ commit dựng khung 06/09 của Hùng, tuần này chỉ rà lại (multi-stage, chạy user non-root) | — |
| K11 | Endpoint `/health` + `/ready` trên cả ba service | T5 17/09 | ✅ Xong trước hạn 1 ngày — xem ghi chú (a) | [#2](https://github.com/KLTN-2023-HCMSU/Scam-Risk-Detector/pull/2) · [#4](https://github.com/KLTN-2023-HCMSU/Scam-Risk-Detector/pull/4) |
| K12 | GitHub Actions chạy `pytest` + `mvn test` trên mỗi PR | T6 18/09 | ✅ Xong trước hạn 2 ngày — xem ghi chú (b) | [#4](https://github.com/KLTN-2023-HCMSU/Scam-Risk-Detector/pull/4) |
| K12 | Chặn merge khi test đỏ | T6 18/09 | ❌ **Bị chặn** — cần cả nhóm quyết, xem mục 4 | — |

**(a) Về "cả ba service".** Kiến trúc v2 mục 5 định nghĩa ba service backend: `api`, `worker`, `scan-engine`. **`worker` thuộc mốc M4 nên chưa tồn tại.** Hai service đang có đều đã đủ cặp probe. `worker` dùng chung codebase với `api`, chỉ khác entrypoint, nên sẽ thừa hưởng `OpsController` sẵn mà không phải viết lại. Nói "xong 3/3" là không chính xác; nói đúng là **"xong trên mọi service đang tồn tại"**.

**(b) CI trước tuần này chưa từng xanh một lần nào.** Chi tiết ở mục 2.

---

## 2. Đã làm gì

- **Tách liveness khỏi readiness ở `scan-engine`** ([PR #2](https://github.com/KLTN-2023-HCMSU/Scam-Risk-Detector/pull/2), đã merge). `/health` cũ đang gọi `get_ruleset()` — tức là làm việc của readiness. Hậu quả vận hành thật: `rules.json` hỏng → liveness fail → orchestrator restart container → restart không sửa được file hỏng → **crash-loop vô hạn**. Nay `/health` là liveness thuần, `/ready` nạp ruleset và trả `503 OUT_OF_SERVICE`. Healthcheck compose trỏ sang `/ready`.

- **Thêm `/health` + `/ready` cho `api`** ([PR #4](https://github.com/KLTN-2023-HCMSU/Scam-Risk-Detector/pull/4)). Cùng đường dẫn và cùng hình dạng JSON với `scan-engine`, để Nginx và probe orchestrator chỉ phải biết **một** hợp đồng. Thành phần tính vào readiness khai trong `application.yml` chứ không hardcode — thêm Redis khi H07 dùng tới chỉ là sửa một dòng.

- **Kiểm chứng bằng docker compose thật, không chỉ bằng unit test.** Dừng `postgres`: `/ready` trả `503 {"failing":{"db":"DOWN"}}`, `/health` vẫn `200`, container thành `(unhealthy)` nhưng **không restart** — log chỉ có đúng một lần `Started ApiApplication`. Bật lại DB thì tự về `(healthy)`. Đây là bằng chứng crash-loop đã tránh được.

- **Phát hiện và sửa lỗi khiến CI chưa từng chạy được test Java.** `services/api/mvnw` nằm trong git với mode `100644`, thiếu cờ thực thi, nên `./mvnw -B test` chết ngay với **exit code 126**. Dockerfile né được vì có sẵn `RUN chmod +x mvnw`, nên lỗi chỉ lộ ở CI. **Cả 7 lần chạy CI từ 06/09 đều đỏ vì lý do này — nghĩa là hai PR trước đã merge vào `main` mà phần Java chưa hề được kiểm.** Sau khi sửa, CI xanh toàn bộ lần đầu tiên trong lịch sử repo.

- **Thêm job CI cho `apps/web`.** CI trước đây không đụng gì tới web, nên 40 test của Thắng chưa từng chạy lần nào. Job dùng `next build` thay cho `npm run typecheck` vì `layout.tsx` xài `LayoutProps<"/">` — type Next sinh lúc build, `tsc` trần trên checkout sạch sẽ báo `TS2304`.

- **Bỏ hardcode cổng host của `postgres` và `redis` trong compose.** Máy nào đã chạy sẵn Postgres thì `docker compose up` chết ở `Bind for 0.0.0.0:5432 failed` — đúng lỗi gặp khi kiểm chứng. Đây là vi phạm quy tắc 3 trong `CONTRIBUTING.md` ("không hardcode cổng — máy mỗi người mỗi khác").

---

## 3. Đã đọc / review code của ai

| PR đã review | Của ai | Kết luận | Phát hiện đáng chú ý |
|---|---|---|---|
| [#1](https://github.com/KLTN-2023-HCMSU/Scam-Risk-Detector/pull/1) | Thắng | Đã merge vào `main` sáng 16/09 | Merge lúc CI đang đỏ — nhưng đỏ là do lỗi `mvnw` của tôi, không phải lỗi code Thắng. Đã sửa ở PR #4 |

**Tự nhận thiếu sót:** theo bảng ghép cặp review chéo trong timeline, **tôi phải review PR của Hùng**, còn PR của tôi thì Thắng review. Tuần này Hùng không mở PR nào nên tôi không có gì để review; PR #2 của tôi cũng chưa ai review mà đã merge. **Cặp review chéo tuần này coi như không hoạt động.**

---

## 4. Đang bị chặn / đang chặn người khác

| Loại | Nội dung | Với ai | Từ ngày | Cách gỡ |
|---|---|---|---|---|
| Tôi đang bị chặn | **"Chặn merge khi test đỏ" (K12, `P0`) không làm được.** Branch protection đòi GitHub Pro cho repo private — API trả `403 Upgrade to GitHub Pro or make this repository public` | Cả nhóm + thầy | 16/09 | Chọn một trong ba: **(1)** nâng gói GitHub Pro, **(2)** để repo công khai, **(3)** chấp nhận quy ước tay "không merge khi đỏ". Cần chốt trong buổi họp gần nhất |
| Tôi đang chặn người khác | Không có. K11/K12 là hạ tầng, không ai đợi đầu ra của nó để code tiếp | — | — | — |

---

## 5. Số liệu tuần

| Chỉ số | Giá trị |
|---|---|
| Số commit | 3 commit nội dung + 2 merge commit |
| Số PR mở / đã merge | 3 mở / 1 đã merge (#2) · #3 đóng và thay bằng [#4](https://github.com/KLTN-2023-HCMSU/Scam-Risk-Detector/pull/4) vì đặt tên định danh lai Anh-Việt · #4 đang chờ review |
| Số test thêm mới | **9** — 2 ở `scan-engine`, 7 ở `api` |
| Tổng test toàn dự án | `api` 27/27 · `scan-engine` 25/25 · `web` 40/40 — tất cả xanh |
| CI hiện tại | 🟢 **Xanh** — lần đầu tiên kể từ 06/09. Trước đó 7/7 lần chạy đều đỏ |
| Số tính năng `P0` đóng được trong tuần | 2 (`/health`+`/ready`; CI chạy test mỗi PR) |
| Tổng `P0` đã đóng / tổng `P0` được giao | **5 / 27** — K11 đóng 4/4 `P0`, K12 đóng 1/2 `P0` |

---

## 6. Kế hoạch tuần sau (W03 timeline · 21/09 – 27/09)

| Mã | Việc | Hạn | Ghi chú |
|---|---|---|---|
| K01 | Chuẩn hoá URL (thêm scheme, hạ chữ thường host, bỏ port mặc định, sắp query) + tách thành phần | T6 25/09 | Nghiệm thu: bộ test 15 URL biến thể ra cùng dạng chuẩn |
| K12 | Nợ tuần này: chốt phương án chặn merge khi đỏ | T2 21/09 | Trả nợ trước, cần cả nhóm quyết chứ tôi không tự làm được |
| — | Review PR của Hùng (H01/H02) theo cặp ghép chéo | Trong tuần | Tuần này chưa làm được vì Hùng chưa mở PR |

---

## 7. Rủi ro tự nhận thấy

| Rủi ro | Mức | Dấu hiệu | Đề xuất |
|---|---|---|---|
| **CI đỏ suốt 10 ngày mà không ai nhận ra** | **Cao** | 7/7 lần chạy đỏ từ 06/09, hai PR vẫn merge bình thường | Đã sửa nguyên nhân. Nhưng gốc rễ là *không ai nhìn CI* — nên quy ước bắt buộc: **người review phải xem CI trước khi Approve** |
| Cặp review chéo không hoạt động | Trung bình | PR #2 merge mà không ai review; PR #1 merge lúc CI đỏ | Đưa vào standup thứ 2: mỗi người báo đã review PR của ai tuần trước |
| `worker` chưa tồn tại nhưng timeline đã tính là "service thứ ba" | Thấp | Mốc K11 ghi "cả ba service" trong khi repo chỉ có hai | Không cần xử lý ngay — M4 mới sinh `worker`. Chỉ cần nói đúng khi báo cáo |
| Ngày bảo vệ trong timeline sai gần 8 tuần | **Cao** | Timeline ghi bảo vệ ~15/09/2027, lịch khoa cho K23 đợt 1 là **19–31/07/2027** | Đã căn lại toàn bộ timeline ngày 16/09. Cần cả nhóm xác nhận nhóm thuộc **K23 đợt 1** |

---

## 8. Báo cáo tính năng đã nộp tuần này

**Không có — đang làm dở K11, dự kiến đóng ở giai đoạn sau.** K11 mới xong phần probe và cấu hình; còn `docker-compose.prod.yml` + Nginx + TLS, sao lưu/phục hồi PostgreSQL, deploy VPS (đều `P1`, lịch tháng 5/2027). BCTN_K11 sẽ nộp khi nhóm tính năng đóng trọn.

---

*Nộp lúc: 16/09/2026 · Mẫu: Báo cáo tuần v1.0*
