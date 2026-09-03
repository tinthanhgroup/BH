# CLAUDE.md

Hướng dẫn ngữ cảnh dự án cho Claude Code khi làm việc trong repo này.

## Tổng quan

Hệ thống báo cáo nội bộ của **Hyundai Tín Thanh** (đại lý Hyundai, các chi nhánh Nha Trang/Đà Lạt/Phú Yên/Phan Rang/Bảo Lộc). Kiến trúc: **file HTML tĩnh host trên GitHub Pages** tự `fetch()` dữ liệu từ các file `.json` trong cùng repo, dữ liệu các file `.json` đó được **Google Apps Script** (`.gs`, chạy trên Google Sheet nội bộ của công ty, KHÔNG nằm trong repo này) tự động đồng bộ và push lên GitHub qua Contents API theo lịch (trigger).

**Không có backend/server riêng.** Toàn bộ "logic server" nằm ở các script `.gs` chạy trên hạ tầng Google Apps Script — không sửa được trực tiếp qua Git, phải copy-paste thủ công vào trình soạn thảo Apps Script (script.google.com) rồi chạy tay 1 lần để áp dụng.

- **Repo GitHub:** `tinthanhgroup/BH`, nhánh `main`, host qua GitHub Pages.
- **Ngôn ngữ:** toàn bộ code, comment, tên biến tiếng Việt có dấu (kể cả trong string literal, key object) — đây là chủ ý, không phải lỗi encoding. Giữ nguyên convention này khi sửa/thêm code.
- **Không có build step.** Tất cả file `.html` là single-file, JS/CSS inline, không bundler, không npm. Sửa xong là dùng được ngay.

## File HTML (đều ở root repo, host qua GitHub Pages)

| File | Vai trò | Auth |
|---|---|---|
| `index.html` | Trang chính, desktop. 7 tab: 1.Hàng hóa · 2.BC Bán hàng · 3.BC Dịch vụ · 4.BC Nhân sự · 5.Chấm công (ẩn, chỉ Admin) · 6.MKT Thương hiệu · 7.Chính sách HTV (mở tự do) + tab Admin. Tab 1 (Hàng hóa) có mục chi tiết riêng bên dưới | Từng tab khoá riêng theo mật khẩu + nhóm quyền (xem bên dưới) |
| `mobile.html` | Bản rút gọn cho di động, KCK_DATA/KCK_DATE trùng với index.html — **sửa 1 nơi thì nhớ sửa nơi kia** | — |
| `chamcong.html` | Báo cáo chấm công chi tiết, nhúng iframe trong tab 5. Chi tiết ở mục "Kiến trúc dữ liệu Chấm công" bên dưới | Không tự có auth — được `index.html` gác cổng trước khi load iframe |
| `nhansu.html` | Báo cáo nhân sự chi tiết, nhúng iframe trong tab 4. Chi tiết cấu trúc dữ liệu ở mục "Kiến trúc dữ liệu Nhân sự" bên dưới | Không tự có auth (như trên) |
| `mkt.html` | Báo cáo tổng hợp MKT Thương hiệu (Nhật ký đăng bài Fanpage), nhúng iframe trong tab 6. Chi tiết ở mục "Kiến trúc dữ liệu MKT Thương hiệu" bên dưới | Không tự có auth (như trên) |
| `theo_doi_chinh_sach.html` | Tra cứu chính sách HTV theo hiệu lực áp dụng, nhúng iframe trong tab 7. Chi tiết ở mục "Kiến trúc theo_doi_chinh_sach.html" bên dưới | **Không khoá** — mở cho mọi nhóm kể cả chưa đăng nhập |

Các trang nhúng iframe (`chamcong/nhansu/mkt/theo_doi_chinh_sach.html`) đều **lazy-load** — chỉ set `iframe.src` khi người dùng thực sự bấm vào tab đó lần đầu (xem `_doSwitchTab()` trong `index.html`), tránh tải dữ liệu thừa.

## File Apps Script (`.gs` — KHÔNG deploy tự động, phải copy tay)

Các file này **không nằm cùng 1 Apps Script project** — mỗi file (trừ AutoSync) tự chứa `CONFIG`/`GITHUB_CONFIG` riêng (token, owner, repo, branch) để không phụ thuộc biến toàn cục của file khác. Lý do lịch sử: từng bị lỗi `CONFIG is not defined` khi tách file ra project khác nhau.

