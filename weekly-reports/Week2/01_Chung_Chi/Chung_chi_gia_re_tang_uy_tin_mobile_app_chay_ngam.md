# Chứng chỉ giá rẻ giúp tăng uy tín cho mobile app chạy ngầm

Ngày cập nhật: 2026-07-22

## 1. Bối cảnh và nguyên tắc lựa chọn

Tài liệu này ưu tiên các lựa chọn giá rẻ giúp tăng uy tín cho mobile app chạy ngầm, gồm chứng chỉ/certificate rõ ràng và một số bằng chứng định danh phân phối chính thống như Google Play hoặc Apple App Store. Các checklist tự đánh giá, badge quy trình và scan report không được đưa vào danh sách này.

Lưu ý quan trọng: chứng chỉ không thay thế chính sách của Google Play hoặc Apple App Store. Nếu app chạy nền, đọc SMS/Call Log, dùng foreground service hoặc xử lý dữ liệu nhạy cảm, vẫn phải có khai báo rõ ràng, consent chủ động, privacy policy và quyền đúng phạm vi.

## 2. Metric đánh giá

Các lựa chọn được đánh giá theo 7 metric:

| Metric | Ý nghĩa |
| --- | --- |
| Chi phí | Tổng chi phí trực tiếp để có chứng chỉ, certificate hoặc tài khoản phân phối. |
| Độ liên quan mobile chạy nền | Có giúp giải thích năng lực bảo mật, quyền nhạy cảm, xử lý dữ liệu nền hoặc bảo vệ backend hay không. |
| Uy tín bên thứ ba | Có tổ chức độc lập hoặc nền tảng lớn xác nhận không. |
| Bằng chứng công khai | Có certificate URL, digital badge, credential ID, store listing hoặc file có thể đưa vào báo cáo. |
| Độ khó đạt được | Thời gian học, chuẩn bị hồ sơ, thi hoặc cấu hình kỹ thuật. |
| Rủi ro gây hiểu nhầm | Có dễ bị hiểu sai là app đã được chứng nhận an toàn tuyệt đối không. |
| Giá trị cho đồ án | Có hỗ trợ thuyết phục giảng viên/user trong phạm vi sinh viên không. |

## 3. So sánh nhanh nhóm áp dụng cho app

| Lựa chọn | Loại | Chi phí tham khảo | Ưu tiên | Vì sao phù hợp |
| --- | --- | ---: | --- | --- |
| Google Play Developer + Play App Signing | Định danh/phân phối Android | 25 USD một lần | Rất cao | Rẻ, trực tiếp liên quan Android, có developer identity, store listing và app signing. |
| Apple Developer Program | Định danh/phân phối iOS | 99 USD/năm | Cao nếu có iOS | Có TestFlight/App Store, signing certificate/provisioning và seller identity. |
| Tín nhiệm mạng - Ứng dụng tín nhiệm | Nhãn/chứng nhận ứng dụng Việt Nam | Chưa công bố công khai | Cân nhắc cao | Có danh bạ ứng dụng đã kiểm duyệt và đạt chứng nhận tại Việt Nam, phù hợp nếu app public cho user Việt Nam. |
| Đăng ký quyền tác giả chương trình máy tính | Giấy chứng nhận sở hữu trí tuệ | 600.000 VND lệ phí nhà nước | Trung bình | Rẻ và có giá trị pháp lý tại Việt Nam, nhưng chứng minh quyền sở hữu phần mềm chứ không chứng nhận bảo mật. |
| Thông báo/đăng ký ứng dụng thương mại điện tử | Thủ tục quản lý ứng dụng TMĐT | Không thấy phí/lệ phí công khai | Thấp, chỉ khi có TMĐT | Có biểu tượng xác nhận/công bố công khai, nhưng chỉ áp dụng nếu app có hoạt động thương mại điện tử. |

## 4. So sánh nhanh chứng chỉ cá nhân/team

