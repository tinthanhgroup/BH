# Module Quảng cáo Facebook (FB Ads)

`TinThanh_FBAds_Sync.gs` → `fbads.json` → `fbads.html` (nhúng iframe trong sub-tab "5.1. Quảng cáo Facebook" — mặc định mở khi vào tab — của tab 5 "MKT" trong `index.html`, xem `switchMktSub()`)

## Nguồn dữ liệu — điểm khác biệt quan trọng

**(Đổi kiến trúc 22/09/2026)** Trước đây 1 tool ngoài (Zapier/Make) tự đồng bộ dữ liệu Facebook Ads vào Sheet, `TinThanh_FBAds_Sync.gs` chỉ đọc ra. **Từ 22/09/2026, script tự gọi thẳng Facebook Graph API** (hàm `fetchFbAdsFromGraphApi_()`) — không còn phụ thuộc tool ngoài nào. Luồng đầy đủ mỗi lần `fbAdsSync()` chạy:
1. **Gọi Facebook Graph API** (`GET /{AD_ACCOUNT_ID}/insights?level=campaign&date_preset=today`) lấy snapshot chi phí/kết quả từng chiến dịch của "hôm nay".
2. **Ghi đè kết quả vào Google Sheet** (`writeFbAdsSnapshotToSheet_()`) — Sheet **vẫn được giữ lại** làm nơi lưu vết/audit thủ công (đọc bằng mắt khi cần), dù không còn là nguồn dữ liệu gốc nữa. Script tự dựng lại header (tên field thô kiểu Facebook, vd `data.campaign_name`) mỗi lần ghi, nên cột `paging.cursors.*` (rác phân trang từ tool cũ) không còn xuất hiện nữa.
3. Đọc lại chính Sheet vừa ghi (bước này giữ nguyên logic cũ, không đổi).
4. **GET** `fbads.json` hiện có trên GitHub (lấy cả `sha` lẫn nội dung).
5. **Upsert** theo khoá `ngày + "|" + tên chiến dịch` — dòng mới của "hôm nay" ghi đè đúng dòng cùng ngày/cùng chiến dịch trong lịch sử cũ, các ngày khác giữ nguyên.
6. Dọn bớt dòng cũ hơn `FBADS_CONFIG.GIU_LICH_SU_NGAY` (mặc định 180 ngày) để `fbads.json` không phình to vô hạn.
7. **PUT** lại lên GitHub.

**Sheet:** https://docs.google.com/spreadsheets/d/12Sl-Eus7-Y7va6pO_yXoV9mYyLfWBYQC3ARmY2KOlvg (sheet cũ trước 22/09/2026: `1bxsPkbKQ6XlDbf4H7oceolB_2IjuNWExWCJZeJE1m24`). Vẫn dùng đúng 1 Sheet này cho cả 3 chi nhánh (xem mục đa chi nhánh ngay dưới) — không phải 1 Sheet/chi nhánh.

## Đa chi nhánh (thêm 23/09/2026)

Công ty có 3 "cụm" ad account (mỗi cụm gộp vài chi nhánh vật lý dùng chung 1 ad account nhưng khác fanpage), khai báo trong mảng `FB_ACCOUNTS` ở `TinThanh_FBAds_Sync.gs`:

| `chiNhanh` | Ad Account | Token | Ghi chú |
|---|---|---|---|
| `Phú Yên` | `act_1469329274856353` ("Hyundai Phú Yên 2") | App "BC ADS" | Chiến dịch có "CSC" (`TUCSON CSC - T9`, `Creta CSC T9`...) |
| `Nha Trang` | `act_746712574524190` ("Hyundai Nha Trang 2") | App "BC ADS NT" | Gồm cả chiến dịch Phan Rang (vd `PHAN RANG CRETA T7 CDR`) — Facebook không tách được theo fanpage con trong cùng 1 ad account, nên "chiNhanh" chỉ dừng ở cấp CỤM ad account. Đặt tên chiến dịch theo đúng chuẩn CSC/CDR |
| `Đà Lạt` | `act_445197535037361` ("Hyundai Đà Lạt") | **Cùng token** với Nha Trang (App "BC ADS NT") — 1 token, 2 ad account khác nhau | Gồm cả chiến dịch Bảo Lộc. Đa số chiến dịch dạng "Bài viết: ..." (boost post), **không theo chuẩn CSC/CDR** → sẽ rơi vào nhóm "Khác" ở panel 2 của `fbads.html` |

Tất cả các hàm gọi Graph API (`fetchFbAdsFromGraphApi_()`, `fetchFbAdsCampaignStatus_()`, `backfillFbAds()`) đều nhận tham số `acc` (1 phần tử của `FB_ACCOUNTS`) và được `fbAdsSync()`/`backfillFbAds()` **lặp qua cả mảng** trong 1 lần chạy — 1 trigger duy nhất vẫn đồng bộ đủ cả 3 chi nhánh, không cần 3 trigger riêng. Muốn thêm/bớt chi nhánh: sửa mảng `FB_ACCOUNTS`, không cần sửa logic các hàm khác.

