# Đặc tả màn hình giao diện Anti-Scam Platform

**Project:** Anti-Scam Platform  
**Tuần:** Week 5  
**Mục đích:** Làm tài liệu đầu vào để thiết kế UI/UX và sinh giao diện web/mobile sau này.

## 1. Nguyên tắc thiết kế chung

1. Giao diện phải giúp người dùng ra quyết định nhanh: có nên bấm link, nhập thông tin, gọi lại hay chuyển tiền hay không.
2. Mỗi kết quả scan cần có 4 lớp thông tin: `riskScore`, `riskLevel`, evidence giải thích, recommendation hành động.
3. Không dùng ngôn ngữ kết tội tuyệt đối. Dùng "có dấu hiệu rủi ro", "nên xác minh", "dữ liệu cộng đồng ghi nhận".
4. Luôn nói rõ dữ liệu nào được gửi lên hệ thống, dữ liệu nào không thu thập.
5. Input nhạy cảm phải có nhắc nhở: không dán OTP, mật khẩu, CVV, số thẻ nếu không cần.
6. Số điện thoại, số tài khoản, OTP-like pattern và bằng chứng nhạy cảm cần mask/redact trong UI user.
7. Mobile MVP chỉ có thao tác chủ động: paste, share, manual input, QR camera. Không có UI nào gợi ý app đọc ngầm SMS/notification/call log.
8. Admin UI phải rõ tác động của hành động: approve report, add blacklist, disable rule, change weight.

## 2. Cấu trúc điều hướng đề xuất

### 2.1. Web user

| Route | Tên màn hình | Vai trò |
|---|---|---|
| `/` | Home / Quick Scan | Khách, User |
| `/login` | Đăng nhập | Khách |
| `/register` | Đăng ký | Khách |
| `/scan/url` | Scan URL | User, khách nếu cho anonymous |
| `/scan/text` | Scan Text | User |
| `/scan/phone` | Kiểm tra số điện thoại | User |
| `/scan/bank-account` | Kiểm tra số tài khoản | User |
| `/scan/qr` | Scan QR | User |
| `/scan/result/[scanId]` | Chi tiết kết quả scan | User |
| `/history` | Lịch sử scan | User |
| `/reports/new` | Gửi báo cáo | User |
| `/reports/my` | Báo cáo của tôi | User |
| `/profile` | Hồ sơ cá nhân | User |
| `/settings/privacy` | Cài đặt riêng tư | User |

### 2.2. Mobile

| Màn hình | Vai trò |
|---|---|
| Onboarding & Trust | Giải thích quyền, dữ liệu, cách hoạt động. |
| Mobile Login | Đăng nhập mobile. |
| Manual Input Tabs | Nhập URL/Text/Phone/Bank/QR raw. |
| QR Camera Scanner | Quét QR bằng camera. |
| Share Target Check | Nhận URL/text do user share từ app khác. |
| Result Summary | Kết quả ngắn gọn dạng bottom sheet/full screen. |
| Report Submission | Gửi báo cáo nhanh từ kết quả scan. |
| History | Lịch sử scan mobile. |
| Privacy & Permission Settings | Quản lý quyền và giải thích không đọc ngầm. |

### 2.3. Admin

| Route | Tên màn hình | Vai trò |
|---|---|---|
| `/admin` | Admin Dashboard | Admin |
| `/admin/reports` | Hàng đợi kiểm duyệt report | Admin/Moderator |
| `/admin/reports/[reportId]` | Chi tiết report | Admin/Moderator |
| `/admin/risk-entities` | Quản lý Risk Entities | Admin |
| `/admin/rules` | Quản lý Rule Engine | Admin |
| `/admin/rules/preview` | Rule Preview | Admin |
| `/admin/users` | Quản lý người dùng | Admin |
| `/admin/audit-logs` | Audit Logs | Admin |
| `/admin/exports` | Export báo cáo | Admin, Phase 2 |

## 3. Component dùng chung

### C01. Risk Result Card

**Mục đích:** Hiện kết quả scan thống nhất cho URL, text, phone, bank, QR.  
**Dùng cho:** User web, mobile user, admin khi xem report/scan.

**Layout component:**

