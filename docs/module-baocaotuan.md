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
| `Nhan dinh tong the` (thêm 26/09/2026) | dò theo mã cột A | Khối `GĐCN` / `BH-000` / `DV-000` / `VP-000`: dòng tiêu đề (A mã · D Người viết) + dòng câu hỏi (A STT · B Nhãn · C Câu hỏi · D Trả lời). **Ghi đè mỗi tuần** |
| `Nhan dinh tuan` (cũ) | từ dòng 4, bỏ dòng D trống | A Ngày BC · D Nhận định · E Người viết — nhận định tự do của GĐCN, web chỉ dùng làm dự phòng khi GĐCN chưa điền khối `GĐCN` ở sheet mới |
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
    nhanDinh:[{ngay, noiDung, nguoiViet}],
    nhanDinhBP:[{nam, tuan, ngay, boPhan:'GDCN'|'BH'|'DV'|'VP', nguoiViet, items:[{stt, nhan, cauHoi, traLoi}]}] }] }
```

### Nhận định tổng thể tuần (sheet `Nhan dinh tong the`, thêm 26/09/2026)

- GĐCN (8 câu) + 3 trưởng BP (10 câu mỗi BP, mã `BH-000`/`DV-000`/`VP-000`) trả lời theo **bộ câu hỏi cố định** — xoáy vào đánh giá + lý do, không chép lại số liệu dashboard. Hiện ở **mục 2** trang web, ngay dưới thẻ chi nhánh (phần TGĐ đọc đầu tiên); cột "Nhận định tuần" ở bảng tổng quan cho biết ai đã/chưa viết.
- Là **sheet riêng**, không phải dòng trong 3 sheet bộ phận — để không đụng công thức/định dạng/`Mã tiếp theo` của bảng việc.
- Sheet bị **ghi đè mỗi tuần**; lịch sử giữ ở `nhanDinhBP` trong `baocaotuan.json`: `xlsx_to_json.ps1` đọc file JSON cũ, gộp theo chi nhánh + năm + tuần + bộ phận (tuần báo cáo = `WEEKNUM(ngayBaoCao || ngayDuLieu, 2)`), khối chưa trả lời câu nào thì không lưu. ⚠ Khoá gộp là **tên chi nhánh** — chi nhánh phải điền ô B3, nếu không tên lấy theo tên file (đổi tên file mỗi tuần → mất nối lịch sử). Apps Script sau này phải làm y như vậy (GET JSON cũ → upsert → PUT, giống `TinThanh_FBAds_Sync.gs`).
- Câu hỏi/nhãn lấy **từ chính file** (cột B/C), nên sửa câu hỏi trong Excel là web tự theo — không hardcode trong HTML.
- File mẫu cũ chưa có sheet này: chạy `Bao cao tuan/them_sheet_nhan_dinh.ps1 -Path <file>` (chèn sheet sau "Huong dan", tự sao lưu `<file>.bak.xlsx` — converter bỏ qua file `.bak.xlsx`; chạy lại trên file đã có sheet thì tự bỏ qua).
Ngày luôn `yyyy-MM-dd`. `xxxRaw` = chữ gốc khi người nhập **gõ ngày dạng chữ** (vd `"30/09/2026"`) — vẫn parse được thì điền cả ngày lẫn raw, web báo lỗi nhập liệu để sửa.

### Chỉ số trọng yếu (sheet `Chi so trong yeu`, thêm 26/09/2026)

- Biểu đồ nằm **trong mục 1** (dưới bảng tổng quan), theo chi nhánh đang chọn — `renderChiSo()`.
- Converter đọc theo cột B: dòng tên bộ phận (`Bán hàng`/`Dịch vụ`/`Văn phòng`) mở khối → dòng chữ đơn = tiêu đề chỉ số → dòng toàn chữ = tiêu đề cột → các dòng sau = số liệu. JSON: `chiSo:[{boPhan, tieuDe, nhanCot, cot:[..], dong:[{nhan, giaTri:[..]}]}]`.
- **Bán hàng**: cột chồng theo tuần, số **tự đếm từ `data.json`** (cột tên chi nhánh → `BRANCH_CODE`, tuần lấy số trong nhãn "Tuần N", đếm theo `weekBH()` giống tab BC Bán hàng; "Tổng" = cộng các cột). Ô trong sheet để trống cũng được; chỉ dùng số trong sheet khi không tải được `data.json`.
- **Dịch vụ** (và khối khác có dòng "Chỉ tiêu" + "Thực hiện"): thanh tiến độ % hoàn thành, xanh ≥100%, đỏ <100%. Khối không có cặp này thì liệt kê số.

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

### Số HĐ ký tự động trong khối BH-000 (thêm 26/09/2026)

- Câu "Hợp đồng" (câu 1) của khối Bán hàng tự gắn thêm dòng số liệu (nền xanh nhạt `.nd-auto`): số HĐ tuần này (+/− so với tuần trước), tuần trước, TB 4 tuần trước, luỹ kế tháng — **trưởng BP chỉ cần viết đánh giá + lý do**, không tự đếm. Hiện cả khi khối BH chưa viết, và ở các tuần trong lịch sử.
- Nguồn: `data.json` (cùng nguồn tab "BC Bán hàng"), đếm bản ghi theo `ngayDatCoc` + `nguonKhach` = mã chi nhánh (`BRANCH_CODE`: tên ô B3 bỏ dấu → NT/ĐL/PY/PR/BL/ĐN). Chỉ cần `data.json` (mọi HĐ 2026 nằm ở đây, `data_history.json` chỉ tới 2025). Tải lỗi thì bỏ qua, trang vẫn chạy.
- ⚠ Tuần tính **Chủ nhật → Thứ 7** (`weekBH()`, sao y `isoWeek()` trong `index.html`) để số khớp biểu đồ "Chốt cọc 5 tuần" của tab BC Bán hàng — **khác** tuần báo cáo của trang này (`weekNum()`, Thứ 2 → CN theo Excel `WEEKNUM(,2)`), lệch nhau 1 ngày; số tuần hiển thị thường trùng nhau. Tuần lấy theo ngày mốc báo cáo (`ngayBaoCao || ngayDuLieu`).

## Quy ước trình bày (26/09/2026, theo góp ý người dùng)

- **Toàn trang dùng 1 kiểu khối duy nhất** — kiểu của mục 2 (`.nd-block` + lưới `.nd-qa` "nhãn → nội dung", hàm `qaBlock()`/`taskBlock()`), xếp 2 cột (`.nd-grid`). **Không** dùng lại thẻ màu (tag/pill trạng thái), bảng nhiều cột hay nhiều kiểu định dạng khác nhau cho mục 3–9. Màu chỉ dùng cho điều bất thường: đỏ = quá hạn/thiếu, vàng = chưa cập nhật/nhập sai, cam = Chuyển thông tin TGĐ (viền trên khối + chữ).
- Mục 1 là bảng duy nhất: `table.ov` cố định độ rộng (`table-layout:fixed`), tiêu đề tự xuống dòng, chỉ 8 cột → **không trượt ngang** (đã kiểm tra ở 700/900/1300px). Thêm cột mới phải cân nhắc bỏ cột khác; số chi tiết để ở ô KPI của thẻ chi nhánh.
- Ô KPI thẻ chi nhánh bấm được (khi số > 0) → `jumpTo()` cuộn tới mục tương ứng và tự mở nếu đang thu gọn: Chuyển thông tin TGĐ / GĐCN đang xử lý → mục 3 (`#sec-dexuat`), Quá hạn / Chưa CN → mục 7 (`#sec-canhbao`), Việc đang mở → mục 8, Hoàn thành tháng → mục 9.

## Phân quyền

Key tab `bct`, khoá cấp tab: **chỉ Admin** (25/09/2026, theo yêu cầu người dùng), nút tab ẩn với người khác (xem `_classifyGroup()` trong `index.html`). Không có trong `mobile.html`.
