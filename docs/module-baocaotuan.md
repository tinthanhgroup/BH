# Module Báo cáo tuần chi nhánh (`baocaotuan.html`, tab 8 "BC tuần")

Thêm 25/09/2026. Tài liệu nghiệp vụ gốc: `Bao cao tuan/Bao cao tuan.md` (folder untracked ở root repo, chứa file Excel mẫu + đặc tả).

## Mục đích

Không lặp lại số liệu kết quả (đã có ở tab 2/3/4…). Báo cáo **quá trình điều hành**: mỗi dòng = 1 công việc theo suốt vòng đời
Nguồn → Nội dung/Mục tiêu → Hạn → Trạng thái → Cập nhật tuần (ghi nối tiếp `T39: … / T40: …`) → Vướng mắc → Đề xuất → Kế hoạch tiếp theo → Hoàn thành.
Trưởng BP nhập, GĐCN chắt lọc (Trọng tâm tuần, Ghi chú, Hướng chỉ đạo, duyệt TT tháng) + tự viết Nhận định tuần, TGĐ xem.

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
| `Ban hang` / `Dich vu` / `Van phong` | từ dòng 9, bỏ dòng có F (Nội dung) trống | B Mã · C Nguồn · D Tháng · E Lĩnh vực · F Nội dung · G Phụ trách · H Hạn · I Trạng thái · J Cập nhật tuần · K Tuần CN · L Vướng mắc · M Đề xuất · N Kế hoạch · O Ngày HT · P Kết quả · Q Trọng tâm tuần · R Ghi chú GĐCN · S Hướng chỉ đạo · T Duyệt TT tháng · U Loại hoạt động · V Mã TCT |
| `Ke hoach TCT giao` | từ dòng 9, bỏ dòng D trống | B Mã · C Ngày giao · D Nội dung · E Bộ phận · F Trạng thái · G Cập nhật · H Tuần CN · I Vướng mắc · J Đề xuất · K Kế hoạch · L Ngày HT · M Kết quả · N Ghi chú GĐCN · O Hướng chỉ đạo |
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
- **Nhân sự** (thẻ rộng hết hàng `.cs-full`, thêm 28/09/2026, **luôn hiện, không cần khối trong sheet**) — `renderNS()`/`nsData()`, tự tính từ `nhansu.json` (`records.data`/`turnover`) + `chamcong.json` theo chi nhánh đang xem (`branchCode()` → tên chi nhánh, so khớp field `chinhanh`): tổng nhân sự đang làm; bảng BH/DV/VP (theo `khoi`/`khoi_hr`) gồm số nhân sự, lượt đi muộn + TB/ngày, lượt nghỉ/vắng + TB/ngày trong **tuần báo cáo (T2–CN chứa ngày mốc)**; danh sách nhân sự mới / nghỉ việc **30 ngày tính tới ngày mốc**.
  - ⚠ **Chưa có dữ liệu đơn "xin đi muộn" / "nghỉ phép"** → đang đếm theo máy chấm công: Đi muộn = quẹt vào sau 07:30 / giờ vào chiều + 5 phút (giờ vào chiều 13:30, **từ tuần 40 — T2 28/09/2026 — là 13:15**, `ccVaoChieu(d)` theo từng ngày), mỗi buổi 1 lượt, ngoại lệ thai sản +60 phút (sao y mặc định + `PERSON_EXCEPTIONS` của `chamcong.html` — đổi bên đó nhớ đổi `CC_*` ở đây); Nghỉ/vắng = dòng chấm công ngày làm việc có `cocong=false`. Bỏ Chủ nhật và ngày < 20% người có công (nghỉ lễ). TB/ngày = lượt / số ngày làm việc có dữ liệu.
  - `chamcong.json` ghi `ngay` dạng **năm-NGÀY-tháng** (`2026-21-09`) — `CC_YDM` tự nhận lúc tải (phần giữa có số > 12).
