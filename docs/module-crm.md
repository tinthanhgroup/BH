# Module CRM

`TinThanh_CRM_Sync.gs` → `crm.json` → `crm.html` (nhúng iframe trong tab 8 "CRM" của `index.html`)

## Nguồn dữ liệu

**1 Google Sheet do CRM Bizfly tự động xuất ra** (không phải Sheet nội bộ công ty tạo tay): `https://docs.google.com/spreadsheets/d/1PBJMEPf0O3RmInaDkDpI_q8MFYkwbhU5QgXNSeZEwJo`, tab "Trang tính1". Mỗi khi có thay đổi trên CRM (đổi tiến trình, đổi nhân viên phụ trách, đổi chi nhánh...), Bizfly **ghi thêm 1 dòng mới** vào Sheet — **đây là log sự kiện, KHÔNG phải 1 dòng/khách hàng**. Cùng 1 khách hàng (cùng "Ngày tạo") có thể xuất hiện hàng chục dòng chỉ trong vài giờ.

Header thật nằm ở **hàng 2** (hàng 1 chỉ là tiêu đề nhóm cột gộp như "Thông tin cơ bản", "Nhân sự phụ trách"...) — script tự dò hàng chứa đúng cột "Tên khách hàng" để xác định header, không phụ thuộc số hàng phía trên.

Cột dùng: *Tên khách hàng, Số điện thoại, Ngày tạo, Ngày cập nhật, Tiến trình, Model, Nhân viên, Chi nhánh, Trạng thái khách hàng, Nguồn, Nhóm nguồn*. Ngày tạo/Ngày cập nhật dạng chuỗi `"HH:mm:ss dd/MM/yyyy"` (giờ trước ngày sau — khác định dạng `dd/MM/yyyy` của các module khác, đã viết hàm parse riêng `parseVNDateTime_CRM_`/`parseVNDateTime` cho đúng).

## Xử lý dữ liệu trong `TinThanh_CRM_Sync.gs`

1. **Dedup theo SĐT chuẩn hóa** (`normalizePhone_CRM_`: bỏ ký tự không phải số, quy `+84`/`84` đầu về `0`; ô có nhiều SĐT nối bằng `|` chỉ lấy số đầu) — trong các dòng log cùng 1 SĐT, lấy dòng có "Ngày cập nhật" mới nhất làm trạng thái hiện tại của khách hàng đó. Tên khách hàng có thể trùng (nhiều dòng test đều tên "test") nên **không dùng tên để dedup**.
2. **Chuẩn hóa Chi nhánh** về đúng 5 mã hệ thống (NT/ĐL/PY/PR/BL) qua `CRM_BRANCH_MAP` (so khớp không dấu/hoa-thường). Dòng nào Chi nhánh trống → **tự suy luận theo email Nhân viên**: với mỗi email, lấy Chi nhánh xuất hiện nhiều nhất trong các dòng khác của chính email đó (`buildBranchByEmail_CRM_`, tính lại mỗi lần sync — không phải bảng ánh xạ tay cố định, tự thích ứng khi có nhân viên mới hoặc đổi chi nhánh). Nếu email chưa từng gắn với chi nhánh nào thì để trống thật, không đoán (quyết định có chủ đích, đã trao đổi với người dùng — xem lịch sử chat lúc dựng module này). Field `chiNhanhSuyLuan: true` đánh dấu dòng nào đang dùng giá trị suy luận (không phải giá trị gốc từ CRM), `crm.html` hiển thị in nghiêng ở bảng chi tiết cho các dòng này.
3. **Tiến trình** (`Tiến trình` dạng `"N. Tên bước"`, N từ 1-7, bước 7 = "Đã mua xe khác/không mua nữa" kèm hậu tố `- Failed` tùy trường hợp) — dữ liệu có thể **bị ghi lùi lại** (quan sát thực tế: 1 khách đi từ "6. Ký hợp đồng" → dòng log sau lại thành "1. Bắt đầu", có thể do CRM tự động re-trigger). Vì vậy chỉ lưu **`tienTrinhMaxNum`/`tienTrinhMaxLabel` (cao nhất từng đạt)** để tính phễu chuyển đổi, không dùng tiến trình mới nhất cho việc này — tránh hiểu nhầm là khách hàng tụt hạng. Field `tienTrinh` (không có hậu tố Max) vẫn giữ giá trị mới nhất, chỉ để hiển thị tham khảo ở bảng chi tiết.
4. **KHÔNG có cờ cảnh báo "lead chưa xử lý"** — quyết định có chủ đích: CRM Bizfly đã tự xử lý việc này (có phương án chuyển lead sang TVBH khác khi không liên hệ kịp), nằm ngoài phạm vi báo cáo tổng hợp này.
5. **Dữ liệu test** (tên hoặc SĐT chứa "test", không phân biệt hoa/thường) — **không xóa/lọc ở tầng sync**, chỉ gắn cờ `isTest: true`. `crm.html` mặc định ẩn các dòng này (checkbox "Hiện cả dữ liệu test", tắt sẵn).

