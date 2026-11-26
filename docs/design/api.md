# Đặc tả API dùng chung vCraft

> Trạng thái: chính thức (đồng bộ 2026-11-26) — bổ sung đầy đủ các endpoint đọc, ghi, upload, webhook và quy ước dùng chung cho toàn bộ 9 module ([SRS README](../srs/README.md), [ERD](erd.md), [Từ điển dữ liệu chi tiết](data-dictionary.md), [Danh mục mã lỗi](error-codes.md)).

## 1. Quy ước chung

- **Nguồn sự thật cho Field & DTO:** Tên trường JSON (`camelCase`), kiểu dữ liệu, độ dài, tập Enum và quy tắc ẩn/hiện trường nhạy cảm tuân theo [data-dictionary.md](data-dictionary.md). Khi bất kỳ thành viên nào thêm/sửa trường hoặc payload API, bắt buộc cập nhật đồng thời `data-dictionary.md` và `api.md` (hoặc file OpenAPI khi có code) trong cùng PR.

- **Base URL:** `/api/v1` (mọi đường dẫn bên dưới là đường dẫn tương đối sau `/api/v1`).
- **Xác thực & Phiên:** Phiên lưu server-side (`sessions`, HttpOnly Cookie hoặc Bearer token) hỗ trợ thu hồi tức thì (UC-04, UC-05, UC-34). Các API công khai hỗ trợ đọc phiên tùy chọn (optional auth) để phục vụ xem trước gian hàng `Đang chuẩn bị` (UC-08, UC-26) và ẩn nút tự mua/tự gửi RFQ.
- **Phân quyền (RBAC):** Mọi API `/admin/*` đọc quyền hiện hành từ `accounts.admin_status` và `admin_grants` trên từng request (không tin JWT claim tĩnh). Mọi API `/seller/*` kiểm tra quyền bán và `shops.owner_id === current_user_id`.
- **Định dạng lỗi chuẩn (JSON)** (chi tiết toàn bộ 157 mã lỗi & HTTP status xem tại [error-codes.md](error-codes.md)):

```json
{
  "code": "ERR-UCxx-yy",
  "message": "Thông điệp hiển thị cho người dùng",
  "details": {}
}
```

- **Mã HTTP chuẩn:** `200` (OK), `201` (Created), `204` (No Content), `301` (redirect slug chuẩn hóa, chỉ áp dụng khi tài nguyên thuộc phạm vi xem), `401` (chưa đăng nhập / hết phiên `ERR-UC04-05`), `403` (thiếu quyền), `404` (không tìm thấy hoặc ngoài phạm vi công khai), `409` (xung đột phiên bản / trạng thái đã thay đổi / khóa đồng thời), `422` (dữ liệu không hợp lệ).
- **Chống gửi lặp (Idempotency):** Các thao tác tạo đơn (`POST /checkout`, `POST /rfqs/{id}/accept`) và hoàn tiền bắt buộc truyền header `Idempotency-Key`.
- **Khóa lạc quan (Optimistic Locking):** Các thao tác sửa/duyệt có cạnh tranh (`certificates`, `articles`, `shops` trạng thái) truyền `version` hiện tại; lệch phiên bản trả `409`.
- **Phân trang:**
  - Danh sách quản trị/lọc: `?page=1&limit=20` trả `{ items, page, limit, total, totalPages }`.
  - Danh sách tải thêm (Sản phẩm UC-10, Thông báo UC-29): `?cursor=...&limit=12` (hoặc `20`) trả `{ items, nextCursor, hasMore }`.
- **Tệp & Ảnh (`assets`):**
  - Ảnh công khai (xưởng, sản phẩm, mốc QR, tin công khai) trả URL gốc + `thumbnailUrl`.
  - Ảnh/tệp nhạy cảm (minh chứng hồ sơ UC-01/02, ảnh & tệp của tin Nháp/Ẩn/Nội bộ UC-27/28) **không dùng URL công khai tĩnh**, bắt buộc truy cập qua `GET /assets/{id}/content` (kiểm tra phiên & quyền hoặc cấp signed URL ngắn hạn).

## 2. Danh sách endpoint theo nhóm chức năng

### 2.1 Hạ tầng Media dùng chung

| Method | Path | Vai trò | Mô tả | UC |
|---|---|---|---|---|
| `POST` | `/assets/upload` | Buyer, Artisan, Admin | Upload ảnh (tự sinh thumbnail) hoặc tệp đính kèm, validate MIME/size thực tế ở backend | Dùng chung |
| `GET` | `/assets/{id}/content` | Theo chủ thể | Tải ảnh/tệp có kiểm tra phiên & phạm vi (minh chứng hồ sơ, tệp tin nội bộ/nháp/ẩn) | UC-01, 02, 27, 28 |

