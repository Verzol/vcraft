---
title: "04 — Đặt hàng riêng — Ca nghiệm thu"
code: SRS-04
type: srs
status: official
version: 0.3.0
date: 2026-11-18
---

# 04 — Đặt hàng riêng — Ca nghiệm thu

[BRD 0.1.0](../../brd/04-quotation/usecase.md) · [ERD](../../design/erd.md) · [Phụ thuộc liên nhóm](../README.md) · [Kiến trúc và giao diện](01-architecture.md) · [Đặc tả chức năng](02-feature-description.md)

Các ca dưới đây truy vết tới AC của BRD. Chạy qua giao diện hoặc API tương ứng khi có ứng dụng.

## UC-18 — Tạo, lưu nháp, xóa nháp và gửi yêu cầu báo giá

| TC | AC và quy tắc | Chuẩn bị | Thao tác | Kết quả mong đợi |
|---|---|---|---|---|
| TC-18.1 | UC-18-AC-01 · Main Flow, BR-1, BR-9 | Buyer đã đăng nhập, xem sản phẩm "Giỏ mây đan hoa cúc" của xưởng đang hoạt động | Chọn Gửi yêu cầu báo giá, nhập mô tả, số lượng 500, đơn vị "chiếc", chọn Nhận tại xưởng, bỏ trống hạn, chọn Gửi yêu cầu | Yêu cầu Đã gửi có mã; kèm tên và ảnh sản phẩm mẫu; chỉ xưởng của sản phẩm nhận NT-UC18-01 nêu "Từ sản phẩm Giỏ mây đan hoa cúc"; hạn phản hồi là 3 ngày sau |
| TC-18.2 | UC-18-AC-02 · Alt-01, BR-2 | Guest chưa đăng nhập đang xem sản phẩm hoặc gian hàng | Chọn Gửi yêu cầu báo giá, rồi đăng nhập | Quay lại đúng sản phẩm hoặc gian hàng đang xem và mở form |
| TC-18.3 | UC-18-AC-03 · Alt-02 | Để trống mô tả, hoặc số lượng 0, hoặc Giao tận nơi thiếu địa chỉ | Chọn Gửi yêu cầu | Hiển thị ERR-UC18-01 tại trường sai; dữ liệu giữ nguyên; yêu cầu không được gửi |
| TC-18.4 | UC-18-AC-04 · Alt-03 | Đang chọn ảnh mẫu | Chọn tệp PDF, ảnh lớn hơn 10 MB hoặc ảnh thứ 6 | Hiển thị ERR-UC18-02; ảnh không được nhận |
| TC-18.5 | UC-18-AC-05 · Alt-04, BR-9 | Yêu cầu gửi chung bỏ trống hạn, hoặc yêu cầu gửi riêng có hạn là hôm nay hoặc 45 ngày sau | Chọn Gửi yêu cầu | Hiển thị ERR-UC18-03; yêu cầu không được gửi |
| TC-18.6 | UC-18-AC-06 · Alt-05, BR-2 | Buyer đã nhập một phần thông tin | Chọn Lưu nháp, đóng trang rồi mở lại từ tab Nháp | Nháp giữ nguồn và dữ liệu đã nhập; xưởng chưa nhận thông báo; chọn Gửi thì gửi được |
| TC-18.7 | UC-18-AC-07 · Alt-06 | Buyer có một nháp | Chọn Xóa và xác nhận | Nháp biến khỏi danh sách; yêu cầu đã gửi không có nút Xóa |
| TC-18.8 | UC-18-AC-08 · Alt-07, BR-3 | Xưởng của sản phẩm vừa chuyển Tạm ngừng sau khi Buyer mở form | Chọn Gửi yêu cầu | Hiển thị ERR-UC18-04 kèm lựa chọn Gửi chung theo phân loại hoặc Lưu nháp; dữ liệu đã nhập được giữ; chọn Gửi chung thì mô tả, số lượng, ảnh được giữ còn sản phẩm mẫu và xưởng nhận cũ bị bỏ; xưởng không nhận thông báo |
| TC-18.9 | UC-18-AC-09 · BR-3, BR-13 | Artisan có gian hàng, và có xưởng khác cùng phân loại | Xem sản phẩm, gian hàng của mình; rồi gửi chung | Không có nút gửi yêu cầu ở sản phẩm, gian hàng của mình; xưởng của mình không có trong danh sách xưởng nhận |
| TC-18.10 | UC-18-AC-10 · Alt-08, BR-4 | Phân loại "Đèn mây" chưa có xưởng nào có sản phẩm Hiển thị | Buyer chọn phân loại này | Hiện câu hỏi gửi tất cả xưởng đang nhận kèm cảnh báo xưởng có thể không làm sản phẩm thuộc phân loại; Đồng ý thì danh sách là tất cả xưởng đang nhận trừ xưởng của Buyer |
| TC-18.11 | UC-18-AC-11 · Alt-09 | Không có xưởng nào đủ điều kiện nhận (không xưởng hoạt động, hoặc chỉ còn xưởng của Buyer) | Buyer mở Đặt hàng theo yêu cầu hoặc chọn phân loại | Hiển thị ERR-UC18-05 ngay; nút Gửi bị vô hiệu, Lưu nháp vẫn dùng được; nếu hết xưởng đúng lúc bấm Gửi thì Backend cũng từ chối với ERR-UC18-05 và giữ nháp |
| TC-18.12 | UC-18-AC-12 · Alt-10 | Phiên hết hạn khi Buyer đang soạn | Chọn Gửi yêu cầu | Hiển thị ERR-UC04-05; đăng nhập lại xong, nội dung đang soạn còn nguyên |
| TC-18.13 | UC-18-AC-13 · Alt-11, BR-10 | Yêu cầu gửi chung hạn sau 3 ngày; xưởng A chưa phản hồi, xưởng B đã báo giá, xưởng C đã Không nhận | Còn 24 giờ trước hạn | Chỉ xưởng A nhận NT-UC18-02; B và C không nhận |
| TC-18.14 | UC-18-AC-14 · BR-10 | Một yêu cầu gửi ngày 18 có hạn ngày 19, một yêu cầu khác có hạn ngày 21 | Đến thời điểm nhắc | Yêu cầu hạn ngày 19 không nhắc; yêu cầu hạn ngày 21 nhắc một lần trước hạn 24 giờ |
| TC-18.15 | UC-18-AC-15 · Alt-12, BR-11 | Yêu cầu gửi chung có hai báo giá còn hiệu lực | Đến hạn nhận báo giá | Yêu cầu vẫn Đang nhận báo giá, có nhãn "Chờ Buyer lựa chọn"; xưởng chưa báo giá không gửi mới được (ERR-UC19-02); Buyer nhận NT-UC18-03 và vẫn chấp nhận được |
| TC-18.16 | UC-18-AC-16 · Alt-12 | Yêu cầu gửi riêng, xưởng chưa phản hồi | Đến hạn phản hồi | Yêu cầu Hết hạn, lý do "Quá hạn phản hồi"; Buyer nhận NT-UC18-04; hiện nút Gia hạn và Tạo yêu cầu mới |
| TC-18.17 | UC-18-AC-17 · Alt-12 | Yêu cầu gửi chung đến hạn mà chưa có báo giá nào | Đến hạn nhận báo giá | Yêu cầu Hết hạn, lý do "Hết hạn nhận báo giá"; Buyer nhận NT-UC18-04 |
| TC-18.18 | UC-18-AC-18 · Alt-13 | Yêu cầu đã đóng nhận báo giá, còn một báo giá hiệu lực | Báo giá đó hết hiệu lực | Yêu cầu Hết hạn, lý do "Mọi báo giá đã hết hiệu lực"; Buyer nhận NT-UC18-04 |
| TC-18.19 | UC-18-AC-19 · Alt-14, BR-7 | Yêu cầu gửi riêng Hết hạn do quá hạn phản hồi | Buyer chọn Gia hạn và nhập hạn mới hợp lệ | Yêu cầu về Đã gửi với hạn mới; xưởng nhận NT-UC18-05; gia hạn lần hai bị ERR-UC18-06 |
| TC-18.20 | UC-18-AC-20 · Alt-16 | Yêu cầu Hết hạn nhưng xưởng đã phản hồi, hoặc xưởng đang Tạm ngừng, hoặc yêu cầu đã gia hạn một lần | Buyer chọn Gia hạn | Hiển thị ERR-UC18-06; yêu cầu giữ nguyên |
| TC-18.21 | UC-18-AC-21 · Alt-15, BR-7 | Yêu cầu Hết hạn, Đã hủy hoặc gửi riêng bị xưởng Không nhận | Buyer chọn Tạo yêu cầu mới từ yêu cầu này, rồi chọn Gửi chung | Nháp mới điền sẵn nội dung; sản phẩm mẫu và xưởng nhận cũ bị bỏ; yêu cầu cũ giữ nguyên |
| TC-18.22 | UC-18-AC-22 · Alt-17, BR-13 | Người dùng khác hoặc Admin | Mở, sửa nháp của Buyer, hoặc gửi yêu cầu bằng tài khoản Admin | Hiển thị ERR-UC18-07; dữ liệu không đổi |
| TC-18.23 | UC-18-AC-23 · BR-5 | Yêu cầu gửi chung gửi cho 5 xưởng | Một xưởng mở yêu cầu | Thấy nguồn "Yêu cầu gửi chung"; không thấy số xưởng khác nhận |
| TC-18.24 | UC-18-AC-24 · BR-6 | Yêu cầu có sản phẩm mẫu; sau đó xưởng đổi tên hoặc Ẩn sản phẩm | Xưởng và Buyer mở yêu cầu | Vẫn thấy tên và ảnh sản phẩm mẫu lúc gửi |
| TC-18.25 | UC-18-AC-25 · BR-7 | Yêu cầu Đã gửi | Buyer tìm thao tác sửa nội dung | Không có thao tác sửa (chỉ Hủy, và Gia hạn trong trường hợp Alt-14) |
| TC-18.26 | UC-18-AC-26 · BR-8 | Yêu cầu Giao tận nơi chưa được chấp nhận | Xưởng nhận mở yêu cầu | Thấy tỉnh/thành và quận/huyện; không thấy họ tên, số điện thoại, địa chỉ chi tiết của Buyer |
| TC-18.27 | UC-18-AC-27 · BR-12 | Mỗi khi yêu cầu đổi trạng thái | Xem lịch sử yêu cầu (UC-21) | Mỗi lần ghi người thực hiện, thời điểm; chuyển trạng thái chỉ theo các bước của BR-12 |
| TC-18.28 | UC-18-AC-28 · Alt-12, BR-9, BR-11 | Yêu cầu gửi chung có hạn ngày 2026-11-25 | Xưởng gửi báo giá lúc 23:50 ngày 25, rồi lúc 00:05 ngày 26 | Lần đầu thành công; lần sau bị ERR-UC19-02 |
| TC-18.29 | UC-18-AC-29 · Alt-12 | Yêu cầu gửi riêng chưa có báo giá; yêu cầu gửi chung chưa có báo giá; yêu cầu có hai báo giá đều đã hết hiệu lực | Đến hết ngày hạn | Lý do Hết hạn lần lượt là "Quá hạn phản hồi", "Hết hạn nhận báo giá", "Mọi báo giá đã hết hiệu lực" |
| TC-18.30 | UC-18-AC-30 · BR-14 | Mọi thời hạn trong nhóm | Xem mô tả hạn trong form, thông báo | Buyer chỉ chọn ngày hạn; xưởng chọn hiệu lực; các hạn 3 ngày và 7 ngày do hệ thống đặt |

