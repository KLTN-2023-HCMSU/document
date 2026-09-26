# Danh sách tính năng toàn hệ thống — Anti-Scam Platform

> **Backlog revision:** V2 — đồng bộ theo **Architecture V3.3**, **Modules Specification V1.3** và **Schema V1.0 (Draft)**.  
> **Ngày cập nhật:** 2026-09-26.  
> **Source of truth:** `architecture_v3.3.md` → `modules_specification_v1.3.md` → `anti_scam_schema_overall_v1.0.md` → backlog này.  
> **Nguyên tắc ownership:** `M01–M14` vẫn là vertical module boundary; **Work Package (WP)** là đơn vị giao việc/phát triển chính; các `Mxx-Fxxx` là feature/acceptance criteria để trace requirement. Mọi feature, kể cả `FUTURE`, đều có owner hiện tại. `Legacy/Source` và `Legacy owner` chỉ dùng để truy vết tài liệu cũ.

## 1. Cách dùng backlog

- Tổng cộng **422 feature** sau khi rebase V2; `P0=164`, `P1=135`, `P2=123`.
- `P0` = MVP bắt buộc; `P1` = nên có nếu kịp; `P2` = mở rộng/hướng phát triển.
- Action traceability hiện tại: `KEEP=259`, `MODIFY=72`, `ADD=54`, `FUTURE_SCOPE=37`.
- `KEEP` = semantics cũ vẫn phù hợp; `MODIFY` = giữ ý tưởng nhưng contract/ownership/runtime đã đổi; `ADD` = bổ sung do Architecture/Module Spec hiện tại yêu cầu; `FUTURE_SCOPE` = không thuộc MVP.
- Khi backlog mâu thuẫn với Architecture V3.3 hoặc Modules Specification V1.3, **hai tài liệu đó thắng**.
- Guest được phép scan URL/Text/Phone/Bank/QR mà không bắt buộc account; persistent history và account follow-up chỉ dành cho authenticated user.

### Những thay đổi quan trọng của V2 so với danh sách cũ

- Rebase toàn bộ backlog từ 48 nhóm theo người sang **14 module M01–M14** theo vertical ownership.
- Phân công lại ngày 26/09: trả **Platform Infrastructure/CI/CD (M13) về Khải** theo ownership ban đầu; điều chỉnh M04/M07/M14 để workload vẫn cân bằng và giữ dependency nội bộ tối đa.
- V2 hiện dùng tầng **Module → Work Package → Feature/Acceptance Criteria → implementation subtask**; không dùng từng dòng `Fxxx` như một task sprint độc lập.
- Chốt M01: Local + Google OIDC, email/password OTP challenge, **JWT access token ngắn hạn + opaque rotating refresh token + server-side `auth_sessions`**, refresh-token reuse detection và session revoke.
- Bỏ `username` khỏi MVP; profile dùng `email/fullName/displayName/dateOfBirth/avatarUrl/emailVerified`.
- Chốt M10 notification channel/provider agnostic: `IN_APP`, `EMAIL`, optional `PUSH`; future SMS/Telegram/WhatsApp/Zalo/Discord qua adapter/config.
- Giữ nguyên scan/AI pipeline: scanner/AI worker chỉ giao tiếp qua RabbitMQ; Spring Boot sở hữu business state và final verdict.
- M11 provider-agnostic; `providerKey`/model chỉ là trace/runtime metadata, downstream không hard-code business rule theo provider.
- Bổ sung Future Scope commercialization: plan/entitlement, subscription, usage, credit ledger/reservation và user-facing API credentials.

## 2. Module catalog hiện tại

| Module | Tên | Type | Actor | Feature | P0 | P1 | P2 |
|---|---|---|---|---:|---:|---:|---:|
| **M01** | Account & Identity | End-to-End Feature | Guest, User, Admin | 41 | 22 | 13 | 6 |
| **M02** | URL & Website Risk Scan | End-to-End Feature | Guest, User | 56 | 23 | 20 | 13 |
| **M03** | Text & Transaction Scam Analysis | End-to-End Feature | Guest, User | 48 | 18 | 16 | 14 |
| **M04** | Phone & Bank Reputation Check | End-to-End Feature | Guest, User | 18 | 8 | 6 | 4 |
| **M05** | QR / VietQR Scan | End-to-End Feature | Guest, User | 10 | 7 | 1 | 2 |
| **M06** | Scan Result, History & Export | End-to-End Feature | Guest, User; Admin theo quyền | 21 | 7 | 9 | 5 |
| **M07** | Community Report & Moderation | End-to-End Feature | User, Moderator/Admin | 26 | 9 | 9 | 8 |
| **M08** | Threat Intelligence Management | Admin/Internal Feature | Admin, Spring Boot scan core | 27 | 8 | 11 | 8 |
| **M09** | Rule, Risk Policy & Admin Operations | Shared/Admin Feature | Admin, All Scan Modules | 39 | 10 | 17 | 12 |
| **M10** | Notification & User Follow-up | End-to-End Feature / Shared Delivery Capability | User, Admin; internal producers M01/M06/M07/M12/M14 | 18 | 4 | 10 | 4 |
| **M11** | AI/ML Inference | Shared Capability | M02, M03 | 20 | 7 | 7 | 6 |
| **M12** | Shared Scan Platform | Shared Platform | M02–M06; phối hợp M08–M11/M14 | 19 | 18 | 1 | 0 |
| **M13** | Platform Infrastructure | Infrastructure | Toàn hệ thống | 35 | 18 | 11 | 6 |
| **M14** | Audit & Observability | Shared Platform / Admin Feature | Admin, Internal Operations | 11 | 5 | 4 | 2 |
| **FUTURE** | Product Extensions ngoài M01–M14 | Future Scope | User/Admin/API Client tương lai | 33 | 0 | 0 | 33 |

## 2.1. Phân công 4 thành viên — cân bằng, độc lập và ưu tiên hạ tầng sớm

Vẫn giữ **revision V2**. `M01–M14` là boundary kiến trúc; **Work Package (WP)** là đơn vị giao việc và theo dõi tiến độ. Phân công này ưu tiên thứ tự: **dependency/unblock → MVP criticality → khả năng chạy song song → cân workload**.

> **Thay đổi ownership quan trọng:** Khải nhận lại **M13 Platform Infrastructure**, bao gồm local runtime, secrets, CI, CD, migration, topology và runtime operations. CI baseline phải có ngay từ B0; CD MVP phải hoàn thành trước cửa sổ release 30/10/2026.

> **Quy ước kích thước:** một WP thường tương đương khoảng **2–5 ngày làm việc**; WP hạ tầng có thể chạy xuyên 1–2 tuần nhưng phải có milestone P0 rõ ràng. Implementation subtask chỉ dùng bên trong WP, không phá ownership.

Workload Index = `3×P0 + 2×P1 + P2`; dùng để cân phạm vi, không phải giờ công/story point.

| Thành viên | Workstream | Module sở hữu | WP M01–M14 | Feature M01–M14 | P0 | P1 | P2 | Workload | Future WP | Tổng feature kể cả Future | Workload kể cả Future |
|---|---|---|---:|---:|---:|---:|---:|---:|---:|---:|---:|
| **Hùng** | Identity, Community, Notification & Audit | M01, M07, M10, M14 | 18 | 96 | 40 | 36 | 20 | **212** | 2 | 103 | **219** |
| **Khải** | URL Detection & Platform / CI-CD | M02, M13 | 14 | 91 | 41 | 31 | 19 | **204** | 3 | 103 | **216** |
| **Kiên** | Reputation, Risk & Orchestration | M04, M08, M09, M12 | 17 | 103 | 44 | 35 | 24 | **226** | 1 | 109 | **232** |
| **Thắng** | Content, QR, Result & AI | M03, M05, M06, M11 | 18 | 99 | 39 | 33 | 27 | **210** | 2 | 107 | **218** |

**Độ lệch M01–M14:** workload `204–226`, P0 `39–44`, feature `91–103`. Đây là mức cân bằng tốt hơn nếu ưu tiên giữ module trọn vẹn và giảm cross-owner dependency. **Kể cả Future đã assign:** workload `216–232`, feature `103–109`. Không còn feature nào chưa có owner hiện tại.

### 2.2. Cấu trúc backlog để không xé task quá nhỏ

```text
Module Mxx
  ↓
Work Package (đơn vị giao việc / PR / demo)
  ↓
Feature Fxxx (acceptance criteria / traceability)
  ↓
Implementation subtasks (tạo khi vào sprint nếu cần)
```

- Không giao từng `Mxx-Fxxx` như một task ngang hàng nếu chúng cùng tạo nên một capability hoàn chỉnh.
- Owner của WP chịu trách nhiệm end-to-end cho UI/API/worker/data/test thuộc package đó.
- Một WP chỉ được phụ thuộc cross-owner qua API/port/event/fixture đã freeze; không import service/repository nội bộ của owner khác.
- `FUTURE` đã có owner nghiên cứu từ bây giờ nhưng **không được tính là cam kết MVP** và không được kéo dependency ngược vào M01–M14.

### 2.3. Work Package catalog — đơn vị thực thi chính

