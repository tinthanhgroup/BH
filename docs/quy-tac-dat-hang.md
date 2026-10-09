# Quy tắc đặt hàng xe

Cập nhật: 09/10/2026. Dùng khi lập đề xuất cho một đợt đặt hàng hoặc khi HTV yêu cầu đặt bổ sung.
Bản dễ đọc cho GĐBH/chi nhánh (trang web riêng tư): https://claude.ai/artifact/VZnNwVdwA357a6pA7v6eN2 — nguồn `2. Dat hang/Quy tac dat hang.html`; sửa quy tắc ở đây thì nhớ sửa và đăng lại trang đó.
Quy định điều chuyển xe và công nợ nội bộ: xem file Word "Quy dinh xu ly kho ton lau" (folder `2. Dat hang/`), tóm tắt ở mục 7.

## Tóm tắt

1. **Đặt đúng số tối thiểu HTV yêu cầu**, không đặt dư (lãi vay ~8%/năm).
2. **HTV chỉ tính số lượng, không quy định model** → dành mỗi suất cho xe **rẻ và bán nhanh** (ít tiền lãi nhất).
3. **Không đặt phiên bản đang tồn lâu hoặc bán chậm**; ưu tiên giải kho, điều chuyển xe trước.
4. **Kho chung toàn công ty**: tính theo phiên bản cho cả công ty, rồi mới chia về đại lý.
5. **KCK** (xe có sẵn ở kho HTV) không cần né bằng mọi giá; chỉ ưu tiên xe không KCK khi chi phí ngang nhau.

## Bước 1. Xác định số xe phải đặt

| Nội dung | Quy tắc |
|---|---|
| Yêu cầu của HTV | **Tồn kho chưa báo cáo DMS + BO + LXX = 1,5 tháng chỉ tiêu** (chỉ tiêu tháng N + ½ chỉ tiêu tháng N+1) |
| Vì sao "chưa BC DMS" | HTV coi xe chưa báo cáo DMS là xe chưa bán, còn trong kho |
| Số HTV gửi | Yêu cầu · Đã đặt · Đã xác nhận · **Còn thiếu = Yêu cầu − Đã xác nhận** |
| Số thực phải đặt | **Còn thiếu − số xe đang Chờ XN** (xe Chờ XN khi được xác nhận sẽ trừ vào phần thiếu) |
| Lấy số nào làm chuẩn | Số HTV gửi. Số tự tính chỉ để kiểm tra |
| Palisade | Đơn riêng, không tính vào đợt |

Ví dụ đợt 10/2026: HTV báo thiếu NT 11, ĐL 7, PY 5. Đang Chờ XN NT 2, PY 1 → phải đặt **NT 9, ĐL 7, PY 4 = 20 xe**.

## Bước 2. Lọc phiên bản được đặt

Tính **theo phiên bản, gộp mọi màu, gộp toàn công ty** (Creta gộp CBU + CKD cùng phiên bản). Màu chỉ dùng ở bước chọn màu.

| Chỉ số | Cách tính |
|---|---|
| **Xe có** | Tồn chưa BC DMS + BO + LXX + Chờ XN. Bỏ Palisade, xe ngập nước, xe Nhà máy cho mượn |
| **Tốc độ bán** | Hợp đồng bán lẻ 90 ngày ÷ 3 (theo ngày đặt cọc; khách NT, PR, ĐL, BL, PY; bỏ bán ngang). Nếu 90 ngày chỉ có 1–2 hợp đồng thì xem thêm 180 ngày ÷ 6 |
| **Số tháng tồn** | Xe có ÷ Tốc độ bán |
| **Không tính** | DS nợ (hợp đồng chờ xe): đã tính khi đặt hàng; khách chưa đủ tiền thì GĐBH tự xử lý |

**Loại khỏi danh sách đặt** khi:

