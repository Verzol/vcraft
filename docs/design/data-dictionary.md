# Từ điển Dữ liệu (Data Dictionary)

> Cập nhật: 2026-11-26 · Liên quan: [ERD](erd.md) · [Đặc tả API](api.md) · [Danh mục mã lỗi](error-codes.md) · [Hợp đồng liên nhóm](../srs/README.md)
>
> Quy định danh sách cột, kiểu dữ liệu, ràng buộc kiểm tra, Enum và phạm vi trả về trên API cho 28 bảng của vCraft.

---

## 1. Quy ước chung & Quy trình cập nhật

### 1.1 Quy ước kỹ thuật

- **Đặt tên:** Cột CSDL dùng `snake_case`; JSON API dùng `camelCase` theo bảng ánh xạ bên dưới.
- **Khóa chính (`id`):** `UUID`, dùng thống nhất cho toàn bộ khóa chính và khóa ngoại `*_id`.
- **Tiền tệ:** `BIGINT` (số nguyên VND, không dùng kiểu số thực).
- **Thời gian:** Lưu `TIMESTAMPTZ` theo UTC; API trả ISO-8601 UTC (`YYYY-MM-DDTHH:mm:ssZ`). Các mốc hạn và lịch đón khách tính theo `Asia/Ho_Chi_Minh` (`UTC+7`). Ngày thuần dùng `DATE` (`YYYY-MM-DD`), giờ thuần dùng `TIME` (`HH:mm`).
- **Tọa độ:** `DECIMAL(9,6)` (`latitude` ∈ `[-90, 90]`, `longitude` ∈ `[-180, 180]`).
- **Phạm vi trả về API (Visibility):**
  - `Public`: Trả cho mọi đối tượng, bao gồm Guest.
  - `Owner`: Chỉ chủ tài khoản, chủ xưởng hoặc chủ đơn.
  - `Buyer/Seller`: Chỉ Buyer và Xưởng tham gia giao dịch (ẩn thông tin liên hệ trước khi chốt báo giá).
  - `Admin`: Chỉ Admin có đúng nhóm quyền.
  - `Internal`: Không trả ra API (ví dụ `password_hash`, `token_hash`).

### 1.2 Quy trình cập nhật khi thay đổi Schema hoặc API

Mọi PR thay đổi cột dữ liệu (thêm, xóa, đổi kiểu, đổi validation, đổi enum) hoặc payload API cần thực hiện đủ 3 bước trong cùng PR:

1. Cập nhật bảng tương ứng tại [data-dictionary.md](data-dictionary.md) (và [erd.md](erd.md) nếu thêm bảng hoặc đổi khóa ngoại).
2. Cập nhật endpoint/payload tại [api.md](api.md) (và `schema.prisma` / OpenAPI khi đã có mã nguồn).
3. Liệt kê thay đổi ở mục **Schema/API Changes** trong mô tả PR và tag thành viên phụ trách module liên quan review trước khi merge.

---

## 2. Danh mục Enum dùng chung toàn hệ thống

| Tên Enum | Giá trị hợp lệ | Ghi chú |
|---|---|---|
| `AccountType` | `BUYER`, `ARTISAN`, `ADMIN`, `SUPER_ADMIN` | `BUYER` nâng lên `ARTISAN` khi duyệt UC-02 |
| `AdminStatus` | `ACTIVE`, `LOCKED` | Chỉ áp dụng với `ADMIN` / `SUPER_ADMIN` |
| `PermissionGroup` | `MODERATION_CATALOG`, `NEWS`, `TRANSACTION_MONITORING` | 3 nhóm cố định UC-34 |
| `ApplicationStatus` | `DRAFT`, `PENDING`, `NEEDS_REVISION`, `APPROVED`, `REJECTED` | UC-01, UC-02 |
| `ShopStatus` | `PREPARING`, `ACTIVE`, `PAUSED` | UC-13 (`PREPARING` không quay lại) |
| `ShopChangeStatus` | `PENDING`, `APPROVED`, `REJECTED` | UC-07, UC-02 |
| `CertificateVerification` | `SELF_PROVIDED`, `VERIFIED` | UC-07, UC-02 |
| `DeliveryMethod` | `PICKUP`, `SHOP_DELIVERY`, `GHN` | UC-07, UC-31 |
| `CategoryStatus` | `PUBLISHED`, `HIDDEN` | UC-12 |
| `ProductKind` | `READY`, `CUSTOM_ONLY` | UC-09 |
| `ProductVisibility` | `DRAFT`, `PUBLISHED`, `HIDDEN` | UC-09 |
| `VisitMode` | `OFF`, `ALWAYS_ON`, `SCHEDULED` | UC-25 |
| `LotState` | `IN_PROGRESS`, `COMPLETED`, `SHIPPED` | UC-14 |
| `RfqSource` | `FROM_PRODUCT`, `DIRECT_SHOP`, `BROADCAST_CATEGORY` | UC-18 |
| `RfqStatus` | `DRAFT`, `SENT`, `RECEIVING_QUOTES`, `ACCEPTED`, `CANCELLED`, `EXPIRED` | UC-18 |
| `RfqExpiredReason` | `RESPONSE_TIMEOUT`, `SHOP_DECLINED`, `ALL_SHOPS_DECLINED`, `DEADLINE_PASSED`, `ALL_QUOTES_EXPIRED` | UC-18 |
| `InvitationStatus` | `PENDING`, `QUOTED`, `DECLINED` | UC-19 |
| `DeclineReason` | `CANNOT_MAKE_DESIGN`, `INSUFFICIENT_CAPACITY`, `TIGHT_DEADLINE`, `OTHER` | UC-19 |
| `UpdateRequestStatus` | `PENDING`, `FULFILLED`, `REJECTED`, `EXPIRED` | UC-19, UC-20 |
| `QuoteStatus` | `VALID`, `EXPIRED`, `CLOSED`, `ACCEPTED` | UC-19, UC-20 |
| `OrderKind` | `READY`, `CUSTOM` | UC-31 (MH), UC-20 (DR) |
| `ReadyOrderState` | `PENDING_PAYMENT`, `PENDING_CONFIRM`, `PREPARING`, `SHIPPING`, `DELIVERED`, `COMPLETED`, `REJECTED`, `CANCELLED` | Đơn MH (UC-32) |
| `CustomOrderState` | `PENDING_PRODUCTION`, `IN_PRODUCTION`, `SHIPPING`, `DELIVERED`, `COMPLETED`, `CANCELLED` | Đơn DR (UC-22) |
| `PaymentMethod` | `COD`, `ZALOPAY`, `VNPAY` | Đơn MH bắt buộc; Đơn DR để `NULL` |
| `PaymentState` | `UNPAID`, `DEPOSIT_PAID`, `PAID`, `REFUNDED` | Độc lập trạng thái đơn |
| `PaymentPurpose` | `FULL_ORDER`, `DEPOSIT` | `payment_attempts` |
| `PaymentAttemptStatus` | `INITIATED`, `PENDING`, `SUCCEEDED`, `FAILED`, `EXPIRED` | `payment_attempts` |
| `RefundState` | `PENDING`, `SUCCEEDED`, `FAILED` | UC-33 BR-11 |
| `CancelRequestState` | `PENDING_SHOP`, `SHOP_APPROVED`, `SHOP_REJECTED`, `WITHDRAWN`, `AUTO_CLOSED` | UC-33 |
| `DepositHandling` | `KEEP`, `REFUND` | UC-33 Alt-05 |
| `ArticleCategory` | `MARKET_MATERIAL`, `INFRASTRUCTURE_OPS`, `POLICY_REGULATION`, `VILLAGE_EVENT`, `GENERAL_NOTICE` | UC-27 BR-2 |
| `ArticleAudience` | `PUBLIC`, `INTERNAL` | UC-27, UC-28 |
| `ArticleState` | `DRAFT`, `PUBLISHED`, `HIDDEN` | UC-27 |
| `EventDispatchStatus` | `PENDING`, `DISPATCHED`, `FAILED` | Outbox UC-29 |
| `NotificationArea` | `BUYER`, `SELLER`, `ADMIN` | UC-29 |

