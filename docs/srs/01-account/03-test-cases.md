---
title: "01 — Tài khoản — Ca nghiệm thu"
code: SRS-01
type: srs
status: official
version: 0.3.0
date: 2026-11-18
---

# 01 — Tài khoản — Ca nghiệm thu

[BRD 0.2.0](../../brd/01-account/usecase.md) · [ERD](../../design/erd.md) · [Phụ thuộc liên nhóm](../README.md) · [Kiến trúc và giao diện](01-architecture.md) · [Đặc tả chức năng](02-feature-description.md)

Các ca dưới đây truy vết tới AC của BRD. Chạy qua giao diện hoặc API tương ứng khi có ứng dụng.

## UC-01 — Đăng ký bán hàng bằng tài khoản hiện có

| TC | AC và quy tắc | Chuẩn bị | Thao tác | Kết quả mong đợi |
|---|---|---|---|---|
| TC-1.1 | UC-01-AC-01 · Main Flow | Buyer đã đăng nhập, chưa có hồ sơ | Điền đủ thông tin xưởng, tải minh chứng, xem lại và chọn Gửi hồ sơ | Hồ sơ ở trạng thái Chờ duyệt; mọi Admin nhận NT-UC01-01; hiện trang Trạng thái hồ sơ |
| TC-1.2 | UC-01-AC-02 · Alt-01, BR-1 | Guest chưa đăng nhập đang ở trang giới thiệu bán hàng | Chọn "Bắt đầu đăng ký bán hàng", rồi đăng nhập | Quay lại đúng form hồ sơ, không phải trang chủ |
| TC-1.3 | UC-01-AC-03 · Alt-02, BR-2 | Tài khoản đã có hồ sơ Chờ duyệt, Bị từ chối hoặc Đã duyệt | Mở form hồ sơ (kể cả bằng đường dẫn trực tiếp) | Hiển thị ERR-UC01-01 và trạng thái hiện có; không mở form mới |
| TC-1.4 | UC-01-AC-04 · Alt-03 | Chưa tải minh chứng | Chọn Gửi hồ sơ | Hiển thị ERR-UC01-02; hồ sơ không được gửi |
| TC-1.5 | UC-01-AC-05 · Alt-04 | Đang ở bước tải ảnh | Chọn tệp PDF hoặc ảnh lớn hơn 10 MB | Hiển thị ERR-UC01-03; ảnh không được nhận |
| TC-1.6 | UC-01-AC-06 · Alt-05 | Bỏ trống tên xưởng hoặc nhập số điện thoại sai | Chọn Gửi hồ sơ | Hiển thị ERR-UC01-04 và lỗi ngay tại trường sai; dữ liệu đã nhập được giữ |
| TC-1.7 | UC-01-AC-07 · Alt-06, BR-8 | Buyer đã nhập một phần thông tin | Đóng trang rồi quay lại "Bán hàng cùng vCraft" | Thấy "Tiếp tục hồ sơ đang soạn" với dữ liệu đã nhập; Admin chưa nhận thông báo |
| TC-1.8 | UC-01-AC-08 · Alt-07, BR-6 | Hồ sơ Cần chỉnh sửa có lý do | Sửa và gửi lại | Cùng hồ sơ về Chờ duyệt; mọi Admin nhận NT-UC01-02; không tạo hồ sơ thứ hai |
| TC-1.9 | UC-01-AC-09 · BR-3 | Hồ sơ đang Chờ duyệt | Buyer mở hồ sơ để sửa | Không có nút sửa; chỉ xem được trạng thái |
| TC-1.10 | UC-01-AC-10 · BR-4 | Hồ sơ Chờ duyệt, Cần chỉnh sửa hoặc Bị từ chối | Buyer mua hàng bình thường; Guest tìm xưởng trên trang công khai | Mua hàng được; xưởng không xuất hiện công khai; không có Kênh người bán |
| TC-1.11 | UC-01-AC-11 · BR-5 | Có hồ sơ đã gửi | Buyer khác, Guest hoặc Artisan khác xem minh chứng; Guest xem trang gian hàng của xưởng đã mở | Minh chứng không xem được, chỉ Admin và người nộp xem; số điện thoại trong hồ sơ không hiện ở trang công khai |
| TC-1.12 | UC-01-AC-12 · BR-7 | Người dùng là Guest, Buyer chưa có hồ sơ, Buyer đã gửi hồ sơ, Artisan, Admin | Xem đầu trang | Guest và Buyer chưa có hồ sơ thấy "Bán hàng cùng vCraft"; Buyer đã gửi thấy "Trạng thái hồ sơ"; Artisan thấy "Kênh người bán"; Admin không thấy mục bán hàng |
| TC-1.13 | UC-01-AC-13 · BR-9 | Hồ sơ được duyệt | Artisan mở gian hàng ở UC-07 | Tên xưởng, địa chỉ, giới thiệu, ảnh xưởng đã điền sẵn từ hồ sơ |

