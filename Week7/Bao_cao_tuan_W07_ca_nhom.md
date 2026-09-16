# Báo cáo tuần W07 — cả nhóm

| Trường | Giá trị |
|---|---|
| **Tuần** | W07 (thư mục tài liệu) · **14/09 – 20/09/2026** · tương ứng **W02** của [timeline](../Week6/04_Timeline_chi_tiet/Timeline_chi_tiet_theo_tuan.md) |
| **Giai đoạn** | GĐ1 · MVP · nhịp tuần · tuần 2/8 |
| **Người soạn** | Khải — 16/09/2026 |
| **Nguồn dữ liệu** | Lịch sử git của cả 3 repo: `Scam-Risk-Detector`, `document`, `Research-Analysis` |

> **Phạm vi của báo cáo này.** Mọi con số dưới đây lấy từ những gì **đã push lên GitHub** tính tới 16/09 11:00. Việc đang làm dở trên máy cá nhân mà chưa push thì báo cáo không nhìn thấy được. Ai có việc như vậy xin bổ sung vào mục 2 trước buổi họp — **đừng để bị ghi nhầm là chưa làm**.

---

## 1. Đối chiếu deadline W02 — hạn 17/09 và 18/09

| Người | Mã | Việc theo timeline | Hạn | Trạng thái |
|---|---|---|---|---|
| **Hùng** | H01 | Đăng ký email + mật khẩu, BCrypt ≥ 10, UNIQUE, validate độ mạnh | T6 18/09 | **Đã giao sớm** — có từ commit 06/09 |
| **Hùng** | H01 | Migration `users` bằng Flyway | T6 18/09 | **Đã giao sớm** — `V1__init_auth.sql` |
| **Khải** | K11 | Dockerfile `api` + `/health` `/ready` trên các service | T5 17/09 | **Xong** trước hạn 1 ngày |
| **Khải** | K12 | GitHub Actions chạy test trên mỗi PR, **chặn merge khi đỏ** | T6 18/09 | **Mới một nửa** — test đã chạy; phần chặn merge bị chặn kỹ thuật, xem mục 4(b) |
| **Kiên** | R02 | Nối `POST /internal/extract` — URL, SĐT, STK, số tiền, OTP | T6 18/09 | **Chưa thấy có gì được push** |
| **Thắng** | T11 | Khung mobile Expo: điều hướng, 4 tab, Secure Storage | T6 18/09 | **Chưa thấy có gì được push** |

**Còn đúng 2 ngày tới hạn 18/09.**

---

## 2. Từng người đã làm gì

### Hùng — không có hoạt động push nào trong tuần

Commit cuối ở repo code là **06/09**, ở repo tài liệu là **09/09**. Tuy nhiên **phần việc W02 của Hùng đã hoàn thành sớm** từ commit dựng khung 06/09: `AuthController`, `AuthService`, `RegisterRequest` và migration `V1__init_auth.sql` đều đã có và 19 test `AuthFlowTest` đang xanh. Đây là lý do `README.md` của repo code ghi H01–H05 là .

Nói cách khác: **Hùng không chậm, Hùng đang chạy trước lịch.** Tuần sau H02 mới là phần nặng của Hùng.

### Khải — 3 commit code, 2 PR, 1 repo tài liệu mới

