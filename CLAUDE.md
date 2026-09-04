# CLAUDE.md

Hướng dẫn ngữ cảnh dự án cho Claude Code khi làm việc trong repo này.

## Tổng quan

Hệ thống báo cáo nội bộ của **Hyundai Tín Thanh** (đại lý Hyundai, các chi nhánh Nha Trang/Đà Lạt/Phú Yên/Phan Rang/Bảo Lộc). Kiến trúc: **file HTML tĩnh host trên GitHub Pages** tự `fetch()` dữ liệu từ các file `.json` trong cùng repo, dữ liệu các file `.json` đó được **Google Apps Script** (`.gs`, chạy trên Google Sheet nội bộ của công ty) tự động đồng bộ và push lên GitHub qua Contents API theo lịch (trigger).

**Không có backend/server riêng.** Toàn bộ "logic server" nằm ở các script `.gs` chạy trên hạ tầng Google Apps Script — không sửa được trực tiếp qua Git, phải copy-paste thủ công vào trình soạn thảo Apps Script (script.google.com) rồi chạy tay 1 lần để áp dụng. Bản sao các file `.gs` được lưu local ở `apps-script/` (xem mục riêng bên dưới) để tiện chỉnh sửa, nhưng **không đại diện cho bản đang chạy thật** cho tới khi bạn tự dán lại vào script.google.com.

- **Repo GitHub:** `tinthanhgroup/BH`, nhánh `main`, host qua GitHub Pages.
- **Ngôn ngữ:** toàn bộ code, comment, tên biến tiếng Việt có dấu (kể cả trong string literal, key object) — đây là chủ ý, không phải lỗi encoding. Giữ nguyên convention này khi sửa/thêm code.
- **Không có build step.** Tất cả file `.html` là single-file, JS/CSS inline, không bundler, không npm. Sửa xong là dùng được ngay.

## File HTML (đều ở root repo, host qua GitHub Pages)

| File | Vai trò | Auth | Chi tiết |
|---|---|---|---|
| `index.html` | Trang chính, desktop. 7 tab: 1.Hàng hóa · 2.BC Bán hàng · 3.BC Dịch vụ · 4.BC Nhân sự · 5.Chấm công (ẩn, chỉ Admin) · 6.MKT Thương hiệu · 7.Chính sách HTV (mở tự do) + tab Admin | Từng tab khoá riêng theo mật khẩu + nhóm quyền (xem bên dưới) | Tab Hàng hóa → [docs/module-hanghoa.md](docs/module-hanghoa.md) |
| `mobile.html` | Bản rút gọn cho di động, KCK_DATA/KCK_DATE trùng với index.html — **sửa 1 nơi thì nhớ sửa nơi kia** | — | — |
| `chamcong.html` | Báo cáo chấm công chi tiết, nhúng iframe trong tab 5 | Không tự có auth — được `index.html` gác cổng trước khi load iframe | [docs/module-chamcong.md](docs/module-chamcong.md) |
| `nhansu.html` | Báo cáo nhân sự chi tiết, nhúng iframe trong tab 4 | Không tự có auth (như trên) | [docs/module-nhansu.md](docs/module-nhansu.md) |
| `mkt.html` | Báo cáo tổng hợp MKT Thương hiệu (Nhật ký đăng bài Fanpage), nhúng iframe trong tab 6 | Không tự có auth (như trên) | [docs/module-mkt.md](docs/module-mkt.md) |
| `theo_doi_chinh_sach.html` | Tra cứu chính sách HTV theo hiệu lực áp dụng, nhúng iframe trong tab 7 | **Không khoá** — mở cho mọi nhóm kể cả chưa đăng nhập | [docs/module-chinhsach.md](docs/module-chinhsach.md) |

Các trang nhúng iframe (`chamcong/nhansu/mkt/theo_doi_chinh_sach.html`) đều **lazy-load** — chỉ set `iframe.src` khi người dùng thực sự bấm vào tab đó lần đầu (xem `_doSwitchTab()` trong `index.html`), tránh tải dữ liệu thừa.