- Header: badge `SAFE`/`CAUTION`/`DANGER`, score 0-100, confidence optional.
- Summary: một câu ngắn nói rủi ro chính.
- Recommendation block: hành động nên làm tiếp theo.
- Evidence list: 3-5 evidence quan trọng nhất; mỗi item có rule name, severity, score contribution, mô tả.
- Expand details: analysis JSON được hiện thành các mục dễ đọc, không hiện raw technical dump.
- Actions: "Gửi báo cáo", "Lưu kết quả", "Quét lại", "Sao chép mã scan".
- Disclaimer footer: kết quả là đánh giá rủi ro dựa trên rule và dữ liệu cộng đồng.

**Trạng thái:**

- Loading: skeleton score/evidence.
- Processing async: hiện `scanId`, polling hoặc nút tải lại.
- Error: validation/rate limit/service unavailable.
- Empty: chưa có kết quả.

### C02. Evidence Item

**Mục đích:** Giải thích vì sao hệ thống cộng điểm.  
**Component con:** severity icon/color, rule code, explanation, matched value masked, source.

**Yêu cầu:** Evidence phải đọc được với người phổ thông; chi tiết kỹ thuật như Levenshtein/redirect/TLS đưa vào expand.

### C03. Entity Mask Display

**Mục đích:** Hiện phone/account/domain an toàn.  
**Ví dụ:** `+849***678`, `1234****7890`, domain hiện đầy đủ nếu không phải PII.

**Dùng cho:** Result card, history, report list, admin list với mode user/admin.

### C04. Scan Input Panel

**Mục đích:** Khung nhập chung cho các màn scan.  
**Thành phần:** title, helper text ngắn, input field, source/context selector, save history toggle, submit button, privacy hint.

### C05. Admin Data Table

**Mục đích:** Hiện danh sách report/rule/entity/user.  
**Thành phần:** filter bar, search, status chips, sortable columns, pagination, row actions, bulk action nếu cần.

### C06. Status Timeline

**Mục đích:** Hiện vòng đời report hoặc scan async.  
**Trạng thái:** submitted, auto analyzed, under review, approved/rejected/request more info, entity created.

## 4. Web user screens

### W01. Home / Quick Scan

**Route:** `/`  
**Người dùng:** Khách, User đã đăng nhập  
**Mục đích:** Cho người dùng bắt đầu scan nhanh và hiểu sản phẩm làm gì.

**Layout:**

- Top navigation: logo, Scan, History, Reports, Login/Profile.
- Main quick scan area:
  - Segmented control: URL, Tin nhắn, Số điện thoại, Số tài khoản, QR.
  - Input tương ứng với tab đang chọn.
  - CTA "Kiểm tra".
  - Privacy hint: "Chỉ kiểm tra nội dung bạn chủ động nhập/share."
- Recent threat education strip: 2-3 mẫu tình huống ngắn như link ngân hàng giả, QR chuyển khoản, số điện thoại bị report.
- Trust strip: explainable result, community report, privacy-by-design.

**API:** tùy tab gọi scan endpoint tương ứng.  
**Trạng thái:** empty input, validation error, anonymous limit, redirect login nếu cần lưu history.

### W02. Đăng ký

**Route:** `/register`  
**Người dùng:** Khách  
**Mục đích:** Tạo tài khoản để lưu lịch sử và gửi report.

**Layout:**

- Centered auth form, không cần hero lớn.
- Fields: email, username, password, confirm password.
- Inline password requirements.
- Submit button, link sang đăng nhập.
- Error summary trên form nếu backend trả validation error.

**API:** `POST /v1/auth/register`  
**Trạng thái:** username/email existed, password invalid, rate limited, success.

### W03. Đăng nhập

**Route:** `/login`  
**Người dùng:** Khách, Admin  
**Mục đích:** Lấy session JWT và role.

**Layout:**

- Auth form: username/email, password.
- Submit button.
- Secondary links: đăng ký, quên mật khẩu phase sau.
- Security note ngắn: "Không chia sẻ mật khẩu/OTP với bất kỳ ai."

**API:** `POST /v1/auth/login`  
**Trạng thái:** sai thông tin, bị rate limit, account suspended, success redirect theo role.

