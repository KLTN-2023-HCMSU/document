# Chứng chỉ và chuẩn giúp tăng độ tin cậy cho Mobile Anti-Scam

## 1. Mục đích

Mobile Anti-Scam là ứng dụng có thể chạy nền, phân tích URL, SMS, cuộc gọi hoặc nội dung người dùng báo cáo. Vì vậy, người dùng cần biết ứng dụng không chỉ phát hiện lừa đảo tốt, mà còn xử lý dữ liệu cá nhân an toàn. Các chứng chỉ, chuẩn kiểm thử và bằng chứng tuân thủ dưới đây giúp nhóm trình bày rõ: app được thiết kế, kiểm thử và vận hành theo chuẩn bảo mật mobile có uy tín.

Lưu ý: OWASP MASVS là chuẩn tham chiếu quan trọng, nhưng OWASP tuyên bố họ không trực tiếp chứng nhận vendor, verifier hoặc phần mềm. Vì vậy, trong đồ án nên dùng cách diễn đạt như "áp dụng theo OWASP MASVS/MASTG" hoặc "được kiểm thử dựa trên OWASP MAS Checklist", không nên ghi "được OWASP chứng nhận" nếu chưa có chứng nhận độc lập hợp lệ.

## 2. Bảng chứng chỉ, chuẩn và bằng chứng uy tín

