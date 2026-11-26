# Design System — vCraft (Phú Vinh × Arc UI)

> **Trạng thái:** Chính thức (2026-11-26) · **Tham chiếu nền tảng:** [Arc UI (`uiarc.dev`)](https://uiarc.dev/docs/introduction) + `shadcn/ui` · **Bản xem trước:** [mockup.html](mockup.html)
> **Liên quan:** [Kiến trúc tổng thể](architecture.md) · [Danh mục mã lỗi](error-codes.md) · [Đề bài — Tiêu chí Giao diện (20%)](../overview/problem-statement.md)

---

## 1. Triết lý thiết kế: Tối giản, Tiết chế & Tôn ảnh thủ công

Thiết kế bám sát tinh thần của **Arc UI (`uiarc.dev`)** và **shadcn/ui** (lưới sạch, tương phản rõ, viền `1px` chuẩn xác, chuyển động mượt vừa đủ), kết hợp bảng màu trung tính ấm của **mây tre Phú Vinh** theo tỷ lệ **90% trung tính đá/vải lanh + 10% điểm nhấn tre & mây**:

- **Ảnh thật làm trung tâm, giao diện lùi lại:** Không dùng nền loang màu, không họa tiết rối, không bo tròn quá đà hay icon màu mè. Toàn bộ hệ thống dùng tông đá ấm (`#F8F6F2`) và thẻ trắng sạch (`#FFFFFF`) để màu mây tre tự nhiên của ảnh chụp thật nổi bật.
- **Một hệ chữ hiện đại, chuẩn dấu tiếng Việt:** Dùng `Plus Jakarta Sans` cho tiêu đề + `Inter` cho nội dung/bảng biểu (không dùng serif cách điệu dễ gãy dấu tiếng Việt) + `JetBrains Mono` cho mã đơn và giá tiền thẳng cột.
- **Một bộ icon duy nhất:** 100% sử dụng **`Lucide Icons`** (`lucide-react`), nét đều `1.5px`, kích thước chuẩn `16px` / `18px` / `20px`. Tuyệt đối không dùng emoji trong giao diện sản phẩm.
- **Sở hữu toàn bộ mã nguồn qua `foundation.css`:** Mọi component đọc biến CSS semantic, hỗ trợ sẵn Light Mode (mặc định) và Dark Mode (tùy chọn).

---

## 2. Hệ thống Màu sắc (Semantic Color Tokens)

### 2.1 Nền & Bề mặt (Warm Stone Neutrals — 90%)

| Token CSS | Light Mode (Mặc định) | Dark Mode | Vai trò sử dụng |
|---|---|---|---|
| `--bg-canvas` | `#F8F6F2` | `#121110` | Nền trang tổng thể (đá ấm rất nhẹ, sạch mắt) |
| `--bg-surface` | `#FFFFFF` | `#1A1816` | Thẻ sản phẩm, thẻ xưởng, bảng dữ liệu, khung form |
| `--bg-subtle` | `#F2EFE9` | `#23201D` | Nền khối phụ, header bảng, thanh chọn tab, skeleton |
| `--bg-hover` | `#EAE5DC` | `#2B2824` | Trạng thái hover của dòng bảng, mục menu, nút phụ |

### 2.2 Hành động chính & Bản sắc Phú Vinh (10%)

| Token CSS | Light Mode | Dark Mode | Vai trò sử dụng |
|---|---|---|---|
| `--ink-primary` / `--cta-bg` | `#1C1917` *(Than Ấm)* | `#F5F2EC` | **Nút CTA chính** (Đặt hàng, Xác nhận, Chấp nhận báo giá) — sắc nét, không tranh màu với ảnh mây tre |
| `--cta-fg` | `#FFFFFF` | `#121110` | Chữ/icon trên nút CTA chính |
| `--brand-bamboo` | `#2D5A43` *(Xanh Tre Già)* | `#5E9E7D` | Logo, link nhấn, trạng thái *Đang hoạt động*, *Đang đón khách*, focus ring |
| `--brand-bamboo-soft` | `#EDF4F0` | `#182920` | Nền nhẹ cho dòng đang chọn, thông báo chưa đọc |
| `--brand-rattan` | `#9A5B25` *(Mây Hun)* | `#D68F50` | Nhãn *Đặt làm riêng*, huy hiệu *BQL đã xác minh*, điểm nhấn câu chuyện nghề |
| `--brand-rattan-soft` | `#F9F1E8` | `#2B2016` | Nền nhẹ cho ghi chú đính chính QR và badge thủ công |

### 2.3 Phân cấp chữ & Đường viền

| Token CSS | Light Mode | Dark Mode | Dùng cho |
|---|---|---|---|
| `--text-primary` | `#1C1917` | `#F5F2EC` | Tiêu đề, tên sản phẩm, giá, nội dung chính (tương phản ~16.5:1 AAA) |
| `--text-secondary` | `#57534E` | `#A8A29E` | Mô tả phụ, thông tin xưởng, tiêu đề cột (tương phản ~7.1:1 AAA) |
| `--text-muted` | `#6B645E` | `#878078` | Thời gian, placeholder, nhãn phụ (đạt chuẩn WCAG AA ≥ 5.2:1 trên cả `#F8F6F2` và `#F1EDE5`) |
| `--border` | `#E7E2D9` | `#2C2824` | Viền `1px` cho card, bảng, thanh điều hướng |
| `--border-strong` | `#D4CDC1` | `#3D3832` | Viền input mặc định và nút phụ |

### 2.4 Màu trạng thái nghiệp vụ (Semantic Status)

| Trạng thái | Text / Icon | Nền Badge | Viền | Áp dụng trong vCraft |
|---|---|---|---|---|
| **Success** | `#1F6442` | `#EDF6F0` | `#CBE6D5` | *Đang hoạt động*, *Đang đón khách*, *Đã thanh toán*, *Hoàn tất*, *Đã xuất xưởng* |
| **Warning** | `#9A580C` | `#FEF6E7` | `#F4D9A6` | *Chờ xác nhận (72h)*, *Chờ thanh toán (15p)*, *Chờ đặt cọc*, *Đang xử lý hoàn tiền* |
| **Danger** | `#B42318` | `#FEF1F0` | `#F6C8C4` | *Đã hủy*, *Bị từ chối*, *Hết hạn*, *Hoàn tiền FAILED*, *Đã khóa* |
| **Info** | `#1E4E8C` | `#EFF5FC` | `#C7DCF5` | *Đang sản xuất*, *Đang giao*, *Tin nội bộ* |
| **Neutral** | `#57534E` | `#F2EFE9` | `#E2DCD2` | *Nháp*, *Ẩn*, *Tạm ngừng*, *Không đón khách*, *Đã đóng* |

---

## 3. Typography & Iconography

| Vai trò | Font chữ | Weight & Tracking | Ứng dụng |
|---|---|---|---|
| **Heading / Display & Giá tiền** | `Plus Jakarta Sans` | `600`, `700` · `-0.025em` (`tabular-nums` cho giá) | Hero, tiêu đề trang, tên xưởng/SP và **giá tiền VND** (`285.000 ₫`, `42.500.000 ₫`) — sắc nét, thẩm mỹ thương mại mà vẫn thẳng cột trong bảng |
| **Body & UI** | `Inter` | `400`, `500`, `600` | Nội dung đọc, menu, nút bấm, form, bảng dữ liệu, thông báo |
| **Mã định danh & Tọa độ** | `JetBrains Mono` | `400`, `500` (`tabular-nums`) | Chỉ dùng cho mã kỹ thuật (`MH-2026-0042`, `DR-`, `YC-`, `LO-`), tọa độ bản đồ và đồng hồ đếm ngược 15p — **không dùng cho giá tiền** |
| **Icon hệ thống** | `Lucide Icons` | Stroke `1.5px` | Toàn bộ icon (tìm kiếm, giỏ hàng, chuông, bản đồ, QR, cảnh báo). Không dùng emoji |

### Thang cỡ chữ chuẩn (Ưu tiên dễ đọc & nổi bật Landing Page)

- `display`: `3.5rem` (56px, responsive `38px–58px`) / line-height `1.1` — Hero Trang chủ (UC-06)
- `h1`: `2rem` (32px) / `1.2` — Tiêu đề trang, Tên xưởng/Sản phẩm
- `h2`: `1.5rem` (24px) / `1.25` — Tiêu đề khối
- `h3`: `1.125rem` (18px) / `1.35` — Tên thẻ sản phẩm, tiêu đề dialog
- `body`: `1rem` (16px) / `1.65` — Nội dung đọc, mô tả, tin tức
- `sm`: `0.90625rem` (14.5px) / `1.5` — Bảng dữ liệu, nút bấm (`height: 44px`), menu dropdown
- `xs`: `0.8125rem` (13px) / `1.4` — Badge, thời gian, chú thích

---

## 4. Bo góc, Khoảng cách & Chuyển động (Arc UI Patterns)

- **Khoảng cách (Spacing):** Hệ lưới `4px` (`4, 8, 12, 16, 20, 24, 32, 48, 76px`). Container tối đa `1240px` (Storefront); cột đọc tin tức (UC-28) & trang QR (UC-17) tối đa `720px`.
- **Bo góc tiết chế:**
  - `--radius-xs: 6px` (Badge, Tag, Checkbox)
  - `--radius-sm: 10px` (Button, Input, Dropdown item)
  - `--radius-md: 14px` (Card sản phẩm, Dropdown menu, Popover thông báo, Bảng)
  - `--radius-lg: 20px` (Hero banner, Khối di sản nền trầm, Modal)
- **Dropdown & Notification Center chuẩn Arc UI (`@uiarc/dropdown-menu`):**
  - Mở mượt với hiệu ứng spring `scale(0.97) translateY(-6px) → scale(1) translateY(0)` và thanh nền trượt (**gliding hover highlight**) lướt theo từng mục khi di chuột.
  - Trung tâm thông báo (UC-29) rộng `420px`, có tab lọc `Tất cả | Chưa đọc`, avatar icon tròn theo loại sự kiện, chữ rõ (`14.5px` tiêu đề + `13.5px` nội dung), chấm chưa đọc gọn bên phải và nút `Xem tất cả thông báo` ở chân khung.
- **Đổ bóng siêu nhẹ:**
  - `--shadow-xs`: `0 1px 2px rgba(28, 25, 23, 0.04)`
  - `--shadow-sm`: `0 2px 8px -2px rgba(28, 25, 23, 0.06), 0 1px 2px rgba(28, 25, 23, 0.03)`
  - `--shadow-md`: `0 10px 28px -6px rgba(28, 25, 23, 0.12), 0 2px 6px rgba(28, 25, 23, 0.04)` (Popover, Modal)
- **Chuyển động (Calm Motion):** `160ms–220ms cubic-bezier(0.22, 1, 0.36, 1)` cho hover/popover/tab; tôn trọng `prefers-reduced-motion`.

---

## 5. Kiến trúc Component (`Arc UI` + `shadcn/ui` + Domain)

Không dùng các control mặc định thô của trình duyệt (`<input type="date">`, `<select>`). Toàn bộ UI dùng chung 1 bộ component chuẩn:

- **`components/arc/*` (`Arc UI` — cài qua `shadcn` CLI):**
  - **Overlay & Menu:** `@uiarc/dropdown-menu` (gliding hover highlight), `User Menu`, `@uiarc/toast-stack` (Top-Center Tuck & Fan-out).
  - **Chọn & Lịch:** `@uiarc/select`, `@uiarc/combobox`, `@uiarc/chip-group`, `@uiarc/calendar` (Date Picker tiếng Việt, không dùng native datepicker).
  - **Nhập liệu đặc thù:** `@uiarc/number-field` (stepper có giới hạn min/max cho giỏ hàng & tem QR), Money Input (VND), Phone Input (VN 10 số).
  - **Điều khiển & Chuyển tab:** `@uiarc/switch`, Checkbox, `@uiarc/radio-cards` (chọn COD/ZaloPay, phương thức giao), `@uiarc/tabs` (thanh trượt mượt).
- **`components/ui/*` (`shadcn/ui`):** Dialog, Table, Pagination, Skeleton, Dropzone upload ảnh/tệp.
- **`components/domain/*`:** `ProductCard`, `WorkshopCard`, `VisitingHoursWidget`, `TraceTimeline`, `QuoteCompareTable`, `NotificationBell`, `StatsOverview`.

### Quy ước hiển thị 4 kiểu Phản hồi & Lỗi (Đồng bộ [error-codes.md](error-codes.md) & Arc UI)

1. **`Inline`:** Chữ đỏ `--danger` (`13.5px`) ngay dưới ô input lỗi hoặc khối thử lại gọn tại vùng tải lỗi.
2. **`Top-Center Toast Stack` (chuẩn `@uiarc/toast-stack`):** Trượt êm từ trên xuống ngay chính giữa phía trên (`top: 80px`, rộng `448px`), hỗ trợ xếp lớp & xòe quạt khi di chuột (Tuck & Fan-out), morph `loading → success` (`350ms`), nút hành động ngắn (`Xem giỏ`, `Hoàn tác`, `Tải lại`), và thời lượng tự đóng phân tách rõ theo mức độ (tự dừng đếm khi di chuột vào):
   - **Thao tác thường / Thành công (`ok`):** **`1.8s`** — gọn nhanh, không cản mắt.
   - **Cảnh báo (`warn`):** **`3.5s`**.
   - **Mã lỗi nghiệp vụ / hệ thống (`err` — `ERR-UC*`, `ERR-SYS*`):** **`6.0s`** (để lâu hơn hẳn để người dùng kịp đọc rõ mã lỗi và bấm nút xử lý, kèm nút đóng chủ động).
3. **`Banner` (Alert neo ngữ cảnh):** Thanh cảnh báo đặt ngay đầu trang chi tiết đơn / checkout (không đẩy Header của Landing Page), **không tự biến mất** — dùng cho đếm ngược 15 phút thanh toán (`ERR-UC31-05`), hoàn tiền đang thử lại (`ERR-UC33-06`) hoặc hoàn tiền `FAILED` (`ERR-UC33-08`).
4. **`Trang lỗi`:** Empty state tối giản cho `403`/`404` kèm nút quay lại trang hợp lệ.

---

## 6. Ba Layout Shell của Ứng dụng

1. **Storefront Shell (Khu mua hàng):** Header 1 hàng gọn (Logo vCraft, 5 mục điều hướng chính, ô tìm kiếm, Giỏ hàng, Chuông thông báo, Tài khoản / Kênh người bán) + Footer.
2. **Seller Portal Shell (Kênh người bán):** Sidebar trái `240px` + Header trạng thái gian hàng (UC-13) + vùng nội dung quản lý sản phẩm, lô QR, báo giá, đơn hàng.
3. **Admin Portal Shell (Trang quản trị):** Sidebar trái động theo `admin_grants` (UC-34) + vùng giám sát giao dịch (UC-35), kiểm duyệt (UC-02), danh mục (UC-12), tin tức (UC-27).

---

## 7. Quy tắc Responsive (Mobile-First & Đa thiết bị)

### 7.1 Breakpoints chuẩn (Tailwind CSS)

| Breakpoint | Kích thước | Bố cục lưới & Padding container |
|---|---|---|
| **Mobile (`default`)** | `< 640px` | 1 cột (`padding: 16px`); lưới sản phẩm 2 cột nhỏ hoặc 1 cột lớn; Hero tối giản nút full-width |
| **Tablet dọc (`sm` / `md`)** | `640px – 1023px` | 2 cột (`padding: 20px`); Sidebar Portal thu thành Drawer trượt trái |
| **Desktop (`lg`)** | `1024px – 1279px` | 3 cột sản phẩm, 2 cột đối chiếu, Sidebar Portal cố định `256px` |
| **Wide (`xl`)** | `≥ 1280px` | Container tối đa `1240px` căn giữa (`padding: 24px`), lưới thống kê 4 cột |

### 7.2 Quy tắc chuyển đổi hành vi trên Mobile (`< 768px`)

1. **Điều hướng & Dropdown (Arc UI Mobile Adaptation):**
   - Header Storefront gom các mục menu vào nút Drawer; `User Menu` và `Select` dài chuyển sang **Bottom Sheet** trượt từ dưới lên để dễ bấm bằng ngón cái.
   - `Top-Center Toast Stack` giữ `width: calc(100vw - 24px)` ở `top: 72px`, không bị tràn mép màn hình.
2. **Kích thước vùng chạm (Touch Targets) & Form:**
   - Mọi nút bấm, mục dropdown, ô ngày trong Calendar và nút stepper `− / +` tối thiểu **`40px–44px`** chiều cao.
   - Mọi `<input>` trên mobile giữ **`font-size ≥ 16px`** để ngăn trình duyệt iOS tự động zoom khi focus.
3. **Trang QR Truy xuất nguồn gốc (UC-17 — Ưu tiên Mobile tuyệt đối):**
   - Thiết kế cột đơn `max-width: 720px`, ảnh mốc sản xuất hiển thị lớn toàn chiều ngang (`aspect-ratio: 4/3`), lazy-load từng mốc và hỗ trợ chạm mở Lightbox toàn màn hình.
4. **Bảng dữ liệu (UC-20, UC-32, UC-35):**
   - Bảng so sánh báo giá (UC-20) hỗ trợ cuộn ngang mượt kèm ghim cột tên xưởng bên trái (sticky first column) trên mobile; danh sách đơn hàng/hồ sơ chuyển sang dạng **Stacked Card** khi `< 768px`.
5. **Bản đồ làng nghề (UC-25, UC-26):**
   - Trên mobile, bản đồ chiếm `calc(100dvh - 68px)`, popup thông tin xưởng khi bấm pin mở dạng **Bottom Sheet** trượt lên thay vì popup nhỏ đè lên bản đồ.

---

## 8. Năm mẫu Page Layout chuẩn cho toàn bộ 35 UC

Cả 3 thành viên bám đúng 5 khung bố cục dưới đây để mọi màn hình trong dự án đồng nhất tuyệt đối:

| Mẫu Layout | Cấu trúc khung (Desktop → Mobile) | Áp dụng cho các UC |
|---|---|---|
| **L1 — Exhibition Landing** | `max-w-[1240px]` · Hero toàn khung → 4 khối tải độc lập (có Skeleton & Retry riêng) → Khối Di sản nền trầm → Footer | `UC-06` (Trang chủ) |
| **L2 — Catalog & Filter Grid** | `max-w-[1240px]` · Header trang + Thanh tìm kiếm/Chip lọc/Sort dropdown → Lưới 3 cột (`lg`) / 2 cột (`md`) / 1–2 cột (`sm`) → Load-more / Pagination | `UC-08` (DS Xưởng), `UC-10` (DS Sản phẩm), `UC-28` (DS Tin tức) |
| **L3 — Detail & Sticky Action (7/5)** | `max-w-[1240px]` · Cột trái `7/12` (Gallery / DS món / Timeline) + Cột phải `5/12` **Sticky Action Box** (Giá, tồn, nút Mua/RFQ/Thanh toán). Mobile xếp dọc + thanh hành động ghim đáy | `UC-08` (Chi tiết xưởng), `UC-11` (Chi tiết SP), `UC-30` (Giỏ), `UC-31` (Checkout), `UC-20` (So sánh BG), `UC-22`/`UC-32`/`UC-33` (Chi tiết đơn) |
| **L4 — Mobile-First Reading** | Cột đơn `max-w-[720px]` căn giữa · Ảnh lớn toàn chiều ngang, timeline / bài đọc sạch không sidebar | `UC-17` (Trang QR), `UC-24` (Nhật ký SX đơn DR), `UC-28` (Chi tiết tin) |
| **L5 — Portal Workspace & Map** | Sidebar trái `256px` (Mobile → Drawer) + Vùng làm việc `max-w-[1040px]` (KPI Cards / Filter / Table / Form 2 cột). Riêng Bản đồ dùng full-viewport `calc(100dvh - 68px)` + Floating Filter + Bottom Sheet | `UC-01..05`, `UC-07`, `UC-09`, `UC-12..16`, `UC-18..19`, `UC-21`, `UC-25..27`, `UC-29`, `UC-34..35` |

> **Lưu ý về phạm vi MVP1:** Toàn bộ màu sắc đã đạt chuẩn tương phản WCAG AA/AAA theo tỷ lệ **60% nền đá ấm · 30% than ấm & viền · 10% Tre & Mây**. Ở MVP1, team ưu tiên hoàn thiện 100% **Light Mode** trước; Dark Mode đã khai báo sẵn biến CSS để mở rộng sau mà không làm tăng gấp đôi công test.
