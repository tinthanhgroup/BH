# Giao diện, tab và phân quyền (`index.html` và các trang module)

Chuyển nguyên văn từ `CLAUDE.md` ngày 15/10/2026. Đọc khi sửa tab/sub-tab, style dùng chung giữa các trang module, hoặc quyền xem.

## Bảng file HTML (đầy đủ)

| File | Vai trò | Auth | Chi tiết |
|---|---|---|---|
| `index.html` | Trang chính, desktop. 7 tab: 1.Hàng hóa · 2.BC Bán hàng · 3.BC Dịch vụ · 4.HCNS (Nhân sự & Chấm công, 4 sub-tab đánh số trong cùng 1 tab) · 5.MKT (2 sub-tab, thêm 09/2026, **đổi tên từ "MKT Thương hiệu" 09/2026**) · 6.Chính sách HTV (mở tự do) · 7.CRM · 8.BC tuần (thêm 25/09/2026) + tab Admin | Từng tab khoá riêng theo mật khẩu + nhóm quyền (xem bên dưới); riêng tab 4 khoá theo **từng sub-tab** (xem chi tiết ngay dưới bảng) | Tab Hàng hóa → [docs/module-hanghoa.md](docs/module-hanghoa.md) |
| `mobile.html` | Bản rút gọn cho di động, KCK_DATA/KCK_DATE trùng với index.html — **sửa 1 nơi thì nhớ sửa nơi kia**. Không có tab 4 (HCNS) lẫn tab 5 (MKT/FB Ads) | — | — |
| `chamcong.html` | Báo cáo chấm công chi tiết, nhúng iframe trong sub-tab "2. Chấm công" của tab 4 | Không tự có auth — được `index.html` gác cổng trước khi load iframe | [docs/module-chamcong.md](docs/module-chamcong.md) |
| `nhansu.html` | Báo cáo nhân sự chi tiết, nhúng iframe trong sub-tab "1. Báo cáo Nhân sự" của tab 4 | Không tự có auth (như trên) | [docs/module-nhansu.md](docs/module-nhansu.md) |
| `nhacungcap.html` | Danh bạ đầu mối Nhà cung cấp sửa chữa/bảo trì CSVC, nhúng iframe trong sub-tab "3. Nhà cung cấp CSVC" của tab 4 | Không tự có auth, nhưng **sub-tab này mở tự do** — xem chi tiết ngay dưới bảng | [docs/module-nhacungcap.md](docs/module-nhacungcap.md) |
| `mkt.html` | Báo cáo tổng hợp Bài viết thương hiệu (Nhật ký đăng bài Fanpage), nhúng iframe trong sub-tab "5.2. Bài viết thương hiệu" của tab 5 | Không tự có auth (như trên) | [docs/module-mkt.md](docs/module-mkt.md) |
| `fbads.html` | Dashboard Quảng cáo Facebook (chi phí, kết quả Messenger, cảnh báo chiến dịch kém hiệu quả), nhúng iframe trong sub-tab "5.1. Quảng cáo Facebook" của tab 5 (thêm 09/2026, **mặc định mở khi vào tab từ 09/2026**) | Không tự có auth (như trên) — dùng chung quyền với sub 5.2, khoá ở cấp tab 5 | [docs/module-fbads.md](docs/module-fbads.md) |
| `theo_doi_chinh_sach.html` | Tra cứu chính sách HTV theo hiệu lực áp dụng, nhúng iframe trong tab 6 | **Không khoá** — mở cho mọi nhóm kể cả chưa đăng nhập | [docs/module-chinhsach.md](docs/module-chinhsach.md) |
| `quy_dinh_noi_bo.html` | Tra cứu quy định/quy chế nội bộ (file Word gốc trên Drive + tóm tắt), nhúng iframe trong sub-tab "4. Quy định nội bộ" của tab 4 (**chuyển từ tab 7 riêng vào đây 09/2026** — không còn là tab cấp cao nhất) | Không tự có auth, nhưng **sub-tab này mở tự do** — xem chi tiết ngay dưới bảng | [docs/module-quydinh.md](docs/module-quydinh.md) |
| `crm.html` | Báo cáo tổng hợp CRM (phễu chuyển đổi lead, nguồn, chi nhánh, nhân viên — dữ liệu từ CRM Bizfly), nhúng iframe trong tab 7 | Không tự có auth (như trên) — dùng chung quyền với tab Bán hàng | [docs/module-crm.md](docs/module-crm.md) |
| `baocaotuan.html` | Báo cáo tuần chi nhánh (quá trình điều hành của GĐCN gửi TGĐ: đề xuất cần quyết, trọng tâm tuần, cải tiến, TCT giao, cảnh báo quá hạn/chưa cập nhật), nhúng iframe trong tab 8 (thêm 25/09/2026). Dữ liệu `baocaotuan.json` — **giai đoạn đầu chuyển tay từ Excel** bằng `Bao cao tuan/xlsx_to_json.ps1`, sau này Google Sheet + Apps Script ghi cùng cấu trúc | Không tự có auth (như trên) — khoá cấp tab key `bct`: **mọi tài khoản đã đăng nhập** (mở cho tất cả 19/10/2026; trước đó Admin + GĐ). Chưa đăng nhập: nút tab ẩn | [docs/module-baocaotuan.md](docs/module-baocaotuan.md) |
| `loinhuan.html` | Bản đồ lợi nhuận gộp từng xe 2026 (mỗi xe một ô vuông tô màu; 2 tab con: theo xe báo bán để rà hỗ trợ HTV, theo xe ký để dự kiến lợi nhuận), mở từ ô KPI "Lợi nhuận gộp" cạnh ô Bảo Lộc ở tab 2 BC Bán hàng, trong cửa sổ nổi `#ln-modal` (thêm 23/10/2026; ban đầu là tab 9 riêng, rồi thẻ 12, cùng ngày chuyển thành ô KPI). Dữ liệu `loinhuan.json` (tạo tay, không tự đồng bộ) | Không tự có auth — `index.html` gác cổng bằng quyền `ln` (chỉ Admin, GĐ/PGĐ, Bán hàng; **MKT không xem**) | [docs/module-loinhuan.md](docs/module-loinhuan.md) |