`ACCESS_TOKEN` của mỗi phần tử là **System User token thật** (`type: SYSTEM_USER`, `expires_at: 0` — không tự hết hạn, xác nhận qua `/debug_token`). **Tuyệt đối không thay bằng token cá nhân (type `USER`)** lấy nhanh từ Graph API Explorer — loại đó thường hết hạn trong vài giờ đến vài tuần, trigger sẽ âm thầm ngừng chạy mà không ai biết cho tới khi dữ liệu ngừng cập nhật (từng xảy ra với token thử nghiệm đầu tiên lúc thêm tính năng này, hết hạn cùng ngày). Nếu cần tạo token mới sau này (token bị thu hồi, app bị gỡ khỏi Business...): Meta Business Settings → Users → System Users → Generate New Token, quyền `ads_read`, Token Expiration = "Never", rồi thay đúng vào phần tử `FB_ACCOUNTS` tương ứng. Kiểm tra loại/hạn token bất cứ lúc nào bằng: `GET https://graph.facebook.com/v21.0/debug_token?input_token={TOKEN}&access_token={TOKEN}` — xem field `type` (phải là `SYSTEM_USER`) và `expires_at` (phải là `0`).

**Khoá upsert lịch sử** (mục GET-merge-PUT ở trên) đổi từ `ngày + "|" + tên chiến dịch` thành **`chiNhanh + "|" + ngày + "|" + tên chiến dịch`** — bắt buộc thêm `chiNhanh` để 2 chi nhánh lỡ đặt trùng tên chiến dịch không ghi đè nhầm nhau. Dữ liệu cũ trước 23/09/2026 (chỉ có Phú Yên, chưa có field `chiNhanh`) được tự động gán `chiNhanh: "Phú Yên"` ngay khi đọc lại trong `fbAdsSync()`/`backfillFbAds()`, không cần chạy script migrate riêng. Tương tự, khoá trong `trangThai` đổi từ `"Tên chiến dịch"` thành `"chiNhanh|Tên chiến dịch"` — khoá cũ (không có `|`) được tự chuẩn hoá thành `"Phú Yên|Tên chiến dịch"` khi đọc lại.

⚠️ **Sheet chỉ chứa snapshot chi phí/kết quả của "HÔM NAY" tại mọi thời điểm** (`data.date_start` cho mọi dòng luôn là ngày hiện tại, bị ghi đè mỗi lần `fbAdsSync()` chạy) — **không tự cộng dồn lịch sử** như các sheet khác trong repo (Nhân sự, MKT Fanpage...), kể cả sau khi đổi sang tự gọi API (Facebook Insights API bản chất cũng có thể tự điều chỉnh số liệu "hôm nay" trong ngày do độ trễ ghi nhận conversion, nên vẫn cần coi là "snapshot tạm" chứ không tổng hợp sẵn theo ngày như 1 API lịch sử đầy đủ). Vì vậy bước GET-merge-PUT (bước 4-7 ở trên) **vẫn bắt buộc giữ nguyên**, không được bỏ dù đã đổi nguồn ghi Sheet.

Nếu sau này viết thêm script tương tự đọc 1 nguồn "chỉ có hôm nay", **copy đúng cơ chế GET-merge-PUT này**, không copy kiểu "ghi đè thẳng" của `TinThanh_MKT_Sync.gs`/`TinThanh_NhanSu_Sync.gs` (2 script đó nguồn đã có đủ lịch sử nên ghi đè thẳng là đúng).

## Backfill lịch sử (nạp lại dữ liệu các ngày trong quá khứ)

Vì tính năng tự gọi Graph API mới bật 22/09/2026, `fbads.json` ban đầu **chỉ có dữ liệu từ ngày bật tính năng trở đi** — các ngày trước đó (kể cả cùng tháng) không tự có. Muốn xem "từ đầu tháng tới giờ" hay nạp lại khoảng ngày bất kỳ trong quá khứ, chạy tay 1 lần trong Apps Script editor:
- `backfillFbAdsThisMonth()` — nạp từ ngày 1 tháng hiện tại tới hôm qua (không đụng "hôm nay", để `fbAdsSync()` tự lo).
- `backfillFbAds('yyyy-MM-dd')` hoặc `backfillFbAds('yyyy-MM-dd', 'yyyy-MM-dd')` — nạp khoảng ngày tuỳ chọn (vd dò lại vài ngày bị thiếu do trigger lỗi).

- `backfillFbAdsFromJuly()` — nạp từ 01/07 năm hiện tại tới hôm qua (thêm 22/09/2026, theo yêu cầu xem lại dữ liệu xa hơn 1 tháng). Muốn mốc khác/năm khác thì gọi thẳng `backfillFbAds('yyyy-MM-dd')` thay vì sửa hàm này.

