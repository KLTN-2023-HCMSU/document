# Kịch bản trình bày Week 6 — cho giảng viên hướng dẫn

**Ngày soạn:** 2026-09-09
**Thời lượng:** 20 phút trình bày + 10 phút hỏi đáp
**Hình thức:** mở file trực tiếp trên màn hình, không dùng slide
**Người trình bày chính:** Hùng · mỗi thành viên bổ sung phần miền của mình khi được hỏi

---

## 0. Chuẩn bị trước buổi — làm trước 15 phút

- [ ] Mở sẵn **6 tab** trong VS Code theo đúng thứ tự ở mục 1, đừng tìm file giữa lúc nói
- [ ] Bật **preview Markdown** (`Ctrl+Shift+V`) — sơ đồ mermaid phải render được, không hiện code thô
- [ ] Chạy thử `cd services/scan-engine && pytest` — để sẵn kết quả xanh trong terminal, phòng khi thầy hỏi "code chạy chưa"
- [ ] Mở sẵn Postman collection `AntiScam-ScanEngine-M1` phòng khi thầy muốn xem demo thật
- [ ] Phóng to cỡ chữ editor lên ít nhất 16px nếu chiếu màn hình
- [ ] In hoặc mở tài liệu này ở máy thứ hai / điện thoại để liếc

---

## 1. Bản đồ buổi trình bày

| # | Phần | File mở | Thời lượng | Câu chốt của phần |
|---|---|---|---|---|
| 0 | Mở đầu | — | 0:30 | Tuần này nhóm chốt xong nền tảng để bắt đầu code |
| 1 | Kiến trúc v2 | [`01_Kien_truc_v2/Kien_truc_he_thong_v2.md`](../01_Kien_truc_v2/Kien_truc_he_thong_v2.md) | 4:00 | Đã chốt ranh giới Java/Python, không còn chỗ hiểu mơ hồ |
| 2 | Danh sách tính năng | [`02_Danh_sach_tinh_nang/Danh_sach_tinh_nang_toan_he_thong.md`](../02_Danh_sach_tinh_nang/Danh_sach_tinh_nang_toan_he_thong.md) | 3:00 | 353 tính năng, nhưng chỉ cam kết 103 cái `P0` |
| 3 | Kế hoạch phát triển | [`03_Ke_hoach_phat_trien/Ke_hoach_phat_trien.md`](../03_Ke_hoach_phat_trien/Ke_hoach_phat_trien.md) | 4:00 | Thứ tự làm việc tính bằng đồ thị phụ thuộc, không phải cảm tính |
| 4 | Timeline | [`04_Timeline_chi_tiet/Timeline_chi_tiet_theo_tuan.md`](../04_Timeline_chi_tiet/Timeline_chi_tiet_theo_tuan.md) | 4:30 | MVP 30/10/2026, bảo vệ 15/09/2027, 12 deadline cứng |
| 5 | Chuẩn báo cáo code | [`05_Chuan_bao_cao_code/00_Quy_dinh_bao_cao_code.md`](../05_Chuan_bao_cao_code/00_Quy_dinh_bao_cao_code.md) | 3:00 | Viết code xong phải báo cáo được, để người khác dùng lại |
| 6 | Kết + xin chốt | — | 1:00 | Bốn việc cần thầy cho ý kiến |

> **Nguyên tắc xuyên suốt:** mỗi phần chỉ mở **1–2 mục** trong file, không cuộn hết từ đầu tới cuối. Thầy cần thấy *kết luận* và *cách nhóm đi tới kết luận đó*, không cần đọc cả tài liệu.

---

## 2. Mở đầu — 30 giây

**Không mở file nào. Nhìn thẳng, nói chậm.**

> "Thưa thầy, tuần này nhóm em không viết thêm tính năng mới. Nhóm em dừng lại để chốt **nền tảng trước khi bốn người bắt đầu code song song**, vì ở Week 4 đã lộ ra hai vấn đề: có slice bị trùng người, và có slice không ai nhận.
>
> Kết quả tuần này là năm tài liệu, trả lời năm câu hỏi khác nhau: hệ thống gồm những gì, phải làm bao nhiêu việc, làm theo thứ tự nào, ai nộp gì vào ngày nào, và viết xong thì báo cáo thế nào. Em xin trình bày lần lượt trong 20 phút."

