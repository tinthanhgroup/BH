# Nhật ký lưu trữ – Hệ thống báo cáo nội bộ Hyundai Tín Thanh (BH)

Chuyển từ `STATUS.md` bằng `dashboard/luu_tru.py`. Chỉ thêm, không sửa nội dung cũ. Tình hình hiện tại: `STATUS.md`.

## Nhật ký

| Ngày | Sự kiện |
|---|---|
| 05/11/2026 | Kế hoạch T11: NT giảm 1 Tucson XĐB Đỏ + 1 Trắng → 12 xe (NT 5 · ĐL 7); đã sửa bảng Giả định đặt hàng |
| 04/11/2026 | Kế hoạch T11: NT, ĐL gửi đề xuất đặt hàng (14 xe: NT 7 · ĐL 7), PY không đặt; đưa vào bảng Giả định đặt hàng, xoá các phương án T10; xem `docs/module-hanghoa.md` |
| 04/11/2026 | HTV bỏ quy định kẹp Creta N Line CBU với CKD; đã sửa `docs/quy-tac-dat-hang.md` mục 6 và trang Quy tắc đặt hàng |
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
| 28/10/2026 | Mục 4 (Nhân sự, Chấm công, Nhà cung cấp, Quy định nội bộ): sửa theo đánh giá UX lần 2 (font biểu đồ, KPI kiểu chung, nhãn chỉ tô khi cần chú ý, bỏ emoji, độ rộng cột) |
| 28/10/2026 | Báo cáo tuần: doanh thu DV "Thực hiện" tự động từ `13. DV - Master Data/Doanh thu.xlsx` (Giá trị trước thuế, gồm xe ngập, theo ngày thanh toán; chỉ tiêu vẫn nhập tay); biểu đồ HĐ ký đổi sang tuần T2–CN như tuần báo cáo; chưa push, chờ dán script; xem `docs/module-baocaotuan.md` |
| 28/10/2026 | Tiêu đề mục tab Hàng hóa đổi theo kiểu tab Bán hàng (chữ thường, màu nhấn, bỏ emoji); cảnh báo xe bán ngang giữ màu |
| 28/10/2026 | Chuyển xong các trang module sang `chung.css` (MKT, Quảng cáo FB, Chấm công, Nhân sự, Chính sách, Quy định); Báo cáo tuần, Lợi nhuận, KQKD chỉ đồng bộ font/màu; xem `docs/giao-dien-va-phan-quyen.md` |
| 28/10/2026 | Đánh giá lại giao diện toàn hệ thống (CRM 5,8 → 7,3/10); chữ phụ đậm hơn (`--muted #5E6E8A`, cả index); chung.css thêm bảng nhẹ, nhãn trung tính, tên nhãn cũ; sửa biểu đồ CRM; chuyển trang Nhà cung cấp sang chung.css |
| 27/10/2026 | Chốt chuẩn giao diện mới theo tab Hàng hóa / Bán hàng, file chung `chung.css`; chuyển trang CRM trước (sửa sót Very Hot, cột ngày chưa cập nhật khách Hot, tách nguồn theo TVBH, thiếu Nguồn); xem `docs/giao-dien-va-phan-quyen.md`; đã push 27/10 |
| 27/10/2026 | Chị Đào duyệt đặt bổ sung T10: 23 xe theo đề xuất 3 chi nhánh (NT 9 · ĐL 9 · PY 5, folder `2. Dat hang/T10 L2 v2/`); bảng Giả định đặt hàng PA5 đã push |
| 27/10/2026 | Đồng bộ phong cách 5 trang module (Nhân sự, Chấm công, Quảng cáo FB, CRM, MKT): màu chữ phụ, bộ màu nhãn chung, tiêu đề mục chữ thường 15px, ngày dd/mm/yyyy, màu chi nhánh theo BC Bán hàng; quy ước ghi ở `docs/giao-dien-va-phan-quyen.md`; đã push 27/10 |
| 27/10/2026 | Đã tách sheet Văn phòng Nha Trang (KT 3, CSKH 4, HCNS 15 việc); push trang Báo cáo tuần hiện nhóm CSKH / Kế toán / HCNS |
| 27/10/2026 | Đã tách sheet Văn phòng Phú Yên (CSKH 12, HCNS 8 việc, giữ mã VP-); hiện lại cột E (ô Mã tiếp theo), xoá nội dung Lĩnh vực ở NT + PY; xem `docs/module-baocaotuan.md` |
| 27/10/2026 | Chốt tách sheet Văn phòng báo cáo tuần thành CSKH / Kế toán / HCNS (nhận định VP viết chung), làm Phú Yên trước; đã viết script + sửa web (chưa push); xem `docs/module-baocaotuan.md` |
| 27/10/2026 | Tab Admin: thẻ bot đổi tên "Bot và hệ thống agent", thêm nút "Hệ thống agent →" (`http://localhost:8765/he-thong`, Project A `dashboard/he_thong_agent.py`); repo chỉ chứa địa chỉ localhost; xem `docs/giao-dien-va-phan-quyen.md` |
| 27/10/2026 | Tab 4 HCNS: sửa giao diện Nhân sự + Chấm công theo đánh giá UX (ngày dd/mm/yyyy, tương phản chữ, cảnh báo HĐ hết hạn lên mục 1, đánh số mục tự động…); đã push; xem `docs/module-nhansu.md`, `docs/module-chamcong.md` |
| 26/10/2026 | Thêm tab 9 "KQKD" (Đánh giá KQKD chi nhánh, chỉ Admin + nhóm TGĐ), số liệu mã hóa bằng mật khẩu KQKD trong `kqkd.enc.json`; bỏ thẻ link claude.ai trong tab Admin; xem `docs/module-kqkd.md` |
| 26/10/2026 | Xong rà soát bán hàng/quyết định đặt bổ sung (20/10), chốt Báo cáo tuần 42 và họp giao ban 21/10 (người dùng xác nhận) |
| 25/10/2026 | Tab MKT › Quảng cáo FB: thêm panel 6 "Quy ước đặt tên chiến dịch" (mẫu `CHI NHÁNH CHƯƠNG TRÌNH KỲ XE ghi chú`, ví dụ cỡ lớn); đã push 27/10; xem `docs/module-fbads.md` |
| 25/10/2026 | Tab Admin: thêm thẻ "Đánh giá KQKD chi nhánh" (nút mở trang claude.ai riêng tư của Project A 9. KQKD); repo chỉ chứa link, không số liệu; xem `docs/giao-dien-va-phan-quyen.md` |
| 25/10/2026 | Tab Admin: thêm thẻ "Chi phí bot Telegram" (nút mở `http://localhost:8765/`, trang chi phí chạy trên máy anh Khanh, Project A `dashboard/trang_chi_phi.py`); repo chỉ chứa địa chỉ localhost, không mã tài khoản hay số liệu; xem `docs/giao-dien-va-phan-quyen.md` |
| 23/10/2026 | Đổi thẻ 12 thành ô KPI "Lợi nhuận gộp" cạnh Bảo Lộc (tab 2); bấm mở cửa sổ nổi chứa danh sách (loinhuan.html?tab=ky); vẫn quyền `ln` |
| 23/10/2026 | Chuyển "Lợi nhuận xe" từ tab 9 riêng thành thẻ 12 trong tab 2 BC Bán hàng (`#ln-card`, vẫn quyền `ln`: Admin, GĐ/PGĐ, Bán hàng; MKT thấy tab 2 nhưng không thấy thẻ); gỡ tab 9 |
| 23/10/2026 | Thêm tab 9 "Lợi nhuận xe" (`loinhuan.html` + `loinhuan.json`): bản đồ lợi nhuận gộp từng xe 2026, 2 tab con theo báo bán / theo ngày ký; khoá quyền `ln` chỉ Admin, GĐ/PGĐ, Bán hàng (xem docs/module-loinhuan.md; dữ liệu nhạy cảm nằm trong repo công khai, anh Khanh chấp nhận) |
| 23/10/2026 | Trang Chính sách: thêm All New Palisade CKD (CV 125 giá/thanh toán, CV 126 tặng bảo hiểm PVI, TVBH 2,5tr; hạn ký HĐ 31/10), CV 159 thanh toán Q2 (Inactive); CV 125 xác nhận LX3 không tính thưởng kinh doanh; đã push |
| 22/10/2026 | Đã thông báo QĐ 03.26 cho nhóm GĐBH (nhấn mạnh chia chi phí) và nhóm Kế toán; trả lời góp ý của Loan về trùng lãi nội bộ, chờ Loan gửi quy định cũ (nếu có) để rà soát |
| 21/10/2026 | Ban hành Quy định xử lý xe tồn kho lâu và điều chuyển xe, Số 03.26, hiệu lực 21/10/2026 (TGĐ duyệt nguyên văn v8); bản gốc `3. Chính sách nội bộ/23. QD 03.26 Xu ly xe ton kho lau.docx`, đã thêm vào trang Quy định nội bộ; xem `G:\My Drive\1. Project A\0. Quy dinh\xu-ly-kho-ton-lau.md` |
| 20/10/2026 | Đã gửi TGĐ dự thảo Quy định xử lý kho tồn lâu v8 (sửa 17/10: lãi và vận chuyển tính khi bán xe theo số ngân hàng, 8%/năm chỉ để minh họa; phụ lục số liệu 17/10); xem `G:\My Drive\1. Project A\0. Quy dinh\xu-ly-kho-ton-lau.md` |
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
| 10/10/2026 | Soạn công văn gửi HTV xin tạm dừng đặt bổ sung T10 (thị trường chưa tăng: HĐ ký mới Q2 57, Q3 54 xe/tháng; tồn + đã đặt 112 xe ≈ 2,1 tháng), cam kết rà soát 20/10. Giả định đặt hàng: thêm PA4 (20 xe + 3 Creta chờ XN) |
| 09/10/2026 | Xe chưa BC DMS: cột đề xuất đặt hàng lấy theo Chờ XN (bỏ Palisade), bỏ cột Ngập nước. Chỉ tiêu T10 +1 xe mỗi đại lý (NT 41, ĐL 29, PY 14). Viết quy tắc đặt hàng (`docs/quy-tac-dat-hang.md`); soạn dự thảo Quy định xử lý kho tồn lâu + công nợ nội bộ |
| 08/10/2026 | Creta: bỏ chữ "FACELIFT" khỏi mọi phiên bản; tách phiên bản CKD theo màu có chữ "CKD" (vd. "Trắng CKD" → "1.5 ĐẶC BIỆT CKD", màu Trắng), chữ CKD tô xanh đậm — index + mobile |
| 07/10/2026 | Giả định đặt hàng: PA3 viết lại = PA2 với Creta N Line NT có 3 CKD; ô hạn mức CKD/CBU chỉ xét N Line → 47 xe, đủ hạn mức |
| 06/10/2026 | Giả định đặt hàng: nhiều lần cập nhật L1/L2 theo file chi nhánh; thêm cột tốc độ bán 90 ngày, nút ẩn/hiện chi nhánh |
| 06/10/2026 | Xe chưa BC DMS: thêm cột 9–12 (đề xuất đặt hàng PA2, tốc độ bán) |