### 2.2 Nhóm `01-account` (UC-01 → UC-05)

| Method | Path | Vai trò | Mô tả | UC |
|---|---|---|---|---|
| `POST` | `/auth/register` | Guest | Đăng ký tài khoản mua hàng, tự đăng nhập và gộp giỏ Guest | UC-03 |
| `POST` | `/auth/login` | Guest | Đăng nhập (kiểm tra khóa 5 lần sai/15p), tạo phiên, gộp giỏ Guest | UC-04 |
| `POST` | `/auth/google` | Guest | Đăng nhập bằng Google OAuth (mức Should) | UC-04 |
| `POST` | `/auth/logout` | Đã đăng nhập | Đăng xuất, thu hồi phiên hiện tại | UC-04 |
| `GET` | `/me` | Đã đăng nhập | Lấy hồ sơ hiện tại, vai trò, trạng thái hồ sơ bán hàng và nhóm quyền Admin | UC-04, 05 |
| `PATCH` | `/me` | Đã đăng nhập | Cập nhật họ tên, số điện thoại | UC-05 |
| `POST` | `/me/password` | Đã đăng nhập | Đổi mật khẩu và thu hồi mọi phiên khác | UC-05 |
| `GET` | `/me/seller-applications` | Buyer, Artisan | Xem hồ sơ bán hàng hiện tại và lịch sử các lần nộp | UC-01 |
| `PUT` | `/me/seller-applications/draft` | Buyer | Lưu bản nháp hồ sơ bán hàng | UC-01 |
| `POST` | `/me/seller-applications` | Buyer | Nộp mới hoặc nộp lại hồ sơ bán hàng, phát `NT-UC01-01`/`02` | UC-01 |
| `GET` | `/admin/applications` | Admin (`MODERATION_CATALOG`) | Danh sách hồ sơ bán hàng (sắp xếp `submitted_at` sớm nhất trước) | UC-02 |
| `GET` | `/admin/applications/{id}` | Admin (`MODERATION_CATALOG`) | Chi tiết hồ sơ kèm ảnh minh chứng riêng tư | UC-02 |
| `POST` | `/admin/applications/{id}/decisions` | Admin (`MODERATION_CATALOG`) | Duyệt (cấp quyền bán + tạo `shops` trong 1 tx), Yêu cầu bổ sung hoặc Từ chối | UC-02 |
| `GET` | `/admin/shop-changes` | Admin (`MODERATION_CATALOG`) | Danh sách yêu cầu đổi tên/địa chỉ xưởng (kèm ghim đề nghị) | UC-02 |
| `POST` | `/admin/shop-changes/{id}/decisions` | Admin (`MODERATION_CATALOG`) | Duyệt (cập nhật đồng thời địa chỉ + tọa độ ghim) hoặc Từ chối | UC-02 |
| `GET` | `/admin/certificates` | Admin (`MODERATION_CATALOG`) | Danh sách chứng nhận xưởng chờ đối chiếu | UC-02 |
| `POST` | `/admin/certificates/{id}/verify` | Admin (`MODERATION_CATALOG`) | Xác minh chứng nhận (kiểm tra `version` chống race) | UC-02 |

### 2.3 Nhóm `02-showcase` (UC-06 → UC-13)

