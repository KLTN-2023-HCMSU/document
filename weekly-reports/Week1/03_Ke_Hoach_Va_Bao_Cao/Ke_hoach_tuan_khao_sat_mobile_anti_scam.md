# Kế hoạch tuần: Khảo sát thị trường và định hướng app mobile chống lừa đảo

**Đề tài:** Xây dựng nền tảng web/mobile kiểm tra và cảnh báo rủi ro lừa đảo trực tuyến trong giao dịch đời sống bằng Java Backend  
**Trọng tâm tuần này:** khảo sát thị trường, xác định góc nhìn người dùng mobile, tìm cách xây dựng niềm tin, thu thập link hội nghị/cộng đồng và đề xuất cách triển khai demo.

## 1. Mục tiêu tuần này

Tuần này cần trả lời được 4 câu hỏi chính:

1. Trên thị trường đã có web/app nào làm chống lừa đảo, kiểm tra link, số điện thoại, số tài khoản, SMS, QR hoặc cảnh báo scam?
2. Nếu sản phẩm của mình là app mobile chạy ngầm, người dùng sẽ nhìn thấy, hiểu và tin nó như thế nào?
3. Làm sao để người dùng tin tưởng app khi app xử lý dữ liệu nhạy cảm như link, tin nhắn, số điện thoại, số tài khoản hoặc cảnh báo giao dịch?
4. Các hội nghị, tổ chức, cộng đồng nào có thể dùng làm nguồn tham khảo, nguồn dữ liệu, nguồn uy tín hoặc nơi cập nhật xu hướng?

## 2. Kết quả cần có sau tuần này

| Nhóm đầu ra | Sản phẩm cần hoàn thành | Cách kiểm tra hoàn thành |
|---|---|---|
| Khảo sát thị trường | Bảng so sánh ít nhất 8 web/app/nền tảng chống lừa đảo | Có link, tính năng, điểm mạnh, điểm yếu, bài học áp dụng |
| Góc nhìn người dùng | 3-5 user scenario cho app mobile chạy ngầm | Mỗi scenario có bối cảnh, hành động, cảnh báo, quyết định người dùng |
| Niềm tin và chứng chỉ | Danh sách yếu tố giúp người dùng tin tưởng app | Có privacy policy, minh bạch dữ liệu, chứng nhận/chuẩn, quy trình báo cáo sai |
| Link hội nghị/cộng đồng | Danh sách link chính thức | Có nhóm Việt Nam, quốc tế, hội nghị, cộng đồng nghiên cứu |
| Định hướng demo | Phạm vi MVP mobile + backend | Có checklist tính năng, API, dữ liệu mẫu, rủi ro pháp lý/kỹ thuật |

## 3. Viết dưới góc nhìn người dùng: app chạy ngầm trên mobile

### 3.1. Tư duy sản phẩm

Không nên mô tả app như một công cụ kỹ thuật kiểu "scanner", mà nên mô tả như một trợ lý an toàn chạy nền:

> "App âm thầm theo dõi các tín hiệu rủi ro do người dùng cho phép, cảnh báo đúng lúc trước khi người dùng bấm link, quét QR, nhận cuộc gọi đáng ngờ hoặc chuyển tiền."

Điểm quan trọng là app không được tạo cảm giác theo dõi quá mức. Người dùng cần hiểu rõ:

- App kiểm tra loại dữ liệu nào.
- App kiểm tra vào thời điểm nào.
- Dữ liệu nào được gửi lên server, dữ liệu nào xử lý trên máy.
- Người dùng có thể tắt quyền hoặc xóa lịch sử bất cứ lúc nào.
- App chỉ đưa ra "đánh giá rủi ro", không kết luận pháp lý rằng ai đó chắc chắn lừa đảo.

### 3.2. User scenario 1: Người dùng nhận SMS có link lạ

**Bối cảnh:** Người dùng nhận SMS: "Tài khoản của bạn sắp bị khóa, xác minh tại link ...".

**Cách app hoạt động:**