---

## 3. Chi tiết Schema theo từng bảng

### 3.1 Nhóm `01-account` & `09-portal` (Tài khoản, Phiên, Phân quyền, Hồ sơ)

#### Bảng `accounts`

| Cột DB (`snake_case`) | JSON (`camelCase`) | Kiểu & Độ dài | Null / Default | Validation & Ràng buộc | Hiển thị API |
|---|---|---|---|---|---|
| `id` | `id` | `UUID` | NOT NULL (PK) | Khóa chính | Owner, Admin |
| `email` | `email` | `VARCHAR(100)` | NOT NULL | Định dạng email, trim, `UNIQUE(LOWER(email))`, không đổi sau khi tạo | Owner, Admin |
| `password_hash` | — | `VARCHAR(255)` | NULL | Mật khẩu gốc 8–64 ký tự có chữ + số (băm Argon2/Bcrypt); NULL nếu OAuth Google | `Internal` |
| `full_name` | `fullName` | `VARCHAR(100)` | NOT NULL | Không trống (1–100 ký tự) | Owner, Admin, Buyer/Seller (theo đơn) |
| `phone` | `phone` | `VARCHAR(10)` | NULL | Regex SĐT di động VN 10 chữ số (`^0[35789]\d{8}$`) | Owner, Admin |
| `account_type` | `accountType` | `AccountType` | NOT NULL, `'BUYER'` | Partial `UNIQUE` đảm bảo đúng 1 `SUPER_ADMIN` | Owner, Admin |
| `admin_status` | `adminStatus` | `AdminStatus` | NULL | Bắt buộc khi `ADMIN`/`SUPER_ADMIN`; mặc định `'ACTIVE'` | Admin |
| `failed_login_attempts` | — | `SMALLINT` | NOT NULL, `0` | Tăng khi sai MK; reset về 0 khi đúng MK | `Internal` |
| `locked_until` | — | `TIMESTAMPTZ` | NULL | Đặt `NOW() + 15m` khi `failed_login_attempts >= 5` | `Internal` |
| `created_at` | `createdAt` | `TIMESTAMPTZ` | NOT NULL, `NOW()` | Thời điểm tạo tài khoản | Owner, Admin |

#### Bảng `admin_grants`

| Cột DB | JSON | Kiểu & Độ dài | Null / Default | Validation & Ràng buộc | Hiển thị API |
|---|---|---|---|---|---|
| `id` | `id` | `UUID` | NOT NULL (PK) | Khóa chính | Admin |
| `account_id` | `accountId` | `UUID` | NOT NULL (FK → `accounts`) | `UNIQUE(account_id, permission_group)` | Admin |
| `permission_group` | `permissionGroup` | `PermissionGroup` | NOT NULL | Admin thường phải có ≥ 1 bản ghi | Owner (Admin), Super Admin |

#### Bảng `sessions`

| Cột DB | JSON | Kiểu & Độ dài | Null / Default | Validation & Ràng buộc | Hiển thị API |
|---|---|---|---|---|---|
| `id` | `id` | `UUID` | NOT NULL (PK) | Khóa chính | `Internal` |
| `account_id` | `accountId` | `UUID` | NOT NULL (FK → `accounts`) | Index `(account_id, revoked_at)` | `Internal` |
| `token_hash` | — | `VARCHAR(255)` | NOT NULL, UNIQUE | Hash token phiên lưu server-side | `Internal` |
| `last_active_at` | — | `TIMESTAMPTZ` | NOT NULL | Cập nhật trượt khi có request hợp lệ | `Internal` |
| `expires_at` | — | `TIMESTAMPTZ` | NOT NULL | Idle timeout (đề xuất 7 ngày Buyer/Artisan, 2 giờ Admin) | `Internal` |
| `revoked_at` | — | `TIMESTAMPTZ` | NULL | Đặt ngay khi logout, đổi MK (các phiên khác), khóa/reset Admin | `Internal` |

#### Bảng `applications` (Hồ sơ đăng ký bán hàng — UC-01, UC-02)

| Cột DB | JSON | Kiểu & Độ dài | Null / Default | Validation & Ràng buộc | Hiển thị API |
|---|---|---|---|---|---|
| `id` | `id` | `UUID` | NOT NULL (PK) | Khóa chính | Owner, Admin (`MODERATION_CATALOG`) |
| `account_id` | `accountId` | `UUID` | NOT NULL (FK → `accounts`) | `UNIQUE(account_id, revision)`; tối đa 1 hồ sơ active (`DRAFT`/`PENDING`/`NEEDS_REVISION`) | Owner, Admin |
| `revision` | `revision` | `INT` | NOT NULL, `1` | Tăng 1 mỗi lần nộp lại sau `NEEDS_REVISION` | Owner, Admin |
| `status` | `status` | `ApplicationStatus` | NOT NULL, `'DRAFT'` | Khóa sửa khi `PENDING` | Owner, Admin |
| `shop_name` | `shopName` | `VARCHAR(150)` | NULL (NOT NULL khi nộp) | 1–150 ký tự khi nộp | Owner, Admin |
| `proposed_address` | `proposedAddress` | `VARCHAR(200)` | NULL (NOT NULL khi nộp) | 1–200 ký tự khi nộp | Owner, Admin |
| `private_phone` | `privatePhone` | `VARCHAR(10)` | NULL (NOT NULL khi nộp) | SĐT VN 10 chữ số; chỉ Admin xem, tách khỏi SĐT công khai | Owner, Admin |
| `started_year` | `startedYear` | `SMALLINT` | NULL | 4 chữ số: `1900 <= started_year <= current_year` | Owner, Admin |
| `intro` | `intro` | `VARCHAR(500)` | NULL | Tối đa 500 ký tự | Owner, Admin |
| `decision_reason` | `decisionReason` | `VARCHAR(500)` | NULL | Bắt buộc 10–500 ký tự khi `NEEDS_REVISION` hoặc `REJECTED` | Owner, Admin |
| `reviewer_id` | `reviewerId` | `UUID` | NULL (FK → `accounts`) | Admin xử lý quyết định | Admin |
| `created_at` | `createdAt` | `TIMESTAMPTZ` | NOT NULL, `NOW()` | Thời điểm tạo bản ghi | Owner, Admin |
| `submitted_at` | `submittedAt` | `TIMESTAMPTZ` | NULL | Ghi khi nộp; index sắp hàng chờ duyệt tăng dần | Owner, Admin |
| `decided_at` | `decidedAt` | `TIMESTAMPTZ` | NULL | Thời điểm Admin ra quyết định | Owner, Admin |

