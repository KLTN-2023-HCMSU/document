# Script thuyết trình Week2 - Anti-Scam Platform

## Ghi chú sử dụng

- Thời lượng gợi ý: khoảng 12-18 phút cho 16 slide.
- Cách nói: dùng ngôi "em" hoặc "nhóm em" tùy người trình bày.
- Mỗi slide gồm: mục tiêu nói, script chính, ý cần nhấn và câu chuyển slide.

---

## Slide 1 - Thiết kế nền tảng chống scam theo hướng an toàn và có thể giải thích

**Mục tiêu:** Mở đầu buổi trình bày, nêu phạm vi Week2.

**Script:**

Thưa thầy/cô, trong Week2 nhóm em tập trung chuyển đề tài từ ý tưởng tổng quát sang các quyết định thiết kế cụ thể cho nền tảng cảnh báo scam/phishing.

Có ba câu hỏi chính nhóm em cần trả lời. Thứ nhất, nếu xây dựng một mobile app chống scam thì cần những bằng chứng hoặc chứng chỉ nào để tăng độ tin cậy với người dùng. Thứ hai, hệ thống sẽ phát hiện rủi ro bằng cách nào: dùng rule, heuristic, blacklist, cộng đồng báo cáo hay machine learning. Thứ ba, mobile app có thể làm gì mà vẫn đúng chính sách nền tảng và không xâm phạm quyền riêng tư.

Vì vậy, thông điệp chính của Week2 là: MVP nên được thiết kế theo hướng an toàn, minh bạch và giải thích được. App không cố gắng đọc dữ liệu ngầm, mà để người dùng chủ động đưa link, tin nhắn, số điện thoại, số tài khoản hoặc QR vào hệ thống để kiểm tra.

**Ý cần nhấn:** Week2 chốt hướng MVP: permission tối thiểu, rule engine giải thích được, có API backend và lộ trình mở rộng ML.

**Chuyển slide:** Trước khi đi vào từng phần, em xin tóm tắt 4 tài liệu nguồn đã dùng trong tuần này.

---

## Slide 2 - 4 tài liệu nguồn, 1 mạch triển khai

**Mục tiêu:** Giải thích cấu trúc nội dung Week2.

**Script:**

Tuần này nhóm em tổng hợp nội dung từ bốn mảng tài liệu. Mảng đầu tiên là uy tín của app, gồm các lựa chọn như Google Play Developer, Apple Developer Program, Tín nhiệm mạng, đăng ký quyền tác giả và chứng chỉ TLS cho backend. Mục tiêu của phần này là xác định cái gì thật sự giúp app đáng tin hơn, và cái gì không nên claim quá mức.

Mảng thứ hai là phát hiện scam. Nhóm em phân tích các hướng rule-based, machine learning và hybrid, sau đó đi vào từng module như URL scanner, text analyzer, phone/bank checker và QR scanner.

Mảng thứ ba là quyền mobile. Đây là phần quan trọng vì app chống scam rất dễ bị hiểu là cần đọc SMS, notification hoặc clipboard ngầm. Sau khi phân tích Android, iOS và Google Play policy, nhóm em chọn hướng user chủ động để giảm rủi ro pháp lý và tăng khả năng được duyệt store.

Mảng cuối cùng là API backend. API được thiết kế để phục vụ các luồng auth, scan, lịch sử, báo cáo lừa đảo và quản trị rule/blacklist.

**Ý cần nhấn:** Bốn phần không rời rạc; chúng nối thành một mạch: tăng uy tín, phát hiện rủi ro, giữ đúng quyền hạn, rồi hiện thực bằng API.

**Chuyển slide:** Đầu tiên em trình bày phần chứng chỉ và các thủ tục giúp app tăng độ tin cậy.

---

## Slide 3 - Bảng so sánh các chứng chỉ/thủ tục cho app

**Mục tiêu:** Trình bày lựa chọn chứng chỉ/thủ tục phù hợp cho MVP.

**Script:**

Ở phần chứng chỉ, nhóm em không xem chứng chỉ như bằng chứng app an toàn tuyệt đối. Nhóm em phân loại chúng theo đúng vai trò: một số giúp định danh và phân phối app chính thống, một số giúp chứng minh pháp lý, một số giúp chứng minh năng lực đội ngũ hoặc bảo vệ backend.

