# Timeline full — Anti-Scam Platform (V2 assignment refresh)

**Cập nhật:** 26/09/2026  
**Điểm thay đổi:** Khải nhận lại M13 Platform Infrastructure; CI/CD được kéo lên trước MVP.

## 1. Nguyên tắc scheduling

- Thứ tự ưu tiên: **dependency/unblock → MVP criticality → parallelism → workload balance**.
- CI baseline phải chạy ngay từ B0; CD MVP phải xong **trước 20/10/2026**.
- MVP `v0.1.0` target **30/10/2026**; W04 dùng chủ yếu cho freeze/regression/release rehearsal.
- Sau MVP: release train 2–4 tuần khi có bugfix/feature đủ giá trị.
- Public Beta cuối tháng 1/2027; data freeze 31/03/2027; đầu tháng 6/2027 product/data/experiment phải xong.

## 2. Phân công workstream

| Thành viên | Workstream | Module | Core workload |
|---|---|---|---:|
| Hùng | Identity, Community, Notification & Audit | M01, M07, M10, M14 | 212 |
| Khải | URL Detection & Platform / CI-CD | M02, M13 | 204 |
| Kiên | Reputation, Risk & Orchestration | M04, M08, M09, M12 | 226 |
| Thắng | Content, QR, Result & AI | M03, M05, M06, M11 | 210 |

## 3. MVP infrastructure gates

| Gate | Hạn | Owner | Done khi |
|---|---|---|---|
| CI baseline | **B0 / trước 05/10** | Khải | PR chạy test; test đỏ chặn merge |
| Runtime/topology baseline | **W01 / trước 12/10** | Khải | compose + service readiness + RabbitMQ/Postgres/Redis/MinIO boundaries chạy được |
| CD + migration + image pipeline | **W02 / trước 19/10** | Khải | build/push image; deploy staging/VPS; migrate; health/readiness; smoke pass |
| Release rehearsal | **W03** | Khải + cả nhóm | deploy lại từ version/tag mới không thao tác ad-hoc |
| MVP release | **30/10** | Cả nhóm | regression xanh + deploy reproducible + demo end-to-end |

## 4. Release roadmap

| Version | Target | Loại | Mục tiêu |
|---|---|---|---|
| `v0.1.0` | 30/10/2026 | MVP | Release đầu tiên; CI/CD baseline chạy được; deploy reproducible lên staging/VPS. |
| `v0.2.0` | 20/11/2026 | Core Expansion 1 | Entity/reputation/community và các capability P0/P1 tiếp theo. |
| `v0.3.0` | 11/12/2026 | Core Expansion 2 | Moderation/notification/QR/rule/threat mở rộng. |
| `v0.4.0` | 08/01/2027 | Beta Candidate | Hardening + AI async baseline + observability cần thiết. |
| `v0.5.0-beta.1` | 22/01/2027 | Public Beta | Mở beta, thu feedback/data/telemetry. |
| `v0.5.0-beta.2` | 19/02/2027 | Feedback Release 1 | Bug/UX/detection fixes từ beta. |
| `v0.5.0-beta.3` | 05/03/2027 | Feedback Release 2 | Cải thiện theo feedback/data. |
| `v0.5.0-beta.4` | 19/03/2027 | Feedback Release 3 | Feedback release cuối trước data freeze. |
| `DATA-FREEZE` | 31/03/2027 | Data & Feedback Freeze | Chốt dataset/feedback chính cho báo cáo. |
| `v0.6.0` | 16/04/2027 | Research/Quality | Cải thiện dựa trên phân tích data. |
| `v0.7.0` | 07/05/2027 | Feature Complete Candidate | Hoàn tất feature cam kết. |
| `v1.0.0-rc1` | 28/05/2027 | Release Candidate | Feature freeze; regression/security/deploy/docs. |
| `v1.0.0` | 04/06/2027 | Final Product Baseline | Chốt product/data/evidence trước 06/06. |

## 5. Weekly execution map