---

### 3.2 Nhóm `02-showcase` & `05-village-map` (Gian hàng, Sản phẩm, Phân loại, Bản đồ)

#### Bảng `shops`

| Cột DB | JSON | Kiểu & Độ dài | Null / Default | Validation & Ràng buộc | Hiển thị API |
|---|---|---|---|---|---|
| `id` | `id` | `UUID` | NOT NULL (PK) | Khóa chính | Public |
| `owner_id` | `ownerId` | `UUID` | NOT NULL, UNIQUE (FK → `accounts`) | Mỗi tài khoản tối đa 1 gian hàng | Owner, Admin |
| `application_id` | `applicationId` | `UUID` | NOT NULL (FK → `applications`) | Hồ sơ gốc đã duyệt | Admin |
| `name` | `name` | `VARCHAR(150)` | NOT NULL | Đổi sau duyệt phải qua `shop_changes` | Public |
| `slug` | `slug` | `VARCHAR(180)` | NOT NULL, UNIQUE | URL dễ đọc (UC-08 BR-6), không dấu | Public |
| `address` | `address` | `VARCHAR(200)` | NOT NULL | Đổi sau duyệt phải qua `shop_changes` kèm tọa độ | Public |
| `started_year` | `startedYear` | `SMALLINT` | NULL | `1900..current_year` | Public |
| `story` | `story` | `VARCHAR(2000)` | NULL | Bắt buộc không trống khi mở gian hàng (UC-13) | Public |
| `public_phones` | `publicPhones` | `JSONB` (`string[]`) | NOT NULL, `'[]'` | 1–3 số khi mở gian hàng; mỗi số 10 chữ số VN, không trùng | Public |
| `status` | `status` | `ShopStatus` | NOT NULL, `'PREPARING'` | `PREPARING` → `ACTIVE` ↔ `PAUSED` | Public (trừ `PREPARING` chỉ Owner/Admin) |
| `accepts_requests` | `acceptsRequests` | `BOOLEAN` | NOT NULL, `true` | Nhận yêu cầu báo giá (UC-18) | Public |
| `visit_mode` | `visitMode` | `VisitMode` | NOT NULL, `'OFF'` | Mặc định `'OFF'` | Public |
| `visit_note` | `visitNote` | `VARCHAR(300)` | NULL | Chỉ nhập khi `visit_mode != 'OFF'`; chỉ hiện khi đang đón khách | Public |
| `latitude` | `latitude` | `DECIMAL(9,6)` | NULL | `[-90, 90]`; bắt buộc khi mở gian hàng (UC-13) | Public |
| `longitude` | `longitude` | `DECIMAL(9,6)` | NULL | `[-180, 180]`; bắt buộc khi mở gian hàng (UC-13) | Public |
| `first_opened_at` | `firstOpenedAt` | `TIMESTAMPTZ` | NULL | Ghi đúng 1 lần khi `PREPARING → ACTIVE` (dùng sort UC-06) | Public |
| `created_at` | `createdAt` | `TIMESTAMPTZ` | NOT NULL, `NOW()` | Thời điểm tạo gian hàng | Owner, Admin |

#### Bảng `shop_changes` (Yêu cầu đổi tên/địa chỉ + ghim đề nghị — UC-07, UC-02, UC-25)

| Cột DB | JSON | Kiểu & Độ dài | Null / Default | Validation & Ràng buộc | Hiển thị API |
|---|---|---|---|---|---|
| `id` | `id` | `UUID` | NOT NULL (PK) | Khóa chính | Owner, Admin |
| `shop_id` | `shopId` | `UUID` | NOT NULL (FK → `shops`) | Partial `UNIQUE(shop_id) WHERE status = 'PENDING'` | Owner, Admin |
| `proposed_name` | `proposedName` | `VARCHAR(150)` | NULL | Ít nhất 1 trong `proposed_name` hoặc `proposed_address` khác NULL | Owner, Admin |
| `proposed_address` | `proposedAddress` | `VARCHAR(200)` | NULL | Nếu khác NULL thì `proposed_latitude` & `proposed_longitude` bắt buộc NOT NULL | Owner, Admin |
| `proposed_latitude` | `proposedLatitude` | `DECIMAL(9,6)` | NULL | `[-90, 90]` | Owner, Admin |
| `proposed_longitude` | `proposedLongitude` | `DECIMAL(9,6)` | NULL | `[-180, 180]` | Owner, Admin |
| `status` | `status` | `ShopChangeStatus` | NOT NULL, `'PENDING'` | `PENDING` → `APPROVED` \| `REJECTED` | Owner, Admin |
| `reviewer_id` | `reviewerId` | `UUID` | NULL (FK → `accounts`) | Admin duyệt/từ chối | Admin |
| `reason` | `reason` | `VARCHAR(500)` | NULL | Bắt buộc 10–500 ký tự khi `REJECTED` | Owner, Admin |
| `created_at` | `createdAt` | `TIMESTAMPTZ` | NOT NULL, `NOW()` | Thời điểm gửi yêu cầu | Owner, Admin |
| `decided_at` | `decidedAt` | `TIMESTAMPTZ` | NULL | Thời điểm Admin xử lý | Owner, Admin |

#### Bảng `certificates` (Chứng nhận xưởng — UC-07, UC-02)

| Cột DB | JSON | Kiểu & Độ dài | Null / Default | Validation & Ràng buộc | Hiển thị API |
|---|---|---|---|---|---|
| `id` | `id` | `UUID` | NOT NULL (PK) | Tối đa 5 chứng nhận mỗi `shop_id` | Public |
| `shop_id` | `shopId` | `UUID` | NOT NULL (FK → `shops`) | Khóa ngoại gian hàng | Public |
| `title` | `title` | `VARCHAR(150)` | NOT NULL | 1–150 ký tự; mỗi chứng nhận kèm 1–3 ảnh ở `assets` | Public |
| `verification` | `verification` | `CertificateVerification` | NOT NULL, `'SELF_PROVIDED'` | Đổi `title` hoặc ảnh reset về `'SELF_PROVIDED'` | Public |
| `verified_by` | `verifiedBy` | `UUID` | NULL (FK → `accounts`) | Admin xác minh | Admin |
| `verified_at` | `verifiedAt` | `TIMESTAMPTZ` | NULL | Thời điểm xác minh | Public |
| `version` | `version` | `INT` | NOT NULL, `1` | Optimistic lock khi Admin bấm Xác minh (`ERR-UC02-02`) | Owner, Admin |
| `updated_at` | `updatedAt` | `TIMESTAMPTZ` | NOT NULL, `NOW()` | Thời điểm sửa gần nhất | Owner, Admin |

#### Bảng `delivery_options` (Phương thức giao hàng của xưởng — UC-07, UC-31)

| Cột DB | JSON | Kiểu & Độ dài | Null / Default | Validation & Ràng buộc | Hiển thị API |
|---|---|---|---|---|---|
| `id` | `id` | `UUID` | NOT NULL (PK) | Khóa chính | Public |
| `shop_id` | `shopId` | `UUID` | NOT NULL (FK → `shops`) | `UNIQUE(shop_id, method)` | Public |
| `method` | `method` | `DeliveryMethod` | NOT NULL | `PICKUP`, `SHOP_DELIVERY`, `GHN` | Public |
| `fee` | `fee` | `BIGINT` | NOT NULL, `0` | `PICKUP` = 0; `SHOP_DELIVERY` ∈ `[0, 1_000_000]` VND | Public |
| `enabled` | `enabled` | `BOOLEAN` | NOT NULL, `false` | Không tắt hết khi xưởng có SP `READY` đang `PUBLISHED` (`ERR-UC07-04`) | Public |

