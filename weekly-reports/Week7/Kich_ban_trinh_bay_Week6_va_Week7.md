# Báo cáo với giảng viên hướng dẫn — gộp Week 6 và Week 7

**Ngày:** 2026-09-16
**Nội dung:** hai tuần 07/09 – 20/09 — nền tảng (W6) và những gì đã code, đã sửa (W7)
**Thời lượng:** 25 phút trình bày + 10 phút hỏi đáp
**Hình thức:** mở thẳng file và terminal, không dùng slide
**Người trình bày:** Khải · các bạn còn lại bổ sung phần của mình khi được hỏi

> **Kịch bản này thay cho việc đọc lại [`Kich_ban_trinh_bay_Week6.md`](../Week6/06_Kich_ban_trinh_bay/Kich_ban_trinh_bay_Week6.md).** Phần Week 6 ở đây đã rút gọn còn 5 phút vì sản phẩm của tuần đó vẫn còn nguyên trên màn hình, không cần trình bày lại từ đầu. Nếu thầy **chưa** nghe báo cáo Week 6 lần nào thì mở file cũ và cộng thêm 10 phút cho mục 4.

---

## 0. Hai câu tóm tắt hai tuần

> **Week 6:** nhóm chuyển từ "có tài liệu" sang "có hệ thống chạy được" — chốt kiến trúc, spec API, danh sách tính năng, kế hoạch, timeline; dựng mono-repo và code xong hai service.
>
> **Week 7:** nhóm chuyển từ "chạy được trên máy em" sang "có quy trình kiểm soát" — và trong lúc làm thì **phát hiện ba lỗi nghiêm trọng mà tuần trước không ai biết**: CI chưa từng chạy được test Java, và ngày bảo vệ trong timeline sai gần 8 tuần.

**Thông điệp của buổi này:** tuần 7 giá trị nhất không nằm ở tính năng mới, mà ở **ba thứ hỏng được tìm ra và sửa**.

---

## 1. Chuẩn bị trước buổi — làm trước 30 phút

### Việc bắt buộc làm trước

- [ ] **BẮT BUỘC — merge PR #4 vào `main` trước buổi.** `main` hiện **chưa có** `/health` `/ready` của `api` — demo sẽ hỏng nếu quên. Nếu chưa kịp review thì demo trên nhánh: `git checkout feat/K11-ops-probes-api`
- [ ] **BẮT BUỘC — báo trước cho Kiên** rằng mục 7 sẽ nói tới việc R01 đang nằm trên đường găng của M2. **Không bao giờ để một thành viên bị nêu bất ngờ trước mặt thầy** — nói với nhau trước, vào buổi thì cùng một tiếng nói.
- [ ] Thống nhất trong nhóm: ai trả lời câu nào ở mục 10.

### Dựng sẵn hệ thống

```bash
cd Scam-Risk-Detector
git checkout main && git pull          # (hoặc: git checkout feat/K11-ops-probes-api)
cp .env.example .env

# May nao dang chay san Postgres thi doi cong host truoc:
#   POSTGRES_HOST_PORT=55432 REDIS_HOST_PORT=56379 \
docker compose -f infra/docker-compose.yml up -d --build

docker compose -f infra/docker-compose.yml ps    # phai thay 4 dong (healthy)
```

- [ ] Terminal 1: `cd services/api && ./mvnw test` — để sẵn kết quả xanh
- [ ] Terminal 2: `cd services/scan-engine && python -m pytest tests/ -q` — để sẵn kết quả xanh
- [ ] Terminal 3: để trống, dùng để chạy demo trực tiếp
- [ ] Mở sẵn tab trình duyệt: trang **Actions** của repo (để show lịch sử CI đỏ → xanh), và PR #4
- [ ] Mở sẵn tab VS Code: `Timeline_chi_tiet_theo_tuan.md` (mục 2.1 và 2.3), `Bao_cao_tuan_W07_ca_nhom.md`, `OpsController.java`
- [ ] Mở sẵn file PDF **lịch năm học 2026–2027 của khoa** — mục 6 sẽ cần chiếu đúng dòng K23
- [ ] Phóng cỡ chữ editor và terminal ≥ 16px

