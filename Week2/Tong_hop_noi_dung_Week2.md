# Tổng hợp nội dung Week2 - Anti-Scam Platform

Ngày tổng hợp: 2026-07-22

Tài liệu này gom nội dung từ `Week2/01_Chung_Chi` và `Week2/02_Phan_Tich_Nghiep_Vu`. Mỗi file nguồn được tóm tắt thành một section riêng để dùng cho báo cáo tuần và chuẩn bị slide trình bày.

## 1. Chung_chi_gia_re_tang_uy_tin_mobile_app_chay_ngam.md

### Mục tiêu

Tài liệu xác định các lựa chọn chứng chỉ, tài khoản phân phối và bằng chứng pháp lý có chi phí thấp để tăng độ tin cậy cho mobile app chống scam/phishing, đặc biệt khi app có yếu tố chạy nền hoặc xử lý dữ liệu nhạy cảm.

### Bảng chứng nhận/thủ tục áp dụng cho app

Lưu ý: các chứng chỉ hoặc thủ tục dưới đây không thay thế chính sách Google Play/App Store. App vẫn phải có privacy policy, consent rõ ràng, Data safety và khai báo quyền đúng phạm vi.

| Tên chứng chỉ | Giải thích ngắn là gì | Target + Giá (target là dev hay chính cái app) | Ứng dụng vào project | Vì sao tăng độ uy tín |
| --- | --- | --- | --- | --- |
| Google Play Developer + Play App Signing | Tài khoản phân phối Android, xác minh developer, store listing, Play App Signing và Data safety. Đây không phải chứng chỉ bảo mật, nhưng là bằng chứng phân phối chính thống. | Target: dev account và chính app Android. Giá: 25 USD một lần. | Nên làm đầu tiên nếu triển khai Android MVP. Dùng để publish/test app qua Google Play, khai báo quyền chạy nền, Data safety và privacy policy. | User thấy app có developer identity, được ký và phân phối qua kênh chính thống thay vì file APK lạ. App signing và Data safety giúp giảm nghi ngại với app xử lý dữ liệu nhạy cảm. |
| Apple Developer Program | Chương trình developer iOS cho phép ký app, TestFlight, App Store distribution, provisioning profile và hiển thị seller name. | Target: dev account và chính app iOS. Giá: 99 USD/năm. | Chỉ ưu tiên nếu project có iOS hoặc cần demo TestFlight/App Store. Với Android-only, chưa cần đưa vào giai đoạn đầu. | Phân phối qua TestFlight/App Store tạo cảm giác chính thống hơn cài file lạ. Seller identity và signing certificate giúp chứng minh nguồn phát hành rõ ràng. |
| Tín nhiệm mạng - Ứng dụng tín nhiệm | Nhãn/chứng nhận ứng dụng Việt Nam trong hệ sinh thái Tín nhiệm mạng/NCA, có danh bạ ứng dụng đã kiểm duyệt và đạt chứng nhận. | Target: chính app public tại Việt Nam. Giá: chưa công bố công khai, cần liên hệ NCA/Tín nhiệm mạng. | Nên nghiên cứu sau Google Play Developer Account và privacy policy nếu app phát hành cho người dùng Việt Nam. Có thể đưa vào lộ trình sau MVP. | Có bằng chứng bên thứ ba tại Việt Nam, phù hợp trực tiếp với app chống scam/phishing và giúp user nhận diện app đáng tin cậy trong bối cảnh địa phương. |
| Đăng ký quyền tác giả chương trình máy tính | Giấy chứng nhận pháp lý tại Việt Nam cho chương trình máy tính/source code. Không phải chứng chỉ bảo mật. | Target: chủ sở hữu/tác giả và sản phẩm phần mềm. Giá: 600.000 VND lệ phí nhà nước. | Phù hợp nếu nhóm muốn chứng minh quyền sở hữu mã nguồn/app trong phụ lục pháp lý của đồ án. | Tăng uy tín pháp lý vì có giấy chứng nhận của cơ quan nhà nước, giúp chứng minh app là sản phẩm thật của nhóm/tổ chức. |
| Thông báo/đăng ký ứng dụng thương mại điện tử với Bộ Công Thương | Thủ tục quản lý ứng dụng TMĐT trên `online.gov.vn`, gồm thông báo ứng dụng bán hàng hoặc đăng ký ứng dụng cung cấp dịch vụ TMĐT. Không phải chứng chỉ bảo mật. | Target: chính app nếu có hoạt động TMĐT. Giá: không thấy phí/lệ phí công khai trong nguồn đã kiểm tra. | Không áp dụng cho MVP chỉ cảnh báo phishing/scam. Chỉ xét nếu app sau này có marketplace, giao dịch trả phí hoặc chức năng trung gian thương mại. | Nếu đúng phạm vi TMĐT, thông tin xác nhận/công bố từ Bộ Công Thương giúp tăng độ tin cậy pháp lý và minh bạch vận hành. |

