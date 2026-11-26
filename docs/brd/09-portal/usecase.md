---
title: "09 — Quản trị & Giám sát sàn"
group: 09-portal
type: brd
status: draft
version: 0.2.0
date: 2026-11-18
author: Khánh Chi
---

# 09 — Quản trị & Giám sát sàn

Change history: [CHANGELOG.md](CHANGELOG.md)

## I. Overview

Nhóm chức năng mở rộng dành cho Ban quản lý (BQL) làng nghề trên Trang quản trị (Admin Portal): quản lý tài khoản và phân quyền cho các cán bộ quản trị viên (`UC-34`), và theo dõi, thống kê toàn bộ giao dịch trên hệ thống (`UC-35`). Trưởng BQL tạo tài khoản quản lý cho từng cán bộ và gán một hoặc nhiều nhóm quyền nghiệp vụ (`Kiểm duyệt & Danh mục`, `Tin tức cộng đồng`, `Giám sát giao dịch`), hoặc khóa tài khoản khi cán bộ ngừng phụ trách mà vẫn giữ nguyên lịch sử thao tác cũ. Cán bộ có quyền Giám sát giao dịch xem được bức tranh tổng quan theo kỳ và tra cứu chi tiết mọi đơn mua hàng, đơn đặt riêng, yêu cầu báo giá, trạng thái thanh toán/hoàn tiền ở chế độ chỉ đọc mà không can thiệp vào giao dịch của Buyer và xưởng.

### Terms

| Term | Meaning |
|---|---|
| Trưởng BQL (Super Admin) | Tài khoản quản trị cao nhất được nạp sẵn khi khởi tạo hệ thống; có đủ mọi quyền trên Trang quản trị và là vai trò duy nhất được thực hiện UC-34. |
| Quản trị viên (Admin) | Tài khoản cán bộ quản lý do Trưởng BQL tạo ở UC-34; chỉ dùng Trang quản trị (không mua, không bán) và chỉ truy cập được các chức năng thuộc nhóm quyền được giao. |
| Nhóm quyền quản trị | Ba nhóm quyền nghiệp vụ cố định gán cho Admin: (1) Kiểm duyệt & Danh mục: duyệt hồ sơ bán hàng, yêu cầu đổi tên/địa chỉ xưởng, xác minh chứng nhận (UC-02) và quản lý phân loại (UC-12); (2) Tin tức cộng đồng: soạn, đăng, sửa, ẩn tin tức (UC-27); (3) Giám sát giao dịch: xem thống kê và tra cứu yêu cầu báo giá, báo giá, đơn đặt riêng, đơn mua hàng toàn hệ thống (UC-35). |
| Trạng thái tài khoản Admin | Đang hoạt động hoặc Đã khóa. Khi bị khóa, tài khoản bị kết thúc ngay mọi phiên đăng nhập, không đăng nhập lại được và ngừng nhận thông báo mới; lịch sử thao tác cũ giữ nguyên. |
| Kỳ thống kê | Khoảng thời gian lọc số liệu tổng quan ở UC-35: 7 ngày qua, 30 ngày qua, Tháng này, hoặc Từ ngày – Đến ngày (tính theo giờ Việt Nam). |
| Tổng giá trị đơn hoàn tất | Tổng thanh toán (tiền hàng cộng phí giao) của các đơn mua hàng (MH) và đơn đặt riêng (DR) tạo trong kỳ thống kê và đã đạt trạng thái Hoàn tất tại thời điểm xem (UC-35 BR-5). |
| Chế độ giám sát chỉ đọc | Admin có quyền Giám sát giao dịch xem được danh sách, chi tiết, lịch sử trạng thái và trạng thái thanh toán/hoàn tiền của mọi giao dịch trên sàn, nhưng không có thao tác tạo, sửa, báo giá, xác nhận hay hủy. |

## II. Scope

| Business topic | Channel | Use case |
|---|---|---|
| Tạo, sửa thông tin, phân nhóm quyền, đặt lại mật khẩu và khóa/mở khóa tài khoản quản trị viên | Trang quản trị | UC-34 |
| Xem thống kê tổng quan theo kỳ; lọc, tìm kiếm và xem chi tiết mọi đơn mua hàng, đơn đặt riêng, yêu cầu báo giá ở chế độ chỉ đọc | Trang quản trị | UC-35 |

**Ngoài phạm vi:** Tạo hoặc sửa định nghĩa nhóm quyền ngoài ba nhóm cố định; xóa cứng tài khoản Admin; nâng cấp tài khoản Buyer hoặc Artisan thành Admin (tài khoản Admin tách biệt, không mua, không bán); Trưởng BQL tự khóa hoặc tự hạ quyền của chính mình; Admin tạo, sửa, chia đơn, báo giá, xác nhận giao hàng, xác nhận thu tiền hoặc hủy yêu cầu/đơn hàng; đối soát tiền và giải ngân cho xưởng (EN-15); xử lý đổi trả, tranh chấp (EN-17); xuất báo cáo Excel/PDF; thống kê lượt xem trang/bản đồ (EN-11); Dashboard thống kê riêng cho Kênh người bán của Artisan; tìm kiếm/lọc giao dịch ở UC-35 theo tên hoặc email Buyer (MVP1 chỉ tìm theo mã MH/DR/YC).

**Liên quan**

| ID | Tài liệu | Quan hệ |
|---|---|---|
| BRD-01 | [01-account](../01-account/usecase.md) | UC-02 chịu kiểm tra quyền Kiểm duyệt & Danh mục (UC-34 BR-4); UC-03 dùng chung quy tắc duy nhất email trên toàn hệ thống; UC-04 kiểm tra đăng nhập, từ chối tài khoản Đã khóa bằng `ERR-UC04-07` (Alt-10) và đưa Admin vào mục đầu tiên có quyền ở Trang quản trị (BR-9); UC-05 Admin tự sửa họ tên và đổi mật khẩu của chính mình. |
| BRD-02 | [02-showcase](../02-showcase/usecase.md) | UC-12 (Quản lý phân loại) thuộc nhóm quyền Kiểm duyệt & Danh mục (UC-34 BR-4). |
| BRD-03 | [03-traceability](../03-traceability/usecase.md) | UC-14 cung cấp danh sách lô sản xuất/QR của đơn đặt riêng để UC-35 hiển thị ở chi tiết đơn DR (UC-35 BR-9), chỉ đọc. |
| BRD-04 | [04-quotation](../04-quotation/usecase.md) | Cung cấp dữ liệu Yêu cầu báo giá (YC), Báo giá và Đơn đặt riêng (DR) cho UC-35 theo dõi ở Trang quản trị; giữ nguyên tắc Admin không tạo yêu cầu, không báo giá và không can thiệp đơn. |
| BRD-06 | [06-news](../06-news/usecase.md) | UC-27 (Quản lý tin tức) thuộc nhóm quyền Tin tức cộng đồng (UC-34 BR-4). |
| BRD-07 | [07-notification](../07-notification/usecase.md) | Tài khoản Admin Đã khóa ngừng nhận thông báo mới; theo G-01, NT-UC01-01, NT-UC01-02, NT-UC07-01 chỉ gửi Trưởng BQL và Admin có quyền Kiểm duyệt & Danh mục. |
| BRD-08 | [08-order](../08-order/usecase.md) | Cung cấp dữ liệu Đơn mua hàng (MH), trạng thái thanh toán và trạng thái hoàn tiền (kể cả hoàn tiền cho đơn đặt riêng ở UC-33) cho UC-35 theo dõi ở Trang quản trị; giữ nguyên tắc Admin không mua, không bán và không hủy hay can thiệp đơn. |

