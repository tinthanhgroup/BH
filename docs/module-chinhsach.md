# Module Chính sách HTV

`theo_doi_chinh_sach.html`

**KHÔNG có backend/Apps Script/Google Sheet nào** — đây là file **tĩnh hoàn toàn**, dữ liệu hardcode ngay trong mảng JS `policies` (xem "Quy trình cập nhật dữ liệu thủ công" trong CLAUDE.md gốc để biết cách thêm chính sách mới). Quyết định có chủ đích: tần suất thêm chính sách thấp (vài lần/năm), không đáng để dựng cả pipeline Sheet→Apps Script→JSON chỉ cho việc này.

**Field mỗi object trong `policies[]`:** `pdfUrl` (link Google Drive, share công khai — KHÔNG nhúng base64, từng làm khiến file nặng >6MB), `category` (dùng để lọc, hiện chỉ có `"Bán hàng - Marketing"`), `code`, `name`, `updated`, `from`/`to` (kiểu `Date`, `to: null` = còn hiệu lực vô thời hạn), `fromLabel`/`toLabel` (chuỗi hiển thị, phải tự khớp tay với `from`/`to` — không tự sinh ra từ Date), `summary` (object gồm các mục tóm tắt nội dung).

**`const TODAY = new Date();`** — PHẢI luôn là ngày thực tế lúc mở file, **TUYỆT ĐỐI không gán cứng** kiểu `new Date(2026,7,31)` (đã từng bị lỗi này — trạng thái Active/Inactive sẽ sai vĩnh viễn kể từ ngày cụ thể đó nếu gán cứng).

**Logic Active/Inactive:** `TODAY >= p.from && (p.to === null || TODAY <= p.to)`. Danh sách sort: Active lên trước Inactive, trong mỗi nhóm sort theo `from` giảm dần (mới nhất lên đầu). Số thứ tự (`stt`) đánh theo **thứ tự hiển thị sau sort**, không phải thứ tự khai báo trong mảng — tự động đổi khi 1 chính sách chuyển Active↔Inactive.

**Bộ lọc `category`** (nút bấm dạng pill) **tự ẩn** nếu tất cả policy chỉ thuộc đúng 1 category — chỉ hiện khi có ≥2 category khác nhau trong mảng, tránh giao diện rối khi chưa cần.