**Ý cần nhấn:** đây là tuần **giảm rủi ro**, không phải tuần chậm tiến độ.

---

## 3. Phần 1 · Kiến trúc v2 — 4 phút

**Mở file:** [`01_Kien_truc_v2/Kien_truc_he_thong_v2.md`](../01_Kien_truc_v2/Kien_truc_he_thong_v2.md)

### 3.1. Cuộn tới **mục 1 — "Vì sao cần bản v2"** · 60 giây

> "Bản Week 3 chọn Modular Monolith kết hợp Event-Driven Workers. Bản v2 **không lật lại lựa chọn đó** — nó chỉ sửa liều lượng và ranh giới, sau khi có ba dữ kiện mới.
>
> Thứ nhất, nhóm em đã có `scan-engine` chạy được bằng Python, 16 rule, 23 test xanh. Hệ thống thành đa ngôn ngữ, nên phải chốt ranh giới Java–Python trước khi cả nhóm code.
>
> Thứ hai, đo được quét văn bản chỉ tốn khoảng 1 đến 5 mili giây. Đó là CPU thuần, không có I/O. Đẩy nó qua message queue như bản Week 3 là lãng phí — thêm một round-trip và thêm một điểm hỏng để tiết kiệm 5 mili giây.
>
> Thứ ba, Week 4 lộ ra việc chia slice bằng họp thì không chống được trùng lặp."

**Ý cần nhấn:** ba sửa đổi đều xuất phát từ **dữ kiện đo được**, không phải từ sở thích.

### 3.2. Cuộn tới **ADR-03 (bảng Java/Python)** · 90 giây

> "Đây là quyết định quan trọng nhất của tuần. Java sở hữu I/O và trạng thái; Python là hàm thuần.
>
> Cụ thể: mọi truy cập PostgreSQL, Redis, mọi lời gọi ra Internet, mọi việc liên quan tới xác thực và phân quyền — đều ở Java. Python chỉ nhận dữ liệu, chuẩn hoá, đánh giá rule, trả điểm.
>
> Ranh giới này giải quyết một lúc ba việc. Một, `scan-engine` không tự gọi ra Internet nên **không bao giờ trở thành lỗ hổng SSRF** — chỉ cần làm và kiểm thử SSRF guard ở đúng một chỗ. Hai, ba bạn còn lại vẫn viết Java đúng như kế hoạch Week 4, chỉ mình em viết Python. Ba, vì là hàm thuần nên `scan-engine` test được bằng `pytest` mà không cần dựng database."

**Nếu thầy hỏi "sao không viết hết bằng Java":** → xem câu hỏi Q1 ở mục 8.

### 3.3. Cuộn tới **mục 4.2 — sơ đồ sync-first có leo thang** · 60 giây

> "Đây là mục kỹ thuật nhóm em định viết vào báo cáo. Quét URL có thể mất từ 5 mili giây tới 15 giây, tuỳ trang có phản hồi hay không. Nếu ép sync thì người dùng chờ; nếu ép async thì mọi lần quét đều phải poll dù thực ra đã có kết quả ngay.
>
> Nhóm em chọn cách thứ ba: thử đường nhanh trong ngân sách 2 giây, kịp thì trả `200` kèm kết quả, không kịp thì trả `202` kèm `scanId` rồi đẩy sang worker. Hợp đồng với client là **mọi endpoint quét đều có thể trả `200` hoặc `202`**, client xử lý được cả hai. Nhờ vậy sau này đổi một endpoint từ sync sang async không phải sửa client."

### 3.4. Cuộn tới **mục 7 — Lộ trình tích hợp M1–M6** · 30 giây

> "Sáu mốc, mỗi mốc phải có một bằng chứng chạy được. M1 đã xong: `scan-engine` chạy độc lập, Postman 16 request xanh, pytest 23 trên 23."

