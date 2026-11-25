---
title: "08 — Mua hàng có sẵn — Đặc tả chức năng"
code: SRS-08
type: srs
status: official
version: 0.4.1
date: 2026-11-25
---

# 08 — Mua hàng có sẵn — Đặc tả chức năng

[BRD 0.3.0](../../brd/08-order/usecase.md) · [ERD](../../design/erd.md) · [Phụ thuộc liên nhóm](../README.md) · [Kiến trúc và giao diện](01-architecture.md) · [Ca nghiệm thu](03-test-cases.md)

Giới hạn dữ liệu và mã lỗi theo phiên bản BRD trên. Các phương án đang mở trong mục Open Questions cần nhóm xác nhận.

## UC-30 — Quản lý giỏ hàng

Guest hoặc Buyer thêm sản phẩm có sẵn vào giỏ, xem giỏ nhóm theo xưởng, đổi số lượng, xóa món và chọn món của một xưởng để mua. Giỏ của Guest lưu trên trình duyệt và được gộp vào giỏ tài khoản khi đăng nhập. Thêm vào giỏ không giữ hàng.

**Quyền và điều kiện** Không yêu cầu đăng nhập Thêm vào giỏ: sản phẩm loại Có sẵn, đang công khai (UC-11)

Giỏ Guest ở trình duyệt. Gộp theo sản phẩm, giới hạn bằng số còn bán. Nhóm theo xưởng và tách món không khả dụng.

| Field | Format | Validation | Required | Ghi chú |
|---|---|---|---|---|
| Số lượng | Number | Số nguyên từ 1 đến số còn bán | Có | Mặc định 1 khi thêm; thêm lại món đã có thì cộng thêm 1. Ví dụ: 2 |
| Chọn món | Hộp chọn | Chỉ món khả dụng | Không | Chỉ là lựa chọn trên màn hình, không đổi giỏ; Mua hàng ở nhóm nào thì chỉ lấy món đã chọn của nhóm đó (BR-4) |
| Nhóm xưởng | Chỉ xem | Tên xưởng, link về gian hàng (UC-08) | — | Mỗi xưởng một nhóm |
| Đơn giá | VND, chỉ xem | Giá hiện tại của sản phẩm | — | Ví dụ: "250.000 ₫" |
| Thành tiền | VND, chỉ xem | Đơn giá nhân số lượng | — | |
| Tạm tính của nhóm | VND, chỉ xem | Tổng thành tiền các món khả dụng đang được chọn trong nhóm | — | Chưa gồm phí giao; phí giao tính ở UC-31 |
| Nhóm Không khả dụng | Chỉ xem | Món hết hàng, bị Ẩn, của xưởng Tạm ngừng, hoặc của xưởng mình; kèm lý do | — | Không chọn được |
| Số món trên biểu tượng giỏ | Number, chỉ xem | Số dòng món trong giỏ; từ 100 trở lên hiện "99+" | — | Hiện ở đầu trang, cập nhật không tải lại trang |

| Mã lỗi | Điều kiện trả lỗi |
|---|---|
| ERR-UC30-01 | Vượt số còn bán |
| ERR-UC30-02 | Số lượng không hợp lệ |
| ERR-UC30-03 | Không thêm được vào giỏ |
| ERR-UC30-04 | Không tải được giỏ |
| ERR-UC30-05 | Tài khoản Admin |

## UC-31 — Checkout: giao hàng, thanh toán, tạo đơn

Buyer thanh toán các món đã chọn của một xưởng: kiểm tra lại món, chọn phương thức giao, nhập thông tin nhận hàng, chọn COD hoặc thanh toán online (ZaloPay sandbox) rồi đặt hàng. Hệ thống giữ hàng, chốt giá, tạo đơn mua hàng và báo xưởng.

**Quyền và điều kiện** Đã đăng nhập (Guest xử lý ở Alt-01) Có ít nhất một món khả dụng được chọn ở một nhóm xưởng trong giỏ (UC-30)

Giữ hàng nguyên tử cho đúng một xưởng. COD vào Chờ xác nhận, online vào Chờ thanh toán 15 phút. Xác minh kết quả ở Backend.