## UC-02 — Kiểm duyệt hồ sơ bán hàng

| TC | AC và quy tắc | Chuẩn bị | Thao tác | Kết quả mong đợi |
|---|---|---|---|---|
| TC-2.1 | UC-02-AC-01 · Main Flow, BR-4 | Có hồ sơ Chờ duyệt | Admin mở chi tiết, chọn Duyệt và xác nhận | Hồ sơ Đã duyệt; tài khoản có Kênh người bán; gian hàng Đang chuẩn bị điền sẵn thông tin; người nộp nhận NT-UC02-01 |
| TC-2.2 | UC-02-AC-02 · Alt-01, BR-3 | Có hồ sơ Chờ duyệt | Admin chọn Yêu cầu chỉnh sửa, nhập lý do hợp lệ | Hồ sơ Cần chỉnh sửa; người nộp nhận NT-UC02-02 và sửa, gửi lại được |
| TC-2.3 | UC-02-AC-03 · Alt-02, BR-3 | Có hồ sơ Chờ duyệt | Admin chọn Từ chối hẳn, nhập lý do hợp lệ | Hồ sơ Bị từ chối; người nộp nhận NT-UC02-03; không còn nút gửi lại |
| TC-2.4 | UC-02-AC-04 · Alt-03 | Admin đang từ chối | Để trống lý do hoặc nhập dưới 10 ký tự | Hiển thị ERR-UC02-01; hồ sơ giữ nguyên |
| TC-2.5 | UC-02-AC-05 · Alt-04, BR-5 | Hai Admin cùng mở một hồ sơ Chờ duyệt | Admin A duyệt, rồi Admin B từ chối | Hồ sơ giữ kết quả Duyệt; Admin B thấy ERR-UC02-02 |
| TC-2.6 | UC-02-AC-06 · Alt-05, BR-2 | Artisan gửi yêu cầu đổi tên xưởng | Admin duyệt; sau đó với yêu cầu khác, Admin từ chối kèm lý do | Duyệt: gian hàng hiện tên mới, NT-UC02-04. Từ chối: giữ tên cũ, NT-UC02-05. Trong lúc chờ, gian hàng hiện tên cũ |
| TC-2.7 | UC-02-AC-07 · BR-1 | Người dùng là Buyer, Artisan hoặc Guest | Mở Trang quản trị hoặc chi tiết hồ sơ bằng đường dẫn trực tiếp | Bị từ chối truy cập (ERR-UC04-06) |
| TC-2.8 | UC-02-AC-08 · BR-6 | Hồ sơ Đã duyệt | Khách và Buyer khác xem trang công khai, tìm xưởng | Ảnh minh chứng không xuất hiện ở bất kỳ trang công khai nào |
| TC-2.9 | UC-02-AC-09 · BR-7 | Hồ sơ đã qua nhiều lần xử lý | Admin mở chi tiết | Lịch sử hiển thị mỗi lần: quyết định, lý do, người xử lý, thời điểm |
| TC-2.10 | UC-02-AC-10 · Alt-06, BR-8 | Xưởng đã đăng chứng nhận nhãn "Do xưởng cung cấp" | Admin lọc loại "Chứng nhận của xưởng", mở chứng nhận và chọn Xác minh | Chứng nhận có nhãn "BQL đã xác minh" ở trang gian hàng công khai; ghi người xác minh và thời điểm |
| TC-2.11 | UC-02-AC-11 · Alt-06, BR-8 | Chứng nhận đã được xác minh | Xưởng sửa hoặc thay ảnh chứng nhận | Nhãn về "Do xưởng cung cấp"; chứng nhận quay lại danh sách chờ đối chiếu của Admin |
| TC-2.12 | UC-02-AC-12 · Alt-04, Alt-06 | Admin đang mở chi tiết chứng nhận, xưởng vừa thay ảnh | Admin chọn Xác minh | Hiển thị ERR-UC02-02; chứng nhận không đổi nhãn |
| TC-2.13 | UC-02-AC-13 · BR-9 | Xưởng vừa thêm một chứng nhận mới | Admin mở chuông thông báo | Không có thông báo về chứng nhận mới; chứng nhận chỉ xuất hiện khi Admin lọc danh sách |