**Chuyển tiếp:** *"Kiến trúc trả lời câu 'hệ thống gồm những gì'. Câu tiếp theo là 'phải làm bao nhiêu việc'."*

---

## 4. Phần 2 · Danh sách tính năng — 3 phút

**Mở file:** [`02_Danh_sach_tinh_nang/Danh_sach_tinh_nang_toan_he_thong.md`](../02_Danh_sach_tinh_nang/Danh_sach_tinh_nang_toan_he_thong.md)

### 4.1. Dừng ở **đầu file — khối "⚠ Đọc mục này trước"** · 60 giây

> "Nhóm em đã liệt kê đầy đủ 48 nhóm tính năng, tổng 353 tính năng, chia đều 12 nhóm mỗi người.
>
> Nhưng em xin nói rõ ngay: **đây là backlog, không phải cam kết tiến độ.** 353 tính năng vượt xa phạm vi một khóa luận cho nhóm bốn người. Nên mỗi tính năng đều có nhãn ưu tiên: 103 cái `P0` là MVP bắt buộc, 135 cái `P1` nên có, và 115 cái `P2` là mở rộng.
>
> Cách nhóm em dùng tài liệu này: khoá phạm vi bảo vệ ở `P0` cộng một phần `P1`. Toàn bộ `P2` đưa vào chương Hướng phát triển của báo cáo — chúng vẫn có giá trị học thuật mà không cần code."

**Ý cần nhấn:** nhóm **biết** mình không làm hết, và đã nói trước điều đó thay vì để thầy phát hiện.

### 4.2. Cuộn tới **"Chỉ mục nhanh" (bảng 4 cột H/K/R/T)** · 45 giây

> "Bốn miền phụ trách: em làm định danh, bảo mật và lõi chấm điểm; Khải làm URL, mạng, threat intel và hạ tầng; Kiên làm văn bản, rule và tri thức chống lừa đảo; Thắng làm thực thể, cộng đồng, quản trị và mobile.
>
> Mã nhóm `H`, `K`, `R`, `T` này được dùng xuyên suốt cả bốn tài liệu còn lại — trong kế hoạch, trong timeline, trong tên nhánh Git và trong báo cáo code. Nhìn mã là biết ai chịu trách nhiệm."

### 4.3. Cuộn xuống cuối — **"Bảng cân đối khối lượng"** · 75 giây

> "Chỗ này là phần nhóm em muốn xin ý kiến thầy. Khi đếm ra số thì lộ hai điểm lệch.
>
> Kiên chỉ có 13 tính năng `P0`, vì phần lõi rule đã hoàn thành ở mốc M1 rồi. Còn Thắng có 31 `P0` và 96 tính năng — nặng nhất, vì bạn ấy nhận thêm Slice 4 vốn chưa có chủ từ Week 4.
>
> Nhóm em đề xuất chuyển hai nhóm T04 Risk Entity và T08 Dashboard từ Thắng sang Kiên. Sau điều chỉnh thì Kiên khoảng 24 `P0`, Thắng khoảng 20 — cân hơn hẳn. Lý do chọn đúng hai nhóm này thì em xin trình bày ở phần kế hoạch, vì nó liên quan tới đường găng."

**Chuyển tiếp:** *"Biết phải làm gì rồi, câu tiếp theo là làm theo thứ tự nào."*

---

## 5. Phần 3 · Kế hoạch phát triển — 4 phút

**Mở file:** [`03_Ke_hoach_phat_trien/Ke_hoach_phat_trien.md`](../03_Ke_hoach_phat_trien/Ke_hoach_phat_trien.md)

> **Đây là tài liệu nhóm nên dành nhiều thời gian nhất.** Nó là phần thể hiện rõ nhất rằng nhóm đã suy nghĩ có phương pháp chứ không xếp việc theo cảm tính.

### 5.1. Dừng ở **mục 1 — bảng "Tóm tắt điều hành"** · 90 giây

