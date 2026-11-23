# Tab 9 "KQKD" – Đánh giá KQKD chi nhánh (`kqkd.html`)

Thêm 26/10/2026 theo yêu cầu anh Khanh, thay trang riêng tư trên claude.ai (không cần chia sẻ từng người nữa). Nội dung (bố cục từ 19/11/2026): trang chính "Theo tháng" (lợi nhuận phòng KD, xưởng từng tháng của hệ thống hoặc từng chi nhánh, xe bán lẻ, lãi gộp/xe, bảng chi nhánh × tháng) và nút chọn từng tháng (từ T9/2026); mỗi tháng có 5 tab con (Tổng quan, Phòng kinh doanh, Xưởng dịch vụ, Lưu ý, Cách tính). `kqkd.enc.json` chứa dữ liệu mọi tháng; `kqkd.html` không đổi. Người xem: Ban lãnh đạo (chị Đào).

## Quyền và bảo mật

- `index.html` chỉ hiện nút tab với quyền `kqkd`: Admin và nhóm chứa "tgd" / "Tổng Giám đốc" (`_classifyGroup()`, xem `docs/giao-dien-va-phan-quyen.md`). GĐ, PGĐ chi nhánh không thấy.
- Repo công khai, nên khoá tab không đủ. `kqkd.html` **không chứa số liệu**: giao diện và số liệu nằm chung trong `kqkd.enc.json`, mã hóa AES-GCM 256, khóa sinh từ **mật khẩu KQKD** qua PBKDF2-SHA256 (số vòng ghi trong file). Trang hỏi mật khẩu rồi giải mã ngay trong trình duyệt; sai mật khẩu thì báo "Mật khẩu không đúng".
- Mật khẩu KQKD **không ghi trong repo, docs hay file nào trên Drive**; anh Khanh giữ và báo người xem. Ô "Nhớ trên máy này" lưu mật khẩu trong `localStorage` của máy đó (không tick: chỉ giữ đến khi đóng tab).
- Đổi mật khẩu: chạy lại script dựng với mật khẩu mới rồi push; mật khẩu cũ hết tác dụng với file mới.

## Cập nhật hằng tháng

File mã hóa dựng ở Project A (không để script trong repo này): `G:\My Drive\1. Project A\9. KQKD\scripts\bh_kqkd.py` (cách chạy ở đầu file và README project 9. KQKD, mục "Trang trình Ban lãnh đạo"). Script ghi đè `kqkd.enc.json`; sau đó commit + push (hỏi trước khi push).

## Kỹ thuật

- `kqkd.html`: khung nhập mật khẩu + đối tượng `dash` tối giản (dữ liệu tĩnh, `calc`, `onData`) thay cho thư viện của mẫu Dashboard claude.ai mà giao diện được viết theo; chạy lại các `<script>` trong phần giao diện sau khi chèn. Biến màu `--color-*`, `--cds-*` ở `:root` theo **Giao diện 1 – tinh tế** (23/11/2026, giống tab 4–8): font Inter từ Google Fonts, màu nhấn `#3b5bdb`, bảng màu biểu đồ `dash.colors` xanh nhấn/xanh nhạt; d3 7.9.0 từ cdnjs. Chỉ nền sáng. Phần kiểu Giao diện 1 của giao diện báo cáo (3 cỡ chữ 12/13/16, độ đậm 400/500, bo 8px, đầu bảng nền nhạt, STT bảng Xếp hạng chi nhánh, nút một kiểu) nằm trong khối `<style>` ghi đè ở `dash_kqkd_khung.html` (Project A). Người dùng chốt 23/11/2026: không có dòng tiêu đề đầu trang và câu nhận xét (kết luận, nội dung thẻ Điểm nóng) — trang vào thẳng thẻ số, biểu đồ, bảng; tháng cũ vẫn hiện ghi chú số đã điều chỉnh; biểu đồ lợi nhuận từng tháng ghi số mọi tháng bằng nhãn nền tối chữ trắng (lỗ: nền đỏ) — đổi giao diện thì sửa ở đó rồi chạy lại `bh_kqkd.py`. Lưu ý: không đặt chú thích HTML `<!-- -->` trong `dash_kqkd_index.html` (script chép giao diện tháng vào cả chú thích đầu khung, chú thích lồng làm vỡ trang).
- `kqkd.enc.json`: `{v, ky, vong, salt, iv, ma}` (base64; `ma` gồm bản mã + tag 16 byte cuối). Chỉ `ky` (vd "T9/2026") đọc được.
