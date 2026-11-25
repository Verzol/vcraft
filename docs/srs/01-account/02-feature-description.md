---
title: "01 — Tài khoản — Đặc tả chức năng"
code: SRS-01
type: srs
status: official
version: 0.4.0
date: 2026-11-18
---

# 01 — Tài khoản — Đặc tả chức năng

[BRD 0.4.0](../../brd/01-account/usecase.md) · [ERD](../../design/erd.md) · [Phụ thuộc liên nhóm](../README.md) · [Kiến trúc và giao diện](01-architecture.md) · [Ca nghiệm thu](03-test-cases.md)

Giới hạn dữ liệu và mã lỗi theo phiên bản BRD trên. Các phương án đang mở trong mục Open Questions cần nhóm xác nhận.

## UC-01 — Đăng ký bán hàng bằng tài khoản hiện có

Khi gửi mới hoặc nộp lại, chỉ chụp Trưởng BQL và Admin đang hoạt động có quyền Kiểm duyệt & Danh mục vào danh sách nhận.

Buyer nhập thông tin xưởng, liên hệ và tải minh chứng hộ làng nghề; hệ thống ghi nhận hồ sơ ở trạng thái Chờ duyệt và báo cho Admin.

**Quyền và điều kiện** Người dùng đã đăng nhập bằng tài khoản Buyer Tài khoản chưa có hồ sơ bán hàng nào đã gửi (hồ sơ Cần chỉnh sửa xử lý ở Alt-07)

Lấy account_id từ phiên mua. Lưu từng lần gửi, khóa sửa ở Chờ duyệt. Nộp lại nối với hồ sơ Cần chỉnh sửa.

| Field | Format | Validation | Required | Ghi chú |
|---|---|---|---|---|
| Họ tên chủ hộ | Text; length 100 | Lấy từ tài khoản | Có | Chỉ đọc. Sửa ở UC-05 |
| Email | Text; length 100 | Lấy từ tài khoản | Có | Chỉ đọc. Chỉ Admin xem |
| Tên xưởng | Text; length 150 | Không để trống | Có | Ví dụ: "Xưởng mây tre Bình An". Đổi sau khi duyệt phải qua Admin (BR-2 của UC-02) |
| Địa chỉ trong làng | Text; length 200 | Không để trống | Có | Ví dụ: "Thôn Phú Vinh, Chương Mỹ, Hà Nội". Đổi sau khi duyệt phải qua Admin |
| Năm bắt đầu làm nghề | Number; 4 chữ số | Từ 1900 đến năm hiện tại | Không | Ví dụ: 1985 |
| Số điện thoại liên hệ | Text; length 10 | Số di động Việt Nam, 10 chữ số | Có | Điền sẵn từ tài khoản nếu có, được sửa. Chỉ Admin xem ở bước này; không phải liên hệ công khai của gian hàng (xưởng nhập riêng ở UC-07) |
| Giới thiệu ngắn về xưởng | Text; length 500 | Không | Không | Giúp Admin hiểu xưởng. Điền sẵn vào gian hàng (UC-07) |
| Minh chứng hộ làng nghề | Ảnh JPG / PNG / WebP | 1–5 ảnh, mỗi ảnh ≤ 10 MB | Có | Chỉ Admin xem. Loại giấy tờ: xem G-01 |
| Ảnh xưởng | Ảnh JPG / PNG / WebP | 0–10 ảnh, mỗi ảnh ≤ 10 MB | Không | Điền sẵn vào gian hàng, công khai khi gian hàng được mở |
| Trạng thái hồ sơ | Chọn một: Chờ duyệt, Cần chỉnh sửa, Đã duyệt, Bị từ chối | Hệ thống đặt | — | Hiển thị trên trang Trạng thái hồ sơ |
| Lý do từ chối | Text; length 500 | Hệ thống đặt từ UC-02 | — | Chỉ hiển thị cho người nộp khi Cần chỉnh sửa hoặc Bị từ chối |

| Mã lỗi | Điều kiện trả lỗi |
|---|---|
| ERR-UC01-01 | Tài khoản đã có hồ sơ đã gửi |
| ERR-UC01-02 | Thiếu minh chứng |
| ERR-UC01-03 | Ảnh không hợp lệ |
| ERR-UC01-04 | Thông tin xưởng chưa hợp lệ |

