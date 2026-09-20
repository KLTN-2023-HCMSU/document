# Nhận xét kiến trúc v3 + bộ sơ đồ đơn giản

**Người review:** Claude (theo yêu cầu của Hưng)
**Ngày:** 2026-09-20
**File được review:** `architectures/architecture_v3.md` (1222 dòng, ~12 sơ đồ Mermaid)

---

## Phần A — Bộ sơ đồ đơn giản (thay cho 12 sơ đồ hiện tại)

Nằm trong `architectures/diagrams/`, mỗi sơ đồ có cả `.svg` (sửa được) và `.png` (dán vào slide/Word).

| # | Ảnh | Trả lời câu hỏi |
| --- | --- | --- |
| 1 | `01-boi-canh.png` | Ai dùng hệ thống, hệ thống nói chuyện với bên ngoài nào |
| 2 | `02-tong-the.png` | Hệ thống gồm những khối chạy nào |
| 3 | `03-luong-scan.png` | Một lần quét đi qua những bước gì |
| 4 | `04-cham-diem.png` | Điểm rủi ro ở đâu ra |
| 5 | `05-du-lieu-lua-dao.png` | Dữ liệu lừa đảo được nạp và tra cứu thế nào |

### Nguyên tắc đã áp dụng khi vẽ lại

Kiến trúc sư vẽ đơn giản không phải vì hệ thống đơn giản, mà vì họ tuân thủ mấy luật sau:

1. **Một sơ đồ = một câu hỏi.** Không nhồi context + container + component + luồng vào cùng một hình.
2. **Tối đa ~12 khối mỗi hình.** Qua 12 là mắt người mất khả năng quét toàn cảnh. Sơ đồ 2.2 hiện tại của v3 có **26 node và 31 mũi tên** — đó là lý do thấy rối.
3. **Mũi tên phải đọc được thành câu.** "Nginx → Spring Boot" nghĩa là gì thì ghi lên mũi tên, còn không thì bỏ mũi tên đó.
4. **Tên khối bằng ngôn ngữ người đọc, không phải tên class.** "Tra cứu uy tín" dễ hiểu hơn "Entity/Reputation Checker Worker"; tên kỹ thuật để dành cho tài liệu chi tiết.
5. **Chi tiết không mất đi — nó xuống bảng.** Cái gì là danh sách (10 core function, 9 queue, 14 routing key) thì viết bảng, đừng vẽ hình.

---

## Phần B — Nhận xét nội dung

### B1. Những chỗ v3 làm đúng, nên giữ

- **Modular Monolith + worker bất đồng bộ** là lựa chọn đúng cho phạm vi khoá luận. Không đu theo microservices — điểm cộng khi bảo vệ.
- **Worker chỉ trả tín hiệu, backend mới ra kết luận.** Đây là quyết định kiến trúc tốt nhất trong tài liệu. Giữ nguyên và nhấn mạnh khi trình bày.
- **AI là tín hiệu bổ sung, mất AI hệ thống vẫn chạy.** Rất đúng — vừa an toàn kỹ thuật vừa an toàn khi hội đồng hỏi "model của em accuracy bao nhiêu".
- **Mục 7 (SSRF protection)** viết chắc tay. Đa số đồ án cùng đề tài quên hẳn phần này.
- **QR Worker không tự gọi worker khác**, trả `DerivedIndicator[]` cho Orchestrator dispatch. Đúng — tránh worker gọi chéo nhau thành mạng nhện.
- **Dùng HMAC keyed digest thay vì hash thường cho CCCD.** Chi tiết nhỏ nhưng đúng về mặt an ninh (không gian CCCD hữu hạn, hash thường brute-force được).
- **Input/Output Coverage Matrix** là ý tưởng hay: buộc mọi input phải có chủ xử lý.

### B2. Vấn đề cần sửa — xếp theo mức độ quan trọng

#### 🔴 1. `architecture_v1.md` và `architecture_v2.md` giống hệt nhau từng byte

