# 1. OWASP

## 1.1. OWASP - Open Web Application Security Project
- Là tổ chức phi lợi nhuận quốc tế với mục tiêu cốt lõi là cải thiện an ninh phần mềm.
- Sứ mệnh: Nâng cao tính an toàn của phần mềm thông qua các sáng kiến mã nguồn mở do cộng đồng dẫn dắt và các chương trình giáo dục cộng đồng.
- Cộng đồng: Mạng lưới rộng lớn bao gồm hàng trăm chi nhánh (chapters) trên toàn thế giới, hàng chục nghìn thành viên và thường xuyên tổ chức các hội nghị bảo mật quy mô địa phương cũng như toàn cầu (như Global AppSec).
- Tính trung lập: Duy trì sự trung lập với các nhà cung cấp (vendor-neutral), tận dụng trí tuệ tập thể của các chuyên gia bảo mật hàng đầu thế giới để đưa ra các hướng dẫn khách quan.
- Các dự án tiêu biểu:
    - OWASP Top Ten: Danh sách 10 rủi ro bảo mật ứng dụng web quan trọng nhất.
    - ASVS (Application Security Verification Standard): Tiêu chuẩn xác minh bảo mật ứng dụng.
    - SAMM (Software Assurance Maturity Model): Mô hình đánh giá mức độ trưởng thành của quy trình bảo mật phần mềm.
    - OWASP Juice Shop: Một ứng dụng web được thiết kế sẵn các lỗ hổng để phục vụ đào tạo.
## 1.2. Reference
- https://owasp.org/about/
---

# 2. OWASP Top 10 rule-set

OWASP Top 10 là tài liệu nhận thức bảo mật do OWASP công bố, tổng hợp 10 nhóm rủi ro bảo mật nghiêm trọng và phổ biến nhất đối với ứng dụng web. Danh sách này không phải là một tiêu chuẩn kiểm thử đầy đủ, mà là điểm khởi đầu để nhà phát triển, kiểm thử viên, quản trị hệ thống và tổ chức nhận diện các lớp rủi ro quan trọng cần ưu tiên phòng chống.

OWASP Top 10 2025 phản ánh sự thay đổi của môi trường phát triển phần mềm hiện đại: ứng dụng phụ thuộc nhiều hơn vào thư viện bên thứ ba, chuỗi cung ứng phần mềm, dịch vụ đám mây, API và cơ chế tự động hóa. Vì vậy, ngoài các lỗi kinh điển như kiểm soát truy cập, cấu hình sai, mã hóa yếu hay injection, phiên bản 2025 cũng nhấn mạnh các rủi ro về chuỗi cung ứng, tính toàn vẹn dữ liệu/phần mềm, ghi log/cảnh báo và xử lý các tình huống ngoại lệ.

## 2.1. A01:2025 - Broken Access Control

- Giải thích: Broken Access Control xảy ra khi ứng dụng không kiểm soát đúng việc người dùng được phép truy cập tài nguyên hoặc thực hiện hành động nào. Người dùng có thể vượt qua giới hạn quyền bằng cách sửa URL, thay đổi tham số, gọi trực tiếp API nội bộ, hoặc khai thác thiếu sót trong kiểm tra quyền ở phía máy chủ.
- Xếp loại độ nghiêm trọng và hậu quả: Rất cao, đứng đầu OWASP Top 10 2025. Hậu quả có thể gồm lộ dữ liệu nhạy cảm, sửa/xóa dữ liệu trái phép, chiếm quyền chức năng quản trị, truy cập tài khoản người khác, hoặc leo thang đặc quyền trong hệ thống.
- Ví dụ minh họa: Một người dùng thường truy cập `/api/users/1001/orders`, sau đó đổi ID thành `/api/users/1002/orders` và xem được đơn hàng của người khác vì máy chủ chỉ kiểm tra người dùng đã đăng nhập, nhưng không kiểm tra người đó có sở hữu tài nguyên hay không.

## 2.2. A02:2025 - Security Misconfiguration