| Lựa chọn | Loại | Chi phí tham khảo | Ưu tiên | Vì sao phù hợp |
| --- | --- | ---: | --- | --- |
| OpenSSF Developing Secure Software LFD121 | Chứng chỉ hoàn thành cá nhân/team | Miễn phí | Rất cao | Tăng uy tín năng lực secure development của nhóm, có certificate/badge dễ đưa vào phụ lục. |
| Google Cybersecurity Professional Certificate | Chứng chỉ nghề nghiệp cá nhân | 49 USD/tháng, thường dưới 300 USD nếu học nhanh | Cao | Hữu ích cho thành viên mới, có credential dễ chia sẻ và liên quan vận hành bảo mật. |
| ISC2 Certified in Cybersecurity (CC) | Chứng chỉ cá nhân | 199 USD exam + 50 USD/năm AMF | Trung bình-cao | Uy tín hơn certificate online, phù hợp chứng minh nền tảng security của thành viên. |

## 5. Ghi chú kỹ thuật cho backend/API

| Lựa chọn | Loại | Chi phí tham khảo | Ưu tiên | Vì sao phù hợp |
| --- | --- | ---: | --- | --- |
| Let's Encrypt TLS Certificate | Chứng chỉ TLS cho backend/API | Miễn phí | Cao nếu có backend API | Chứng minh backend có HTTPS hợp lệ, trực tiếp liên quan bảo vệ dữ liệu app gửi lên server. |

## 6. Google Play Developer, Play App Signing và Data safety

### Là gì?

Đây không phải chứng chỉ bảo mật theo nghĩa pentest, nhưng là bộ bằng chứng uy tín quan trọng nhất nếu app Android cần chạy nền. Google Play Developer Account cho phép publish app, xác minh danh tính developer và tạo store listing. Play App Signing giúp Google quản lý và bảo vệ app signing key. Data safety là phần hiển thị cách app thu thập, chia sẻ và bảo vệ dữ liệu trước khi user cài đặt.

### Đánh giá trên metric gì?

- Định danh developer được Google yêu cầu xác minh.
- App được ký và update hợp lệ phải đi theo key hợp lệ.
- Store listing và Data safety có thể đưa vào báo cáo hoặc demo.
- Liên quan trực tiếp đến permission nhạy cảm, foreground service, disclosure và consent.

### Giá tiền

Google Play Console nêu phí đăng ký developer là 25 USD một lần.

### Cách đạt được

1. Tạo Google Play Developer Account.
2. Hoàn tất xác minh danh tính cá nhân hoặc tổ chức.
3. Tạo release bằng Android App Bundle (`.aab`).
4. Dùng Play App Signing, tách upload key và app signing key.
5. Điền Data safety form, privacy policy, quyền foreground service và quyền SMS/Call Log nếu có.
6. Với app chạy nền, chuẩn bị màn hình disclosure trong app trước khi xin quyền.

### Ứng dụng vào dự án

Đây là lựa chọn nên làm đầu tiên cho bản Android MVP vì chi phí thấp và có tác động trực tiếp tới niềm tin của user. Trong báo cáo có thể ghi: "Ứng dụng được phân phối qua Google Play, có developer identity, app signing, Data safety declaration và privacy policy công khai."

Không nên ghi rằng việc có tài khoản Play Console đồng nghĩa app đã được Google chứng nhận an toàn.

### Minh chứng

- Google Play Console yêu cầu phí đăng ký 25 USD một lần và xác minh developer identity: https://support.google.com/googleplay/android-developer/answer/6112435
- Play App Signing: Google quản lý và bảo vệ app signing key: https://support.google.com/googleplay/android-developer/answer/9842756
- Data safety hiển thị cách app thu thập/chia sẻ/bảo vệ dữ liệu: https://support.google.com/googleplay/android-developer/answer/10787469

## 7. Apple Developer Program và định danh tổ chức

### Là gì?

Nếu dự án có iOS app, Apple Developer Program cho phép TestFlight, App Store distribution, signing certificate/provisioning và hiển thị seller name. Với tài khoản organization, Apple yêu cầu thông tin pháp nhân để xác minh tổ chức.

### Đánh giá trên metric gì?