**Mở file `docs/module-*.md` tương ứng khi thực sự đang sửa module đó** — các file này không tự động nạp vào context, chỉ đọc khi cần để đỡ tốn context cho các module không liên quan.

## File Apps Script (`.gs` — KHÔNG deploy tự động, phải copy tay)

Bản sao lưu ở thư mục local `apps-script/` (đã thêm vào `.gitignore`, **không** push lên GitHub vì chứa token/ID thật). Các file này **không nằm cùng 1 Apps Script project** — mỗi file (trừ ChamCong_Sync) tự chứa `CONFIG`/`GITHUB_CONFIG` riêng (token, owner, repo, branch) để không phụ thuộc biến toàn cục của file khác. Lý do lịch sử: từng bị lỗi `CONFIG is not defined` khi tách file ra project khác nhau.

| File | Việc làm | Trigger | Ghi vào |
|---|---|---|---|
| `TinThanh_AutoSync.gs` | Đồng bộ Sheet "DATA TỔNG" → `data.json`. Chứa cả `weeklySnapshot()` + `monthlySnapshot()` | `autoSync`: 1 trigger `everyMinutes(30)` (24/7) · `weeklySnapshot`: Thứ 6 21h · `monthlySnapshot`: hàng ngày 21h (tự bỏ qua nếu chưa phải ngày cuối tháng — xem `isLastDayOfMonth_()`) | `data.json`, `data_weekly.json`, `data_monthly.json` |
| `TinThanh_ChamCong_Sync.gs` | Đọc Excel chấm công (admin từng chi nhánh tự upload lên Drive) → `chamcong.json` — chi tiết [docs/module-chamcong.md](docs/module-chamcong.md) | 9h & 10h hàng ngày | `chamcong.json` |
| `TinThanh_NhanSu_Sync.gs` | Đọc Sheet Nhân sự (2 tab: đang làm / đã nghỉ) → `nhansu.json` — chi tiết [docs/module-nhansu.md](docs/module-nhansu.md) | 12h hàng ngày | `nhansu.json` |
| `TinThanh_MKT_Sync.gs` | Đọc Sheet "Nhật ký đăng bài" Fanpage → `mkt.json` — chi tiết [docs/module-mkt.md](docs/module-mkt.md) | `mktSync`: 1 trigger `everyHours(4)` (~6 lần/ngày) | `mkt.json` |
| `TinThanh_Auth.gs` | `checkPassword()` xác thực mật khẩu + phân quyền, deploy riêng thành **Web App** (`AUTH_URL` gọi từ `index.html`) | — (gọi qua HTTP, không có trigger) | — |

### ⚠️ Bẫy quan trọng khi sửa `.gs`

1. **`TinThanh_Auth.gs` là Web App** — sửa code xong **PHẢI** vào Deploy → Manage deployments → **New version** thì mới có hiệu lực. Chỉ bấm Save (Ctrl+S) sẽ KHÔNG cập nhật URL đang chạy.
2. **Giới hạn ~20 trigger/project của Google Apps Script.** Đã từng bị lỗi "This script has too many triggers" 2 lần (do tạo vòng lặp N trigger cố định từng giờ thay vì 1 trigger lặp). Quy tắc bắt buộc từ giờ: **luôn dùng `.everyHours(n)` (1 trigger lặp)**, KHÔNG BAO GIỜ dùng vòng lặp `for` gọi `.atHour(h).create()` nhiều lần để giả lập chạy nhiều lần/ngày.
3. Mỗi file `.gs` khi gửi cho người dùng đều chỉ có **placeholder** cho Token/Sheet ID/Folder ID (`"NHAP_GITHUB_TOKEN"` v.v.) — luôn nhắc người dùng tự điền giá trị thật trước khi chạy, không tự bịa giá trị. Với bản sao trong `apps-script/` (local, gitignored) có thể chứa giá trị thật nếu người dùng tự dán vào — không tự ý xóa/thay giá trị thật đó trừ khi được yêu cầu.
4. Muốn cài trigger mới/xoá trigger cũ: mỗi file đều có sẵn cặp hàm `setupXxxTrigger()` / `removeXxxTrigger()` tự dọn trigger cũ (theo tên hàm) trước khi tạo mới — không tự viết `ScriptApp.newTrigger()` tay, gọi đúng các hàm có sẵn.
5. Muốn test snapshot cuối tuần/cuối tháng ngay lập tức (không đợi đúng ngày): dùng `testWeeklySnapshot()` / `testMonthlySnapshot()` — các hàm này **bỏ qua điều kiện ngày**, gọi thẳng phần lõi.

