---
title: "04 — Đặt hàng riêng — Đặc tả chức năng"
code: SRS-04
type: srs
status: official
version: 0.3.0
date: 2026-11-18
---

# 04 — Đặt hàng riêng — Đặc tả chức năng

[BRD 0.1.0](../../brd/04-quotation/usecase.md) · [ERD](../../design/erd.md) · [Phụ thuộc liên nhóm](../README.md) · [Kiến trúc và giao diện](01-architecture.md) · [Ca nghiệm thu](03-test-cases.md)

Giới hạn dữ liệu và mã lỗi theo phiên bản BRD trên. Các phương án đang mở trong mục Open Questions cần nhóm xác nhận.

## UC-18 — Tạo, lưu nháp, xóa nháp và gửi yêu cầu báo giá

Buyer soạn yêu cầu báo giá cho nhu cầu đặt riêng (mẫu, số lượng, hình thức nhận hàng, hạn), lưu nháp hoặc gửi. Yêu cầu gửi riêng một xưởng (từ trang sản phẩm hoặc trang gian hàng) hoặc gửi chung nhiều xưởng theo phân loại (từ Đặt hàng theo yêu cầu). Hệ thống chốt danh sách xưởng nhận, thông báo xưởng, theo dõi hạn, nhắc xưởng trước hạn; Buyer gia hạn hoặc tạo yêu cầu mới từ yêu cầu đã đóng.

**Quyền và điều kiện** Đã đăng nhập bằng tài khoản Buyer (Artisan thao tác như Buyer, không gửi cho xưởng của chính mình) Khi Gửi yêu cầu: có ít nhất một xưởng đủ điều kiện nhận (Alt-07, Alt-09). Soạn và lưu nháp không cần điều kiện này

Phân biệt ba nguồn yêu cầu. Kiểm lại xưởng đủ điều kiện khi gửi, đóng băng danh sách nhận cùng RFQ. Lịch nhắc, hết hạn và gia hạn dựa trên hạn phản hồi.

| Field | Format | Validation | Required | Ghi chú |
|---|---|---|---|---|
| Nguồn yêu cầu | Chỉ xem | Từ sản phẩm, Gửi riêng cho xưởng, Gửi chung theo phân loại | — | Hiện ở form và ở xưởng nhận. Không đổi sau khi gửi |
| Sản phẩm mẫu | Tên và ảnh, chỉ xem | Chỉ có khi gửi từ trang sản phẩm; chụp lúc gửi (BR-6) | — | Xưởng thấy "từ sản phẩm {tên}" |
| Xưởng nhận | Danh sách tên, chỉ xem | Đang hoạt động, không phải xưởng của Buyer; chốt lúc gửi (BR-3) | — | Gửi riêng: một xưởng. Gửi chung: các xưởng đúng phân loại, hoặc tất cả xưởng đang nhận nếu Buyer đồng ý (Alt-08) |
| Phân loại | Chọn một | Chỉ phân loại đang Hiển thị | Có điều kiện | Bắt buộc với gửi chung. Từ UC-12 |
| Mô tả mẫu cần đặt | Text; length 2000 | Không để trống | Có | Ví dụ: "Cốc tre khắc logo Trường ĐH Công nghệ, cao 10 cm, màu tự nhiên" |
| Số lượng | Number | Số nguyên từ 1 đến 1.000.000 | Có | Ví dụ: 5000 |
| Đơn vị | Text; length 30 | Không để trống | Có | Ví dụ: "chiếc", "bộ" |
| Ảnh mẫu | Ảnh JPG / PNG / WebP | 0–5 ảnh, mỗi ảnh ≤ 10 MB | Không | Ảnh sản phẩm mẫu hiện riêng, không tính vào 5 ảnh |
| Ngày cần hàng | Date | Không ở quá khứ | Không | Chỉ là mong muốn; xưởng báo thời gian hoàn thành ở báo giá (UC-19) |
| Hình thức nhận hàng | Chọn một | Nhận tại xưởng hoặc Giao tận nơi | Có | Quyết định phí giao trong báo giá |
| Địa chỉ nhận hàng | Tỉnh/thành, quận/huyện, phường/xã chọn theo danh mục; địa chỉ chi tiết text length 150 | Đủ bốn mục | Có điều kiện | Bắt buộc khi Giao tận nơi. Xưởng chỉ thấy đến quận/huyện trước khi báo giá được chấp nhận (BR-8) |
| Số điện thoại người nhận | Text; length 10 | Số di động Việt Nam, 10 chữ số | Có điều kiện | Bắt buộc khi Giao tận nơi; điền sẵn từ tài khoản, được sửa. Xưởng chỉ thấy sau khi báo giá được chấp nhận. Ví dụ: 0912345678 |
| Hạn nhận báo giá | Date | Từ ngày mai đến tối đa 30 ngày kể từ ngày gửi; hết lúc 23:59 | Có điều kiện | Bắt buộc với yêu cầu gửi chung. Buyer chỉ chọn ngày |
| Hạn phản hồi | Date | Từ ngày mai đến tối đa 30 ngày kể từ ngày gửi; hết lúc 23:59 | Không | Yêu cầu gửi riêng. Để trống thì hệ thống đặt 3 ngày kể từ ngày gửi |
| Mã yêu cầu | Text | Hệ thống sinh, duy nhất | — | Ví dụ: "YC-2026-0001" |
| Trạng thái yêu cầu | Chỉ xem | Nháp, Đã gửi, Đang nhận báo giá, Đã chấp nhận, Đã hủy, Hết hạn | — | |
| Lý do hết hạn | Chỉ xem | Quá hạn phản hồi; Xưởng không nhận; Tất cả xưởng không nhận; Hết hạn nhận báo giá; Mọi báo giá đã hết hiệu lực | — | Chỉ khi Hết hạn |
| Số lần gia hạn | Chỉ xem | Tối đa 1 | — | Chỉ yêu cầu gửi riêng (Alt-14) |