- Định danh developer: cá nhân hoặc tổ chức.
- Phân phối chính thống: TestFlight/App Store thay vì file lạ.
- Signing certificate/provisioning cho iOS build.
- Bằng chứng công khai: App Store listing, TestFlight build, seller name.

### Giá tiền

Apple Developer Program có phí 99 USD mỗi năm.

### Cách đạt được

1. Tạo Apple Account và enroll Developer Program.
2. Chọn individual hoặc organization.
3. Nếu organization, chuẩn bị thông tin pháp nhân, website tổ chức và email domain.
4. Ký app, tạo TestFlight/App Store listing.
5. Khai báo privacy và background modes đúng thực tế.

### Ứng dụng vào dự án

Chỉ nên ưu tiên nếu nhóm thật sự có iOS. Với app Android-only, ngân sách này nên dành cho Play Console và các chứng chỉ/certificate bảo mật trước.

### Minh chứng

- Apple Developer Program có giá 99 USD/năm và hỗ trợ TestFlight/App Store distribution: https://developer.apple.com/programs/
- Apple enrollment cho cá nhân hoặc tổ chức: https://developer.apple.com/programs/enroll/

## 8. OpenSSF Developing Secure Software LFD121

### Là gì?

LFD121 là khóa học miễn phí của OpenSSF/Linux Foundation về phát triển phần mềm an toàn. Người hoàn thành khóa học và pass final exam nhận certificate of completion/digital badge, có hiệu lực 2 năm.

### Đánh giá trên metric gì?

- Secure requirements, design, reuse.
- Secure implementation.
- Verification, static/dynamic analysis.
- Threat modeling, cryptography basics, vulnerability response.

### Giá tiền

Miễn phí cho cả khóa học và certificate of completion trên Linux Foundation Training.

### Cách đạt được

1. Thành viên phụ trách security đăng ký khóa LFD121.
2. Hoàn thành nội dung online tự học.
3. Pass final exam.
4. Đưa certificate URL hoặc ảnh badge vào phụ lục nhóm.

### Ứng dụng vào dự án

Đây là lựa chọn tốt để tăng uy tín năng lực nhóm khi chưa có ngân sách pentest. Trong báo cáo có thể ghi: "Thành viên phụ trách bảo mật đã hoàn thành OpenSSF Developing Secure Software LFD121" và liên kết sang checklist kiểm thử bảo mật của app.

### Minh chứng

- OpenSSF nêu khóa LFD121 và certificate miễn phí, học online 14-18 giờ, badge/certificate hiệu lực 2 năm: https://openssf.org/training/courses/

## 9. Google Cybersecurity Professional Certificate

### Là gì?

Đây là Professional Certificate online của Google trên Coursera, hướng đến nền tảng cybersecurity entry-level. Chứng chỉ có shareable certificate và dạy các kỹ năng như Linux, SQL, Python, SIEM, IDS, risk/threat/vulnerability và incident response.

### Đánh giá trên metric gì?

- Nền tảng vận hành bảo mật.
- Khả năng phân tích risk/threat/vulnerability.
- Kỹ năng công cụ: Linux, SQL, Python, SIEM, IDS.
- Credential dễ chia sẻ trên LinkedIn/CV.

### Giá tiền

Coursera/Google nêu giá 49 USD/tháng ở Mỹ và Canada sau 7 ngày trial; nhiều học viên hoàn thành dưới 300 USD nếu học dưới 6 tháng. Giá có thể thấp hơn theo quốc gia.

### Cách đạt được

1. Đăng ký trên Coursera.
2. Hoàn thành các course và assessment.
3. Xuất shareable certificate.
4. Đưa certificate vào phụ lục năng lực nhóm.

### Ứng dụng vào dự án

Hữu ích cho thành viên viết phần threat model, incident response, logging/monitoring và vận hành backend chống scam. Không dùng để claim app đã được kiểm thử độc lập.

### Minh chứng

- Google Cybersecurity Professional Certificate trên Coursera: https://www.coursera.org/professional-certificates/google-cybersecurity
- Google Career Certificates subscription 49 USD/tháng: https://career.skills.google/subscriptions

