# API Specification - Anti-Scam Platform

**Version:** 1.0  
**Date:** 21/07/2026  
**Base URL:** `https://api.antiscam.vn/v1`

---

## 1. Authentication

### 1.1. Đăng ký người dùng
```http
POST /auth/register
Content-Type: application/json

Request:
{
  "email": "user@example.com",
  "password": "SecurePass123!",
  "fullName": "Nguyễn Văn A",
  "phoneNumber": "+84912345678"  // optional
}

Response: 201 Created
{
  "success": true,
  "data": {
    "userId": "usr_abc123",
    "email": "user@example.com",
    "fullName": "Nguyễn Văn A",
    "role": "USER",
    "createdAt": "2026-07-21T10:30:00Z"
  },
  "message": "Đăng ký thành công. Vui lòng kiểm tra email để xác thực tài khoản."
}

Errors:
400 - Email đã tồn tại
400 - Mật khẩu không đủ mạnh
422 - Validation error
```

### 1.2. Đăng nhập
```http
POST /auth/login
Content-Type: application/json

Request:
{
  "email": "user@example.com",
  "password": "SecurePass123!"
}

Response: 200 OK
{
  "success": true,
  "data": {
    "accessToken": "eyJhbGciOiJIUzI1NiIs...",
    "refreshToken": "eyJhbGciOiJIUzI1NiIs...",
    "expiresIn": 3600,
    "tokenType": "Bearer",
    "user": {
      "userId": "usr_abc123",
      "email": "user@example.com",
      "fullName": "Nguyễn Văn A",
      "role": "USER"
    }
  }
}

Errors:
401 - Email hoặc mật khẩu không đúng
403 - Tài khoản chưa được xác thực
403 - Tài khoản đã bị khóa
```

### 1.3. Làm mới token
```http
POST /auth/refresh
Content-Type: application/json

Request:
{
  "refreshToken": "eyJhbGciOiJIUzI1NiIs..."
}

Response: 200 OK
{
  "success": true,
  "data": {
    "accessToken": "eyJhbGciOiJIUzI1NiIs...",
    "expiresIn": 3600
  }
}
```

---

## 2. Scan APIs (Kiểm tra rủi ro)

### 2.1. Quét URL/Link
```http
POST /scan/url
Authorization: Bearer {accessToken}
Content-Type: application/json

Request:
{
  "url": "https://vietc0mbank-verify.com/xacthuc",
  "context": "Nhận được qua SMS",  // optional
  "saveHistory": true              // optional, default: true
}

Response: 200 OK
{
  "success": true,
  "data": {
    "scanId": "scan_xyz789",
    "input": {
      "url": "https://vietc0mbank-verify.com/xacthuc",
      "normalizedUrl": "https://vietc0mbank-verify.com/xacthuc",
      "domain": "vietc0mbank-verify.com"
    },
    "result": {
      "riskScore": 85,
      "riskLevel": "DANGER",
      "levelColor": "RED",
      "confidence": 0.92
    },
    "analysis": {
      "urlFeatures": {
        "hasHttps": true,
        "hasIpAddress": false,
        "urlLength": 45,
        "subdomainCount": 0,
        "hasSpecialChars": true,
        "redirectCount": 0
      },
      "domainAnalysis": {
        "age": null,
        "registrar": null,
        "country": null,
        "inBlacklist": false,
        "inWhitelist": false,
        "similarToKnownBrand": {
          "match": true,
          "brand": "Vietcombank",
          "officialDomain": "vietcombank.com.vn",
          "similarity": 0.89,
          "technique": "Character substitution (0 → o)"
        }
      }
    },
    "evidences": [
      {
        "ruleCode": "DOMAIN_TYPOSQUATTING",
        "ruleName": "Domain giả mạo thương hiệu",
        "score": 30,
        "severity": "HIGH",
        "description": "Domain sử dụng ký tự '0' thay cho 'o' để giả mạo Vietcombank"
      },
      {
        "ruleCode": "DOMAIN_SUSPICIOUS_KEYWORD",
        "ruleName": "Từ khóa đáng ngờ trong domain",
        "score": 25,
        "severity": "MEDIUM",
        "description": "Domain chứa từ 'verify', 'xacthuc' - thường xuất hiện trong phishing"
      },
      {
        "ruleCode": "URL_NO_REPUTATION",
        "ruleName": "Domain không có lịch sử uy tín",
        "score": 15,
        "severity": "LOW",
        "description": "Không tìm thấy thông tin về domain này"
      }
    ],
    "recommendation": {
      "action": "BLOCK",
      "message": "KHÔNG truy cập link này",
      "reasons": [
        "Domain giả mạo ngân hàng Vietcombank",
        "Sử dụng kỹ thuật typosquatting (thay ký tự)",
        "Ngân hàng thật không bao giờ yêu cầu xác thực qua link lạ"
      ],
      "nextSteps": [
        "Xóa tin nhắn chứa link này",
        "Liên hệ Vietcombank qua số hotline chính thức: 1900 54 54 13",
        "Báo cáo lừa đảo cho hệ thống"
      ]
    },
    "scannedAt": "2026-07-21T10:35:22Z",
    "processingTime": 245  // milliseconds
  }
}

Errors:
400 - URL không hợp lệ
429 - Quá nhiều request (rate limit)
500 - Lỗi hệ thống
```