1. App phát hiện trong thông báo/SMS có URL.
2. App chuẩn hóa URL, tách domain và gửi domain/link đã rút gọn hoặc hash lên backend.
3. Backend kiểm tra blacklist, redirect, HTTPS, domain similarity, lịch sử báo cáo.
4. App hiện cảnh báo: "Link có dấu hiệu giả mạo. Không nhập OTP/mật khẩu."
5. Người dùng có 3 lựa chọn: bỏ qua, xem lý do, gửi báo cáo.

**Điểm cần thiết kế:** cảnh báo phải ngắn, dễ hiểu, có mức độ màu sắc rõ ràng: An toàn tương đối, Cần cẩn trọng, Rủi ro cao.

### 3.3. User scenario 2: Người dùng chuẩn bị chuyển tiền

**Bối cảnh:** Người dùng mua hàng online và nhận số tài khoản ngân hàng để đặt cọc.

**Cách app hoạt động:**

1. Người dùng copy số tài khoản hoặc nhập vào app.
2. App kiểm tra số tài khoản trong database rủi ro và lịch sử báo cáo cộng đồng.
3. Nếu có báo cáo, app hiển thị: số lần bị báo cáo, trạng thái xác minh, thời gian báo cáo gần nhất.
4. App khuyến nghị: "Không chuyển tiền nếu chưa xác minh lại người nhận qua kênh chính thức."

**Điểm cần thiết kế:** không nên hiện câu "tài khoản này chắc chắn lừa đảo" nếu chưa được kiểm duyệt. Nên dùng cách viết: "Tài khoản này có rủi ro cao do đã có nhiều báo cáo."

### 3.4. User scenario 3: Người dùng quét mã QR

**Bối cảnh:** Người dùng thấy mã QR trong bài đăng khuyến mãi hoặc vé sự kiện.

**Cách app hoạt động:**

1. App mở camera/quét QR theo thao tác chủ động của người dùng.
2. Nếu QR chứa URL, app kiểm tra như URL scanner.
3. Nếu QR chứa nội dung chuyển khoản, app tách số tài khoản/ngân hàng/số tiền/nội dung.
4. App cảnh báo nếu domain hoặc tài khoản có lịch sử rủi ro.

**Điểm cần thiết kế:** QR là use case dễ demo, ít cần quyền nhạy cảm hơn đọc SMS/call log tự động.

### 3.5. User scenario 4: Người dùng nhận cuộc gọi lạ

**Bối cảnh:** Người dùng nhận cuộc gọi tự xưng ngân hàng/công an/giao hàng.

**Cách app hoạt động:**

1. App kiểm tra số điện thoại gọi đến nếu người dùng cấp quyền phù hợp.
2. Nếu số đã bị báo cáo, app hiện cảnh báo hoặc notification.
3. Người dùng có thể xem lý do: số bị báo cáo quảng cáo, làm phiền, giả danh, yêu cầu chuyển tiền.
4. Người dùng có thể gửi báo cáo sau cuộc gọi.

**Điểm cần thiết kế:** chức năng này liên quan quyền nhạy cảm. Với demo khóa luận, có thể làm ở mức nhập số thủ công trước, sau đó trình bày auto-detect là hướng mở rộng.

## 4. Khảo sát web/app trên thị trường

### 4.1. Bảng khảo sát nhanh