## 10. ISC2 Certified in Cybersecurity (CC)

### Là gì?

ISC2 Certified in Cybersecurity là chứng chỉ nền tảng cho cá nhân, phù hợp với sinh viên hoặc thành viên mới phụ trách bảo mật. Nó không chứng nhận app, nhưng giúp tăng độ tin cậy của người thiết kế quy trình bảo mật.

### Đánh giá trên metric gì?

Theo badge ISC2/Credly, kỹ năng bao gồm security principles, access controls, business continuity, disaster recovery, incident response, network security và security operations.

### Giá tiền

ISC2 công bố CC exam là 199 USD ở Asia Pacific. Người chỉ giữ CC phải trả Annual Maintenance Fee 50 USD/năm để duy trì chứng chỉ.

### Cách đạt được

1. Đăng ký ISC2/Pearson VUE.
2. Học các domain nền tảng.
3. Thi CC exam.
4. Hoàn tất application/Code of Ethics/AMF.
5. Claim digital badge trên Credly.

### Ứng dụng vào dự án

Phù hợp nếu nhóm cần chứng minh có thành viên hiểu nền tảng security. Trong báo cáo nên ghi chứng chỉ thuộc về cá nhân, không phải chứng nhận app. Kết hợp tốt với checklist kiểm thử bảo mật và threat model.

### Minh chứng

- ISC2 exam pricing: CC exam 199 USD ở Asia Pacific: https://www.isc2.org/register-for-exam/isc2-exam-pricing
- ISC2 AMF: CC holders trả 50 USD/năm: https://www.isc2.org/policies-procedures/amfs-overview
- Credly badge mô tả domain/skills của CC: https://www.credly.com/org/isc2/badge/certified-in-cybersecurity-cc.1

## 11. Let's Encrypt TLS Certificate cho backend/API

### Là gì?

Nếu app gửi URL/SMS metadata lên backend để phân tích scam, server phải dùng HTTPS đúng cấu hình. Let's Encrypt cung cấp TLS certificate miễn phí để chứng minh domain backend có chứng chỉ TLS hợp lệ.

### Đánh giá trên metric gì?

- TLS certificate hợp lệ, chain đúng, không hết hạn.
- Domain backend có HTTPS.
- Dữ liệu app gửi lên server được mã hóa khi truyền.
- Bằng chứng: certificate details, HTTPS URL, ảnh chụp hoặc log kiểm tra chứng chỉ.

### Giá tiền

Miễn phí cho Let's Encrypt TLS certificate.

### Cách đạt được

1. Trỏ domain backend, ví dụ `api.anti-scam.example`.
2. Cấp TLS certificate bằng Let's Encrypt hoặc ACME client như Certbot.
3. Bật HTTPS-only và tự động gia hạn certificate.
4. Lưu thông tin certificate issuer, expiry date và domain vào tài liệu kỹ thuật.

### Ứng dụng vào dự án

Đây là chứng chỉ kỹ thuật rẻ và trực tiếp liên quan privacy: app chạy nền không được gửi dữ liệu nhạy cảm qua kênh yếu. Nếu chỉ xử lý local và không có backend, mục này có thể ghi "không áp dụng".

### Minh chứng

- Let's Encrypt cung cấp free TLS certificates: https://letsencrypt.org/
- Certbot hỗ trợ tự động dùng Let's Encrypt certificate: https://certbot.eff.org/

## 12. Tín nhiệm mạng - Ứng dụng tín nhiệm

### Là gì?

Tín nhiệm mạng là hệ sinh thái do Trung tâm An ninh mạng Quốc gia (NCA) xây dựng nhằm đánh giá, chứng nhận và công bố các đối tượng có độ tin cậy trên không gian mạng. Với mobile app, mục liên quan nhất là danh bạ "Ứng dụng tín nhiệm", nơi hiển thị các ứng dụng đã được kiểm duyệt và đạt chứng nhận.

