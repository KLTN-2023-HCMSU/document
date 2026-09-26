# Timeline & Assignment Plan — Anti-Scam Platform V2.1

> **Version:** V2.1  
> **Ngày cập nhật:** 27/09/2026  
> **Phạm vi thay đổi:** chỉ cập nhật **timeline + task assignment / implementation slicing**.  
> **Feature Backlog V2 không thay đổi**; Architecture V3.3, Modules Specification V1.3 và Schema V1.0 Draft tiếp tục là nguồn yêu cầu hiện hành.

## 1. Mục tiêu V2.1

Mốc **30/10/2026** là **MVP feature-complete ở mức demo cơ bản (breadth-complete)**:

- mọi module **M01–M14** đều có capability cơ bản chạy được;
- các luồng chính chạy end-to-end trên staging/VPS;
- không yêu cầu mọi heuristic, UX, admin screen hay edge case phải sâu/hoàn thiện;
- từ tháng 11 trở đi là **mở rộng chiều sâu, hardening, chất lượng, dữ liệu, đánh giá và UX**, không phải bổ sung một capability lớn còn thiếu.

## 2. Nguyên tắc scheduling

1. **Dependency / unblock trước**: contract và shared platform phải có trước consumer.
2. **MVP breadth trước depth**: mỗi capability có một vertical slice cơ bản trước khi làm nâng cao.
3. **Parallelism**: cross-owner dependency chỉ qua port/API/event/fixture đã freeze.
4. **Ownership nguyên vẹn**: không chuyển module giữa người để chữa cháy timeline.
5. **Công bằng**: mỗi thành viên có 4 milestone MVP tích hợp W09–W12; planning points cân bằng.
6. **W13 không nhận feature mới**: chỉ regression, security smoke, deploy rehearsal, demo rehearsal và fix blocker.

## 3. Ownership giữ nguyên

| Thành viên | Workstream | Module sở hữu | MVP planning points |
|---|---|---|---:|
| Hùng | Identity, Community, Notification & Audit | M01, M07, M10, M14 | 60 |
| Khải | URL Detection & Platform / CI-CD | M02, M13 | 60 |
| Kiên | Reputation, Risk & Orchestration | M04, M08, M09, M12 | 60 |
| Thắng | Content, QR, Result & AI | M03, M05, M06, M11 | 60 |

> Planning points chỉ dùng để cân scope V2.1, không thay `Workload Index` của Feature Backlog.

## 4. Dependency map MVP

```text
W09 — CONTRACT / FOUNDATION
Auth + Notification/Audit contracts
URL/Text worker contracts
Scan/Entity/Risk contracts
CI + secrets
        ↓
W10 — CORE VERTICALS
Session/Auth
Runtime + URL basic
Scan lifecycle + Risk core
Text + Result/History
        ↓
W11 — BREADTH
Community + Notification
SSRF + CD + Redirect
ThreatQuery + Phone/Bank
QR/VietQR + Entity extraction
        ↓
W12 — CLOSE THE LOOP
Moderation + Reputation bridge + Audit + Google
URL web-depth baseline
Nested Scan + AI barrier
AI worker/provider + QR nested
        ↓
W13 — RELEASE ONLY
Regression → security smoke → deploy rehearsal → demo rehearsal → v0.1.0
```

## 5. Weekly execution — MVP

### W09 · 27/09–05/10 — Foundation & Contract Freeze

| Owner | Task | Mục tiêu | Source WP | Depends on | Done khi |
|---|---|---|---|---|---|
| **Hùng** | `H-MVP01` | Identity Foundation + Shared Delivery/Audit Contracts | `H-WP01 + H-WP10(contract) + H-WP16(contract)` | TEAM-C01 contract freeze | Register/verify tests green; contract fixtures merged; no downstream module waits for notification/audit implementation. |
| **Khải** | `K-MVP01` | Secrets + CI + URL Worker Contract | `K-WP09 + K-WP11 + K-WP08` | TEAM-C01 contract freeze | Red PR is blocked; main CI green; secret scan clean; URL producer/consumer contract fixture passes. |
| **Kiên** | `I-MVP01` | Scan / Entity / Risk Contracts + Rule Repository Baseline | `I-WP11 + I-WP17 + I-WP07(basic)` | TEAM-C01 contract freeze | Contract tests pass for URL/TEXT/ENTITY/QR; rules can be seeded/read; fake bus accepts dispatch. |
| **Thắng** | `T-MVP01` | Text Normalization + Worker / Result Contracts | `T-WP01 + T-WP07 + T-WP12(contract)` | TEAM-C01 contract freeze | Normalization fixtures pass; text worker contract fixtures pass; UI renders mocked RiskResult. |