- Giải thích: Security Misconfiguration là lỗi cấu hình bảo mật không đúng hoặc không đầy đủ trong ứng dụng, máy chủ, cơ sở dữ liệu, container, cloud service, framework hoặc middleware. Các lỗi thường gặp gồm bật chế độ debug ở môi trường production, dùng cấu hình mặc định, phân quyền cloud quá rộng, thiếu security header, hoặc để lộ thông tin lỗi chi tiết.
- Xếp loại độ nghiêm trọng và hậu quả: Cao. Hậu quả có thể là lộ thông tin hệ thống, mở rộng bề mặt tấn công, cho phép truy cập trái phép, tạo điều kiện cho kẻ tấn công khai thác các lỗi khác, hoặc chiếm quyền máy chủ nếu cấu hình sai nghiêm trọng.
- Ví dụ minh họa: Một ứng dụng production vẫn bật trang debug. Khi xảy ra lỗi, trang này hiển thị stack trace, biến môi trường, đường dẫn file và thông tin kết nối cơ sở dữ liệu, giúp kẻ tấn công hiểu cấu trúc hệ thống để khai thác sâu hơn.

## 2.3. A03:2025 - Software Supply Chain Failures

- Giải thích: Software Supply Chain Failures là rủi ro phát sinh từ các thành phần bên thứ ba và quy trình cung ứng phần mềm như thư viện mã nguồn mở, package manager, plugin, container image, CI/CD pipeline, artifact repository hoặc dependency chưa được kiểm soát. Lỗi có thể đến từ dependency có lỗ hổng, package độc hại, dependency confusion, build pipeline bị can thiệp hoặc thiếu xác minh nguồn gốc phần mềm.
- Xếp loại độ nghiêm trọng và hậu quả: Rất cao. Hậu quả có thể lan rộng vì một thành phần bị nhiễm độc có thể ảnh hưởng đến nhiều ứng dụng, nhiều khách hàng hoặc toàn bộ quy trình triển khai. Kẻ tấn công có thể chèn mã độc, đánh cắp secret, kiểm soát bản build hoặc phát tán phần mềm bị sửa đổi.
- Ví dụ minh họa: Dự án sử dụng một thư viện npm phổ biến. Phiên bản mới của thư viện bị chiếm quyền và được chèn mã đánh cắp token truy cập. Khi hệ thống CI tự động cập nhật dependency và build lại ứng dụng, mã độc được đưa vào môi trường production.

## 2.4. A04:2025 - Cryptographic Failures

- Giải thích: Cryptographic Failures là các lỗi liên quan đến bảo vệ dữ liệu bằng mật mã, đặc biệt là dữ liệu nhạy cảm khi lưu trữ hoặc truyền qua mạng. Lỗi có thể gồm không mã hóa dữ liệu quan trọng, dùng thuật toán yếu, quản lý khóa kém, lưu mật khẩu bằng hash yếu, tắt kiểm tra chứng chỉ TLS hoặc truyền dữ liệu qua kênh không an toàn.
- Xếp loại độ nghiêm trọng và hậu quả: Cao. Hậu quả thường là lộ dữ liệu cá nhân, thông tin đăng nhập, token, dữ liệu tài chính hoặc dữ liệu nội bộ. Nếu khóa mã hóa bị lộ hoặc thuật toán yếu bị phá, toàn bộ dữ liệu được bảo vệ bởi cơ chế đó có thể mất tính bí mật.
- Ví dụ minh họa: Một hệ thống lưu mật khẩu người dùng bằng MD5 không có salt. Khi cơ sở dữ liệu bị rò rỉ, kẻ tấn công có thể dùng rainbow table hoặc brute force để khôi phục nhiều mật khẩu gốc.

## 2.5. A05:2025 - Injection

- Giải thích: Injection xảy ra khi dữ liệu không đáng tin cậy từ người dùng được đưa vào câu lệnh hoặc trình thông dịch mà không được kiểm soát đúng cách. Các dạng phổ biến gồm SQL injection, NoSQL injection, command injection, LDAP injection, template injection hoặc expression language injection.
- Xếp loại độ nghiêm trọng và hậu quả: Cao. Hậu quả có thể gồm đọc, sửa hoặc xóa dữ liệu trái phép, vượt qua xác thực, thực thi lệnh trên máy chủ, chiếm quyền hệ thống hoặc làm gián đoạn dịch vụ. Injection đặc biệt nguy hiểm vì thường biến dữ liệu đầu vào thành một phần của lệnh thực thi.
- Ví dụ minh họa: Chức năng đăng nhập tạo câu SQL bằng cách nối chuỗi trực tiếp: `SELECT * FROM users WHERE username = '$u' AND password = '$p'`. Kẻ tấn công nhập username là `' OR '1'='1` để làm điều kiện luôn đúng và vượt qua đăng nhập.