Cả 3 hàm dùng `time_increment=1` khi gọi Graph API để tách kết quả theo từng ngày, rồi upsert thẳng vào `fbads.json` qua đúng cơ chế GET-merge-PUT như `fbAdsSync()` — **không ghi vào Sheet** (Sheet chỉ giữ vai trò snapshot "hôm nay"). An toàn khi chạy lại nhiều lần / chồng ngày đã có sẵn (upsert theo khoá `chiNhanh+ngày+tên chiến dịch`, không tạo trùng dòng). Cả 3 hàm đều tự backfill cho **TẤT CẢ chi nhánh trong `FB_ACCOUNTS`** trong 1 lần gọi (thêm 23/09/2026) — không cần gọi riêng từng chi nhánh, kể cả khi mới thêm 1 chi nhánh mới vào mảng (chạy lại 1 trong 3 hàm này sẽ tự nạp lịch sử cho chi nhánh mới, chồng lên chi nhánh cũ vẫn an toàn nhờ upsert). Sau khi backfill xong, bộ lọc "30 ngày"/"toàn bộ lịch sử" có sẵn trong `fbads.html` sẽ tự hiển thị đúng dữ liệu mới nạp, không cần sửa gì ở `fbads.html`. Lưu ý `FBADS_CONFIG.GIU_LICH_SU_NGAY` (mặc định 180 ngày) vẫn áp dụng — backfill quá xa quá ngưỡng này sẽ bị lọc bỏ ngay sau khi nạp.

## Cấu trúc Sheet

Sheet chỉ có 1 tab (script tự lấy sheet **đầu tiên**, để trống `FBADS_CONFIG.SHEET_NAME` — điền tên tab nếu Sheet có nhiều tab), **dùng chung cho cả 3 chi nhánh** (không phải 1 Sheet/chi nhánh). Header do `writeFbAdsSnapshotToSheet_()` tự dựng lại mỗi lần chạy, là tên field thô của Facebook Graph API (không phải tên tiếng Việt), script đọc lại dò cột theo đúng tên trong `FBADS_COLUMN_MAP`, không phụ thuộc thứ tự cột. Có thêm cột `meta.chi_nhanh` (thêm 23/09/2026) — tên cột tự đặt, không phải field thật của Facebook, chỉ để lưu chi nhánh theo đúng cơ chế cột hiện có.

**Cột `data.spend`**: giá trị lấy trực tiếp từ field `spend` của Graph API (số nguyên VNĐ dạng chuỗi thuần, không có dấu phân cách nghìn). Script vẫn strip hết ký tự không phải chữ số trước khi `parseInt` (`parseSoFBADS_()`) để phòng hờ — giữ nguyên hàm này dù nguồn Sheet cũ (trước 22/09/2026, do tool ngoài ghi) mới là nơi từng có định dạng số không đồng nhất (có dòng hiện dấu chấm phân cách nghìn kiểu Việt, có dòng không).

## `fbads.json`

```json
{
  "updated_at": "...", "updated_vn": "dd/MM/yyyy HH:mm", "today": "yyyy-MM-dd",
  "total": <số dòng lịch sử>,
  "records": { "lichSu": [
    { "ngay": "yyyy-MM-dd", "chienDich": "...", "chiNhanh": "Phú Yên", "chiPhi": 43332,
      "linkClick": 0, "videoView": 125, "postEngagement": 128, "pageEngagement": 129,
      "like": 1, "comment": 1, "postReaction": 2,
      "postInteractionGross": 3, "postInteractionNet": 3,
      "messengerBatDau": 1, "messengerTraLoi": 0, "messengerTongKetNoi": 1,
      "messengerXemChaoMung": 2, "messengerTraLoiDauTien": 1, "messengerNhanTin2Cap": 0
    }
  ]},
  "trangThai": { "Phú Yên|Creta CSC T9": "ACTIVE", "Nha Trang|TUCSON CSC": "PAUSED" }
}
```
Mỗi object trong `records.lichSu` = 1 chiến dịch trong 1 ngày **của 1 chi nhánh** (thêm field `chiNhanh` 23/09/2026 — dữ liệu trước đó không có field này, đọc như "Phú Yên" mặc định, xem mục Đa chi nhánh ở trên). Mới nhất lên đầu (`ngay` desc, cùng ngày thì theo `chiNhanh` rồi tên chiến dịch).

**`trangThai`** (thêm 22/09/2026, đổi định dạng khoá 23/09/2026): trạng thái **ACTIVE/PAUSED thật** của từng chiến dịch tại thời điểm sync gần nhất — lấy riêng từ endpoint `/campaigns` (field `effective_status`, không suy luận qua có/không chi tiêu hôm nay), do `fetchFbAdsCampaignStatus_()` cập nhật mỗi giờ trong `fbAdsSync()`. Khoá là **`"chiNhanh|Tên chiến dịch"`** (không phải object lồng nhau theo chi nhánh — gộp thành 1 chuỗi khoá cho gọn). Khác `records.lichSu` (tích luỹ theo ngày), `trangThai` là **snapshot hiện tại**, bị ghi đè hoàn toàn mỗi lần `fbAdsSync()` chạy thành công cho ĐÚNG chi nhánh đó; nếu 1 chi nhánh gọi API lấy trạng thái bị lỗi thì **chỉ giữ nguyên** giá trị cũ của riêng chi nhánh đó (không xoá trắng, không ảnh hưởng 2 chi nhánh còn lại) — `backfillFbAds()`/`backfillFbAdsThisMonth()`/`backfillFbAdsFromJuly()` cũng giữ nguyên field này khi ghi lại file (chỉ đụng vào `records.lichSu`), tránh vô tình xoá mất trạng thái khi backfill. `fbads.html` dùng để gắn badge "● Đang chạy"/"Tạm dừng" cạnh tên chiến dịch ở panel 3 + panel 4 — chiến dịch nào chưa có trong `trangThai` (chưa từng sync qua bản code mới) thì không hiện badge, không suy đoán bừa.