**Team gate:** freeze `AccessContext`, Scan/Job/Event envelope, `WorkerResult`, `DerivedIndicator`, `RiskResult`, `ThreatQuery/RiskEvaluationPort`, `NotificationPort`, `AuditPort`; CI required checks phải xanh.

### W10 · 06/10–12/10 — Core Vertical Slices

| Owner | Task | Mục tiêu | Source WP | Depends on | Done khi |
|---|---|---|---|---|---|
| **Hùng** | `H-MVP02` | Session, Guest, RBAC, Basic Profile & Quota | `H-WP03 + H-WP02(basic) + H-WP04(basic) + H-WP05(basic) + H-WP07(basic)` | H-MVP01 | Register→verify→login→refresh→logout E2E; guest scan context works; USER blocked from admin route; profile read/update smoke pass. |
| **Khải** | `K-MVP02` | Platform Runtime + URL Basic Vertical Slice | `K-WP10 + K-WP01` | K-MVP01 + I-MVP01 contracts | docker compose up; readiness green; URL submit→worker result fixture→RiskResult path passes. |
| **Kiên** | `I-MVP02` | Scan Lifecycle + Risk Entity Registry + Risk Core | `I-WP12 + I-WP03(basic) + I-WP05 + I-WP06(basic)` | I-MVP01 | Duplicate event safe; retry/DLQ pass; seeded signals deterministically produce score/level/evidence/recommendation. |
| **Thắng** | `T-MVP02` | Async Text Scan + Result / History / Basic Export | `T-WP02 + T-WP10(basic) + T-WP12 + T-WP14(basic)` | T-MVP01 + I-MVP01 RiskResult contract | Text submit→processing→result; result shows score/evidence/recommendation; history works; one export/download smoke passes. |

**Team gate:** local/runtime stack chạy; Auth, URL, Text, Scan Lifecycle và Risk Core đều có demo path với real implementation hoặc contract fixture đúng boundary.

### W11 · 13/10–19/10 — MVP Breadth Expansion

| Owner | Task | Mục tiêu | Source WP | Depends on | Done khi |
|---|---|---|---|---|---|
| **Hùng** | `H-MVP03` | Community Report Submission + Minimal Notification | `H-WP12(basic) + H-WP10 + H-WP11(basic)` | H-MVP02 + I-MVP01 entity contract | Report is created with evidence; at least one real/minimal notification channel delivers; mocked channel fallback remains available. |
| **Khải** | `K-MVP03` | SSRF Guard + CD + Redirect Baseline | `K-WP02 + K-WP12 + K-WP03(basic)` | K-MVP02 + K-MVP01 CI | SSRF attack fixtures blocked; deploy from version/tag succeeds; migration + health/readiness + smoke pass. |
| **Kiên** | `I-MVP03` | ThreatQuery + Phone / Bank + Threat Intel Basic | `I-WP04 + I-WP15(basic) + I-WP16(basic) + I-WP01/I-WP02(basic)` | I-MVP02 registry + I-MVP01 entity contract | PHONE/BANK/NO_DATA fixtures pass; no worker direct DB/Redis/core call; seeded threat entity affects final RiskResult. |
| **Thắng** | `T-MVP03` | Entity Extraction + QR / VietQR Basic | `T-WP03 + T-WP08(decode/classify basic)` | T-MVP02 + I-MVP01 entity contract | URL/phone/bank extraction fixtures pass; QR URL/VietQR/text fixtures pass; nested intent contract emitted. |

**Team gate:** URL/Text/Phone/Bank/QR/Community đều có luồng cơ bản; staging deploy reproducible; notification tối thiểu chạy.

### W12 · 20/10–26/10 — Close the Loop & Feature Freeze