| Sản phẩm/nền tảng | Link | Tính năng chính | Bài học áp dụng vào đề tài |
|---|---|---|---|
| nTrust | https://ntrust.vn/ và Google Play/App Store | Kiểm tra số điện thoại, link website, số tài khoản, mã QR, ứng dụng đáng ngờ; có chặn cuộc gọi lừa đảo trên Android | Chứng minh nhu cầu tại Việt Nam không chỉ là URL mà còn phone/account/QR/app |
| ChongLuaDao | https://chongluadao.vn/ | Cộng đồng chống lừa đảo, extension cảnh báo website nguy hiểm, danh sách chặn, báo cáo URL | Học mô hình cộng đồng + blocklist + mức cảnh báo Safe/Careful/Danger |
| Tín Nhiệm Mạng | https://tinnhiemmang.vn/website-lua-dao | Danh sách đen website/mạng xã hội giả mạo, trạng thái xử lý, phân loại lĩnh vực | Học cách quản trị dữ liệu rủi ro, trạng thái xác minh và phân loại đối tượng |
| Google Safe Browsing | https://developers.google.com/safe-browsing/reference | API kiểm tra URL nguy hiểm theo danh sách cập nhật | Có thể tham khảo threat intelligence/blacklist lookup, nhưng cần xem điều kiện sử dụng |
| PhishTank | https://www.phishtank.net/faq.php | Cộng đồng submit/verify phishing, có public API | Học luồng user report -> verify -> đưa vào danh sách cảnh báo |
| VirusTotal | https://docs.virustotal.com/reference/overview | Tổng hợp phân tích URL/domain/file từ nhiều engine bảo mật | Học cách gom nhiều tín hiệu và giải thích kết quả, tránh dựa vào một rule |
| ScamAdviser | https://www.scamadviser.com/home | Website trust score/scam checker cho người tiêu dùng | Học cách trình bày trust score, lý do ngắn gọn, khuyến nghị hành động |
| CheckPhish | https://checkphish.bolster.ai/ | URL scanner, email scanner, typosquat/lookalike monitoring | Học module kiểm tra domain gần giống thương hiệu thật |
| F-Secure Text Message Checker | https://www.f-secure.com/us-en/text-message-checker | Cho người dùng paste sender và nội dung SMS để AI kiểm tra scam text/smishing | Chứng minh hướng phân tích nội dung tin nhắn là nhu cầu thật, không chỉ kiểm link |
| Bitdefender Scamio | https://www.bitdefender.com/en-us/consumer/scamio | Chatbot AI cho phép gửi text, email, social media message, link hoặc QR code để phân tích scam | Học cách trả kết quả theo kiểu hội thoại, dễ hiểu, có giải thích |
| Ask Silver | https://www.asksilver.com/ | Kiểm tra text message, email, website hoặc phone number để giúp người dùng nhận diện scam | Học cách thiết kế trải nghiệm đơn giản cho người dùng phổ thông |
| AI Scam Checker | https://apps.apple.com/us/app/ai-scam-checker/id6670762828 | App cho kiểm tra suspicious messages, links, QR codes, screenshots và phone calls trước khi click/trả lời/trả tiền | Gợi ý hướng kết hợp message + QR + screenshot + call cho app mobile |
| IsThisSpam | https://isthisspam.org/ | Phân tích suspicious emails/texts bằng AI, có reasoning và PDF report | Học cách tạo báo cáo giải thích cho từng tình huống đáng ngờ |
| Bitdefender Mobile Security | https://play.google.com/store/apps/details?id=com.bitdefender.security | Scam protection cho link trong text, messaging apps, notifications; cảnh báo scam wave | Gợi ý hướng app mobile chạy nền và cảnh báo theo notification |
| Avast One Mobile | https://www.avast.com/free-mobile-security | AI-powered scam protection, cảnh báo website/text/offer đáng ngờ | Học cách định vị sản phẩm như bảo vệ đời sống số, không chỉ antivirus |
| NordVPN Message Protection | https://www.techradar.com/vpn/vpn-services/nordvpn-expands-scam-text-protection-to-all-android-users-worldwide | Tự động sàng lọc SMS, dùng phone reputation, URL analysis, content categorization; có push notification | Gợi ý luồng chạy nền nhưng phải minh bạch quyền và không lưu nội dung tin nhắn |

### 4.2. Nhóm web/app có phân tích nội dung tin nhắn/giao dịch

Ngoài các công cụ kiểm tra URL, domain, số điện thoại hoặc số tài khoản, thị trường đã có một số sản phẩm cho phép người dùng dán trực tiếp nội dung tin nhắn, email, đoạn chat hoặc mô tả cuộc gọi đáng ngờ để phân tích. Đây là nhóm sản phẩm liên quan trực tiếp đến mục **3.7. Phân tích nội dung tin nhắn/giao dịch** của đề tài.