## Field mỗi object trong `records.leads[]`

`ten, sdt (khóa dedup, đã chuẩn hóa), sdtHienThi (giá trị gốc để hiển thị), ngayTao, ngayCapNhat, model, nhanVien, nhanVienTen (tên thật tra theo CRM_STAFF_MAP, rỗng nếu email chưa có trong roster — crm.html tự fallback hiển thị email), chiNhanh, chiNhanhSuyLuan, tienTrinh, tienTrinhMaxNum, tienTrinhMaxLabel, trangThai, nguon, nhomNguon, soLanCapNhat (số dòng log đã gộp — phản ánh mức độ tương tác/độ phức tạp xử lý), isTest`.

## Roster nhân viên (`CRM_STAFF_MAP` trong `.gs`)

Người dùng cung cấp trực tiếp danh sách 26 nhân viên (Khu vực/Chức danh/Họ tên/Email) 09/2026 — hardcode thành `CRM_STAFF_MAP` (email thường → `{ten, chiNhanh}`), **ưu tiên cao hơn** suy luận tần suất (`buildBranchByEmail_CRM_`): tra roster trước, chỉ fallback sang suy luận tần suất khi email không có trong roster (nhân viên mới chưa kịp cập nhật). Sửa tay `CRM_STAFF_MAP` khi có nhân viên mới/nghỉ việc/đổi chi nhánh — không có cơ chế tự động đồng bộ từ nguồn nào khác.

2 email trong roster gốc người dùng gửi bị lệch chính tả so với dữ liệu thực tế trên Sheet CRM (gõ tay nhầm) — đã xác nhận với người dùng và sửa `CRM_STAFF_MAP` theo đúng email trên Sheet:
- Võ Minh Nhã: dùng `minhnha2312@gmail.com` (không phải `minhha2312@gmail.com` như roster gốc).
- Võ Trịnh Chí Đức: dùng `ducvotrinhchi@gmail.com` (không phải `ducvotrinchi@gmail.com` như roster gốc).

## `crm.html`

Theo đúng khuôn mẫu style dùng chung giữa các module (xem CLAUDE.md gốc — `--navy`, `.chart-num`, `.wrap` 1280px, Inter font, `tr:hover #F7F9FC`; cỡ chữ trong bảng/thẻ KPI/nhãn tự thu nhỏ hơn 1 chút so với 4 module kia để hợp với layout dashboard nhiều panel — không phải bug, xem `.kpi-num`/`table`/`.panel .desc`...). 8 mục đánh số, mục 1 và 3 (Phễu, Hot & Veryhot) luôn hiện (`<div class="panel">`, không thu gọn — quan trọng nhất), mục 2 (Lead theo tuần) và 4-6 để `open` sẵn dù về kỹ thuật vẫn là `<details>` thu gọn được, còn lại (7-8) thu gọn mặc định. Mục 4-5-6 (Nguồn/Chi nhánh/Model) gộp chung 1 hàng 3 cột (`.grid3`) cho gọn thay vì xếp dọc từng bảng riêng:

