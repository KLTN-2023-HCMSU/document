# Phân tích Quyền hạn Mobile App - Anti-Scam Platform

**Ngày:** 21/07/2026  
**Câu hỏi:** App mobile có thể vượt quyền không?  
**Trả lời ngắn gọn:** **KHÔNG. Tuyệt đối KHÔNG được vượt quyền.**

---

## 1. Tại sao KHÔNG được vượt quyền?

### 1.1. Lý do Pháp lý

```
┌─────────────────────────────────────────────────────┐
│  LUẬT PHÁP VIỆT NAM                                 │
├─────────────────────────────────────────────────────┤
│  Nghị định 13/2023/NĐ-CP                            │
│  về Bảo vệ dữ liệu cá nhân                          │
│                                                      │
│  Điều 8: Nguyên tắc xử lý dữ liệu cá nhân           │
│  ✓ Chỉ thu thập tối thiểu cần thiết                 │
│  ✓ Phải có sự đồng ý rõ ràng                        │
│  ✓ Minh bạch mục đích sử dụng                       │
│  ✓ Không sử dụng vượt mục đích đã thông báo         │
│                                                      │
│  Vi phạm → Phạt tiền + Đình chỉ ứng dụng            │
└─────────────────────────────────────────────────────┘
```

**Hậu quả vi phạm:**
- ⚠️ Phạt hành chính: 10-50 triệu VNĐ (cá nhân), 20-100 triệu (tổ chức)
- ⚠️ App bị gỡ khỏi Google Play / App Store
- ⚠️ Mất niềm tin người dùng → Chết sản phẩm
- ⚠️ Trách nhiệm hình sự nếu làm lộ dữ liệu nghiêm trọng

### 1.2. Lý do Kỹ thuật (Hệ điều hành chặn)

**Android 13+ (API 33):**
```java
// CÁC QUYỀN BỊ CHẶN NGHIÊM NGẶT

❌ KHÔNG THỂ đọc toàn bộ SMS/Call log nếu không phải app mặc định
❌ KHÔNG THỂ truy cập file của app khác (Scoped Storage)
❌ KHÔNG THỂ chạy nền vô hạn (Background execution limits)
❌ KHÔNG THỂ đọc clipboard tự động (Privacy changes)
❌ KHÔNG THỂ truy cập location khi app ở background (nhiều hạn chế)
❌ KHÔNG THỂ bật Accessibility Service tự động

✅ CHỈ CÓ THỂ: Yêu cầu quyền rõ ràng → User phê duyệt → Sử dụng đúng mục đích
```

**iOS 17+:**
```swift
// APPLE NGHIÊM KHẮC HƠN ANDROID

❌ KHÔNG THỂ đọc SMS (Apple không cho phép app thứ 3)
❌ KHÔNG THỂ đọc Call log chi tiết
❌ KHÔNG THỂ chạy background task kéo dài
❌ KHÔNG THỂ truy cập clipboard ngầm
❌ App Store Review bị reject nếu xin quyền không hợp lý

✅ CHỈ CÓ THỂ: CallKit (nhận diện số), Share Extension (nhận data khi user share)
```

### 1.3. Lý do Google Play Policy

**Google Play Console - User Data Policy:**
```
Nếu app khai báo:
├── "App không thu thập dữ liệu" 
│   → Nhưng thực tế có xin quyền SMS/Contact
│   → BỊ TỪ CHỐI ngay lập tức
│
├── "App thu thập SMS cho mục đích bảo mật"
│   → Phải chứng minh CHÍNH XÁC app làm gì với SMS
│   → Phải có Privacy Policy công khai
│   → Google sẽ kiểm tra thủ công (manual review)
│   → Nếu phát hiện gửi SMS lên server không mã hóa → BANNED
│
└── Penalty: Gỡ app + cảnh cáo + có thể ban account developer
```

**Sensitive Permissions yêu cầu giải trình:**
- READ_SMS, RECEIVE_SMS
- READ_CALL_LOG, PROCESS_OUTGOING_CALLS
- READ_CONTACTS
- CAMERA, RECORD_AUDIO
- ACCESS_FINE_LOCATION
- SYSTEM_ALERT_WINDOW
- BIND_ACCESSIBILITY_SERVICE (Cực kỳ nhạy cảm)

---

## 2. Quyền nào ĐƯỢC PHÉP cho Anti-Scam App?

