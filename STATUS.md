# Status – Hệ thống báo cáo nội bộ Hyundai Tín Thanh (BH)
Cập nhật: 13/10/2026

## Trạng thái: 🟢 Đúng tiến độ

- **Việc gấp nhất:** Trình TGĐ dự thảo Quy định xử lý kho tồn lâu
- **Đang chờ:** TGĐ: duyệt Quy định xử lý kho tồn lâu

Hệ thống đang chạy, auto-sync bình thường. Đơn T10 đã đặt, HTV xác nhận gần hết (NT 28/30, ĐL 10/10, PY 7/8). HTV yêu cầu đặt bổ sung để tuân thủ CV157 (thiếu NT 11 · ĐL 7 · PY 5, hạn 12h 10/10/2026); Công ty chọn **xin tạm dừng đặt bổ sung**: đã gửi công văn cho HTV ngày 10/10/2026, **HTV đã đồng ý tạm dừng (ghi nhận 13/10/2026)**; phương án 20 xe giữ làm dự phòng (bảng Giả định đặt hàng PA4). Quy tắc đặt hàng đã viết lại ở `docs/quy-tac-dat-hang.md`. Mô tả hệ thống: xem `CLAUDE.md` và `docs/`.

## Việc tiếp theo
- [x] Chốt phương án đặt hàng T10 với HTV (đã đặt, HTV xác nhận gần hết)
- [x] Soạn công văn xin tạm dừng đặt bổ sung T10 (`2. Dat hang/CV de nghi tam dung dat hang bo sung T10 v3.docx`)
- [x] Gửi công văn xin tạm dừng đặt bổ sung T10 cho HTV (10/10/2026; bản gửi: `2. Dat hang/CV de nghi tam dung dat hang bo sung T10.pdf`, số CV: _cập nhật_)
- [x] Theo dõi phản hồi của HTV về công văn → HTV đồng ý tạm dừng đặt bổ sung T10 (13/10/2026)
- [ ] 20/10/2026: rà soát bán hàng; nếu thị trường tăng thì đặt bổ sung ngay (dự phòng: PA4 20 xe)
- [ ] Trình TGĐ dự thảo "Quy định xử lý kho tồn lâu" (`2. Dat hang/Quy dinh xu ly kho ton lau v8.docx`)
- [ ] Sửa hàm `uploadKCK()` (nút Admin): đang nhận diện "xe sắp hết" theo năm SX, đúng ra phải theo chữ đỏ ở ô "Năm SX"
- [ ] Báo cáo tuần: mở quyền cho PGĐ (hiện chỉ Admin + GĐ)
- [ ] Báo cáo tuần: chuyển từ nhập tay Excel → Google Sheet + Apps Script

## Đang chờ
- TGĐ: duyệt dự thảo Quy định xử lý kho tồn lâu (mốc ngày, lãi suất nội bộ, thời hạn thanh toán công nợ)

## Mốc sắp tới
- 20/10/2026: rà soát bán hàng, quyết định đặt bổ sung

## Nhật ký
| Ngày | Sự kiện |
|---|---|
| 13/10/2026 | HTV đồng ý tạm dừng đặt bổ sung T10 theo công văn ngày 10/10; vẫn rà soát bán hàng 20/10 như đã cam kết |
| 10/10/2026 | Đã gửi công văn cho HTV xin tạm dừng đặt bổ sung T10 (`CV de nghi tam dung dat hang bo sung T10.pdf`); chờ HTV phản hồi |
| 10/10/2026 | Soạn công văn gửi HTV xin tạm dừng đặt bổ sung T10 (thị trường chưa tăng: HĐ ký mới Q2 57, Q3 54 xe/tháng; tồn + đã đặt 112 xe ≈ 2,1 tháng), cam kết rà soát 20/10. Giả định đặt hàng: thêm PA4 (20 xe + 3 Creta chờ XN) |
| 09/10/2026 | Xe chưa BC DMS: cột đề xuất đặt hàng lấy theo Chờ XN (bỏ Palisade), bỏ cột Ngập nước. Chỉ tiêu T10 +1 xe mỗi đại lý (NT 41, ĐL 29, PY 14). Viết quy tắc đặt hàng (`docs/quy-tac-dat-hang.md`); soạn dự thảo Quy định xử lý kho tồn lâu + công nợ nội bộ |
| 08/10/2026 | Creta: bỏ chữ "FACELIFT" khỏi mọi phiên bản; tách phiên bản CKD theo màu có chữ "CKD" (vd. "Trắng CKD" → "1.5 ĐẶC BIỆT CKD", màu Trắng), chữ CKD tô xanh đậm — index + mobile |
| 07/10/2026 | Giả định đặt hàng: PA3 viết lại = PA2 với Creta N Line NT có 3 CKD; ô hạn mức CKD/CBU chỉ xét N Line → 47 xe, đủ hạn mức |
| 06/10/2026 | Giả định đặt hàng: nhiều lần cập nhật L1/L2 theo file chi nhánh; thêm cột tốc độ bán 90 ngày, nút ẩn/hiện chi nhánh |
| 06/10/2026 | Xe chưa BC DMS: thêm cột 9–12 (đề xuất đặt hàng PA2, tốc độ bán) |