### 2.2. Quét nội dung tin nhắn/giao dịch
```http
POST /scan/text
Authorization: Bearer {accessToken}
Content-Type: application/json

Request:
{
  "text": "THÔNG BÁO: Tài khoản của quý khách sắp bị khóa. Xác minh ngay tại http://vietc0mbank.com hoặc gọi 0912345678",
  "source": "SMS",  // SMS, ZALO, MESSENGER, EMAIL, etc.
  "saveHistory": true
}

Response: 200 OK
{
  "success": true,
  "data": {
    "scanId": "scan_text456",
    "input": {
      "textLength": 125,
      "source": "SMS"
    },
    "result": {
      "riskScore": 95,
      "riskLevel": "DANGER",
      "levelColor": "RED",
      "confidence": 0.97
    },
    "analysis": {
      "extractedEntities": {
        "urls": ["http://vietc0mbank.com"],
        "phoneNumbers": ["+84912345678"],
        "bankAccounts": [],
        "amounts": [],
        "otpCodes": []
      },
      "keywordMatches": [
        {
          "category": "URGENCY",
          "keywords": ["sắp bị khóa", "ngay"],
          "score": 10
        },
        {
          "category": "IMPERSONATION",
          "keywords": ["ngân hàng", "tài khoản"],
          "score": 15
        },
        {
          "category": "ACTION_REQUIRED",
          "keywords": ["xác minh"],
          "score": 5
        }
      ],
      "patternMatches": [
        {
          "pattern": "BANK_IMPERSONATION",
          "description": "Giả mạo ngân hàng yêu cầu xác minh",
          "score": 35
        }
      ],
      "crossReferenceChecks": {
        "urlCheck": {
          "url": "http://vietc0mbank.com",
          "riskScore": 85,
          "result": "DANGER"
        },
        "phoneCheck": {
          "phone": "+84912345678",
          "riskScore": 60,
          "reportCount": 12,
          "result": "CAUTION"
        }
      }
    },
    "evidences": [
      {
        "ruleCode": "PATTERN_BANK_SCAM",
        "ruleName": "Mẫu lừa đảo giả mạo ngân hàng",
        "score": 35,
        "severity": "CRITICAL"
      },
      {
        "ruleCode": "TEXT_URGENCY_PRESSURE",
        "ruleName": "Tạo áp lực khẩn cấp",
        "score": 15,
        "severity": "HIGH"
      },
      {
        "ruleCode": "CROSS_REF_URL_DANGER",
        "ruleName": "URL trong tin nhắn có rủi ro cao",
        "score": 30,
        "severity": "CRITICAL"
      }
    ],
    "recommendation": {
      "action": "BLOCK",
      "message": "Đây là tin nhắn lừa đảo giả mạo ngân hàng",
      "reasons": [
        "Giả danh ngân hàng để yêu cầu xác minh",
        "Tạo áp lực 'sắp bị khóa' để người dùng hoảng sợ",
        "URL và số điện thoại đều có dấu hiệu lừa đảo",
        "Ngân hàng thật KHÔNG BAO GIỜ gửi link yêu cầu xác minh qua SMS"
      ],
      "nextSteps": [
        "XÓA tin nhắn này ngay",
        "KHÔNG bấm link hoặc gọi số điện thoại trong tin nhắn",
        "KHÔNG cung cấp OTP, mật khẩu cho bất kỳ ai",
        "Liên hệ ngân hàng qua số hotline chính thức nếu lo lắng",
        "Gửi báo cáo lừa đảo"
      ]
    },
    "scannedAt": "2026-07-21T10:40:15Z",
    "processingTime": 180
  }
}
```

