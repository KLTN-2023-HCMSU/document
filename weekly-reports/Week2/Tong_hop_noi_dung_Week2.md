# Tổng hợp nội dung Week2 - Anti-Scam Platform

Ngày tổng hợp: 2026-07-22

Tài liệu này gom nội dung từ `Week2/01_Chung_Chi` và `Week2/02_Phan_Tich_Nghiep_Vu`. Phần chứng chỉ được tổng hợp theo nhóm, còn phần `Week2/02_Phan_Tich_Nghiep_Vu` được đưa vào chi tiết theo từng file nguồn để tránh mất nội dung khi dùng cho báo cáo tuần và chuẩn bị slide trình bày.

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

Nguồn: `Week2/02_Phan_Tich_Nghiep_Vu/Phan_tich_thuat_toan_va_ky_thuat.md`

### Phân tích Thuật toán và Kỹ thuật cho Nền tảng Anti-Scam

**Ngày cập nhật:** 21/07/2026  
**Mục đích:** Đối chiếu các thuật toán được sử dụng trong các hệ thống phát hiện lừa đảo hiện đại với yêu cầu của đề tài khóa luận

---

#### 1. Tổng quan về các hướng tiếp cận

Dựa trên khảo sát các nghiên cứu gần đây (2024-2026) và các hệ thống thực tế, có **3 hướng chính** để phát hiện lừa đảo trực tuyến:

| Hướng tiếp cận | Ưu điểm | Nhược điểm | Phù hợp với đề tài |
|---|---|---|---|
| **Rule-based (Dựa trên luật)** | - Giải thích được rõ ràng<br>- Triển khai nhanh<br>- Không cần dữ liệu lớn<br>- Dễ kiểm thử | - Cứng nhắc<br>- Cần cập nhật thủ công<br>- Dễ bị kẻ tấn công học pattern | ⭐⭐⭐⭐⭐<br>Rất phù hợp cho MVP |
| **Machine Learning** | - Tự học pattern<br>- Phát hiện mẫu mới<br>- Độ chính xác cao (90-98%) | - Cần dataset lớn<br>- "Black box"<br>- Chi phí training<br>- Cần chuyên gia ML | ⭐⭐⭐<br>Hướng mở rộng |
| **Hybrid (Kết hợp)** | - Cân bằng ưu nhược điểm<br>- Rule xử lý nhanh, ML xử lý phức tạp<br>- Giải thích được | - Phức tạp hơn<br>- Cần duy trì 2 hệ thống | ⭐⭐⭐⭐<br>Mục tiêu dài hạn |

**Khuyến nghị cho đề tài:** Bắt đầu với **Rule-based Engine** + **Heuristic Analysis**, sau đó mở rộng sang **ML** trong phần "Hướng phát triển".

---

#### 2. Module 1: URL/Link Scanner

##### 2.1. Các thuật toán nghiên cứu hiện đại

Theo nghiên cứu năm 2024-2026, các thuật toán ML phổ biến cho phát hiện phishing URL:


| Thuật toán | Độ chính xác | Nguồn nghiên cứu | Ưu điểm | Nhược điểm |
|---|---|---|---|---|
| **Random Forest** | 98% | ResearchGate 2024 | Chống overfitting tốt, xử lý nhiều feature | Cần nhiều cây → chậm |
| **XGBoost** | 90.4% | IJSRA 2026 | Nhanh, hiệu quả, xử lý missing values | Phức tạp, khó tune |
| **MLP (Neural Network)** | 90.3% | IJSRA 2026 | Học pattern phi tuyến | Cần dataset lớn, lâu train |
| **1D CNN** | 95%+ | MDPI 2024 | Trích xuất pattern tự động từ URL | Cần GPU, phức tạp |
| **Naive Bayes** | 85-92% | Nhiều nguồn | Đơn giản, nhanh, ít tài nguyên | Giả định độc lập yếu |