## Tab 4 (HCNS), tab 5 (MKT) và lazy-load

Tab 4 **nhãn hiển thị là "HCNS"** (đổi tên từ "Nhân sự & Chấm công" 09/2026, xem `_tabLabel()`/`_pwModalTitle()` — nội bộ code vẫn dùng key `ns`, không đổi) — gộp 4 báo cáo vào 1 tab (ban đầu 2 tab riêng "Báo cáo Nhân sự"/"Chấm công", sau đó lần lượt thêm "Nhà cung cấp CSVC" và "Quy định nội bộ" — cái sau vốn là tab 7 riêng, chuyển hẳn vào đây 09/2026). Bên trong dùng "sub-tab" (`.subtabs`/`.subtab`/`.subtab-panel` trong `index.html`, khác với `.tabs`/`.tab` cấp cao nhất), **đánh số 1-4** ngay trong nhãn sub-tab ("1. Báo cáo Nhân sự", "2. Chấm công", "3. Nhà cung cấp CSVC", "4. Quy định nội bộ"), chuyển qua hàm `switchNsSub('ns'|'cc'|'ncc'|'noiquy')`, mỗi sub-tab lazy-load iframe riêng khi mở lần đầu (giống cơ chế lazy-load ở cấp tab).

**Khoá theo từng sub-tab, không khoá cả tab 4** (thêm 09/2026, khác các tab khác): sub 1 "Báo cáo Nhân sự" và sub 2 "Chấm công" vẫn đòi quyền `_allowedTabs.has('ns')` như cũ; sub 3 "Nhà cung cấp CSVC" và sub 4 "Quy định nội bộ" **mở tự do, không cần đăng nhập** — giữ đúng tính chất mở-cho-mọi-người mà "Quy định nội bộ" từng có khi còn là tab 7 riêng. Cơ chế: `switchTab('ns')` không còn chặn ở cấp tab (vào `_doSwitchTab('ns')` thẳng luôn, giống `hh`/`policy`/`admin`); việc kiểm tra quyền chuyển hẳn vào bên trong `switchNsSub(sub, silent)` — nếu `sub` là `ns`/`cc` mà chưa đủ quyền thì chặn (hiện popup mật khẩu nếu `!_authenticated`, hoặc `alert` nếu đã đăng nhập nhưng sai nhóm), còn `ncc`/`noiquy` luôn cho qua. Vào tab 4 lần đầu khi chưa đủ quyền sẽ **tự động rơi vào sub 3** (không tự bật popup mật khẩu — tham số `silent=true` khi `_doSwitchTab` gọi mặc định); chỉ khi người dùng **chủ động bấm** đúng sub 1/2 mới yêu cầu xác thực. `_nsActiveSub` được cập nhật ngay cả khi bị chặn (ghi nhớ ý định), để sau khi nhập đúng mật khẩu, `_doSwitchTab('ns')` mở lại **đúng sub vừa định xem** thay vì luôn rơi về sub 3.