### W04. Scan URL

**Route:** `/scan/url`  
**Người dùng:** User, khách nếu cho anonymous  
**Mục đích:** Kiểm tra URL/link đáng nghi.

**Layout:**

- Header ngắn: "Kiểm tra link trước khi bấm".
- Scan Input Panel:
  - URL input.
  - Context dropdown: SMS, Zalo, Messenger, Email, Browser, Other.
  - Save history toggle.
  - Submit button.
- Result area bên dưới:
  - Risk Result Card.
  - URL analysis section: normalized URL, domain, HTTPS, redirect count, blacklist/whitelist, brand similarity.
  - Form/HTML signals section nếu có: sensitive fields, form action, hidden URL.
- Side panel desktop / accordion mobile: cách đọc kết quả và khuyến nghị liên hệ kênh chính thức.

**API:** `POST /v1/scan/url`, `GET /v1/scan/{scanId}`  
**Trạng thái:** invalid URL, SSRF blocked, timeout, processing async, rate limited.

### W05. Scan Text

**Route:** `/scan/text`  
**Người dùng:** User  
**Mục đích:** Phân tích SMS, email, chat, bài đăng giao dịch, tuyển dụng, thuê trọ.

**Layout:**

- Header: "Kiểm tra nội dung tin nhắn/giao dịch".
- Textarea lớn, counter tối đa 5000 ký tự.
- Source selector: SMS, ZALO, MESSENGER, EMAIL, MARKETPLACE, OTHER.
- Save history toggle.
- Privacy warning inline: không dán OTP/mật khẩu/số thẻ nếu không cần.
- Submit button.
- Result sections:
  - Risk Result Card.
  - Extracted entities: URL, phone, bank account, amount, deadline, OTP-like masked.
  - Keyword/pattern matches.
  - Cross-reference checks: URL/phone/bank sub-results.

**API:** `POST /v1/scan/text`, `GET /v1/scan/{scanId}`  
**Trạng thái:** text quá dài, empty text, sensitive data detected warning, processing, failed worker.

### W06. Kiểm tra số điện thoại

**Route:** `/scan/phone`  
**Người dùng:** User  
**Mục đích:** Tra cứu số điện thoại trước khi gọi lại hoặc giao dịch.

**Layout:**

- Phone input với helper: hỗ trợ `0912345678` hoặc `+84912345678`.
- Context optional: gọi điện, tin nhắn, người bán, shipper, khác.
- Submit button.
- Result:
  - Risk Result Card.
  - Report stats: report count, verified reports, last reported at, scam categories.
  - Entity status: whitelist/verified/pending/resolved.
  - Actions: gửi report, copy số đã mask, xem báo cáo liên quan nếu có quyền.

**API:** `POST /v1/scan/phone`  
**Trạng thái:** invalid VN phone, no data found, rate limited, masked display.

### W07. Kiểm tra số tài khoản ngân hàng

**Route:** `/scan/bank-account`  
**Người dùng:** User  
**Mục đích:** Kiểm tra STK trước khi chuyển tiền/đặt cọc.

**Layout:**

- Form fields:
  - Bank dropdown: VCB, TCB, MB, ACB, BIDV, Other/Unknown.
  - Account number input.
  - Account holder name optional.
  - Transaction context optional: mua hàng, đặt cọc, thuê trọ, tuyển dụng, hoàn tiền.
- Submit button.
- Result:
  - Risk Result Card.
  - Blacklist/report history section.
  - Recommendation: dừng chuyển tiền nếu DANGER; xác minh qua kênh khác nếu CAUTION.
  - Button gửi report.

**API:** `POST /v1/scan/bank-account`  
**Trạng thái:** invalid account, unknown bank code, no report found, verified blacklist.

### W08. Scan QR

**Route:** `/scan/qr`  
**Người dùng:** User web  
**Mục đích:** Kiểm tra QR/VietQR khi user có ảnh QR hoặc raw data.

**Layout:**

- Input tabs:
  - Upload QR image.
  - Paste raw QR data.
- Preview area: ảnh QR nếu upload.
- Parsed data panel:
  - QR type: URL, VietQR, Text, Unknown.
  - URL/domain hoặc bank/account/amount/content đã mask.