| Thông báo | Người nhận |
|---|---|
| NT-UC01-01 | Trưởng BQL và Admin có quyền Kiểm duyệt & Danh mục |
| NT-UC01-02 | Trưởng BQL và Admin có quyền Kiểm duyệt & Danh mục |

## UC-02 — Kiểm duyệt hồ sơ bán hàng

Mỗi thao tác cần Trưởng BQL hoặc quyền Kiểm duyệt & Danh mục, kể cả duyệt thay đổi địa chỉ và xác minh chứng nhận. Sai quyền trả ERR-UC04-06.

Admin đối chiếu thông tin và minh chứng của hồ sơ bán hàng, rồi duyệt hoặc từ chối kèm lý do; người nộp được báo kết quả. Admin cũng duyệt yêu cầu đổi tên, địa chỉ xưởng và xác minh chứng nhận mà xưởng đăng trên gian hàng.

**Quyền và điều kiện** Admin đã đăng nhập Có hồ sơ ở trạng thái Chờ duyệt (hoặc yêu cầu đổi thông tin xưởng, xem Alt-05; hoặc chứng nhận cần xác minh, xem Alt-06)

Quyết định có điều kiện theo trạng thái hiện tại. Duyệt lần đầu khởi tạo gian hàng Đang chuẩn bị. Xử lý thay đổi tên, địa chỉ và xác minh chứng nhận bằng lệnh riêng.

| Field | Format | Validation | Required | Ghi chú |
|---|---|---|---|---|
| Bộ lọc loại và trạng thái | Chọn một | Mặc định: Chờ duyệt | Không | Loại: Hồ sơ bán hàng, Đổi thông tin xưởng, Chứng nhận của xưởng. Với chứng nhận, trạng thái là nhãn: Do xưởng cung cấp, BQL đã xác minh |
| Thông tin hồ sơ | Hiển thị | — | — | Toàn bộ trường của UC-01, chỉ Admin xem email, số điện thoại, minh chứng |
| Lịch sử xử lý | Hiển thị | — | — | Quyết định, lý do, người xử lý, thời điểm của các lần trước |
| Lý do | Text; length 10–500 | Không để trống khi từ chối | Có điều kiện | Bắt buộc với Yêu cầu chỉnh sửa, Từ chối hẳn, Từ chối đổi thông tin. Ví dụ: "Ảnh giấy xác nhận bị mờ, chưa đọc được tên thôn." |

| Mã lỗi | Điều kiện trả lỗi |
|---|---|
| ERR-UC02-01 | Lý do trống hoặc dưới 10 ký tự |
| ERR-UC02-02 | Hồ sơ hoặc chứng nhận đã được xử lý hoặc đã thay đổi |

| Thông báo | Người nhận |
|---|---|
| NT-UC02-01 | Người nộp hồ sơ |
| NT-UC02-02 | Người nộp hồ sơ |
| NT-UC02-03 | Người nộp hồ sơ |
| NT-UC02-04 | Artisan |
| NT-UC02-05 | Artisan |

## UC-03 — Đăng ký tài khoản mua hàng

Chỉ Trưởng BQL được nạp sẵn. UC-34 tạo các tài khoản quản trị còn lại.

Guest nhập họ tên, email, mật khẩu để tạo tài khoản Buyer; hệ thống tự đăng nhập và đưa Guest về thao tác đang làm.

**Quyền và điều kiện** Người dùng chưa đăng nhập Email chưa được dùng cho tài khoản nào

Kiểm tra duy nhất email tại DB. Băm mật khẩu. Gộp giỏ Guest theo UC-30 và khôi phục điểm vào.

| Field | Format | Validation | Required | Ghi chú |
|---|---|---|---|---|
| Họ tên | Text; length 100 | Không để trống | Có | Ví dụ: "Trần Thị Lan" |
| Email | Text; length 100 | Đúng định dạng email; duy nhất, không phân biệt chữ hoa chữ thường, bỏ khoảng trắng đầu cuối (BR-1) | Có | Dùng để đăng nhập. Ví dụ: `lan.tran@gmail.com` |
| Mật khẩu | Text; length 8–64 | Tối thiểu 8 ký tự, có chữ và số | Có | Không hiển thị lại, không ghi nhật ký. Có nút xem/ẩn khi nhập |
| Nhập lại mật khẩu | Text; length 8–64 | Trùng mật khẩu | Có | |
| Đồng ý Điều khoản sử dụng | Hộp chọn | Phải chọn | Có | Nội dung điều khoản: xem G-07 |