| Mã lỗi | Điều kiện trả lỗi |
|---|---|
| ERR-UC18-01 | Dữ liệu không hợp lệ |
| ERR-UC18-02 | Ảnh không hợp lệ |
| ERR-UC18-03 | Hạn không hợp lệ |
| ERR-UC18-04 | Xưởng nhận không khả dụng |
| ERR-UC18-05 | Không có xưởng để gửi |
| ERR-UC18-06 | Không gia hạn được |
| ERR-UC18-07 | Không có quyền |

| Thông báo | Người nhận |
|---|---|
| NT-UC18-01 | Từng xưởng nhận |
| NT-UC18-02 | Xưởng nhận chưa phản hồi |
| NT-UC18-03 | Buyer sở hữu yêu cầu |
| NT-UC18-04 | Buyer sở hữu yêu cầu |
| NT-UC18-05 | Xưởng nhận |

## UC-19 — Tiếp nhận yêu cầu, gửi báo giá hoặc không nhận

Xưởng nhận yêu cầu báo giá, xem chi tiết, rồi gửi báo giá cho toàn bộ số lượng (đơn giá, phí giao, thời gian hoàn thành, tỷ lệ cọc, hiệu lực), cập nhật báo giá trước hạn, hoặc không nhận kèm lý do. Xưởng cũng xử lý Yêu cầu cập nhật giá của Buyer khi báo giá đã hết hiệu lực.

**Quyền và điều kiện** Đã đăng nhập Xưởng có trong danh sách xưởng nhận của yêu cầu Gửi hoặc cập nhật báo giá: gian hàng đang hoạt động. Không nhận: được cả khi gian hàng đang Tạm ngừng (Alt-05)

Kiểm quyền đúng xưởng nhận. Lưu phiên bản kế tiếp, tính tiền hàng, phí giao và cọc theo báo giá. Hạn hiệu lực thuộc phiên bản.