## 2.6. A06:2025 - Insecure Design

- Giải thích: Insecure Design là rủi ro đến từ thiết kế hệ thống không an toàn ngay từ đầu, khác với lỗi lập trình đơn lẻ. Vấn đề nằm ở việc thiếu mô hình mối đe dọa, thiếu kiểm soát bảo mật trong luồng nghiệp vụ, tin tưởng sai vào người dùng hoặc hệ thống bên ngoài, hoặc thiết kế không có cơ chế giới hạn hành vi nguy hiểm.
- Xếp loại độ nghiêm trọng và hậu quả: Cao. Hậu quả có thể nghiêm trọng vì lỗi nằm trong logic nghiệp vụ và kiến trúc, nên không thể khắc phục chỉ bằng vá một đoạn code. Nó có thể dẫn đến gian lận, lạm dụng chức năng, vượt hạn mức, né quy trình kiểm duyệt hoặc phá vỡ giả định bảo mật của toàn hệ thống.
- Ví dụ minh họa: Một ứng dụng ví điện tử chỉ kiểm tra số dư ở giao diện trước khi gửi lệnh chuyển tiền, nhưng API phía máy chủ không kiểm tra lại. Kẻ tấn công gọi trực tiếp API nhiều lần để tạo giao dịch vượt quá số dư thực tế.

## 2.7. A07:2025 - Authentication Failures

- Giải thích: Authentication Failures là các lỗi trong xác thực danh tính người dùng hoặc quản lý phiên đăng nhập. Lỗi có thể gồm mật khẩu yếu, thiếu giới hạn thử đăng nhập, cơ chế khôi phục mật khẩu không an toàn, session token dễ đoán, token không hết hạn đúng cách, hoặc triển khai đa yếu tố không đầy đủ.
- Xếp loại độ nghiêm trọng và hậu quả: Cao. Hậu quả có thể gồm chiếm quyền tài khoản, giả mạo danh tính, truy cập dữ liệu riêng tư, thực hiện giao dịch trái phép hoặc mở đường cho leo thang đặc quyền nếu tài khoản bị chiếm là tài khoản quản trị.
- Ví dụ minh họa: Trang đăng nhập không giới hạn số lần thử và không có cơ chế phát hiện brute force. Kẻ tấn công dùng danh sách mật khẩu phổ biến để thử hàng nghìn lần cho đến khi đăng nhập thành công vào tài khoản người dùng.

## 2.8. A08:2025 - Software or Data Integrity Failures

- Giải thích: Software or Data Integrity Failures xảy ra khi ứng dụng không bảo đảm tính toàn vẹn của phần mềm, dữ liệu hoặc bản cập nhật. Rủi ro này liên quan đến việc tin tưởng dữ liệu hoặc mã nguồn chưa được xác minh, cập nhật phần mềm không kiểm tra chữ ký, deserialization không an toàn, hoặc pipeline triển khai không có kiểm soát toàn vẹn.
- Xếp loại độ nghiêm trọng và hậu quả: Cao. Hậu quả có thể gồm chạy mã bị sửa đổi, cập nhật phần mềm độc hại, thay đổi dữ liệu quan trọng, phá vỡ kết quả xử lý hoặc dẫn đến thực thi mã từ xa trong một số trường hợp.
- Ví dụ minh họa: Ứng dụng tự động tải plugin từ một URL bên ngoài và nạp vào hệ thống mà không kiểm tra chữ ký số hoặc checksum. Nếu nguồn tải bị thay đổi, kẻ tấn công có thể cung cấp plugin độc hại để ứng dụng thực thi.

## 2.9. A09:2025 - Security Logging and Alerting Failures

