# Module Chính sách HTV

`theo_doi_chinh_sach.html`

**KHÔNG có backend/Apps Script/Google Sheet nào** — đây là file **tĩnh hoàn toàn**, dữ liệu hardcode ngay trong mảng JS `policies` (xem "Quy trình cập nhật dữ liệu thủ công" trong CLAUDE.md gốc để biết cách thêm chính sách mới). Quyết định có chủ đích: tần suất thêm chính sách thấp (vài lần/năm), không đáng để dựng cả pipeline Sheet→Apps Script→JSON chỉ cho việc này.

**Field mỗi object trong `policies[]`:** `pdfUrl` (link Google Drive, share công khai — KHÔNG nhúng base64, từng làm khiến file nặng >6MB), `category` (dùng để lọc, hiện chỉ có `"Bán hàng - Marketing"`), `code`, `name`, `updated`, `from`/`to` (kiểu `Date`, `to: null` = còn hiệu lực vô thời hạn), `fromLabel`/`toLabel` (chuỗi hiển thị, phải tự khớp tay với `from`/`to` — không tự sinh ra từ Date), `summary` (object gồm các mục tóm tắt nội dung).

**`const TODAY = new Date();`** — PHẢI luôn là ngày thực tế lúc mở file, **TUYỆT ĐỐI không gán cứng** kiểu `new Date(2026,7,31)` (đã từng bị lỗi này — trạng thái Active/Inactive sẽ sai vĩnh viễn kể từ ngày cụ thể đó nếu gán cứng).

**Logic Active/Inactive:** `TODAY >= p.from && (p.to === null || TODAY <= p.to)`. Danh sách sort: Active lên trước Inactive, trong mỗi nhóm sort theo `from` giảm dần (mới nhất lên đầu). Số thứ tự (`stt`) đánh theo **thứ tự hiển thị sau sort**, không phải thứ tự khai báo trong mảng — tự động đổi khi 1 chính sách chuyển Active↔Inactive.

**Bộ lọc `category`** (nút bấm dạng pill) **tự ẩn** nếu tất cả policy chỉ thuộc đúng 1 category — chỉ hiện khi có ≥2 category khác nhau trong mảng, tránh giao diện rối khi chưa cần.

## Quy trình cập nhật khi có chính sách mới

1. Người dùng nhận email chính sách mới → mở **Claude.ai (trình duyệt)**, dùng **Gmail connector** để Claude tìm/đọc email đó và tóm tắt nội dung chính (thời hạn áp dụng, điểm mới, ghi chú...). Connector tìm kiếm theo yêu cầu cụ thể (giống search Gmail), không quét toàn bộ hộp thư mỗi lần — nên hỏi cụ thể (người gửi/từ khóa/khoảng ngày) để đỡ tốn context nếu email dài.
2. Người dùng tự tải file PDF gốc lên **Google Drive**, đặt chia sẻ công khai, lấy link.
3. Người dùng gửi cho Claude Code (phiên làm việc trong repo này): phần tóm tắt + link Drive.
4. Claude Code thêm object mới vào mảng `policies[]` (gồm `pdfUrl`, `category`, `code`, `name`, `updated`, `from`/`to`, `fromLabel`/`toLabel`, `summary`) — người dùng xem lại trước khi commit/push.

**Không tự động hoàn toàn** (email → tự publish lên web) một cách cố ý — nội dung chính sách/pháp lý cần người duyệt trước khi lên web, tránh rủi ro AI tóm tắt sai mà không ai kiểm tra lại.