| Owner | Task | Mục tiêu | Source WP | Depends on | Done khi |
|---|---|---|---|---|---|
| **Hùng** | `H-MVP04` | Moderation + Verified Report Bridge + Audit + Google Login | `H-WP13(basic) + H-WP15 + H-WP16 + H-WP17(basic) + H-WP02(Google basic)` | H-MVP03 + H-MVP02 + I-MVP03 ThreatQuery | Report→moderation→reputation signal flow passes; Google login works; auth/report/moderation/scan audit trace queryable by correlation id. |
| **Khải** | `K-MVP04` | URL Web-Depth Baseline + Demo Entry UX | `K-WP04(basic) + K-WP05(basic) + K-WP14(basic)` | K-MVP03 | URL demo exposes TLS/header + HTML/form signals; keyboard/mobile-width smoke passes; Vietnamese UI baseline works. |
| **Kiên** | `I-MVP04` | Nested Scan + AI Barrier + Final Orchestration | `I-WP13 + I-WP14` | I-MVP02 + T-MVP03 nested contract + T-MVP04 AI contract | QR→child URL/entity end-to-end; AI success/fail/timeout fixtures pass; parent closes only after barrier/deadline. |
| **Thắng** | `T-MVP04` | AI Worker + One Provider + QR Nested Integration | `T-WP15(basic) + T-WP16(basic) + T-WP08(nested integration)` | T-MVP01 AI contract + K-MVP02 runtime/bus + I-MVP02 scan core | AI success + failure cases pass; no direct DB/core call from AI worker; QR nested demo reaches final result. |

**Gate 26/10:** **feature freeze**. Không thêm capability mới sau ngày này.

### W13 · 27/10–02/11 — Release

| Owner | Trách nhiệm release |
|---|---|
| Hùng | Regression Auth/Community/Notification/Audit; fix blocker; demo user/admin flows |
| Khải | Clean deploy from tag; migration; health/readiness; security/runtime smoke; rollback/redeploy rehearsal |
| Kiên | Regression Scan/Risk/Reputation/Phone/Bank/Nested/AI barrier; fix integration blocker |
| Thắng | Regression Text/QR/Result/History/AI; demo UX; fix integration blocker |
| Cả nhóm | 29/10 release candidate; **30/10 v0.1.0 MVP demo/release** |

## 6. MVP coverage bắt buộc trước 30/10

| Module | Capability demo tối thiểu |
|---|---|
| **M01** | Account & Identity: Register/verify; login/session/refresh/logout; Guest; basic profile/RBAC/quota; Google login basic. |
| **M02** | URL & Website Risk Scan: URL intake/normalize/cache; SSRF-safe fetch; redirect; TLS/header; HTML/form basic signals. |
| **M03** | Text & Transaction Scam Analysis: Vietnamese normalization; async scan; scam patterns; entity extraction. |
| **M04** | Phone & Bank Reputation Check: PHONE/BANK entity endpoints; normalize; reputation/NO_DATA; final RiskResult. |
| **M05** | QR / VietQR Scan: Decode/classify; VietQR parse; nested routing to URL/Text/Entity. |
| **M06** | Scan Result, History & Export: Shared RiskResult UI; history list/detail; minimal export/download. |
| **M07** | Community Report & Moderation: Submit report/evidence; moderator approve/reject; verified report updates reputation. |
| **M08** | Threat Intelligence Management: Risk entity registry; ThreatQuery; seeded/imported threat data; basic list/admin path. |
| **M09** | Rule, Risk Policy & Admin Operations: Seeded/versioned rule repository; deterministic rule evaluation; fusion; result composition. |
| **M10** | Notification & User Follow-up: NotificationPort + one minimal real/in-app/console delivery path for required events. |
| **M11** | AI/ML Inference: Async AI worker; one provider/stub; success/fail/timeout; normalized AiResult. |
| **M12** | Shared Scan Platform: Dispatch, lifecycle, retry/idempotency, nested scans, AI completion barrier/degraded result. |
| **M13** | Platform Infrastructure: Secrets, Docker/runtime, CI, CD, migration, readiness/smoke, basic demo UX/a11y/i18n entry. |
| **M14** | Audit & Observability: Security/business audit events and correlation trace for auth/scan/report/moderation. |

## 7. Quy tắc để 4 người độc lập

