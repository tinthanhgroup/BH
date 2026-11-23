# Status – Hệ thống báo cáo nội bộ Hyundai Tín Thanh (BH)
Cập nhật: 24/11/2026

## Trạng thái: 🟢 Đúng tiến độ

- **Việc gấp nhất:** Đặt bổ sung T11 (NT 9 · ĐL 7 · PY 2), theo dõi HTV xác nhận và phản hồi email gia hạn CV157 đến 20/11
- **Đang chờ:** HTV: phản hồi email xin gia hạn CV157 đến 20/11; Loan (KTT): gửi quy định cũ về lãi nội bộ / xe bán ngang (nếu có) để rà soát trùng lặp với QĐ 03.26

Hệ thống đang chạy, auto-sync bình thường. Đơn T10 đã đặt, HTV xác nhận gần hết (NT 28/30, ĐL 10/10, PY 7/8). Đặt bổ sung T10: lần 1 xin tạm dừng (CV 10/10, HTV đồng ý 13/10); HTV yêu cầu lại 1,5 tháng chỉ tiêu (NT 9 · ĐL 9 · PY 6), Công ty gửi CV 02/2026/CV-BHTT ngày 24/10 xin chưa đặt; **27/10/2026 chị Đào duyệt đặt bổ sung 23 xe theo đề xuất chi nhánh (NT 9 · ĐL 9 · PY 5)** — bảng Giả định đặt hàng PA5. Quy tắc đặt hàng đã viết lại ở `docs/quy-tac-dat-hang.md`. Mô tả hệ thống: xem `CLAUDE.md` và `docs/`.

## Việc tiếp theo
- [ ] Báo cáo tuần W47 chốt 17h30 thứ 4 25/11 (24/11 nghỉ lễ): đã dán script mới (24/11); còn báo chi nhánh hạn mới, thứ 4 trước 17h30 vẫn ghi Tuần CN = 47 (ô E3 hiện 48); họp giao ban dời theo
- [ ] Đặt bổ sung T11 (18 xe: NT 9 · ĐL 7 · PY 2, đủ phần tối thiểu để HTV trả Palisade; phần còn lại của CV157 đã gửi email xin gia hạn đến 20/11, báo 11/11): chi nhánh đặt trên hệ thống HTV, theo dõi HTV xác nhận; PY chưa gửi file đề xuất
- [ ] Công nợ BH Phú Yên (báo cáo tuần): dán `TinThanh_CongNo_Sync.gs` vào script.google.com (điền token, bật Drive API, chạy ▶ `congNoSyncNgay` rồi `setupCongNoTrigger`), kế toán trưởng cập nhật file mỗi thứ 7 (kể cả đổi ngày "ĐẾN" ở ô A1); push `baocaotuan.html` + `congno.json`
- [ ] Báo cáo tuần: dán hàm `doanhThuDVTuDong()` vào script Báo cáo tuần, chạy 1 lần (xoá + bỏ tô cam ô C15, sửa mục J Hướng dẫn ở NT, PY, file mẫu)
- [ ] Đặt bổ sung T10 (23 xe, chị Đào duyệt 27/10): chi nhánh đặt trên hệ thống HTV, theo dõi HTV xác nhận (PY đặt 5/6 xe HTV yêu cầu)
- [ ] Quy ước tên chiến dịch FB: chốt ý nghĩa VM (Vùng miền), CDR ghi theo quý, ngày áp dụng; phổ biến MKT chi nhánh; sau đó sửa `phanLoaiChienDich()` đọc theo mẫu
- [ ] Nhận quy định cũ từ Loan (nếu có): rà soát trùng lặp với cách tính lãi nội bộ hằng tuần của PKT; cần thì đề xuất chị Đào sửa đổi QĐ 03.26
- [ ] KCK: dán `TinThanh_KCK_Sync.gs` (bản 02/11, thêm chặn ghi đè lùi) vào script.google.com trước lần đồng bộ kế tiếp, nếu không `kck.json` bị kéo về bản HTV 20/10
- [ ] Sửa hàm `uploadKCK()` (nút Admin): đang nhận diện "xe sắp hết" theo năm SX, đúng ra phải theo chữ đỏ ở ô "Năm SX"
- [ ] Báo cáo tuần: xoá 2 bản sao lưu "… sao luu truoc tach VP …" (NT, PY) trên Drive khi chạy ổn vài ngày
- [ ] Báo cáo tuần – quyền file: chuyển chủ sở hữu NT, PY (đang là khanhpt235) sang tinthanhgroup123 (làm tay, tinthanhgroup123 bấm chấp nhận); thêm khanhpt235 vào file ĐL; sau đó cấp quyền sửa theo email từng người (cần danh sách email NT, PY) rồi khoá link về chỉ xem
- [ ] Báo cáo tuần Đà Lạt: đã tạo file (`1-_MDVNi…`, 31/10) và thêm vào `SHEET_IDS`; còn dán script → ▶ `setupBaoCaoTuanTriggers` → ▶ `syncBaoCaoTuan`, chia sẻ file cho ĐL
- [ ] Kiểm tra admin chạy .bat có dòng "Da chep Master Data len Google Drive" và `dichvu.json` cập nhật ngày mới
- [ ] Kiểm tra log `apDungLichMoi`: câu chữ hạn chót trong Hướng dẫn / sheet 6 / sheet 7 đã đổi hết
- [ ] Trang Chính sách: xác nhận IONIQ 5 còn áp dụng (đang hiện Active); tìm bản có số của T2, IONIQ 5, Bổ sung T1; bổ sung CV 160/161 (thưởng quý 2) nếu cần

