# Status – Hệ thống báo cáo nội bộ Hyundai Tín Thanh (BH)
Cập nhật: 02/11/2026

## Trạng thái: 🟢 Đúng tiến độ

- **Việc gấp nhất:** Tucson Hybrid: NT, ĐL, PY mỗi chi nhánh đặt 1 xe Trắng (chị Đào duyệt 02/11, hạn HTV 17h 02/11); theo dõi HTV xác nhận, cọc 15% trong 5 ngày làm việc
- **Đang chờ:** Phòng bán hàng: kết quả ký HĐ Palisade LX3 đến 31/10; Loan (KTT): gửi quy định cũ về lãi nội bộ / xe bán ngang (nếu có) để rà soát trùng lặp với QĐ 03.26

Hệ thống đang chạy, auto-sync bình thường. Đơn T10 đã đặt, HTV xác nhận gần hết (NT 28/30, ĐL 10/10, PY 7/8). Đặt bổ sung T10: lần 1 xin tạm dừng (CV 10/10, HTV đồng ý 13/10); HTV yêu cầu lại 1,5 tháng chỉ tiêu (NT 9 · ĐL 9 · PY 6), Công ty gửi CV 02/2026/CV-BHTT ngày 24/10 xin chưa đặt; **27/10/2026 chị Đào duyệt đặt bổ sung 23 xe theo đề xuất chi nhánh (NT 9 · ĐL 9 · PY 5)** — bảng Giả định đặt hàng PA5. Quy tắc đặt hàng đã viết lại ở `docs/quy-tac-dat-hang.md`. Mô tả hệ thống: xem `CLAUDE.md` và `docs/`.

## Việc tiếp theo
- [ ] Tucson Hybrid (CV 139/2026): 3 chi nhánh đặt mỗi nơi 1 xe Trắng; theo dõi HTV xác nhận, cọc 15% trong 5 ngày làm việc, bảo lãnh/LC 85% trong 7 ngày làm việc
- [x] Máy phụ: tạo token GitHub `.projecta/github_bh.json` (02/11, kiểm tra token đọc được repo)
- [ ] Đã đăng ký tác vụ `ProjectA - Loi nhuan BH` (làm mới `loinhuan.json` 6h hằng ngày), kiểm tra log `dashboard/log/loinhuan_len_bh.log` sáng 03/11
- [ ] Công nợ BH Phú Yên (báo cáo tuần): dán `TinThanh_CongNo_Sync.gs` vào script.google.com (điền token, bật Drive API, chạy ▶ `congNoSyncNgay` rồi `setupCongNoTrigger`), kế toán trưởng cập nhật file mỗi thứ 7 (kể cả đổi ngày "ĐẾN" ở ô A1); push `baocaotuan.html` + `congno.json`
- [ ] Báo cáo tuần: dán hàm `doanhThuDVTuDong()` vào script Báo cáo tuần, chạy 1 lần (xoá + bỏ tô cam ô C15, sửa mục J Hướng dẫn ở NT, PY, file mẫu)
- [ ] Đặt bổ sung T10 (23 xe, chị Đào duyệt 27/10): chi nhánh đặt trên hệ thống HTV, theo dõi HTV xác nhận; lưu ý 6 Creta N Line CBU chưa có CKD kèm (PY đặt 5/6 xe HTV yêu cầu)
- [ ] Quy ước tên chiến dịch FB: chốt ý nghĩa VM (Vùng miền), CDR ghi theo quý, ngày áp dụng; phổ biến MKT chi nhánh; sau đó sửa `phanLoaiChienDich()` đọc theo mẫu
- [ ] Nhận quy định cũ từ Loan (nếu có): rà soát trùng lặp với cách tính lãi nội bộ hằng tuần của PKT; cần thì đề xuất chị Đào sửa đổi QĐ 03.26
- [ ] KCK: dán `TinThanh_KCK_Sync.gs` (bản 02/11, thêm chặn ghi đè lùi) vào script.google.com trước lần đồng bộ kế tiếp, nếu không `kck.json` bị kéo về bản HTV 20/10
- [ ] Sửa hàm `uploadKCK()` (nút Admin): đang nhận diện "xe sắp hết" theo năm SX, đúng ra phải theo chữ đỏ ở ô "Năm SX"
- [ ] Báo cáo tuần: xoá 2 bản sao lưu "… sao luu truoc tach VP …" (NT, PY) trên Drive khi chạy ổn vài ngày
- [ ] Báo cáo tuần Đà Lạt: đã tạo file (`1-_MDVNi…`, 31/10) và thêm vào `SHEET_IDS`; còn dán script → ▶ `setupBaoCaoTuanTriggers` → ▶ `syncBaoCaoTuan`, chia sẻ file cho ĐL
- [ ] Kiểm tra admin chạy .bat có dòng "Da chep Master Data len Google Drive" và `dichvu.json` cập nhật ngày mới
- [ ] Kiểm tra log `apDungLichMoi`: câu chữ hạn chót trong Hướng dẫn / sheet 6 / sheet 7 đã đổi hết
- [ ] Palisade LX3 (CV 126): rà khách đặt xe đến 16/10, ký HĐ chính thức trước 31/10/2026 để được tặng bảo hiểm PVI và thưởng TVBH
- [ ] Trang Chính sách: xác nhận IONIQ 5 còn áp dụng (đang hiện Active); tìm bản có số của T2, IONIQ 5, Bổ sung T1; bổ sung CV 160/161 (thưởng quý 2) nếu cần

