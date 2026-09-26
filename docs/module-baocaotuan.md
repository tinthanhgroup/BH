# Module Báo cáo tuần chi nhánh (`baocaotuan.html`, tab 8 "BC tuần")

Thêm 25/09/2026. Tài liệu nghiệp vụ gốc: `Bao cao tuan/Bao cao tuan.md` (folder untracked ở root repo, chứa file Excel mẫu + đặc tả).

## Mục đích

Không lặp lại số liệu kết quả (đã có ở tab 2/3/4…). Báo cáo **quá trình điều hành**: mỗi dòng = 1 công việc theo suốt vòng đời
Nguồn → Nội dung/Mục tiêu → Hạn → Trạng thái → Cập nhật tuần (ghi nối tiếp `T39: … / T40: …`) → Vướng mắc → Đề xuất → Kế hoạch tiếp theo → Hoàn thành.
Trưởng BP nhập, GĐCN chắt lọc (Trọng tâm tuần, Ghi chú, GĐCN xử lý, duyệt TT tháng) + tự viết Nhận định tuần, TGĐ xem.

## Luồng dữ liệu

**Giai đoạn Excel (hiện tại):** mỗi chi nhánh gửi 1 file `.xlsx` (cùng mẫu) → bỏ vào folder `Bao cao tuan/` → chạy
```
powershell -ExecutionPolicy Bypass -File "Bao cao tuan\xlsx_to_json.ps1"
```
→ ghi `baocaotuan.json` ở root → commit/push. Script mở .xlsx như ZIP, đọc XML trực tiếp (máy không có Python/node). File `.ps1` **phải lưu UTF-8 có BOM** (PowerShell 5.1).

**Giai đoạn Google Sheet (sau này):** viết Apps Script ghi ra **đúng cấu trúc JSON bên dưới** → `baocaotuan.html` không phải sửa.

Tên chi nhánh lấy từ ô `B3` sheet bộ phận/TCT/Bao cao GDCN; trống thì lấy **tên file** (nên đặt tên file theo chi nhánh, vd `Nha Trang.xlsx`) và hiện cảnh báo.

## Sheet được đọc (chỉ dữ liệu gốc, KHÔNG đọc các sheet tổng hợp FILTER)

| Sheet | Dòng dữ liệu | Cột |
|---|---|---|
| `Ban hang` / `Dich vu` / `Van phong` | từ dòng 9, bỏ dòng có F (Nội dung) trống | B Mã · C Nguồn · D Tháng · E Lĩnh vực · F Nội dung · G Phụ trách · H Hạn · I Trạng thái · J Cập nhật tuần · K Tuần CN · L Vướng mắc · M Đề xuất · N Kế hoạch · O Ngày HT · P Kết quả · Q Trọng tâm tuần · R Ghi chú GĐCN · S GĐCN xử lý · T Duyệt TT tháng · U Loại hoạt động · V Mã TCT |
| `Ke hoach TCT giao` | từ dòng 9, bỏ dòng D trống | B Mã · C Ngày giao · D Nội dung · E Bộ phận · F Trạng thái · G Cập nhật · H Tuần CN · I Vướng mắc · J Đề xuất · K Kế hoạch · L Ngày HT · M Kết quả · N Ghi chú GĐCN · O GĐCN xử lý |
| `Nhan dinh tuan` | từ dòng 4, bỏ dòng D trống | A Ngày BC · D Nhận định · E Người viết |
| `Bao cao GDCN` | ô `G3` | Ngày báo cáo |

## Cấu trúc `baocaotuan.json`

```
{ updated_at, nguon:'excel'|'sheet',
  branches:[{ chiNhanh, file, ngayBaoCao:'yyyy-MM-dd'|'', ngayDuLieu, canhBao:[str],
    viec:[{boPhan:'BH'|'DV'|'VP', dong, ma, nguon, thang, linhVuc, noiDung, phuTrach, han, hanRaw, trangThai,
           capNhat, tuanCN, vuongMac, deXuat, keHoach, ngayHT, ngayHTRaw, ketQua, trongTamTuan, ghiChuGD,
           gdXuLy, duyetTT, loaiHD, maTCT, nguoiCapNhat}],
    tct:[{dong, ma, ngayGiao, ngayGiaoRaw, noiDung, boPhan, trangThai, capNhat, tuanCN, vuongMac, deXuat,
          keHoach, ngayHT, ngayHTRaw, ketQua, ghiChuGD, gdXuLy}],
    nhanDinh:[{ngay, noiDung, nguoiViet}] }] }
```
Ngày luôn `yyyy-MM-dd`. `xxxRaw` = chữ gốc khi người nhập **gõ ngày dạng chữ** (vd `"30/09/2026"`) — vẫn parse được thì điền cả ngày lẫn raw, web báo lỗi nhập liệu để sửa.

## Logic trang (tự tính lại, bám sát sheet Bao cao GDCN)

- **Mốc tham chiếu** = `ngayBaoCao` (GĐCN điền) hoặc `ngayDuLieu` (ngày lưu file) — KHÔNG dùng ngày hôm nay, để xem lại dữ liệu cũ không bị báo quá hạn sai. Tuần = `weekNum()` giống Excel `WEEKNUM(date,2)`.
- Quá hạn: có Hạn < mốc và chưa Hoàn thành/Tạm dừng. Chưa CN tuần này: đang mở và `tuanCN` trống hoặc < tuần mốc.
- **Quy ước 2 cột GĐCN (26/09/2026):**
  - "Trọng tâm tuần" chỉ có lựa chọn `Có`; **để trống = Không** (web chỉ đếm `==='Có'`).
  - "GĐCN xử lý" có `Chuyển thông tin TGĐ` (cần TGĐ biết/quyết) / `TGĐ đã có phản hồi` (TGĐ đã trả lời, việc chưa xong — nội dung phản hồi ghi ở Ghi chú GĐCN). Bỏ lựa chọn "GĐCN tự xử lý"; tên cũ lần lượt là "Chuyển TGĐ" / "Đã có quyết định", web nhận cả tên cũ (`isTGD()`/`isPhanHoi()`). **Để trống = GĐCN tự xử lý** → web hiện nhãn trung tính "GĐCN đang xử lý", không coi là lỗi. 
- Mục 2 Đề xuất: mọi dòng có Đề xuất và chưa Hoàn thành (không lọc Trọng tâm), "Chuyển thông tin TGĐ" xếp đầu, tô cam.
- Mục 3 Trọng tâm tuần: `trongTamTuan==='Có'`, nhóm theo BP. Mục 4 Cải tiến: `loaiHD==='Cải tiến'`.
- Mục 6 Cảnh báo & lỗi nhập liệu (tự ẩn khi rỗng): quá hạn, chưa CN, thiếu/trùng mã, hạn dạng chữ, Hoàn thành thiếu ngày/kết quả, có Ngày HT mà chưa Hoàn thành, TT tháng chưa duyệt, TCT giao thiếu Mã TCT, nhiệm vụ TCT chưa BP nào triển khai.
- Nhận định: lấy dòng cùng tuần/năm với mốc; các tuần khác vào "Nhận định các tuần khác".
- Chi nhánh đang chọn nhớ ở `localStorage` key `_bctBranch`.

## Phân quyền

Key tab `bct`, khoá cấp tab: **chỉ Admin** (25/09/2026, theo yêu cầu người dùng), nút tab ẩn với người khác (xem `_classifyGroup()` trong `index.html`). Không có trong `mobile.html`.