| Sản phẩm | Loại nội dung có thể kiểm tra | Bài học áp dụng |
|---|---|---|
| F-Secure Text Message Checker | Sender + nội dung SMS | Có thể thiết kế form nhập nội dung tin nhắn, sau đó phát hiện từ khóa khẩn cấp, giả mạo, yêu cầu OTP, yêu cầu chuyển tiền |
| Bitdefender Scamio | Text, email, social media message, link, QR code | Có thể trả kết quả theo dạng hội thoại: mức rủi ro, dấu hiệu đáng ngờ, khuyến nghị hành động |
| Ask Silver | Text message, email, website, phone number | Cho thấy người dùng phổ thông cần giao diện cực đơn giản: dán nội dung và nhận câu trả lời dễ hiểu |
| AI Scam Checker | Message, link, QR, screenshot, phone call | Gợi ý hướng phát triển OCR/screenshot và mô tả cuộc gọi đáng ngờ |
| IsThisSpam | Email, SMS, website, phishing attempt | Gợi ý chức năng xuất báo cáo giải thích chi tiết cho từng lần kiểm tra |

Nhận xét quan trọng: các công cụ quốc tế thường xử lý tốt SMS/email tiếng Anh hoặc các mẫu scam phổ biến toàn cầu. Tuy nhiên, phần **giao dịch đời sống tại Việt Nam** như đặt cọc phòng trọ, tuyển dụng yêu cầu đóng phí, mua vé sự kiện, hoàn tiền giả, chuyển khoản giữ chỗ, giả danh shipper/ngân hàng/công an vẫn là khoảng trống phù hợp cho khóa luận. Đề tài có thể khai thác hướng này bằng rule engine, keyword matching, pattern extraction và dữ liệu mẫu tiếng Việt.

Ví dụ tiêu chí phân tích nội dung tin nhắn/giao dịch:

- Có yếu tố thúc ép thời gian: "gấp", "ngay lập tức", "trong 5 phút", "tài khoản sẽ bị khóa".
- Có yêu cầu thông tin nhạy cảm: OTP, mật khẩu, CCCD, số thẻ, mã xác minh.
- Có yêu cầu chuyển tiền trước: đặt cọc, phí hồ sơ, phí giữ chỗ, phí nhận thưởng, phí hoàn tiền.
- Có dấu hiệu giả danh: ngân hàng, công an, shipper, sàn thương mại điện tử, nhà tuyển dụng.
- Có thông tin giao dịch cần tách ra: URL, số điện thoại, số tài khoản, số tiền, tên ngân hàng, deadline.
- Có dấu hiệu "quá tốt để là thật": trúng thưởng lớn, việc nhẹ lương cao, hoàn tiền bất thường.

Với bản demo, hệ thống không cần dùng AI phức tạp ngay từ đầu. Có thể làm phiên bản cơ bản bằng:

1. Regex để tách URL, số điện thoại, số tài khoản, số tiền, mã OTP nếu có.
2. Từ điển keyword tiếng Việt theo nhóm rủi ro: khẩn cấp, giả danh, chuyển tiền, nhận thưởng, khóa tài khoản.
3. Rule Engine cộng điểm theo từng dấu hiệu.
4. Kết quả trả về gồm điểm rủi ro, mức cảnh báo, lý do và khuyến nghị.
5. Lưu lịch sử kiểm tra và cho phép người dùng gửi báo cáo nếu gặp trường hợp lừa đảo mới.

### 4.3. Nhận xét thị trường

Các sản phẩm hiện tại chia thành 5 nhóm:

- **Nhóm cộng đồng Việt Nam:** nTrust, ChongLuaDao, Tín Nhiệm Mạng. Mạnh ở bối cảnh Việt Nam, số điện thoại, tài khoản, website giả mạo, báo cáo cộng đồng.
- **Nhóm threat intelligence quốc tế:** Google Safe Browsing, PhishTank, VirusTotal. Mạnh ở dữ liệu URL/domain/phishing quy mô lớn.
- **Nhóm trust score tiêu dùng:** ScamAdviser, CheckPhish. Mạnh ở trình bày dễ hiểu và kiểm tra domain/lookalike.
- **Nhóm phân tích nội dung tin nhắn/giao dịch:** F-Secure Text Message Checker, Bitdefender Scamio, Ask Silver, AI Scam Checker, IsThisSpam. Mạnh ở việc cho người dùng paste nội dung đáng ngờ và nhận giải thích dễ hiểu.
- **Nhóm mobile security:** Bitdefender, Avast, NordVPN. Mạnh ở bảo vệ trên điện thoại, chạy nền, thông báo tức thời, tích hợp SMS/notification/link protection.

