# Module KM đối thủ (MKT 5.3)

`km_doi_thu.json` → `km_doi_thu.html` (nhúng iframe trong sub-tab "5.3. KM đối thủ" của tab 5 "MKT" trong `index.html`, xem `switchMktSub('kmdt')`). Thêm 30/10/2026, anh Khanh chọn đặt ở tab MKT.

**Nguồn dữ liệu:** không qua Google Sheet/Apps Script. Agent `doi-thu-km` của Project A (project `G:\My Drive\1. Project A\16. KM doi thu`, chạy 9h hằng ngày) ghi `km_doi_thu.json` trong thư mục project đó; `dashboard/km_len_bh.py` (gọi trong `km_doi_thu.ps1`) đẩy file đó lên repo này qua GitHub API sau mỗi lần chạy (commit "Auto-sync km_doi_thu.json …"). Sửa nội dung/quy tắc rà KM ở project 16, không sửa tay JSON ở đây.

**Cấu trúc JSON:** `updated_vn`, `cong_thuc` (`truoc_ba_pct`, `phi_5_cho`, `phi_7_cho`), `km[]` (`model, doi_thu, phien_ban, cho, niem_yet, giam_truc_tiep, ho_tro_truoc_ba, km, qua, han, nguon_loai, nguon, ngay, ghi_chu`), `thay_doi[]` (`ngay, noi_dung`), `nguon[]` (`ten, loai, link, trang_thai, ngay`), `tvbh[]`.

**Giá lăn bánh tạm tính** tính ở trang (`lanBanh()`): niêm yết − giảm trực tiếp + trước bạ sau hỗ trợ + phí cố định 5/7 chỗ. Công thức gốc: README project 16 mục "Giá lăn bánh tạm tính".

Giao diện: `chung.css`, không `<h1>`. Quyền: dùng chung quyền tab 5 như 5.1, 5.2.
