# Báo cáo tuần W09 — Khải

| Trường | Giá trị |
|---|---|
| **Tuần** | W09 · 27/09–05/10/2026 |
| **Sprint** | MVP · Foundation & Contract Freeze — Timeline V2.1 |
| **Người** | Ninh Văn Khải — URL Detection & Platform / CI-CD (M02, M13) |
| **Giờ làm thực tế** | Chưa ghi nhận — Khải bổ sung |

## 1. Deadline tuần này — đối chiếu timeline

Theo [Timeline V2.1](../../plan/v2.1/Timeline_full_Anti_Scam_2026_2027_V2.1.md) và [task K-MVP01](../../tasks/W09/K-MVP01_Secrets_CI_URL_Worker_Contract.md), hạn **05/10**:

| Mã | Việc theo timeline | Trạng thái | PR |
|---|---|---|---|
| K-MVP01 / K-WP09 | Secrets và sensitive logging baseline | Baseline đã merge; bổ sung kiểm tra runtime trong PR #9 | [#6](https://github.com/KLTN-2023-HCMSU/Scam-Risk-Detector/pull/6), [#9](https://github.com/KLTN-2023-HCMSU/Scam-Risk-Detector/pull/9) |
| K-MVP01 / K-WP11 | CI và merge gate | PR #6/main xanh; CI mở rộng PR #9 xanh 8/8 job, chờ review | #6, #9 |
| K-MVP01 / K-WP08 | URL worker contract | Schema, 5 fixture và test đạt; chờ xác nhận freeze liên thành viên | #6 |

**PR #6 đã merge lúc 20:09 ngày 04/10**, main tại `5cbfbb7` có [CI xanh](https://github.com/KLTN-2023-HCMSU/Scam-Risk-Detector/actions/runs/37204629673). Khải xác nhận đã cấu hình merge gate; API protection/rules trả 403 với quyền kiểm tra hiện tại nên chưa xác minh độc lập enforcement. K-MVP01 còn bước review/freeze contract và nghiệm thu đầy đủ.

## 2. Đã làm gì — mô tả cho người khác hiểu

- **Bảo mật cấu hình và log:** bỏ JWT key/mật khẩu DB mặc định, từ chối key thiếu/ngắn/placeholder, tắt mock auth mặc định, bỏ password chung của tài khoản dev; che credential, OTP và dữ liệu cá nhân trong log/audit.
- **CI bắt buộc:** quét secret trên file và lịch sử Git; `required-ci` từ chối job fail/skip/cancel/thiếu hoặc kết quả không hợp lệ. Bổ sung actionlint và test kiểm tra mọi job đều được nối vào gate; pin Actions/tool, giới hạn quyền và thời gian.
- **Kiểm tra tích hợp thật:** build Docker image API/Python, chạy PostgreSQL/Flyway/Hibernate validate, kiểm tra ghi dữ liệu qua HTTP; DB dừng thì `/ready` trả 503 còn `/health` vẫn 200, DB bật lại thì phục hồi. Stack test dùng credential/cổng/volume riêng, không dùng `.env` dev.
- **Sửa lỗi được CI phát hiện:** route dev khi mock tắt từng trả 500; sửa thành 404 `NOT_FOUND`, thêm 2 test và đồng bộ OpenAPI/web types. Thêm kiểm tra type sinh từ OpenAPI bị lệch; vá Next.js/eslint-config-next lên 16.3.8 và thêm production dependency audit.
- **Nền tảng URL detection:** schema v1.0 và 5 fixture mô tả job/result, signal, derived indicator và pending AI; 11 test kiểm tra roundtrip/invalid payload. Worker không trả final verdict; reputation lookup và `RiskResult` thuộc core. URL scan thực tế, SSRF và RabbitMQ chưa được nghiệm thu trong slice này.

## 3. Đã đọc / review code của ai

| PR đã review | Của ai | Kết luận | Ghi chú |
|---|---|---|---|
| Chưa có evidence review cá nhân trong W09 | — | Chưa xác nhận | Khải bổ sung PR nếu đã review; không suy ra review từ merge/commit |

## 4. Đang bị chặn / đang chặn người khác

