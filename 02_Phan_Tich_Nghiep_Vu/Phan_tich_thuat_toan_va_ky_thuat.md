# Phân tích Thuật toán và Kỹ thuật cho Nền tảng Anti-Scam

**Ngày cập nhật:** 21/07/2026  
**Mục đích:** Đối chiếu các thuật toán được sử dụng trong các hệ thống phát hiện lừa đảo hiện đại với yêu cầu của đề tài khóa luận

---

## 1. Tổng quan về các hướng tiếp cận

Dựa trên khảo sát các nghiên cứu gần đây (2024-2026) và các hệ thống thực tế, có **3 hướng chính** để phát hiện lừa đảo trực tuyến:

| Hướng tiếp cận | Ưu điểm | Nhược điểm | Phù hợp với đề tài |
|---|---|---|---|
| **Rule-based (Dựa trên luật)** | - Giải thích được rõ ràng<br>- Triển khai nhanh<br>- Không cần dữ liệu lớn<br>- Dễ kiểm thử | - Cứng nhắc<br>- Cần cập nhật thủ công<br>- Dễ bị kẻ tấn công học pattern | ⭐⭐⭐⭐⭐<br>Rất phù hợp cho MVP |
| **Machine Learning** | - Tự học pattern<br>- Phát hiện mẫu mới<br>- Độ chính xác cao (90-98%) | - Cần dataset lớn<br>- "Black box"<br>- Chi phí training<br>- Cần chuyên gia ML | ⭐⭐⭐<br>Hướng mở rộng |
| **Hybrid (Kết hợp)** | - Cân bằng ưu nhược điểm<br>- Rule xử lý nhanh, ML xử lý phức tạp<br>- Giải thích được | - Phức tạp hơn<br>- Cần duy trì 2 hệ thống | ⭐⭐⭐⭐<br>Mục tiêu dài hạn |

**Khuyến nghị cho đề tài:** Bắt đầu với **Rule-based Engine** + **Heuristic Analysis**, sau đó mở rộng sang **ML** trong phần "Hướng phát triển".

---

## 2. Module 1: URL/Link Scanner

### 2.1. Các thuật toán nghiên cứu hiện đại

Theo nghiên cứu năm 2024-2026, các thuật toán ML phổ biến cho phát hiện phishing URL:


| Thuật toán | Độ chính xác | Nguồn nghiên cứu | Ưu điểm | Nhược điểm |
|---|---|---|---|---|
| **Random Forest** | 98% | ResearchGate 2024 | Chống overfitting tốt, xử lý nhiều feature | Cần nhiều cây → chậm |
| **XGBoost** | 90.4% | IJSRA 2026 | Nhanh, hiệu quả, xử lý missing values | Phức tạp, khó tune |
| **MLP (Neural Network)** | 90.3% | IJSRA 2026 | Học pattern phi tuyến | Cần dataset lớn, lâu train |
| **1D CNN** | 95%+ | MDPI 2024 | Trích xuất pattern tự động từ URL | Cần GPU, phức tạp |
| **Naive Bayes** | 85-92% | Nhiều nguồn | Đơn giản, nhanh, ít tài nguyên | Giả định độc lập yếu |