| Field | Format | Validation | Required | Ghi chú |
|---|---|---|---|---|
| Thông tin yêu cầu | Chỉ xem | Nguồn, sản phẩm mẫu, mô tả, ảnh, số lượng, ngày cần hàng, hình thức nhận, hạn | — | Địa chỉ chỉ hiện đến quận/huyện cho đến khi báo giá được chấp nhận (UC-18, BR-8) |
| Đơn giá | Number (VND, mỗi đơn vị) | Số nguyên từ 1.000 đến 1.000.000.000 | Có | Ví dụ: 85000 |
| Phí giao | Number (VND) | Số nguyên từ 0 đến 50.000.000; bằng 0 nếu Nhận tại xưởng | Có | Ví dụ: 150000 |
| Thời gian hoàn thành | Number (ngày) | Số nguyên từ 1 đến 365 | Có | Tính từ lúc Buyer chấp nhận. Ví dụ: 21 |
| Tỷ lệ cọc | Chọn một | 0%, 30% hoặc 50% | Có | Xưởng chọn (BR-4) |
| Hiệu lực đến ngày | Date | Hết 23:59 ngày chọn; theo BR-5 | Có | Ví dụ: 2026-12-20 |
| Ghi chú | Text; length 500 | — | Không | Chất liệu, quy cách, điều kiện |
| Tổng giá trị | Number, chỉ xem | Đơn giá nhân số lượng, cộng phí giao | — | Hệ thống tính, hiển thị cho cả hai bên |
| Số tiền cọc | Number, chỉ xem | Tổng giá trị nhân tỷ lệ cọc, làm tròn đến đồng | — | 0 nếu tỷ lệ cọc 0% |
| Phiên bản | Number, chỉ xem | Bắt đầu từ 1, tăng 1 mỗi lần cập nhật | — | |
| Trạng thái báo giá | Chỉ xem | Còn hiệu lực, Hết hiệu lực, Đã đóng, Được chấp nhận | — | |
| Phản hồi của xưởng | Chỉ xem | Chưa phản hồi, Đã báo giá, Không nhận | — | |
| Lý do không nhận | Chọn một | Không làm được mẫu này; Không đủ năng lực cho số lượng; Không kịp thời gian; Khác | Có (khi Không nhận) | |
| Ghi chú lý do | Text; length 300 | Bắt buộc khi chọn Khác | Có điều kiện | |

| Mã lỗi | Điều kiện trả lỗi |
|---|---|
| ERR-UC19-01 | Dữ liệu báo giá không hợp lệ |
| ERR-UC19-02 | Yêu cầu đã đóng nhận báo giá |
| ERR-UC19-03 | Gian hàng đang Tạm ngừng |
| ERR-UC19-04 | Đã phản hồi |
| ERR-UC19-05 | Không có quyền |
| ERR-UC19-06 | Yêu cầu cập nhật giá đã đóng |

| Thông báo | Người nhận |
|---|---|
| NT-UC19-01 | Buyer sở hữu yêu cầu |
| NT-UC19-02 | Buyer sở hữu yêu cầu |
| NT-UC19-03 | Buyer sở hữu yêu cầu |
| NT-UC19-04 | Buyer sở hữu yêu cầu |
| NT-UC19-05 | Buyer sở hữu yêu cầu |

## UC-20 — So sánh và chấp nhận báo giá

Buyer so sánh các báo giá của yêu cầu, chấp nhận một báo giá còn hiệu lực, thanh toán cọc (nếu có) để tạo đơn đặt riêng; khi báo giá hết hiệu lực mà vẫn muốn mua thì Yêu cầu cập nhật giá. Hệ thống tự cho báo giá hết hiệu lực và nhắc Buyer trước hạn.

**Quyền và điều kiện** Đã đăng nhập, là chủ yêu cầu Yêu cầu ở Đang nhận báo giá (có ít nhất một báo giá Còn hiệu lực), hoặc Hết hạn khi dùng Yêu cầu cập nhật giá (Alt-08)

Buyer xác nhận phiên bản còn hiệu lực. Khóa cùng RFQ với UC-23. Tạo một đơn DR ngay lúc chấp nhận và đóng lựa chọn còn lại.

