# Module Nhà cung cấp CSVC

`TinThanh_NhaCungCap_Sync.gs` → `nhacungcap.json` → `nhacungcap.html`

Danh bạ đầu mối liên hệ nhà cung cấp/thợ sửa chữa cho việc bảo trì cơ sở vật chất (CSVC) — điện, nước, xây dựng, sắt/nhôm kính, phòng cháy chữa cháy... Nhúng iframe trong sub-tab thứ 3 "3. Nhà cung cấp CSVC" của tab 4 "HCNS" (tên nội bộ vẫn là Nhân sự & Chấm công, đổi nhãn hiển thị 09/2026) trong `index.html`, xem [CLAUDE.md](../CLAUDE.md).

## Nguồn dữ liệu

Google Sheet do người dùng (quản lý chi nhánh) tự cập nhật tay, không qua form nào: `https://docs.google.com/spreadsheets/d/1ZfA6SQRHdR92Qur0-WE3QgpCi73oSjNcWnea3f_MELE`.

**Mỗi TAB trong Sheet = 1 chi nhánh.** Hiện có 2 tab: **"Nha Trang"** (17 đầu mối) và **"Phú Yên"** (25 đầu mối, thêm 09/2026) — tính tới 09/2026. Script (`nhaCungCapSync()`) tự đọc **hết tất cả các tab** trong file — thêm tab chi nhánh mới (VD "Đà Lạt") thì lần sync sau tự nhận diện, gán `chiNhanh` = đúng tên tab, không cần sửa code. `nhacungcap.html` cũng tự động hiện bộ lọc Chi nhánh ngay khi có ≥2 chi nhánh trong dữ liệu (xem mục `nhacungcap.html` bên dưới) — đã verify khi thêm tab Phú Yên, không cần sửa gì thêm.

Mỗi tab: hàng 1 là tiêu đề gộp merge-cell (VD "Đầu mối CSVC Tín Thanh Nha Trang"), header thật nằm ở hàng chứa đúng cột "Hạng mục" — `detectNccHeaderRow_()` tự dò trong 10 hàng đầu, so khớp qua `stripDiacritics_NCC_()` (bỏ dấu/hạ chữ thường), không phụ thuộc số hàng ghi chú phía trên (giống pattern `detectCrmHeaderRow_()` ở CRM).

Cột cần có (tên chứa các từ sau, không phân biệt hoa/thường/dấu): **Nhóm, Hạng mục, Liên hệ, SĐT, Công ty, Ghi chú.**

## Field mỗi object trong `records.nhaCungCap[]`

`chiNhanh` (= tên tab Sheet), `nhom`, `hangMuc`, `lienHe`, `sdt`, `congTy`, `ghiChu`. Không có ID/STT riêng — dòng trống hoàn toàn (cả 4 cột Hạng mục/Liên hệ/SĐT/Công ty đều rỗng) bị bỏ qua khi sync.

## `nhacungcap.html`

Trang lookup đơn giản (không phải dashboard nhiều panel như nhansu/chamcong/crm — không dùng `.chart-num`), theo mẫu style gần với `theo_doi_chinh_sach.html`/`quy_dinh_noi_bo.html`: `--navy:#1B4F8C`, `.wrap` 1280px, `h1` 24px/800, `tr:hover #F7F9FC`, Inter font. Gồm:

- Bộ lọc **Chi nhánh** (`#chinhanhBar`) — **tự ẩn nếu chỉ có 1 chi nhánh** (hiện tại đúng vậy, chỉ có Nha Trang) để không thừa UI; sẽ tự hiện khi Sheet có ≥2 tab.
- Bộ lọc **Nhóm** (`#nhomBar`) — badge tự sinh theo danh sách `nhom` thực có trong dữ liệu, không hardcode.
- Ô tìm kiếm tự do (khớp Hạng mục/Liên hệ/SĐT/Công ty/Ghi chú, bỏ dấu).
- 1 bảng phẳng duy nhất (không group theo Nhóm bằng section riêng — chỉ hiện cột "Nhóm" dạng badge màu xanh lá `.nhom-badge` trong bảng), cột SĐT là link `tel:` bấm gọi được trên di động.

## Trigger

`nhaCungCapSync`: **1 trigger duy nhất `everyDays(7)` (1 lần/tuần, khoảng 6h sáng)** — dùng hàm có sẵn `setupNhaCungCapTrigger()`/`removeNhaCungCapTrigger()`, không tự viết `ScriptApp.newTrigger()` tay (giống pattern CRM/AutoSync). Có thể bấm ▶ `nhaCungCapSync()` bất cứ lúc nào để đồng bộ ngay (VD ngay sau khi vừa sửa Sheet), không cần đợi lịch tuần. Nhắc kiểm tra trang ⏰ Triggers trước khi cài (hạn mức ~20 trigger/project tính chung với các file `.gs` khác).

## Bootstrap dữ liệu ban đầu

`nhacungcap.json` lần đầu được Claude tạo thủ công (đọc trực tiếp CSV export công khai của Sheet, không qua Apps Script) để có dữ liệu ngay khi tính năng ra mắt — **không đại diện cho lần sync tự động đầu tiên**. Lần `nhaCungCapSync()` chạy thật đầu tiên (sau khi người dùng dán `.gs` vào script.google.com và chạy tay) sẽ ghi đè lại theo đúng format chuẩn từ script.
