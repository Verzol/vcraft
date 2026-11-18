---
title: "08 — Mua hàng có sẵn — Ca nghiệm thu"
code: SRS-08
type: srs
status: official
version: 0.3.0
date: 2026-11-18
---

# 08 — Mua hàng có sẵn — Ca nghiệm thu

[BRD 0.1.0](../../brd/08-order/usecase.md) · [ERD](../../design/erd.md) · [Phụ thuộc liên nhóm](../README.md) · [Kiến trúc và giao diện](01-architecture.md) · [Đặc tả chức năng](02-feature-description.md)

Các ca dưới đây truy vết tới AC của BRD. Chạy qua giao diện hoặc API tương ứng khi có ứng dụng.

## UC-30 — Quản lý giỏ hàng

| TC | AC và quy tắc | Chuẩn bị | Thao tác | Kết quả mong đợi |
|---|---|---|---|---|
| TC-30.1 | UC-30-AC-01 · Main Flow, BR-1, BR-8 | Guest xem sản phẩm "Giỏ mây đan hoa cúc" (250.000 ₫, còn 10) của xưởng Đang hoạt động | Chọn Thêm vào giỏ, rồi mở Giỏ hàng, đổi số lượng thành 2 | Hiện "Đã thêm vào giỏ"; giỏ có nhóm xưởng với thành tiền 500.000 ₫; số còn bán của sản phẩm vẫn là 10; trang không tải lại |
| TC-30.2 | UC-30-AC-02 · Main Flow, BR-4 | Giỏ có món của xưởng A và xưởng B | Mở Giỏ hàng | Món tách thành hai nhóm theo xưởng; mỗi nhóm có tạm tính riêng và nút Mua hàng riêng |
| TC-30.3 | UC-30-AC-03 · BR-4 | Đã chọn một món ở nhóm xưởng A và một món ở nhóm xưởng B | Chọn Mua hàng ở nhóm A | Chuyển sang UC-31 chỉ với món đã chọn của nhóm A; lựa chọn ở nhóm B giữ nguyên |
| TC-30.4 | UC-30-AC-04 · Alt-02, BR-7 | Giỏ đã có 3 "Giỏ mây đan hoa cúc", số còn bán 4 | Chọn Thêm vào giỏ hai lần nữa | Lần đầu giỏ có 4; lần sau hiển thị ERR-UC30-01 và số lượng giữ ở 4 |
| TC-30.5 | UC-30-AC-05 · Alt-02, BR-7 | Giỏ có món, số còn bán vừa giảm từ 5 xuống 2 (đơn của người khác) | Buyer mở Giỏ hàng | Số lượng được điều chỉnh về 2 kèm ERR-UC30-01; các món khác không đổi |
| TC-30.6 | UC-30-AC-06 · Alt-03 | Giỏ có món số lượng 2 | Nhập số lượng 0 hoặc "abc" | Hiển thị ERR-UC30-02; số lượng cũ được giữ |
| TC-30.7 | UC-30-AC-07 · Alt-04, BR-2 | Trang sản phẩm mở sẵn, sau đó xưởng Tạm ngừng | Guest chọn Thêm vào giỏ | Hiển thị ERR-UC30-03 nêu lý do xưởng tạm ngừng; giỏ không đổi |
| TC-30.8 | UC-30-AC-08 · Alt-04, BR-2, BR-5 | Sản phẩm chỉ nhận đặt riêng, hoặc sản phẩm của xưởng mình | Gọi thao tác thêm vào giỏ trực tiếp | Hiển thị ERR-UC30-03 nêu lý do tương ứng; giỏ không đổi |
| TC-30.9 | UC-30-AC-09 · Alt-05, BR-5 | Giỏ có 3 món: một hết hàng, một sản phẩm bị Ẩn, một của xưởng Tạm ngừng | Mở Giỏ hàng | Ba món nằm ở nhóm Không khả dụng kèm đúng lý do; không chọn được, không tính tạm tính; xóa được |
| TC-30.10 | UC-30-AC-10 · Alt-05 | Món đang ở Không khả dụng vì hết hàng, xưởng nhập thêm hàng | Mở lại Giỏ hàng | Món về nhóm xưởng và chọn được |
| TC-30.11 | UC-30-AC-11 · Alt-06 | Giỏ trống | Mở Giỏ hàng | Hiện "Giỏ hàng của bạn đang trống" kèm nút Xem sản phẩm; không có nút Mua hàng |
| TC-30.12 | UC-30-AC-12 · Alt-07, BR-8 | Giỏ có 2 món | Xóa một món | Món biến khỏi giỏ; số món và tạm tính cập nhật không tải lại trang; nhóm không còn món thì biến luôn |
| TC-30.13 | UC-30-AC-13 · Alt-08 | API giỏ lỗi | Buyer mở Giỏ hàng | Hiển thị ERR-UC30-04 kèm nút Thử lại; giỏ đang hiển thị được giữ |
| TC-30.14 | UC-30-AC-14 · Alt-09 | Phiên hết hạn khi Buyer đang đổi số lượng | Lưu thay đổi | Hiển thị ERR-UC04-05; đăng nhập xong thì thao tác được thực hiện tiếp |
| TC-30.15 | UC-30-AC-15 · Alt-10, BR-9 | Tài khoản Admin | Mở Giỏ hàng hoặc thêm sản phẩm | Hiển thị ERR-UC30-05; không có giỏ |
| TC-30.16 | UC-30-AC-16 · Alt-01, BR-3 | Guest có 2 món trong giỏ trình duyệt (trong đó 1 món đã có trong giỏ tài khoản với số lượng 1) | Đăng nhập | Giỏ tài khoản chứa cả hai món; món trùng được cộng số lượng nhưng không vượt số còn bán; giỏ trên trình duyệt được xóa |
| TC-30.17 | UC-30-AC-17 · Alt-01, BR-5 | Guest có món của xưởng X trong giỏ | Đăng nhập bằng tài khoản Artisan của xưởng X | Món của xưởng X nằm ở nhóm Không khả dụng với lý do "Sản phẩm của xưởng bạn"; không bị mất |
| TC-30.18 | UC-30-AC-18 · BR-3, BR-10 | Buyer đã có giỏ, rồi đăng xuất | Đăng nhập lại, kể cả từ thiết bị khác | Giỏ còn nguyên |
| TC-30.19 | UC-30-AC-19 · BR-6 | Giá một sản phẩm trong giỏ vừa đổi từ 250.000 ₫ thành 270.000 ₫ | Mở Giỏ hàng | Giỏ hiện giá hiện tại 270.000 ₫ và thành tiền theo giá mới |
| TC-30.20 | UC-30-AC-20 · Data Fields | Giỏ có 105 dòng món | Xem biểu tượng giỏ | Hiện "99+" |