### Bảng chứng chỉ cá nhân/team

| Tên chứng chỉ | Giải thích ngắn là gì | Target + Giá (target là dev hay chính cái app) | Ứng dụng vào project | Vì sao tăng độ uy tín |
| --- | --- | --- | --- | --- |
| OpenSSF Developing Secure Software LFD121 | Khóa học miễn phí của OpenSSF/Linux Foundation về phát triển phần mềm an toàn, có certificate/badge sau khi hoàn thành. | Target: dev/team. Giá: miễn phí. | Nên cho ít nhất một thành viên phụ trách security hoàn thành, rồi đưa certificate URL/badge vào phụ lục năng lực nhóm. | Chứng minh nhóm có nền tảng secure development, threat modeling và kiểm thử bảo mật ở mức cơ bản. Phù hợp khi chưa có ngân sách pentest. |
| Google Cybersecurity Professional Certificate | Professional Certificate online của Google trên Coursera về nền tảng cybersecurity entry-level, gồm risk, threat, SIEM, Linux, SQL, Python và incident response. | Target: dev/team. Giá: 49 USD/tháng ở Mỹ và Canada, thường dưới 300 USD nếu học nhanh; giá có thể khác theo quốc gia. | Phù hợp cho thành viên viết threat model, logging/monitoring, incident response và vận hành backend chống scam. | Credential dễ chia sẻ, giúp tăng độ tin cậy năng lực cá nhân của người thiết kế/quản trị phần bảo mật, nhưng không chứng nhận app đã an toàn. |
| ISC2 Certified in Cybersecurity (CC) | Chứng chỉ nền tảng cybersecurity của ISC2 cho cá nhân, uy tín hơn certificate online thông thường. | Target: dev/team. Giá: 199 USD exam ở Asia Pacific + 50 USD/năm AMF. | Cân nhắc nếu nhóm cần credential cá nhân mạnh hơn cho thành viên phụ trách security. | ISC2 là tổ chức uy tín ngành bảo mật; chứng chỉ giúp chứng minh nền tảng security principles, access control, incident response, network security và security operations. |

### Ghi chú kỹ thuật backend/API

| Tên chứng chỉ | Giải thích ngắn là gì | Target + Giá (target là dev hay chính cái app) | Ứng dụng vào project | Vì sao tăng độ uy tín |
| --- | --- | --- | --- | --- |
| Let's Encrypt TLS Certificate cho backend/API | Chứng chỉ TLS miễn phí cho domain backend/API, dùng để bật HTTPS hợp lệ. | Target: chính backend/API của app. Giá: miễn phí. | Nên dùng nếu app gửi URL/SMS metadata hoặc dữ liệu quét lên server. Nếu app xử lý hoàn toàn local thì ghi "không áp dụng". | Chứng minh kênh truyền dữ liệu giữa app và backend được mã hóa bằng HTTPS hợp lệ, trực tiếp liên quan privacy và bảo vệ dữ liệu nhạy cảm. |

### Khuyến nghị ưu tiên cho project