| Trường hợp | Kết luận |
|---|---|
| Sau khi đặt, số tháng tồn toàn công ty **> 2,5 tháng** | Không đặt |
| Phiên bản có xe tồn **> 90 ngày** ở NT, ĐL hoặc PY | Không đặt (kể cả phiên bản cùng model thay thế được, vd. Venue ĐB tồn lâu thì không đặt Venue thường) |
| **Ngoại lệ:** phiên bản bán nhanh (số tháng tồn hiện tại < 2) | Vẫn đặt nếu xe tồn lâu chỉ nằm ở **Phan Rang / Bảo Lộc** (thị trường nhỏ, để đó cho đỡ phí vận chuyển), hoặc chỉ có **1 xe** tồn lâu ở NT/ĐL/PY. Kèm đề xuất chuyển xe đó cho nơi bán được |

## Bước 3. Chọn xe theo chi phí lãi thấp nhất

**Chi phí lãi của 1 xe đặt thêm ≈ giá vốn × 8% ÷ 12 × số tháng xe nằm kho**

- Số tháng nằm kho = (Xe có + số xe đã thêm, gồm cả xe này) ÷ Tốc độ bán.
- Giá vốn = trung bình giá vốn các xe cùng phiên bản nhập trong năm.
- Lãi bắt đầu khi xe về đại lý và hồ sơ về ngân hàng (~5 ngày sau khi xe về).
- 8%/năm là lãi suất tạm tính (10/2026) để so sánh phương án; khi cần số chính xác dùng tiền lãi Ngân hàng tính cho từng xe.
- Chọn lần lượt xe có chi phí lãi thấp nhất cho đến đủ số phải đặt.

Thứ tự ưu tiên khi hai lựa chọn gần nhau:

1. Xe **không có trong KCK** (HTV phải sản xuất, giao rải rác → phải đặt trước; xe KCK đặt lúc nào cũng có, giao 3–5 ngày).
2. Xe KCK **sắp hết** mà bán đều → nên đặt sớm.
3. Tránh phiên bản mà tốc độ bán chỉ dựa trên 1 hợp đồng.

## Bước 4. Chia về đại lý

1. Tính số tháng tồn **của từng đại lý** cho phiên bản đó (xe có theo đại lý đặt hàng; tốc độ theo nguồn khách: PR tính vào NT, BL tính vào ĐL).
2. Thả xe về đại lý có số tháng thấp nhất. Đại lý đang dư thì không thả thêm.
3. Kiểm tra **ràng buộc đặt / thả xe** (xe chở từ nhà máy Ninh Bình theo tuyến PY → NT → ĐL):

| Đại lý đặt | Được thả xe ở |
|---|---|
| Đà Lạt | ĐL, NT, PY |
| Nha Trang | NT, PY |
| Phú Yên | PY |

→ Xe về ĐL chỉ do ĐL đặt; xe về NT do NT hoặc ĐL đặt; xe về PY do đại lý nào đặt cũng được. Mỗi đại lý phải đặt đúng số của mình.

4. Chọn màu theo màu bán chạy của phiên bản đó ở đại lý nhận.

## Bước 5. Kiểm tra trước khi trình

- [ ] Tổng số từng đại lý đúng số phải đặt.
- [ ] Không có phiên bản tồn lâu / bán chậm.
- [ ] Không phiên bản nào vượt 2,5 tháng (toàn công ty) sau khi đặt.
- [ ] Đúng ràng buộc đặt / thả xe.
- [ ] Creta N Line nhập khẩu (CBU) đủ xe CKD kẹp kèm (mục 6).
- [ ] Xem ngày cập nhật KCK; nếu cũ thì hỏi lại.
- [ ] Không tự bỏ model nào nếu không có lý do rõ ràng.

## 6. Quy tắc riêng

- **Creta N Line CBU**: đặt 1 xe Creta **CKD** (bất kỳ phiên bản CKD nào) được xác nhận 3 xe N Line CBU. Nên dồn Creta của đợt về 1 đại lý đặt (thường ĐL, vì thả được ở NT/PY) rồi chia lại.
- **Nhận diện Creta CKD**: màu có chữ "CKD" (xem mục Creta trong `module-hanghoa.md`).
- **File đề xuất của chi nhánh**: VC037 = NT (gồm PR), VC048 = ĐL (gồm BL), VC079 = PY; sheet "ĐƠN ĐẶT HÀNG", cột U..AF. Có file chi nhánh thì số trong file là số chốt; phân bổ tự tính chỉ là gợi ý.

## 7. Điều chuyển kho (tóm tắt)

