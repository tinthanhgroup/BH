# CLAUDE.md

Repo này là project con của **Project A** (`G:\My Drive\1. Project A`), đặt ở `C:\Data\BH` để git không bị Google Drive đồng bộ. **Đầu mỗi phiên, đọc `G:\My Drive\1. Project A\CLAUDE.md`** (lãnh đạo, quy định nội bộ, ranh giới nội dung các file, cập nhật STATUS, trang tiến độ) và làm theo như thể repo này vẫn nằm trong Project A.

Chỉ ghi những gì đang áp dụng. Chi tiết kỹ thuật ở `docs/` (bảng dưới); tiến độ ở `STATUS.md`; nhật ký cũ ở `NHAT_KY.md`.

## Tổng quan

Hệ thống báo cáo nội bộ **Hyundai Tín Thanh** (chi nhánh Nha Trang/Đà Lạt/Phú Yên/Phan Rang/Bảo Lộc): **file HTML tĩnh trên GitHub Pages** (`tinthanhgroup/BH`, nhánh `main`) tự `fetch()` các file `.json` trong repo; `.json` do **Google Apps Script** (`.gs`, chạy trên Google Sheet nội bộ) đồng bộ và push lên GitHub theo trigger.

- **Không có backend, không build step:** mỗi `.html` là single-file, JS/CSS inline. Sửa xong là dùng được.
- **Code, comment, tên biến tiếng Việt có dấu** (kể cả string, key object) — chủ ý, giữ nguyên convention.
- `.gs` **không deploy qua git**: bản sao local ở `apps-script/` (gitignored, có token thật) chỉ có hiệu lực khi người dùng dán vào script.google.com.

## Bản đồ tài liệu

| Việc đang làm | Đọc |
|---|---|
| Sửa một trang/tab module | `docs/module-<tên>.md` của module đó: hanghoa (tab 1 Hàng hóa, KCK, Creta), chamcong, nhansu, nhacungcap, mkt, fbads, chinhsach, quydinh, crm, baocaotuan, loinhuan, kqkd |
| Tab/sub-tab, lazy-load, style dùng chung giữa các trang, phân quyền | `docs/giao-dien-va-phan-quyen.md` |
| Sửa / thêm script `.gs`, trigger | `docs/apps-script.md` |
| Cập nhật tay: chỉ tiêu, KCK dự phòng, chính sách HTV, quy định nội bộ, nhà cung cấp | `docs/cap-nhat-thu-cong.md` |
| Đặt hàng xe với HTV | `docs/quy-tac-dat-hang.md` |

Chỉ mở file `docs/` của phần đang sửa, không đọc hết.

## Trang HTML (root repo)

`index.html` (desktop, các tab + Admin), `mobile.html` (rút gọn, không có HCNS/MKT), và các trang module nhúng iframe, lazy-load khi mở: `chamcong`, `nhansu`, `nhacungcap`, `quy_dinh_noi_bo` (sub-tab của tab 4 HCNS), `fbads`, `mkt` (sub-tab của tab 5 MKT), `theo_doi_chinh_sach` (tab 6, mở tự do), `crm` (tab 7), `baocaotuan` (tab 8), `kqkd` (tab 9, số liệu mã hóa). Bảng đầy đủ: `docs/giao-dien-va-phan-quyen.md`.

- `index.html` và `mobile.html` **trùng dữ liệu có chủ đích** (`KCK_DATA`, `KCK_DATE`): sửa một file thì kiểm tra file kia.
- Trang module **không có `<h1>`**, theo chuẩn style chung (màu `--navy:#1B4F8C`, `.wrap` 1280px, font Inter, badge `.chart-num`) — chi tiết trong docs.
- Phân quyền là khoá **client-side** (ẩn hiển thị), không phải bảo mật thật; không tự nâng cấp. Mục "Xe chưa xuất HĐ" dùng cờ `_isAdmin`/`_isGD` riêng, giữ tách biệt với quyền tab Nhân sự.