- Giải thích: Security Logging and Alerting Failures là việc hệ thống không ghi nhận, giám sát hoặc cảnh báo đầy đủ các sự kiện bảo mật quan trọng. Các sự kiện như đăng nhập thất bại liên tục, thay đổi quyền, truy cập dữ liệu nhạy cảm, lỗi xác thực token, hoặc hành vi bất thường cần được ghi log và cảnh báo phù hợp.
- Xếp loại độ nghiêm trọng và hậu quả: Trung bình đến cao. Bản thân lỗi này không phải lúc nào cũng tạo ra lỗ hổng khai thác trực tiếp, nhưng làm tổ chức không phát hiện hoặc phản ứng kịp thời khi bị tấn công. Hậu quả là thời gian xâm nhập kéo dài, khó điều tra nguyên nhân, mất bằng chứng số và tăng thiệt hại khi sự cố xảy ra.
- Ví dụ minh họa: Kẻ tấn công thử đăng nhập vào tài khoản quản trị hàng nghìn lần từ nhiều địa chỉ IP. Hệ thống chỉ ghi log lỗi chung chung và không tạo cảnh báo, nên đội vận hành không biết có tấn công brute force đang diễn ra.

## 2.10. A10:2025 - Mishandling of Exceptional Conditions

- Giải thích: Mishandling of Exceptional Conditions là việc ứng dụng xử lý sai hoặc không đầy đủ các tình huống bất thường như lỗi hệ thống, timeout, dữ liệu không hợp lệ, trạng thái cạnh tranh, kết nối thất bại, tài nguyên cạn kiệt hoặc hành vi ngoài luồng dự kiến. Khi ngoại lệ không được xử lý an toàn, ứng dụng có thể rơi vào trạng thái không nhất quán hoặc để lộ thông tin.
- Xếp loại độ nghiêm trọng và hậu quả: Cao. Hậu quả có thể gồm lộ thông tin nhạy cảm qua thông báo lỗi, bỏ qua kiểm tra bảo mật khi xảy ra lỗi, làm hỏng dữ liệu, tạo điều kiện tấn công từ chối dịch vụ hoặc khiến giao dịch nghiệp vụ được xử lý sai.
- Ví dụ minh họa: Trong quy trình thanh toán, hệ thống trừ số lượng hàng tồn kho trước rồi gọi cổng thanh toán. Nếu cổng thanh toán timeout nhưng ứng dụng không rollback đúng cách, đơn hàng có thể rơi vào trạng thái không rõ ràng, gây sai lệch dữ liệu hoặc bị lợi dụng để tạo giao dịch bất thường.

## 2.11. Reference

- https://www.cloudflare.com/learning/security/threats/owasp-top-10/
- https://owasp.org/Top10/2025/
- https://owasp.org/Top10/2025/A01_2025-Broken_Access_Control/
- https://owasp.org/Top10/2025/A02_2025-Security_Misconfiguration/
- https://owasp.org/Top10/2025/A03_2025-Software_Supply_Chain_Failures/
- https://owasp.org/Top10/2025/A04_2025-Cryptographic_Failures/
- https://owasp.org/Top10/2025/A05_2025-Injection/
- https://owasp.org/Top10/2025/A06_2025-Insecure_Design/
- https://owasp.org/Top10/2025/A07_2025-Authentication_Failures/
- https://owasp.org/Top10/2025/A08_2025-Software_or_Data_Integrity_Failures/
- https://owasp.org/Top10/2025/A09_2025-Security_Logging_and_Alerting_Failures/
- https://owasp.org/Top10/2025/A10_2025-Mishandling_of_Exceptional_Conditions/

---

# 3. Ứng dụng rủi ro OWASP Top 10 vào đồ án tốt nghiệp

Đối với đồ án Mobile Anti-Scam, OWASP Top 10 có thể được dùng như một khung tham chiếu để thiết kế hệ thống an toàn hơn, đồng thời tăng mức độ tin cậy của ứng dụng trong mắt người dùng. Vì ứng dụng có thể chạy ngầm trên mobile, thu thập URL/tin nhắn/cuộc gọi nghi ngờ, gửi dữ liệu lên backend để phân tích và cảnh báo lừa đảo, các rủi ro OWASP cần được chuyển thành yêu cầu cụ thể cho cả mobile app, API backend, cơ sở dữ liệu, mô hình phát hiện và cơ chế vận hành.