### 2.1. Phân loại quyền theo mức độ

| Quyền | Mức độ | Cho phép? | Lý do |
|---|---|---|---|
| **INTERNET** | Normal | ✅ Có | Gọi API backend |
| **CAMERA** | Dangerous | ✅ Có | Quét QR code scam |
| **VIBRATE** | Normal | ✅ Có | Rung cảnh báo scam |
| **RECEIVE_BOOT_COMPLETED** | Normal | ✅ Có | Khởi động service bảo vệ |
| **POST_NOTIFICATIONS** (Android 13+) | Runtime | ✅ Có | Hiển thị cảnh báo |
| **READ_PHONE_STATE** | Dangerous | ⚠️ Có điều kiện | Chỉ để lấy số đang gọi đến (CallScreening) |
| **CALL_PHONE** | Dangerous | ❌ Không cần | App chỉ cảnh báo, không gọi |
| **READ_SMS** | Dangerous | ❌ KHÔNG | Google Play từ chối 99% trường hợp |
| **READ_CONTACTS** | Dangerous | ❌ KHÔNG | Không cần toàn bộ danh bạ |
| **READ_CALL_LOG** | Dangerous | ❌ KHÔNG | Không cần lịch sử cuộc gọi |
| **SYSTEM_ALERT_WINDOW** | Signature | ⚠️ Có điều kiện | Hiển thị overlay cảnh báo |
| **BIND_ACCESSIBILITY_SERVICE** | Signature | ❌ TUYỆT ĐỐI KHÔNG | Malware thường lạm dụng |

### 2.2. Quyền ĐỀ XUẤT cho MVP (Khóa luận)

```xml
<!-- AndroidManifest.xml - CHỈ XIN QUYỀN CẦN THIẾT -->

<!-- NORMAL PERMISSIONS - Tự động được cấp -->
<uses-permission android:name="android.permission.INTERNET" />
<uses-permission android:name="android.permission.VIBRATE" />
<uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED" />

<!-- DANGEROUS PERMISSIONS - Phải xin runtime -->
<uses-permission android:name="android.permission.CAMERA" />
<!-- Cho QR Scanner -->

<uses-permission android:name="android.permission.POST_NOTIFICATIONS" />
<!-- Android 13+ -->

<!-- OPTIONAL - Chỉ nếu làm tính năng call screening -->
<uses-permission android:name="android.permission.READ_PHONE_STATE" />
<!-- CHỈ để lấy số gọi đến, KHÔNG đọc call log -->

<!-- TUYỆT ĐỐI KHÔNG XIN NHỮNG QUYỀN NÀY CHO MVP -->
<!-- <uses-permission android:name="android.permission.READ_SMS" /> ❌ -->
<!-- <uses-permission android:name="android.permission.READ_CONTACTS" /> ❌ -->
<!-- <uses-permission android:name="android.permission.READ_CALL_LOG" /> ❌ -->
<!-- <uses-permission android:name="android.permission.ACCESS_FINE_LOCATION" /> ❌ -->
```


---

## 3. Các tính năng "chạy ngầm" ĐƯỢC PHÉP

### 3.1. Tính năng 1: Call Screening (Cảnh báo cuộc gọi)

**Cách triển khai ĐÚNG (không vượt quyền):**

```kotlin
// Android CallScreeningService - Chính thức từ Android 10+
class ScamCallScreeningService : CallScreeningService() {
    
    override fun onScreenCall(callDetails: Call.Details) {
        // 1. Lấy số điện thoại đang gọi đến
        val phoneNumber = callDetails.handle.schemeSpecificPart
        
        // 2. Kiểm tra trong database local hoặc gọi API
        val riskScore = checkPhoneNumber(phoneNumber)
        
        // 3. Trả về response cho hệ thống
        val response = CallResponse.Builder()
            .setDisallowCall(riskScore > 80)  // Chặn nếu nguy hiểm
            .setRejectCall(riskScore > 80)
            .setSkipCallLog(false)
            .setSkipNotification(false)
            .build()
        
        respondToCall(callDetails, response)
        
        // 4. Hiển thị notification cảnh báo
        if (riskScore > 50) {
            showScamWarningNotification(phoneNumber, riskScore)
        }
    }
}
```