| Field | Format | Validation | Required | Ghi chú |
|---|---|---|---|---|
| Món thanh toán | Chỉ xem | Các món đã chọn của một xưởng, không vượt số còn bán | — | Ảnh, tên, đơn giá, số lượng, thành tiền |
| Họ tên người nhận | Text | 1-100 ký tự | Có | Điền sẵn từ hồ sơ, sửa được; chỉ áp cho đơn này. Ví dụ: "Nguyễn Văn A" |
| Số điện thoại | Text | Số điện thoại Việt Nam hợp lệ, cùng quy tắc UC-05 | Có | Điền sẵn từ hồ sơ, sửa được; chỉ áp cho đơn này |
| Địa chỉ nhận | Chọn theo danh mục cộng Text | Tỉnh hoặc thành phố, quận hoặc huyện, phường hoặc xã chọn theo danh mục (cấp dưới phụ thuộc cấp trên); số nhà, đường 5-255 ký tự | Có khi Xưởng tự giao hoặc GHN; không có khi Nhận tại xưởng | Nhập tại thanh toán; không có sổ địa chỉ (ngoài phạm vi) |
| Phương thức giao | Chọn một | Chỉ phương thức xưởng đang bật | Có | Nhận tại xưởng (0 ₫), Xưởng tự giao (phí cố định của xưởng), GHN (Should) |
| Phương thức thanh toán | Chọn một | COD hoặc ZaloPay | Có | Mặc định COD |
| Ghi chú cho xưởng | Text | Tối đa 500 ký tự | Không | |
| Tiền hàng | VND, chỉ xem | Tổng thành tiền các món | — | Số nguyên |
| Phí giao | VND, chỉ xem | Theo phương thức giao; từ 0 đến 1.000.000 ₫ với Xưởng tự giao | — | |
| Tổng thanh toán | VND, chỉ xem | Tiền hàng cộng phí giao | — | Đây là số tiền gửi sang cổng thanh toán |
| Mã đơn | Chỉ xem | MH-năm-số 4 chữ số, tăng dần | — | Ví dụ: MH-2026-0001 |
| Hạn thanh toán | Thời gian, chỉ xem | 15 phút kể từ lúc tạo đơn online | — | Chỉ có với đơn Chờ thanh toán |

| Mã lỗi | Điều kiện trả lỗi |
|---|---|
| ERR-UC31-01 | Món không còn mua được |
| ERR-UC31-02 | Thông tin nhận hàng chưa hợp lệ |
| ERR-UC31-03 | Phương thức giao không còn dùng |
| ERR-UC31-04 | Giá đã thay đổi |
| ERR-UC31-05 | Thanh toán thất bại hoặc bị hủy |
| ERR-UC31-06 | Cổng thanh toán không khởi tạo được |
| ERR-UC31-07 | Không tính được phí GHN |
| ERR-UC31-08 | Chưa có kết quả thanh toán |

| Thông báo | Người nhận |
|---|---|
| NT-UC31-01 | "Có đơn hàng mới {{orderCode}} ({{paymentNote}}). Vui lòng xác nhận trong 3 ngày." ({{paymentNote}}: "thu tiền khi giao" hoặc "đã thanh toán online") |
| NT-UC31-02 | "Đơn hàng {{orderCode}} đã thanh toán thành công và đang chờ xưởng xác nhận." |
| NT-UC31-03 | "Đơn hàng {{orderCode}} đã bị hủy do quá hạn thanh toán." |

## UC-32 — Quản lý đơn mua hàng

Tra cứu toàn sàn thuộc UC-35. Admin không được chuyển trạng thái đơn.

Xưởng xem đơn mua hàng của mình, xác nhận hoặc từ chối đơn, chuẩn bị, giao, xác nhận đã thu COD. Buyer xem đơn của mình, theo dõi trạng thái và xác nhận đã nhận để đơn Hoàn tất. Hệ thống tự hủy đơn xưởng không xác nhận sau 3 ngày và tự hoàn tất đơn Buyer không xác nhận sau 7 ngày.

**Quyền và điều kiện** Đã đăng nhập Artisan: gian hàng có đơn mua hàng (xưởng Tạm ngừng vẫn xử lý đơn đang thực hiện) Buyer: tài khoản có đơn mua hàng

Kiểm quyền Buyer/xưởng từng bước. Xử lý nhận tại xưởng và giao hàng theo luồng khác nhau. Tác vụ hết hạn kiểm tra lại trạng thái.