Đây là lựa chọn Việt Nam phù hợp nhất nếu app chống phishing/scam được phát hành công khai cho user Việt Nam. Bằng chứng có thể là trang danh bạ ứng dụng tín nhiệm, tên ứng dụng, chủ sở hữu, ngày tín nhiệm và link tải Google Play/App Store.

### Đánh giá trên metric gì?

- Độ liên quan mobile: áp dụng trực tiếp cho ứng dụng, không chỉ cho cá nhân hoặc backend.
- Uy tín bên thứ ba: gắn với hệ sinh thái Tín nhiệm mạng của NCA.
- Bằng chứng công khai: có danh bạ "Ứng dụng tín nhiệm" để trích dẫn trong báo cáo hoặc demo.
- Giá trị cho đồ án: phù hợp chủ đề chống lừa đảo/phishing vì nhấn mạnh niềm tin và nhận diện ứng dụng đáng tin cậy tại Việt Nam.
- Rủi ro gây hiểu nhầm: không được viết rằng app an toàn tuyệt đối hoặc chắc chắn được Google Play/App Store duyệt.

### Giá tiền

Trang công khai chưa nêu phí rõ ràng cho luồng chứng nhận ứng dụng. Vì vậy chỉ nên ghi "chưa công bố công khai, cần liên hệ NCA/Tín nhiệm mạng để xác nhận".

### Cách đạt được

1. Chuẩn bị bản app phát hành công khai hoặc bản gần release.
2. Chuẩn bị thông tin chủ sở hữu, tên app, package/bundle ID, link Google Play/App Store nếu có.
3. Chuẩn bị privacy policy, mô tả quyền nhạy cảm, quy trình xử lý dữ liệu và bằng chứng bảo mật cơ bản.
4. Liên hệ hoặc đăng ký với Tín nhiệm mạng/NCA để hỏi luồng đánh giá ứng dụng.
5. Nếu được duyệt, lưu trang danh bạ ứng dụng tín nhiệm và thông tin ngày tín nhiệm vào phụ lục.

### Ứng dụng vào dự án

Nếu app có bản public cho người dùng Việt Nam, đây là lựa chọn nên nghiên cứu sau Google Play Developer Account và privacy policy. Trong báo cáo có thể ghi: "Ứng dụng được đưa vào quy trình đánh giá Tín nhiệm mạng; nếu đạt, bằng chứng công khai là trang Ứng dụng tín nhiệm của NCA."

Nếu app chỉ là MVP chưa phát hành, nên ghi đây là hướng triển khai giai đoạn sau thay vì mục bắt buộc.

### Minh chứng

- Tín nhiệm mạng nêu hệ sinh thái do NCA xây dựng và cung cấp chứng nhận Tín nhiệm mạng: https://tinnhiemmang.vn/
- Danh bạ "Ứng dụng tín nhiệm" hiển thị các ứng dụng đã được kiểm duyệt và đạt chứng nhận: https://tinnhiemmang.vn/ung-dung-tin-nhiem

## 13. Đăng ký quyền tác giả chương trình máy tính

### Là gì?

Đây là thủ tục cấp Giấy chứng nhận đăng ký quyền tác giả cho chương trình máy tính tại Việt Nam. Nó không phải chứng chỉ bảo mật, nhưng là giấy chứng nhận pháp lý rẻ để chứng minh app/source code là sản phẩm của nhóm hoặc tổ chức.

### Đánh giá trên metric gì?

- Chi phí thấp, phù hợp phạm vi sinh viên.
- Uy tín pháp lý tại Việt Nam: có giấy chứng nhận của cơ quan nhà nước.
- Bằng chứng rõ ràng: số giấy chứng nhận, ngày cấp, chủ sở hữu/tác giả.
- Độ liên quan mobile chạy nền thấp: chứng minh quyền sở hữu phần mềm, không chứng minh app xử lý dữ liệu an toàn.
- Rủi ro gây hiểu nhầm: không được gọi là chứng nhận bảo mật hoặc chứng nhận chất lượng app.

### Giá tiền