- **Ghi chú nguồn/cách tính** trong các thẻ mục 1 thu gọn sau nút "Ghi chú" (`csNote()`, 28/09/2026); thông báo lỗi tải dữ liệu vẫn hiện thẳng.
- **Mục 2 Nhận định tuần hiện tại**: khối GĐCN luôn mở; 3 bộ phận BH/DV/VP thu gọn (`ndBlock(..., fold=true)` → `<details class="nd-fold">`, tiêu đề ghi người viết + số câu đã trả lời). Lịch sử các tuần trước giữ như cũ.
- **Bố cục**: 2 cột bằng nhau, cao bằng nhau — Bán hàng trái, Dịch vụ phải (27/09/2026). Trong mỗi thẻ các phần xếp dọc, ngăn bằng gạch mờ `.cs-part`.
- **Bán hàng**: trên là biểu đồ HĐ ký `CS_WEEKS` (=10) tuần gần nhất, dưới là bảng **Báo bán so với chỉ tiêu** (27/09/2026) = bảng 9 "Thực hiện chỉ tiêu Tháng" tab BC Bán hàng bỏ các cột 60–90%, thêm cột Hoàn thành = Báo bán DMS / Chỉ tiêu; **chỉ hiện nhóm của chi nhánh đang xem** (PR→NT, BL/ĐN→ĐL; ghi chú chuyển chỉ tiêu cũng chỉ giữ lần chuyển liên quan), 2 dòng: **Tháng** (bảng 9) + **Quý** (bảng 10, `getDMSMonth().quy` ← `dmsQuarterGroupData()`; quý không tách Chờ đóng tiền/Chưa có xe nên gộp 1 ô "Chờ phân xe") — không nhận ra chi nhánh thì hiện bảng tháng đủ 3 nhóm + Tổng. Số lấy **từ trang cha** `parent.getDMSMonth()` (định nghĩa trong `index.html`, dùng chung hàm `dmsMonthGroupData()` với bảng 9 → cùng logic, cùng chỉ tiêu chỉnh tay trong localStorage của máy đó); trang cha chưa tải xong thì iframe thử lại mỗi giây trong 60s; mở `baocaotuan.html` riêng lẻ thì bảng chỉ hiện ghi chú. Tháng = tháng của dữ liệu `data.json` hiện tại, không theo tuần báo cáo.
- Biểu đồ HĐ ký: cột chồng theo tuần, số **tự đếm từ `data.json`** (cột tên chi nhánh → `BRANCH_CODE`, tuần lấy số trong nhãn "Tuần N", đếm theo `weekBH()` giống tab BC Bán hàng; "Tổng" = cộng các cột). Ô trong sheet để trống cũng được; chỉ dùng số trong sheet khi không tải được `data.json`.
- **Dịch vụ** (và khối khác có dòng "Chỉ tiêu" + "Thực hiện"): thanh tiến độ % hoàn thành, xanh ≥100%, đỏ <100%. Khối không có cặp này thì liệt kê số.
- **Khối không phải BH có dòng tiêu đề cột ≥ 2 cột** (thêm 27/09/2026): bảng so sánh cột 1 (kỳ này) với cột 2 (mốc so sánh) — **hiện giá trị tuyệt đối trước** theo thứ tự cột 2 → cột 1, rồi mới tới "Tăng/giảm" (số tuyệt đối) và "%" (xanh tăng/đỏ giảm). Đơn vị ghi trong tiêu đề cột ở sheet (vd "TB 90 ngày (lượt/ngày)"). Muốn thêm chỉ số thứ 2 cho cùng bộ phận thì mở **khối mới** (thêm dòng tên bộ phận), vì tiêu đề chỉ số chỉ nhận ở đầu khối — web tự **gộp các khối cùng bộ phận thành 1 thẻ** (khối sau thành phần phụ `.cs-part` có tiêu đề riêng, 27/09/2026), JSON vẫn giữ tách khối. Đang dùng cho "Lượt xe TB/ngày làm việc" Dịch vụ (dòng 17–22): Bảo dưỡng / Bảo hiểm / Đồng sơn tiền mặt, tuần gần nhất so với 90 ngày trước. **Số điền tay**, tính từ file xuất Power BI dịch vụ (`PBI DV/1. PBI.xlsx`, bảng lệnh sửa chữa): đếm RO không trùng theo cột Phân loại (`BD`/`Bảo hiểm`/`Đồng sơn tiền mặt`), ngày lấy từ cột Day/Month/Year (cột "Ngày vào xưởng" lẫn định dạng, đảo ngày-tháng), bỏ Chủ nhật + ngày lễ (lệnh phát sinh những ngày đó cũng bỏ), chia cho số ngày làm việc có lệnh của chi nhánh.

