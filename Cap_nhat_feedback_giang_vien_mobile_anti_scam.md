# Cập nhật feedback giảng viên cho đề tài Mobile Anti-Scam

## 1. Tóm tắt góp ý từ giảng viên

Sau buổi góp ý, đề tài cần được điều chỉnh theo hướng gần với nhu cầu thực tế của người dùng hơn. Các điểm cần bổ sung gồm:

- Bổ sung link hội nghị, tổ chức, nền tảng cộng đồng và nguồn dữ liệu uy tín liên quan đến an toàn thông tin, chống phishing và chống lừa đảo trực tuyến.
- Viết lại một phần đề tài dưới góc nhìn người dùng, đặc biệt là trải nghiệm app mobile chạy ngầm hoặc hỗ trợ cảnh báo khi người dùng thao tác trên điện thoại.
- Làm rõ vì sao người dùng có thể tin tưởng hệ thống, nhất là khi app xử lý dữ liệu nhạy cảm như link, tin nhắn, số điện thoại, số tài khoản hoặc mã QR.
- Khảo sát các web/app đang có trên thị trường để chứng minh nhu cầu, học mô hình triển khai và xác định khoảng trống phù hợp cho khóa luận.

## 2. Điều chỉnh định hướng đề tài

Đề tài không nên chỉ được mô tả như một hệ thống web kiểm tra URL hoặc biểu mẫu giả mạo. Hướng trình bày phù hợp hơn là:

> Xây dựng nền tảng web/mobile hỗ trợ cảnh báo rủi ro lừa đảo trực tuyến trong các tình huống giao dịch đời sống, kết hợp kiểm tra URL, biểu mẫu, tin nhắn, số điện thoại, số tài khoản, mã QR và báo cáo cộng đồng.

Trong đó, mobile app là góc nhìn trải nghiệm mục tiêu vì người dùng thường gặp scam qua điện thoại: SMS, ứng dụng chat, mạng xã hội, cuộc gọi, QR chuyển khoản và link trong trình duyệt. Backend Java Spring Boot vẫn là phần lõi kỹ thuật để xử lý API, rule engine, dữ liệu cảnh báo, lịch sử kiểm tra và dashboard quản trị.

## 3. Viết dưới góc nhìn người dùng mobile

Thay vì mô tả app như một công cụ kỹ thuật, nên mô tả app như một trợ lý an toàn số:

> Khi người dùng chuẩn bị bấm vào link, quét QR, chuyển khoản hoặc nhận cuộc gọi/tin nhắn đáng nghi, ứng dụng hỗ trợ kiểm tra nhanh và đưa ra cảnh báo dễ hiểu trước khi người dùng thực hiện hành động rủi ro.

Một số tình huống người dùng:

- Người dùng nhận SMS có link yêu cầu cập nhật tài khoản ngân hàng. App cho phép chia sẻ link vào hệ thống để kiểm tra domain, HTTPS, redirect, blacklist và dấu hiệu phishing.
- Người dùng chuẩn bị chuyển tiền cho một số tài khoản lạ. App kiểm tra số tài khoản trong dữ liệu cảnh báo và báo cáo cộng đồng.
- Người dùng quét mã QR thanh toán. App tách thông tin URL hoặc nội dung chuyển khoản rồi chấm điểm rủi ro.
- Người dùng nhận cuộc gọi từ số lạ. App cho phép kiểm tra thủ công hoặc cảnh báo nếu số đã có nhiều báo cáo.

Với demo khóa luận, không nên phụ thuộc hoàn toàn vào cơ chế chạy ngầm vì Android/iOS có giới hạn quyền riêng tư và pin. MVP nên ưu tiên các thao tác chủ động: dán link, share link vào app, quét QR, nhập số điện thoại/số tài khoản và gửi báo cáo.

## 4. Làm sao để người dùng tin tưởng hệ thống?

Vì app chống lừa đảo có thể cần quyền nhạy cảm, niềm tin phải được thiết kế ngay từ đầu.

| Nhóm yếu tố | Cách áp dụng vào đề tài |
| --- | --- |
| Minh bạch dữ liệu | Nêu rõ app thu thập gì, không thu thập gì, dữ liệu nào được gửi lên backend. Không yêu cầu mật khẩu, OTP, số thẻ hoặc thông tin đăng nhập. |
| Giải thích cảnh báo | Mỗi kết quả cần có điểm rủi ro, mức cảnh báo, lý do và khuyến nghị xử lý. Không chỉ hiển thị "nguy hiểm" một cách mơ hồ. |
| Nguồn dữ liệu | Công bố nguồn tham khảo như blacklist, whitelist, báo cáo cộng đồng, dữ liệu mẫu, rule engine và trạng thái xác minh. |
| Quy trình cộng đồng | Báo cáo của người dùng phải có trạng thái: mới gửi, đang xác minh, đã xác minh, bị từ chối hoặc cần bổ sung bằng chứng. |
| Bảo mật kỹ thuật | API dùng HTTPS/TLS, xác thực người dùng, phân quyền admin, log thao tác quan trọng và kiểm thử theo OWASP ASVS/MASVS. |
| Chứng nhận/chuẩn tham khảo | Trình bày việc tham chiếu Google Play Data Safety, OWASP MASVS, OWASP ASVS, Play App Signing và Play Integrity API cho hướng triển khai thật. |
| Xử lý cảnh báo sai | Có cơ chế phản hồi, khiếu nại hoặc yêu cầu kiểm duyệt lại để tránh tố cáo sai số điện thoại, tài khoản hoặc website. |