## III. Use Cases

| Use case ID | Use case | Description |
|---|---|---|
| UC-34 | Quản lý tài khoản và phân quyền quản trị viên | Trưởng BQL tạo tài khoản Admin, gán hoặc sửa nhóm quyền quản trị, đặt lại mật khẩu, khóa và mở khóa tài khoản Admin |
| UC-35 | Theo dõi và thống kê giao dịch, đơn hàng toàn hệ thống | Trưởng BQL hoặc Admin có quyền Giám sát giao dịch xem tổng quan số liệu theo kỳ và tra cứu chi tiết đơn mua, đơn đặt riêng, yêu cầu báo giá ở chế độ chỉ đọc |

### UC-34 — Quản lý tài khoản và phân quyền quản trị viên

#### Detail

| Field | Content |
|---|---|
| Use case ID | UC-34 |
| Description | Trưởng BQL (Super Admin) xem danh sách tài khoản quản trị, tạo tài khoản Admin mới và gán một hoặc nhiều nhóm quyền (Kiểm duyệt & Danh mục, Tin tức cộng đồng, Giám sát giao dịch), cập nhật thông tin và nhóm quyền, đặt lại mật khẩu, khóa hoặc mở khóa tài khoản Admin. |
| Actors | Trưởng BQL (Super Admin) |
| Channel | Trang quản trị |
| Preconditions | • Người dùng đã đăng nhập bằng tài khoản Trưởng BQL đang hoạt động |
| Expected | **Thành công:**<br>• Tài khoản Admin được tạo ở trạng thái Đang hoạt động với đúng các nhóm quyền đã chọn, hoặc được cập nhật quyền, mật khẩu, trạng thái khóa/mở khóa<br>• Quyền mới có hiệu lực ngay từ thao tác kế tiếp; khi khóa hoặc đặt lại mật khẩu thì mọi phiên đăng nhập của tài khoản đó kết thúc ngay<br>**Thất bại:**<br>• Hiển thị lỗi, không tạo hoặc không thay đổi tài khoản quản trị |
| Screens | • Danh sách tài khoản quản trị (bước 1)<br>• Form tạo / chỉnh sửa tài khoản và nhóm quyền (bước 2-4, Alt-03)<br>• Hộp xác nhận khóa / mở khóa và đặt lại mật khẩu (Alt-04, Alt-05, Alt-06) |
| Notifications | Không có |

#### Main Flow

Trigger: Trưởng BQL chọn mục Quản trị viên trong Trang quản trị.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Trưởng BQL | Trưởng BQL mở mục Quản trị viên. | Danh sách tài khoản quản trị (gồm Trưởng BQL và các Admin) hiển thị, mới tạo trước: họ tên, email, vai trò/nhóm quyền, trạng thái (Đang hoạt động, Đã khóa), ngày tạo; có bộ lọc theo trạng thái và nhóm quyền. |
| 2 | Trưởng BQL | Trưởng BQL chọn Thêm quản trị viên. | Form tạo tài khoản Admin hiển thị. |
| 3 | Trưởng BQL | Trưởng BQL nhập họ tên, email, số điện thoại (nếu có), mật khẩu khởi tạo và tích chọn ít nhất một nhóm quyền (Kiểm duyệt & Danh mục, Tin tức cộng đồng, Giám sát giao dịch). | Thông tin và nhóm quyền sẵn sàng để gửi. |
| 4 | Trưởng BQL | Trưởng BQL chọn Tạo tài khoản. | Thông tin được gửi lên kiểm tra. |
| 5 | Backend | Backend kiểm tra quyền Trưởng BQL, kiểm tra dữ liệu hợp lệ, kiểm tra email chưa tồn tại trên toàn hệ thống, tạo tài khoản vai trò Admin với trạng thái Đang hoạt động và gán các nhóm quyền đã chọn. | Tài khoản Admin mới được lưu, chỉ truy cập được Trang quản trị theo đúng nhóm quyền được giao. |
| 6 | Trang quản trị | Trang quản trị đóng form và cập nhật danh sách. | Tài khoản Admin mới xuất hiện ở đầu danh sách kèm thông báo "Đã tạo tài khoản quản trị viên". |

Outcome: Tài khoản Admin mới ở trạng thái Đang hoạt động, đăng nhập được vào Trang quản trị theo đúng nhóm quyền được gán.

#### Alternative Flows

##### Alt-01 — ERR-UC34-01 — Dữ liệu không hợp lệ hoặc chưa chọn nhóm quyền

- At step: 4 (hoặc Alt-03, Alt-06) · Trigger: Họ tên trống hoặc quá 100 ký tự, email sai định dạng, số điện thoại sai định dạng 10 chữ số, mật khẩu không đạt 8–64 ký tự gồm cả chữ và số, hoặc chưa chọn nhóm quyền nào.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Backend | Backend từ chối lưu và đánh dấu trường sai. | Hiển thị ERR-UC34-01 tại từng trường sai; giữ nguyên dữ liệu đã nhập (trừ mật khẩu). |

Resume: Quay lại bước 3 (hoặc bước nhập lại ở Alt-03, Alt-06).

##### Alt-02 — ERR-UC34-02 — Email đã được sử dụng

- At step: 5 · Trigger: Email trùng với bất kỳ tài khoản nào đã có trên hệ thống (Buyer, Artisan, Admin hoặc Trưởng BQL), không phân biệt chữ hoa chữ thường.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Backend | Backend từ chối tạo tài khoản. | Hiển thị ERR-UC34-02 tại trường email; dữ liệu đã nhập được giữ trừ mật khẩu. |

Resume: Quay lại bước 3 để nhập email khác.

##### Alt-03 — Cập nhật thông tin và nhóm quyền của quản trị viên

- At step: 1 · Trigger: Trưởng BQL chọn Sửa ở một tài khoản Admin thường.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Trang quản trị | Trang quản trị mở form chỉnh sửa với họ tên, email (chỉ đọc), số điện thoại và các nhóm quyền hiện tại. | Trưởng BQL sẵn sàng cập nhật. |
| 2 | Trưởng BQL | Trưởng BQL sửa họ tên, số điện thoại và tích chọn hoặc bỏ chọn nhóm quyền (giữ ít nhất một nhóm quyền), rồi chọn Lưu. | Yêu cầu cập nhật được gửi. |
| 3 | Backend | Backend kiểm tra quyền Trưởng BQL, kiểm tra dữ liệu và lưu nhóm quyền mới. | Quyền mới có hiệu lực ngay từ thao tác kế tiếp của Admin đó mà không cần đăng nhập lại. |
| 4 | Trang quản trị | Trang quản trị cập nhật danh sách. | Hiển thị "Đã cập nhật quản trị viên". |

Resume: Kết thúc. Nếu dữ liệu sai hoặc bỏ hết nhóm quyền thì xử lý theo Alt-01.

##### Alt-04 — Khóa tài khoản quản trị viên