| Nhóm rủi ro OWASP Top 10 | Rủi ro có thể gặp trong đồ án | Cách ứng dụng vào project | Góc nhìn dev / user |
|---|---|---|---|
| A01: Broken Access Control | Người dùng hoặc attacker truy cập được báo cáo, lịch sử quét, thông tin thiết bị hoặc dữ liệu cộng đồng không thuộc quyền của mình. | Thiết kế phân quyền rõ cho user, admin, moderator; kiểm tra quyền ở backend cho mọi API; không chỉ dựa vào UI mobile để ẩn chức năng. | Dev: kiểm soát quyền truy cập theo vai trò và chủ sở hữu dữ liệu.<br>User: chỉ xem được dữ liệu của mình, không lo người khác đọc lịch sử quét hoặc báo cáo cá nhân. |
| A02: Security Misconfiguration | Backend, database, Firebase/cloud storage hoặc API gateway cấu hình quá mở, để lộ log, token, endpoint nội bộ hoặc file cấu hình. | Tách môi trường dev/prod; tắt debug ở production; giới hạn CORS, storage rule và database rule; dùng security header và cấu hình mặc định an toàn. | Dev: rà soát cấu hình server, cloud, database và API trước khi demo/triển khai.<br>User: app không vô tình làm lộ thông tin cá nhân vì hệ thống bị cấu hình hớ hênh. |
| A03: Software Supply Chain Failures | Mobile app và backend phụ thuộc vào SDK, package AI, thư viện quét URL hoặc API bên thứ ba có lỗ hổng hoặc bị thay đổi độc hại. | Khóa phiên bản dependency; kiểm tra CVE định kỳ; dùng nguồn package chính thức; ghi rõ danh sách thư viện, API, model và lý do chọn trong tài liệu đồ án. | Dev: chọn và kiểm soát thư viện/API bên thứ ba có nguồn gốc rõ ràng.<br>User: có cơ sở tin rằng app không cài kèm thành phần độc hại hoặc thư viện thiếu an toàn. |
| A04: Cryptographic Failures | URL, token đăng nhập, dữ liệu báo cáo, thông tin thiết bị hoặc kết quả phân tích bị lộ khi truyền/lưu trữ. | Bắt buộc HTTPS/TLS; không lưu token dạng plain text; mã hóa dữ liệu nhạy cảm; hash dữ liệu cần ẩn danh; không tắt kiểm tra chứng chỉ TLS trên mobile. | Dev: mã hóa dữ liệu nhạy cảm khi truyền và khi lưu trữ.<br>User: thông tin quét, tài khoản và dữ liệu báo cáo không bị đọc trộm khi dùng mạng công cộng. |
| A05: Injection | API nhận URL, domain, nội dung SMS, bình luận cộng đồng hoặc báo cáo người dùng và đưa trực tiếp vào truy vấn/database/script. | Validate và sanitize input; dùng prepared statement/ORM an toàn; giới hạn độ dài URL/nội dung; không thực thi dữ liệu người dùng như lệnh hoặc template. | Dev: coi mọi URL, nội dung SMS và báo cáo người dùng là dữ liệu không tin cậy.<br>User: khi gửi link nghi ngờ để kiểm tra, app vẫn an toàn và không bị lợi dụng để tấn công hệ thống. |
| A06: Insecure Design | Ứng dụng chạy ngầm quá nhiều quyền, thu thập dữ liệu vượt nhu cầu, hoặc cảnh báo lừa đảo thiếu cơ chế giải thích khiến người dùng không tin tưởng. | Áp dụng privacy-by-design; chỉ xin quyền cần thiết; hiển thị lý do cảnh báo; cho phép người dùng bật/tắt quét nền; có luồng báo cáo sai và kiểm duyệt kết quả. | Dev: thiết kế từ đầu theo nguyên tắc tối thiểu hóa dữ liệu và minh bạch quyền truy cập.<br>User: hiểu vì sao app cần quyền, app đang bảo vệ gì, và có thể kiểm soát chế độ chạy ngầm. |
| A07: Authentication Failures | Tài khoản người dùng, admin hoặc moderator bị chiếm quyền, dẫn đến sửa dữ liệu blacklist/whitelist hoặc duyệt báo cáo sai. | Dùng đăng nhập an toàn; giới hạn thử đăng nhập; token có thời hạn; refresh token an toàn; admin/moderator nên có MFA hoặc cơ chế xác minh mạnh hơn. | Dev: bảo vệ phiên đăng nhập, tài khoản quản trị và các chức năng kiểm duyệt.<br>User: tài khoản và dữ liệu cá nhân khó bị chiếm đoạt hơn, đặc biệt khi dùng app trên thiết bị di động. |
| A08: Software or Data Integrity Failures | Dữ liệu blacklist, whitelist, điểm uy tín domain, model AI hoặc bản cập nhật rule bị sửa đổi, làm app cảnh báo sai hoặc bỏ sót scam. | Kiểm tra chữ ký/checksum cho rule/model; log thay đổi dữ liệu quan trọng; phân quyền người duyệt dữ liệu; có versioning cho dataset, model và rule cảnh báo. | Dev: bảo đảm rule, dataset và model cảnh báo không bị sửa trái phép.<br>User: kết quả cảnh báo đáng tin hơn, giảm nguy cơ app báo an toàn cho link lừa đảo hoặc chặn nhầm link hợp lệ. |
| A09: Security Logging and Alerting Failures | Hệ thống không phát hiện spam báo cáo, brute force tài khoản admin, thay đổi bất thường trong blacklist hoặc tấn công vào API. | Ghi log sự kiện bảo mật; cảnh báo khi có nhiều request bất thường; theo dõi thay đổi dữ liệu nhạy cảm; xây dashboard đơn giản cho admin trong phạm vi đồ án. | Dev: theo dõi sự kiện bất thường để điều tra và phản ứng khi có tấn công.<br>User: khi hệ thống bị lạm dụng hoặc dữ liệu cảnh báo bị tác động, đội vận hành có thể phát hiện sớm hơn. |
| A10: Mishandling of Exceptional Conditions | Khi API phân tích lỗi, timeout hoặc mất mạng, app có thể hiển thị kết quả sai, crash, hoặc bỏ qua cảnh báo quan trọng. | Thiết kế fallback rõ ràng: "không đủ dữ liệu để kết luận" thay vì báo an toàn; retry có giới hạn; xử lý offline; không để lỗi backend làm lộ stack trace cho người dùng. | Dev: xử lý lỗi mạng, timeout, dữ liệu bất thường và lỗi model theo trạng thái an toàn.<br>User: nếu app không phân tích được, người dùng nhận cảnh báo trung thực thay vì kết luận sai rằng link an toàn. |

