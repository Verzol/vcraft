---
title: "09 — Quản trị & Giám sát sàn — Đặc tả chức năng"
code: SRS-09
type: srs
status: official
version: 0.4.0
date: 2026-11-18
---

# 09 — Quản trị & Giám sát sàn — Đặc tả chức năng

[BRD 0.2.0](../../brd/09-portal/usecase.md) · [ERD](../../design/erd.md) · [Phụ thuộc liên nhóm](../README.md) · [Kiến trúc và giao diện](01-architecture.md) · [Ca nghiệm thu](03-test-cases.md)

Giới hạn dữ liệu và mã lỗi theo phiên bản BRD trên. Các phương án đang mở trong mục Open Questions cần nhóm xác nhận.

## UC-34 — Quản lý tài khoản và phân quyền quản trị viên

Chỉ Trưởng BQL được tạo Admin, sửa quyền, khóa, mở khóa và đặt lại mật khẩu. Kiểm tra quyền hiện hành ở Backend trước mỗi lệnh. Thay tập quyền nguyên tử và yêu cầu ít nhất một nhóm. Khóa hoặc đặt lại mật khẩu thu hồi mọi phiên trong cùng giao dịch. Bảo vệ tài khoản Trưởng BQL duy nhất cả khi gọi API trực tiếp.

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

| Mã lỗi | Điều kiện |
| --- | --- |
| ERR-UC34-01 | Dữ liệu không hợp lệ hoặc chưa chọn nhóm quyền |
| ERR-UC34-02 | Email đã tồn tại trên hệ thống |
| ERR-UC34-03 | Tự khóa hoặc đổi quyền tài khoản Trưởng BQL |
| ERR-UC34-04 | Không đủ quyền quản trị cho thao tác này |
| ERR-UC34-05 | Lỗi tải danh sách tài khoản quản trị |

- **BR-1** Duy nhất tài khoản Trưởng BQL (Super Admin) được nạp sẵn khi khởi tạo hệ thống và có quyền thực hiện UC-34; không có đăng ký Admin công khai và không nâng cấp tài khoản Buyer hoặc Artisan thành Admin.
- **BR-2** Tài khoản Admin do Trưởng BQL tạo là tài khoản tách biệt cho Trang quản trị: không mua hàng, không gửi yêu cầu báo giá, không bán hàng, không có giỏ hàng.
- **BR-3** Mỗi Admin thường phải được gán ít nhất một trong ba nhóm quyền cố định (Kiểm duyệt & Danh mục, Tin tức cộng đồng, Giám sát giao dịch); Trưởng BQL mặc định có toàn bộ quyền quản trị. Không tạo hay sửa định nghĩa nhóm quyền ngoài ba nhóm này.
- **BR-4** Thay đổi nhóm quyền có hiệu lực ngay từ thao tác kế tiếp mà không cần Admin đăng nhập lại. Mọi màn hình và API thuộc UC-02, UC-12, UC-27, UC-34, UC-35 đều kiểm tra đúng vai trò và nhóm quyền ở Backend, không chỉ ẩn mục trên menu (kể cả khi mở URL trực tiếp). Mỗi use case trả đúng mã lỗi không-có-quyền của chính nó (UC-02 → `ERR-UC04-06`, UC-12 → `ERR-UC12-03`, UC-27 → `ERR-UC27-06`, UC-34 → `ERR-UC34-04`, UC-35 → `ERR-UC35-04`), không dùng chung một mã.
- **BR-5** Email là duy nhất trên toàn hệ thống (không phân biệt chữ hoa chữ thường), dùng chung quy tắc với UC-03; không cho sửa email sau khi tạo.
- **BR-6** Không xóa cứng tài khoản Admin. Khi không còn phụ trách, Trưởng BQL chuyển sang Đã khóa: mọi phiên đăng nhập của tài khoản đó kết thúc ngay, không đăng nhập lại được (khi cố đăng nhập đúng mật khẩu, hệ thống từ chối và báo tài khoản đã bị khóa — `ERR-UC04-07`, UC-04 Alt-10/BR-9, `01-account`) và ngừng nhận thông báo mới; mọi lịch sử thao tác cũ (hồ sơ đã duyệt, chứng nhận đã xác minh, tin đã đăng) giữ nguyên người thực hiện.
- **BR-7** Trưởng BQL không thể tự khóa, tự hạ quyền hoặc bỏ nhóm quyền của chính tài khoản Trưởng BQL. Trên giao diện danh sách, dòng Trưởng BQL ẩn nút Khóa và Sửa quyền (song song với chặn ở Backend theo Alt-07/ERR-UC34-03). Hệ thống chỉ có đúng một tài khoản Trưởng BQL (BR-1).
- **BR-8** Khi Trưởng BQL đặt lại mật khẩu cho một Admin, mọi phiên đăng nhập hiện tại của Admin đó bị kết thúc để buộc đăng nhập lại bằng mật khẩu mới; Admin tự đổi mật khẩu của mình ở UC-05.
- **BR-9** Sidebar Trang quản trị chỉ hiện các mục thuộc nhóm quyền của Admin đang đăng nhập. Khi đăng nhập vào gốc Trang quản trị mà không có URL đích trước đó (UC-04 bước 6, `01-account`), hệ thống tự chuyển tới mục đầu tiên Admin có quyền theo thứ tự ưu tiên cố định: Hồ sơ bán hàng (UC-02) → Phân loại (UC-12) → Tin tức (UC-27) → Giám sát giao dịch (UC-35) → Quản trị viên (UC-34, chỉ Trưởng BQL).