| Loại | Nội dung | Với ai | Từ ngày | Cách gỡ |
|---|---|---|---|---|
| Phụ thuộc review | Contract cần xác nhận freeze; CI mở rộng cần review PR #9 | Kiên/Thắng; Hùng review tác động auth/audit | Chưa ghi nhận thời điểm chặn | Review schema/fixtures và PR; consumer có thể dùng fixture trong lúc chờ runtime |
| Thiếu evidence nghiệm thu | Chưa đọc được setting enforcement qua API | Quản trị repo | Kiểm tra lại 05/10 | Lưu bằng chứng rule hoặc PR đỏ bị chặn; main CI xanh đã có |

## 5. Số liệu tuần

| Chỉ số | Giá trị |
|---|---|
| Commit nội dung của Khải | **2** — `63cc335` và `4e92abd` (không tính merge commit) |
| PR mở / đã merge | **2 / 1** — #6 đã merge; #9 đang mở |
| Test thêm mới trong tuần | **32 ca**: 13 Java (gồm tham số hóa), 11 URL contract, 8 gate/workflow; thêm scanner negative control |
| Kiểm tra local bản CI bổ sung | **152 test đạt**: Java 40, Python legacy 25, web 68, URL contract 11, gate/workflow 8 |
| Kiểm tra khác | Docker/PostgreSQL smoke, actionlint, web lint/build/typecheck/type generation và secret scan đạt; production audit 0 finding; OpenAPI hợp lệ với 4 warning |
| GitHub CI | #6/main xanh; [PR #9: 8/8 job đạt](https://github.com/KLTN-2023-HCMSU/Scam-Risk-Detector/actions/runs/37341308147), gồm Docker/PostgreSQL và `required-ci` |
| P0 đóng tuần này / lũy kế | Chưa tổng hợp theo backlog V2; không quy đổi slice thành WP hoàn tất |

Evidence local và CI ngày **05/10**, nguồn tại commit [`4e92abd`](https://github.com/KLTN-2023-HCMSU/Scam-Risk-Detector/commit/4e92abd645b72b4354821f5752e29f6146e2cb15). Python test local dùng 3.14; CI dùng 3.12. Lệnh và giới hạn kiểm chứng nằm trong [security/CI baseline](https://github.com/KLTN-2023-HCMSU/Scam-Risk-Detector/blob/4e92abd645b72b4354821f5752e29f6146e2cb15/docs/security-ci-baseline.md). Kết quả PostgreSQL thật không thay thế kiểm thử RabbitMQ/URL end-to-end.

## 6. Kế hoạch tuần sau

| Mã | Việc | Hạn | Ghi chú |
|---|---|---|---|
| K-MVP01 | Review PR #9, freeze contract và bổ sung evidence enforcement | Ưu tiên đầu W10 | Phần nghiệm thu còn lại của mốc 05/10 |
| K-MVP02 | Platform Runtime + URL Basic Vertical Slice (`K-WP10 + K-WP01`) | 12/10 | URL submit → worker result fixture → RiskResult; phụ thuộc I-MVP01 |

## 7. Rủi ro tự nhận thấy

| Rủi ro | Mức | Dấu hiệu | Đề xuất |
|---|---|---|---|
| Dependency phát triển còn advisory | Cao theo npm audit | Full audit còn 8 entry high; production audit sạch | Theo dõi bản vá tương thích; không coi secret scan là dependency audit |
| Contract/runtime lệch khi tích hợp | Trung bình | Fixture đạt nhưng chưa có worker/broker thật | Review liên owner và thêm integration test cùng implementation W10–W11 |

## 8. Báo cáo tính năng đã nộp tuần này

Chưa đóng trọn source WP. Tài liệu bàn giao: [K-MVP01 spec/evidence](https://github.com/KLTN-2023-HCMSU/Scam-Risk-Detector/blob/4e92abd645b72b4354821f5752e29f6146e2cb15/docs/specs/K-MVP01_security_url_contracts.md), [URL contracts](https://github.com/KLTN-2023-HCMSU/Scam-Risk-Detector/blob/4e92abd645b72b4354821f5752e29f6146e2cb15/contracts/events/README.md), cùng PR #6 và #9.

*Cập nhật: 05/10/2026 · Theo Mẫu 02 — Báo cáo tuần cá nhân · CI bổ sung: `fix/K-MVP01-ci-hardening`, commit `4e92abd`.*
