# Timeline full — Anti-Scam Platform (2026–2027)

**Cập nhật:** 26/09/2026  

**Căn cứ:** Backlog V2 hiện tại (Module → Work Package → Feature/Acceptance Criteria), Architecture V3.3, Modules Specification V1.3 và timeline KLTN ban đầu đã cập nhật lịch bảo vệ.

## 1. Mốc chiến lược

| Mốc | Thời gian | Mục tiêu |
|---|---|---|
| MVP / release đầu tiên | **30/10/2026** | Có vertical slice chạy end-to-end, đủ demo và deploy.
| Release train xuyên suốt | **2–4 tuần / release** | Chỉ release khi có bugfix đáng kể hoặc feature expansion có giá trị.
| Public Beta | **22/01/2027** | Cho user trải nghiệm; bắt đầu lấy feedback, telemetry và data phục vụ đánh giá.
| Beta feedback loop | **02–03/2027** | Mỗi 2–4 tuần có feedback release; ưu tiên bug/UX/detection quality.
| Data & feedback freeze | **31/03/2027** | Chốt data/feedback chính dùng cho phân tích và báo cáo.
| Feature complete candidate | **07/05/2027** | Không còn feature cam kết lớn chưa xong.
| Release Candidate | **28/05/2027** | Feature freeze; chỉ hardening/regression/security.
| Product / experiment freeze | **04–06/06/2027** | Product, data, metrics, evidence và experiment phải xong.
| Nộp bản cuối | **28/06–03/07/2027** | Chỉ còn hoàn thiện báo cáo, slide, video demo, thủ tục.
| Bảo vệ | **19–31/07/2027** | Không phát triển tính năng mới.

## 2. Nhịp release

| Version | Ngày target | Loại | Nội dung |
|---|---|---|---|
| `v0.1.0` | 30/10/2026 | MVP | Vertical slice ổn định: auth/session, text scan, URL scan + SSRF, risk result/history, orchestration nền tảng. |
| `v0.2.0` | 20/11/2026 | Core Expansion 1 | Entity reputation + threat/reputation nền tảng + community report cơ bản. |
| `v0.3.0` | 11/12/2026 | Core Expansion 2 | Moderation, notification nền tảng, QR baseline, rule/threat management mở rộng. |
| `v0.4.0` | 08/01/2027 | Beta Candidate | AI async baseline, export/ops hardening, Google OIDC/profile/security polish. |
| `v0.5.0-beta.1` | 22/01/2027 | Public Beta | Mở beta cho user trải nghiệm; bật feedback/data collection và monitoring. |
| `v0.5.0-beta.2` | 19/02/2027 | Feedback Release 1 | Sửa lỗi/UX và ưu tiên feedback đầu tiên sau Tết. |
| `v0.5.0-beta.3` | 05/03/2027 | Feedback Release 2 | Cải thiện detection/UX/notifications theo feedback + dữ liệu beta. |
| `v0.5.0-beta.4` | 19/03/2027 | Feedback Release 3 | Release feedback cuối trước data freeze. |
| `DATA-FREEZE` | 31/03/2027 | Data & Feedback Freeze | Chốt tập dữ liệu/feedback dùng cho phân tích và báo cáo thực nghiệm. |
| `v0.6.0` | 16/04/2027 | Research/Quality | Cải thiện chất lượng dựa trên phân tích dữ liệu; không làm thay đổi thiết kế cốt lõi. |
| `v0.7.0` | 07/05/2027 | Feature Complete Candidate | Hoàn tất feature đã cam kết; chỉ còn hardening và chốt số liệu. |
| `v1.0.0-rc1` | 28/05/2027 | Release Candidate | Feature freeze; regression + security + deploy + tài liệu. |
| `v1.0.0` | 04/06/2027 | Final Product Baseline | Chốt product/research/evidence trước 06/06; sau đó chỉ critical hotfix. |

**Quy tắc:** release train là cửa sổ, không phải bắt buộc phải ship nếu thay đổi chưa đủ giá trị. Sau `v1.0.0` ngày 04/06 chỉ cho phép critical hotfix.