| WP | Owner | Module | Work Package | Feature IDs | P0 | P1 | P2 | Workload | Mock / contract boundary |
|---|---|---|---|---|---:|---:|---:|---:|---|
| `H-WP01` | **Hùng** | M01 | Local Registration & Email Verification | M01-F001, M01-F002, M01-F003, M01-F004, M01-F005 | 4 | 1 | 0 | 14 | MockNotificationPort + FakeAccessContext/EntitlementContext; external IdP behind adapter |
| `H-WP02` | **Hùng** | M01 | External Identity, Guest Access & Account Linking | M01-F006, M01-F007, M01-F019, M01-F020 | 3 | 1 | 0 | 11 | MockNotificationPort + FakeAccessContext/EntitlementContext; external IdP behind adapter |
| `H-WP03` | **Hùng** | M01 | Session & Token Security | M01-F008, M01-F009, M01-F010, M01-F011, M01-F012, M01-F013, M01-F014, M01-F041 | 6 | 2 | 0 | 22 | MockNotificationPort + FakeAccessContext/EntitlementContext; external IdP behind adapter |
| `H-WP04` | **Hùng** | M01 | Profile, Password Lifecycle & Privacy | M01-F015, M01-F016, M01-F017, M01-F018, M01-F038, M01-F039, M01-F040 | 3 | 3 | 1 | 16 | MockNotificationPort + FakeAccessContext/EntitlementContext; external IdP behind adapter |
| `H-WP05` | **Hùng** | M01 | RBAC & User Administration | M01-F021, M01-F022, M01-F023, M01-F024, M01-F025, M01-F026, M01-F027 | 3 | 3 | 1 | 16 | MockNotificationPort + FakeAccessContext/EntitlementContext; external IdP behind adapter |
| `H-WP06` | **Hùng** | M01 | Advanced Account Security | M01-F028, M01-F029, M01-F030, M01-F031, M01-F032, M01-F033 | 0 | 3 | 3 | 9 | MockNotificationPort + FakeAccessContext/EntitlementContext; external IdP behind adapter |
| `H-WP07` | **Hùng** | M01 | Abuse Protection & Account Quota | M01-F034, M01-F035, M01-F036, M01-F037 | 3 | 0 | 1 | 10 | MockNotificationPort + FakeAccessContext/EntitlementContext; external IdP behind adapter |
| `H-WP08` | **Hùng** | M10 | Notification Center & User Follow-up UX | M10-F001, M10-F002, M10-F004, M10-F005, M10-F006, M10-F007 | 0 | 3 | 3 | 9 | InMemory/Console providers + MockNotificationPort + notification event fixtures |
| `H-WP09` | **Hùng** | M10 | Notification Delivery Reliability | M10-F003, M10-F009, M10-F017 | 0 | 3 | 0 | 6 | InMemory/Console providers + MockNotificationPort + notification event fixtures |
| `H-WP10` | **Hùng** | M10 | Notification Contract, Routing & Channel Abstraction | M10-F008, M10-F010, M10-F011, M10-F012 | 3 | 1 | 0 | 11 | InMemory/Console providers + MockNotificationPort + notification event fixtures |
| `H-WP11` | **Hùng** | M10 | Notification Endpoints, Preferences, Templates & Secret Safety | M10-F013, M10-F014, M10-F015, M10-F016, M10-F018 | 1 | 3 | 1 | 10 | InMemory/Console providers + MockNotificationPort + notification event fixtures |
| `H-WP12` | **Hùng** | M07 | Community Report Submission & Evidence | M07-F001, M07-F002, M07-F003, M07-F004, M07-F005, M07-F006, M07-F007, M07-F008 | 3 | 3 | 2 | 17 | Seed report fixtures + MockThreatQuery + MockNotificationPort + InMemoryAuditPort |
| `H-WP13` | **Hùng** | M07 | Moderation Queue & Review Workflow | M07-F009, M07-F010, M07-F011, M07-F012, M07-F013, M07-F014, M07-F015, M07-F016, M07-F017 | 4 | 3 | 2 | 20 | Seed report fixtures + MockThreatQuery + MockNotificationPort + InMemoryAuditPort |
| `H-WP14` | **Hùng** | M07 | Reporter Reputation & Abuse Controls | M07-F018, M07-F019, M07-F020, M07-F021, M07-F022, M07-F023, M07-F024 | 0 | 3 | 4 | 10 | Seed report fixtures + MockThreatQuery + MockNotificationPort + InMemoryAuditPort |
| `H-WP15` | **Hùng** | M07 | Verified Report → Threat/Reputation Signal Bridge | M07-F025, M07-F026 | 2 | 0 | 0 | 6 | Seed report fixtures + MockThreatQuery + MockNotificationPort + InMemoryAuditPort |
| `H-WP16` | **Hùng** | M14 | Security & Business Audit Capture | M14-F001, M14-F002, M14-F003, M14-F011 | 4 | 0 | 0 | 12 | InMemoryAuditPort + mock admin/security events + correlation fixtures |
| `H-WP17` | **Hùng** | M14 | Cross-Pipeline Correlation & Failure Trace | M14-F008, M14-F009 | 1 | 1 | 0 | 5 | InMemoryAuditPort + mock admin/security events + correlation fixtures |
| `H-WP18` | **Hùng** | M14 | Audit Query, Operations UI & Investigation | M14-F004, M14-F005, M14-F006, M14-F007, M14-F010 | 0 | 3 | 2 | 8 | InMemoryAuditPort + mock admin/security events + correlation fixtures |
| `K-WP01` | **Khải** | M02 | URL Intake, Normalization, Cache & Basic Signals | M02-F001, M02-F002, M02-F003, M02-F004, M02-F005, M02-F006, M02-F007, M02-F008 | 5 | 2 | 1 | 20 | FakeMessageBus + MockThreatQuery + RiskResult/worker fixtures; no direct DB/core call from worker |
| `K-WP02` | **Khải** | M02 | Safe Fetch & SSRF Guard | M02-F009, M02-F010, M02-F011, M02-F012, M02-F013, M02-F014, M02-F015, M02-F016 | 5 | 2 | 1 | 20 | FakeMessageBus + MockThreatQuery + RiskResult/worker fixtures; no direct DB/core call from worker |
| `K-WP03` | **Khải** | M02 | Redirect Chain Analysis | M02-F017, M02-F018, M02-F019, M02-F020, M02-F021, M02-F022, M02-F023 | 2 | 3 | 2 | 14 | FakeMessageBus + MockThreatQuery + RiskResult/worker fixtures; no direct DB/core call from worker |
| `K-WP04` | **Khải** | M02 | TLS, Certificate & Security Header Analysis | M02-F024, M02-F025, M02-F026, M02-F027, M02-F028, M02-F029, M02-F030 | 2 | 3 | 2 | 14 | FakeMessageBus + MockThreatQuery + RiskResult/worker fixtures; no direct DB/core call from worker |
| `K-WP05` | **Khải** | M02 | HTML/Form & Web Content Analysis | M02-F031, M02-F032, M02-F033, M02-F034, M02-F035, M02-F036, M02-F037, M02-F038 | 3 | 3 | 2 | 17 | FakeMessageBus + MockThreatQuery + RiskResult/worker fixtures; no direct DB/core call from worker |
| `K-WP06` | **Khải** | M02 | Brand Impersonation & Typosquatting Detection | M02-F039, M02-F040, M02-F041, M02-F042, M02-F043, M02-F044, M02-F045 | 2 | 3 | 2 | 14 | FakeMessageBus + MockThreatQuery + RiskResult/worker fixtures; no direct DB/core call from worker |
| `K-WP07` | **Khải** | M02 | Web Evidence Capture & Retention | M02-F046, M02-F047, M02-F048, M02-F049, M02-F050, M02-F051, M02-F052 | 0 | 4 | 3 | 11 | FakeMessageBus + MockThreatQuery + RiskResult/worker fixtures; no direct DB/core call from worker |
| `K-WP08` | **Khải** | M02 | URL Worker Contract & Reputation Enrichment Boundary | M02-F053, M02-F054, M02-F055, M02-F056 | 4 | 0 | 0 | 12 | FakeMessageBus + MockThreatQuery + RiskResult/worker fixtures; no direct DB/core call from worker |
| `K-WP09` | **Khải** | M13 | Data Privacy & Secret Baseline | M13-F001, M13-F002, M13-F005, M13-F035 | 4 | 0 | 0 | 12 | shared local compose + env/secret templates + CI/CD fixtures; no business logic ownership |
| `K-WP10` | **Khải** | M13 | Platform Runtime, Containers, Core Topology & Readiness | M13-F003, M13-F004, M13-F006, M13-F008, M13-F030, M13-F031, M13-F032, M13-F033, M13-F034 | 8 | 1 | 0 | 26 | shared local compose + env/secret templates + CI/CD fixtures; no business logic ownership |
| `K-WP11` | **Khải** | M13 | CI Baseline & Quality Gates | M13-F012, M13-F013, M13-F014, M13-F015, M13-F017 | 2 | 3 | 0 | 12 | shared local compose + env/secret templates + CI/CD fixtures; no business logic ownership |
| `K-WP12` | **Khải** | M13 | MVP CD, Migration & Release Automation | M13-F007, M13-F009, M13-F011, M13-F020 | 3 | 1 | 0 | 11 | shared local compose + env/secret templates + CI/CD fixtures; no business logic ownership |
| `K-WP13` | **Khải** | M13 | Data Ops, Structured Logging, Metrics & Alerting | M13-F010, M13-F016, M13-F018, M13-F019 | 0 | 2 | 2 | 6 | shared local compose + env/secret templates + CI/CD fixtures; no business logic ownership |
| `K-WP14` | **Khải** | M13 | Accessibility, i18n & Mobile Entry Surfaces | M13-F021, M13-F022, M13-F023, M13-F024, M13-F025, M13-F026, M13-F027, M13-F028, M13-F029 | 1 | 4 | 4 | 15 | shared local compose + env/secret templates + CI/CD fixtures; no business logic ownership |
| `I-WP01` | **Kiên** | M08 | Threat Feed Ingestion & Dataset Lifecycle | M08-F001, M08-F002, M08-F003, M08-F004, M08-F005, M08-F006, M08-F007 | 0 | 4 | 3 | 11 | sample threat feeds + in-memory repository/cache adapter; core-only ThreatQuery contract |
| `I-WP02` | **Kiên** | M08 | Whitelist / Blacklist Administration | M08-F008, M08-F009, M08-F010, M08-F011, M08-F012, M08-F013, M08-F014, M08-F015 | 2 | 4 | 2 | 16 | sample threat feeds + in-memory repository/cache adapter; core-only ThreatQuery contract |
| `I-WP03` | **Kiên** | M08 | Risk Entity Registry & Evidence Consolidation | M08-F016, M08-F017, M08-F018, M08-F019, M08-F020, M08-F021, M08-F022, M08-F023, M08-F024 | 3 | 3 | 3 | 18 | sample threat feeds + in-memory repository/cache adapter; core-only ThreatQuery contract |
| `I-WP04` | **Kiên** | M08 | Core ThreatQuery & Deferred Reputation Enrichment | M08-F025, M08-F026, M08-F027 | 3 | 0 | 0 | 9 | sample threat feeds + in-memory repository/cache adapter; core-only ThreatQuery contract |
| `I-WP05` | **Kiên** | M09 | Rule Evaluation Core & Result Composition | M09-F001, M09-F002, M09-F004, M09-F005, M09-F037 | 5 | 0 | 0 | 15 | UnifiedSignalSet fixtures + deterministic policy fixtures; expose RiskEvaluationPort |
| `I-WP06` | **Kiên** | M09 | Risk Fusion Policy, Thresholds & Missing-Signal Handling | M09-F003, M09-F006, M09-F007, M09-F008, M09-F038, M09-F039 | 3 | 2 | 1 | 14 | UnifiedSignalSet fixtures + deterministic policy fixtures; expose RiskEvaluationPort |
| `I-WP07` | **Kiên** | M09 | Rule Repository & Admin Management | M09-F009, M09-F010, M09-F011, M09-F012, M09-F013, M09-F014, M09-F015, M09-F016 | 2 | 4 | 2 | 16 | UnifiedSignalSet fixtures + deterministic policy fixtures; expose RiskEvaluationPort |
| `I-WP08` | **Kiên** | M09 | Rule Preview, Simulation & Shadow Evaluation | M09-F017, M09-F018, M09-F019, M09-F020, M09-F021, M09-F022 | 0 | 3 | 3 | 9 | UnifiedSignalSet fixtures + deterministic policy fixtures; expose RiskEvaluationPort |
| `I-WP09` | **Kiên** | M09 | Rule Versioning, History & Rollback | M09-F023, M09-F024, M09-F025, M09-F026, M09-F027, M09-F028 | 0 | 3 | 3 | 9 | UnifiedSignalSet fixtures + deterministic policy fixtures; expose RiskEvaluationPort |
| `I-WP10` | **Kiên** | M09 | Risk & Scam Operations Analytics | M09-F029, M09-F030, M09-F031, M09-F032, M09-F033, M09-F034, M09-F035, M09-F036 | 0 | 5 | 3 | 13 | UnifiedSignalSet fixtures + deterministic policy fixtures; expose RiskEvaluationPort |
| `I-WP11` | **Kiên** | M12 | Request Controls, Authorization & Scan Dispatch | M12-F001, M12-F002, M12-F003, M12-F004, M12-F018, M12-F019 | 5 | 1 | 0 | 17 | FakeMessageBus + worker/AI/nested-scan/race fixtures + in-memory repositories |
| `I-WP12` | **Kiên** | M12 | Scan Lifecycle, Event Envelope, Retry & Idempotency | M12-F005, M12-F006, M12-F007, M12-F008, M12-F012 | 5 | 0 | 0 | 15 | FakeMessageBus + worker/AI/nested-scan/race fixtures + in-memory repositories |
| `I-WP13` | **Kiên** | M12 | Nested Scan Orchestration & Derived Indicators | M12-F009, M12-F010, M12-F011, M12-F017 | 4 | 0 | 0 | 12 | FakeMessageBus + worker/AI/nested-scan/race fixtures + in-memory repositories |
| `I-WP14` | **Kiên** | M12 | AI Task Barrier, Deadline & Race Handling | M12-F013, M12-F014, M12-F015, M12-F016 | 4 | 0 | 0 | 12 | FakeMessageBus + worker/AI/nested-scan/race fixtures + in-memory repositories |
| `I-WP15` | **Kiên** | M04 | Phone Reputation Check | M04-F001, M04-F002, M04-F003, M04-F004, M04-F005, M04-F006, M04-F007, M04-F008 | 3 | 3 | 2 | 17 | MockThreatQuery + entity fixtures + fake scan-platform adapter |
| `I-WP16` | **Kiên** | M04 | Bank Account Reputation Check | M04-F009, M04-F010, M04-F011, M04-F012, M04-F013, M04-F014, M04-F015, M04-F016 | 3 | 3 | 2 | 17 | MockThreatQuery + entity fixtures + fake scan-platform adapter |
| `I-WP17` | **Kiên** | M04 | Unified Entity Scan Contract & NO_DATA Semantics | M04-F017, M04-F018 | 2 | 0 | 0 | 6 | MockThreatQuery + entity fixtures + fake scan-platform adapter |
| `T-WP01` | **Thắng** | M03 | Vietnamese Text Normalization & Evasion Handling | M03-F001, M03-F002, M03-F003, M03-F004, M03-F005, M03-F006, M03-F007 | 3 | 2 | 2 | 15 | FakeMessageBus + text/AI/derived-indicator fixtures + MockRiskEvaluationPort |
| `T-WP02` | **Thắng** | M03 | Async Text Scan, Scam Patterns & User Result UX | M03-F008, M03-F009, M03-F010, M03-F011, M03-F012, M03-F013, M03-F014, M03-F015 | 4 | 2 | 2 | 18 | FakeMessageBus + text/AI/derived-indicator fixtures + MockRiskEvaluationPort |
| `T-WP03` | **Thắng** | M03 | Entity Extraction from Text | M03-F016, M03-F017, M03-F018, M03-F019, M03-F020, M03-F021, M03-F022, M03-F023 | 5 | 2 | 1 | 20 | FakeMessageBus + text/AI/derived-indicator fixtures + MockRiskEvaluationPort |
| `T-WP04` | **Thắng** | M03 | Scam Taxonomy & User-facing Classification | M03-F024, M03-F025, M03-F026, M03-F027, M03-F028, M03-F029, M03-F030 | 1 | 3 | 3 | 12 | FakeMessageBus + text/AI/derived-indicator fixtures + MockRiskEvaluationPort |
| `T-WP05` | **Thắng** | M03 | Keyword Dictionary Administration | M03-F031, M03-F032, M03-F033, M03-F034, M03-F035, M03-F036, M03-F037 | 1 | 3 | 3 | 12 | FakeMessageBus + text/AI/derived-indicator fixtures + MockRiskEvaluationPort |
| `T-WP06` | **Thắng** | M03 | Multi-turn Conversation Analysis | M03-F038, M03-F039, M03-F040, M03-F041, M03-F042, M03-F043 | 0 | 3 | 3 | 9 | FakeMessageBus + text/AI/derived-indicator fixtures + MockRiskEvaluationPort |
| `T-WP07` | **Thắng** | M03 | Text Input Privacy & Worker Contract | M03-F044, M03-F045, M03-F046, M03-F047, M03-F048 | 4 | 1 | 0 | 14 | FakeMessageBus + text/AI/derived-indicator fixtures + MockRiskEvaluationPort |
| `T-WP08` | **Thắng** | M05 | QR/VietQR Decode, Classification & Nested Routing | M05-F001, M05-F002, M05-F003, M05-F004, M05-F005, M05-F006, M05-F009, M05-F010 | 7 | 1 | 0 | 23 | QR fixtures + fake scan-platform/child-scan adapter; SSRF handled by M02 contract |
| `T-WP09` | **Thắng** | M05 | Advanced QR Safety Checks | M05-F007, M05-F008 | 0 | 0 | 2 | 2 | QR fixtures + fake scan-platform/child-scan adapter; SSRF handled by M02 contract |
| `T-WP10` | **Thắng** | M06 | History Query, Access Control & Pagination | M06-F003, M06-F004, M06-F005, M06-F006 | 3 | 1 | 0 | 11 | RiskResult/history fixtures + mock object storage/export event + MockNotificationPort |
| `T-WP11` | **Thắng** | M06 | History Retention & User Management | M06-F001, M06-F002, M06-F007, M06-F008, M06-F009, M06-F010 | 1 | 3 | 2 | 11 | RiskResult/history fixtures + mock object storage/export event + MockNotificationPort |
| `T-WP12` | **Thắng** | M06 | RiskResult Contract & Presentation Components | M06-F011, M06-F012, M06-F013, M06-F014, M06-F020 | 3 | 2 | 0 | 13 | RiskResult/history fixtures + mock object storage/export event + MockNotificationPort |
| `T-WP13` | **Thắng** | M06 | Result Sharing & External Consumption | M06-F015, M06-F017, M06-F018 | 0 | 1 | 2 | 4 | RiskResult/history fixtures + mock object storage/export event + MockNotificationPort |
| `T-WP14` | **Thắng** | M06 | Async Export & Admin Reporting | M06-F016, M06-F019, M06-F021 | 0 | 2 | 1 | 5 | RiskResult/history fixtures + mock object storage/export event + MockNotificationPort |
| `T-WP15` | **Thắng** | M11 | AI Task/Result Contract & Worker Core | M11-F001, M11-F002, M11-F003, M11-F004, M11-F005, M11-F006, M11-F017, M11-F018 | 5 | 3 | 0 | 21 | FakeMessageBus + ai request/result fixtures + mock provider adapters |
| `T-WP16` | **Thắng** | M11 | AI Provider Resolver & Adapter Registry | M11-F009, M11-F019, M11-F020 | 1 | 1 | 1 | 6 | FakeMessageBus + ai request/result fixtures + mock provider adapters |
| `T-WP17` | **Thắng** | M11 | AI Safety, Prompt/Output Guardrails & Cache Options | M11-F007, M11-F008 | 0 | 0 | 2 | 2 | FakeMessageBus + ai request/result fixtures + mock provider adapters |
| `T-WP18` | **Thắng** | M11 | AI Evaluation Dataset, Metrics & Calibration | M11-F010, M11-F011, M11-F012, M11-F013, M11-F014, M11-F015, M11-F016 | 1 | 3 | 3 | 12 | FakeMessageBus + ai request/result fixtures + mock provider adapters |
| `H-FWP01` | **Hùng** | FUTURE | Future Entitlements, Plans & Subscription Boundary | FUTURE-F025, FUTURE-F026, FUTURE-F027, FUTURE-F028 | 0 | 0 | 4 | 4 | Build against current public ports/fixtures; must not change MVP contracts until promoted |
| `H-FWP02` | **Hùng** | FUTURE | Community Contribution Gamification | FUTURE-F021, FUTURE-F022, FUTURE-F023 | 0 | 0 | 3 | 3 | Build against current public ports/fixtures; must not change MVP contracts until promoted |
| `K-FWP01` | **Khải** | FUTURE | Browser Extension Protection Surface | FUTURE-F001, FUTURE-F002, FUTURE-F003, FUTURE-F004, FUTURE-F005, FUTURE-F006 | 0 | 0 | 6 | 6 | Build against current public ports/fixtures; must not change MVP contracts until promoted |
| `K-FWP02` | **Khải** | FUTURE | Future Usage, Credit Ledger & API Credentials | FUTURE-F029, FUTURE-F030, FUTURE-F031, FUTURE-F032, FUTURE-F033 | 0 | 0 | 5 | 5 | Build against current public ports/fixtures; must not change MVP contracts until promoted |
| `K-FWP03` | **Khải** | FUTURE | Social Sharing for Learning/Engagement | FUTURE-F024 | 0 | 0 | 1 | 1 | Build against current public ports/fixtures; must not change MVP contracts until promoted |
| `I-FWP01` | **Kiên** | FUTURE | Knowledge Library & Editorial Workflow | FUTURE-F007, FUTURE-F008, FUTURE-F009, FUTURE-F010, FUTURE-F011, FUTURE-F012 | 0 | 0 | 6 | 6 | Build against current public ports/fixtures; must not change MVP contracts until promoted |
| `T-FWP01` | **Thắng** | FUTURE | Scam Q&A Assistant | FUTURE-F013, FUTURE-F014, FUTURE-F015, FUTURE-F016, FUTURE-F017, FUTURE-F018 | 0 | 0 | 6 | 6 | Build against current public ports/fixtures; must not change MVP contracts until promoted |
| `T-FWP02` | **Thắng** | FUTURE | Anti-Scam Quiz & Learning Evaluation | FUTURE-F019, FUTURE-F020 | 0 | 0 | 2 | 2 | Build against current public ports/fixtures; must not change MVP contracts until promoted |

### 2.4. Quy tắc để 4 người phát triển độc lập tối đa

1. **Freeze contract trước implementation.** Chốt `AccessContext`, scan request/response, `ScanJobEnvelope`, `WorkerAnalysisResult`, `DerivedIndicator`, `AiTask/AiResult`, `RiskEvaluationPort`, `RiskResult`, `NotificationPort`, event envelope và routing key trước khi code song song.
2. **Mock-first bắt buộc.** Dependency cross-owner phải có fake/in-memory adapter hoặc fixture ngay từ đầu; Module Spec V1.3 mục 15 là nguồn contract chính.
3. **M12 không được trở thành blocker.** Khải/Thắng phát triển worker bằng `FakeMessageBus` + fixtures; Kiên phát triển orchestration bằng worker/AI fixtures; Hùng phát triển auth/community/notification/audit bằng fake ports.
4. **M13 do Khải sở hữu nhưng không giữ business logic.** Local runtime/CI/CD phải unblock team, còn từng module vẫn test được với test double khi hạ tầng thật chưa sẵn sàng.
5. **CI/CD là enabling capability của MVP, không phải feature sau MVP.** CI P0 phải chạy từ B0; CD P0 phải hoàn thành trước 20/10 để còn thời gian regression/release rehearsal trước 30/10.
6. **Contract test trước integration test.** Chỉ nối môi trường thật khi producer và consumer cùng pass fixture/contract test.
7. **Cross-owner change cần review contract.** Thay đổi schema/event/API dùng chung không được merge chỉ trong một workstream.
8. **Owner giữ vertical scope.** Không tách một WP thành “frontend của A / backend của B”; subtask có thể chia nội bộ nhưng owner chịu trách nhiệm Definition of Done cuối cùng.

### 2.5. Integration seams giữa các thành viên

| Producer / Owner | Contract seam | Consumer / Owner | Cách dev độc lập |
|---|---|---|---|
| Hùng / M01 | `AccessContext`, auth/session contract | Kiên / M12 và scan modules | `FakeAccessContextResolver`, guest/free fixtures |
| Hùng / M07 | verified-report event / moderated evidence | Kiên / M08/M04 | seeded verified-report fixtures; không đọc repository của M07 |
| Hùng / M10 | `NotificationPort` + `notification.requested` | M01/M06/M07/M12/M14 | `MockNotificationPort`, seeded events |
| Hùng / M14 | `AuditPort` / audit event | toàn bộ module | `InMemoryAuditPort`; producer không ghi audit table trực tiếp |
| Khải / M02 | URL worker result / derived indicators | Kiên / M12/M08/M09 | `FakeMessageBus`, worker/result fixtures |
| Khải / M13 | runtime, CI/CD, queues, storage, secret/config topology | toàn team | compose/env templates + CI reusable workflow; không chứa business logic |
| Kiên / M04/M08 | entity/reputation contract, `ThreatQuery`, `ReputationContext` | Khải/Thắng scan modules, M12 | `MockThreatQuery`, reputation fixtures |
| Kiên / M09 | `RiskEvaluationPort` | M02–M06 / M12 | deterministic signal fixtures + `MockRiskEvaluationPort` |
| Kiên / M12 | scan lifecycle + job/result event contracts | Khải/Thắng workers, Hùng/M10 | fake scan-platform adapter + worker/AI fixtures |
| Thắng / M11 | AI task/result contract | Kiên / M12/M09; M02/M03 producer | `ai-*-request/result.json`, mock AI result |


## 3. Contract hiện hành phải phản ánh trong backlog

### 3.1. Release scope

> **MVP release gate 30/10/2026:** CI P0 (`M13-F012`, `M13-F013`) phải hoạt động từ đầu chu kỳ; CD P0 (`M13-F009`, `M13-F011`, `M13-F020`) phải hoàn tất trước 20/10 để còn tối thiểu một tuần regression/release rehearsal.


- Guest: scan public inputs, guest quota, không có persistent account history.
- Authenticated Free: persistent history/profile/session/quota/follow-up.
- Không có payment/subscription/credit wallet/user-facing API key trong MVP.
- CCCD không phải standalone lookup input trong MVP; chỉ phát hiện việc yêu cầu/thu thập CCCD trong Text/Web và mask/redact raw value.

### 3.2. Public scan API

```text
POST /v1/scans/url
POST /v1/scans/text      # contentType = MESSAGE | TRANSACTION_POST
POST /v1/scans/entity    # entityType = PHONE | BANK_ACCOUNT
POST /v1/scans/qr
GET  /v1/scans/{scanId}
GET  /v1/scans/history
POST /v1/reports
```

- `WEB_CONTENT/HTML_FORM` là internal mode của URL scan, không phải public endpoint.
- `DOMAIN` là derived indicator nội bộ.

### 3.3. Identity & auth-session model

```text
Local email/password ─┐
                     ├─> M01 Account & Identity
Google OIDC ──────────┘
                           ↓
                    server-side auth_session
                           ↓
        JWT Access Token (~15 phút, sub/sid/roles/iss/aud/iat/exp)
        + Opaque Refresh Token (~30 ngày, hash + rotate + token family)
```