## UC-31 — Checkout: giao hàng, thanh toán, tạo đơn

| TC | AC và quy tắc | Chuẩn bị | Thao tác | Kết quả mong đợi |
|---|---|---|---|---|
| TC-31.1 | UC-31-AC-01 · Main Flow, BR-3, BR-7, BR-11 | Buyer chọn món "Giỏ mây đan hoa cúc" x2 (250.000 ₫) của xưởng Đang hoạt động, chọn Xưởng tự giao phí 30.000 ₫ và COD | Điền họ tên, số điện thoại, địa chỉ rồi chọn Đặt hàng | Tạo đơn MH-2026-0001 Chờ xác nhận, tổng 530.000 ₫, số còn bán giảm 2; hiện "Đặt hàng thành công"; xưởng nhận NT-UC31-01 ghi "thu tiền khi giao"; món đã đặt biến khỏi giỏ |
| TC-31.2 | UC-31-AC-02 · Main Flow, BR-5 | Trang Thanh toán mở | Đổi phương thức giao từ Xưởng tự giao sang Nhận tại xưởng | Phí giao về 0 ₫, tổng cập nhật không tải lại trang, ô địa chỉ nhận ẩn, hiện địa chỉ xưởng và nút Chỉ đường |
| TC-31.3 | UC-31-AC-03 · BR-5 | Xưởng chỉ bật Nhận tại xưởng | Buyer mở trang Thanh toán | Chỉ hiển thị phương thức Nhận tại xưởng |
| TC-31.4 | UC-31-AC-04 · Main Flow, BR-12 | Hồ sơ Buyer đã có họ tên và số điện thoại | Mở trang Thanh toán | Hai trường được điền sẵn, sửa được; sửa không đổi hồ sơ |
| TC-31.5 | UC-31-AC-05 · Main Flow, Alt-02, BR-3, BR-7 | Buyer chọn ZaloPay | Chọn Đặt hàng | Tạo đơn Chờ thanh toán với hạn 15 phút; hiển thị "Đơn hàng đã được tạo. Vui lòng hoàn tất thanh toán trong 15 phút" (không hiện "Đặt hàng thành công") rồi chuyển sang cổng; số còn bán đã giảm |
| TC-31.6 | UC-31-AC-06 · Alt-02, BR-7 | Đơn Chờ thanh toán | Thanh toán thành công tại cổng trong hạn | Đơn Chờ xác nhận, thanh toán Đã thanh toán; Buyer nhận NT-UC31-02, xưởng nhận NT-UC31-01 ghi "đã thanh toán online"; Buyer về trang kết quả và lúc này mới thấy "Đặt hàng thành công" |
| TC-31.7 | UC-31-AC-07 · Alt-03, BR-8 | Đơn Chờ thanh toán | Buyer chọn hủy tại cổng hoặc cổng báo thanh toán thất bại | Hiển thị ERR-UC31-05; đơn vẫn Chờ thanh toán với hạn cũ; có Thanh toán lại và Hủy đơn |
| TC-31.8 | UC-31-AC-08 · Alt-04, BR-8 | Đơn Chờ thanh toán, đã 15 phút chưa thanh toán | Hết hạn | Đơn Đã hủy, số còn bán được hoàn lại, Buyer nhận NT-UC31-03; có nút Mua lại |
| TC-31.9 | UC-31-AC-09 · Alt-04, BR-8 | Đơn đã tự hủy do quá hạn, cổng báo thanh toán thành công muộn | Hệ thống nhận kết quả | Đơn vẫn Đã hủy; khoản tiền được hoàn theo UC-33, Alt-09 |
| TC-31.10 | UC-31-AC-10 · Alt-05, BR-3 | Còn 1 sản phẩm, hai Buyer cùng bấm Đặt hàng | Hai yêu cầu đến gần nhau | Một đơn được tạo; Buyer còn lại nhận ERR-UC31-01 và không có đơn |
| TC-31.11 | UC-31-AC-11 · Alt-05, BR-3 | Đơn gồm hai món, một món chỉ còn 1 trong khi Buyer đặt 2 | Chọn Đặt hàng | Không tạo đơn, số còn bán của món còn lại không đổi; hiển thị ERR-UC31-01 liệt kê món vướng |
| TC-31.12 | UC-31-AC-12 · Alt-06 | Thiếu địa chỉ nhận khi giao tận nơi, hoặc số điện thoại sai | Chọn Đặt hàng | Hiển thị ERR-UC31-02 tại trường sai; không gửi yêu cầu |
| TC-31.13 | UC-31-AC-13 · Alt-07, BR-5 | Xưởng vừa tắt phương thức Buyer đã chọn | Chọn Đặt hàng | Hiển thị ERR-UC31-03 kèm danh sách và phí hiện tại; không tạo đơn |
| TC-31.14 | UC-31-AC-14 · Alt-08, BR-4 | Giá một món vừa đổi từ 250.000 ₫ thành 270.000 ₫ khi Buyer đang ở trang Thanh toán | Chọn Đặt hàng | Hiển thị ERR-UC31-04 với giá mới; không tạo đơn; đặt lần sau thì đơn chốt giá 270.000 ₫ |
| TC-31.15 | UC-31-AC-15 · BR-4 | Đơn đã tạo với giá 250.000 ₫, sau đó xưởng đổi giá hoặc xóa sản phẩm | Buyer xem đơn | Đơn vẫn hiển thị giá, tên, ảnh lúc đặt |
| TC-31.16 | UC-31-AC-16 · Alt-09 | Cổng ZaloPay lỗi, VNPay không cấu hình | Chọn Đặt hàng với thanh toán online | Hiển thị ERR-UC31-06; đơn Chờ thanh toán kèm nút Thanh toán lại và Hủy đơn; hạn vẫn chạy |
| TC-31.17 | UC-31-AC-17 · Alt-10, BR-5 | Buyer chọn GHN (Should) nhưng nhà cung cấp lỗi; trường hợp 1: xưởng có phí cố định, trường hợp 2: không có | Chọn GHN | Trường hợp 1: hiển thị ERR-UC31-07, tổng tính theo phí cố định. Trường hợp 2: hiển thị ERR-UC31-07 yêu cầu chọn phương thức khác |
| TC-31.18 | UC-31-AC-18 · Alt-11, BR-10 | Buyer bấm Đặt hàng hai lần | Hai yêu cầu liên tiếp | Chỉ có một đơn; hàng chỉ bị giữ một lần |
| TC-31.19 | UC-31-AC-19 · Alt-12 | Phiên hết hạn trên trang Thanh toán | Chọn Đặt hàng | Hiển thị ERR-UC04-05; đăng nhập xong quay lại trang Thanh toán với dữ liệu đã nhập; chưa có đơn |
| TC-31.20 | UC-31-AC-20 · Alt-01 | Guest có món trong giỏ | Chọn Mua hàng, rồi đăng nhập | Về giỏ đã gộp với các món đã chọn, rồi vào trang Thanh toán |
| TC-31.21 | UC-31-AC-21 · Alt-13, BR-2 | Tài khoản Admin; hoặc món của xưởng mình; hoặc xưởng Tạm ngừng | Thanh toán | Bị từ chối bằng lỗi tương ứng; không tạo đơn |
| TC-31.22 | UC-31-AC-22 · BR-1, BR-11 | Giỏ có món của hai xưởng | Chọn Mua hàng ở một nhóm | Chỉ món của nhóm đó vào thanh toán và tạo đơn; món nhóm kia còn trong giỏ |
| TC-31.23 | UC-31-AC-23 · BR-12 | Đơn vừa tạo | Xem nội dung NT-UC31-01 | Chỉ có mã đơn và ghi chú thanh toán; không có họ tên, số điện thoại, địa chỉ |
| TC-31.24 | UC-31-AC-24 · BR-9 | Hai đơn trong cùng năm | Tạo lần lượt | Mã khác nhau và tăng dần theo dạng MH-2026-xxxx |
| TC-31.25 | UC-31-AC-25 · BR-6, BR-13, BR-14 | Trang Thanh toán mở | Xem phương thức thanh toán và tổng thanh toán | Chỉ có COD và ZaloPay, mặc định COD; thanh toán online chạy trên môi trường sandbox; tổng chỉ gồm tiền hàng và phí giao, không có phí sàn |
| TC-31.26 | UC-31-AC-26 · Alt-14, BR-7 | Đơn Chờ thanh toán, Buyer đang ở trang cổng | Buyer đóng tab cổng, cổng chưa báo kết quả | Đơn vẫn Chờ thanh toán, hạn không đổi, không bị đánh dấu thất bại; Buyer quay lại đơn thấy ERR-UC31-08 kèm Kiểm tra lại kết quả, Thanh toán lại, Hủy đơn |
| TC-31.27 | UC-31-AC-27 · Alt-14, Alt-02, BR-7 | Như UC-31-AC-26, sau đó cổng báo thanh toán thành công trong hạn | Hệ thống nhận kết quả hoặc Buyer chọn Kiểm tra lại kết quả | Đơn Chờ xác nhận, thanh toán Đã thanh toán; Buyer nhận NT-UC31-02, xưởng nhận NT-UC31-01 |
| TC-31.28 | UC-31-AC-28 · Alt-03, Alt-14, BR-7 | Như UC-31-AC-26, giao dịch cũ thực tế đã thành công | Buyer chọn Thanh toán lại | Hệ thống tra giao dịch cũ, ghi nhận thành công, không tạo giao dịch mới; Buyer không bị trừ tiền hai lần |

