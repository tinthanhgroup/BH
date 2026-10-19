# Status – Hệ thống báo cáo nội bộ Hyundai Tín Thanh (BH)
Cập nhật: 19/10/2026

## Trạng thái: 🟢 Đúng tiến độ

- **Việc gấp nhất:** Trình TGĐ dự thảo Quy định xử lý kho tồn lâu
- **Đang chờ:** TGĐ: duyệt Quy định xử lý kho tồn lâu

Hệ thống đang chạy, auto-sync bình thường. Đơn T10 đã đặt, HTV xác nhận gần hết (NT 28/30, ĐL 10/10, PY 7/8). HTV yêu cầu đặt bổ sung để tuân thủ CV157 (thiếu NT 11 · ĐL 7 · PY 5, hạn 12h 10/10/2026); Công ty chọn **xin tạm dừng đặt bổ sung**: đã gửi công văn cho HTV ngày 10/10/2026, **HTV đã đồng ý tạm dừng (ghi nhận 13/10/2026)**; phương án 20 xe giữ làm dự phòng (bảng Giả định đặt hàng PA4). Quy tắc đặt hàng đã viết lại ở `docs/quy-tac-dat-hang.md`. Mô tả hệ thống: xem `CLAUDE.md` và `docs/`.

## Việc tiếp theo
- [ ] 20/10/2026: rà soát bán hàng; nếu thị trường tăng thì đặt bổ sung ngay (dự phòng: PA4 20 xe)
- [ ] Trình TGĐ dự thảo "Quy định xử lý kho tồn lâu" (`2. Dat hang/Quy dinh xu ly kho ton lau v8.docx`)
- [ ] Sửa hàm `uploadKCK()` (nút Admin): đang nhận diện "xe sắp hết" theo năm SX, đúng ra phải theo chữ đỏ ở ô "Năm SX"
- [x] Báo cáo tuần: mở tab BC tuần cho mọi tài khoản đã đăng nhập (19/10/2026)
- [ ] Báo cáo tuần: sửa múi giờ file mẫu (tinthanhgroup123) trước khi nhân bản cho ĐL, PR, BL
- [ ] Báo cáo tuần: đưa các chi nhánh còn lại (ĐL, PR, BL) lên Google Sheet
- [ ] Kiểm tra admin chạy .bat có dòng "Da chep Master Data len Google Drive" và `dichvu.json` cập nhật ngày mới
- [ ] Kiểm tra log `apDungLichMoi`: câu chữ hạn chót trong Hướng dẫn / sheet 6 / sheet 7 đã đổi hết
- [ ] 19/10/2026: kiểm tra số liệu tự động tuần 42 lên web; 20/10 sau 17h: kiểm tra bản chốt tuần 42
- [ ] Trang Chính sách: xác nhận IONIQ 5 còn áp dụng (đang hiện Active); tìm bản có số của T2, IONIQ 5, Bổ sung T1; bổ sung CV 160/161 (thưởng quý 2) nếu cần

## Đang chờ
- TGĐ: duyệt dự thảo Quy định xử lý kho tồn lâu (mốc ngày, lãi suất nội bộ, thời hạn thanh toán công nợ)

## Mốc sắp tới
- 20/10/2026: rà soát bán hàng, quyết định đặt bổ sung
- 19/10/2026: trưởng BP Nha Trang, Phú Yên chốt Báo cáo tuần 42
- 20/10/2026: 17h chốt Báo cáo tuần 42 (GĐCN), hệ thống lưu bản chốt
- 21/10/2026: họp giao ban thứ 4 theo bản chốt tuần 42

## Nhật ký
| Ngày | Sự kiện |
|---|---|
| 19/10/2026 | Tab BC tuần mở cho mọi tài khoản đã đăng nhập (trưởng BP cùng xem để họp giao ban); xem `docs/giao-dien-va-phan-quyen.md` |
| 17/10/2026 | Trang Chính sách: thêm 11 mục từ 12 công văn HTV T1–T6/2026 (722+723+bổ sung T1, T2, IONIQ 5, 73, 143 Custin, 151 Stargazer FL, 162, 3, 26, 27), PDF ở thư mục Drive `1. Chinh sach 2026`; đã push |
| 17/10/2026 | Báo cáo tuần đổi lịch (anh chốt, từ tuần 42): tuần T2–CN số tuần ISO, đổi 0h thứ 2; thứ 2 BP chốt, 17h thứ 3 chốt + lưu bản chốt (`bct_luu/`), thứ 4 họp; web có ô chọn bản đã chốt. Xem `docs/module-baocaotuan.md` mục "Lịch tuần báo cáo & bản chốt" |
| 17/10/2026 | Báo cáo tuần: đã chia sẻ file cho Nha Trang, Phú Yên và gửi hướng dẫn; sheet 6 nhãn tuần tự tính, bỏ khối nhập tay lượt xe; Nha Trang đã nhập 4 việc |
| 17/10/2026 | Lượt xe DV tự động chạy: `dichvu.json` từ Master Data (49.136 lệnh, 12/09/2025–17/10/2026, NT/PY/ĐL/BL); NT T39 khớp số nhập tay (BD 18,0 so với 18,17) |
| 17/10/2026 | Báo cáo tuần sheet 6: nhãn tuần tự tính; lượt xe DV chuyển sang tự động từ Master Data (cap_nhat.vbs tự chép lên Drive, `TinThanh_DichVu_Sync.gs` đọc sheet PBI mỗi giờ); xem `docs/apps-script.md` |
| 17/10/2026 | Báo cáo tuần: tạo Google Sheet Nha Trang + Phú Yên từ file mẫu, xoá dữ liệu DEMO, đặt lại `baocaotuan.json`, sửa múi giờ GMT+7 (tuần báo cáo đổi đúng 10h T7); xem `docs/module-baocaotuan.md` |
| 16/10/2026 | Trang Chính sách: 5 công văn CV 45, 72, 86, 113, 114 chuyển sang thư mục chung `1. Chinh sach 2026` trên Drive, đổi link sang từng file |
| 16/10/2026 | Thêm 5 chính sách HTV vào trang Chính sách (CV 45, 72, 86 chương trình T7–T9; CV 114 T10 gồm chương trình riêng Creta; CV 113 Venue), link thư mục Drive `Chinh sach ho tro HTV`; chưa commit/push, chờ anh Khanh xem |
| 13/10/2026 | HTV đồng ý tạm dừng đặt bổ sung T10 theo công văn ngày 10/10; vẫn rà soát bán hàng 20/10 như đã cam kết |
| 10/10/2026 | Đã gửi công văn cho HTV xin tạm dừng đặt bổ sung T10 (`CV de nghi tam dung dat hang bo sung T10.pdf`); chờ HTV phản hồi |