| Cycle | Thời gian | Giai đoạn | Hùng | Khải | Kiên | Thắng | Việc lặp lại / gate | Release / mốc | Báo GV |
|---|---|---|---|---|---|---|---|---|---|
| B0 | 27/09–05/10/2026 | P1 · MVP | H-WP03 | K-WP09, K-WP11 | I-WP11 | T-WP01 | Contract/integration smoke + tracker update | — | Có |
| W01 | 06/10–12/10/2026 | P1 · MVP | H-WP01 | K-WP01, K-WP10 | I-WP12 | T-WP02 | Contract/integration smoke + tracker update | — | Có |
| W02 | 13/10–19/10/2026 | P1 · MVP | H-WP05 | K-WP02, K-WP12 | I-WP05, I-WP17 | T-WP12 | Contract/integration smoke + tracker update | — | Có |
| W03 | 20/10–26/10/2026 | P1 · MVP | H-WP02 | K-WP03, K-WP08 | I-WP06, I-WP15 | T-WP08 | Contract/integration smoke + tracker update | — | Có |
| W04 | 27/10–02/11/2026 | P1 · MVP / Release | H-WP07, H-WP16 | K-WP04 | I-WP04, I-WP16 | T-WP07 | MVP freeze + regression + CD rehearsal + smoke test + release v0.1.0 | 30/10: v0.1.0 — MVP | Có |
| W05 | 03/11–09/11/2026 | P2 · Core Expansion | H-WP10 | K-WP05 | I-WP13 | T-WP03 | Contract/integration smoke + tracker update | 09/11: Đăng ký đề tài KLTN window bắt đầu | Tuỳ chọn |
| W06 | 10/11–16/11/2026 | P2 · Core Expansion | H-WP12 | K-WP06 | I-WP14 | T-WP10 | Contract/integration smoke + tracker update | — | Có |
| W07 | 17/11–23/11/2026 | P2 · Core Expansion | H-WP13 | K-WP07 | I-WP03 | T-WP15 | Contract/integration smoke + tracker update | 20/11: v0.2.0 — Core Expansion 1 | Tuỳ chọn |
| W08 | 24/11–30/11/2026 | P2 · Core Expansion | H-WP15 | K-WP13 | I-WP02 | T-WP16 | Contract/integration smoke + tracker update | — | Có |
| W09 | 01/12–07/12/2026 | P2 · Core Expansion | H-WP04 | K-WP14 | I-WP01 | T-WP11 | Contract/integration smoke + tracker update | — | Tuỳ chọn |
| W10 | 08/12–14/12/2026 | P2 · Core Expansion | H-WP11 | Buffer / integration / spillover | I-WP07 | T-WP04 | Contract/integration smoke + tracker update | 11/12: v0.3.0 — Core Expansion 2 | Có |
| W11 | 15/12–21/12/2026 | P3 · Beta Preparation | H-WP09 | Buffer / integration / spillover | I-WP09 | T-WP05 | Contract/integration smoke + tracker update | — | Tuỳ chọn |
| W12 | 22/12–28/12/2026 | P3 · Beta Preparation | Buffer / integration / spillover | Buffer / integration / spillover | Buffer / integration / spillover | Buffer / integration / spillover | Contract/integration smoke + tracker update | — | Có |
| W13 | 29/12–04/01/2027 | P3 · Beta Preparation | Buffer / integration / spillover | Buffer / integration / spillover | Buffer / integration / spillover | Buffer / integration / spillover | Contract/integration smoke + tracker update | — | Tuỳ chọn |
| W14 | 05/01–11/01/2027 | P3 · Beta Preparation | H-WP14 | Buffer / integration / spillover | I-WP08 | T-WP18 | Contract/integration smoke + tracker update | 08/01: v0.4.0 — Beta Candidate | Có |
| W15 | 12/01–18/01/2027 | P3 · Beta Preparation | H-WP17 | Buffer / integration / spillover | I-WP10 | T-WP14 | Contract/integration smoke + tracker update | — | Tuỳ chọn |
| W16 | 19/01–25/01/2027 | P4 · Beta Feedback & Data | Buffer / integration / spillover | Buffer / integration / spillover | Buffer / integration / spillover | Buffer / integration / spillover | Feedback triage + data quality + issue backlog | 22/01: v0.5.0-beta.1 — Public Beta | Có |
| W17 | 26/01–01/02/2027 | P4 · Beta Feedback & Data | Buffer / integration / spillover | Buffer / integration / spillover | Buffer / integration / spillover | Buffer / integration / spillover | Feedback triage + data quality + issue backlog | — | Tuỳ chọn |
| W18 | 02/02–08/02/2027 | P4 · Beta Feedback & Data | Buffer / integration / spillover | Buffer / integration / spillover | Buffer / integration / spillover | Buffer / integration / spillover | Feedback triage + data quality + issue backlog | — | Có |
| W19 | 09/02–15/02/2027 | P4 · Beta Feedback & Data | Buffer / integration / spillover | Buffer / integration / spillover | Buffer / integration / spillover | Buffer / integration / spillover | Feedback triage + data quality + issue backlog | — | Tuỳ chọn |
| W20 | 16/02–22/02/2027 | P4 · Beta Feedback & Data | H-WP18 | Buffer / integration / spillover | Buffer / integration / spillover | T-WP13 | Feedback triage + data quality + issue backlog | 19/02: v0.5.0-beta.2 — Feedback Release 1 | 22/02: Nộp đề cương KLTN window bắt đầu | Có |
| W21 | 23/02–01/03/2027 | P4 · Beta Feedback & Data | H-WP08 | Buffer / integration / spillover | Buffer / integration / spillover | T-WP06 | Feedback triage + data quality + issue backlog | — | Tuỳ chọn |
| W22 | 02/03–08/03/2027 | P4 · Beta Feedback & Data | H-WP06 | Buffer / integration / spillover | Buffer / integration / spillover | T-WP17 | Feedback triage + data quality + issue backlog | 05/03: v0.5.0-beta.3 — Feedback Release 2 | Có |
| W23 | 09/03–15/03/2027 | P4 · Beta Feedback & Data | Buffer / integration / spillover | Buffer / integration / spillover | Buffer / integration / spillover | T-WP09 | Feedback triage + data quality + issue backlog | — | Tuỳ chọn |
| W24 | 16/03–22/03/2027 | P4 · Beta Feedback & Data | Buffer / integration / spillover | Buffer / integration / spillover | Buffer / integration / spillover | Buffer / integration / spillover | Feedback triage + data quality + issue backlog | 19/03: v0.5.0-beta.4 — Feedback Release 3 | Có |
| W25 | 23/03–29/03/2027 | P4 · Beta Feedback & Data | Buffer / integration / spillover | Buffer / integration / spillover | Buffer / integration / spillover | Buffer / integration / spillover | Feedback triage + data quality + issue backlog | — | Tuỳ chọn |
| W26 | 30/03–05/04/2027 | P5 · Research, Quality & Completion | Buffer / integration / spillover | Buffer / integration / spillover | Buffer / integration / spillover | Buffer / integration / spillover | Regression/metrics + report writing + evidence capture | 31/03: DATA-FREEZE — Data & Feedback Freeze | Có |
| W27 | 06/04–12/04/2027 | P5 · Research, Quality & Completion | Buffer / integration / spillover | Buffer / integration / spillover | Buffer / integration / spillover | Buffer / integration / spillover | Regression/metrics + report writing + evidence capture | — | Tuỳ chọn |
| W28 | 13/04–19/04/2027 | P5 · Research, Quality & Completion | H-FWP01 | K-FWP01 | Buffer / integration / spillover | Buffer / integration / spillover | Regression/metrics + report writing + evidence capture | 16/04: v0.6.0 — Research/Quality | 19/04: Báo cáo tiến độ / kỳ thi HK2 | Có |
| W29 | 20/04–26/04/2027 | P5 · Research, Quality & Completion | Buffer / integration / spillover | Buffer / integration / spillover | I-FWP01 | T-FWP01 | Regression/metrics + report writing + evidence capture | — | Tuỳ chọn |
| W30 | 27/04–03/05/2027 | P5 · Research, Quality & Completion | Buffer / integration / spillover | Buffer / integration / spillover | Buffer / integration / spillover | Buffer / integration / spillover | Regression/metrics + report writing + evidence capture | — | Có |
| W31 | 04/05–10/05/2027 | P5 · Research, Quality & Completion | Buffer / integration / spillover | K-FWP02 | Buffer / integration / spillover | Buffer / integration / spillover | Regression/metrics + report writing + evidence capture | 07/05: v0.7.0 — Feature Complete Candidate | Tuỳ chọn |
| W32 | 11/05–17/05/2027 | P6 · Final Product/Data Freeze | H-FWP02 | Buffer / integration / spillover | Buffer / integration / spillover | Buffer / integration / spillover | Regression/metrics + report writing + evidence capture | — | Có |
| W33 | 18/05–24/05/2027 | P6 · Final Product/Data Freeze | Buffer / integration / spillover | K-FWP03 | Buffer / integration / spillover | T-FWP02 | Regression/metrics + report writing + evidence capture | — | Có |
| W34 | 25/05–31/05/2027 | P6 · Final Product/Data Freeze | Buffer / integration / spillover | Buffer / integration / spillover | Buffer / integration / spillover | Buffer / integration / spillover | Regression/metrics + report writing + evidence capture | 28/05: v1.0.0-rc1 — Release Candidate | Có |
| W35 | 01/06–07/06/2027 | P7 · Thesis Finalization & Defense | Buffer / integration / spillover | Buffer / integration / spillover | Buffer / integration / spillover | Buffer / integration / spillover | Critical hotfix only + report/slide/rehearsal | 04/06: v1.0.0 — Final Product Baseline | 06/06: Internal completion: product + data + experiments | Có |
| W36 | 08/06–14/06/2027 | P7 · Thesis Finalization & Defense | Buffer / integration / spillover | Buffer / integration / spillover | Buffer / integration / spillover | Buffer / integration / spillover | Critical hotfix only + report/slide/rehearsal | — | Có |
| W37 | 15/06–21/06/2027 | P7 · Thesis Finalization & Defense | Buffer / integration / spillover | Buffer / integration / spillover | Buffer / integration / spillover | Buffer / integration / spillover | Critical hotfix only + report/slide/rehearsal | — | Có |
| W38 | 22/06–28/06/2027 | P7 · Thesis Finalization & Defense | Buffer / integration / spillover | Buffer / integration / spillover | Buffer / integration / spillover | Buffer / integration / spillover | Critical hotfix only + report/slide/rehearsal | 28/06: Nộp đơn bảo vệ + bản cuối window | Có |
| W39 | 29/06–05/07/2027 | P7 · Thesis Finalization & Defense | Buffer / integration / spillover | Buffer / integration / spillover | Buffer / integration / spillover | Buffer / integration / spillover | Critical hotfix only + report/slide/rehearsal | — | Có |
| W40 | 06/07–12/07/2027 | P7 · Thesis Finalization & Defense | Buffer / integration / spillover | Buffer / integration / spillover | Buffer / integration / spillover | Buffer / integration / spillover | Critical hotfix only + report/slide/rehearsal | 12/07: Phản biện | Có |
| W41 | 13/07–19/07/2027 | P7 · Thesis Finalization & Defense | Buffer / integration / spillover | Buffer / integration / spillover | Buffer / integration / spillover | Buffer / integration / spillover | Critical hotfix only + report/slide/rehearsal | 19/07: Bảo vệ KLTN window bắt đầu | Tuỳ chọn |
| W42 | 20/07–26/07/2027 | P7 · Thesis Finalization & Defense | Buffer / integration / spillover | Buffer / integration / spillover | Buffer / integration / spillover | Buffer / integration / spillover | Critical hotfix only + report/slide/rehearsal | — | Có |
| W43 | 27/07–02/08/2027 | P7 · Thesis Finalization & Defense | Buffer / integration / spillover | Buffer / integration / spillover | Buffer / integration / spillover | Buffer / integration / spillover | Critical hotfix only + report/slide/rehearsal | — | Tuỳ chọn |

## 6. Nhịp họp cố định

- **Thứ 5:** progress/unblock/contract risk.
- **Thứ 7:** demo nội bộ + PR/test review.
- **Thứ 2:** tổng hợp tuần, tick tracker, chuẩn bị template báo cáo.
- **Trưa Thứ 3:** báo giảng viên; MVP báo hàng tuần, sau MVP mặc định 2 tuần/lần và tăng tần suất ở release/freeze.

## 7. Lưu ý

- `K-WP11` là CI; không chờ tới Beta mới làm.
- `K-WP12` là CD/migration/release automation; phải xong trước release MVP.
- Mọi module khác vẫn phải phát triển bằng mock/fixture nếu môi trường thật đang thay đổi.
- Future WP có owner nhưng không được kéo dependency ngược vào MVP.