## UC-32 — Quản lý đơn mua hàng

| TC | AC và quy tắc | Chuẩn bị | Thao tác | Kết quả mong đợi |
|---|---|---|---|---|
| TC-32.1 | UC-32-AC-01 · Main Flow, BR-9 | Xưởng có đơn COD Chờ xác nhận, giao bằng Xưởng tự giao | Artisan mở đơn, chọn Xác nhận đơn | Đơn Đang chuẩn bị, có dòng lịch sử; Buyer nhận NT-UC32-01 |
| TC-32.2 | UC-32-AC-02 · Main Flow, BR-1 | Đơn Đang chuẩn bị, Xưởng tự giao | Artisan chọn Bắt đầu giao | Đơn Đang giao; Buyer nhận NT-UC32-03 |
| TC-32.3 | UC-32-AC-03 · Main Flow, BR-6 | Đơn COD Đang giao | Artisan chọn Đã giao, đã tích xác nhận đã thu đủ tiền | Đơn Đã giao, trạng thái thanh toán Đã thanh toán, hạn 7 ngày bắt đầu; Buyer nhận NT-UC32-04 |
| TC-32.4 | UC-32-AC-04 · Alt-10, BR-6 | Đơn COD Đang giao | Artisan chọn Đã giao nhưng chưa tích xác nhận đã thu | Hiển thị ERR-UC32-04; đơn vẫn Đang giao, thanh toán vẫn Chưa thanh toán |
| TC-32.5 | UC-32-AC-05 · BR-6 | Đơn online đã thanh toán, Đang giao | Artisan chọn Đã giao | Đơn Đã giao; không yêu cầu xác nhận thu tiền; trạng thái thanh toán vẫn Đã thanh toán |
| TC-32.6 | UC-32-AC-06 · Main Flow, BR-7 | Đơn Đã giao | Buyer chọn Đã nhận hàng | Đơn Hoàn tất; xưởng nhận NT-UC32-08 |
| TC-32.7 | UC-32-AC-07 · Alt-04, BR-7 | Đơn Đã giao, Buyer không xác nhận | Qua 7 ngày kể từ Đã giao | Đơn tự Hoàn tất; Buyer nhận NT-UC32-05, xưởng nhận NT-UC32-08 |
| TC-32.8 | UC-32-AC-08 · Alt-01, BR-3 | Đơn Chờ xác nhận | Artisan chọn Từ chối và nhập lý do 20 ký tự | Đơn Bị từ chối kèm lý do; số còn bán tăng lại; Buyer nhận NT-UC32-02 kèm lý do |
| TC-32.9 | UC-32-AC-09 · Alt-01, BR-3 | Đơn Chờ xác nhận đã thanh toán online | Xưởng từ chối | Đơn Bị từ chối và khoản thanh toán được hoàn theo UC-33, Alt-08 |
| TC-32.10 | UC-32-AC-10 · Alt-08, BR-3 | Hộp lý do từ chối bỏ trống | Xác nhận từ chối | Hiển thị ERR-UC32-02; đơn không đổi |
| TC-32.11 | UC-32-AC-11 · Alt-03, BR-4 | Đơn COD Chờ xác nhận, xưởng chưa phản hồi | Qua 3 ngày kể từ lúc tạo đơn | Đơn Đã hủy lý do "Xưởng không xác nhận trong 3 ngày"; số còn bán được hoàn; Buyer nhận NT-UC32-06, xưởng nhận NT-UC32-07 |
| TC-32.12 | UC-32-AC-12 · Alt-03, BR-4 | Đơn online Chờ xác nhận, đã thanh toán, xưởng chưa phản hồi | Qua 3 ngày kể từ lúc thanh toán | Đơn Đã hủy và khoản thanh toán được hoàn theo UC-33, Alt-08 |
| TC-32.13 | UC-32-AC-13 · Alt-02, BR-5 | Đơn Nhận tại xưởng, Đang chuẩn bị | Buyer đến lấy; Artisan chọn Đã bàn giao tại xưởng | Đơn Đã giao, không qua Đang giao; Buyer nhận NT-UC32-04 |
| TC-32.14 | UC-32-AC-14 · Alt-09, BR-5 | Đơn Nhận tại xưởng | Gọi thao tác Bắt đầu giao | Hiển thị ERR-UC32-03; đơn giữ nguyên |
| TC-32.15 | UC-32-AC-15 · Alt-07 | Buyer vừa hủy đơn Chờ xác nhận ở một tab; xưởng mở trang cũ ở tab khác | Artisan chọn Xác nhận đơn | Hiển thị ERR-UC32-01; trang tải lại với trạng thái Đã hủy |
| TC-32.16 | UC-32-AC-16 · Alt-09, BR-1 | Đơn Chờ xác nhận | Gọi thao tác Đã nhận hàng | Hiển thị ERR-UC32-03; đơn giữ nguyên |
| TC-32.17 | UC-32-AC-17 · Alt-11, BR-10 | Đơn của xưởng A | Artisan xưởng B mở đơn; hoặc Buyer khác mở đơn; hoặc Admin mở danh sách đơn | Hiển thị ERR-UC32-05; không lộ đơn có tồn tại |
| TC-32.18 | UC-32-AC-18 · Main Flow, BR-14 | Xưởng có 30 đơn ở nhiều trạng thái | Lọc Đang chuẩn bị rồi tìm "MH-2026-0007" | Danh sách thu hẹp không tải lại trang; đơn Chờ xác nhận hiển thị hạn còn lại |
| TC-32.19 | UC-32-AC-19 · Alt-12 | Xưởng chưa có đơn; Buyer chưa có đơn | Mở danh sách đơn | Hiện "Chưa có đơn hàng nào"; Buyer có nút Xem sản phẩm |
| TC-32.20 | UC-32-AC-20 · Alt-13 | API đơn lỗi | Mở danh sách hoặc chi tiết đơn | Hiển thị ERR-UC32-06 kèm nút Thử lại |
| TC-32.21 | UC-32-AC-21 · Alt-14 | Phiên hết hạn khi xưởng đang xác nhận đơn | Chọn Xác nhận đơn | Hiển thị ERR-UC04-05; đăng nhập xong quay lại đơn đó, trạng thái chưa đổi |
| TC-32.22 | UC-32-AC-22 · BR-11 | Xưởng đang Tạm ngừng, có đơn Đang chuẩn bị | Artisan chọn Bắt đầu giao | Thao tác thành công; đơn Đang giao |
| TC-32.23 | UC-32-AC-23 · Alt-05 | Đơn Chờ thanh toán; đơn Đã hủy | Buyer mở đơn | Chờ thanh toán có Thanh toán lại và Hủy đơn; Đã hủy có Mua lại, món còn mua được vào giỏ |
| TC-32.24 | UC-32-AC-24 · BR-12 | Đơn Đang giao | Gọi thao tác hủy từ Buyer hoặc xưởng | Bị từ chối theo UC-33; đơn giữ nguyên |
| TC-32.25 | UC-32-AC-25 · BR-9 | Đơn có người nhận tên "Nguyễn Văn A", số 0912345678 | Xem nội dung NT-UC32-01 đến NT-UC32-08 | Chỉ có mã đơn, lý do (NT-UC32-02) và nội dung nêu ở bảng; không có tên, số điện thoại, địa chỉ |
| TC-32.26 | UC-32-AC-26 · BR-13 | Sản phẩm trong đơn vừa bị Ẩn và đổi giá | Buyer xem đơn | Đơn hiển thị tên, ảnh, giá lúc đặt; liên kết sản phẩm không hiển thị vì sản phẩm không còn công khai |
| TC-32.27 | UC-32-AC-27 · Alt-06 | Đơn Chờ xác nhận; đơn Đang chuẩn bị | Buyer chọn Hủy đơn ở đơn thứ nhất; Artisan chọn Hủy đơn ở đơn thứ hai | Hệ thống chuyển sang UC-33 (Buyer hủy đơn Chờ xác nhận; xưởng hủy đơn Đang chuẩn bị) |
| TC-32.28 | UC-32-AC-28 · BR-2 | Đơn Chờ xác nhận của xưởng A | Buyer gọi thao tác Xác nhận đơn hoặc Bắt đầu giao | Hiển thị ERR-UC32-05; đơn giữ nguyên; Buyer chỉ có Đã nhận hàng và hủy theo UC-33 |
| TC-32.29 | UC-32-AC-29 · BR-8 | Đơn online Chờ xác nhận, đã thanh toán, xưởng vừa từ chối | Cổng chưa xác nhận hoàn tiền, sau đó xác nhận | Đơn là Bị từ chối ngay khi từ chối, trạng thái thanh toán vẫn Đã thanh toán cho đến khi cổng xác nhận rồi mới chuyển Đã hoàn tiền |
| TC-32.30 | UC-32-AC-30 · BR-15 | Đơn giao bằng GHN (Should), Đang chuẩn bị | Artisan chọn Bắt đầu giao rồi Đã giao | Đơn đổi trạng thái theo thao tác của xưởng như Xưởng tự giao; không có cập nhật tự động từ GHN |