- Refresh token reuse → revoke token family + session + 401 + security notification.
- PostgreSQL là source of truth cho auth session/refresh metadata; Redis dùng cache/revocation lookup.
- Web: refresh credential qua `HttpOnly + Secure + SameSite` cookie; Mobile: Keychain/Keystore/secure storage.

### 3.4. Notification contract

```text
Producer module
  → notification.requested
  → q.notification
  → Notification Delivery Worker
  → Channel Resolver + Provider Adapter
  → IN_APP / EMAIL / PUSH / future SMS / Telegram / WhatsApp / Zalo / ...
```

- M01 sở hữu OTP/challenge; M10 chỉ delivery.
- `channelKey`, `providerKey`, `templateKey` là extensible config key, không dùng closed DB enum.
- Raw OTP/credential không được persist/log trong canonical notification DB/audit/DLQ.

### 3.5. RabbitMQ / worker isolation / AI

```text
Exchange: antiscan.topic
DLX:      antiscan.dlx

q.scan.url
q.scan.text
q.scan.entity
q.scan.qr
q.ai.analyze
q.scan.result
q.report.export
q.notification
q.threat.ingest
```

- Scanner/AI worker không đọc PostgreSQL/Redis và không gọi Spring Boot API.
- URL Worker là scanner duy nhất có outbound public fetch theo SSRF policy.
- AI task/result bất đồng bộ; mỗi task phải sinh đúng một success/failure result.
- Provider AI được resolve trong M11; AI chỉ trả prediction, Spring Boot/M09 mới tạo verdict.

### 3.6. Risk Fusion

```text
URL:    Technical 35% | Content 15% | Reputation/Community 35% | AI 15%
TEXT:   Technical 10% | Content 45% | Reputation/Community 25% | AI 20%
ENTITY: Reputation/Community 100%
QR:     child scores + QR-specific rules

0–34  SAFE
35–69 CAUTION
70–100 DANGER
```

- Missing signal group được omit + renormalize, không coi missing = 0.
- Verified trusted blacklist có thể hard-override minimum DANGER theo policy; unverified report không được hard-blacklist.

### 3.7. Operational defaults của MVP

| Hạng mục | Default ban đầu |
|---|---:|
| Parent/nested scan deadline | 30 s |
| Max nested depth | 2 |
| Reputation lookup trong core | 500 ms |
| AI task deadline | 20 s |
| AI model/LLM timeout | 10 s |
| AI Worker prefetch | 4 |
| Worker retry | 2 retry sau attempt đầu |
| URL redirect limit | 5 |
| URL fetched body | 5 MB |
| Text input | 20.000 ký tự |
| Result cache TTL | 10 phút |
| Threat cache TTL | theo source; mặc định 1 giờ |

## 4. Feature backlog theo module

## M01. Account & Identity

> **Type:** End-to-End Feature  
> **Actor:** Guest, User, Admin  
> **Dependencies:** M10; M13; M14  
> **Owner:** Hùng — Identity, Community, Notification & Audit.

**Mục đích:** Cung cấp Guest/Local/Google identity, JWT access token + rotating opaque refresh token + server-side auth session, RBAC, profile và AccessContext thống nhất.

**Backlog:** 41 feature — P0 22, P1 13, P2 6.

| ID | Priority | Action | Work Package | Owner hiện tại | Feature hiện tại | Legacy/Source | Legacy owner (trace only) |
|---|---|---|---|---|---|---|---|
| `M01-F001` | P0 | `MODIFY` | `H-WP01` | **Hùng** | Đăng ký Local bằng email + mật khẩu; validate định dạng, độ mạnh mật khẩu và profile tối thiểu. | `H01-01 + V3.3` | Hùng |
| `M01-F002` | P0 | `MODIFY` | `H-WP01` | **Hùng** | Hash mật khẩu Local bằng BCrypt cost phù hợp; password hash chỉ nằm trong `local_credentials`, không ghi log. | `H01-02 + V3.3` | Hùng |
| `M01-F003` | P0 | `MODIFY` | `H-WP01` | **Hùng** | Chuẩn hoá email (trim/lowercase) trước khi lưu/so sánh và enforce UNIQUE trên `users.email`; MVP không có `username`/public handle unique. | `H01-03 + MODSPEC-V1.3` | Hùng |
| `M01-F004` | P0 | `MODIFY` | `H-WP01` | **Hùng** | Xác thực email bằng OTP/challenge do M01 sở hữu: lưu hash, expiry, attempt count và single-use consume; M10 chỉ chịu trách nhiệm delivery. | `H01-04 + V3.3` | Hùng |
| `M01-F005` | P1 | `MODIFY` | `H-WP01` | **Hùng** | Cho phép resend email-verification OTP/challenge với rate limit; challenge cũ được expire/replace theo policy. | `H01-05 + V3.3` | Hùng |
| `M01-F006` | P0 | `MODIFY` | `H-WP02` | **Hùng** | Đăng nhập bằng Google OAuth 2.0 / OpenID Connect qua external identity adapter; định danh bằng `providerKey=google` + Google `sub`, không dùng email làm identity primary. | `H01-06 + V3.3` | Hùng |
| `M01-F007` | P0 | `MODIFY` | `H-WP02` | **Hùng** | Guest được scan URL/Text/Entity/QR mà không cần tài khoản; dùng ephemeral `AccessContext`, quota thấp hơn và không có persistent account history. | `H01-07 + MODSPEC-V1.3` | Hùng |
| `M01-F008` | P0 | `MODIFY` | `H-WP03` | **Hùng** | Sau Local/Google authentication thành công: tạo server-side `auth_session`, cấp JWT access token ngắn hạn (mặc định 15 phút) + opaque refresh token dài hạn (mặc định 30 ngày). | `H02-01 + V3.3` | Hùng |
| `M01-F009` | P0 | `MODIFY` | `H-WP03` | **Hùng** | Refresh token là high-entropy opaque credential; DB chỉ lưu `token_hash`, `session_id`, `token_family_id` và metadata rotation; không persist/log raw refresh token. | `H02-02 + V3.3` | Hùng |
| `M01-F010` | P0 | `MODIFY` | `H-WP03` | **Hùng** | Logout phiên hiện tại revoke `auth_session` và refresh-token family tương ứng; access JWT mang `sid` phải bị từ chối khi session đã revoked/expired. | `H02-03 + V3.3` | Hùng |
| `M01-F011` | P0 | `MODIFY` | `H-WP03` | **Hùng** | Refresh token rotation ở mỗi lần refresh: token cũ được consume/replaced, token mới cùng family được cấp atomically. | `H02-04 + V3.3` | Hùng |
| `M01-F012` | P0 | `MODIFY` | `H-WP03` | **Hùng** | Phát hiện refresh-token reuse: revoke toàn bộ token family + `auth_session`, trả 401 và phát security notification qua M10. | `H02-05 + V3.3` | Hùng |
| `M01-F013` | P1 | `MODIFY` | `H-WP03` | **Hùng** | Đăng xuất khỏi tất cả thiết bị: revoke toàn bộ `auth_sessions` và refresh-token families của user. | `H02-06 + V3.3` | Hùng |
| `M01-F014` | P1 | `MODIFY` | `H-WP03` | **Hùng** | Mỗi login/device có một server-side `auth_session` riêng với lifecycle ACTIVE/REVOKED/EXPIRED; hỗ trợ truy vấn và thu hồi từng phiên. | `H02-07 + V3.3` | Hùng |
| `M01-F015` | P0 | `MODIFY` | `H-WP04` | **Hùng** | Xem hồ sơ gồm email, fullName, displayName, dateOfBirth, avatarUrl, emailVerified và roles; không hiển thị `username` vì MVP không có public handle unique. | `H03-01 + MODSPEC-V1.3` | Hùng |
| `M01-F016` | P0 | `MODIFY` | `H-WP04` | **Hùng** | Đổi mật khẩu: xác minh current password + `PASSWORD_CHANGE` OTP challenge theo policy; sau đổi áp dụng revoke/rotate session và gửi security notification. | `H03-02 + V3.3` | Hùng |
| `M01-F017` | P1 | `MODIFY` | `H-WP04` | **Hùng** | Cập nhật profile `fullName`, `displayName`, `dateOfBirth`, `avatarUrl` và hiển thị trạng thái email verification. | `H03-03 + MODSPEC-V1.3` | Hùng |
| `M01-F018` | P0 | `MODIFY` | `H-WP04` | **Hùng** | Forgot/Reset Password bằng OTP challenge: response trung tính chống account enumeration; reset thành công đổi credential và revoke toàn bộ session/token family theo policy. | `H03-04 + V3.3` | Hùng |
| `M01-F019` | P0 | `MODIFY` | `H-WP02` | **Hùng** | Chuẩn hoá mọi auth method (Local/Google/future provider) thành cùng `AuthResult` + `AccessContext`/`AuthenticatedPrincipal`; downstream không branch theo auth method. | `H03-05 + V3.3` | Hùng |
| `M01-F020` | P1 | `MODIFY` | `H-WP02` | **Hùng** | Account linking có kiểm soát giữa Local credential và external identity; Google-only account có thể không có `local_credentials`. | `H03-06 + V3.3` | Hùng |
| `M01-F021` | P0 | `MODIFY` | `H-WP05` | **Hùng** | RBAC với các role `USER`, `MODERATOR`, `ADMIN`; roles được đưa vào `AccessContext` và có thể phản ánh trong JWT claims. | `H04-01 + MODSPEC-V1.3` | Hùng |
| `M01-F022` | P0 | `MODIFY` | `H-WP05` | **Hùng** | Bảo vệ toàn bộ route/admin operation bằng backend authorization/RBAC; unauthorized trả 401/403 chuẩn hoá. | `H04-02 + MODSPEC-V1.3` | Hùng |
| `M01-F023` | P0 | `MODIFY` | `H-WP05` | **Hùng** | Route guard phía Next.js/Admin Dashboard để ẩn/chặn UI trái quyền; backend vẫn là nguồn quyết định authorization cuối. | `H04-03 + MODSPEC-V1.3` | Hùng |
| `M01-F024` | P1 | `MODIFY` | `H-WP05` | **Hùng** | Vai trò `MODERATOR` được quyền moderation theo policy nhưng không mặc định có quyền sửa rule/risk policy. | `H04-04 + MODSPEC-V1.3` | Hùng |
| `M01-F025` | P1 | `KEEP` | `H-WP05` | **Hùng** | Trang quản lý người dùng cho admin (danh sách, phân trang, lọc theo vai trò/trạng thái) | `H04-05` | Hùng |
| `M01-F026` | P1 | `KEEP` | `H-WP05` | **Hùng** | Khoá / mở khoá tài khoản, kèm lý do | `H04-06` | Hùng |
| `M01-F027` | P2 | `KEEP` | `H-WP05` | **Hùng** | Phân quyền chi tiết theo permission thay vì theo vai trò | `H04-07` | Hùng |
| `M01-F028` | P1 | `KEEP` | `H-WP06` | **Hùng** | Xác thực hai lớp bằng TOTP (Google Authenticator) | `H06-01` | Hùng |
| `M01-F029` | P1 | `MODIFY` | `H-WP06` | **Hùng** | UI/API quản lý danh sách phiên đăng nhập theo thiết bị/login và thu hồi một `auth_session` cụ thể. | `H06-02 + V3.3` | Hùng |
| `M01-F030` | P1 | `MODIFY` | `H-WP06` | **Hùng** | Security notification khi password thay đổi, phát hiện token reuse hoặc sự kiện đăng nhập đáng chú ý; việc delivery thuộc M10. | `H06-03 + V3.3` | Hùng |
| `M01-F031` | P2 | `KEEP` | `H-WP06` | **Hùng** | Mã khôi phục dự phòng khi mất thiết bị 2FA | `H06-04` | Hùng |
| `M01-F032` | P2 | `KEEP` | `H-WP06` | **Hùng** | Đăng nhập sinh trắc học trên mobile (Face ID / vân tay) | `H06-05` | Hùng |
| `M01-F033` | P2 | `KEEP` | `H-WP06` | **Hùng** | Kiểm tra mật khẩu có nằm trong danh sách rò rỉ đã biết (k-anonymity) | `H06-06` | Hùng |
| `M01-F034` | P0 | `KEEP` | `H-WP07` | **Hùng** | Rate limit đăng nhập: 5 lần sai / IP / 15 phút, sau đó khoá 15 phút | `H07-01` | Hùng |
| `M01-F035` | P0 | `KEEP` | `H-WP07` | **Hùng** | Rate limit đăng ký: 5 request / phút / IP | `H07-02` | Hùng |
| `M01-F036` | P0 | `MODIFY` | `H-WP07` | **Hùng** | Business quota theo `AccessContext`: guest quota thấp hơn authenticated user; Nginx chỉ làm coarse IP/flood limiting. | `H07-03 + V3.3` | Hùng |
| `M01-F037` | P2 | `KEEP` | `H-WP07` | **Hùng** | CAPTCHA khi vượt ngưỡng nghi ngờ | `H07-07` | Hùng |
| `M01-F038` | P1 | `KEEP` | `H-WP04` | **Hùng** | Xuất toàn bộ dữ liệu cá nhân theo yêu cầu (data portability) | `H08-05` | Hùng |
| `M01-F039` | P1 | `KEEP` | `H-WP04` | **Hùng** | Xoá tài khoản kèm xoá/ẩn danh toàn bộ dữ liệu liên quan | `H08-06` | Hùng |
| `M01-F040` | P2 | `KEEP` | `H-WP04` | **Hùng** | Trang chính sách quyền riêng tư kèm nhật ký thay đổi | `H08-07` | Hùng |
| `M01-F041` | P0 | `MODIFY` | `H-WP03` | **Hùng** | Token storage client: Web giữ access token ưu tiên trong memory và refresh credential qua `HttpOnly + Secure + SameSite` cookie; Mobile giữ refresh credential trong Keychain/Keystore/secure storage, không dùng localStorage/AsyncStorage thường. | `T11-01 + V3.3` | Thắng |

## M02. URL & Website Risk Scan

> **Type:** End-to-End Feature  
> **Actor:** Guest, User  
> **Dependencies:** M08; M09; M11 optional; M12; M13; M14  
> **Owner:** Khải — URL Detection & Platform / CI-CD.

**Mục đích:** Quét URL/website bằng lexical, DNS/TLS, redirect, HTML/Form, reputation và optional AI; worker chỉ trả signal, Spring Boot mới tạo final RiskResult.

**Backlog:** 56 feature — P0 23, P1 20, P2 13.