1. **Phễu chuyển đổi theo Tiến trình — từng Chi nhánh** — mỗi chi nhánh 1 phễu **hình thang** riêng (SVG thuần, mỗi tầng 1 màu trong `FUNNEL_COLORS`, cycle nếu >7 bước) xếp cạnh nhau trong `#funnelGrid` (CSS grid `auto-fit`), kèm bảng tỷ lệ chuyển đổi **giữa từng cặp bước liền kề** (không phải tích lũy, ghi ngắn gọn "Bước N→M" + `title` hiện tên đầy đủ khi hover, do khung hẹp) bên dưới mỗi phễu. Danh sách bước **dùng chung cho mọi chi nhánh** (union, tự suy ra từ `buildStages()` trên toàn bộ `LEADS` — không hardcode tên bước, không tính riêng theo từng chi nhánh) để chi nhánh nào cũng hiện đủ số bước như nhau, kể cả bước 0 lead (không bị mất bước). Chiều rộng hình thang mỗi tầng tính tích lũy (`tienTrinhMaxNum >= N`) trong phạm vi chi nhánh đó, nhưng **dùng chung 1 thang đo `globalMax`** (lấy từ chi nhánh có tổng lead cao nhất) giữa các chi nhánh — nên chi nhánh ít lead hơn thì phễu nhỏ hơn thật, không bị phóng to riêng theo max của chính nó (% trong mỗi tầng vẫn tính riêng theo tổng của chi nhánh đó, không theo `globalMax`). **Mục này CỐ Ý không áp dụng bộ lọc Chi nhánh/Nguồn/Nhân viên/Trạng thái phía trên** (giống mục 2), chỉ trừ checkbox ẩn dữ liệu test.
2. **Lead theo tuần theo Model — từng Chi nhánh (6 tuần gần nhất)** — đặt ngay sau mục 1 (Phễu) vì cùng là 2 mục "theo chi nhánh" nên đặt cạnh nhau cho liền mạch. Mỗi chi nhánh 1 biểu đồ cột chồng (stacked bar) riêng, xếp cạnh nhau trong `#weeklyChartsGrid` (CSS grid `auto-fit`, tự xuống dòng khi màn hẹp). Tự vẽ bằng SVG thuần (không dùng thư viện chart), tính theo **Ngày tạo**, tuần từ Thứ 2 đến Chủ nhật (`startOfWeekTs()`). Model hiển thị tối đa 7 màu riêng (`MODEL_COLORS`, cycle nếu >8 model) + gộp phần dư vào "Khác"; lead thiếu Model tính vào nhóm "(Chưa nhập Model)" riêng (không gộp vào "Khác"); màu Model tính chung trên toàn bộ dữ liệu (không tính riêng theo từng chi nhánh) để đồng nhất màu giữa các chi nhánh; trục cao dùng **chung 1 thang đo `maxTotal`** giữa tất cả chi nhánh để so sánh công bằng (chi nhánh ít lead hơn thì cột thấp hơn thật, không bị co giãn riêng theo max của chính nó). Danh sách chi nhánh theo thứ tự cố định `BRANCH_ORDER = ['NT','ĐL','PY','PR','BL']`, chỉ hiện chi nhánh nào có dữ liệu; lead chưa gán chi nhánh không được vẽ riêng ở mục này (đã có mục 8 theo dõi thiếu dữ liệu). **Mục này và mục 1 (Phễu) CỐ Ý không áp dụng bộ lọc Chi nhánh/Nguồn/Nhân viên/Trạng thái phía trên** — luôn tính trên toàn bộ dữ liệu (chỉ trừ checkbox ẩn dữ liệu test) để không bị mất chi nhánh/bước nào khi đang lọc riêng 1 chi nhánh.
3. **Khách Hot & Veryhot cần theo dõi** — bảng riêng, LUÔN hiện (không phải toàn bộ lead — bảng tổng đã xem được trên app CRM nên cố ý không lặp lại ở đây, quyết định có chủ đích 09/2026). Veryhot luôn xếp trên Hot (`trangThaiPriority()`), trong cùng nhóm thì mới cập nhật gần nhất lên trước; dòng Veryhot tô nền đỏ nhạt (`.veryhot-row`).
4. Lead theo Nhóm nguồn.
5. Lead theo Chi nhánh.
6. Model quan tâm nhiều nhất.
7. Lead theo Nhân viên phụ trách (hiển thị `nhanVienTen`, fallback email nếu chưa có trong roster).
8. **Dữ liệu thiếu — giai đoạn triển khai**: bảng Chi nhánh × Nhân viên, đếm lead thiếu Tiến trình (`tienTrinhMaxNum===0`) và/hoặc thiếu Model, dùng cho GĐBH từng chi nhánh chấn chỉnh nhân viên trong giai đoạn mới triển khai CRM (09/2026). **Chỉ mang tính tạm thời** — cân nhắc bỏ mục này khi dữ liệu nhập đã ổn định, tránh biến thành báo cáo "trị" nhân viên lâu dài ngoài ý định ban đầu.

