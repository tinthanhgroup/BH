# Status – Hệ thống báo cáo nội bộ Hyundai Tín Thanh (BH)
Cập nhật: 07/10/2026

## Trạng thái: 🟢 Đúng tiến độ
Hệ thống đang chạy, auto-sync bình thường. 06–07/10 tập trung vào module **Giả định đặt hàng T10**: đã chốt Phương án 3, tổng 47 xe (NT 30 · PY 8 · ĐL 9); Creta N Line đạt tỷ lệ hạn mức CKD/CBU (3 CKD : 9 CBU). Mô tả hệ thống: xem `CLAUDE.md` và `docs/`.

## Việc tiếp theo
- [ ] Chốt phương án đặt hàng T10 với HTV
- [ ] Sửa hàm `uploadKCK()` (nút Admin): đang nhận diện "xe sắp hết" theo năm SX, đúng ra phải theo chữ đỏ ở ô "Năm SX"
- [ ] Báo cáo tuần: mở quyền cho PGĐ (hiện chỉ Admin + GĐ)
- [ ] Báo cáo tuần: chuyển từ nhập tay Excel → Google Sheet + Apps Script

## Đang chờ
- _(chưa có)_

## Mốc sắp tới
- _(chưa có)_

## Nhật ký
| Ngày | Sự kiện |
|---|---|
| 07/10/2026 | Giả định đặt hàng: PA3 viết lại = PA2 với Creta N Line NT có 3 CKD; ô hạn mức CKD/CBU chỉ xét N Line → 47 xe, đủ hạn mức |
| 06/10/2026 | Giả định đặt hàng: nhiều lần cập nhật L1/L2 theo file chi nhánh; thêm cột tốc độ bán 90 ngày, nút ẩn/hiện chi nhánh |
| 06/10/2026 | Xe chưa BC DMS: thêm cột 9–12 (đề xuất đặt hàng PA2, tốc độ bán) |