Thông điệp nên nhấn mạnh: hệ thống hỗ trợ ra quyết định, không thay thế hoàn toàn phán đoán của người dùng và không kết luận tuyệt đối khi dữ liệu chưa đủ mạnh.

## 5. Khảo sát web/app trên thị trường

| Sản phẩm/nền tảng | Link | Điểm cần học |
| --- | --- | --- |
| nTrust | https://ntrust.vn/ | App chống lừa đảo tại Việt Nam, có kiểm tra số điện thoại, link, số tài khoản, mã QR và ứng dụng đáng ngờ. |
| ChongLuaDao.vn | https://chongluadao.vn/ | Mô hình cộng đồng, extension cảnh báo website nguy hiểm, danh sách chặn và báo cáo URL. |
| Tín Nhiệm Mạng | https://tinnhiemmang.vn/website-lua-dao | Danh sách website/mạng xã hội giả mạo, phân loại lĩnh vực và trạng thái xử lý. |
| Google Safe Browsing | https://safebrowsing.google.com/ | Dữ liệu cảnh báo URL độc hại/phishing ở quy mô lớn, phù hợp để tham khảo mô hình threat intelligence. |
| PhishTank | https://www.phishtank.net/faq.php | Cộng đồng submit, verify và chia sẻ dữ liệu phishing; có thể học luồng báo cáo cộng đồng. |
| VirusTotal | https://docs.virustotal.com/reference/overview | Tổng hợp kết quả từ nhiều engine, có community reputation; học cách gom nhiều tín hiệu và giải thích kết quả. |
| ScamAdviser | https://www.scamadviser.com/ | Trust score cho website, hướng đến người dùng phổ thông và thương mại điện tử. |
| Bitdefender Mobile Security | https://play.google.com/store/apps/details?id=com.bitdefender.security | Gợi ý trải nghiệm mobile security, cảnh báo link/tin nhắn và bảo vệ theo ngữ cảnh điện thoại. |
| Avast Mobile Security | https://www.avast.com/free-mobile-security | Gợi ý cách định vị sản phẩm như bảo vệ đời sống số, không chỉ là trình quét kỹ thuật. |

Khoảng trống phù hợp cho khóa luận là xây dựng bản demo học thuật kết hợp nhiều loại dữ liệu trong bối cảnh Việt Nam: URL, form, SMS/nội dung giao dịch, số điện thoại, số tài khoản, QR và báo cáo cộng đồng.

## 6. Link hội nghị, tổ chức và nền tảng cộng đồng

| Nhóm | Link | Vai trò trong đề tài |
| --- | --- | --- |
| OWASP | https://owasp.org/ | Nguồn tham khảo chuẩn về bảo mật ứng dụng web/API. |
| OWASP MASVS | https://mas.owasp.org/MASVS/ | Checklist bảo mật mobile app. |
| OWASP ASVS | https://owasp.org/www-project-application-security-verification-standard/ | Checklist bảo mật backend, API, xác thực và phân quyền. |
| Vietnam Security Summit | https://securitysummit.vn/ | Hội nghị an toàn thông tin tại Việt Nam, dùng để chứng minh bối cảnh và xu hướng thực tế. |
| Black Hat | https://www.blackhat.com/ | Hội nghị bảo mật quốc tế, tham khảo xu hướng tấn công/phòng thủ. |
| DEF CON | https://defcon.org/ | Cộng đồng và hội nghị hacker/security quốc tế. |
| FIRST | https://www.first.org/ | Diễn đàn các đội ứng cứu sự cố, tham khảo quy trình xử lý sự cố. |
| APWG | https://apwg.org/ | Tổ chức chuyên về chống phishing và cybercrime. |
| PhishTank | https://www.phishtank.net/ | Nền tảng cộng đồng về dữ liệu phishing. |
| VirusTotal Community | https://www.virustotal.com/gui/community | Cộng đồng đánh giá và chia sẻ thông tin threat intelligence. |
| Tín Nhiệm Mạng | https://tinnhiemmang.vn/ | Nguồn tham khảo Việt Nam về website giả mạo và tra cứu rủi ro. |
| ChongLuaDao.vn | https://chongluadao.vn/ | Cộng đồng Việt Nam về cảnh báo lừa đảo trực tuyến. |

## 7. Việc cần cập nhật vào đồ án

- Cập nhật phần mở đầu để nhấn mạnh bối cảnh người dùng mobile gặp lừa đảo trong đời sống hằng ngày.
- Bổ sung mục khảo sát thị trường với bảng so sánh web/app/nền tảng đã có.
- Bổ sung mục "Niềm tin người dùng" gồm minh bạch dữ liệu, quyền riêng tư, nguồn dữ liệu, chuẩn bảo mật và cơ chế xử lý cảnh báo sai.
- Bổ sung user scenario cho app mobile: kiểm tra link, QR, số tài khoản, số điện thoại và tin nhắn giao dịch.
- Điều chỉnh phạm vi MVP: backend Java Spring Boot + web admin + mobile app thao tác chủ động; chạy ngầm là hướng trải nghiệm mục tiêu hoặc hướng phát triển.
- Bổ sung tài liệu tham khảo từ hội nghị, tổ chức bảo mật, cộng đồng chống phishing và các sản phẩm thực tế trên thị trường.

## 8. Kết luận cập nhật

Feedback của giảng viên giúp đề tài chuyển từ góc nhìn kỹ thuật thuần túy sang góc nhìn sản phẩm thực tế. Đề tài nên được trình bày như một hệ thống hỗ trợ người dùng phổ thông phòng tránh lừa đảo trực tuyến trên mobile, có backend xử lý rủi ro, dữ liệu cộng đồng, dashboard quản trị và cơ chế xây dựng niềm tin rõ ràng.
