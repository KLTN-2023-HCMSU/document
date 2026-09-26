# Anti-Scam — Task Specifications W09–W10 (Timeline V2.1)

Bộ đặc tả này triển khai **8 task cá nhân** trong W09 và W10 của Timeline V2.1. Feature Backlog V2 **không bị sửa**; mỗi task chỉ là một implementation slice để đạt MVP breadth-complete vào 30/10.

## Thứ tự phụ thuộc

```text
W09
H-MVP01 ───────────────→ H-MVP02
K-MVP01 ───────────────→ K-MVP02
I-MVP01 ───────────────→ I-MVP02
T-MVP01 ───────────────→ T-MVP02

Cross-owner contracts:
I-MVP01 → K-MVP02 / T-MVP02
K-MVP01 → I-MVP02 URL result fixture
T-MVP01 → I-MVP02 Text result fixture
H-MVP01 → mock delivery/audit boundary cho team
```

## W09 — Foundation & Contract Freeze

1. [H-MVP01 — Identity Foundation + Shared Delivery/Audit Contracts](W09/H-MVP01_Identity_Foundation_Shared_Contracts.md)
2. [K-MVP01 — Secrets + CI + URL Worker Contract](W09/K-MVP01_Secrets_CI_URL_Worker_Contract.md)
3. [I-MVP01 — Scan / Entity / Risk Contracts + Rule Repository Baseline](W09/I-MVP01_Scan_Entity_Risk_Contracts_Rule_Repository.md)
4. [T-MVP01 — Text Normalization + Worker / Result Contracts](W09/T-MVP01_Text_Normalization_Worker_Result_Contracts.md)

### Gate W09

- `AccessContext`, `EntitlementContext` freeze.
- Scan type/routing + entity `NO_DATA` freeze.
- Worker result / `DerivedIndicator` / `pendingAiTasks` freeze.
- `RiskResult`, `RiskEvaluationPort`, `ThreatQuery` freeze.
- `NotificationPort`, `AuditPort` freeze.
- CI required checks xanh; red test chặn merge.

## W10 — Core Vertical Slices

1. [H-MVP02 — Session, Guest, RBAC, Basic Profile & Quota](W10/H-MVP02_Session_Guest_RBAC_Profile_Quota.md)
2. [K-MVP02 — Platform Runtime + URL Basic Vertical Slice](W10/K-MVP02_Platform_Runtime_URL_Basic_Vertical_Slice.md)
3. [I-MVP02 — Scan Lifecycle + Risk Entity Registry + Risk Core](W10/I-MVP02_Scan_Lifecycle_Risk_Entity_Registry_Risk_Core.md)
4. [T-MVP02 — Async Text Scan + Result / History / Basic Export](W10/T-MVP02_Async_Text_Result_History_Basic_Export.md)

### Gate W10

Cuối W10 phải demo được tối thiểu:

```text
Local register/verify/login/session
Guest AccessContext + quota basic
URL submit → async worker/basic signals → RiskResult
Text submit → async worker/basic patterns → RiskResult
Result detail + authenticated history
Rule Engine + Risk Fusion + Result Composer
Docker/runtime topology + health/readiness
```

## Nguyên tắc sử dụng các file spec

- `Required` = phải xong trong task để không block tuần sau.
- `Contract only` = chốt interface/fixture; implementation sâu có thể ở tuần sau.
- `Basic` = happy path đủ demo MVP; edge cases/depth có thể post-MVP.
- `Deferred` = vẫn giữ nguyên trong Feature Backlog, **không bị xoá/hạ priority bởi bộ spec này**.
- Nếu task spec khác Architecture V3.3 / Modules Specification V1.3 thì source cấp cao hơn thắng.
