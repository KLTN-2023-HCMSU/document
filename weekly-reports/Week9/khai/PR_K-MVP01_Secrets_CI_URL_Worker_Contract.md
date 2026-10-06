# Mô tả Pull Request — K-MVP01

## Mã nhóm tính năng

`K-MVP01` · Secrets + CI + URL Worker Contract (`K-WP09`, `K-WP11`, `K-WP08`). Hồ sơ tóm tắt hai PR code trong W09, đối chiếu ngày 06/10/2026:

| PR | Phạm vi | Commit / trạng thái |
|---|---|---|
| [#6 — baseline](https://github.com/KLTN-2023-HCMSU/Scam-Risk-Detector/pull/6) | Secrets/logging, CI gate, schema/fixtures URL | `63cc335`; merge `5cbfbb7` ngày 04/10 |
| [#9 — CI hardening](https://github.com/KLTN-2023-HCMSU/Scam-Risk-Detector/pull/9) | Workflow/runtime smoke, type drift/audit, dependency patch và disabled route 404 | `4e92abd`; đang mở, [CI 8/8 xanh](https://github.com/KLTN-2023-HCMSU/Scam-Risk-Detector/actions/runs/37341308147) |

## PR này làm gì

Tạo nền tảng bảo mật/CI và URL worker contract để nhóm tích hợp theo V3.3. #9 bổ sung build image, PostgreSQL migration/write/outage smoke, kiểm tra workflow/gate và generated types; vá Next.js 16.3.8 và sửa route dev khi tắt mock trả 404 thay vì 500. Đây là code foundation/contract, chưa có URL fetch/SSRF hay RabbitMQ worker runtime.

## Vì sao làm theo cách này

Fixture offline giúp producer/consumer làm việc độc lập; chỉ core có reputation lookup và final verdict. H2 giữ tốc độ unit test, PostgreSQL smoke kiểm chứng migration/runtime thật. Giữ tên check `required-ci` và các check hiện có để tránh làm lệch setting repository; test đảm bảo job mới không bị bỏ khỏi gate.

## Loại thay đổi

- [x] `feat` — security/config và URL contract baseline (#6)
- [x] `fix` — route mock bị tắt, dependency patch (#9)
- [x] `test` / `chore` / `docs` — CI, integration smoke và tài liệu bàn giao
- [ ] `refactor` — không có đợt tái cấu trúc độc lập

## Cách kiểm chứng

Từ root `Scam-Risk-Detector` tại commit `4e92abd`, dùng Python venv đã kích hoạt; cần Java 17, Node 22 và Docker Compose:

```bash
python -m pip install -r contracts/requirements.txt -r scripts/requirements-ci.txt
python -m unittest discover -s contracts/tests -v
python -m unittest discover -s scripts/tests -v
(cd services/api && ./mvnw -B verify)
python scripts/ci-runtime-smoke.py
bash scripts/scan-secrets.sh
```

Evidence 05/10: 152 test local đạt, Docker/PostgreSQL smoke đạt; run GitHub của #9 xanh toàn bộ. Lệnh web/Python legacy và bảng case đầy đủ nằm trong [BCKT](BCKT_K-MVP01_Secrets_CI_URL_Contract_Khai.md).

## Ảnh hưởng tới người khác

| Ai | Cần làm gì |
|---|---|
| Kiên/Thắng | Review schema, correlation/reputation/derived/pending AI trước freeze; không coi fixture là runtime đã có |
| Hùng/cả nhóm | Tự cấp secret, bật mock chủ động khi cần; bỏ phụ thuộc password seed chung; review 404 và log/audit |
| Người sửa API/CI | Regenerate web types khi đổi OpenAPI; thêm job vào YAML `needs` và gate `REQUIRED` |

## Checklist trước review

- [x] CI #9 xanh; có positive/negative contract, security, gate và runtime tests.
- [x] Secret scan đạt tại evidence; không commit `.env`/credential thật.
- [x] OpenAPI và generated web types cập nhật cùng code; `.env.example` cập nhật trong #6.
- [x] Migration hiện có chạy từ PostgreSQL rỗng; PR không thêm migration/schema nghiệp vụ mới.
- [x] Ghi rõ Python legacy, giới hạn AMQP/SSRF và 8 high dev-tool entries tại audit 05/10.
- [ ] Human approval/team contract freeze: chưa có bằng chứng xác nhận.
- [ ] Merge #9 và evidence độc lập chặn PR đỏ: chưa được xác nhận trong hồ sơ này.

## Báo cáo tính năng

[BCTN_K-MVP01_Secrets_CI_URL_Worker_Contract_Khai.md](BCTN_K-MVP01_Secrets_CI_URL_Worker_Contract_Khai.md) · [Báo cáo tuần](Tuan_W09_Khai.md). Báo cáo bàn giao slice W09; chưa đóng trọn source WP.

## Ảnh chụp màn hình

Không áp dụng — không sửa giao diện sản phẩm; bằng chứng là test, output runtime smoke và CI run liên kết ở trên.