- Result:
  - Risk Result Card tổng hợp.
  - Sub-result cards cho URL, bank, text nếu có.

**API:** `POST /v1/scan/qr`  
**Trạng thái:** cannot decode image, qrData quá dài, unknown QR format, processing async.

### W09. Chi tiết kết quả scan

**Route:** `/scan/result/[scanId]`  
**Người dùng:** User, Admin khi có quyền audit  
**Mục đích:** Xem lại đầy đủ một lần scan.

**Layout:**

- Header: type, scan time, status, risk badge.
- Main: Risk Result Card.
- Detail tabs:
  - Analysis.
  - Evidence.
  - Recommendation.
  - Raw normalized input masked.
- Actions: gửi report, tải báo cáo PDF/HTML phase 2, xóa khỏi lịch sử phase sau.

**API:** `GET /v1/scan/{scanId}`  
**Trạng thái:** not found, forbidden ownership, processing, failed.

### W10. Lịch sử scan

**Route:** `/history`  
**Người dùng:** User  
**Mục đích:** Quản lý các lần scan đã lưu.

**Layout:**

- Filter bar: type, risk level, date range, keyword.
- Table/list:
  - Type icon.
  - Input masked.
  - Risk badge.
  - Score.
  - Scanned at.
  - Actions: view detail, report.
- Pagination.
- Empty state: hướng dẫn scan đầu tiên.

**API:** `GET /v1/scan/history`  
**Trạng thái:** no history, filters no result, rate limited.

### W11. Gửi báo cáo

**Route:** `/reports/new`  
**Người dùng:** User  
**Mục đích:** Gửi report cộng đồng về URL/phone/bank/text.

**Layout:**

- Bước 1: Entity info.
  - Entity type: URL, PHONE, BANK_ACCOUNT, TEXT.
  - Entity value, auto-fill nếu đi từ scan result.
- Bước 2: Scam details.
  - Scam type dropdown: FAKE_SELLER, BANK_IMPERSONATION, FAKE_JOB, FAKE_SHIPPER, PHISHING_URL, OTHER.
  - Description textarea.
  - Amount lost optional.
  - Transaction date optional.
- Bước 3: Evidence upload.
  - Screenshots/chat logs.
  - Note về che thông tin nhạy cảm nếu cần.
- Submit summary và consent.

**API:** `POST /v1/reports`  
**Trạng thái:** missing required fields, file too large, report rate limit, success pending.

### W12. Báo cáo của tôi

**Route:** `/reports/my`  
**Người dùng:** User  
**Mục đích:** Theo dõi trạng thái report đã gửi.

**Layout:**

- Status filter: PENDING, UNDER_REVIEW, VERIFIED, REJECTED, REQUEST_MORE_INFO.
- Report list: entity masked, scam type, status, submittedAt, reviewedAt.
- Detail drawer:
  - Reviewer note.
  - Timeline.
  - Nút bổ sung bằng chứng nếu REQUEST_MORE_INFO.

**API:** `GET /v1/reports/my`  
**Trạng thái:** empty, no status match, forbidden.

### W13. Hồ sơ cá nhân

**Route:** `/profile`  
**Người dùng:** User, Admin  
**Mục đích:** Xem thông tin tài khoản và hành động cá nhân.

**Layout:**

- Profile summary: username, email, role, joined date, scan count.
- Security actions: đổi mật khẩu, logout all devices phase sau.
- Recent scans module.
- Recent reports module.

**API:** `GET /v1/users/me`, `PATCH /v1/users/me/password`  
**Trạng thái:** token expired, password changed success, validation error.

### W14. Cài đặt riêng tư

**Route:** `/settings/privacy`  
**Người dùng:** User  
**Mục đích:** Tăng niềm tin và cho user quản lý dữ liệu.

**Layout:**

- Data collection explanation:
  - Hệ thống scan dữ liệu user chủ động gửi.
  - Không yêu cầu OTP/mật khẩu/số thẻ.
  - Mobile không đọc SMS/notification/call log ngầm.
- Toggles:
  - Save scan history.
  - Allow anonymized analytics phase sau.