| ID | Priority | Action | Work Package | Owner hiện tại | Feature hiện tại | Legacy/Source | Legacy owner (trace only) |
|---|---|---|---|---|---|---|---|
| `M02-F001` | P0 | `MODIFY` | `K-WP01` | **Khải** | `POST /v1/scans/url`: cache hit có thể trả cached `RiskResult`; cache miss tạo `ScanRequest`, publish `scan.url.requested` và trả `202 + scanId`. | `K01-01` | Khải |
| `M02-F002` | P0 | `KEEP` | `K-WP01` | **Khải** | Chuẩn hoá URL: thêm scheme, hạ chữ thường host, bỏ port mặc định, sắp xếp query | `K01-02` | Khải |
| `M02-F003` | P0 | `KEEP` | `K-WP01` | **Khải** | Tách thành phần: scheme, subdomain, domain, TLD, path, query, fragment | `K01-03` | Khải |
| `M02-F004` | P0 | `MODIFY` | `K-WP01` | **Khải** | Cache kết quả URL theo normalized-input hash qua shared result cache; TTL mặc định 10 phút và cấu hình được, PostgreSQL vẫn là source of truth. | `K01-04 + MODSPEC-V1.3` | Khải |
| `M02-F005` | P0 | `KEEP` | `K-WP01` | **Khải** | Phát hiện dấu hiệu cơ bản: dùng IP thay tên miền, URL quá dài, ký tự bất thường, `@` trong host | `K01-05` | Khải |
| `M02-F006` | P1 | `KEEP` | `K-WP01` | **Khải** | Nhận diện dịch vụ rút gọn link và mở rộng về địa chỉ thật | `K01-06` | Khải |
| `M02-F007` | P1 | `KEEP` | `K-WP01` | **Khải** | Trang kết quả chi tiết URL trên web, hiển thị từng bằng chứng | `K01-07` | Khải |
| `M02-F008` | P2 | `KEEP` | `K-WP01` | **Khải** | Quét hàng loạt nhiều URL cùng lúc | `K01-08` | Khải |
| `M02-F009` | P0 | `KEEP` | `K-WP02` | **Khải** | Chặn dải IP nội bộ: `127.0.0.0/8`, `10/8`, `172.16/12`, `192.168/16`, `169.254/16`, `::1`, `fc00::/7` | `K02-01` | Khải |
| `M02-F010` | P0 | `KEEP` | `K-WP02` | **Khải** | Phân giải DNS trước, kiểm tra IP đích, rồi mới kết nối (chống DNS rebinding) | `K02-02` | Khải |
| `M02-F011` | P0 | `KEEP` | `K-WP02` | **Khải** | Chỉ cho phép scheme `http` và `https` | `K02-03` | Khải |
| `M02-F012` | P0 | `KEEP` | `K-WP02` | **Khải** | Giới hạn kích thước phản hồi và thời gian chờ, huỷ khi vượt ngưỡng | `K02-04` | Khải |
| `M02-F013` | P0 | `KEEP` | `K-WP02` | **Khải** | Kiểm tra lại IP đích ở **mỗi** bước chuyển hướng, không chỉ URL đầu tiên | `K02-05` | Khải |
| `M02-F014` | P1 | `KEEP` | `K-WP02` | **Khải** | Chạy fetch bằng user riêng, không có quyền truy cập DB | `K02-06` | Khải |
| `M02-F015` | P1 | `KEEP` | `K-WP02` | **Khải** | Bộ test tấn công: `169.254.169.254`, `localhost`, redirect về IP nội bộ, DNS rebinding | `K02-07` | Khải |
| `M02-F016` | P2 | `KEEP` | `K-WP02` | **Khải** | Tách tầng fetch ra container riêng có network policy giới hạn | `K02-08` | Khải |
| `M02-F017` | P0 | `KEEP` | `K-WP03` | **Khải** | Theo redirect tối đa N bước (mặc định 5), ghi lại toàn bộ chuỗi | `K03-01` | Khải |
| `M02-F018` | P0 | `KEEP` | `K-WP03` | **Khải** | Cảnh báo khi chuỗi quá dài hoặc có vòng lặp | `K03-02` | Khải |
| `M02-F019` | P1 | `KEEP` | `K-WP03` | **Khải** | Phát hiện chuyển hướng đổi tên miền gốc (cross-domain redirect) | `K03-03` | Khải |
| `M02-F020` | P1 | `KEEP` | `K-WP03` | **Khải** | Phát hiện chuyển hướng từ HTTPS xuống HTTP (downgrade) | `K03-04` | Khải |
| `M02-F021` | P1 | `KEEP` | `K-WP03` | **Khải** | Bắt cả chuyển hướng bằng JavaScript và thẻ `<meta refresh>` | `K03-05` | Khải |
| `M02-F022` | P2 | `KEEP` | `K-WP03` | **Khải** | Hiển thị trực quan chuỗi chuyển hướng trên giao diện | `K03-06` | Khải |
| `M02-F023` | P2 | `KEEP` | `K-WP03` | **Khải** | Phát hiện cloaking — trả nội dung khác nhau theo User-Agent | `K03-07` | Khải |
| `M02-F024` | P0 | `KEEP` | `K-WP04` | **Khải** | Kiểm tra có HTTPS hay không | `K04-01` | Khải |
| `M02-F025` | P0 | `KEEP` | `K-WP04` | **Khải** | Kiểm tra chứng chỉ còn hạn và khớp tên miền | `K04-02` | Khải |
| `M02-F026` | P1 | `KEEP` | `K-WP04` | **Khải** | Trích xuất tổ chức phát hành, ngày cấp, ngày hết hạn | `K04-03` | Khải |
| `M02-F027` | P1 | `KEEP` | `K-WP04` | **Khải** | Cảnh báo chứng chỉ tự ký hoặc mới cấp trong vài ngày | `K04-04` | Khải |
| `M02-F028` | P1 | `KEEP` | `K-WP04` | **Khải** | Kiểm tra các HTTP security header (HSTS, CSP, X-Frame-Options) | `K04-05` | Khải |
| `M02-F029` | P2 | `KEEP` | `K-WP04` | **Khải** | Tra Certificate Transparency log để tìm chứng chỉ khả nghi của thương hiệu | `K04-06` | Khải |
| `M02-F030` | P2 | `KEEP` | `K-WP04` | **Khải** | Cảnh báo bộ mã hoá TLS yếu | `K04-07` | Khải |
| `M02-F031` | P0 | `KEEP` | `K-WP05` | **Khải** | Parse HTML bằng Jsoup, trích xuất toàn bộ `<form>` và `<input>` | `K05-01` | Khải |
| `M02-F032` | P0 | `KEEP` | `K-WP05` | **Khải** | Phát hiện input nhạy cảm: password, OTP, PIN, CVV, số thẻ, CCCD, số tài khoản | `K05-02` | Khải |
| `M02-F033` | P0 | `KEEP` | `K-WP05` | **Khải** | Cảnh báo `form action` trỏ sang tên miền khác hoặc không dùng HTTPS | `K05-03` | Khải |
| `M02-F034` | P1 | `KEEP` | `K-WP05` | **Khải** | Tìm từ khoá lừa đảo trong nội dung công khai của trang | `K05-04` | Khải |
| `M02-F035` | P1 | `KEEP` | `K-WP05` | **Khải** | Phát hiện trang sao chép giao diện thương hiệu (logo, favicon, tiêu đề) | `K05-05` | Khải |
| `M02-F036` | P1 | `KEEP` | `K-WP05` | **Khải** | Phát hiện iframe ẩn và input bị che | `K05-06` | Khải |
| `M02-F037` | P2 | `KEEP` | `K-WP05` | **Khải** | So khớp mã nguồn trang với bộ kit phishing đã biết | `K05-07` | Khải |
| `M02-F038` | P2 | `KEEP` | `K-WP05` | **Khải** | Phát hiện obfuscated JavaScript | `K05-08` | Khải |
| `M02-F039` | P0 | `KEEP` | `K-WP06` | **Khải** | So khoảng cách Levenshtein giữa tên miền và danh sách thương hiệu | `K06-01` | Khải |
| `M02-F040` | P0 | `KEEP` | `K-WP06` | **Khải** | Phát hiện tên miền lộ ra thương hiệu sau khi gỡ leetspeak | `K06-02` | Khải |
| `M02-F041` | P1 | `KEEP` | `K-WP06` | **Khải** | Phát hiện ký tự đồng hình Unicode (homoglyph, tên miền IDN) | `K06-03` | Khải |
| `M02-F042` | P1 | `KEEP` | `K-WP06` | **Khải** | Phát hiện thương hiệu nằm ở subdomain (`vietcombank.kẻ-gian.com`) | `K06-04` | Khải |
| `M02-F043` | P1 | `KEEP` | `K-WP06` | **Khải** | Phát hiện thêm/bớt dấu gạch nối, đảo ký tự, thêm từ (`-verify`, `-secure`) | `K06-05` | Khải |
| `M02-F044` | P2 | `KEEP` | `K-WP06` | **Khải** | Quản lý danh sách thương hiệu qua trang admin | `K06-06` | Khải |
| `M02-F045` | P2 | `KEEP` | `K-WP06` | **Khải** | Tra tuổi tên miền qua WHOIS, cảnh báo tên miền mới đăng ký | `K06-07` | Khải |
| `M02-F046` | P1 | `KEEP` | `K-WP07` | **Khải** | Lưu HTML thô của trang tại thời điểm quét làm bằng chứng | `K09-01` | Khải |
| `M02-F047` | P1 | `KEEP` | `K-WP07` | **Khải** | Chụp ảnh màn hình trang bằng headless browser | `K09-02` | Khải |
| `M02-F048` | P1 | `KEEP` | `K-WP07` | **Khải** | Lưu file vào object storage (MinIO / S3-compatible) | `K09-03` | Khải |
| `M02-F049` | P1 | `KEEP` | `K-WP07` | **Khải** | Sinh URL tạm có hạn để xem bằng chứng | `K09-04` | Khải |
| `M02-F050` | P2 | `KEEP` | `K-WP07` | **Khải** | Băm nội dung để chứng minh bằng chứng không bị sửa | `K09-05` | Khải |
| `M02-F051` | P2 | `KEEP` | `K-WP07` | **Khải** | Tự động xoá bằng chứng sau N ngày | `K09-06` | Khải |
| `M02-F052` | P2 | `KEEP` | `K-WP07` | **Khải** | So sánh ảnh chụp với trang thật của thương hiệu | `K09-07` | Khải |
| `M02-F053` | P0 | `KEEP` | `K-WP08` | **Khải** | Màn hình nhập liệu 4 tab: URL, văn bản, số điện thoại, số tài khoản | `T11-02` | Thắng |
| `M02-F054` | P0 | `ADD` | `K-WP08` | **Khải** | URL Worker chỉ trả `AnalysisSignal[]`, `DerivedIndicator[]` và `pendingAiTasks[]`; Spring Boot/M12 mới aggregate, gọi M09 và persist final `RiskResult`. | `ARCH-V3.2-01` | — |
| `M02-F055` | P0 | `ADD` | `K-WP08` | **Khải** | Spring Boot core pre-enrich known URL/domain reputation trước khi publish job; redirect/domain mới được worker trả về `DerivedIndicator` với `handling=REPUTATION_ONLY` hoặc `CHILD_SCAN`, worker không gọi ngược core. | `ARCH-V3.2-02` | — |
| `M02-F056` | P0 | `ADD` | `K-WP08` | **Khải** | Deferred reputation chạy trong core qua `ThreatQuery`: Redis→PostgreSQL, timeout ban đầu 500 ms; unavailable → `REPUTATION_UNAVAILABLE` nhưng scan tiếp tục degraded khi phù hợp. | `ARCH-V3.2-03` | — |

## M03. Text & Transaction Scam Analysis

> **Type:** End-to-End Feature  
> **Actor:** Guest, User  
> **Dependencies:** M08; M09; M11 optional; M12; M13; M14  
> **Owner:** Thắng — Content, QR, Result & AI.

**Mục đích:** Phân tích tin nhắn, hội thoại và bài đăng giao dịch; trích xuất indicator, phát hiện scam cues/patterns và tạo nested scan khi cần.

**Backlog:** 48 feature — P0 18, P1 16, P2 14.

| ID | Priority | Action | Work Package | Owner hiện tại | Feature hiện tại | Legacy/Source | Legacy owner (trace only) |
|---|---|---|---|---|---|---|---|
| `M03-F001` | P0 | `KEEP` | `T-WP01` | **Thắng** | Hạ chữ thường, bỏ dấu tiếng Việt, gộp khoảng trắng | `H09-01` | Hùng |
| `M03-F002` | P0 | `KEEP` | `T-WP01` | **Thắng** | Gỡ leetspeak theo token có chứa chữ cái (`vi3tc0mb4nk` → `vietcombank`) | `H09-02` | Hùng |
| `M03-F003` | P0 | `KEEP` | `T-WP01` | **Thắng** | So sánh hai mức chuẩn hoá để phát hiện cố tình né bộ lọc (fact `LEET_BRAND`) | `H09-03` | Hùng |
| `M03-F004` | P1 | `KEEP` | `T-WP01` | **Thắng** | Chuẩn hoá dấu câu, emoji, ký tự Unicode đồng hình (homoglyph) | `H09-04` | Hùng |
| `M03-F005` | P1 | `KEEP` | `T-WP01` | **Thắng** | Phát hiện chèn ký tự vô hình (zero-width space) để né bộ lọc | `H09-05` | Hùng |
| `M03-F006` | P2 | `KEEP` | `T-WP01` | **Thắng** | Chuẩn hoá teencode và viết tắt tiếng Việt phổ biến | `H09-06` | Hùng |
| `M03-F007` | P2 | `KEEP` | `T-WP01` | **Thắng** | Nhận diện ngôn ngữ đầu vào, cảnh báo khi không phải tiếng Việt | `H09-07` | Hùng |
| `M03-F008` | P0 | `MODIFY` | `T-WP02` | **Thắng** | `POST /v1/scans/text` xử lý bất đồng bộ qua `q.scan.text`, tối đa 20.000 ký tự ban đầu, trả `202 + scanId` khi tạo job thành công. | `R01-01` | Kiên |
| `M03-F009` | P0 | `KEEP` | `T-WP02` | **Thắng** | Khớp từ khoá theo 6 nhóm: khẩn cấp, giả danh, thông tin nhạy cảm, chuyển tiền, quá tốt để tin, mời việc làm | `R01-02` | Kiên |
| `M03-F010` | P0 | `KEEP` | `T-WP02` | **Thắng** | Nhận diện mẫu tổ hợp: giả mạo ngân hàng, giả danh cơ quan, trúng thưởng, tuyển CTV, shipper giả | `R01-03` | Kiên |
| `M03-F011` | P0 | `KEEP` | `T-WP02` | **Thắng** | Trang quét nội dung trên web, hiển thị điểm và bằng chứng | `R01-04` | Kiên |
| `M03-F012` | P1 | `KEEP` | `T-WP02` | **Thắng** | Hỗ trợ chọn nguồn tin (SMS / Zalo / Messenger / Email / Facebook) và điều chỉnh trọng số theo nguồn | `R01-05` | Kiên |
| `M03-F013` | P1 | `KEEP` | `T-WP02` | **Thắng** | Đánh dấu trực quan đoạn văn bản đã kích hoạt rule | `R01-06` | Kiên |
| `M03-F014` | P2 | `KEEP` | `T-WP02` | **Thắng** | Phân tích ảnh chụp màn hình tin nhắn bằng OCR | `R01-07` | Kiên |
| `M03-F015` | P2 | `KEEP` | `T-WP02` | **Thắng** | Phân tích tệp email `.eml` gồm cả header | `R01-08` | Kiên |
| `M03-F016` | P0 | `KEEP` | `T-WP03` | **Thắng** | Trích xuất URL (kể cả không có scheme) và địa chỉ IP | `R02-01` | Kiên |
| `M03-F017` | P0 | `KEEP` | `T-WP03` | **Thắng** | Trích xuất số điện thoại Việt Nam, chuẩn hoá về `+84...` | `R02-02` | Kiên |
| `M03-F018` | P0 | `KEEP` | `T-WP03` | **Thắng** | Trích xuất số tài khoản ngân hàng, không nhầm với số điện thoại | `R02-03` | Kiên |
| `M03-F019` | P0 | `KEEP` | `T-WP03` | **Thắng** | Trích xuất số tiền (`500k`, `100 triệu`, `1.000.000đ`) | `R02-04` | Kiên |
| `M03-F020` | P0 | `KEEP` | `T-WP03` | **Thắng** | Trích xuất mã OTP theo ngữ cảnh từ khoá | `R02-05` | Kiên |
| `M03-F021` | P1 | `KEEP` | `T-WP03` | **Thắng** | Trích xuất tên ngân hàng và mã ngân hàng | `R02-06` | Kiên |
| `M03-F022` | P1 | `KEEP` | `T-WP03` | **Thắng** | Trích xuất mốc thời gian và hạn chót ("trong vòng 24h") | `R02-07` | Kiên |
| `M03-F023` | P2 | `KEEP` | `T-WP03` | **Thắng** | Trích xuất địa chỉ ví tiền mã hoá | `R02-08` | Kiên |
| `M03-F024` | P0 | `KEEP` | `T-WP04` | **Thắng** | Định nghĩa và phân loại các mẫu lừa đảo phổ biến tại Việt Nam | `R03-01` | Kiên |
| `M03-F025` | P1 | `KEEP` | `T-WP04` | **Thắng** | Mỗi mẫu có mã, tên, mô tả, dấu hiệu nhận biết, ví dụ thật | `R03-02` | Kiên |
| `M03-F026` | P1 | `KEEP` | `T-WP04` | **Thắng** | Gán nhãn loại lừa đảo vào kết quả quét | `R03-03` | Kiên |
| `M03-F027` | P1 | `KEEP` | `T-WP04` | **Thắng** | Trang tra cứu các mẫu lừa đảo cho người dùng | `R03-04` | Kiên |
| `M03-F028` | P2 | `KEEP` | `T-WP04` | **Thắng** | Thống kê mẫu nào đang phổ biến theo thời gian | `R03-05` | Kiên |
| `M03-F029` | P2 | `KEEP` | `T-WP04` | **Thắng** | Cho phép admin thêm mẫu mới không cần sửa code | `R03-06` | Kiên |
| `M03-F030` | P2 | `KEEP` | `T-WP04` | **Thắng** | Ánh xạ mẫu sang khung phân loại quốc tế | `R03-07` | Kiên |
| `M03-F031` | P0 | `KEEP` | `T-WP05` | **Thắng** | Nhóm từ khoá lưu trong cấu hình, tự chuẩn hoá khi nạp | `R07-01` | Kiên |
| `M03-F032` | P1 | `KEEP` | `T-WP05` | **Thắng** | Trang admin quản lý nhóm từ khoá và từng từ khoá | `R07-02` | Kiên |
| `M03-F033` | P1 | `KEEP` | `T-WP05` | **Thắng** | Thêm/xoá từ khoá không cần deploy lại | `R07-03` | Kiên |
| `M03-F034` | P1 | `KEEP` | `T-WP05` | **Thắng** | Cảnh báo khi thêm từ khoá quá phổ thông dễ gây báo động giả | `R07-04` | Kiên |
| `M03-F035` | P2 | `KEEP` | `T-WP05` | **Thắng** | Gợi ý từ khoá mới từ các báo cáo cộng đồng đã duyệt | `R07-05` | Kiên |
| `M03-F036` | P2 | `KEEP` | `T-WP05` | **Thắng** | Hỗ trợ biểu thức chính quy cho từ khoá nâng cao | `R07-06` | Kiên |
| `M03-F037` | P2 | `KEEP` | `T-WP05` | **Thắng** | Từ điển đồng nghĩa và biến thể viết tắt | `R07-07` | Kiên |
| `M03-F038` | P1 | `KEEP` | `T-WP06` | **Thắng** | Nhận đầu vào là nhiều tin nhắn có thứ tự, không chỉ một đoạn | `R08-01` | Kiên |
| `M03-F039` | P1 | `KEEP` | `T-WP06` | **Thắng** | Nhận diện kịch bản leo thang: làm quen → tạo lòng tin → yêu cầu tiền | `R08-02` | Kiên |
| `M03-F040` | P1 | `KEEP` | `T-WP06` | **Thắng** | Tính điểm cho cả hội thoại, không chỉ từng tin nhắn rời | `R08-03` | Kiên |
| `M03-F041` | P2 | `KEEP` | `T-WP06` | **Thắng** | Nhận diện mẫu lừa đảo tình cảm và đầu tư dài ngày | `R08-04` | Kiên |
| `M03-F042` | P2 | `KEEP` | `T-WP06` | **Thắng** | Cảnh báo khi hội thoại chuyển hướng sang yêu cầu tài chính | `R08-05` | Kiên |
| `M03-F043` | P2 | `KEEP` | `T-WP06` | **Thắng** | Dòng thời gian trực quan của hội thoại kèm điểm rủi ro theo từng bước | `R08-06` | Kiên |
| `M03-F044` | P0 | `KEEP` | `T-WP07` | **Thắng** | **Không** đọc SMS, notification, call log, danh bạ hay clipboard ngầm; **không** dùng Accessibility Service | `T11-05` | Thắng |
| `M03-F045` | P1 | `KEEP` | `T-WP07` | **Thắng** | Dán nhanh từ clipboard khi ứng dụng đang mở (do người dùng chủ động bấm) | `T11-07` | Thắng |
| `M03-F046` | P0 | `ADD` | `T-WP07` | **Thắng** | `TEXT.contentType` thống nhất `MESSAGE` hoặc `TRANSACTION_POST`; không tạo endpoint riêng cho transaction post. | `ARCH-04 (Architecture V3.2)` | — |
| `M03-F047` | P0 | `ADD` | `T-WP07` | **Thắng** | Text Worker trả `DerivedIndicator[]` cho URL/Phone/Bank và `pendingAiTasks[]`; M12 Orchestrator quyết định `REPUTATION_ONLY`/`CHILD_SCAN`, tạo child scans và chờ AI task, worker không gọi worker/core trực tiếp. | `ARCH-V3.2-04` | — |
| `M03-F048` | P0 | `ADD` | `T-WP07` | **Thắng** | Phát hiện yêu cầu/thu thập CCCD như `SENSITIVE_IDENTITY_REQUEST`/sensitive-data signal; không có standalone CCCD lookup trong MVP. | `ARCH-06 (Architecture V3.2)` | — |