## 3. Nhịp làm việc cố định trong tuần

- **Thứ 5:** họp tiến độ giữa chu kỳ — unblock, contract change, PR risk.
- **Thứ 7:** demo nội bộ / review những gì đã chạy được, cập nhật tracker.
- **Thứ 2:** chốt tuần, tổng hợp template báo cáo, tick task hoàn thành, xác định blocker.
- **Trưa Thứ 3:** báo cáo với giảng viên. Giai đoạn MVP báo **hàng tuần**; sau MVP mặc định **2 tuần/lần**, nhưng chuyển về hàng tuần ở tuần release/beta/data-freeze/final-freeze.
- Mỗi WP có một owner end-to-end; cross-owner chỉ qua contract/mock/fixture đã freeze.

## 4. Giai đoạn phát triển

| Giai đoạn | Thời gian | Trọng tâm | Exit criteria |
|---|---|---|---|
| P1 · MVP | 27/09–31/10/2026 | Auth/session, URL + SSRF, Text scan, M12 lifecycle, RiskResult/history, core risk evaluation, deploy/stabilize | v0.1.0 chạy end-to-end; P0 demo blocker = 0 |
| P2 · Core Expansion | 01/11–20/12/2026 | Entity reputation, threat intelligence, community report/moderation, QR, notification, rule/admin nền tảng | v0.2 + v0.3; vòng report→moderation→reputation hoạt động |
| P3 · Beta Preparation | 21/12/2026–24/01/2027 | Hardening, AI async baseline, monitoring, profile/Google OIDC, beta instrumentation | v0.5.0-beta.1; có feedback/data collection |
| P4 · Beta Feedback & Data | 25/01–31/03/2027 | Feedback releases, detection quality, UX, dataset/metrics, bugfix | Data freeze 31/03; có dataset/feedback đủ phân tích |
| P5 · Research, Quality & Completion | 01/04–16/05/2027 | Phân tích dữ liệu, quality/security, selected P1/P2, report writing song song | Feature complete candidate 07/05 |
| P6 · Final Product/Data Freeze | 17/05–06/06/2027 | Regression, security review, deploy, metrics cuối, evidence, docs | v1.0.0 + internal completion 06/06 |
| P7 · Thesis Finalization & Defense | 07/06–31/07/2027 | Không thêm feature; báo cáo, phản biện, slide, rehearsal, defense | Nộp đúng hạn và bảo vệ |

## 5. Weekly execution map