## Quy ước giao diện chung giữa các trang module

`chamcong.html`, `nhansu.html`, `mkt.html`, `theo_doi_chinh_sach.html` là 4 trang độc lập (nhúng iframe riêng trong `index.html`), **không dùng chung 1 file CSS** — mỗi file tự khai báo style riêng, nên rất dễ bị lệch nhau khi sửa từng file một cách rời rạc (đã từng xảy ra: `theo_doi_chinh_sach.html` có header nhỏ hơn và màu đen thay vì xanh navy như 3 file kia). Khi sửa hoặc thêm màu/size cho tiêu đề `<h1>` đầu trang, nhân bản đúng theo chuẩn hiện có thay vì tự chọn giá trị mới:
- Màu tiêu đề: `--navy:#1B4F8C` (khai báo trong `:root` của từng file — nếu file chưa có biến này, thêm vào thay vì hardcode hex lặp lại).
- Cỡ chữ: `32px`, `font-weight:800` (riêng `mkt.html` đang dùng `28px`, không phải lỗi — chỉ là chưa đồng bộ, có thể nâng lên `32px` nếu được yêu cầu chỉnh đồng bộ toàn bộ).

## Hệ thống phân quyền (`index.html`)

- Nguồn: cột "Nhóm" trong Sheet Users, nhận diện theo **từ khoá chứa trong chuỗi** (không cần khớp tuyệt đối, không phân biệt hoa/thường/dấu — xem `_stripDiacritics()`), hoặc cờ `isAdmin=TRUE`.
- Bảng quyền (hàm `_classifyGroup()`):

  | Nhóm (chứa từ khoá) | Xem được tab |
  |---|---|
  | Admin (`isAdmin=TRUE`) | Tất cả (Bán hàng, Dịch vụ, Nhân sự, Chấm công, MKT) |
  | `gd` / `giam doc` | Bán hàng, Dịch vụ, Nhân sự, MKT (không Chấm công) |
  | `hcns` / `nhan su` | Chỉ Nhân sự |
  | `ban hang` / `mkt` / `marketing` | Bán hàng, MKT |
  | `dich vu` | Chỉ Dịch vụ |
  | Không khớp gì | Không xem được tab khoá nào (an toàn theo mặc định) |

- Đây là khoá **client-side** (ẩn hiển thị), KHÔNG phải bảo mật dữ liệu thật — dữ liệu vẫn tải hết về trình duyệt. Không tự ý nâng cấp thành "bảo mật thật" trừ khi được yêu cầu (đổi kiến trúc lớn).
- Mục "Xe chưa xuất HĐ" dùng cờ riêng `_isAdmin`/`_isGD`, **không dùng chung** điều kiện với quyền xem tab Nhân sự (2 thứ này từng bị gộp chung gây lộ dữ liệu ngoài ý muốn cho nhóm HCNS — đã tách hẳn, giữ nguyên tách biệt).

## Vài khái niệm nghiệp vụ hay gây nhầm

