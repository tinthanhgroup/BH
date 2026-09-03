# Module Chấm công

`TinThanh_ChamCong_Sync.gs` → `chamcong.json` → `chamcong.html`

⚠️ **Khác với NhanSu_Sync/MKT_Sync**: file này **KHÔNG tự chứa CONFIG riêng** — bắt buộc phải dán vào **cùng Apps Script project** với `TinThanh_AutoSync.gs` để dùng chung biến `CONFIG.GITHUB_TOKEN/OWNER/REPO/BRANCH` (không tách project được như 2 file kia).

**Nguồn dữ liệu:** 1 folder Drive gốc (`DRIVE_FOLDER_ID`), bên trong là các **folder con = tên chi nhánh** (VD "Nha Trang", "Phú Yên"...). Admin mỗi chi nhánh tự thả file `.xlsx` chấm công vào đúng folder mình mỗi ngày, không cần đặt tên cố định — script luôn lấy file `.xlsx` **sửa gần nhất** trong mỗi folder. Thêm chi nhánh mới chỉ cần tạo thêm 1 folder con, không cần sửa code.

**Ghi đè theo từng chi nhánh riêng biệt** (không ghi đè toàn bộ `chamcong.json` mỗi lần chạy): nếu 1 chi nhánh không có file mới trong lần chạy này, dữ liệu cũ của chi nhánh đó trong `chamcong.json` vẫn được giữ nguyên — chỉ các chi nhánh vừa đọc được file mới mới bị thay thế.

**Mỗi bản ghi (1 người × 1 ngày) có các field chính:** `manv, ten, phong, chinhanh, ngay (ISO), thu, punches[] (danh sách giờ quẹt, đã sort), sopunch, giovao, giora, sogiolam, cocong (bool), thieucham (bool, true nếu chỉ quẹt đúng 1 lần), giovao_sang, giora_trua, giovao_chieu, giora_cuoi, co_chieu` — cộng thêm field nối từ Nhân sự: `phong_hr, to_hr, khoi_hr, capbac_hr, chucvu_hr, ten_hr, daNghiViec, manv_goc`.

**Join với Nhân sự — CÓ 2 LỚP xử lý trùng Mã NV, đừng nhầm là dư thừa nhau:**
1. **Lớp server (`.gs`, hàm `joinWithNhanSu_()`):** fetch thẳng `nhansu.json` (`JOIN_NHANSU=true`), match theo `Mã NV + Chi nhánh` trước, rơi về `Tên + Chi nhánh` nếu thiếu mã. Mã NV phụ (cột "Mã NV phụ" khai báo tường minh trong Sheet Nhân sự, xem [module-nhansu.md](module-nhansu.md)) được trỏ về cùng 1 hồ sơ ở bước này — **đây là cách gộp chắc chắn, dựa trên khai báo tường minh của HR**. Nếu người đã nghỉ việc (có trong `turnover`) và ngày chấm công sau ngày nghỉ → **loại bỏ dòng đó** (không tính là vắng nhầm sau khi đã nghỉ).
2. **Lớp client (`chamcong.html`, hàm `mergeDuplicateManv_()`):** xử lý các trường hợp HR **chưa kịp khai báo** Mã NV phụ. Heuristic: gom theo cùng Tên+Chi nhánh, mã nào có số ngày công ≤ `max(2, 15% số ngày công của mã nhiều nhất)` bị coi là "mã bóng ma" (vân tay phụ ít dùng) và tự gộp vào mã chính. Nếu ≥2 mã đều có nhiều ngày công gần nhau → **không tự gộp**, đẩy vào bảng "🔁 Cần kiểm tra thủ công" để người dùng tự xác nhận (rất có thể là 2 người trùng tên thật, không phải cùng 1 người).

**3 bảng chẩn đoán cuối `chamcong.html`** (đều dropdown, mặc định đóng khi không có gì bất thường):
- **"⚠ Nhân sự chưa khớp với dữ liệu Nhân sự"** — có chấm công nhưng không khớp được cả Mã NV lẫn Tên với sheet Nhân sự.
- **"🔁 Cần kiểm tra thủ công"** — nghi trùng vân tay nhưng lớp client không tự tin gộp (xem heuristic ở trên).
- **"👤 Nhân sự đang làm nhưng chưa có dữ liệu chấm công"** — có trong Nhân sự (đang làm) nhưng không thấy trong `chamcong.json`.
