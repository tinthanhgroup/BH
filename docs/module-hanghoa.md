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