---

## 2. Bảng "hai tuần làm được gì" — mở đầu bằng bảng này

| # | Tuần | Đã làm xong | Bằng chứng để show |
|---|---|---|---|
| 1 | W6 | **Kiến trúc v2** — 8 ADR, 6 nguyên tắc, 6 mốc | `01_Kien_truc_v2/` |
| 2 | W6 | **Spec API contract-first** — 12 endpoint, CI tự kiểm | `contracts/openapi/` |
| 3 | W6 | **Backlog 353 tính năng** đã gán P0/P1/P2 | `02_Danh_sach_tinh_nang/` |
| 4 | W6 | **Kế hoạch + timeline** có ngày cụ thể | `03_`, `04_` |
| 5 | W6 | **Hai service chạy thật** — mono-repo, Docker Compose, CI | `docker compose up` |
| 6 | **W7** | **Probe vận hành `/health` + `/ready`** trên các service, tách liveness khỏi readiness | Demo dừng database, mục 5 |
| 7 | **W7** | **Sửa lỗi khiến CI chưa từng chạy được test Java** | Tab Actions: 7 lần đỏ → xanh |
| 8 | **W7** | **Thêm CI cho `apps/web`** — 40 test của Thắng trước giờ chưa chạy lần nào | `ci.yml` 4 job |
| 9 | **W7** | **Căn lại timeline theo lịch khoa** — ngày bảo vệ trước đó sai gần 8 tuần | Mục 6 |
| 10 | **W7** | **Quy trình đã áp thật** — 4 PR, 2 báo cáo đã nộp | Mục 8 |

> **Ngân sách thời gian.** Cộng đủ là ~25 phút. Nếu trôi giờ thì **cắt mục 4 (recap Week 6)** trước tiên. **Tuyệt đối không cắt mục 6 và mục 7** — đó là hai chỗ ảnh hưởng trực tiếp tới việc nhóm có kịp bảo vệ hay không.

---

## 3. Mở đầu — 45 giây

**Chưa mở file nào.**

> "Thưa thầy, em xin báo cáo gộp hai tuần ạ.
>
> Tuần trước nhóm em chốt nền tảng và code xong hai service. Tuần này nhóm em định làm tiếp phần hạ tầng, nhưng trong lúc làm thì **phát hiện ra ba chỗ hỏng mà tuần trước cả nhóm đều không biết** — và em nghĩ đó mới là phần đáng báo cáo nhất hôm nay.
>
> Chỗ thứ nhất: hệ thống kiểm thử tự động của nhóm em **chưa từng chạy được một dòng test Java nào** suốt mười ngày, mà vẫn báo là đã chạy.
>
> Chỗ thứ hai, và chỗ này liên quan trực tiếp đến nhóm em: **ngày bảo vệ trong kế hoạch bị sai gần tám tuần** so với lịch của khoa.
>
> Chỗ thứ ba là về phân bổ công việc trong nhóm, em xin báo cáo ở cuối.
>
> Em xin bắt đầu bằng phần code đã chạy, rồi tới ba chỗ đó ạ."

---

## 4. Recap Week 6 — 5 phút *(cắt được nếu thiếu giờ)*

**Mở terminal 1 và 2 — kết quả test đã để sẵn.**

> "Đây là phần của tuần trước, em xin nói rất ngắn vì sản phẩm vẫn còn nguyên.
>
> Nhóm em có một mono-repo với hai service chạy thật: `scan-engine` viết bằng Python lo chuẩn hoá tiếng Việt và chấm điểm theo rule, `api` viết bằng Spring Boot lo xác thực, phân quyền và database. Ranh giới giữa hai bên ghi rõ trong ADR-03: **Java sở hữu I/O và trạng thái, Python là hàm thuần**."