```
$ cmp architecture_v1.md architecture_v2.md   →  không khác gì
```

Repo đang có 2 file y hệt nhau mang 2 tên phiên bản khác nhau. Người chấm mở ra sẽ hỏi ngay. Xử lý: xoá một file, hoặc viết `architectures/CHANGELOG.md` nói rõ v1→v2→v3 đổi cái gì.

#### ✅ 2. Worker gọi ngược vào Spring Boot để tra uy tín — mâu thuẫn với chính mục 7 — ĐÃ CHỐT

Sơ đồ mục 2.2 có:

```
WebWorker    --> Threat / reputation query --> Backend
EntityWorker --> Threat / reputation query --> Backend
```

Nhưng mục 7 lại yêu cầu *"Network-isolate worker khỏi management plane/database nếu có thể"*. Hai điều này chửi nhau. Và cách hiện tại tạo ra **phụ thuộc vòng**: Backend → RabbitMQ → Worker → Backend (đồng bộ). Backend nghẽn thì worker treo theo, mất luôn ý nghĩa của việc chạy bất đồng bộ.

Chọn một trong ba, rồi ghi rõ:

- **(a)** Worker đọc thẳng Redis threat cache ở chế độ read-only — nhanh nhất, nhưng Redis thành phụ thuộc cứng của worker.
- **(b)** Tách một internal endpoint `threat-lookup` riêng hẳn khỏi API công khai, có timeout ngắn + circuit breaker. Worker treo thì trả tín hiệu `REPUTATION_UNAVAILABLE` chứ không chờ.
- **(c)** Backend tra sẵn uy tín rồi **nhét luôn vào message job** — worker không gọi ngược gì cả. Sạch nhất về mặt phụ thuộc, nhưng message to hơn và dữ liệu có thể cũ vài giây.

Gợi ý cho MVP lúc viết review: **(c) cho lượt tra biết trước, (b) cho lượt tra phát sinh giữa chừng.**

> **Quyết định của nhóm:** chọn **(c) cho cả hai trường hợp** — cô lập worker hoàn toàn, **không** dùng (b).
>
> Lượt tra phát sinh giữa chừng được giải bằng cách khác thay vì mở endpoint cho worker: worker trả chỉ dấu mới về trong `derivedIndicators[]` kèm trường `handling`, rồi lõi tra hộ khi consume result event. Với `handling=REPUTATION_ONLY` lõi tra ngay tại chỗ và gắn signal vào scan cha, không tạo scan con và không tốn thêm vòng hàng đợi — nên chi phí gần bằng phương án (b) mà không phải mở đường đồng bộ nào.
>
> Phương án (a) bị loại vì biến Redis thành phụ thuộc cứng của worker, đi ngược đúng cái mục 7 yêu cầu.
>
> Đánh đổi đã chấp nhận: worker không biết uy tín của host mới **trong lúc** đang chạy nên không rẽ nhánh giữa chừng được. Chấp nhận được vì an toàn khi fetch do SSRF protection bảo đảm, còn reputation chỉ là một nhóm signal được chấm ở lõi.
>
> Đặc tả: `architecture_v3.1.md` mục 4.6.1 (revision V3.2). Sơ đồ `02-tong-the-v2` và `05-du-lieu-lua-dao` đã vẽ lại, không còn mũi tên đồng bộ từ worker về lõi.

#### 🟠 3. Số lượng scan type và endpoint đang phình gấp đôi mức cần thiết

Hiện có 8 endpoint scan, 9 queue, 14 routing key. Trong đó:

- `TRANSACTION_POST` **dùng chung worker, chung function, chung pipeline** với `TEXT`, chỉ khác bộ rule. Tài liệu tự thừa nhận `(hoặc dùng /text + contentType)`. → Bỏ scan type riêng, dùng `TEXT` + `contentType`.
- `/phone`, `/bank-account`, `/cccd` **cùng đi vào một worker, một hàm `checkEntity()`**. → Bỏ, chỉ giữ `/entity` + `entityType`.
- `WEB_CONTENT` mở thành endpoint công khai nhưng **không ai dùng**: người dùng thật không dán HTML thô. Thực tế nó chạy như một bước *bên trong* luồng quét URL. → Giữ làm mode nội bộ, gỡ khỏi API công khai (hoặc chỉ mở cho admin để test rule).