## UC-19 — Tiếp nhận yêu cầu, gửi báo giá hoặc không nhận

| TC | AC và quy tắc | Chuẩn bị | Thao tác | Kết quả mong đợi |
|---|---|---|---|---|
| TC-19.1 | UC-19-AC-01 · Main Flow, BR-4 | Xưởng nhận yêu cầu 500 giỏ, nhận tại xưởng | Nhập đơn giá 85.000, phí giao 0, 21 ngày, cọc 30%, hiệu lực 2026-12-20, chọn Gửi báo giá | Tổng giá trị 42.500.000 đồng, tiền cọc 12.750.000 đồng; báo giá Còn hiệu lực phiên bản 1; yêu cầu sang Đang nhận báo giá; Buyer nhận NT-UC19-01 |
| TC-19.2 | UC-19-AC-02 · Alt-01, BR-7 | Báo giá phiên bản 1 chưa chấp nhận, yêu cầu còn trong hạn | Xưởng cập nhật đơn giá và gửi | Phiên bản 2 thay phiên bản 1; Buyer nhận NT-UC19-02; lịch sử còn phiên bản 1 |
| TC-19.3 | UC-19-AC-03 · Alt-02, BR-9, BR-14 | Yêu cầu gửi chung còn trong hạn, 3 xưởng nhận | Một xưởng chọn Không nhận với lý do "Không kịp thời gian" | Phản hồi Không nhận chỉ cập nhật trong danh sách; Buyer không nhận thông báo; xưởng đó không nhận nhắc hạn và không báo giá lại được |
| TC-19.4 | UC-19-AC-04 · Alt-02, BR-14 | Yêu cầu gửi riêng còn trong hạn | Xưởng Không nhận | Yêu cầu chuyển Hết hạn ngay, lý do "Xưởng không nhận"; Buyer nhận NT-UC19-03 |
| TC-19.5 | UC-19-AC-05 · Alt-03, BR-4, BR-5 | Đơn giá 0, hoặc cọc 40%, hoặc để trống hiệu lực | Chọn Gửi báo giá | Hiển thị ERR-UC19-01 tại trường sai; báo giá chưa lưu |
| TC-19.6 | UC-19-AC-06 · Alt-03, BR-4 | Nhận tại xưởng | Nhập phí giao 50.000 | Hiển thị ERR-UC19-01 |
| TC-19.7 | UC-19-AC-07 · BR-5 | Yêu cầu gửi chung hạn 2026-11-25 | Xưởng chọn hiệu lực 2026-11-24, rồi hiệu lực hôm nay, rồi hiệu lực sau 100 ngày | Ngày 2026-11-24 gửi được kèm cảnh báo hết hiệu lực trước khi yêu cầu đóng; hôm nay và 100 ngày bị ERR-UC19-01 |
| TC-19.8 | UC-19-AC-08 · Alt-04, BR-6 | Yêu cầu đã đến hạn nhận báo giá | Xưởng chưa báo giá chọn Gửi báo giá | Hiển thị ERR-UC19-02 |
| TC-19.9 | UC-19-AC-09 · Alt-04 | Yêu cầu đã Đã chấp nhận hoặc Đã hủy | Xưởng gửi hoặc cập nhật báo giá | Hiển thị ERR-UC19-02 |
| TC-19.10 | UC-19-AC-10 · Alt-05, BR-8 | Xưởng chuyển Tạm ngừng | Gửi hoặc cập nhật báo giá; rồi chọn Không nhận | Gửi và cập nhật bị ERR-UC19-03; Không nhận thực hiện được |
| TC-19.11 | UC-19-AC-11 · BR-8 | Xưởng Tạm ngừng sau khi đã gửi báo giá | Buyer chấp nhận báo giá đó | Chấp nhận thành công (UC-20) |
| TC-19.12 | UC-19-AC-12 · Alt-06, BR-3 | Xưởng đã có báo giá | Gửi báo giá mới thay vì Cập nhật | Hiển thị ERR-UC19-04 |
| TC-19.13 | UC-19-AC-13 · Alt-07, BR-6, BR-11 | Báo giá Hết hiệu lực, Buyer đã Yêu cầu cập nhật giá | Xưởng cập nhật giá với hiệu lực mới | Phiên bản mới Còn hiệu lực; yêu cầu đang Hết hạn quay về Đang nhận báo giá; Buyer nhận NT-UC19-04 |
| TC-19.14 | UC-19-AC-14 · Alt-08, BR-11 | Yêu cầu cập nhật giá tạo cách đây 3 ngày, xưởng chưa trả lời | Đến 3 ngày | Báo giá giữ Hết hiệu lực; Buyer nhận NT-UC19-05; không còn nút Yêu cầu cập nhật giá |
| TC-19.15 | UC-19-AC-15 · Alt-09, BR-1 | Xưởng không thuộc danh sách nhận, Buyer hoặc Admin | Mở yêu cầu hoặc gửi báo giá | Hiển thị ERR-UC19-05 |
| TC-19.16 | UC-19-AC-16 · BR-10 | Ba xưởng cùng báo giá | Xưởng A mở yêu cầu | Chỉ thấy báo giá của mình, không thấy xưởng B, C |
| TC-19.17 | UC-19-AC-17 · BR-12 | Yêu cầu Giao tận nơi chưa được chấp nhận | Xưởng mở yêu cầu | Chỉ thấy địa chỉ đến quận/huyện; không thấy tên, số điện thoại, địa chỉ chi tiết |
| TC-19.18 | UC-19-AC-18 · BR-2 | Yêu cầu 300 chiếc | Xưởng thử báo giá cho 100 chiếc | Không có chỗ nhập số lượng; báo giá áp cho toàn bộ số lượng |
| TC-19.19 | UC-19-AC-19 · Alt-02, BR-14 | Yêu cầu gửi chung có 3 xưởng nhận, không báo giá nào | Cả 3 xưởng chọn Không nhận | Sau xưởng cuối, yêu cầu Hết hạn, lý do "Tất cả xưởng không nhận"; Buyer nhận NT-UC19-03 một lần |
| TC-19.20 | UC-19-AC-20 · Alt-06, BR-13 | Xưởng đã Không nhận, hoặc đã báo giá | Mở yêu cầu; hoặc gửi request cũ | Giao diện ẩn Gửi báo giá, Không nhận (xưởng đã báo giá còn Cập nhật báo giá nếu còn hạn); request cũ bị ERR-UC19-04 |
| TC-19.21 | UC-19-AC-21 · Alt-11, BR-11 | Yêu cầu cập nhật giá quá 3 ngày | Xưởng cập nhật giá | Hiển thị ERR-UC19-06; báo giá giữ Hết hiệu lực |
| TC-19.22 | UC-19-AC-22 · Alt-10, BR-11 | Yêu cầu cập nhật giá còn hạn | Xưởng chọn Từ chối cập nhật | Báo giá giữ Hết hiệu lực; yêu cầu giữ trạng thái; Buyer nhận NT-UC19-05 |

