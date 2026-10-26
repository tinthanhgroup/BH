# Tab 9 "KQKD" – Đánh giá KQKD chi nhánh (`kqkd.html`)

Thêm 26/10/2026 theo yêu cầu anh Khanh, thay trang riêng tư trên claude.ai (không cần chia sẻ từng người nữa). Nội dung: kết quả kinh doanh từng hạng mục của 4 chi nhánh theo tháng, 5 tab con (Tổng quan, Phòng kinh doanh, Xưởng dịch vụ, Lưu ý, Cách tính). Người xem: Ban lãnh đạo (chị Đào).

## Quyền và bảo mật

- `index.html` chỉ hiện nút tab với quyền `kqkd`: Admin và nhóm chứa "tgd" / "Tổng Giám đốc" (`_classifyGroup()`, xem `docs/giao-dien-va-phan-quyen.md`). GĐ, PGĐ chi nhánh không thấy.
- Repo công khai, nên khoá tab không đủ. `kqkd.html` **không chứa số liệu**: giao diện và số liệu nằm chung trong `kqkd.enc.json`, mã hóa AES-GCM 256, khóa sinh từ **mật khẩu KQKD** qua PBKDF2-SHA256 (số vòng ghi trong file). Trang hỏi mật khẩu rồi giải mã ngay trong trình duyệt; sai mật khẩu thì báo "Mật khẩu không đúng".
- Mật khẩu KQKD **không ghi trong repo, docs hay file nào trên Drive**; anh Khanh giữ và báo người xem. Ô "Nhớ trên máy này" lưu mật khẩu trong `localStorage` của máy đó (không tick: chỉ giữ đến khi đóng tab).
- Đổi mật khẩu: chạy lại script dựng với mật khẩu mới rồi push; mật khẩu cũ hết tác dụng với file mới.

## Cập nhật hằng tháng

File mã hóa dựng ở Project A (không để script trong repo này): `G:\My Drive\1. Project A\9. KQKD\scripts\bh_kqkd.py` (cách chạy ở đầu file và README project 9. KQKD, mục "Trang trình Ban lãnh đạo"). Script ghi đè `kqkd.enc.json`; sau đó commit + push (hỏi trước khi push).

## Kỹ thuật

- `kqkd.html`: khung nhập mật khẩu + đối tượng `dash` tối giản (dữ liệu tĩnh, `calc`, `onData`) thay cho thư viện của mẫu Dashboard claude.ai mà giao diện được viết theo; chạy lại các `<script>` trong phần giao diện sau khi chèn. Biến màu `--color-*`, `--cds-*` gán theo bảng màu BH ở `:root`. Font Be Vietnam Pro + JetBrains Mono từ Google Fonts, d3 7.9.0 từ cdnjs. Chỉ nền sáng.
- `kqkd.enc.json`: `{v, ky, vong, salt, iv, ma}` (base64; `ma` gồm bản mã + tag 16 byte cuối). Chỉ `ky` (vd "T9/2026") đọc được.