## M04. Phone & Bank Reputation Check

> **Type:** End-to-End Feature  
> **Actor:** Guest, User  
> **Dependencies:** M08; M09; M12; M13; M14  
> **Owner:** Kiên — Reputation, Risk & Orchestration.

**Mục đích:** Kiểm tra reputation của số điện thoại và tài khoản ngân hàng; worker dùng reputationContext do core cấp và không tự đọc DB/cache.

**Backlog:** 18 feature — P0 8, P1 6, P2 4.

| ID | Priority | Action | Work Package | Owner hiện tại | Feature hiện tại | Legacy/Source | Legacy owner (trace only) |
|---|---|---|---|---|---|---|---|
| `M04-F001` | P0 | `MODIFY` | `I-WP15` | **Kiên** | `POST /v1/scans/entity` (`entityType=PHONE`), chuẩn hoá về `+84xxxxxxxxx` | `T01-01` | Thắng |
| `M04-F002` | P0 | `KEEP` | `I-WP15` | **Kiên** | Tra `risk_entities` và số lượt bị báo cáo | `T01-02` | Thắng |
| `M04-F003` | P0 | `KEEP` | `I-WP15` | **Kiên** | Tính điểm theo: có trong danh sách đen, số báo cáo, số báo cáo đã xác minh | `T01-03` | Thắng |
| `M04-F004` | P1 | `KEEP` | `I-WP15` | **Kiên** | Suy giảm điểm theo thời gian — báo cáo cũ có trọng số thấp hơn | `T01-04` | Thắng |
| `M04-F005` | P1 | `MODIFY` | `I-WP15` | **Kiên** | Tra uy tín số điện thoại qua core `ThreatQuery` cache-aside: Redis hit, cache miss về PostgreSQL; TTL theo source, mặc định 1 giờ thay vì hard-code theo entity. | `T01-05 + MODSPEC-V1.3` | Thắng |
| `M04-F006` | P1 | `KEEP` | `I-WP15` | **Kiên** | Che số điện thoại khi ghi log và khi hiển thị công khai | `T01-06` | Thắng |
| `M04-F007` | P2 | `KEEP` | `I-WP15` | **Kiên** | Nhận diện đầu số dịch vụ, đầu số quốc tế bất thường | `T01-07` | Thắng |
| `M04-F008` | P2 | `KEEP` | `I-WP15` | **Kiên** | Nhận diện số giả mạo tổng đài ngân hàng | `T01-08` | Thắng |
| `M04-F009` | P0 | `MODIFY` | `I-WP16` | **Kiên** | `POST /v1/scans/entity` (`entityType=BANK_ACCOUNT`), chuẩn hoá số tài khoản và mã ngân hàng | `T02-01` | Thắng |
| `M04-F010` | P0 | `KEEP` | `I-WP16` | **Kiên** | Validate mã ngân hàng theo danh sách ngân hàng Việt Nam | `T02-02` | Thắng |
| `M04-F011` | P0 | `KEEP` | `I-WP16` | **Kiên** | Tra `risk_entities` và các báo cáo liên quan | `T02-03` | Thắng |
| `M04-F012` | P1 | `MODIFY` | `I-WP16` | **Kiên** | Tra uy tín tài khoản ngân hàng qua cùng core `ThreatQuery` cache-aside; Redis chỉ là hot cache, TTL theo source (mặc định 1 giờ), PostgreSQL là source of truth. | `T02-04 + MODSPEC-V1.3` | Thắng |
| `M04-F013` | P1 | `KEEP` | `I-WP16` | **Kiên** | Che số tài khoản khi ghi log (`****1234`) | `T02-05` | Thắng |
| `M04-F014` | P1 | `KEEP` | `I-WP16` | **Kiên** | Cảnh báo khi tên chủ tài khoản không khớp với tên người bán được nhắc tới | `T02-06` | Thắng |
| `M04-F015` | P2 | `KEEP` | `I-WP16` | **Kiên** | Nhóm các báo cáo cùng một số tài khoản để thấy quy mô | `T02-07` | Thắng |
| `M04-F016` | P2 | `KEEP` | `I-WP16` | **Kiên** | Cảnh báo tài khoản xuất hiện trong nhiều vụ khác nhau | `T02-08` | Thắng |
| `M04-F017` | P0 | `ADD` | `I-WP17` | **Kiên** | Phone và Bank dùng chung `POST /v1/scans/entity` + `entityType`; validation/normalization theo strategy. | `ARCH-07 (Architecture V3.2)` | — |
| `M04-F018` | P0 | `ADD` | `I-WP17` | **Kiên** | UI/result phải phân biệt `NO_DATA` với verified-safe; không tìm thấy dữ liệu rủi ro không được diễn giải thành an toàn tuyệt đối. | `ARCH-08 (Architecture V3.2)` | — |

## M05. QR / VietQR Scan

> **Type:** End-to-End Feature  
> **Actor:** Guest, User  
> **Dependencies:** M02–M04; M09; M12; M13; M14  
> **Owner:** Thắng — Content, QR, Result & AI.

**Mục đích:** Decode/parse QR/VietQR, trích URL/text/bank/amount/content thành derived indicators để Orchestrator tạo nested scan phù hợp.

**Backlog:** 10 feature — P0 7, P1 1, P2 2.

| ID | Priority | Action | Work Package | Owner hiện tại | Feature hiện tại | Legacy/Source | Legacy owner (trace only) |
|---|---|---|---|---|---|---|---|
| `M05-F001` | P0 | `MODIFY` | `T-WP08` | **Thắng** | `POST /v1/scans/qr` nhận ảnh multipart hoặc encoded QR payload theo contract; Mobile có thể decode trên thiết bị nhưng server không phụ thuộc bắt buộc vào client decode. | `T03-01` | Thắng |
| `M05-F002` | P0 | `KEEP` | `T-WP08` | **Thắng** | Phân loại nội dung QR: URL / VietQR / văn bản thường / không xác định | `T03-02` | Thắng |
| `M05-F003` | P0 | `KEEP` | `T-WP08` | **Thắng** | Định tuyến: URL → quét URL, VietQR → tra tài khoản, văn bản → phân tích nội dung | `T03-03` | Thắng |
| `M05-F004` | P0 | `KEEP` | `T-WP08` | **Thắng** | Parse VietQR lấy mã ngân hàng, số tài khoản, số tiền, nội dung chuyển khoản | `T03-04` | Thắng |
| `M05-F005` | P0 | `KEEP` | `T-WP08` | **Thắng** | QR chứa URL bắt buộc đi qua SSRF guard | `T03-05` | Thắng |
| `M05-F006` | P1 | `KEEP` | `T-WP08` | **Thắng** | Giải mã QR từ ảnh tải lên trên web | `T03-06` | Thắng |
| `M05-F007` | P2 | `FUTURE_SCOPE` | `T-WP09` | **Thắng** | Cảnh báo QR bị dán đè lên QR thật (so với lịch sử quét cùng địa điểm) | `T03-07` | Thắng |
| `M05-F008` | P2 | `FUTURE_SCOPE` | `T-WP09` | **Thắng** | Cảnh báo khi số tiền trong QR khác số tiền người dùng dự kiến | `T03-08` | Thắng |
| `M05-F009` | P0 | `KEEP` | `T-WP08` | **Thắng** | Trình quét QR bằng camera, **giải mã trên thiết bị**, chỉ gửi `qrData` lên server | `T11-03` | Thắng |
| `M05-F010` | P0 | `ADD` | `T-WP08` | **Thắng** | QR Parser chỉ decode/classify/return `DerivedIndicator[]`; M12 Orchestrator chịu trách nhiệm dispatch URL/Text/Entity child scans. | `ARCH-09 (Architecture V3.2)` | — |

## M06. Scan Result, History & Export

> **Type:** End-to-End Feature  
> **Actor:** Guest, User; Admin theo quyền  
> **Dependencies:** M01; M09; M12; M13; M14  
> **Owner:** Thắng — Content, QR, Result & AI.

**Mục đích:** Hiển thị RiskResult, evidence/explanation/recommendation, quản lý persistent history cho account và xuất báo cáo PDF/HTML.

**Backlog:** 21 feature — P0 7, P1 9, P2 5.

| ID | Priority | Action | Work Package | Owner hiện tại | Feature hiện tại | Legacy/Source | Legacy owner (trace only) |
|---|---|---|---|---|---|---|---|
| `M06-F001` | P0 | `KEEP` | `T-WP11` | **Thắng** | Người dùng xoá được lịch sử quét của mình | `H08-03` | Hùng |
| `M06-F002` | P1 | `KEEP` | `T-WP11` | **Thắng** | Tự động xoá bản ghi quét cũ hơn N ngày (cấu hình được) | `H08-04` | Hùng |
| `M06-F003` | P0 | `MODIFY` | `T-WP10` | **Thắng** | `GET /v1/scans/history` — chỉ trả scan mà user có quyền truy cập. | `T10-01` | Thắng |
| `M06-F004` | P0 | `MODIFY` | `T-WP10` | **Thắng** | `GET /v1/scans/{scanId}` với authorization theo owner/RBAC; không để client query DB trực tiếp. | `T10-02` | Thắng |
| `M06-F005` | P0 | `KEEP` | `T-WP10` | **Thắng** | Phân trang bằng cursor | `T10-03` | Thắng |
| `M06-F006` | P1 | `KEEP` | `T-WP10` | **Thắng** | Lọc theo loại đầu vào, mức rủi ro, khoảng thời gian | `T10-04` | Thắng |
| `M06-F007` | P1 | `KEEP` | `T-WP11` | **Thắng** | Xoá từng bản ghi hoặc xoá toàn bộ lịch sử, kèm xoá bằng chứng | `T10-05` | Thắng |
| `M06-F008` | P1 | `KEEP` | `T-WP11` | **Thắng** | Đánh dấu và ghi chú cho bản ghi quan trọng | `T10-06` | Thắng |
| `M06-F009` | P2 | `KEEP` | `T-WP11` | **Thắng** | Quét lại một mục cũ để xem điểm đã thay đổi chưa | `T10-07` | Thắng |
| `M06-F010` | P2 | `KEEP` | `T-WP11` | **Thắng** | Xuất lịch sử ra CSV | `T10-08` | Thắng |
| `M06-F011` | P1 | `KEEP` | `T-WP12` | **Thắng** | Màn hình kết quả rút gọn, nêu 3–5 lý do chính | `T11-06` | Thắng |
| `M06-F012` | P0 | `KEEP` | `T-WP12` | **Thắng** | Component `RiskResultCard` dùng chung cho mọi loại kết quả quét | `T12-01` | Thắng |
| `M06-F013` | P0 | `KEEP` | `T-WP12` | **Thắng** | Hiển thị nhất quán: điểm, mức, màu, biểu tượng, danh sách bằng chứng, khuyến nghị | `T12-02` | Thắng |
| `M06-F014` | P1 | `KEEP` | `T-WP12` | **Thắng** | Thành phần hiển thị bằng chứng có thể mở rộng/thu gọn từng rule | `T12-03` | Thắng |
| `M06-F015` | P1 | `KEEP` | `T-WP13` | **Thắng** | Chia sẻ kết quả quét qua link công khai có hạn, đã che dữ liệu nhạy cảm | `T12-04` | Thắng |
| `M06-F016` | P1 | `KEEP` | `T-WP14` | **Thắng** | Xuất kết quả quét ra PDF (worker chạy bất đồng bộ) | `T12-05` | Thắng |
| `M06-F017` | P2 | `KEEP` | `T-WP13` | **Thắng** | Sinh ảnh tóm tắt kết quả để chia sẻ lên mạng xã hội | `T12-06` | Thắng |
| `M06-F018` | P2 | `KEEP` | `T-WP13` | **Thắng** | Nhúng widget tra cứu vào website khác | `T12-07` | Thắng |
| `M06-F019` | P2 | `KEEP` | `T-WP14` | **Thắng** | Xuất báo cáo thống kê theo ngày/tuần/tháng cho admin | `T12-08` | Thắng |
| `M06-F020` | P0 | `ADD` | `T-WP12` | **Thắng** | Dùng một `RiskResult` contract cho mọi scan type, gồm `riskScore`, `riskLevel`, `degraded`, evidence, explanations, recommendations, `policyVersion`. | `ARCH-10 (Architecture V3.2)` | — |
| `M06-F021` | P1 | `ADD` | `T-WP14` | **Thắng** | Export PDF/HTML chạy bất đồng bộ qua `q.report.export` / `report.export.requested`, lưu artifact ở MinIO/S3 và không block core scan. | `ARCH-11 (Architecture V3.2)` | — |

## M07. Community Report & Moderation

> **Type:** End-to-End Feature  
> **Actor:** User, Moderator/Admin  
> **Dependencies:** M01; M08; M10; M13; M14  
> **Owner:** Hùng — Identity, Community, Notification & Audit.

**Mục đích:** Cho user gửi báo cáo lừa đảo/evidence và moderator/admin duyệt; chỉ report đã xác minh mới có thể trở thành reputation signal.

**Backlog:** 26 feature — P0 9, P1 9, P2 8.

| ID | Priority | Action | Work Package | Owner hiện tại | Feature hiện tại | Legacy/Source | Legacy owner (trace only) |
|---|---|---|---|---|---|---|---|
| `M07-F001` | P0 | `KEEP` | `H-WP12` | **Hùng** | `POST /v1/reports` với loại thực thể, giá trị, mô tả | `T05-01` | Thắng |
| `M07-F002` | P0 | `KEEP` | `H-WP12` | **Hùng** | Rate limit 10 báo cáo / ngày / user | `T05-02` | Thắng |
| `M07-F003` | P0 | `KEEP` | `H-WP12` | **Hùng** | Nút "Gửi báo cáo" ngay trong trang kết quả quét, điền sẵn dữ liệu | `T05-03` | Thắng |
| `M07-F004` | P1 | `KEEP` | `H-WP12` | **Hùng** | Đính kèm file bằng chứng (ảnh chụp màn hình), giới hạn dung lượng và định dạng | `T05-04` | Thắng |
| `M07-F005` | P1 | `KEEP` | `H-WP12` | **Hùng** | Chọn loại lừa đảo theo taxonomy (R03) | `T05-05` | Thắng |
| `M07-F006` | P1 | `KEEP` | `H-WP12` | **Hùng** | Xem trạng thái các báo cáo mình đã gửi | `T05-06` | Thắng |
| `M07-F007` | P2 | `KEEP` | `H-WP12` | **Hùng** | Báo cáo ẩn danh không cần đăng nhập, có CAPTCHA | `T05-07` | Thắng |
| `M07-F008` | P2 | `KEEP` | `H-WP12` | **Hùng** | Cảnh báo trùng lặp khi entity đã được báo cáo | `T05-08` | Thắng |
| `M07-F009` | P0 | `KEEP` | `H-WP13` | **Hùng** | Trạng thái báo cáo: `PENDING`, `UNDER_REVIEW`, `VERIFIED`, `REJECTED`, `NEED_MORE_INFO`, `RESOLVED` | `T06-01` | Thắng |
| `M07-F010` | P0 | `KEEP` | `H-WP13` | **Hùng** | Hàng đợi duyệt cho admin, sắp xếp theo mức độ và thời gian | `T06-02` | Thắng |
| `M07-F011` | P0 | `KEEP` | `H-WP13` | **Hùng** | Duyệt báo cáo → tạo/cập nhật `risk_entities` | `T06-03` | Thắng |
| `M07-F012` | P0 | `KEEP` | `H-WP13` | **Hùng** | Ghi audit log mọi thao tác duyệt | `T06-04` | Thắng |
| `M07-F013` | P1 | `KEEP` | `H-WP13` | **Hùng** | Xem chi tiết báo cáo kèm bằng chứng và kết quả quét liên quan | `T06-05` | Thắng |
| `M07-F014` | P1 | `KEEP` | `H-WP13` | **Hùng** | Duyệt hàng loạt các báo cáo cùng một entity | `T06-06` | Thắng |
| `M07-F015` | P1 | `KEEP` | `H-WP13` | **Hùng** | Ghi lý do khi từ chối, gửi thông báo cho người báo cáo | `T06-07` | Thắng |
| `M07-F016` | P2 | `KEEP` | `H-WP13` | **Hùng** | Tự động ưu tiên báo cáo từ người có uy tín cao | `T06-08` | Thắng |
| `M07-F017` | P2 | `KEEP` | `H-WP13` | **Hùng** | Phân công báo cáo cho từng moderator | `T06-09` | Thắng |
| `M07-F018` | P1 | `KEEP` | `H-WP14` | **Hùng** | Tính điểm uy tín dựa trên tỷ lệ báo cáo được duyệt | `T07-01` | Thắng |
| `M07-F019` | P1 | `KEEP` | `H-WP14` | **Hùng** | Hạ uy tín khi báo cáo bị từ chối nhiều lần | `T07-02` | Thắng |
| `M07-F020` | P1 | `KEEP` | `H-WP14` | **Hùng** | Phát hiện báo cáo trùng lặp và báo cáo hàng loạt bất thường | `T07-03` | Thắng |
| `M07-F021` | P2 | `KEEP` | `H-WP14` | **Hùng** | Người uy tín cao được duyệt nhanh hoặc tự động duyệt | `T07-04` | Thắng |
| `M07-F022` | P2 | `KEEP` | `H-WP14` | **Hùng** | Tạm khoá quyền báo cáo khi uy tín xuống dưới ngưỡng | `T07-05` | Thắng |
| `M07-F023` | P2 | `KEEP` | `H-WP14` | **Hùng** | Huy hiệu người đóng góp tích cực | `T07-06` | Thắng |
| `M07-F024` | P2 | `KEEP` | `H-WP14` | **Hùng** | Phát hiện nhóm tài khoản phối hợp báo cáo sai sự thật | `T07-07` | Thắng |
| `M07-F025` | P0 | `ADD` | `H-WP15` | **Hùng** | Community Report lifecycle thống nhất `PENDING -> UNDER_REVIEW -> VERIFIED/REJECTED`; review phải trace reviewer/time/reason. | `ARCH-12 (Architecture V3.2)` | — |
| `M07-F026` | P0 | `ADD` | `H-WP15` | **Hùng** | Chỉ report `VERIFIED` đủ trust/policy mới được chuyển thành reputation/threat signal; unverified report không tạo hard blacklist. | `ARCH-13 (Architecture V3.2)` | — |