| Method | Path | Vai trò | Mô tả | UC |
|---|---|---|---|---|
| `GET` | `/home/categories` | Public | Khối 1 trang chủ: phân loại đang hiển thị (tải độc lập theo UC-06 BR-3) | UC-06 |
| `GET` | `/home/new-shops` | Public | Khối 2 trang chủ: xưởng mới mở (xếp theo `first_opened_at DESC`) | UC-06 |
| `GET` | `/home/latest-products` | Public | Khối 3 trang chủ: sản phẩm mới nhất (`created_at DESC`) | UC-06 |
| `GET` | `/home/public-news` | Public | Khối 4 trang chủ: tin tức công khai mới nhất | UC-06 |
| `GET` | `/categories` | Public | Danh sách phân loại đang hiển thị | UC-12 |
| `GET` | `/admin/categories` | Admin (`MODERATION_CATALOG`) | Danh sách quản trị phân loại kèm số sản phẩm đang hiển thị | UC-12 |
| `POST` | `/admin/categories` | Admin (`MODERATION_CATALOG`) | Tạo phân loại mới (unique tên không phân biệt hoa thường) | UC-12 |
| `PATCH` | `/admin/categories/{id}` | Admin (`MODERATION_CATALOG`) | Sửa tên, thứ tự hoặc Ẩn/Hiện lại phân loại | UC-12 |
| `GET` | `/seller/shop` | Artisan | Lấy thông tin gian hàng, chứng nhận, phương thức giao, yêu cầu đổi đang chờ | UC-07 |
| `PATCH` | `/seller/shop` | Artisan | Cập nhật câu chuyện, SĐT công khai, ảnh, chứng nhận, phương thức giao | UC-07 |
| `POST` | `/seller/shop/change-requests` | Artisan | Gửi yêu cầu đổi tên hoặc địa chỉ (kèm tọa độ ghim đề nghị) | UC-07 |
| `GET` | `/seller/shop/checklist` | Artisan | Kiểm tra 7 tiêu chí đủ điều kiện mở / mở lại gian hàng | UC-13 |
| `POST` | `/seller/shop/status` | Artisan | Mở gian hàng (`PREPARING → ACTIVE`, ghi `first_opened_at`), Tạm ngừng hoặc Mở lại | UC-13 |
| `GET` | `/shops` | Public | Khám phá xưởng (tìm không dấu, lọc phân loại & đang đón khách) | UC-08 |
| `GET` | `/shops/{slugOrId}` | Public / Owner | Chi tiết gian hàng (chủ xưởng xem trước được khi `PREPARING`) | UC-08 |
| `GET` | `/seller/products` | Artisan | Danh sách sản phẩm của xưởng | UC-09 |
| `GET` | `/seller/products/{id}` | Artisan | Chi tiết sản phẩm để chỉnh sửa | UC-09 |
| `POST` | `/seller/products` | Artisan | Tạo sản phẩm Nháp (`category_id` tùy chọn) hoặc Hiển thị | UC-09 |
| `PATCH` | `/seller/products/{id}` | Artisan | Sửa sản phẩm, tồn kho, chuyển trạng thái `DRAFT/PUBLISHED/HIDDEN` | UC-09 |
| `GET` | `/products` | Public | Danh sách, tìm kiếm không dấu, lọc đa tiêu chí, load-more 12 SP | UC-10 |
| `GET` | `/products/suggest` | Public | Gợi ý tự động tên sản phẩm (tối đa 8 gợi ý) | UC-10 |
| `GET` | `/products/{slugOrId}` | Public / Owner | Chi tiết sản phẩm kèm gallery và danh sách lô công khai | UC-11 |

### 2.4 Nhóm `03-traceability` (UC-14 → UC-17)

| Method | Path | Vai trò | Mô tả | UC |
|---|---|---|---|---|
| `GET` | `/seller/lots` | Artisan | Danh sách lô sản xuất của xưởng (lọc theo nguồn SP/đơn DR, trạng thái) | UC-14 |
| `GET` | `/seller/lots/{id}` | Artisan | Chi tiết lô, các mốc nhật ký và ghi chú đính chính | UC-14 |
| `POST` | `/seller/lots` | Artisan | Tạo lô sản xuất (đơn DR bắt buộc `Đang sản xuất`), tự sinh `code` và `qr_token` | UC-14 |
| `PATCH` | `/seller/lots/{id}` | Artisan | Sửa thông tin lô (khi còn `Đang thực hiện`), bật/tắt `list_on_product` | UC-14 |
| `POST` | `/seller/lots/{id}/state` | Artisan | Chuyển trạng thái `IN_PROGRESS ↔ COMPLETED → SHIPPED` (row-lock `lots`) | UC-14 |
| `POST` | `/seller/lots/{id}/steps` | Artisan | Thêm mốc nhật ký sản xuất kèm 1–10 ảnh | UC-15 |
| `PATCH` | `/seller/lots/{id}/steps/{stepId}` | Artisan | Sửa mốc (đánh dấu `is_edited = true` nếu `ever_completed = true`) | UC-15 |
| `DELETE` | `/seller/lots/{id}/steps/{stepId}` | Artisan | Xóa mốc (khi lô đang `IN_PROGRESS`) | UC-15 |
| `POST` | `/seller/lots/{id}/corrections` | Artisan | Thêm ghi chú đính chính bất biến ở mức lô | UC-15 |
| `GET` | `/seller/lots/{id}/qr` | Artisan | Xem QR và tải PNG / dữ liệu in tem 1–1000 tem (chặn tải/in nếu chưa `COMPLETED`) | UC-16 |
| `GET` | `/trace/{qrToken}` | Public | Trang truy xuất nguồn gốc công khai (DTO riêng chống lộ Buyer/đơn DR) | UC-17 |