**Tab 5 "MKT" có 2 sub-tab** (thêm 09/2026, cơ chế tương tự tab 4 nhưng đơn giản hơn — **không khoá riêng từng sub**; tab tự đổi tên từ "MKT Thương hiệu" → "MKT" và đảo thứ tự 2 sub-tab 09/2026): "5.1. Quảng cáo Facebook" (`fbads.html`, **mặc định mở khi vào tab** từ 09/2026) và "5.2. Bài viết thương hiệu" (`mkt.html`, tên hiển thị cũ "Nhật ký đăng bài" — nội bộ vẫn dùng key `mkt`), chuyển qua hàm `switchMktSub('mkt'|'fbads')`, mỗi sub lazy-load iframe riêng khi mở lần đầu. Khác tab 4: cả 2 sub dùng chung đúng 1 quyền xem tab `mkt` — `switchTab('mkt')` vẫn khoá ở **cấp tab** như cũ (không đẩy việc kiểm tra quyền xuống từng sub như tab 4), nên `switchMktSub()` không cần tham số `silent`/kiểm tra quyền riêng.

Các trang nhúng iframe (`chamcong/nhansu/nhacungcap/mkt/fbads/theo_doi_chinh_sach/quy_dinh_noi_bo/crm.html`) đều **lazy-load** — chỉ set `iframe.src` khi người dùng thực sự bấm vào tab/sub-tab đó lần đầu (xem `_doSwitchTab()`/`switchNsSub()` trong `index.html`), tránh tải dữ liệu thừa.

## Quy ước giao diện chung giữa các trang module