> "Nhóm em không xếp lịch bằng cảm tính. Nhóm em dựng đồ thị phụ thuộc giữa 48 nhóm rồi tính bằng máy: phân lớp topo, tìm đường găng, đếm điểm phối hợp chéo. Không có nhóm nào tạo chu trình.
>
> Đồ thị đó cho ra bốn kết luận, em xin nêu hai cái quan trọng nhất.
>
> **Thứ nhất: H01 Đăng ký chặn 25 trong 48 nhóm**, thuộc cả bốn người. Nếu H01 trễ một tuần thì cả nhóm trễ một tuần.
>
> **Thứ hai: Thắng không có việc nào ở Wave 0 và Wave 1.** Nếu làm đúng thứ tự phụ thuộc thì bạn ấy ngồi chờ hai tới ba tuần đầu."

### 5.2. Vẫn ở mục 1 — **khối "Hai hành động phải làm ngay"** · 60 giây

> "Hai vấn đề đó có hai cách gỡ, và cả hai đều gần như miễn phí.
>
> Với nút chặn H01: em dựng **auth giả** trước khi làm auth thật. Một endpoint dev cấp JWT cố định cho hai tài khoản `test` và `admin`, bật bằng biến môi trường. Tốn nửa ngày của em, nhưng **gỡ 25 nhóm ra khỏi trạng thái chờ** — ba bạn còn lại bắt đầu được ngay ngày đầu tiên. Việc này em đã làm xong hôm mùng 9.
>
> Với việc Thắng ngồi chờ: đảo thứ tự, cho bạn ấy làm thư viện UI dùng chung và khung ứng dụng mobile trước, chạy trên dữ liệu giả. Đây là việc hoàn toàn không phụ thuộc API."

**Ý cần nhấn:** nhóm không chỉ *phát hiện* rủi ro, mà đã *xử lý* rồi.

### 5.3. Cuộn tới **mục 5 — Đường găng** · 60 giây

> "Đường găng dài 7 nhóm: H01 sang H02 sang H04, rồi T04, T02, T03, T11. **Thêm người vào cũng không rút ngắn được** — đây là giới hạn dưới về thời gian của dự án.
>
> Nhưng có bốn cách rút thật sự, đều nằm trong bảng dưới đây. Mock auth cắt được ba mắt xích đầu. Tách T04 làm hai phần — bảng dữ liệu và API làm trước, trang admin làm sau — cho T01 và T02 chạy sớm hơn khoảng một tuần. Áp cả bốn cách thì đường găng rút từ 7 nhóm xuống còn 4.
>
> Và đây cũng là lý do nhóm em muốn chuyển T04 sang Kiên: T04 nằm trên đường găng, Kiên đang nhẹ nhất, và bạn ấy rảnh đúng lúc T04 cần khởi động."

### 5.4. Cuộn tới **mục 7 — "Mười hai điểm phối hợp chéo"** · 60 giây

> "Đây là **toàn bộ** chỗ hai người phải nói chuyện với nhau — chỉ 12 điểm trên 48 nhóm. Mức ghép nối thấp, nghĩa là chia việc tốt.
>
> Chốt xong 12 hợp đồng API này thì bốn người chạy song song gần như hoàn toàn. Nhóm em đã họp chốt hôm mùng 10 và ghi vào thư mục `contracts/openapi/`.
>
> Một nhận xét nữa từ bảng này: **9 trên 12 điểm đều trỏ về em.** Đây là bằng chứng số học cho kết luận ở trên — em là nút cổ chai của cả nhóm, nên em phải công bố hợp đồng của H02 và H04 sớm, kể cả khi phần thân chưa xong."

**Chuyển tiếp:** *"Kế hoạch nói thứ tự. Còn ngày tháng cụ thể thì ở tài liệu tiếp theo."*

---

## 6. Phần 4 · Timeline — 4 phút 30

**Mở file:** [`04_Timeline_chi_tiet/Timeline_chi_tiet_theo_tuan.md`](../04_Timeline_chi_tiet/Timeline_chi_tiet_theo_tuan.md)

### 6.1. Dừng ở **mục 0 — "Dự án này có hai nhịp khác hẳn nhau"** · 75 giây

