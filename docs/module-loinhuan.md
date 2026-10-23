# Module Lợi nhuận xe (`loinhuan.html`, thẻ 12 "Lợi nhuận gộp từng xe" trong tab 2 BC Bán hàng)

Thêm 23/10/2026 theo yêu cầu anh Khanh (ban đầu làm tab 9 riêng, cùng ngày chuyển thành thẻ 12 trong tab 2). Trang nhúng iframe `#ln-iframe` trong `#ln-card`, lazy-load khi mở tab 2 nếu có quyền. Mục đích: nhìn nhanh tỷ lệ lợi nhuận gộp từng chiếc xe, theo phiên bản, tháng và chi nhánh; mỗi xe là một ô vuông tô màu (6 mức: dưới 0%, 0–1, 1–2, 2–3, 3–4, từ 4%).

## Quyền xem

Quyền `ln` trong `_classifyGroup()` (`index.html`): chỉ **Admin, GĐ/PGĐ (gồm Tổng GĐ), Bán hàng thường**. MKT vẫn vào được tab 2 nhưng thẻ 12 bị ẩn. Chi tiết và giới hạn của khoá client-side: `docs/giao-dien-va-phan-quyen.md`. File `loinhuan.json` chứa giá vốn, lợi nhuận, tên khách, số khung từng xe và **nằm trong repo công khai**, anh Khanh đã chấp nhận (23/10/2026). Không đưa thêm dữ liệu nhạy cảm vào file này khi chưa hỏi lại.

## Hai tab con

| Tab | Tháng của ô | Dùng để |
|---|---|---|
| Theo xe báo bán | Ngày báo bán DMS (`ngayBaoBan`) | Rà hỗ trợ HTV phải nhận (HTV tính chương trình theo tháng báo bán) |
| Theo xe ký | Ngày ký = ngày đặt cọc (`ngayDatCoc`) | Dự kiến lợi nhuận sớm hơn bản báo bán khoảng 1 tuần (trung vị 8 ngày từ ký đến báo bán) |

Tab "Theo xe ký" gồm cả xe đã phân xe chưa báo bán (hỗ trợ tạm tính theo chương trình gần nhất) và khách ký chưa có xe (danh sách nợ xe: giá bán, giá vốn, chi phí KM ước theo trung vị xe cùng phiên bản; phiên bản mới chưa có xe bán, vd. Palisade Hybrid, chưa ước được). Bảng mở ra tự cuộn sang phải để thấy các tháng gần nhất; thứ tự tháng vẫn từ T1 đến T mới nhất.

## Cách tính (một thông tin một nơi: chi tiết nghiệp vụ ở `9. KQKD/README.md` mục "Hỗ trợ bán hàng HTV theo xe")

Lợi nhuận gộp = giá bán − giá vốn − chi phí KM − hoa hồng môi giới + hỗ trợ HTV còn nhận, chia giá bán. Giá bán trong sheet đã gồm hỗ trợ CT HTV nên không cộng lại.

- **Chi phí KM:** cột CHI PHÍ KM của sheet; xe có ghi quà nhưng chưa có chi phí tính theo định mức tạm tính (anh Khanh chốt dùng tạm 23/10/2026, sẽ chỉnh): dán phim 2,0 (3M 2,5), camera HT 1,5, camera Miox 2,0, phủ gầm 2,7, lót sàn 0,5 triệu; BHTX 0,9–1,2% giá bán tùy dòng xe, BHTV 0,85%, BHVC 1,0%; bảo hiểm "HTV tặng" tính 0.
- **Hoa hồng môi giới:** sổ 641 TK 6418.12 của kế toán Nha Trang (7 xe, 58 triệu, ghép theo số khung hoặc tên khách). Chưa có sổ Đà Lạt, Bảo Lộc T1–T8.
- **Hỗ trợ HTV:** theo chương trình tháng báo bán (CV 722/2025 đến CV 114/2026), cùng mức cho xe tồn và xe mới mua, còn nhận = mức − phần đã giảm vào giá vốn; Palisade tồn trước 23/04 được bù chênh giá theo CV 177; xe nội bộ/ngang đại lý không có hỗ trợ.
- **Xe nội bộ:** nhận diện theo file lãi gộp kế toán (NT, PR, PY cả năm; ĐL–BL chỉ T9) và tên khách; xe nội bộ ĐL, BL T1–T8 chưa biết nên chưa đánh dấu.

## Dữ liệu và cách làm mới

`loinhuan.json` (khoá `bb` và `ky`, mỗi khoá gồm `xe[]`, `loai`, `updated`) là **ảnh chụp tạo tay**, không tự đồng bộ: được dựng từ `data.json` (cột QUÀ KM, CHI PHÍ KM… do `TinThanh_AutoSync.gs` xuất từ 23/10/2026) cộng bảng mức hỗ trợ HTV, định mức KM và hoa hồng môi giới. Muốn làm mới phải chạy lại bộ script tính rồi ghi đè `loinhuan.json`. Bộ script nằm ở Project A: `G:\My Drive\1. Project A\9. KQKD\scripts\loi_nhuan_xe\` (README trong thư mục: cách chạy, nơi sửa định mức KM, mức hỗ trợ HTV tháng mới, hoa hồng môi giới, các điểm còn tạm tính). Không lưu script trong repo BH này.

Trang đứng riêng, không có `<h1>` và chỉ nền sáng (theo quy ước các trang module nhúng iframe); tải `loinhuan.json` cùng thư mục bằng `fetch`.