**Manifest:**
```xml
<service
    android:name=".ScamCallScreeningService"
    android:permission="android.permission.BIND_SCREENING_SERVICE"
    android:exported="true">
    <intent-filter>
        <action android:name="android.telecom.CallScreeningService" />
    </intent-filter>
</service>
```

**Ưu điểm:**
- ✅ Google Play CHO PHÉP
- ✅ Không cần quyền nguy hiểm
- ✅ User có thể tắt bất cứ lúc nào
- ✅ Hệ thống kiểm soát, app không can thiệp trực tiếp

**Giới hạn:**
- ⚠️ Chỉ biết số đang gọi đến, KHÔNG biết lịch sử cuộc gọi
- ⚠️ Không đọc được SMS
- ⚠️ Không can thiệp được trong cuộc gọi

### 3.2. Tính năng 2: Share Target (Kiểm tra URL khi share)

**Cách triển khai ĐÚNG:**

```kotlin
// Activity nhận share intent
class ShareCheckActivity : AppCompatActivity() {
    
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        
        when (intent?.action) {
            Intent.ACTION_SEND -> {
                // Nhận URL được share
                val sharedUrl = intent.getStringExtra(Intent.EXTRA_TEXT)
                
                if (sharedUrl != null) {
                    // Hiển thị dialog loading
                    checkUrlSafety(sharedUrl)
                }
            }
        }
    }
}
```

**Manifest:**
```xml
<activity android:name=".ShareCheckActivity">
    <intent-filter>
        <action android:name="android.intent.action.SEND" />
        <category android:name="android.intent.category.DEFAULT" />
        <data android:mimeType="text/plain" />
    </intent-filter>
</activity>
```

**User workflow:**
1. User nhận link nghi ngờ trên Zalo/Messenger
2. User long-press → Share → Chọn "Anti-Scam Check"
3. App mở → Phân tích → Hiển thị kết quả ngay
4. User quyết định có mở link hay không

**Ưu điểm:**
- ✅ Hoàn toàn hợp pháp
- ✅ KHÔNG cần quyền đặc biệt
- ✅ User chủ động, không bị theo dõi
- ✅ Trải nghiệm tốt, không xâm phạm

### 3.3. Tính năng 3: Clipboard Monitoring (Giới hạn)

**Android 12+ ĐÃ CHẶN clipboard tự động:**

```kotlin
// CÁCH CŨ (Android 11-) - KHÔNG DÙNG NỮA
❌ clipboardManager.addPrimaryClipChangedListener {
    // Đọc clipboard mỗi khi thay đổi → BỊ CHẶN Android 12+
}

// CÁCH MỚI (Android 12+) - Chỉ khi app ở foreground
✅ Manual check khi user bấm nút
button.setOnClickListener {
    val clipboard = clipboardManager.primaryClip
    val text = clipboard?.getItemAt(0)?.text
    if (text != null && isUrl(text)) {
        checkUrlSafety(text.toString())
    }
}
```

**Khuyến nghị:**
- ✅ Hiển thị button "Kiểm tra link từ Clipboard"
- ✅ User BẤM → App đọc clipboard → Phân tích
- ❌ KHÔNG tự động đọc clipboard ngầm

### 3.4. Tính năng 4: Notification Listener (CỰC KỲ NHẠY CẢM)

**⚠️ CẢNH BÁO: Quyền này gần như KHÔNG được phê duyệt**

```kotlin
// NotificationListenerService có thể đọc MỌI thông báo
class ScamNotificationListener : NotificationListenerService() {
    override fun onNotificationPosted(sbn: StatusBarNotification) {
        val notification = sbn.notification
        val text = notification.extras.getString(Notification.EXTRA_TEXT)
        
        // Phát hiện link/số điện thoại trong notification
        // → Cảnh báo user
    }
}
```

**Vấn đề:**
- ❌ Đọc được notification của TẤT CẢ app (Gmail, Banking, Chat...)
- ❌ Google Play CỰC KỲ khó chấp nhận
- ❌ User thường từ chối quyền này
- ❌ Dễ bị coi là spyware

**Khuyến nghị cho đề tài:**
- 🔴 **KHÔNG NÊN** làm tính năng này cho MVP
- 📝 Chỉ đề cập như "hướng phát triển có rủi ro cao"
- 📝 Nếu làm, phải có giải trình CỰC KỲ rõ ràng

---

## 4. Giải pháp thay thế "chạy ngầm" cho MVP