### 2.5 Nhóm `04-quotation` (UC-18 → UC-24)

| Method | Path | Vai trò | Mô tả | UC |
|---|---|---|---|---|
| `GET` | `/rfqs/eligible-shops` | Buyer | Danh sách xưởng đủ điều kiện nhận RFQ theo phân loại (loại xưởng của chính Buyer) | UC-18 |
| `POST` | `/rfqs` | Buyer | Tạo nháp hoặc gửi RFQ | UC-18 |
| `PATCH` | `/rfqs/{id}` | Buyer | Sửa bản nháp RFQ hoặc gửi bản nháp | UC-18 |
| `DELETE` | `/rfqs/{id}` | Buyer | Xóa bản nháp RFQ | UC-18 |
| `POST` | `/rfqs/{id}/extend` | Buyer | Gia hạn nhận báo giá (tối đa 1 lần) | UC-18 |
| `POST` | `/rfqs/{id}/clone` | Buyer | Nhân bản nháp từ RFQ đã đóng/hủy | UC-18 |
| `POST` | `/rfqs/{id}/cancel` | Buyer | Hủy RFQ trước khi chấp nhận (khóa `rfqs`, đóng báo giá) | UC-23 |
| `GET` | `/rfqs` | Buyer | Danh sách RFQ của Buyer | UC-21 |
| `GET` | `/rfqs/{id}` | Buyer | Chi tiết RFQ, bảng so sánh báo giá và lịch sử `rfq_events` | UC-20, 21 |
| `POST` | `/rfqs/{id}/accept` | Buyer | Chấp nhận báo giá (`quoteId`, `revision`, `Idempotency-Key`) → tạo đơn DR + chụp `deposit_due_at` + link ZaloPay | UC-20 |
| `POST` | `/quotes/{id}/request-update` | Buyer | Gửi Yêu cầu cập nhật giá cho báo giá hết hiệu lực (hạn 3 ngày) | UC-20 |
| `GET` | `/seller/invitations` | Artisan | Danh sách lời mời RFQ của xưởng | UC-21 |
| `GET` | `/seller/invitations/{id}` | Artisan | Chi tiết lời mời (che PII Buyer trước khi chốt) và các phiên bản báo giá của xưởng | UC-19, 21 |
| `POST` | `/seller/invitations/{id}/quotes` | Artisan | Gửi mới hoặc cập nhật phiên bản báo giá (xưởng tự đặt `deposit_deadline_days` 1–14 ngày khi cọc > 0%) | UC-19 |
| `POST` | `/seller/invitations/{id}/decline` | Artisan | Từ chối nhận RFQ kèm lý do | UC-19 |
| `POST` | `/seller/invitations/{id}/decline-update` | Artisan | Từ chối Yêu cầu cập nhật giá của Buyer | UC-19 |
| `GET` | `/custom-orders` | Buyer, Artisan | Danh sách đơn đặt riêng theo vai trò (`?role=buyer\|seller`) | UC-21 |
| `GET` | `/custom-orders/{id}` | Buyer, Artisan | Chi tiết đơn đặt riêng, thanh toán cọc và lịch sử | UC-21 |
| `POST` | `/custom-orders/{id}/deposit-retry` | Buyer | Tạo lại giao dịch cọc ZaloPay trong hạn `deposit_due_at` | UC-20 |
| `POST` | `/custom-orders/{id}/transitions` | Artisan, Buyer | Chuyển trạng thái sản xuất/giao hàng/xác nhận nhận hàng | UC-22 |
| `POST` | `/custom-orders/{id}/collect-balance` | Artisan | Xác nhận đã thu phần tiền còn lại (hợp lệ cả sau khi Hoàn tất) | UC-22 |
| `GET` | `/custom-orders/{id}/lots` | Buyer | Xem danh sách lô và nhật ký sản xuất của đơn đặt riêng của mình | UC-24 |

### 2.6 Nhóm `05-village-map` (UC-25 → UC-26)