#### Bảng `categories` (Phân loại sản phẩm — UC-12)

| Cột DB | JSON | Kiểu & Độ dài | Null / Default | Validation & Ràng buộc | Hiển thị API |
|---|---|---|---|---|---|
| `id` | `id` | `UUID` | NOT NULL (PK) | Khóa chính | Public |
| `name` | `name` | `VARCHAR(100)` | NOT NULL | `UNIQUE(LOWER(name))`, 1–100 ký tự | Public |
| `status` | `status` | `CategoryStatus` | NOT NULL, `'PUBLISHED'` | Không chuyển `HIDDEN` khi còn SP `PUBLISHED` (`ERR-UC12-02`) | Public (chỉ `PUBLISHED`), Admin (tất cả) |
| `sort_order` | `sortOrder` | `INT` | NOT NULL, `0` | Thứ tự hiển thị | Public, Admin |

#### Bảng `products` (Sản phẩm — UC-09, UC-10, UC-11)

| Cột DB | JSON | Kiểu & Độ dài | Null / Default | Validation & Ràng buộc | Hiển thị API |
|---|---|---|---|---|---|
| `id` | `id` | `UUID` | NOT NULL (PK) | Khóa chính | Public |
| `shop_id` | `shopId` | `UUID` | NOT NULL (FK → `shops`) | Khóa ngoại xưởng | Public |
| `category_id` | `categoryId` | `UUID` | NULL (FK → `categories`) | Nullable khi `DRAFT`; bắt buộc khi `PUBLISHED` | Public |
| `name` | `name` | `VARCHAR(150)` | NOT NULL | 1–150 ký tự; đánh index unaccent/trigram cho tìm kiếm UC-10 | Public |
| `slug` | `slug` | `VARCHAR(180)` | NOT NULL, UNIQUE | URL dễ đọc (UC-11 BR-6) | Public |
| `description` | `description` | `VARCHAR(2000)` | NULL | Bắt buộc khi `PUBLISHED` | Public |
| `kind` | `kind` | `ProductKind` | NOT NULL | `READY` (có sẵn) hoặc `CUSTOM_ONLY` (chỉ đặt riêng) | Public |
| `price` | `price` | `BIGINT` | NULL | Bắt buộc `> 0` khi `kind = 'READY'` & `PUBLISHED`; NULL khi `CUSTOM_ONLY` | Public |
| `available_qty` | `availableQty` | `INT` | NOT NULL, `0` | `>= 0`; khóa `FOR UPDATE` theo thứ tự `id` khi checkout (UC-31) | Public |
| `visibility` | `visibility` | `ProductVisibility` | NOT NULL, `'DRAFT'` | `DRAFT → PUBLISHED ↔ HIDDEN` | Public (chỉ `PUBLISHED`), Owner |
| `ever_published` | `everPublished` | `BOOLEAN` | NOT NULL, `false` | `true` thì cấm đổi `READY → CUSTOM_ONLY` (`ERR-UC09-07`) và cấm xóa cứng | Owner |
| `weight_gram` | `weightGram` | `INT` | NULL | `> 0`; bắt buộc khi xưởng bật `GHN` (mức Should) | Owner, Checkout |
| `length_cm` | `lengthCm` | `INT` | NULL | `> 0`; bắt buộc khi bật `GHN` | Owner, Checkout |
| `width_cm` | `widthCm` | `INT` | NULL | `> 0`; bắt buộc khi bật `GHN` | Owner, Checkout |
| `height_cm` | `heightCm` | `INT` | NULL | `> 0`; bắt buộc khi bật `GHN` | Owner, Checkout |
| `created_at` | `createdAt` | `TIMESTAMPTZ` | NOT NULL, `NOW()` | Index `(visibility, created_at DESC)` cho UC-06 & UC-10 | Public |

#### Bảng `weekly_hours` & `closures` (Lịch đón khách — UC-25, UC-26)

**`weekly_hours`**

| Cột DB | JSON | Kiểu & Độ dài | Null / Default | Validation & Ràng buộc | Hiển thị API |
|---|---|---|---|---|---|
| `id` | `id` | `UUID` | NOT NULL (PK) | Khóa chính | Owner |
| `shop_id` | `shopId` | `UUID` | NOT NULL (FK → `shops`) | `UNIQUE(shop_id, weekday)` | Owner |
| `weekday` | `weekday` | `SMALLINT` | NOT NULL | `1` (Thứ 2) .. `7` (Chủ nhật) | Owner, Public (qua lịch 7 ngày) |
| `enabled` | `enabled` | `BOOLEAN` | NOT NULL, `false` | Khi `visit_mode = SCHEDULED`, phải có ≥ 1 ngày `enabled = true` | Owner |
| `opens` | `opens` | `TIME` | NOT NULL | `HH:mm` (giờ VN) | Owner |
| `closes` | `closes` | `TIME` | NOT NULL | `closes > opens` trong cùng ngày, không qua nửa đêm | Owner |

**`closures`**

| Cột DB | JSON | Kiểu & Độ dài | Null / Default | Validation & Ràng buộc | Hiển thị API |
|---|---|---|---|---|---|
| `id` | `id` | `UUID` | NOT NULL (PK) | Tối đa 20 mục còn hiệu lực mỗi `shop_id` | Owner |
| `shop_id` | `shopId` | `UUID` | NOT NULL (FK → `shops`) | Khóa ngoại xưởng | Owner |
| `start_date` | `startDate` | `DATE` | NOT NULL | Không ở quá khứ khi tạo mới (giờ VN) | Owner |
| `end_date` | `endDate` | `DATE` | NOT NULL | `start_date <= end_date <= today + 1 year` | Owner |
| `whole_day` | `wholeDay` | `BOOLEAN` | NOT NULL, `true` | `true`: nghỉ cả ngày; `false`: bắt buộc có `start_time < end_time` | Owner |
| `start_time` | `startTime` | `TIME` | NULL | Áp dụng cho từng ngày trong `[start_date, end_date]` | Owner |
| `end_time` | `endTime` | `TIME` | NULL | `end_time > start_time` (khoảng half-open `[start_time, end_time)`) | Owner |
| `private_reason` | `privateReason` | `VARCHAR(100)` | NULL | Tối đa 100 ký tự; **tuyệt đối không trả ở API công khai UC-26** | Owner |

---

### 3.3 Nhóm `03-traceability` (Lô sản xuất, Nhật ký, Đính chính, QR)

#### Bảng `lots`