1. Android MVP: Google Play Developer + Play App Signing, privacy policy và Data safety.
2. Nếu app public cho user Việt Nam: nghiên cứu Tín nhiệm mạng - Ứng dụng tín nhiệm và đăng ký quyền tác giả chương trình máy tính.
3. Nếu có iOS: thêm Apple Developer Program.
4. Nếu có backend/API: dùng Let's Encrypt cho HTTPS.
5. Tách riêng OpenSSF LFD121, Google Cybersecurity Professional Certificate và ISC2 CC như chứng chỉ cá nhân/team.
6. Chỉ xét TMĐT hoặc Appdome khi app thật sự thuộc phạm vi áp dụng hoặc chuyển sang giai đoạn thương mại hóa/enterprise.

### Link tham khảo

- Google Play Console fee và developer identity: https://support.google.com/googleplay/android-developer/answer/6112435
- Play App Signing: https://support.google.com/googleplay/android-developer/answer/9842756
- Google Play Data safety: https://support.google.com/googleplay/android-developer/answer/10787469
- Apple Developer Program: https://developer.apple.com/programs/
- Apple Developer enrollment: https://developer.apple.com/programs/enroll/
- Tín nhiệm mạng: https://tinnhiemmang.vn/
- Danh bạ Ứng dụng tín nhiệm: https://tinnhiemmang.vn/ung-dung-tin-nhiem
- Dịch vụ công Bộ Văn hóa, Thể thao và Du lịch - đăng ký quyền tác giả chương trình máy tính: https://dichvucong.bvhttdl.gov.vn/congdan/Default.aspx?DKC=true&MucDo=4&ctl=ChiTiet&lv=OAB3AHoANQBMAGoAcAB3AGIAKwAxAHQASABTAE0ASQBIAEQAMgBYADIAZwA9AD0A0&mid=915&nid=1&tabid=249&ttid=MgBKADIAeQBaAFoALwBRAHMANABvADMAYgB4AHEAQQB4AGcAYQBCAGYAUQA9AD0A0
- Online.gov.vn: https://online.gov.vn/
- Dịch vụ công Bộ Công Thương - đăng ký ứng dụng cung cấp dịch vụ TMĐT: https://dichvucong.moit.gov.vn/VdxpTTHCOnlineDetail.aspx?DocId=443
- OpenSSF Developing Secure Software LFD121: https://openssf.org/training/courses/
- Google Cybersecurity Professional Certificate: https://www.coursera.org/professional-certificates/google-cybersecurity
- Google Career Certificates subscription: https://career.skills.google/subscriptions
- ISC2 exam pricing: https://www.isc2.org/register-for-exam/isc2-exam-pricing
- ISC2 AMF overview: https://www.isc2.org/policies-procedures/amfs-overview
- ISC2 CC badge: https://www.credly.com/org/isc2/badge/certified-in-cybersecurity-cc.1
- Let's Encrypt: https://letsencrypt.org/
- Certbot: https://certbot.eff.org/
- Appdome Certified Secure: https://www.appdome.com/certified-secure-mobile-devsecops-certification/
- Appdome Certified Secure Knowledge Base: https://www.appdome.com/how-to/devsecops-automation-mobile-cicd/certified-secure-devsecops-certification-android-ios/using-certified-secure-android-ios-apps-build-certification-in-devops-cicd/
- Appdome pricing: https://www.appdome.com/appdome-pricing/

## 2. Phan_tich_thuat_toan_va_ky_thuat.md

### Mục tiêu

Tài liệu phân tích các hướng phát hiện lừa đảo trực tuyến và đề xuất kiến trúc thuật toán phù hợp với phạm vi khóa luận.

### Nội dung chính

- Có ba hướng tiếp cận: rule-based, machine learning và hybrid.
- MVP nên bắt đầu bằng rule-based engine kết hợp heuristic analysis vì dễ giải thích, triển khai nhanh, không cần dataset lớn và dễ kiểm thử.
- Machine learning phù hợp cho hướng mở rộng khi đã có dataset đủ lớn và cần giảm false positive.
- Hybrid là mục tiêu dài hạn: rule engine xử lý nhanh các case rõ ràng, ML xử lý các case không chắc chắn.

### Các module phân tích

