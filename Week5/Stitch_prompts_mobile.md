# Prompt Stitch cho 9 màn hình mobile (M01–M09)

**Nguồn:** `Dac_ta_man_hinh_giao_dien.md` mục 5
**Công cụ:** Google Stitch (stitch.withgoogle.com), chế độ **Mobile**
**Cách dùng:** mỗi mục dưới đây là một prompt độc lập, copy nguyên khối trong code block rồi dán vào ô prompt của Stitch.

---

## 0. Vài điều cần biết trước khi dán

**Thao tác bắt buộc ở màn hình đầu tiên của Stitch.** Ngay dưới ô prompt có cặp nút chọn loại sản phẩm: **Ứng dụng** (icon điện thoại) và **Web** (icon màn hình). Mặc định Stitch đang chọn **Web**, phải bấm sang **Ứng dụng** trước khi dán prompt, nếu không nó sinh giao diện desktop. Đây là lỗi dễ mắc nhất và mất một lượt quota.

**Model.** Ô chọn model bên phải ô prompt (hiện là `3.1 Pro`) cứ để nguyên bản mạnh nhất đang có. Không bấm "Bắt đầu với bản thiết kế của bạn" vì nút đó dùng để upload ảnh thiết kế có sẵn. Cũng không cần dùng icon bảng màu để set theme, vì các prompt dưới đây đã ghi mã màu cụ thể, set hai nơi dễ xung đột.

**Thứ tự làm.** Theo mức ưu tiên trong đặc tả (mục 10): làm M01, M02, M03, M06 trước vì thuộc P1 và đủ để demo luồng mobile; M04, M05, M07, M08, M09 làm sau.

**Làm M03 trước tiên, kể cả khi nó không phải màn đầu.** Stitch giữ ngữ cảnh trong cùng một project: màn đầu bạn generate sẽ định luôn tông màu, font, bo góc cho các màn sau. M03 có nhiều thành phần nhất nên dùng nó để "đặt chuẩn" là tốt nhất. Các màn sau chỉ cần nói "same theme as previous screen".

**Tất cả 9 màn dán trong cùng MỘT project.** Sau khi generate màn đầu, ở lại trong project và dùng ô chat ở đó cho các màn tiếp theo. Tạo project mới thì mất ngữ cảnh và câu "same theme as the previous screen" trong các prompt sau sẽ không có tác dụng.

**Thứ tự dán cụ thể:** M03, rồi M06 bản DANGER, rồi hai prompt phụ ra bản CAUTION và SAFE, rồi M01, rồi M02. Sau đó mới đến M04, M05, M07, M08, M09.

**Về tiếng Việt.** Stitch đôi khi làm rơi dấu hoặc tự dịch sang tiếng Anh. Hai cách xử lý: hoặc nhắc lại "keep all Vietnamese text exactly as written, including diacritics" trong prompt tiếp theo, hoặc để nó sinh layout rồi sửa chữ trong Figma sau khi export. Đừng mất thời gian bắt Stitch gõ đúng dấu, layout mới là thứ cần lấy.

**Export.** Xong thì dùng "Copy to Figma" để dán vào Figma chỉnh tiếp, hoặc export HTML/CSS để tham chiếu khi code React Native. Code Stitch xuất ra là HTML, không phải React Native, nên chỉ dùng để đối chiếu khoảng cách, màu và kích thước chứ không copy trực tiếp được.

**Tên app.** Các prompt đang dùng tên tạm là "SafeScan". Nhóm chốt tên rồi thì tìm thay thế trong tất cả prompt.

---

## 1. Khối design system

Khối này đã được nhúng sẵn vào từng prompt bên dưới nên bạn không cần dán riêng. Ghi ở đây để nhóm biết đang thống nhất theo bộ nào, và để sửa một chỗ nếu muốn đổi màu.

```
Style: modern Material 3, clean and trustworthy, generous whitespace,
16dp screen margins, 12dp rounded corners, subtle shadows.
Font: Inter. Headings semibold, body regular.
Primary: #1D4ED8 (blue). Background: #F6F8FB. Surface: white.
Text primary #0F172A, text secondary #64748B.
Risk colors: SAFE #16A34A green, CAUTION #F59E0B amber, DANGER #DC2626 red.
Icons: Material Symbols Rounded.
```