## Đang chờ
- HTV: phản hồi email xin gia hạn đặt đủ tồn tối thiểu CV157 đến 20/11/2026
- Loan (KTT): góp ý 22/10 – PKT đang đối chiếu công nợ nội bộ và tính lãi nội bộ hằng tuần (cả xe bán ngang), cho rằng phần lãi sau điều chuyển của QĐ 03.26 bị trùng. Anh đã trả lời: đối chiếu định kỳ PKT tự sắp xếp; QĐ quy định chi phí thuộc chi nhánh nào trong 4 trường hợp; nhờ Loan gửi quy định trước đó (nếu có) để anh rà soát

## Mốc sắp tới
- 20/11/2026: hạn đặt đủ tồn tối thiểu CV157 (đã xin HTV gia hạn, chờ phản hồi)

## Nhật ký
| Ngày | Sự kiện |
|---|---|
| 24/11/2026 | Báo cáo tuần: đã dán `TinThanh_BaoCaoTuan_Sync.gs` mới (giờ chốt W47 17h30 thứ 4 25/11). Tab Hàng hóa: ô Tổng giữ nguyên gồm xe Nhà máy cho mượn (anh Khanh chốt) |
| 23/11/2026 | Báo cáo tuần: W47 đổi giờ chốt sang 17h30 thứ 4 25/11 (24/11 nghỉ lễ, chỉ tuần này) — thêm `CHOT_DOI` trong script, chưa dán; xem `docs/module-baocaotuan.md` mục Lịch tuần |
| 23/11/2026 | KQKD: bỏ dòng tiêu đề đầu và câu nhận xét, biểu đồ lợi nhuận ghi số từng tháng (nhãn nền tối chữ trắng); chờ chạy lại `bh_kqkd.py` với mật khẩu thật |
| 23/11/2026 | Chính sách HTV: thêm CV 157 (khai báo bán lẻ, giao xe, bảo hành DMS), CV 158 & 159 (hỗ trợ lãi suất, 3 gói ưu đãi T11), CV 161 (hỗ trợ thanh toán đặt hàng T11); `POLICY_LATEST_UPDATE` = 20/11 |
| 23/11/2026 | KQKD (tab 9) chuyển sang Giao diện 1 sau chấm điểm 5,2/10: `kqkd.html` (font Inter, màu, khung mật khẩu) + khối ghi đè trong `dash_kqkd_khung.html`, STT bảng Xếp hạng, màu sơ đồ dòng tiền; chờ chạy lại `bh_kqkd.py` với mật khẩu thật rồi push; xem `docs/module-kqkd.md` |
| 22/11/2026 | Nhân sự: bỏ dòng cảnh báo hợp đồng đầu trang |
| 22/11/2026 | Nhân sự (4.1): bỏ mục "Điểm nóng nhân sự" (trùng bảng 1), các mục sau đánh lại số; danh sách HĐ không tô đỏ dòng đã hết hạn; xem `docs/module-nhansu.md` |
| 22/11/2026 | Tab 4–9: thêm lại cột STT cho mọi bảng danh sách (người dùng chốt, thay quyết định bỏ STT 21/11), tự đánh bằng CSS trong `giao-dien-1.css`; sửa chung sau chấm điểm (ô trống "–", bỏ kẻ dọc/chữ nghiêng, chữ SVG 12px, 4 nhãn lệch màu); xem `docs/giao-dien-va-phan-quyen.md` |
| 22/11/2026 | Tab 4–9 chốt 3 cỡ chữ 12 / 13 / 16px (chữ phụ / nội dung / tiêu đề khối), số thẻ chỉ số 26px: sửa `giao-dien-1.css`, chamcong, fbads; quy chuẩn ở QUY_CHUAN.md (Giao diện 1) |
| 22/11/2026 | Chấm công (4.2) sửa theo chấm điểm Giao diện 1 (5,7/10): bảng 2–3 lưới 2 cột, biểu đồ hàng riêng, số chi nhánh 26px, bỏ ngày/màu cam ở tiêu đề, màu biểu đồ xanh–xám, số thập phân dấu phẩy, "Đi trễ" thống nhất; `giao-dien-1.css`: tiêu đề nhỏ 14px, ô số/giờ 36px, bỏ chữ nghiêng |
| 21/11/2026 | FB Ads mục 6 "Quy ước đặt tên": còn 3 cỡ chữ (16/14/12–13px), khối công thức thành nhãn nền nhạt, ví dụ 14px; quy chuẩn Giao diện 1 thêm "tối đa 3 cỡ chữ trong một khối" |
| 21/11/2026 | Tab 4–9 đợt 2: sửa chung `giao-dien-1.css` (ô tìm kiếm, màu nhấn, nhãn, chữ ≥ 12px), FB Ads ghi số kiểu Việt, bỏ cột STT/"Hôm nay"/emoji; chấm lại tăng 0,8–2,0 điểm/trang; xem `docs/giao-dien-va-phan-quyen.md` |
| 21/11/2026 | Tab 4–9 chuyển sang "Giao diện 1 – tinh tế", chỉ dùng trên máy tính: thêm `giao-dien-1.css`, nạp vào 9 trang module tab 4–7 ; xem `docs/giao-dien-va-phan-quyen.md` mục "Quy ước giao diện chung" |
| 11/11/2026 | Tab Hàng hóa (`index.html`): ô LXX và Đã phân xe cộng xe LXX đã khớp khách DS nợ, ô LXX ghi "(N đã phân xe)"; bảng Thực hiện chỉ tiêu chuyển khách đó sang cột Đã phân xe; mobile giữ nguyên; xem `docs/module-hanghoa.md` mục "LXX đã phân khách" |
| 11/11/2026 | Tucson Hybrid 3 chi nhánh đã xong; Palisade LX3 (ký HĐ đến 31/10) bỏ theo dõi, admin đã báo cáo HTV |
| 11/11/2026 | Đã gửi email HTV xin gia hạn đặt đủ tồn tối thiểu CV157 đến 20/11; chờ HTV phản hồi |
| 10/11/2026 | Trang Chính sách: thêm CV 59 (thanh toán đơn hàng T7–T12), CV 80 (phân bổ LX3, kèm thư 16/10, 31/10, 10/11), CV 82 (thưởng KD loại LX3), CV 61 & 103 (chi trả thưởng TVBH, PGBank), CV 33 (giữ xe sửa chữa lớn), CV 109 (điều kiện, hồ sơ thưởng DV-PT); PDF chép vào `1. Chinh sach 2026` |
| 10/11/2026 | Đặt bổ sung T11 theo file chi nhánh (`2. Dat hang/T11 BS/`): NT 9, ĐL 7, PY 2 (Creta N Line Đỏ, Tucson XĐB Đỏ); thay bảng Giả định đặt hàng |
| 10/11/2026 | Palisade Hybrid: chị Đào giữ nguyên đơn (thừa xe có thể bán ngang ra Bắc, theo anh Sơn HTV), bỏ CV xin giảm; HTV yêu cầu đặt đủ tồn tối thiểu CV157 (49 xe) mới trả Palisade; dự kiến được trả 18–19 xe (70% Cali 7 chỗ, 30% Cali 8 chỗ, anh Nam báo) |
| 09/11/2026 | Tab Hàng hóa: ô KPI BO và Tổng hiện số LX3 trong ngoặc như ô Xe nợ (BO 105 gồm 61 LX3) |