| Cột DB | JSON | Kiểu & Độ dài | Null / Default | Validation & Ràng buộc | Hiển thị API |
|---|---|---|---|---|---|
| `id` | `id` | `UUID` | NOT NULL (PK) | Khóa chính | Owner, Buyer (UC-24) |
| `shop_id` | `shopId` | `UUID` | NOT NULL (FK → `shops`) | Khóa ngoại xưởng | Public (UC-17) |
| `product_id` | `productId` | `UUID` | NULL (FK → `products`) | CHECK: chính xác 1 trong `(product_id, order_id)` khác NULL | Public (nếu SP `PUBLISHED`), Owner |
| `order_id` | `orderId` | `UUID` | NULL (FK → `orders`) | Chỉ tạo khi đơn DR ở `IN_PRODUCTION`; **không trả ra UC-17** | Owner, Buyer (UC-24), Admin (UC-35) |
| `category_id` | `categoryId` | `UUID` | NOT NULL (FK → `categories`) | Lô SP copy từ `products.category_id`; lô DR chọn từ UC-12 | Public (UC-17) |
| `code` | `code` | `VARCHAR(20)` | NOT NULL, UNIQUE | Hệ thống sinh `LO-YYYY-xxxx` | Public (UC-17) |
| `qr_token` | `qrToken` | `VARCHAR(64)` | NOT NULL, UNIQUE | Token ngẫu nhiên cố định dùng cho `/trace/{qrToken}` | Public (UC-17), Owner |
| `name` | `name` | `VARCHAR(150)` | NOT NULL | 1–150 ký tự; không nhập PII Buyer | Public (UC-17) |
| `description` | `description` | `VARCHAR(1000)` | NULL | Tối đa 1000 ký tự | Public (UC-17) |
| `start_date` | `startDate` | `DATE` | NOT NULL | `<= today` (giờ VN) | Public (UC-17) |
| `quantity` | `quantity` | `INT` | NOT NULL | `> 0` | Public (UC-17) |
| `state` | `state` | `LotState` | NOT NULL, `'IN_PROGRESS'` | Hoàn thành yêu cầu ≥ 1 `lot_steps` (`ERR-UC14-03`) | Public (UC-17) |
| `ever_completed` | `everCompleted` | `BOOLEAN` | NOT NULL, `false` | `true` ngay lần đầu đạt `COMPLETED` | Public (UC-17) |
| `reopened_at` | `reopenedAt` | `TIMESTAMPTZ` | NULL | Ghi thời điểm mở lại từ `COMPLETED → IN_PROGRESS` | Public (UC-17) |
| `list_on_product` | `listOnProduct` | `BOOLEAN` | NOT NULL, `true` | Điều khiển hiển thị trên trang SP (UC-11) | Owner |
| `created_at` | `createdAt` | `TIMESTAMPTZ` | NOT NULL, `NOW()` | Thời điểm tạo lô | Owner |

#### Bảng `lot_steps` & `corrections`

**`lot_steps`**

| Cột DB | JSON | Kiểu & Độ dài | Null / Default | Validation & Ràng buộc | Hiển thị API |
|---|---|---|---|---|---|
| `id` | `id` | `UUID` | NOT NULL (PK) | Khóa chính | Public (UC-17) |
| `lot_id` | `lotId` | `UUID` | NOT NULL (FK → `lots`) | Mỗi mốc bắt buộc 1–10 ảnh ở `assets` | Public (UC-17) |
| `title` | `title` | `VARCHAR(100)` | NOT NULL | Tên công đoạn (1–100 ký tự) | Public (UC-17) |
| `body` | `body` | `VARCHAR(1000)` | NOT NULL | Mô tả mốc (1–1000 ký tự) | Public (UC-17) |
| `step_date` | `stepDate` | `DATE` | NOT NULL | `lots.start_date <= step_date <= today` | Public (UC-17) |
| `position` | `position` | `INT` | NOT NULL, `0` | Thứ tự phụ | Public (UC-17) |
| `is_edited` | `isEdited` | `BOOLEAN` | NOT NULL, `false` | `true` nếu thêm/sửa sau khi `lots.ever_completed = true` | Public (UC-17) |
| `created_at` | `createdAt` | `TIMESTAMPTZ` | NOT NULL, `NOW()` | Dùng sort tăng dần khi cùng `step_date` | Public (UC-17) |
| `updated_at` | `updatedAt` | `TIMESTAMPTZ` | NOT NULL, `NOW()` | Thời điểm sửa gần nhất | Public (UC-17) |

**`corrections`**

| Cột DB | JSON | Kiểu & Độ dài | Null / Default | Validation & Ràng buộc | Hiển thị API |
|---|---|---|---|---|---|
| `id` | `id` | `UUID` | NOT NULL (PK) | Bất biến (chỉ INSERT khi `lots.state = 'SHIPPED'`) | Public (UC-17) |
| `lot_id` | `lotId` | `UUID` | NOT NULL (FK → `lots`) | Khóa ngoại lô | Public (UC-17) |
| `step_id` | `stepId` | `UUID` | NULL (FK → `lot_steps`) | Tùy chọn (mặc định NULL — đính chính ở mức lô) | Public (UC-17) |
| `body` | `body` | `VARCHAR(1000)` | NOT NULL | 1–1000 ký tự | Public (UC-17) |
| `created_at` | `createdAt` | `TIMESTAMPTZ` | NOT NULL, `NOW()` | Sort tăng dần (mới nhất cuối) | Public (UC-17) |

---

### 3.4 Nhóm `04-quotation` (Yêu cầu báo giá, Lời mời, Báo giá)

#### Bảng `rfqs`

| Cột DB | JSON | Kiểu & Độ dài | Null / Default | Validation & Ràng buộc | Hiển thị API |
|---|---|---|---|---|---|
| `id` | `id` | `UUID` | NOT NULL (PK) | Khóa chính | Buyer, Invited Seller, Admin (UC-35, trừ `DRAFT`) |
| `buyer_id` | `buyerId` | `UUID` | NOT NULL (FK → `accounts`) | Chủ yêu cầu | Buyer, Admin |
| `source` | `source` | `RfqSource` | NOT NULL | Cố định sau khi gửi | Buyer, Invited Seller, Admin |
| `target_shop_id` | `targetShopId` | `UUID` | NULL (FK → `shops`) | Có khi `FROM_PRODUCT` hoặc `DIRECT_SHOP` | Buyer, Invited Seller, Admin |
| `sample_product_id` | `sampleProductId` | `UUID` | NULL (FK → `products`) | Có khi `FROM_PRODUCT` | Buyer, Invited Seller, Admin |
| `sample_product_snapshot` | `sampleProductSnapshot` | `JSONB` | NULL | `{ productId, name, thumbnailUrl }` chụp lúc gửi | Buyer, Invited Seller, Admin |
| `category_id` | `categoryId` | `UUID` | NULL (FK → `categories`) | Bắt buộc khi `BROADCAST_CATEGORY` | Buyer, Invited Seller, Admin |
| `code` | `code` | `VARCHAR(20)` | NOT NULL, UNIQUE | Hệ thống sinh `YC-YYYY-xxxx` | Buyer, Invited Seller, Admin |
| `specifications` | `specifications` | `VARCHAR(2000)` | NOT NULL | 1–2000 ký tự (khi gửi) | Buyer, Invited Seller, Admin |
| `quantity` | `quantity` | `INT` | NOT NULL | `1..1_000_000` | Buyer, Invited Seller, Admin |
| `unit` | `unit` | `VARCHAR(30)` | NOT NULL | 1–30 ký tự | Buyer, Invited Seller, Admin |
| `delivery_method` | `deliveryMethod` | `DeliveryMethod` | NOT NULL | `PICKUP` hoặc `SHOP_DELIVERY` (Giao tận nơi) | Buyer, Invited Seller, Admin |
| `recipient_snapshot` | `recipientSnapshot` | `JSONB` | NULL | Bắt buộc khi Giao tận nơi; **che SĐT & địa chỉ chi tiết với xưởng trước khi chấp nhận** | Buyer, Admin (đủ); Seller (che đến quận/huyện) |
| `desired_date` | `desiredDate` | `DATE` | NULL | `>= today` khi tạo/gửi | Buyer, Invited Seller, Admin |
| `first_sent_at` | `firstSentAt` | `TIMESTAMPTZ` | NULL | Ghi đúng 1 lần khi gửi; dùng thống kê UC-35 | Buyer, Invited Seller, Admin |
| `deadline` | `deadline` | `TIMESTAMPTZ` | NULL | 23:59 ngày chọn (ngày mai → +30 ngày; gửi riêng mặc định +3 ngày) | Buyer, Invited Seller, Admin |
| `status` | `status` | `RfqStatus` | NOT NULL, `'DRAFT'` | SM 6 trạng thái | Buyer, Invited Seller, Admin |
| `extension_count` | `extensionCount` | `SMALLINT` | NOT NULL, `0` | `0..1` (chỉ gửi riêng) | Buyer, Invited Seller, Admin |
| `expired_reason` | `expiredReason` | `RfqExpiredReason` | NULL | Ghi khi `status = 'EXPIRED'` | Buyer, Invited Seller, Admin |
| `cancel_reason` | `cancelReason` | `VARCHAR(300)` | NULL | Tối đa 300 ký tự (UC-23) | Buyer, Quoted Seller, Admin |
| `created_at` | `createdAt` | `TIMESTAMPTZ` | NOT NULL, `NOW()` | Thời điểm tạo | Buyer, Admin |