- At step: 1 · Trigger: Trưởng BQL chọn Khóa ở một tài khoản Admin đang ở trạng thái Đang hoạt động.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Trang quản trị | Trang quản trị hiện hộp xác nhận nêu rõ tài khoản sẽ bị đăng xuất ngay và không đăng nhập lại được cho đến khi mở khóa. | Trưởng BQL xác nhận khóa. |
| 2 | Backend | Backend chuyển tài khoản sang trạng thái Đã khóa, kết thúc ngay mọi phiên đăng nhập đang hoạt động của tài khoản đó và ngừng gửi thông báo mới cho tài khoản đó. | Mọi lịch sử thao tác cũ (hồ sơ đã duyệt, tin đã đăng) vẫn giữ nguyên tên người thực hiện. Nếu người đó cố đăng nhập lại, hệ thống từ chối và báo tài khoản đã bị khóa (`ERR-UC04-07`, UC-04 Alt-10, `01-account`). |
| 3 | Trang quản trị | Trang quản trị cập nhật danh sách. | Tài khoản hiện trạng thái Đã khóa. |

Resume: Kết thúc.

##### Alt-05 — Mở khóa tài khoản quản trị viên

- At step: 1 · Trigger: Trưởng BQL chọn Mở khóa ở một tài khoản Admin đang ở trạng thái Đã khóa.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Trang quản trị | Trang quản trị hiện hộp xác nhận mở khóa. | Trưởng BQL xác nhận. |
| 2 | Backend | Backend chuyển tài khoản về trạng thái Đang hoạt động với các nhóm quyền đang gán. | Admin đăng nhập lại được vào Trang quản trị. |
| 3 | Trang quản trị | Trang quản trị cập nhật danh sách. | Tài khoản hiện trạng thái Đang hoạt động. |

Resume: Kết thúc.

##### Alt-06 — Đặt lại mật khẩu khởi tạo cho quản trị viên

- At step: 1 · Trigger: Trưởng BQL chọn Đặt lại mật khẩu ở một tài khoản Admin (khi Admin quên mật khẩu hoặc cần cấp lại).

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Trang quản trị | Trang quản trị hiện hộp nhập mật khẩu mới (có nút xem/ẩn). | Trưởng BQL nhập mật khẩu mới đạt quy tắc và xác nhận. |
| 2 | Backend | Backend kiểm tra mật khẩu mới hợp lệ, lưu mật khẩu mới và kết thúc ngay mọi phiên đăng nhập hiện tại của tài khoản Admin đó. | Admin đăng nhập bằng mật khẩu mới; các phiên cũ bị buộc đăng nhập lại. |
| 3 | Trang quản trị | Trang quản trị đóng hộp thoại. | Hiển thị "Đã đặt lại mật khẩu". |

Resume: Kết thúc. Nếu mật khẩu không đạt quy tắc thì xử lý theo Alt-01.

##### Alt-07 — ERR-UC34-03 — Không thể tự khóa hoặc sửa quyền của Trưởng BQL

- At step: Alt-03 hoặc Alt-04 · Trigger: Trưởng BQL gửi thao tác khóa hoặc sửa nhóm quyền trên chính tài khoản Trưởng BQL (kể cả gọi API trực tiếp).

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Backend | Backend từ chối thao tác. | Hiển thị ERR-UC34-03; tài khoản Trưởng BQL giữ nguyên trạng thái và toàn quyền. |

Resume: Kết thúc.

##### Alt-08 — ERR-UC34-04 — Không có quyền thực hiện

- At step: 1 (hoặc bất kỳ thao tác nào của UC-34) · Trigger: Admin thường (không phải Trưởng BQL) mở mục Quản trị viên hoặc gọi thao tác của UC-34, kể cả bằng đường dẫn trực tiếp.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Backend | Backend từ chối yêu cầu. | Hiển thị ERR-UC34-04; dữ liệu không đổi. |

Resume: Kết thúc. Admin mở mục thuộc nhóm quyền không được gán nhận đúng mã lỗi của use case đích, không dùng chung `ERR-UC34-04`: UC-02 → `ERR-UC04-06` (`01-account`); UC-12 → `ERR-UC12-03` (`02-showcase`); UC-27 → `ERR-UC27-06` (`06-news`); UC-35 → `ERR-UC35-04`. Tài khoản Guest, Buyer, Artisan mở Trang quản trị xử lý theo UC-04 (ERR-UC04-06).

##### Alt-09 — ERR-UC34-05 — Không tải được danh sách hoặc không có kết quả lọc

- At step: 1 · Trigger: API tải danh sách tài khoản quản trị gặp lỗi, hoặc bộ lọc trạng thái/nhóm quyền không khớp tài khoản nào.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Trang quản trị | Khi API lỗi: hiển thị lỗi kèm nút Thử lại (không hiện danh sách cũ sai lệch). Khi lọc không khớp: hiển thị "Không tìm thấy tài khoản phù hợp" kèm nút Xóa bộ lọc. | Hiển thị ERR-UC34-05 ở trường hợp lỗi API; không có màn hình trống gây hiểu nhầm ở trường hợp lọc rỗng. |

Resume: Chọn Thử lại hoặc Xóa bộ lọc để xem lại.

#### Data Fields

| Field | Format | Validation | Required | Ghi chú |
|---|---|---|---|---|
| Bộ lọc danh sách | Chọn một | Trạng thái (Tất cả, Đang hoạt động, Đã khóa); Nhóm quyền (Tất cả hoặc một trong 3 nhóm) | Không | Lọc không tải lại trang |
| Họ tên | Text; length 100 | Không để trống | Có | Ví dụ: "Nguyễn Văn Minh" |
| Email | Text; length 100 | Đúng định dạng email; duy nhất toàn hệ thống, không phân biệt hoa thường, bỏ khoảng trắng đầu cuối | Có (chỉ đọc khi sửa) | Không đổi được sau khi tạo |
| Số điện thoại | Text; length 10 | Số di động Việt Nam, 10 chữ số | Không | Để trống hoặc đúng 10 chữ số |
| Mật khẩu khởi tạo | Text; length 8–64 | Tối thiểu 8 ký tự, có cả chữ và số | Có khi tạo mới và đặt lại mật khẩu | Không hiển thị lại, không ghi nhật ký; có nút xem/ẩn |
| Nhóm quyền | Chọn nhiều trong 3 nhóm | Bắt buộc chọn ít nhất 1 nhóm (Kiểm duyệt & Danh mục, Tin tức cộng đồng, Giám sát giao dịch) | Có với Admin thường | Trưởng BQL mặc định đủ mọi quyền |
| Vai trò quản trị | Chỉ xem | Trưởng BQL hoặc Quản trị viên | — | |
| Trạng thái tài khoản | Chỉ xem | Đang hoạt động hoặc Đã khóa | — | Đổi qua Khóa / Mở khóa |
| Ngày tạo | Ngày giờ, chỉ xem | dd/MM/yyyy HH:mm (giờ Việt Nam) | — | |

#### Error Messages

| Code | Condition | Message | Display | Flow |
|---|---|---|---|---|
| ERR-UC34-01 | Dữ liệu không hợp lệ hoặc chưa chọn nhóm quyền | "Thông tin chưa hợp lệ hoặc chưa chọn nhóm quyền. Vui lòng kiểm tra lại các trường được đánh dấu." | Inline | Alt-01 |
| ERR-UC34-02 | Email đã tồn tại trên hệ thống | "Email này đã gắn với một tài khoản khác trên hệ thống." | Inline | Alt-02 |
| ERR-UC34-03 | Tự khóa hoặc đổi quyền tài khoản Trưởng BQL | "Không thể tự khóa hoặc thay đổi quyền của tài khoản Trưởng BQL." | Toast | Alt-07 |
| ERR-UC34-04 | Không đủ quyền quản trị cho thao tác này | "Bạn không có quyền thực hiện chức năng quản trị này." | Trang lỗi | Alt-08 |
| ERR-UC34-05 | Lỗi tải danh sách tài khoản quản trị | "Chưa tải được danh sách tài khoản quản trị. Vui lòng thử lại." | Inline | Alt-09 |