| Method | Path | Vai trò | Mô tả | UC |
|---|---|---|---|---|
| `GET` | `/seller/shop/visiting` | Artisan | Lấy tọa độ hiện tại, trạng thái khóa ghim (`shop_changes`), lịch tuần, lịch tắt, ghi chú | UC-25 |
| `GET` | `/seller/shop/geocode` | Artisan | Gợi ý tọa độ `(lat, lng)` theo địa chỉ chữ qua dịch vụ geocoding backend | UC-25 |
| `PUT` | `/seller/shop/location` | Artisan | Lưu tọa độ ghim (bị chặn `ERR-UC25-07` khi đang có yêu cầu đổi địa chỉ `PENDING`) | UC-25 |
| `PUT` | `/seller/shop/visiting-hours` | Artisan | Lưu chế độ đón khách, lịch tuần (`enabled` + giờ) và tối đa 20 lịch tắt | UC-25 |
| `GET` | `/map/pins` | Public / Owner | Danh sách pin xưởng đã mở (kèm pin xem trước nếu chủ xưởng `PREPARING` đang đăng nhập) | UC-26 |
| `GET` | `/map/pins/{shopId}` | Public / Owner | Popup chi tiết xưởng, ảnh đại diện, dòng trạng thái giờ hiện tại và lịch 7 ngày (UTC+7) | UC-26 |

### 2.7 Nhóm `06-news` (UC-27 → UC-28)

| Method | Path | Vai trò | Mô tả | UC |
|---|---|---|---|---|
| `GET` | `/admin/articles` | Admin (`NEWS`) | Danh sách tin tức quản trị (lọc trạng thái/loại/phạm vi, 20 tin/trang) | UC-27 |
| `GET` | `/admin/articles/{id}` | Admin (`NEWS`) | Chi tiết tin để sửa | UC-27 |
| `POST` | `/admin/articles` | Admin (`NEWS`) | Tạo tin Nháp hoặc Đăng ngay (sanitize HTML, phát `NT-UC27-01` lần đăng đầu) | UC-27 |
| `PATCH` | `/admin/articles/{id}` | Admin (`NEWS`) | Sửa nội dung hoặc chuyển trạng thái `DRAFT → PUBLISHED ↔ HIDDEN` (kiểm tra `version`) | UC-27 |
| `DELETE` | `/admin/articles/{id}` | Admin (`NEWS`) | Xóa tin ở trạng thái `DRAFT` | UC-27 |
| `GET` | `/news` | Public | Danh sách tin công khai (Khu mua hàng) | UC-28 |
| `GET` | `/news/{slugAndId}` | Public | Chi tiết tin công khai (301 redirect slug nếu hợp lệ; `ERR-UC28-01` nếu ngoài phạm vi) | UC-28 |
| `GET` | `/seller/news` | Artisan | Danh sách tin công khai + nội bộ (Kênh người bán) | UC-28 |
| `GET` | `/seller/news/{slugAndId}` | Artisan | Chi tiết tin công khai/nội bộ kèm link tải tệp đính kèm có xác thực | UC-28 |

### 2.8 Nhóm `07-notification` (UC-29)

| Method | Path | Vai trò | Mô tả | UC |
|---|---|---|---|---|
| `GET` | `/me/notifications/unread-count` | Đã đăng nhập | Trả số thông báo chưa đọc (dùng cho polling 60 giây) | UC-29 |
| `GET` | `/me/notifications` | Đã đăng nhập | Danh sách thông báo phân trang cursor (20 dòng/lần, mới nhất trước) | UC-29 |
| `GET` | `/me/notifications/{id}` | Đã đăng nhập | Mở trực tiếp thông báo (trả cùng `ERR-UC29-03` nếu không tồn tại hoặc của người khác) | UC-29 |
| `POST` | `/me/notifications/{id}/read` | Đã đăng nhập | Đánh dấu đã đọc (idempotent) | UC-29 |
| `POST` | `/me/notifications/read-all` | Đã đăng nhập | Đánh dấu tất cả là đã đọc | UC-29 |

### 2.9 Nhóm `08-order` (UC-30 → UC-33)