**`baiViet`** (thêm 23/09/2026): link bài viết gốc trên Facebook của chiến dịch, nếu có — chỉ khả thi với chiến dịch dạng "boost 1 bài viết cụ thể" (creative của ad có field `effective_object_story_id`, định dạng `<page_id>_<post_id>`) — đa số chiến dịch CSC/CDR (Phú Yên/Nha Trang) **không có** field này (quảng cáo không gắn 1 bài cụ thể), chỉ chiến dịch dạng "Bài viết: ..." (chủ yếu Đà Lạt) mới có. Lấy qua `fetchFbAdsPostLinks_()` — gọi endpoint `/ads` (khác `/campaigns` — field này chỉ có ở cấp creative của từng ad, không có ở cấp campaign), ghép URL dạng `https://www.facebook.com/<page_id>/posts/<post_id>`. Khoá **`"chiNhanh|Tên chiến dịch"`** giống hệt `trangThai` (cùng cơ chế merge/fallback per-chi-nhánh khi lỗi, cùng được `backfillFbAds()` giữ nguyên khi ghi lại file). `fbads.html` dùng để bọc `<a>` quanh tên chiến dịch ở panel 3 + panel 4 (hàm `tenChienDichHtml()`) — chiến dịch không có trong `baiViet` thì hiện tên chiến dịch dạng text thường, không suy đoán/tạo link giả.

⚠️ **Giai đoạn chuyển tiếp** (từ lúc code multi-chi-nhánh được viết 23/09/2026 tới lúc người dùng thực sự dán code mới vào script.google.com): `trangThaiBadge()` trong `fbads.html` tự **fallback sang khoá cũ không tiền tố chi nhánh** (`TRANGTHAI[chiNhanh+"|"+chienDich] || TRANGTHAI[chienDich]`) — vì script CŨ (trước 23/09) vẫn ghi `trangThai` theo khoá cũ. Nhờ vậy badge vẫn hiện đúng ngay cả khi người dùng CHƯA kịp cập nhật script. Fallback này tự vô hiệu (không còn dòng nào khớp) sau khi script mới chạy ít nhất 1 lần — không cần dọn lại code, an toàn để giữ vĩnh viễn.

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

Theo đúng quy ước giao diện chung (xem CLAUDE.md gốc — `--navy`, `.wrap` 1280px, `chart-num`, Inter font...). Không dùng thư viện chart (đúng chủ trương "không build step" của repo) — biểu đồ xu hướng chi phí (panel 1) dựng bằng cột `<div>` CSS thuần (`.trend-bar`), chỉ gắn nhãn trực tiếp (số tiền) lên cột cao nhất — không ghi số lên mọi cột.

**Tooltip tuỳ chỉnh (đổi 23/09/2026 từ `title` mặc định trình duyệt)** — `title` có độ trễ hiện ~1s và style tuỳ theo OS, dễ bị hiểu nhầm là "chưa có" tính năng hover. Cơ chế mới: mỗi `.trend-bar-wrap` mang `data-tooltip` (chuỗi nhiều dòng, escape qua `esc()`), 1 cặp `mouseover`/`mouseleave` gắn 1 lần vào `#trendChartWrap` (event delegation, không đổi qua mỗi lần `renderTrend()` vẽ lại) đọc `data-tooltip` rồi gán vào `#trendTooltip` bằng `textContent` (không phải `innerHTML` — an toàn tuyệt đối, không lo XSS), định vị bằng `getBoundingClientRect()` so với `#trendChartWrap` (`position:relative`). Giá trị trong tooltip **chỉ hiện số** (hàm `fmtTrieu()`, vd `2.3`), không kèm chữ "triệu" — đơn vị đã ghi 1 lần trong mô tả panel (theo yêu cầu người dùng 23/09/2026); riêng nhãn trên cột cao nhất (`trend-bar-label`, luôn hiện không cần hover) vẫn giữ `fmtTrieuNhan()` có chữ "triệu" vì là số duy nhất hiện sẵn không có ngữ cảnh mô tả kèm theo.

### Định dạng số (đổi 23/09/2026, theo yêu cầu người dùng — khác nhau giữa panel 1 và các panel còn lại)

- **Panel 1 (biểu đồ xu hướng)**: hàm `fmtTrieu()`/`fmtTrieuNhan()` — làm tròn **triệu đồng, 1 số thập phân**, dùng dấu chấm thập phân kiểu Anh (vd `1.2 triệu`, không phải `1,2 triệu` kiểu Việt) — theo đúng ví dụ người dùng cho, khác quy ước `vi-VN` dùng ở nơi khác trong repo.
- **Các panel 2/3/4/5**: hàm `fmtNgan()` — làm tròn **chục nghìn đồng** (`Math.round(n/10000)*10`), hiển thị theo đơn vị **nghìn đồng** (không có hậu tố "đ" lặp lại mỗi ô — đơn vị ghi 1 lần trên header cột, vd "Chi phí (nghìn đ)"), dùng dấu phẩy phân cách nghìn kiểu Anh (`toLocaleString('en-US')`, vd `1,260`) — theo đúng ví dụ người dùng cho, KHÁC `fmtVND()` (đơn vị đồng đầy đủ, dấu chấm kiểu Việt, hậu tố "đ") vẫn dùng ở KPI row (không đổi vì không được yêu cầu).
- `fmtVND()` (đồng đầy đủ) vẫn giữ nguyên, chỉ còn dùng ở KPI row (panel KPI) và phần mô tả ngưỡng cảnh báo (`warnDesc`) — 2 chỗ này không phải "bảng" nên không thuộc phạm vi đổi định dạng theo yêu cầu.