#### Business Rules

| ID | Rule | Source |
|---|---|---|
| BR-1 | Duy nhất tài khoản Trưởng BQL (Super Admin) được nạp sẵn khi khởi tạo hệ thống và có quyền thực hiện UC-34; không có đăng ký Admin công khai và không nâng cấp tài khoản Buyer hoặc Artisan thành Admin. | Chốt phạm vi `09-portal`; mở rộng EN-19 |
| BR-2 | Tài khoản Admin do Trưởng BQL tạo là tài khoản tách biệt cho Trang quản trị: không mua hàng, không gửi yêu cầu báo giá, không bán hàng, không có giỏ hàng. | UC-04 BR-6 (`01-account`) |
| BR-3 | Mỗi Admin thường phải được gán ít nhất một trong ba nhóm quyền cố định (Kiểm duyệt & Danh mục, Tin tức cộng đồng, Giám sát giao dịch); Trưởng BQL mặc định có toàn bộ quyền quản trị. Không tạo hay sửa định nghĩa nhóm quyền ngoài ba nhóm này. | Chốt phạm vi `09-portal` |
| BR-4 | Thay đổi nhóm quyền có hiệu lực ngay từ thao tác kế tiếp mà không cần Admin đăng nhập lại. Mọi màn hình và API thuộc UC-02, UC-12, UC-27, UC-34, UC-35 đều kiểm tra đúng vai trò và nhóm quyền ở Backend, không chỉ ẩn mục trên menu (kể cả khi mở URL trực tiếp). Mỗi use case trả đúng mã lỗi không-có-quyền của chính nó (UC-02 → `ERR-UC04-06`, UC-12 → `ERR-UC12-03`, UC-27 → `ERR-UC27-06`, UC-34 → `ERR-UC34-04`, UC-35 → `ERR-UC35-04`), không dùng chung một mã. | Đề bài §1 (An ninh); Chốt 2026-11-18 |
| BR-5 | Email là duy nhất trên toàn hệ thống (không phân biệt chữ hoa chữ thường), dùng chung quy tắc với UC-03; không cho sửa email sau khi tạo. | UC-03 BR-1 (`01-account`) |
| BR-6 | Không xóa cứng tài khoản Admin. Khi không còn phụ trách, Trưởng BQL chuyển sang Đã khóa: mọi phiên đăng nhập của tài khoản đó kết thúc ngay, không đăng nhập lại được (khi cố đăng nhập đúng mật khẩu, hệ thống từ chối và báo tài khoản đã bị khóa — `ERR-UC04-07`, UC-04 Alt-10/BR-9, `01-account`) và ngừng nhận thông báo mới; mọi lịch sử thao tác cũ (hồ sơ đã duyệt, chứng nhận đã xác minh, tin đã đăng) giữ nguyên người thực hiện. | Nhóm đề xuất; UC-04 Alt-10 (`01-account`) |
| BR-7 | Trưởng BQL không thể tự khóa, tự hạ quyền hoặc bỏ nhóm quyền của chính tài khoản Trưởng BQL. Trên giao diện danh sách, dòng Trưởng BQL ẩn nút Khóa và Sửa quyền (song song với chặn ở Backend theo Alt-07/ERR-UC34-03). Hệ thống chỉ có đúng một tài khoản Trưởng BQL (BR-1). | Nhóm đề xuất; Chốt 2026-11-18 |
| BR-8 | Khi Trưởng BQL đặt lại mật khẩu cho một Admin, mọi phiên đăng nhập hiện tại của Admin đó bị kết thúc để buộc đăng nhập lại bằng mật khẩu mới; Admin tự đổi mật khẩu của mình ở UC-05. | UC-05 BR-4 (`01-account`) |
| BR-9 | Sidebar Trang quản trị chỉ hiện các mục thuộc nhóm quyền của Admin đang đăng nhập. Khi đăng nhập vào gốc Trang quản trị mà không có URL đích trước đó (UC-04 bước 6, `01-account`), hệ thống tự chuyển tới mục đầu tiên Admin có quyền theo thứ tự ưu tiên cố định: Hồ sơ bán hàng (UC-02) → Phân loại (UC-12) → Tin tức (UC-27) → Giám sát giao dịch (UC-35) → Quản trị viên (UC-34, chỉ Trưởng BQL). | Chốt 2026-11-18; UC-04 bước 6 (`01-account`) |

#### Acceptance Criteria