| Method | Path | Vai trò | Mô tả | UC |
|---|---|---|---|---|
| `GET` | `/cart` | Buyer | Lấy giỏ hàng nhóm theo xưởng + nhóm "Không khả dụng" (giá & tồn realtime) | UC-30 |
| `POST` | `/cart/items` | Buyer | Cộng thêm số lượng sản phẩm vào giỏ | UC-30 |
| `PUT` | `/cart/items/{productId}` | Buyer | Đặt lại số lượng cụ thể của món trong giỏ | UC-30 |
| `DELETE` | `/cart/items/{productId}` | Buyer | Xóa món khỏi giỏ | UC-30 |
| `POST` | `/cart/merge` | Buyer | Gộp giỏ Guest từ trình duyệt vào tài khoản (cap theo tồn kho) | UC-30 |
| `POST` | `/checkout/preview` | Buyer | Kiểm tra lại giá, tồn, phương thức giao & tính phí giao (kèm fallback GHN) | UC-31 |
| `POST` | `/checkout` | Buyer | Khóa `products` theo thứ tự ID, trừ tồn nguyên tử, tạo đơn (`Idempotency-Key`) + link ZaloPay | UC-31 |
| `POST` | `/orders/{id}/payment-retry` | Buyer | Tạo lại phiên thanh toán online trong hạn 15 phút | UC-31 |
| `GET` | `/orders/{id}/payment-status` | Buyer | Tra cứu trạng thái thanh toán thủ công từ cổng | UC-31 |
| `POST` | `/payments/webhooks/{provider}` | Payment Gateway | Webhook callback ZaloPay/VNPay (xác minh chữ ký & số tiền, idempotent, tự hoàn nếu đến muộn) | UC-31, 20, 33 |
| `GET` | `/orders` | Buyer, Artisan | Danh sách đơn mua hàng lọc theo 8 trạng thái, mã đơn (`?role=buyer\|seller`) | UC-32 |
| `GET` | `/orders/{id}` | Buyer, Artisan | Chi tiết đơn mua hàng, thanh toán và lịch sử `order_events` | UC-32 |
| `POST` | `/orders/{id}/transitions` | Artisan, Buyer | Xác nhận, từ chối, chuẩn bị, giao hàng, xác nhận nhận hàng / thu COD | UC-32 |
| `POST` | `/orders/{id}/cancel` | Buyer, Artisan | Hủy trực tiếp đơn MH hoặc đơn DR (xưởng hủy `Đang chuẩn bị` truyền `restocked_qty` từng dòng) | UC-33 |
| `POST` | `/orders/{id}/cancel-requests` | Buyer | Gửi yêu cầu hủy đơn DR đang sản xuất kèm lý do | UC-33 |
| `POST` | `/orders/{id}/cancel-requests/{reqId}/withdraw` | Buyer | Rút yêu cầu hủy đang chờ xưởng | UC-33 |
| `POST` | `/orders/{id}/cancel-requests/{reqId}/resolve` | Artisan | Đồng ý (chọn `KEEP` \| `REFUND` cọc) hoặc Từ chối yêu cầu hủy | UC-33 |

### 2.10 Nhóm `09-portal` (UC-34 → UC-35)

| Method | Path | Vai trò | Mô tả | UC |
|---|---|---|---|---|
| `GET` | `/admin/admins` | Super Admin | Danh sách tài khoản Admin, nhóm quyền và trạng thái `ACTIVE`/`LOCKED` | UC-34 |
| `POST` | `/admin/admins` | Super Admin | Tạo tài khoản Admin mới kèm ít nhất 1 nhóm quyền | UC-34 |
| `PATCH` | `/admin/admins/{id}` | Super Admin | Sửa họ tên, SĐT và thay tập nhóm quyền trong cùng giao dịch | UC-34 |
| `POST` | `/admin/admins/{id}/lock` | Super Admin | Khóa/Mở khóa Admin (khóa thu hồi toàn bộ phiên tức thì) | UC-34 |
| `POST` | `/admin/admins/{id}/reset-password` | Super Admin | Đặt lại mật khẩu Admin và thu hồi toàn bộ phiên | UC-34 |
| `GET` | `/admin/monitor/summary` | Admin (`TRANSACTION_MONITORING`) | 7 chỉ số theo kỳ (UTC+7) + tồn đọng chờ hoàn tiền toàn lịch sử (từ bảng `refunds`) | UC-35 |
| `GET` | `/admin/monitor/shops` | Admin (`TRANSACTION_MONITORING`) | Danh sách xưởng đã duyệt cho dropdown bộ lọc | UC-35 |
| `GET` | `/admin/monitor/{kind}` | Admin (`TRANSACTION_MONITORING`) | Danh sách chỉ đọc theo tab (`ready-orders` \| `custom-orders` \| `rfqs`), lọc trạng thái/thanh toán/xưởng/mã | UC-35 |
| `GET` | `/admin/monitor/{kind}/{id}` | Admin (`TRANSACTION_MONITORING`) | Chi tiết chỉ đọc kèm liên kết chéo YC ↔ DR ↔ Lô/QR ↔ Thanh toán/Hoàn tiền (`FAILED`) | UC-35 |