## Việc đã xong

### Lưu trữ 19/10/2026

- [x] Chốt phương án đặt hàng T10 với HTV (đã đặt, HTV xác nhận gần hết)
- [x] Soạn công văn xin tạm dừng đặt bổ sung T10 (`2. Dat hang/CV de nghi tam dung dat hang bo sung T10 v3.docx`)
- [x] Gửi công văn xin tạm dừng đặt bổ sung T10 cho HTV (10/10/2026; bản gửi: `2. Dat hang/CV de nghi tam dung dat hang bo sung T10.pdf`, số CV: _cập nhật_)
- [x] Theo dõi phản hồi của HTV về công văn → HTV đồng ý tạm dừng đặt bổ sung T10 (13/10/2026)
- [x] Báo cáo tuần: đưa Nha Trang + Phú Yên lên Google Sheet (17/10/2026; file mẫu DEMO giữ riêng, không đồng bộ)
- [x] Báo cáo tuần: đặt múi giờ GMT+7 cho 2 file (file mẫu để GMT+0 → tuần đổi lúc 17h); đã ra tuần 42
- [x] Báo cáo tuần: chia sẻ file + hướng dẫn cho GĐCN, trưởng BP Nha Trang và Phú Yên (17/10/2026)
- [x] Báo cáo tuần sheet 6: nhãn tuần tự tính (17/10/2026)
- [x] Lượt xe DV tự động: thư mục Drive `13. DV - Master Data` (tinthanhgroup123), cài `TinThanh_DichVu_Sync.gs`, `dichvu.json` có dữ liệu đến 17/10 (17/10/2026)
- [x] Bỏ khối nhập tay lượt xe ở sheet 6 (17/10/2026)
- [x] Chia sẻ thư mục `13. DV - Master Data` cho admin (17/10/2026)
- [x] Báo cáo tuần lịch mới (từ tuần 42): đã chạy `apDungLichMoi` (ngày báo cáo chuyển 10/10 lúc 18:10), push `baocaotuan.html` (17/10/2026)
- [x] Trang Chính sách: thêm 12 công văn chương trình T1–T6/2026, Custin, Stargazer FL, IONIQ 5, CV 27 (17/10/2026, đã push)