- W09 freeze contract trước khi code consumer sâu.
- Module khác **không import service/repository nội bộ** của owner khác.
- Khi producer chưa xong, consumer dùng `Fake/Mock/InMemory` adapter theo contract.
- Integration chỉ qua API/port/event/fixture.
- Owner vẫn chịu trách nhiệm end-to-end trong module của mình; không chia lại theo FE/BE/worker.
- Cross-owner blocker quá 1 ngày phải được chuyển thành fixture/mock hoặc quyết định contract, không chờ code thật.

## 8. Post-MVP roadmap — chỉ mở rộng/hardening

| Cycle | Thời gian | Mục tiêu | Hùng | Khải | Kiên | Thắng |
|---|---|---|---|---|---|---|
| W14 | 03/11–09/11 | Depth Expansion 1 | H-WP04 remainder: password lifecycle/privacy | K-WP06: brand impersonation/typosquatting | I-WP01: threat feed ingestion/dataset lifecycle | T-WP04: scam taxonomy/classification |
| W15 | 10/11–16/11 | Depth Expansion 1 | H-WP08: notification center/follow-up UX | K-WP07: web evidence capture/retention | I-WP02: whitelist/blacklist administration | T-WP05: keyword dictionary administration |
| W16 | 17/11–23/11 | Depth Expansion 1 / v0.2.0 | H-WP09 + H-WP11 remainder: delivery reliability/templates/preferences | K-WP13: structured logging/metrics/alerting | I-WP07 remainder: rule admin UI/version activation | T-WP11: history retention/user management |
| W17 | 24/11–30/11 | Depth Expansion 2 | H-WP14: reporter reputation/abuse controls | K-WP14 remainder: a11y/i18n/mobile entry | I-WP08: rule preview/simulation/shadow evaluation | T-WP13: result sharing/external consumption |
| W18 | 01/12–07/12 | Depth Expansion 2 | H-WP18: audit query/operations/investigation UI | K-WP03/04/05 remainder: advanced redirect/TLS/web content | I-WP09: rule version history/rollback | T-WP14 remainder: async export/admin reporting |
| W19 | 08/12–14/12 | Depth Expansion 2 / v0.3.0 | H-WP06: advanced account security | K-WP06/07 hardening + evidence polish | I-WP10: risk/scam operations analytics | T-WP17: AI safety/guardrails/cache options |
| W20 | 15/12–21/12 | Beta Hardening | Auth/community regression + notification hardening | Infra/runtime/security hardening | Risk/reputation load + failure-path hardening | Text/QR/AI regression + UX polish |
| W21 | 22/12–28/12 | Beta Hardening | Buffer / bugfix / docs | Buffer / bugfix / release automation | Buffer / bugfix / data quality | T-WP18: AI evaluation baseline + bugfix |
| W22 | 29/12–04/01 | Beta Hardening | Buffer / integration | Buffer / integration | Buffer / integration | Buffer / integration |
| W23 | 05/01–11/01 | Beta Candidate v0.4.0 | Security/privacy review | Release rehearsal + observability | Risk/reputation regression | AI/QR/text evaluation regression |
| W24 | 12/01–18/01 | Public Beta Prep | Community/moderation UX polish | Production config + dashboards | Threat/risk ops readiness | Result/history/AI UX polish |

Từ W25 trở đi giữ release train theo plan hiện tại: Public Beta cuối 01/2027, feedback/data đến Data Freeze 31/03/2027, research/quality và thesis finalization sau đó.

## 9. Release roadmap V2.1

| Version | Target | Mục tiêu |
|---|---|---|
| `v0.1.0` | **30/10/2026** | **MVP breadth-complete**: M01–M14 có basic demo flow; staging deploy reproducible |
| `v0.2.0` | 20/11/2026 | Depth Expansion 1 |
| `v0.3.0` | 11/12/2026 | Depth Expansion 2 |
| `v0.4.0` | 08/01/2027 | Beta Candidate / hardening |
| `v0.5.0-beta.1` | 22/01/2027 | Public Beta |
| `DATA-FREEZE` | 31/03/2027 | Freeze dataset/feedback chính |
| `v1.0.0-rc1` | 28/05/2027 | Final regression/security/docs |
| `v1.0.0` | 04/06/2027 | Final Product Baseline |

## 10. Nhịp review

- **Thứ 5:** progress + unblock + contract risk.
- **Thứ 7:** internal demo + PR/test review.
- **Thứ 2:** tracker/report update.
- **Trước 30/10:** báo GV hàng tuần; sau MVP chuyển về release/checkpoint cadence.

