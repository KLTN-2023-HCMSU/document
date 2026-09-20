# Đối chiếu sơ đồ tổng thể với thiết kế v3

Sơ đồ: [PNG](02-tong-the-v2.png) · [SVG](02-tong-the-v2.svg).

Đã đối chiếu với `../architecture_v3.md`, mục 2.2, 3.1, 4.1, 5 và 6. Workspace hiện là repository tài liệu, không có mã nguồn ứng dụng hay cấu hình triển khai để xác nhận hệ thống thực chạy. Kết luận chỉ áp dụng cho tài liệu thiết kế. Riêng luồng AI trong ảnh đã được cập nhật theo đề xuất dùng LLM bất đồng bộ; các phần Mermaid, contract và topology AI trong v3 chưa được đồng bộ theo đề xuất này.

| Nội dung | Thể hiện trong ảnh | Căn cứ |
| --- | --- | --- |
| Client và gateway | Web Next.js, Mobile React Native, Admin Next.js → Nginx → Spring Boot | 2.2, 4.1 |
| Quyền quyết định kết quả | Spring Boot tổng hợp tín hiệu, áp dụng luật, Risk Fusion và tạo kết quả; nhóm thêm auth, validation, orchestration, history, report, admin, notification, audit | 3.1, 4.1 |
| Messaging | Hai luồng riêng: core publish job và consume result; worker nhận job và publish tín hiệu/chỉ dấu | 2.2, 3.1, 5.3 |
| Worker phân tích | URL/Web, Text, Entity/Reputation, QR; QR trả chỉ dấu để core tạo scan con | 2.2, 4.1, 4.5 |
| AI/ML | Web/Text gửi job AI qua RabbitMQ; worker AI/LLM Python xử lý riêng và trả tín hiệu qua hàng đợi kết quả về Spring Boot | Đề xuất cập nhật sau v3, chờ đồng bộ đặc tả |
| Tra cứu uy tín | Web/Entity gọi API nội bộ của core; Text tra chỉ dấu khi cần | 2.2, 3.1, 4.1 |
| Xuất báo cáo | RabbitMQ → Report Export Worker → MinIO/S3 | 2.2, 3.1, 4.1 |
| Lưu trữ | Core truy cập PostgreSQL, Redis và MinIO/S3; các kho không nối tuần tự với nhau | 2.2, 6 |
| Threat ingestion | Feed/dataset → ingestion → PostgreSQL upsert → refresh/invalidate Redis | 2.2, 4.6, 6.3 |

## Phạm vi giản lược

- Mũi tên nối vào khung nhóm biểu diễn các kết nối của thành phần được ghi nhãn, không ngụ ý có thêm một service trung gian hoặc các worker chạy chung process.
- RabbitMQ chỉ hiện nhóm queue cho scan/AI/result/export. `q.notification`, `q.threat.ingest`, exchange/routing key, retry và DLQ chi tiết vẫn tra ở mục 5; ảnh không phải đặc tả đầy đủ topology.
- Luồng AI mới thay đường gọi đồng bộ Web/Text → Python bằng job qua RabbitMQ. Spring Boot theo dõi tác vụ AI đang chờ, ghép theo `scanId`/`taskId` và xử lý thời hạn; không chốt scan chỉ vì Web/Text đã xong. Cần đặc tả đăng ký tác vụ AI trước khi đóng scan, bàn giao job tin cậy, chống trùng, retry và kết quả đến muộn trước khi triển khai.
- Worker AI vẫn có thể phải chờ API LLM; hàng đợi tách việc chờ khỏi Web/Text, không tự loại bỏ timeout. Giới hạn đồng thời và thời gian chờ được quản lý riêng tại worker AI.
- TLS/REST là luồng request đại diện; không vẽ mọi response và các luồng notification, giám sát, triển khai.
- Spring Boot sở hữu trạng thái nghiệp vụ/kết quả scan. Ingestion vẫn ghi dữ liệu threat; export vẫn ghi artifact. Vì vậy không dùng chú thích tuyệt đối “chỉ Spring Boot ghi mọi dữ liệu”.

## Điểm thiết kế còn cần chốt

V3 đang cho Web/Entity worker gọi ngược API của core để tra uy tín. `../architecture_v3_review.md`, mục B2.2, đã nêu sự căng thẳng giữa phụ thuộc này và yêu cầu cô lập worker ở mục 7. Ảnh giữ đúng phương án v3 hiện tại, không tự chuyển sang đọc Redis trực tiếp hoặc nhét reputation vào job. Cần thống nhất phương án trước khi xem đây là kiến trúc triển khai đã chốt.

## Sửa lỗi trình bày

- Tách AI, xuất báo cáo và ingestion thành các khu vực riêng; không còn khối đè lên nhau.
- Khôi phục đủ QR, Admin, MinIO/S3 và phần tiêu đề/chú thích bị mất do bản PNG cũ bị crop.
- Xuất PNG trực tiếp từ SVG bằng Chrome ở tỉ lệ 2×: 4400 × 2840 px, không dùng thumbnail vuông của Quick Look rồi cắt ảnh.
