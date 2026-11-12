# Module KM đối thủ (MKT 5.3)

`km_doi_thu.json` → `km_doi_thu.html` (nhúng iframe trong sub-tab "5.3. KM đối thủ" của tab 5 "MKT" trong `index.html`, xem `switchMktSub('kmdt')`). Thêm 30/10/2026, anh Khanh chọn đặt ở tab MKT.

**Nguồn dữ liệu:** không qua Google Sheet/Apps Script. Agent `doi-thu-km` của Project A (project `G:\My Drive\1. Project A\16. KM doi thu`, chạy 9h hằng ngày) ghi `km_doi_thu.json` trong thư mục project đó; `dashboard/km_len_bh.py` (gọi trong `km_doi_thu.ps1`) đẩy file đó lên repo này qua GitHub API sau mỗi lần chạy (commit "Auto-sync km_doi_thu.json …"). Sửa nội dung/quy tắc rà KM ở project 16, không sửa tay JSON ở đây.

**Cấu trúc JSON:** `updated_vn`, `cong_thuc` (`truoc_ba_pct`, `phi_5_cho`, `phi_7_cho`), `km[]` (`model, doi_thu, phien_ban, cho, niem_yet, giam_truc_tiep, ho_tro_truoc_ba, km, qua, han, nguon_loai, nguon, ngay, ghi_chu`), `thay_doi[]` (`ngay, noi_dung`), `nguon[]` (`ten, loai, link, trang_thai, ngay`), `tvbh[]`.

**Giá lăn bánh tạm tính** tính ở trang (`lanBanh()`): niêm yết − giảm trực tiếp + trước bạ sau hỗ trợ + phí cố định 5/7 chỗ. Công thức gốc: README project 16 mục "Giá lăn bánh tạm tính".

**Giá trị KM ước lượng và dòng Hyundai** (thêm 12/11/2026, anh Khanh chốt):
- Cột "Giá trị KM ước lượng" (`giaTriKM()`) = giảm trực tiếp + hỗ trợ trước bạ quy tiền + `qua_tien` (quà có ghi số tiền). Thu cũ đổi mới, lãi suất không cộng. `qua_tien` = null mà có quà → ghi "+ quà chưa rõ tiền".
- Dòng Hyundai đầu mỗi nhóm, **mọi phiên bản** có xe ký: trang tự tải `data.json`, lấy xe `nguonKhach = NT`, `ngayDatCoc` trong 90 ngày (`HY_NGAY`), nhóm theo `loaiXe2|phienBan`: giá bán bình quân (thấp–cao), số xe, quà hay gặp, chi phí quà bình quân (`chiPhiKM`). Giá niêm yết lấy từ `km_doi_thu.json` → `hyundai_niem_yet` (`{"Tucson|2.0 XĂNG ĐẶC BIỆT 2024": 859}`, triệu; phiên chính ghi ở project 16, agent giữ nguyên); nguồn hiện tại: giá bán lẻ khuyến nghị CV 38/2026/CV-HTV ngày 11/06/2026 (thư `21. Mail HTV/2026-06/`), ghi ở `hyundai_niem_yet_nguon`; HTV ra bảng giá mới thì cập nhật. Có niêm yết thì tính giá trị KM = niêm yết − giá bán bình quân + chi phí quà, lăn bánh = giá bán bình quân + trước bạ trên niêm yết + phí cố định; chưa có thì "chưa rõ". Dòng Hyundai không tính vào mức "thấp nhất nhóm".

Giao diện: `chung.css`, không `<h1>`. Bảng mục 1 (chốt 12/11/2026, theo chấm điểm giao diện): `table-layout:fixed` + `<colgroup>` (cột Khuyến mãi tự giãn, không cuộn ngang ở 1300px); cột Nguồn chỉ hiện tên miền, link đầy đủ ở `title`; lăn bánh thấp nhất nhóm tô xanh dương `--accent` (không xanh lá, vì là đối thủ cạnh tranh giá mạnh nhất); số trong bảng không dùng font Mono. Quyền: dùng chung quyền tab 5 như 5.1, 5.2.