### Escape HTML (phát hiện + sửa 23/09/2026)

Mọi chuỗi lấy từ dữ liệu (`chienDich`, `chiNhanh`) nội suy vào HTML **bắt buộc** đi qua hàm `esc()` trước — phát hiện lỗi thật khi thêm dữ liệu Đà Lạt: tên chiến dịch dạng "Bài viết: ..." lấy nguyên caption Facebook, chứa dấu ngoặc kép (vd `Bài viết: "🚀 BỨT PHÁ SỰ NGHIỆP..."`), nội suy thẳng không escape làm vỡ cấu trúc `<option value="...">` trong dropdown chọn chiến dịch (option sau đó bị trình duyệt hiểu nhầm thành nhiều thuộc tính rác) — **dropdown này đã bị bỏ hẳn 23/09/2026** (xem mục Bộ lọc bên dưới) nhưng `esc()` vẫn bắt buộc giữ nguyên vì `chienDich`/`chiNhanh` còn hiển thị ở nhiều chỗ khác (panel 3/4/5, tên file CSV...). Nút chọn Chi nhánh dùng `data-chinhanh` + `addEventListener` (event delegation, gắn 1 lần ở cuối file) thay vì `onclick` nội suy trực tiếp giá trị — tránh phải escape 2 lớp (thuộc tính HTML lồng chuỗi JS).

5 panel đánh số:
1. **Xu hướng Chi phí theo ngày (hoặc theo tháng)** (luôn hiện, **không còn đoạn mô tả `<p class="desc">`** — bỏ 23/09/2026 theo yêu cầu người dùng) — chi phí tất cả chiến dịch, **xếp chồng theo chi nhánh** (mỗi đoạn màu = 1 chi nhánh, màu cố định theo `CHI_NHANH_COLORS`/`CHI_NHANH_ORDER`: Phú Yên=`--navy`, Nha Trang=`--green`, Đà Lạt=`--accent`, chi nhánh mới chưa định nghĩa màu → xám `#9AA7BD`), theo bộ lọc khoảng thời gian + Chi nhánh. Có **legend** (`#trendLegend`) liệt kê chi nhánh đang xuất hiện trong dữ liệu đã lọc.

**Chiều cao panel cố định** (thêm 23/09/2026, sửa theo phản hồi người dùng: "khi ấn vào các nút lọc thì độ cao bảng thay đổi... khá khó chịu khi xem báo cáo") — nguyên nhân xác định qua đo thực tế (`getBoundingClientRect()`): khi bộ lọc không khớp dữ liệu nào, `renderTrend()` xoá trắng `#trendLabels`/`#trendLegend` (`innerHTML=''`), 2 hàng này tự co về 0px (không có `min-height` riêng) dù `#trendChart` vẫn giữ nguyên `220px` cố định — làm panel hụt đột ngột ~25px (đo được 322px → 297px). Sửa bằng class `.panel-trend{min-height:322px;}` gắn thêm vào `.panel` bọc panel 1 — dùng `min-height` (không phải `height` cứng) để vẫn co giãn tự nhiên nếu sau này legend cần xuống 2 dòng (thêm chi nhánh mới), chỉ chặn chiều **thấp hơn** mức bình thường (322px, đo lúc có đủ dữ liệu) — đúng nguồn gây khó chịu. Đã test lại 5 tổ hợp bộ lọc (kể cả ép buộc không khớp dữ liệu) — chiều cao panel luôn đúng 322px không đổi.

**Gộp theo ngày hay theo tháng phụ thuộc bộ lọc khoảng thời gian** (đổi 23/09/2026, biến `theoThang` trong `renderTrend()` = `state.range === 0`): chọn **"30 ngày gần nhất"** hoặc **"Quý hiện tại"** → mỗi cột = 1 **ngày** (có **vạch mờ** `.month-start` đánh dấu ranh giới giữa các tháng, trục X chỉ ghi tên tháng ở cột đầu tiên của mỗi tháng); chọn **"All time - Theo tháng"** (mặc định) → mỗi cột = 1 **tháng** (không cần vạch mờ nữa vì mỗi cột đã là 1 tháng, trục X ghi tên tháng ở MỌI cột) — tránh biểu đồ quá dày cột khi xem toàn bộ lịch sử nhiều tháng.

**Chế độ "Theo tháng" để sẵn các cột tới cuối năm** (thêm 23/09/2026, theo yêu cầu người dùng) — không dừng ở tháng cuối cùng có dữ liệu thật: từ tháng đầu tiên có dữ liệu, tự sinh thêm cột cho MỌI tháng còn lại tới hết tháng 12 của năm hiện tại (`new Date().getFullYear()`), kể cả chưa có dữ liệu (cột hiện rỗng, chiều cao tối thiểu 2% để vẫn thấy có cột, không nhãn) — giúp thấy trước "còn bao nhiêu tháng tới cuối năm" khi lên kế hoạch ngân sách. Ví dụ 23/09/2026: dữ liệu thật chỉ có T7-T9, biểu đồ vẫn hiện đủ 6 cột T7→T12/2026.