- **PR #2** (đã merge 15/09) — tách liveness khỏi readiness ở `scan-engine`, thêm `/ready`. `/health` cũ đang gọi `get_ruleset()`, tức là `rules.json` hỏng sẽ làm liveness fail → orchestrator restart → **crash-loop vô hạn**. +2 test.
- **PR #4** (đang chờ review) — `/health` + `/ready` cho `api` cùng đường dẫn và cùng hình dạng JSON với `scan-engine`; healthcheck cho `api` trong compose; bỏ hardcode cổng host của postgres/redis; cập nhật hợp đồng OpenAPI. +7 test.
- **Sửa lỗi khiến CI chưa từng chạy được test Java** — chi tiết ở mục 4(a).
- **Thêm job CI cho `apps/web`** — trước đây CI không đụng gì tới web.
- **Repo [`Research-Analysis`](https://github.com/KLTN-2023-HCMSU/Research-Analysis)** (15/09) — 9 file phân tích từng bài báo (1.178 dòng), bản đồ bài báo → module/rule, và danh sách việc rút ra từ đợt đọc.
- **Căn lại toàn bộ timeline theo lịch khoa** (16/09) — chi tiết ở mục 4(d).

### Kiên — không có hoạt động nào

**Chưa có commit nào trong bất kỳ repo nào kể từ đầu dự án, cũng không có nhánh riêng.** `scan-engine` hiện chỉ có 4 route: `/v1/scan/text`, `/v1/rules/active`, `/health`, `/ready` — **không có `/internal/extract`**.

Đáng chú ý: [`extractor.py`](https://github.com/KLTN-2023-HCMSU/Scam-Risk-Detector/blob/main/services/scan-engine/app/engine/extractor.py) đã có sẵn logic trích xuất, nhưng do **Hùng** viết hôm 06/09. Việc của Kiên ở R02 là nối nó ra thành endpoint — tức là phần khó nhất đã có người làm hộ.

### Thắng — 1 PR được merge, nhưng là việc của tuần trước

**PR #1** merge sáng 16/09 (T12 `RiskResultCard` + trang `/demo/card`, 40 test). Nhưng commit trong đó đề ngày **09/09** — đó là việc của **W01**, không phải sản lượng tuần này.

Việc W02 của Thắng là T11 khung mobile Expo. [`apps/mobile/`](https://github.com/KLTN-2023-HCMSU/Scam-Risk-Detector/tree/main/apps/mobile) hiện **chỉ có đúng một file `README.md`**.

---

## 3. Số liệu cả nhóm

| Chỉ số | Giá trị |
|---|---|
| Người có commit code trong tuần | **1 / 4** (Khải) |
| Commit nội dung vào repo code | 3 — tất cả của Khải |
| PR mở trong tuần | 2 (#2 đã merge, #4 đang chờ review) · #3 đóng và thay bằng #4 |
| Test thêm mới | 9 (2 ở `scan-engine`, 7 ở `api`) |
| Tổng test | `api` 27/27 · `scan-engine` 25/25 · `web` 40/40 — **đều xanh** |
| Trạng thái CI | **Xanh** từ 16/09 — trước đó **7/7 lần chạy đều đỏ** |
| Nhóm tính năng đóng được trong tuần | K11 (4/4 `P0`) · K12 (1/2 `P0`) |

---

## 4. Có gì không ổn — 7 điểm

### (a) Nặng — CI đỏ suốt 10 ngày mà không ai nhận ra

Từ 06/09 tới 16/09, **cả 7 lần chạy CI đều đỏ**. Nguyên nhân: `services/api/mvnw` nằm trong git với mode `100644`, thiếu cờ thực thi, nên bước `./mvnw -B test` chết ngay với **exit code 126**. Dockerfile né được vì có sẵn `RUN chmod +x mvnw`, nên lỗi chỉ lộ ở CI.

**Hệ quả: hai PR đã merge vào `main` mà phần Java chưa hề được CI kiểm.** Đã sửa ở PR #4, CI nay xanh 4/4 job.

Nhưng nguyên nhân gốc không phải cái cờ thực thi — mà là **không ai nhìn CI trước khi merge**.

> **Đề xuất:** người review bắt buộc xem trạng thái CI trước khi Approve. Thêm dòng này vào checklist review chéo trong [quy định báo cáo code](../Week6/05_Chuan_bao_cao_code/00_Quy_dinh_bao_cao_code.md) mục 7.

### (b) Nặng — "chặn merge khi test đỏ" không làm được — cần cả nhóm quyết

Đây là một `P0` của K12. Branch protection đòi **GitHub Pro** cho repo private; API trả `403 Upgrade to GitHub Pro or make this repository public`.

> **Ba lựa chọn, cần chốt trong buổi họp gần nhất:** (1) nâng gói GitHub Pro · (2) để repo công khai · (3) chấp nhận quy ước tay. Không quyết thì `P0` này treo vô thời hạn.

### (c) Nặng — hai trong bốn người không có sản lượng, và M2 đang bị đe doạ

Kiên và Thắng đều chưa push gì cho việc W02, hạn còn 2 ngày. Nghiêm trọng hơn là **nhìn sang tuần sau**:

- **Kiên** sẽ có **3 việc** cùng hạn 25/09 (R02 nợ lại + R01 hai phần), trong khi tới giờ sản lượng là 0.
- Mốc **nghiệm thu M2 ngày 27/09** yêu cầu chạy được `POST /v1/scan/text` bằng token thật và ra bằng chứng theo rule — **đó chính là R01 của Kiên**.

**Kiên đang nằm trên đường găng của M2.** Nếu R01 không xong thì M2 trượt, kéo theo cả lịch GĐ1 vốn đã không còn chỗ giãn.

> **Đề xuất:** hỏi thẳng Kiên trong standup thứ 2 xem có đang bị chặn gì không. Timeline có sẵn nguyên tắc *"ai xong việc sớm thì giúp người đang chậm"* — Hùng đang chạy trước lịch, là người hỗ trợ được.

### (d) Nặng — ngày bảo vệ trong timeline sai gần 8 tuần — đã sửa, cần xác nhận

Timeline bản 07/09 giả định bảo vệ ~15/09/2027 và nộp bản cuối 27/08/2027. Lịch năm học 2026–2027 của khoa, **cột K23 đợt 1**: nộp đơn bảo vệ **28/06 – 03/07/2027**, phản biện **12 – 17/07/2027**, bảo vệ **19 – 31/07/2027**. Ngày nộp cũ rơi **sau khi đợt bảo vệ đã đóng gần 4 tuần**.

Đã căn lại toàn bộ GĐ3–GĐ7 ngày 16/09. Hệ quả nặng nhất: **quỹ thời gian cho kiểm thử + viết báo cáo rút từ 11 tuần xuống 4 tuần**, nên từ GĐ3 phải viết báo cáo song song với code chứ không dồn về cuối.

> **Cần cả nhóm xác nhận:** nhóm thuộc **K23, bảo vệ đợt 1**. Suy ra từ tên tổ chức `KLTN-2023-HCMSU`. Mọi ngày ở nửa sau timeline treo vào giả định này.

### (e) Nặng — mốc đăng ký đề tài 09–14/11/2026 — chưa ai nhắc tới

Bốn mốc hành chính của khoa trước đây **không có dòng nào** trong timeline. Gần nhất là **đăng ký đề tài KLTN đợt 1 K23, ngày 09–14/11/2026 — còn khoảng 8 tuần**. Trễ mốc này là hỏng cả năm, không bù được bằng cách code chăm hơn.

Ba mốc còn lại: nộp đề cương 22–27/02/2027 · báo cáo tiến độ 19–24/04/2027 · nộp đơn bảo vệ 28/06–03/07/2027.

### (f) Vừa — cặp review chéo không hoạt động

PR #2 merge mà không ai review. PR #1 merge lúc CI đang đỏ. Theo bảng ghép cặp trong timeline: Hùng ← Khải, Khải ← Thắng, Kiên ← Hùng, Thắng ← Kiên. Tuần này **không cặp nào chạy**.

### (g) Vừa — tài liệu nghiên cứu 9 bài đang nằm ở hai nơi

Bản tổng hợp 9 bài báo + 7 câu hỏi cho thầy hiện tồn tại ở **hai chỗ với nội dung trùng ~98%**:

- [`Research-Analysis/analysis/00_Tong_hop_va_cau_hoi.md`](https://github.com/KLTN-2023-HCMSU/Research-Analysis) — đã push
- `document/Week6/07_Nghien_cuu_bai_bao/Tom_tat_bai_bao_va_cau_hoi.md` — **chưa commit**

> **Cần chốt đâu là nguồn sự thật** rồi xoá hoặc trỏ link ở chỗ còn lại, trước khi hai bản bắt đầu lệch nhau.

**Cùng nhóm vấn đề:** `document/Week6/README.md` và `06_Kich_ban_trinh_bay/` đang có phần sửa ngày bảo vệ **chưa commit**. Kịch bản trình bày hiện vẫn còn câu *"timeline 53 tuần, bảo vệ khoảng giữa tháng 9/2027"* — **sai**. Ai dùng file đó để trình bày thì nhớ nói theo ngày mới.

---

## 5. Kế hoạch W03 · 21/09 – 27/09 → **nghiệm thu M2**

| Người | Mã | Việc phải xong | Hạn | Bằng chứng nghiệm thu |
|---|---|---|---|---|
| **Hùng** | H02 | Đăng nhập trả JWT (15') + refresh token (7 ngày), **lưu hash** trong DB | **T6 25/09** | Bảng `refresh_tokens` không có plain text |
| **Hùng** | H02 | Refresh token rotation + đăng xuất thu hồi token phiên hiện tại | **T6 25/09** | Dùng lại token cũ → `401` |
| **Hùng** | — | `api` gọi `scan-engine` sync, timeout 2s, circuit breaker Resilience4j | T6 25/09 | Tắt `scan-engine` → `api` trả `503`, không treo |
| **Khải** | K01 | Chuẩn hoá URL + tách thành phần | T6 25/09 | Bộ test 15 URL biến thể ra cùng dạng chuẩn |
| **Kiên** | R01 | Hoàn thiện 6 nhóm từ khoá + 5 mẫu tổ hợp trong `scan-engine` | T6 25/09 | `pytest` xanh, thêm ≥ 5 case mới |
| **Kiên** | R01 | Trang quét nội dung trên web: nhập text → hiện điểm và từng bằng chứng | T6 25/09 | Quét thật qua `api`, không phải mock |
| **Thắng** | T11 | Màn hình kết quả mobile dùng `RiskResultCard`, gọi API qua mock auth | T6 25/09 | Quét văn bản trên mobile ra kết quả thật |
| **Cả nhóm** | — | **NGHIỆM THU M2** | **CN 27/09** | Đăng ký → đăng nhập → `POST /v1/scan/text` bằng token thật → có bản ghi trong `scan_requests` |

### Nợ mang sang từ W02

| Người | Nợ | Ghi chú |
|---|---|---|
| **Kiên** | R02 `/internal/extract` | Cộng dồn với 2 việc R01 → **3 việc cùng hạn 25/09** |
| **Thắng** | T11 khung mobile Expo | Là tiền đề của việc T11 tuần sau; không có khung thì không có màn hình kết quả |
| **Khải** | K12 chốt phương án chặn merge khi đỏ | Cần cả nhóm quyết, không tự làm được |

---

## 6. Bốn việc cần quyết trong standup thứ 2 (21/09)

| # | Việc cần quyết | Ai chủ trì |
|---|---|---|
| 1 | **Kiên có đang bị chặn gì không** — và có cần Hùng hỗ trợ R01/R02 không. M2 phụ thuộc vào đây | Cả nhóm |
| 2 | **Chặn merge khi CI đỏ**: nâng GitHub Pro / để repo công khai / quy ước tay | Cả nhóm |
| 3 | **Xác nhận nhóm thuộc K23 đợt 1**, bảo vệ 19–31/07/2027 | Cả nhóm |
| 4 | **Chốt nguồn sự thật cho tài liệu nghiên cứu 9 bài báo** | Hùng + Khải |

---

*Nộp lúc: 16/09/2026 · Báo cáo cả nhóm, bổ sung cho các báo cáo tuần cá nhân trong [`BaoCao/`](BaoCao/)*