- Actions:
  - Xem/xóa lịch sử phase sau.
  - Tải dữ liệu của tôi phase sau.

**API:** tùy thiết kế settings phase sau.  
**Trạng thái:** settings saved, permission explanation only.

## 5. Mobile screens

### M01. Onboarding & Trust

**Người dùng:** Mobile user mới  
**Mục đích:** Giải thích sản phẩm, quyền và privacy trước khi xin bất kỳ permission nào.

**Layout:**

- 3-4 slides ngắn:
  - Kiểm tra link/tin nhắn/QR/số tài khoản.
  - Kết quả có điểm, lý do, khuyến nghị.
  - App chỉ xử lý khi bạn paste/share/quét.
  - CAMERA chỉ dùng khi bạn mở QR Scanner.
- CTA: Bắt đầu, Đăng nhập, Dùng không cần đăng nhập nếu cho phép.

**Permission:** Không xin CAMERA tại onboarding; chỉ giải thích trước.

### M02. Mobile Login

**Người dùng:** Mobile user  
**Mục đích:** Đăng nhập và lưu token an toàn.

**Layout:**

- Email/username field.
- Password field.
- Login button.
- Register link.
- Error message.

**API:** `POST /v1/auth/login`  
**Trạng thái:** sai thông tin, network error, rate limit.

### M03. Manual Input Tabs

**Người dùng:** Mobile user  
**Mục đích:** Scan nhanh bằng thao tác chủ động.

**Layout:**

- Top app bar: title, history icon, profile icon.
- Tab bar: URL, Text, Phone, Bank, QR raw.
- Input theo tab:
  - URL input.
  - Textarea ngắn có expand.
  - Phone input.
  - Bank dropdown + account input.
  - QR raw paste.
- Button "Kiểm tra".
- Secondary button "Kiểm tra từ clipboard" chỉ đọc clipboard khi bấm.
- Result bottom sheet dùng Risk Result Card mobile.

**API:** scan endpoints tương ứng.  
**Trạng thái:** keyboard overlap, invalid input, clipboard empty, processing.

### M04. QR Camera Scanner

**Người dùng:** Mobile user  
**Mục đích:** Quét QR/VietQR bằng camera.

**Layout:**

- Full camera preview.
- Scan frame.
- Top actions: close, flashlight nếu hỗ trợ.
- Permission rationale screen nếu chưa cấp CAMERA.
- On detected QR: pause camera, hiện parsed preview và loading scan.
- Result bottom sheet.

**API:** `POST /v1/scan/qr`  
**Permission:** CAMERA runtime only.  
**Trạng thái:** permission denied, camera unavailable, QR unknown, scan failed.

### M05. Share Target Check

**Người dùng:** Mobile user  
**Mục đích:** Nhận URL/text từ app khác qua share intent.

**Layout:**

- Compact loading screen: "Đang kiểm tra nội dung bạn vừa chia sẻ".
- Preview card với URL/text snippet masked.
- Nếu input ambiguous: chọn scan as URL hoặc Text.
- Result summary.
- Actions: quay lại app trước, mở Anti-Scam, gửi report.

**API:** `POST /v1/scan/url` hoặc `POST /v1/scan/text`  
**Trạng thái:** no shared content, unsupported MIME, network error.

### M06. Mobile Result Summary

**Người dùng:** Mobile user  
**Mục đích:** Hiện kết quả ngắn gọn để quyết định tại chỗ.

**Layout:**

- Top risk badge và score lớn.
- Một câu summary.
- 3-5 reason cards.
- Recommendation checklist.
- Primary action theo level:
  - SAFE: "Đã hiểu".
  - CAUTION: "Xem chi tiết".
  - DANGER: "Gửi báo cáo" hoặc "Không mở link".
- Secondary actions: scan lại, save history, share warning.

**API:** `GET /v1/scan/{scanId}`, `POST /v1/reports` khi report.  
**Yêu cầu:** Không nhồi quá nhiều chi tiết kỹ thuật trên mobile; evidence chi tiết ở expand.

### M07. Mobile Report Submission

**Người dùng:** Mobile user  
**Mục đích:** Gửi report nhanh từ kết quả scan.