## M08. Threat Intelligence Management

> **Type:** Admin/Internal Feature  
> **Actor:** Admin, Spring Boot scan core  
> **Dependencies:** M07; M13; M14  
> **Owner:** Kiên — Reputation, Risk & Orchestration.

**Mục đích:** Quản lý threat/risk source, whitelist/blacklist/reputation và pipeline ingestion; core là nơi duy nhất tra dữ liệu uy tín cho scan.

**Backlog:** 27 feature — P0 8, P1 11, P2 8.

| ID | Priority | Action | Work Package | Owner hiện tại | Feature hiện tại | Legacy/Source | Legacy owner (trace only) |
|---|---|---|---|---|---|---|---|
| `M08-F001` | P1 | `KEEP` | `I-WP01` | **Kiên** | Nhập danh sách đen từ file CSV/JSON | `K07-01` | Khải |
| `M08-F002` | P1 | `KEEP` | `I-WP01` | **Kiên** | Tích hợp nguồn công khai (PhishTank, OpenPhish, danh sách của ChongLuaDao) | `K07-02` | Khải |
| `M08-F003` | P1 | `KEEP` | `I-WP01` | **Kiên** | Lập lịch cập nhật định kỳ, ghi phiên bản dataset vào `risk_sources` | `K07-03` | Khải |
| `M08-F004` | P1 | `KEEP` | `I-WP01` | **Kiên** | Khử trùng lặp và hợp nhất khi nhiều nguồn cùng báo một tên miền | `K07-04` | Khải |
| `M08-F005` | P2 | `KEEP` | `I-WP01` | **Kiên** | Ghi nguồn gốc và độ tin cậy cho từng bản ghi | `K07-05` | Khải |
| `M08-F006` | P2 | `KEEP` | `I-WP01` | **Kiên** | Cho phép quay lại phiên bản dataset trước khi nạp sai | `K07-06` | Khải |
| `M08-F007` | P2 | `KEEP` | `I-WP01` | **Kiên** | Xuất ngược danh sách của hệ thống cho cộng đồng dùng | `K07-07` | Khải |
| `M08-F008` | P0 | `KEEP` | `I-WP02` | **Kiên** | Danh sách trắng tên miền tin cậy để giảm báo động giả | `K08-01` | Khải |
| `M08-F009` | P0 | `KEEP` | `I-WP02` | **Kiên** | Danh sách đen tên miền đã xác minh là lừa đảo | `K08-02` | Khải |
| `M08-F010` | P1 | `KEEP` | `I-WP02` | **Kiên** | Trang admin quản lý: thêm, sửa, xoá, tìm kiếm, phân trang | `K08-03` | Khải |
| `M08-F011` | P1 | `KEEP` | `I-WP02` | **Kiên** | Nhập hàng loạt từ file, kèm xem trước trước khi áp dụng | `K08-04` | Khải |
| `M08-F012` | P1 | `KEEP` | `I-WP02` | **Kiên** | Vô hiệu hoá cache Redis khi danh sách thay đổi | `K08-05` | Khải |
| `M08-F013` | P1 | `KEEP` | `I-WP02` | **Kiên** | Ghi audit log mọi thay đổi danh sách | `K08-06` | Khải |
| `M08-F014` | P2 | `KEEP` | `I-WP02` | **Kiên** | Đặt hạn hiệu lực cho từng bản ghi | `K08-07` | Khải |
| `M08-F015` | P2 | `KEEP` | `I-WP02` | **Kiên** | Quy trình khiếu nại gỡ khỏi danh sách đen | `K08-08` | Khải |
| `M08-F016` | P0 | `KEEP` | `I-WP03` | **Kiên** | Bảng `risk_entities` cho tên miền, URL, số điện thoại, số tài khoản, từ khoá, thương hiệu | `T04-01` | Thắng |
| `M08-F017` | P0 | `KEEP` | `I-WP03` | **Kiên** | Trạng thái: `SUSPECTED`, `VERIFIED`, `CLEARED` | `T04-02` | Thắng |
| `M08-F018` | P0 | `KEEP` | `I-WP03` | **Kiên** | Tạo/cập nhật entity khi một báo cáo được duyệt | `T04-03` | Thắng |
| `M08-F019` | P1 | `KEEP` | `I-WP03` | **Kiên** | Trang admin quản lý entity: tìm kiếm, lọc theo loại và trạng thái, phân trang | `T04-04` | Thắng |
| `M08-F020` | P1 | `KEEP` | `I-WP03` | **Kiên** | Xem toàn bộ báo cáo dẫn tới một entity | `T04-05` | Thắng |
| `M08-F021` | P1 | `KEEP` | `I-WP03` | **Kiên** | Gộp các entity trùng lặp | `T04-06` | Thắng |
| `M08-F022` | P2 | `KEEP` | `I-WP03` | **Kiên** | Điểm tin cậy của entity dựa trên số nguồn độc lập xác nhận | `T04-07` | Thắng |
| `M08-F023` | P2 | `KEEP` | `I-WP03` | **Kiên** | Tự động hạ trạng thái entity không còn báo cáo mới trong thời gian dài | `T04-08` | Thắng |
| `M08-F024` | P2 | `FUTURE_SCOPE` | `I-WP03` | **Kiên** | Chế độ ngoại tuyến dùng danh sách đen tải sẵn | `T11-09` | Thắng |
| `M08-F025` | P0 | `ADD` | `I-WP04` | **Kiên** | Core-only `ThreatQuery` dùng cache-aside: Redis HIT; MISS → PostgreSQL source of truth → cache `ReputationContext`; scanner worker không gọi `ThreatQuery`. | `ARCH-V3.2-05` | — |
| `M08-F026` | P0 | `ADD` | `I-WP04` | **Kiên** | Threat ingestion normalize/validate/deduplicate/version/upsert PostgreSQL rồi refresh/invalidate Redis; scanner worker không đọc DB/Redis/core API. | `ARCH-V3.2-06` | — |
| `M08-F027` | P0 | `ADD` | `I-WP04` | **Kiên** | Deferred enrichment chỉ diễn ra trong Spring Boot core khi consume `DerivedIndicator`; `ThreatQuery` bounded ~500 ms và trả `NO_DATA`/`REPUTATION_UNAVAILABLE`, không expose worker-facing lookup endpoint. | `ARCH-V3.2-07` | — |

## M09. Rule, Risk Policy & Admin Operations

> **Type:** Shared/Admin Feature  
> **Actor:** Admin, All Scan Modules  
> **Dependencies:** M08; M11; M12; M13; M14  
> **Owner:** Kiên — Reputation, Risk & Orchestration.

**Mục đích:** Quản lý rule/policy, Signal Aggregation, Rule Engine, Risk Fusion, threshold, versioning và admin operations liên quan scoring.

**Backlog:** 39 feature — P0 10, P1 17, P2 12.

| ID | Priority | Action | Work Package | Owner hiện tại | Feature hiện tại | Legacy/Source | Legacy owner (trace only) |
|---|---|---|---|---|---|---|---|
| `M09-F001` | P0 | `KEEP` | `I-WP05` | **Kiên** | Mô hình rule khai báo bằng FACT: `requiresAll` / `requiresAny` / `requiresNone` | `H10-01` | Hùng |
| `M09-F002` | P0 | `KEEP` | `I-WP05` | **Kiên** | Sinh `evidences[]` với `ruleCode`, `ruleName`, `score`, `severity`, `description` | `H10-02` | Hùng |
| `M09-F003` | P0 | `MODIFY` | `I-WP06` | **Kiên** | Risk Fusion tạo `riskScore` trong `[0,100]` từ các nhóm Technical / Content / Reputation-Community / AI theo `FusionPolicy` versioned; mapping `0-34 SAFE`, `35-69 CAUTION`, `70-100 DANGER`. | `H10-03` | Hùng |
| `M09-F004` | P0 | `MODIFY` | `I-WP05` | **Kiên** | Bất biến kiểm thử: cùng `UnifiedSignalSet` + cùng `FusionPolicy` phải cho kết quả deterministic; không giả định `riskScore = sum(evidences[].score)`. | `H10-04` | Hùng |
| `M09-F005` | P0 | `MODIFY` | `I-WP05` | **Kiên** | Result Composer sinh `evidence[]`, `explanation[]` và `recommendations[]` từ signal/rule/fusion result; không để AI tự quyết định business verdict. | `H10-05` | Hùng |
| `M09-F006` | P1 | `MODIFY` | `I-WP06` | **Kiên** | Nạp `rules`/`rule_versions` từ PostgreSQL; policy/rule active có version rõ ràng, cache Redis và invalidate khi admin activate version mới. | `H10-06` | Hùng |
| `M09-F007` | P1 | `MODIFY` | `I-WP06` | **Kiên** | Nếu cần `confidence`, định nghĩa như metadata riêng có source/calibration rõ ràng; không dùng confidence để thay thế `riskScore`. | `H10-07` | Hùng |
| `M09-F008` | P2 | `MODIFY` | `I-WP06` | **Kiên** | P2: hỗ trợ policy profile theo user chỉ khi có requirement rõ ràng; không thay đổi semantics `SAFE/CAUTION/DANGER` tùy tiện theo client. | `H10-08` | Hùng |
| `M09-F009` | P0 | `MODIFY` | `I-WP07` | **Kiên** | Bảng `rules` và `rule_versions` / versioned rule definition trong PostgreSQL | `R04-01` | Kiên |
| `M09-F010` | P0 | `MODIFY` | `I-WP07` | **Kiên** | `Spring Boot Rule/Risk capability` nạp rule từ DB thay vì file, cache Redis | `R04-02` | Kiên |
| `M09-F011` | P1 | `KEEP` | `I-WP07` | **Kiên** | Trang admin: liệt kê, tìm kiếm, lọc rule theo danh mục và mức nghiêm trọng | `R04-03` | Kiên |
| `M09-F012` | P1 | `KEEP` | `I-WP07` | **Kiên** | Tạo, sửa, bật/tắt rule qua giao diện | `R04-04` | Kiên |
| `M09-F013` | P1 | `KEEP` | `I-WP07` | **Kiên** | Chỉnh trọng số và mức nghiêm trọng, kích hoạt rebuild cache | `R04-05` | Kiên |
| `M09-F014` | P1 | `KEEP` | `I-WP07` | **Kiên** | Ghi audit log mọi thay đổi rule | `R04-06` | Kiên |
| `M09-F015` | P2 | `KEEP` | `I-WP07` | **Kiên** | Sao chép rule để tạo biến thể | `R04-07` | Kiên |
| `M09-F016` | P2 | `KEEP` | `I-WP07` | **Kiên** | Nhập/xuất bộ rule dạng JSON | `R04-08` | Kiên |
| `M09-F017` | P1 | `KEEP` | `I-WP08` | **Kiên** | `GET /v1/rules/evaluate/preview` — chạy thử rule trên văn bản mẫu, không ghi lịch sử | `R05-01` | Kiên |
| `M09-F018` | P1 | `KEEP` | `I-WP08` | **Kiên** | Giao diện sandbox: nhập văn bản, xem rule nào khớp và cộng bao nhiêu điểm | `R05-02` | Kiên |
| `M09-F019` | P1 | `KEEP` | `I-WP08` | **Kiên** | Xem trước ảnh hưởng khi đổi trọng số, trước khi lưu | `R05-03` | Kiên |
| `M09-F020` | P2 | `KEEP` | `I-WP08` | **Kiên** | Chạy rule mới trên tập dữ liệu gán nhãn, báo số case bị ảnh hưởng | `R05-04` | Kiên |
| `M09-F021` | P2 | `KEEP` | `I-WP08` | **Kiên** | So sánh hai phiên bản bộ rule trên cùng tập đầu vào | `R05-05` | Kiên |
| `M09-F022` | P2 | `KEEP` | `I-WP08` | **Kiên** | Chế độ shadow — chạy rule mới song song nhưng chưa áp dụng điểm | `R05-06` | Kiên |
| `M09-F023` | P1 | `KEEP` | `I-WP09` | **Kiên** | Đánh phiên bản cho mỗi lần thay đổi bộ rule | `R06-01` | Kiên |
| `M09-F024` | P1 | `KEEP` | `I-WP09` | **Kiên** | Lưu phiên bản rule đã dùng vào từng bản ghi kết quả quét | `R06-02` | Kiên |
| `M09-F025` | P1 | `KEEP` | `I-WP09` | **Kiên** | Xem lịch sử thay đổi của một rule (ai sửa, sửa gì, khi nào) | `R06-03` | Kiên |
| `M09-F026` | P2 | `KEEP` | `I-WP09` | **Kiên** | Quay lại phiên bản trước bằng một thao tác | `R06-04` | Kiên |
| `M09-F027` | P2 | `KEEP` | `I-WP09` | **Kiên** | So sánh khác biệt giữa hai phiên bản | `R06-05` | Kiên |
| `M09-F028` | P2 | `KEEP` | `I-WP09` | **Kiên** | Phê duyệt hai bước cho thay đổi rule quan trọng | `R06-06` | Kiên |
| `M09-F029` | P1 | `KEEP` | `I-WP10` | **Kiên** | Số lượt quét theo ngày/tuần/tháng, tách theo loại đầu vào | `T08-01` | Thắng |
| `M09-F030` | P1 | `KEEP` | `I-WP10` | **Kiên** | Phân bố mức rủi ro `SAFE` / `CAUTION` / `DANGER` | `T08-02` | Thắng |
| `M09-F031` | P1 | `KEEP` | `I-WP10` | **Kiên** | Top loại lừa đảo phổ biến | `T08-03` | Thắng |
| `M09-F032` | P1 | `KEEP` | `I-WP10` | **Kiên** | Top tên miền, số điện thoại, số tài khoản bị báo cáo nhiều nhất | `T08-04` | Thắng |
| `M09-F033` | P1 | `KEEP` | `I-WP10` | **Kiên** | Số báo cáo theo trạng thái, thời gian xử lý trung bình | `T08-05` | Thắng |
| `M09-F034` | P2 | `KEEP` | `I-WP10` | **Kiên** | Biểu đồ xu hướng theo thời gian, so sánh kỳ trước | `T08-06` | Thắng |
| `M09-F035` | P2 | `KEEP` | `I-WP10` | **Kiên** | Bản đồ nhiệt theo khung giờ trong ngày | `T08-07` | Thắng |
| `M09-F036` | P2 | `KEEP` | `I-WP10` | **Kiên** | Xuất dashboard ra PDF theo kỳ | `T08-08` | Thắng |
| `M09-F037` | P0 | `ADD` | `I-WP05` | **Kiên** | Tách rõ `Rule Engine -> RuleMatch[]/evidence`, `Risk Fusion -> score/level`, `Result Composer -> explanation/recommendation`. | `ARCH-17 (Architecture V3.2)` | — |
| `M09-F038` | P0 | `ADD` | `I-WP06` | **Kiên** | `FusionPolicy` versioned/configurable theo scan profile; missing signal group phải renormalize trọng số thay vì xem như 0. | `ARCH-18 (Architecture V3.2)` | — |
| `M09-F039` | P0 | `ADD` | `I-WP06` | **Kiên** | Initial threshold dùng `0-34 SAFE`, `35-69 CAUTION`, `70-100 DANGER`; hard override phải có evidence và nằm trong policy version. | `ARCH-19 (Architecture V3.2)` | — |

## M10. Notification & User Follow-up

> **Type:** End-to-End Feature / Shared Delivery Capability  
> **Actor:** User, Admin; internal producers M01/M06/M07/M12/M14  
> **Dependencies:** M01; M06; M07; M12; M13; M14  
> **Owner:** Hùng — Identity, Community, Notification & Audit.

**Mục đích:** Tập trung notification policy, channel resolver, template, endpoint và delivery worker; channel/provider agnostic và M10 không sở hữu business truth của OTP/scan/report.

**Backlog:** 18 feature — P0 4, P1 10, P2 4.

| ID | Priority | Action | Work Package | Owner hiện tại | Feature hiện tại | Legacy/Source | Legacy owner (trace only) |
|---|---|---|---|---|---|---|---|
| `M10-F001` | P1 | `KEEP` | `H-WP08` | **Hùng** | Thông báo trong ứng dụng khi báo cáo được duyệt hoặc bị từ chối | `T09-01` | Thắng |
| `M10-F002` | P1 | `KEEP` | `H-WP08` | **Hùng** | Email thông báo kết quả xử lý báo cáo | `T09-02` | Thắng |
| `M10-F003` | P1 | `MODIFY` | `H-WP09` | **Hùng** | Notification Delivery Worker xử lý bất đồng bộ; attempt đầu + tối đa 2 retry, exhausted -> failed/DLQ; delivery failure không rollback business transaction. | `T09-03 + V3.3` | Thắng |
| `M10-F004` | P1 | `KEEP` | `H-WP08` | **Hùng** | Trung tâm thông báo, đánh dấu đã đọc | `T09-04` | Thắng |
| `M10-F005` | P2 | `KEEP` | `H-WP08` | **Hùng** | Push notification trên mobile | `T09-05` | Thắng |
| `M10-F006` | P2 | `KEEP` | `H-WP08` | **Hùng** | Cảnh báo chủ động khi entity người dùng từng quét bị nâng lên `DANGER` | `T09-06` | Thắng |
| `M10-F007` | P2 | `KEEP` | `H-WP08` | **Hùng** | Bản tin tuần về xu hướng lừa đảo mới | `T09-07` | Thắng |
| `M10-F008` | P1 | `MODIFY` | `H-WP10` | **Hùng** | Producer/domain event được chuẩn hoá thành `notification.requested`; M10 resolve policy/channel/template rồi dispatch delivery, không để notification logic rải rác trong scan/report/auth module. | `ARCH-V3.3-M10` | — |
| `M10-F009` | P1 | `MODIFY` | `H-WP09` | **Hùng** | Notification consumer idempotent theo `idempotencyKey`/event-recipient; provider retry bounded và không tạo duplicate user-visible message khi provider đầu đã xác nhận success. | `ARCH-V3.3-M10` | — |
| `M10-F010` | P0 | `ADD` | `H-WP10` | **Hùng** | Transactional auth notification types cho `AUTH_EMAIL_VERIFICATION`, `AUTH_PASSWORD_RESET`, `AUTH_PASSWORD_CHANGE_OTP`, `AUTH_PASSWORD_CHANGED`; M10 chỉ deliver, M01 mới verify/consume challenge. | `MODSPEC-V1.3-M10` | — |
| `M10-F011` | P0 | `ADD` | `H-WP10` | **Hùng** | Canonical `NotificationRequest` có `eventId`, `correlationId`, `notificationType`, recipient, `channelHints[]`, `templateKey`, `templateData`, resource/deep-link, priority, idempotencyKey, expiresAt. | `MODSPEC-V1.3-M10` | — |
| `M10-F012` | P0 | `ADD` | `H-WP10` | **Hùng** | Tách `channelKey` khỏi `providerKey`; dùng Channel Resolver + Provider Adapter Registry để thay/add provider mà producer contract không đổi. | `ARCH-V3.3-M10` | — |
| `M10-F013` | P1 | `ADD` | `H-WP11` | **Hùng** | Quản lý `notification_endpoints` generic theo channel (email, phone, push token, Telegram chat id, ...), hỗ trợ verify/enable/disable và bảo vệ address value. | `SCHEMA-V1.0 + MODSPEC-V1.3` | — |
| `M10-F014` | P1 | `ADD` | `H-WP11` | **Hùng** | Notification preferences theo notification type/channel; mandatory security notification không được user tắt nếu policy yêu cầu. | `MODSPEC-V1.3-M10` | — |
| `M10-F015` | P1 | `ADD` | `H-WP11` | **Hùng** | Template resolution bằng `templateKey`, deep-link/resource metadata và authorize lại khi user mở deep link. | `MODSPEC-V1.3-M10` | — |
| `M10-F016` | P0 | `ADD` | `H-WP11` | **Hùng** | Raw OTP/credential chỉ được truyền transient/encrypted khi cần; không persist/log trong `notification_jobs`, `notification_deliveries`, audit hoặc DLQ lâu dài; message hết `expiresAt` không được gửi. | `ARCH-V3.3 + MODSPEC-V1.3` | — |
| `M10-F017` | P1 | `ADD` | `H-WP09` | **Hùng** | Theo dõi delivery attempt/status/provider/external message id/sanitized error; provider fallback nếu policy cho phép nhưng không phát duplicate sau success. | `MODSPEC-V1.3-M10` | — |
| `M10-F018` | P2 | `ADD` | `H-WP11` | **Hùng** | Bổ sung channel tương lai như SMS/Telegram/WhatsApp/Zalo/Discord bằng adapter/config + endpoint/template capability, không sửa schema business event của producer. | `ARCH-V3.3-M10` | — |