### 2.3. Kiểm tra số điện thoại
```http
POST /scan/phone
Authorization: Bearer {accessToken}
Content-Type: application/json

Request:
{
  "phoneNumber": "0912345678",
  "countryCode": "VN"  // optional, default: VN
}

Response: 200 OK
{
  "success": true,
  "data": {
    "scanId": "scan_phone789",
    "input": {
      "phoneNumber": "0912345678",
      "normalized": "+84912345678"
    },
    "result": {
      "riskScore": 65,
      "riskLevel": "CAUTION",
      "levelColor": "YELLOW"
    },
    "analysis": {
      "inBlacklist": false,
      "reportCount": 12,
      "lastReportDate": "2026-07-20T15:30:00Z",
      "reportCategories": {
        "SCAM_CALL": 8,
        "SPAM": 3,
        "IMPERSONATION": 1
      },
      "verifiedReports": 3,
      "timeDecayScore": 0.85  // báo cáo gần đây
    },
    "evidences": [
      {
        "ruleCode": "PHONE_MULTIPLE_REPORTS",
        "ruleName": "Số điện thoại có nhiều báo cáo",
        "score": 40,
        "description": "12 người dùng đã báo cáo số này trong 30 ngày qua"
      },
      {
        "ruleCode": "PHONE_RECENT_REPORTS",
        "ruleName": "Báo cáo gần đây",
        "score": 15,
        "description": "Có báo cáo mới trong 7 ngày qua"
      }
    ],
    "recommendation": {
      "action": "CAUTION",
      "message": "Cẩn trọng với số điện thoại này",
      "reasons": [
        "Đã có 12 báo cáo từ người dùng khác",
        "8 báo cáo liên quan đến cuộc gọi lừa đảo",
        "Có báo cáo mới trong tuần qua"
      ],
      "nextSteps": [
        "Không tin tưởng thông tin từ số này",
        "Xác minh lại qua kênh chính thức nếu họ tự xưng là tổ chức/doanh nghiệp",
        "Không cung cấp thông tin cá nhân qua điện thoại"
      ]
    },
    "scannedAt": "2026-07-21T10:42:00Z"
  }
}
```

### 2.4. Kiểm tra số tài khoản ngân hàng
```http
POST /scan/bank-account
Authorization: Bearer {accessToken}
Content-Type: application/json

Request:
{
  "accountNumber": "1234567890",
  "bankCode": "VCB",  // optional
  "accountName": "NGUYEN VAN X"  // optional
}

Response: 200 OK
{
  "success": true,
  "data": {
    "scanId": "scan_bank321",
    "input": {
      "accountNumber": "1234567890",
      "bankCode": "VCB"
    },
    "result": {
      "riskScore": 75,
      "riskLevel": "DANGER",
      "levelColor": "RED"
    },
    "analysis": {
      "inBlacklist": true,
      "blacklistSource": "VERIFIED_ADMIN",
      "reportCount": 28,
      "lastReportDate": "2026-07-21T09:15:00Z",
      "verifiedReports": 8,
      "reportCategories": {
        "FRAUD_TRANSFER": 20,
        "FAKE_SELLER": 5,
        "FAKE_LANDLORD": 3
      },
      "accountStatus": "BLOCKED_BY_BANK",  // nếu có thông tin
      "relatedScams": [
        {
          "type": "FAKE_PRODUCT_SALE",
          "description": "Bán hàng online không giao hàng",
          "reportCount": 15
        }
      ]
    },
    "evidences": [
      {
        "ruleCode": "ACCOUNT_IN_BLACKLIST",
        "ruleName": "Tài khoản trong danh sách đen",
        "score": 50,
        "severity": "CRITICAL"
      },
      {
        "ruleCode": "ACCOUNT_HIGH_REPORT_COUNT",
        "ruleName": "Số lượng báo cáo cao",
        "score": 25,
        "severity": "HIGH"
      }
    ],
    "recommendation": {
      "action": "BLOCK",
      "message": "KHÔNG chuyển tiền vào tài khoản này",
      "reasons": [
        "Tài khoản đã được xác minh là lừa đảo",
        "28 người dùng đã báo cáo",
        "Liên quan đến nhiều vụ bán hàng online lừa đảo"
      ],
      "nextSteps": [
        "HỦY giao dịch ngay lập tức",
        "Báo cáo cho người bán/chủ phòng về tài khoản đáng ngờ này",
        "Yêu cầu gặp trực tiếp hoặc sử dụng nền tảng trung gian có bảo vệ"
      ]
    },
    "scannedAt": "2026-07-21T10:45:30Z"
  }
}
```