`chamcong.html`, `nhansu.html`, `mkt.html`, `fbads.html`, `theo_doi_chinh_sach.html`, `quy_dinh_noi_bo.html`, `crm.html` là 7 trang độc lập (nhúng iframe riêng trong `index.html`), **không dùng chung 1 file CSS** — mỗi file tự khai báo style riêng, nên rất dễ bị lệch nhau khi sửa từng file một cách rời rạc (từng xảy ra nhiều lần: header nhỏ hơn/màu khác, `.wrap` rộng khác nhau, số thứ tự mục 2 kiểu khác nhau, màu hover bảng lệch hex — đã rà soát và đồng bộ 09/2026). Khi sửa hoặc thêm style dùng chung giữa các trang này, nhân bản đúng theo chuẩn hiện có thay vì tự chọn giá trị mới:
- Màu tiêu đề: `--navy:#1B4F8C` (khai báo trong `:root` của từng file — nếu file chưa có biến này, thêm vào thay vì hardcode hex lặp lại).
- Tiêu đề `<h1>` đầu trang: cỡ chữ `24px`, `font-weight:800` khi CÓ dùng — nhưng **từ 09/2026, không trang module nào (nhúng iframe) còn `<h1>` nữa**, kể cả trang nhúng thẳng ở tab cấp cao nhất chứ không riêng sub-tab (đổi 09/2026, theo yêu cầu người dùng — ban đầu chỉ bỏ ở 6 sub-tab của tab 4/5 vì trùng nhãn sub-tab, sau đó người dùng yêu cầu bỏ nốt ở `theo_doi_chinh_sach.html`/tab 6 và `crm.html`/tab 7 dù 2 trang này không phải sub-tab — vậy nên coi đây là quy ước áp dụng cho **toàn bộ 8 trang module**, không còn ngoại lệ theo sub-tab/tab nữa). Áp dụng luôn cho module mới thêm sau này — **trang module mới không cần `<h1>` ngay từ đầu**, không cần đợi được yêu cầu bỏ riêng. Các phần tử khác trong header (dòng "Cập nhật lúc...", mô tả ngắn dạng `<p>`, nút hành động như "Xem tất cả bài đăng Facebook", tag ngày/giờ) **vẫn giữ nguyên**, chỉ bỏ đúng `<h1>`. Quy tắc `24px`/`font-weight:800` ở trên giờ chỉ còn ý nghĩa lịch sử/tham khảo nếu sau này có lý do phải thêm lại `<h1>` ở 1 trang cụ thể.
- **Thiết bị:** toàn bộ link BH dùng trên máy tính (người dùng chốt 24/10/2026): tối ưu cho màn hình 1440–1920px, không sửa bố cục điện thoại.
- Container chính `.wrap`: `max-width:1280px`.
- Màu hover cho dòng bảng (`tr:hover`): `#F7F9FC`.
- Font: `'Inter', sans-serif` cho toàn bộ trang, kể cả bảng/mô tả — không để fallback stack (`-apple-system`...) đứng trước Inter, sẽ khiến chữ hiển thị bằng font hệ điều hành thay vì Inter.
- **Đánh số mục/panel**: dùng badge tròn `<span class="chart-num">N</span>` (nền `--navy`, chữ trắng, tròn 20px) đặt trước tiêu đề trong `<h3>`/`<summary>` — không dùng kiểu số thường `"3. Tiêu đề"`. CSS class `.chart-num` cần khai báo riêng trong từng file (copy nguyên khối từ 1 trong 4 file trên).
- **Panel nào luôn hiện, panel nào thu gọn**: theo nguyên tắc "1-2 mục quan trọng/tổng quan nhất luôn hiện (`<div class="panel">`), các mục còn lại thu gọn mặc định (`<details class="panel"><summary>...`)" — áp dụng khi 1 trang có nhiều panel/bảng để tránh rối mắt. Panel có logic tự ẩn khi rỗng (`style.display='none'` khi không có dữ liệu, ví dụ các mục cảnh báo trong `chamcong.html`) không tính vào quy tắc này.

## Hệ thống phân quyền (`index.html`)