## UC-35 — Theo dõi và thống kê giao dịch, đơn hàng toàn hệ thống

Tách truy vấn tổng quan, danh sách và chi tiết. Kiểm quyền Giám sát giao dịch trên từng API. Loại nháp ở cả ba truy vấn. Thống kê theo ngày tạo đơn hoặc lần gửi RFQ đầu tiên, phân nhóm theo trạng thái hiện tại. Chỉ số chờ hoàn đếm đơn hủy/từ chối có tiền online đang chờ hoàn trên mọi kỳ. Lấy đầy đủ thông tin nhận hàng cho quản trị đủ quyền. Chi tiết nối YC, phiên bản báo giá, DR, lô/QR, yêu cầu hủy và giao dịch tiền bằng ID.

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
| Giao dịch đang chờ hoàn tiền | Number, chỉ xem | Số nguyên ≥ 0 | — | Chỉ số tồn đọng hiện thời, không giới hạn theo kỳ: mọi đơn MH + DR Đã hủy/Bị từ chối (bất kể tạo khi nào) có thanh toán online nhưng chưa Đã hoàn tiền |
| Tab danh sách | Chọn một | Đơn mua hàng, Đơn đặt riêng, Yêu cầu báo giá | — | Mặc định: Đơn mua hàng |
| Bộ lọc trạng thái | Chọn một | Tất cả hoặc trạng thái tương ứng với tab đang chọn | Không | Với YC không có lọc Nháp |
| Bộ lọc trạng thái thanh toán | Chọn một | Tất cả, Chưa thanh toán, Đã đặt cọc, Đã thanh toán, Đã hoàn tiền (kèm tùy chọn Đang chờ hoàn tiền) | Không | Áp dụng cho tab Đơn mua hàng và Đơn đặt riêng |
| Bộ lọc xưởng | Chọn một | Tất cả hoặc chọn từ danh sách xưởng đã duyệt | Không | Với tab YC lọc theo xưởng có trong danh sách nhận |
| Tìm theo mã | Text; length 20 | Khớp một phần hoặc đầy đủ mã MH, DR, YC | Không | Ví dụ: `MH-2026-0001`, `DR-2026-0002`, `YC-2026-0005` |
| Trang | Number | 20 dòng mỗi trang | — | Mới nhất trước |
| Chi tiết giao dịch | Chỉ xem | Mã, xưởng, Buyer, sản phẩm/yêu cầu, phương thức giao, giá/cọc, trạng thái đơn, trạng thái thanh toán, báo giá các xưởng (với YC), lịch sử trạng thái, liên kết YC ↔ DR hai chiều, thông tin yêu cầu hủy/xử lý cọc (UC-33), danh sách lô sản xuất (UC-14, với DR) | — | Chỉ đọc; thông tin liên hệ người nhận (số điện thoại, địa chỉ chi tiết) hiển thị đầy đủ cho Admin có quyền Giám sát giao dịch (chốt G-02) |