| File | Việc làm | Trigger | Ghi vào |
|---|---|---|---|
| `TinThanh_AutoSync.gs` | Đồng bộ Sheet "DATA TỔNG" → `data.json`. Chứa cả `weeklySnapshot()` + `monthlySnapshot()` | `autoSync`: 1 trigger `everyHours(1)` (24/7) · `weeklySnapshot`: Thứ 6 21h · `monthlySnapshot`: hàng ngày 21h (tự bỏ qua nếu chưa phải ngày cuối tháng — xem `isLastDayOfMonth_()`) | `data.json`, `data_weekly.json`, `data_monthly.json` |
| `TinThanh_ChamCong_Sync.gs` | Đọc Excel chấm công (admin từng chi nhánh tự upload lên Drive) → `chamcong.json` | 9h & 10h hàng ngày | `chamcong.json` |
| `TinThanh_NhanSu_Sync.gs` | Đọc Sheet Nhân sự (2 tab: đang làm / đã nghỉ) → `nhansu.json` | 12h hàng ngày | `nhansu.json` |
| `TinThanh_MKT_Sync.gs` | Đọc Sheet "Nhật ký đăng bài" Fanpage → `mkt.json` | `mktSync`: 1 trigger `everyHours(4)` (~6 lần/ngày) | `mkt.json` |
| `TinThanh_Auth.gs` | `checkPassword()` xác thực mật khẩu + phân quyền, deploy riêng thành **Web App** (`AUTH_URL` gọi từ `index.html`) | — (gọi qua HTTP, không có trigger) | — |

### ⚠️ Bẫy quan trọng khi sửa `.gs`

1. **`TinThanh_Auth.gs` là Web App** — sửa code xong **PHẢI** vào Deploy → Manage deployments → **New version** thì mới có hiệu lực. Chỉ bấm Save (Ctrl+S) sẽ KHÔNG cập nhật URL đang chạy.
2. **Giới hạn ~20 trigger/project của Google Apps Script.** Đã từng bị lỗi "This script has too many triggers" 2 lần (do tạo vòng lặp N trigger cố định từng giờ thay vì 1 trigger lặp). Quy tắc bắt buộc từ giờ: **luôn dùng `.everyHours(n)` (1 trigger lặp)**, KHÔNG BAO GIỜ dùng vòng lặp `for` gọi `.atHour(h).create()` nhiều lần để giả lập chạy nhiều lần/ngày.
3. Mỗi file `.gs` khi gửi cho người dùng đều chỉ có **placeholder** cho Token/Sheet ID/Folder ID (`"NHAP_GITHUB_TOKEN"` v.v.) — luôn nhắc người dùng tự điền giá trị thật trước khi chạy, không tự bịa giá trị.
4. Muốn cài trigger mới/xoá trigger cũ: mỗi file đều có sẵn cặp hàm `setupXxxTrigger()` / `removeXxxTrigger()` tự dọn trigger cũ (theo tên hàm) trước khi tạo mới — không tự viết `ScriptApp.newTrigger()` tay, gọi đúng các hàm có sẵn.
5. Muốn test snapshot cuối tuần/cuối tháng ngay lập tức (không đợi đúng ngày): dùng `testWeeklySnapshot()` / `testMonthlySnapshot()` — các hàm này **bỏ qua điều kiện ngày**, gọi thẳng phần lõi.

## Cơ chế snapshot lịch sử ("chốt sổ" theo tuần/tháng)

Mục "Xe chưa xuất hóa đơn" (tab Hàng hóa) cần xem lại được **trạng thái tại 1 thời điểm trong quá khứ** (chốt cuối tuần Thứ 6, chốt cuối tháng), không chỉ xem dữ liệu real-time. Cơ chế:

- `data_weekly.json` / `data_monthly.json`: mỗi file là 1 mảng `snapshots[]`, mỗi phần tử = 1 lần chốt, chỉ lưu phần **"chưa xuất hồ sơ"** đã lọc + rút gọn field (không lưu nguyên toàn bộ tồn kho) — để tránh vượt 1MB (giới hạn GitHub Contents API trả `content` base64 trực tiếp; vượt 1MB phải đọc qua `download_url`).
- Frontend (`index.html`) có 2 dropdown độc lập "Tuần"/"Tháng" — chọn cái này tự bỏ chọn cái kia (`hhSelectWeek()` / `hhSelectMonth()`), dùng chung hàm `buildChuaHDFromRecords()` để dựng lại bảng từ snapshot.
- Muốn thêm 1 loại snapshot mới (vd theo quý): nhân bản đúng pattern `weeklySnapshot`/`monthlySnapshot` (backend) + `hhSelectWeek`/`hhSelectMonth` (frontend), tái sử dụng `buildChuaHDSnapshot_()` ở backend để đảm bảo các loại snapshot luôn khớp field với nhau.