#### Bảng `rfq_events`, `invitations`, `quotes`

**`rfq_events`**: `id (PK)`, `rfq_id (FK)`, `actor_id (FK?)`, `action (VARCHAR 50)`, `from_state (RfqStatus?)`, `to_state (RfqStatus)`, `reason (VARCHAR 500?)`, `occurred_at (TIMESTAMPTZ)`.

**`invitations`**

| Cột DB | JSON | Kiểu & Độ dài | Null / Default | Validation & Ràng buộc | Hiển thị API |
|---|---|---|---|---|---|
| `id` | `id` | `UUID` | NOT NULL (PK) | Khóa chính | Buyer, That Seller, Admin |
| `rfq_id` | `rfqId` | `UUID` | NOT NULL (FK → `rfqs`) | `UNIQUE(rfq_id, shop_id)` | Buyer, That Seller, Admin |
| `shop_id` | `shopId` | `UUID` | NOT NULL (FK → `shops`) | Khóa ngoại xưởng nhận | Buyer, That Seller, Admin |
| `status` | `status` | `InvitationStatus` | NOT NULL, `'PENDING'` | `PENDING` \| `QUOTED` \| `DECLINED` | Buyer, That Seller, Admin |
| `decline_reason` | `declineReason` | `DeclineReason` | NULL | Bắt buộc khi `DECLINED` | Buyer, That Seller, Admin |
| `decline_note` | `declineNote` | `VARCHAR(300)` | NULL | Bắt buộc khi `decline_reason = 'OTHER'` | Buyer, That Seller, Admin |
| `reminded_at` | `remindedAt` | `TIMESTAMPTZ` | NULL | Đảm bảo chỉ nhắc xưởng 1 lần trước 24h | `Internal` |
| `update_request_status` | `updateRequestStatus` | `UpdateRequestStatus` | NULL | Trạng thái Yêu cầu cập nhật giá | Buyer, That Seller, Admin |
| `update_requested_at` | `updateRequestedAt` | `TIMESTAMPTZ` | NULL | Thời điểm Buyer yêu cầu cập nhật giá | Buyer, That Seller, Admin |
| `update_deadline` | `updateDeadline` | `TIMESTAMPTZ` | NULL | `update_requested_at + 3 days` | Buyer, That Seller, Admin |

**`quotes`**

| Cột DB | JSON | Kiểu & Độ dài | Null / Default | Validation & Ràng buộc | Hiển thị API |
|---|---|---|---|---|---|
| `id` | `id` | `UUID` | NOT NULL (PK) | Khóa chính | Buyer, Owner Seller, Admin |
| `invitation_id` | `invitationId` | `UUID` | NOT NULL (FK → `invitations`) | `UNIQUE(invitation_id, revision)` | Buyer, Owner Seller, Admin |
| `revision` | `revision` | `INT` | NOT NULL, `1` | Tăng 1 mỗi lần xưởng cập nhật | Buyer, Owner Seller, Admin |
| `unit_price` | `unitPrice` | `BIGINT` | NOT NULL | `1_000..1_000_000_000` VND | Buyer, Owner Seller, Admin |
| `shipping_fee` | `shippingFee` | `BIGINT` | NOT NULL, `0` | `0..50_000_000` VND (`0` nếu `PICKUP`) | Buyer, Owner Seller, Admin |
| `lead_time` | `leadTime` | `SMALLINT` | NOT NULL | `1..365` ngày | Buyer, Owner Seller, Admin |
| `deposit_percent` | `depositPercent` | `SMALLINT` | NOT NULL | CHECK `IN (0, 30, 50)` | Buyer, Owner Seller, Admin |
| `deposit_deadline_days` | `depositDeadlineDays` | `SMALLINT` | NULL | Do xưởng tự đặt trong `1..14` ngày (mặc định 3) khi `deposit_percent > 0`; NULL khi `0` | Buyer, Owner Seller, Admin |
| `valid_until` | `validUntil` | `TIMESTAMPTZ` | NOT NULL | 23:59 ngày chọn theo BR-5 | Buyer, Owner Seller, Admin |
| `status` | `status` | `QuoteStatus` | NOT NULL, `'VALID'` | Báo giá kín: xưởng khác không đọc được | Buyer, Owner Seller, Admin |
| `terms` | `terms` | `VARCHAR(500)` | NULL | Ghi chú chất liệu/quy cách | Buyer, Owner Seller, Admin |
| `reminded_at` | `remindedAt` | `TIMESTAMPTZ` | NULL | Chống nhắc trùng Buyer trước 24h | `Internal` |
| `created_at` | `createdAt` | `TIMESTAMPTZ` | NOT NULL, `NOW()` | Thời điểm gửi phiên bản | Buyer, Owner Seller, Admin |

---

### 3.5 Nhóm `08-order` (Giỏ hàng, Đơn hàng MH & DR, Thanh toán, Hoàn tiền, Hủy đơn)

#### Bảng `cart_items`

| Cột DB | JSON | Kiểu & Độ dài | Null / Default | Validation & Ràng buộc | Hiển thị API |
|---|---|---|---|---|---|
| `id` | `id` | `UUID` | NOT NULL (PK) | Khóa chính | Owner |
| `account_id` | `accountId` | `UUID` | NOT NULL (FK → `accounts`) | `UNIQUE(account_id, product_id)` | Owner |
| `product_id` | `productId` | `UUID` | NOT NULL (FK → `products`) | Giá & tồn luôn JOIN đọc mới nhất từ `products` | Owner |
| `quantity` | `quantity` | `INT` | NOT NULL | `>= 1` (và `<= products.available_qty` khi cập nhật/gộp) | Owner |