**Chạy demo — copy-paste, đừng gõ tay:**

```bash
# 1. Lay token, khong can dang ky (mock auth)
curl -X POST "http://localhost:8080/v1/dev/token?role=USER"

# 2. Quet mot tin nhan lua dao tieng Viet
curl -X POST http://localhost:8000/v1/scan/text \
  -H "Content-Type: application/json" \
  -d '{"text":"Tài khoản Vietcombank của bạn sắp bị khóa, xác minh ngay tại http://vietc0mbank-verify.tk trong 24h","source":"SMS"}'
```

> "Kết quả trả về `riskScore` 100, mức `DANGER`. Nhưng phần em muốn thầy để ý là `evidences` — **danh sách bằng chứng theo từng rule**: nhóm từ khoá `URGENCY` bắt được cụm 'sắp bị khóa' cộng 10 điểm, nhóm `IMPERSONATION` bắt được 'Vietcombank' cộng 15 điểm, và tên miền đuôi `.tk` viết gần giống ngân hàng thật.
>
> Với một hệ thống cảnh báo cho người dùng phổ thông, em nghĩ **giải thích được quan trọng ngang với chính xác** — người dùng phải hiểu vì sao bị cảnh báo thì mới tin và mới dừng lại.
>
> Em xin nói thẳng một điểm yếu, giống tuần trước: **trọng số từng rule và hai ngưỡng 40/70 hiện vẫn là phỏng đoán.** Kế hoạch khắc phục là gán nhãn 500 mẫu rồi tinh chỉnh bằng số liệu, hạn tháng 3/2027."

---

## 5. Week 7 · Phần 1 — probe vận hành và một lỗi thiết kế đã sửa — 5 phút

**Mở terminal 3.**

> "Việc theo kế hoạch của em tuần này là thêm hai endpoint `/health` và `/ready` cho các service. Nghe thì nhỏ, nhưng lúc làm em phát hiện cái `/health` đang có bị sai về mặt thiết kế."

```bash
curl -s localhost:8080/health   # api  - liveness
curl -s localhost:8080/ready    # api  - readiness
curl -s localhost:8000/health   # scan-engine
curl -s localhost:8000/ready    # scan-engine
```

> "Bốn cái đều trả `UP`. Nhưng hai cái này **khác nhau về mục đích**, và tuần trước nhóm em gộp làm một — đó mới là vấn đề.
>
> `/health` trả lời câu hỏi *tiến trình còn sống không*. Nếu nó fail thì hệ điều phối container sẽ **khởi động lại** container.
>
> `/ready` trả lời câu hỏi *đã sẵn sàng nhận request chưa*. Nếu nó fail thì container bị **rút khỏi bộ cân bằng tải**, nhưng vẫn để nguyên cho người vận hành vào xem log.
>
> Cái `/health` cũ của `scan-engine` lại đi đọc file rule. Nghĩa là nếu file rule hỏng thì `/health` fail, container bị restart — mà restart thì file rule vẫn hỏng, nên nó lại fail, lại restart. **Vòng lặp vô hạn**, và mất luôn cơ hội vào xem log để biết hỏng cái gì."

**Demo phần quan trọng nhất — dừng database:**

```bash
docker compose -f infra/docker-compose.yml stop postgres
sleep 5

curl -s localhost:8080/health   # {"status":"UP"}            <- KHONG restart
curl -s localhost:8080/ready    # 503 {"failing":{"db":"DOWN"}}

docker logs antiscam-api 2>&1 | grep -c "Started ApiApplication"   # 1
```

> "Em vừa giết database. `/ready` trả 503 và nói rõ thành phần nào hỏng, nên `api` bị rút khỏi cân bằng tải. Nhưng `/health` vẫn trả 200, nên container **không bị restart** — thầy thấy số lần khởi động vẫn là 1.
>
> Bật database lại thì hệ thống tự phục hồi, không cần ai can thiệp."

