> **Superseded — 17/10/2026:** Nhóm đã chốt dùng MinIO theo INFRA-01 FR01. Đề xuất
> SeaweedFS bên dưới không còn hiệu lực và không được dùng làm hướng triển khai.
> Xem [ADR-09 hiện hành: MinIO](ADR_09_Chon_MinIO_cho_object_storage_Khai.md).

# ADR-09 · Chọn SeaweedFS cho object storage của runtime

| Trường | Giá trị |
|---|---|
| **Người đề xuất** | Khải |
| **Ngày** | 2026-10-14 |
| **Trạng thái** | **Đề xuất** — local Compose đã dùng SeaweedFS; nhóm chưa duyệt việc thay tên MinIO trong tiêu chí INFRA-01 |
| **Liên quan** | INFRA-01, W10–W11; [Architecture V3.3](../../../architectures/architecture_v3.3.md), [task W10](https://github.com/KLTN-2023-HCMSU/document/blob/76db0a7/tasks/W10/Khai.md), [task W11](https://github.com/KLTN-2023-HCMSU/document/blob/76db0a7/tasks/W11/Khai.md) |
| **Ảnh hưởng** | Người triển khai staging, API/worker dùng object storage, người nghiệm thu INFRA-01 |

## 1. Bối cảnh

Architecture V3.3 mô tả nơi lưu evidence và report là **MinIO / S3-compatible storage**, nhưng bảng công nghệ local/dev và FR01 của W10–W11 ghi đích danh **MinIO**. Compose hiện tại dùng SeaweedFS 4.48 với S3 gateway, volume lưu dữ liệu và credential tách giữa runtime với smoke admin ([PR runtime #15](https://github.com/KLTN-2023-HCMSU/Scam-Risk-Detector/pull/15)). Smoke local đã kiểm tra ký AWS SigV4, tạo bucket, ghi/đọc/xóa object; chưa chứng minh dữ liệu bền sau restart hoặc staging đã sẵn sàng. Cần quyết định rõ sự khác biệt về sản phẩm trước khi nhận INFRA-01 hoàn tất.

## 2. Ràng buộc

| # | Ràng buộc | Nguồn |
|---|---|---|
| 1 | Binary artifact dùng S3 API; PostgreSQL chỉ giữ metadata/object key. | [Architecture V3.3 §6.4](../../../architectures/architecture_v3.3.md) |
| 2 | Local runtime phải khởi động lặp lại được, có smoke; staging dùng secret riêng và kiểm tra kết nối thực tế. | [W10](https://github.com/KLTN-2023-HCMSU/document/blob/76db0a7/tasks/W10/Khai.md), [W11](https://github.com/KLTN-2023-HCMSU/document/blob/76db0a7/tasks/W11/Khai.md) |
| 3 | Quyết định này không tự sửa FR01/checklist ghi MinIO, cũng không tự xác nhận gate G3. | [W10](https://github.com/KLTN-2023-HCMSU/document/blob/76db0a7/tasks/W10/Khai.md), [W11](https://github.com/KLTN-2023-HCMSU/document/blob/76db0a7/tasks/W11/Khai.md) |

## 3. Phương án đã cân nhắc

| Phương án | Lợi ích | Đánh đổi |
|---|---|---|
| **A. MinIO Community** | Khớp tên trong task và bảng local/dev. | [Repository chính đã archive, không còn bảo trì](https://github.com/minio/minio); không phù hợp làm dependency mới cho runtime. |
| **B. MinIO AIStor Free** | Sản phẩm MinIO hiện hành, khớp tên task. | [Cần license hợp lệ để S3 hoạt động; gói Free không cho server-side encryption at rest](https://docs.min.io/aistor/operations/licenses/). Thêm bước cấp và theo dõi license cho cả nhóm. |
| **C. SeaweedFS S3 gateway** | [Dự án Apache-2.0](https://github.com/seaweedfs/seaweedfs) [còn phát hành](https://github.com/seaweedfs/seaweedfs/releases); Compose và smoke S3 hiện có chạy theo phương án này, không cần bước cấp license. | Khác tên MinIO trong task/bảng local; S3-compatible không bảo đảm mọi API/hành vi giống MinIO. Cần kiểm tra đúng tập thao tác ứng dụng sử dụng. |

## 4. Quyết định đề xuất

**Chọn C — SeaweedFS S3 gateway cho local/CI của INFRA-01.** Staging có thể dùng SeaweedFS nếu vượt cùng bộ kiểm tra vận hành; ứng dụng tiếp tục phụ thuộc vào **S3 API**, không phụ thuộc tính năng riêng của SeaweedFS. Lựa chọn này giữ runtime có thể dựng lại và smoke được với release đang bảo trì, tránh phụ thuộc bản MinIO Community đã dừng bảo trì hoặc quy trình license của AIStor Free. Đây là **đề xuất chấp nhận sai khác có chủ đích** so với chữ “MinIO” ở W10–W11 và bảng local/dev của Architecture V3.3; người sở hữu tài liệu cần duyệt rồi sửa các dòng đó thành “S3-compatible object storage (local: SeaweedFS)” trước khi nghiệm thu INFRA-01.

## 5. Hệ quả và điều kiện nghiệm thu

- **Đã có:** Compose pin SeaweedFS 4.48, tách credential runtime/admin và smoke local cho create/put/get/delete. Đây là bằng chứng cho tập thao tác cơ bản, chưa phải bằng chứng G3.
- **Cần bổ sung trước G3:** deploy staging với credential khác dev; kiểm tra quyền runtime không có quyền admin, dữ liệu còn sau restart, backup/restore, health/log và S3 read/write qua cấu hình staging. Lưu log/kết quả smoke và runbook rollback. Nếu ứng dụng thêm multipart upload, presigned URL, lifecycle hoặc mã hóa, phải kiểm tra từng tính năng trên backend đã chọn.
- **Rủi ro:** khác biệt tương thích S3 hoặc mất dữ liệu ở cấu hình một node; giới hạn API bằng contract/smoke, kiểm tra lưu bền và backup/restore. Không suy ra độ bền hoặc tính sẵn sàng cao từ smoke hiện tại.

## 6. Ảnh hưởng và điểm cần chốt

Khải cập nhật task/README sau khi nhóm duyệt. Người triển khai staging cung cấp bằng chứng G3; nhóm API/worker chỉ dùng endpoint và credential S3 từ cấu hình. **Trạng thái duyệt của nhóm: chưa ghi nhận**; ADR này không thay cho quyết định của owner/giảng viên.

## 7. Khi xem lại

Xem lại nếu backend cần một S3 feature SeaweedFS không đáp ứng qua integration test, nếu staging đòi HA/SLA vượt cấu hình một node, hoặc nếu owner yêu cầu MinIO đúng tên sản phẩm. Khi đó đánh giá AIStor với license và yêu cầu bảo mật phù hợp trước khi đổi provider.