| Field | Format | Validation | Required | Ghi chú |
|---|---|---|---|---|
| Mã đơn | Chỉ xem | Dạng MH-năm-số | — | Ví dụ: MH-2026-0001 |
| Trạng thái đơn | Chỉ xem | Chờ thanh toán, Chờ xác nhận, Đang chuẩn bị, Đang giao, Đã giao, Hoàn tất, Bị từ chối, Đã hủy | — | |
| Trạng thái thanh toán | Chỉ xem | Chưa thanh toán, Đã thanh toán, Đã hoàn tiền | — | Độc lập với trạng thái đơn; Đã đặt cọc chỉ có với đơn đặt riêng |
| Hạn xác nhận còn lại | Thời gian, chỉ xem | 3 ngày kể từ lúc đơn vào Chờ xác nhận | — | Chỉ hiện ở đơn Chờ xác nhận |
| Lý do từ chối | Text | 10-500 ký tự | Có khi từ chối | Hiện cho Buyer ở NT-UC32-02 và chi tiết đơn |
| Xác nhận đã thu đủ tiền | Hộp chọn | Phải chọn | Có với đơn COD khi chuyển sang Đã giao | Chỉ có ở đơn COD; không chọn thì không đổi được trạng thái |
| Thông tin người nhận | Chỉ xem | Họ tên, số điện thoại, địa chỉ nhận | — | Chỉ Buyer và xưởng của đơn xem |
| Lịch sử trạng thái | Chỉ xem | Mỗi dòng gồm trạng thái, thời điểm, người thực hiện (Buyer, xưởng, hệ thống), lý do nếu có | — | Mới nhất trước |
| Lọc trạng thái | Chọn một | Tất cả hoặc một trong tám trạng thái | Không | Lọc không tải lại trang |
| Tìm theo mã đơn | Text | Tối đa 20 ký tự | Không | Khớp một phần |

| Mã lỗi | Điều kiện trả lỗi |
|---|---|
| ERR-UC32-01 | Đơn đã đổi trạng thái |
| ERR-UC32-02 | Lý do không hợp lệ |
| ERR-UC32-03 | Chuyển trạng thái không hợp lệ |
| ERR-UC32-04 | Chưa xác nhận đã thu tiền |
| ERR-UC32-05 | Không xem được đơn |
| ERR-UC32-06 | Không tải được đơn |

| Thông báo | Người nhận |
|---|---|
| NT-UC32-01 | "Xưởng đã xác nhận đơn hàng {{orderCode}} và đang chuẩn bị hàng." |
| NT-UC32-02 | "Xưởng đã từ chối đơn hàng {{orderCode}}. Lý do: {{reason}}." |
| NT-UC32-03 | "Đơn hàng {{orderCode}} đang được giao." |
| NT-UC32-04 | "Đơn hàng {{orderCode}} đã giao. Vui lòng xác nhận đã nhận hàng; đơn tự hoàn tất sau 7 ngày." |
| NT-UC32-05 | "Đơn hàng {{orderCode}} đã tự động hoàn tất." |
| NT-UC32-06 | "Đơn hàng {{orderCode}} đã bị hủy do xưởng không xác nhận trong 3 ngày." |
| NT-UC32-07 | "Đơn hàng {{orderCode}} đã bị hủy do quá 3 ngày chưa được xác nhận." |
| NT-UC32-08 | "Đơn hàng {{orderCode}} đã hoàn tất." |

## UC-33 — Hủy đơn hàng (đơn mua và đơn đặt riêng)

UC-35 đọc thanh toán, yêu cầu hủy và hoàn tiền từ cùng nguồn. Mỗi đơn tồn đọng chỉ đếm một lần bất kể số lần thử hoàn.

Buyer hoặc xưởng hủy đơn theo trạng thái của đơn. Đơn mua hàng: Buyer tự hủy khi Chờ xác nhận, chỉ xưởng hủy khi Đang chuẩn bị, từ Đang giao không hủy. Đơn đặt riêng: Buyer hoặc xưởng hủy khi Chờ sản xuất; khi Đang sản xuất Buyer gửi yêu cầu hủy và xưởng quyết định; từ Đang giao không hủy. Hủy đơn mua hoàn số lượng về số còn bán (trừ phần hàng hư hỏng khi xưởng hủy đơn Đang chuẩn bị); tiền đã thanh toán online được hoàn qua cổng thanh toán.

**Quyền và điều kiện** Đã đăng nhập Đơn thuộc tài khoản Buyer hoặc gian hàng của xưởng Đơn đang ở trạng thái cho phép hủy với người thao tác (BR-1, BR-2)

Phân nhánh theo loại đơn, bên yêu cầu, trạng thái và cọc đã thu. Hoàn tồn đơn mua đúng lượng được trả. Callback muộn (gồm cả đơn đặt riêng quá hạn cọc đã tự hủy) chỉ hoàn tiền, không khôi phục đơn. Hoàn tiền thất bại dùng idempotency key theo `(payment_id, reason)`, tra trạng thái cổng trước mỗi lần thử lại tối đa 5 lần (`1p → 5p → 15p → 1h → 6h`); gặp lỗi vĩnh viễn hoặc hết ngưỡng đặt `refunds.state = FAILED` (giữ nguyên trạng thái đơn và `payment_state`) và vẫn tính vào chỉ số tồn đọng của UC-35.