| Chứng chỉ / chuẩn / tài liệu | Đó là gì? | Target | Vai trò trong project | Vì sao tăng độ tin cậy với user? |
|---|---|---|---|---|
| [OWASP MASVS](https://mas.owasp.org/MASVS/) | Mobile Application Security Verification Standard, chuẩn kiểm chứng bảo mật cho ứng dụng mobile. MASVS bao phủ các nhóm như lưu trữ dữ liệu, mã hóa, xác thực, giao tiếp mạng, tương tác nền tảng, chất lượng mã, chống reverse engineering và quyền riêng tư. | App và team dev | Dùng làm bộ yêu cầu bảo mật cốt lõi khi thiết kế app: không lưu token thô, mã hóa dữ liệu nhạy cảm, dùng HTTPS/TLS, hạn chế quyền truy cập, bảo vệ dữ liệu khi app chạy nền. | User thấy app được xây theo một chuẩn bảo mật mobile phổ biến, không chỉ dựa vào cam kết chủ quan của nhóm phát triển. |
| [OWASP MASVS Assessment and Certification Guidance](https://mas.owasp.org/MASVS/04-Assessment_and_Certification/) | Hướng dẫn cách đánh giá app theo MASVS. OWASP khuyến nghị kiểm thử kiểu "open book": tester có tài liệu, source code, tài khoản kiểm thử và quyền truy cập endpoint cần thiết. | App, team dev, tester | Định nghĩa phạm vi đánh giá, bằng chứng kiểm thử, kết quả pass/fail và cách xử lý lỗi. Dùng để viết mục "Đánh giá bảo mật" trong báo cáo đồ án. | User có thêm niềm tin vì app có quy trình kiểm thử rõ ràng, có bằng chứng, không chỉ chạy tool tự động rồi tuyên bố an toàn. |
| [OWASP MASTG](https://mas.owasp.org/MASTG/) | Mobile Application Security Testing Guide, tài liệu hướng dẫn kiểm thử bảo mật mobile và reverse engineering. | Team dev, tester | Dùng để chuyển các yêu cầu MASVS thành test case thực tế cho Android/iOS, ví dụ kiểm tra lưu trữ dữ liệu, TLS, WebView, logging, backup, permission. | User được bảo vệ tốt hơn vì các rủi ro mobile thực tế đã được kiểm tra bằng quy trình cụ thể, không chỉ kiểm tra chức năng bề mặt. |
| [OWASP MASWE](https://mas.owasp.org/MASWE/) | Mobile Application Security Weakness Enumeration, danh sách điểm yếu bảo mật và quyền riêng tư thường gặp trong mobile app. | Team dev, security reviewer | Dùng làm danh mục lỗi cần phòng tránh: API key hardcoded, cleartext traffic, insecure certificate validation, dữ liệu nhạy cảm trong log/thông báo, permission không phù hợp. | User tin tưởng hơn vì nhóm có danh sách rủi ro cụ thể để tránh làm app chống scam trở thành nguồn rò rỉ dữ liệu mới. |
| [OWASP MAS Checklist](https://mas.owasp.org/checklists/) | Checklist liên kết các MASVS control với test case trong MASTG, hỗ trợ pentest, standard compliance, học tập và bug bounty. | App, tester, báo cáo kiểm thử | Dùng làm bảng tự đánh giá trong đồ án: control nào đã áp dụng, test nào đã chạy, kết quả pass/fail, bằng chứng ảnh chụp/log. | User và giảng viên có thể thấy trạng thái bảo mật minh bạch theo từng mục, thay vì một câu chung chung "app an toàn". |
| [OWASP MAS Crackmes](https://mas.owasp.org/crackmes/) | Bộ bài thực hành reverse engineering mobile, còn gọi là UnCrackable Apps, được dùng làm ví dụ trong MASTG. | Team dev, security learner | Dùng để rèn kỹ năng phân tích app bị chỉnh sửa, debug, hook, reverse engineering; từ đó cải thiện chống giả mạo app, chống sửa rule phát hiện scam, chống lộ khóa. | User tin tưởng hơn nếu team chứng minh có năng lực hiểu rủi ro reverse engineering, đặc biệt với app bảo mật dễ bị kẻ xấu phân tích để né phát hiện. |
| [CREST OVS Mobile](https://www.crest-approved.org/membership/crest-ovs-programme/) | Chương trình accreditation của CREST cho công ty cung cấp dịch vụ kiểm thử bảo mật app, dựa trên OWASP ASVS và MASVS. Có nhánh CREST OVS App và CREST OVS Mobile. | Đơn vị kiểm thử / công ty pentest, không phải trực tiếp app sinh viên | Nếu đồ án phát triển thành sản phẩm thật, có thể thuê hoặc tham khảo đơn vị CREST OVS Accredited Provider để kiểm thử độc lập theo MASVS. | Đây là bằng chứng mạnh hơn tự đánh giá nội bộ vì có bên thứ ba có năng lực kiểm thử. User dễ tin hơn khi app được đánh giá bởi đơn vị độc lập có accreditation. |
| [OWASP Agentic Skills Top 10 Training & Certification](https://owasp.org/www-project-agentic-skills-top-10/training-certification.html) | Chương trình training/certification cho bảo mật AI agent skills, gồm các nội dung như threat landscape, secure skill development, risk assessment, scanning, CI/CD và monitoring. | Team dev, AI/security engineer | Chỉ nên đưa vào nếu Mobile Anti-Scam có module AI agent hoặc skill tự động, ví dụ agent tự phân tích URL, truy cập dữ liệu, sinh khuyến nghị hoặc gọi công cụ ngoài. | User tin tưởng hơn khi tính năng AI không hoạt động như "hộp đen" quá quyền, mà được thiết kế có kiểm soát quyền, kiểm thử input, logging và giám sát rủi ro. |

## 3. Cách ứng dụng vào đồ án

### 3.1. Với phạm vi đồ án tốt nghiệp

Nhóm nên ưu tiên dùng MASVS, MASTG, MASWE và MAS Checklist làm bộ khung chính. Đây là các tài liệu sát nhất với app mobile và có thể biến thành tiêu chí thiết kế, checklist kiểm thử, bảng đánh giá trong báo cáo.

CREST OVS Mobile nên được trình bày như hướng phát triển khi sản phẩm đi vào thực tế hoặc cần kiểm thử độc lập. Không nên ghi rằng project đã đạt CREST OVS nếu nhóm chưa được kiểm thử bởi tổ chức đủ điều kiện.

OWASP Agentic Skills Top 10 Training & Certification chỉ nên đưa vào nếu app có thành phần AI agent. Nếu app chỉ dùng mô hình phân loại scam thông thường, phần này có thể để ở mục "mở rộng trong tương lai".

### 3.2. Cách trình bày với người dùng

- Công khai chính sách dữ liệu: app thu thập gì, không thu thập gì, dữ liệu nào được gửi lên server, dữ liệu nào xử lý local.
- Công khai phạm vi quyền: vì sao cần quyền SMS, notification, call log hoặc accessibility nếu có; cho phép tắt quyền không cần thiết.
- Công khai tiêu chí bảo mật: "Ứng dụng được thiết kế tham chiếu OWASP MASVS và kiểm thử theo OWASP MAS Checklist".
- Công khai kết quả kiểm thử: số control đã kiểm tra, lỗi đã sửa, lỗi còn giới hạn trong phạm vi đồ án.
- Không dùng ngôn ngữ gây hiểu nhầm: tránh "OWASP certified" nếu chưa có chứng nhận chính thức từ bên thứ ba hợp lệ.

## 4. Khuyến nghị ưu tiên

| Mức ưu tiên | Nên làm | Lý do |
|---|---|---|
| Cao | Áp dụng OWASP MASVS vào yêu cầu bảo mật của app | Tác động trực tiếp đến dữ liệu người dùng và kiến trúc app mobile. |
| Cao | Tạo bảng kiểm thử theo OWASP MAS Checklist | Có bằng chứng cụ thể để trình bày với giảng viên và user. |
| Cao | Dùng MASWE để liệt kê rủi ro cần tránh | Giúp giải thích rõ các lỗi bảo mật mobile mà app đã phòng ngừa. |
| Trung bình | Dùng MASTG cho test case kỹ thuật | Phù hợp nếu nhóm có thời gian kiểm thử sâu hơn. |
| Trung bình | Luyện MAS Crackmes | Tăng năng lực team, đặc biệt nếu app cần chống reverse engineering/tampering. |
| Thấp trong phạm vi đồ án | CREST OVS Mobile | Có giá trị cao cho sản phẩm thật, nhưng thường vượt phạm vi đồ án sinh viên. |
| Tùy kiến trúc | OWASP Agentic Skills Top 10 Training & Certification | Chỉ liên quan mạnh nếu app có AI agent/skill tự động. |

## 5. Reference

- OWASP Mobile Application Security: https://mas.owasp.org/
- OWASP MASVS: https://mas.owasp.org/MASVS/
- OWASP MASVS Assessment and Certification: https://mas.owasp.org/MASVS/04-Assessment_and_Certification/
- OWASP MASWE: https://mas.owasp.org/MASWE/
- OWASP MASTG: https://mas.owasp.org/MASTG/
- OWASP MAS Checklist: https://mas.owasp.org/checklists/
- OWASP MAS Crackmes: https://mas.owasp.org/crackmes/
- CREST OVS Programme: https://www.crest-approved.org/membership/crest-ovs-programme/
- OWASP Agentic Skills Top 10 Training & Certification: https://owasp.org/www-project-agentic-skills-top-10/training-certification.html
