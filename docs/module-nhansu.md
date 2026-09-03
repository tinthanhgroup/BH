# Module Nhân sự

`TinThanh_NhanSu_Sync.gs` → `nhansu.json` → `nhansu.html`

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
- `manv_phu` (Mã NV phụ, hỗ trợ người có 2 mã chấm công/2 vân tay) — 1 chuỗi tách bằng dấu phẩy/chấm phẩy/khoảng trắng thành mảng (`parseManvPhu_()`). Đây là cơ sở để [module-chamcong.md](module-chamcong.md) gộp Mã NV trùng ở lớp server.
- `thamnien` = số năm làm việc tính tới hiện tại, làm tròn 1 chữ số thập phân.
- Có hàm `baoCaoTrungMaNV_()` tự rà soát Mã NV bị trùng (cùng Mã NV + Chi nhánh xuất hiện ≥2 người) — chỉ log cảnh báo ra Apps Script log, KHÔNG chặn sync, HR tự sửa tay trong Sheet.
- 1 người có thể xuất hiện ở **cả `data` lẫn `turnover`** (từng nghỉ rồi vào lại) — 2 mảng không loại trừ nhau, đừng giả định `turnover` chỉ chứa người *không* có trong `data`.

**`nhansu.html`** tự tính thêm ở phía client (không nằm trong JSON): 4 KPI "Tình hình nhân sự" (Nghỉ việc/Tuyển mới — tháng trước/tháng này, tự tính theo ngày hệ thống — **không** gán cứng tháng), khối "Biến động" dùng cửa sổ **45 ngày gần nhất** (không phải top-10 cố định), bảng "Danh sách hợp đồng hết hạn" hiển thị thẳng từ `HD_HET_HAN`.