Với Android MVP, lựa chọn ưu tiên nhất là Google Play Developer kết hợp Play App Signing và Data safety. Chi phí 25 USD một lần, nhưng đem lại các bằng chứng quan trọng: app được phát hành qua kênh chính thống, có developer identity, được ký đúng quy trình và có khai báo dữ liệu rõ ràng.

Nếu có iOS, Apple Developer Program là lựa chọn tương ứng, nhưng chi phí 99 USD mỗi năm nên chưa phải ưu tiên nếu MVP tập trung Android. Với bối cảnh Việt Nam, Tín nhiệm mạng - Ứng dụng tín nhiệm là hướng đáng nghiên cứu sau MVP vì có bên thứ ba trong nước xác nhận, nhưng chi phí chưa công bố công khai và cần liên hệ đơn vị vận hành.

Đăng ký quyền tác giả chương trình máy tính giúp tăng uy tín pháp lý, nhưng chỉ chứng minh quyền sở hữu phần mềm, không chứng minh bảo mật. Còn thủ tục thương mại điện tử chỉ phù hợp nếu app có giao dịch, marketplace hoặc trung gian thanh toán, nên chưa áp dụng cho MVP cảnh báo scam.

**Ý cần nhấn:** Claim phải đúng phạm vi: các chứng chỉ hỗ trợ niềm tin, nhưng không thay thế privacy policy, consent, Data safety hoặc review của Google/Apple.

**Chuyển slide:** Sau phần uy tín, em chuyển sang quyết định kỹ thuật quan trọng nhất: hệ thống sẽ phát hiện scam bằng hướng nào.

---

## Slide 4 - Chọn hướng tiếp cận: rule-based trước, ML sau

**Mục tiêu:** Bảo vệ lựa chọn rule-based cho MVP.

**Script:**

Trong tài liệu phân tích thuật toán, nhóm em so sánh ba hướng chính: rule-based, machine learning và hybrid. Machine learning có ưu điểm là phát hiện pattern mới và trong nhiều nghiên cứu có độ chính xác cao. Nhưng để dùng tốt, nhóm cần dataset đủ lớn, dữ liệu được label rõ ràng, quy trình training/evaluation và khả năng giải thích model.

Với phạm vi khóa luận và MVP, nhóm em chọn rule-based kết hợp heuristic trước. Lý do là hướng này giải thích được rõ ràng, dễ triển khai, dễ kiểm thử và không phụ thuộc vào dataset lớn ngay từ đầu. Mỗi cảnh báo trả về không chỉ có điểm số, mà còn có evidence: rule nào match, vì sao cộng điểm, người dùng nên làm gì tiếp theo.

Hybrid vẫn là mục tiêu dài hạn. Khi hệ thống đã có dữ liệu báo cáo thật và baseline rule-based rõ ràng, nhóm có thể thêm ML vào các trường hợp không chắc chắn. Ví dụ rule engine xử lý nhanh các case quá an toàn hoặc quá nguy hiểm; còn vùng điểm trung gian mới chuyển sang ML classifier.

**Ý cần nhấn:** Rule-based không phải vì ML kém, mà vì MVP cần minh bạch, kiểm thử được và phù hợp dữ liệu hiện có.

**Chuyển slide:** Tiếp theo là module đầu tiên: phân tích link và các trang nhập liệu.

---

## Slide 5 - Phân tích link và trang nhập liệu bằng tín hiệu có thể giải thích

**Mục tiêu:** Giải thích URL Scanner và Form/HTML Analyzer.

**Script:**

Với URL Scanner, nhóm em thiết kế theo từng bước có thể giải thích. Đầu tiên là chuẩn hóa URL: loại bỏ khoảng trắng, lowercase, decode URL encoding, expand shortener nếu có, rồi tách domain, path và query.

Sau đó hệ thống trích xuất các đặc trưng rủi ro như URL quá dài, nhiều subdomain, dùng IP thay vì domain, không có HTTPS, có ký tự bất thường như `@` hoặc `//`, hoặc có redirect chain đáng ngờ. Những tín hiệu này được cộng điểm theo trọng số.