**Layout:**

- Entity auto-filled masked.
- Scam type chips.
- Description textarea.
- Attach screenshot button.
- Amount/date optional collapsed.
- Submit button.

**API:** `POST /v1/reports`  
**Trạng thái:** upload progress, file error, success pending.

### M08. Mobile History

**Người dùng:** Mobile user  
**Mục đích:** Xem lại scan gần đây.

**Layout:**

- Search/filter chips: URL, Text, Phone, Bank, QR, Danger.
- Vertical list cards.
- Swipe/action menu: view, report.

**API:** `GET /v1/scan/history`  
**Trạng thái:** empty, offline cached phase sau.

### M09. Privacy & Permission Settings

**Người dùng:** Mobile user  
**Mục đích:** Quản lý niềm tin và permission.

**Layout:**

- Permission list:
  - Camera: chỉ dùng QR scanner.
  - Notifications của app: phase sau nếu cần báo scan complete.
  - Không dùng SMS/call log/notification listener/accessibility.
- Data controls: save history toggle, clear local cache.
- Links: privacy policy, data safety summary.

**API:** settings phase sau.  
**Trạng thái:** permission denied, open system settings.

## 6. Admin screens

### A01. Admin Dashboard

**Route:** `/admin`  
**Người dùng:** Admin  
**Mục đích:** Theo dõi tổng quan hệ thống.

**Layout:**

- KPI cards:
  - Total scans.
  - Danger scans.
  - Pending reports.
  - Verified reports.
  - Entities added this period.
- Charts:
  - Scan trend theo ngày.
  - Risk level distribution.
  - Scan by type.
  - Top scam types.
- Action queue:
  - Reports cần duyệt.
  - Rules vừa thay đổi.
  - Entity spike đáng chú ý.

**API:** `GET /v1/admin/dashboard/overview`, `GET /v1/admin/dashboard/trends`  
**Trạng thái:** loading, empty period, API forbidden.

### A02. Hàng đợi kiểm duyệt report

**Route:** `/admin/reports`  
**Người dùng:** Admin/Moderator  
**Mục đích:** Lọc và ưu tiên report cần xử lý.

**Layout:**

- Filter bar: status, priority, entity type, scam type, date.
- Data table columns:
  - Report ID.
  - Entity type/value.
  - Scam type.
  - Reporter reputation.
  - Auto risk score.
  - Status.
  - Submitted at.
  - Action.
- Bulk action chỉ nên có cho thao tác an toàn; approve nên xử lý từng report.

**API:** `GET /v1/admin/reports`  
**Trạng thái:** no pending reports, high priority highlight, pagination.

### A03. Chi tiết report

**Route:** `/admin/reports/[reportId]`  
**Người dùng:** Admin/Moderator  
**Mục đích:** Kiểm tra bằng chứng và ra quyết định.

**Layout:**

- Header: report status, priority, submittedAt.
- Left/main:
  - Entity card: type, full value cho admin, masked preview, current entity status.
  - Description.
  - Evidence gallery: screenshots/chat logs.
  - Auto analysis: score, suggestions, related scan result.
  - Related reports.
  - Timeline.
- Right decision panel:
  - Action: approve, reject, request more info.
  - Add to blacklist toggle.
  - Risk level selector.
  - Category selector.
  - Reviewer note.
  - Notify reporter checkbox.
  - Confirm button with impact summary.

**API:** `GET /v1/admin/reports/{reportId}`, `POST /v1/admin/reports/{reportId}/review`  
**Trạng thái:** evidence loading, report already reviewed, missing reviewer note, confirm destructive impact.

### A04. Quản lý Risk Entities

**Route:** `/admin/risk-entities`  
**Người dùng:** Admin  
**Mục đích:** Quản lý blacklist/whitelist/risk entity.

**Layout:**

- Search/filter: type, status, risk level, source, report count.
- Entity table:
  - Type.
  - Value full/masked toggle theo quyền.
  - Risk level.
  - Status.
  - Source.
  - Report count.
  - Verified at.
  - Updated at.
- Create/Edit drawer:
  - Entity type.
  - Value.
  - Risk level.
  - Status: VERIFIED, PENDING, RESOLVED, WHITELIST.
  - Category.
  - Notes.