## Kiến trúc dữ liệu Chấm công (`TinThanh_ChamCong_Sync.gs` → `chamcong.json` → `chamcong.html`)

⚠️ **Khác với NhanSu_Sync/MKT_Sync**: file này **KHÔNG tự chứa CONFIG riêng** — bắt buộc phải dán vào **cùng Apps Script project** với `TinThanh_AutoSync.gs` để dùng chung biến `CONFIG.GITHUB_TOKEN/OWNER/REPO/BRANCH` (không tách project được như 2 file kia).

**Nguồn dữ liệu:** 1 folder Drive gốc (`DRIVE_FOLDER_ID`), bên trong là các **folder con = tên chi nhánh** (VD "Nha Trang", "Phú Yên"...). Admin mỗi chi nhánh tự thả file `.xlsx` chấm công vào đúng folder mình mỗi ngày, không cần đặt tên cố định — script luôn lấy file `.xlsx` **sửa gần nhất** trong mỗi folder. Thêm chi nhánh mới chỉ cần tạo thêm 1 folder con, không cần sửa code.

**Ghi đè theo từng chi nhánh riêng biệt** (không ghi đè toàn bộ `chamcong.json` mỗi lần chạy): nếu 1 chi nhánh không có file mới trong lần chạy này, dữ liệu cũ của chi nhánh đó trong `chamcong.json` vẫn được giữ nguyên — chỉ các chi nhánh vừa đọc được file mới mới bị thay thế.

**Mỗi bản ghi (1 người × 1 ngày) có các field chính:** `manv, ten, phong, chinhanh, ngay (ISO), thu, punches[] (danh sách giờ quẹt, đã sort), sopunch, giovao, giora, sogiolam, cocong (bool), thieucham (bool, true nếu chỉ quẹt đúng 1 lần), giovao_sang, giora_trua, giovao_chieu, giora_cuoi, co_chieu` — cộng thêm field nối từ Nhân sự: `phong_hr, to_hr, khoi_hr, capbac_hr, chucvu_hr, ten_hr, daNghiViec, manv_goc`.

**Join với Nhân sự — CÓ 2 LỚP xử lý trùng Mã NV, đừng nhầm là dư thừa nhau:**
1. **Lớp server (`.gs`, hàm `joinWithNhanSu_()`):** fetch thẳng `nhansu.json` (`JOIN_NHANSU=true`), match theo `Mã NV + Chi nhánh` trước, rơi về `Tên + Chi nhánh` nếu thiếu mã. Mã NV phụ (cột "Mã NV phụ" khai báo tường minh trong Sheet Nhân sự, xem mục Nhân sự phía trên) được trỏ về cùng 1 hồ sơ ở bước này — **đây là cách gộp chắc chắn, dựa trên khai báo tường minh của HR**. Nếu người đã nghỉ việc (có trong `turnover`) và ngày chấm công sau ngày nghỉ → **loại bỏ dòng đó** (không tính là vắng nhầm sau khi đã nghỉ).
2. **Lớp client (`chamcong.html`, hàm `mergeDuplicateManv_()`):** xử lý các trường hợp HR **chưa kịp khai báo** Mã NV phụ. Heuristic: gom theo cùng Tên+Chi nhánh, mã nào có số ngày công ≤ `max(2, 15% số ngày công của mã nhiều nhất)` bị coi là "mã bóng ma" (vân tay phụ ít dùng) và tự gộp vào mã chính. Nếu ≥2 mã đều có nhiều ngày công gần nhau → **không tự gộp**, đẩy vào bảng "🔁 Cần kiểm tra thủ công" để người dùng tự xác nhận (rất có thể là 2 người trùng tên thật, không phải cùng 1 người).

**3 bảng chẩn đoán cuối `chamcong.html`** (đều dropdown, mặc định đóng khi không có gì bất thường):
- **"⚠ Nhân sự chưa khớp với dữ liệu Nhân sự"** — có chấm công nhưng không khớp được cả Mã NV lẫn Tên với sheet Nhân sự.
- **"🔁 Cần kiểm tra thủ công"** — nghi trùng vân tay nhưng lớp client không tự tin gộp (xem heuristic ở trên).
- **"👤 Nhân sự đang làm nhưng chưa có dữ liệu chấm công"** — có trong Nhân sự (đang làm) nhưng không thấy trong `chamcong.json`.

