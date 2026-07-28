# Kịch bản thuyết trình kiến trúc Anti-Scam Platform

**Phiên bản:** 2026-07-28  
**Thời lượng mục tiêu:** 12 phút 25 giây  
**Đối tượng:** Giảng viên hướng dẫn kỹ thuật phần mềm

## Slide 1 - Anti-Scam Platform

**Mục tiêu:** Đặt vấn đề và tuyên bố luận điểm kiến trúc.  
**Thời lượng:** 30 giây

**Script:**

Tuần này nhóm em tập trung trả lời một câu hỏi: với một nền tảng chống lừa đảo có nhiều loại đầu vào, kiến trúc nào vừa đủ khả thi cho nhóm bốn người, vừa bảo đảm tính tin cậy và vẫn có đường mở rộng? Đề xuất của nhóm là Modular Monolith kết hợp Event-Driven Workers. Phần trình bày sẽ đi từ bài toán, lý do lựa chọn, kiến trúc tổng thể, luồng scan, bảo mật đến cách chia việc và roadmap.

**Ý cần nhấn:** Đây là kiến trúc mục tiêu, không phải tuyên bố rằng toàn bộ hệ thống đã hoàn thành.

**Chuyển slide:** Trước khi chọn kiến trúc, cần làm rõ hệ thống phải xử lý những gì.

## Slide 2 - Một nền tảng, nhiều tín hiệu

**Mục tiêu:** Chốt phạm vi đầu vào và đầu ra thống nhất.  
**Thời lượng:** 55 giây

**Script:**

Người dùng có thể gặp scam qua link, tin nhắn, số điện thoại, tài khoản ngân hàng hoặc QR. Ngoài ra hệ thống còn nhận report cộng đồng. Sáu đầu vào này có pipeline phân tích khác nhau, nhưng trải nghiệm người dùng phải thống nhất: điểm rủi ro 0 đến 100, ba mức Safe, Caution, Danger, evidence giải thích vì sao và khuyến nghị nên làm gì. MVP dùng rule-based và heuristic vì dễ kiểm thử, giải thích và không cần dataset lớn ngay từ đầu.

**Ý cần nhấn:** Kiến trúc phải cho phép scanner khác nhau nhưng kết quả có một contract chung.

**Chuyển slide:** Từ phạm vi đó, nhóm so sánh ba mức độ kiến trúc.

## Slide 3 - Quyết định kiến trúc

**Mục tiêu:** Bảo vệ lựa chọn Modular Monolith + Event-Driven Workers.  
**Thời lượng:** 75 giây

**Script:**

Modular Monolith thuần là phương án đơn giản nhất, nhưng các tác vụ như fetch URL, follow redirect hoặc export report có thể làm request chờ lâu. Full microservices cho phép scale độc lập, nhưng kéo theo API gateway, service discovery, distributed tracing, nhiều database contract và chi phí deploy. Với nhóm bốn người, overhead đó lớn hơn giá trị ở giai đoạn MVP.

Phương án giữa là Modular Monolith kết hợp worker. Backend vẫn là một lõi nghiệp vụ dễ debug và deploy, nhưng tác vụ chậm được đưa qua queue cho worker. Các module vẫn có ranh giới đủ rõ để sau này tách service nếu có nhu cầu scale, ownership hoặc failure isolation thật.

**Ý cần nhấn:** Nhóm không từ chối microservices; nhóm trì hoãn nó đến khi có bằng chứng.

**Chuyển slide:** Slide tiếp theo cho thấy ranh giới của phương án được chọn.

## Slide 4 - Kiến trúc tổng thể

**Mục tiêu:** Giải thích năm lớp và hướng giao tiếp chính.  
**Thời lượng:** 80 giây

**Script:**

Hệ thống có ba client: Web User, Admin Dashboard và Mobile App. Tất cả đi qua Nginx để route và terminate TLS. Spring Boot API là entry point nghiệp vụ, gồm Auth/User, Scan Orchestrator, Rule Engine, Report, Admin và Notification.

Các tác vụ nền được tách thành URL Scanner, Text Analyzer, Phone/Bank Checker, QR Parser, Report Export và Threat Data Ingestion. API và worker dùng PostgreSQL làm nguồn dữ liệu chính, Redis cho dữ liệu ngắn hạn, RabbitMQ cho job queue và MinIO cho evidence hoặc report file. Ranh giới này giúp request nhẹ trả nhanh, còn công việc chậm không chặn client.

**Ý cần nhấn:** Worker có thể chạy container riêng nhưng chưa cần trở thành microservice độc lập.

**Chuyển slide:** Bên trong kiến trúc, ba trách nhiệm giữ hệ thống nhất quán.

## Slide 5 - Orchestrate, analyze, explain

**Mục tiêu:** Làm rõ trách nhiệm Orchestrator, scanner và Rule Engine.  
**Thời lượng:** 65 giây

**Script:**

