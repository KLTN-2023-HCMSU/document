# Bản thảo báo cáo W13 — Kiến trúc Web Client, Async AI Worker và UI/UX

**Trạng thái:** Bản thảo học thuật cập nhật 29/10/2026; mô tả theo ứng viên code W12/W13. Đây là nội dung phục vụ chương Thiết kế hệ thống, chưa phải xác nhận nghiệm thu v0.1.0. Nguồn kiến trúc duy trì duy nhất ở [Technical Architecture](https://github.com/KLTN-2023-HCMSU/Scam-Risk-Detector/blob/0fe194976260bba5b7d82553ee0d51a5b644fa7c/docs/technical-architecture.md); yêu cầu ở [Requirements](https://github.com/KLTN-2023-HCMSU/Scam-Risk-Detector/blob/0fe194976260bba5b7d82553ee0d51a5b644fa7c/docs/thesis/02_Requirements_Specification.md), quy tắc nghiệp vụ ở [Business Rules](https://github.com/KLTN-2023-HCMSU/Scam-Risk-Detector/blob/0fe194976260bba5b7d82553ee0d51a5b644fa7c/docs/business-rules.md) và palette ở [Design System](https://github.com/KLTN-2023-HCMSU/Scam-Risk-Detector/blob/0fe194976260bba5b7d82553ee0d51a5b644fa7c/docs/design-system.md). Trích dẫn nguyên file spec/contract khi hoàn chỉnh báo cáo học thuật.

## 1. Kiến trúc ứng dụng Web Client

Ứng dụng khách được tổ chức dưới dạng dự án **Next.js App Router**, sử dụng **React, TypeScript và Tailwind CSS**. Giao diện đáp ứng các hành trình nhập dữ liệu, theo dõi trạng thái quét, xem kết quả, truy xuất lịch sử và in báo cáo. Thành phần trong `apps/web/src/app` định tuyến; các form thực thi kiểm tra đầu vào và gọi client dùng chung; các cấu phần `components/risk` chỉ đảm trách biểu diễn dữ liệu của RiskResult. Mô-đun `shared/risk` giữ logic kiểm tra và chuẩn hóa dữ liệu ở TypeScript thuần, tránh phụ thuộc vào React và trình duyệt để có thể dùng lại khi mở rộng thiết bị.

Web Client không tự đánh giá rủi ro và không suy diễn điểm từ màu sắc hoặc chỉ số evidence. Kết quả cuối của lượt quét đến từ Java Core dưới dạng canonical risk result. Trước khi render, client kiểm tra runtime các thuộc tính quan trọng như `status=COMPLETED`, `riskScore`, `riskLevel`, `degraded`, `scanId`, phiên bản chính sách và tập bằng chứng. Nếu payload thiếu hoặc sai, giao diện thông báo kết quả không hợp lệ; không gán kết quả SAFE theo mặc định. Thiết kế này giảm nguy cơ tạo kết luận sai khi có sai lệch contract hoặc response lỗi.

Các màn URL/Text/QR phân tách dữ liệu nhập của người dùng và phản hồi của dịch vụ. Text Scan giới hạn số ký tự Unicode, cảnh báo nội dung nhạy cảm và chỉ gửi sau thao tác rõ ràng. Đối với QR, ảnh được giải mã ở trình duyệt; dữ liệu URL hoặc VietQR vừa giải mã mới được gửi khi người dùng đồng ý. URL không tự mở, hạn chế rủi ro kích hoạt liên kết độc hại. Với trường hợp có đăng nhập, thành phần phiên dùng access token do lớp xác thực cung cấp; xử lý refresh/session thuộc module auth, không lưu khóa API của worker trong Web.

Luồng bất đồng bộ thể hiện bằng trạng thái đang quét, lỗi xử lý, mất khả dụng, không đủ dữ liệu và kết quả đã hoàn tất. Tại màn hình lịch sử, client gọi API với bộ lọc `scanType`/`riskLevel` và phân trang; dữ liệu trả về phải hợp lệ trước khi sử dụng. Giao diện không giữ hiển thị kết quả trang hoặc phiên trước trong thời gian tải response cho bộ lọc/người dùng mới. Các trạng thái loading và lỗi có ngữ nghĩa ARIA để không làm mất khả năng tiếp cận của người dùng sử dụng thiết bị hỗ trợ.

## 2. Tích hợp Async AI Worker

Trong kiến trúc V3.3, **Java Core** chịu trách nhiệm quản lý vòng đời lượt quét, trạng thái nghiệp vụ và tính toán Risk Fusion. Công việc phân tích văn bản được đưa qua RabbitMQ đến Text Worker. Text Worker thực hiện chuẩn hóa và nhận diện tín hiệu/dữ liệu nghi vấn rồi phát sự kiện kết quả; một phần phân tích ngữ nghĩa có thể được giao cho AI Worker Python. AI Worker nhận tác vụ bất đồng bộ, qua adapter tương ứng gọi model provider khi được cấu hình, kiểm tra JSON phản hồi, sau đó xuất `AiPrediction` theo event contract. Worker không quyết định mức nguy cơ cuối cùng và không truy cập trực tiếp PostgreSQL của Core.

Cơ chế Completion Barrier tại Core thu thập kết quả cần thiết, phân biệt các tín hiệu có mặt và bị thiếu, đồng thời áp dụng chính sách Risk Fusion phiên bản hóa. Trường hợp AI lỗi, quá hạn hoặc chưa được cấu hình, xử lý còn lại không chờ vô hạn. Core có thể đưa ra kết quả dựa trên nguồn đang có cùng cờ `degraded`, hoặc trạng thái lỗi/không đủ dữ liệu tùy phạm vi tín hiệu và nghiệp vụ. Kết quả được xác định bởi Core; Web chỉ hiển thị trung thực `riskLevel`, evidence, policy version và cảnh báo degraded. Chính sách hiện sử dụng cấu hình ứng viên V3.3 `document-v3.3-mvp-1` cho local QA; lựa chọn production cần được phê duyệt và kiểm thử riêng.

Ranh giới bất đồng bộ còn giúp cô lập lỗi nhà cung cấp AI khỏi request HTTP và giảm sự phụ thuộc giữa giao diện và nhà cung cấp mô hình. Tuy nhiên, việc có queue, timeout và unit test chưa chứng minh độ ổn định production: cần đo thời gian xử lý, redelivery/dedup, mất kết nối broker, tỷ lệ degraded, an toàn log và tính đúng của kết quả sau khôi phục trên staging. Những tiêu chí này cần bằng chứng gắn với commit và môi trường đã kiểm thử.

## 3. Thiết kế trải nghiệm người dùng và hiển thị an toàn

Cách trình bày rủi ro dựa trên ba màu có ý nghĩa: **SAFE** với emerald/xanh, **CAUTION** với amber/vàng và **DANGER** với rose/đỏ. Biểu tượng, chữ mô tả và điểm số luôn đi cùng màu để không phụ thuộc vào khả năng phân biệt màu. `INSUFFICIENT_DATA` hoặc thiếu tín hiệu không tự chuyển thành SAFE. Giao diện Result Detail trình bày mã lượt quét, cảnh báo tình trạng dữ liệu/giả lập, điểm số, giải thích, khuyến nghị, evidence và metadata. Trên màn hình hẹp, card co giãn và chuỗi URL/mã dài được xuống dòng; bảng lịch sử cho phép cuộn ngang cục bộ nhằm bảo toàn thông tin và khả năng bấm vào kết quả.

Dữ liệu nhận từ API có thể chứa nội dung giả mạo, mã HTML/SVG hoặc văn bản kỹ thuật giống JavaScript. Ứng dụng sử dụng React text binding để escape nội dung ở evidence/explanations/recommendations, không sử dụng `dangerouslySetInnerHTML` cho dữ liệu không tin cậy. Khi render URL đã giải mã từ QR, client giữ chúng ở dạng văn bản, không biến thành hyperlink có thể bấm. Kiểm thử bảo mật phải kết hợp bộ trường hợp XSS ở test component, kiểm tra DOM đã render và kiểm tra trình duyệt thật; khẳng định an toàn tuyệt đối không thể chỉ dựa vào một lớp kiểm thử.

Kết quả có trạng thái **degraded** được trình bày như dữ liệu chưa đầy đủ, không phải kết luận an toàn có xác suất thấp. Khi mạng/API lỗi, client thông báo trạng thái không khả dụng hoặc lỗi thân thiện và không tiết lộ lỗi kỹ thuật thô chứa dữ liệu người dùng. Thao tác quét chủ động; không đọc tin nhắn/clipboard hoặc notification ngầm. Dữ liệu ngân hàng dùng trong demo có nhãn `SYNTHETIC_DEMO` và cảnh báo chưa có xác minh từ nhà cung cấp thực.

## 4. Lịch sử, xuất PDF và khả năng đối chiếu

R03 cung cấp danh sách lịch sử dựa trên API `GET /v1/scans/history`, hỗ trợ lọc và phân trang. Phân quyền lịch sử thuộc Java Core; kiểm tra ẩn dữ liệu cũ trên Web khi đổi phiên chỉ là lớp bảo vệ bổ sung. R04 dùng bản báo cáo HTML dành cho chế độ in, với CSS `@page A4`; người dùng chọn in hoặc Save as PDF tại browser. Tài liệu có thời điểm hoàn tất, scan ID, score/level, trạng thái degraded, evidence, giải thích, khuyến nghị, policy version và SHA-256 cục bộ. Mã SHA-256 chỉ giúp so sánh nội dung trên client, **không chứng minh máy chủ phát hành**.

Khi user có phiên hợp lệ, client yêu cầu mã xác thực trực tuyến từ Core (`/v1/scans/{scanId}/report-proof`) và hiển thị mã với điều kiện định dạng và scan ID đúng. Người nhận đối chiếu tại trang `/scans/verify` và phải so sánh chi tiết có ý nghĩa với máy chủ; thiếu hoặc sai proof phải hiển thị là chưa xác thực. Điều này không tương đương chữ ký PDF đạt chuẩn pháp lý. Báo cáo ngân hàng demo có watermark nhằm tránh nhầm với dữ liệu ngân hàng thật.

## 5. Phương pháp kiểm thử và giới hạn

W13 sử dụng test component cho năm nhóm kết quả URL/TEXT/PHONE/BANK/QR, trường hợp chuỗi dài, code injection ở UI Detail/Report, proof không hợp lệ, trạng thái tải/lỗi và cách ly dữ liệu lịch sử khi đổi phiên. Phần tương thích in A4 và mức độ lệch trang cần xuất PDF từ browser thật rồi soát bằng trình đọc PDF trên ít nhất ba engine Chrome/Firefox/Safari; HTML fixture ở jsdom không thay thế kiểm tra pagination, font, lề, watermark. Cần ghi lại kích thước viewport desktop/mobile, video/screenshot, console errors và SHA commit.

Với các thành phần có mạng/broker, test unit hoặc local mô phỏng không chứng minh S02/S05/E-AI đã đạt G4. Đối với phân công W13, kiểm thử chéo của Kiên có thể thực hiện trên môi trường local được ghi rõ; VPS/staging dùng chung không phải điều kiện bắt buộc. Kiểm chứng provider production và nghiệm thu G4 của các luồng nghiệp vụ là phạm vi riêng, không được nhận từ kết quả audit giao diện. Phần kết quả thực nghiệm trong báo cáo phải được cập nhật từ log nghiệm thu sau buổi thử thật, không tự điền số liệu latency hay tỷ lệ phát hiện.

## 6. Đối chiếu bản triển khai phục vụ W13

Trang kết quả có skeleton trung tính khi đang chờ; hình khối trang trí ẩn khỏi accessibility tree, trạng thái chờ có aria-busy và hiệu ứng chỉ chạy khi người dùng không yêu cầu giảm chuyển động. Không hiển thị điểm hoặc nhãn SAFE giả khi đang tải. Khi lỗi hoặc có kết quả, skeleton được thay bằng trạng thái tương ứng.

Bằng chứng PDF ba trình duyệt, PHONE và kiểm thử HTTPS local được duy trì tại [hồ sơ W13](https://github.com/KLTN-2023-HCMSU/Scam-Risk-Detector/blob/0fe194976260bba5b7d82553ee0d51a5b644fa7c/docs/specs/T-W13_ui_xss_pdf_demo.md). Async AI đã có luồng qua RabbitMQ trong code và bài QA riêng; overlay Docker HTTPS hiện cấu hình Text Worker rule-only khi thiếu credential provider. Bản thảo này không coi sơ đồ kiến trúc là bằng chứng đang sử dụng AI thật trong buổi demo. Tổng duyệt với người trình bày và Kiên ký kiểm thử chéo còn cần thực hiện trực tiếp.


Bản sao đọc trong repo document; nguồn ghim tại https://github.com/KLTN-2023-HCMSU/Scam-Risk-Detector/blob/0fe194976260bba5b7d82553ee0d51a5b644fa7c/docs/thesis/W13_Web_Client_Async_AI_UIUX.md . Chưa phải bản luận văn được giảng viên nghiệm thu.