**Ghi số tiền trực tiếp trên biểu đồ** (thêm 23/09/2026, theo yêu cầu người dùng):
- **Tổng mỗi cột** — ghi ở MỌI cột có dữ liệu (trước đó chỉ ghi ở cột cao nhất duy nhất). Vị trí (`.trend-bar-label`) đổi từ `top:-18px` cố định sang `bottom:${h}%` gán qua inline style, khớp đúng đỉnh của TỪNG cột bất kể cao thấp khác nhau. **Định dạng khác nhau theo chế độ** (sửa 23/09/2026, theo phản hồi người dùng — bản đầu dùng chung 1 kiểu cho cả 2 chế độ gây chồng chữ ở "30 ngày gần nhất"):
  - Chế độ **theo tháng** (cột rộng ~184px, xem mục gap bên dưới): `fmtTrieuNhan()` — đầy đủ "X triệu", chỉ ghi nếu tổng cột ≥ ngưỡng `FBADS_TREND_NGUONG_GHI_SO` (2.000.000đ) — tránh cột quá nhỏ bị chữ tràn.
  - Chế độ **theo ngày, khi đủ rộng** (biến `duRongGhiNhanNgay` = số cột ≤ 40 — khớp "30 ngày gần nhất" ~30 cột, KHÔNG khớp "Quý hiện tại" ~85-92 cột quá hẹp): `fmtTrieu()` — số GỌN không kèm "triệu" (vd "1.2"), ghi **ĐẦY ĐỦ mọi cột có chi phí > 0** (không qua ngưỡng `FBADS_TREND_NGUONG_GHI_SO` — ngưỡng đó chỉ áp dụng chế độ theo tháng).
  - Chế độ **theo ngày, quá nhiều cột** (vd "Quý hiện tại"): không ghi tổng cột nào (cột ~11px, không đủ chỗ cho bất kỳ chữ số nào).
