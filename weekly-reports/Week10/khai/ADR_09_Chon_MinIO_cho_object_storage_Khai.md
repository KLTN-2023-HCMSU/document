# ADR-09 · MinIO cho object storage của runtime

- Ngày: 17/10/2026.
- Owner: Khải; reviewer dự kiến: Thắng (chưa có review).
- Trạng thái: triển khai theo quyết định chuyển sang MinIO của nhóm; chưa nghiệm thu staging.
- Thay thế đề xuất SeaweedFS trước đây; giữ file cũ chỉ để truy vết.

W10 INFRA-01 FR01 yêu cầu PostgreSQL, Redis, RabbitMQ và **MinIO** theo Architecture
V3.3. Runtime dùng MinIO; Spring Boot tiếp tục là owner business state, worker không
được truy cập storage để thay thế DB hoặc gọi ngược core.

Local/CI/staging foundation dùng source release MinIO `RELEASE.2025-10-15T17-29-55Z`,
mc `RELEASE.2025-08-13T08-35-41Z`, build thành image tại repo. Nguồn:
[upstream release và hướng dẫn build](https://github.com/minio/minio/releases/tag/RELEASE.2025-10-15T17-29-55Z).
Không dùng container AIStor cần license hoặc đổi sang S3 provider khác dưới tên MinIO.

Bootstrap tạo bucket private `antiscam`, user runtime riêng chỉ có quyền object CRUD/list
trong bucket đó. Root credential chỉ cấp cho server/bootstrap/smoke; secrets không commit.
Endpoint nội bộ `http://object-storage:9000`, console tắt, volume mới `minio-data`.
Volume SeaweedFS cũ không bị xóa hay tái sử dụng; chuyển object qua S3 và kiểm tra dữ liệu
trước khi chủ động xóa volume cũ.

Quy trình boot, kiểm thử quyền/persistence, migration PostgreSQL, queue roundtrip và staging:
[runbook trong code repo](../../../../Scam-Risk-Detector/docs/runtime-w10.md).
Checklist W11 và blocker Guest/M12/Result UI:
[spec hiện hành](../../../../Scam-Risk-Detector/docs/specs/S01_INFRA-01_url_scan_foundation.md).

G2 phải dựa trên kết quả kiểm thử của thay đổi này và review của Thắng; không lấy smoke
SeaweedFS cũ làm bằng chứng MinIO. G3 chỉ ghi khi có deployment và smoke staging thật.