### 4.1. Thao tác chủ động (KHUYẾN NGHỊ)

```
THAY VÌ: Tự động đọc SMS/notification
→ LÀM: User paste/share nội dung vào app

WORKFLOW:
1. User nhận SMS/tin nhắn nghi ngờ
2. User COPY nội dung
3. User MỞ app Anti-Scam
4. User PASTE hoặc BẤM "Kiểm tra từ Clipboard"
5. App phân tích và cảnh báo

Ưu điểm:
✅ Hoàn toàn hợp pháp
✅ KHÔNG cần quyền nguy hiểm
✅ User kiểm soát hoàn toàn
✅ Dễ demo cho giảng viên
✅ Dễ pass Google Play review
```

### 4.2. Share Extension (KHUYẾN NGHỊ)

```
THAY VÌ: Tự động bắt URL
→ LÀM: User share URL vào app

WORKFLOW:
1. User thấy link nghi ngờ trên Zalo/Messenger
2. User long-press link → Share
3. User chọn "Anti-Scam Check"
4. App phân tích ngay
5. Hiện kết quả trong dialog

Ưu điểm:
✅ Trải nghiệm mượt mà
✅ Không cần quyền đặc biệt
✅ Phù hợp với hành vi người dùng thật
```

### 4.3. QR Scanner (AN TOÀN NHẤT)

```
THAY VÌ: Đọc camera liên tục
→ LÀM: User mở app → Bấm "Quét QR"

WORKFLOW:
1. User thấy QR code nghi ngờ
2. User mở app Anti-Scam
3. User bấm nút "Quét QR"
4. User chỉ camera vào QR
5. App phân tích và cảnh báo

Quyền cần:
✅ CAMERA (runtime permission)
✅ Google Play chấp nhận 100%
```

### 4.4. Manual Input (BACKUP)

```
THAY VÌ: Tự động bắt
→ LÀM: User nhập thủ công

WORKFLOW:
1. User mở app
2. Chọn tab: URL / Số điện thoại / Số TK
3. Nhập vào
4. Bấm "Kiểm tra"
5. Nhận kết quả

Ưu điểm:
✅ Đơn giản nhất
✅ Chắc chắn pass review
✅ Phù hợp demo học thuật
```

---

## 5. So sánh các approach

| Tính năng | Quyền cần | Google Play | Trải nghiệm | Phù hợp MVP |
|---|---|---|---|---|
| **Thao tác chủ động** | Không | ✅ 100% pass | ⭐⭐⭐ OK | ⭐⭐⭐⭐⭐ Rất cao |
| **Share Extension** | Không | ✅ 100% pass | ⭐⭐⭐⭐ Tốt | ⭐⭐⭐⭐⭐ Rất cao |
| **QR Scanner** | Camera | ✅ 100% pass | ⭐⭐⭐⭐ Tốt | ⭐⭐⭐⭐⭐ Rất cao |
| **Call Screening** | Phone State | ✅ Có thể pass | ⭐⭐⭐⭐⭐ Rất tốt | ⭐⭐⭐⭐ Cao |
| **Clipboard Auto** | Không | ❌ Bị chặn OS | ⭐ Xấu | ❌ Không |
| **SMS Reader** | Read SMS | ❌ 99% reject | ⭐⭐⭐⭐⭐ Tuyệt vời | ❌ Không |
| **Notification Listener** | Notification | ❌ Khó pass | ⭐⭐⭐⭐⭐ Tuyệt vời | ❌ Không |
| **Accessibility** | Accessibility | ❌ Nguy hiểm | ⭐⭐⭐⭐⭐ Tuyệt vời | ❌❌ Tuyệt đối không |


---

## 6. Kịch bản Demo cho Giảng viên

### 6.1. Demo Scenario 1: Kiểm tra URL từ SMS