Lớp tiếp theo là reputation. Nếu domain nằm trong blacklist thì cộng điểm mạnh; nếu nằm trong whitelist thì trừ điểm. Ngoài ra, nhóm em dùng similarity để phát hiện typosquatting, ví dụ domain giả mạo thương hiệu bằng cách thay chữ `o` bằng số `0`. Các thuật toán phù hợp là Levenshtein Distance hoặc Jaro-Winkler, so với danh sách brand phổ biến tại Việt Nam.

Phần Form/HTML dùng Jsoup để parse trang, trích xuất form và input. Nếu form yêu cầu OTP, PIN, CVV, CCCD hoặc tài khoản ngân hàng, hoặc form submit sang domain khác, hệ thống sẽ tăng điểm rủi ro. Nhờ đó kết quả không chỉ nói "nguy hiểm", mà nói rõ nguy hiểm vì đặc điểm nào.

**Ý cần nhấn:** Mỗi tín hiệu đều chuyển thành điểm và evidence, giúp người dùng hiểu lý do cảnh báo.

**Chuyển slide:** Sau URL và form, nhóm em xử lý một nguồn scam rất phổ biến: nội dung tin nhắn hoặc giao dịch.

---

## Slide 6 - Tin nhắn được xử lý bằng entity, keyword và pattern kết hợp

**Mục tiêu:** Trình bày pipeline phân tích text.

**Script:**

Với tin nhắn, nhóm em không cần bắt đầu bằng NLP phức tạp. MVP dùng keyword-based, pattern extraction và rule scoring. Bước đầu tiên là tiền xử lý văn bản tiếng Việt: lowercase, loại ký tự nhiễu, chuẩn hóa số điện thoại, số tiền và tách câu đơn giản.

Bước thứ hai là trích xuất thực thể bằng regex. Hệ thống tìm URL, số điện thoại Việt Nam, số tài khoản, số tiền, mã OTP và các cụm deadline như "trong 5 phút" hoặc "trước ngày hôm nay". Đây là các thực thể thường xuất hiện trong lừa đảo.

Sau đó hệ thống áp dụng keyword dictionary. Nhóm từ khóa được chia theo ý đồ: tạo áp lực khẩn cấp, giả mạo ngân hàng/công an/shipper, yêu cầu thông tin nhạy cảm, yêu cầu chuyển tiền hoặc hứa hẹn quá tốt để tin như trúng thưởng, việc nhẹ lương cao.

Điểm quan trọng là nhóm em không chỉ cộng keyword rời rạc. Hệ thống còn có composite rules. Ví dụ nếu một tin nhắn có từ khóa ngân hàng, có yêu cầu xác minh, có OTP hoặc URL lạ thì match pattern giả mạo ngân hàng. Nếu có tuyển dụng, phí và chuyển khoản thì match pattern lừa đảo tuyển dụng.

Cuối cùng, nếu tin nhắn chứa URL, số điện thoại hoặc số tài khoản, hệ thống gọi chéo sang các module tương ứng để có điểm tổng hợp.

**Ý cần nhấn:** Text analyzer kết hợp entity, keyword, pattern và cross-reference để tránh chỉ dựa vào một dấu hiệu đơn lẻ.

**Chuyển slide:** Các thực thể được trích xuất như số điện thoại, tài khoản ngân hàng và QR sẽ được kiểm tra ở module tiếp theo.

---

## Slide 7 - Số điện thoại, tài khoản ngân hàng và QR dùng lookup cộng đồng

**Mục tiêu:** Trình bày Phone/Bank Checker và QR Scanner.

**Script:**

Với số điện thoại và tài khoản ngân hàng, nhóm em dùng hướng blacklist lookup kết hợp community score. Đầu vào trước hết được chuẩn hóa: số điện thoại đưa về dạng canonical, số tài khoản bỏ khoảng trắng hoặc dấu gạch.

Để giảm rủi ro privacy, hệ thống có thể lưu và tra cứu bằng hash, ví dụ SHA-256 của số điện thoại hoặc số tài khoản, thay vì lưu dữ liệu thô trong log. Khi tra cứu, backend kiểm tra entity có trong blacklist hay whitelist không, có bao nhiêu báo cáo, báo cáo gần nhất khi nào và người báo cáo có độ uy tín ra sao.

