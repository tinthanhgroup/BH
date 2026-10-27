# Status – Hệ thống báo cáo nội bộ Hyundai Tín Thanh (BH)
Cập nhật: 27/10/2026

## Trạng thái: 🟢 Đúng tiến độ

- **Việc gấp nhất:** Palisade LX3 (CV 126): ký HĐ chính thức các khách đặt xe trước 31/10/2026
- **Đang chờ:** Loan (KTT): gửi quy định cũ về lãi nội bộ / xe bán ngang (nếu có) để rà soát trùng lặp với QĐ 03.26

Hệ thống đang chạy, auto-sync bình thường. Đơn T10 đã đặt, HTV xác nhận gần hết (NT 28/30, ĐL 10/10, PY 7/8). Đặt bổ sung T10: lần 1 xin tạm dừng (CV 10/10, HTV đồng ý 13/10); HTV yêu cầu lại 1,5 tháng chỉ tiêu (NT 9 · ĐL 9 · PY 6), Công ty gửi CV 02/2026/CV-BHTT ngày 24/10 xin chưa đặt; **27/10/2026 chị Đào duyệt đặt bổ sung 23 xe theo đề xuất chi nhánh (NT 9 · ĐL 9 · PY 5)** — bảng Giả định đặt hàng PA5. Quy tắc đặt hàng đã viết lại ở `docs/quy-tac-dat-hang.md`. Mô tả hệ thống: xem `CLAUDE.md` và `docs/`.

## Việc tiếp theo
- [x] Chuyển các trang module sang `chung.css` (28/10/2026; Báo cáo tuần, Lợi nhuận, KQKD chỉ đồng bộ font và màu)
- [ ] Chờ anh Khanh chốt: tiêu đề tab Hàng hóa có đổi theo kiểu tab Bán hàng (bỏ màu riêng từng mục, emoji) không
- [ ] Đặt bổ sung T10 (23 xe, chị Đào duyệt 27/10): chi nhánh đặt trên hệ thống HTV, theo dõi HTV xác nhận; lưu ý 6 Creta N Line CBU chưa có CKD kèm (PY đặt 5/6 xe HTV yêu cầu)
- [ ] Quy ước tên chiến dịch FB: chốt ý nghĩa VM (Vùng miền), CDR ghi theo quý, ngày áp dụng; phổ biến MKT chi nhánh; sau đó sửa `phanLoaiChienDich()` đọc theo mẫu
- [ ] Nhận quy định cũ từ Loan (nếu có): rà soát trùng lặp với cách tính lãi nội bộ hằng tuần của PKT; cần thì đề xuất chị Đào sửa đổi QĐ 03.26
- [ ] Sửa hàm `uploadKCK()` (nút Admin): đang nhận diện "xe sắp hết" theo năm SX, đúng ra phải theo chữ đỏ ở ô "Năm SX"
- [x] Báo cáo tuần: tách sheet Văn phòng thành CSKH / Kế toán / HCNS — Phú Yên (đã tách, dữ liệu đồng bộ đủ 20 việc)
- [x] Báo cáo tuần: tách Văn phòng Nha Trang (KT 3, CSKH 4, HCNS 15 việc), push trang `baocaotuan.html` mới
- [ ] Báo cáo tuần: xoá 2 bản sao lưu "… sao luu truoc tach VP …" (NT, PY) trên Drive khi chạy ổn vài ngày; tách Văn phòng cho ĐL, PR, BL khi đưa lên Google Sheet
- [ ] Báo cáo tuần: sửa múi giờ file mẫu (tinthanhgroup123) trước khi nhân bản cho ĐL, PR, BL
- [ ] Báo cáo tuần: đưa các chi nhánh còn lại (ĐL, PR, BL) lên Google Sheet
- [ ] Kiểm tra admin chạy .bat có dòng "Da chep Master Data len Google Drive" và `dichvu.json` cập nhật ngày mới
- [ ] Kiểm tra log `apDungLichMoi`: câu chữ hạn chót trong Hướng dẫn / sheet 6 / sheet 7 đã đổi hết
- [ ] Palisade LX3 (CV 126): rà khách đặt xe đến 16/10, ký HĐ chính thức trước 31/10/2026 để được tặng bảo hiểm PVI và thưởng TVBH
- [ ] Trang Chính sách: xác nhận IONIQ 5 còn áp dụng (đang hiện Active); tìm bản có số của T2, IONIQ 5, Bổ sung T1; bổ sung CV 160/161 (thưởng quý 2) nếu cần

## Đang chờ
- Loan (KTT): góp ý 22/10 – PKT đang đối chiếu công nợ nội bộ và tính lãi nội bộ hằng tuần (cả xe bán ngang), cho rằng phần lãi sau điều chuyển của QĐ 03.26 bị trùng. Anh đã trả lời: đối chiếu định kỳ PKT tự sắp xếp; QĐ quy định chi phí thuộc chi nhánh nào trong 4 trường hợp; nhờ Loan gửi quy định trước đó (nếu có) để anh rà soát

## Mốc sắp tới
- 31/10/2026: hạn ký HĐ chính thức Palisade LX3 (CV 126)

## Nhật ký
| Ngày | Sự kiện |
|---|---|
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