#### Bảng `orders`

| Cột DB | JSON | Kiểu & Độ dài | Null / Default | Validation & Ràng buộc | Hiển thị API |
|---|---|---|---|---|---|
| `id` | `id` | `UUID` | NOT NULL (PK) | Khóa chính | Buyer, Seller, Admin (UC-35) |
| `buyer_id` | `buyerId` | `UUID` | NOT NULL (FK → `accounts`) | Chủ đơn mua | Buyer, Seller, Admin |
| `shop_id` | `shopId` | `UUID` | NOT NULL (FK → `shops`) | Xưởng thực hiện | Buyer, Seller, Admin |
| `kind` | `kind` | `OrderKind` | NOT NULL | `READY` (MH) hoặc `CUSTOM` (DR) | Buyer, Seller, Admin |
| `code` | `code` | `VARCHAR(20)` | NOT NULL, UNIQUE | `MH-YYYY-xxxx` hoặc `DR-YYYY-xxxx` | Buyer, Seller, Admin |
| `rfq_id` | `rfqId` | `UUID` | NULL (FK → `rfqs`) | Bắt buộc & partial UNIQUE khi `kind = 'CUSTOM'` | Buyer, Seller, Admin |
| `quote_id` | `quoteId` | `UUID` | NULL (FK → `quotes`) | Bắt buộc khi `kind = 'CUSTOM'` | Buyer, Seller, Admin |
| `state` | `state` | `VARCHAR(30)` | NOT NULL | `ReadyOrderState` (MH) hoặc `CustomOrderState` (DR) | Buyer, Seller, Admin |
| `payment_method` | `paymentMethod` | `PaymentMethod` | NULL | Bắt buộc (`COD`/`ZALOPAY`/`VNPAY`) khi `READY`; NULL khi `CUSTOM` | Buyer, Seller, Admin |
| `payment_state` | `paymentState` | `PaymentState` | NOT NULL, `'UNPAID'` | Độc lập với `state` | Buyer, Seller, Admin |
| `delivery_method` | `deliveryMethod` | `DeliveryMethod` | NOT NULL | Quyết định bỏ qua `SHIPPING` khi `PICKUP` | Buyer, Seller, Admin |
| `recipient_snapshot` | `recipientSnapshot` | `JSONB` | NOT NULL | `{ fullName, phone, province, district, ward, addressDetail }` | Buyer, Seller, Admin |
| `delivery_snapshot` | `deliverySnapshot` | `JSONB` | NOT NULL | `{ method, fee, itemsTotal }` | Buyer, Seller, Admin |
| `note` | `note` | `VARCHAR(500)` | NULL | Ghi chú Buyer cho xưởng (UC-31) | Buyer, Seller, Admin |
| `shipping_note` | `shippingNote` | `VARCHAR(200)` | NULL | Đơn vị vận chuyển / ghi chú giao (UC-22) | Buyer, Seller, Admin |
| `amount` | `amount` | `BIGINT` | NOT NULL | Tổng thanh toán (VND nguyên `>= 0`) | Buyer, Seller, Admin |
| `deposit_due` | `depositDue` | `BIGINT` | NOT NULL, `0` | Số tiền cọc (đơn DR); `0` với đơn MH hoặc cọc 0% | Buyer, Seller, Admin |
| `payment_due_at` | `paymentDueAt` | `TIMESTAMPTZ` | NULL | `created_at + 15m` với đơn MH online | Buyer, Seller, Admin |
| `deposit_due_at` | `depositDueAt` | `TIMESTAMPTZ` | NULL | `created_at + deposit_deadline_days` với đơn DR cọc > 0% | Buyer, Seller, Admin |
| `confirm_due_at` | `confirmDueAt` | `TIMESTAMPTZ` | NULL | `+72h` kể từ lúc đơn MH vào `PENDING_CONFIRM` (job 72h) | Buyer, Seller, Admin |
| `balance_collected_at` | `balanceCollectedAt` | `TIMESTAMPTZ` | NULL | Thời điểm xưởng xác nhận thu COD / phần còn lại | Buyer, Seller, Admin |
| `idempotency_key` | — | `VARCHAR(100)` | NULL | Partial `UNIQUE(buyer_id, idempotency_key)` chống đặt lặp | `Internal` |
| `created_at` | `createdAt` | `TIMESTAMPTZ` | NOT NULL, `NOW()` | Index `(created_at)`, `(shop_id, state)` cho UC-32 & UC-35 | Buyer, Seller, Admin |
| `delivered_at` | `deliveredAt` | `TIMESTAMPTZ` | NULL | Mốc tính job tự hoàn tất sau 7 ngày | Buyer, Seller, Admin |

#### Bảng `order_lines`, `payment_attempts`, `refunds`, `cancel_requests`, `order_events`

- **`order_lines`**: `id (PK)`, `order_id (FK)`, `product_id (FK?)`, `product_snapshot (JSONB: { name, thumbnailUrl, kind })`, `specification (VARCHAR 2000?)`, `quantity (INT >= 1)`, `unit_price (BIGINT >= 0)`, `restocked_qty (INT DEFAULT 0, 0..quantity)`.
- **`payment_attempts`**: `id (PK)`, `order_id (FK)`, `provider (VARCHAR 20: ZALOPAY|VNPAY)`, `reference (VARCHAR 100 UNIQUE)`, `purpose (PaymentPurpose)`, `amount (BIGINT > 0)`, `status (PaymentAttemptStatus)`, `expires_at (TIMESTAMPTZ?)`, `created_at (TIMESTAMPTZ)`.
- **`refunds`**: `id (PK)`, `payment_id (FK → payment_attempts)`, `reason (VARCHAR 50)`, `idempotency_key (VARCHAR 120 UNIQUE theo payment_id+reason)`, `amount (BIGINT > 0)`, `provider_ref (VARCHAR 100?)`, `state (RefundState: PENDING|SUCCEEDED|FAILED)`, `retry_count (SMALLINT DEFAULT 0, max 5)`, `next_retry_at (TIMESTAMPTZ?)`, `last_error (VARCHAR 500?)`, `created_at`, `updated_at`.
- **`cancel_requests`**: `id (PK)`, `order_id (FK)`, `requester_id (FK → accounts)`, `reason (VARCHAR 500, 10–500 ký tự)`, `state (CancelRequestState)`, `deposit_handling (DepositHandling?)`, `decision_reason (VARCHAR 500?, 10–500 ký tự khi từ chối)`, `resolved_by (FK → accounts?)`, `created_at`, `resolved_at (TIMESTAMPTZ?)`. Partial `UNIQUE(order_id) WHERE state = 'PENDING_SHOP'`.
- **`order_events`**: `id (PK)`, `order_id (FK)`, `actor_id (FK → accounts?, NULL nếu hệ thống)`, `action (VARCHAR 50)`, `from_state (VARCHAR 30?)`, `to_state (VARCHAR 30)`, `reason (VARCHAR 500?)`, `occurred_at (TIMESTAMPTZ)`.

---

### 3.6 Nhóm `06-news`, `07-notification` & Hạ tầng `assets`

#### Bảng `articles` (Tin tức — UC-27, UC-28)