## UC-03 — Đăng ký tài khoản mua hàng

| TC | AC và quy tắc | Chuẩn bị | Thao tác | Kết quả mong đợi |
|---|---|---|---|---|
| TC-3.1 | UC-03-AC-01 · Main Flow | Guest chưa đăng nhập, email chưa tồn tại | Điền đủ thông tin hợp lệ, đồng ý điều khoản, chọn Tạo tài khoản | Tài khoản Buyer được tạo; người dùng đã đăng nhập; về đúng trang đang xem |
| TC-3.2 | UC-03-AC-02 · Alt-01, BR-1 | Email đã có tài khoản (Buyer, Artisan hoặc Admin) | Đăng ký bằng email đó | Hiển thị ERR-UC03-01; không tạo tài khoản; dữ liệu đã nhập được giữ trừ mật khẩu |
| TC-3.3 | UC-03-AC-03 · Alt-02 | Mật khẩu "abcdefgh" (thiếu số) | Chọn Tạo tài khoản | Hiển thị ERR-UC03-02; không tạo tài khoản |
| TC-3.4 | UC-03-AC-04 · Alt-03 | Hai lần nhập mật khẩu khác nhau | Chọn Tạo tài khoản | Hiển thị ERR-UC03-03 |
| TC-3.5 | UC-03-AC-05 · Alt-04 | Họ tên trống hoặc email "lan@" | Chọn Tạo tài khoản | Hiển thị ERR-UC03-04 tại trường sai |
| TC-3.6 | UC-03-AC-06 · Alt-05 | Chưa chọn đồng ý điều khoản | Chọn Tạo tài khoản | Hiển thị ERR-UC03-05 |
| TC-3.7 | UC-03-AC-07 · BR-1 | Email đã đăng ký là `lan.tran@gmail.com` | Đăng ký bằng `Lan.Tran@Gmail.com` (có khoảng trắng ở cuối) | Hiển thị ERR-UC03-01 |
| TC-3.8 | UC-03-AC-08 · BR-2, BR-4 | Đăng ký thành công | Kiểm tra quyền của tài khoản | Chỉ là Buyer; không có Kênh người bán; không thấy Trang quản trị |
| TC-3.9 | UC-03-AC-09 · BR-3 | Đăng ký thành công ngay sau khi chưa mở email | Dùng tài khoản | Dùng được ngay, không yêu cầu xác thực email |
| TC-3.10 | UC-03-AC-10 · BR-5 | Guest có 2 món trong giỏ, hoặc bắt đầu đăng ký từ nút "Bắt đầu đăng ký bán hàng" | Đăng ký thành công | Giỏ còn 2 món; hoặc quay lại đúng form hồ sơ bán hàng |

## UC-04 — Đăng nhập, đăng xuất