| Field | Format | Validation | Required | Ghi chú |
|---|---|---|---|---|
| Lý do hủy | Text | 10-500 ký tự | Buyer hủy đơn mua hoặc đơn Chờ sản xuất: không. Buyer gửi yêu cầu hủy: có. Xưởng hủy đơn, xưởng từ chối yêu cầu hủy: có | Hiện cho bên còn lại trong thông báo và chi tiết đơn |
| Số lượng trả về hàng còn bán | Number, theo từng món | Số nguyên từ 0 đến số lượng đặt | Có khi xưởng hủy đơn Đang chuẩn bị | Mặc định bằng số lượng đặt; phần còn lại là hàng hư hỏng, không cộng vào số còn bán. Các trường hợp hủy khác luôn trả đủ |
| Cách xử lý cọc | Chọn một | Giữ cọc hoặc Hoàn cọc | Có khi xưởng đồng ý yêu cầu hủy đơn có cọc | Mặc định Giữ cọc; xưởng hủy chủ động luôn hoàn đủ nên không có lựa chọn |
| Tình trạng yêu cầu hủy | Chỉ xem | Đang chờ xưởng, Xưởng đồng ý, Xưởng từ chối, Đã rút, Tự đóng | — | Mỗi đơn chỉ có một yêu cầu Đang chờ xưởng tại một thời điểm |
| Người hủy | Chỉ xem | Buyer, xưởng, hệ thống | — | Hiện trong lịch sử đơn |
| Số tiền hoàn | VND, chỉ xem | Không vượt số đã thanh toán | — | Đơn mua: tổng thanh toán; đơn đặt riêng: cọc đã trả |
| Trạng thái thanh toán | Chỉ xem | Đã thanh toán hoặc Đã đặt cọc, chuyển Đã hoàn tiền khi hoàn thành công | — | Giữ nguyên khi xưởng giữ cọc |

| Mã lỗi | Điều kiện trả lỗi |
|---|---|
| ERR-UC33-01 | Không hủy được ở trạng thái hiện tại |
| ERR-UC33-02 | Đơn đã đổi trạng thái |
| ERR-UC33-03 | Lý do không hợp lệ |
| ERR-UC33-04 | Đã có yêu cầu hủy đang chờ |
| ERR-UC33-05 | Không có quyền hủy |
| ERR-UC33-06 | Hoàn tiền thất bại |
| ERR-UC33-07 | Số lượng trả về không hợp lệ |

| Thông báo | Người nhận |
|---|---|
| NT-UC33-01 | "Người mua đã hủy đơn hàng {{orderCode}}." |
| NT-UC33-02 | "Xưởng đã hủy đơn hàng {{orderCode}}. Lý do: {{reason}}. {{refundNote}}" ({{refundNote}}: "Số tiền đã thanh toán sẽ được hoàn lại." hoặc để trống với đơn COD) |
| NT-UC33-03 | "Người mua gửi yêu cầu hủy đơn đặt riêng {{orderCode}}. Vui lòng phản hồi." |
| NT-UC33-04 | "Người mua đã rút yêu cầu hủy đơn đặt riêng {{orderCode}}." |
| NT-UC33-05 | "Xưởng đồng ý hủy đơn đặt riêng {{orderCode}}. {{depositNote}}" ({{depositNote}}: "Xưởng giữ khoản cọc để bù nguyên liệu.", "Khoản cọc sẽ được hoàn lại." hoặc để trống với đơn không có cọc) |
| NT-UC33-06 | "Xưởng từ chối yêu cầu hủy đơn đặt riêng {{orderCode}}. Lý do: {{reason}}. Đơn tiếp tục được sản xuất." |
| NT-UC33-07 | "Đơn đặt riêng {{orderCode}} đã bị {{actor}} hủy.{{reasonNote}} {{depositNote}}" ({{actor}}: "người mua" hoặc "xưởng"; {{reasonNote}}: " Lý do: …" khi xưởng hủy; {{depositNote}}: "Khoản cọc sẽ được hoàn lại." hoặc để trống) |
| NT-UC33-08 | "Đã hoàn {{amount}} cho đơn hàng {{orderCode}} về nguồn thanh toán ban đầu." |
| NT-UC33-09 | "Khoản thanh toán {{amount}} của đơn hàng {{orderCode}} đã được hoàn cho người mua." |
