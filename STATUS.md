# Status – Hệ thống báo cáo nội bộ Hyundai Tín Thanh (BH)
Cập nhật: 17/10/2026

## Trạng thái: 🟢 Đúng tiến độ

- **Việc gấp nhất:** Trình TGĐ dự thảo Quy định xử lý kho tồn lâu
- **Đang chờ:** TGĐ: duyệt Quy định xử lý kho tồn lâu

Hệ thống đang chạy, auto-sync bình thường. Đơn T10 đã đặt, HTV xác nhận gần hết (NT 28/30, ĐL 10/10, PY 7/8). HTV yêu cầu đặt bổ sung để tuân thủ CV157 (thiếu NT 11 · ĐL 7 · PY 5, hạn 12h 10/10/2026); Công ty chọn **xin tạm dừng đặt bổ sung**: đã gửi công văn cho HTV ngày 10/10/2026, **HTV đã đồng ý tạm dừng (ghi nhận 13/10/2026)**; phương án 20 xe giữ làm dự phòng (bảng Giả định đặt hàng PA4). Quy tắc đặt hàng đã viết lại ở `docs/quy-tac-dat-hang.md`. Mô tả hệ thống: xem `CLAUDE.md` và `docs/`.

## Việc tiếp theo
- [x] Chốt phương án đặt hàng T10 với HTV (đã đặt, HTV xác nhận gần hết)
- [x] Soạn công văn xin tạm dừng đặt bổ sung T10 (`2. Dat hang/CV de nghi tam dung dat hang bo sung T10 v3.docx`)
- [x] Gửi công văn xin tạm dừng đặt bổ sung T10 cho HTV (10/10/2026; bản gửi: `2. Dat hang/CV de nghi tam dung dat hang bo sung T10.pdf`, số CV: _cập nhật_)
- [x] Theo dõi phản hồi của HTV về công văn → HTV đồng ý tạm dừng đặt bổ sung T10 (13/10/2026)
- [ ] 20/10/2026: rà soát bán hàng; nếu thị trường tăng thì đặt bổ sung ngay (dự phòng: PA4 20 xe)
- [ ] Trình TGĐ dự thảo "Quy định xử lý kho tồn lâu" (`2. Dat hang/Quy dinh xu ly kho ton lau v8.docx`)
- [ ] Sửa hàm `uploadKCK()` (nút Admin): đang nhận diện "xe sắp hết" theo năm SX, đúng ra phải theo chữ đỏ ở ô "Năm SX"
- [ ] Báo cáo tuần: mở quyền cho PGĐ (hiện chỉ Admin + GĐ)
- [x] Báo cáo tuần: đưa Nha Trang + Phú Yên lên Google Sheet (17/10/2026; file mẫu DEMO giữ riêng, không đồng bộ)
- [x] Báo cáo tuần: đặt múi giờ GMT+7 cho 2 file (file mẫu để GMT+0 → tuần đổi lúc 17h); đã ra tuần 42
- [ ] Báo cáo tuần: sửa múi giờ file mẫu (tinthanhgroup123) trước khi nhân bản cho ĐL, PR, BL
- [x] Báo cáo tuần: chia sẻ file + hướng dẫn cho GĐCN, trưởng BP Nha Trang và Phú Yên (17/10/2026)
- [ ] Báo cáo tuần: đưa các chi nhánh còn lại (ĐL, PR, BL) lên Google Sheet
- [x] Báo cáo tuần sheet 6: nhãn tuần tự tính (17/10/2026)
- [x] Lượt xe DV tự động: thư mục Drive `13. DV - Master Data` (tinthanhgroup123), cài `TinThanh_DichVu_Sync.gs`, `dichvu.json` có dữ liệu đến 17/10 (17/10/2026)
- [x] Bỏ khối nhập tay lượt xe ở sheet 6 (17/10/2026)
- [x] Chia sẻ thư mục `13. DV - Master Data` cho admin (17/10/2026)
- [ ] Kiểm tra admin chạy .bat có dòng "Da chep Master Data len Google Drive" và `dichvu.json` cập nhật ngày mới
- [ ] Báo cáo tuần lịch mới (từ tuần 42): dán script, chạy `apDungLichMoi`; push `baocaotuan.html`
- [ ] 19/10/2026: kiểm tra số liệu tự động tuần 42 lên web; 20/10 sau 17h: kiểm tra bản chốt tuần 42

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
| 17/10/2026 | Báo cáo tuần đổi lịch (anh chốt, từ tuần 42): tuần T2–CN số tuần ISO, đổi 0h thứ 2; thứ 2 BP chốt, 17h thứ 3 chốt + lưu bản chốt (`bct_luu/`), thứ 4 họp; web có ô chọn bản đã chốt. Xem `docs/module-baocaotuan.md` mục "Lịch tuần báo cáo & bản chốt" |
| 17/10/2026 | Báo cáo tuần: đã chia sẻ file cho Nha Trang, Phú Yên và gửi hướng dẫn; sheet 6 nhãn tuần tự tính, bỏ khối nhập tay lượt xe; Nha Trang đã nhập 4 việc |
| 17/10/2026 | Lượt xe DV tự động chạy: `dichvu.json` từ Master Data (49.136 lệnh, 12/09/2025–17/10/2026, NT/PY/ĐL/BL); NT T39 khớp số nhập tay (BD 18,0 so với 18,17) |
| 17/10/2026 | Báo cáo tuần sheet 6: nhãn tuần tự tính; lượt xe DV chuyển sang tự động từ Master Data (cap_nhat.vbs tự chép lên Drive, `TinThanh_DichVu_Sync.gs` đọc sheet PBI mỗi giờ); xem `docs/apps-script.md` |
| 17/10/2026 | Báo cáo tuần: tạo Google Sheet Nha Trang + Phú Yên từ file mẫu, xoá dữ liệu DEMO, đặt lại `baocaotuan.json`, sửa múi giờ GMT+7 (tuần báo cáo đổi đúng 10h T7); xem `docs/module-baocaotuan.md` |
| 16/10/2026 | Trang Chính sách: 5 công văn CV 45, 72, 86, 113, 114 chuyển sang thư mục chung `1. Chinh sach 2026` trên Drive, đổi link sang từng file |
| 16/10/2026 | Thêm 5 chính sách HTV vào trang Chính sách (CV 45, 72, 86 chương trình T7–T9; CV 114 T10 gồm chương trình riêng Creta; CV 113 Venue), link thư mục Drive `Chinh sach ho tro HTV`; chưa commit/push, chờ anh Khanh xem |
| 13/10/2026 | HTV đồng ý tạm dừng đặt bổ sung T10 theo công văn ngày 10/10; vẫn rà soát bán hàng 20/10 như đã cam kết |
| 10/10/2026 | Đã gửi công văn cho HTV xin tạm dừng đặt bổ sung T10 (`CV de nghi tam dung dat hang bo sung T10.pdf`); chờ HTV phản hồi |
| 10/10/2026 | Soạn công văn gửi HTV xin tạm dừng đặt bổ sung T10 (thị trường chưa tăng: HĐ ký mới Q2 57, Q3 54 xe/tháng; tồn + đã đặt 112 xe ≈ 2,1 tháng), cam kết rà soát 20/10. Giả định đặt hàng: thêm PA4 (20 xe + 3 Creta chờ XN) |
| 09/10/2026 | Xe chưa BC DMS: cột đề xuất đặt hàng lấy theo Chờ XN (bỏ Palisade), bỏ cột Ngập nước. Chỉ tiêu T10 +1 xe mỗi đại lý (NT 41, ĐL 29, PY 14). Viết quy tắc đặt hàng (`docs/quy-tac-dat-hang.md`); soạn dự thảo Quy định xử lý kho tồn lâu + công nợ nội bộ |
| 08/10/2026 | Creta: bỏ chữ "FACELIFT" khỏi mọi phiên bản; tách phiên bản CKD theo màu có chữ "CKD" (vd. "Trắng CKD" → "1.5 ĐẶC BIỆT CKD", màu Trắng), chữ CKD tô xanh đậm — index + mobile |
| 07/10/2026 | Giả định đặt hàng: PA3 viết lại = PA2 với Creta N Line NT có 3 CKD; ô hạn mức CKD/CBU chỉ xét N Line → 47 xe, đủ hạn mức |
| 06/10/2026 | Giả định đặt hàng: nhiều lần cập nhật L1/L2 theo file chi nhánh; thêm cột tốc độ bán 90 ngày, nút ẩn/hiện chi nhánh |
| 06/10/2026 | Xe chưa BC DMS: thêm cột 9–12 (đề xuất đặt hàng PA2, tốc độ bán) |