| Module | Cách xử lý MVP | Điểm mạnh |
| --- | --- | --- |
| URL/Link Scanner | Chuẩn hóa URL, trích feature, blacklist/whitelist, typosquatting | Phù hợp phishing link, giải thích được lý do cảnh báo |
| Form/HTML Analyzer | Parse form/input, tìm trường nhạy cảm, kiểm tra action domain/HTTPS | Phát hiện trang giả mạo thu thập OTP, PIN, thẻ, CCCD |
| Text Analyzer | Regex entity, keyword dictionary tiếng Việt, composite rules | Phù hợp SMS/Zalo/Messenger/email lừa đảo |
| Phone/Bank Checker | Normalize, hash, lookup blacklist, community score, time decay | Hỗ trợ cảnh báo số điện thoại và số tài khoản bị báo cáo |
| QR Scanner | Decode QR, phân loại URL/VietQR/text, gọi module tương ứng | Phù hợp bối cảnh thanh toán QR tại Việt Nam |

### Kiến trúc đề xuất

- Rule Repository lưu `RiskRule`, `RuleCondition`, weight, priority và trạng thái enabled.
- Rule Evaluator kiểm tra điều kiện, cộng điểm rủi ro và thu thập evidence.
- Rule Composer hỗ trợ AND/OR/NOT, threshold và mapping mức rủi ro.
- Score chuẩn hóa về 0-100 và phân loại `SAFE`, `CAUTION`, `DANGER`.
- Mỗi kết quả cần có evidence và recommendation để người dùng hiểu tại sao bị cảnh báo.

### Roadmap kỹ thuật

1. Rule Engine Core với 20-30 rule cơ bản.
2. URL Scanner có normalization, HTTPS/SSL checker, redirect analyzer và Levenshtein similarity.
3. Text Analyzer có keyword dictionary tiếng Việt, regex entity extraction và cross-reference URL/phone.
4. Phone/Account Checker dùng hashing, cache và time-decay.
5. QR Scanner dùng ZXing và parser VietQR.
6. ML prototype optional với Random Forest/XGBoost hoặc TF-IDF + Naive Bayes.

### Kết luận kỹ thuật

Trong phạm vi 6 tháng, hướng phù hợp nhất là rule-based + heuristic + blacklist/whitelist + community report. Cách này đủ học thuật, dễ demo, dễ kiểm thử và không cần hạ tầng ML lớn.

## 3. Mobile_Permissions_Analysis.md

### Mục tiêu

Tài liệu trả lời câu hỏi app mobile có thể vượt quyền không và xác định phạm vi quyền hợp pháp cho Anti-Scam Platform.

### Kết luận chính

App mobile tuyệt đối không được vượt quyền. Vượt quyền có rủi ro pháp lý, bị hệ điều hành chặn, bị Google Play/App Store từ chối và làm mất niềm tin người dùng. App chống scam mà xâm phạm quyền riêng tư sẽ đi ngược mục tiêu sản phẩm.

### Quyền đề xuất cho MVP

| Quyền | Trạng thái | Lý do |
| --- | --- | --- |
| `INTERNET` | Nên dùng | Gọi backend API |
| `VIBRATE` | Nên dùng | Rung cảnh báo |
| `RECEIVE_BOOT_COMPLETED` | Có thể dùng | Khởi động service bảo vệ nếu có |
| `CAMERA` | Nên dùng | Quét QR code nghi ngờ |
| `POST_NOTIFICATIONS` | Nên dùng trên Android 13+ | Hiển thị cảnh báo |
| `READ_PHONE_STATE` | Có điều kiện | Chỉ khi làm call screening |
| `READ_SMS` | Không nên dùng | Dễ bị Google Play từ chối |
| `READ_CONTACTS` | Không nên dùng | Không cần cho MVP |
| `READ_CALL_LOG` | Không nên dùng | Không cần lịch sử cuộc gọi |
| `BIND_ACCESSIBILITY_SERVICE` | Không dùng | Rủi ro cao, dễ bị xem là spyware |

### Luồng chạy ngầm hoặc thay thế an toàn

- Share Target: user share link từ Zalo/Messenger/browser sang app để quét.
- Manual Input: user nhập URL, số điện thoại hoặc số tài khoản.
- Clipboard foreground: chỉ đọc clipboard khi user đang mở app và bấm kiểm tra.
- QR Scanner: user chủ động mở camera để quét QR.
- Call Screening: có thể nghiên cứu sau, dùng API chính thức và chỉ lấy số gọi đến.