**41 features phổ biến** được sử dụng (theo nghiên cứu Frontiers 2024):
- Độ dài URL, số subdomain, số dấu gạch ngang
- Có HTTPS hay không, tuổi domain
- Có ký tự lạ (@, //, ...), IP thay vì domain
- Entropy của URL, số chữ số, số ký tự đặc biệt
- Độ giống với brand nổi tiếng (typosquatting)

### 2.2. Thuật toán đề xuất cho đề tài (MVP)

**Heuristic + Rule-based Analysis** (không cần ML):

```
Bước 1: Chuẩn hóa URL
├── Loại bỏ whitespace, lowercase
├── Decode URL encoding
├── Expand URL shortener (bit.ly, tinyurl) nếu có
└── Tách domain, path, query parameters

Bước 2: Phân tích đặc trưng (Feature Extraction)
├── URL length > 75 chars → +10 điểm
├── Số subdomain > 3 → +15 điểm
├── Có IP address thay vì domain → +25 điểm
├── Không có HTTPS → +20 điểm
├── Có @ hoặc // trong URL → +15 điểm
└── Domain age < 30 ngày → +20 điểm (nếu có API)

Bước 3: Kiểm tra Blacklist/Whitelist
├── Domain trong blacklist → +50 điểm (HIGH RISK)
├── Domain trong whitelist → -30 điểm (SAFE)
└── Kiểm tra redirect chain → mỗi redirect +10 điểm


Bước 4: Domain Similarity (Typosquatting Detection)
├── Thuật toán: Levenshtein Distance hoặc Jaro-Winkler
├── So sánh với danh sách 100 brand phổ biến tại VN
├── Ví dụ: "v1etcombank.com" vs "vietcombank.com.vn"
└── Khoảng cách <= 2 → +30 điểm

Bước 5: Tính điểm tổng và phân loại
├── 0-29: AN TOÀN (GREEN)
├── 30-59: CẨN TRỌNG (YELLOW)
└── 60-100: NGUY HIỂM (RED)
```

**Kỹ thuật triển khai Java:**
- **Apache Commons Validator** → validate URL format
- **OkHttp / HttpClient** → kiểm tra SSL, redirect, response
- **Jsoup** → parse HTML nếu cần
- **Apache Commons Text** → Levenshtein Distance
- **Caffeine Cache** → cache kết quả domain đã kiểm tra

---

## 3. Module 2: Phân tích Form/HTML

### 3.1. Heuristic Analysis cho biểu mẫu nghi ngờ

```
Bước 1: Parse HTML bằng Jsoup
└── Trích xuất tất cả <form> và <input>

Bước 2: Phân tích các trường nhạy cảm
├── input type="password" → +15 điểm
├── name/id chứa "otp", "pin", "cvv" → +20 điểm
├── name/id chứa "card", "cccd", "cmnd" → +25 điểm
├── name/id chứa "bank", "account" → +20 điểm
└── Tổng số trường nhạy cảm > 3 → +15 điểm

Bước 3: Kiểm tra hành vi form
├── Form action gửi về domain khác → +25 điểm
├── Form không có HTTPS trong action → +20 điểm
├── Form có hidden field chứa URL lạ → +15 điểm
└── Trang có nhiều hơn 2 form yêu cầu password → +20 điểm

Bước 4: Phát hiện từ khóa dụ dỗ trong nội dung
├── "xác minh ngay", "tài khoản sẽ bị khóa" → +15 điểm
├── "nhận thưởng", "trúng giải", "miễn phí" → +10 điểm
└── "khẩn cấp", "trong vòng 24h" → +10 điểm
```

**Kỹ thuật:**
- Jsoup: `Document.select("form"), Element.attr("action")`
- Regex để tìm keyword trong text content
- XPath selector cho phân tích phức tạp

---

## 4. Module 3: Phân tích Tin nhắn/Giao dịch

### 4.1. Các thuật toán NLP hiện đại (2024-2026)


| Phương pháp | Độ chính xác | Đặc điểm | Phù hợp đề tài |
|---|---|---|---|
| **TF-IDF + Naive Bayes** | 96.2% | Đơn giản, nhanh, ít tài nguyên | ⭐⭐⭐⭐⭐ MVP |
| **BERT/ALBERT** | 98%+ | Context-aware, phát hiện ngữ nghĩa | ⭐⭐ Mở rộng |
| **LLM (GPT/Claude)** | 97-100% | Giải thích được, zero-shot | ⭐⭐ Chi phí cao |
| **Rule + Keyword Matching** | 85-90% | Rất nhanh, giải thích rõ | ⭐⭐⭐⭐⭐ MVP |

### 4.2. Thuật toán đề xuất cho đề tài (MVP)

**Keyword-based + Pattern Extraction + Rule Scoring:**

```
Bước 1: Tiền xử lý văn bản tiếng Việt
├── Lowercase, loại bỏ dấu (optional)
├── Loại bỏ emoji, ký tự đặc biệt
├── Tách câu, tokenize (không cần NLP phức tạp)
└── Chuẩn hóa số điện thoại, số tiền

Bước 2: Trích xuất thực thể (Entity Extraction - Regex)
├── URL: regex pattern http/https
├── Số điện thoại: regex VN (0xx-xxx-xxxx, +84)
├── Số tài khoản: 8-16 chữ số liên tiếp
├── Số tiền: "xxx.xxx đồng", "xxx,xxx VND", "xxxK"
├── Mã OTP: 4-6 chữ số trong context "mã", "OTP"
└── Thời hạn: "trong X phút", "trước DD/MM"

Bước 3: Phát hiện từ khóa nguy hiểm (Keyword Dictionary)

NHÓM 1: Khẩn cấp/Áp lực (mỗi từ +5 điểm)
├── "gấp", "khẩn cấp", "ngay lập tức"
├── "trong vòng", "trước khi", "hết hạn"
├── "bị khóa", "sẽ khóa", "tạm ngưng"
└── "mất quyền", "không thể truy cập"

NHÓM 2: Giả mạo cơ quan (mỗi từ +10 điểm)
├── "ngân hàng", "vietcombank", "techcombank"
├── "công an", "bộ công an", "cảnh sát"
├── "shipper", "giao hàng", "bưu điện"
├── "lazada", "shopee", "tiki", "zalo", "facebook"
└── "thuế", "bảo hiểm xã hội"

NHÓM 3: Yêu cầu thông tin nhạy cảm (+15 điểm)
├── "mật khẩu", "password", "pin"
├── "otp", "mã xác thực", "mã bảo mật"
├── "cccd", "cmnd", "căn cước"
├── "số thẻ", "cvv", "cvc"
└── "số tài khoản", "stk"

NHÓM 4: Yêu cầu chuyển tiền (+20 điểm)
├── "chuyển khoản", "chuyển tiền", "nạp tiền"
├── "đặt cọc", "cọc trước", "phí"
├── "thanh toán ngay", "trả trước"
└── "hoàn tiền", "hoàn lại", "bồi thường"


NHÓM 5: Quá tốt để tin (+10 điểm)
├── "trúng giải", "trúng thưởng", "may mắn"
├── "miễn phí", "free", "không mất phí"
├── "việc nhẹ lương cao", "thu nhập khủng"
└── "làm tại nhà", "không cần kinh nghiệm"

Bước 4: Phân tích Pattern kết hợp (Composite Rules)

PATTERN 1: SMS Giả mạng ngân hàng
├── Có từ "ngân hàng" + "tài khoản" + "OTP"
└── → +30 điểm

PATTERN 2: Lừa đảo tuyển dụng
├── Có từ "tuyển dụng" + "phí" + "chuyển khoản"
└── → +35 điểm

PATTERN 3: Giả mạo shipper
├── Có từ "shipper" + "đơn hàng" + "phí ship" + URL/SĐT
└── → +30 điểm

PATTERN 4: Hoàn tiền giả
├── Có từ "hoàn tiền" + "nhập mã" hoặc "OTP"
└── → +25 điểm

PATTERN 5: Khẩn cấp + Thông tin nhạy cảm
├── Có từ khẩn cấp + từ yêu cầu info nhạy cảm
└── → +25 điểm

Bước 5: Kiểm tra Cross-reference
├── URL trong tin nhắn → gọi Module 1 (URL Scanner)
├── SĐT trong tin nhắn → gọi Module 4 (Phone Checker)
├── STK trong tin nhắn → gọi Module 4 (Bank Account Checker)
└── Cộng điểm từ các module phụ

Bước 6: Tính điểm và giải thích
├── Tổng điểm < 30 → AN TOÀN
├── 30-59 → CẨN TRỌNG với lý do cụ thể
└── >= 60 → NGUY HIỂM với khuyến nghị
```

**Kỹ thuật Java:**
- **Regex Pattern** cho entity extraction
- **HashMap<String, Integer>** cho keyword dictionary
- **Custom Rule Engine** để áp dụng composite patterns
- Có thể dùng **Apache Lucene** cho text analysis nâng cao

**Ví dụ minh họa:**

```
INPUT: "THÔNG BÁO: Tài khoản ngân hàng của quý khách sắp bị khóa. 
Vui lòng xác minh ngay tại link: http://vietc0mbank-verify.com 
hoặc gọi 0912345678 để được hỗ trợ."

PHÂN TÍCH:
✓ URL phát hiện: http://vietc0mbank-verify.com
  └── Module 1 trả về: 75 điểm (giả mạo Vietcombank)
✓ SĐT phát hiện: 0912345678
  └── Module 4 trả về: 25 điểm (đã có 3 báo cáo)
✓ Keywords:
  - "ngân hàng" (+10)
  - "tài khoản" (+0, từ trung tính)
  - "bị khóa" (+5)
  - "xác minh ngay" (+5)
✓ Pattern match: SMS giả mạo ngân hàng (+30)

TỔNG ĐIỂM: 75 + 25 + 20 + 30 = 150 → Cắt về 100
KẾT QUẢ: NGUY HIỂM (RED)


LỜI GIẢI THÍCH:
"Tin nhắn này có dấu hiệu giả mạo ngân hàng Vietcombank:
1. Domain trong link có ký tự số '0' thay cho chữ 'o' (v1etc0mbank)
2. Số điện thoại 0912345678 đã có 3 báo cáo lừa đảo
3. Nội dung tạo áp lực 'sắp bị khóa' và yêu cầu 'xác minh ngay'
4. Ngân hàng thật không bao giờ yêu cầu xác minh qua link lạ

KHUYẾN NGHỊ:
❌ KHÔNG bấm vào link
❌ KHÔNG gọi số điện thoại trong tin nhắn
❌ KHÔNG cung cấp OTP, mật khẩu
✅ Liên hệ ngân hàng qua số hotline chính thức
✅ Gửi báo cáo lừa đảo cho hệ thống"
```

---

## 5. Module 4: Phone Number & Bank Account Checker

### 5.1. Thuật toán đơn giản nhưng hiệu quả

**Blacklist Lookup + Community Score:**

```
Bước 1: Chuẩn hóa input
├── Phone: 0912345678 → +84912345678 (canonical form)
└── Bank Account: loại bỏ space, dash → 123456789012

Bước 2: Hash để bảo vệ privacy (optional)
├── SHA-256(phone_number) → lookup bằng hash
└── Không lưu số thật vào log

Bước 3: Tra cứu Database
├── Check blacklist: entity_type='PHONE', value='hash'
├── Đếm số báo cáo: COUNT(*) từ ScamReport
├── Tính thời gian báo cáo gần nhất
└── Lấy điểm uy tín người báo cáo (nếu có)

Bước 4: Scoring
├── Trong blacklist được verify → 100 điểm
├── Có >= 5 báo cáo trong 30 ngày → 80 điểm
├── Có 2-4 báo cáo → 50 điểm
├── Có 1 báo cáo nhưng chưa verify → 30 điểm
├── Không có báo cáo → 0 điểm
└── Trong whitelist (tổ chức uy tín) → -50 điểm

Bước 5: Time-decay (giảm trọng số theo thời gian)
├── Báo cáo < 7 ngày → 100% trọng số
├── Báo cáo 7-30 ngày → 80% trọng số
├── Báo cáo 30-90 ngày → 50% trọng số
└── Báo cáo > 90 ngày → 30% trọng số
```

**Kỹ thuật:**
- PostgreSQL với index trên hash column
- Redis cache cho số được tra cứu nhiều
- Time-series aggregate cho trending analysis

---

## 6. Module 5: QR Code Scanner

### 6.1. Phân tích nội dung QR

```
Bước 1: Decode QR (ZXing library)
└── Trích xuất chuỗi raw data

Bước 2: Phân loại nội dung
├── URL → gọi Module 1 (URL Scanner)
├── VietQR / Banking format → parse thông tin chuyển khoản
├── Plain text → gọi Module 3 (Text Analyzer)
└── Unrecognized format → cảnh báo chung

Bước 3: Đối với VietQR Payment
├── Parse: bank_id, account_number, amount, content
├── Kiểm tra account_number → gọi Module 4
├── Phát hiện keyword lừa đảo trong content
└── Cảnh báo nếu amount > ngưỡng (VD: 5 triệu)


Bước 4: Tính điểm tổng hợp
├── Điểm từ account checker
├── Điểm từ content analysis
└── Warning nếu QR từ nguồn không tin cậy
```

**Library Java:**
- **ZXing (Zebra Crossing)** - decode QR code
- Parse VietQR theo spec: `https://www.vietqr.io/`

---

## 7. Rule Engine Architecture

### 7.1. Kiến trúc đề xuất

```
┌─────────────────────────────────────────┐
│         Rule Engine Core                │
├─────────────────────────────────────────┤
│  1. Rule Repository (DB + Cache)        │
│     ├── RiskRule table                  │
│     ├── RuleCondition                   │
│     └── RuleAction                      │
├─────────────────────────────────────────┤
│  2. Rule Evaluator                      │
│     ├── Condition Matcher               │
│     ├── Score Calculator                │
│     └── Evidence Collector              │
├─────────────────────────────────────────┤
│  3. Rule Composer                       │
│     ├── AND / OR / NOT logic            │
│     ├── Weight & Priority               │
│     └── Threshold & Level Mapping       │
└─────────────────────────────────────────┘
```

### 7.2. Cấu trúc Rule trong Database

```sql
-- Bảng RiskRule
id | code               | name                      | weight | enabled
---|-------------------|---------------------------|--------|--------
1  | URL_NO_HTTPS      | URL không dùng HTTPS      | 20     | true
2  | URL_IP_ADDRESS    | URL dùng IP thay domain   | 25     | true
3  | TEXT_HAS_OTP      | Tin nhắn yêu cầu OTP      | 15     | true
4  | PATTERN_BANK_SCAM | Pattern giả mạo ngân hàng | 30     | true

-- Bảng RuleCondition (cho composite rule)
rule_id | condition_type | field_name    | operator | value
--------|---------------|---------------|----------|-------
4       | CONTAINS      | text          | REGEX    | ngân hàng
4       | AND           | text          | REGEX    | OTP|mã xác thực
4       | AND           | has_url       | EQUALS   | true
```

### 7.3. Thuật toán áp dụng Rule

```java
public RiskScore evaluateRules(ScanInput input) {
    List<Rule> activeRules = ruleRepository.findByEnabled(true);
    int totalScore = 0;
    List<Evidence> evidences = new ArrayList<>();
    
    for (Rule rule : activeRules) {
        if (rule.matches(input)) {
            totalScore += rule.getWeight();
            evidences.add(new Evidence(
                rule.getCode(),
                rule.getName(),
                rule.getWeight(),
                rule.getExplanation()
            ));
        }
    }
    
    // Áp dụng tối đa 100 điểm
    totalScore = Math.min(totalScore, 100);
    
    // Phân loại level
    RiskLevel level = classifyLevel(totalScore);
    
    return new RiskScore(totalScore, level, evidences);
}

private RiskLevel classifyLevel(int score) {
    if (score < 30) return RiskLevel.SAFE;
    if (score < 60) return RiskLevel.CAUTION;
    return RiskLevel.DANGER;
}
```


---

## 8. Machine Learning - Hướng mở rộng

### 8.1. Khi nào nên chuyển sang ML?

Chuyển sang ML khi:
- ✅ Đã có dataset >= 10,000 mẫu đã label (phishing/legitimate)
- ✅ Rule-based đạt baseline nhưng có nhiều false positive
- ✅ Team có kỹ năng ML hoặc ngân sách thuê chuyên gia
- ✅ Cần phát hiện các pattern mới, chưa biết trước

### 8.2. Roadmap ML cho đề tài

**Phase 1: Tập dataset**
```
Nguồn dữ liệu:
├── PhishTank API (phishing URLs)
├── Alexa Top 1M (legitimate URLs)
├── Tín Nhiệm Mạng (VN scam websites)
├── User reports từ hệ thống (sau 3-6 tháng vận hành)
└── Augmentation: tạo biến thể typosquatting
```

**Phase 2: Feature Engineering**
```
41 features cơ bản:
├── URL-based: length, depth, subdomain_count, has_ip, has_@, 
│   entropy, digit_ratio, special_char_count, tld_type
├── Domain-based: age, registrar, country, alexa_rank, 
│   has_ssl, ssl_age, whois_privacy
├── Content-based: title_similarity, form_count, 
│   external_link_ratio, iframe_count
└── Similarity-based: levenshtein_distance_to_brands
```

**Phase 3: Chọn thuật toán**
```
Đề xuất: Random Forest hoặc XGBoost
├── Lý do: 
│   - Accuracy cao (95-98%)
│   - Feature importance → giải thích được
│   - Không cần chuẩn hóa dữ liệu nhiều
│   - Robust với missing values
└── Alternative: Gradient Boosting, LightGBM
```

**Phase 4: Training & Evaluation**
```
Split: 70% train, 15% validation, 15% test
Metrics cần đo:
├── Accuracy
├── Precision (quan trọng - tránh false positive)
├── Recall (quan trọng - phát hiện được scam)
├── F1-Score
└── AUC-ROC

Target KPI:
├── Precision >= 95% (chỉ 5% cảnh báo nhầm)
├── Recall >= 90% (bắt được 90% scam thật)
└── Inference time < 100ms
```

### 8.3. Hybrid Approach (Khuyến nghị cho dài hạn)

```
┌────────────────────────────────────────┐
│         Input (URL/Text/Phone)         │
└──────────────┬─────────────────────────┘
               │
               ▼
     ┌─────────────────────┐
     │  Fast Rule Engine   │  <-- Xử lý 80% case đơn giản
     │  (< 10ms)           │      trong < 10ms
     └────┬───────────┬────┘
          │ PASS      │ UNCERTAIN
          │           │
          ▼           ▼
      ┌──────┐   ┌──────────────┐
      │ Safe │   │  ML Classifier│  <-- Xử lý 20% case phức tạp
      └──────┘   │  (50-100ms)  │      cần phân tích sâu
                 └───────┬───────┘
                         │
                         ▼
                  ┌─────────────┐
                  │ Final Score │
                  └─────────────┘
```

**Logic:**
1. Rule engine xử lý trước (blacklist, whitelist, rule đơn giản)
2. Nếu điểm < 20 (rõ ràng safe) → trả về ngay
3. Nếu điểm > 70 (rõ ràng danger) → trả về ngay
4. Nếu 20-70 (uncertain) → gọi ML model phân tích sâu
5. Kết hợp score từ rule + ML prediction


---

## 9. Performance Optimization

### 9.1. Caching Strategy

```
┌─────────────────────────────────────────┐
│         Cache Layers                    │
├─────────────────────────────────────────┤
│ L1: Caffeine (In-memory)                │
│     ├── Domain reputation (TTL: 1h)     │
│     ├── Phone/Account lookup (TTL: 15m) │
│     └── Recent scan results (TTL: 5m)   │
├─────────────────────────────────────────┤
│ L2: Redis (Distributed)                 │
│     ├── Blacklist/Whitelist (TTL: 24h)  │
│     ├── ML model predictions (TTL: 1h)  │
│     └── Aggregated statistics           │
└─────────────────────────────────────────┘
```

### 9.2. Database Optimization

```sql
-- Index quan trọng
CREATE INDEX idx_risk_entity_type_value 
    ON risk_entity(entity_type, value_hash);

CREATE INDEX idx_risk_entity_status 
    ON risk_entity(status, updated_at) 
    WHERE status = 'VERIFIED';

CREATE INDEX idx_scan_request_user_created 
    ON scan_request(user_id, created_at DESC);

-- Partition bảng log theo tháng
CREATE TABLE audit_log_2026_07 PARTITION OF audit_log
    FOR VALUES FROM ('2026-07-01') TO ('2026-08-01');
```

### 9.3. Async Processing

```java
@Async
public CompletableFuture<UrlAnalysis> analyzeUrl(String url) {
    // Phân tích chạy background, không block request
}

// Combine multiple checks
CompletableFuture<DomainCheck> domainCheck = ...;
CompletableFuture<SslCheck> sslCheck = ...;
CompletableFuture<ContentCheck> contentCheck = ...;

CompletableFuture.allOf(domainCheck, sslCheck, contentCheck)
    .thenApply(v -> combineResults(...));
```

---

## 10. So sánh với các hệ thống thực tế

| Hệ thống | Thuật toán chính | Độ chính xác | Phù hợp đề tài |
|---|---|---|---|
| **Google Safe Browsing** | Blacklist + Hash lookup + ML | 99%+ | Quá phức tạp, cần hạ tầng lớn |
| **PhishTank** | Community report + Verify | 92-95% | ✅ Học mô hình cộng đồng |
| **VirusTotal** | Multi-engine aggregation | 95%+ | Phức tạp, nhiều chi phí |
| **ScamAdviser** | Trust score (40+ signals) | 90%+ | ✅ Học cách tính trust score |
| **nTrust VN** | Blacklist + Manual review | ~90% | ✅ Phù hợp bối cảnh VN |
| **Bitdefender Scamio** | LLM-based | 97%+ | Chi phí cao, cần API |
| **TF-IDF + Naive Bayes** | Classic ML | 96% SMS | ✅ Đơn giản, hiệu quả |

**Kết luận cho đề tài:**
- **MVP (Khóa luận):** Rule-based + Heuristic + Blacklist/Whitelist
- **Nâng cao (Demo thêm):** + TF-IDF + Naive Bayes cho text
- **Tương lai:** + Random Forest/XGBoost cho URL + Hybrid


---

## 11. Implementation Checklist

### 11.1. Phase 1: Rule Engine Core (Tuần 1-2)
- [ ] Thiết kế schema cho RiskRule, RuleCondition
- [ ] Implement Rule Evaluator với weight scoring
- [ ] Viết unit test cho từng rule
- [ ] Tạo 20-30 rule cơ bản cho URL/Text/Phone

### 11.2. Phase 2: URL Scanner (Tuần 3-4)
- [ ] URL normalization & validation
- [ ] HTTPS/SSL checker
- [ ] Redirect chain analyzer
- [ ] Domain similarity (Levenshtein)
- [ ] Blacklist/Whitelist lookup
- [ ] Integration test với real URLs

### 11.3. Phase 3: Text Analyzer (Tuần 5-6)
- [ ] Regex patterns cho entity extraction
- [ ] Keyword dictionary (100+ từ tiếng Việt)
- [ ] Composite pattern matcher
- [ ] Cross-reference với URL/Phone checker
- [ ] Test với 50 mẫu tin nhắn thật

### 11.4. Phase 4: Phone/Account Checker (Tuần 7)
- [ ] Normalization & hashing
- [ ] Database lookup với cache
- [ ] Time-decay scoring
- [ ] Admin CRUD cho blacklist/whitelist

### 11.5. Phase 5: QR Scanner (Tuần 8)
- [ ] ZXing integration
- [ ] VietQR parser
- [ ] Routing đến module phù hợp

### 11.6. Phase 6: ML Prototype (Optional - Tuần 9-10)
- [ ] Collect dataset (PhishTank + Alexa)
- [ ] Feature extraction (41 features)
- [ ] Train Random Forest model
- [ ] Export model sang PMML/ONNX
- [ ] Integrate vào Java backend
- [ ] A/B test: Rule vs ML vs Hybrid

---

## 12. Datasets & Resources

### 12.1. Public Datasets cho Training/Testing

| Dataset | Loại | Số lượng | Link |
|---|---|---|---|
| PhishTank | Phishing URLs | ~15K active | https://phishtank.org/developer_info.php |
| Alexa Top 1M | Legitimate URLs | 1M | https://www.alexa.com/topsites |
| UNB Phishing Dataset | URLs + Features | 11K+ | https://www.unb.ca/cic/datasets/ |
| SMS Spam Collection | SMS spam/ham | 5.5K | UCI Machine Learning Repo |
| Vietnamese Scam Messages | VN SMS scam | Tự thu thập | Community report |

### 12.2. Libraries & Tools

**Java:**
- Apache Commons Validator, Apache Commons Text
- OkHttp, Jsoup
- ZXing (QR)
- Caffeine Cache
- Spring Boot, Spring Data JPA

**Python (cho ML prototyping):**
- scikit-learn, pandas, numpy
- XGBoost, LightGBM
- NLTK, spaCy (NLP)

**Infrastructure:**
- PostgreSQL, Redis
- Docker, Nginx
- Prometheus + Grafana (monitoring)

---

## 13. Tài liệu tham khảo

### 13.1. Nghiên cứu học thuật

| Tên | Năm | Điểm chính |
|---|---|---|
| Unveiling suspicious phishing attacks (Frontiers) | 2024 | 41 optimal URL features, OFVA algorithm |
| Phishing URL detection with neural networks (Nature) | 2024 | Neural network comparison for phishing |
| Detecting Phishing URLs with 1D CNN (MDPI) | 2024 | 200K dataset, 1D CNN approach |
| SMS Spam Detection with TF-IDF (arXiv) | 2026 | TF-IDF 96.2% accuracy với Naive Bayes |
| Rule-based ML for fraud detection (ResearchGate) | 2024 | Hybrid rule + ML model |

### 13.2. Công cụ thực tế

- Google Safe Browsing API: https://developers.google.com/safe-browsing
- PhishTank API: https://phishtank.org/api_info.php
- VirusTotal API: https://developers.virustotal.com/
- OWASP Cheat Sheets: https://cheatsheetseries.owasp.org/

---

## 14. Kết luận & Khuyến nghị

### 14.1. Cho phạm vi khóa luận (6 tháng)

**Nên làm:**
✅ Rule-based Engine với 30-50 rules cấu hình được
✅ Heuristic Analysis cho URL + Form + Text
✅ Blacklist/Whitelist + Community Report
✅ Phone/Account Checker với time-decay
✅ Explanation + Recommendation rõ ràng
✅ Dashboard quản trị rule + report

**Không nên làm (quá phạm vi):**
❌ Deep Learning model từ scratch
❌ Real-time crawler ở quy mô lớn
❌ Distributed ML training pipeline
❌ Production-grade infrastructure với k8s

### 14.2. Điểm mạnh của approach này

1. **Giải thích được:** Mỗi cảnh báo có lý do cụ thể
2. **Kiểm thử dễ:** Unit test từng rule độc lập
3. **Mở rộng tốt:** Thêm rule mới không ảnh hưởng cũ
4. **Phù hợp tài nguyên:** Không cần GPU, dataset lớn
5. **Học thuật vững:** Thể hiện hiểu biết về security + software engineering

### 14.3. Con đường phát triển

```
[MVP] Rule Engine + Heuristic
   ↓
[v1.5] + TF-IDF/Naive Bayes cho text
   ↓
[v2.0] + Random Forest cho URL
   ↓
[v2.5] Hybrid: Rule (fast path) + ML (complex cases)
   ↓
[v3.0] LLM Integration cho conversation analysis
```

**Thời gian ước tính:**
- MVP Rule-based: 2-3 tháng
- + ML basic: thêm 1-1.5 tháng
- + Hybrid: thêm 0.5-1 tháng
- Testing + Docs: 1 tháng

---

**Tác giả:** Nhóm Anti-Scam Project  
**Cập nhật lần cuối:** 21/07/2026  
**Version:** 1.0