Ba màu rủi ro là phần quan trọng nhất và phải giữ nguyên qua mọi màn hình, vì người dùng nhận diện mức nguy hiểm bằng màu trước khi kịp đọc chữ.

---

## 2. M03. Manual Input Tabs

Làm màn này đầu tiên.

```
Design a mobile app screen for "SafeScan", a Vietnamese anti-scam checking app
that lets users check suspicious links, messages, phone numbers, bank accounts
and QR codes before they click or transfer money.

Style: modern Material 3, clean and trustworthy, generous whitespace,
16dp screen margins, 12dp rounded corners, subtle shadows.
Font: Inter. Headings semibold, body regular.
Primary: #1D4ED8 (blue). Background: #F6F8FB. Surface: white.
Text primary #0F172A, text secondary #64748B.
Risk colors: SAFE #16A34A green, CAUTION #F59E0B amber, DANGER #DC2626 red.
Icons: Material Symbols Rounded.

Screen: "Manual Input" — the main scanning screen. Keep ALL UI text exactly in
Vietnamese as written below, including diacritics.

Layout top to bottom:
1. Top app bar: title "Kiểm tra", a history icon and a profile avatar on the right.
2. A scrollable segmented tab bar with 5 tabs, first tab selected:
   "Link" | "Tin nhắn" | "Số ĐT" | "Số TK" | "QR"
3. Active tab content (URL tab):
   - Label "Dán link cần kiểm tra"
   - A large outlined text field, placeholder "https://..."
   - Helper text below in secondary color: "Tối đa 2048 ký tự"
4. A dropdown labeled "Bạn nhận link này từ đâu?" with value "Tin nhắn SMS".
5. A row with a switch, label "Lưu vào lịch sử", switch is on.
6. Full-width filled primary button: "Kiểm tra"
7. Below it a full-width outlined secondary button with a clipboard icon:
   "Dán từ clipboard"
8. A small privacy note card with a shield icon, light blue background,
   text in secondary color: "Ứng dụng chỉ kiểm tra nội dung bạn chủ động dán,
   chia sẻ hoặc quét. Không đọc tin nhắn hay thông báo của bạn."
9. Bottom navigation bar with 4 items, first active:
   "Kiểm tra" (shield icon), "Quét QR" (qr icon), "Lịch sử" (history icon),
   "Cá nhân" (person icon).

No result content on this screen yet.
```

Nút "Dán từ clipboard" phải là nút bấm rõ ràng chứ không phải tự động đọc clipboard khi mở app. Đây là ràng buộc từ mục 1.7 và 5.M03 của đặc tả, và cũng là điểm nhóm sẽ bị hỏi khi bảo vệ.

---

## 3. M06. Mobile Result Summary

Màn quan trọng thứ hai. Sinh bản DANGER trước vì nó có nhiều thành phần nhất, hai bản còn lại chỉ cần đổi màu và chữ.

```
Same app and same theme as the previous screen.

Screen: "Result Summary" — a full-screen scan result shown as a bottom sheet
that covers about 92% of the screen, with a drag handle at the top and rounded
top corners. Keep ALL UI text exactly in Vietnamese as written below.

This is the DANGER state, so the accent color is red #DC2626.

Layout top to bottom:
1. Drag handle, then a close X icon on the left and a share icon on the right.
2. Hero risk block, centered, on a very light red background (#FEF2F2):
   - A large circular score ring, red, showing "92" in very large bold text
     with "/100" small next to it
   - Below it a red pill badge with a warning icon: "NGUY HIỂM"
3. Summary sentence, 18sp semibold, centered:
   "Trang này có nhiều dấu hiệu giả mạo ngân hàng"
4. Section title "Vì sao?" then 4 reason cards stacked vertically. Each card is
   white with rounded corners, a colored severity dot on the left, a bold title,
   a one-line explanation in secondary color, and a small score chip on the right:
   - "Tên miền gần giống thương hiệu thật" / "Chỉ khác vietcombank.com.vn 2 ký tự" / "+30"
   - "Form yêu cầu thông tin nhạy cảm" / "Trang có ô nhập mã OTP và mật khẩu" / "+25"
   - "Không có HTTPS hợp lệ" / "Kết nối không được mã hóa" / "+20"
   - "Tên miền mới đăng ký" / "Đăng ký cách đây 5 ngày" / "+20"
   Below the cards a text button: "Xem tất cả bằng chứng"
5. Section title "Bạn nên làm gì" then a checklist with 3 items, each with a
   green check icon:
   - "Không nhập thông tin đăng nhập hay mã OTV vào trang này"
   - "Gọi tổng đài ngân hàng theo số in trên thẻ để xác minh"
   - "Không chuyển tiền cho đến khi xác minh được"
6. Sticky bottom action area on white with a top border:
   - Full-width filled RED button: "Gửi báo cáo"
   - Below it a full-width outlined button: "Kiểm tra lại"
7. Tiny disclaimer text at the very bottom, centered, secondary color, 11sp:
   "Đây là đánh giá rủi ro tự động dựa trên quy tắc và dữ liệu cộng đồng,
   không phải kết luận pháp lý."
```

