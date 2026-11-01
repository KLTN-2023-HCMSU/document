# CORE-01/R01 · Scan Intake, Idempotency, Quota, Ownership — Báo cáo kiểm thử

| Trường | Giá trị |
|---|---|
| **Mã nhóm** | `CORE-01`, `R01` (W10) |
| **Người kiểm thử** | Kiên / AI Agent (thông tin kết quả do Agent cung cấp) |
| **Ngày chạy** | 15–19/10/2026; tổng hợp 19/10 |
| **Phiên bản code** | Snapshot `(6)`, branch `feat/CORE-01-R01-scan-lifecycle`; **chưa xác minh SHA** |
| **Môi trường** | JUnit/Maven; Python contracts; Docker PostgreSQL + Redis + RabbitMQ smoke (theo Agent) |

## 1. Mục tiêu kiểm thử

Chứng minh POST Scan không tạo duplicate scan/outbox khi retry hoặc race; quota không bị trừ sai do replay/rollback; status chỉ chủ sở hữu/guest proof hợp lệ được đọc; payload nhạy cảm không lộ trong display/outbox.

## 2. Chiến lược

| Tầng | Kiểm thử | Công cụ |
|---|---|---|
| Unit/contract | W09 typed payload, access/quota, NEW/REPLAY/CONFLICT | JUnit 5 |
| Persistence | PostgreSQL uniqueness, rollback, concurrency | `ScanPersistenceHardeningTest`, runtime smoke |
| Security | Owner/IDOR, guest proof domain | `ScanW10DomainTest`, smoke |
| Crypto | AES-GCM/masking | `ScanPayloadProtectionTest` |
| Integration | HTTP/PG/Redis/migration | `scripts/ci-runtime-smoke.py` |

## 3. Bảng ca kiểm thử

| # | Ca | Đầu vào | Kỳ vọng | Bằng chứng/kết quả quan sát | Đạt |
|---|---|---|---|---|---|
| 1 | POST URL | token hợp lệ + key + URL | `202` + scanId | Smoke PASS theo Agent 19/10 | ✅* |
| 2 | Durable replay | cùng actor/key/hash | cùng scanId, 1 scan/outbox | Smoke PASS theo Agent | ✅* |
| 3 | Key conflict | cùng key, khác body | Từ chối, không job mới | Smoke PASS theo Agent | ✅* |
| 4 | DB winner concurrency | same key concurrent | một durable winner | `ScanPersistenceHardeningTest` 6 focused PASS, smoke đã có case | ✅* |
| 5 | Quota refund DB rollback | rollback/trở lại | quota phục hồi, retry thành công | Smoke PASS theo Agent, **chưa đủ cross-window case** | ⚠ |
| 6 | Quota refund qua bucket boundary | reserve trước, release sau window | refund đúng reservation cũ | `RedisQuotaGate` còn tự tính bucket mới, **chưa có test đạt** | ❌ |
| 7 | Idempotent double refund | release cùng token hai lần | chỉ hoàn 1 lần | **Chưa có reservation token** | ❌ |
| 8 | Redis outage | Redis offline | fail-closed, restore chạy lại | Smoke PASS theo Agent | ✅* |
| 9 | Authenticated IDOR | owner và user khác | owner success, other denied | Smoke PASS theo Agent | ✅* |
| 10 | Guest proof HTTP E2E | valid/expired/forged/wrong-owner | allow/deny đúng | Mới domain evidence, **chưa đủ HTTP E2E** | ⚠ |
| 11 | AES-GCM & masking | URL/TEXT/ENTITY | display/outbox không raw | `ScanPayloadProtectionTest` + source | ✅* |
| 12 | Re-run after follow-up `(6)` | full suite | tất cả tests PASS | Focused 11 PASS do Agent; **runtime exit code gián đoạn** | ⚠ |

`✅*`: PASS **theo báo cáo Agent**, chưa được tác giả bộ báo cáo chạy lại độc lập từ ZIP `(6)`.

## 4. Cách chạy lại toàn bộ

```bash
cd Scam-Risk-Detector/services/api
./mvnw -B test
cd ../..
python3 -m unittest discover -s contracts/tests -v
python3 scripts/ci-runtime-smoke.py
bash scripts/scan-secrets.sh
```

**Số liệu đã báo cáo:** 155 Maven tests PASS và 21 Python tests PASS ở thời điểm trước follow-up; 11 focused tests PASS sau follow-up. Không ghi `CI xanh` nếu chưa có run/URL từ CI ở commit nghiệm thu.

## 5. Ca không đạt/đạt một phần

| # | Ca | Sai ở đâu | Nguyên nhân | Xử lý | Hạn |
|---|---|---|---|---|---|
| 6–7 | Cross-window/double quota refund | Bucket refund không cố định, DECR có thể lặp | Thiếu reservation token | Redesign `RedisQuotaGate` + Redis concurrency tests | W12 (đề xuất) |
| 10 | Guest HTTP E2E | Chưa chứng minh production issuer | Thiếu Hùng handoff | Test Guest POST/GET với signer thật | Sau handoff |
| 12 | Runtime full rerun | Mất tool transport/exit code | Môi trường Agent | Rerun giữ log & exit status | Trước review |

## 6. Số liệu

Không có Precision/Recall/F1: W10 không thực hiện phân loại rủi ro. Chưa có benchmark throughput/latency hoặc coverage % được xác minh.

## 7. Kết luận

| Câu hỏi | Trả lời |
|---|---|
| Số ca liệt kê | 12 |
| Đạt theo báo cáo trước / đạt một phần / chưa đạt | 7 PASS theo Agent; 3 partial; 2 fail/unverified — cần chạy lại trên commit nghiệm thu |
| Đủ điều kiện nghiệm thu? | **Chưa ký nghiệm thu G2 theo tiêu chí chặt về quota/guest E2E/peer review** |
| Rủi ro còn lại | Quota cross-window, double refund, production guest proof |
| Kiến nghị | Sửa K1; rerun PG+Redis; mời Hùng review |

*Người tổng hợp: Kiên · 19/10/2026 · Mẫu: BCKT v1.0*