| ID | Given | When | Then | Covers |
|---|---|---|---|---|
| UC-34-AC-01 | Đã đăng nhập bằng Trưởng BQL; email `canbo1@phuvinh.vn` chưa có trên hệ thống | Mở Quản trị viên, chọn Thêm quản trị viên, nhập đủ thông tin và tích nhóm quyền Kiểm duyệt & Danh mục, chọn Tạo tài khoản | Tài khoản Admin được tạo ở trạng thái Đang hoạt động, hiện ở đầu danh sách; đăng nhập được bằng email và mật khẩu khởi tạo | Main Flow, BR-1, BR-3 |
| UC-34-AC-02 | Đang tạo Admin | Bỏ trống họ tên, nhập mật khẩu "abcdefgh" (thiếu số) hoặc "abc12" (dưới 8 ký tự), hoặc không chọn nhóm quyền nào | Hiển thị ERR-UC34-01 tại trường sai; không tạo tài khoản | Alt-01, BR-3 |
| UC-34-AC-03 | Đã có tài khoản Buyer dùng email `lan.tran@gmail.com` | Trưởng BQL tạo Admin với email `Lan.Tran@Gmail.com` | Hiển thị ERR-UC34-02; không tạo tài khoản trùng và không nâng cấp tài khoản Buyer đó | Alt-02, BR-1, BR-5 |
| UC-34-AC-04 | Admin A đang đăng nhập và chỉ có quyền Tin tức cộng đồng | Trưởng BQL bỏ quyền Tin tức cộng đồng và gán quyền Giám sát giao dịch cho Admin A | Ở thao tác kế tiếp mà không cần đăng nhập lại, Admin A vào được UC-35 và bị chặn bằng ERR-UC27-06 khi gọi UC-27 | Alt-03, BR-4 |
| UC-34-AC-05 | Admin A đang đăng nhập và trước đó đã duyệt hồ sơ xưởng X | Trưởng BQL khóa tài khoản Admin A | Admin A bị kết thúc phiên ở thao tác kế tiếp; thử đăng nhập lại đúng mật khẩu bị từ chối với `ERR-UC04-07`; hồ sơ xưởng X vẫn ghi người duyệt là Admin A | Alt-04, BR-6 |
| UC-34-AC-06 | Admin A đã bị khóa, có một hồ sơ mới gửi sau khi khóa | Trưởng BQL mở khóa Admin A; trước đó kiểm tra thông báo | Trong thời gian bị khóa, Admin A không nhận thông báo mới; sau khi mở khóa, Admin A đăng nhập lại được với nhóm quyền cũ | Alt-05, BR-6 |
| UC-34-AC-07 | Admin A đang đăng nhập ở một trình duyệt | Trưởng BQL đặt lại mật khẩu mới cho Admin A | Phiên đang mở của Admin A bị kết thúc; Admin A phải đăng nhập lại bằng mật khẩu mới | Alt-06, BR-8 |
| UC-34-AC-08 | Trưởng BQL đang đăng nhập | Gửi thao tác khóa hoặc sửa nhóm quyền của chính tài khoản Trưởng BQL | Hiển thị ERR-UC34-03; tài khoản Trưởng BQL vẫn Đang hoạt động và đủ toàn quyền | Alt-07, BR-7 |
| UC-34-AC-09 | Admin B chỉ có quyền Kiểm duyệt & Danh mục | Mở thẳng URL Quản trị viên (UC-34), Tin tức cộng đồng (UC-27) hoặc Giám sát giao dịch (UC-35) | Hiển thị ERR-UC34-04 ở Quản trị viên, ERR-UC27-06 ở Tin tức cộng đồng, ERR-UC35-04 ở Giám sát giao dịch; vào được UC-02 và UC-12 bình thường | Alt-08, BR-4 |
| UC-34-AC-10 | Tài khoản Admin mới tạo | Mở đường dẫn giỏ hàng hoặc gửi yêu cầu báo giá, hoặc xem đầu trang | Không có chức năng mua/bán, bị từ chối theo UC-30 / UC-18 | BR-2 |
| UC-34-AC-11 | Admin đang ở trạng thái Đã khóa (trước khi mở khóa) | Tìm thao tác xóa vĩnh viễn tài khoản Admin | Không có chức năng xóa cứng tài khoản | BR-6 |
| UC-34-AC-12 | Admin A tự muốn đổi mật khẩu và họ tên của mình | Mở Tài khoản của tôi (UC-05) | Sửa được họ tên và mật khẩu của chính mình; trường email và nhóm quyền không sửa được ở UC-05 | BR-5, BR-8 |
| UC-34-AC-13 | Admin C chỉ được gán quyền Giám sát giao dịch | Đăng nhập không có URL đích trước đó | Sidebar chỉ hiện mục Giám sát giao dịch; hệ thống tự chuyển vào UC-35 (không vào Hồ sơ bán hàng) | BR-9 |
| UC-34-AC-14 | API tải danh sách tài khoản quản trị gặp lỗi | Mở mục Quản trị viên | Hiển thị ERR-UC34-05 kèm nút Thử lại | Alt-09 |
| UC-34-AC-15 | Danh sách tài khoản quản trị đang hiện, gồm dòng Trưởng BQL | Tìm nút Khóa / Sửa quyền ở dòng Trưởng BQL | Không có nút Khóa hay Sửa quyền ở dòng đó | BR-7 |

### UC-35 — Theo dõi và thống kê giao dịch, đơn hàng toàn hệ thống

#### Detail

| Field | Content |
|---|---|
| Use case ID | UC-35 |
| Description | Trưởng BQL hoặc Admin được gán quyền Giám sát giao dịch xem các chỉ số thống kê tổng quan theo kỳ và tra cứu, lọc, tìm kiếm, xem chi tiết mọi đơn mua hàng (MH), đơn đặt riêng (DR) và yêu cầu báo giá (YC) trên toàn hệ thống ở chế độ chỉ đọc. |
| Actors | Trưởng BQL (Super Admin), hoặc Admin có quyền Giám sát giao dịch |
| Channel | Trang quản trị |
| Preconditions | • Người dùng đã đăng nhập bằng tài khoản Trưởng BQL hoặc Admin Đang hoạt động được gán quyền Giám sát giao dịch |
| Expected | **Thành công:**<br>• Hiển thị đúng các chỉ số thống kê theo kỳ đã chọn và danh sách giao dịch khớp bộ lọc<br>• Xem được chi tiết đầy đủ giao dịch ở chế độ chỉ đọc mà không làm thay đổi dữ liệu<br>**Thất bại:**<br>• Hiển thị lỗi rõ ràng tại khối bị lỗi hoặc trang lỗi; không lộ nháp của Buyer |
| Screens | • Tổng quan và Danh sách giao dịch (bước 1-4)<br>• Chi tiết giao dịch — chế độ chỉ đọc (bước 5) |
| Notifications | Không có |

#### Main Flow

Trigger: Admin chọn mục Giám sát giao dịch trong Trang quản trị.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Admin | Admin mở mục Giám sát giao dịch. | Hệ thống gửi yêu cầu tải riêng khối thống kê tổng quan (mặc định kỳ 30 ngày qua) và danh sách ở tab đầu tiên (Đơn mua hàng). |
| 2 | Backend | Backend kiểm tra quyền Giám sát giao dịch, tính các chỉ số tổng quan trong kỳ và trả danh sách đơn mua hàng toàn sàn (mới nhất trước, 20 dòng mỗi trang). | Trang quản trị hiển thị 8 chỉ số tổng quan (Tổng đơn mua hàng, Tổng đơn đặt riêng, Tổng yêu cầu báo giá đã gửi, Đơn đang xử lý, Đơn hoàn tất, Đơn đã hủy/bị từ chối, Tổng giá trị đơn hoàn tất, Giao dịch đang chờ hoàn tiền) và bảng danh sách đơn. |
| 3 | Admin | Admin chuyển giữa ba tab: Đơn mua hàng, Đơn đặt riêng, Yêu cầu báo giá. | Danh sách cập nhật sang loại giao dịch tương ứng mà không tải lại cả trang. |
| 4 | Admin | Admin lọc theo trạng thái đơn/yêu cầu, trạng thái thanh toán, xưởng, khoảng ngày tạo hoặc tìm theo mã (`MH-...`, `DR-...`, `YC-...`). | Danh sách thu hẹp theo điều kiện lọc và giữ phân trang 20 dòng mỗi trang. |
| 5 | Admin | Admin chọn một dòng giao dịch để xem chi tiết. | Trang chi tiết chỉ đọc hiển thị: mã, xưởng, Buyer, danh sách sản phẩm hoặc nội dung yêu cầu, thông tin giao/nhận hàng, tổng tiền/cọc, trạng thái đơn/yêu cầu, trạng thái thanh toán/hoàn tiền và toàn bộ lịch sử chuyển trạng thái; không có nút chỉnh sửa hay hủy đơn. Nếu đơn có yêu cầu hủy: hiển thị lý do Buyer gửi, quyết định của xưởng và cách xử lý cọc/hoàn tiền (UC-33). Với đơn DR: hiển thị liên kết hai chiều tới Yêu cầu báo giá gốc (YC) và danh sách lô sản xuất/QR liên quan nếu có (UC-14, `03-traceability`). |

Outcome: Admin nắm được tình hình giao dịch toàn hệ thống và kiểm tra được chi tiết giao dịch khi cần giám sát.

#### Alternative Flows

##### Alt-01 — Đổi kỳ thống kê tổng quan

- At step: 2 · Trigger: Admin chọn kỳ `7 ngày qua`, `30 ngày qua`, `Tháng này`, hoặc chọn `Tùy chọn` và nhập `Từ ngày – Đến ngày`.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Backend | Backend kiểm tra khoảng ngày hợp lệ và tính lại 8 chỉ số tổng quan trong kỳ mới (tính theo giờ Việt Nam). | Khối thống kê tổng quan cập nhật không tải lại trang; danh sách đang xem bên dưới không bị mất bộ lọc. |