Chú ý một lỗi tôi cố tình để bạn kiểm tra khi generate: dòng checklist đầu ghi "mã OTV", đúng phải là "mã OTP". Stitch hay chép nguyên si nên sửa trong prompt trước khi dán, hoặc sửa lại sau trong Figma.

Sau khi có bản DANGER, dùng hai prompt phụ để ra hai state còn lại:

```
Same screen, but the CAUTION state: change the accent to amber #F59E0B,
hero background to #FFFBEB, score "48", badge text "CẦN THẬN TRỌNG",
summary "Có một vài dấu hiệu đáng ngờ, nên xác minh trước khi giao dịch",
primary button becomes "Xem chi tiết" in amber, and show only 2 reason cards.
```

```
Same screen, but the SAFE state: accent green #16A34A, hero background #F0FDF4,
score "12", badge text "CHƯA THẤY RỦI RO", summary
"Chưa tìm thấy tín hiệu rủi ro trong dữ liệu hiện có",
show 1 reason card, primary button "Đã hiểu",
and add a note under the summary in secondary color:
"Không có dữ liệu không đồng nghĩa với an toàn tuyệt đối."
```

Câu cuối của bản SAFE là ràng buộc từ mục 8 của đặc tả. Không có nó thì hệ thống đang ngầm hứa một điều không kiểm chứng được.

---

## 4. M01. Onboarding & Trust

```
Same app and same theme as the previous screens.

Screen: onboarding slide 3 of 4. Keep ALL UI text exactly in Vietnamese.

Layout:
1. "Bỏ qua" text button in the top right corner.
2. A large centered flat vector illustration, about 55% of screen width:
   a smartphone with a blue shield in front of it and a paste/share arrow icon,
   using the primary blue with green accents, friendly and simple, no text inside.
3. Headline, 24sp bold, centered: "App chỉ xử lý khi bạn chủ động gửi"
4. Body text, 16sp, centered, secondary color, max 3 lines:
   "Ứng dụng không đọc tin nhắn SMS, thông báo hay nhật ký cuộc gọi.
   Chỉ nội dung bạn dán, chia sẻ hoặc quét mới được kiểm tra."
5. A small rounded info row with a light blue background and a lock icon:
   "Quyền camera chỉ được xin khi bạn mở trình quét QR"
6. Page indicator: 4 dots, the 3rd one active and wider, primary blue.
7. Bottom: full-width filled primary button "Tiếp tục",
   and below it a centered text button "Đăng nhập".

No bottom navigation bar on this screen.
```

Nội dung 4 slide còn lại, thay vào mục 3 và 4 rồi generate tiếp nếu cần:

| Slide | Headline | Body |
|---|---|---|
| 1 | Kiểm tra trước khi bấm, trước khi chuyển | Dán link, tin nhắn, số điện thoại, số tài khoản hoặc quét mã QR để xem có dấu hiệu lừa đảo không. |
| 2 | Kết quả luôn kèm lý do | Mỗi cảnh báo đều nói rõ vì sao, dựa trên dấu hiệu nào và cộng bao nhiêu điểm rủi ro. |
| 4 | Dữ liệu của bạn do bạn quyết định | Bạn chọn có lưu lịch sử hay không, và xóa được bất cứ lúc nào. Số điện thoại và số tài khoản luôn được che bớt khi hiển thị. |