```
🎬 KỊCH BẢN DEMO

Bước 1: Chuẩn bị
├── SMS giả: "TÀI KHOẢN SẮP BỊ KHÓA. Xác minh tại: http://vietc0mbank.com"
└── Hiển thị trên điện thoại demo

Bước 2: User action
├── User đọc SMS → Nghi ngờ
├── User LONG-PRESS vào link
├── User chọn "Copy link"
└── User mở app Anti-Scam

Bước 3: App workflow
├── App hiển thị màn hình chính
├── Phát hiện clipboard có URL
├── Hiện dialog: "Phát hiện link trong clipboard. Kiểm tra ngay?"
├── User bấm "Kiểm tra"
└── App phân tích (hiện loading 2-3s)

Bước 4: Kết quả
├── Hiển thị: NGUY HIỂM (RED)
├── Điểm rủi ro: 85/100
├── Lý do:
│   • Domain giả mạo Vietcombank (typosquatting)
│   • Sử dụng HTTP thay vì HTTPS
│   • Domain không có lịch sử uy tín
├── Khuyến nghị:
│   • KHÔNG truy cập link này
│   • Xóa SMS
│   • Liên hệ Vietcombank qua hotline chính thức
└── Nút "Gửi báo cáo lừa đảo"

💡 Giải thích cho giảng viên:
"Em không xin quyền đọc SMS tự động vì:
1. Google Play sẽ từ chối
2. Vi phạm Nghị định 13/2023
3. User không tin tưởng app đọc SMS
→ Thay vào đó, em để user chủ động copy-paste
→ Vẫn đạt mục đích bảo vệ user nhưng hợp pháp"
```

### 6.2. Demo Scenario 2: Quét QR giả mạo

```
🎬 KỊCH BẢN DEMO

Bước 1: Chuẩn bị
├── In QR code chuyển khoản giả
└── QR chứa: STK 123456789 - Ngân hàng VCB - Số tiền 5,000,000đ

Bước 2: User action
├── User mở app Anti-Scam
├── Chọn tab "Quét QR"
├── App yêu cầu quyền Camera (runtime)
├── User bấm "Cho phép"
└── User hướng camera vào QR

Bước 3: App workflow
├── ZXing library decode QR
├── Parse VietQR format
├── Trích xuất: bankCode, accountNumber, amount
├── Gọi API kiểm tra STK
└── Phân tích trong 1-2s

Bước 4: Kết quả
├── Hiển thị: CẨN TRỌNG (YELLOW)
├── Điểm rủi ro: 65/100
├── Thông tin:
│   • Số TK: 123456789
│   • Ngân hàng: Vietcombank
│   • Số tiền: 5.000.000đ
├── Cảnh báo:
│   • Số tài khoản này có 12 báo cáo lừa đảo
│   • 8 báo cáo liên quan "bán hàng không giao"
└── Khuyến nghị:
    • Xác minh lại người nhận qua điện thoại
    • Không quét QR từ nguồn không rõ ràng
    • Ưu tiên thanh toán khi nhận hàng

💡 Điểm mạnh cho giảng viên:
"QR scanning rất phổ biến ở VN, đặc biệt thanh toán online.
User có thể quét QR trước khi thanh toán để check.
Chỉ cần quyền Camera - Google Play chấp nhận 100%."
```

### 6.3. Demo Scenario 3: Share link từ Zalo

```
🎬 KỊCH BẢN DEMO

Bước 1: Chuẩn bị
├── Mở Zalo (hoặc Messenger)
└── Tin nhắn có link: "Mua iPhone giá rẻ: http://iphon3-sale.com"

Bước 2: User action
├── User long-press vào link
├── Menu hiện ra
├── User chọn "Chia sẻ" (Share)
└── Chọn "Anti-Scam Check" trong danh sách

Bước 3: App workflow
├── App mở share dialog
├── Nhận URL từ intent
├── Tự động phân tích (không cần user bấm gì)
└── Hiện kết quả trong 2-3s

Bước 4: Kết quả
├── Dialog hiển thị full-screen
├── NGUY HIỂM (RED) - 90/100
├── Lý do:
│   • Domain mới tạo (< 7 ngày)
│   • Sử dụng số "3" thay chữ "e" (iphon3)
│   • Giả mạo thương hiệu iPhone
│   • Không có HTTPS
├── Bằng chứng bổ sung:
│   • 5 người dùng đã báo cáo trong 2 ngày
│   • Không tìm thấy thông tin công ty
└── Nút: [Đóng] [Gửi báo cáo]

💡 Highlight:
"Đây là trải nghiệm tốt nhất vì:
- User đang dùng app chat, thấy link nghi ngờ
- Chỉ cần share sang app em
- Không cần copy-paste hay chuyển app
- Kết quả hiện ngay, quyết định nhanh"
```

---

## 7. Câu trả lời cho các câu hỏi thường gặp