- **Số tiền từng chi nhánh** — ghi thẳng bên trong mỗi đoạn màu (`.trend-seg`, chữ trắng, căn giữa) — **CHỈ ở chế độ theo tháng** (đủ rộng), theo `fmtTrieu()` (số gọn), qua ngưỡng `FBADS_TREND_NGUONG_GHI_SO` như cũ.
- Chiều cao khung biểu đồ tăng từ `160px` → `220px` (`.trend-chart`) để có thêm không gian cho các nhãn mới.
- **Khoảng cách giữa các cột** (thêm 23/09/2026, tăng lên `80px` cùng ngày theo yêu cầu người dùng) — rộng hơn hẳn ở chế độ theo tháng (`gap:80px`, chỉ 6 cột nên đủ chỗ tách rõ từng tháng) so với chế độ theo ngày (`gap:3px`, giữ nguyên — ~90 cột, tăng lên sẽ tràn/cột quá hẹp). Đặt qua `chartEl.style.gap`/`labelsEl.style.gap` trong `renderTrend()` (không phải CSS cố định) — **bắt buộc đặt giống nhau cho `#trendChart` và `#trendLabels`** (2 hàng flex riêng biệt) để nhãn tháng ở trục X luôn thẳng hàng đúng dưới cột tương ứng.
- **Nhãn trục X ở chế độ theo tháng** (thêm 23/09/2026, theo yêu cầu người dùng): dùng `fmtThangNgan()` — rút gọn còn **"Tháng 7"** (không kèm năm, khác `fmtThangVN()` "Tháng 7/2026" vẫn dùng cho tooltip/panel 2), và **canh giữa cột** (`text-align:center` đặt qua inline style trên từng `.trend-tick`, ghi đè CSS class mặc định `text-align:left` — vốn dùng cho chế độ theo ngày để khớp vị trí vạch mờ `.month-start`, không đổi CSS class chung để tránh ảnh hưởng chế độ theo ngày).
2. **Chi phí theo Nhóm & Kỳ** (luôn hiện, thêm 22/09/2026, **cấu trúc theo đúng ảnh mẫu người dùng cung cấp 23/09/2026, không còn đoạn mô tả `<p class="desc">`**) — mỗi chi nhánh **1 bảng duy nhất**, 3 bảng xếp **ngang hàng** (`.budget-grid`, cột lưới = chi nhánh, xếp theo `CHI_NHANH_ORDER`). Cột trong mỗi bảng = **Tháng | CSC | Khác | CDR** (chi phí, đơn vị nghìn đồng); dòng = từng tháng có dữ liệu, mới nhất lên đầu, cuối bảng có dòng **"Tổng"** (`class="budget-total"`) cộng dồn từng cột qua mọi tháng đang hiện. Phân loại cột bằng `phanLoaiChienDich()` — tên chiến dịch chứa "CSC"/"CDR" (không phân biệt hoa/thường) vào đúng cột, còn lại vào "Khác". **CDR giờ gộp theo THÁNG** (đổi 23/09/2026 từ quý trước đó, theo yêu cầu người dùng — để nằm chung 1 bảng với CSC/Khác). Ô = 0 hoặc không có dữ liệu hiện **trống** (không ghi "0") — phân biệt trực quan "chi nhánh không chạy loại chiến dịch này" (vd Đà Lạt luôn trống cột CSC/CDR) khỏi số liệu thật. **Không có cột Kết quả Messenger** (bỏ theo yêu cầu người dùng) và **không so sánh với ngân sách/mục tiêu** (chưa có nơi lưu số ngân sách). Panel này **luôn dùng toàn bộ lịch sử của cả 3 chi nhánh**, **KHÔNG áp dụng bất kỳ bộ lọc nào phía trên** — kể cả bộ lọc Chi nhánh (đổi 23/09/2026, theo yêu cầu người dùng; trước đó có áp dụng bộ lọc Chi nhánh qua hàm `filteredByChiNhanh()` — hàm này đã bị xoá, `renderBudgetGroups()` giờ luôn nhận thẳng `LICHSU`). Chọn nút Chi nhánh nào ở bộ lọc trên cùng cũng không ảnh hưởng panel này — vẫn luôn hiện đủ 3 bảng.
3. **Cảnh báo hiệu quả theo Chiến dịch** (luôn hiện, có cột **STT** thêm 23/09/2026) — mục đích chính của dashboard, bảng xếp hạng theo **3 cấp ưu tiên** (hàm `xepLoaiHieuQua()`): (1) **Đang chạy lên trước Tạm dừng** (theo yêu cầu người dùng — luôn thấy ngay các chiến dịch đang chạy; chiến dịch chưa rõ trạng thái coi như Tạm dừng), (2) trong cùng nhóm trạng thái, xếp theo mức độ cần chú ý `xau` → `canhbao` → `tot` → `na`, (3) cùng mức cần chú ý thì chi phí giảm dần. Tên chiến dịch ở panel 3 + panel 4 có kèm badge "● Đang chạy"/"Tạm dừng" (hàm `trangThaiBadge(chiNhanh, chienDich)`, dữ liệu từ `json.trangThai` — xem mục `fbads.json` ở trên) — badge này là trạng thái **thật** của chiến dịch trên Facebook, khác hẳn việc chiến dịch có/không xuất hiện trong `records.lichSu` của khoảng ngày đang lọc. **Tên chiến dịch tự bấm được** (thêm 23/09/2026, hàm `tenChienDichHtml()`) nếu có trong `json.baiViet` (xem mục `fbads.json` ở trên) — mở link bài viết gốc trên Facebook ở tab mới; chiến dịch không có link (đa số CSC/CDR) vẫn hiện tên bình thường, không phải chữ bấm được. **Độ rộng cột cố định qua `<colgroup>`** (đổi 23/09/2026, theo yêu cầu người dùng, class `.warn-table` + `table-layout:fixed` — chỉ áp dụng riêng bảng này, không ảnh hưởng panel 4/5): STT 4%, Chi nhánh 11% (đủ rộng để "Nha Trang"/"Đà Lạt" không xuống dòng dù header đã bỏ `nowrap`), Chiến dịch 33% (rộng nhất, tên chiến dịch dài), 3 cột Chi phí/Kết quả/Chi phí-Kết quả **bằng nhau 12%** mỗi cột, Trạng thái 16%.

**Nút "⬇️ Xuất Excel"** (thêm 23/09/2026, theo yêu cầu người dùng — dùng để đối chiếu thanh toán với công ty): hàm `xuatExcelCanhBao()`, xuất đúng danh sách đang hiển thị trong bảng (theo bộ lọc khoảng ngày/Chi nhánh đang chọn — bộ lọc Chiến dịch đã bỏ, xem mục Bộ lọc bên dưới), 4 cột **STT, Chi nhánh, Tên chiến dịch, Số tiền**. Số tiền là **VNĐ nguyên, không làm tròn** (khác `fmtNgan()` dùng để hiển thị trên bảng — thanh toán cần số chính xác). Xuất file **`.csv`** (không phải `.xlsx` thật) — Excel mở CSV trực tiếp, không cần thư viện JS ngoài nào (giữ đúng chủ trương "không build step, không thư viện" đã áp dụng ở panel 1) — có thêm BOM UTF-8 (`﻿`) đầu file để Excel nhận đúng tiếng Việt có dấu, và escape CSV chuẩn RFC4180 (`csvEscape()`, bọc `"..."` + nhân đôi dấu ngoặc kép nội bộ) cho tên chiến dịch có chứa dấu phẩy/ngoặc kép (đã test thực tế với 16 chiến dịch "Bài viết: ..." của Đà Lạt/Nha Trang — escape đúng). Tên file tự gồm chi nhánh + khoảng ngày + ngày xuất, vd `CanhBaoHieuQua_TatCaChiNhanh_ToanBoLichSu_2026-09-23.csv`.
4. **Chi tiết chỉ số theo Chiến dịch** (thu gọn, có cột **STT**) — toàn bộ chỉ số tương tác cộng dồn theo chiến dịch.
5. **Nhật ký theo ngày** (thu gọn, có cột **STT**) — bảng thô từng dòng ngày × chiến dịch, mới nhất lên đầu.