Điểm rủi ro cũng cần tính theo thời gian. Một số có nhiều báo cáo trong 7 ngày gần đây nên nguy hiểm hơn số chỉ có báo cáo từ vài tháng trước. Vì vậy tài liệu đề xuất time-decay: báo cáo càng mới thì trọng số càng cao.

Với QR Scanner, hệ thống decode QR bằng thư viện như ZXing. Sau khi lấy raw data, app phân loại nội dung: nếu là URL thì gọi URL Scanner, nếu là VietQR thì parse bank ID, account number, amount và content, nếu là plain text thì gọi Text Analyzer. Như vậy QR không phải một module tách biệt hoàn toàn, mà là cửa vào để định tuyến sang các module phân tích khác.

**Ý cần nhấn:** Lookup cộng đồng cần đi cùng privacy và time-decay để vừa hữu ích vừa tránh kết luận quá cứng.

**Chuyển slide:** Để các module này nhất quán, nhóm em cần một rule engine lõi.

---

## Slide 8 - Core engine cần tách rule, condition và evidence

**Mục tiêu:** Giải thích kiến trúc rule engine.

**Script:**

Rule engine là lõi giúp các module dùng chung cách tính điểm. Nhóm em tách thành ba phần.

Phần đầu là Rule Repository. Đây là nơi lưu các rule như `URL_NO_HTTPS`, `DOMAIN_TYPOSQUATTING`, `TEXT_HAS_OTP` hoặc `PATTERN_BANK_SCAM`. Mỗi rule có mã, tên, trọng số và trạng thái enabled. Nếu lưu rule trong database kèm cache, hệ thống có thể cập nhật trọng số hoặc bật/tắt rule mà không phải sửa code lõi.

Phần thứ hai là Rule Evaluator. Evaluator nhận input đã được chuẩn hóa, duyệt qua các active rules, kiểm tra rule nào match và cộng điểm. Đồng thời evaluator thu thập evidence gồm mã rule, tên rule, điểm, severity và lời giải thích.

Phần thứ ba là Rule Composer. Phần này cho phép tạo rule kết hợp bằng AND, OR, NOT. Ví dụ một rule giả mạo ngân hàng không chỉ cần từ "ngân hàng", mà cần thêm OTP, xác minh hoặc URL lạ. Composer cũng ánh xạ điểm sang level: dưới 30 là SAFE, 30 đến 59 là CAUTION, từ 60 trở lên là DANGER.

**Ý cần nhấn:** Evidence là phần quan trọng để hệ thống có thể giải thích, kiểm thử và bảo trì.

**Chuyển slide:** Khi rule engine đã ổn định, machine learning sẽ là hướng mở rộng có điều kiện.

---

## Slide 9 - ML chỉ nên thêm khi có dữ liệu và baseline rõ

**Mục tiêu:** Đặt ML vào đúng vai trò mở rộng.

**Script:**

Nhóm em không loại bỏ machine learning, nhưng không đưa ML thành phần bắt buộc của MVP. Lý do là ML chỉ hiệu quả khi có dataset đủ lớn và được label đáng tin cậy. Tài liệu đặt điều kiện tối thiểu là khoảng 10.000 mẫu đã label, gồm cả phishing và legitimate.

Ngoài dữ liệu, nhóm cũng cần baseline rule-based trước. Nếu rule-based đã chạy được, đo được false positive và false negative, lúc đó mới biết ML cần cải thiện phần nào. Nếu đưa ML quá sớm, hệ thống có thể khó giải thích và khó chứng minh trong phạm vi khóa luận.

Roadmap ML của nhóm gồm bốn bước. Đầu tiên là thu thập dữ liệu từ PhishTank, nguồn URL hợp lệ, dữ liệu Tín nhiệm mạng, và user reports sau vài tháng vận hành. Thứ hai là feature engineering với các đặc trưng URL, domain, content và similarity. Thứ ba là thử các model như Random Forest hoặc XGBoost vì có feature importance, dễ giải thích hơn deep learning. Cuối cùng là đánh giá bằng precision, recall, F1 và inference time.

Mục tiêu dài hạn là hybrid: rule engine xử lý nhanh phần rõ ràng, ML xử lý vùng không chắc chắn.

**Ý cần nhấn:** ML là hướng phát triển hợp lý, nhưng phải có dữ liệu, baseline và metric trước.

