# Module Hàng hóa — Xe chưa xuất hóa đơn

`index.html`, hàm `renderChuaHD()` — tab 1 (Hàng hóa)

Đây là phần logic phức tạp nhất trong `index.html`, đáng có ghi chú riêng dù không tách file riêng:

- **Nguồn `allData.chuaHD`**: lọc từ dữ liệu tồn kho sống, điều kiện gốc `kho!=='' && soKhung!=='' && !r.xuatHoSo && r.ttHoSo && !r.fromHistory && daly in (NT,ĐL,PY)`. Field mỗi dòng: `model, phienBan, mau, kho, soKhung, daly, tvbh, giaVon, ngayBan, baoCaoDMS, nguonKhach, khach, xuatHoSo` (2 field cuối lưu tường minh dù luôn `false` trong mảng này — để bảng con phía dưới filter lại được mà không phải đoán ngầm).
- **Bảng cảnh báo "Xe bán ngang chưa xuất hóa đơn"** (đặt TRÊN bảng tổng hợp theo Model): lọc `nguonKhach.trim().toLowerCase()==='khác' && !xuatHoSo` — 2 điều kiện đều bắt buộc, viết tường minh cả 2 dù về lý thuyết `!xuatHoSo` đã đúng sẵn vì cả mảng cha đều vậy (phòng trường hợp sau này đổi nguồn dữ liệu). Có dòng thông báo "✅ Không có xe bán ngang nào bị sót" khi rỗng — đừng để trống không hiện gì.
- **Nút "📊 Xuất đặt hàng"** (`exportOrderExcel()`, header trang): xuất Excel 2 sheet (Chi tiết xe tồn + Chi tiết xe nợ) theo đúng bộ lọc Kho/Năm SX đang chọn, dùng thư viện SheetJS nạp qua CDN (`xlsx.full.min.js`) — thư viện này còn được đoạn code cũ `uploadKCK()`/xử lý file thủ công dùng chung, đừng gỡ nếu không kiểm tra kỹ các chỗ dùng khác.

## Cơ chế snapshot lịch sử ("chốt sổ" theo tuần/tháng)

Mục "Xe chưa xuất hóa đơn" cần xem lại được **trạng thái tại 1 thời điểm trong quá khứ** (chốt cuối tuần Thứ 6, chốt cuối tháng), không chỉ xem dữ liệu real-time. Cơ chế:

- `data_weekly.json` / `data_monthly.json`: mỗi file là 1 mảng `snapshots[]`, mỗi phần tử = 1 lần chốt, chỉ lưu phần **"chưa xuất hồ sơ"** đã lọc + rút gọn field (không lưu nguyên toàn bộ tồn kho) — để tránh vượt 1MB (giới hạn GitHub Contents API trả `content` base64 trực tiếp; vượt 1MB phải đọc qua `download_url`).
- Frontend (`index.html`) có 2 dropdown độc lập "Tuần"/"Tháng" — chọn cái này tự bỏ chọn cái kia (`hhSelectWeek()` / `hhSelectMonth()`), dùng chung hàm `buildChuaHDFromRecords()` để dựng lại bảng từ snapshot.
- Muốn thêm 1 loại snapshot mới (vd theo quý): nhân bản đúng pattern `weeklySnapshot`/`monthlySnapshot` (backend, trong `apps-script/TinThanh_AutoSync.gs`) + `hhSelectWeek`/`hhSelectMonth` (frontend), tái sử dụng `buildChuaHDSnapshot_()` ở backend để đảm bảo các loại snapshot luôn khớp field với nhau.
- **Snapshot Tuần/Tháng** dùng `buildChuaHDFromRecords()` để dựng lại đúng các field trên từ dữ liệu lịch sử. Nếu thêm field mới vào `allData.chuaHD`, nhớ thêm cả vào `buildChuaHDSnapshot_()` (backend `.gs`) lẫn `buildChuaHDFromRecords()` (frontend) để snapshot cũ/mới nhất quán.