- Audit side panel for selected entity.

**API:** `GET /v1/admin/risk-entities`, `POST /v1/admin/risk-entities`, `PUT /v1/admin/risk-entities/{entityId}`  
**Trạng thái:** duplicate entity, invalid phone/account, cache invalidation notice.

### A05. Quản lý Rule Engine

**Route:** `/admin/rules`  
**Người dùng:** Admin cấu hình rule  
**Mục đích:** Xem và cập nhật rule chấm điểm.

**Layout:**

- Filter tabs: URL, TEXT, PHONE, BANK, QR, COMPOSITE.
- Rule table:
  - Code.
  - Name.
  - Category.
  - Weight.
  - Severity.
  - Enabled.
  - Version.
  - Updated at.
- Edit panel:
  - Name, description.
  - Weight numeric input.
  - Severity selector.
  - Enabled toggle.
  - Explanation template.
  - Recommendation template.
  - Conditions builder: field, operator, value, AND/OR.
- Actions: save, duplicate, disable, delete with confirmation.

**API:** `GET /v1/admin/rules`, `POST /v1/admin/rules`, `PUT /v1/admin/rules/{ruleId}`, `DELETE /v1/admin/rules/{ruleId}`  
**Trạng thái:** invalid condition, duplicate code, disabled rule, cache rebuild failed.

### A06. Rule Preview

**Route:** `/admin/rules/preview`  
**Người dùng:** Admin cấu hình rule  
**Mục đích:** Thử payload mẫu để xem rule match trước khi bật rule thật.

**Layout:**

- Payload type selector: URL, TEXT, PHONE, BANK, QR.
- Sample input textarea/form.
- Button evaluate.
- Result:
  - Matched rules.
  - Score contribution.
  - Final risk level dự kiến.
  - Evidence preview.
  - Recommendation preview.
- Warning: preview không lưu vào lịch sử scan user.

**API:** `GET /v1/rules/evaluate/preview` hoặc endpoint preview admin tương ứng.  
**Trạng thái:** invalid payload, no matched rules, preview service error.

### A07. Quản lý người dùng

**Route:** `/admin/users`  
**Người dùng:** Admin  
**Mục đích:** Xem user và trạng thái tài khoản.

**Layout:**

- Filter: role, status, created date.
- User table:
  - Username.
  - Email.
  - Role.
  - Status.
  - Scan count.
  - Report count.
  - Created at.
- Detail drawer:
  - Basic profile.
  - Recent activity.
  - Account status actions: suspend/reactivate phase sau.

**API:** `GET /v1/admin/users`  
**Trạng thái:** forbidden, pagination, no users match.

### A08. Audit Logs

**Route:** `/admin/audit-logs`  
**Người dùng:** Admin  
**Mục đích:** Truy vết thao tác nhạy cảm.

**Layout:**

- Filter: actor, action, target type, date range.
- Append-only log table:
  - Timestamp.
  - Actor.
  - Action.
  - Target.
  - IP address.
  - User agent.
  - Metadata summary.
- Detail drawer: JSON metadata formatted.

**API đề xuất:** `GET /v1/admin/audit-logs`  
**Trạng thái:** no logs, restricted metadata, export phase sau.

### A09. Export báo cáo

**Route:** `/admin/exports`  
**Người dùng:** Admin  
**Mục đích:** Tạo báo cáo HTML/PDF theo ngày/tuần/tháng, Phase 2.

**Layout:**

- Report type selector: scan statistics, report moderation, top risk entities.
- Period picker.
- Format selector: HTML, PDF.
- Generate button.
- Export jobs table: status, createdAt, file link.

**API đề xuất:** report export endpoint phase 2.  
**Trạng thái:** queued, processing, completed, failed.

## 7. Error và empty states cần có