- Nguồn: cột "Nhóm" trong Sheet Users, nhận diện theo **từ khoá chứa trong chuỗi** (không cần khớp tuyệt đối, không phân biệt hoa/thường/dấu — xem `_stripDiacritics()`), hoặc cờ `isAdmin=TRUE`.
- Bảng quyền (hàm `_classifyGroup()`):

  | Nhóm (chứa từ khoá) | Xem được tab |
  |---|---|
  | Admin (`isAdmin=TRUE`) | Tất cả (Bán hàng, Dịch vụ, HCNS, MKT, CRM, BC tuần) |
  | `pgd` hoặc `pho giam doc` (Phó Giám Đốc, khớp cả viết tắt lẫn viết đầy đủ — **phải xét trước** `gd` bên dưới vì "pgd" cũng chứa substring "gd") | Bán hàng, Dịch vụ, HCNS, MKT, CRM, BC tuần |
  | `gd` / `giam doc` (Giám Đốc thường — gồm cả "Tổng Giám đốc" nếu không có cờ Admin) | Bán hàng, Dịch vụ, HCNS, MKT, CRM (mở 28/09/2026), BC tuần (mở 28/09/2026) |
  | `hcns` / `nhan su` | HCNS, BC tuần |
  | `mkt` / `marketing` | Bán hàng, MKT, CRM, BC tuần |
  | `ban hang` (không khớp `mkt`/`marketing`) | Bán hàng, MKT, CRM, BC tuần |
  | `dich vu` | Dịch vụ, BC tuần |
  | Không khớp gì (Văn phòng, Kế toán…) | Chỉ BC tuần |

  **BC tuần mở cho mọi tài khoản đã đăng nhập từ 19/10/2026** (trưởng BP cùng xem để họp giao ban).

  **Ô KPI "Lợi nhuận gộp" cạnh Bảo Lộc trong tab 2 (`ln`, thêm 23/10/2026, anh Khanh): chỉ Admin, GĐ/PGĐ (gồm Tổng GĐ) và Bán hàng thường.** Ô `#kpi-ln` chỉ được chèn khi có `ln` (`_lnKpiHTML()`, gọi trong `renderKPI()` và `updateKpiCards()`, vì hai hàm này dựng lại cả hàng KPI); nhóm MKT/Marketing vẫn vào được tab 2 nhưng không thấy ô này (nhánh `mkt` được xét trước nhánh `ban hang`, nhóm ghi cả "MKT - Bán hàng" cũng rơi vào nhánh `mkt`). Vì là khoá client-side nên file `loinhuan.json` (giá vốn, lợi nhuận, tên khách từng xe) vẫn tải được bằng đường dẫn trực tiếp trên GitHub Pages — anh Khanh biết và chấp nhận (23/10/2026).

  **CRM tạm ẩn bớt từ 09/2026, mở lại cho Bán hàng thường và PGĐ từ 09/2026, mở nốt cho GĐ thường 28/09/2026** — hiện Admin/PGĐ/GĐ/MKT/Bán hàng thường đều xem được CRM.

  Từ 09/2026, Chấm công không còn là quyền riêng — ai xem được tab Nhân sự thì xem được luôn sub-tab Chấm công bên trong (xem giải thích ở bảng File HTML phía trên).

- **Bảng phân quyền hiển thị ở đầu tab Admin** (`_permMatrixHTML()` trong `index.html`, thêm 26/09/2026) — tự tính từ `_classifyGroup()` với tên nhóm mẫu, sửa quyền là bảng tự đúng; chỉ cần sửa hàm này khi **thêm nhóm hoặc tab mới** (thêm dòng/cột).
- **Thẻ "Đánh giá KQKD chi nhánh" trên cùng tab Admin** (`_kqkdHTML()`, thêm 25/10/2026): nút mở trang claude.ai riêng tư `https://claude.ai/artifact/TaQGKEsNo9vrWGhwoVNA9G` (Project A `9. KQKD`, README mục "Trang trình Ban lãnh đạo"). Repo chỉ chứa link, không chứa số liệu; người chưa được chia sẻ trang không mở được.
- **Thẻ "Chi phí bot Telegram" (dưới thẻ KQKD) trong tab Admin** (`_chiPhiBotHTML()`, thêm 25/10/2026): chỉ là nút mở `http://localhost:8765/`, trang chi phí do máy anh Khanh chạy (Project A `dashboard/trang_chi_phi.py`), chỉ mở được trên máy đó. **Chỉ ghi địa chỉ localhost trong repo công khai**: không ghi mã tài khoản thanh toán, mã project Google Cloud hay số liệu mức dùng.
- Đây là khoá **client-side** (ẩn hiển thị), KHÔNG phải bảo mật dữ liệu thật — dữ liệu vẫn tải hết về trình duyệt. Không tự ý nâng cấp thành "bảo mật thật" trừ khi được yêu cầu (đổi kiến trúc lớn).
- Mục "Xe chưa xuất HĐ" dùng cờ riêng `_isAdmin`/`_isGD`, **không dùng chung** điều kiện với quyền xem tab Nhân sự (2 thứ này từng bị gộp chung gây lộ dữ liệu ngoài ý muốn cho nhóm HCNS — đã tách hẳn, giữ nguyên tách biệt).