**41 features phổ biến** được sử dụng (theo nghiên cứu Frontiers 2024):
- Độ dài URL, số subdomain, số dấu gạch ngang
- Có HTTPS hay không, tuổi domain
- Có ký tự lạ (@, //, ...), IP thay vì domain
- Entropy của URL, số chữ số, số ký tự đặc biệt
- Độ giống với brand nổi tiếng (typosquatting)

##### 2.2. Thuật toán đề xuất cho đề tài (MVP)

**Heuristic + Rule-based Analysis** (không cần ML):

```
Bước 1: Chuẩn hóa URL
├── Loại bỏ whitespace, lowercase
├── Decode URL encoding
├── Expand URL shortener (bit.ly, tinyurl) nếu có
└── Tách domain, path, query parameters

Bước 2: Phân tích đặc trưng (Feature Extraction)
├── URL length > 75 chars → +10 điểm
├── Số subdomain > 3 → +15 điểm
├── Có IP address thay vì domain → +25 điểm
├── Không có HTTPS → +20 điểm
├── Có @ hoặc // trong URL → +15 điểm
└── Domain age < 30 ngày → +20 điểm (nếu có API)

Bước 3: Kiểm tra Blacklist/Whitelist
├── Domain trong blacklist → +50 điểm (HIGH RISK)
├── Domain trong whitelist → -30 điểm (SAFE)
└── Kiểm tra redirect chain → mỗi redirect +10 điểm


Bước 4: Domain Similarity (Typosquatting Detection)
├── Thuật toán: Levenshtein Distance hoặc Jaro-Winkler
├── So sánh với danh sách 100 brand phổ biến tại VN
├── Ví dụ: "v1etcombank.com" vs "vietcombank.com.vn"
└── Khoảng cách <= 2 → +30 điểm

Bước 5: Tính điểm tổng và phân loại
├── 0-29: AN TOÀN (GREEN)
├── 30-59: CẨN TRỌNG (YELLOW)
└── 60-100: NGUY HIỂM (RED)
```

**Kỹ thuật triển khai Java:**
- **Apache Commons Validator** → validate URL format
- **OkHttp / HttpClient** → kiểm tra SSL, redirect, response
- **Jsoup** → parse HTML nếu cần
- **Apache Commons Text** → Levenshtein Distance
- **Caffeine Cache** → cache kết quả domain đã kiểm tra

---

#### 3. Module 2: Phân tích Form/HTML

##### 3.1. Heuristic Analysis cho biểu mẫu nghi ngờ

```
Bước 1: Parse HTML bằng Jsoup
└── Trích xuất tất cả <form> và <input>

Bước 2: Phân tích các trường nhạy cảm
├── input type="password" → +15 điểm
├── name/id chứa "otp", "pin", "cvv" → +20 điểm
├── name/id chứa "card", "cccd", "cmnd" → +25 điểm
├── name/id chứa "bank", "account" → +20 điểm
└── Tổng số trường nhạy cảm > 3 → +15 điểm

Bước 3: Kiểm tra hành vi form
├── Form action gửi về domain khác → +25 điểm
├── Form không có HTTPS trong action → +20 điểm
├── Form có hidden field chứa URL lạ → +15 điểm
└── Trang có nhiều hơn 2 form yêu cầu password → +20 điểm

Bước 4: Phát hiện từ khóa dụ dỗ trong nội dung
├── "xác minh ngay", "tài khoản sẽ bị khóa" → +15 điểm
├── "nhận thưởng", "trúng giải", "miễn phí" → +10 điểm
└── "khẩn cấp", "trong vòng 24h" → +10 điểm
```

**Kỹ thuật:**
- Jsoup: `Document.select("form"), Element.attr("action")`
- Regex để tìm keyword trong text content
- XPath selector cho phân tích phức tạp

---

#### 4. Module 3: Phân tích Tin nhắn/Giao dịch

##### 4.1. Các thuật toán NLP hiện đại (2024-2026)


| Phương pháp | Độ chính xác | Đặc điểm | Phù hợp đề tài |
|---|---|---|---|
| **TF-IDF + Naive Bayes** | 96.2% | Đơn giản, nhanh, ít tài nguyên | ⭐⭐⭐⭐⭐ MVP |
| **BERT/ALBERT** | 98%+ | Context-aware, phát hiện ngữ nghĩa | ⭐⭐ Mở rộng |
| **LLM (GPT/Claude)** | 97-100% | Giải thích được, zero-shot | ⭐⭐ Chi phí cao |
| **Rule + Keyword Matching** | 85-90% | Rất nhanh, giải thích rõ | ⭐⭐⭐⭐⭐ MVP |

##### 4.2. Thuật toán đề xuất cho đề tài (MVP)

**Keyword-based + Pattern Extraction + Rule Scoring:**

```
Bước 1: Tiền xử lý văn bản tiếng Việt
├── Lowercase, loại bỏ dấu (optional)
├── Loại bỏ emoji, ký tự đặc biệt
├── Tách câu, tokenize (không cần NLP phức tạp)
└── Chuẩn hóa số điện thoại, số tiền

Bước 2: Trích xuất thực thể (Entity Extraction - Regex)
├── URL: regex pattern http/https
├── Số điện thoại: regex VN (0xx-xxx-xxxx, +84)
├── Số tài khoản: 8-16 chữ số liên tiếp
├── Số tiền: "xxx.xxx đồng", "xxx,xxx VND", "xxxK"
├── Mã OTP: 4-6 chữ số trong context "mã", "OTP"
└── Thời hạn: "trong X phút", "trước DD/MM"

Bước 3: Phát hiện từ khóa nguy hiểm (Keyword Dictionary)

NHÓM 1: Khẩn cấp/Áp lực (mỗi từ +5 điểm)
├── "gấp", "khẩn cấp", "ngay lập tức"
├── "trong vòng", "trước khi", "hết hạn"
├── "bị khóa", "sẽ khóa", "tạm ngưng"
└── "mất quyền", "không thể truy cập"

NHÓM 2: Giả mạo cơ quan (mỗi từ +10 điểm)
├── "ngân hàng", "vietcombank", "techcombank"
├── "công an", "bộ công an", "cảnh sát"
├── "shipper", "giao hàng", "bưu điện"
├── "lazada", "shopee", "tiki", "zalo", "facebook"
└── "thuế", "bảo hiểm xã hội"

NHÓM 3: Yêu cầu thông tin nhạy cảm (+15 điểm)
├── "mật khẩu", "password", "pin"
├── "otp", "mã xác thực", "mã bảo mật"
├── "cccd", "cmnd", "căn cước"
├── "số thẻ", "cvv", "cvc"
└── "số tài khoản", "stk"

NHÓM 4: Yêu cầu chuyển tiền (+20 điểm)
├── "chuyển khoản", "chuyển tiền", "nạp tiền"
├── "đặt cọc", "cọc trước", "phí"
├── "thanh toán ngay", "trả trước"
└── "hoàn tiền", "hoàn lại", "bồi thường"


NHÓM 5: Quá tốt để tin (+10 điểm)
├── "trúng giải", "trúng thưởng", "may mắn"
├── "miễn phí", "free", "không mất phí"
├── "việc nhẹ lương cao", "thu nhập khủng"
└── "làm tại nhà", "không cần kinh nghiệm"

Bước 4: Phân tích Pattern kết hợp (Composite Rules)

PATTERN 1: SMS Giả mạng ngân hàng
├── Có từ "ngân hàng" + "tài khoản" + "OTP"
└── → +30 điểm

PATTERN 2: Lừa đảo tuyển dụng
├── Có từ "tuyển dụng" + "phí" + "chuyển khoản"
└── → +35 điểm

PATTERN 3: Giả mạo shipper
├── Có từ "shipper" + "đơn hàng" + "phí ship" + URL/SĐT
└── → +30 điểm

PATTERN 4: Hoàn tiền giả
├── Có từ "hoàn tiền" + "nhập mã" hoặc "OTP"
└── → +25 điểm

PATTERN 5: Khẩn cấp + Thông tin nhạy cảm
├── Có từ khẩn cấp + từ yêu cầu info nhạy cảm
└── → +25 điểm

Bước 5: Kiểm tra Cross-reference
├── URL trong tin nhắn → gọi Module 1 (URL Scanner)
├── SĐT trong tin nhắn → gọi Module 4 (Phone Checker)
├── STK trong tin nhắn → gọi Module 4 (Bank Account Checker)
└── Cộng điểm từ các module phụ

Bước 6: Tính điểm và giải thích
├── Tổng điểm < 30 → AN TOÀN
├── 30-59 → CẨN TRỌNG với lý do cụ thể
└── >= 60 → NGUY HIỂM với khuyến nghị
```

**Kỹ thuật Java:**
- **Regex Pattern** cho entity extraction
- **HashMap<String, Integer>** cho keyword dictionary
- **Custom Rule Engine** để áp dụng composite patterns
- Có thể dùng **Apache Lucene** cho text analysis nâng cao

**Ví dụ minh họa:**

```
INPUT: "THÔNG BÁO: Tài khoản ngân hàng của quý khách sắp bị khóa. 
Vui lòng xác minh ngay tại link: http://vietc0mbank-verify.com 
hoặc gọi 0912345678 để được hỗ trợ."

PHÂN TÍCH:
✓ URL phát hiện: http://vietc0mbank-verify.com
  └── Module 1 trả về: 75 điểm (giả mạo Vietcombank)
✓ SĐT phát hiện: 0912345678
  └── Module 4 trả về: 25 điểm (đã có 3 báo cáo)
✓ Keywords:
  - "ngân hàng" (+10)
  - "tài khoản" (+0, từ trung tính)
  - "bị khóa" (+5)
  - "xác minh ngay" (+5)
✓ Pattern match: SMS giả mạo ngân hàng (+30)

TỔNG ĐIỂM: 75 + 25 + 20 + 30 = 150 → Cắt về 100
KẾT QUẢ: NGUY HIỂM (RED)


LỜI GIẢI THÍCH:
"Tin nhắn này có dấu hiệu giả mạo ngân hàng Vietcombank:
1. Domain trong link có ký tự số '0' thay cho chữ 'o' (v1etc0mbank)
2. Số điện thoại 0912345678 đã có 3 báo cáo lừa đảo
3. Nội dung tạo áp lực 'sắp bị khóa' và yêu cầu 'xác minh ngay'
4. Ngân hàng thật không bao giờ yêu cầu xác minh qua link lạ

KHUYẾN NGHỊ:
❌ KHÔNG bấm vào link
❌ KHÔNG gọi số điện thoại trong tin nhắn
❌ KHÔNG cung cấp OTP, mật khẩu
✅ Liên hệ ngân hàng qua số hotline chính thức
✅ Gửi báo cáo lừa đảo cho hệ thống"
```

---

#### 5. Module 4: Phone Number & Bank Account Checker

##### 5.1. Thuật toán đơn giản nhưng hiệu quả

**Blacklist Lookup + Community Score:**

```
Bước 1: Chuẩn hóa input
├── Phone: 0912345678 → +84912345678 (canonical form)
└── Bank Account: loại bỏ space, dash → 123456789012

Bước 2: Hash để bảo vệ privacy (optional)
├── SHA-256(phone_number) → lookup bằng hash
└── Không lưu số thật vào log

Bước 3: Tra cứu Database
├── Check blacklist: entity_type='PHONE', value='hash'
├── Đếm số báo cáo: COUNT(*) từ ScamReport
├── Tính thời gian báo cáo gần nhất
└── Lấy điểm uy tín người báo cáo (nếu có)

Bước 4: Scoring
├── Trong blacklist được verify → 100 điểm
├── Có >= 5 báo cáo trong 30 ngày → 80 điểm
├── Có 2-4 báo cáo → 50 điểm
├── Có 1 báo cáo nhưng chưa verify → 30 điểm
├── Không có báo cáo → 0 điểm
└── Trong whitelist (tổ chức uy tín) → -50 điểm

Bước 5: Time-decay (giảm trọng số theo thời gian)
├── Báo cáo < 7 ngày → 100% trọng số
├── Báo cáo 7-30 ngày → 80% trọng số
├── Báo cáo 30-90 ngày → 50% trọng số
└── Báo cáo > 90 ngày → 30% trọng số
```

**Kỹ thuật:**
- PostgreSQL với index trên hash column
- Redis cache cho số được tra cứu nhiều
- Time-series aggregate cho trending analysis

---

#### 6. Module 5: QR Code Scanner

##### 6.1. Phân tích nội dung QR

```
Bước 1: Decode QR (ZXing library)
└── Trích xuất chuỗi raw data

Bước 2: Phân loại nội dung
├── URL → gọi Module 1 (URL Scanner)
├── VietQR / Banking format → parse thông tin chuyển khoản
├── Plain text → gọi Module 3 (Text Analyzer)
└── Unrecognized format → cảnh báo chung

Bước 3: Đối với VietQR Payment
├── Parse: bank_id, account_number, amount, content
├── Kiểm tra account_number → gọi Module 4
├── Phát hiện keyword lừa đảo trong content
└── Cảnh báo nếu amount > ngưỡng (VD: 5 triệu)


Bước 4: Tính điểm tổng hợp
├── Điểm từ account checker
├── Điểm từ content analysis
└── Warning nếu QR từ nguồn không tin cậy
```

**Library Java:**
- **ZXing (Zebra Crossing)** - decode QR code
- Parse VietQR theo spec: `https://www.vietqr.io/`

---

#### 7. Rule Engine Architecture

##### 7.1. Kiến trúc đề xuất

```
┌─────────────────────────────────────────┐
│         Rule Engine Core                │
├─────────────────────────────────────────┤
│  1. Rule Repository (DB + Cache)        │
│     ├── RiskRule table                  │
│     ├── RuleCondition                   │
│     └── RuleAction                      │
├─────────────────────────────────────────┤
│  2. Rule Evaluator                      │
│     ├── Condition Matcher               │
│     ├── Score Calculator                │
│     └── Evidence Collector              │
├─────────────────────────────────────────┤
│  3. Rule Composer                       │
│     ├── AND / OR / NOT logic            │
│     ├── Weight & Priority               │
│     └── Threshold & Level Mapping       │
└─────────────────────────────────────────┘
```

##### 7.2. Cấu trúc Rule trong Database

```sql
-- Bảng RiskRule
id | code               | name                      | weight | enabled
---|-------------------|---------------------------|--------|--------
1  | URL_NO_HTTPS      | URL không dùng HTTPS      | 20     | true
2  | URL_IP_ADDRESS    | URL dùng IP thay domain   | 25     | true
3  | TEXT_HAS_OTP      | Tin nhắn yêu cầu OTP      | 15     | true
4  | PATTERN_BANK_SCAM | Pattern giả mạo ngân hàng | 30     | true

-- Bảng RuleCondition (cho composite rule)
rule_id | condition_type | field_name    | operator | value
--------|---------------|---------------|----------|-------
4       | CONTAINS      | text          | REGEX    | ngân hàng
4       | AND           | text          | REGEX    | OTP|mã xác thực
4       | AND           | has_url       | EQUALS   | true
```

##### 7.3. Thuật toán áp dụng Rule

```java
public RiskScore evaluateRules(ScanInput input) {
    List<Rule> activeRules = ruleRepository.findByEnabled(true);
    int totalScore = 0;
    List<Evidence> evidences = new ArrayList<>();
    
    for (Rule rule : activeRules) {
        if (rule.matches(input)) {
            totalScore += rule.getWeight();
            evidences.add(new Evidence(
                rule.getCode(),
                rule.getName(),
                rule.getWeight(),
                rule.getExplanation()
            ));
        }
    }
    
    // Áp dụng tối đa 100 điểm
    totalScore = Math.min(totalScore, 100);
    
    // Phân loại level
    RiskLevel level = classifyLevel(totalScore);
    
    return new RiskScore(totalScore, level, evidences);
}

private RiskLevel classifyLevel(int score) {
    if (score < 30) return RiskLevel.SAFE;
    if (score < 60) return RiskLevel.CAUTION;
    return RiskLevel.DANGER;
}
```


---

#### 8. Machine Learning - Hướng mở rộng

##### 8.1. Khi nào nên chuyển sang ML?

Chuyển sang ML khi:
- ✅ Đã có dataset >= 10,000 mẫu đã label (phishing/legitimate)
- ✅ Rule-based đạt baseline nhưng có nhiều false positive
- ✅ Team có kỹ năng ML hoặc ngân sách thuê chuyên gia
- ✅ Cần phát hiện các pattern mới, chưa biết trước

##### 8.2. Roadmap ML cho đề tài

**Phase 1: Tập dataset**
```
Nguồn dữ liệu:
├── PhishTank API (phishing URLs)
├── Alexa Top 1M (legitimate URLs)
├── Tín Nhiệm Mạng (VN scam websites)
├── User reports từ hệ thống (sau 3-6 tháng vận hành)
└── Augmentation: tạo biến thể typosquatting
```

**Phase 2: Feature Engineering**
```
41 features cơ bản:
├── URL-based: length, depth, subdomain_count, has_ip, has_@, 
│   entropy, digit_ratio, special_char_count, tld_type
├── Domain-based: age, registrar, country, alexa_rank, 
│   has_ssl, ssl_age, whois_privacy
├── Content-based: title_similarity, form_count, 
│   external_link_ratio, iframe_count
└── Similarity-based: levenshtein_distance_to_brands
```

**Phase 3: Chọn thuật toán**
```
Đề xuất: Random Forest hoặc XGBoost
├── Lý do: 
│   - Accuracy cao (95-98%)
│   - Feature importance → giải thích được
│   - Không cần chuẩn hóa dữ liệu nhiều
│   - Robust với missing values
└── Alternative: Gradient Boosting, LightGBM
```

**Phase 4: Training & Evaluation**
```
Split: 70% train, 15% validation, 15% test
Metrics cần đo:
├── Accuracy
├── Precision (quan trọng - tránh false positive)
├── Recall (quan trọng - phát hiện được scam)
├── F1-Score
└── AUC-ROC

Target KPI:
├── Precision >= 95% (chỉ 5% cảnh báo nhầm)
├── Recall >= 90% (bắt được 90% scam thật)
└── Inference time < 100ms
```

##### 8.3. Hybrid Approach (Khuyến nghị cho dài hạn)

```
┌────────────────────────────────────────┐
│         Input (URL/Text/Phone)         │
└──────────────┬─────────────────────────┘
               │
               ▼
     ┌─────────────────────┐
     │  Fast Rule Engine   │  <-- Xử lý 80% case đơn giản
     │  (< 10ms)           │      trong < 10ms
     └────┬───────────┬────┘
          │ PASS      │ UNCERTAIN
          │           │
          ▼           ▼
      ┌──────┐   ┌──────────────┐
      │ Safe │   │  ML Classifier│  <-- Xử lý 20% case phức tạp
      └──────┘   │  (50-100ms)  │      cần phân tích sâu
                 └───────┬───────┘
                         │
                         ▼
                  ┌─────────────┐
                  │ Final Score │
                  └─────────────┘
```

**Logic:**
1. Rule engine xử lý trước (blacklist, whitelist, rule đơn giản)
2. Nếu điểm < 20 (rõ ràng safe) → trả về ngay
3. Nếu điểm > 70 (rõ ràng danger) → trả về ngay
4. Nếu 20-70 (uncertain) → gọi ML model phân tích sâu
5. Kết hợp score từ rule + ML prediction


---

#### 9. Performance Optimization

##### 9.1. Caching Strategy

```
┌─────────────────────────────────────────┐
│         Cache Layers                    │
├─────────────────────────────────────────┤
│ L1: Caffeine (In-memory)                │
│     ├── Domain reputation (TTL: 1h)     │
│     ├── Phone/Account lookup (TTL: 15m) │
│     └── Recent scan results (TTL: 5m)   │
├─────────────────────────────────────────┤
│ L2: Redis (Distributed)                 │
│     ├── Blacklist/Whitelist (TTL: 24h)  │
│     ├── ML model predictions (TTL: 1h)  │
│     └── Aggregated statistics           │
└─────────────────────────────────────────┘
```

##### 9.2. Database Optimization

```sql
-- Index quan trọng
CREATE INDEX idx_risk_entity_type_value 
    ON risk_entity(entity_type, value_hash);

CREATE INDEX idx_risk_entity_status 
    ON risk_entity(status, updated_at) 
    WHERE status = 'VERIFIED';

CREATE INDEX idx_scan_request_user_created 
    ON scan_request(user_id, created_at DESC);

-- Partition bảng log theo tháng
CREATE TABLE audit_log_2026_07 PARTITION OF audit_log
    FOR VALUES FROM ('2026-07-01') TO ('2026-08-01');
```

##### 9.3. Async Processing

```java
@Async
public CompletableFuture<UrlAnalysis> analyzeUrl(String url) {
    // Phân tích chạy background, không block request
}

// Combine multiple checks
CompletableFuture<DomainCheck> domainCheck = ...;
CompletableFuture<SslCheck> sslCheck = ...;
CompletableFuture<ContentCheck> contentCheck = ...;

CompletableFuture.allOf(domainCheck, sslCheck, contentCheck)
    .thenApply(v -> combineResults(...));
```

---

#### 10. So sánh với các hệ thống thực tế

| Hệ thống | Thuật toán chính | Độ chính xác | Phù hợp đề tài |
|---|---|---|---|
| **Google Safe Browsing** | Blacklist + Hash lookup + ML | 99%+ | Quá phức tạp, cần hạ tầng lớn |
| **PhishTank** | Community report + Verify | 92-95% | ✅ Học mô hình cộng đồng |
| **VirusTotal** | Multi-engine aggregation | 95%+ | Phức tạp, nhiều chi phí |
| **ScamAdviser** | Trust score (40+ signals) | 90%+ | ✅ Học cách tính trust score |
| **nTrust VN** | Blacklist + Manual review | ~90% | ✅ Phù hợp bối cảnh VN |
| **Bitdefender Scamio** | LLM-based | 97%+ | Chi phí cao, cần API |
| **TF-IDF + Naive Bayes** | Classic ML | 96% SMS | ✅ Đơn giản, hiệu quả |

**Kết luận cho đề tài:**
- **MVP (Khóa luận):** Rule-based + Heuristic + Blacklist/Whitelist
- **Nâng cao (Demo thêm):** + TF-IDF + Naive Bayes cho text
- **Tương lai:** + Random Forest/XGBoost cho URL + Hybrid


---

#### 11. Implementation Checklist

##### 11.1. Phase 1: Rule Engine Core (Tuần 1-2)
- [ ] Thiết kế schema cho RiskRule, RuleCondition
- [ ] Implement Rule Evaluator với weight scoring
- [ ] Viết unit test cho từng rule
- [ ] Tạo 20-30 rule cơ bản cho URL/Text/Phone

##### 11.2. Phase 2: URL Scanner (Tuần 3-4)
- [ ] URL normalization & validation
- [ ] HTTPS/SSL checker
- [ ] Redirect chain analyzer
- [ ] Domain similarity (Levenshtein)
- [ ] Blacklist/Whitelist lookup
- [ ] Integration test với real URLs

##### 11.3. Phase 3: Text Analyzer (Tuần 5-6)
- [ ] Regex patterns cho entity extraction
- [ ] Keyword dictionary (100+ từ tiếng Việt)
- [ ] Composite pattern matcher
- [ ] Cross-reference với URL/Phone checker
- [ ] Test với 50 mẫu tin nhắn thật

##### 11.4. Phase 4: Phone/Account Checker (Tuần 7)
- [ ] Normalization & hashing
- [ ] Database lookup với cache
- [ ] Time-decay scoring
- [ ] Admin CRUD cho blacklist/whitelist

##### 11.5. Phase 5: QR Scanner (Tuần 8)
- [ ] ZXing integration
- [ ] VietQR parser
- [ ] Routing đến module phù hợp

##### 11.6. Phase 6: ML Prototype (Optional - Tuần 9-10)
- [ ] Collect dataset (PhishTank + Alexa)
- [ ] Feature extraction (41 features)
- [ ] Train Random Forest model
- [ ] Export model sang PMML/ONNX
- [ ] Integrate vào Java backend
- [ ] A/B test: Rule vs ML vs Hybrid

---

#### 12. Datasets & Resources

##### 12.1. Public Datasets cho Training/Testing

| Dataset | Loại | Số lượng | Link |
|---|---|---|---|
| PhishTank | Phishing URLs | ~15K active | https://phishtank.org/developer_info.php |
| Alexa Top 1M | Legitimate URLs | 1M | https://www.alexa.com/topsites |
| UNB Phishing Dataset | URLs + Features | 11K+ | https://www.unb.ca/cic/datasets/ |
| SMS Spam Collection | SMS spam/ham | 5.5K | UCI Machine Learning Repo |
| Vietnamese Scam Messages | VN SMS scam | Tự thu thập | Community report |

##### 12.2. Libraries & Tools

**Java:**
- Apache Commons Validator, Apache Commons Text
- OkHttp, Jsoup
- ZXing (QR)
- Caffeine Cache
- Spring Boot, Spring Data JPA

**Python (cho ML prototyping):**
- scikit-learn, pandas, numpy
- XGBoost, LightGBM
- NLTK, spaCy (NLP)

**Infrastructure:**
- PostgreSQL, Redis
- Docker, Nginx
- Prometheus + Grafana (monitoring)

---

#### 13. Tài liệu tham khảo

##### 13.1. Nghiên cứu học thuật

| Tên | Năm | Điểm chính |
|---|---|---|
| Unveiling suspicious phishing attacks (Frontiers) | 2024 | 41 optimal URL features, OFVA algorithm |
| Phishing URL detection with neural networks (Nature) | 2024 | Neural network comparison for phishing |
| Detecting Phishing URLs with 1D CNN (MDPI) | 2024 | 200K dataset, 1D CNN approach |
| SMS Spam Detection with TF-IDF (arXiv) | 2026 | TF-IDF 96.2% accuracy với Naive Bayes |
| Rule-based ML for fraud detection (ResearchGate) | 2024 | Hybrid rule + ML model |

##### 13.2. Công cụ thực tế

- Google Safe Browsing API: https://developers.google.com/safe-browsing
- PhishTank API: https://phishtank.org/api_info.php
- VirusTotal API: https://developers.virustotal.com/
- OWASP Cheat Sheets: https://cheatsheetseries.owasp.org/

---

#### 14. Kết luận & Khuyến nghị

##### 14.1. Cho phạm vi khóa luận (6 tháng)

**Nên làm:**
✅ Rule-based Engine với 30-50 rules cấu hình được
✅ Heuristic Analysis cho URL + Form + Text
✅ Blacklist/Whitelist + Community Report
✅ Phone/Account Checker với time-decay
✅ Explanation + Recommendation rõ ràng
✅ Dashboard quản trị rule + report

**Không nên làm (quá phạm vi):**
❌ Deep Learning model từ scratch
❌ Real-time crawler ở quy mô lớn
❌ Distributed ML training pipeline
❌ Production-grade infrastructure với k8s

##### 14.2. Điểm mạnh của approach này

1. **Giải thích được:** Mỗi cảnh báo có lý do cụ thể
2. **Kiểm thử dễ:** Unit test từng rule độc lập
3. **Mở rộng tốt:** Thêm rule mới không ảnh hưởng cũ
4. **Phù hợp tài nguyên:** Không cần GPU, dataset lớn
5. **Học thuật vững:** Thể hiện hiểu biết về security + software engineering

##### 14.3. Con đường phát triển

```
[MVP] Rule Engine + Heuristic
   ↓
[v1.5] + TF-IDF/Naive Bayes cho text
   ↓
[v2.0] + Random Forest cho URL
   ↓
[v2.5] Hybrid: Rule (fast path) + ML (complex cases)
   ↓
[v3.0] LLM Integration cho conversation analysis
```

**Thời gian ước tính:**
- MVP Rule-based: 2-3 tháng
- + ML basic: thêm 1-1.5 tháng
- + Hybrid: thêm 0.5-1 tháng
- Testing + Docs: 1 tháng

---

**Tác giả:** Nhóm Anti-Scam Project  
**Cập nhật lần cuối:** 21/07/2026  
**Version:** 1.0

## 3. Mobile_Permissions_Analysis.md

Nguồn: `Week2/02_Phan_Tich_Nghiep_Vu/Mobile_Permissions_Analysis.md`

### Phân tích Quyền hạn Mobile App - Anti-Scam Platform

**Ngày:** 21/07/2026  
**Câu hỏi:** App mobile có thể vượt quyền không?  
**Trả lời ngắn gọn:** **KHÔNG. Tuyệt đối KHÔNG được vượt quyền.**

---

#### 1. Tại sao KHÔNG được vượt quyền?

##### 1.1. Lý do Pháp lý

```
┌─────────────────────────────────────────────────────┐
│  LUẬT PHÁP VIỆT NAM                                 │
├─────────────────────────────────────────────────────┤
│  Nghị định 13/2023/NĐ-CP                            │
│  về Bảo vệ dữ liệu cá nhân                          │
│                                                      │
│  Điều 8: Nguyên tắc xử lý dữ liệu cá nhân           │
│  ✓ Chỉ thu thập tối thiểu cần thiết                 │
│  ✓ Phải có sự đồng ý rõ ràng                        │
│  ✓ Minh bạch mục đích sử dụng                       │
│  ✓ Không sử dụng vượt mục đích đã thông báo         │
│                                                      │
│  Vi phạm → Phạt tiền + Đình chỉ ứng dụng            │
└─────────────────────────────────────────────────────┘
```

**Hậu quả vi phạm:**
- ⚠️ Phạt hành chính: 10-50 triệu VNĐ (cá nhân), 20-100 triệu (tổ chức)
- ⚠️ App bị gỡ khỏi Google Play / App Store
- ⚠️ Mất niềm tin người dùng → Chết sản phẩm
- ⚠️ Trách nhiệm hình sự nếu làm lộ dữ liệu nghiêm trọng

##### 1.2. Lý do Kỹ thuật (Hệ điều hành chặn)

**Android 13+ (API 33):**
```java
// CÁC QUYỀN BỊ CHẶN NGHIÊM NGẶT

❌ KHÔNG THỂ đọc toàn bộ SMS/Call log nếu không phải app mặc định
❌ KHÔNG THỂ truy cập file của app khác (Scoped Storage)
❌ KHÔNG THỂ chạy nền vô hạn (Background execution limits)
❌ KHÔNG THỂ đọc clipboard tự động (Privacy changes)
❌ KHÔNG THỂ truy cập location khi app ở background (nhiều hạn chế)
❌ KHÔNG THỂ bật Accessibility Service tự động

✅ CHỈ CÓ THỂ: Yêu cầu quyền rõ ràng → User phê duyệt → Sử dụng đúng mục đích
```

**iOS 17+:**
```swift
// APPLE NGHIÊM KHẮC HƠN ANDROID

❌ KHÔNG THỂ đọc SMS (Apple không cho phép app thứ 3)
❌ KHÔNG THỂ đọc Call log chi tiết
❌ KHÔNG THỂ chạy background task kéo dài
❌ KHÔNG THỂ truy cập clipboard ngầm
❌ App Store Review bị reject nếu xin quyền không hợp lý

✅ CHỈ CÓ THỂ: CallKit (nhận diện số), Share Extension (nhận data khi user share)
```

##### 1.3. Lý do Google Play Policy

**Google Play Console - User Data Policy:**
```
Nếu app khai báo:
├── "App không thu thập dữ liệu" 
│   → Nhưng thực tế có xin quyền SMS/Contact
│   → BỊ TỪ CHỐI ngay lập tức
│
├── "App thu thập SMS cho mục đích bảo mật"
│   → Phải chứng minh CHÍNH XÁC app làm gì với SMS
│   → Phải có Privacy Policy công khai
│   → Google sẽ kiểm tra thủ công (manual review)
│   → Nếu phát hiện gửi SMS lên server không mã hóa → BANNED
│
└── Penalty: Gỡ app + cảnh cáo + có thể ban account developer
```

**Sensitive Permissions yêu cầu giải trình:**
- READ_SMS, RECEIVE_SMS
- READ_CALL_LOG, PROCESS_OUTGOING_CALLS
- READ_CONTACTS
- CAMERA, RECORD_AUDIO
- ACCESS_FINE_LOCATION
- SYSTEM_ALERT_WINDOW
- BIND_ACCESSIBILITY_SERVICE (Cực kỳ nhạy cảm)

---

#### 2. Quyền nào ĐƯỢC PHÉP cho Anti-Scam App?

##### 2.1. Phân loại quyền theo mức độ

| Quyền | Mức độ | Cho phép? | Lý do |
|---|---|---|---|
| **INTERNET** | Normal | ✅ Có | Gọi API backend |
| **CAMERA** | Dangerous | ✅ Có | Quét QR code scam |
| **VIBRATE** | Normal | ✅ Có | Rung cảnh báo scam |
| **RECEIVE_BOOT_COMPLETED** | Normal | ✅ Có | Khởi động service bảo vệ |
| **POST_NOTIFICATIONS** (Android 13+) | Runtime | ✅ Có | Hiển thị cảnh báo |
| **READ_PHONE_STATE** | Dangerous | ⚠️ Có điều kiện | Chỉ để lấy số đang gọi đến (CallScreening) |
| **CALL_PHONE** | Dangerous | ❌ Không cần | App chỉ cảnh báo, không gọi |
| **READ_SMS** | Dangerous | ❌ KHÔNG | Google Play từ chối 99% trường hợp |
| **READ_CONTACTS** | Dangerous | ❌ KHÔNG | Không cần toàn bộ danh bạ |
| **READ_CALL_LOG** | Dangerous | ❌ KHÔNG | Không cần lịch sử cuộc gọi |
| **SYSTEM_ALERT_WINDOW** | Signature | ⚠️ Có điều kiện | Hiển thị overlay cảnh báo |
| **BIND_ACCESSIBILITY_SERVICE** | Signature | ❌ TUYỆT ĐỐI KHÔNG | Malware thường lạm dụng |

##### 2.2. Quyền ĐỀ XUẤT cho MVP (Khóa luận)

```xml
<!-- AndroidManifest.xml - CHỈ XIN QUYỀN CẦN THIẾT -->

<!-- NORMAL PERMISSIONS - Tự động được cấp -->
<uses-permission android:name="android.permission.INTERNET" />
<uses-permission android:name="android.permission.VIBRATE" />
<uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED" />

<!-- DANGEROUS PERMISSIONS - Phải xin runtime -->
<uses-permission android:name="android.permission.CAMERA" />
<!-- Cho QR Scanner -->

<uses-permission android:name="android.permission.POST_NOTIFICATIONS" />
<!-- Android 13+ -->

<!-- OPTIONAL - Chỉ nếu làm tính năng call screening -->
<uses-permission android:name="android.permission.READ_PHONE_STATE" />
<!-- CHỈ để lấy số gọi đến, KHÔNG đọc call log -->

<!-- TUYỆT ĐỐI KHÔNG XIN NHỮNG QUYỀN NÀY CHO MVP -->
<!-- <uses-permission android:name="android.permission.READ_SMS" /> ❌ -->
<!-- <uses-permission android:name="android.permission.READ_CONTACTS" /> ❌ -->
<!-- <uses-permission android:name="android.permission.READ_CALL_LOG" /> ❌ -->
<!-- <uses-permission android:name="android.permission.ACCESS_FINE_LOCATION" /> ❌ -->
```


---

#### 3. Các tính năng "chạy ngầm" ĐƯỢC PHÉP

##### 3.1. Tính năng 1: Call Screening (Cảnh báo cuộc gọi)

**Cách triển khai ĐÚNG (không vượt quyền):**

```kotlin
// Android CallScreeningService - Chính thức từ Android 10+
class ScamCallScreeningService : CallScreeningService() {
    
    override fun onScreenCall(callDetails: Call.Details) {
        // 1. Lấy số điện thoại đang gọi đến
        val phoneNumber = callDetails.handle.schemeSpecificPart
        
        // 2. Kiểm tra trong database local hoặc gọi API
        val riskScore = checkPhoneNumber(phoneNumber)
        
        // 3. Trả về response cho hệ thống
        val response = CallResponse.Builder()
            .setDisallowCall(riskScore > 80)  // Chặn nếu nguy hiểm
            .setRejectCall(riskScore > 80)
            .setSkipCallLog(false)
            .setSkipNotification(false)
            .build()
        
        respondToCall(callDetails, response)
        
        // 4. Hiển thị notification cảnh báo
        if (riskScore > 50) {
            showScamWarningNotification(phoneNumber, riskScore)
        }
    }
}
```

**Manifest:**
```xml
<service
    android:name=".ScamCallScreeningService"
    android:permission="android.permission.BIND_SCREENING_SERVICE"
    android:exported="true">
    <intent-filter>
        <action android:name="android.telecom.CallScreeningService" />
    </intent-filter>
</service>
```

**Ưu điểm:**
- ✅ Google Play CHO PHÉP
- ✅ Không cần quyền nguy hiểm
- ✅ User có thể tắt bất cứ lúc nào
- ✅ Hệ thống kiểm soát, app không can thiệp trực tiếp

**Giới hạn:**
- ⚠️ Chỉ biết số đang gọi đến, KHÔNG biết lịch sử cuộc gọi
- ⚠️ Không đọc được SMS
- ⚠️ Không can thiệp được trong cuộc gọi

##### 3.2. Tính năng 2: Share Target (Kiểm tra URL khi share)

**Cách triển khai ĐÚNG:**

```kotlin
// Activity nhận share intent
class ShareCheckActivity : AppCompatActivity() {
    
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        
        when (intent?.action) {
            Intent.ACTION_SEND -> {
                // Nhận URL được share
                val sharedUrl = intent.getStringExtra(Intent.EXTRA_TEXT)
                
                if (sharedUrl != null) {
                    // Hiển thị dialog loading
                    checkUrlSafety(sharedUrl)
                }
            }
        }
    }
}
```

**Manifest:**
```xml
<activity android:name=".ShareCheckActivity">
    <intent-filter>
        <action android:name="android.intent.action.SEND" />
        <category android:name="android.intent.category.DEFAULT" />
        <data android:mimeType="text/plain" />
    </intent-filter>
</activity>
```

**User workflow:**
1. User nhận link nghi ngờ trên Zalo/Messenger
2. User long-press → Share → Chọn "Anti-Scam Check"
3. App mở → Phân tích → Hiển thị kết quả ngay
4. User quyết định có mở link hay không

**Ưu điểm:**
- ✅ Hoàn toàn hợp pháp
- ✅ KHÔNG cần quyền đặc biệt
- ✅ User chủ động, không bị theo dõi
- ✅ Trải nghiệm tốt, không xâm phạm

##### 3.3. Tính năng 3: Clipboard Monitoring (Giới hạn)

**Android 12+ ĐÃ CHẶN clipboard tự động:**

```kotlin
// CÁCH CŨ (Android 11-) - KHÔNG DÙNG NỮA
❌ clipboardManager.addPrimaryClipChangedListener {
    // Đọc clipboard mỗi khi thay đổi → BỊ CHẶN Android 12+
}

// CÁCH MỚI (Android 12+) - Chỉ khi app ở foreground
✅ Manual check khi user bấm nút
button.setOnClickListener {
    val clipboard = clipboardManager.primaryClip
    val text = clipboard?.getItemAt(0)?.text
    if (text != null && isUrl(text)) {
        checkUrlSafety(text.toString())
    }
}
```

**Khuyến nghị:**
- ✅ Hiển thị button "Kiểm tra link từ Clipboard"
- ✅ User BẤM → App đọc clipboard → Phân tích
- ❌ KHÔNG tự động đọc clipboard ngầm

##### 3.4. Tính năng 4: Notification Listener (CỰC KỲ NHẠY CẢM)

**⚠️ CẢNH BÁO: Quyền này gần như KHÔNG được phê duyệt**

```kotlin
// NotificationListenerService có thể đọc MỌI thông báo
class ScamNotificationListener : NotificationListenerService() {
    override fun onNotificationPosted(sbn: StatusBarNotification) {
        val notification = sbn.notification
        val text = notification.extras.getString(Notification.EXTRA_TEXT)
        
        // Phát hiện link/số điện thoại trong notification
        // → Cảnh báo user
    }
}
```

**Vấn đề:**
- ❌ Đọc được notification của TẤT CẢ app (Gmail, Banking, Chat...)
- ❌ Google Play CỰC KỲ khó chấp nhận
- ❌ User thường từ chối quyền này
- ❌ Dễ bị coi là spyware

**Khuyến nghị cho đề tài:**
- 🔴 **KHÔNG NÊN** làm tính năng này cho MVP
- 📝 Chỉ đề cập như "hướng phát triển có rủi ro cao"
- 📝 Nếu làm, phải có giải trình CỰC KỲ rõ ràng

---

#### 4. Giải pháp thay thế "chạy ngầm" cho MVP

##### 4.1. Thao tác chủ động (KHUYẾN NGHỊ)

```
THAY VÌ: Tự động đọc SMS/notification
→ LÀM: User paste/share nội dung vào app

WORKFLOW:
1. User nhận SMS/tin nhắn nghi ngờ
2. User COPY nội dung
3. User MỞ app Anti-Scam
4. User PASTE hoặc BẤM "Kiểm tra từ Clipboard"
5. App phân tích và cảnh báo

Ưu điểm:
✅ Hoàn toàn hợp pháp
✅ KHÔNG cần quyền nguy hiểm
✅ User kiểm soát hoàn toàn
✅ Dễ demo cho giảng viên
✅ Dễ pass Google Play review
```

##### 4.2. Share Extension (KHUYẾN NGHỊ)

```
THAY VÌ: Tự động bắt URL
→ LÀM: User share URL vào app

WORKFLOW:
1. User thấy link nghi ngờ trên Zalo/Messenger
2. User long-press link → Share
3. User chọn "Anti-Scam Check"
4. App phân tích ngay
5. Hiện kết quả trong dialog

Ưu điểm:
✅ Trải nghiệm mượt mà
✅ Không cần quyền đặc biệt
✅ Phù hợp với hành vi người dùng thật
```

##### 4.3. QR Scanner (AN TOÀN NHẤT)

```
THAY VÌ: Đọc camera liên tục
→ LÀM: User mở app → Bấm "Quét QR"

WORKFLOW:
1. User thấy QR code nghi ngờ
2. User mở app Anti-Scam
3. User bấm nút "Quét QR"
4. User chỉ camera vào QR
5. App phân tích và cảnh báo

Quyền cần:
✅ CAMERA (runtime permission)
✅ Google Play chấp nhận 100%
```

##### 4.4. Manual Input (BACKUP)

```
THAY VÌ: Tự động bắt
→ LÀM: User nhập thủ công

WORKFLOW:
1. User mở app
2. Chọn tab: URL / Số điện thoại / Số TK
3. Nhập vào
4. Bấm "Kiểm tra"
5. Nhận kết quả

Ưu điểm:
✅ Đơn giản nhất
✅ Chắc chắn pass review
✅ Phù hợp demo học thuật
```

---

#### 5. So sánh các approach

| Tính năng | Quyền cần | Google Play | Trải nghiệm | Phù hợp MVP |
|---|---|---|---|---|
| **Thao tác chủ động** | Không | ✅ 100% pass | ⭐⭐⭐ OK | ⭐⭐⭐⭐⭐ Rất cao |
| **Share Extension** | Không | ✅ 100% pass | ⭐⭐⭐⭐ Tốt | ⭐⭐⭐⭐⭐ Rất cao |
| **QR Scanner** | Camera | ✅ 100% pass | ⭐⭐⭐⭐ Tốt | ⭐⭐⭐⭐⭐ Rất cao |
| **Call Screening** | Phone State | ✅ Có thể pass | ⭐⭐⭐⭐⭐ Rất tốt | ⭐⭐⭐⭐ Cao |
| **Clipboard Auto** | Không | ❌ Bị chặn OS | ⭐ Xấu | ❌ Không |
| **SMS Reader** | Read SMS | ❌ 99% reject | ⭐⭐⭐⭐⭐ Tuyệt vời | ❌ Không |
| **Notification Listener** | Notification | ❌ Khó pass | ⭐⭐⭐⭐⭐ Tuyệt vời | ❌ Không |
| **Accessibility** | Accessibility | ❌ Nguy hiểm | ⭐⭐⭐⭐⭐ Tuyệt vời | ❌❌ Tuyệt đối không |


---

#### 6. Kịch bản Demo cho Giảng viên

##### 6.1. Demo Scenario 1: Kiểm tra URL từ SMS

```
🎬 KỊCH BẢN DEMO

Bước 1: Chuẩn bị
├── SMS giả: "TÀI KHOẢN SẮP BỊ KHÓA. Xác minh tại: http://vietc0mbank.com"
└── Hiển thị trên điện thoại demo

Bước 2: User action
├── User đọc SMS → Nghi ngờ
├── User LONG-PRESS vào link
├── User chọn "Copy link"
└── User mở app Anti-Scam

Bước 3: App workflow
├── App hiển thị màn hình chính
├── Phát hiện clipboard có URL
├── Hiện dialog: "Phát hiện link trong clipboard. Kiểm tra ngay?"
├── User bấm "Kiểm tra"
└── App phân tích (hiện loading 2-3s)

Bước 4: Kết quả
├── Hiển thị: NGUY HIỂM (RED)
├── Điểm rủi ro: 85/100
├── Lý do:
│   • Domain giả mạo Vietcombank (typosquatting)
│   • Sử dụng HTTP thay vì HTTPS
│   • Domain không có lịch sử uy tín
├── Khuyến nghị:
│   • KHÔNG truy cập link này
│   • Xóa SMS
│   • Liên hệ Vietcombank qua hotline chính thức
└── Nút "Gửi báo cáo lừa đảo"

💡 Giải thích cho giảng viên:
"Em không xin quyền đọc SMS tự động vì:
1. Google Play sẽ từ chối
2. Vi phạm Nghị định 13/2023
3. User không tin tưởng app đọc SMS
→ Thay vào đó, em để user chủ động copy-paste
→ Vẫn đạt mục đích bảo vệ user nhưng hợp pháp"
```

##### 6.2. Demo Scenario 2: Quét QR giả mạo

```
🎬 KỊCH BẢN DEMO

Bước 1: Chuẩn bị
├── In QR code chuyển khoản giả
└── QR chứa: STK 123456789 - Ngân hàng VCB - Số tiền 5,000,000đ

Bước 2: User action
├── User mở app Anti-Scam
├── Chọn tab "Quét QR"
├── App yêu cầu quyền Camera (runtime)
├── User bấm "Cho phép"
└── User hướng camera vào QR

Bước 3: App workflow
├── ZXing library decode QR
├── Parse VietQR format
├── Trích xuất: bankCode, accountNumber, amount
├── Gọi API kiểm tra STK
└── Phân tích trong 1-2s

Bước 4: Kết quả
├── Hiển thị: CẨN TRỌNG (YELLOW)
├── Điểm rủi ro: 65/100
├── Thông tin:
│   • Số TK: 123456789
│   • Ngân hàng: Vietcombank
│   • Số tiền: 5.000.000đ
├── Cảnh báo:
│   • Số tài khoản này có 12 báo cáo lừa đảo
│   • 8 báo cáo liên quan "bán hàng không giao"
└── Khuyến nghị:
    • Xác minh lại người nhận qua điện thoại
    • Không quét QR từ nguồn không rõ ràng
    • Ưu tiên thanh toán khi nhận hàng

💡 Điểm mạnh cho giảng viên:
"QR scanning rất phổ biến ở VN, đặc biệt thanh toán online.
User có thể quét QR trước khi thanh toán để check.
Chỉ cần quyền Camera - Google Play chấp nhận 100%."
```

##### 6.3. Demo Scenario 3: Share link từ Zalo

```
🎬 KỊCH BẢN DEMO

Bước 1: Chuẩn bị
├── Mở Zalo (hoặc Messenger)
└── Tin nhắn có link: "Mua iPhone giá rẻ: http://iphon3-sale.com"

Bước 2: User action
├── User long-press vào link
├── Menu hiện ra
├── User chọn "Chia sẻ" (Share)
└── Chọn "Anti-Scam Check" trong danh sách

Bước 3: App workflow
├── App mở share dialog
├── Nhận URL từ intent
├── Tự động phân tích (không cần user bấm gì)
└── Hiện kết quả trong 2-3s

Bước 4: Kết quả
├── Dialog hiển thị full-screen
├── NGUY HIỂM (RED) - 90/100
├── Lý do:
│   • Domain mới tạo (< 7 ngày)
│   • Sử dụng số "3" thay chữ "e" (iphon3)
│   • Giả mạo thương hiệu iPhone
│   • Không có HTTPS
├── Bằng chứng bổ sung:
│   • 5 người dùng đã báo cáo trong 2 ngày
│   • Không tìm thấy thông tin công ty
└── Nút: [Đóng] [Gửi báo cáo]

💡 Highlight:
"Đây là trải nghiệm tốt nhất vì:
- User đang dùng app chat, thấy link nghi ngờ
- Chỉ cần share sang app em
- Không cần copy-paste hay chuyển app
- Kết quả hiện ngay, quyết định nhanh"
```

---

#### 7. Câu trả lời cho các câu hỏi thường gặp

##### Q1: "Tại sao không làm như app nTrust/Whoscall?"

**Trả lời:**
```
nTrust và Whoscall là các app của:
• Tổ chức/doanh nghiệp lớn có uy tín
• Đã được Google phê duyệt đặc biệt
• Có partnership với Google/Apple
• Có đội ngũ pháp lý riêng

Với app khóa luận của em:
• Là cá nhân/sinh viên
• Chưa có uy tín để xin quyền đặc biệt
• Google sẽ review nghiêm ngặt hơn
• Mục tiêu là DEMO học thuật, không phải sản phẩm thương mại

→ Em chọn approach an toàn, hợp pháp, vẫn đạt mục đích bảo vệ user
```

##### Q2: "User không chủ động thì làm sao bảo vệ?"

**Trả lời:**
```
Thực tế:
• 70% người dùng không tin app đọc SMS/notification
• Google/Apple ngày càng hạn chế quyền tự động
• Xu hướng là user kiểm soát data nhiều hơn

Giải pháp của em:
• Giáo dục user: "Nghi ngờ link → Share vào app → Kiểm tra"
• UX đơn giản: 2 bước (Share → Xem kết quả)
• Nhanh: < 3 giây có kết quả
• An toàn: User kiểm soát 100%

Tương lai (nếu sản phẩm hóa):
• Xin partnership với Google
• Xây dựng uy tín qua cộng đồng
• Lúc đó mới xin quyền nâng cao
```

##### Q3: "App chạy ngầm thì có vi phạm OWASP không?"

**Trả lời:**
```
OWASP MASVS có các yêu cầu:
✅ MSTG-STORAGE-1: Không lưu data nhạy cảm trong log
✅ MSTG-PLATFORM-1: Chỉ xin quyền cần thiết
✅ MSTG-PLATFORM-2: Input từ user phải validate
✅ MSTG-PRIVACY-1: Thu thập data tối thiểu
✅ MSTG-PRIVACY-2: Có privacy policy rõ ràng

App của em:
✅ KHÔNG đọc SMS/Contact → Không vi phạm privacy
✅ CHỈ xin Camera (cho QR), Phone State (cho call screening)
✅ Validate mọi input (URL, text, phone number)
✅ KHÔNG lưu SMS/notification content
✅ Chỉ gửi hash/metadata lên server (không phải full data)

→ Tuân thủ OWASP MASVS Level 1 (Standard)
→ Có thể đạt Level 2 nếu thêm certificate pinning
```

##### Q4: "iOS có khó khăn hơn Android không?"

**Trả lời:**
```
iOS NGHIÊM KHẮC HƠN ANDROID:

❌ iOS không cho app thứ 3 đọc SMS
❌ iOS không cho đọc Call log chi tiết
❌ iOS hạn chế background execution rất chặt
❌ App Store review nghiêm ngặt về quyền

Giải pháp cho iOS:
✅ Share Extension (Apple khuyến khích)
✅ CallKit Directory Extension (cho caller ID)
✅ Camera cho QR scanner
✅ Clipboard khi app ở foreground

→ Em ưu tiên Android cho MVP
→ iOS sẽ là v2.0 với scope giới hạn hơn
```

---

#### 8. Checklist tuân thủ cho đề tài

##### 8.1. Pháp lý
- [ ] Viết Privacy Policy rõ ràng (tiếng Việt + English)
- [ ] Khai báo chính xác quyền trong Google Play Console
- [ ] Giải thích TẠI SAO cần từng quyền
- [ ] Có cơ chế user xóa dữ liệu
- [ ] Tuân thủ Nghị định 13/2023

##### 8.2. Kỹ thuật
- [ ] Request runtime permissions đúng cách
- [ ] Explain permission trước khi xin
- [ ] Xử lý trường hợp user từ chối permission
- [ ] KHÔNG lưu data nhạy cảm trong SharedPreferences/log
- [ ] Mã hóa data trước khi gửi lên server
- [ ] Implement certificate pinning (nâng cao)

##### 8.3. UX/UI
- [ ] Màn hình onboarding giải thích app làm gì
- [ ] Dialog giải thích TẠI SAO cần quyền
- [ ] Cho phép user tắt tính năng "chạy ngầm"
- [ ] Settings để user quản lý quyền
- [ ] Hiển thị rõ data nào được thu thập

##### 8.4. Testing
- [ ] Test trên Android 13+ (permissions mới nhất)
- [ ] Test khi user TỪ CHỐI quyền
- [ ] Test khi user THU HỒI quyền giữa chừng
- [ ] Test background service bị kill
- [ ] Test với Play Protect bật

##### 8.5. Documentation
- [ ] Ghi rõ KHÔNG vượt quyền trong báo cáo
- [ ] Giải thích approach thay thế
- [ ] So sánh với các app thương mại
- [ ] Trích dẫn Nghị định 13/2023, OWASP MASVS
- [ ] Có phần "Limitations" thành thật

---

#### 9. Kết luận & Khuyến nghị

##### 9.1. TÓM TẮT

```
┌────────────────────────────────────────────────────┐
│  CÂU TRẢ LỜI CHÍNH THỨC                            │
├────────────────────────────────────────────────────┤
│                                                     │
│  ❌ App mobile TUYỆT ĐỐI KHÔNG được vượt quyền    │
│                                                     │
│  Lý do:                                            │
│  1. Vi phạm pháp luật VN (Nghị định 13/2023)      │
│  2. Bị Google Play/App Store từ chối              │
│  3. Hệ điều hành chặn kỹ thuật                    │
│  4. Mất niềm tin người dùng                       │
│  5. Vi phạm đạo đức nghề nghiệp                   │
│                                                     │
│  Thay vào đó:                                      │
│  ✅ Thao tác chủ động (copy-paste, manual input)  │
│  ✅ Share Extension                                │
│  ✅ QR Scanner                                     │
│  ✅ Call Screening API (chính thức)               │
│                                                     │
│  → Vẫn đạt mục đích nhưng HỢP PHÁP & AN TOÀN      │
└────────────────────────────────────────────────────┘
```

##### 9.2. KHUYẾN NGHỊ CHO ĐỀ TÀI

**Phạm vi MVP (3-4 tháng):**
1. ✅ URL Scanner (share/paste)
2. ✅ Text Analyzer (paste/input)
3. ✅ QR Scanner (camera)
4. ✅ Phone Checker (input)
5. ✅ Bank Account Checker (input)

**Phạm vi nâng cao (thêm 1-2 tháng):**
1. ✅ Call Screening Service
2. ✅ Share Extension cho nhiều app
3. ✅ Widget (quick check)

**KHÔNG NÊN LÀM (vượt phạm vi/rủi ro cao):**
1. ❌ SMS Auto Reader
2. ❌ Notification Listener
3. ❌ Clipboard Auto Monitor
4. ❌ Accessibility Service
5. ❌ Background Location Tracking

##### 9.3. Câu trả lời cho giảng viên

**Nếu giảng viên hỏi: "Tại sao không làm tự động như app thương mại?"**

> "Thưa thầy/cô, em có nghiên cứu kỹ các app như nTrust, Whoscall. 
> Các app đó được phép vì:
> 1. Là tổ chức lớn có partnership với Google
> 2. Có đội ngũ pháp lý và compliance team
> 3. Đã được phê duyệt đặc biệt
> 
> Với đề tài khóa luận, em tuân thủ nguyên tắc:
> • KHÔNG vi phạm pháp luật (Nghị định 13/2023)
> • KHÔNG vượt quyền hệ điều hành
> • Thiết kế theo OWASP MASVS
> • Giữ niềm tin người dùng
> 
> Em chọn approach 'user chủ động' vì:
> • Vẫn đạt mục đích bảo vệ user
> • Hợp pháp và an toàn 100%
> • Demo được cho khóa luận
> • Có thể mở rộng sau nếu sản phẩm hóa
> 
> Em tin rằng một app anti-scam vi phạm privacy 
> thì không khác gì scammer."

---

**Tài liệu tham khảo:**
- [Nghị định 13/2023/NĐ-CP](https://vanban.chinhphu.vn/?pageid=27160&docid=207759)
- [OWASP MASVS](https://mas.owasp.org/MASVS/)
- [Google Play User Data Policy](https://support.google.com/googleplay/android-developer/answer/10787469)
- [Android Background Execution Limits](https://developer.android.com/about/versions/oreo/background)
- [iOS Privacy Best Practices](https://developer.apple.com/documentation/uikit/protecting_the_user_s_privacy)

**Ngày cập nhật:** 21/07/2026  
**Version:** 1.0

## 4. API_Specification.md

Nguồn: `Week2/02_Phan_Tich_Nghiep_Vu/API_Specification.md`

### API Specification - Anti-Scam Platform

**Version:** 1.0  
**Date:** 21/07/2026  
**Base URL:** `https://api.antiscam.vn/v1`

---

#### 1. Authentication

##### 1.1. Đăng ký người dùng
```http
POST /auth/register
Content-Type: application/json

Request:
{
  "email": "user@example.com",
  "password": "SecurePass123!",
  "fullName": "Nguyễn Văn A",
  "phoneNumber": "+84912345678"  // optional
}

Response: 201 Created
{
  "success": true,
  "data": {
    "userId": "usr_abc123",
    "email": "user@example.com",
    "fullName": "Nguyễn Văn A",
    "role": "USER",
    "createdAt": "2026-07-21T10:30:00Z"
  },
  "message": "Đăng ký thành công. Vui lòng kiểm tra email để xác thực tài khoản."
}

Errors:
400 - Email đã tồn tại
400 - Mật khẩu không đủ mạnh
422 - Validation error
```

##### 1.2. Đăng nhập
```http
POST /auth/login
Content-Type: application/json

Request:
{
  "email": "user@example.com",
  "password": "SecurePass123!"
}

Response: 200 OK
{
  "success": true,
  "data": {
    "accessToken": "eyJhbGciOiJIUzI1NiIs...",
    "refreshToken": "eyJhbGciOiJIUzI1NiIs...",
    "expiresIn": 3600,
    "tokenType": "Bearer",
    "user": {
      "userId": "usr_abc123",
      "email": "user@example.com",
      "fullName": "Nguyễn Văn A",
      "role": "USER"
    }
  }
}

Errors:
401 - Email hoặc mật khẩu không đúng
403 - Tài khoản chưa được xác thực
403 - Tài khoản đã bị khóa
```

##### 1.3. Làm mới token
```http
POST /auth/refresh
Content-Type: application/json

Request:
{
  "refreshToken": "eyJhbGciOiJIUzI1NiIs..."
}

Response: 200 OK
{
  "success": true,
  "data": {
    "accessToken": "eyJhbGciOiJIUzI1NiIs...",
    "expiresIn": 3600
  }
}
```

---

#### 2. Scan APIs (Kiểm tra rủi ro)

##### 2.1. Quét URL/Link
```http
POST /scan/url
Authorization: Bearer {accessToken}
Content-Type: application/json

Request:
{
  "url": "https://vietc0mbank-verify.com/xacthuc",
  "context": "Nhận được qua SMS",  // optional
  "saveHistory": true              // optional, default: true
}

Response: 200 OK
{
  "success": true,
  "data": {
    "scanId": "scan_xyz789",
    "input": {
      "url": "https://vietc0mbank-verify.com/xacthuc",
      "normalizedUrl": "https://vietc0mbank-verify.com/xacthuc",
      "domain": "vietc0mbank-verify.com"
    },
    "result": {
      "riskScore": 85,
      "riskLevel": "DANGER",
      "levelColor": "RED",
      "confidence": 0.92
    },
    "analysis": {
      "urlFeatures": {
        "hasHttps": true,
        "hasIpAddress": false,
        "urlLength": 45,
        "subdomainCount": 0,
        "hasSpecialChars": true,
        "redirectCount": 0
      },
      "domainAnalysis": {
        "age": null,
        "registrar": null,
        "country": null,
        "inBlacklist": false,
        "inWhitelist": false,
        "similarToKnownBrand": {
          "match": true,
          "brand": "Vietcombank",
          "officialDomain": "vietcombank.com.vn",
          "similarity": 0.89,
          "technique": "Character substitution (0 → o)"
        }
      }
    },
    "evidences": [
      {
        "ruleCode": "DOMAIN_TYPOSQUATTING",
        "ruleName": "Domain giả mạo thương hiệu",
        "score": 30,
        "severity": "HIGH",
        "description": "Domain sử dụng ký tự '0' thay cho 'o' để giả mạo Vietcombank"
      },
      {
        "ruleCode": "DOMAIN_SUSPICIOUS_KEYWORD",
        "ruleName": "Từ khóa đáng ngờ trong domain",
        "score": 25,
        "severity": "MEDIUM",
        "description": "Domain chứa từ 'verify', 'xacthuc' - thường xuất hiện trong phishing"
      },
      {
        "ruleCode": "URL_NO_REPUTATION",
        "ruleName": "Domain không có lịch sử uy tín",
        "score": 15,
        "severity": "LOW",
        "description": "Không tìm thấy thông tin về domain này"
      }
    ],
    "recommendation": {
      "action": "BLOCK",
      "message": "KHÔNG truy cập link này",
      "reasons": [
        "Domain giả mạo ngân hàng Vietcombank",
        "Sử dụng kỹ thuật typosquatting (thay ký tự)",
        "Ngân hàng thật không bao giờ yêu cầu xác thực qua link lạ"
      ],
      "nextSteps": [
        "Xóa tin nhắn chứa link này",
        "Liên hệ Vietcombank qua số hotline chính thức: 1900 54 54 13",
        "Báo cáo lừa đảo cho hệ thống"
      ]
    },
    "scannedAt": "2026-07-21T10:35:22Z",
    "processingTime": 245  // milliseconds
  }
}

Errors:
400 - URL không hợp lệ
429 - Quá nhiều request (rate limit)
500 - Lỗi hệ thống
```

##### 2.2. Quét nội dung tin nhắn/giao dịch
```http
POST /scan/text
Authorization: Bearer {accessToken}
Content-Type: application/json

Request:
{
  "text": "THÔNG BÁO: Tài khoản của quý khách sắp bị khóa. Xác minh ngay tại http://vietc0mbank.com hoặc gọi 0912345678",
  "source": "SMS",  // SMS, ZALO, MESSENGER, EMAIL, etc.
  "saveHistory": true
}

Response: 200 OK
{
  "success": true,
  "data": {
    "scanId": "scan_text456",
    "input": {
      "textLength": 125,
      "source": "SMS"
    },
    "result": {
      "riskScore": 95,
      "riskLevel": "DANGER",
      "levelColor": "RED",
      "confidence": 0.97
    },
    "analysis": {
      "extractedEntities": {
        "urls": ["http://vietc0mbank.com"],
        "phoneNumbers": ["+84912345678"],
        "bankAccounts": [],
        "amounts": [],
        "otpCodes": []
      },
      "keywordMatches": [
        {
          "category": "URGENCY",
          "keywords": ["sắp bị khóa", "ngay"],
          "score": 10
        },
        {
          "category": "IMPERSONATION",
          "keywords": ["ngân hàng", "tài khoản"],
          "score": 15
        },
        {
          "category": "ACTION_REQUIRED",
          "keywords": ["xác minh"],
          "score": 5
        }
      ],
      "patternMatches": [
        {
          "pattern": "BANK_IMPERSONATION",
          "description": "Giả mạo ngân hàng yêu cầu xác minh",
          "score": 35
        }
      ],
      "crossReferenceChecks": {
        "urlCheck": {
          "url": "http://vietc0mbank.com",
          "riskScore": 85,
          "result": "DANGER"
        },
        "phoneCheck": {
          "phone": "+84912345678",
          "riskScore": 60,
          "reportCount": 12,
          "result": "CAUTION"
        }
      }
    },
    "evidences": [
      {
        "ruleCode": "PATTERN_BANK_SCAM",
        "ruleName": "Mẫu lừa đảo giả mạo ngân hàng",
        "score": 35,
        "severity": "CRITICAL"
      },
      {
        "ruleCode": "TEXT_URGENCY_PRESSURE",
        "ruleName": "Tạo áp lực khẩn cấp",
        "score": 15,
        "severity": "HIGH"
      },
      {
        "ruleCode": "CROSS_REF_URL_DANGER",
        "ruleName": "URL trong tin nhắn có rủi ro cao",
        "score": 30,
        "severity": "CRITICAL"
      }
    ],
    "recommendation": {
      "action": "BLOCK",
      "message": "Đây là tin nhắn lừa đảo giả mạo ngân hàng",
      "reasons": [
        "Giả danh ngân hàng để yêu cầu xác minh",
        "Tạo áp lực 'sắp bị khóa' để người dùng hoảng sợ",
        "URL và số điện thoại đều có dấu hiệu lừa đảo",
        "Ngân hàng thật KHÔNG BAO GIỜ gửi link yêu cầu xác minh qua SMS"
      ],
      "nextSteps": [
        "XÓA tin nhắn này ngay",
        "KHÔNG bấm link hoặc gọi số điện thoại trong tin nhắn",
        "KHÔNG cung cấp OTP, mật khẩu cho bất kỳ ai",
        "Liên hệ ngân hàng qua số hotline chính thức nếu lo lắng",
        "Gửi báo cáo lừa đảo"
      ]
    },
    "scannedAt": "2026-07-21T10:40:15Z",
    "processingTime": 180
  }
}
```

##### 2.3. Kiểm tra số điện thoại
```http
POST /scan/phone
Authorization: Bearer {accessToken}
Content-Type: application/json

Request:
{
  "phoneNumber": "0912345678",
  "countryCode": "VN"  // optional, default: VN
}

Response: 200 OK
{
  "success": true,
  "data": {
    "scanId": "scan_phone789",
    "input": {
      "phoneNumber": "0912345678",
      "normalized": "+84912345678"
    },
    "result": {
      "riskScore": 65,
      "riskLevel": "CAUTION",
      "levelColor": "YELLOW"
    },
    "analysis": {
      "inBlacklist": false,
      "reportCount": 12,
      "lastReportDate": "2026-07-20T15:30:00Z",
      "reportCategories": {
        "SCAM_CALL": 8,
        "SPAM": 3,
        "IMPERSONATION": 1
      },
      "verifiedReports": 3,
      "timeDecayScore": 0.85  // báo cáo gần đây
    },
    "evidences": [
      {
        "ruleCode": "PHONE_MULTIPLE_REPORTS",
        "ruleName": "Số điện thoại có nhiều báo cáo",
        "score": 40,
        "description": "12 người dùng đã báo cáo số này trong 30 ngày qua"
      },
      {
        "ruleCode": "PHONE_RECENT_REPORTS",
        "ruleName": "Báo cáo gần đây",
        "score": 15,
        "description": "Có báo cáo mới trong 7 ngày qua"
      }
    ],
    "recommendation": {
      "action": "CAUTION",
      "message": "Cẩn trọng với số điện thoại này",
      "reasons": [
        "Đã có 12 báo cáo từ người dùng khác",
        "8 báo cáo liên quan đến cuộc gọi lừa đảo",
        "Có báo cáo mới trong tuần qua"
      ],
      "nextSteps": [
        "Không tin tưởng thông tin từ số này",
        "Xác minh lại qua kênh chính thức nếu họ tự xưng là tổ chức/doanh nghiệp",
        "Không cung cấp thông tin cá nhân qua điện thoại"
      ]
    },
    "scannedAt": "2026-07-21T10:42:00Z"
  }
}
```

##### 2.4. Kiểm tra số tài khoản ngân hàng
```http
POST /scan/bank-account
Authorization: Bearer {accessToken}
Content-Type: application/json

Request:
{
  "accountNumber": "1234567890",
  "bankCode": "VCB",  // optional
  "accountName": "NGUYEN VAN X"  // optional
}

Response: 200 OK
{
  "success": true,
  "data": {
    "scanId": "scan_bank321",
    "input": {
      "accountNumber": "1234567890",
      "bankCode": "VCB"
    },
    "result": {
      "riskScore": 75,
      "riskLevel": "DANGER",
      "levelColor": "RED"
    },
    "analysis": {
      "inBlacklist": true,
      "blacklistSource": "VERIFIED_ADMIN",
      "reportCount": 28,
      "lastReportDate": "2026-07-21T09:15:00Z",
      "verifiedReports": 8,
      "reportCategories": {
        "FRAUD_TRANSFER": 20,
        "FAKE_SELLER": 5,
        "FAKE_LANDLORD": 3
      },
      "accountStatus": "BLOCKED_BY_BANK",  // nếu có thông tin
      "relatedScams": [
        {
          "type": "FAKE_PRODUCT_SALE",
          "description": "Bán hàng online không giao hàng",
          "reportCount": 15
        }
      ]
    },
    "evidences": [
      {
        "ruleCode": "ACCOUNT_IN_BLACKLIST",
        "ruleName": "Tài khoản trong danh sách đen",
        "score": 50,
        "severity": "CRITICAL"
      },
      {
        "ruleCode": "ACCOUNT_HIGH_REPORT_COUNT",
        "ruleName": "Số lượng báo cáo cao",
        "score": 25,
        "severity": "HIGH"
      }
    ],
    "recommendation": {
      "action": "BLOCK",
      "message": "KHÔNG chuyển tiền vào tài khoản này",
      "reasons": [
        "Tài khoản đã được xác minh là lừa đảo",
        "28 người dùng đã báo cáo",
        "Liên quan đến nhiều vụ bán hàng online lừa đảo"
      ],
      "nextSteps": [
        "HỦY giao dịch ngay lập tức",
        "Báo cáo cho người bán/chủ phòng về tài khoản đáng ngờ này",
        "Yêu cầu gặp trực tiếp hoặc sử dụng nền tảng trung gian có bảo vệ"
      ]
    },
    "scannedAt": "2026-07-21T10:45:30Z"
  }
}
```

##### 2.5. Quét QR Code
```http
POST /scan/qr
Authorization: Bearer {accessToken}
Content-Type: application/json

Request:
{
  "qrData": "00020101021238530010A00000072701270006970436011086868686862503040208QRIBFTTA53037045802VN6304ABCD",
  "qrType": "VIETQR"  // AUTO, VIETQR, URL, TEXT
}

Response: 200 OK
{
  "success": true,
  "data": {
    "scanId": "scan_qr555",
    "qrType": "VIETQR",
    "parsedData": {
      "type": "BANK_TRANSFER",
      "bankCode": "970436",
      "bankName": "Vietcombank",
      "accountNumber": "8686868686",
      "amount": null,
      "content": "QRIBFTTA"
    },
    "result": {
      "riskScore": 40,
      "riskLevel": "CAUTION",
      "levelColor": "YELLOW"
    },
    "analysis": {
      "accountCheck": {
        "riskScore": 35,
        "reportCount": 2,
        "inBlacklist": false
      },
      "contentAnalysis": {
        "hasSuspiciousKeywords": false,
        "riskScore": 5
      }
    },
    "recommendation": {
      "action": "VERIFY",
      "message": "Xác minh kỹ trước khi quét",
      "reasons": [
        "Tài khoản có 2 báo cáo nhưng chưa được xác minh",
        "Không rõ người nhận và mục đích chuyển tiền"
      ],
      "nextSteps": [
        "Xác nhận với người yêu cầu thanh toán qua kênh khác (gọi điện, nhắn tin)",
        "Kiểm tra số tài khoản có khớp với thông tin đã biết không",
        "Nếu mua hàng online, ưu tiên thanh toán qua nền tảng trung gian"
      ]
    },
    "scannedAt": "2026-07-21T10:50:00Z"
  }
}
```


---

#### 3. History & Reports APIs

##### 3.1. Lấy lịch sử quét
```http
GET /scan/history?page=1&limit=20&type=URL,TEXT&level=DANGER,CAUTION
Authorization: Bearer {accessToken}

Response: 200 OK
{
  "success": true,
  "data": {
    "items": [
      {
        "scanId": "scan_xyz789",
        "type": "URL",
        "input": "https://vietc0mbank-verify.com/xacthuc",
        "riskScore": 85,
        "riskLevel": "DANGER",
        "scannedAt": "2026-07-21T10:35:22Z"
      },
      // ... more items
    ],
    "pagination": {
      "page": 1,
      "limit": 20,
      "total": 156,
      "totalPages": 8
    }
  }
}
```

##### 3.2. Xem chi tiết một lần quét
```http
GET /scan/{scanId}
Authorization: Bearer {accessToken}

Response: 200 OK
{
  "success": true,
  "data": {
    // Full scan result như response của POST /scan/*
  }
}
```

##### 3.3. Gửi báo cáo lừa đảo
```http
POST /reports
Authorization: Bearer {accessToken}
Content-Type: multipart/form-data

Request:
{
  "entityType": "URL",  // URL, PHONE, BANK_ACCOUNT, TEXT
  "entityValue": "https://fake-shop.com",
  "scamType": "FAKE_SELLER",
  "description": "Mua hàng qua website này nhưng không nhận được hàng, shop không trả lời",
  "evidenceFiles": [File1, File2],  // screenshots, chat logs
  "amountLost": 2500000,  // optional
  "transactionDate": "2026-07-15"  // optional
}

Response: 201 Created
{
  "success": true,
  "data": {
    "reportId": "rpt_abc123",
    "status": "PENDING",
    "submittedAt": "2026-07-21T11:00:00Z",
    "estimatedReviewTime": "24-48 giờ"
  },
  "message": "Cảm ơn báo cáo của bạn. Chúng tôi sẽ xem xét trong 24-48 giờ."
}
```

##### 3.4. Lấy danh sách báo cáo của mình
```http
GET /reports/my?status=PENDING,VERIFIED&page=1&limit=10
Authorization: Bearer {accessToken}

Response: 200 OK
{
  "success": true,
  "data": {
    "items": [
      {
        "reportId": "rpt_abc123",
        "entityType": "URL",
        "entityValue": "https://fake-shop.com",
        "status": "UNDER_REVIEW",
        "submittedAt": "2026-07-21T11:00:00Z",
        "reviewedAt": null,
        "reviewerNote": null
      }
    ],
    "pagination": {...}
  }
}
```

---

#### 4. Admin APIs

##### 4.1. Quản lý Rule

###### 4.1.1. Lấy danh sách rules
```http
GET /admin/rules?category=URL,TEXT&enabled=true&page=1&limit=50
Authorization: Bearer {adminAccessToken}

Response: 200 OK
{
  "success": true,
  "data": {
    "items": [
      {
        "ruleId": "rule_001",
        "code": "URL_NO_HTTPS",
        "name": "URL không dùng HTTPS",
        "category": "URL",
        "weight": 20,
        "enabled": true,
        "description": "Phát hiện URL không sử dụng HTTPS",
        "conditionType": "SIMPLE",
        "createdAt": "2026-01-15T10:00:00Z",
        "updatedAt": "2026-07-10T14:30:00Z"
      }
    ],
    "pagination": {...}
  }
}
```

###### 4.1.2. Tạo rule mới
```http
POST /admin/rules
Authorization: Bearer {adminAccessToken}
Content-Type: application/json

Request:
{
  "code": "TEXT_FAKE_JOB_OFFER",
  "name": "Lừa đảo tuyển dụng",
  "category": "TEXT",
  "weight": 30,
  "enabled": true,
  "description": "Phát hiện tin nhắn tuyển dụng giả mạo",
  "conditionType": "COMPOSITE",
  "conditions": [
    {
      "field": "text",
      "operator": "CONTAINS",
      "value": "tuyển dụng|việc làm"
    },
    {
      "field": "text",
      "operator": "CONTAINS",
      "value": "phí|đặt cọc|chuyển khoản"
    }
  ]
}

Response: 201 Created
{
  "success": true,
  "data": {
    "ruleId": "rule_125",
    "code": "TEXT_FAKE_JOB_OFFER",
    // ... full rule object
  }
}
```

###### 4.1.3. Cập nhật rule
```http
PUT /admin/rules/{ruleId}
Authorization: Bearer {adminAccessToken}

Request:
{
  "weight": 35,  // tăng từ 30 lên 35
  "enabled": true
}

Response: 200 OK
```

###### 4.1.4. Xóa rule
```http
DELETE /admin/rules/{ruleId}
Authorization: Bearer {adminAccessToken}

Response: 200 OK
{
  "success": true,
  "message": "Rule đã được xóa"
}
```

##### 4.2. Quản lý Risk Entities (Blacklist/Whitelist)

###### 4.2.1. Lấy danh sách entities
```http
GET /admin/risk-entities?type=DOMAIN,PHONE&status=VERIFIED&page=1&limit=50
Authorization: Bearer {adminAccessToken}

Response: 200 OK
{
  "success": true,
  "data": {
    "items": [
      {
        "entityId": "ent_123",
        "entityType": "DOMAIN",
        "value": "fake-bank.com",
        "valueHash": "a1b2c3...",
        "riskLevel": "HIGH",
        "status": "VERIFIED",
        "source": "ADMIN_MANUAL",
        "reportCount": 45,
        "verifiedAt": "2026-07-20T10:00:00Z",
        "notes": "Website giả mạo ngân hàng"
      }
    ],
    "pagination": {...}
  }
}
```

###### 4.2.2. Thêm entity vào blacklist
```http
POST /admin/risk-entities
Authorization: Bearer {adminAccessToken}

Request:
{
  "entityType": "PHONE",
  "value": "+84987654321",
  "riskLevel": "HIGH",
  "status": "VERIFIED",
  "source": "ADMIN_MANUAL",
  "category": "SCAM_CALL",
  "notes": "Số điện thoại giả mạo ngân hàng, đã xác minh qua 20 báo cáo"
}

Response: 201 Created
```

###### 4.2.3. Cập nhật entity
```http
PUT /admin/risk-entities/{entityId}

Request:
{
  "status": "RESOLVED",  // chuyển từ VERIFIED sang RESOLVED
  "notes": "Số này đã ngừng hoạt động"
}
```

##### 4.3. Quản lý Reports (Kiểm duyệt)

###### 4.3.1. Lấy danh sách báo cáo cần xem xét
```http
GET /admin/reports?status=PENDING,UNDER_REVIEW&priority=HIGH&page=1&limit=20
Authorization: Bearer {adminAccessToken}

Response: 200 OK
{
  "success": true,
  "data": {
    "items": [
      {
        "reportId": "rpt_abc123",
        "entityType": "URL",
        "entityValue": "https://fake-shop.com",
        "scamType": "FAKE_SELLER",
        "description": "...",
        "evidenceUrls": ["https://storage.../screenshot1.png"],
        "reporterInfo": {
          "userId": "usr_xyz",
          "reputation": 0.85,
          "totalReports": 12,
          "verifiedReports": 10
        },
        "status": "PENDING",
        "priority": "MEDIUM",
        "submittedAt": "2026-07-21T11:00:00Z",
        "autoAnalysis": {
          "riskScore": 75,
          "suggestions": ["Có dấu hiệu domain giả mạo", "Nhiều keyword lừa đảo"]
        }
      }
    ],
    "pagination": {...}
  }
}
```

###### 4.3.2. Xem chi tiết báo cáo
```http
GET /admin/reports/{reportId}
Authorization: Bearer {adminAccessToken}

Response: 200 OK
{
  "success": true,
  "data": {
    // Full report details
    "reportId": "rpt_abc123",
    "timeline": [
      {
        "action": "SUBMITTED",
        "timestamp": "2026-07-21T11:00:00Z",
        "actor": "usr_xyz"
      },
      {
        "action": "AUTO_ANALYZED",
        "timestamp": "2026-07-21T11:00:15Z",
        "result": {...}
      }
    ],
    "relatedReports": [
      // Other reports về cùng entity
    ]
  }
}
```

###### 4.3.3. Duyệt báo cáo
```http
POST /admin/reports/{reportId}/review
Authorization: Bearer {adminAccessToken}

Request:
{
  "action": "APPROVE",  // APPROVE, REJECT, REQUEST_MORE_INFO
  "decision": {
    "addToBlacklist": true,
    "riskLevel": "HIGH",
    "category": "FAKE_SELLER"
  },
  "reviewerNote": "Đã xác minh qua 15 báo cáo tương tự. Thêm vào blacklist.",
  "notifyReporter": true
}

Response: 200 OK
{
  "success": true,
  "data": {
    "reportId": "rpt_abc123",
    "status": "VERIFIED",
    "reviewedAt": "2026-07-21T14:30:00Z",
    "createdEntity": {
      "entityId": "ent_456",
      "entityType": "URL",
      "value": "https://fake-shop.com"
    }
  },
  "message": "Báo cáo đã được xác minh và thêm vào blacklist"
}
```

##### 4.4. Dashboard & Statistics

###### 4.4.1. Tổng quan dashboard
```http
GET /admin/dashboard/overview?period=7d
Authorization: Bearer {adminAccessToken}

Response: 200 OK
{
  "success": true,
  "data": {
    "period": "7d",
    "scans": {
      "total": 15420,
      "byType": {
        "URL": 8500,
        "TEXT": 4200,
        "PHONE": 1800,
        "BANK_ACCOUNT": 920
      },
      "byRiskLevel": {
        "SAFE": 9850,
        "CAUTION": 3200,
        "DANGER": 2370
      }
    },
    "reports": {
      "total": 248,
      "pending": 45,
      "underReview": 12,
      "verified": 156,
      "rejected": 35
    },
    "entities": {
      "totalInBlacklist": 3420,
      "addedThisPeriod": 87,
      "byType": {
        "URL": 1850,
        "PHONE": 980,
        "BANK_ACCOUNT": 590
      }
    },
    "topScamTypes": [
      {"type": "FAKE_SELLER", "count": 89},
      {"type": "BANK_IMPERSONATION", "count": 72},
      {"type": "FAKE_JOB", "count": 45}
    ]
  }
}
```

###### 4.4.2. Trend analysis
```http
GET /admin/dashboard/trends?metric=scans&period=30d&groupBy=day
Authorization: Bearer {adminAccessToken}

Response: 200 OK
{
  "success": true,
  "data": {
    "metric": "scans",
    "period": "30d",
    "dataPoints": [
      {"date": "2026-06-21", "value": 450, "dangerCount": 85},
      {"date": "2026-06-22", "value": 520, "dangerCount": 92},
      // ... 30 days
    ]
  }
}
```

---

#### 5. Common Response Patterns

##### 5.1. Success Response
```json
{
  "success": true,
  "data": { /* response data */ },
  "message": "Optional success message"
}
```

##### 5.2. Error Response
```json
{
  "success": false,
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "Dữ liệu không hợp lệ",
    "details": [
      {
        "field": "url",
        "message": "URL không đúng định dạng"
      }
    ]
  },
  "timestamp": "2026-07-21T10:00:00Z",
  "path": "/v1/scan/url"
}
```

##### 5.3. Error Codes

| HTTP Code | Error Code | Mô tả |
|---|---|---|
| 400 | VALIDATION_ERROR | Dữ liệu đầu vào không hợp lệ |
| 400 | INVALID_URL | URL không đúng format |
| 401 | UNAUTHORIZED | Chưa đăng nhập hoặc token không hợp lệ |
| 403 | FORBIDDEN | Không có quyền truy cập |
| 404 | NOT_FOUND | Không tìm thấy resource |
| 409 | ALREADY_EXISTS | Resource đã tồn tại |
| 429 | RATE_LIMIT_EXCEEDED | Quá số lần request cho phép |
| 500 | INTERNAL_ERROR | Lỗi hệ thống |
| 503 | SERVICE_UNAVAILABLE | Dịch vụ tạm thời không khả dụng |

---

#### 6. Rate Limiting

| Endpoint Pattern | User Role | Limit |
|---|---|---|
| POST /scan/* | Free User | 50 requests/hour |
| POST /scan/* | Authenticated User | 200 requests/hour |
| POST /scan/* | Premium User | 1000 requests/hour |
| POST /reports | Authenticated User | 10 requests/day |
| GET /scan/history | Authenticated User | 100 requests/hour |
| /admin/* | Admin | 500 requests/hour |

**Rate Limit Headers:**
```
X-RateLimit-Limit: 200
X-RateLimit-Remaining: 187
X-RateLimit-Reset: 1658404800  // Unix timestamp
```

---

#### 7. Webhooks (Optional - Future)

```http
POST {user_webhook_url}
Content-Type: application/json
X-Signature: sha256=...

{
  "event": "report.verified",
  "data": {
    "reportId": "rpt_abc123",
    "entityType": "URL",
    "entityValue": "https://fake-shop.com",
    "status": "VERIFIED",
    "verifiedAt": "2026-07-21T14:30:00Z"
  },
  "timestamp": "2026-07-21T14:30:05Z"
}
```

---

**Lưu ý triển khai:**
- Tất cả API phải sử dụng HTTPS
- JWT token có thời hạn 1 giờ, refresh token 7 ngày
- Implement CORS cho web client
- Log tất cả API calls cho security audit
- Validate input ở cả client và server
- Rate limiting implement bằng Redis
- Cache kết quả scan trong 5 phút với Redis