## Kiến trúc dữ liệu Nhân sự (`TinThanh_NhanSu_Sync.gs` → `nhansu.json` → `nhansu.html`)

Sheet Nhân sự có **2 tab**, tên tab cố định (script tự nhận diện cột theo *tên chứa từ khoá*, không phụ thuộc thứ tự cột):
- `"Tổng hợp nhân sự"` — toàn bộ nhân sự **đang làm**.
- `"Nhân sự nghỉ việc"` — nhân sự **đã nghỉ**.

Cột cần có trong mỗi tab (tên chứa các từ sau, không phân biệt hoa/thường): *Chi nhánh, Khối, Phòng, Tổ/Nhóm, Cấp bậc, Chức vụ, Họ và tên, Ngày sinh, Giới tính, Ngày nhận việc, Ngày nghỉ việc, Trạng thái, Loại HĐ hiện tại, Ngày hết hạn HĐ, Mức lương đóng BHXH, Số phép năm còn lại, Năm nghỉ việc (ước tính), Mã NV, Mã NV phụ, Địa điểm làm việc.*

⚠️ Đọc bằng `getDisplayValues()` chứ không phải `getValues()` — lý do: giữ đúng số 0 ở đầu Mã NV (VD `"00013"`), tránh bị Google Sheet tự hiểu thành số `13` nếu cột lỡ định dạng Số thay vì Văn bản.

**`nhansu.json` có 4 mảng, `nhansu.html` load thẳng theo tên này** (biến JS tương ứng trong ngoặc):
| Mảng | Sinh ra khi nào | Biến trong `nhansu.html` |
|---|---|---|
| `data` | Mỗi dòng có tên ở tab "Tổng hợp nhân sự" | `DATA` |
| `turnover` | Người có "Ngày nghỉ việc" điền sẵn ở tab "Tổng hợp" (dù chưa được HR dọn qua tab nghỉ việc) **CỘNG** toàn bộ tab "Nhân sự nghỉ việc" | `TURNOVER` |
| `hires` | Người có Ngày nhận việc rơi vào năm hiện tại (`hireYear`) | `HIRES` |
| `hdHetHan` | Người có Ngày hết hạn HĐ trong vòng `HD_CANH_BAO_NGAY` (mặc định 45 ngày, gồm cả HĐ đã hết hạn rồi — `songay` âm) | `HD_HET_HAN` |

**Field trong mỗi object của `data`**: `ten, manv, manv_phu[], chinhanh, diadiem, khoi, phong, to, sx, capbac, chucvu, gioitinh, tuoi, thamnien, trangthai, luong, phepconlai, loaihd, ngayvao, ngaynghi`.

**Logic đáng chú ý:**
- `diadiem` (địa điểm làm việc thực tế, khác `chinhanh` sổ sách) — hàm `resolveDiaDiem_()`: ưu tiên đọc thẳng cột "Địa điểm làm việc" (map qua `DIADIEM_CODE_MAP_`: NT/PY/ĐL/DL/BL/PR), nếu trống thì suy luận theo Tổ chứa "Phan Rang" → Phan Rang, Phòng chứa hậu tố "BL" (regex `\bBL\b`) → Bảo Lộc, cuối cùng fallback về `chinhanh`.
- `manv_phu` (Mã NV phụ, hỗ trợ người có 2 mã chấm công/2 vân tay) — 1 chuỗi tách bằng dấu phẩy/chấm phẩy/khoảng trắng thành mảng (`parseManvPhu_()`).
- `thamnien` = số năm làm việc tính tới hiện tại, làm tròn 1 chữ số thập phân.
- Có hàm `baoCaoTrungMaNV_()` tự rà soát Mã NV bị trùng (cùng Mã NV + Chi nhánh xuất hiện ≥2 người) — chỉ log cảnh báo ra Apps Script log, KHÔNG chặn sync, HR tự sửa tay trong Sheet.
- 1 người có thể xuất hiện ở **cả `data` lẫn `turnover`** (từng nghỉ rồi vào lại) — 2 mảng không loại trừ nhau, đừng giả định `turnover` chỉ chứa người *không* có trong `data`.