### 2.5. Quét QR Code
```http
POST /scan/qr
Authorization: Bearer {accessToken}
Content-Type: application/json

Request:
{
  "qrData": "00020101021238530010A00000072701270006970436011086868686862503040208QRIBFTTA53037045802VN6304ABCD",
  "qrType": "VIETQR"  // AUTO, VIETQR, URL, TEXT
}

Response: 200 OK
{
  "success": true,
  "data": {
    "scanId": "scan_qr555",
    "qrType": "VIETQR",
    "parsedData": {
      "type": "BANK_TRANSFER",
      "bankCode": "970436",
      "bankName": "Vietcombank",
      "accountNumber": "8686868686",
      "amount": null,
      "content": "QRIBFTTA"
    },
    "result": {
      "riskScore": 40,
      "riskLevel": "CAUTION",
      "levelColor": "YELLOW"
    },
    "analysis": {
      "accountCheck": {
        "riskScore": 35,
        "reportCount": 2,
        "inBlacklist": false
      },
      "contentAnalysis": {
        "hasSuspiciousKeywords": false,
        "riskScore": 5
      }
    },
    "recommendation": {
      "action": "VERIFY",
      "message": "Xác minh kỹ trước khi quét",
      "reasons": [
        "Tài khoản có 2 báo cáo nhưng chưa được xác minh",
        "Không rõ người nhận và mục đích chuyển tiền"
      ],
      "nextSteps": [
        "Xác nhận với người yêu cầu thanh toán qua kênh khác (gọi điện, nhắn tin)",
        "Kiểm tra số tài khoản có khớp với thông tin đã biết không",
        "Nếu mua hàng online, ưu tiên thanh toán qua nền tảng trung gian"
      ]
    },
    "scannedAt": "2026-07-21T10:50:00Z"
  }
}
```


---

## 3. History & Reports APIs

### 3.1. Lấy lịch sử quét
```http
GET /scan/history?page=1&limit=20&type=URL,TEXT&level=DANGER,CAUTION
Authorization: Bearer {accessToken}

Response: 200 OK
{
  "success": true,
  "data": {
    "items": [
      {
        "scanId": "scan_xyz789",
        "type": "URL",
        "input": "https://vietc0mbank-verify.com/xacthuc",
        "riskScore": 85,
        "riskLevel": "DANGER",
        "scannedAt": "2026-07-21T10:35:22Z"
      },
      // ... more items
    ],
    "pagination": {
      "page": 1,
      "limit": 20,
      "total": 156,
      "totalPages": 8
    }
  }
}
```

### 3.2. Xem chi tiết một lần quét
```http
GET /scan/{scanId}
Authorization: Bearer {accessToken}

Response: 200 OK
{
  "success": true,
  "data": {
    // Full scan result như response của POST /scan/*
  }
}
```