Nếu sau này công ty muốn quay lại so sánh với ngân sách thật (số tiền dự kiến chi mỗi tháng/quý), cần thêm cơ chế lưu số ngân sách — gợi ý theo đúng khuôn mẫu `DEFAULT_TARGETS` ở `index.html` (hằng số mặc định trong code + nút "✏️ Chỉnh sửa" ghi đè vào `localStorage` riêng từng máy), không phải việc Claude tự bịa số.

**Đa chi nhánh (thêm 23/09/2026):** panel 3 + panel 4 giờ **cộng dồn theo cặp `chiNhanh + chienDich`** (hàm `aggregateByCampaign()`), không chỉ theo tên chiến dịch — tránh 2 chi nhánh lỡ trùng tên chiến dịch bị gộp nhầm làm 1 dòng. Cả 2 bảng + panel 5 (Nhật ký) đều thêm cột "Chi nhánh" ở đầu (sau cột STT nếu có).

Bộ lọc: khoảng thời gian + Chi nhánh dạng nút bấm. **Bộ lọc chọn Chiến dịch (dropdown) đã bỏ hẳn** (thêm 22/09/2026, bỏ 23/09/2026 theo yêu cầu người dùng — cùng lúc dọn theo: xoá hàm `updateChienDichOptions()`/`onFilterChange()`, xoá `state.chienDich`, xoá CSS `select{...}` dùng chung không còn phần tử nào tham chiếu).

**Khoảng thời gian** (đổi 23/09/2026, theo cấu trúc người dùng cung cấp — 3 nút, `state.range` nhận giá trị `30` (số) | `'quarter'` (chuỗi) | `0` (số), so sánh tường minh bằng `===` ở mọi chỗ dùng, KHÔNG dùng `> 0` vì `'quarter' > 0` luôn `false` do ép kiểu):
- **"30 ngày gần nhất"** (`state.range = 30`) — 30 ngày gần nhất tính tới hôm nay.
- **"Quý hiện tại"** (`state.range = 'quarter'`) — hàm `quyHienTaiRange()` tính khoảng `[đầu quý, cuối quý]` theo giờ local (vd hôm nay 23/09/2026 → quý 3 → `["2026-07-01","2026-09-30"]`).
- **"All time - Theo tháng"** (`state.range = 0`, mặc định lúc tải trang) — không lọc theo ngày (toàn bộ lịch sử), **VÀ** khiến biểu đồ panel 1 gộp cột theo THÁNG thay vì theo ngày (xem chi tiết ở mục panel 1 phía trên) — đây là lý do tên nút có "Theo tháng", không chỉ là đổi tên suông.

⚠️ **Lỗi đã sửa 23/09/2026**: `quyHienTaiRange()` (và bộ lọc "30 ngày" cũ) ban đầu dùng `d.toISOString().slice(0,10)` để lấy ngày dạng `yyyy-MM-dd` — bị quy đổi sang UTC nên **lùi mất 1 ngày với múi giờ trước UTC như Việt Nam** (UTC+7; vd nửa đêm 01/07 giờ VN vẫn còn là "2026-06-30" giờ UTC). Đã thêm hàm dùng chung `isoLocal(d)` (dùng `getFullYear()`/`getMonth()`/`getDate()` — getter theo giờ local, không quy đổi UTC) và thay thế toàn bộ chỗ cần "ngày hôm nay"/"ngày N ngày trước" theo giờ người xem. **Bất kỳ code mới nào cần định dạng ngày kiểu này đều phải dùng `isoLocal()`, không dùng `toISOString()`.**

**Chi nhánh dạng nút bấm** (đổi 23/09/2026 từ dropdown, theo yêu cầu người dùng — danh sách nút vẫn suy ra động từ dữ liệu, không hardcode). Chọn Chi nhánh sẽ **tự lọc lại danh sách chiến dịch** trong dropdown kế bên (hàm `updateChienDichOptions()`) — chỉ hiện chiến dịch thuộc đúng chi nhánh đang chọn, tránh nhầm lẫn khi 2 chi nhánh trùng tên chiến dịch. Đổi Chi nhánh sẽ tự bỏ chọn Chiến dịch cũ.

**Header bảng tự xuống dòng** (đổi 23/09/2026, theo yêu cầu người dùng) — bỏ `white-space:nowrap` ở `thead th` (CSS dùng chung cho mọi bảng) để header dài (vd "Kết quả (Messenger bắt đầu)") tự wrap xuống nhiều dòng thay vì kéo bảng rộng ra — bảng gọn hơn, nhất là panel 4 (nhiều cột).

## Trigger

`fbAdsSync`: 1 trigger `everyHours(1)` (24 lần/ngày, 24/7 — đổi từ `everyHours(6)` ngày 22/09/2026 khi chuyển sang tự gọi Facebook Graph API thay vì đợi tool ngoài; chi phí quảng cáo phát sinh liên tục cả ngoài giờ hành chính, khác Nhân sự/Chấm công chỉ cần đồng bộ giờ hành chính). Vẫn chỉ 1 trigger duy nhất (đổi tần suất lặp, không tạo thêm trigger) nên không ảnh hưởng hạn mức ~20 trigger/project.