```bash
docker compose -f infra/docker-compose.yml start postgres
```

> "Một chi tiết nhỏ về bảo mật em xin nêu: `/ready` chỉ trả **tên** thành phần hỏng và mã trạng thái, **không** trả thông điệp lỗi của driver — chỗ đó chứa chuỗi kết nối và địa chỉ máy chủ nội bộ, mà `/ready` là endpoint công khai. Em có viết test khoá lại chuyện này, và có thử ngược: cố tình cho nó rò ra thì test đỏ ngay."

---

## 6. Week 7 · Phần 2 — CI chưa từng chạy được test Java — 4 phút

**Mở tab Actions của repo.**

**Đây là phần dễ bị hỏi nhất. Nói trước thì là thành thật, để thầy tự thấy thì thành che giấu.**

> "Thưa thầy, đây là chỗ em xin báo cáo thẳng.
>
> Thầy nhìn cột trạng thái: từ ngày 6 tháng 9 tới sáng nay, **cả bảy lần chạy CI đều đỏ**. Không phải một lần, mà là toàn bộ.
>
> Nguyên nhân rất nhỏ: file `mvnw` — script chạy build của Java — được lưu trong git mà **thiếu cờ cho phép thực thi**. Nên bước chạy test chết ngay lập tức với mã lỗi 126, tức là *tìm thấy lệnh nhưng không chạy được*. Nó chưa từng chạy tới dòng test nào.
>
> Chỗ này che mắt được vì Dockerfile có sẵn một lệnh `chmod` cấp quyền, nên khi build image thì không sao. Chỉ CI mới dính.
>
> **Hệ quả là hai pull request đã được merge vào nhánh chính mà phần Java chưa hề được kiểm tra lần nào.**"

**Chỉ vào lần chạy mới nhất — xanh.**

> "Em đã sửa trong PR #4. Đây là lần chạy sau khi sửa: bốn job đều xanh — test Java, test Python, test giao diện web, và kiểm tra spec API. **Đây là lần CI xanh đầu tiên kể từ khi repo được dựng.**
>
> Em cũng thêm một job mới cho phần web. Trước đó CI không đụng gì tới thư mục web, nên **40 test của bạn Thắng chưa từng chạy trên CI lần nào** — giờ thì có.
>
> Nhưng em xin nói phần tự nhận: **nguyên nhân gốc không phải cái cờ thực thi.** Cờ đó sửa mất ba giây. Nguyên nhân gốc là **suốt mười ngày không ai trong nhóm nhìn vào trạng thái CI trước khi merge**. Nhóm em đã thêm một dòng vào quy định review chéo: người review bắt buộc xem CI trước khi Approve."

**Nếu thầy hỏi vặn "sao để mười ngày mới phát hiện":** đừng chống chế. Nói: *"Dạ đúng ạ, chỗ này nhóm em sai. Có công cụ mà không nhìn thì bằng không có. Em nhận và đã thêm vào checklist review."*

---

## 7. Week 7 · Phần 3 — ngày bảo vệ trong kế hoạch bị sai — 5 phút

**Mở file PDF lịch năm học của khoa, chiếu đúng dòng K23. Rồi mở `Timeline_chi_tiet_theo_tuan.md` mục 2.1.**

**Đây là mục quan trọng nhất của cả buổi. Không được cắt.**

> "Thưa thầy, đây là chỗ nghiêm trọng nhất tuần này.
>
> Bản timeline nhóm em trình thầy tuần trước giả định **bảo vệ khoảng giữa tháng 9 năm 2027**, và nộp bản cuối ngày 27 tháng 8.
>
> Tuần này em đối chiếu với **kế hoạch năm học 2026–2027 của khoa**, cột K23, đợt 1. Lịch thật là:
>
> - Nộp đơn bảo vệ: **28/06 – 03/07/2027**
> - Phản biện: **12 – 17/07/2027**
> - Bảo vệ: **19 – 31/07/2027**
>
> Nghĩa là **ngày nộp bản cuối trong kế hoạch cũ rơi sau khi đợt bảo vệ đã đóng gần bốn tuần.** Nếu cứ chạy theo bản cũ thì nhóm em trượt đợt 1, phải đợi đợt sau.
>
> Em đã căn lại toàn bộ nửa sau của timeline theo lịch khoa."