## M11. AI/ML Inference

> **Type:** Shared Capability  
> **Actor:** M02, M03  
> **Dependencies:** M02; M03; M09; M12; M13; M14  
> **Owner:** Thắng — Content, QR, Result & AI.

**Mục đích:** Python AI/ML Worker xử lý AI task bất đồng bộ qua RabbitMQ với provider registry mở; chỉ trả normalized prediction signal, không trả business verdict.

**Backlog:** 20 feature — P0 7, P1 7, P2 6.

| ID | Priority | Action | Work Package | Owner hiện tại | Feature hiện tại | Legacy/Source | Legacy owner (trace only) |
|---|---|---|---|---|---|---|---|
| `M11-F001` | P0 | `MODIFY` | `T-WP15` | **Thắng** | Triển khai **Python AI/ML Worker** consume `q.ai.analyze`; FastAPI nếu giữ chỉ dùng `/health`, `/ready`, diagnostics nội bộ, không phải đường inference của scan. | `ARCH/V3.2 + H11-01` | Hùng |
| `M11-F002` | P1 | `MODIFY` | `T-WP15` | **Thắng** | Feature flag `ai.enabled`; AI fail/quá `aiTaskDeadline` thì M12 finalize bằng tín hiệu còn lại với `degraded=true`, M09 renormalize thay vì coi AI = 0. | `ARCH/V3.2 + H11-02` | Hùng |
| `M11-F003` | P0 | `MODIFY` | `T-WP15` | **Thắng** | AI Worker publish `ai.analysis.completed\ | failed` vào `q.scan.result`; prediction normalized gồm `label`, `probability`, `providerKey`, `modelId`, `modelVersion`, `latencyMs`, không chứa `riskScore`/`riskLevel`. | `ARCH-V3.3 + H11-03` | Hùng |
| `M11-F004` | P0 | `MODIFY` | `T-WP15` | **Thắng** | AI task/result contract versioned có `eventId`, `correlationId`, `scanId`, `taskId`, `kind`, `deadlineAt`; consumer idempotent theo `eventId` + `taskId`. | `ARCH/V3.2 + H11-04` | Hùng |
| `M11-F005` | P1 | `MODIFY` | `T-WP15` | **Thắng** | M12/Spring Boot ghép AI result theo `scanId + taskId`, chuyển prediction thành AI `AnalysisSignal`, quản lý barrier rồi mới gọi M09 Risk Fusion. | `ARCH/V3.2 + H11-05` | Hùng |
| `M11-F006` | P1 | `MODIFY` | `T-WP15` | **Thắng** | Mask/minimize PII trong AI payload; AI Worker không đọc/ghi PostgreSQL/Redis/business tables và không gọi Spring Boot API. | `ARCH/V3.2 + H11-06` | Hùng |
| `M11-F007` | P2 | `MODIFY` | `T-WP17` | **Thắng** | Nếu dùng LLM/provider ngoài, chống prompt injection và ràng buộc output; LLM/model không được tự thay đổi business verdict. | `ARCH/V3.2 + H11-07` | Hùng |
| `M11-F008` | P2 | `MODIFY` | `T-WP17` | **Thắng** | Có thể cache inference theo input/model version nếu phù hợp; cache không được thay thế task/result/idempotency contract hoặc làm stale safety signal. | `ARCH/V3.2 + H11-08` | Hùng |
| `M11-F009` | P2 | `MODIFY` | `T-WP16` | **Thắng** | Local/fine-tuned model (`hf-local`, `custom-local`, Ollama/custom runtime) là provider implementation option; không thay đổi AMQP AI task/result contract. | `ARCH-V3.3 + H11-09` | Hùng |
| `M11-F010` | P0 | `KEEP` | `T-WP18` | **Thắng** | Bộ test tự động cho từng rule, chạy trong CI | `H12-01` | Hùng |
| `M11-F011` | P1 | `KEEP` | `T-WP18` | **Thắng** | Xây tập dữ liệu gán nhãn ~300–500 tin nhắn/URL tiếng Việt (scam / không scam) | `H12-02` | Hùng |
| `M11-F012` | P1 | `MODIFY` | `T-WP18` | **Thắng** | Script đo Precision / Recall / F1 / latency cho các cấu hình rule-reputation, AI-only (nghiên cứu) và hybrid theo cùng tập dữ liệu. | `H12-03` | Hùng |
| `M11-F013` | P1 | `KEEP` | `T-WP18` | **Thắng** | Ma trận nhầm lẫn và danh sách case sai để phân tích nguyên nhân | `H12-04` | Hùng |
| `M11-F014` | P2 | `KEEP` | `T-WP18` | **Thắng** | Tinh chỉnh trọng số rule dựa trên số liệu thay vì phỏng đoán | `H12-05` | Hùng |
| `M11-F015` | P2 | `KEEP` | `T-WP18` | **Thắng** | Theo dõi tỷ lệ báo động giả trên dữ liệu thật sau khi triển khai | `H12-06` | Hùng |
| `M11-F016` | P2 | `KEEP` | `T-WP18` | **Thắng** | So sánh với công cụ có sẵn (ChongLuaDao, PhishTank) trên cùng tập dữ liệu | `H12-07` | Hùng |
| `M11-F017` | P0 | `MODIFY` | `T-WP15` | **Thắng** | AI Worker có `handleAiTask`: validate → dedupe task → route model (`URL_FEATURES` / `TEXT_CONTENT` / `WEB_CONTENT`) → preprocess → inference → publish success/failure. | `ARCH/V3.2 + ARCH-22 (Architecture V3.2)` | — |
| `M11-F018` | P0 | `MODIFY` | `T-WP15` | **Thắng** | Operational default: `aiTaskDeadline=20s` ở M12, model/LLM timeout=10s trong AI Worker, `prefetch_count=4`; task phải publish FAILED khi timeout/model unavailable; retry exhausted → `q.ai.analyze.dlq`. | `ARCH-V3.3 + ARCH-AI-OPS` | — |
| `M11-F019` | P0 | `ADD` | `T-WP16` | **Thắng** | Inference Provider Resolver/registry map `kind + inferenceProfile` sang `providerKey + model config`; `providerKey` là extensible string, không phải closed enum. | `ARCH-V3.3-M11` | — |
| `M11-F020` | P1 | `ADD` | `T-WP16` | **Thắng** | Hỗ trợ provider adapter cho direct API, gateway/aggregator, custom HTTP và local/fine-tuned runtime; mọi output normalize về cùng `AiPrediction`, downstream không branch business rule theo provider/model. | `MODSPEC-V1.3-M11` | — |

## M12. Shared Scan Platform

> **Type:** Shared Platform  
> **Actor:** M02–M06; phối hợp M08–M11/M14  
> **Dependencies:** M08; M09; M11; M13; M14; M02–M05  
> **Owner:** Kiên — Reputation, Risk & Orchestration.

**Mục đích:** Sở hữu scan lifecycle/orchestration, Guest/User access/quota gate, queue dispatch, result consumption, nested scan, AI task barrier và finalization.

**Backlog:** 19 feature — P0 18, P1 1, P2 0.

| ID | Priority | Action | Work Package | Owner hiện tại | Feature hiện tại | Legacy/Source | Legacy owner (trace only) |
|---|---|---|---|---|---|---|---|
| `M12-F001` | P0 | `MODIFY` | `I-WP11` | **Kiên** | `Idempotency-Key` cho scan mutation qua M12/Redis; TTL là cấu hình của platform, không hard-code 24 giờ và phải gắn với request hash. | `H07-04` | Hùng |
| `M12-F002` | P0 | `KEEP` | `I-WP11` | **Kiên** | Trùng key nhưng khác request hash → trả `409 IDEMPOTENCY_KEY_CONFLICT` | `H07-05` | Hùng |
| `M12-F003` | P1 | `KEEP` | `I-WP11` | **Kiên** | Trả `429` kèm header `Retry-After` và thông báo thân thiện | `H07-06` | Hùng |
| `M12-F004` | P0 | `ADD` | `I-WP11` | **Kiên** | `ScanTypeResolver` + `ProcessorRegistry/JobRouter` map `URL`, `TEXT`, `ENTITY`, `QR` sang routing key/worker phù hợp. | `ARCH-24 (Architecture V3.2)` | — |
| `M12-F005` | P0 | `ADD` | `I-WP12` | **Kiên** | Scan lifecycle thống nhất `PENDING -> PROCESSING -> COMPLETED/FAILED`; không để scan treo `PROCESSING` vô hạn. | `ARCH-25 (Architecture V3.2)` | — |
| `M12-F006` | P0 | `ADD` | `I-WP12` | **Kiên** | Worker Result Consumer nhận kết quả qua `q.scan.result`, validate schema, deduplicate event và chuyển signal vào Signal Aggregator. | `ARCH-26 (Architecture V3.2)` | — |
| `M12-F007` | P0 | `ADD` | `I-WP12` | **Kiên** | Async event envelope có `eventId`, `jobId`, `correlationId`, `eventVersion`, `attempt`, `scanId`, processor/model version để trace pipeline. | `ARCH-27 (Architecture V3.2)` | — |
| `M12-F008` | P0 | `ADD` | `I-WP12` | **Kiên** | RabbitMQ at-least-once -> consumer phải idempotent theo `eventId`, duplicate ACK/ignore và không double-count signal. | `ARCH-28 (Architecture V3.2)` | — |
| `M12-F009` | P0 | `ADD` | `I-WP13` | **Kiên** | Nested scan dùng PostgreSQL `scan_relations` làm source of truth và Redis làm temporary barrier/counter. | `ARCH-29 (Architecture V3.2)` | — |
| `M12-F010` | P0 | `ADD` | `I-WP13` | **Kiên** | Nested scan: parent deadline 30 giây, `maxDepth=2`, cycle detection bằng normalized indicator hash/visited set. | `ARCH-30 (Architecture V3.2)` | — |
| `M12-F011` | P0 | `ADD` | `I-WP13` | **Kiên** | Child fail/timeout không nhất thiết fail parent; finalize bằng signal hiện có và `degraded=true` khi đủ dữ liệu. | `ARCH-31 (Architecture V3.2)` | — |
| `M12-F012` | P0 | `ADD` | `I-WP12` | **Kiên** | Worker delivery policy: attempt đầu + tối đa 2 retry; exhausted -> DLQ tương ứng, không auto-retry vô hạn. | `ARCH-32 (Architecture V3.2)` | — |
| `M12-F013` | P0 | `ADD` | `I-WP14` | **Kiên** | RabbitMQ topology có `q.ai.analyze` + `q.ai.analyze.dlq`; routing `ai.analysis.requested`, `ai.analysis.completed`, `ai.analysis.failed`; AI result đi vào `q.scan.result`. | `ARCH-V3.2-M12-01` | — |
| `M12-F014` | P0 | `ADD` | `I-WP14` | **Kiên** | PostgreSQL `scan_ai_tasks` là source of truth cho AI task chờ; Redis chỉ giữ `expectedAiTasks/completedAiTasks` và barrier tạm thời. | `ARCH-V3.2-M12-02` | — |
| `M12-F015` | P0 | `ADD` | `I-WP14` | **Kiên** | Completion barrier đếm **child scans + AI tasks**; `aiTaskDeadline=20s`, parent deadline=30s. Hết AI deadline → bỏ task treo, `degraded=true`, renormalize. | `ARCH-V3.2-M12-03` | — |
| `M12-F016` | P0 | `ADD` | `I-WP14` | **Kiên** | Xử lý race: AI result về trước worker result thì park theo `scanId` rồi match khi `pendingAiTasks[]` xuất hiện; AI result về sau COMPLETED chỉ ghi late signal/audit, không mutate `RiskResult`. | `ARCH-V3.2-M12-04` | — |
| `M12-F017` | P0 | `ADD` | `I-WP13` | **Kiên** | Scanner/AI worker chỉ giao tiếp qua RabbitMQ, không route tới PostgreSQL/Redis/Spring Boot API; M12 xử lý `DerivedIndicator.handling` và deferred reputation/child-scan policy. | `ARCH-V3.2-M12-05` | — |
| `M12-F018` | P0 | `ADD` | `I-WP11` | **Kiên** | M12 nhận `AccessContext`/`EntitlementContext` và enforce Guest/Free ownership + quota trước khi dispatch; GUEST được scan public inputs, FREE có persistent history, cả hai dùng logical `inferenceProfile=standard` trong MVP. | `MODSPEC-V1.3-M12` | — |
| `M12-F019` | P0 | `ADD` | `I-WP11` | **Kiên** | `scanId` không phải authorization token: đọc result/history phải kiểm tra guest-session ownership hoặc user ownership; Guest không có persistent account history. | `MODSPEC-V1.3-M12` | — |

## M13. Platform Infrastructure

> **Type:** Infrastructure  
> **Actor:** Toàn hệ thống  
> **Dependencies:** —  
> **Owner:** Khải — URL Detection & Platform / CI-CD.

**Mục đích:** Cung cấp Nginx, PostgreSQL, Redis, RabbitMQ, MinIO/S3, network isolation, secret management, deployment/CI và health/readiness.

**Backlog:** 35 feature — P0 18, P1 11, P2 6.