| Field | Format | Validation | Required | Ghi chú |
|---|---|---|---|---|
| Danh sách báo giá | Bảng, chỉ xem | Mọi báo giá của yêu cầu; xưởng, đơn giá, phí giao, tổng giá trị, thời gian hoàn thành, tỷ lệ cọc, số tiền cọc, hiệu lực, phiên bản, trạng thái | — | Sắp xếp theo giá hoặc thời gian hoàn thành |
| Báo giá được chọn | Chọn một | Còn hiệu lực, đúng phiên bản đang xem | Có | |
| Số tiền cọc | Number, chỉ xem | Tổng giá trị nhân tỷ lệ cọc, làm tròn đến đồng | — | Thanh toán qua ZaloPay sandbox |
| Phần còn lại | Number, chỉ xem | Tổng giá trị trừ số tiền cọc | — | Thu khi giao hoặc nhận (UC-22) |
| Mã đơn | Text | Hệ thống sinh, duy nhất | — | Ví dụ: "DR-2026-0001" |
| Trạng thái thanh toán | Chỉ xem | Chưa thanh toán, Đã đặt cọc, Đã thanh toán, Đã hoàn tiền | — | |
| Hạn trả lời cập nhật giá | Date-time, chỉ xem | 3 ngày kể từ lúc Buyer yêu cầu | — | Hiện cho cả Buyer và xưởng |

| Mã lỗi | Điều kiện trả lỗi |
|---|---|
| ERR-UC20-01 | Báo giá đã hết hiệu lực |
| ERR-UC20-02 | Báo giá có phiên bản mới |
| ERR-UC20-03 | Yêu cầu đã chốt hoặc đã đóng |
| ERR-UC20-04 | Thanh toán cọc không thành công |
| ERR-UC20-05 | Không có quyền |
| ERR-UC20-06 | Không yêu cầu cập nhật giá được |

| Thông báo | Người nhận |
|---|---|
| NT-UC20-01 | Buyer sở hữu yêu cầu |
| NT-UC20-02 | Xưởng được chọn |
| NT-UC20-03 | Các xưởng còn lại đã nhận yêu cầu |
| NT-UC20-04 | Xưởng có báo giá hết hiệu lực |
| NT-UC20-05 | Xưởng được chọn |

## UC-21 — Xem yêu cầu, báo giá và đơn đặt riêng

Buyer xem danh sách và chi tiết yêu cầu báo giá, báo giá nhận được và đơn đặt riêng của mình. Xưởng xem danh sách yêu cầu nhận, báo giá của mình và đơn đặt riêng của xưởng. Mỗi bên chỉ thấy phạm vi của mình; báo giá là kín.

**Quyền và điều kiện** Đã đăng nhập Buyer: có ít nhất một yêu cầu hoặc đơn; Artisan: gian hàng đã từng nhận yêu cầu hoặc đơn

Chọn góc nhìn mua/bán từ phiên. Xưởng chỉ đọc lời mời và giá của mình. Đọc đơn qua Buyer hoặc xưởng trúng.

| Field | Format | Validation | Required | Ghi chú |
|---|---|---|---|---|
| Tab | Chọn một | Theo vai trò (xem bước 2) | — | |
| Mã tìm kiếm | Text; length 20 | Mã yêu cầu hoặc mã đơn | Không | Ví dụ: "YC-2026-0001" |
| Trang | Number | 10 mục mỗi trang | — | |
| Lịch sử trạng thái | Danh sách, chỉ xem | Trạng thái, người thực hiện, thời điểm | — | |
| Nội dung yêu cầu | Chỉ xem | Như UC-18 Data Fields | — | Xưởng thấy địa chỉ đến quận/huyện cho đến khi báo giá được chấp nhận |
| Báo giá | Chỉ xem | Như UC-19 Data Fields | — | Báo giá kín (BR-2) |
| Đơn đặt riêng | Chỉ xem | Mã, xưởng, Buyer (xưởng thấy sau khi chấp nhận), yêu cầu và báo giá lúc chấp nhận, trạng thái đơn, trạng thái thanh toán | — | |

| Mã lỗi | Điều kiện trả lỗi |
|---|---|
| ERR-UC21-01 | Không có quyền hoặc không tồn tại |
| ERR-UC21-02 | Không tải được |

## UC-22 — Cập nhật và theo dõi đơn đặt riêng

Xưởng cập nhật đơn đặt riêng qua các trạng thái sản xuất và giao hàng, xác nhận đã thu phần còn lại khi giao hoặc nhận. Buyer theo dõi và xác nhận đã nhận để đơn Hoàn tất; không xác nhận thì sau 7 ngày đơn tự Hoàn tất.

**Quyền và điều kiện** Đã đăng nhập Có đơn đặt riêng chưa Hoàn tất hoặc Đã hủy