Khoảng trống phù hợp cho khóa luận:

- Làm một demo học thuật bằng Java Backend có phạm vi vừa sức.
- Kết hợp nhiều loại dữ liệu: URL, form, tin nhắn, số điện thoại, số tài khoản, QR và báo cáo cộng đồng.
- Nhấn mạnh phân tích tin nhắn/giao dịch tiếng Việt: đặt cọc, tuyển dụng, thuê trọ, mua bán online, hoàn tiền, giả danh shipper/ngân hàng/cơ quan chức năng.
- Tập trung vào giải thích kết quả cho người dùng phổ thông.
- Có dashboard quản trị để kiểm duyệt báo cáo, quản lý rule và dữ liệu rủi ro.

## 5. Làm sao để người ta tin tưởng mình?

### 5.1. Niềm tin đến từ minh bạch, không chỉ từ lời hứa

Người dùng sẽ nghi ngại vì app chống lừa đảo lại yêu cầu quyền nhạy cảm. Vì vậy phải thiết kế niềm tin ngay từ đầu:

- Minh bạch quyền: giải thích vì sao cần quyền notification/SMS/call/overlay/accessibility nếu dùng.
- Tối thiểu dữ liệu: chỉ gửi domain, URL đã chuẩn hóa, hash hoặc metadata khi có thể; không gửi toàn bộ tin nhắn nếu chưa cần.
- Cho phép kiểm soát: người dùng bật/tắt từng module, xóa lịch sử, rút quyền.
- Giải thích kết quả: mỗi cảnh báo phải có lý do cụ thể, ví dụ "domain mới tạo", "không dùng HTTPS", "số tài khoản có 5 báo cáo".
- Có cơ chế khiếu nại/sửa sai: nếu báo cáo nhầm, chủ thể có thể gửi yêu cầu xem xét.
- Không dùng ngôn ngữ tuyệt đối: dùng "rủi ro cao", "cần xác minh", "đã có báo cáo", thay vì "chắc chắn lừa đảo".

### 5.2. Chứng chỉ, chuẩn và tín hiệu uy tín nên đưa vào tài liệu

| Nhóm uy tín | Nên làm trong khóa luận | Lý do |
|---|---|---|
| Google Play Data Safety | Mô phỏng/chuẩn bị bảng khai báo dữ liệu thu thập, chia sẻ, bảo vệ | Google Play yêu cầu mô tả thực hành dữ liệu để người dùng hiểu trước khi cài app |
| Privacy Policy | Viết trang chính sách quyền riêng tư đơn giản | Người dùng biết dữ liệu nào được thu thập và dùng để làm gì |
| OWASP MASVS | Dùng làm checklist bảo mật mobile | Đây là chuẩn tham khảo phổ biến cho bảo mật ứng dụng mobile |
| OWASP ASVS | Dùng cho backend/API | Có tiêu chí kiểm thử xác thực, phân quyền, session, input validation |
| Play App Signing / Play Integrity API | Đưa vào hướng triển khai thật | Giúp chứng minh app là bản chính chủ, giảm giả mạo client |
| HTTPS/TLS | Bắt buộc cho API | Bảo vệ dữ liệu gửi giữa app và backend |
| Audit log | Lưu thao tác admin và kiểm duyệt báo cáo | Tăng tính minh bạch và truy vết |
| Public methodology | Công khai cách tính điểm ở mức khái quát | Người dùng hiểu vì sao app cảnh báo |

### 5.3. Thứ tự ưu tiên để xây niềm tin trong demo

1. Màn hình onboarding giải thích app làm gì và không làm gì.
2. Màn hình quyền riêng tư: quyền nào bắt buộc, quyền nào tùy chọn.
3. Kết quả kiểm tra có lý do và khuyến nghị.
4. Trang "Phương pháp chấm điểm rủi ro".
5. Trang "Nguồn dữ liệu và báo cáo cộng đồng".
6. Admin dashboard có trạng thái kiểm duyệt: Pending, Verified, Rejected, Resolved.
7. Tài liệu Privacy Policy và Data Safety.