| Tình huống | Cách hiển thị |
|---|---|
| Validation error | Inline field message, nếu có nhiều lỗi thì thêm summary nhỏ. |
| Rate limit | Nói rõ còn bao lâu thử lại; không để user bấm spam. |
| Unauthorized | Chuyển login và giữ redirect URL. |
| Forbidden | Hiện "Không có quyền truy cập" và nút về trang chủ/admin dashboard tùy role. |
| Processing async | Hiện scanId, progress text, polling hoặc nút refresh. |
| Worker failed | Hiện lý do thân thiện, cho scan lại. |
| No reports/history | Empty state có CTA scan/gửi report đầu tiên. |
| Permission denied mobile camera | Giải thích CAMERA chỉ dùng QR, có nút mở settings và fallback manual input. |
| Sensitive input detected | Nhắc user mask/loại bỏ OTP/mật khẩu/số thẻ trước khi gửi. |

## 8. Mẫu nội dung hiển thị nên dùng

| Ngữ cảnh | Nên dùng | Không nên dùng |
|---|---|---|
| Kết quả nguy hiểm | "Có dấu hiệu rủi ro cao. Không nên truy cập/chuyển tiền trước khi xác minh." | "Đây chắc chắn là lừa đảo." |
| Báo cáo cộng đồng | "Đã có 5 báo cáo, 3 báo cáo đã được xác minh." | "Số này là kẻ lừa đảo." |
| Không có dữ liệu | "Chưa tìm thấy tín hiệu rủi ro trong dữ liệu hiện có." | "An toàn tuyệt đối." |
| Mobile privacy | "App chỉ kiểm tra nội dung bạn chủ động gửi." | "App tự động bảo vệ mọi tin nhắn." |
| Admin approve | "Approve sẽ thêm entity vào blacklist và ảnh hưởng kết quả scan sau." | "Duyệt." |

## 9. API mapping nhanh theo screen

| Screen | API |
|---|---|
| W02 Đăng ký | `POST /v1/auth/register` |
| W03 Đăng nhập, M02 Mobile Login | `POST /v1/auth/login` |
| W04 Scan URL | `POST /v1/scan/url`, `GET /v1/scan/{scanId}` |
| W05 Scan Text | `POST /v1/scan/text`, `GET /v1/scan/{scanId}` |
| W06 Phone | `POST /v1/scan/phone` |
| W07 Bank | `POST /v1/scan/bank-account` |
| W08/M04 QR | `POST /v1/scan/qr` |
| W09/M06 Result | `GET /v1/scan/{scanId}` |
| W10/M08 History | `GET /v1/scan/history` |
| W11/M07 Report | `POST /v1/reports` |
| W12 My Reports | `GET /v1/reports/my` |
| W13 Profile | `GET /v1/users/me`, `PATCH /v1/users/me/password` |
| A01 Dashboard | `GET /v1/admin/dashboard/overview`, `GET /v1/admin/dashboard/trends` |
| A02-A03 Reports Admin | `GET /v1/admin/reports`, `GET /v1/admin/reports/{reportId}`, `POST /v1/admin/reports/{reportId}/review` |
| A04 Risk Entities | `GET /v1/admin/risk-entities`, `POST /v1/admin/risk-entities`, `PUT /v1/admin/risk-entities/{entityId}` |
| A05 Rules | `GET /v1/admin/rules`, `POST /v1/admin/rules`, `PUT /v1/admin/rules/{ruleId}`, `DELETE /v1/admin/rules/{ruleId}` |
| A06 Rule Preview | `GET /v1/rules/evaluate/preview` hoặc endpoint admin preview tương ứng |
| A07 Users | `GET /v1/admin/users` |
| A08 Audit | `GET /v1/admin/audit-logs` đề xuất |

## 10. Ưu tiên thiết kế giao diện

| Mức ưu tiên | Screens | Lý do |
|---|---|---|
| P0 | W02, W03, W04, W05, W06, W07, W09, W10, A01, A02, A03, A05 | Đủ để demo auth, scan, result, history, admin review và rule. |
| P1 | W08, W11, W12, W13, A04, M01, M02, M03, M06 | Hoàn thiện QR, report cộng đồng, profile, entity và mobile manual MVP. |
| P2 | M04, M05, M07, M08, M09, A06, A07, A08 | Nâng cao mobile/share/admin audit và rule preview. |
| P3 | W14, A09, export PDF/HTML, notification scan complete | Phase 2 hoặc sau MVP. |
