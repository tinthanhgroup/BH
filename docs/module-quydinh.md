# Module Quy định nội bộ

`quy_dinh_noi_bo.html`

**KHÔNG có backend/Apps Script/Google Sheet nào** — giống hệt cách "Chính sách HTV" ([docs/module-chinhsach.md](module-chinhsach.md)) đang làm: file **tĩnh hoàn toàn**, dữ liệu hardcode ngay trong mảng JS `quyDinh`. Quyết định có chủ đích, cùng lý do với Chính sách HTV: tần suất thêm quy định thấp, nội dung cần người duyệt trước khi lên web.

## Kiến trúc 3 tầng

1. **File gốc (Word)** — lưu trên Google Drive, thư mục nội bộ do công ty quản lý (`2. Quy dinh noi bo/1. Da duyet/<năm>/`), share ở chế độ "Anyone with the link". Link từng file nằm ở field `docUrl` trong `quyDinh[]`.
2. **File A (tổng hợp)** — chính là mảng `quyDinh[]` trong file này. Mỗi phần tử có field `summary.blocks` — nội dung do **Claude đọc trực tiếp file Word gốc rồi tóm tắt lại**, người dùng duyệt lại trước khi commit/push. Đây là "nguồn dữ liệu" thật sự mà cả 2 cách tra cứu (xem danh sách + tìm từ khóa) dùng.
3. **Tương tác người dùng** — trang `quy_dinh_noi_bo.html` cho 2 cách tra cứu:
   - **Xem file gốc**: nút "Xem file gốc" trong phần chi tiết mỗi quy định, mở `docUrl` (Google Drive preview) ở tab mới.
   - **Tìm theo từ khóa**: ô search phía trên bảng, lọc client-side theo `name`/`code`/`category`/toàn bộ nội dung `summary` (hàm `flattenSearchText()`), không phân biệt hoa/thường/dấu (`stripDiacritics()` cục bộ trong file — không dùng chung với `index.html`).
   - **Tìm bằng AI**: **chưa làm** — cần 1 Apps Script Web App riêng làm proxy gọi Anthropic API (API key lưu Script Properties, không lộ ra client) vì trang là HTML tĩnh trên GitHub Pages public, không thể nhúng API key trực tiếp. Xem trao đổi kiến trúc gốc trong lịch sử chat lúc dựng module này nếu cần làm tiếp phần này.

## Field mỗi object trong `quyDinh[]`

Cấu trúc gần giống `policies[]` của Chính sách HTV nhưng khác tên field và phần tóm tắt linh hoạt hơn (nhiều mục, có bảng nhiều dòng thay vì chỉ 1 dòng):

- `docUrl`: link Google Drive file Word gốc (dạng `https://drive.google.com/file/d/<ID>/view`).
- `category`: dùng để lọc + hiển thị cột "Phân loại". Hiện có 4 nhóm: `"Tài chính - Chi phí"`, `"Bán hàng - Lương thưởng"`, `"Bán hàng - MKT"`, `"Dịch vụ"` — thêm nhóm mới nếu quy định không khớp nhóm nào có sẵn, không gượng ép vào nhóm gần đúng.
- `code`: số hiệu văn bản nếu có (nhiều file quy định gốc bị bỏ trống "Số:" — khi đó để `code: ""`, **không tự bịa số**).
- `name`, `updated` (chuỗi `"dd/MM/yyyy"`, dùng `parseVNDate()` để sort).
- `from`/`to` (kiểu `Date`, `to: null` = còn hiệu lực vô thời hạn) + `fromLabel`/`toLabel` (chuỗi hiển thị, tự khớp tay với `from`/`to`) — **dùng lại đúng logic Active/Inactive của Chính sách HTV**: `TODAY >= from && (to === null || TODAY <= to)`.
- `summary.blocks`: mảng các khối nội dung, mỗi khối `{title, items?: string[], table?: {head: string[], rows: string[][]}}` — khác với Chính sách HTV (chỉ có 2 khối cố định `quy`/`thang`), ở đây số khối tùy theo nội dung từng quy định, và `table.rows` hỗ trợ **nhiều dòng dữ liệu** (không chỉ 1 dòng như hàm `renderTable` gốc bên Chính sách HTV).
- `summary.note`: ghi chú cuối — dùng cả cho lưu ý nghiệp vụ lẫn **cảnh báo khi nội dung trích xuất từ Word bị mơ hồ/thiếu dữ liệu** (ví dụ bảng bị lệch cột khi convert từ XML, merged-cell bị mất khi bóc tag). Từng gặp ở quy định "22. Thu hồi công nợ bảo hiểm": bảng ngưỡng miễn phí ban đầu thiếu 1 giá trị do ô gộp (Đà Lạt + Bảo Lộc dùng chung 1 ngưỡng vì thực chất là 1 đại lý, 2 xưởng dịch vụ — quy XML flatten không nhân đôi giá trị ô gộp) — ban đầu để trống chờ người dùng xác nhận, sau khi hỏi lại mới điền đúng số. Nguyên tắc: khi không chắc, **không bịa số**, để trống/ghi chú yêu cầu xác nhận thay vì đoán.

## Quy trình cập nhật khi có quy định mới

1. Người dùng tải file Word quy định mới về máy (hoặc share thư mục Google Drive).
2. Gửi đường dẫn file/thư mục cho Claude Code (phiên làm việc trong repo này).
3. Claude đọc file Word — do Claude Code **không đọc trực tiếp được file `.docx` (binary)**, quy trình trích xuất text đã dùng là: PowerShell mở `.docx` như file ZIP (`System.IO.Compression.ZipFile`), đọc `word/document.xml`, dùng regex thay `</w:p>` bằng xuống dòng rồi bóc hết tag XML còn lại. Không cần cài thêm phần mềm gì (Python/pandoc không có sẵn trong môi trường này).
4. Claude thêm object mới vào mảng `quyDinh[]` (đủ field như trên) — **người dùng xem lại trước khi commit/push**, đặc biệt các chỗ Claude đánh dấu không chắc chắn trong `summary.note`.
5. Nếu file gốc lấy từ Google Drive: cần lấy đúng `docUrl` dạng `/file/d/<ID>/view` — ID lấy qua `https://drive.google.com/embeddedfolderview?id=<FOLDER_ID>#list` (WebFetch đọc được trang này dù connector Drive chưa cấp quyền, vì đây là trang public không cần đăng nhập nếu folder đã share link) — **luôn verify lại từng ID** bằng cách WebFetch thử `/file/d/<ID>/view` và kiểm tra tên file trả về đúng khớp trước khi dùng chính thức, vì ID do model đọc gián tiếp có thể sai lệch.

**Không tự động hoàn toàn** (Drive → tự publish lên web), cùng lý do với Chính sách HTV: nội dung quy chế công ty cần người duyệt trước khi lên web.
