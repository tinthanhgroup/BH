# Module Quảng cáo Facebook (FB Ads)

`TinThanh_FBAds_Sync.gs` → `fbads.json` → `fbads.html` (nhúng iframe trong sub-tab "5.2. Quảng cáo Facebook" của tab 5 "MKT Thương hiệu" trong `index.html`, xem `switchMktSub()`)

## Nguồn dữ liệu — điểm khác biệt quan trọng

**(Đổi kiến trúc 22/09/2026)** Trước đây 1 tool ngoài (Zapier/Make) tự đồng bộ dữ liệu Facebook Ads vào Sheet, `TinThanh_FBAds_Sync.gs` chỉ đọc ra. **Từ 22/09/2026, script tự gọi thẳng Facebook Graph API** (hàm `fetchFbAdsFromGraphApi_()`) — không còn phụ thuộc tool ngoài nào. Luồng đầy đủ mỗi lần `fbAdsSync()` chạy:
1. **Gọi Facebook Graph API** (`GET /{AD_ACCOUNT_ID}/insights?level=campaign&date_preset=today`) lấy snapshot chi phí/kết quả từng chiến dịch của "hôm nay".
2. **Ghi đè kết quả vào Google Sheet** (`writeFbAdsSnapshotToSheet_()`) — Sheet **vẫn được giữ lại** làm nơi lưu vết/audit thủ công (đọc bằng mắt khi cần), dù không còn là nguồn dữ liệu gốc nữa. Script tự dựng lại header (tên field thô kiểu Facebook, vd `data.campaign_name`) mỗi lần ghi, nên cột `paging.cursors.*` (rác phân trang từ tool cũ) không còn xuất hiện nữa.
3. Đọc lại chính Sheet vừa ghi (bước này giữ nguyên logic cũ, không đổi).
4. **GET** `fbads.json` hiện có trên GitHub (lấy cả `sha` lẫn nội dung).
5. **Upsert** theo khoá `ngày + "|" + tên chiến dịch` — dòng mới của "hôm nay" ghi đè đúng dòng cùng ngày/cùng chiến dịch trong lịch sử cũ, các ngày khác giữ nguyên.
6. Dọn bớt dòng cũ hơn `FBADS_CONFIG.GIU_LICH_SU_NGAY` (mặc định 180 ngày) để `fbads.json` không phình to vô hạn.
7. **PUT** lại lên GitHub.

**Sheet:** https://docs.google.com/spreadsheets/d/12Sl-Eus7-Y7va6pO_yXoV9mYyLfWBYQC3ARmY2KOlvg (sheet cũ trước 22/09/2026: `1bxsPkbKQ6XlDbf4H7oceolB_2IjuNWExWCJZeJE1m24`).

**Ad Account:** `act_1469329274856353` ("Hyundai Phú Yên 2" trên Meta Business) — đây là account đang chạy 2 chiến dịch tên có "CSC" (`TUCSON CSC - T9`, `Creta CSC T9`, xác nhận qua Graph API lúc thêm code 22/09/2026). Nếu công ty đổi sang chạy ads từ account khác, phải tự sửa `FB_GRAPH_API_CONFIG.AD_ACCOUNT_ID`.

`ACCESS_TOKEN` trong `FB_GRAPH_API_CONFIG` là **System User token thật** (app "BC ADS", `type: SYSTEM_USER`, `expires_at: 0` — không tự hết hạn, xác nhận qua `/debug_token` ngày 22/09/2026). **Tuyệt đối không thay bằng token cá nhân (type `USER`)** lấy nhanh từ Graph API Explorer — loại đó thường hết hạn trong vài giờ đến vài tuần, trigger sẽ âm thầm ngừng chạy mà không ai biết cho tới khi dữ liệu ngừng cập nhật (từng xảy ra với token thử nghiệm đầu tiên lúc thêm tính năng này, hết hạn cùng ngày). Nếu cần tạo token mới sau này (token bị thu hồi, app "BC ADS" bị gỡ khỏi Business...): Meta Business Settings → Users → System Users → Generate New Token, quyền `ads_read`, Token Expiration = "Never". Kiểm tra loại/hạn token bất cứ lúc nào bằng: `GET https://graph.facebook.com/v21.0/debug_token?input_token={TOKEN}&access_token={TOKEN}` — xem field `type` (phải là `SYSTEM_USER`) và `expires_at` (phải là `0`).

⚠️ **Sheet chỉ chứa snapshot chi phí/kết quả của "HÔM NAY" tại mọi thời điểm** (`data.date_start` cho mọi dòng luôn là ngày hiện tại, bị ghi đè mỗi lần `fbAdsSync()` chạy) — **không tự cộng dồn lịch sử** như các sheet khác trong repo (Nhân sự, MKT Fanpage...), kể cả sau khi đổi sang tự gọi API (Facebook Insights API bản chất cũng có thể tự điều chỉnh số liệu "hôm nay" trong ngày do độ trễ ghi nhận conversion, nên vẫn cần coi là "snapshot tạm" chứ không tổng hợp sẵn theo ngày như 1 API lịch sử đầy đủ). Vì vậy bước GET-merge-PUT (bước 4-7 ở trên) **vẫn bắt buộc giữ nguyên**, không được bỏ dù đã đổi nguồn ghi Sheet.