**`nhansu.html`** tự tính thêm ở phía client (không nằm trong JSON): 4 KPI "Tình hình nhân sự" (Nghỉ việc/Tuyển mới — tháng trước/tháng này, tự tính theo ngày hệ thống — **không** gán cứng tháng), khối "Biến động" dùng cửa sổ **45 ngày gần nhất** (không phải top-10 cố định), bảng "Danh sách hợp đồng hết hạn" hiển thị thẳng từ `HD_HET_HAN`.

## Kiến trúc dữ liệu MKT Thương hiệu (`TinThanh_MKT_Sync.gs` → `mkt.json` → `mkt.html`)

**Nguồn:** 1 sheet **dùng chung** ("Nhật ký đăng bài") cho 3 chi nhánh Nha Trang/Phú Yên/Đà Lạt (⚠️ **chỉ 3 chi nhánh — KHÁC danh sách 5 kho hàng hóa** NT/ĐL/PY/PR/BL, xem thêm mục "khái niệm hay nhầm" bên dưới). Mỗi dòng = 1 bài đăng ký kế hoạch, KHÔNG có sheet chỉ tiêu riêng: **chỉ tiêu = số dòng đăng ký, thực hiện = số dòng có Trạng thái chứa "đăng"** (chuẩn hoá bỏ dấu, không phân biệt hoa/thường qua `stripDiacritics_MKT_()` — ⚠️ hàm này có bug lịch sử: thiếu bước đổi `đ`→`d` khiến "đăng" không bao giờ khớp "dang", đã vá, nhớ giữ đủ bước `.replace(/đ/g,'d').replace(/Đ/g,'d')` nếu viết lại hàm tương tự ở đâu khác).

**Header sheet dò tự động** (quét 10 hàng đầu tìm cột tên đúng "Chi nhánh") — không phụ thuộc số hàng ghi chú phía trên. Cột: *STT, Chi nhánh, Tuần, Từ ngày, Đến ngày, Mã HM, Nhóm (Thương hiệu/HTV/Sản phẩm...), Hạng mục, Ngày đăng thực tế, Trạng thái, Link bài đăng*.

**Mỗi object trong `records.posts[]`**: `stt, chinhanh, tuan, tuNgay, denNgay, maHM, nhom, hangMuc, ngayDangThucTe, trangThai, daDang (bool), link, treHan (bool)`. `treHan` tính sẵn ở backend = `!daDang && denNgay đã qua`.

**`mkt.html` có 4 bảng, đều đánh số** (2 bảng đầu gộp chung 1 hàng qua CSS `.grid2`):
1. **Thực hiện theo Chi nhánh** — theo bộ lọc đang chọn.
2. **Theo Tuần** — kiểm tra quy định tối thiểu 1 bài/tuần/chi nhánh, mỗi ô hiện `đã đăng/đăng ký` (VD "1/3"), cột "Cảnh báo" chỉ dựa trên **đăng ký** (kế hoạch) chứ không dựa trên đã đăng.
3. **Độ phủ hạng mục theo Chi nhánh** — ma trận Mã HM × Chi nhánh, cùng format `đã đăng/đăng ký`.
4. **Chi tiết bài đăng** — sắp xếp 4 tầng: Trễ hạn lên đầu → Từ ngày (parse thật `dd/MM/yyyy` thành số `yyyyMMdd` để so sánh, **không dùng `localeCompare` trên chuỗi ngày** — lỗi sắp sai thứ tự tháng đã từng gặp) → Chi nhánh → Mã HM.

**Bảng 2 và 3 CỐ Ý không áp dụng bộ lọc phía trên** (Chi nhánh/Tuần/Trạng thái) — vì mục đích là kiểm tra **kế hoạch tổng thể**, lọc theo tuần sẽ làm mất ý nghĩa "đủ hạng mục"/"đủ tần suất".

**Trigger `mktSync`: 1 trigger duy nhất `everyHours(4)`** (từng bị lỗi "too many triggers" y hệt AutoSync — xem mục Bẫy phía trên, đã sửa cùng cách).

## Kiến trúc `theo_doi_chinh_sach.html` (Chính sách HTV)

**KHÔNG có backend/Apps Script/Google Sheet nào** — đây là file **tĩnh hoàn toàn**, dữ liệu hardcode ngay trong mảng JS `policies` (xem "Quy trình cập nhật dữ liệu thủ công" bên dưới để biết cách thêm chính sách mới). Quyết định có chủ đích: tần suất thêm chính sách thấp (vài lần/năm), không đáng để dựng cả pipeline Sheet→Apps Script→JSON chỉ cho việc này.