Chặn bắt đầu nếu còn thiếu cọc. Ghi tiến độ, xác nhận thu phần còn lại và mốc giao. Dùng UC-33 cho hủy hoặc hoàn cọc.

| Field | Format | Validation | Required | Ghi chú |
|---|---|---|---|---|
| Trạng thái đơn | Chỉ xem | Chờ sản xuất, Đang sản xuất, Đang giao, Đã giao, Hoàn tất, Đã hủy | — | |
| Trạng thái thanh toán | Chỉ xem | Chưa thanh toán, Đã đặt cọc, Đã thanh toán, Đã hoàn tiền | — | |
| Đơn vị vận chuyển, ghi chú giao | Text; length 200 | — | Không | Chỉ với Giao tận nơi |
| Đã thu phần còn lại | Chọn một | Đã thu hoặc Chưa thu khi chuyển Đã giao; Xác nhận đã thu sau được | Có | Phần còn lại là tổng giá trị trừ cọc |
| Lịch sử trạng thái | Danh sách, chỉ xem | Trạng thái, người thực hiện, thời điểm | — | |

| Mã lỗi | Điều kiện trả lỗi |
|---|---|
| ERR-UC22-01 | Chuyển trạng thái sai thứ tự |
| ERR-UC22-02 | Chưa đặt cọc |
| ERR-UC22-03 | Đơn đã kết thúc |
| ERR-UC22-04 | Không có quyền |

| Thông báo | Người nhận |
|---|---|
| NT-UC22-01 | Buyer |
| NT-UC22-02 | Buyer |
| NT-UC22-03 | Xưởng |
| NT-UC22-04 | Buyer và xưởng |

## UC-23 — Hủy yêu cầu báo giá trước khi chấp nhận

Buyer hủy yêu cầu báo giá đã gửi mà chưa chấp nhận báo giá nào. Các báo giá của yêu cầu chuyển Đã đóng; xưởng đã báo giá được thông báo.

**Quyền và điều kiện** Đã đăng nhập, là chủ yêu cầu Yêu cầu ở Đã gửi hoặc Đang nhận báo giá

Khóa yêu cầu, kiểm tra chủ sở hữu và chưa chấp nhận. Yêu cầu đã có đơn chuyển xử lý hủy theo UC-33.

| Field | Format | Validation | Required | Ghi chú |
|---|---|---|---|---|
| Lý do hủy | Text; length 300 | — | Không | Xưởng đã báo giá thấy lý do |

| Mã lỗi | Điều kiện trả lỗi |
|---|---|
| ERR-UC23-01 | Yêu cầu đã được chấp nhận |
| ERR-UC23-02 | Yêu cầu không thể hủy |
| ERR-UC23-03 | Không có quyền |

| Thông báo | Người nhận |
|---|---|
| NT-UC23-01 | Xưởng đã báo giá |

## UC-24 — Xem nhật ký sản xuất của đơn đặt riêng

Buyer xem các lô và nhật ký sản xuất xưởng ghi cho đơn đặt riêng của mình, gồm tên lô, phân loại, trạng thái lô, các mốc và ghi chú đính chính.

**Quyền và điều kiện** Đã đăng nhập, là chủ đơn

Kiểm Buyer của đơn trước khi đọc toàn bộ lô gắn order_id. Mốc và đính chính do UC-14/15 quản lý.

| Field | Format | Validation | Required | Ghi chú |
|---|---|---|---|---|
| Danh sách lô | Chỉ xem | Lô của đơn: tên lô, phân loại, số lượng, ngày bắt đầu, trạng thái lô (Đang thực hiện, Hoàn thành, Đã xuất xưởng) | — | Một đơn có thể có nhiều lô |
| Mốc | Danh sách, chỉ xem | Ngày, công đoạn, mô tả, ảnh; ngày tăng dần | — | Nhãn "đã chỉnh sửa" nếu có |
| Ghi chú đính chính | Danh sách, chỉ xem | Mới nhất cuối, kèm ngày giờ | — | Chỉ lô Đã xuất xưởng |

| Mã lỗi | Điều kiện trả lỗi |
|---|---|
| ERR-UC24-01 | Không có quyền |
| ERR-UC24-02 | Không tải được |