**Chuyển slide:** Dù dùng rule hay ML, trải nghiệm người dùng vẫn cần kết quả gần như tức thời.

---

## Slide 10 - Thiết kế phải đủ nhanh cho trải nghiệm scan tức thời

**Mục tiêu:** Trình bày chiến lược performance.

**Script:**

Với app chống scam, tốc độ rất quan trọng vì người dùng thường cần quyết định ngay: có bấm link không, có chuyển tiền không, có gọi lại số này không. Vì vậy backend cần tối ưu theo ba hướng.

Thứ nhất là cache. L1 cache như Caffeine dùng trong memory để lưu domain reputation, phone/account lookup hoặc kết quả scan gần đây. L2 cache như Redis dùng cho blacklist, whitelist, prediction hoặc thống kê tổng hợp. TTL cần khác nhau theo loại dữ liệu: recent scan có thể chỉ vài phút, blacklist/whitelist có thể lâu hơn.

Thứ hai là database. Các bảng entity rủi ro cần index theo `entity_type` và `value_hash`, vì lookup số điện thoại, tài khoản hoặc domain là truy vấn rất thường xuyên. Lịch sử scan cần index theo user và thời gian để hiển thị nhanh. Audit log có thể partition theo tháng khi dữ liệu tăng.

Thứ ba là async processing. Một lần scan URL có thể cần kiểm tra domain, SSL, redirect và nội dung trang. Các bước này nên chạy song song rồi combine kết quả, thay vì chạy tuần tự và làm user chờ lâu.

**Ý cần nhấn:** Performance không phải tối ưu phụ; nó ảnh hưởng trực tiếp đến việc người dùng có kịp tránh scam hay không.

**Chuyển slide:** Từ các module và tối ưu này, nhóm em đề xuất lộ trình triển khai theo độ ưu tiên.

---

## Slide 11 - Lộ trình triển khai module theo độ ưu tiên

**Mục tiêu:** Nêu kế hoạch triển khai thực tế cho MVP.

**Script:**

Lộ trình triển khai được chia theo dependency kỹ thuật. Giai đoạn đầu tiên là Rule Core. Nhóm cần thiết kế schema cho `RiskRule` và `RuleCondition`, implement evaluator, viết unit test cho từng rule và tạo khoảng 20 đến 30 rule cơ bản cho URL, text và phone.

Giai đoạn thứ hai là URL và Text. Đây là hai luồng có giá trị demo cao nhất: URL normalization, SSL checker, redirect analyzer, domain similarity, regex entity extraction và keyword dictionary tiếng Việt.

Giai đoạn thứ ba là Phone, Bank và QR. Nhóm sẽ xây dựng hash lookup, community report score, time-decay, sau đó thêm QR scanner bằng ZXing và parser cho VietQR. Đây là phần rất phù hợp với bối cảnh Việt Nam vì QR chuyển khoản và tài khoản ngân hàng xuất hiện nhiều trong lừa đảo online.

Giai đoạn cuối là ML optional. Khi đã có dữ liệu và log scan đủ tốt, nhóm mới thu thập dataset, extract feature và thử Random Forest hoặc XGBoost để so sánh rule-based, ML và hybrid.

**Ý cần nhấn:** Thứ tự triển khai đi từ lõi chung đến module, rồi mới đến ML; không nhảy vào phần phức tạp trước khi có baseline.

**Chuyển slide:** Bên cạnh thuật toán, một quyết định quan trọng khác là mobile app không được vượt quyền.

---

## Slide 12 - Không vượt quyền là quyết định thiết kế

**Mục tiêu:** Bảo vệ quan điểm permission tối thiểu.

**Script:**

Với mobile app chống scam, cám dỗ kỹ thuật là xin nhiều quyền: đọc SMS, đọc notification, đọc call log hoặc clipboard tự động. Nhưng nhóm em xem việc không vượt quyền là một quyết định thiết kế, không chỉ là hạn chế kỹ thuật.

Về pháp lý và privacy, SMS, call log, contact, notification và clipboard đều có thể chứa dữ liệu cá nhân hoặc dữ liệu nhạy cảm. Theo nguyên tắc xử lý dữ liệu cá nhân, app chỉ nên thu thập tối thiểu, có mục đích rõ ràng và có sự đồng ý chủ động.