Sau khi gộp:

| Trước | Sau |
| --- | --- |
| 8 endpoint scan | **4**: `/url`, `/text`, `/entity`, `/qr` |
| 9 queue | **6** |
| 14 routing key | **8** |

Không mất tính năng nào. Đây là thay đổi làm kiến trúc bớt rối nhiều nhất, và cũng là thứ dễ bảo vệ nhất trước hội đồng: *"em gộp vì chúng dùng chung processor, khác nhau chỉ ở bộ luật."*

#### 🟠 4. Chỗ dễ chết nhất của kiến trúc chỉ được nhắc bằng một dòng

Toàn bộ cơ chế scan con (`checkCompletionBarrier()`) — thứ quyết định hệ thống có treo hay không — chỉ xuất hiện đúng một dòng trong danh sách function ở mục 4.7. Cần một mục riêng chốt 4 điều:

1. Đếm scan con bằng gì? (counter trong `scan_relations`, hay `DECR` trên Redis)
2. Parent chờ tối đa bao lâu? (đề xuất: 20–30s)
3. Một scan con fail thì parent làm gì? → **chấm điểm với phần tín hiệu đã có, gắn cờ `degraded`, không treo `PROCESSING` vĩnh viễn.**
4. Có chặn đệ quy không? Một trang web chứa QR, QR chứa URL, URL đó lại chứa QR… → **giới hạn độ sâu tối đa 2 cấp.**

Thiếu điểm 4, hệ thống có thể tự tạo scan vô hạn. Đây là câu hỏi hội đồng rất hay hỏi.

#### 🟠 5. Risk Fusion chưa phải là thiết kế, mới là cái tên

*"Kết hợp signal theo policy/version"* chưa nói được gì. Tối thiểu phải có:

- Trọng số từng nhóm tín hiệu (kỹ thuật / nội dung / uy tín / AI) — ghi thành bảng số cụ thể.
- Luật đè cứng: ví dụ domain nằm trong blacklist **đã xác minh** → `DANGER` bất kể tổng điểm.
- Ngưỡng cắt mức: SAFE < ? ≤ CAUTION < ? ≤ DANGER.
- Xử lý khi thiếu tín hiệu: AI tắt thì **chuẩn hoá lại trọng số các nhóm còn lại**, chứ không cộng 0 — nếu cộng 0, tắt AI là mọi thứ tự động an toàn hơn, sai hoàn toàn.

#### 🟡 6. CCCD: rủi ro cao nhất, giá trị thấp nhất

Tài liệu tự viết: *"Không gọi nguồn dữ liệu định danh chính phủ nếu không có quyền truy cập hợp pháp."* Đúng — và hệ quả là: tra CCCD chỉ còn dựa vào báo cáo cộng đồng. Lúc MVP dữ liệu này gần như rỗng, nên tính năng sẽ **luôn trả về "không có tín hiệu"** trong khi vẫn phải gánh toàn bộ chi phí bảo vệ dữ liệu cá nhân (HMAC, masking, cấm log, cấm đưa vào message…).

Hai hướng, cả hai đều mạnh hơn hiện tại:

- **Bỏ CCCD khỏi MVP**, ghi vào phần "hướng phát triển". Gọn, an toàn.
- **Lật ngược tính năng**: không tra CCCD, mà **phát hiện CCCD xuất hiện trong tin nhắn/bài đăng** và cảnh báo *"đối tượng đang yêu cầu bạn cung cấp CCCD — đây là dấu hiệu lừa đảo phổ biến"*. Cái này **mới đúng là tín hiệu lừa đảo thật**, giá trị cao hơn hẳn, và **không cần lưu hay tra cứu gì cả**.