## 6. Phạm vi kỹ thuật cho app mobile chạy ngầm

### 6.1. Lưu ý nền tảng Android

Android giới hạn việc chạy nền để bảo vệ pin, RAM và quyền riêng tư. Vì vậy không nên thiết kế app kiểu "luôn đọc mọi thứ". Cách phù hợp hơn:

- Dùng foreground service nếu tác vụ chạy nền cần người dùng biết rõ, kèm notification thường trực.
- Dùng notification listener hoặc quyền phù hợp chỉ khi thật sự cần đọc notification.
- Với SMS/call log/accessibility, phải cân nhắc chính sách Google Play và sự đồng ý rõ ràng của người dùng.
- Với demo khóa luận, nên ưu tiên thao tác chủ động: paste link, share link vào app, quét QR, nhập số điện thoại/số tài khoản.

### 6.2. MVP mobile nên làm trước

| Tính năng | Mức ưu tiên | Ghi chú |
|---|---:|---|
| Nhập/paste URL để kiểm tra | Cao | Dễ demo, ít quyền nhạy cảm |
| Share URL từ trình duyệt/app khác vào app | Cao | Gần với trải nghiệm mobile thật |
| Quét QR chứa URL hoặc thông tin chuyển khoản | Cao | Use case đời sống rõ ràng |
| Nhập số điện thoại/số tài khoản để kiểm tra | Cao | Phù hợp thị trường Việt Nam |
| Push notification kết quả scan | Trung bình | Có thể dùng sau khi backend trả kết quả |
| Chạy nền kiểm tra notification/SMS | Mở rộng | Cần quyền nhạy cảm và giải thích kỹ |
| Cảnh báo cuộc gọi realtime | Mở rộng | Có rào cản quyền call log/phone |
| Accessibility service để đọc màn hình | Chỉ nghiên cứu | Nhạy cảm, dễ mất niềm tin nếu dùng sai mục đích |

### 6.3. Kiến trúc đề xuất

```text
Mobile App
  -> URL/QR/Text/Phone/Bank input
  -> REST API Spring Boot
  -> Normalizer
  -> URL Scanner / Text Analyzer / Phone & Bank Checker
  -> Rule Engine
  -> PostgreSQL + Redis
  -> Risk Result + Explanation + Recommendation
  -> Mobile notification/result screen
```

### 6.4. API cần thiết cho demo

| API | Method | Mục đích |
|---|---|---|
| `/api/mobile/scan/url` | POST | Kiểm tra URL/link |
| `/api/mobile/scan/text` | POST | Kiểm tra tin nhắn/bài đăng |
| `/api/mobile/scan/qr` | POST | Kiểm tra dữ liệu trích từ QR |
| `/api/mobile/check/phone` | POST | Kiểm tra số điện thoại |
| `/api/mobile/check/bank-account` | POST | Kiểm tra số tài khoản |
| `/api/mobile/reports` | POST | Gửi báo cáo lừa đảo |
| `/api/mobile/history` | GET | Xem lịch sử kiểm tra |
| `/api/admin/reports/{id}/review` | PATCH | Admin duyệt/từ chối báo cáo |
| `/api/admin/rules` | CRUD | Quản lý rule chấm điểm |
| `/api/admin/risk-entities` | CRUD | Quản lý domain/phone/account/keyword rủi ro |

## 7. Link hội nghị, tổ chức và nền tảng cộng đồng

### 7.1. Hội nghị và tổ chức quốc tế

| Tên | Link | Vì sao liên quan |
|---|---|---|
| Global Anti-Scam Alliance (GASA) | https://gasa.org/ | Mạng lưới chống scam toàn cầu, có knowledge base, summit, thành viên quốc tế |
| Global Anti-Scam Summit America 2026 | https://gasa.org/global-anti-scam-summit-america-2026 | Hội nghị chuyên về scam, fraud, phối hợp ngân hàng, công nghệ, chính phủ |
| APWG | https://apwg.org/ | Tổ chức chống phishing/cybercrime, có research program và eCrime exchange |
| APWG eCrime 2026 | https://apwg.org/events/ecrime2026 | Hội nghị nghiên cứu electronic crime/phishing/cybercrime |
| OWASP Mobile Application Security | https://mas.owasp.org/ | Chuẩn và tài liệu kiểm thử bảo mật mobile |
| OWASP ASVS | https://owasp.org/www-project-application-security-verification-standard/ | Checklist kiểm thử bảo mật backend/web API |