| Cycle | Thời gian | Giai đoạn | Hùng | Khải | Kiên | Thắng | Việc lặp lại | Release / mốc | Báo GV |
|---|---|---|---|---|---|---|---|---|---|
| B0 | 27/09–05/10/2026 | P1 · MVP | H-WP03 | K-WP01 | I-WP11 | T-WP01 | Contract test + integration smoke + cập nhật tracker | — | Có |
| W01 | 06/10–12/10/2026 | P1 · MVP | H-WP01 | K-WP02 | I-WP12 | T-WP02 | Contract test + integration smoke + cập nhật tracker | — | Có |
| W02 | 13/10–19/10/2026 | P1 · MVP | H-WP05 | K-WP08 | I-WP05 | T-WP12 | Contract test + integration smoke + cập nhật tracker | — | Có |
| W03 | 20/10–26/10/2026 | P1 · MVP | H-WP07 | K-WP04 | I-WP06 | T-WP10 | Contract test + integration smoke + cập nhật tracker | — | Có |
| W04 | 27/10–02/11/2026 | P2 · Core Expansion | H-WP12 | K-WP05 | I-WP15 | T-WP07 | Contract test + integration smoke + cập nhật tracker | 30/10: v0.1.0 — MVP | Có |
| W05 | 03/11–09/11/2026 | P2 · Core Expansion | H-WP13 | K-WP09 | I-WP04 | T-WP03 | Contract test + integration smoke + cập nhật tracker | 09/11: Đăng ký đề tài KLTN window bắt đầu | Tuỳ chọn |
| W06 | 10/11–16/11/2026 | P2 · Core Expansion | H-WP17 | K-WP10 | I-WP13 | T-WP04 | Contract test + integration smoke + cập nhật tracker | — | Có |
| W07 | 17/11–23/11/2026 | P2 · Core Expansion | H-WP10 | K-WP11 | I-WP14 | T-WP08 | Contract test + integration smoke + cập nhật tracker | 20/11: v0.2.0 — Core Expansion 1 | Tuỳ chọn |
| W08 | 24/11–30/11/2026 | P2 · Core Expansion | H-WP02 | K-WP12 | I-WP01 | T-WP15 | Contract test + integration smoke + cập nhật tracker | — | Có |
| W09 | 01/12–07/12/2026 | P2 · Core Expansion | H-WP04 | K-WP13 | I-WP02 | T-WP16 | Contract test + integration smoke + cập nhật tracker | — | Tuỳ chọn |
| W10 | 08/12–14/12/2026 | P2 · Core Expansion | H-WP11 | K-WP15 | I-WP03 | T-WP18 | Contract test + integration smoke + cập nhật tracker | 11/12: v0.3.0 — Core Expansion 2 | Có |
| W11 | 15/12–21/12/2026 | P3 · Beta Preparation | H-WP09 | K-WP03 | I-WP07 | T-WP11 | Contract test + integration smoke + cập nhật tracker | — | Tuỳ chọn |
| W12 | 22/12–28/12/2026 | P3 · Beta Preparation | Buffer / integration / spillover | Buffer / integration / spillover | Buffer / integration / spillover | Buffer / integration / spillover | Contract test + integration smoke + cập nhật tracker | — | Có |
| W13 | 29/12–04/01/2027 | P3 · Beta Preparation | Buffer / integration / spillover | Buffer / integration / spillover | Buffer / integration / spillover | Buffer / integration / spillover | Contract test + integration smoke + cập nhật tracker | — | Tuỳ chọn |
| W14 | 05/01–11/01/2027 | P3 · Beta Preparation | H-WP14 | K-WP06 | I-WP09 | T-WP14 | Contract test + integration smoke + cập nhật tracker | 08/01: v0.4.0 — Beta Candidate | Có |
| W15 | 12/01–18/01/2027 | P3 · Beta Preparation | H-WP08 | K-WP07 | I-WP08 | T-WP13 | Contract test + integration smoke + cập nhật tracker | — | Tuỳ chọn |
| W16 | 19/01–25/01/2027 | P4 · Beta Feedback & Data | Buffer / integration / spillover | Buffer / integration / spillover | Buffer / integration / spillover | Buffer / integration / spillover | Feedback triage + data quality check + cập nhật issue backlog | 22/01: v0.5.0-beta.1 — Public Beta | Có |
| W17 | 26/01–01/02/2027 | P4 · Beta Feedback & Data | Buffer / integration / spillover | Buffer / integration / spillover | Buffer / integration / spillover | Buffer / integration / spillover | Feedback triage + data quality check + cập nhật issue backlog | — | Tuỳ chọn |
| W18 | 02/02–08/02/2027 | P4 · Beta Feedback & Data | Buffer / integration / spillover | Buffer / integration / spillover | Buffer / integration / spillover | Buffer / integration / spillover | Feedback triage + data quality check + cập nhật issue backlog | — | Có |
| W19 | 09/02–15/02/2027 | P4 · Beta Feedback & Data | Buffer / integration / spillover | Buffer / integration / spillover | Buffer / integration / spillover | Buffer / integration / spillover | Feedback triage + data quality check + cập nhật issue backlog | — | Tuỳ chọn |
| W20 | 16/02–22/02/2027 | P4 · Beta Feedback & Data | H-WP16 | K-WP14 | I-WP10 | T-WP05 | Feedback triage + data quality check + cập nhật issue backlog | 19/02: v0.5.0-beta.2 — Feedback Release 1 | 22/02: Nộp đề cương KLTN window bắt đầu | Có |
| W21 | 23/02–01/03/2027 | P4 · Beta Feedback & Data | H-WP15 | Buffer / integration / spillover | I-WP16 | T-WP06 | Feedback triage + data quality check + cập nhật issue backlog | — | Tuỳ chọn |
| W22 | 02/03–08/03/2027 | P4 · Beta Feedback & Data | H-WP06 | Buffer / integration / spillover | I-WP17 | T-WP17 | Feedback triage + data quality check + cập nhật issue backlog | 05/03: v0.5.0-beta.3 — Feedback Release 2 | Có |
| W23 | 09/03–15/03/2027 | P4 · Beta Feedback & Data | Buffer / integration / spillover | Buffer / integration / spillover | Buffer / integration / spillover | T-WP09 | Feedback triage + data quality check + cập nhật issue backlog | — | Tuỳ chọn |
| W24 | 16/03–22/03/2027 | P4 · Beta Feedback & Data | Buffer / integration / spillover | Buffer / integration / spillover | Buffer / integration / spillover | Buffer / integration / spillover | Feedback triage + data quality check + cập nhật issue backlog | 19/03: v0.5.0-beta.4 — Feedback Release 3 | Có |
| W25 | 23/03–29/03/2027 | P4 · Beta Feedback & Data | Buffer / integration / spillover | Buffer / integration / spillover | Buffer / integration / spillover | Buffer / integration / spillover | Feedback triage + data quality check + cập nhật issue backlog | — | Tuỳ chọn |
| W26 | 30/03–05/04/2027 | P5 · Research, Quality & Completion | Buffer / integration / spillover | Buffer / integration / spillover | Buffer / integration / spillover | Buffer / integration / spillover | Regression/metrics + viết báo cáo song song + evidence capture | 31/03: DATA-FREEZE — Data & Feedback Freeze | Có |
| W27 | 06/04–12/04/2027 | P5 · Research, Quality & Completion | Buffer / integration / spillover | Buffer / integration / spillover | Buffer / integration / spillover | Buffer / integration / spillover | Regression/metrics + viết báo cáo song song + evidence capture | — | Tuỳ chọn |
| W28 | 13/04–19/04/2027 | P5 · Research, Quality & Completion | H-FWP01 | K-FWP01 | Buffer / integration / spillover | Buffer / integration / spillover | Regression/metrics + viết báo cáo song song + evidence capture | 16/04: v0.6.0 — Research/Quality | 19/04: Báo cáo tiến độ / kỳ thi HK2 | Có |
| W29 | 20/04–26/04/2027 | P5 · Research, Quality & Completion | Buffer / integration / spillover | Buffer / integration / spillover | I-FWP01 | T-FWP01 | Regression/metrics + viết báo cáo song song + evidence capture | — | Tuỳ chọn |
| W30 | 27/04–03/05/2027 | P5 · Research, Quality & Completion | Buffer / integration / spillover | Buffer / integration / spillover | Buffer / integration / spillover | Buffer / integration / spillover | Regression/metrics + viết báo cáo song song + evidence capture | — | Có |
| W31 | 04/05–10/05/2027 | P5 · Research, Quality & Completion | Buffer / integration / spillover | Buffer / integration / spillover | Buffer / integration / spillover | Buffer / integration / spillover | Regression/metrics + viết báo cáo song song + evidence capture | 07/05: v0.7.0 — Feature Complete Candidate | Tuỳ chọn |
| W32 | 11/05–17/05/2027 | P6 · Final Product/Data Freeze | H-FWP02 | K-FWP02 | Buffer / integration / spillover | Buffer / integration / spillover | Regression/metrics + viết báo cáo song song + evidence capture | — | Có |
| W33 | 18/05–24/05/2027 | P6 · Final Product/Data Freeze | Buffer / integration / spillover | Buffer / integration / spillover | I-FWP02 | T-FWP02 | Regression/metrics + viết báo cáo song song + evidence capture | — | Có |
| W34 | 25/05–31/05/2027 | P6 · Final Product/Data Freeze | Buffer / integration / spillover | Buffer / integration / spillover | Buffer / integration / spillover | Buffer / integration / spillover | Regression/metrics + viết báo cáo song song + evidence capture | 28/05: v1.0.0-rc1 — Release Candidate | Có |
| W35 | 01/06–07/06/2027 | P7 · Thesis Finalization & Defense | Buffer / integration / spillover | Buffer / integration / spillover | Buffer / integration / spillover | Buffer / integration / spillover | Không thêm feature; chỉ critical hotfix + báo cáo/slide/rehearsal | 04/06: v1.0.0 — Final Product Baseline | 06/06: Internal completion deadline: product + data + experiments complete | Có |
| W36 | 08/06–14/06/2027 | P7 · Thesis Finalization & Defense | Buffer / integration / spillover | Buffer / integration / spillover | Buffer / integration / spillover | Buffer / integration / spillover | Không thêm feature; chỉ critical hotfix + báo cáo/slide/rehearsal | — | Có |
| W37 | 15/06–21/06/2027 | P7 · Thesis Finalization & Defense | Buffer / integration / spillover | Buffer / integration / spillover | Buffer / integration / spillover | Buffer / integration / spillover | Không thêm feature; chỉ critical hotfix + báo cáo/slide/rehearsal | — | Có |
| W38 | 22/06–28/06/2027 | P7 · Thesis Finalization & Defense | Buffer / integration / spillover | Buffer / integration / spillover | Buffer / integration / spillover | Buffer / integration / spillover | Không thêm feature; chỉ critical hotfix + báo cáo/slide/rehearsal | 28/06: Nộp đơn bảo vệ + bản cuối window | Có |
| W39 | 29/06–05/07/2027 | P7 · Thesis Finalization & Defense | Buffer / integration / spillover | Buffer / integration / spillover | Buffer / integration / spillover | Buffer / integration / spillover | Không thêm feature; chỉ critical hotfix + báo cáo/slide/rehearsal | — | Có |
| W40 | 06/07–12/07/2027 | P7 · Thesis Finalization & Defense | Buffer / integration / spillover | Buffer / integration / spillover | Buffer / integration / spillover | Buffer / integration / spillover | Không thêm feature; chỉ critical hotfix + báo cáo/slide/rehearsal | 12/07: Phản biện | Có |
| W41 | 13/07–19/07/2027 | P7 · Thesis Finalization & Defense | Buffer / integration / spillover | Buffer / integration / spillover | Buffer / integration / spillover | Buffer / integration / spillover | Không thêm feature; chỉ critical hotfix + báo cáo/slide/rehearsal | 19/07: Bảo vệ KLTN window bắt đầu | Tuỳ chọn |
| W42 | 20/07–26/07/2027 | P7 · Thesis Finalization & Defense | Buffer / integration / spillover | Buffer / integration / spillover | Buffer / integration / spillover | Buffer / integration / spillover | Không thêm feature; chỉ critical hotfix + báo cáo/slide/rehearsal | — | Có |
| W43 | 27/07–02/08/2027 | P7 · Thesis Finalization & Defense | Buffer / integration / spillover | Buffer / integration / spillover | Buffer / integration / spillover | Buffer / integration / spillover | Không thêm feature; chỉ critical hotfix + báo cáo/slide/rehearsal | — | Tuỳ chọn |