## UC-20 — So sánh và chấp nhận báo giá

| TC | AC và quy tắc | Chuẩn bị | Thao tác | Kết quả mong đợi |
|---|---|---|---|---|
| TC-20.1 | UC-20-AC-01 · Main Flow, BR-1 | Yêu cầu có 3 báo giá Còn hiệu lực | Buyer mở So sánh báo giá | Thấy đủ 3 báo giá với đơn giá, phí giao, tổng, thời gian hoàn thành, cọc, hiệu lực; sắp xếp được theo giá |
| TC-20.2 | UC-20-AC-02 · Main Flow, BR-6, BR-7 | Báo giá tổng 42.500.000 đồng, cọc 30%, nhận tại xưởng | Buyer chấp nhận và xác nhận | Hộp xác nhận nêu cọc 12.750.000 đồng và phần còn lại 29.750.000 đồng; yêu cầu Đã chấp nhận; đơn Chờ sản xuất; chuyển sang ZaloPay sandbox |
| TC-20.3 | UC-20-AC-03 · Main Flow, BR-7 | Cọc đã tạo giao dịch | Thanh toán thành công | Trạng thái thanh toán Đã đặt cọc; xưởng nhận NT-UC20-05 |
| TC-20.4 | UC-20-AC-04 · BR-1, BR-4 | Yêu cầu có 3 báo giá | Buyer chấp nhận một báo giá rồi đóng cổng thanh toán, chưa đặt cọc | Hai báo giá còn lại Đã đóng; xưởng được chọn vẫn nhận NT-UC20-02 ngay (ghi trạng thái Chưa thanh toán); hai xưởng còn lại nhận NT-UC20-03 và chỉ thấy "Đã đóng" |
| TC-20.5 | UC-20-AC-05 · Alt-05, BR-4 | Hai Buyer cùng một tài khoản chấp nhận hai báo giá cùng lúc | Gửi hai thao tác | Chỉ một thành công; thao tác còn lại hiển thị ERR-UC20-03 |
| TC-20.6 | UC-20-AC-06 · Alt-01 | Yêu cầu Đã gửi chưa có báo giá | Buyer mở So sánh báo giá | Hiện "Chưa có xưởng nào báo giá"; không có nút chấp nhận |
| TC-20.7 | UC-20-AC-07 · Alt-02, BR-10 | Yêu cầu đã đóng nhận báo giá, còn báo giá hiệu lực | Buyer chấp nhận | Chấp nhận thành công |
| TC-20.8 | UC-20-AC-08 · Alt-03, BR-11 | Báo giá hết hiệu lực lúc Buyer đang xem | Chọn Xác nhận chấp nhận | Hiển thị ERR-UC20-01; nút chấp nhận khóa; hiện Yêu cầu cập nhật giá |
| TC-20.9 | UC-20-AC-09 · Alt-04, BR-3 | Xưởng cập nhật báo giá khi Buyer đang xem phiên bản 1 | Buyer xác nhận chấp nhận | Hiển thị ERR-UC20-02; tải phiên bản 2; chấp nhận phiên bản 2 thành công |
| TC-20.10 | UC-20-AC-10 · Alt-05 | Yêu cầu đã Đã hủy | Buyer chấp nhận | Hiển thị ERR-UC20-03 |
| TC-20.11 | UC-20-AC-11 · Alt-06, BR-8 | Buyer đóng cổng ZaloPay | Quay lại | Hiển thị ERR-UC20-04; đơn giữ Chờ sản xuất, thanh toán Chưa thanh toán; có Thanh toán lại; Buyer thanh toán lại được bất cứ lúc nào |
| TC-20.12 | UC-20-AC-12 · Alt-07 | Báo giá cọc 0% | Buyer chấp nhận | Không chuyển sang cổng thanh toán; đơn tạo ngay; thanh toán Chưa thanh toán |
| TC-20.13 | UC-20-AC-13 · Alt-08, BR-11 | Báo giá Hết hiệu lực | Buyer chọn Yêu cầu cập nhật giá | Báo giá hiện "Đang chờ xưởng cập nhật giá"; xưởng nhận NT-UC20-04; yêu cầu Hết hạn giữ nguyên trạng thái, hiện nhãn "Đang chờ cập nhật giá"; xưởng gửi giá mới thì yêu cầu về Đang nhận báo giá |
| TC-20.14 | UC-20-AC-14 · Alt-12, BR-11 | Báo giá đang chờ cập nhật giá | Buyer chọn Yêu cầu cập nhật giá lần nữa, hoặc báo giá còn hiệu lực | Hiển thị ERR-UC20-06 |
| TC-20.15 | UC-20-AC-15 · Alt-09, BR-11 | Báo giá hiệu lực đến 2026-12-20 | Qua 23:59 ngày 2026-12-20 | Báo giá Hết hiệu lực; nút Chấp nhận bị khóa |
| TC-20.16 | UC-20-AC-16 · Alt-10, BR-12 | Báo giá còn hiệu lực 24 giờ nữa, Buyer chưa chốt | Đến thời điểm nhắc | Buyer nhận NT-UC20-01 một lần cho báo giá này |
| TC-20.17 | UC-20-AC-17 · BR-9 | Xưởng Tạm ngừng sau khi gửi báo giá | Buyer chấp nhận báo giá | Thành công; đơn tạo bình thường |
| TC-20.18 | UC-20-AC-18 · Alt-11, BR-2 | Người dùng khác, xưởng hoặc Admin | Chấp nhận hoặc Yêu cầu cập nhật giá | Hiển thị ERR-UC20-05; dữ liệu không đổi |
| TC-20.19 | UC-20-AC-19 · BR-5 | Yêu cầu đã chấp nhận một báo giá | Buyer tìm cách chọn thêm xưởng khác hoặc chia đơn | Không có thao tác này |
| TC-20.20 | UC-20-AC-20 · BR-6 | Đơn đã tạo; xưởng sửa báo giá hoặc sản phẩm mẫu bị đổi sau đó | Mở chi tiết đơn | Đơn vẫn hiện yêu cầu và báo giá lúc chấp nhận |