Resume: Tiếp tục bước 3 hoặc 4.

##### Alt-02 — ERR-UC35-01 — Khoảng ngày không hợp lệ

- At step: Alt-01 hoặc bước 4 · Trigger: `Từ ngày` lớn hơn `Đến ngày`, hoặc `Từ ngày` ở tương lai.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Trang quản trị | Trang quản trị chặn gửi hoặc Backend từ chối yêu cầu lọc. | Hiển thị ERR-UC35-01 tại bộ lọc ngày; giữ nguyên số liệu đang hiển thị. |

Resume: Admin chọn lại khoảng ngày hợp lệ.

##### Alt-03 — Xem chi tiết yêu cầu báo giá và toàn bộ báo giá của các xưởng

- At step: 5 · Trigger: Admin chọn xem chi tiết một Yêu cầu báo giá (`YC`) ở tab Yêu cầu báo giá.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Backend | Backend trả nội dung yêu cầu đã gửi (kèm sản phẩm mẫu nếu có), danh sách mọi xưởng nhận, phản hồi của từng xưởng (Chưa phản hồi, Đã báo giá, Không nhận kèm lý do) và chi tiết các phiên bản báo giá của từng xưởng. | Trang chi tiết chỉ đọc hiển thị đầy đủ để Admin đối chiếu tính minh bạch của quá trình chào giá (kể cả khi yêu cầu đã chốt xưởng khác); nếu đã thành đơn đặt riêng thì có liên kết hai chiều tới chi tiết đơn `DR` (và chi tiết đơn `DR` cũng liên kết ngược về `YC` gốc). |

Resume: Kết thúc.

##### Alt-04 — Chưa có giao dịch hoặc không có kết quả khớp bộ lọc

- At step: 2 hoặc 4 · Trigger: Hệ thống chưa có giao dịch ở tab đang chọn, hoặc không có dòng nào khớp bộ lọc/mã tìm kiếm.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Trang quản trị | Trang quản trị hiện "Chưa có giao dịch nào" hoặc "Không tìm thấy giao dịch phù hợp" kèm nút Xóa bộ lọc; các chỉ số thống kê bằng 0 hiển thị `0` hoặc `0 đ`. | Không có màn hình trống gây hiểu nhầm lỗi. |

Resume: Kết thúc hoặc xóa bộ lọc để xem lại.

##### Alt-05 — ERR-UC35-02 — Không tìm thấy giao dịch

- At step: 5 · Trigger: Admin mở mã đơn hoặc mã yêu cầu không tồn tại (kể cả qua URL trực tiếp), hoặc mở một Yêu cầu báo giá đang ở trạng thái Nháp của Buyer.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Backend | Backend từ chối và không tiết lộ thông tin nháp. | Hiển thị ERR-UC35-02. |

Resume: Quay lại danh sách ở bước 2.

##### Alt-06 — ERR-UC35-03 — Không tải được dữ liệu

- At step: 1, 3, 4 hoặc Alt-01 · Trigger: API tải khối thống kê hoặc khối danh sách gặp lỗi.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Trang quản trị | Trang quản trị giữ nguyên phần còn lại và hiển thị lỗi kèm nút Thử lại tại đúng khối bị lỗi. | Hiển thị ERR-UC35-03; khối còn lại vẫn xem bình thường. |

Resume: Chọn Thử lại để lặp bước bị lỗi.

##### Alt-07 — ERR-UC35-04 — Không có quyền Giám sát giao dịch

- At step: 1 hoặc 5 · Trigger: Admin không có quyền Giám sát giao dịch mở mục hoặc đường dẫn của UC-35.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Backend | Backend từ chối truy cập. | Hiển thị ERR-UC35-04; không lộ bất kỳ số liệu hay giao dịch nào. |

Resume: Kết thúc.

#### Data Fields

| Field | Format | Validation | Required | Ghi chú |
|---|---|---|---|---|
| Kỳ thống kê | Chọn một | 7 ngày qua, 30 ngày qua (mặc định), Tháng này, Tùy chọn | Có | Theo giờ Việt Nam |
| Khoảng ngày thống kê (Từ ngày – Đến ngày) | Ngày (dd/MM/yyyy) | Từ ngày ≤ Đến ngày; Từ ngày ≤ hôm nay | Có điều kiện | Bắt buộc khi Kỳ thống kê chọn Tùy chọn; độc lập với khoảng ngày tạo của danh sách (BR-10) |
| Khoảng ngày tạo (danh sách) | Ngày (dd/MM/yyyy) | Từ ngày ≤ Đến ngày; Từ ngày ≤ hôm nay | Không | Lọc riêng cho bảng danh sách ở bước 4, không ảnh hưởng khối thống kê tổng quan (BR-10) |
| Tổng đơn mua hàng (MH) | Number, chỉ xem | Số nguyên ≥ 0 | — | Đếm đơn MH tạo trong kỳ |
| Tổng đơn đặt riêng (DR) | Number, chỉ xem | Số nguyên ≥ 0 | — | Đếm đơn DR tạo trong kỳ |
| Tổng yêu cầu báo giá (YC) | Number, chỉ xem | Số nguyên ≥ 0 | — | Đếm yêu cầu đã gửi trong kỳ, không tính Nháp |
| Số đơn đang xử lý | Number, chỉ xem | Số nguyên ≥ 0 | — | Trong các đơn MH + DR **tạo trong kỳ**: đơn MH (Chờ thanh toán, Chờ xác nhận, Đang chuẩn bị, Đang giao, Đã giao) + đơn DR (Chờ sản xuất, Đang sản xuất, Đang giao, Đã giao) đang ở các trạng thái này tại thời điểm xem |
| Số đơn hoàn tất | Number, chỉ xem | Số nguyên ≥ 0 | — | Trong các đơn MH + DR **tạo trong kỳ**: đơn đã đạt Hoàn tất tại thời điểm xem |
| Số đơn đã hủy / bị từ chối | Number, chỉ xem | Số nguyên ≥ 0 | — | Trong các đơn MH + DR **tạo trong kỳ**: đơn MH (Đã hủy, Bị từ chối) + đơn DR (Đã hủy) |
| Tổng giá trị đơn hoàn tất | Tiền VND, chỉ xem | Số nguyên ≥ 0, định dạng phân cách nghìn | — | Tổng thanh toán (tiền hàng cộng phí giao) của các đơn MH + DR **tạo trong kỳ** và đã đạt Hoàn tất tại thời điểm xem; nhãn UI ghi rõ "theo đơn tạo trong kỳ" (không phải doanh thu theo thời điểm hoàn tất) |
| Giao dịch đang chờ hoàn tiền | Number, chỉ xem | Số nguyên ≥ 0 | — | Chỉ số tồn đọng hiện thời, không giới hạn theo kỳ: đếm số đơn (ID đơn phân biệt) còn nghĩa vụ hoàn tiền chưa hoàn tất (`refunds.state != SUCCEEDED`, gồm cả đang retry `PENDING` và hoàn tiền thất bại `FAILED`); không tính đơn DR bị hủy mà xưởng giữ cọc |
| Tab danh sách | Chọn một | Đơn mua hàng, Đơn đặt riêng, Yêu cầu báo giá | — | Mặc định: Đơn mua hàng |
| Bộ lọc trạng thái | Chọn một | Tất cả hoặc trạng thái tương ứng với tab đang chọn | Không | Với YC không có lọc Nháp |
| Bộ lọc trạng thái thanh toán | Chọn một | Tất cả, Chưa thanh toán, Đã đặt cọc, Đã thanh toán, Đã hoàn tiền (kèm tùy chọn Đang chờ hoàn tiền) | Không | Áp dụng cho tab Đơn mua hàng và Đơn đặt riêng |
| Bộ lọc xưởng | Chọn một | Tất cả hoặc chọn từ danh sách xưởng đã duyệt | Không | Với tab YC lọc theo xưởng có trong danh sách nhận |
| Tìm theo mã | Text; length 20 | Khớp một phần hoặc đầy đủ mã MH, DR, YC | Không | Ví dụ: `MH-2026-0001`, `DR-2026-0002`, `YC-2026-0005` |
| Trang | Number | 20 dòng mỗi trang | — | Mới nhất trước |
| Chi tiết giao dịch | Chỉ xem | Mã, xưởng, Buyer, sản phẩm/yêu cầu, phương thức giao, giá/cọc, trạng thái đơn, trạng thái thanh toán, báo giá các xưởng (với YC), lịch sử trạng thái, liên kết YC ↔ DR hai chiều, thông tin yêu cầu hủy/xử lý cọc (UC-33), danh sách lô sản xuất (UC-14, với DR) | — | Chỉ đọc; thông tin liên hệ người nhận (số điện thoại, địa chỉ chi tiết) hiển thị đầy đủ cho Admin có quyền Giám sát giao dịch (chốt G-02) |