### 3.3. Gửi báo cáo lừa đảo
```http
POST /reports
Authorization: Bearer {accessToken}
Content-Type: multipart/form-data

Request:
{
  "entityType": "URL",  // URL, PHONE, BANK_ACCOUNT, TEXT
  "entityValue": "https://fake-shop.com",
  "scamType": "FAKE_SELLER",
  "description": "Mua hàng qua website này nhưng không nhận được hàng, shop không trả lời",
  "evidenceFiles": [File1, File2],  // screenshots, chat logs
  "amountLost": 2500000,  // optional
  "transactionDate": "2026-07-15"  // optional
}

Response: 201 Created
{
  "success": true,
  "data": {
    "reportId": "rpt_abc123",
    "status": "PENDING",
    "submittedAt": "2026-07-21T11:00:00Z",
    "estimatedReviewTime": "24-48 giờ"
  },
  "message": "Cảm ơn báo cáo của bạn. Chúng tôi sẽ xem xét trong 24-48 giờ."
}
```

### 3.4. Lấy danh sách báo cáo của mình
```http
GET /reports/my?status=PENDING,VERIFIED&page=1&limit=10
Authorization: Bearer {accessToken}

Response: 200 OK
{
  "success": true,
  "data": {
    "items": [
      {
        "reportId": "rpt_abc123",
        "entityType": "URL",
        "entityValue": "https://fake-shop.com",
        "status": "UNDER_REVIEW",
        "submittedAt": "2026-07-21T11:00:00Z",
        "reviewedAt": null,
        "reviewerNote": null
      }
    ],
    "pagination": {...}
  }
}
```

---

## 4. Admin APIs

### 4.1. Quản lý Rule

#### 4.1.1. Lấy danh sách rules
```http
GET /admin/rules?category=URL,TEXT&enabled=true&page=1&limit=50
Authorization: Bearer {adminAccessToken}

Response: 200 OK
{
  "success": true,
  "data": {
    "items": [
      {
        "ruleId": "rule_001",
        "code": "URL_NO_HTTPS",
        "name": "URL không dùng HTTPS",
        "category": "URL",
        "weight": 20,
        "enabled": true,
        "description": "Phát hiện URL không sử dụng HTTPS",
        "conditionType": "SIMPLE",
        "createdAt": "2026-01-15T10:00:00Z",
        "updatedAt": "2026-07-10T14:30:00Z"
      }
    ],
    "pagination": {...}
  }
}
```

#### 4.1.2. Tạo rule mới
```http
POST /admin/rules
Authorization: Bearer {adminAccessToken}
Content-Type: application/json

Request:
{
  "code": "TEXT_FAKE_JOB_OFFER",
  "name": "Lừa đảo tuyển dụng",
  "category": "TEXT",
  "weight": 30,
  "enabled": true,
  "description": "Phát hiện tin nhắn tuyển dụng giả mạo",
  "conditionType": "COMPOSITE",
  "conditions": [
    {
      "field": "text",
      "operator": "CONTAINS",
      "value": "tuyển dụng|việc làm"
    },
    {
      "field": "text",
      "operator": "CONTAINS",
      "value": "phí|đặt cọc|chuyển khoản"
    }
  ]
}

Response: 201 Created
{
  "success": true,
  "data": {
    "ruleId": "rule_125",
    "code": "TEXT_FAKE_JOB_OFFER",
    // ... full rule object
  }
}
```

#### 4.1.3. Cập nhật rule
```http
PUT /admin/rules/{ruleId}
Authorization: Bearer {adminAccessToken}

Request:
{
  "weight": 35,  // tăng từ 30 lên 35
  "enabled": true
}

Response: 200 OK
```

#### 4.1.4. Xóa rule
```http
DELETE /admin/rules/{ruleId}
Authorization: Bearer {adminAccessToken}

Response: 200 OK
{
  "success": true,
  "message": "Rule đã được xóa"
}
```

### 4.2. Quản lý Risk Entities (Blacklist/Whitelist)

#### 4.2.1. Lấy danh sách entities
```http
GET /admin/risk-entities?type=DOMAIN,PHONE&status=VERIFIED&page=1&limit=50
Authorization: Bearer {adminAccessToken}

Response: 200 OK
{
  "success": true,
  "data": {
    "items": [
      {
        "entityId": "ent_123",
        "entityType": "DOMAIN",
        "value": "fake-bank.com",
        "valueHash": "a1b2c3...",
        "riskLevel": "HIGH",
        "status": "VERIFIED",
        "source": "ADMIN_MANUAL",
        "reportCount": 45,
        "verifiedAt": "2026-07-20T10:00:00Z",
        "notes": "Website giả mạo ngân hàng"
      }
    ],
    "pagination": {...}
  }
}
```