## UC-21 — Xem yêu cầu, báo giá và đơn đặt riêng

| TC | AC và quy tắc | Chuẩn bị | Thao tác | Kết quả mong đợi |
|---|---|---|---|---|
| TC-21.1 | UC-21-AC-01 · Main Flow, BR-3 | Buyer có 1 nháp, 2 yêu cầu Đang nhận báo giá, 1 Hết hạn | Mở danh sách yêu cầu | Mỗi tab hiện đúng yêu cầu; mới nhất lên đầu; yêu cầu hiện số báo giá |
| TC-21.2 | UC-21-AC-02 · Main Flow, BR-3 | Xưởng nhận 5 yêu cầu: 2 chưa phản hồi, 2 đã báo giá, 1 Không nhận | Mở Yêu cầu nhận | Tab hiển thị đúng phản hồi của xưởng |
| TC-21.3 | UC-21-AC-03 · Alt-01 | Buyer chưa có yêu cầu | Mở danh sách | Hiện "Chưa có yêu cầu nào" kèm nút Đặt hàng theo yêu cầu |
| TC-21.4 | UC-21-AC-04 · Alt-02 | Buyer có nháp | Chọn nháp | Form mở với dữ liệu đã lưu |
| TC-21.5 | UC-21-AC-05 · Alt-03, BR-1 | Buyer A | Mở yêu cầu của Buyer B; xưởng không được nhận mở yêu cầu; Admin mở yêu cầu | Hiển thị ERR-UC21-01 trong cả ba trường hợp |
| TC-21.6 | UC-21-AC-06 · Alt-04 | Lỗi tải dữ liệu | Mở danh sách | Hiển thị ERR-UC21-02 và nút Thử lại |
| TC-21.7 | UC-21-AC-07 · Alt-05, BR-2 | Yêu cầu có 3 xưởng báo giá, Buyer chấp nhận xưởng A | Xưởng B mở yêu cầu | Chỉ thấy báo giá của mình với nhãn "Đã đóng"; không thấy xưởng A hay giá |
| TC-21.8 | UC-21-AC-08 · BR-5 | Báo giá chưa được chấp nhận, Giao tận nơi | Xưởng mở yêu cầu | Chỉ thấy tỉnh/thành, quận/huyện; sau khi chấp nhận, xưởng được chọn thấy tên, số điện thoại, địa chỉ chi tiết |
| TC-21.9 | UC-21-AC-09 · Main Flow, BR-4 | Đơn Đang sản xuất, thanh toán Đã đặt cọc | Buyer và xưởng mở chi tiết đơn | Thấy yêu cầu và báo giá lúc chấp nhận, trạng thái đơn, trạng thái thanh toán riêng |
| TC-21.10 | UC-21-AC-10 · BR-6 | Sản phẩm mẫu bị Ẩn, xưởng Tạm ngừng | Mở yêu cầu và đơn | Nội dung không đổi |
| TC-21.11 | UC-21-AC-11 · Main Flow, BR-3 | Yêu cầu đã hết hạn nhận báo giá còn báo giá hiệu lực; yêu cầu Hết hạn có Yêu cầu cập nhật giá đang chờ | Buyer mở danh sách | Yêu cầu thứ nhất nằm tab Đang xử lý với nhãn "Chờ Buyer lựa chọn"; yêu cầu thứ hai nằm tab Hết hạn với nhãn "Đang chờ cập nhật giá" |
| TC-21.12 | UC-21-AC-12 · Main Flow, BR-3 | Yêu cầu đã Hết hạn, Buyer vừa Yêu cầu cập nhật giá cho báo giá của xưởng A | Xưởng A mở Yêu cầu nhận | Yêu cầu nằm ở tab Đã báo giá với nhãn "Cần cập nhật giá"; hết hạn trả lời hoặc đã trả lời thì nhãn mất |

