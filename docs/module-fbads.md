# Module Quảng cáo Facebook (FB Ads)

`TinThanh_FBAds_Sync.gs` → `fbads.json` → `fbads.html` (nhúng iframe trong sub-tab "5.2. Quảng cáo Facebook" của tab 5 "MKT Thương hiệu" trong `index.html`, xem `switchMktSub()`)

## Nguồn dữ liệu — điểm khác biệt quan trọng

**Sheet nguồn:** https://docs.google.com/spreadsheets/d/1bxsPkbKQ6XlDbf4H7oceolB_2IjuNWExWCJZeJE1m24 (được 1 tool bên ngoài đã kết nối sẵn tự đồng bộ dữ liệu quảng cáo từ Facebook Ads vào — không phải Apps Script của repo này ghi vào Sheet, chỉ đọc ra).

⚠️ **Sheet này chỉ chứa snapshot chi phí/kết quả của "HÔM NAY" tại mọi thời điểm** (`data.date_start` = `data.date_stop` = ngày hiện tại cho mọi dòng, bị tool nguồn ghi đè lại mỗi lần nó chạy) — **không tự cộng dồn lịch sử** như các sheet khác trong repo (Nhân sự, MKT Fanpage...). Vì vậy `TinThanh_FBAds_Sync.gs` phải tự lo việc cộng dồn:
1. Đọc snapshot "hôm nay" từ Sheet.
2. **GET** `fbads.json` hiện có trên GitHub (lấy cả `sha` lẫn nội dung).
3. **Upsert** theo khoá `ngày + "|" + tên chiến dịch` — dòng mới của "hôm nay" ghi đè đúng dòng cùng ngày/cùng chiến dịch trong lịch sử cũ, các ngày khác giữ nguyên.
4. Dọn bớt dòng cũ hơn `FBADS_CONFIG.GIU_LICH_SU_NGAY` (mặc định 180 ngày) để `fbads.json` không phình to vô hạn.
5. **PUT** lại lên GitHub.

Nếu sau này viết thêm script tương tự đọc 1 Sheet "chỉ có hôm nay", **copy đúng cơ chế GET-merge-PUT này**, không copy kiểu "ghi đè thẳng" của `TinThanh_MKT_Sync.gs`/`TinThanh_NhanSu_Sync.gs` (2 script đó Sheet nguồn đã có đủ lịch sử nên ghi đè thẳng là đúng).

## Cấu trúc Sheet

Sheet chỉ có 1 tab (script tự lấy sheet **đầu tiên**, để trống `FBADS_CONFIG.SHEET_NAME` — điền tên tab nếu Sheet có nhiều tab). Header là tên field thô của Facebook Graph API (không phải tên tiếng Việt), script dò cột theo đúng tên trong `FBADS_COLUMN_MAP`, không phụ thuộc thứ tự cột. Cột `paging.cursors.*` là rác phân trang còn sót từ tool export, bỏ qua.

**Cột `data.spend` định dạng số không đồng nhất** trong Sheet nguồn — có dòng hiện dấu chấm phân cách nghìn kiểu Việt (vd `"43.332"`), có dòng không (vd `"42529"`), nhưng đều là số nguyên VNĐ (không phải số thập phân). Script strip hết ký tự không phải chữ số trước khi `parseInt` (`parseSoFBADS_()`) — **không được** parse dấu `.` như phần thập phân.

## `fbads.json`

```json
{
  "updated_at": "...", "updated_vn": "dd/MM/yyyy HH:mm", "today": "yyyy-MM-dd",
  "total": <số dòng lịch sử>,
  "records": { "lichSu": [
    { "ngay": "yyyy-MM-dd", "chienDich": "...", "chiPhi": 43332,
      "linkClick": 0, "videoView": 125, "postEngagement": 128, "pageEngagement": 129,
      "like": 1, "comment": 1, "postReaction": 2,
      "postInteractionGross": 3, "postInteractionNet": 3,
      "messengerBatDau": 1, "messengerTraLoi": 0, "messengerTongKetNoi": 1,
      "messengerXemChaoMung": 2, "messengerTraLoiDauTien": 1, "messengerNhanTin2Cap": 0
    }
  ]}
}
```
Mỗi object = 1 chiến dịch trong 1 ngày. Mới nhất lên đầu (`ngay` desc, cùng ngày thì theo tên chiến dịch).