- **"Xuất hồ sơ" = "Xuất hóa đơn" = "Xuất HĐ"** — 3 cách gọi cho cùng 1 field/cột (`xuatHoSo` trong code). Không phải 3 khái niệm khác nhau.
- **"Bán ngang"** = bán xe cho đại lý khác (không phải khách lẻ) — nhận diện qua cột "Nguồn khách" = `"Khác"`.
- **Kho hàng hóa** (NT/ĐL/PY/PR/BL — 5 chi nhánh) ≠ **Kho MKT Fanpage** (chỉ NT/PY/ĐL — 3 chi nhánh). Đừng giả định 2 danh sách chi nhánh này giống nhau.
- **KCK** = "Kho hàng sẵn Nhà máy" (Hyundai Thành Công). Cột "sắp hết" nhận diện theo **màu chữ ĐỎ** trong file Excel gốc ở cột "Màu ngoại thất" — **không phải** theo Năm SX (có 1 đoạn code cũ trong nút Admin `uploadKCK()` làm sai theo năm SX, chưa dọn — biết vậy để không nhầm là "đúng chuẩn").
- **Chỉ tiêu tháng** (`DEFAULT_TARGETS` trong `index.html`) là mảng 12 số theo tháng, đây chỉ là **giá trị mặc định**. Người dùng có thể tự sửa qua nút "✏️ Chỉnh sửa chỉ tiêu" → lưu vào `localStorage` riêng máy đó, **ghi đè** `DEFAULT_TARGETS`. Sửa `DEFAULT_TARGETS` trong code không ảnh hưởng người đã từng tự chỉnh tay.

## Quy trình cập nhật dữ liệu thủ công (không qua Apps Script)

- **Chỉ tiêu tháng**: sửa mảng `DEFAULT_TARGETS` trong `index.html`.
- **KCK**: nhận file `KCK.xlsx` → đọc bằng `openpyxl` (không dùng `pandas`, cần đọc **font color** của cell, `pandas` không đọc được) → forward-fill cột "Đặc tả xe"/"Năm SX" (dữ liệu gốc dạng merged-cell) → bỏ dòng "Total"/subtotal → group theo Model+Phiên bản → ghi đè `KCK_DATA`/`KCK_DATE` trong **cả** `index.html` và `mobile.html`. Xử lý trường hợp trùng màu 2 năm SX khác nhau trong cùng Model+Phiên bản: ưu tiên giữ bản ghi có cờ "sắp hết" (không để năm mới ghi đè mất cảnh báo).
- **Chính sách HTV** (`theo_doi_chinh_sach.html`): người dùng gửi PDF → Claude đọc, tóm tắt nội dung chính/thêm/ghi chú → người dùng duyệt lại → thêm object mới vào mảng `policies` (hardcode, KHÔNG dùng Google Sheet — quyết định có chủ đích). PDF gốc để trên Google Drive (link chia sẻ công khai), gán vào field `pdfUrl` — không nhúng base64 (từng làm vậy, khiến file nặng >6MB, đã bỏ).

## Trước khi sửa bất kỳ file nào

1. Luôn kiểm tra cú pháp JS sau khi sửa `<script>` inline trong file `.html` (dùng `node --check`), vì không có type-check/lint tự động nào khác.
2. `index.html` và `mobile.html` có 1 số phần dữ liệu **trùng lặp có chủ đích** (KCK_DATA, KCK_DATE) — sửa 1 file thì luôn kiểm tra có cần sửa file kia không.
3. Nếu thêm 1 script `.gs` mới hoặc thêm trigger mới: nhắc người dùng kiểm tra trang **⏰ Triggers** trước, vì hạn mức ~20 trigger tính chung cả project — không giả định còn chỗ trống.
4. Repo này chạy trong VS Code với Claude Code, có git access trực tiếp qua Bash/PowerShell. Khi sửa xong file `.html`, có thể tự `git add`/`git commit` nếu người dùng yêu cầu — GitHub Pages tự deploy lại sau khi push, không cần bước thủ công nào thêm. **Luôn hỏi xác nhận trước khi `git push`**, kể cả khi trong cùng phiên đã push trước đó.
5. Sửa module nào thì mở đúng file chi tiết trong `docs/` của module đó trước (xem bảng File HTML phía trên) — không cần đọc hết các module khác.