## UC-22 — Cập nhật và theo dõi đơn đặt riêng

| TC | AC và quy tắc | Chuẩn bị | Thao tác | Kết quả mong đợi |
|---|---|---|---|---|
| TC-22.1 | UC-22-AC-01 · Main Flow, BR-3 | Đơn Chờ sản xuất, cọc đã thanh toán | Xưởng chọn Bắt đầu sản xuất | Đơn Đang sản xuất; Buyer thấy trạng thái mới trong chi tiết đơn |
| TC-22.2 | UC-22-AC-02 · Main Flow, BR-2 | Đơn Giao tận nơi Đang sản xuất | Xưởng chọn Giao hàng, nhập ghi chú | Đơn Đang giao; Buyer nhận NT-UC22-01 |
| TC-22.3 | UC-22-AC-03 · Main Flow, BR-4 | Đơn Đang giao, phần còn lại 29.750.000 đồng | Xưởng chọn Đã giao, rồi chọn Đã thu phần còn lại | Đơn Đã giao; thanh toán Đã thanh toán; Buyer nhận NT-UC22-02 |
| TC-22.4 | UC-22-AC-04 · Main Flow, BR-5 | Đơn Đã giao | Buyer chọn Xác nhận đã nhận | Đơn Hoàn tất; xưởng nhận NT-UC22-03 |
| TC-22.5 | UC-22-AC-05 · Alt-01, BR-2 | Đơn Nhận tại xưởng, Đang sản xuất | Xưởng chọn Đã giao, rồi chọn Đã thu phần còn lại | Đơn chuyển thẳng Đã giao, không qua Đang giao |
| TC-22.6 | UC-22-AC-06 · Alt-02, BR-5 | Đơn Đã giao, Buyer không xác nhận | Qua 7 ngày | Đơn tự Hoàn tất; Buyer và xưởng nhận NT-UC22-04 |
| TC-22.7 | UC-22-AC-07 · Alt-03, BR-2 | Đơn Chờ sản xuất | Xưởng chọn Đã giao | Hiển thị ERR-UC22-01; trạng thái giữ nguyên |
| TC-22.8 | UC-22-AC-08 · Alt-04, BR-3 | Đơn có cọc 12.750.000 đồng chưa thanh toán | Xưởng chọn Bắt đầu sản xuất | Hiển thị ERR-UC22-02; sau khi Buyer thanh toán thì bắt đầu được |
| TC-22.9 | UC-22-AC-09 · BR-3 | Đơn cọc 0% | Xưởng chọn Bắt đầu sản xuất | Chuyển Đang sản xuất, không cần cọc |
| TC-22.10 | UC-22-AC-10 · Alt-05, BR-4 | Xưởng đã giao nhưng chưa thu đủ phần còn lại | Chọn Đã giao, rồi chọn Chưa thu; sau đó thu xong và chọn Xác nhận đã thu | Đơn vẫn Đã giao; thanh toán giữ Đã đặt cọc; sau khi xác nhận thì thanh toán Đã thanh toán, kể cả khi đơn đã Hoàn tất |
| TC-22.11 | UC-22-AC-11 · Alt-06, BR-6 | Đơn Hoàn tất hoặc Đã hủy | Xưởng hoặc Buyer thao tác | Hiển thị ERR-UC22-03; dữ liệu không đổi |
| TC-22.12 | UC-22-AC-12 · Alt-07, BR-1 | Xưởng khác, Buyer khác hoặc Admin | Thao tác trên đơn | Hiển thị ERR-UC22-04 |
| TC-22.13 | UC-22-AC-13 · Alt-08, BR-7 | Xưởng chuyển Tạm ngừng khi đơn Đang sản xuất | Xưởng cập nhật đơn | Cập nhật được bình thường |
| TC-22.14 | UC-22-AC-14 · BR-8 | Mỗi lần đơn đổi trạng thái | Xem lịch sử đơn | Ghi người thực hiện và thời điểm |