**Cuộn tới mục 2.2.**

> "Hệ quả nặng nhất không phải là dời ngày, mà là **quỹ thời gian cho kiểm thử và viết báo cáo rút từ 11 tuần xuống còn 4 tuần**.
>
> Bốn tuần thì không thể viết bốn chương báo cáo từ đầu. Nên nhóm em đổi cách làm: **viết báo cáo song song với code, bắt đầu từ tháng 2/2027**, dùng luôn bản đề cương làm dàn ý. Tới đầu tháng 6 phải có bản nháp đủ bốn chương, bốn tuần cuối chỉ ghép và điền số liệu.
>
> Kèm theo đó là đẩy mốc đóng băng tính năng lên sớm bốn tuần, và đặt thêm một điểm chốt cắt phạm vi vào giữa tháng 4."

**Cuộn tới mục 2.3 — bảng mốc hành chính.**

> "Còn một chỗ nữa em xin báo cáo: **bốn mốc hành chính của khoa trước đây không có một dòng nào trong kế hoạch của nhóm em.** Nhóm em chỉ lo mốc kỹ thuật mà quên mất mốc thủ tục.
>
> Gần nhất là **đăng ký đề tài khóa luận đợt 1, ngày 09 đến 14 tháng 11** — còn khoảng tám tuần. Trễ mốc này thì hỏng cả năm, không bù được bằng cách code chăm hơn.
>
> Em xin hỏi thầy hai việc ở chỗ này ạ:
>
> **Một** — nhóm em xác định là **K23, bảo vệ đợt 1 tháng 7/2027**. Nhờ thầy xác nhận giúp em có đúng không, vì toàn bộ nửa sau kế hoạch treo vào giả định đó.
>
> **Hai** — thủ tục đăng ký đề tài tháng 11 nhóm em cần chuẩn bị những gì, và thầy cần nhóm em nộp cho thầy trước bao lâu ạ."

---

## 8. Week 7 · Phần 4 — quy trình: những gì đã hứa tuần trước — 2 phút

**Mở `Bao_cao_tuan_W07_ca_nhom.md`.**

> "Tuần trước nhóm em nhận với thầy ba việc, em xin báo cáo lại bằng số.
>
> **Việc thứ nhất — không push thẳng lên nhánh chính nữa, phải qua pull request.** Đã làm. Tuần trước repo mới có 1 commit đẩy thẳng `main`. Hiện đã có **4 pull request**, đều có mô tả đầy đủ theo mẫu của nhóm, và CI chạy trên từng cái.
>
> **Việc thứ hai — nộp báo cáo theo chuẩn.** Đã làm. Có **hai báo cáo** trong `document/Week7/`: một báo cáo tuần cá nhân và một báo cáo tổng hợp cả nhóm.
>
> **Việc thứ ba — bật branch protection để chặn merge khi test đỏ.** Chỗ này em xin nhận là **chưa làm được**, và không phải do quên.
>
> GitHub yêu cầu tài khoản trả phí mới bật được tính năng này cho repo riêng tư. API trả về đúng dòng: *nâng lên GitHub Pro, hoặc để repo công khai*. Nhóm em đang có ba lựa chọn: trả phí, để repo công khai, hoặc chấp nhận quy ước tay.
>
> Em xin ý kiến thầy — **để repo công khai thì có ảnh hưởng gì tới việc bảo vệ hoặc bản quyền đề tài không ạ?** Nếu không sao thì nhóm em chọn cách đó vì nó miễn phí và đúng kỹ thuật nhất."

---

## 9. Week 7 · Phần 5 — phân bổ công việc, và một rủi ro — 2 phút