| Mã lỗi | Điều kiện trả lỗi |
|---|---|
| ERR-UC03-01 | Email đã được sử dụng |
| ERR-UC03-02 | Mật khẩu không đạt |
| ERR-UC03-03 | Mật khẩu nhập lại không khớp |
| ERR-UC03-04 | Họ tên trống hoặc email sai định dạng |
| ERR-UC03-05 | Chưa đồng ý điều khoản |

## UC-04 — Đăng nhập, đăng xuất

Kiểm mật khẩu trước trạng thái khóa. Sai mật khẩu trả ERR-UC04-01. Đúng mật khẩu nhưng Admin đã khóa trả ERR-UC04-07. Khi thiếu URL đích, chọn mục đầu có quyền theo UC-34 BR-9.

Người dùng nhập email và mật khẩu để vào hệ thống, được đưa về nơi đang làm hoặc khu vực đúng quyền; có thể đăng xuất bất kỳ lúc nào.

**Quyền và điều kiện** Người dùng chưa đăng nhập (với đăng nhập) Người dùng đã có tài khoản

Tách quyền mua và quyền vào kênh bán theo hồ sơ duyệt. Thu hồi phiên đăng xuất và gộp giỏ một lần khi xác thực thành công.

| Field | Format | Validation | Required | Ghi chú |
|---|---|---|---|---|
| Email | Text; length 100 | Không để trống | Có | Không phân biệt chữ hoa chữ thường |
| Mật khẩu | Text; length 8–64 | Không để trống | Có | Không hiển thị lại, không ghi nhật ký. Có nút xem/ẩn |

| Mã lỗi | Điều kiện trả lỗi |
|---|---|
| ERR-UC04-01 | Sai email hoặc mật khẩu |
| ERR-UC04-02 | Đăng nhập sai 5 lần liên tiếp |
| ERR-UC04-03 | Chưa có hồ sơ bán hàng |
| ERR-UC04-04 | Hồ sơ chưa được duyệt |
| ERR-UC04-05 | Phiên hết hạn |
| ERR-UC04-06 | Không phải Admin mở Trang quản trị, hoặc Admin thiếu nhóm quyền mở UC-02 |
| ERR-UC04-07 | Tài khoản Admin đang Đã khóa |

## UC-05 — Quản lý thông tin tài khoản

Admin tự sửa họ tên và mật khẩu ở đây. Trưởng BQL đặt lại mật khẩu cho Admin khác qua UC-34.

Người dùng đã đăng nhập xem và sửa họ tên, số điện thoại, đổi mật khẩu của chính mình. (Mức Should)

**Quyền và điều kiện** Người dùng đã đăng nhập

Chỉ cập nhật trường được phép. Đổi mật khẩu kết thúc các phiên khác. Dùng bản chụp cho các giao dịch cũ.

| Field | Format | Validation | Required | Ghi chú |
|---|---|---|---|---|
| Họ tên | Text; length 100 | Không để trống | Có | |
| Email | Text; length 100 | — | — | Chỉ đọc, không đổi được (BR-1) |
| Số điện thoại | Text; length 10 | Số di động Việt Nam, 10 chữ số | Không | Buyer, Artisan nhập khi cần liên hệ giao hàng. Admin có thể bỏ trống |
| Mật khẩu hiện tại | Text; length 8–64 | Không để trống | Có (khi đổi mật khẩu) | Không hiển thị lại, không ghi nhật ký |
| Mật khẩu mới | Text; length 8–64 | Tối thiểu 8 ký tự, có chữ và số | Có (khi đổi mật khẩu) | Như trên |
| Nhập lại mật khẩu mới | Text; length 8–64 | Trùng mật khẩu mới | Có (khi đổi mật khẩu) | |

| Mã lỗi | Điều kiện trả lỗi |
|---|---|
| ERR-UC05-01 | Họ tên trống hoặc số điện thoại sai |
| ERR-UC05-02 | Mật khẩu hiện tại sai |
| ERR-UC05-03 | Mật khẩu mới không hợp lệ hoặc không khớp |