## Logic trang (tự tính lại, bám sát sheet Bao cao GDCN)

- **Mốc tham chiếu** = `ngayBaoCao` (GĐCN điền) hoặc `ngayDuLieu` (ngày lưu file) — KHÔNG dùng ngày hôm nay, để xem lại dữ liệu cũ không bị báo quá hạn sai. Tuần = `weekNum()` giống Excel `WEEKNUM(date,2)`.
- Quá hạn: có Hạn < mốc và chưa Hoàn thành/Tạm dừng. Chưa CN tuần này: đang mở và `tuanCN` trống hoặc < tuần mốc.
- **Quy ước 2 cột GĐCN (26/09/2026):**
  - "Trọng tâm tuần" chỉ có lựa chọn `Có`; **để trống = Không** (web chỉ đếm `==='Có'`).
  - Cột **"Hướng chỉ đạo"** (28/09/2026; tên cũ "Chuyển TGĐ" ← "GĐCN xử lý" — cột S sheet bộ phận, cột O sheet TCT giao; field JSON vẫn là `gdXuLy`, web ghi nhãn dòng "Hướng chỉ đạo") có 2 lựa chọn: `GĐCN đã có ý kiến chỉ đạo` (GĐCN tự chỉ đạo, nội dung ghi ở Ghi chú GĐCN) / `Chuyển thông tin TGĐ` (cần TGĐ biết/quyết). **Có Đề xuất, chưa Hoàn thành mà để trống = chưa có ý kiến**: Excel tô VÀNG ô Hướng chỉ đạo (CF dùng lại dxf vàng của cột Tuần CN), web hiện chữ vàng "Chưa có ý kiến chỉ đạo". Web vẫn nhận giá trị cũ: "Chuyển TGĐ" (`isTGD()`), "TGĐ đã có chỉ đạo"/"TGĐ đã có phản hồi"/"Đã có quyết định" (`isPhanHoi()`, coi như đã có chỉ đạo — `daCoChiDao()`).
- **Loại hoạt động (cột U) đánh số** (28/09/2026): `1. Vận hành thường xuyên` · `2. Phát sinh không thường xuyên` (việc thi thoảng mới có: tuyển dụng, nhân sự nghỉ việc…) · `3. Xử lý sự cố` · `4. Cải tiến` · `5. Tuân thủ - TCT giao`. Công thức/tô màu trong file dò "Cải tiến" bằng `SEARCH`/`"*Cải tiến*"` (không so bằng tuyệt đối); web so qua `loaiHD()`/`isCaiTien()` (bỏ số đầu chuỗi) → nhận cả giá trị cũ không số.
- **Ngày hoàn thành / Kết quả hoàn thành KHÔNG bắt buộc** (28/09/2026): web không báo thiếu; ô KPI "Hoàn thành tháng" đếm theo Ngày HT, dòng không có Ngày HT thì theo cột Tháng.
- ⚠ Ô **Ngày báo cáo** (`Bao cao GDCN!G3`) để trống thì mốc = ngày lưu file → mở sửa file vào tuần sau (vd sáng thứ 2) là bị coi thành tuần mới, nhận định bị chép sang tuần mới trong lịch sử. Luôn điền G3 (file tuần 39 được điền 26/09/2026 vào ngày 28/09 vì lý do này).
- Mục 3 Vướng mắc & đề xuất cần TGĐ / Công ty quyết: dòng có Đề xuất, chưa Hoàn thành, **Hướng chỉ đạo = "Chuyển thông tin TGĐ" hoặc TRỐNG** (bỏ dòng đã có ý kiến chỉ đạo); không lọc Trọng tâm. 2 nhóm: "Chuyển thông tin TGĐ" (xếp đầu, viền cam) → "Chưa có ý kiến chỉ đạo". Sheet Bao cao GDCN (khối VƯỚNG MẮC & ĐỀ XUẤT, 24 FILTER + COUNTIFS ô A9) lọc y như vậy. ⚠ Việc "Tạm dừng" vẫn được tính (chỉ loại Hoàn thành).
- Mục 3 Trọng tâm tuần: `trongTamTuan==='Có'`, nhóm theo BP — **mỗi việc 1 dòng thu gọn, chữ thường** (`taskBlock(t,{fold:true})` → `<details class="tt-fold">`, bên phải: trạng thái · hạn · phụ trách), bấm mở chi tiết (28/09/2026). Mục 4 Cải tiến: `loaiHD==='Cải tiến'`.
- Mục 6 Cảnh báo & lỗi nhập liệu (tự ẩn khi rỗng): quá hạn, chưa CN, thiếu/trùng mã, hạn dạng chữ, có Ngày HT mà chưa Hoàn thành, TT tháng chưa duyệt, TCT giao thiếu Mã TCT, nhiệm vụ TCT chưa BP nào triển khai.
- Nhận định: lấy dòng cùng tuần/năm với mốc; các tuần khác vào "Nhận định các tuần khác".
- Chi nhánh đang chọn nhớ ở `localStorage` key `_bctBranch`.

