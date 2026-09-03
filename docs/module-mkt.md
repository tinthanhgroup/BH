# Module MKT Thương hiệu

`TinThanh_MKT_Sync.gs` → `mkt.json` → `mkt.html`

**Nguồn:** 1 sheet **dùng chung** ("Nhật ký đăng bài") cho 3 chi nhánh Nha Trang/Phú Yên/Đà Lạt (⚠️ **chỉ 3 chi nhánh — KHÁC danh sách 5 kho hàng hóa** NT/ĐL/PY/PR/BL, xem "khái niệm hay nhầm" trong CLAUDE.md gốc). Mỗi dòng = 1 bài đăng ký kế hoạch, KHÔNG có sheet chỉ tiêu riêng: **chỉ tiêu = số dòng đăng ký, thực hiện = số dòng có Trạng thái chứa "đăng"** (chuẩn hoá bỏ dấu, không phân biệt hoa/thường qua `stripDiacritics_MKT_()` — ⚠️ hàm này có bug lịch sử: thiếu bước đổi `đ`→`d` khiến "đăng" không bao giờ khớp "dang", đã vá, nhớ giữ đủ bước `.replace(/đ/g,'d').replace(/Đ/g,'d')` nếu viết lại hàm tương tự ở đâu khác).

**Header sheet dò tự động** (quét 10 hàng đầu tìm cột tên đúng "Chi nhánh") — không phụ thuộc số hàng ghi chú phía trên. Cột: *STT, Chi nhánh, Tuần, Từ ngày, Đến ngày, Mã HM, Nhóm (Thương hiệu/HTV/Sản phẩm...), Hạng mục, Ngày đăng thực tế, Trạng thái, Link bài đăng*.

**Mỗi object trong `records.posts[]`**: `stt, chinhanh, tuan, tuNgay, denNgay, maHM, nhom, hangMuc, ngayDangThucTe, trangThai, daDang (bool), link, treHan (bool)`. `treHan` tính sẵn ở backend = `!daDang && denNgay đã qua`.

**`mkt.html` có 4 bảng, đều đánh số** (2 bảng đầu gộp chung 1 hàng qua CSS `.grid2`):
1. **Thực hiện theo Chi nhánh** — theo bộ lọc đang chọn.
2. **Theo Tuần** — kiểm tra quy định tối thiểu 1 bài/tuần/chi nhánh, mỗi ô hiện `đã đăng/đăng ký` (VD "1/3"), cột "Cảnh báo" chỉ dựa trên **đăng ký** (kế hoạch) chứ không dựa trên đã đăng.
3. **Độ phủ hạng mục theo Chi nhánh** — ma trận Mã HM × Chi nhánh, cùng format `đã đăng/đăng ký`.
4. **Chi tiết bài đăng** — sắp xếp 4 tầng: Trễ hạn lên đầu → Từ ngày (parse thật `dd/MM/yyyy` thành số `yyyyMMdd` để so sánh, **không dùng `localeCompare` trên chuỗi ngày** — lỗi sắp sai thứ tự tháng đã từng gặp) → Chi nhánh → Mã HM.

**Bảng 2 và 3 CỐ Ý không áp dụng bộ lọc phía trên** (Chi nhánh/Tuần/Trạng thái) — vì mục đích là kiểm tra **kế hoạch tổng thể**, lọc theo tuần sẽ làm mất ý nghĩa "đủ hạng mục"/"đủ tần suất".

**Trigger `mktSync`: 1 trigger duy nhất `everyHours(4)`** (từng bị lỗi "too many triggers" — xem mục Bẫy trong CLAUDE.md gốc, đã sửa cùng cách).