> "Nhóm em có hai mốc cố định: **MVP xong ngày 30 tháng 10 năm nay**, và **bảo vệ khoảng giữa tháng 9 năm sau**. Giữa hai mốc đó là 53 tuần.
>
> Nên timeline chia làm hai nhịp khác hẳn nhau. Tám tuần đầu là nhịp gấp, khoảng 15 đến 20 giờ mỗi tuần mỗi người, deadline mỗi tuần. Từ tháng 11 trở đi là nhịp thong thả, khoảng 8 tới 12 giờ, deadline mỗi hai tuần.
>
> Em xin nói thẳng một điều: **tám tuần đầu gần như không giãn ra được.** Mốc MVP trong tháng 10 là ràng buộc cứng, và 13 nhóm tính năng trong MVP đã là mức tối thiểu để có hệ thống chạy được đầu-cuối. Chỗ giãn nằm ở 45 tuần sau."

### 6.2. Cuộn tới **"Phạm vi MVP — 13 nhóm, không hơn"** · 60 giây

> "MVP gồm đúng 13 nhóm: bên em là đăng ký, đăng nhập, phân quyền, chống lạm dụng, audit log và che dữ liệu nhạy cảm. Khải làm hạ tầng Docker, CI, quét URL và SSRF guard. Kiên làm i18n, phân tích nội dung, trích xuất thực thể và phân loại mẫu lừa đảo. Thắng làm thư viện UI dùng chung, lịch sử quét và khung mobile.
>
> Mọi thứ ngoài 13 nhóm này **không thuộc MVP**, kể cả khi nó là `P0`. Ai xong sớm thì giúp người đang chậm, không tự nhận thêm nhóm mới — theo kinh nghiệm thì đây là chỗ hay vỡ kế hoạch nhất."

### 6.3. Cuộn xuống **"Kịch bản demo MVP — ngày 30/10/2026"** · 45 giây

**Đọc chậm, gần như nguyên văn — đây là cam kết cụ thể nhất nhóm đưa ra:**

> "Nhóm em định nghĩa MVP bằng một kịch bản demo chạy liền một mạch, không dừng giữa chừng:
>
> `docker compose up`, đăng ký tài khoản, đăng nhập, dán một tin nhắn lừa đảo tiếng Việt, nhận điểm rủi ro kèm bằng chứng và khuyến nghị, dán một URL giả mạo ngân hàng, nhận mức `DANGER`, thử quét địa chỉ `169.254.169.254` và bị SSRF guard chặn, mở lịch sử quét thấy đủ ba lần, đăng nhập bằng tài khoản khác thì không thấy lịch sử của người trước, rồi mở app mobile quét lại tin nhắn đầu tiên ra cùng kết quả.
>
> Chạy được trọn kịch bản này là MVP đạt. Không chạy được thì không đạt, bất kể đã viết bao nhiêu dòng code."

### 6.4. Cuộn tới **mục 5 — "Mười hai deadline cứng"** · 60 giây

> "Cả dự án có 12 mốc mà trễ là kéo người khác trễ theo. Mỗi mốc đều ghi rõ ai bị ảnh hưởng nếu trễ.
>
> Ví dụ mốc số 1: mock auth ngày mùng 9 tháng 9 — trễ thì 25 trên 48 nhóm bị chặn. Mốc số 5: bộ test tấn công SSRF ngày 23 tháng 10 — nhóm em quy ước **không có bộ test tấn công thì không nghiệm thu**, dù code đã chạy. Mốc số 9: 500 mẫu dữ liệu gán nhãn ngày 19 tháng 3 — trễ thì mất luôn chương đánh giá của báo cáo.
>
> Bảng này nhóm em định dán ở chỗ dễ thấy nhất."

### 6.5. Cuộn tới **mục 8 — "Khi trễ hạn thì cắt gì"** · 45 giây

> "Cuối cùng, nhóm em chuẩn bị sẵn thứ tự cắt, bảy bước, từ cắt trước tới cắt sau. Cắt `P2` trước, rồi tới các tính năng tiện ích, rồi tới dashboard, và cuối cùng mới tới lớp LLM.
>
> Lý do viết trước: cắt phạm vi lúc đang gấp thì rất dễ cắt nhầm vào thứ quan trọng. Có bảng này rồi thì cứ theo thứ tự mà làm, không phải tranh luận giữa lúc căng thẳng.
>
> Và có một danh sách **không được cắt trong mọi trường hợp** — 13 nhóm MVP cộng với T04, T05, T06, T01, T02, R04 và phần đo Precision/Recall."