| Mã lỗi | Điều kiện |
| --- | --- |
| ERR-UC35-01 | Khoảng ngày lọc không hợp lệ |
| ERR-UC35-02 | Giao dịch không tồn tại hoặc đang là nháp của Buyer |
| ERR-UC35-03 | Lỗi tải thống kê hoặc danh sách |
| ERR-UC35-04 | Không có quyền Giám sát giao dịch |

- **BR-1** Chỉ Trưởng BQL và Admin đang hoạt động có quyền Giám sát giao dịch mới truy cập được UC-35. Admin không có quyền này (và Guest, Buyer, Artisan) bị từ chối ở cả giao diện và API.
- **BR-2** Chế độ giám sát chỉ đọc: Trang quản trị không có thao tác tạo, sửa, chia đơn, gửi/chọn báo giá, xác nhận giao hàng, xác nhận thu tiền hay hủy yêu cầu/đơn hàng; số liệu chỉ phản ánh dữ liệu thực tế từ `04-quotation` và `08-order`.
- **BR-3** Yêu cầu báo giá ở trạng thái Nháp là dữ liệu soạn riêng của Buyer nên không tính vào thống kê, không hiện trong danh sách và không mở được ở UC-35 (kể cả khi nhập thẳng mã).
- **BR-4** Tại chi tiết Yêu cầu báo giá (UC-35), Admin xem được đầy đủ các xưởng nhận, trạng thái phản hồi (Chưa phản hồi, Đã báo giá, Không nhận kèm lý do) và toàn bộ báo giá/phiên bản báo giá của từng xưởng để giám sát minh bạch — khác với quy tắc báo giá kín giữa các xưởng ở UC-21.
- **BR-5** Cả 7 chỉ số đếm đơn/yêu cầu (Tổng MH, Tổng DR, Tổng YC, Đang xử lý, Hoàn tất, Đã hủy/Bị từ chối, Tổng giá trị đơn hoàn tất) cùng lọc trên một tập duy nhất: đơn MH/DR tạo trong kỳ, hoặc yêu cầu YC gửi lần đầu trong kỳ (không tính Nháp). Trong tập đó, Đang xử lý/Hoàn tất/Đã hủy-Bị từ chối đếm theo trạng thái tại thời điểm xem; Tổng giá trị đơn hoàn tất là tổng thanh toán của các đơn trong tập đã đạt Hoàn tất. Cách tính này đảm bảo Tổng MH + Tổng DR = Đang xử lý + Hoàn tất + Đã hủy/Bị từ chối khớp nhau trong mọi kỳ; riêng Giao dịch đang chờ hoàn tiền không thuộc tập này (BR-6).
- **BR-6** Chỉ số Giao dịch đang chờ hoàn tiền là tồn đọng hiện thời, không giới hạn theo kỳ thống kê đang chọn: đếm mọi đơn đã Đã hủy hoặc Bị từ chối (bất kể tạo khi nào) có phát sinh thanh toán online nhưng trạng thái thanh toán chưa chuyển Đã hoàn tiền (đang tự động thử lại theo UC-33 BR-11), giúp BQL phát hiện sớm trường hợp cổng thanh toán kẹt hoàn tiền dù đơn tạo ở kỳ trước.
- **BR-7** Trạng thái thanh toán và trạng thái đơn/yêu cầu hiển thị tách riêng, bám đúng định nghĩa của `04-quotation` và `08-order`; số tiền hiển thị số nguyên đơn vị đồng (VND).
- **BR-8** Đổi kỳ thống kê, chuyển tab, lọc, tìm kiếm và phân trang tải dữ liệu từng phần qua API, không tải lại cả trang; khối thống kê và khối danh sách tải độc lập để lỗi một khối không chặn khối còn lại.
- **BR-9** Chi tiết đơn DR hiển thị liên kết hai chiều YC ↔ DR, thông tin yêu cầu hủy/xử lý cọc/hoàn tiền (UC-33) và danh sách lô sản xuất/QR liên quan (UC-14, `03-traceability`) nếu có.
- **BR-10** Khoảng ngày thống kê tổng quan và khoảng ngày tạo của danh sách là hai bộ lọc độc lập. Khi đổi tab (Đơn mua hàng, Đơn đặt riêng, Yêu cầu báo giá), bộ lọc trạng thái và trạng thái thanh toán reset về `Tất cả`; tab Yêu cầu báo giá ẩn bộ lọc trạng thái thanh toán (không áp dụng). Khoảng ngày tạo và ô tìm theo mã giữ nguyên khi đổi tab.