**Field mỗi object trong `policies[]`:** `pdfUrl` (link Google Drive, share công khai — KHÔNG nhúng base64, từng làm khiến file nặng >6MB), `category` (dùng để lọc, hiện chỉ có `"Bán hàng - Marketing"`), `code`, `name`, `updated`, `from`/`to` (kiểu `Date`, `to: null` = còn hiệu lực vô thời hạn), `fromLabel`/`toLabel` (chuỗi hiển thị, phải tự khớp tay với `from`/`to` — không tự sinh ra từ Date), `summary` (object gồm các mục tóm tắt nội dung).

**`const TODAY = new Date();`** — PHẢI luôn là ngày thực tế lúc mở file, **TUYỆT ĐỐI không gán cứng** kiểu `new Date(2026,7,31)` (đã từng bị lỗi này — trạng thái Active/Inactive sẽ sai vĩnh viễn kể từ ngày cụ thể đó nếu gán cứng).

**Logic Active/Inactive:** `TODAY >= p.from && (p.to === null || TODAY <= p.to)`. Danh sách sort: Active lên trước Inactive, trong mỗi nhóm sort theo `from` giảm dần (mới nhất lên đầu). Số thứ tự (`stt`) đánh theo **thứ tự hiển thị sau sort**, không phải thứ tự khai báo trong mảng — tự động đổi khi 1 chính sách chuyển Active↔Inactive.

**Bộ lọc `category`** (nút bấm dạng pill) **tự ẩn** nếu tất cả policy chỉ thuộc đúng 1 category — chỉ hiện khi có ≥2 category khác nhau trong mảng, tránh giao diện rối khi chưa cần.

## Tab Hàng hóa — mục "Xe chưa xuất hóa đơn" (`index.html`, hàm `renderChuaHD()`)

Đây là phần logic phức tạp nhất trong `index.html`, đáng có ghi chú riêng dù không tách file riêng:

- **Nguồn `allData.chuaHD`**: lọc từ dữ liệu tồn kho sống, điều kiện gốc `kho!=='' && soKhung!=='' && !r.xuatHoSo && r.ttHoSo && !r.fromHistory && daly in (NT,ĐL,PY)`. Field mỗi dòng: `model, phienBan, mau, kho, soKhung, daly, tvbh, giaVon, ngayBan, baoCaoDMS, nguonKhach, khach, xuatHoSo` (2 field cuối lưu tường minh dù luôn `false` trong mảng này — để bảng con phía dưới filter lại được mà không phải đoán ngầm).
- **Bảng cảnh báo "Xe bán ngang chưa xuất hóa đơn"** (đặt TRÊN bảng tổng hợp theo Model): lọc `nguonKhach.trim().toLowerCase()==='khác' && !xuatHoSo` — 2 điều kiện đều bắt buộc, viết tường minh cả 2 dù về lý thuyết `!xuatHoSo` đã đúng sẵn vì cả mảng cha đều vậy (phòng trường hợp sau này đổi nguồn dữ liệu). Có dòng thông báo "✅ Không có xe bán ngang nào bị sót" khi rỗng — đừng để trống không hiện gì.
- **Snapshot Tuần/Tháng** dùng `buildChuaHDFromRecords()` để dựng lại đúng các field trên từ dữ liệu lịch sử — xem mục "Cơ chế snapshot lịch sử" phía trên. Nếu thêm field mới vào `allData.chuaHD`, nhớ thêm cả vào hàm này (backend `.gs`) lẫn `buildChuaHDFromRecords()` (frontend) để snapshot cũ/mới nhất quán.
- **Nút "📊 Xuất đặt hàng"** (`exportOrderExcel()`, header trang): xuất Excel 2 sheet (Chi tiết xe tồn + Chi tiết xe nợ) theo đúng bộ lọc Kho/Năm SX đang chọn, dùng thư viện SheetJS nạp qua CDN (`xlsx.full.min.js`) — thư viện này còn được đoạn code cũ `uploadKCK()`/xử lý file thủ công dùng chung, đừng gỡ nếu không kiểm tra kỹ các chỗ dùng khác.

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
4. Repo này chạy trong VS Code với Claude Code, có git access trực tiếp qua Bash/PowerShell. Khi sửa xong file `.html`, có thể tự `git add`/`git commit`/`git push` lên `origin/main` nếu người dùng yêu cầu — GitHub Pages tự deploy lại sau khi push, không cần bước thủ công nào thêm. **Không tự ý push** khi chưa được người dùng xác nhận trong phiên làm việc đó.