#### Error Messages

| Code | Condition | Message | Display | Flow |
|---|---|---|---|---|
| ERR-UC35-01 | Khoảng ngày lọc không hợp lệ | "Khoảng ngày không hợp lệ. Ngày bắt đầu phải nhỏ hơn hoặc bằng ngày kết thúc và không ở tương lai." | Inline | Alt-02 |
| ERR-UC35-02 | Giao dịch không tồn tại hoặc đang là nháp của Buyer | "Không tìm thấy giao dịch hoặc giao dịch chưa được gửi." | Trang lỗi | Alt-05 |
| ERR-UC35-03 | Lỗi tải thống kê hoặc danh sách | "Chưa tải được dữ liệu giám sát. Vui lòng thử lại." | Inline | Alt-06 |
| ERR-UC35-04 | Không có quyền Giám sát giao dịch | "Bạn không có quyền truy cập mục Giám sát giao dịch." | Trang lỗi | Alt-07 |

#### Business Rules

| ID | Rule | Source |
|---|---|---|
| BR-1 | Chỉ Trưởng BQL và Admin đang hoạt động có quyền Giám sát giao dịch mới truy cập được UC-35. Admin không có quyền này (và Guest, Buyer, Artisan) bị từ chối ở cả giao diện và API. | Chốt phạm vi `09-portal`; Đề bài §1 (An ninh) |
| BR-2 | Chế độ giám sát chỉ đọc: Trang quản trị không có thao tác tạo, sửa, chia đơn, gửi/chọn báo giá, xác nhận giao hàng, xác nhận thu tiền hay hủy yêu cầu/đơn hàng; số liệu chỉ phản ánh dữ liệu thực tế từ `04-quotation` và `08-order`. | Chốt phạm vi `09-portal` |
| BR-3 | Yêu cầu báo giá ở trạng thái Nháp là dữ liệu soạn riêng của Buyer nên không tính vào thống kê, không hiện trong danh sách và không mở được ở UC-35 (kể cả khi nhập thẳng mã). | Nhóm đề xuất |
| BR-4 | Tại chi tiết Yêu cầu báo giá (UC-35), Admin xem được đầy đủ các xưởng nhận, trạng thái phản hồi (Chưa phản hồi, Đã báo giá, Không nhận kèm lý do) và toàn bộ báo giá/phiên bản báo giá của từng xưởng để giám sát minh bạch — khác với quy tắc báo giá kín giữa các xưởng ở UC-21. | Nhóm đề xuất |
| BR-5 | Cả 7 chỉ số đếm đơn/yêu cầu (Tổng MH, Tổng DR, Tổng YC, Đang xử lý, Hoàn tất, Đã hủy/Bị từ chối, Tổng giá trị đơn hoàn tất) cùng lọc trên một tập duy nhất: đơn MH/DR tạo trong kỳ, hoặc yêu cầu YC gửi lần đầu trong kỳ (không tính Nháp). Trong tập đó, Đang xử lý/Hoàn tất/Đã hủy-Bị từ chối đếm theo trạng thái tại thời điểm xem; Tổng giá trị đơn hoàn tất là tổng thanh toán của các đơn trong tập đã đạt Hoàn tất. Cách tính này đảm bảo Tổng MH + Tổng DR = Đang xử lý + Hoàn tất + Đã hủy/Bị từ chối khớp nhau trong mọi kỳ; riêng Giao dịch đang chờ hoàn tiền không thuộc tập này (BR-6). | Nhóm đề xuất; Chốt 2026-11-18 |
| BR-6 | Chỉ số Giao dịch đang chờ hoàn tiền là tồn đọng hiện thời, không giới hạn theo kỳ thống kê đang chọn: đếm số đơn (ID đơn phân biệt, bất kể tạo khi nào) còn nghĩa vụ hoàn tiền chưa hoàn tất trong bảng `refunds` (`refunds.state != SUCCEEDED`, bao gồm cả đơn đang tự động thử lại `PENDING` và đơn đã dừng retry `FAILED` theo UC-33 BR-11). Đơn đặt riêng bị hủy mà xưởng được quyền giữ cọc (không phát sinh bản ghi hoàn tiền) không tính vào chỉ số này. Trên màn hình chi tiết, hiển thị rõ nhãn `Hoàn tiền thất bại` khi `refunds.state = FAILED` để BQL nhận diện xử lý thủ công. | UC-33 BR-11, G-02 (`08-order`); Chốt 2026-11-25 |
| BR-7 | Trạng thái thanh toán và trạng thái đơn/yêu cầu hiển thị tách riêng, bám đúng định nghĩa của `04-quotation` và `08-order`; số tiền hiển thị số nguyên đơn vị đồng (VND). | `04-quotation`; `08-order` |
| BR-8 | Đổi kỳ thống kê, chuyển tab, lọc, tìm kiếm và phân trang tải dữ liệu từng phần qua API, không tải lại cả trang; khối thống kê và khối danh sách tải độc lập để lỗi một khối không chặn khối còn lại. | Đề bài §1 (tiêu chí 4) |
| BR-9 | Chi tiết đơn DR hiển thị liên kết hai chiều YC ↔ DR, thông tin yêu cầu hủy/xử lý cọc/hoàn tiền (UC-33) và danh sách lô sản xuất/QR liên quan (UC-14, `03-traceability`) nếu có. | Chốt 2026-11-18; UC-33 (`08-order`), UC-14 (`03-traceability`) |
| BR-10 | Khoảng ngày thống kê tổng quan và khoảng ngày tạo của danh sách là hai bộ lọc độc lập. Khi đổi tab (Đơn mua hàng, Đơn đặt riêng, Yêu cầu báo giá), bộ lọc trạng thái và trạng thái thanh toán reset về `Tất cả`; tab Yêu cầu báo giá ẩn bộ lọc trạng thái thanh toán (không áp dụng). Khoảng ngày tạo và ô tìm theo mã giữ nguyên khi đổi tab. | Chốt 2026-11-18 |