#### 4.2.2. Thêm entity vào blacklist
```http
POST /admin/risk-entities
Authorization: Bearer {adminAccessToken}

Request:
{
  "entityType": "PHONE",
  "value": "+84987654321",
  "riskLevel": "HIGH",
  "status": "VERIFIED",
  "source": "ADMIN_MANUAL",
  "category": "SCAM_CALL",
  "notes": "Số điện thoại giả mạo ngân hàng, đã xác minh qua 20 báo cáo"
}

Response: 201 Created
```

#### 4.2.3. Cập nhật entity
```http
PUT /admin/risk-entities/{entityId}

Request:
{
  "status": "RESOLVED",  // chuyển từ VERIFIED sang RESOLVED
  "notes": "Số này đã ngừng hoạt động"
}
```

### 4.3. Quản lý Reports (Kiểm duyệt)

#### 4.3.1. Lấy danh sách báo cáo cần xem xét
```http
GET /admin/reports?status=PENDING,UNDER_REVIEW&priority=HIGH&page=1&limit=20
Authorization: Bearer {adminAccessToken}

Response: 200 OK
{
  "success": true,
  "data": {
    "items": [
      {
        "reportId": "rpt_abc123",
        "entityType": "URL",
        "entityValue": "https://fake-shop.com",
        "scamType": "FAKE_SELLER",
        "description": "...",
        "evidenceUrls": ["https://storage.../screenshot1.png"],
        "reporterInfo": {
          "userId": "usr_xyz",
          "reputation": 0.85,
          "totalReports": 12,
          "verifiedReports": 10
        },
        "status": "PENDING",
        "priority": "MEDIUM",
        "submittedAt": "2026-07-21T11:00:00Z",
        "autoAnalysis": {
          "riskScore": 75,
          "suggestions": ["Có dấu hiệu domain giả mạo", "Nhiều keyword lừa đảo"]
        }
      }
    ],
    "pagination": {...}
  }
}
```

#### 4.3.2. Xem chi tiết báo cáo
```http
GET /admin/reports/{reportId}
Authorization: Bearer {adminAccessToken}

Response: 200 OK
{
  "success": true,
  "data": {
    // Full report details
    "reportId": "rpt_abc123",
    "timeline": [
      {
        "action": "SUBMITTED",
        "timestamp": "2026-07-21T11:00:00Z",
        "actor": "usr_xyz"
      },
      {
        "action": "AUTO_ANALYZED",
        "timestamp": "2026-07-21T11:00:15Z",
        "result": {...}
      }
    ],
    "relatedReports": [
      // Other reports về cùng entity
    ]
  }
}
```

#### 4.3.3. Duyệt báo cáo
```http
POST /admin/reports/{reportId}/review
Authorization: Bearer {adminAccessToken}

Request:
{
  "action": "APPROVE",  // APPROVE, REJECT, REQUEST_MORE_INFO
  "decision": {
    "addToBlacklist": true,
    "riskLevel": "HIGH",
    "category": "FAKE_SELLER"
  },
  "reviewerNote": "Đã xác minh qua 15 báo cáo tương tự. Thêm vào blacklist.",
  "notifyReporter": true
}

Response: 200 OK
{
  "success": true,
  "data": {
    "reportId": "rpt_abc123",
    "status": "VERIFIED",
    "reviewedAt": "2026-07-21T14:30:00Z",
    "createdEntity": {
      "entityId": "ent_456",
      "entityType": "URL",
      "value": "https://fake-shop.com"
    }
  },
  "message": "Báo cáo đã được xác minh và thêm vào blacklist"
}
```

### 4.4. Dashboard & Statistics