**Đã báo cho Kiên trước buổi. Nói bình tĩnh, không quy trách nhiệm.**

> "Cuối cùng em xin báo cáo thật về tình hình nhân sự tuần này.
>
> Tuần này **chỉ có một trên bốn người có commit code**. Nhưng em xin nói rõ từng người để thầy nắm đúng, vì con số đó dễ gây hiểu nhầm:
>
> - **Bạn Hùng** không commit tuần này, nhưng phần việc của tuần này bạn ấy **đã giao xong từ tuần trước** — đăng ký tài khoản và migration database đều đã chạy, 19 test xanh. Bạn ấy đang chạy trước lịch.
> - **Bạn Thắng** có một pull request được merge, nhưng code trong đó viết từ tuần trước. Phần việc tuần này là khung ứng dụng di động thì chưa bắt đầu.
> - **Bạn Kiên** tuần này chưa có gì được đẩy lên.
>
> Chỗ em lo không phải là một tuần chậm, mà là **tuần sau nhóm em có mốc nghiệm thu M2 vào ngày 27 tháng 9**. Mốc đó yêu cầu chạy trọn luồng: đăng ký, đăng nhập, rồi quét một tin nhắn bằng token thật và ra được bằng chứng theo rule. **Phần quét đó là việc của bạn Kiên**, và tuần sau bạn ấy có ba việc cùng hạn ngày 25.
>
> Nhóm em định xử lý theo đúng nguyên tắc đã ghi trong kế hoạch: *ai xong việc sớm thì hỗ trợ người đang chậm*. Bạn Hùng đang chạy trước lịch nên sẽ hỗ trợ phần này. Thứ hai tới nhóm em họp chốt lại."

> *Nếu Kiên có mặt, nhường lời:* **"Phần này bạn Kiên phụ trách, em mời bạn nói rõ hơn ạ."**

---

## 10. Kết + xin thầy cho ý kiến — 2 phút

**Đóng hết file.**

> "Tóm lại hai tuần: nhóm em có nền tảng đã chốt, hai service chạy thật, và tuần này thêm phần hạ tầng vận hành cùng một hệ thống kiểm thử tự động **nay mới thật sự hoạt động**.
>
> Ba chỗ em xin báo cáo thật để thầy nắm đúng: CI đỏ mười ngày mà không ai nhìn; ngày bảo vệ trong kế hoạch sai gần tám tuần, nay đã căn lại; và tuần này chỉ một trên bốn người có sản lượng code.
>
> Nhóm em xin thầy cho ý kiến về bốn việc ạ:
>
> **Một** — nhờ thầy **xác nhận nhóm em thuộc K23, bảo vệ đợt 1 tháng 7/2027**. Toàn bộ nửa sau kế hoạch treo vào giả định này.
>
> **Hai** — **thủ tục đăng ký đề tài tháng 11** nhóm em cần chuẩn bị gì, và nộp cho thầy trước bao lâu ạ.
>
> **Ba** — **để repo công khai** có ảnh hưởng gì tới bảo vệ hoặc bản quyền đề tài không ạ.
>
> **Bốn** — với lịch mới, quỹ thời gian viết báo cáo chỉ còn bốn tuần cuối nên nhóm em phải viết song song từ tháng 2. Thầy thấy cách đó có ổn không, hay thầy có cách khác ạ."

---

## 11. Câu hỏi thầy có thể hỏi — chuẩn bị trước