Cổng dịch vụ công Bộ Văn hóa, Thể thao và Du lịch nêu phí cấp giấy chứng nhận cho chương trình máy tính, sưu tập dữ liệu hoặc chương trình chạy trên máy tính là 600.000 VND.

### Cách đạt được

1. Chuẩn bị tờ khai đăng ký quyền tác giả.
2. Chuẩn bị bản sao tác phẩm/chương trình máy tính theo yêu cầu hồ sơ.
3. Chuẩn bị giấy tờ chủ sở hữu/tác giả và giấy ủy quyền nếu nộp qua đại diện.
4. Nộp hồ sơ trực tuyến hoặc theo luồng của Cục Bản quyền tác giả.
5. Lưu Giấy chứng nhận đăng ký quyền tác giả vào phụ lục pháp lý của đồ án.

### Ứng dụng vào dự án

Phù hợp nếu nhóm muốn có bằng chứng Việt Nam rẻ về quyền sở hữu phần mềm. Trong báo cáo có thể ghi: "Nhóm đăng ký quyền tác giả chương trình máy tính cho mã nguồn/ứng dụng để chứng minh quyền sở hữu sản phẩm."

Không dùng mục này để chứng minh app đã được kiểm thử bảo mật, đạt chuẩn privacy hoặc được phép dùng quyền chạy nền.

### Minh chứng

- Dịch vụ công Bộ Văn hóa, Thể thao và Du lịch: thủ tục cấp Giấy chứng nhận đăng ký quyền tác giả, phí 600.000 VND cho chương trình máy tính/sưu tập dữ liệu/chương trình chạy trên máy tính: https://dichvucong.bvhttdl.gov.vn/congdan/Default.aspx?DKC=true&MucDo=4&ctl=ChiTiet&lv=OAB3AHoANQBMAGoAcAB3AGIAKwAxAHQASABTAE0ASQBIAEQAMgBYADIAZwA9AD0A0&mid=915&nid=1&tabid=249&ttid=MgBKADIAeQBaAFoALwBRAHMANABvADMAYgB4AHEAQQB4AGcAYQBCAGYAUQA9AD0A0

## 14. Thông báo/đăng ký ứng dụng thương mại điện tử với Bộ Công Thương

### Là gì?

Đây là thủ tục quản lý ứng dụng thương mại điện tử trên `online.gov.vn`, gồm thông báo ứng dụng bán hàng hoặc đăng ký ứng dụng cung cấp dịch vụ thương mại điện tử. Đây không phải chứng chỉ bảo mật, nhưng có thể tăng uy tín pháp lý nếu app thật sự có hoạt động thương mại điện tử.

### Đánh giá trên metric gì?

- Bằng chứng công khai: ứng dụng có thể được xác nhận/truy xuất trên hệ thống quản lý TMĐT.
- Uy tín bên thứ ba: gắn với Bộ Công Thương.
- Độ liên quan mobile chạy nền thấp nếu app chỉ cảnh báo scam/phishing.
- Chỉ phù hợp nếu app bán hàng, cung cấp dịch vụ giao dịch, marketplace, đặt dịch vụ hoặc có vai trò trung gian thương mại.
- Rủi ro gây hiểu nhầm: không phải chứng nhận bảo mật và không áp dụng cho app không có hoạt động TMĐT.

### Giá tiền

Không thấy mức phí/lệ phí công khai trong nguồn chính thức đã kiểm tra. Nên ghi "không thấy phí công khai; cần kiểm tra theo thủ tục cụ thể tại thời điểm nộp".

### Cách đạt được

1. Xác định app thuộc nhóm ứng dụng bán hàng hay ứng dụng cung cấp dịch vụ thương mại điện tử.
2. Tạo tài khoản trên `online.gov.vn`.
3. Khai báo thông tin thương nhân/tổ chức/cá nhân, ứng dụng, chính sách giao dịch và quy chế hoạt động nếu có.
4. Nộp hồ sơ thông báo hoặc đăng ký theo đúng loại ứng dụng.
5. Nếu được xác nhận, lưu thông tin xác nhận/công bố vào phụ lục pháp lý.