### Q1: "Tại sao không làm như app nTrust/Whoscall?"

**Trả lời:**
```
nTrust và Whoscall là các app của:
• Tổ chức/doanh nghiệp lớn có uy tín
• Đã được Google phê duyệt đặc biệt
• Có partnership với Google/Apple
• Có đội ngũ pháp lý riêng

Với app khóa luận của em:
• Là cá nhân/sinh viên
• Chưa có uy tín để xin quyền đặc biệt
• Google sẽ review nghiêm ngặt hơn
• Mục tiêu là DEMO học thuật, không phải sản phẩm thương mại

→ Em chọn approach an toàn, hợp pháp, vẫn đạt mục đích bảo vệ user
```

### Q2: "User không chủ động thì làm sao bảo vệ?"

**Trả lời:**
```
Thực tế:
• 70% người dùng không tin app đọc SMS/notification
• Google/Apple ngày càng hạn chế quyền tự động
• Xu hướng là user kiểm soát data nhiều hơn

Giải pháp của em:
• Giáo dục user: "Nghi ngờ link → Share vào app → Kiểm tra"
• UX đơn giản: 2 bước (Share → Xem kết quả)
• Nhanh: < 3 giây có kết quả
• An toàn: User kiểm soát 100%

Tương lai (nếu sản phẩm hóa):
• Xin partnership với Google
• Xây dựng uy tín qua cộng đồng
• Lúc đó mới xin quyền nâng cao
```

### Q3: "App chạy ngầm thì có vi phạm OWASP không?"

**Trả lời:**
```
OWASP MASVS có các yêu cầu:
✅ MSTG-STORAGE-1: Không lưu data nhạy cảm trong log
✅ MSTG-PLATFORM-1: Chỉ xin quyền cần thiết
✅ MSTG-PLATFORM-2: Input từ user phải validate
✅ MSTG-PRIVACY-1: Thu thập data tối thiểu
✅ MSTG-PRIVACY-2: Có privacy policy rõ ràng

App của em:
✅ KHÔNG đọc SMS/Contact → Không vi phạm privacy
✅ CHỈ xin Camera (cho QR), Phone State (cho call screening)
✅ Validate mọi input (URL, text, phone number)
✅ KHÔNG lưu SMS/notification content
✅ Chỉ gửi hash/metadata lên server (không phải full data)

→ Tuân thủ OWASP MASVS Level 1 (Standard)
→ Có thể đạt Level 2 nếu thêm certificate pinning
```

### Q4: "iOS có khó khăn hơn Android không?"

**Trả lời:**
```
iOS NGHIÊM KHẮC HƠN ANDROID:

❌ iOS không cho app thứ 3 đọc SMS
❌ iOS không cho đọc Call log chi tiết
❌ iOS hạn chế background execution rất chặt
❌ App Store review nghiêm ngặt về quyền

Giải pháp cho iOS:
✅ Share Extension (Apple khuyến khích)
✅ CallKit Directory Extension (cho caller ID)
✅ Camera cho QR scanner
✅ Clipboard khi app ở foreground

→ Em ưu tiên Android cho MVP
→ iOS sẽ là v2.0 với scope giới hạn hơn
```

---

## 8. Checklist tuân thủ cho đề tài

### 8.1. Pháp lý
- [ ] Viết Privacy Policy rõ ràng (tiếng Việt + English)
- [ ] Khai báo chính xác quyền trong Google Play Console
- [ ] Giải thích TẠI SAO cần từng quyền
- [ ] Có cơ chế user xóa dữ liệu
- [ ] Tuân thủ Nghị định 13/2023

### 8.2. Kỹ thuật
- [ ] Request runtime permissions đúng cách
- [ ] Explain permission trước khi xin
- [ ] Xử lý trường hợp user từ chối permission
- [ ] KHÔNG lưu data nhạy cảm trong SharedPreferences/log
- [ ] Mã hóa data trước khi gửi lên server
- [ ] Implement certificate pinning (nâng cao)

### 8.3. UX/UI
- [ ] Màn hình onboarding giải thích app làm gì
- [ ] Dialog giải thích TẠI SAO cần quyền
- [ ] Cho phép user tắt tính năng "chạy ngầm"
- [ ] Settings để user quản lý quyền
- [ ] Hiển thị rõ data nào được thu thập