## UC-33 — Hủy đơn hàng (đơn mua và đơn đặt riêng)

| TC | AC và quy tắc | Chuẩn bị | Thao tác | Kết quả mong đợi |
|---|---|---|---|---|
| TC-33.1 | UC-33-AC-01 · Main Flow, BR-1, BR-3 | Đơn COD Chờ xác nhận, mua 2 "Giỏ mây đan hoa cúc" | Buyer chọn Hủy đơn rồi Xác nhận hủy | Đơn Đã hủy; số còn bán tăng 2; xưởng nhận NT-UC33-01; không có tiền để hoàn |
| TC-33.2 | UC-33-AC-02 · Main Flow, Alt-08, BR-4 | Đơn online đã thanh toán, Chờ xác nhận | Buyer hủy | Đơn Đã hủy; khoản tiền được hoàn qua cổng, thanh toán Đã hoàn tiền; Buyer nhận NT-UC33-08; xưởng nhận NT-UC33-01 và NT-UC33-09 |
| TC-33.3 | UC-33-AC-03 · Main Flow | Hộp xác nhận hủy mở | Buyer xem hộp | Hộp nêu mã đơn, tổng thanh toán, việc trả hàng về cho người khác mua và việc hoàn tiền nếu đã thanh toán online; lý do không bắt buộc |
| TC-33.4 | UC-33-AC-04 · Alt-13, BR-1 | Đơn Đang chuẩn bị | Buyer gọi thao tác hủy | Hiển thị ERR-UC33-01; đơn giữ nguyên; giao diện không có nút Hủy đơn cho Buyer |
| TC-33.5 | UC-33-AC-05 · Alt-01, BR-1, BR-3, BR-7 | Đơn Đang chuẩn bị mua 2 "Giỏ mây đan hoa cúc", đã thanh toán online | Xưởng hủy kèm lý do 20 ký tự, giữ số lượng trả về mặc định | Đơn Đã hủy kèm lý do; số còn bán tăng đủ 2; hoàn tiền; Buyer nhận NT-UC33-02 và NT-UC33-08 |
| TC-33.6 | UC-33-AC-06 · Alt-01, BR-4 | Đơn COD Đang chuẩn bị | Xưởng hủy kèm lý do | Đơn Đã hủy; không có hoàn tiền; NT-UC33-02 không có câu hoàn tiền |
| TC-33.7 | UC-33-AC-07 · Alt-15, BR-7 | Xưởng hủy đơn Đang chuẩn bị | Bỏ trống lý do | Hiển thị ERR-UC33-03; đơn giữ nguyên |
| TC-33.8 | UC-33-AC-08 · Alt-13, BR-1 | Đơn Đang giao hoặc Đã giao | Buyer hoặc xưởng gọi thao tác hủy | Hiển thị ERR-UC33-01; đơn giữ nguyên |
| TC-33.9 | UC-33-AC-09 · Alt-02, BR-1 | Đơn online Chờ thanh toán, chưa quá 15 phút | Buyer hủy đơn | Đơn Đã hủy, số còn bán tăng lại, không có tiền để hoàn; xưởng không nhận thông báo |
| TC-33.10 | UC-33-AC-10 · Alt-09, BR-10 | Đơn Chờ thanh toán đã hủy, cổng báo thanh toán thành công muộn | Hệ thống nhận kết quả | Đơn vẫn Đã hủy; khoản tiền được hoàn đủ; Buyer nhận NT-UC33-08; xưởng không nhận thông báo |
| TC-33.11 | UC-33-AC-11 · Alt-03, BR-2, BR-8 | Đơn đặt riêng Chờ sản xuất đã đặt cọc 30% | Buyer hủy | Đơn Đã hủy; hoàn đủ cọc; xưởng nhận NT-UC33-07 và NT-UC33-09; Buyer có nút Tạo yêu cầu mới |
| TC-33.12 | UC-33-AC-12 · Alt-03, BR-7 | Đơn đặt riêng Chờ sản xuất, cọc 0% | Xưởng hủy kèm lý do | Đơn Đã hủy; không có tiền để hoàn; Buyer nhận NT-UC33-07 kèm lý do |
| TC-33.13 | UC-33-AC-13 · Alt-04, BR-6 | Đơn đặt riêng Đang sản xuất | Buyer gửi yêu cầu hủy kèm lý do | Yêu cầu Đang chờ xưởng; đơn vẫn Đang sản xuất; xưởng nhận NT-UC33-03; Buyer thấy thẻ chờ xưởng phản hồi |
| TC-33.14 | UC-33-AC-14 · Alt-16, BR-6 | Đơn đặt riêng Đang sản xuất đã có yêu cầu hủy đang chờ | Buyer gửi thêm yêu cầu | Hiển thị ERR-UC33-04; không tạo yêu cầu thứ hai |
| TC-33.15 | UC-33-AC-15 · Alt-05, BR-5, BR-9 | Yêu cầu hủy Đang chờ xưởng, đơn có cọc | Xưởng đồng ý và chọn Giữ cọc | Đơn Đã hủy; yêu cầu Xưởng đồng ý; cọc không hoàn, thanh toán vẫn Đã đặt cọc; Buyer nhận NT-UC33-05 nêu xưởng giữ cọc |
| TC-33.16 | UC-33-AC-16 · Alt-05, BR-5 | Yêu cầu hủy Đang chờ xưởng, đơn có cọc | Xưởng đồng ý và chọn Hoàn cọc | Đơn Đã hủy; cọc được hoàn, thanh toán Đã hoàn tiền; Buyer nhận NT-UC33-05 và NT-UC33-08 |
| TC-33.17 | UC-33-AC-17 · Alt-06, BR-7 | Yêu cầu hủy Đang chờ xưởng | Xưởng từ chối kèm lý do | Yêu cầu Xưởng từ chối; đơn tiếp tục Đang sản xuất; Buyer nhận NT-UC33-06 kèm lý do |
| TC-33.18 | UC-33-AC-18 · Alt-07, BR-5 | Đơn Đang sản xuất, chưa có yêu cầu hủy | Xưởng chủ động hủy kèm lý do | Đơn Đã hủy; hoàn đủ cọc, không có lựa chọn giữ cọc; Buyer nhận NT-UC33-07 |
| TC-33.19 | UC-33-AC-19 · Alt-11, BR-6 | Yêu cầu hủy Đang chờ xưởng | Buyer chọn Rút yêu cầu hủy | Yêu cầu Đã rút; đơn vẫn Đang sản xuất; xưởng nhận NT-UC33-04 |
| TC-33.20 | UC-33-AC-20 · Alt-12, BR-6 | Yêu cầu hủy Đang chờ xưởng | Xưởng chuyển đơn sang Đang giao | Yêu cầu Tự đóng; không có thông báo riêng cho yêu cầu |
| TC-33.21 | UC-33-AC-21 · Alt-07, Alt-12 | Yêu cầu hủy Đang chờ xưởng | Xưởng chủ động hủy đơn | Đơn Đã hủy; yêu cầu Tự đóng |
| TC-33.22 | UC-33-AC-22 · Alt-14, BR-12 | Buyer đang mở hộp hủy đơn Chờ xác nhận; xưởng vừa xác nhận đơn | Buyer chọn Xác nhận hủy | Hiển thị ERR-UC33-02; đơn là Đang chuẩn bị, không bị hủy |
| TC-33.23 | UC-33-AC-23 · Alt-10, BR-11 | Cổng từ chối yêu cầu hoàn tiền | Xưởng hủy đơn online đã thanh toán | Đơn vẫn Đã hủy; thanh toán giữ nguyên; Buyer thấy ERR-UC33-06; hệ thống thử lại và khi thành công thì Đã hoàn tiền |
| TC-33.24 | UC-33-AC-24 · Alt-17, BR-12 | Buyer khác, xưởng khác hoặc Admin | Hủy đơn không thuộc mình | Hiển thị ERR-UC33-05; không lộ đơn có tồn tại |
| TC-33.25 | UC-33-AC-25 · Alt-18 | Phiên hết hạn trên hộp xác nhận hủy | Chọn Xác nhận hủy | Hiển thị ERR-UC04-05; đăng nhập xong quay lại đơn, đơn chưa đổi |
| TC-33.26 | UC-33-AC-26 · BR-13 | Xưởng Tạm ngừng, có đơn Đang chuẩn bị và yêu cầu hủy | Xưởng hủy đơn hoặc phản hồi yêu cầu | Thao tác thành công |
| TC-33.27 | UC-33-AC-27 · Alt-03, BR-8 | Đơn đặt riêng bị hủy, đã có lô sản xuất | Xem lô | Lô giữ nguyên |
| TC-33.28 | UC-33-AC-28 · BR-14 | Đơn có người nhận tên "Nguyễn Văn A", số 0912345678 | Xem nội dung NT-UC33-01 đến NT-UC33-09 | Không có tên, số điện thoại, địa chỉ nhận |
| TC-33.29 | UC-33-AC-29 · BR-15 | Đơn đặt riêng có yêu cầu hủy được xưởng đồng ý và hoàn cọc | Xem lịch sử đơn | Có các dòng: yêu cầu hủy, xưởng đồng ý, đơn Đã hủy, hoàn tiền; mỗi dòng có thời điểm, người hoặc hệ thống thực hiện, lý do nếu có |
| TC-33.30 | UC-33-AC-30 · BR-16 | Xưởng chọn Giữ cọc, hoặc hoàn tiền xong | Kiểm tra dữ liệu thanh toán của đơn | Chỉ có trạng thái thanh toán (giữ Đã đặt cọc hoặc chuyển Đã hoàn tiền); không có bút toán chuyển tiền cho xưởng |
| TC-33.31 | UC-33-AC-31 · Alt-01, BR-3, BR-15 | Đơn Đang chuẩn bị mua 3 "Giỏ mây đan hoa cúc", 1 chiếc hư hỏng khi đóng gói | Xưởng hủy kèm lý do, nhập số lượng trả về 2 | Đơn Đã hủy; số còn bán chỉ tăng 2; 1 chiếc hư hỏng không được cộng và lịch sử đơn ghi số hư hỏng; Buyer được hoàn đủ theo đơn |
| TC-33.32 | UC-33-AC-32 · Alt-19, BR-3 | Xưởng hủy đơn Đang chuẩn bị mua 3 món | Nhập số lượng trả về 5, hoặc -1, hoặc "abc" | Hiển thị ERR-UC33-07 tại món sai; đơn giữ nguyên |
| TC-33.33 | UC-33-AC-33 · BR-3 | Đơn Chờ xác nhận mua 3 món; đơn khác bị xưởng từ chối; đơn khác tự hủy do quá 3 ngày | Buyer hủy, xưởng từ chối, hệ thống tự hủy | Cả ba trường hợp số còn bán tăng đủ 3; không có ô số lượng hư hỏng |