## UC-23 — Hủy yêu cầu báo giá trước khi chấp nhận

| TC | AC và quy tắc | Chuẩn bị | Thao tác | Kết quả mong đợi |
|---|---|---|---|---|
| TC-23.1 | UC-23-AC-01 · Main Flow, BR-1 | Yêu cầu Đang nhận báo giá, có 2 báo giá | Buyer chọn Hủy yêu cầu và xác nhận | Yêu cầu Đã hủy; 2 báo giá Đã đóng; 2 xưởng nhận NT-UC23-01 |
| TC-23.2 | UC-23-AC-02 · Main Flow, BR-4 | Yêu cầu Đã gửi chưa có báo giá | Buyer hủy | Yêu cầu Đã hủy; không thông báo xưởng nào |
| TC-23.3 | UC-23-AC-03 · BR-1 | Yêu cầu đã đóng nhận báo giá, còn báo giá hiệu lực | Buyer hủy | Hủy được |
| TC-23.4 | UC-23-AC-04 · Alt-01, BR-2 | Yêu cầu Đã chấp nhận | Buyer tìm nút Hủy yêu cầu, hoặc thao tác cùng lúc với chấp nhận | Không có nút hủy; thao tác muộn hơn bị ERR-UC23-01 |
| TC-23.5 | UC-23-AC-05 · Alt-02 | Yêu cầu Hết hạn hoặc Đã hủy | Buyer hủy | Hiển thị ERR-UC23-02 |
| TC-23.6 | UC-23-AC-06 · Alt-03 | Người dùng khác hoặc Admin | Hủy yêu cầu | Hiển thị ERR-UC23-03 |
| TC-23.7 | UC-23-AC-07 · Alt-04 | Hộp xác nhận hủy | Buyer chọn Không hủy | Yêu cầu giữ nguyên |
| TC-23.8 | UC-23-AC-08 · BR-3 | Yêu cầu Đã hủy | Buyer tìm cách mở lại | Không có thao tác mở lại; có nút Tạo yêu cầu mới từ yêu cầu này |