#### Acceptance Criteria

| ID | Given | When | Then | Covers |
|---|---|---|---|---|
| UC-35-AC-01 | Có 3 đơn MH (2 Hoàn tất tổng 1.500.000 đ, 1 Đang giao), 2 đơn DR (1 Hoàn tất 5.000.000 đ, 1 Chờ sản xuất) và 2 yêu cầu YC đã gửi trong 30 ngày qua | Admin có quyền Giám sát giao dịch mở UC-35 | Tổng quan mặc định 30 ngày qua hiện đúng: MH = 3, DR = 2, YC = 2, Đang xử lý = 2, Hoàn tất = 3, Tổng giá trị hoàn tất = 6.500.000 đ; bảng mặc định hiện Đơn mua hàng mới nhất trước | Main Flow, BR-5 |
| UC-35-AC-02 | Có 20 đơn MH trong 7 ngày qua và 5 đơn MH từ 15 ngày trước | Đổi kỳ thống kê từ 30 ngày qua sang 7 ngày qua, rồi thử Tùy chọn | Khối thống kê cập nhật về 20 đơn mà không tải lại trang; bộ lọc danh sách bên dưới giữ nguyên | Alt-01, BR-8 |
| UC-35-AC-03 | Đang ở bộ lọc ngày thống kê hoặc danh sách | Chọn Từ ngày 2026-11-20, Đến ngày 2026-11-10 (hoặc Từ ngày ở tương lai) | Hiển thị ERR-UC35-01; số liệu cũ giữ nguyên | Alt-02 |
| UC-35-AC-04 | Yêu cầu YC-2026-0001 gửi 3 xưởng: xưởng A báo giá 2 phiên bản, xưởng B Không nhận, xưởng C chưa phản hồi; Buyer đã chọn xưởng A | Admin mở chi tiết YC-2026-0001 ở UC-35 | Thấy cả 3 xưởng, lý do Không nhận của xưởng B, cả 2 phiên bản báo giá của xưởng A và liên kết tới đơn DR đã tạo; không có nút sửa hay chọn lại xưởng | Alt-03, BR-2, BR-4 |
| UC-35-AC-05 | Tìm mã `MH-9999-0000` không khớp đơn nào | Nhấn tìm kiếm ở tab Đơn mua hàng | Hiện "Không tìm thấy giao dịch phù hợp" kèm nút Xóa bộ lọc | Alt-04 |
| UC-35-AC-06 | Có một yêu cầu YC đang ở trạng thái Nháp của Buyer | Admin xem thống kê/danh sách YC và thử mở thẳng URL của bản nháp đó | Bản nháp không tính vào thống kê, không có trong danh sách và mở thẳng báo ERR-UC35-02 | Alt-05, BR-3 |
| UC-35-AC-07 | API khối thống kê lỗi, API danh sách hoạt động bình thường | Mở Giám sát giao dịch | Khối thống kê hiện ERR-UC35-03 kèm nút Thử lại; bảng danh sách đơn vẫn tải và lọc được | Alt-06, BR-8 |
| UC-35-AC-08 | Admin chỉ có quyền Kiểm duyệt & Danh mục | Mở mục hoặc gọi API của UC-35 | Hiển thị ERR-UC35-04; không lộ số liệu hay danh sách đơn | Alt-07, BR-1 |
| UC-35-AC-09 | Đơn MH-2026-0004 đã thanh toán online 300.000 đ rồi Đã hủy nhưng cổng hoàn tiền chưa hoàn tất | Xem khối thống kê và lọc đơn chờ hoàn tiền ở UC-35 | Chỉ số Giao dịch đang chờ hoàn tiền tăng 1; chi tiết đơn hiện trạng thái đơn Đã hủy và trạng thái thanh toán tách riêng là Đã thanh toán (kèm nhãn đang xử lý hoàn tiền) | BR-6, BR-7 |
| UC-35-AC-10 | Admin mở chi tiết đơn MH và đơn DR bất kỳ ở UC-35 | Tìm các nút xác nhận, cập nhật giao hàng, thu tiền hoặc hủy đơn | Không có bất kỳ thao tác thay đổi trạng thái nào; mọi API sửa/hủy đơn từ chối tài khoản Admin | BR-2 |
| UC-35-AC-11 | Có 25 đơn MH thuộc nhiều xưởng | Chuyển tab, lọc theo xưởng A và sang trang 2 | Dữ liệu cập nhật qua API không tải lại trang; trang 2 hiện 5 đơn còn lại | Main Flow, BR-8 |
| UC-35-AC-12 | Đơn DR-2026-0007 bị Buyer gửi yêu cầu hủy, xưởng đồng ý và giữ cọc; đơn có 2 lô sản xuất đã tạo (UC-14) | Admin mở chi tiết DR-2026-0007 | Thấy lý do hủy, quyết định giữ cọc của xưởng, liên kết tới YC gốc và danh sách 2 lô sản xuất | BR-9 |
| UC-35-AC-13 | Đang lọc tab Đơn đặt riêng theo trạng thái "Đang sản xuất" và trạng thái thanh toán "Đã đặt cọc" | Chuyển sang tab Yêu cầu báo giá | Bộ lọc trạng thái reset về Tất cả; bộ lọc trạng thái thanh toán bị ẩn (không áp dụng cho YC); khoảng ngày tạo và ô tìm theo mã giữ nguyên | BR-10 |
| UC-35-AC-14 | Kỳ thống kê đang là 30 ngày qua; danh sách đang lọc khoảng ngày tạo 01/10–31/10 | Đổi kỳ thống kê sang 7 ngày qua | Khối thống kê đổi theo kỳ mới; bộ lọc khoảng ngày tạo của danh sách không bị ảnh hưởng | BR-10 |

## IV. Open Questions

| ID | Question | Affects | Decision |
|---|---|---|---|
| G-01 | Thông báo dành cho Admin (NT-UC01-01, NT-UC01-02, NT-UC07-01) chỉ gửi cho Trưởng BQL và Admin đang hoạt động có quyền Kiểm duyệt & Danh mục, hay gửi cho mọi Admin đang hoạt động? | UC-34; BRD `01-account`, `02-showcase`, `07-notification` | Đã chốt 2026-11-18: chỉ gửi cho Trưởng BQL và Admin đang hoạt động có quyền `Kiểm duyệt & Danh mục`, để Admin chỉ phụ trách Tin tức / Giám sát không nhận thông báo thừa rồi bấm vào bị báo lỗi không có quyền. Đã áp dụng ở `01-account` v0.4.0, `02-showcase` v0.2.0, `07-notification` v0.2.0. |
| G-02 | Thông tin liên hệ người nhận (số điện thoại, địa chỉ chi tiết) trên màn hình chi tiết giao dịch ở UC-35 hiển thị đầy đủ hay che một phần? | UC-35 Data Fields | Đã chốt 2026-11-18: hiển thị đầy đủ cho tài khoản có quyền `Giám sát giao dịch` để phục vụ tra cứu/đối chiếu sự cố (tương tự Admin xem được số điện thoại và minh chứng của chủ xưởng ở UC-02). |