| Cột DB | JSON | Kiểu & Độ dài | Null / Default | Validation & Ràng buộc | Hiển thị API |
|---|---|---|---|---|---|
| `id` | `id` | `UUID` | NOT NULL (PK) | Khóa chính (dùng trong URL `{slug}-{id}`) | Public / Artisan / Admin |
| `author_id` | `authorId` | `UUID` | NOT NULL (FK → `accounts`) | Admin tạo bản ghi | Admin |
| `published_by` | `publishedBy` | `UUID` | NULL (FK → `accounts`) | Admin bấm Đăng lần đầu | Admin |
| `title` | `title` | `VARCHAR(150)` | NOT NULL | 1–150 ký tự | Theo `audience` & `state` |
| `category` | `category` | `ArticleCategory` | NULL | Nullable khi `DRAFT`; bắt buộc khi `PUBLISHED` | Theo `audience` & `state` |
| `rich_body` | `richBody` | `TEXT` | NULL | Tối đa 10.000 ký tự chữ thuần, sanitize allowlist HTML; bắt buộc khi `PUBLISHED` | Theo `audience` & `state` |
| `audience` | `audience` | `ArticleAudience` | NOT NULL, `'INTERNAL'` | Mặc định `'INTERNAL'` (UC-27 Data Fields) | Artisan, Admin |
| `state` | `state` | `ArticleState` | NOT NULL, `'DRAFT'` | `DRAFT → PUBLISHED ↔ HIDDEN` | Admin |
| `version` | `version` | `INT` | NOT NULL, `1` | Optimistic lock (`ERR-UC27-05`) | Admin |
| `first_published_at` | `firstPublishedAt` | `TIMESTAMPTZ` | NULL | Ghi đúng 1 lần khi đăng đầu, không đổi khi Ẩn/Đăng lại | Theo `audience` & `state` |
| `content_updated_at` | `contentUpdatedAt` | `TIMESTAMPTZ` | NULL | Chỉ cập nhật khi sửa nội dung sau khi đã đăng | Theo `audience` & `state` |
| `created_at` | `createdAt` | `TIMESTAMPTZ` | NOT NULL, `NOW()` | Thời điểm tạo | Admin |
| `updated_at` | `updatedAt` | `TIMESTAMPTZ` | NOT NULL, `NOW()` | Sort danh sách quản trị mới cập nhật trước | Admin |

#### Bảng `assets` (Ảnh & Tệp đính kèm dùng chung)

| Cột DB | JSON | Kiểu & Độ dài | Null / Default | Validation & Ràng buộc | Hiển thị API |
|---|---|---|---|---|---|
| `id` | `id` | `UUID` | NOT NULL (PK) | Khóa chính | Theo chủ thể |
| `object_key` | — | `VARCHAR(255)` | NOT NULL, UNIQUE | Key trên Object Storage (API map sang `url` hoặc `/assets/{id}/content`) | `Internal` |
| `thumbnail_key` | — | `VARCHAR(255)` | NULL | Key bản thu nhỏ do backend sinh (API map sang `thumbnailUrl`) | `Internal` |
| `original_filename` | `originalFilename` | `VARCHAR(255)` | NOT NULL | Tên tệp gốc hiển thị khi tải đính kèm (UC-27/28) | Theo chủ thể |
| `mime` | `mime` | `VARCHAR(100)` | NOT NULL | Ảnh: `image/jpeg`, `image/png`, `image/webp`; Tệp: PDF/DOC/DOCX/XLS/XLSX | Theo chủ thể |
| `size` | `size` | `INT` | NOT NULL | `1..10_485_760` bytes (≤ 10 MB) | Theo chủ thể |
| `owner_kind` | `ownerKind` | `VARCHAR(40)` | NOT NULL | `APPLICATION`, `SHOP`, `CERTIFICATE`, `PRODUCT`, `RFQ`, `LOT_STEP`, `ARTICLE` | Theo chủ thể |
| `owner_id` | `ownerId` | `UUID` | NULL | Nullable tạm cho upload xem trước tin chưa lưu nháp (job dọn orphan) | Theo chủ thể |
| `purpose` | `purpose` | `VARCHAR(40)` | NOT NULL | `PROOF`, `AVATAR`, `GALLERY`, `COVER`, `INLINE`, `ATTACHMENT`, `SAMPLE` | Theo chủ thể |
| `position` | `position` | `SMALLINT` | NOT NULL, `0` | Thứ tự hiển thị | Theo chủ thể |
| `created_at` | `createdAt` | `TIMESTAMPTZ` | NOT NULL, `NOW()` | Dùng job dọn tệp mồ côi `owner_id IS NULL` | `Internal` |

#### Bảng `events` & `notifications` (Outbox & Trung tâm thông báo — UC-29)

- **`events`**: `id (PK)`, `kind (VARCHAR 60)`, `subject_type (VARCHAR 40)`, `subject_id (UUID)`, `operation_key (VARCHAR 150 UNIQUE)`, `actor_id (FK?)`, `before_state (VARCHAR 40?)`, `after_state (VARCHAR 40?)`, `occurred_at (TIMESTAMPTZ)`, `recipients_snapshot (JSONB: array of { userId, area })`, `dispatch_status (EventDispatchStatus DEFAULT 'PENDING')`, `dispatched_at (TIMESTAMPTZ?)`, `retry_count (SMALLINT DEFAULT 0)`.
- **`notifications`**: `id (PK)`, `event_id (FK → events)`, `user_id (FK → accounts)`, `template (VARCHAR 30: NT-UCxx-yy)`, `area (NotificationArea)`, `target_type (VARCHAR 40: APPLICATION|SHOP|RFQ|READY_ORDER|CUSTOM_ORDER|ARTICLE)`, `target_id (UUID)`, `title (VARCHAR 150)`, `body (VARCHAR 500)`, `read_at (TIMESTAMPTZ?)`, `created_at (TIMESTAMPTZ)`.
  - **Ràng buộc & Index:** `UNIQUE(event_id, user_id)`, Index `(user_id, created_at DESC)` (phân trang cursor), Partial Index `(user_id) WHERE read_at IS NULL` (tối ưu polling `unread-count`).

---

## 4. DTO lọc dữ liệu nhạy cảm

Không trả trực tiếp Entity từ CSDL ra API. Ba endpoint dưới đây bắt buộc dùng DTO lọc riêng:

1. **`PublicTraceLotDTO` (`GET /trace/{qrToken}` — UC-17):** Chỉ trả `lotCode`, `name`, `description`, `startDate`, `quantity`, `state`, `reopenedAt`, `categoryName`, `isCustomOrder`, `product?` (ẩn khi `isCustomOrder = true` hoặc sản phẩm `HIDDEN`), `shop: { id, slug, name, avatarUrl, status }`, `steps[]`, `corrections[]`. Không trả `orderId`, `buyer`, `price`, `sampleProduct`.
2. **`RfqForSellerDTO` (`GET /seller/invitations/{id}` — UC-19/21):** Khi `rfq.status != 'ACCEPTED'` (hoặc xưởng không được chọn), `recipientSnapshot` chỉ trả `{ province, district }`; không trả `fullName`, `phone`, `ward`, `addressDetail`.
3. **`PublicMapPinPopupDTO` (`GET /map/pins/{shopId}` — UC-26):** Chỉ trả `visitingStatus`, `statusLine`, `weeklySchedule7Days[]`, `visitNote?` (khi đang đón khách); không trả `closures.privateReason` và `applications.privatePhone`.