---

## 5. M02. Mobile Login

```
Same app and same theme as the previous screens.

Screen: login. Keep ALL UI text exactly in Vietnamese.

Layout, vertically centered with comfortable spacing:
1. App logo mark (a simple blue shield with a check inside) centered near the top,
   with the app name "SafeScan" below it in semibold.
2. Title, 22sp bold, centered: "Đăng nhập"
3. Outlined text field, label "Email hoặc tên đăng nhập", with a person icon.
4. Outlined password field, label "Mật khẩu", with a lock icon and a
   show/hide eye icon on the right.
5. An inline error message under the password field in red with a small warning
   icon: "Sai tên đăng nhập hoặc mật khẩu. Bạn còn 3 lần thử."
6. Full-width filled primary button: "Đăng nhập"
7. A centered row: "Chưa có tài khoản?" in secondary color followed by a
   primary-colored text link "Đăng ký".
8. Near the bottom, a small note card with a shield icon and light gray
   background: "Không chia sẻ mật khẩu hoặc mã OTP với bất kỳ ai, kể cả người
   tự xưng là nhân viên ngân hàng."
```

Dòng cảnh báo cuối và việc hiện số lần thử còn lại là chi tiết nhỏ nhưng đúng tinh thần sản phẩm chống lừa đảo: chính màn đăng nhập cũng phải dạy người dùng thói quen an toàn.

---

## 6. M04. QR Camera Scanner

```
Same app and same theme as the previous screens.

Screen: QR camera scanner, full screen. Keep ALL UI text exactly in Vietnamese.

Layout:
1. Full-bleed camera preview background: a dim, slightly blurred photo of a
   printed QR code sticker on a cafe table, seen from above.
2. A dark translucent overlay covering the whole screen EXCEPT a centered
   square cutout of about 65% screen width. The cutout has 4 white rounded
   corner brackets and a thin blue scanning line across the middle.
3. Top bar over the overlay, no background: a white X close icon on the left,
   a white flashlight icon on the right.
4. Under the cutout, centered white text, 16sp:
   "Đưa mã QR vào khung để quét"
5. Below that, smaller white text at 70% opacity:
   "Mã được giải trên máy của bạn, ảnh không được gửi lên hệ thống"
6. At the bottom, a white rounded pill button with a keyboard icon:
   "Nhập thủ công"
```

Rồi sinh thêm màn giải thích quyền, bắt buộc phải có theo mục 7 của đặc tả:

```
Same app and same theme. Screen: camera permission rationale, shown BEFORE
requesting the Android CAMERA permission. Keep ALL UI text exactly in Vietnamese.

Centered layout on the normal light background:
- A large camera icon inside a light blue circle
- Title 22sp bold: "Cần quyền camera để quét mã QR"
- Body in secondary color: "Camera chỉ hoạt động khi bạn đang mở màn hình quét
  mã. Ứng dụng không chụp ảnh, không quay video và không gửi hình ảnh lên hệ thống."
- Full-width filled primary button: "Cho phép truy cập camera"
- Outlined button below: "Nhập mã thủ công"
```

---

## 7. M05. Share Target Check

```
Same app and same theme as the previous screens.

Screen: share target sheet — shown when the user shares a link from Zalo or
Messenger into this app. It is a compact bottom sheet covering about 55% of the
screen, with rounded top corners and a drag handle, over a dimmed background.
Keep ALL UI text exactly in Vietnamese.

Layout:
1. Small header row: the app logo mark and the name "SafeScan", and an X icon
   on the right.
2. Title 18sp semibold: "Đang kiểm tra nội dung bạn vừa chia sẻ"
3. A preview card with a light gray background, a link icon, and a truncated
   URL in monospace: "https://v1etcombank-verify.tk/xac-thuc..." with a small
   label above it in secondary color: "Nội dung được chia sẻ".
4. A horizontal linear progress bar in primary blue, indeterminate style.
5. Status text in secondary color, centered: "Đang phân tích, thường mất 5-10 giây"
6. A row of two outlined chips, side by side, for when the content type is
   ambiguous, with a small label above: "Kiểm tra dưới dạng":
   "Đường link" (selected, blue) and "Văn bản"
7. A text button at the bottom, centered: "Hủy"
```