Về nền tảng, Android và iOS đều ngày càng giới hạn quyền chạy nền. Android hạn chế đọc SMS/Call Log nếu app không phải app mặc định, hạn chế background execution và clipboard. iOS còn chặt hơn, gần như không cho app bên thứ ba đọc SMS hoặc call log.

Về Google Play policy, các quyền như READ_SMS, READ_CALL_LOG, Accessibility hoặc Notification Listener rất nhạy cảm và dễ bị reject nếu không chứng minh use case hợp lệ.

Vì vậy MVP chỉ dùng các quyền hợp lý: Internet để gọi API, Camera khi user quét QR, Share Target để nhận link do user chủ động chia sẻ, và notification của chính app để trả cảnh báo.

**Ý cần nhấn:** Bảo vệ người dùng không được đánh đổi bằng việc xâm phạm quyền riêng tư của chính người dùng.

**Chuyển slide:** Từ nguyên tắc đó, nhóm em thay các tính năng chạy ngầm bằng các luồng user chủ động.

---

## Slide 13 - Thay "chạy ngầm" bằng luồng user chủ động

**Mục tiêu:** Trình bày các flow mobile có thể demo và đúng policy.

**Script:**

Thay vì tự động đọc SMS hoặc notification, nhóm em thiết kế các luồng user chủ động. Luồng đầu tiên là Share Target. Khi user thấy một link nghi ngờ trong SMS, Zalo, Messenger hoặc browser, user có thể share link sang Anti-Scam. App nhận intent, phân tích và trả kết quả ngay.

Luồng thứ hai là QR Scanner. User mở app, chọn quét QR, cấp quyền camera tại thời điểm sử dụng, sau đó app decode QR và kiểm tra URL hoặc tài khoản ngân hàng bên trong. Luồng này rất dễ giải thích với giảng viên và phù hợp hành vi thanh toán QR tại Việt Nam.

Luồng thứ ba là Manual Input. User có thể nhập URL, số điện thoại, số tài khoản hoặc nội dung tin nhắn để kiểm tra. Đây là backup đơn giản nhất và gần như chắc chắn pass review vì không cần quyền nhạy cảm.

Luồng thứ tư là Clipboard foreground. App chỉ đọc clipboard khi đang mở và user bấm nút kiểm tra nội dung vừa copy, không theo dõi clipboard ngầm.

Khi demo, nhóm em có thể chuẩn bị ba kịch bản: SMS có URL giả mạo ngân hàng, QR chuyển khoản đáng ngờ và share link từ Zalo. Nếu được hỏi tại sao không làm như Whoscall hoặc nTrust, câu trả lời là các app đó có uy tín, partnership và use case đã được phê duyệt; còn MVP khóa luận nên chọn hướng an toàn, hợp pháp và đủ chứng minh thuật toán.

**Ý cần nhấn:** User chủ động không làm giảm giá trị MVP; nó giúp MVP hợp pháp, dễ duyệt và dễ được người dùng tin hơn.

**Chuyển slide:** Các flow mobile này cần backend API để nhận input, scan và trả kết quả có cấu trúc.

---

## Slide 14 - API bao phủ auth, scan, history, report và admin

**Mục tiêu:** Trình bày phạm vi API backend.

**Script:**

API backend được chia thành bốn nhóm chính. Nhóm đầu tiên là Authentication, gồm đăng ký, đăng nhập và refresh token. Backend dùng JWT access token và refresh token để phân quyền user và admin.

Nhóm thứ hai là Scan APIs. Đây là phần lõi cho mobile app, gồm `/scan/url`, `/scan/text`, `/scan/phone`, `/scan/bank-account` và `/scan/qr`. Mỗi endpoint nhận input tương ứng, chạy module phân tích và trả về risk score, risk level, analysis, evidence và recommendation.

Nhóm thứ ba là History và Reports. User có thể xem lịch sử scan, xem chi tiết một lần scan và gửi báo cáo lừa đảo kèm mô tả, bằng chứng hoặc số tiền thiệt hại nếu có. Phần này giúp hệ thống xây dựng dữ liệu cộng đồng cho blacklist và future ML.