## UC-24 — Xem nhật ký sản xuất của đơn đặt riêng

| TC | AC và quy tắc | Chuẩn bị | Thao tác | Kết quả mong đợi |
|---|---|---|---|---|
| TC-24.1 | UC-24-AC-01 · Main Flow, BR-2 | Đơn có 2 lô | Buyer mở Nhật ký sản xuất | Thấy cả 2 lô với tên, phân loại, số lượng, trạng thái |
| TC-24.2 | UC-24-AC-02 · Main Flow | Lô có 3 mốc và một ghi chú đính chính | Buyer chọn lô | Timeline từ ngày đầu; ghi chú đính chính có ngày giờ |
| TC-24.3 | UC-24-AC-03 · Alt-01 | Đơn chưa có lô | Buyer mở Nhật ký sản xuất | Hiện "Xưởng chưa cập nhật nhật ký sản xuất" |
| TC-24.4 | UC-24-AC-04 · Alt-02, BR-3 | Lô Đang thực hiện | Buyer chọn lô | Hiện nhãn trạng thái và các mốc hiện có |
| TC-24.5 | UC-24-AC-05 · Alt-03, BR-5 | Đơn Hoàn tất | Buyer mở nhật ký | Xem được, không có thao tác thêm lô |
| TC-24.6 | UC-24-AC-06 · Alt-04, BR-1 | Buyer khác, xưởng khác hoặc Admin | Mở nhật ký của đơn | Hiển thị ERR-UC24-01 |
| TC-24.7 | UC-24-AC-07 · BR-4 | Lô của đơn đặt riêng | Mở trang nguồn gốc công khai của lô | Không có tên Buyer, mã đơn, sản phẩm mẫu |