Nếu sau này viết thêm script tương tự đọc 1 nguồn "chỉ có hôm nay", **copy đúng cơ chế GET-merge-PUT này**, không copy kiểu "ghi đè thẳng" của `TinThanh_MKT_Sync.gs`/`TinThanh_NhanSu_Sync.gs` (2 script đó nguồn đã có đủ lịch sử nên ghi đè thẳng là đúng).

## Backfill lịch sử (nạp lại dữ liệu các ngày trong quá khứ)

Vì tính năng tự gọi Graph API mới bật 22/09/2026, `fbads.json` ban đầu **chỉ có dữ liệu từ ngày bật tính năng trở đi** — các ngày trước đó (kể cả cùng tháng) không tự có. Muốn xem "từ đầu tháng tới giờ" hay nạp lại khoảng ngày bất kỳ trong quá khứ, chạy tay 1 lần trong Apps Script editor:
- `backfillFbAdsThisMonth()` — nạp từ ngày 1 tháng hiện tại tới hôm qua (không đụng "hôm nay", để `fbAdsSync()` tự lo).
- `backfillFbAds('yyyy-MM-dd')` hoặc `backfillFbAds('yyyy-MM-dd', 'yyyy-MM-dd')` — nạp khoảng ngày tuỳ chọn (vd dò lại vài ngày bị thiếu do trigger lỗi).

- `backfillFbAdsFromJuly()` — nạp từ 01/07 năm hiện tại tới hôm qua (thêm 22/09/2026, theo yêu cầu xem lại dữ liệu xa hơn 1 tháng). Muốn mốc khác/năm khác thì gọi thẳng `backfillFbAds('yyyy-MM-dd')` thay vì sửa hàm này.

Cả 3 hàm dùng `time_increment=1` khi gọi Graph API để tách kết quả theo từng ngày, rồi upsert thẳng vào `fbads.json` qua đúng cơ chế GET-merge-PUT như `fbAdsSync()` — **không ghi vào Sheet** (Sheet chỉ giữ vai trò snapshot "hôm nay"). An toàn khi chạy lại nhiều lần / chồng ngày đã có sẵn (upsert theo khoá `ngày+tên chiến dịch`, không tạo trùng dòng). Sau khi backfill xong, bộ lọc "30 ngày"/"toàn bộ lịch sử" có sẵn trong `fbads.html` sẽ tự hiển thị đúng dữ liệu mới nạp, không cần sửa gì ở `fbads.html`. Lưu ý `FBADS_CONFIG.GIU_LICH_SU_NGAY` (mặc định 180 ngày) vẫn áp dụng — backfill quá xa quá ngưỡng này sẽ bị lọc bỏ ngay sau khi nạp.

## Cấu trúc Sheet

Sheet chỉ có 1 tab (script tự lấy sheet **đầu tiên**, để trống `FBADS_CONFIG.SHEET_NAME` — điền tên tab nếu Sheet có nhiều tab). Header do `writeFbAdsSnapshotToSheet_()` tự dựng lại mỗi lần chạy, là tên field thô của Facebook Graph API (không phải tên tiếng Việt), script đọc lại dò cột theo đúng tên trong `FBADS_COLUMN_MAP`, không phụ thuộc thứ tự cột.

