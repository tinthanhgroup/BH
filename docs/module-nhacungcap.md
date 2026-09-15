# Module Nhà cung cấp CSVC

`nhacungcap.html` — dữ liệu hardcode ngay trong file, **không còn qua Google Sheet/Apps Script/JSON** (đổi phương án 09/2026, xem lịch sử bên dưới).

Danh bạ đầu mối liên hệ nhà cung cấp/thợ sửa chữa cho việc bảo trì cơ sở vật chất (CSVC) — điện, nước, xây dựng, sắt/nhôm kính, phòng cháy chữa cháy... Nhúng iframe trong sub-tab thứ 3 "3. Nhà cung cấp CSVC" của tab 4 "HCNS" (tên nội bộ vẫn là Nhân sự & Chấm công, đổi nhãn hiển thị 09/2026) trong `index.html`, xem [CLAUDE.md](../CLAUDE.md).

## Kiến trúc dữ liệu (đổi phương án 09/2026)

Dữ liệu **rất ít khi thay đổi** (chỉ đầu mối liên hệ, không phải số liệu vận hành) nên đã bỏ hẳn pipeline Sheet → Apps Script → JSON, chuyển sang hardcode trực tiếp trong `nhacungcap.html` — giống hệt cơ chế `policies[]` của `theo_doi_chinh_sach.html` / `quyDinh[]` của `quy_dinh_noi_bo.html`. Lý do: đại lý báo dữ liệu chỉ **1 lần/6 tháng**, không đáng để duy trì Sheet + trigger + quyền chỉnh sửa cho nhiều người.

**Field mỗi object trong mảng `NCC_DATA[]`:** `chiNhanh`, `nhom`, `hangMuc`, `lienHe`, `sdt`, `congTy`, `ghiChu` — giữ nguyên đúng 7 field như bản Sheet/JSON cũ để không phải sửa lại phần render (`render()`, `renderChinhanhBar()`).

**`NCC_UPDATED`** (hằng số ngay phía trên `NCC_DATA[]`): ngày tổng hợp gần nhất, dạng chuỗi `"dd/MM/yyyy"` — **chỉ ngày, không có giờ** (khác hẳn `updated_vn` kiểu cũ từng có giờ:phút). Dùng để tính cảnh báo quá hạn, xem mục riêng bên dưới.

## Quy trình cập nhật mỗi ~6 tháng/lần

1. Các chi nhánh tự gửi thông tin đầu mối (Zalo/email/file...) cho quản lý — **không còn sửa trực tiếp trên Google Sheet nữa**.
2. Quản lý gom tất cả file nhận được vào **1 folder trên máy tính**, báo đường dẫn folder đó cho Claude Code.
3. Claude Code đọc toàn bộ file trong folder, tổng hợp thành mảng `NCC_DATA[]` mới (giữ đúng 7 field ở trên) + cập nhật `NCC_UPDATED` thành ngày hôm đó.
4. Người dùng xem lại nội dung tổng hợp, xác nhận đúng.
5. Sau khi được duyệt, Claude Code mới `git commit`/`push` (luôn hỏi xác nhận trước khi push, theo quy tắc chung ở CLAUDE.md gốc).

**Không tự động hoàn toàn** một cách cố ý (giống chính sách HTV/quy định nội bộ) — dữ liệu liên hệ cần người duyệt lại trước khi lên web, tránh rủi ro đọc nhầm số điện thoại/tên người liên hệ mà không ai kiểm tra lại.

## Cảnh báo "quá hạn cập nhật"

`NCC_STALE_MONTHS = 6` (hardcode ngay cạnh `NCC_UPDATED`). Khi mở trang, so sánh ngày hiện tại với `NCC_UPDATED + 6 tháng`:
- Nếu đã quá hạn: hiện banner cảnh báo đỏ nhạt phía trên bộ lọc (`#staleBanner`) + đổi màu `.updated-tag` sang tông đỏ cảnh báo (class `.stale`).
- Nếu chưa quá hạn: ẩn banner, `.updated-tag` giữ màu xanh dương mặc định.

Logic nằm trong `initPage()` (chạy ngay khi trang load, không còn `fetch` bất đồng bộ như trước) — dùng `parseVNDate()`/`addMonths()` tự viết trong file, không phụ thuộc thư viện ngoài.

## `nhacungcap.html` — giao diện

Trang lookup đơn giản (không phải dashboard nhiều panel như nhansu/chamcong/crm — không dùng `.chart-num`), theo mẫu style gần với `theo_doi_chinh_sach.html`/`quy_dinh_noi_bo.html`: `--navy:#1B4F8C`, `.wrap` 1280px, `h1` 24px/800, `tr:hover #F7F9FC`, Inter font. Gồm:

- Banner cảnh báo quá hạn cập nhật (`#staleBanner`, xem mục trên) — tự ẩn khi chưa quá hạn.
- Bộ lọc **Chi nhánh** (`#chinhanhBar`) — **tự ẩn nếu chỉ có 1 chi nhánh** để không thừa UI; tự hiện khi dữ liệu có ≥2 chi nhánh (hiện có Nha Trang, Phú Yên).
- Bộ lọc **Nhóm** — badge tự sinh theo danh sách `nhom` thực có trong `NCC_DATA`, không hardcode danh sách nhóm.
- Ô tìm kiếm tự do (khớp Hạng mục/Liên hệ/SĐT/Công ty/Ghi chú, bỏ dấu).
- 1 bảng phẳng duy nhất (không group theo Nhóm bằng section riêng — chỉ hiện cột "Nhóm" dạng badge màu xanh lá `.nhom-badge`, gộp ô theo `rowspan` cho các dòng liền kề cùng Chi nhánh+Nhóm), cột SĐT là link `tel:` bấm gọi được trên di động.

## Lịch sử (bối cảnh, không còn áp dụng)

Trước 09/2026: dữ liệu đến từ Google Sheet "Đầu mối CSVC" (mỗi tab = 1 chi nhánh, người dùng tự sửa tay) → Apps Script `TinThanh_NhaCungCap_Sync.gs` (trigger `nhaCungCapSync`, `everyDays(7)`) → ghi vào `nhacungcap.json` → `nhacungcap.html` fetch JSON đó từ GitHub raw. Đã bỏ hoàn toàn 09/2026: `nhacungcap.json` đã xoá khỏi repo, trigger `nhaCungCapSync` cần **tự vào script.google.com xoá tay** (gọi `removeNhaCungCapTrigger()` có sẵn trong file `.gs`) vì Claude Code không có quyền thao tác trực tiếp trên Apps Script đang chạy thật. File `TinThanh_NhaCungCap_Sync.gs` trong `apps-script/` (local, gitignored) vẫn được giữ lại để tham khảo lịch sử, không còn được dùng.