**Chuyển tiếp:** *"Tài liệu cuối cùng nói về cách nhóm làm việc với nhau khi đã bắt đầu code."*

---

## 7. Phần 5 · Chuẩn báo cáo code — 3 phút

**Mở file:** [`05_Chuan_bao_cao_code/00_Quy_dinh_bao_cao_code.md`](../05_Chuan_bao_cao_code/00_Quy_dinh_bao_cao_code.md)

### 7.1. Dừng ở **mục 1 — "Vấn đề tài liệu này giải quyết"** · 60 giây

> "Nhóm bốn người, bốn miền riêng, ba ngôn ngữ, 48 nhóm tính năng. Nếu mỗi người chỉ commit code rồi nói 'xong rồi' thì đến tuần thứ 10 sẽ xảy ra ba chuyện: không ai gọi được code của người khác, hai người viết trùng nhau, và tới lúc viết báo cáo thì không nhớ mình đã làm gì.
>
> Nên nhóm em đặt ra một tiêu chí kiểm tra duy nhất: **đưa báo cáo cho một bạn khác trong nhóm, bạn đó phải làm được hai việc mà không hỏi câu nào — gọi được code, và giải thích lại được cho thầy.** Không đạt cả hai thì báo cáo chưa xong, bất kể dài bao nhiêu trang."

### 7.2. Cuộn tới **mục 2 — bảng 5 loại báo cáo** · 45 giây

> "Có năm mẫu. Báo cáo tính năng là mẫu chính, viết khi đóng xong một nhóm. Báo cáo tuần nộp mỗi chủ nhật. Mô tả pull request dùng mỗi lần mở PR. Báo cáo kiểm thử bắt buộc với các nhóm có yếu tố bảo mật hoặc chấm điểm — như SSRF guard hay rule engine. Và ADR để ghi lại quyết định kỹ thuật.
>
> Hai mẫu đầu là bắt buộc, ba mẫu còn lại viết khi có tình huống tương ứng."

### 7.3. **Mở file thứ hai:** [`Vi_du_da_dien_BCTN_H09.md`](../05_Chuan_bao_cao_code/Vi_du_da_dien_BCTN_H09.md) — cuộn tới **mục 7 "Quyết định kỹ thuật và đánh đổi"** · 75 giây

> "Em xin mở một ví dụ đã điền, viết trên module chuẩn hoá tiếng Việt đang chạy thật, để thầy thấy mức chi tiết nhóm em đặt ra.
>
> Đây là mục quan trọng nhất của mẫu báo cáo và bắt buộc phải điền. Ví dụ dòng đầu: quyết định gỡ leetspeak theo token, điều kiện là token phải chứa chữ cái. Cột 'phương án đã cân nhắc' ghi cả ba lựa chọn. Cột 'vì sao chọn' giải thích: nếu áp bảng leetspeak lên toàn chuỗi thì số điện thoại `0912345678` sẽ biến thành chữ và mất khỏi kết quả trích xuất. Cột cuối ghi cả đánh đổi phải chấp nhận.
>
> Lý do nhóm em bắt buộc mục này: code đọc là biết *làm gì*, nhưng *vì sao* thì mất luôn nếu không ghi lại. Và đây cũng là phần hội đồng hỏi nhiều nhất khi bảo vệ."

**Nếu còn thời gian, lướt nhanh mục 6 của cùng file:** *"Mỗi báo cáo còn có bảng file nào chịu trách nhiệm gì, và một dòng chỉ rõ nên đọc hàm nào trước."*

---

## 8. Kết + bốn việc xin thầy cho ý kiến — 1 phút

**Đóng hết file. Nói thẳng.**