## 3.1. Các rủi ro nên ưu tiên trong phạm vi đồ án

Trong phạm vi một đồ án tốt nghiệp, không nhất thiết triển khai đầy đủ mọi kiểm soát ở mức sản phẩm thương mại. Tuy nhiên, các nhóm rủi ro nên ưu tiên cao gồm:

- A01, A07 và A08: Bảo vệ tài khoản, quyền truy cập và tính toàn vẹn của dữ liệu cảnh báo, vì đây là phần ảnh hưởng trực tiếp đến độ tin cậy của hệ thống.
- A04 và A06: Bảo vệ dữ liệu người dùng mobile và thiết kế quyền riêng tư hợp lý, đặc biệt khi ứng dụng chạy ngầm hoặc phân tích nội dung nhạy cảm.
- A09 và A10: Ghi nhận sự kiện, cảnh báo bất thường và xử lý lỗi an toàn để hệ thống không đưa ra kết luận sai khi thiếu dữ liệu.

## 3.2. Cách đưa OWASP vào tài liệu và sản phẩm đồ án

- Trong phần phân tích yêu cầu: bổ sung yêu cầu bảo mật như phân quyền API, mã hóa dữ liệu, kiểm soát quyền truy cập mobile và cơ chế xin quyền minh bạch.
- Trong phần thiết kế hệ thống: mô tả luồng dữ liệu từ mobile app đến backend, điểm nào cần kiểm tra quyền, validate input, ghi log và bảo vệ dữ liệu.
- Trong phần hiện thực: áp dụng kiểm tra token, validate URL/nội dung đầu vào, giới hạn request, lưu cấu hình an toàn và xử lý timeout/lỗi phân tích.
- Trong phần đánh giá: tạo các test case bảo mật đơn giản, ví dụ truy cập trái quyền, gửi URL độc hại, giả lập API timeout, kiểm tra dữ liệu nhạy cảm có bị log ra hay không.