## Bẫy bắt buộc nhớ khi sửa `.gs`

1. `TinThanh_Auth.gs` (và `TinThanh_DichVu_Sync.gs` phần `doGet`) là **Web App**: sửa xong phải Deploy → Manage deployments → **New version**; chỉ Save thì URL đang chạy không đổi.
2. Giới hạn **~20 trigger/project**: luôn dùng 1 trigger lặp (`.everyHours(n)`), **không** dùng vòng lặp `.atHour(h).create()`. Thêm script/trigger mới → nhắc người dùng kiểm tra trang ⏰ Triggers trước.
3. File `.gs` gửi người dùng chỉ có **placeholder** (`"NHAP_GITHUB_TOKEN"`…), nhắc họ tự điền; không xóa/thay giá trị thật trong `apps-script/`.
4. Cài/xóa trigger: dùng cặp hàm có sẵn `setupXxxTrigger()` / `removeXxxTrigger()`.
5. FBAds, Báo cáo tuần, Dịch vụ: **GET JSON cũ → gộp → PUT** (Sheet không giữ lịch sử); các script khác ghi đè thẳng.

## Khái niệm nghiệp vụ hay nhầm

- **"Xuất hồ sơ" = "Xuất hóa đơn" = "Xuất HĐ"** — cùng một field (`xuatHoSo`).
- **"Bán ngang"** = bán xe cho đại lý khác — cột "Nguồn khách" = `"Khác"`.
- **Xe ngập nước** (ô "KH đặt cọc" chứa "ngập nước") bị loại khỏi **mọi** báo cáo (`_initFromRecords()`). Ngoại lệ: xe ngập nước **có Ngày báo bán** = bán nội bộ làm xe công vụ → chỉ cộng vào bảng 9/10/11 "Thực hiện chỉ tiêu" (`window._banNoiBo`); không tính tốc độ bán, SL hợp đồng, doanh số bán lẻ, tồn kho.
- **Creta CKD/CBU:** màu chứa "CKD" = lắp ráp trong nước, tách phiên bản có đuôi " CKD"; không có = nhập khẩu (CBU). Đã bỏ chữ "FACELIFT".
- **Kho hàng hóa** (NT/ĐL/PY/PR/BL — 5 chi nhánh) ≠ **Kho MKT Fanpage** (NT/PY/ĐL — 3 chi nhánh).
- **KCK** = "Kho hàng sẵn Nhà máy" (Hyundai Thành Công — **HTV**). Cờ "sắp hết" (`r:1`) theo **màu chữ ĐỎ ở ô "Năm SX"** trong file HTV, không theo giá trị năm. Code cũ của nút Admin `uploadKCK()` suy theo năm là sai, chưa dọn.
- **Chỉ tiêu tháng** `DEFAULT_TARGETS` chỉ là mặc định; người dùng chỉnh qua nút "✏️ Chỉnh sửa chỉ tiêu" lưu `localStorage`, ghi đè giá trị trong code.

## Trước khi sửa bất kỳ file nào

1. Sau khi sửa `<script>` inline: máy **không có `node`** → kiểm tra bằng Edge headless (`msedge.exe --headless=new --disable-gpu --no-sandbox --virtual-time-budget=5000 --enable-logging=stderr --dump-dom <file URL>`): `stderr` không có `SyntaxError`/`Uncaught`, DOM có đúng nội dung mong đợi.
2. Có thể `git add`/`git commit` khi người dùng yêu cầu (GitHub Pages tự deploy sau push). **Luôn hỏi xác nhận trước khi `git push`**, kể cả khi đã push trong cùng phiên.

## Cập nhật trạng thái

`STATUS.md` là nơi duy nhất ghi tiến độ. Khung và cách cập nhật (gồm chạy `G:\My Drive\1. Project A\dashboard\build.py` rồi đăng lại trang tiến độ): mục "Cập nhật trạng thái" trong `G:\My Drive\1. Project A\CLAUDE.md`. Không sửa tay bảng trong `TONG_HOP.md`.