- **Kho chung**: xe nằm kho chi nhánh này có thể bán cho khách chi nhánh khác; ưu tiên giải kho hơn đặt mới.
- **Tuyến điều chuyển giữa đại lý** (2 chiều): **NT ↔ ĐL**, **NT ↔ PY**. Không chuyển thẳng ĐL ↔ PY.
- **Vận chuyển**: ưu tiên **xe cứu hộ nội bộ** (tính chi phí thực tế của chuyến), ưu tiên **ghép chuyến 2 chiều** để giảm chi phí; chỉ thuê ngoài khi xe nội bộ không kịp (tham khảo ~3 triệu/xe).
- **Nội bộ chi nhánh** (ĐL ↔ BL, NT ↔ PR): chi nhánh tự quyết.
- **Khi nào chuyển**: phiên bản bán rất chậm mà một **kho** tồn từ 2 xe trở lên cùng phiên bản (NT, PR, ĐL, BL, PY tính riêng), nhất là cùng màu.
- **Ai làm**: danh sách xe tồn trên 60/90/150 ngày cập nhật hằng ngày trên trang (mục Hàng hóa → Tồn >60 ngày); đề xuất điều chuyển bất kỳ lúc nào, GĐBH các chi nhánh tự làm việc với nhau rồi trình Giám đốc hai chi nhánh quyết định (loại A, B1); loại B2, B3 trình TGĐ. Điều chuyển nội bộ chi nhánh (ĐL↔BL, NT↔PR) do Giám đốc chi nhánh quyết định.
- **Chia chi phí** (lãi trước khi chuyển luôn do bên giao chịu; mỗi xe chỉ chuyển 1 lần):

| Loại | Xe tồn khi chuyển | Ai quyết | Phí vận chuyển | Lãi sau khi chuyển |
|---|---|---|---|---|
| A | Bên nhận đã có khách ký | Bên nhận xin | Bên nhận | Bên nhận |
| B1 | ≤ 90 ngày | Bên nhận tự nguyện | Bên giao | Bên giao 15 ngày đầu, sau đó bên nhận |
| B2 | 91–180 ngày | Công ty có thể chỉ định | Bên giao | Bên giao 30 ngày đầu, sau đó bên nhận |
| B3 | > 180 ngày | Công ty chỉ định | Bên giao | Bên giao đến khi bán |

- **Chi phí lãi** chia theo **tiền lãi Ngân hàng tính thực tế cho đúng xe đó**; 8%/năm chỉ là mức tạm tính (10/2026) khi chưa có số ngân hàng.
- Các chi nhánh **hạch toán riêng** → mọi lần điều chuyển phải **ghi nhận công nợ nội bộ** (chi tiết trong file Word).

## Phụ lục: lấy số liệu từ `data.json`

| Số liệu | Trường / điều kiện |
|---|---|
| Đơn của đợt | `soDonHang` bắt đầu bằng `YYMM` (vd. `2610…`), đếm theo `daiLyDatHang` |
| Đã xác nhận / chưa | `trangThai` = `BO` / `Chờ XN` |
| Tồn chưa BC DMS | `trangThai` = "Xe đã về kho", `baoCaoDMS` = false |
| Xe tồn lâu | Tồn chưa phân khách (`ngayPhanXe` trống), `ngayNhapKho` quá 90 ngày; kho thực tế ở `khoXe` |
| Hợp đồng | `ngayDatCoc`, nguồn khách `nguonKhach` |
| Giá vốn | `giaVon` |
| KCK | `kck.json` (xét theo phiên bản + màu; nhóm màu theo `_mauToKckKey`) |

## Những lần đã tính sai (để không lặp lại)

- Chỉ chọn xe có trong KCK → sai: KCK là xe HTV đang giữ hộ, đặt lúc nào cũng có.
- Né KCK bằng mọi giá → sai: xe bán chậm nằm kho tốn lãi hơn.
- Tính theo màu → sai: bỏ sót xe tồn ở màu khác (Elantra Trắng "0 xe" trong khi tồn 2 xe Đen từ 05/2026).
- Tính DS nợ vào nhu cầu → không cần (xem Bước 2).
- Chỉ tính số tháng chung toàn công ty → thiếu: phải tính thêm từng đại lý để biết thả xe về đâu.