Scan Orchestrator nhận mọi yêu cầu, chuẩn hóa input, kiểm tra cache và quyết định sync hay async. Scanner chuyên trách chỉ tập trung trích tín hiệu: URL Scanner xử lý redirect, TLS, domain và HTML; Text Analyzer xử lý keyword, pattern, entity; QR Parser giải mã rồi định tuyến.

Rule Engine là lớp dùng chung để biến tín hiệu thành điểm, risk level và evidence. Nhờ đó client không phải hiểu output riêng của từng scanner. Report, Admin và Notification đứng bên cạnh pipeline scan, không chen logic quản trị vào scanner.

**Ý cần nhấn:** Orchestrator điều phối, scanner phân tích, Rule Engine giải thích.

**Chuyển slide:** Có thể thấy rõ cách phối hợp đó qua một yêu cầu scan URL.

## Slide 6 - Luồng scan URL: nhanh khi có thể, async khi cần

**Mục tiêu:** Mô tả cache hit và cache miss end-to-end.  
**Thời lượng:** 80 giây

**Script:**

Client gửi URL cùng idempotency key. API kiểm tra rate limit, request trùng và recent cache. Nếu cache hit, RiskResult được trả ngay. Nếu cache miss, API tạo ScanRequest trạng thái Processing, publish job vào RabbitMQ và trả scanId.

URL worker kiểm tra redirect, TLS, domain và HTML trong giới hạn SSRF an toàn. Rule Engine tính điểm và evidence, sau đó worker lưu RiskResult vào PostgreSQL JSONB và cập nhật cache. Client dùng scanId để lấy kết quả hoàn tất. Các lookup nhẹ như phone hoặc account có thể đi đường sync; URL fetch và report export ưu tiên async.

**Ý cần nhấn:** Sync/async là quyết định theo độ nặng công việc, không phải hai hệ thống khác nhau.

**Chuyển slide:** Luồng này cần phân vai dữ liệu rõ để không biến cache thành nguồn sự thật.

## Slide 7 - Mỗi kho dữ liệu có một trách nhiệm

**Mục tiêu:** Giải thích PostgreSQL, JSONB, Redis, RabbitMQ và MinIO.  
**Thời lượng:** 65 giây

**Script:**

PostgreSQL là nguồn dữ liệu chính cho user, scan, report, rule và audit. JSONB nằm trong PostgreSQL để lưu evidence đa dạng của URL, text hoặc QR mà vẫn giữ transaction và ownership thống nhất.

Redis chỉ giữ dữ liệu ngắn hạn: cache, rate limit, idempotency và lock. RabbitMQ vận chuyển job với ack, retry và dead-letter. MinIO giữ evidence file hoặc report export. Cách phân vai này tránh thêm NoSQL quá sớm, nhưng vẫn có chỗ mở rộng khi volume và truy vấn thay đổi.

**Ý cần nhấn:** PostgreSQL là source of truth; Redis không thay database.

**Chuyển slide:** Với hệ thống chống scam, kiến trúc dữ liệu phải đi cùng trust-by-design.

## Slide 8 - Trust-by-design

**Mục tiêu:** Chứng minh privacy và security nằm trong kiến trúc.  
**Thời lượng:** 75 giây

**Script:**

Mobile MVP chỉ scan dữ liệu người dùng chủ động gửi. Ứng dụng không đọc ngầm SMS, call log, notification hoặc clipboard nền. Hệ thống không yêu cầu OTP, mật khẩu, PIN hay số thẻ.

Ở API, nhóm dùng HTTPS, JWT, RBAC, rate limit, idempotency và audit log. Điểm rủi ro phải đi kèm evidence để người dùng hiểu cảnh báo. URL Scanner có một ranh giới bảo mật riêng: chỉ cho HTTP/HTTPS, chặn private IP và metadata endpoint, kiểm tra lại DNS sau redirect, giới hạn timeout, port, response size và redirect count.

**Ý cần nhấn:** Một sản phẩm chống scam chỉ đáng tin khi chính nó hạn chế thu thập và giải thích được quyết định.

**Chuyển slide:** Các nguyên tắc này được giữ nhất quán từ local đến production.

## Slide 9 - Một mô hình triển khai, hai môi trường

**Mục tiêu:** Trình bày local và production mà không phóng đại vận hành.  
**Thời lượng:** 60 giây

**Script:**

Local dùng Docker Compose cho web, API, worker, PostgreSQL, Redis, RabbitMQ và MinIO. Khi cần, nhóm chỉ chạy infrastructure để phát triển từng module.

Production có thể đặt trên AWS EC2 hoặc VPS. Traffic public đi qua Cloudflare Tunnel và Nginx/TLS tới web hoặc API. Worker và data services ở network nội bộ; PostgreSQL, Redis, RabbitMQ và MinIO không public trực tiếp. Backup, health check và log rotation là trách nhiệm vận hành tối thiểu.

**Ý cần nhấn:** Cùng container model giúp giảm khác biệt môi trường, nhưng production có biên bảo mật chặt hơn.