**Cột `data.spend`**: giá trị lấy trực tiếp từ field `spend` của Graph API (số nguyên VNĐ dạng chuỗi thuần, không có dấu phân cách nghìn). Script vẫn strip hết ký tự không phải chữ số trước khi `parseInt` (`parseSoFBADS_()`) để phòng hờ — giữ nguyên hàm này dù nguồn Sheet cũ (trước 22/09/2026, do tool ngoài ghi) mới là nơi từng có định dạng số không đồng nhất (có dòng hiện dấu chấm phân cách nghìn kiểu Việt, có dòng không).

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
  ]},
  "trangThai": { "Creta CSC T9": "ACTIVE", "TUCSON CSC": "PAUSED" }
}
```
Mỗi object trong `records.lichSu` = 1 chiến dịch trong 1 ngày. Mới nhất lên đầu (`ngay` desc, cùng ngày thì theo tên chiến dịch).

**`trangThai`** (thêm 22/09/2026): trạng thái **ACTIVE/PAUSED thật** của từng chiến dịch tại thời điểm sync gần nhất — lấy riêng từ endpoint `/campaigns` (field `effective_status`, không suy luận qua có/không chi tiêu hôm nay), do `fetchFbAdsCampaignStatus_()` cập nhật mỗi giờ trong `fbAdsSync()`. Khác `records.lichSu` (tích luỹ theo ngày), `trangThai` là **snapshot hiện tại**, bị ghi đè hoàn toàn mỗi lần `fbAdsSync()` chạy thành công; nếu lần gọi API lấy trạng thái bị lỗi thì **giữ nguyên** giá trị cũ (không xoá trắng) — `backfillFbAds()`/`backfillFbAdsThisMonth()` cũng giữ nguyên field này khi ghi lại file (chỉ đụng vào `records.lichSu`), tránh vô tình xoá mất trạng thái khi backfill. `fbads.html` dùng để gắn badge "● Đang chạy"/"Tạm dừng" cạnh tên chiến dịch ở panel 2 + panel 3 — chiến dịch nào chưa có trong `trangThai` (chưa từng sync qua bản code mới) thì không hiện badge, không suy đoán bừa.

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

5 panel đánh số:
1. **Xu hướng Chi phí theo ngày** (luôn hiện) — tổng chi phí tất cả chiến dịch mỗi ngày, theo bộ lọc khoảng thời gian.
2. **Chi phí theo Nhóm & Kỳ** (luôn hiện, thêm 22/09/2026) — 3 bảng cạnh nhau: **CSC** (gộp theo **tháng**), **CDR** (gộp theo **quý**), **Khác** (chiến dịch không khớp CSC/CDR, gộp theo tháng). Phân nhóm bằng `phanLoaiChienDich()` — kiểm tra chuỗi con "CSC"/"CDR" trong tên chiến dịch (không phân biệt hoa/thường), không khớp cả 2 thì xếp vào "Khác". **Chỉ hiển thị số đã chi thực tế + kết quả Messenger, KHÔNG so sánh với ngân sách/mục tiêu** (quyết định có chủ đích theo yêu cầu người dùng 22/09/2026 — hệ thống chưa có nơi lưu số ngân sách, và người dùng xác nhận chỉ cần xem kết quả đã chi, không cần % sử dụng ngân sách). Panel này **luôn dùng toàn bộ `LICHSU`**, không theo bộ lọc khoảng thời gian/chiến dịch ở trên — vì mục đích là xem theo kỳ ngân sách cố định (tháng/quý dương lịch), không phải theo khoảng ngày tuỳ chọn.
3. **Cảnh báo hiệu quả theo Chiến dịch** (luôn hiện) — mục đích chính của dashboard, bảng xếp hạng theo **3 cấp ưu tiên** (hàm `xepLoaiHieuQua()`): (1) **Đang chạy lên trước Tạm dừng** (thêm 22/09/2026, theo yêu cầu người dùng — luôn thấy ngay các chiến dịch đang chạy; chiến dịch chưa rõ trạng thái coi như Tạm dừng), (2) trong cùng nhóm trạng thái, xếp theo mức độ cần chú ý `xau` → `canhbao` → `tot` → `na`, (3) cùng mức cần chú ý thì chi phí giảm dần. Tên chiến dịch ở panel 3 + panel 4 có kèm badge "● Đang chạy"/"Tạm dừng" (hàm `trangThaiBadge()`, dữ liệu từ `json.trangThai` — xem mục `fbads.json` ở trên) — badge này là trạng thái **thật** của chiến dịch trên Facebook, khác hẳn việc chiến dịch có/không xuất hiện trong `records.lichSu` của khoảng ngày đang lọc.
4. **Chi tiết chỉ số theo Chiến dịch** (thu gọn) — toàn bộ chỉ số tương tác cộng dồn theo chiến dịch.
5. **Nhật ký theo ngày** (thu gọn) — bảng thô từng dòng ngày × chiến dịch, mới nhất lên đầu.

Nếu sau này công ty muốn quay lại so sánh với ngân sách thật (số tiền dự kiến chi mỗi tháng/quý), cần thêm cơ chế lưu số ngân sách — gợi ý theo đúng khuôn mẫu `DEFAULT_TARGETS` ở `index.html` (hằng số mặc định trong code + nút "✏️ Chỉnh sửa" ghi đè vào `localStorage` riêng từng máy), không phải việc Claude tự bịa số.

Bộ lọc: khoảng thời gian (7 ngày / 30 ngày / toàn bộ lịch sử — nút bấm kiểu `.filter-btn` giống `crm.html`) + chọn chiến dịch.

## Trigger

`fbAdsSync`: 1 trigger `everyHours(1)` (24 lần/ngày, 24/7 — đổi từ `everyHours(6)` ngày 22/09/2026 khi chuyển sang tự gọi Facebook Graph API thay vì đợi tool ngoài; chi phí quảng cáo phát sinh liên tục cả ngoài giờ hành chính, khác Nhân sự/Chấm công chỉ cần đồng bộ giờ hành chính). Vẫn chỉ 1 trigger duy nhất (đổi tần suất lặp, không tạo thêm trigger) nên không ảnh hưởng hạn mức ~20 trigger/project.