## "Giả định đặt hàng" (`renderDatHang()`, thêm 09/2026)

Bảng tạm thời cho **1 đợt đặt hàng cụ thể** — chỉ liệt kê các Model+Phiên bản đang đặt thêm (không phải toàn bộ danh mục), bấm vào tile "📦 Đặt hàng" trong `mkStats()` để xem (`toggleType('dathang')`, chỉ có ở `index.html`, không có ở `mobile.html`).

**Chỉ dành riêng cho Admin (thêm 09/2026)** — khác hẳn cơ chế khoá của "Chưa xuất HD" (vẫn hiện nút kèm icon 🔒 cho người chưa đủ quyền): tile "📦 Đặt hàng" trong `mkStats()` **chỉ render khi `_isAdmin===true`**, không có nút/icon khoá nào hiện ra cho nhóm khác — chủ đích để "không ai biết có mục này" ngoài Admin. Có thêm 2 lớp chặn phòng thủ (dù bình thường không ai chạm tới được vì nút không tồn tại): `toggleType('dathang')` return sớm nếu `!_isAdmin` (không alert, không lộ thông tin), và `renderHH()` tự reset `typeFilter` về `null` nếu đang ở view này mà `_isAdmin` false (phòng trường hợp session hết hạn/đăng xuất giữa chừng). Sửa quyền xem mục này thì sửa đúng 3 chỗ đó, đừng chỉ sửa mỗi điều kiện render nút.

- **`DAT_HANG_ORDERS[]`** (hardcode ngay trên `renderKCK()`): mỗi phần tử gồm `model`/`phienBan` (PHẢI khớp đúng chuỗi thật trong dữ liệu DMS — xem `parseJsonRecords()`, ví dụ `"1.6 TURBO 2024"` chứ không phải nhãn rút gọn `"1.6 Turbo"`), `label` (nhãn hiển thị rút gọn cho người đọc), và `orders` (object theo chi nhánh `NT`/`PY`/`ĐL`, mỗi chi nhánh là mảng `{mau, sl}` — số liệu **cố định** của đúng đợt đặt hàng này, sửa tay khi có đợt mới, không tự suy ra).
- Cột **Tồn/BO/LXX/Nợ**: tính **sống** từ `allData.stock/bo/lxx/debt` lọc đúng `model`+`phienBan` — tự cập nhật theo `data.json` mới nhất, KHÔNG hardcode cứng (khác với `orders`, vốn cố định theo đợt đặt). Nợ hiển thị riêng (`+N`), KHÔNG cộng vào Tổng — giữ đúng quy ước "Nợ tách biệt, không cộng vào tổng tồn" đã dùng xuyên suốt module này (xem bảng Model ở `renderHH()`).
- Cột **Đặt hàng** = tổng `sl` của tất cả chi nhánh trong `orders` của dòng đó. Cột **Tổng** = Tồn + BO + LXX + Đặt hàng (không gồm Nợ).
- Cột **Số lượng xe bán (30 ngày gần nhất)**: tổng số xe **đã bán** (theo ngày báo bán `ngayBan`, KHÔNG phải ngày đặt cọc) trong 30 ngày gần nhất tính đến `fileLastModified`, lấy từ `allSales` lọc đúng `model`+`phienBan`. Hàm `_last30DaysCutoff()` tính mốc cắt (rolling 30 ngày, không theo tuần/tháng lịch). Đã đổi từ "chốt cọc 5 tuần" sang "đã bán 30 ngày" theo yêu cầu người dùng 09/2026 — nếu đổi lại cách tính, nhớ sửa cả tiêu đề cột lẫn dòng ghi chú `⚠️` bên dưới `sec-title`.
- Muốn thêm đợt đặt hàng mới: ghi đè hẳn `DAT_HANG_ORDERS[]` (không cộng dồn với đợt cũ, trừ khi người dùng muốn giữ lại để đối chiếu).