| # | Câu hỏi | Trả lời ngắn |
|---|---|---|
| **Q1** | *Sao để CI đỏ mười ngày mới phát hiện?* | **Nhận, không chống chế.** Có công cụ mà không nhìn thì bằng không có. Nguyên nhân kỹ thuật là thiếu cờ thực thi trên `mvnw`, sửa mất ba giây; nguyên nhân thật là không ai xem CI trước khi merge. Đã thêm dòng bắt buộc xem CI vào checklist review chéo. |
| **Q2** | *Ai chịu trách nhiệm việc timeline sai ngày bảo vệ?* | Bản timeline do cả nhóm chốt, nên là lỗi chung. Nguyên nhân: nhóm tự suy ra ngày bảo vệ thay vì tra lịch khoa. Bài học đã áp: **mọi mốc hành chính giờ phải dẫn nguồn từ văn bản của khoa**, và đã thêm hẳn một mục riêng cho bốn mốc đó trong timeline. |
| **Q3** | *Chỉ một người code trong một tuần thì có vấn đề gì không?* | Có, và nhóm em không giấu. Nhưng cần tách hai chuyện: Hùng không commit vì **đã giao trước lịch**; Thắng và Kiên thì đúng là chậm. Rủi ro cụ thể là mốc M2 ngày 27/09 phụ thuộc vào phần của Kiên. Cách xử lý: Hùng hỗ trợ, chốt trong standup thứ hai. |
| **Q4** | *`/health` với `/ready` khác gì nhau, sao phải tách?* | `/health` = tiến trình còn sống, fail thì bị **restart**. `/ready` = sẵn sàng nhận request, fail thì bị **rút khỏi cân bằng tải** nhưng vẫn để nguyên. Gộp làm một thì database hỏng sẽ gây restart vô hạn, mà restart không sửa được database. Demo ở mục 5 cho thấy đúng chỗ đó. |
| **Q5** | *Sao không dùng luôn `/actuator/health` có sẵn của Spring?* | Vì mốc K11 cần **cùng một cặp đường dẫn trên mọi service**. Nếu Java dùng `/actuator/health/readiness` còn Python dùng `/ready` thì Nginx và hệ điều phối phải nhớ hai hợp đồng. Bên trong vẫn dùng lại toàn bộ cơ chế health của Spring, chỉ đổi đường dẫn và hình dạng JSON. |
| **Q6** | *Timeline mới có còn kịp không? Cắt bảy tuần thì lấy đâu ra?* | Lấy từ bốn chỗ: rút giai đoạn kiểm thử + viết báo cáo từ 11 tuần xuống 4 bằng cách **viết song song từ tháng 2**; thu hồi ba tuần tháng 1 mà bản cũ bỏ phí; tận dụng ba tuần nghỉ thi THPT Quốc gia tháng 6; và đẩy mốc cắt phạm vi lên sớm. Đã ghi chi tiết ở mục 2.2 của timeline. |
| **Q7** | *Nếu tới tháng 6 mà chưa xong thì sao?* | Đã có thứ tự cắt phạm vi bảy bước viết sẵn từ trước, để lúc gấp không phải tranh luận. Cắt trước: các tính năng P2, trợ lý hỏi đáp, quiz, xuất PDF. Không cắt: 13 nhóm MVP và phần đo Precision/Recall — đó là phần có nội dung học thuật. |
| **Q8** | *Trọng số rule vẫn là phỏng đoán à?* | Vẫn ạ, nhóm em nhận đây là điểm yếu lớn nhất. Kế hoạch: gán nhãn 500 mẫu tiếng Việt, hạn 19/03/2027, rồi đo Precision/Recall/F1 và tinh chỉnh. Có tiền lệ: bài TypoDS trên *Computers & Security* 2026 cũng dùng hàm cộng điểm rồi so ngưỡng, ngưỡng của họ chọn bằng thực nghiệm. |
| **Q9** | *Web và mobile đâu?* | `apps/web` đã có component hiển thị kết quả rủi ro và trang demo, 40 test xanh, nay đã vào CI. `apps/mobile` thì **chưa khởi tạo** — đó là việc đang chậm của bạn Thắng, hạn 18/09. |
| **Q10** | *Cho thầy xem một bản báo cáo code thật.* | Mở `document/Week7/BaoCao/Tuan_W07_Khai.md` và `Bao_cao_tuan_W07_ca_nhom.md`. Tuần trước nhóm em nhận là chưa áp lần nào; tuần này đã có hai bản. |
| **Q11** | *Sao chưa bật branch protection như đã hứa?* | GitHub đòi tài khoản trả phí cho repo riêng tư. Ba lựa chọn: trả phí, để repo công khai, hoặc quy ước tay. Đang xin ý kiến thầy về lựa chọn thứ hai. |
| **Q12** | *Có bao nhiêu test tất cả?* | Sau khi PR #4 merge: **92 test** — `api` 27, `scan-engine` 25, `apps/web` 40. Tuần trước là 85 và trong đó phần Java chưa từng chạy trên CI. |