| TC | AC và quy tắc | Chuẩn bị | Thao tác | Kết quả mong đợi |
|---|---|---|---|---|
| TC-4.1 | UC-04-AC-01 · Main Flow | Buyer có tài khoản | Nhập đúng email, mật khẩu | Đăng nhập thành công, về trang đang xem hoặc Khu mua hàng |
| TC-4.2 | UC-04-AC-02 · Alt-01, BR-4 | Nhập sai mật khẩu, và riêng một lần nhập email không tồn tại | Chọn Đăng nhập | Cả hai trường hợp đều hiện cùng ERR-UC04-01 |
| TC-4.3 | UC-04-AC-03 · Alt-02, BR-5 | Đã nhập sai 4 lần | Nhập sai lần thứ 5, rồi nhập đúng ngay sau đó | Hiện ERR-UC04-02; lần nhập đúng vẫn bị từ chối cho đến hết 15 phút |
| TC-4.4 | UC-04-AC-04 · Alt-03, BR-8 | Guest bấm "Đăng nhập bằng Google" (nếu có làm) | Xác thực Google thành công | Email đã có tài khoản thì vào tài khoản đó; chưa có thì tạo Buyer; không tạo hai tài khoản cùng email |
| TC-4.5 | UC-04-AC-05 · Alt-04, BR-2 | Buyer chưa có hồ sơ | Mở thẳng đường dẫn Kênh người bán | Hiện ERR-UC04-03 kèm nút Đăng ký bán hàng |
| TC-4.6 | UC-04-AC-06 · Alt-05, BR-7 | Buyer có hồ sơ Chờ duyệt, Cần chỉnh sửa hoặc Bị từ chối | Mở Kênh người bán | Hiện ERR-UC04-04 kèm nút Xem trạng thái hồ sơ |
| TC-4.7 | UC-04-AC-07 · Alt-06, BR-2 | Buyer hoặc Artisan đã đăng nhập | Mở thẳng đường dẫn Trang quản trị | Hiện ERR-UC04-06; không thấy dữ liệu quản trị |
| TC-4.8 | UC-04-AC-08 · Alt-07 | Guest chưa đăng nhập | Mở thẳng đường dẫn Kênh người bán, rồi đăng nhập bằng tài khoản Artisan | Vào đúng Kênh người bán |
| TC-4.9 | UC-04-AC-09 · Alt-08, BR-3 | Phiên đã hết hạn khi đang soạn yêu cầu báo giá | Bấm Gửi | Hiện ERR-UC04-05; đăng nhập lại xong, yêu cầu đang soạn còn nguyên |
| TC-4.10 | UC-04-AC-10 · Alt-09 | Người dùng đang đăng nhập | Chọn Đăng xuất, rồi bấm nút Quay lại của trình duyệt | Về trang chủ; trang cần đăng nhập không mở lại được; giỏ tài khoản còn nguyên khi đăng nhập lần sau |
| TC-4.11 | UC-04-AC-11 · BR-1 | Artisan đã duyệt đang ở Khu mua hàng | Vào Kênh người bán rồi quay về sàn | Không bị hỏi đăng nhập lại |
| TC-4.12 | UC-04-AC-12 · BR-3 | Guest có 2 món trong giỏ | Đăng nhập | Giỏ gộp vào giỏ tài khoản; món trùng cộng số lượng |
| TC-4.13 | UC-04-AC-13 · BR-6 | Admin | Đăng nhập | Về Trang quản trị; không có giỏ hàng, nút mua hay Kênh người bán |

## UC-05 — Quản lý thông tin tài khoản

| TC | AC và quy tắc | Chuẩn bị | Thao tác | Kết quả mong đợi |
|---|---|---|---|---|
| TC-5.1 | UC-05-AC-01 · Main Flow | Người dùng đã đăng nhập | Đổi họ tên và số điện thoại hợp lệ, chọn Lưu | Hiển thị "Đã lưu thông tin"; tên mới hiện ở menu |
| TC-5.2 | UC-05-AC-02 · Alt-01 | Người dùng đã đăng nhập | Đổi mật khẩu với mật khẩu hiện tại đúng, mật khẩu mới hợp lệ | Mật khẩu mới có hiệu lực; đăng nhập lần sau dùng mật khẩu mới |
| TC-5.3 | UC-05-AC-03 · Alt-02 | Họ tên trống hoặc số điện thoại 9 chữ số | Chọn Lưu | Hiển thị ERR-UC05-01; thông tin cũ giữ nguyên |
| TC-5.4 | UC-05-AC-04 · Alt-03 | Nhập mật khẩu hiện tại sai | Chọn Đổi mật khẩu | Hiển thị ERR-UC05-02; mật khẩu không đổi |
| TC-5.5 | UC-05-AC-05 · Alt-04 | Mật khẩu mới thiếu số, hoặc nhập lại không khớp | Chọn Đổi mật khẩu | Hiển thị ERR-UC05-03; mật khẩu không đổi |
| TC-5.6 | UC-05-AC-06 · BR-1 | Người dùng mở "Tài khoản của tôi" | Tìm cách đổi email | Trường email chỉ đọc, không có nút đổi |
| TC-5.7 | UC-05-AC-07 · BR-2 | Artisan mở "Tài khoản của tôi" | Tìm tên xưởng, địa chỉ xưởng | Không có ở trang này; chỉ có ở Kênh người bán (UC-07) |
| TC-5.8 | UC-05-AC-08 · BR-3 | Buyer đã có đơn dưới tên cũ | Đổi họ tên | Đơn cũ vẫn hiển thị tên lúc đặt |
| TC-5.9 | UC-05-AC-09 · BR-4 | Tài khoản đăng nhập ở hai trình duyệt | Đổi mật khẩu ở trình duyệt A | Phiên ở trình duyệt B bị kết thúc, phải đăng nhập lại; phiên A giữ nguyên |
| TC-5.10 | UC-05-AC-10 · BR-5 | Admin | Mở "Tài khoản của tôi" | Sửa được họ tên và mật khẩu; không có mục gian hàng hay hồ sơ bán hàng |