### Luồng không nên làm cho MVP

- Tự động đọc SMS.
- Tự động đọc notification.
- Tự động monitor clipboard.
- Accessibility Service để đọc màn hình app khác.
- Background location hoặc các quyền không phục vụ trực tiếp chống scam.

### Checklist tuân thủ

- Viết privacy policy rõ ràng bằng tiếng Việt và tiếng Anh.
- Giải thích từng permission trước khi xin.
- Xử lý trường hợp user từ chối hoặc thu hồi quyền.
- Không log dữ liệu nhạy cảm.
- Mã hóa dữ liệu gửi lên server.
- Khai báo đúng trong Google Play Console.
- Ghi rõ trong báo cáo rằng thiết kế không vượt quyền và tuân thủ OWASP MASVS.

### Câu trả lời khi thuyết trình

Nếu được hỏi tại sao không làm tự động như app thương mại, câu trả lời nên nhấn mạnh: các app lớn có uy tín, partnership và đội ngũ compliance riêng. Với khóa luận, phương án user chủ động an toàn hơn, hợp pháp hơn, dễ demo hơn và vẫn đạt mục tiêu cảnh báo.

## 4. API_Specification.md

### Mục tiêu

Tài liệu đặc tả API backend cho Anti-Scam Platform, gồm authentication, scan, report, admin, response pattern, rate limiting và webhook tương lai.

### API surface chính

| Nhóm API | Endpoint tiêu biểu | Vai trò |
| --- | --- | --- |
| Authentication | `POST /auth/register`, `POST /auth/login`, `POST /auth/refresh` | Đăng ký, đăng nhập, làm mới token |
| Scan | `POST /scan/url`, `/scan/text`, `/scan/phone`, `/scan/bank-account`, `/scan/qr` | Kiểm tra rủi ro URL, nội dung, số điện thoại, tài khoản và QR |
| History | `GET /scan/history`, `GET /scan/{scanId}` | Xem lịch sử và chi tiết lần quét |
| Reports | `POST /reports`, `GET /reports/my` | Người dùng gửi và theo dõi báo cáo |
| Admin Rules | `/admin/rules` | Quản trị rule phát hiện |
| Admin Risk Entities | `/admin/risk-entities` | Quản lý blacklist/whitelist |
| Admin Reports | `/admin/reports` | Duyệt báo cáo cộng đồng |
| Dashboard | `/admin/dashboard/overview`, `/admin/dashboard/trends` | Theo dõi scan, report, blacklist và xu hướng |

### Response model

- Response thành công dùng `success: true`, `data` và `message` tùy chọn.
- Response lỗi dùng `success: false`, `error.code`, `error.message`, `details`, `timestamp` và `path`.
- Kết quả scan luôn nên có `riskScore`, `riskLevel`, `levelColor`, `confidence`, `analysis`, `evidences` và `recommendation`.

### Luồng scan tiêu biểu

1. Client gửi input cần kiểm tra.
2. Backend chuẩn hóa input và validate.
3. Rule engine, blacklist/whitelist và các module phân tích chạy theo loại input.
4. Backend trả về điểm rủi ro, evidence và khuyến nghị hành động.
5. Nếu user đồng ý lưu lịch sử, scan result được lưu để truy xuất.
6. Nếu user gửi báo cáo, admin hoặc hệ thống tự động xét duyệt để cập nhật blacklist.

### Kiểm soát an toàn

- Tất cả API phải dùng HTTPS.
- JWT access token có thời hạn 1 giờ, refresh token 7 ngày.
- Rate limiting áp dụng theo role, ví dụ free user 50 scan/giờ, authenticated user 200 scan/giờ.
- Input phải validate ở client và server.
- Redis dùng cho rate limiting và cache kết quả scan.
- API calls cần được log phục vụ security audit.

### Hướng mở rộng

- Webhook `report.verified` để thông báo khi báo cáo được xác minh.
- Dashboard trend analysis theo ngày/tháng.
- Admin scoring và rule management để cập nhật pattern scam mới mà không cần deploy lại toàn bộ hệ thống.