"Đã ký hợp đồng" (`isWon()`) nhận diện bằng cách tìm chuỗi "ký hợp đồng" (không dấu) trong `tienTrinhMaxLabel` — không hardcode đúng số bước 6, để vẫn đúng nếu công ty đổi thứ tự bước trong CRM. "Đã dừng/không mua" (`isLost()`) = `tienTrinhMaxNum >= 7`.

Bảng "Danh sách chi tiết lead" (hiện toàn bộ) đã **bỏ hẳn** khỏi trang (từng có ở bản đầu, xem lịch sử git nếu cần) theo yêu cầu người dùng — lý do: quá nhiều dòng, và xem được đầy đủ trên ứng dụng CRM gốc rồi.

## Tab CRM trong `index.html`

Tab 8 "CRM 🔒" — **tạm ẩn bớt từ 09/2026**: chỉ Admin và nhóm `mkt`/`marketing` xem được (`_classifyGroup()`), kể cả GĐ và Bán hàng thường (trước đây cùng nhóm quyền với `bh`) cũng không còn thấy tab này nữa — xem bảng quyền + lý do trong CLAUDE.md gốc. Lazy-load iframe khi bấm vào tab lần đầu, giống cơ chế các tab MKT/Chính sách/Quy định.

## Trigger

`crmSync`: **1 trigger duy nhất `everyMinutes(30)`** (24/7) — dùng hàm có sẵn `setupCrmSyncTrigger()`/`removeCrmSyncTrigger()`, không tự viết `ScriptApp.newTrigger()` tay. Nhắc kiểm tra trang ⏰ Triggers trước khi cài (hạn mức ~20 trigger/project tính chung với các file `.gs` khác).

## ⚠️ Bẫy đã gặp: dò cột header phải bỏ dấu trước khi so khớp

Lần đầu chạy `crmSync()` trả về **0 khách hàng** dù Sheet có đủ dữ liệu thật (đã verify bằng hàm debug tạm: đúng tab, đúng 82 hàng, đúng cấu trúc cột). Nguyên nhân: `detectCrmColumns_()` ban đầu so khớp **trực tiếp chuỗi có dấu** (`h === "số điện thoại"`), và riêng cột này không khớp được (dù hiển thị giống hệt bằng mắt) — khả năng cao do lệch encoding Unicode dấu tiếng Việt (NFC/NFD) giữa dữ liệu Bizfly xuất ra Sheet và chuỗi hardcode trong code, dù các cột có dấu khác (Chi nhánh, Nhân viên, Nguồn...) vẫn khớp bình thường. Hệ quả: cột "sdt" không được nhận diện → mọi dòng bị bỏ qua ở bước `if (!sdtKey) continue`.

**Đã sửa**: `detectCrmHeaderRow_()`/`detectCrmColumns_()` giờ so khớp qua `stripDiacritics_CRM_()` (bỏ dấu, hạ chữ thường) trước khi so sánh, thay vì so khớp chuỗi có dấu trực tiếp — tránh lặp lại lỗi tương tự dù nguyên nhân gốc (NFC/NFD) không rõ 100%. **Áp dụng nguyên tắc này cho mọi sync script đọc header tiếng Việt từ Sheet nguồn bên thứ 3 (không phải Sheet công ty tự tạo)** — độ tin cậy encoding thấp hơn Sheet nội bộ.

## Việc còn để ngỏ / có thể cần làm tiếp

- Dữ liệu CRM hiện còn rất ít (mới tích hợp từ đầu tháng 9/2026, lẫn nhiều dòng test) — số liệu báo cáo ban đầu sẽ không phản ánh đúng thực tế cho tới khi dữ liệu tích lũy đủ nhiều và đội ngũ ngừng dùng dữ liệu test trên Sheet thật.
