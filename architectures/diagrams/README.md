# Sơ đồ kiến trúc — bản đơn giản

Bộ sơ đồ thay cho ~12 sơ đồ Mermaid dày đặc trong `architecture_v3.md`.
Mỗi sơ đồ trả lời đúng một câu hỏi và giữ dưới ~12 khối.

| # | File | Trả lời câu hỏi | Dùng ở đâu |
| --- | --- | --- | --- |
| 1 | `01-boi-canh` | Ai dùng hệ thống, hệ thống trao đổi với bên ngoài nào | Slide mở đầu |
| 2 | `02-tong-the` | Hệ thống gồm những khối chạy nào | Slide kiến trúc chính |
| 3 | `03-luong-scan` | Một lần quét đi qua những bước gì | Slide demo luồng |
| 4 | `04-cham-diem` | Điểm rủi ro ở đâu ra | Slide thuật toán / AI |
| 5 | `05-du-lieu-lua-dao` | Dữ liệu lừa đảo nạp và tra cứu thế nào | Slide dữ liệu |

Mỗi sơ đồ có hai định dạng:

- `.svg` — bản gốc, sửa trực tiếp bằng text editor hoặc Figma/Illustrator.
- `.png` — xuất sẵn ở độ phân giải 2x, dán thẳng vào slide/Word.

## Sửa và xuất lại PNG

Sửa file `.svg`, rồi chạy lại từ thư mục này:

```bash
CH="/c/Program Files/Google/Chrome/Application/chrome.exe"
for f in 01-boi-canh:960:390 02-tong-the:1120:880 03-luong-scan:1200:440 \
         04-cham-diem:1040:520 05-du-lieu-lua-dao:1100:440; do
  n=${f%%:*}; rest=${f#*:}; w=${rest%%:*}; h=${rest#*:}
  "$CH" --headless --disable-gpu --hide-scrollbars --force-device-scale-factor=2 \
        --default-background-color=ffffffff --window-size=$w,$h \
        --screenshot="$(pwd -W)/$n.png" "$(pwd -W)/$n.svg"
done
```

Kích thước trong vòng lặp phải khớp `width`/`height` của từng file SVG.

## Quy ước màu

| Màu | Ý nghĩa |
| --- | --- |
| Xanh dương | Client — người dùng tương tác trực tiếp |
| Tím | Lõi nghiệp vụ Spring Boot, nơi ra quyết định |
| Cam | Hàng đợi và luồng dữ liệu chạy nền |
| Xanh nhạt | Worker phân tích |
| Xám | Hạ tầng lưu trữ |
| Xanh lá | Kết quả trả về người dùng |