Nhóm thứ tư là Admin APIs. Admin có thể quản lý rule, risk entities, blacklist/whitelist, review báo cáo và xem dashboard/trend analysis. Đây là phần quan trọng vì rule-based system cần cơ chế cập nhật rule và kiểm duyệt dữ liệu báo cáo.

**Ý cần nhấn:** API không chỉ phục vụ demo scan, mà còn hỗ trợ vòng đời vận hành: scan, lưu lịch sử, report, kiểm duyệt và cập nhật rule.

**Chuyển slide:** Điểm quan trọng của API là response phải giúp user ra quyết định, không chỉ trả điểm số.

---

## Slide 15 - Kết quả API phải phục vụ quyết định của user

**Mục tiêu:** Nêu cấu trúc response và chuẩn vận hành API.

**Script:**

Kết quả scan của API cần được thiết kế để người dùng hiểu và hành động được. Vì vậy response không chỉ có `riskScore`. Nó gồm nhiều lớp thông tin.

Lớp đầu tiên là kết quả tổng quan: `riskScore` từ 0 đến 100, `riskLevel` là SAFE, CAUTION hoặc DANGER, kèm màu hiển thị như green, yellow hoặc red. Đây là phần app dùng để hiển thị nhanh.

Lớp thứ hai là analysis. Với URL, analysis có thể gồm HTTPS, redirect, domain similarity, blacklist hoặc whitelist. Với text, analysis có entities, keyword matches và pattern matches. Với QR, analysis có dữ liệu parse từ QR và kết quả check tài khoản hoặc URL bên trong.

Lớp thứ ba là evidences. Đây là danh sách rule đã match, severity và mô tả. Ví dụ domain giả mạo thương hiệu, tin nhắn tạo áp lực khẩn cấp hoặc tài khoản có nhiều báo cáo.

Lớp cuối là recommendation. Đây là phần rất quan trọng cho UX: user nên block, verify hay tiếp tục cẩn trọng; vì sao; và next steps cụ thể như không bấm link, gọi hotline chính thức hoặc gửi báo cáo.

Ngoài response scan, API cũng cần chuẩn vận hành: success/error response thống nhất, error code rõ cho validation/auth/rate limit và rate limiting theo nhóm endpoint để tránh abuse.

**Ý cần nhấn:** Một hệ thống chống scam tốt phải trả lời được ba câu: rủi ro bao nhiêu, vì sao rủi ro, và người dùng nên làm gì.

**Chuyển slide:** Em xin kết luận lại các quyết định chính của Week2.

---

## Slide 16 - Week2 chốt phạm vi MVP thực tế

**Mục tiêu:** Kết luận và mở hướng Week3.

**Script:**

Tóm lại, Week2 giúp nhóm em chốt phạm vi MVP theo hướng thực tế. Về uy tín, nhóm ưu tiên phân phối chính thống, privacy policy, Data safety, chứng chỉ TLS nếu có backend và nghiên cứu thêm Tín nhiệm mạng khi app public tại Việt Nam.

Về thuật toán, nhóm chọn rule-based và heuristic cho MVP vì giải thích được, dễ kiểm thử và không cần dataset lớn ngay từ đầu. Các module chính gồm URL scanner, text analyzer, phone/bank checker và QR scanner, tất cả dùng chung rule engine và evidence.

Về mobile, nhóm không thiết kế app vượt quyền. Thay vì đọc SMS, notification hoặc clipboard ngầm, app dùng các luồng user chủ động như share target, QR scanner, manual input và clipboard foreground. Cách này phù hợp privacy, chính sách Google Play/App Store và vẫn đủ để demo giá trị bảo vệ người dùng.

Về backend, API đã có phạm vi cho auth, scan, history, report và admin. Response được thiết kế để hỗ trợ quyết định của user, gồm điểm rủi ro, phân tích, evidence và khuyến nghị.

Sang Week3, nhóm em có thể bắt đầu thiết kế chi tiết kiến trúc hệ thống, database schema và prototype các luồng scan ưu tiên.

**Ý cần nhấn:** MVP không phải phiên bản thiếu tham vọng; MVP là phiên bản đủ an toàn, đủ giải thích và đủ khả thi để triển khai trước.

**Câu kết:** Em xin hết phần trình bày Week2 và sẵn sàng trả lời câu hỏi của thầy/cô.