---

## 8. M07. Mobile Report Submission

```
Same app and same theme as the previous screens.

Screen: submit a community scam report, opened from a scan result so the entity
is pre-filled. Keep ALL UI text exactly in Vietnamese.

Layout:
1. Top app bar with a back arrow and the title "Gửi báo cáo".
2. A read-only pre-filled card with a light gray background, a small label
   "Đối tượng báo cáo" and the masked value in monospace with a bank icon:
   "Vietcombank · 1234****7890". A small text link on the right: "Đổi".
3. Field group "Hình thức lừa đảo" shown as selectable chips, wrap to 2 rows,
   the first one selected in primary blue:
   "Người bán giả" | "Giả mạo ngân hàng" | "Tuyển dụng giả" | "Shipper giả" |
   "Link lừa đảo" | "Khác"
4. A multiline outlined textarea, label "Mô tả ngắn chuyện đã xảy ra",
   placeholder "Ví dụ: người bán yêu cầu chuyển cọc trước rồi chặn liên lạc",
   with a character counter "0/500" at the bottom right.
5. An upload area: a dashed-border rounded box with a camera icon and text
   "Thêm ảnh chụp màn hình" and below it in secondary color "Tối đa 3 ảnh".
   Show 1 uploaded thumbnail already with a small X remove badge, and an
   upload progress bar at 60% on a second thumbnail.
6. A collapsed expandable row with a chevron: "Thêm số tiền và ngày giao dịch
   (không bắt buộc)".
7. A warning note card with an amber left border and light amber background,
   a warning icon, text: "Vui lòng che thông tin cá nhân của người khác trong
   ảnh trước khi gửi."
8. Sticky bottom: full-width filled primary button "Gửi báo cáo", and under it
   small secondary text centered: "Báo cáo sẽ được quản trị viên xem xét trước
   khi ảnh hưởng đến kết quả cảnh báo."
```

Câu cuối là điểm nhóm nên giữ: nó cho người báo cáo biết hệ thống có kiểm duyệt, đồng thời ngầm cảnh báo người định lạm dụng chức năng này để hạ uy tín người khác.

---

## 9. M08. Mobile History

```
Same app and same theme as the previous screens.

Screen: scan history list. Keep ALL UI text exactly in Vietnamese.

Layout:
1. Top app bar: title "Lịch sử", a search icon on the right.
2. A horizontally scrollable row of filter chips, first one selected:
   "Tất cả" | "Nguy hiểm" | "Link" | "Tin nhắn" | "Số ĐT" | "Số TK" | "QR"
3. A vertical list of result cards. Each card: a leading type icon in a light
   circle, the masked input value in medium weight, a second line in secondary
   color with the relative time, a colored risk pill on the right and the score
   under it. Show these 5 cards:
   - link icon, "v1etcombank-verify.tk", "2 giờ trước", red pill "NGUY HIỂM", 92
   - bank icon, "Vietcombank · 1234****7890", "Hôm qua", red pill "NGUY HIỂM", 78
   - message icon, "Thông báo tài khoản của quý khách sẽ bị...", "Hôm qua",
     amber pill "THẬN TRỌNG", 45
   - phone icon, "+849***678", "3 ngày trước", green pill "CHƯA THẤY RỦI RO", 8
   - qr icon, "Mã VietQR · 500.000đ", "5 ngày trước", amber pill "THẬN TRỌNG", 52
4. Group the list under small date section headers: "Hôm nay" and "Trước đó".
5. Bottom navigation bar, "Lịch sử" tab active.
```

Prompt phụ cho trạng thái rỗng, đặc tả yêu cầu ở mục 7:

```
Same screen but the empty state: no list, instead a centered illustration of a
magnifying glass over a shield, title "Chưa có lượt kiểm tra nào",
body in secondary color "Các link, tin nhắn và số tài khoản bạn kiểm tra sẽ
được lưu tại đây nếu bạn bật lưu lịch sử.",
and a filled primary button "Kiểm tra ngay".
```