## "Kết quả chính" dùng để đánh giá hiệu quả

Chọn **`messengerBatDau`** (`data.actions.onsite_conversion.messaging_conversation_started_7d` — cuộc trò chuyện Messenger bắt đầu) làm "kết quả" để tính Chi phí/Kết quả — quyết định có chủ đích vì tên các chiến dịch hiện tại đều có "CSC" (chăm sóc khách hàng qua chat), mục tiêu là dẫn khách vào Messenger chứ không phải đưa ra landing page. Sheet nguồn **không có cột impressions/reach** nên không tính được CTR/CPM kiểu cổ điển.

Nếu sau này có thêm chiến dịch với mục tiêu khác (vd đưa traffic ra web qua `linkClick`), cân nhắc đổi/thêm lựa chọn metric ở `fbads.html` thay vì hardcode cứng `messengerBatDau` cho mọi chiến dịch.

## Logic cảnh báo hiệu quả (`fbads.html`, hàm `xepLoaiHieuQua()`)

3 hằng số đầu file `<script>` của `fbads.html` — chỉnh trực tiếp nếu cần đổi độ nhạy:
- `FBADS_CHI_TOI_THIEU_DANH_GIA` (mặc định 50.000đ): chi phí dưới mức này → **chưa đủ dữ liệu để đánh giá** (badge xám `na`), tránh gắn nhãn oan chiến dịch mới chạy vài giờ.
- `FBADS_NGUONG_KHONG_KQ` (mặc định 200.000đ): đã chi vượt mức này mà **0 kết quả Messenger** → cảnh báo đỏ `xau` ("Chi nhiều, chưa có kết quả").
- `FBADS_HE_SO_CANH_BAO` (mặc định 1.5): Chi phí/Kết quả cao hơn X lần **trung bình của các chiến dịch có kết quả** (không tính chiến dịch chưa đủ dữ liệu) → cảnh báo vàng `canhbao` ("Cao hơn trung bình").
- Còn lại → `tot` (xanh, "Tốt").

Đây là ngưỡng đặt tạm dựa trên quy mô chi phí ban đầu (~40-50k/chiến dịch/ngày) — **cần người dùng rà soát lại sau khi có vài tuần dữ liệu thật**, không phải số liệu nghiệp vụ chính thức từ công ty.

## `fbads.html`

Theo đúng quy ước giao diện chung (xem CLAUDE.md gốc — `--navy`, `.wrap` 1280px, `chart-num`, Inter font...). Không dùng thư viện chart (đúng chủ trương "không build step" của repo) — biểu đồ xu hướng chi phí (panel 1) dựng bằng cột `<div>` CSS thuần (`.trend-bar`), có `title` tooltip hiện đúng ngày + số tiền khi hover, chỉ gắn nhãn trực tiếp (số tiền) lên cột cao nhất — không ghi số lên mọi cột.

4 panel đánh số:
1. **Xu hướng Chi phí theo ngày** (luôn hiện) — tổng chi phí tất cả chiến dịch mỗi ngày, theo bộ lọc khoảng thời gian.
2. **Cảnh báo hiệu quả theo Chiến dịch** (luôn hiện) — mục đích chính của dashboard, bảng xếp hạng theo mức độ cần chú ý (`xau` → `canhbao` → `tot` → `na`).
3. **Chi tiết chỉ số theo Chiến dịch** (thu gọn) — toàn bộ chỉ số tương tác cộng dồn theo chiến dịch.
4. **Nhật ký theo ngày** (thu gọn) — bảng thô từng dòng ngày × chiến dịch, mới nhất lên đầu.

Bộ lọc: khoảng thời gian (7 ngày / 30 ngày / toàn bộ lịch sử — nút bấm kiểu `.filter-btn` giống `crm.html`) + chọn chiến dịch.

## Trigger

`fbAdsSync`: 1 trigger `everyHours(6)` (4 lần/ngày, 24/7 — chi phí quảng cáo phát sinh liên tục cả ngoài giờ hành chính, khác Nhân sự/Chấm công chỉ cần đồng bộ giờ hành chính).