> "Tóm lại, tuần này nhóm em chốt được: kiến trúc có ranh giới rõ giữa Java và Python, backlog 353 tính năng đã phân mức ưu tiên, thứ tự làm việc tính từ đồ thị phụ thuộc, timeline 53 tuần với hai mốc cứng, và bộ chuẩn báo cáo code.
>
> Nhóm em xin thầy cho ý kiến về bốn việc:
>
> **Một** — mốc MVP ngày 30 tháng 10 có hợp lý không, hay thầy thấy nên đặt sớm hoặc muộn hơn.
>
> **Hai** — việc chuyển T04 Risk Entity và T08 Dashboard từ Thắng sang Kiên để cân khối lượng, thầy thấy có ổn không.
>
> **Ba** — phạm vi cam kết bảo vệ ở mức `P0` cộng một phần `P1`, còn `P2` đưa vào chương Hướng phát triển. Mức này có đủ cho một khóa luận không ạ.
>
> **Bốn** — nhóm em định lấy hai điểm làm trọng tâm học thuật: pattern sync-first có leo thang, và phần đo Precision/Recall so sánh ba cấu hình rule-only, LLM-only và hybrid trên tập 500 mẫu tiếng Việt tự gán nhãn. Thầy thấy hai điểm này đã đủ chưa ạ."

---

## 9. Câu hỏi thầy có thể hỏi — chuẩn bị trước

| # | Câu hỏi | Trả lời ngắn |
|---|---|---|
| **Q1** | *Sao không viết hết bằng Java cho đỡ phức tạp?* | Xử lý tiếng Việt — bỏ dấu, leetspeak, chuẩn hoá — và tích hợp LLM về sau thuận lợi hơn hẳn trên Python. Đổi lại có hai ngôn ngữ và một network hop. Nhóm giảm nhẹ bằng ADR-03: **chỉ một người viết Python**, ranh giới hẹp và cố định, và `scan-engine` không có database nên test không cần hạ tầng. |
| **Q2** | *Code đâu, mới có tài liệu thôi à?* | `scan-engine` đã chạy từ mốc M1: 16 rule, 23 test xanh, Postman collection đầy đủ. Mock auth đã xong ngày 09/09. Em có thể demo ngay nếu thầy muốn. Tuần này nhóm cố ý dừng viết tính năng để chốt nền tảng, vì Week 4 đã lộ ra vấn đề trùng người và thiếu chủ. |
| **Q3** | *353 tính năng thì làm sao hết trong một khóa luận?* | Nhóm em không định làm hết, và đã ghi rõ điều đó ngay đầu tài liệu. Cam kết là 103 cái `P0` cộng một phần `P1`. 115 cái `P2` đưa vào chương Hướng phát triển — vẫn có giá trị học thuật mà không cần code. |
| **Q4** | *Trọng số rule lấy đâu ra, có cơ sở gì không?* | Hiện tại là phỏng đoán, và nhóm em nhận đây là rủi ro lớn nhất với hội đồng. Cách xử lý: gán nhãn 500 mẫu tin nhắn và URL tiếng Việt, hạn 19/03/2027, rồi đo Precision, Recall, F1 cho ba cấu hình và tinh chỉnh trọng số dựa trên số liệu thay vì phỏng đoán. |
| **Q5** | *Dùng LLM thì có gửi dữ liệu người dùng ra ngoài không?* | Có che PII trước khi gửi — số tài khoản, số điện thoại, OTP. Đây là một deadline cứng riêng, hạn 16/04/2027. Ngoài ra LLM nằm sau feature flag `ai.enabled`, tắt đi thì hệ thống vẫn chạy đủ, chỉ mất phần diễn giải. Và LLM **không được đổi** điểm rủi ro — nếu nó đóng góp điểm thì phải thành một evidence riêng có trần cứng ±15 điểm. |
| **Q6** | *Nhóm chống SSRF thế nào?* | Chỉ **một** thành phần được gọi ra Internet là `worker` Java, nên SSRF guard chỉ phải làm và kiểm thử ở một chỗ. Cơ chế: phân giải DNS trước, kiểm tra IP đích, rồi mới kết nối — chống DNS rebinding; và kiểm tra lại ở **mỗi** bước chuyển hướng. Nhóm quy ước không có bộ test tấn công thì không nghiệm thu. |
| **Q7** | *Nếu một bạn bỏ giữa chừng thì sao?* | Mức ghép nối thấp — chỉ 12 điểm phối hợp chéo trên 48 nhóm, và cả 12 đã có hợp đồng API viết sẵn trong `contracts/openapi/`. Nên phần của người nghỉ chia lại được mà không phải viết lại phần của người khác. Ngoài ra bộ chuẩn báo cáo code bắt mỗi nhóm tính năng phải có tài liệu bàn giao. |
| **Q8** | *Sao chỉ có 8 tuần cho MVP mà lại 45 tuần cho phần còn lại?* | Vì MVP là phần **bắt buộc phải có sớm** để biết kiến trúc có chạy được không — phát hiện sai kiến trúc ở tuần 8 rẻ hơn nhiều so với ở tuần 40. Còn 45 tuần sau là để làm phần nghiệp vụ sâu, đo đạc và viết báo cáo, ở nhịp bền hơn vì còn phải học các môn khác. |
| **Q9** | *Timeline một năm có bị trôi không?* | Có nguy cơ, nên nhóm em đã đưa sẵn vào bảng: 8 tuần nhịp chậm cho thi học kỳ 1 và Tết, không đặt deadline cứng trong đó. Cộng thêm một mốc rà soát toàn bộ ngày 11/06/2027 để quyết định cắt gì, và thứ tự cắt bảy bước đã viết trước. |
| **Q10** | *Bộ chuẩn báo cáo có làm chậm nhóm không?* | Ngược lại. Mỗi báo cáo tốn khoảng 30 phút viết ngay lúc mở PR, lúc mọi thứ còn trong đầu. Nếu không viết thì mỗi lần người khác cần dùng code của mình lại mất một vòng hỏi đáp, và tới lúc viết báo cáo khóa luận thì phải đọc lại code của chính mình từ đầu — tốn gấp ba lần. |