**Chuyển slide:** Kiến trúc chỉ khả thi khi cách chia người khớp với ranh giới module.

## Slide 10 - Bốn người, bốn vertical slice

**Mục tiêu:** Chứng minh tính khả thi và ownership end-to-end.  
**Thời lượng:** 65 giây

**Script:**

Nhóm không chia cứng thành frontend, backend và database. Mỗi thành viên sở hữu một vertical slice. Người thứ nhất làm Auth/User, RBAC và audit. Người thứ hai làm URL, HTML/Form và SSRF guard. Người thứ ba làm Text, Phone/Bank, QR và Rule Engine. Người thứ tư làm Community Report, Admin và dashboard.

Mỗi slice gồm UI, API, data/cache/event, test và một flow demo độc lập. Contract chung như error format, event naming, migration và risk result được thống nhất trước để các slice ghép lại được.

**Ý cần nhấn:** Mỗi người đều có đầu ra end-to-end có thể đánh giá.

**Chuyển slide:** Sau MVP, hệ thống chỉ mở rộng khi có điều kiện rõ.

## Slide 11 - Roadmap theo bằng chứng

**Mục tiêu:** Phân biệt cam kết MVP và hướng Future.  
**Thời lượng:** 60 giây

**Script:**

Phase 1 hoàn thiện auth, rule engine, các flow scan chính, community report và Docker Compose local. Phase 2 làm async worker đầy đủ hơn, retry/dead-letter, report export, ingestion và notification.

Phase 3 mới cân nhắc tách microservice, dùng Kafka hoặc thêm machine learning. Điều kiện là có tải cần scale, ownership riêng, event cần replay hoặc dataset và baseline đủ tốt. Như vậy roadmap thể hiện khả năng mở rộng nhưng không đưa chi phí tương lai vào MVP quá sớm.

**Ý cần nhấn:** Kafka, microservices và ML đều là Future, có điều kiện vào rõ ràng.

**Chuyển slide:** Từ toàn bộ phân tích, nhóm chốt ba giá trị của kiến trúc.

## Slide 12 - Cân bằng để đi được đường dài

**Mục tiêu:** Kết luận và mở phần trao đổi.  
**Thời lượng:** 35 giây

**Script:**

Kiến trúc đề xuất cân bằng ba mục tiêu. Thứ nhất là khả thi cho nhóm bốn người. Thứ hai là đáng tin nhờ privacy, security và evidence giải thích được. Thứ ba là mở rộng được nhờ module boundary và event-driven workers. Nhóm giữ MVP đủ gọn để làm và demo, đồng thời đặt sẵn điều kiện để kiến trúc lớn lên khi có bằng chứng.

**Ý cần nhấn:** Khả thi, đáng tin và mở rộng có điều kiện.

## Câu hỏi dự kiến

### 1. Vì sao không dùng full microservices ngay?

Vì nhóm bốn người sẽ phải dành nhiều thời gian cho deploy, tracing, contract versioning, retry và lỗi phân tán trước khi chứng minh giá trị nghiệp vụ. Modular Monolith vẫn có module boundary; worker xử lý được tác vụ chậm. Nhóm chỉ tách service khi có nhu cầu scale, ownership hoặc failure isolation thật.

### 2. RabbitMQ và Kafka khác vai trò thế nào?

RabbitMQ phù hợp job queue của MVP: route theo loại job, ack, retry và dead-letter. Kafka phù hợp event streaming dài hạn, nhiều consumer và replay. Hệ thống hiện cần xử lý job hơn là xây event analytics platform, nên RabbitMQ hợp lý hơn.

### 3. Vì sao MVP chưa dùng machine learning?

ML cần dataset đủ lớn, label đáng tin, baseline và metric. Rule-based cho evidence rõ, dễ test và phù hợp dữ liệu hiện tại. ML thuộc Phase 3, có thể xử lý vùng không chắc chắn sau khi rule baseline ổn định.

### 4. Hệ thống bảo vệ quyền riêng tư và chống SSRF thế nào?

Mobile chỉ scan dữ liệu user chủ động gửi, không đọc SMS/call log/notification ngầm. URL Scanner chỉ cho HTTP/HTTPS, chặn private/link-local/metadata IP, kiểm tra DNS sau redirect và giới hạn port, timeout, response size cùng redirect count.

### 5. Kiến trúc có quá nặng với nhóm 4 người không?

MVP có thể chạy một Spring Boot application cùng vài infrastructure container. Worker được thêm dần theo vertical slice; chưa cần vận hành nhiều service độc lập. Docker Compose giữ môi trường thống nhất và mỗi thành viên có một flow end-to-end.

## Checklist diễn tập

- Tổng thời lượng nằm trong 10-15 phút.
- Không đọc nguyên văn slide.
- Ba trong bốn thành viên giải thích được quyết định kiến trúc.
- Mỗi người nêu được demo flow của vertical slice mình.
- Phân biệt rõ MVP và Future trong mọi câu trả lời.