#### 4.4.1. Tổng quan dashboard
```http
GET /admin/dashboard/overview?period=7d
Authorization: Bearer {adminAccessToken}

Response: 200 OK
{
  "success": true,
  "data": {
    "period": "7d",
    "scans": {
      "total": 15420,
      "byType": {
        "URL": 8500,
        "TEXT": 4200,
        "PHONE": 1800,
        "BANK_ACCOUNT": 920
      },
      "byRiskLevel": {
        "SAFE": 9850,
        "CAUTION": 3200,
        "DANGER": 2370
      }
    },
    "reports": {
      "total": 248,
      "pending": 45,
      "underReview": 12,
      "verified": 156,
      "rejected": 35
    },
    "entities": {
      "totalInBlacklist": 3420,
      "addedThisPeriod": 87,
      "byType": {
        "URL": 1850,
        "PHONE": 980,
        "BANK_ACCOUNT": 590
      }
    },
    "topScamTypes": [
      {"type": "FAKE_SELLER", "count": 89},
      {"type": "BANK_IMPERSONATION", "count": 72},
      {"type": "FAKE_JOB", "count": 45}
    ]
  }
}
```

#### 4.4.2. Trend analysis
```http
GET /admin/dashboard/trends?metric=scans&period=30d&groupBy=day
Authorization: Bearer {adminAccessToken}

Response: 200 OK
{
  "success": true,
  "data": {
    "metric": "scans",
    "period": "30d",
    "dataPoints": [
      {"date": "2026-06-21", "value": 450, "dangerCount": 85},
      {"date": "2026-06-22", "value": 520, "dangerCount": 92},
      // ... 30 days
    ]
  }
}
```

---

## 5. Common Response Patterns

### 5.1. Success Response
```json
{
  "success": true,
  "data": { /* response data */ },
  "message": "Optional success message"
}
```

### 5.2. Error Response
```json
{
  "success": false,
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "Dữ liệu không hợp lệ",
    "details": [
      {
        "field": "url",
        "message": "URL không đúng định dạng"
      }
    ]
  },
  "timestamp": "2026-07-21T10:00:00Z",
  "path": "/v1/scan/url"
}
```

### 5.3. Error Codes

| HTTP Code | Error Code | Mô tả |
|---|---|---|
| 400 | VALIDATION_ERROR | Dữ liệu đầu vào không hợp lệ |
| 400 | INVALID_URL | URL không đúng format |
| 401 | UNAUTHORIZED | Chưa đăng nhập hoặc token không hợp lệ |
| 403 | FORBIDDEN | Không có quyền truy cập |
| 404 | NOT_FOUND | Không tìm thấy resource |
| 409 | ALREADY_EXISTS | Resource đã tồn tại |
| 429 | RATE_LIMIT_EXCEEDED | Quá số lần request cho phép |
| 500 | INTERNAL_ERROR | Lỗi hệ thống |
| 503 | SERVICE_UNAVAILABLE | Dịch vụ tạm thời không khả dụng |

---

## 6. Rate Limiting

| Endpoint Pattern | User Role | Limit |
|---|---|---|
| POST /scan/* | Free User | 50 requests/hour |
| POST /scan/* | Authenticated User | 200 requests/hour |
| POST /scan/* | Premium User | 1000 requests/hour |
| POST /reports | Authenticated User | 10 requests/day |
| GET /scan/history | Authenticated User | 100 requests/hour |
| /admin/* | Admin | 500 requests/hour |

**Rate Limit Headers:**
```
X-RateLimit-Limit: 200
X-RateLimit-Remaining: 187
X-RateLimit-Reset: 1658404800  // Unix timestamp
```

---

## 7. Webhooks (Optional - Future)

```http
POST {user_webhook_url}
Content-Type: application/json
X-Signature: sha256=...

{
  "event": "report.verified",
  "data": {
    "reportId": "rpt_abc123",
    "entityType": "URL",
    "entityValue": "https://fake-shop.com",
    "status": "VERIFIED",
    "verifiedAt": "2026-07-21T14:30:00Z"
  },
  "timestamp": "2026-07-21T14:30:05Z"
}
```

---

**Lưu ý triển khai:**
- Tất cả API phải sử dụng HTTPS
- JWT token có thời hạn 1 giờ, refresh token 7 ngày
- Implement CORS cho web client
- Log tất cả API calls cho security audit
- Validate input ở cả client và server
- Rate limiting implement bằng Redis
- Cache kết quả scan trong 5 phút với Redis