---

## 10. M09. Privacy & Permission Settings

```
Same app and same theme as the previous screens.

Screen: privacy and permission settings. Keep ALL UI text exactly in Vietnamese.

Layout:
1. Top app bar with a back arrow and the title "Quyền riêng tư".
2. A highlighted card at the top with a light green background and a green
   shield icon, title "Ứng dụng không đọc dữ liệu ngầm" and body in secondary
   color: "SafeScan chỉ kiểm tra nội dung bạn chủ động dán, chia sẻ hoặc quét."
3. Section header "Quyền ứng dụng" then a list of rows, each with a leading
   icon, a title, a subtitle in secondary color, and a trailing status:
   - camera icon, "Camera", "Chỉ dùng khi bạn mở trình quét mã QR",
     trailing green text "Đã cấp"
   - bell icon, "Thông báo", "Báo khi kết quả kiểm tra đã xong",
     trailing gray text "Chưa cấp"
4. Section header "Ứng dụng không sử dụng" then a list of 4 rows, each with a
   gray crossed-out icon and gray text, no toggles:
   "Đọc tin nhắn SMS" | "Đọc thông báo" | "Nhật ký cuộc gọi" |
   "Dịch vụ trợ năng (Accessibility)"
5. Section header "Dữ liệu của bạn" then:
   - a row with a switch turned on: "Lưu lịch sử kiểm tra",
     subtitle "Tắt thì kết quả không được lưu lại"
   - a row with a chevron: "Xóa toàn bộ lịch sử", title in red
   - a row with a chevron: "Xóa bộ nhớ đệm trên máy"
6. Section with two plain link rows with external-link icons:
   "Chính sách quyền riêng tư" and "Tóm tắt an toàn dữ liệu"
7. Bottom navigation bar, "Cá nhân" tab active.
```

Mục 4 là phần nhóm nên giữ nguyên khi làm thật. Liệt kê những quyền app **không** dùng là cách xây niềm tin rẻ nhất mà lại hiệu quả, nhất là với một app chống lừa đảo, vì người dùng có lý do để nghi ngờ chính nó.

---

## 11. Sau khi generate xong

Vài prompt tinh chỉnh hay cần đến:

```
Increase the contrast of the secondary text, it is too light for accessibility.
```

```
Make the risk score ring larger and move the badge directly under it,
keep everything else unchanged.
```

```
Generate a dark mode version of this screen using the same layout,
background #0F172A, surface #1E293B, and keep the same three risk colors.
```

Khi hài lòng thì export và lưu vào `document/Week5/` theo cấu trúc:

```
Week5/
  Mockup_mobile/
    M01_Onboarding.png
    M02_Login.png
    M03_Manual_Input.png
    M06_Result_Danger.png
    M06_Result_Caution.png
    M06_Result_Safe.png
    ...
```

Ba biến thể của M06 nên xuất đủ cả ba, vì khi bảo vệ thầy nhiều khả năng sẽ hỏi hệ thống hiển thị gì khi **không** phát hiện rủi ro, chứ không chỉ xem màn cảnh báo đỏ.

---

## 12. Những gì Stitch sẽ không làm được

Để nhóm không mất thời gian ép nó:

- **Luồng chuyển màn.** Stitch sinh từng màn rời, không nối thành flow. Muốn có prototype bấm được thì export sang Figma rồi tự nối.
- **Camera preview thật ở M04.** Nó chỉ vẽ được ảnh nền giả lập. Bản thân điều đó là đủ cho tài liệu thiết kế.
- **Code React Native.** Xuất ra HTML/CSS. Dùng để lấy màu, khoảng cách, kích thước font, không copy trực tiếp được.
- **Nhất quán tuyệt đối giữa các màn.** Kể cả có "same theme as previous", bo góc và khoảng cách vẫn lệch nhẹ giữa các lần generate. Chuẩn hóa lại ở bước Figma.

---

*Dựa trên `Dac_ta_man_hinh_giao_dien.md` mục 5 (M01–M09), mục 7 (error/empty states) và mục 8 (mẫu nội dung hiển thị).*