## 6. Phân công chi tiết 2 chu kỳ đầu

> Cách hiểu: chu kỳ đầu bắt đầu từ **Chủ nhật 27/09** và kết thúc ở buổi tổng hợp **Thứ 2 05/10**, để báo giảng viên **Thứ 3 06/10**. Nếu nhóm thực tế có buổi báo cáo ngay 29/09 thì dùng 28/09 như checkpoint ngắn, không đổi scope chính.

| Cycle | Owner | WP | Mục tiêu | Deliverable | Evidence |
|---|---|---|---|---|---|
| B0 (27/09/2026 → 05/10/2026) | Hùng | `H-WP03` | Session & Token Security | auth_session; JWT access 15'; opaque refresh; token hash; rotation/reuse detection; revoke current/all session; tests. | Integration test login→refresh→rotate→reuse=401; DB không lưu raw refresh token. |
| B0 (27/09/2026 → 05/10/2026) | Khải | `K-WP01` | URL Intake, Normalization, Cache & Basic Signals | POST /v1/scans/url contract; URL normalize/hash; cache/result path; basic lexical signals; worker fixture. | 15 URL variants normalize đúng; cache hit/miss test; fixture worker-result pass contract. |
| B0 (27/09/2026 → 05/10/2026) | Kiên | `I-WP11` | Request Controls, Authorization & Scan Dispatch | validate input; guest/user AccessContext; create ScanRequest; dispatch async bằng FakeMessageBus; 202 + scanId. | Contract test 4 scan types; unauthorized/invalid input đúng; dispatch event đúng schema. |
| B0 (27/09/2026 → 05/10/2026) | Thắng | `T-WP01` | Vietnamese Text Normalization & Evasion Handling | normalization pipeline tiếng Việt; obfuscation/evasion fixtures; unit tests; output dùng được bởi text worker. | Bộ fixture scam/benign/evasion chạy xanh; normalization không phá phone/account/url extraction. |
| B0 (27/09/2026 → 05/10/2026) | Cả nhóm | `TEAM-C01` | Contract Freeze v1 + baseline audit | Freeze AccessContext, ScanJobEnvelope, WorkerAnalysisResult, RiskResult, NotificationPort; rà repo để tick phần đã làm trước 27/09. | Contract files merge main; tracker status được cập nhật; smoke test local stack. |
| W01 (06/10/2026 → 12/10/2026) | Hùng | `H-WP01` | Local Registration & Email Verification | register Local; password hash; email normalization/unique; verification challenge; resend rate limit qua MockNotificationPort. | Register/login test; challenge expiry/single-use test; không log password/OTP. |
| W01 (06/10/2026 → 12/10/2026) | Khải | `K-WP02` | Safe Fetch & SSRF Guard | allow http/https; DNS resolve trước connect; private/link-local block; redirect re-check; size/timeout limits; attack tests. | 169.254.169.254/localhost/DNS rebinding/redirect nội bộ đều bị chặn. |
| W01 (06/10/2026 → 12/10/2026) | Kiên | `I-WP12` | Scan Lifecycle, Event Envelope, Retry & Idempotency | PENDING→PROCESSING→COMPLETED/FAILED; event envelope; dedupe eventId; retry/DLQ; no infinite processing. | Race/idempotency/retry test xanh; duplicate event không tạo duplicate state transition. |
| W01 (06/10/2026 → 12/10/2026) | Thắng | `T-WP02` | Async Text Scan, Scam Patterns & User Result UX | POST /v1/scans/text; MESSAGE/TRANSACTION_POST; fake bus worker flow; evidence/result UX theo RiskResult mock. | Submit→202 scanId→PROCESSING→RiskResult fixture; UI render evidence/recommendation. |
| W01 (06/10/2026 → 12/10/2026) | Cả nhóm | `TEAM-C02` | Integration checkpoint 1 | Nối auth context → scan dispatch → text/url worker fixture → RiskResult mock; fix contract mismatches only. | End-to-end smoke test chạy được bằng mock/fake, không cần AI/threat thật. |

## 7. Nguyên tắc tracker

1. **Work Package là đơn vị tick chính**, không tick từng `Fxxx` như task sprint độc lập.
2. `Fxxx` vẫn được giữ ở sheet Work Package Catalog để trace requirement.
3. Task chỉ được tick `☑` khi có evidence: PR/commit/test/demo/link tài liệu.
4. Nếu WP chưa xong cuối cycle: giữ owner, chuyển sang cycle kế tiếp và ghi lý do; không tự ý xé ownership.
5. Future WP đã assign owner nhưng là **Optional / Future**, chỉ kéo vào khi core + report đang đúng tiến độ.
6. Sau 31/03 mọi thay đổi feature phải chứng minh không phá data/report; sau 28/05 chỉ cho bugfix/hardening.
7. Sau 06/06 chỉ critical hotfix; mọi thời gian còn lại dành cho thesis/report/slide/defense.
