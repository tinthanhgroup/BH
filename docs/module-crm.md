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

Theo đúng khuôn mẫu style dùng chung giữa các module (xem CLAUDE.md gốc — `--navy`, `.chart-num`, `.wrap` 1280px, Inter font, `tr:hover #F7F9FC`). 8 mục đánh số, mục 1-2 luôn hiện (không thu gọn — quan trọng nhất), còn lại thu gọn mặc định (trừ mục 4-5 trong `.grid2` để `open` sẵn theo quy ước cũ):

1. **Phễu chuyển đổi theo Tiến trình** — cột động, tự suy ra danh sách bước từ dữ liệu thực tế (`buildStages()`, không hardcode tên bước — nếu công ty đổi/thêm bước trong CRM thì phễu tự cập nhật theo, không cần sửa code). Đếm **tích lũy** (số khách có `tienTrinhMaxNum >= N`), không phải đếm riêng từng bước.
2. **Khách Hot & Veryhot cần theo dõi** — bảng riêng, LUÔN hiện (không phải toàn bộ lead — bảng tổng đã xem được trên app CRM nên cố ý không lặp lại ở đây, quyết định có chủ đích 09/2026). Veryhot luôn xếp trên Hot (`trangThaiPriority()`), trong cùng nhóm thì mới cập nhật gần nhất lên trước; dòng Veryhot tô nền đỏ nhạt (`.veryhot-row`).
3. **Lead theo tuần theo Model (6 tuần gần nhất)** — biểu đồ cột chồng (stacked bar) tự vẽ bằng SVG thuần (không dùng thư viện chart), tính theo **Ngày tạo**, tuần từ Thứ 2 đến Chủ nhật (`startOfWeekTs()`). Model hiển thị tối đa 7 màu riêng (`MODEL_COLORS`, cycle nếu >8 model) + gộp phần dư vào "Khác"; lead thiếu Model tính vào nhóm "(Chưa nhập Model)" riêng (không gộp vào "Khác").
4. Lead theo Nhóm nguồn.
5. Lead theo Chi nhánh.
6. Lead theo Nhân viên phụ trách (hiển thị `nhanVienTen`, fallback email nếu chưa có trong roster).
7. **Dữ liệu thiếu — giai đoạn triển khai**: bảng Chi nhánh × Nhân viên, đếm lead thiếu Tiến trình (`tienTrinhMaxNum===0`) và/hoặc thiếu Model, dùng cho GĐBH từng chi nhánh chấn chỉnh nhân viên trong giai đoạn mới triển khai CRM (09/2026). **Chỉ mang tính tạm thời** — cân nhắc bỏ mục này khi dữ liệu nhập đã ổn định, tránh biến thành báo cáo "trị" nhân viên lâu dài ngoài ý định ban đầu.
8. Model quan tâm nhiều nhất.

"Đã ký hợp đồng" (`isWon()`) nhận diện bằng cách tìm chuỗi "ký hợp đồng" (không dấu) trong `tienTrinhMaxLabel` — không hardcode đúng số bước 6, để vẫn đúng nếu công ty đổi thứ tự bước trong CRM. "Đã dừng/không mua" (`isLost()`) = `tienTrinhMaxNum >= 7`.

Bảng "Danh sách chi tiết lead" (hiện toàn bộ) đã **bỏ hẳn** khỏi trang (từng có ở bản đầu, xem lịch sử git nếu cần) theo yêu cầu người dùng — lý do: quá nhiều dòng, và xem được đầy đủ trên ứng dụng CRM gốc rồi.

## Tab CRM trong `index.html`

Tab 8 "CRM 🔒" — dùng chung nhóm quyền với tab "BC Bán hàng" (`_classifyGroup()`: mọi nhóm được xem `bh` thì cũng xem được `crm`, xem bảng quyền trong CLAUDE.md gốc). Lazy-load iframe khi bấm vào tab lần đầu, giống cơ chế các tab MKT/Chính sách/Quy định.

## Trigger

`crmSync`: **1 trigger duy nhất `everyMinutes(30)`** (24/7) — dùng hàm có sẵn `setupCrmSyncTrigger()`/`removeCrmSyncTrigger()`, không tự viết `ScriptApp.newTrigger()` tay. Nhắc kiểm tra trang ⏰ Triggers trước khi cài (hạn mức ~20 trigger/project tính chung với các file `.gs` khác).

## ⚠️ Bẫy đã gặp: dò cột header phải bỏ dấu trước khi so khớp

Lần đầu chạy `crmSync()` trả về **0 khách hàng** dù Sheet có đủ dữ liệu thật (đã verify bằng hàm debug tạm: đúng tab, đúng 82 hàng, đúng cấu trúc cột). Nguyên nhân: `detectCrmColumns_()` ban đầu so khớp **trực tiếp chuỗi có dấu** (`h === "số điện thoại"`), và riêng cột này không khớp được (dù hiển thị giống hệt bằng mắt) — khả năng cao do lệch encoding Unicode dấu tiếng Việt (NFC/NFD) giữa dữ liệu Bizfly xuất ra Sheet và chuỗi hardcode trong code, dù các cột có dấu khác (Chi nhánh, Nhân viên, Nguồn...) vẫn khớp bình thường. Hệ quả: cột "sdt" không được nhận diện → mọi dòng bị bỏ qua ở bước `if (!sdtKey) continue`.

**Đã sửa**: `detectCrmHeaderRow_()`/`detectCrmColumns_()` giờ so khớp qua `stripDiacritics_CRM_()` (bỏ dấu, hạ chữ thường) trước khi so sánh, thay vì so khớp chuỗi có dấu trực tiếp — tránh lặp lại lỗi tương tự dù nguyên nhân gốc (NFC/NFD) không rõ 100%. **Áp dụng nguyên tắc này cho mọi sync script đọc header tiếng Việt từ Sheet nguồn bên thứ 3 (không phải Sheet công ty tự tạo)** — độ tin cậy encoding thấp hơn Sheet nội bộ.

## Việc còn để ngỏ / có thể cần làm tiếp

- Dữ liệu CRM hiện còn rất ít (mới tích hợp từ đầu tháng 9/2026, lẫn nhiều dòng test) — số liệu báo cáo ban đầu sẽ không phản ánh đúng thực tế cho tới khi dữ liệu tích lũy đủ nhiều và đội ngũ ngừng dùng dữ liệu test trên Sheet thật.