### Lưu trữ 25/10/2026

- [x] Trình TGĐ dự thảo "Quy định xử lý kho tồn lâu" (`2. Dat hang/Quy dinh xu ly kho ton lau v8.docx`) (20/10/2026)
- [x] Quy định xử lý xe tồn kho lâu: TGĐ duyệt nguyên văn v8, ban hành Số 03.26 ngày 21/10/2026; đưa lên Drive và trang Quy định nội bộ (21/10/2026)
- [x] Báo cáo tuần: mở tab BC tuần cho mọi tài khoản đã đăng nhập (19/10/2026)

### Lưu trữ 26/10/2026

- [x] 20/10/2026: rà soát bán hàng, quyết định đặt bổ sung
- [x] 19/10/2026: kiểm tra số liệu tự động tuần 42 lên web; 20/10 sau 17h: kiểm tra bản chốt tuần 42

### Lưu trữ 02/11/2026

- [x] Báo cáo tuần: dán `TinThanh_DichVu_Sync.gs` mới (đọc thêm Doanh thu.xlsx) vào script.google.com, chạy ▶ `dichVuSyncNgay`, kiểm tra `dichvu.json` có `doanhThu`; push `baocaotuan.html` (doanh thu DV tự động, tuần HĐ T2–CN)
- [x] Chuyển các trang module sang `chung.css` (28/10/2026; Báo cáo tuần, Lợi nhuận, KQKD chỉ đồng bộ font và màu)
- [x] Tiêu đề tab Hàng hóa đổi theo kiểu tab Bán hàng (28/10/2026); anh Khanh xem trên web, không đẹp thì trả lại bằng git revert
- [x] Báo cáo tuần: tách sheet Văn phòng thành CSKH / Kế toán / HCNS — Phú Yên (đã tách, dữ liệu đồng bộ đủ 20 việc)
- [x] Báo cáo tuần: tách Văn phòng Nha Trang (KT 3, CSKH 4, HCNS 15 việc), push trang `baocaotuan.html` mới

### Lưu trữ 11/11/2026

- [x] Kế hoạch T11: NT 5 · ĐL 7 đã đặt (07/11)
- [x] Máy phụ: tạo token GitHub `.projecta/github_bh.json` (02/11, kiểm tra token đọc được repo)
- [x] Đã đăng ký tác vụ `ProjectA - Loi nhuan BH` (làm mới `loinhuan.json` 6h hằng ngày); log chạy được từ 03/11

### Lưu trữ 11/11/2026

- [x] (Xong, anh Khanh báo 11/11) Tucson Hybrid (CV 139/2026): 3 chi nhánh đặt mỗi nơi 1 xe Trắng; theo dõi HTV xác nhận, cọc 15% trong 5 ngày làm việc, bảo lãnh/LC 85% trong 7 ngày làm việc
- [x] (Bỏ theo dõi, admin đã báo cáo HTV – 11/11) Palisade LX3 (CV 126): rà khách đặt xe đến 16/10, ký HĐ chính thức trước 31/10/2026 để được tặng bảo hiểm PVI và thưởng TVBH