### Số HĐ ký tự động trong khối BH-000 (thêm 26/09/2026)

- Câu "Hợp đồng" (câu 1) của khối Bán hàng tự gắn thêm dòng số liệu (nền xanh nhạt `.nd-auto`): số HĐ tuần này (+/− so với tuần trước), tuần trước, TB 4 tuần trước, luỹ kế tháng — **trưởng BP chỉ cần viết đánh giá + lý do**, không tự đếm. Hiện cả khi khối BH chưa viết, và ở các tuần trong lịch sử.
- Nguồn: `data.json` (cùng nguồn tab "BC Bán hàng"), đếm bản ghi theo `ngayDatCoc` + `nguonKhach` = mã chi nhánh (`branchCode()`: tên ô B3 bỏ dấu → NT/ĐL/PY/PR/BL/ĐN; nhận cả tên có chữ thêm như "CN Đà Lạt" và mã viết tắt "ĐL" — dùng chung cho tiêu đề cột biểu đồ HĐ và bảng báo bán, 27/09/2026). Chỉ cần `data.json` (mọi HĐ 2026 nằm ở đây, `data_history.json` chỉ tới 2025). Tải lỗi thì bỏ qua, trang vẫn chạy.
- ⚠ Tuần tính **Chủ nhật → Thứ 7** (`weekBH()`, sao y `isoWeek()` trong `index.html`) để số khớp biểu đồ "Chốt cọc 5 tuần" của tab BC Bán hàng — **khác** tuần báo cáo của trang này (`weekNum()`, Thứ 2 → CN theo Excel `WEEKNUM(,2)`), lệch nhau 1 ngày; số tuần hiển thị thường trùng nhau. Tuần lấy theo ngày mốc báo cáo (`ngayBaoCao || ngayDuLieu`).

## Quy ước trình bày (26/09/2026, theo góp ý người dùng)

- **Toàn trang dùng 1 kiểu khối duy nhất** — kiểu của mục 2 (`.nd-block` + lưới `.nd-qa` "nhãn → nội dung", hàm `qaBlock()`/`taskBlock()`), xếp 2 cột (`.nd-grid`). **Không** dùng lại thẻ màu (tag/pill trạng thái), bảng nhiều cột hay nhiều kiểu định dạng khác nhau cho mục 3–9. Màu chỉ dùng cho điều bất thường: đỏ = quá hạn/thiếu, vàng = chưa cập nhật/nhập sai, cam = Chuyển thông tin TGĐ (viền trên khối + chữ).
- Mục 1 là bảng duy nhất: `table.ov` cố định độ rộng (`table-layout:fixed`), tiêu đề tự xuống dòng, chỉ 8 cột → **không trượt ngang** (đã kiểm tra ở 700/900/1300px). Thêm cột mới phải cân nhắc bỏ cột khác; số chi tiết để ở ô KPI của thẻ chi nhánh.
- Ô KPI thẻ chi nhánh bấm được (khi số > 0) → `jumpTo()` cuộn tới mục tương ứng và tự mở nếu đang thu gọn: Chuyển thông tin TGĐ / Chưa có ý kiến chỉ đạo → mục 3 (`#sec-dexuat`), Quá hạn / Chưa CN → mục 7 (`#sec-canhbao`), Việc đang mở → mục 8, Hoàn thành tháng → mục 9.

## Phân quyền

Key tab `bct`, khoá cấp tab: **chỉ Admin** (25/09/2026, theo yêu cầu người dùng), nút tab ẩn với người khác (xem `_classifyGroup()` trong `index.html`). Không có trong `mobile.html`.