---

## 10. Bản rút gọn 5 phút — dùng khi thầy bận

Nếu chỉ có 5 phút, bỏ hết, chỉ mở **hai file** và nói bốn ý:

| Thứ tự | Mở gì | Nói gì | Thời lượng |
|---|---|---|---|
| 1 | Kiến trúc — **ADR-03** | Đã chốt ranh giới Java/Python: Java sở hữu I/O và trạng thái, Python là hàm thuần. Nhờ vậy chỉ một chỗ ra được Internet nên SSRF guard chỉ làm một lần, và chỉ một người phải viết Python | 1:30 |
| 2 | Kế hoạch — **mục 1** | Dựng đồ thị phụ thuộc rồi tính bằng máy. Phát hiện H01 chặn 25/48 nhóm, đã gỡ bằng mock auth tốn nửa ngày. Phát hiện Thắng bị dồn việc cuối kỳ, đã đảo thứ tự việc | 1:30 |
| 3 | Timeline — **kịch bản demo MVP** | Đọc nguyên kịch bản. Chốt: MVP 30/10/2026, bảo vệ 15/09/2027 | 1:30 |
| 4 | — | Bốn việc xin thầy cho ý kiến ở mục 8 | 0:30 |

---

## 11. Phân vai nếu cả nhóm cùng trình bày

| Người | Phần | Thời lượng |
|---|---|---|
| **Hùng** | Mở đầu · Kiến trúc v2 · Kế hoạch · Kết | 10:00 |
| **Khải** | Bổ sung phần SSRF guard trong kiến trúc (mục 5.1) + hạ tầng trong MVP | 2:30 |
| **Kiên** | Danh sách tính năng — phần phân bổ và cân đối khối lượng | 3:00 |
| **Thắng** | Timeline — phạm vi MVP và kịch bản demo | 3:00 |
| **Hùng** | Chuẩn báo cáo code | 3:00 |

**Quy tắc:** người không nói thì **không xen ngang**. Nếu thầy hỏi vào miền của ai thì người đó trả lời, người trình bày chính nhường lời bằng một câu: *"Phần này bạn Khải phụ trách, em mời bạn."*

---

*Người soạn: Hùng · Ngày 2026-09-09 · Rà soát lại trước buổi 15 phút*
