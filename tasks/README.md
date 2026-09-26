# Task Specifications — 2 tuần đầu (V2 rebalanced)

**Khoảng:** 27/09/2026 → 12/10/2026  
**Điểm thay đổi:** Khải nhận lại M13 Platform Infrastructure; CI được làm ngay W09, runtime/topology ở W10, CD sẽ vào W11 và phải xong trước 20/10.

> **Nguồn chuẩn:** Architecture V3.3 · Modules Specification V1.3 · Schema V1.0 Draft · Feature Backlog V2.

## W09 — 27/09 → 05/10

| Owner | Task |
|---|---|
| Hùng | H-WP03 · Session & Token Security |
| Khải | K-WP09 · Data Privacy & Secret Baseline |
| Khải | K-WP11 · CI Baseline & Quality Gates |
| Kiên | I-WP11 · Request Controls, Authorization & Scan Dispatch |
| Thắng | T-WP01 · Vietnamese Text Normalization & Evasion Handling |
| Cả nhóm | TEAM-C01 · Contract Freeze v1 + Baseline Audit |

## W10 — 06/10 → 12/10

| Owner | Task |
|---|---|
| Hùng | H-WP01 · Local Registration & Email Verification |
| Khải | K-WP10 · Platform Runtime, Containers, Core Topology & Readiness |
| Khải | K-WP01 · URL Intake, Normalization, Cache & Basic Signals |
| Kiên | I-WP12 · Scan Lifecycle, Event Envelope, Retry & Idempotency |
| Thắng | T-WP02 · Async Text Scan, Scam Patterns & User Result UX |
| Cả nhóm | TEAM-C02 · Integration Checkpoint 1 |


## Mapping đã thay đổi so với bản đặc tả trước

Bản cũ **không còn dùng** cho việc giao task 2 tuần đầu. Các thay đổi bắt buộc:

```text
W09 cũ:  Khải K-WP01 · URL Intake
W09 mới: Khải K-WP09 · Data Privacy & Secret Baseline
         Khải K-WP11 · CI Baseline & Quality Gates

W10 cũ: Khải K-WP02 · Safe Fetch & SSRF Guard
W10 mới: Khải K-WP10 · Platform Runtime, Containers, Core Topology & Readiness
         Khải K-WP01 · URL Intake, Normalization, Cache & Basic Signals

W11 kế tiếp: Khải K-WP12 · MVP CD, Migration & Release Automation
              Khải K-WP02 · Safe Fetch & SSRF Guard
```

Lý do: M13 Platform Infrastructure + CI/CD đã trả về Khải; CI phải là merge gate ngay từ W09 và CD phải hoàn tất trước 20/10 để 30/10 là **MVP release thực sự**, không chỉ demo local.

## Cách dùng

1. WP là đơn vị giao việc; không tách frontend/backend cho hai owner khác nhau.
2. Task Done chỉ khi có PR/test/demo evidence.
3. Cross-owner dependency phải qua contract/mock/fixture.
4. CI gate không được bypass để merge nhanh.
5. K-WP12 CD bắt đầu W11 và target Done trước 20/10.