### 7.2. Việt Nam và khu vực

| Tên | Link | Vì sao liên quan |
|---|---|---|
| Vietnam Security Summit 2026 | https://securitysummit.vn/2026/ | Hội nghị an toàn thông tin lớn tại Việt Nam, có cơ quan/tổ chức an ninh mạng tham gia |
| ChongLuaDao | https://chongluadao.vn/ | Cộng đồng chống lừa đảo Việt Nam, dữ liệu báo cáo, extension cảnh báo |
| Tín Nhiệm Mạng | https://tinnhiemmang.vn/website-lua-dao | Danh sách website/mạng xã hội giả mạo, phân loại và trạng thái xử lý |
| nTrust | https://ntrust.vn/ | App phòng chống lừa đảo tại Việt Nam, có use case phone/link/account/QR |

## 8. Cách thực hiện khảo sát trong tuần

### Ngày 1: Thu thập nguồn

- Tạo file bảng khảo sát với các cột: tên sản phẩm, link, quốc gia, nền tảng, tính năng, quyền mobile, dữ liệu kiểm tra, mô hình tin cậy, bài học.
- Ưu tiên nguồn chính thức: website, Google Play, App Store, tài liệu API, blog kỹ thuật.
- Chụp lại hoặc ghi chú các màn hình quan trọng nếu cần đưa vào báo cáo.

### Ngày 2: Phân nhóm sản phẩm

- Nhóm Việt Nam: nTrust, ChongLuaDao, Tín Nhiệm Mạng.
- Nhóm quốc tế: Google Safe Browsing, PhishTank, VirusTotal, ScamAdviser, CheckPhish.
- Nhóm mobile security: Bitdefender, Avast, NordVPN hoặc Norton/McAfee nếu cần mở rộng.
- Rút ra mỗi nhóm 3 bài học áp dụng.

### Ngày 3: Viết user scenario

- Viết 3-5 tình huống đời sống: SMS link lạ, chuyển khoản đặt cọc, QR khuyến mãi, cuộc gọi giả danh, bài đăng tuyển dụng.
- Mỗi tình huống phải có: bối cảnh, dữ liệu đầu vào, cảnh báo, lý do, hành động khuyến nghị.
- Viết bằng ngôn ngữ người dùng phổ thông, tránh thuật ngữ kỹ thuật quá nặng.

### Ngày 4: Thiết kế niềm tin

- Soạn Privacy Policy bản nháp.
- Soạn Data Safety bản nháp: dữ liệu thu thập, mục đích, lưu bao lâu, có chia sẻ không.
- Lập checklist OWASP MASVS/ASVS rút gọn cho demo.
- Viết quy trình xử lý báo cáo sai/khiếu nại.

### Ngày 5: Chốt phạm vi MVP

- Chọn tính năng bắt buộc: URL, QR, phone/account, report, admin review, rule engine.
- Chọn tính năng mở rộng: chạy nền notification/SMS/call.
- Vẽ sơ đồ kiến trúc mobile -> backend -> rule engine -> database/cache.
- Viết danh sách API và dữ liệu mẫu cần tạo.

### Ngày 6-7: Tổng hợp và chuẩn bị báo cáo

- Hoàn thiện bảng khảo sát.
- Viết phần "vì sao đề tài không trùng sản phẩm đã có".
- Viết phần "điểm khác biệt của đề tài": Java Backend, rule engine, dashboard, báo cáo, dữ liệu Việt Nam giả lập/mẫu.
- Chuẩn bị slide ngắn hoặc section báo cáo để trình giảng viên.

## 9. Checklist câu hỏi cần trả lời trong báo cáo