---

## 12. Bản rút gọn 6 phút — dùng khi thầy bận

| Thứ tự | Làm gì | Nói gì | Thời lượng |
|---|---|---|---|
| 1 | Terminal — dừng postgres, gọi 2 endpoint | "Database chết thì `/ready` báo 503 và bị rút khỏi cân bằng tải, nhưng `/health` vẫn 200 nên container không bị restart vô hạn" | 1:30 |
| 2 | Tab Actions | "Bảy lần chạy CI đều đỏ suốt mười ngày, test Java chưa từng chạy. Đã sửa, nay xanh cả bốn job" | 1:15 |
| 3 | Lịch khoa + timeline mục 2.1 | "Ngày bảo vệ trong kế hoạch cũ sai gần tám tuần. Lịch thật là 19–31/07/2027. Đã căn lại toàn bộ" | 2:00 |
| 4 | Timeline mục 2.3 | "Mốc đăng ký đề tài 09–14/11, còn tám tuần. Trước đây kế hoạch không có dòng nào về mốc hành chính" | 0:45 |
| 5 | — | Bốn việc xin ý kiến ở mục 10 | 0:30 |

---

## 13. Phân vai nếu cả nhóm cùng trình bày

| Người | Phần | Thời lượng |
|---|---|---|
| **Khải** | Mở đầu · probe và CI · timeline và lịch khoa · kết | 14:00 |
| **Hùng** | Recap Week 6: kiến trúc, slice Auth, demo quét tin nhắn | 5:00 |
| **Kiên** | Rule engine: 16 rule hiện có, kế hoạch R01/R02 và cam kết cho M2 | 3:00 |
| **Thắng** | Phần web đã xong, và kế hoạch khung mobile | 3:00 |

**Quy tắc:** người không nói thì **không xen ngang**. Thầy hỏi vào miền của ai thì người đó trả lời, người trình bày chính nhường lời bằng một câu: *"Phần này bạn X phụ trách, em mời bạn."*

---

## 14. Bốn điều tuyệt đối không làm

1. **Không nói "gần xong"** cho thứ chưa chạy được. Chưa có thì nói chưa có, kèm mốc dự kiến. Thầy trừ điểm vì nói quá nhiều hơn là vì làm chậm.
2. **Không sửa code khi demo lỗi.** Chuyển sang kết quả đã chạy sẵn, ghi lại lỗi, nói *"em sẽ kiểm tra và báo cáo lại"*.
3. **Không đổ lỗi cho nhau trước mặt thầy.** Mục 9 đã thống nhất trong nhóm từ trước. Vào buổi thì nói một tiếng nói: đây là việc nhóm đang xử lý, không phải lỗi của một người.
4. **Không giấu ba chỗ hỏng.** CI đỏ, timeline sai ngày, một trên bốn người có sản lượng — nói trước cả ba. Tự tìm ra lỗi của mình và sửa là **điểm cộng**; để thầy phát hiện mới là điểm trừ.

---

*Người soạn: Khải · 16/09/2026 · Dựa trên [`Bao_cao_tuan_W07_ca_nhom.md`](Bao_cao_tuan_W07_ca_nhom.md) và [`Kich_ban_trinh_bay_Week6.md`](../Week6/06_Kich_ban_trinh_bay/Kich_ban_trinh_bay_Week6.md)*