Hướng thứ hai đáng để trình bày — nó cho thấy nhóm hiểu bài toán nghiệp vụ, không chỉ làm theo danh sách input.

#### 🟡 7. Không có một con số nào về vận hành

Cả 1222 dòng không có: mục tiêu độ trễ, số scan/giây, timeout worker, số lần retry, TTL cache, giới hạn kích thước input. Checklist mục 10 có 16 câu hỏi nhưng không câu nào là số. Bổ sung một bảng nhỏ ~10 dòng là đủ, và nó sẽ cứu nhóm ở phần hỏi đáp.

#### 🟡 8. Thiếu exchange trong phần RabbitMQ

Mục 5 liệt kê queue và routing key nhưng **không nói exchange nào, kiểu gì, bind ra sao**. Routing key dạng `scan.url.requested` ngụ ý topic exchange — cần ghi rõ, kèm cấu hình DLQ (hiện chỉ nói "nên có").

#### 🟡 9. Hai lớp rate limit chồng nhau mà không phân vai

Nginx "giới hạn request cơ bản" + Redis rate limit trong backend. Không sai, nhưng phải nói rõ ai lo gì: **Nginx chặn flood theo IP; Backend áp quota theo tài khoản người dùng.** Không nói thì khi debug sẽ không biết ai đang chặn.

### B3. Vấn đề về cách trình bày tài liệu

1. **Mục 3.1 vẽ lại gần như y hệt mục 2.2.** Cùng một bức tranh, vẽ hai lần, hai cách đặt tên hơi khác → người đọc tưởng là hai thứ khác nhau. **Xoá mục 3.1**, trỏ về 2.2.
2. **Trộn ba mức trừu tượng trong một file.** C4 L1/L2/L3 + luồng tuần tự + danh sách hàm + schema DB + endpoint nằm chung một chỗ. Đề xuất tách:
   - `architecture.md` — bản chính, ~300 dòng, dùng 5 ảnh ở trên. Đây là file để nộp và để trình bày.
   - `architecture-detail.md` — contract, queue, danh sách core function, bảng dữ liệu.
   - `architecture-decisions.md` — ghi *vì sao* chọn monolith, vì sao RabbitMQ, vì sao tách Python. Hội đồng hỏi "sao không microservices" thì mở file này ra.
3. **Bảng client feature parity (mục 2.2.1) không thuộc về tài liệu kiến trúc** — đó là tài liệu tính năng. Chuyển sang thư mục feature, ở đây chỉ giữ một câu: *"Web và Mobile dùng chung API và chung bộ luật nghiệp vụ."*
4. **Checklist 16 câu ở mục 10** rút còn ~6 câu cốt lõi, phần còn lại là chi tiết triển khai.
5. **Thuật ngữ Anh–Việt lẫn lộn** ("Signal Aggregator" / "tín hiệu", "Risk Fusion" / "chấm điểm"). Thêm một bảng thuật ngữ ở đầu và dùng nhất quán.

---

## Phần C — Việc nên làm, theo thứ tự

1. Xử lý trùng lặp `v1` / `v2` (5 phút).
2. ~~Chốt hướng worker tra uy tín — B2.2~~ ✅ **đã chốt: cô lập worker, phương án (c)**; xem `architecture_v3.1.md` mục 4.6.1.
3. Gộp scan type, cắt còn 4 endpoint — B2.3.
4. Viết mục "Xử lý scan con và điều kiện hoàn tất" — B2.4.
5. Viết bảng trọng số + ngưỡng cho Risk Fusion — B2.5.
6. Quyết định số phận CCCD — B2.6 (nên bàn với giảng viên hướng dẫn).
7. Thêm bảng chỉ tiêu vận hành — B2.7.
8. Tách file và thay sơ đồ bằng 5 ảnh — B3.

Bước 1, 3, 8 làm xong là tài liệu đã bớt rối đi rõ rệt mà chưa cần đụng tới thiết kế.