| ID | Priority | Action | Work Package | Owner hiện tại | Feature hiện tại | Legacy/Source | Legacy owner (trace only) |
|---|---|---|---|---|---|---|---|
| `M13-F001` | P0 | `KEEP` | `K-WP09` | **Khải** | Không lưu mật khẩu, OTP, số thẻ, CVV của người dùng dưới bất kỳ dạng nào | `H08-01` | Hùng |
| `M13-F002` | P0 | `KEEP` | `K-WP09` | **Khải** | Che dữ liệu nhạy cảm trong log (số tài khoản → `****1234`, SĐT → `+849****678`) | `H08-02` | Hùng |
| `M13-F003` | P0 | `MODIFY` | `K-WP10` | **Khải** | Docker Compose local/dev gồm PostgreSQL, Redis, RabbitMQ, MinIO, Spring Boot, scanner workers, Python AI/ML Worker, Notification Delivery Worker, Report Export Worker, Threat Ingestion Worker, Web/Admin/Mobile dev dependencies và Nginx theo Architecture V3.3. | `K11-01 + V3.3` | Khải |
| `M13-F004` | P0 | `KEEP` | `K-WP10` | **Khải** | Dockerfile cho từng service, dùng multi-stage build | `K11-02` | Khải |
| `M13-F005` | P0 | `MODIFY` | `K-WP09` | **Khải** | Cấu hình qua environment/secret management; không hardcode/commit JWT signing key, OAuth client secret, AI provider credential hay notification provider credential. | `K11-03 + V3.3` | Khải |
| `M13-F006` | P0 | `MODIFY` | `K-WP10` | **Khải** | Health/readiness cho Spring Boot, scanner workers, AI Worker, Notification Worker, Report Export/Threat Ingestion và dependency chính; FastAPI nếu dùng cho AI chỉ giữ health/readiness/diagnostics. | `K11-04 + V3.3` | Khải |
| `M13-F007` | P1 | `KEEP` | `K-WP12` | **Khải** | `docker-compose.prod.yml` kèm Nginx và TLS Let's Encrypt | `K11-05` | Khải |
| `M13-F008` | P1 | `MODIFY` | `K-WP10` | **Khải** | RabbitMQ và MinIO là thành phần kiến trúc hiện tại, cấu hình ngay trong local stack; không coi là addon chỉ tới “M4”. | `K11-06` | Khải |
| `M13-F009` | P0 | `KEEP` | `K-WP12` | **Khải** | Script khởi tạo và migration cơ sở dữ liệu (Flyway/Liquibase) | `K11-07` | Khải |
| `M13-F010` | P1 | `KEEP` | `K-WP13` | **Khải** | Sao lưu và phục hồi PostgreSQL định kỳ | `K11-08` | Khải |
| `M13-F011` | P0 | `MODIFY` | `K-WP12` | **Khải** | CD triển khai MVP lên staging/VPS từ image/version đã build; chạy migration, health/readiness và smoke check theo quy trình reproducible, không phụ thuộc SSH thủ công tùy hứng | `K11-09` | Khải |
| `M13-F012` | P0 | `KEEP` | `K-WP11` | **Khải** | GitHub Actions chạy test của cả 3 service trên mỗi pull request | `K12-01` | Khải |
| `M13-F013` | P0 | `KEEP` | `K-WP11` | **Khải** | Chặn merge khi test đỏ | `K12-02` | Khải |
| `M13-F014` | P1 | `KEEP` | `K-WP11` | **Khải** | Kiểm tra định dạng và lint (Checkstyle, ruff, ESLint) | `K12-03` | Khải |
| `M13-F015` | P1 | `KEEP` | `K-WP11` | **Khải** | Đo độ phủ test, hiển thị badge | `K12-04` | Khải |
| `M13-F016` | P1 | `KEEP` | `K-WP13` | **Khải** | Log có cấu trúc (JSON) kèm `X-Request-Id` xuyên suốt mọi hop | `K12-05` | Khải |
| `M13-F017` | P1 | `KEEP` | `K-WP11` | **Khải** | Quét lỗ hổng phụ thuộc (Dependabot / OWASP Dependency-Check) | `K12-06` | Khải |
| `M13-F018` | P2 | `KEEP` | `K-WP13` | **Khải** | Metrics Prometheus + dashboard Grafana | `K12-07` | Khải |
| `M13-F019` | P2 | `KEEP` | `K-WP13` | **Khải** | Cảnh báo khi tỷ lệ lỗi hoặc độ trễ vượt ngưỡng | `K12-08` | Khải |
| `M13-F020` | P0 | `MODIFY` | `K-WP12` | **Khải** | GitHub Actions tự động build và push Docker image khi merge vào `main` hoặc tạo release tag; image tag trace được commit/version để dùng cho CD | `K12-09` | Khải |
| `M13-F021` | P1 | `KEEP` | `K-WP14` | **Khải** | Tách toàn bộ chuỗi giao diện ra file ngôn ngữ, mặc định tiếng Việt | `R12-01` | Kiên |
| `M13-F022` | P1 | `KEEP` | `K-WP14` | **Khải** | Tương phản màu và cỡ chữ đạt WCAG AA | `R12-02` | Kiên |
| `M13-F023` | P1 | `KEEP` | `K-WP14` | **Khải** | Điều hướng được hoàn toàn bằng bàn phím | `R12-03` | Kiên |
| `M13-F024` | P1 | `KEEP` | `K-WP14` | **Khải** | Không truyền đạt thông tin chỉ bằng màu (kèm biểu tượng và chữ cho SAFE/CAUTION/DANGER) | `R12-04` | Kiên |
| `M13-F025` | P2 | `KEEP` | `K-WP14` | **Khải** | Bản dịch tiếng Anh | `R12-05` | Kiên |
| `M13-F026` | P2 | `KEEP` | `K-WP14` | **Khải** | Nhãn ARIA và kiểm thử với trình đọc màn hình | `R12-06` | Kiên |
| `M13-F027` | P2 | `KEEP` | `K-WP14` | **Khải** | Chế độ chữ lớn dành cho người cao tuổi | `R12-07` | Kiên |
| `M13-F028` | P0 | `KEEP` | `K-WP14` | **Khải** | Share Target nhận link và văn bản từ Zalo/Messenger/SMS, tự định tuyến theo nội dung | `T11-04` | Thắng |
| `M13-F029` | P2 | `FUTURE_SCOPE` | `K-WP14` | **Khải** | Widget quét nhanh trên màn hình chính | `T11-08` | Thắng |
| `M13-F030` | P0 | `MODIFY` | `K-WP10` | **Khải** | RabbitMQ topology chuẩn: `antiscan.topic`, `antiscan.dlx`, queues `q.scan.url`, `q.scan.text`, `q.scan.entity`, `q.scan.qr`, `q.ai.analyze`, `q.scan.result`, `q.report.export`, `q.notification`, `q.threat.ingest` + DLQ tương ứng. | `ARCH-V3.3-MQ` | — |
| `M13-F031` | P0 | `MODIFY` | `K-WP10` | **Khải** | PostgreSQL là source of truth; Redis chỉ cache/session-revocation/rate-limit/idempotency/lock/temporary coordination và không giữ canonical business data duy nhất. | `ARCH-V3.3-DATA` | — |
| `M13-F032` | P0 | `ADD` | `K-WP10` | **Khải** | Nginx chịu TLS/reverse proxy/coarse IP-flood limit; Spring Boot + Redis chịu business quota theo user/account/API identity. | `ARCH-35 (Architecture V3.2)` | — |
| `M13-F033` | P0 | `ADD` | `K-WP10` | **Khải** | MinIO/S3 lưu evidence/export/binary; PostgreSQL chỉ lưu metadata/object key/hash/MIME/size/owner relation. | `ARCH-36 (Architecture V3.2)` | — |
| `M13-F034` | P0 | `ADD` | `K-WP10` | **Khải** | Network segmentation/egress policy: scanner/AI worker không truy cập PostgreSQL/Redis/core API; URL Worker chỉ thêm outbound public fetch SSRF-safe; AI/Notification worker chỉ thêm egress tới provider được cấu hình; Ingestion Worker tới feed + PG/Redis. | `ARCH-V3.3-NET` | — |
| `M13-F035` | P0 | `ADD` | `K-WP09` | **Khải** | Quản lý secret/key tập trung cho JWT signing keys, OAuth/OIDC client credentials, AI provider credentials và notification provider credentials; rotate được và không lưu raw secret trong business tables. | `ARCH-V3.3-SECRETS` | — |

## M14. Audit & Observability

> **Type:** Shared Platform / Admin Feature  
> **Actor:** Admin, Internal Operations  
> **Dependencies:** M01; M12; M13  
> **Owner:** Hùng — Identity, Community, Notification & Audit.

**Mục đích:** Tập trung audit trail và operational metadata cho auth/security, admin, scan/worker/AI, retry/DLQ và correlation chain.

**Backlog:** 11 feature — P0 5, P1 4, P2 2.

| ID | Priority | Action | Work Package | Owner hiện tại | Feature hiện tại | Legacy/Source | Legacy owner (trace only) |
|---|---|---|---|---|---|---|---|
| `M14-F001` | P0 | `MODIFY` | `H-WP16` | **Hùng** | Audit thao tác nhạy cảm: login/logout, refresh-token reuse/session revoke, password reset/change, admin duyệt báo cáo và admin sửa rule/policy. | `H05-01 + V3.3` | Hùng |
| `M14-F002` | P0 | `MODIFY` | `H-WP16` | **Hùng** | Persist `audit_records`/`admin_actions` theo append-only semantics phù hợp; không để module khác tự tạo schema audit riêng. | `H05-02` | Hùng |
| `M14-F003` | P0 | `KEEP` | `H-WP16` | **Hùng** | Lưu `X-Request-Id` (correlation ID) trong mỗi bản ghi | `H05-03` | Hùng |
| `M14-F004` | P1 | `KEEP` | `H-WP18` | **Hùng** | Trang xem audit log cho admin, lọc theo user / hành động / khoảng thời gian | `H05-04` | Hùng |
| `M14-F005` | P1 | `KEEP` | `H-WP18` | **Hùng** | Ghi kèm IP và User-Agent | `H05-05` | Hùng |
| `M14-F006` | P2 | `KEEP` | `H-WP18` | **Hùng** | Xuất audit log ra CSV phục vụ điều tra | `H05-06` | Hùng |
| `M14-F007` | P2 | `KEEP` | `H-WP18` | **Hùng** | Cảnh báo khi phát hiện chuỗi thao tác bất thường | `H05-07` | Hùng |
| `M14-F008` | P0 | `ADD` | `H-WP17` | **Hùng** | Chuẩn hóa correlation metadata xuyên pipeline: `correlationId`, `eventId`, `jobId`, `taskId`, `scanId`, `processorVersion`, `eventVersion`; cho phép truy vết từ request tới worker/AI/DLQ. | `ARCH-V3.2-M14-01` | — |
| `M14-F009` | P1 | `ADD` | `H-WP17` | **Hùng** | Ghi operational metadata cho scan/worker/AI failure, retry, DLQ và late AI result; không tạo business effect lần hai khi event bị duplicate. | `ARCH-V3.2-M14-02` | — |
| `M14-F010` | P1 | `ADD` | `H-WP18` | **Hùng** | Admin/Operations có thể lọc audit theo actor/action/resource/time và các correlation IDs; sensitive fields phải mask/redact. | `ARCH-V3.2-M14-03` | — |
| `M14-F011` | P0 | `ADD` | `H-WP16` | **Hùng** | Security audit cho auth-session lifecycle/reuse detection phải trace được theo user/session/correlation nhưng tuyệt đối không ghi raw access token, refresh token, OTP hoặc provider assertion. | `ARCH-V3.3-AUDIT` | — |

## FUTURE. Product Extensions ngoài M01–M14

> **Type:** Future Scope  
> **Actor:** User/Admin/API Client tương lai  
> **Dependencies:** —  
> **Owner:** Hùng — Identity, Community, Notification & Audit.

**Mục đích:** Các extension không thuộc MVP: browser extension/knowledge/gamification và commercialization như plan, subscription, usage, credit, API credentials.

**Backlog:** 33 feature — P0 0, P1 0, P2 33.

| ID | Priority | Action | Work Package | Owner hiện tại | Feature hiện tại | Legacy/Source | Legacy owner (trace only) |
|---|---|---|---|---|---|---|---|
| `FUTURE-F001` | P2 | `FUTURE_SCOPE` | `K-FWP01` | **Khải** | Extension Chrome/Edge cảnh báo khi truy cập tên miền trong danh sách đen | `K10-01` | Khải |
| `FUTURE-F002` | P2 | `FUTURE_SCOPE` | `K-FWP01` | **Khải** | Chèn nhãn an toàn cạnh link trong kết quả tìm kiếm và mạng xã hội | `K10-02` | Khải |
| `FUTURE-F003` | P2 | `FUTURE_SCOPE` | `K-FWP01` | **Khải** | Chuột phải vào link để quét ngay | `K10-03` | Khải |
| `FUTURE-F004` | P2 | `FUTURE_SCOPE` | `K-FWP01` | **Khải** | Chặn trang nguy hiểm bằng màn hình cảnh báo có nút bỏ qua | `K10-04` | Khải |
| `FUTURE-F005` | P2 | `FUTURE_SCOPE` | `K-FWP01` | **Khải** | Đồng bộ lịch sử quét với tài khoản | `K10-05` | Khải |
| `FUTURE-F006` | P2 | `FUTURE_SCOPE` | `K-FWP01` | **Khải** | Chế độ ngoại tuyến dùng danh sách đen tải sẵn | `K10-06` | Khải |
| `FUTURE-F007` | P2 | `FUTURE_SCOPE` | `I-FWP01` | **Kiên** | Bài viết hướng dẫn nhận biết từng loại lừa đảo | `R09-01` | Kiên |
| `FUTURE-F008` | P2 | `FUTURE_SCOPE` | `I-FWP01` | **Kiên** | Danh mục theo chủ đề, có tìm kiếm | `R09-02` | Kiên |
| `FUTURE-F009` | P2 | `FUTURE_SCOPE` | `I-FWP01` | **Kiên** | Gợi ý bài viết liên quan ngay trong trang kết quả quét | `R09-03` | Kiên |
| `FUTURE-F010` | P2 | `FUTURE_SCOPE` | `I-FWP01` | **Kiên** | Trang admin soạn và xuất bản bài viết | `R09-04` | Kiên |
| `FUTURE-F011` | P2 | `FUTURE_SCOPE` | `I-FWP01` | **Kiên** | Cảnh báo xu hướng lừa đảo mới theo tuần | `R09-05` | Kiên |
| `FUTURE-F012` | P2 | `FUTURE_SCOPE` | `I-FWP01` | **Kiên** | Nội dung dạng thẻ ngắn cho người lớn tuổi, chữ to, ít thuật ngữ | `R09-06` | Kiên |
| `FUTURE-F013` | P2 | `FUTURE_SCOPE` | `T-FWP01` | **Thắng** | Giao diện hỏi đáp: "Tin nhắn này có phải lừa đảo không?" | `R10-01` | Kiên |
| `FUTURE-F014` | P2 | `FUTURE_SCOPE` | `T-FWP01` | **Thắng** | Trả lời dựa trên thư viện kiến thức, có trích dẫn nguồn | `R10-02` | Kiên |
| `FUTURE-F015` | P2 | `FUTURE_SCOPE` | `T-FWP01` | **Thắng** | Gợi ý hành động tiếp theo tuỳ tình huống người dùng mô tả | `R10-03` | Kiên |
| `FUTURE-F016` | P2 | `FUTURE_SCOPE` | `T-FWP01` | **Thắng** | Bàn giao sang biểu mẫu báo cáo khi xác định là lừa đảo | `R10-04` | Kiên |
| `FUTURE-F017` | P2 | `FUTURE_SCOPE` | `T-FWP01` | **Thắng** | Giới hạn phạm vi trả lời, từ chối câu hỏi ngoài lĩnh vực | `R10-05` | Kiên |
| `FUTURE-F018` | P2 | `FUTURE_SCOPE` | `T-FWP01` | **Thắng** | Ghi nhận câu hỏi thường gặp để bổ sung vào thư viện | `R10-06` | Kiên |
| `FUTURE-F019` | P2 | `FUTURE_SCOPE` | `T-FWP02` | **Thắng** | Bài trắc nghiệm "Bạn có nhận ra tin nhắn lừa đảo không?" | `R11-01` | Kiên |
| `FUTURE-F020` | P2 | `FUTURE_SCOPE` | `T-FWP02` | **Thắng** | Chấm điểm và giải thích từng câu sai | `R11-02` | Kiên |
| `FUTURE-F021` | P2 | `FUTURE_SCOPE` | `H-FWP02` | **Hùng** | Huy hiệu theo mốc: số lần quét, số báo cáo được duyệt | `R11-03` | Kiên |
| `FUTURE-F022` | P2 | `FUTURE_SCOPE` | `H-FWP02` | **Hùng** | Bảng xếp hạng đóng góp cộng đồng | `R11-04` | Kiên |
| `FUTURE-F023` | P2 | `FUTURE_SCOPE` | `H-FWP02` | **Hùng** | Chuỗi ngày sử dụng liên tục | `R11-05` | Kiên |
| `FUTURE-F024` | P2 | `FUTURE_SCOPE` | `K-FWP03` | **Khải** | Chia sẻ kết quả bài kiểm tra lên mạng xã hội | `R11-06` | Kiên |
| `FUTURE-F025` | P2 | `FUTURE_SCOPE` | `H-FWP01` | **Hùng** | Entitlement profiles định nghĩa quota/featureFlags/historyPolicy/inferenceProfile cho các plan tương lai; M12 chỉ nhận resolved `EntitlementContext`. | `MODSPEC-V1.3-FUTURE` | — |
| `FUTURE-F026` | P2 | `FUTURE_SCOPE` | `H-FWP01` | **Hùng** | Plans catalog cho Free/Premium/API tiers hoặc gói tương lai; không hard-code plan trong scan/worker contract. | `SCHEMA-V1.0-FUTURE` | — |
| `FUTURE-F027` | P2 | `FUTURE_SCOPE` | `H-FWP01` | **Hùng** | Account-plan assignment có hiệu lực theo thời gian và audit thay đổi entitlement. | `SCHEMA-V1.0-FUTURE` | — |
| `FUTURE-F028` | P2 | `FUTURE_SCOPE` | `H-FWP01` | **Hùng** | Subscription lifecycle độc lập với Identity/Scan; trạng thái subscription không đi trực tiếp vào worker message. | `SCHEMA-V1.0-FUTURE` | — |
| `FUTURE-F029` | P2 | `FUTURE_SCOPE` | `K-FWP02` | **Khải** | Usage metering theo account/API identity và loại execution; ghi `usage_records` để phục vụ quota/billing sau này. | `SCHEMA-V1.0-FUTURE` | — |
| `FUTURE-F030` | P2 | `FUTURE_SCOPE` | `K-FWP02` | **Khải** | Credit account cho user/API client; số dư được suy ra từ ledger, không lưu `credit_balance` trực tiếp trong `users`. | `SCHEMA-V1.0-FUTURE` | — |
| `FUTURE-F031` | P2 | `FUTURE_SCOPE` | `K-FWP02` | **Khải** | Credit ledger entries trace đầy đủ debit/credit/adjustment và correlation tới usage/billing event. | `SCHEMA-V1.0-FUTURE` | — |
| `FUTURE-F032` | P2 | `FUTURE_SCOPE` | `K-FWP02` | **Khải** | Credit reservation/settlement/release cho tác vụ tốn tài nguyên trước khi execution, không làm thay đổi `RiskResult` contract. | `SCHEMA-V1.0-FUTURE` | — |
| `FUTURE-F033` | P2 | `FUTURE_SCOPE` | `K-FWP02` | **Khải** | User-facing API credentials/API client identity với key hash/revoke/rotate; downstream vẫn nhận `AccessContext`, không truyền raw API key tới worker. | `SCHEMA-V1.0-FUTURE` | — |

## 5. Quy tắc duy trì backlog

1. Thay đổi `ScanType`, queue/routing key, auth/session contract, notification contract, event envelope, Risk Fusion hoặc data ownership phải sửa Architecture/Module Spec trước.
2. Feature UI/API/worker/data/test thuộc cùng module Mxx; không tạo ownership riêng theo frontend/backend.
3. **Work Package là đơn vị giao việc chính**; feature `Fxxx` là acceptance criteria/traceability. Không cân tiến độ bằng cách đếm raw feature rows.
4. Mọi feature, kể cả Future Scope, phải có `Work Package` và `Owner hiện tại`; Future owner chỉ mang nghĩa nghiên cứu/POC cho tới khi scope được promote.
5. Owner hiện tại được chốt theo workstream/WP ở mục 2.1–2.3; đổi owner/module/WP phải cập nhật đồng thời Markdown, Excel và kế hoạch tích hợp nhưng **không tự động tăng revision khỏi V2**.
6. Feature mới phải có module, priority, WP, owner, contract/dependency và Definition of Done tương ứng.
7. `Legacy/Source`/`Legacy owner` chỉ để truy vết; không dùng Legacy owner làm boundary runtime.
8. Cross-owner dependency phải có mock/fixture/contract test để không biến thành blocker phát triển.
9. Schema V1.0 hiện vẫn là Draft; thay đổi schema trước khi team approve vẫn giữ version V1.0 theo quy ước hiện tại.