## Đang chờ
- Loan (KTT): góp ý 22/10 – PKT đang đối chiếu công nợ nội bộ và tính lãi nội bộ hằng tuần (cả xe bán ngang), cho rằng phần lãi sau điều chuyển của QĐ 03.26 bị trùng. Anh đã trả lời: đối chiếu định kỳ PKT tự sắp xếp; QĐ quy định chi phí thuộc chi nhánh nào trong 4 trường hợp; nhờ Loan gửi quy định trước đó (nếu có) để anh rà soát

## Mốc sắp tới
—

## Nhật ký
| Ngày | Sự kiện |
|---|---|
| 02/11/2026 | KCK: cập nhật tay từ file HTV gửi 02/11 (link SharePoint vẫn bản 20/10): thêm Tucson Hybrid FWD, Creta CKD Cao cấp/Đặc biệt, bớt Santafe Turbo Calligraphy Đen; xem `docs/cap-nhat-thu-cong.md` |
| 02/11/2026 | Tucson Hybrid: HTV yêu cầu đặt trong T11 (hạn 17h 02/11, không đặt thì cắt KM T11, ngưng giao LX3); chị Đào duyệt NT, ĐL, PY mỗi nơi 1 xe Trắng |
| 02/11/2026 | Trang Chính sách: thêm CV 139/2026 (Tucson Hybrid), CV 157/2025 (tồn kho + B/O ≥ 1,5 tháng, thay CV 449/2024) |
| 02/11/2026 | Palisade LX3: hết hạn ký HĐ 31/10, phòng bán hàng chưa cập nhật số khách đã ký |
| 02/11/2026 | Lợi nhuận gộp: `loinhuan.json` làm mới theo dữ liệu 02/11 (đứng từ 23/10, nên xe admin sửa chưa lên); từ nay tự làm mới 6h hằng ngày, xem `docs/module-loinhuan.md` |
| 01/11/2026 | Báo cáo tuần: thử gộp số liệu + nhận định theo bộ phận rồi bỏ (anh Khanh không duyệt), giữ bố cục cũ; mặc định mở tuần đang diễn ra, bỏ mục "sau giờ chốt"; doanh thu DV = doanh thu nội bộ; xem `docs/module-baocaotuan.md` |
| 01/11/2026 | Báo cáo tuần: chốt và áp đủ chuẩn Giao diện 1 – tinh tế (Inter, màu trung tính, cỡ chữ 14/16/24; thay quy ước 20/10, 28/10); đã sửa chữ "Chưa trả lời" đủ tương phản, lỗi nhập liệu tô vàng, mục 7 chia 3 nhóm, khối 1.3 Nhân sự gọn, biểu đồ vẽ theo bề rộng thật, khung 1280px; xem `docs/module-baocaotuan.md` |
| 01/11/2026 | Báo cáo tuần: hoàn tất các mục còn lại của đánh giá Giao diện 1 (danh sách tổng quan trên điện thoại, vùng bấm 40px, bo góc 8px, ngày dd/mm, ô trống "–"); xem `docs/module-baocaotuan.md` |
| 31/10/2026 | Báo cáo tuần: sửa giao diện theo đánh giá Giao diện 1 (hết tràn ngang điện thoại, bỏ đậm 600, dòng tóm tắt việc 2 mẩu, bỏ viền màu và biểu tượng, bớt màu KPI, đầu bảng nền nhạt); xem `docs/module-baocaotuan.md` |
| 31/10/2026 | Báo cáo tuần: chốt 3 file — NT (gồm Phan Rang, bán hàng PR do Duy báo cáo), PY, ĐL (gồm Bảo Lộc); không tạo file riêng PR, BL; xem `docs/module-baocaotuan.md` |
| 31/10/2026 | Báo cáo tuần: thêm hàm `taoFileDaLat()` tạo file Đà Lạt bằng cách chép file Phú Yên (đã tách VP, GMT+7) rồi xoá dữ liệu; xem `docs/module-baocaotuan.md` mục Tách Văn phòng |
| 30/10/2026 | Tab MKT thêm mục 5.3 KM đối thủ (`km_doi_thu.html` + `km_doi_thu.json` từ project 16); xem `docs/module-kmdoithu.md` |
| 29/10/2026 | Báo cáo tuần Phú Yên: thêm khối Công nợ bảo hiểm (biểu đồ theo tuần từ `congno.json`, điểm đầu 16/10); xem `docs/module-baocaotuan.md` |
| 28/10/2026 | Nhân sự, Chấm công: dựng lại bố cục (KPI → so sánh chi nhánh → bất thường/xu hướng luôn hiện, chi tiết thu gọn; Phan Rang tách riêng; tuổi/thâm niên theo nhóm); đổi quy tắc thu gọn, xem `docs/giao-dien-va-phan-quyen.md` |
