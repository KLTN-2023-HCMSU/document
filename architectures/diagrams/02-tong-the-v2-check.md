# Đối chiếu sơ đồ tổng thể với thiết kế v3.2

Sơ đồ: [PNG](02-tong-the-v2.png) · [SVG](02-tong-the-v2.svg).

Đã đối chiếu với `../architecture_v3.1.md` (revision **V3.2 — Async AI Alignment**), mục 2.2, 2.4, 3.1, 4.1, 4.11, 5 và 6, cùng `../../specifications/modules_specification.md`. Workspace hiện là repository tài liệu, không có mã nguồn ứng dụng hay cấu hình triển khai để xác nhận hệ thống thực chạy. Kết luận chỉ áp dụng cho tài liệu thiết kế.

**Trạng thái đồng bộ:** luồng AI bất đồng bộ trong ảnh **đã được đồng bộ vào đặc tả** ở V3.2. Mermaid, contract và topology AI trong tài liệu nay khớp với ảnh; không còn đường gọi đồng bộ Web/Text → Python trong luồng scan.

| Nội dung | Thể hiện trong ảnh | Căn cứ |
| --- | --- | --- |
| Client và gateway | Web Next.js, Mobile React Native, Admin Next.js → Nginx → Spring Boot | 2.2, 4.1 |
| Quyền quyết định kết quả | Spring Boot tổng hợp tín hiệu, áp dụng luật, Risk Fusion và tạo kết quả; nhóm thêm auth, validation, orchestration, history, report, admin, notification, audit | 3.1, 4.1 |
| Messaging | Hai luồng riêng: core publish job và consume result; worker nhận job và publish tín hiệu/chỉ dấu; thêm `q.ai.analyze` cho AI task | 2.2, 3.1, 5.1, 5.3, 5.4 |
| Worker phân tích | URL/Web, Text, Entity/Reputation, QR; QR trả chỉ dấu để core tạo scan con | 2.2, 4.1, 4.5 |
| AI/ML | Web/Text gửi job AI qua RabbitMQ; worker AI/LLM Python xử lý riêng và trả tín hiệu qua hàng đợi kết quả về Spring Boot | v3.2 mục 2.4, 4.11, 5.4; M11 |
| Tra cứu uy tín | Chỉ lõi tra. Pre-enrich trước khi giao việc; chỉ dấu worker trả về thì lõi tra hộ | 2.2, 3.1, 4.1, 4.6.1 |
| Cô lập worker | Không có mũi tên đồng bộ nào từ nhóm worker về lõi | 2.2, 4.6.1, 7 |
| Xuất báo cáo | RabbitMQ → Report Export Worker → MinIO/S3 | 2.2, 3.1, 4.1 |
| Lưu trữ | Core truy cập PostgreSQL, Redis và MinIO/S3; các kho không nối tuần tự với nhau | 2.2, 6 |
| Threat ingestion | Feed/dataset → ingestion → PostgreSQL upsert → refresh/invalidate Redis | 2.2, 4.6, 6.3 |

## Phạm vi giản lược

- Mũi tên nối vào khung nhóm biểu diễn các kết nối của thành phần được ghi nhãn, không ngụ ý có thêm một service trung gian hoặc các worker chạy chung process.
- Nhãn dọc "Worker chỉ nói chuyện với RabbitMQ" là một ràng buộc kiến trúc, không phải một thành phần. Nó kiểm chứng được ở tầng network policy.
- RabbitMQ chỉ hiện nhóm queue cho scan/AI/result/export. `q.notification`, `q.threat.ingest`, exchange/routing key, retry và DLQ chi tiết vẫn tra ở mục 5; ảnh không phải đặc tả đầy đủ topology.
- Luồng AI thay đường gọi đồng bộ Web/Text → Python bằng job qua RabbitMQ. Spring Boot theo dõi tác vụ AI đang chờ, ghép theo `scanId`/`taskId` và xử lý thời hạn; không chốt scan chỉ vì Web/Text đã xong. Các điểm trước đây còn treo nay đã có đặc tả:

| Điểm cần chốt | Đã đặc tả ở |
| --- | --- |
| Đăng ký tác vụ AI trước khi đóng scan | `pendingAiTasks[]` trong worker result — v3.2 mục 5.3; barrier ở mục 4.7.1; M12 |
| Bàn giao job tin cậy | Contract AI task/result — v3.2 mục 5.4; quy tắc "mỗi task sinh đúng một result" ở M11 |
| Chống trùng | Idempotent theo `eventId` và `taskId` ở cả hai phía — mục 5.4; M11 mục 11 |
| Retry | Retry nội bộ trong AI Worker + `q.ai.analyze.dlq` — mục 5.0, 4.11 |
| Kết quả đến muộn | `handleAiResult()` mục 4.7, bảng race ở 4.7.1: park nếu tới sớm, ghi late signal nếu scan đã đóng |
- Worker AI vẫn có thể phải chờ API LLM; hàng đợi tách việc chờ khỏi Web/Text, không tự loại bỏ timeout. Giới hạn đồng thời (`prefetch_count`) và thời gian chờ được quản lý riêng tại worker AI, tách khỏi `aiTaskDeadline` mà Orchestrator dùng — xem v3.2 mục 7.1.
- TLS/REST là luồng request đại diện; không vẽ mọi response và các luồng notification, giám sát, triển khai.
- Spring Boot sở hữu trạng thái nghiệp vụ/kết quả scan. Ingestion vẫn ghi dữ liệu threat; export vẫn ghi artifact. Vì vậy không dùng chú thích tuyệt đối “chỉ Spring Boot ghi mọi dữ liệu”.

## Điểm thiết kế đã chốt

Trước đây V3 cho Web/Entity worker gọi ngược API của core để tra uy tín, mâu thuẫn với yêu cầu cô lập worker ở mục 7 — nêu trong `../architecture_v3_review.md` mục B2.2.

**Nhóm đã chốt: cô lập worker.** Đường gọi đồng bộ ngược từ worker về lõi bị bỏ hoàn toàn, và ảnh đã được vẽ lại theo quyết định này.

| Trước | Sau |
| --- | --- |
| Worker gọi internal threat-lookup endpoint của core | Worker chỉ nói chuyện với RabbitMQ |
| Worker tự tra uy tín cho chỉ dấu phát hiện giữa chừng | Worker trả `derivedIndicators[]` kèm `handling`; lõi tra hộ |
| `REPUTATION_UNAVAILABLE` phát ra từ worker | Signal này chỉ còn do lõi phát ra |

Cơ chế thay thế nằm ở `../architecture_v3.1.md` mục 4.6.1. Điểm đáng chú ý: `handling=REPUTATION_ONLY` được lõi tra ngay trong lúc xử lý result event, không tạo scan con, nên bỏ đường gọi ngược không làm scan chậm đi đáng kể.

Với điểm này đóng lại, **không còn điểm thiết kế nào treo** trong phạm vi sơ đồ tổng thể.

## Sửa lỗi trình bày

- Tách AI, xuất báo cáo và ingestion thành các khu vực riêng; không còn khối đè lên nhau.
- Khôi phục đủ QR, Admin, MinIO/S3 và phần tiêu đề/chú thích bị mất do bản PNG cũ bị crop.
- Xuất PNG trực tiếp từ SVG bằng Chrome ở tỉ lệ 2×: 4400 × 2840 px, không dùng thumbnail vuông của Quick Look rồi cắt ảnh.