- Người dùng có hiểu app đang bảo vệ họ khỏi rủi ro gì không?
- App có xin quá nhiều quyền so với MVP không?
- Nếu app cảnh báo sai, người dùng/admin xử lý thế nào?
- Dữ liệu nhạy cảm có được giảm thiểu, mã hóa, hoặc không lưu không?
- Kết quả risk score có giải thích được không?
- Có nguồn tham khảo thị trường và hội nghị/cộng đồng đủ uy tín không?
- Demo có vừa sức khóa luận Java Backend không?

## 10. Đề xuất phạm vi chốt cho khóa luận

Nên trình bày app mobile chạy ngầm như **hướng trải nghiệm mục tiêu**, nhưng MVP nên triển khai theo thứ tự an toàn:

1. Web backend + admin dashboard.
2. Mobile app nhập/share link, quét QR, nhập phone/account.
3. Notification kết quả kiểm tra.
4. Báo cáo cộng đồng và admin kiểm duyệt.
5. Chạy nền kiểm tra SMS/notification/call là phần mở rộng, có phân tích quyền riêng tư và chính sách nền tảng.

Cách này giúp đề tài vẫn có góc nhìn mobile thực tế, nhưng không bị kẹt ở quyền nhạy cảm hoặc chính sách Google Play trong giai đoạn demo.

## 11. Tài liệu tham khảo

| Mã | Nguồn | Link |
|---|---|---|
| [1] | nTrust Google Play | https://play.google.com/store/apps/details?id=com.nca.vn.ntrust |
| [2] | nTrust App Store | https://apps.apple.com/vn/app/ntrust-ph%C3%B2ng-ch%E1%BB%91ng-l%E1%BB%ABa-%C4%91%E1%BA%A3o/id6504554337 |
| [3] | ChongLuaDao | https://chongluadao.vn/ |
| [4] | ChongLuaDao report | https://chongluadao.vn/en/report/reportphishing |
| [5] | Tín Nhiệm Mạng | https://tinnhiemmang.vn/website-lua-dao |
| [6] | Google Safe Browsing | https://developers.google.com/safe-browsing/reference |
| [7] | PhishTank | https://www.phishtank.net/faq.php |
| [8] | VirusTotal API | https://docs.virustotal.com/reference/overview |
| [9] | ScamAdviser | https://www.scamadviser.com/home |
| [10] | CheckPhish | https://checkphish.bolster.ai/ |
| [11] | Bitdefender Mobile Security | https://play.google.com/store/apps/details?id=com.bitdefender.security |
| [12] | Avast Mobile Security | https://www.avast.com/free-mobile-security |
| [13] | Android Background Execution Limits | https://developer.android.com/about/versions/oreo/background |
| [14] | Android Foreground Services | https://developer.android.com/develop/background-work/services/fgs |
| [15] | Android Accessibility Service | https://developer.android.com/guide/topics/ui/accessibility/service |
| [16] | Google Play AccessibilityService policy | https://support.google.com/googleplay/android-developer/answer/10964491 |
| [17] | Google Play Data Safety | https://support.google.com/googleplay/android-developer/answer/10787469 |
| [18] | Android Package Visibility | https://developer.android.com/training/package-visibility |
| [19] | Play Integrity API | https://developer.android.com/google/play/integrity |
| [20] | OWASP MASVS | https://mas.owasp.org/MASVS/ |
| [21] | OWASP ASVS | https://owasp.org/www-project-application-security-verification-standard/ |
| [22] | APWG | https://apwg.org/ |
| [23] | APWG eCrime 2026 | https://apwg.org/events/ecrime2026 |
| [24] | GASA | https://gasa.org/ |
| [25] | Global Anti-Scam Summit America 2026 | https://gasa.org/global-anti-scam-summit-america-2026 |
| [26] | Vietnam Security Summit 2026 | https://securitysummit.vn/2026/ |
| [27] | F-Secure Text Message Checker | https://www.f-secure.com/us-en/text-message-checker |
| [28] | Bitdefender Scamio | https://www.bitdefender.com/en-us/consumer/scamio |
| [29] | Ask Silver | https://www.asksilver.com/ |
| [30] | AI Scam Checker | https://apps.apple.com/us/app/ai-scam-checker/id6670762828 |
| [31] | IsThisSpam | https://isthisspam.org/ |