### Ứng dụng vào dự án

Với app cảnh báo phishing/scam không bán hàng và không vận hành sàn giao dịch, mục này thường không áp dụng. Chỉ đưa vào kế hoạch nếu app sau này có marketplace, giao dịch trả phí, dịch vụ trung gian hoặc chức năng thương mại điện tử.

### Minh chứng

- Online.gov.vn là cổng quản lý hoạt động thương mại điện tử và có quy trình thông báo/đăng ký ứng dụng TMĐT: https://online.gov.vn/
- Dịch vụ công Bộ Công Thương: đăng ký ứng dụng cung cấp dịch vụ thương mại điện tử thực hiện trực tuyến tại `online.gov.vn`: https://dichvucong.moit.gov.vn/VdxpTTHCOnlineDetail.aspx?DocId=443

## 15. Khuyến nghị triển khai cho đồ án

### Giai đoạn 1: miễn phí hoặc gần như miễn phí

1. Mua Google Play Developer Account 25 USD nếu làm Android MVP.
2. Hoàn thành OpenSSF LFD121 cho ít nhất 1 thành viên.
3. Nếu có backend, dùng Let's Encrypt TLS Certificate cho API.
4. Chuẩn bị privacy policy, Data safety, mô tả quyền chạy nền và consent rõ ràng.
5. Lưu certificate URL, digital badge, store listing hoặc thông tin TLS certificate vào phụ lục.

### Giai đoạn 2: chi phí thấp và phù hợp Việt Nam

1. Nếu app public cho user Việt Nam, liên hệ Tín nhiệm mạng/NCA để hỏi quy trình "Ứng dụng tín nhiệm".
2. Nếu cần bằng chứng sở hữu sản phẩm, đăng ký quyền tác giả chương trình máy tính.
3. Hoàn thành Google Cybersecurity Professional Certificate cho thành viên phụ trách threat model hoặc vận hành bảo mật.
4. Nếu có iOS, thêm Apple Developer Program 99 USD/năm để dùng TestFlight/App Store.

### Giai đoạn 3: khi cần credential hoặc thủ tục mạnh hơn

1. Cân nhắc ISC2 Certified in Cybersecurity cho thành viên phụ trách security.
2. Chỉ cân nhắc thủ tục TMĐT nếu app có chức năng thương mại điện tử.
3. Ghi rõ chứng chỉ cá nhân, thủ tục pháp lý và chứng nhận app là ba loại bằng chứng khác nhau.

## 16. Cách viết claim an toàn

Nên dùng:

- "Một thành viên nhóm đã hoàn thành OpenSSF Developing Secure Software LFD121."
- "Một thành viên nhóm đã hoàn thành Google Cybersecurity Professional Certificate."
- "Một thành viên nhóm đạt ISC2 Certified in Cybersecurity."
- "Backend sử dụng Let's Encrypt TLS Certificate hợp lệ cho HTTPS."
- "Ứng dụng được phân phối qua Google Play với developer identity, app signing và Data safety declaration."
- "Bản iOS được phân phối qua TestFlight/App Store bằng Apple Developer Program."
- "Ứng dụng được nghiên cứu/đưa vào quy trình Tín nhiệm mạng - Ứng dụng tín nhiệm tại Việt Nam."
- "Nhóm đăng ký quyền tác giả chương trình máy tính để chứng minh quyền sở hữu phần mềm."

Không nên dùng:

- "Có chứng chỉ nên app được phép chạy nền/đọc SMS/Call Log."
- "Chứng chỉ cá nhân chứng minh app đã an toàn tuyệt đối."
- "Let's Encrypt chứng nhận toàn bộ hệ thống bảo mật."
- "Google/ISC2/OpenSSF đã chứng nhận app chống scam này."
- "Có tài khoản Google Play hoặc Apple Developer nên app đã được chứng nhận bảo mật."
- "Đăng ký quyền tác giả chứng minh app bảo mật."
- "Thông báo/đăng ký TMĐT chứng minh app an toàn thông tin."

## 17. Appdome Certified Secure có phù hợp không?