### 8.4. Testing
- [ ] Test trên Android 13+ (permissions mới nhất)
- [ ] Test khi user TỪ CHỐI quyền
- [ ] Test khi user THU HỒI quyền giữa chừng
- [ ] Test background service bị kill
- [ ] Test với Play Protect bật

### 8.5. Documentation
- [ ] Ghi rõ KHÔNG vượt quyền trong báo cáo
- [ ] Giải thích approach thay thế
- [ ] So sánh với các app thương mại
- [ ] Trích dẫn Nghị định 13/2023, OWASP MASVS
- [ ] Có phần "Limitations" thành thật

---

## 9. Kết luận & Khuyến nghị

### 9.1. TÓM TẮT

```
┌────────────────────────────────────────────────────┐
│  CÂU TRẢ LỜI CHÍNH THỨC                            │
├────────────────────────────────────────────────────┤
│                                                     │
│  ❌ App mobile TUYỆT ĐỐI KHÔNG được vượt quyền    │
│                                                     │
│  Lý do:                                            │
│  1. Vi phạm pháp luật VN (Nghị định 13/2023)      │
│  2. Bị Google Play/App Store từ chối              │
│  3. Hệ điều hành chặn kỹ thuật                    │
│  4. Mất niềm tin người dùng                       │
│  5. Vi phạm đạo đức nghề nghiệp                   │
│                                                     │
│  Thay vào đó:                                      │
│  ✅ Thao tác chủ động (copy-paste, manual input)  │
│  ✅ Share Extension                                │
│  ✅ QR Scanner                                     │
│  ✅ Call Screening API (chính thức)               │
│                                                     │
│  → Vẫn đạt mục đích nhưng HỢP PHÁP & AN TOÀN      │
└────────────────────────────────────────────────────┘
```

### 9.2. KHUYẾN NGHỊ CHO ĐỀ TÀI

**Phạm vi MVP (3-4 tháng):**
1. ✅ URL Scanner (share/paste)
2. ✅ Text Analyzer (paste/input)
3. ✅ QR Scanner (camera)
4. ✅ Phone Checker (input)
5. ✅ Bank Account Checker (input)

**Phạm vi nâng cao (thêm 1-2 tháng):**
1. ✅ Call Screening Service
2. ✅ Share Extension cho nhiều app
3. ✅ Widget (quick check)

**KHÔNG NÊN LÀM (vượt phạm vi/rủi ro cao):**
1. ❌ SMS Auto Reader
2. ❌ Notification Listener
3. ❌ Clipboard Auto Monitor
4. ❌ Accessibility Service
5. ❌ Background Location Tracking

### 9.3. Câu trả lời cho giảng viên

**Nếu giảng viên hỏi: "Tại sao không làm tự động như app thương mại?"**

> "Thưa thầy/cô, em có nghiên cứu kỹ các app như nTrust, Whoscall. 
> Các app đó được phép vì:
> 1. Là tổ chức lớn có partnership với Google
> 2. Có đội ngũ pháp lý và compliance team
> 3. Đã được phê duyệt đặc biệt
> 
> Với đề tài khóa luận, em tuân thủ nguyên tắc:
> • KHÔNG vi phạm pháp luật (Nghị định 13/2023)
> • KHÔNG vượt quyền hệ điều hành
> • Thiết kế theo OWASP MASVS
> • Giữ niềm tin người dùng
> 
> Em chọn approach 'user chủ động' vì:
> • Vẫn đạt mục đích bảo vệ user
> • Hợp pháp và an toàn 100%
> • Demo được cho khóa luận
> • Có thể mở rộng sau nếu sản phẩm hóa
> 
> Em tin rằng một app anti-scam vi phạm privacy 
> thì không khác gì scammer."

---

**Tài liệu tham khảo:**
- [Nghị định 13/2023/NĐ-CP](https://vanban.chinhphu.vn/?pageid=27160&docid=207759)
- [OWASP MASVS](https://mas.owasp.org/MASVS/)
- [Google Play User Data Policy](https://support.google.com/googleplay/android-developer/answer/10787469)
- [Android Background Execution Limits](https://developer.android.com/about/versions/oreo/background)
- [iOS Privacy Best Practices](https://developer.apple.com/documentation/uikit/protecting_the_user_s_privacy)

**Ngày cập nhật:** 21/07/2026  
**Version:** 1.0
