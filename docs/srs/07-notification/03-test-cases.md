---
title: "07 — Trung tâm thông báo — Ca nghiệm thu"
code: SRS-07
type: srs
status: official
version: 0.4.0
date: 2026-11-18
---

# 07 — Trung tâm thông báo — Ca nghiệm thu

[BRD 0.2.0](../../brd/07-notification/usecase.md) · [ERD](../../design/erd.md) · [Phụ thuộc liên nhóm](../README.md) · [Kiến trúc và giao diện](01-architecture.md) · [Đặc tả chức năng](02-feature-description.md)

Các ca dưới đây truy vết tới AC của BRD. Chạy qua giao diện hoặc API tương ứng khi có ứng dụng.

## UC-29 — Chuông thông báo, số chưa đọc, mở đúng đối tượng

| TC | AC và quy tắc | Chuẩn bị | Thao tác | Kết quả mong đợi |
|---|---|---|---|---|
| TC-29.1 | UC-29-AC-01 · Main Flow | Buyer đã đăng nhập, có 3 thông báo Chưa đọc, trong đó NT-UC19-01 của yêu cầu YC-2026-0001 | Mở một trang bất kỳ, bấm chuông, chọn thông báo NT-UC19-01 | Chuông hiện số 3; bảng xếp mới nhất trước; sau khi chọn, thông báo Đã đọc, số còn 2 và mở So sánh báo giá của đúng yêu cầu YC-2026-0001 |
| TC-29.2 | UC-29-AC-02 · Alt-01, BR-6 | Tài khoản có 25 thông báo, trong đó 8 Chưa đọc | Chọn Xem tất cả, chuyển sang tab Chưa đọc, rồi quay lại tab Tất cả và chọn Xem thêm | Trang Thông báo hiện 20 thông báo đầu; tab Chưa đọc chỉ có 8 thông báo chưa đọc; Xem thêm tải 5 thông báo còn lại; trang không tải lại |
| TC-29.3 | UC-29-AC-03 · Alt-02 | Tài khoản có 40 thông báo Chưa đọc, màn hình mới tải 20 | Chọn Đánh dấu tất cả đã đọc | Cả 40 thông báo Đã đọc kể cả 20 thông báo chưa tải; số chuông về 0 và ẩn; không chuyển trang; thông báo đến sau đó vẫn Chưa đọc |
| TC-29.4 | UC-29-AC-04 · Alt-03 | Tài khoản chưa có thông báo nào; tài khoản khác đã đọc hết | Mở bảng; mở tab Chưa đọc | Hiện "Bạn chưa có thông báo nào"; hiện "Bạn đã đọc hết thông báo"; nút Đánh dấu tất cả đã đọc bị vô hiệu |
| TC-29.5 | UC-29-AC-05 · Alt-04, BR-9, BR-10 | Artisan có NT-UC27-01 chưa đọc; Admin sau đó Ẩn tin | Artisan bấm thông báo | Thông báo Đã đọc; hiển thị ERR-UC28-01; thông báo vẫn còn trong danh sách với nội dung cũ |
| TC-29.6 | UC-29-AC-06 · Alt-04, BR-10 | Xưởng có NT-UC18-01 cũ của yêu cầu đã bị Buyer hủy | Xưởng bấm thông báo | Mở chi tiết yêu cầu nhận ở trạng thái Đã hủy, chỉ xem; không hiện lỗi; thông báo Đã đọc |
| TC-29.7 | UC-29-AC-07 · Alt-05 | Việc đánh dấu Đã đọc của một thông báo bị lỗi | Người dùng bấm thông báo đó | Vẫn mở đúng đối tượng đích, không hiện lỗi chặn; thông báo giữ Chưa đọc; lần bấm sau đánh dấu được |
| TC-29.8 | UC-29-AC-08 · Alt-06 | API thông báo lỗi | Người dùng bấm chuông | Hiển thị ERR-UC29-01 trong bảng kèm nút Thử lại; số chưa đọc đang có giữ nguyên; chọn Thử lại thì tải lại |
| TC-29.9 | UC-29-AC-09 · Alt-07 | Có thông báo Chưa đọc; việc đánh dấu tất cả lỗi | Chọn Đánh dấu tất cả đã đọc | Hiển thị ERR-UC29-02; số chưa đọc và chấm Chưa đọc giữ nguyên |
| TC-29.10 | UC-29-AC-10 · Alt-08, BR-7 | Buyer đang mở trang và bảng thông báo; một xưởng vừa gửi báo giá | Đến chu kỳ làm mới | Số trên chuông tăng mà trang không tải lại; bảng đang mở không tự thêm dòng; đóng rồi mở lại bảng thì thông báo mới đứng đầu |
| TC-29.11 | UC-29-AC-11 · Alt-09, BR-14 | Buyer B; thông báo của Buyer A; mã thông báo không tồn tại | Buyer B mở thẳng đường dẫn thông báo của A, rồi mở mã không tồn tại | Cả hai trường hợp hiển thị ERR-UC29-03; thông báo của A vẫn Chưa đọc |
| TC-29.12 | UC-29-AC-12 · Alt-10 | Phiên đã hết hạn khi người dùng đang xem bảng thông báo | Bấm một thông báo | Hiển thị ERR-UC04-05; đăng nhập lại xong, thông báo đã chọn được mở |
| TC-29.13 | UC-29-AC-13 · Alt-11, BR-8 | Guest chưa đăng nhập | Mở đầu trang; mở đường dẫn trang Thông báo | Không có chuông; được chuyển sang đăng nhập, đăng nhập xong vào trang Thông báo |
| TC-29.14 | UC-29-AC-14 · BR-2 | Có hai Admin A, B cùng có quyền Kiểm duyệt & Danh mục, và một Artisan; Buyer gửi hồ sơ bán hàng | Admin A mở NT-UC01-01 và đọc | Thông báo của A Đã đọc, số của A giảm; thông báo của B vẫn Chưa đọc, số của B không đổi; Artisan không có thông báo này |
| TC-29.15 | UC-29-AC-15 · BR-3 | Buyer có NT-UC20-01 (báo giá sắp hết hiệu lực) và NT-UC22-02 (đơn đã giao) | Bấm từng thông báo | Mở So sánh báo giá; mở chi tiết đơn; báo giá chưa được chấp nhận và đơn chưa Hoàn tất cho tới khi Buyer tự thao tác ở màn hình đích; bấm lại thông báo đã đọc chỉ mở đích |
| TC-29.16 | UC-29-AC-16 · BR-4 | Tài khoản có 5 thông báo Chưa đọc | Mở bảng, xem, đóng bảng mà không bấm thông báo nào | Vẫn 5 thông báo Chưa đọc; không có thao tác đánh dấu chưa đọc lại |
| TC-29.17 | UC-29-AC-17 · BR-5 | Tài khoản lần lượt có 0, 7 và 150 thông báo Chưa đọc | Xem chuông | Không hiện số; hiện "7"; hiện "99+" |
| TC-29.18 | UC-29-AC-18 · BR-6 | Tài khoản có 12 thông báo | Mở bảng; rồi chuyển tab Tất cả và Chưa đọc ở trang Thông báo | Bảng hiện 10 thông báo mới nhất xếp từ mới tới cũ; đổi tab và xem thêm không tải lại trang |
| TC-29.19 | UC-29-AC-19 · BR-8 | Artisan có thông báo bán hàng NT-UC18-01 và mua hàng NT-UC19-01; Admin có thông báo quản trị | Artisan đang ở Kênh người bán mở chuông và bấm NT-UC19-01; Admin mở chuông | Artisan thấy cả hai thông báo với nhãn Bán hàng và Mua hàng; bấm NT-UC19-01 chuyển sang Khu mua hàng mở So sánh báo giá mà không bị hỏi đăng nhập lại; Admin chỉ thấy thông báo nhãn Quản trị |
| TC-29.20 | UC-29-AC-20 · BR-9 | Buyer nhận NT-UC19-01 nêu "Xưởng Bình An"; sau đó xưởng đổi tên và được Admin duyệt | Buyer mở lại thông báo | Thông báo vẫn nêu "Xưởng Bình An" như lúc gửi |
| TC-29.21 | UC-29-AC-21 · BR-11 | Yêu cầu giao tận nơi gửi cho 3 xưởng, Buyer chấp nhận xưởng A | Xưởng B mở NT-UC20-03; xưởng A mở NT-UC18-01 cũ | NT-UC20-03 chỉ nêu yêu cầu đã chốt và báo giá đã đóng, không có tên xưởng A, giá, mã đơn; NT-UC18-01 không có họ tên, số điện thoại, địa chỉ chi tiết của Buyer |
| TC-29.22 | UC-29-AC-22 · BR-12 | Yêu cầu gửi chung cho 5 xưởng; Buyer lưu nháp một yêu cầu khác; Buyer đồng thời gửi lặp | Gửi yêu cầu rồi kiểm tra thông báo | Mỗi xưởng nhận đúng một NT-UC18-01; Buyer không nhận thông báo vì chính thao tác của mình; lưu nháp không tạo thông báo |
| TC-29.23 | UC-29-AC-23 · BR-13 | Việc tạo thông báo gặp lỗi khi Buyer chấp nhận báo giá | Buyer chấp nhận báo giá | Đơn đặt riêng vẫn được tạo; xưởng vẫn thấy đơn trong danh sách đơn (UC-21) dù chưa có thông báo |
| TC-29.24 | UC-29-AC-24 · BR-15 | Sau một sự kiện có thông báo | Kiểm tra các kênh và thao tác khả dụng | Chỉ có thông báo trong hệ thống, không có email, SMS, push; không có thao tác xóa thông báo |
| TC-29.25 | UC-29-AC-25 · Main Flow, BR-1, BR-3 | Mỗi mẫu thông báo trong Danh mục thông báo | Người nhận bấm thông báo thuộc mẫu đó | Mở đúng đối tượng đích và khu vực nêu trong danh mục |
| TC-29.26 | UC-29-AC-26 · BR-8 | Artisan vừa là chủ xưởng vừa mua hàng của xưởng khác; đơn MH-2026-0001 của xưởng nhận NT-UC31-01, đơn MH-2026-0002 do chính Artisan mua nhận NT-UC32-01 | Artisan mở chuông và bấm lần lượt hai thông báo | NT-UC31-01 nhãn Bán hàng, mở Đơn bán hàng ở Kênh người bán; NT-UC32-01 nhãn Mua hàng, mở Đơn mua của tôi ở Khu mua hàng; không bị hỏi đăng nhập lại |
| TC-29.27 | UC-29-AC-27 · BR-12 | Đơn đặt riêng DR-2026-0001 đang Chờ sản xuất; đơn mua MH-2026-0003 đã thanh toán online | Buyer hủy đơn DR-2026-0001; xưởng hủy đơn MH-2026-0003 | NT-UC33-07 chỉ gửi cho xưởng (bên còn lại), không gửi cho Buyer là người hủy; NT-UC33-02 gửi cho Buyer; mỗi người nhận đúng một bản và mở đúng đơn phía của mình |