### Kết luận nhanh

Appdome Certified Secure phù hợp về mặt kỹ thuật nếu dự án cần chứng nhận bảo mật theo từng build Android/iOS trong CI/CD. Tuy nhiên, không nên đưa vào danh sách chính của tài liệu này vì chưa có giá rẻ công khai và cần license Appdome riêng.

### Vì sao có liên quan?

- Đây là certificate theo từng build, gắn với app name, bundle ID, version, build ID và các lớp bảo vệ đã bật.
- Có thể xuất bằng chứng dạng certificate/report cho release, audit hoặc phụ lục kỹ thuật.
- Liên quan trực tiếp mobile app vì bao gồm các nhóm bảo vệ như RASP, anti-fraud, anti-malware, obfuscation, secure communication, root/jailbreak detection.
- Hữu ích hơn các chứng chỉ cá nhân nếu nhóm cần chứng minh chính build app đã được hardening bằng nền tảng Appdome.

### Vì sao không ưu tiên cho đồ án giá rẻ?

- Appdome yêu cầu app được protect bằng Appdome và cần license Certified Secure riêng.
- Trang pricing chính thức dùng cơ chế quote request, không công bố mức phí thấp cố định như Google Play Developer 25 USD hoặc Apple Developer Program 99 USD/năm.
- Dễ gây hiểu nhầm nếu viết quá mạnh, vì certificate này xác nhận các protection được build bằng Appdome, không thay thế review chính sách Google Play/App Store, pentest độc lập hoặc kiểm chứng hành vi xử lý dữ liệu nhạy cảm.

### Cách ghi trong báo cáo nếu sau này dùng

Nên dùng:

- "Bản build Android/iOS được Appdome Certified Secure ghi nhận các lớp bảo vệ đã tích hợp trong pipeline."
- "Appdome certificate được dùng như bằng chứng kỹ thuật cho release readiness, không thay thế kiểm thử bảo mật độc lập."

Không nên dùng:

- "Appdome chứng nhận app chống scam an toàn tuyệt đối."
- "Có Appdome Certified Secure nên app chắc chắn được Google Play/App Store duyệt."

### Minh chứng

- Appdome Certified Secure tạo certificate theo từng Android/iOS release/build và ghi lại các protection trong DevSecOps pipeline: https://www.appdome.com/certified-secure-mobile-devsecops-certification/
- Appdome Knowledge Base nêu cần Appdome account, app binary, signing credentials và license Certified Secure riêng: https://www.appdome.com/how-to/devsecops-automation-mobile-cicd/certified-secure-devsecops-certification-android-ios/using-certified-secure-android-ios-apps-build-certification-in-devops-cicd/
- Trang pricing Appdome là quote request và Certified Secure là một feature trong platform package: https://www.appdome.com/appdome-pricing/

## 18. Kết luận

Với mục tiêu giữ các lựa chọn giá rẻ nhưng vẫn tăng uy tín cho mobile app chạy ngầm, nhóm nên ưu tiên Google Play Developer Account cho Android MVP. Nếu app public cho người dùng Việt Nam, Tín nhiệm mạng - Ứng dụng tín nhiệm là lựa chọn Việt Nam đáng kiểm tra nhất. Nếu cần bằng chứng sở hữu sản phẩm, đăng ký quyền tác giả chương trình máy tính là lựa chọn rẻ và rõ ràng. Nếu có iOS, Apple Developer Program nên được đưa vào kế hoạch. Nếu app có chức năng thương mại điện tử, mới xét thủ tục TMĐT với Bộ Công Thương. Nhóm chứng chỉ cá nhân nên tách riêng để chứng minh năng lực thành viên, gồm OpenSSF LFD121, Google Cybersecurity Professional Certificate và ISC2 Certified in Cybersecurity. Let's Encrypt chỉ nên ghi như hạng mục kỹ thuật cho backend/API nếu có backend. Appdome Certified Secure chỉ nên xem là lựa chọn enterprise hoặc giai đoạn sau, khi nhóm có license Appdome và cần chứng nhận theo từng build app.
