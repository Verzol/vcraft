---
title: "01 — Tài khoản — Kiến trúc và giao diện"
code: SRS-01
type: srs
status: official
version: 0.4.0
date: 2026-11-18
---

# 01 — Tài khoản — Kiến trúc và giao diện

[BRD 0.4.0](../../brd/01-account/usecase.md) · [ERD](../../design/erd.md) · [Phụ thuộc liên nhóm](../README.md) · [Đặc tả chức năng](02-feature-description.md) · [Ca nghiệm thu](03-test-cases.md)

API dùng tiền tố `/api`. Backend lấy danh tính từ phiên và kiểm quyền trên đối tượng ở mỗi thao tác. Các lệnh dưới đây mô tả giao diện chính của từng UC.

## UC-01 — Đăng ký bán hàng bằng tài khoản hiện có

Khi gửi mới hoặc nộp lại, chỉ chụp Trưởng BQL và Admin đang hoạt động có quyền Kiểm duyệt & Danh mục vào danh sách nhận.

`POST /me/seller-applications`

Lấy account_id từ phiên mua. Lưu từng lần gửi, khóa sửa ở Chờ duyệt. Nộp lại nối với hồ sơ Cần chỉnh sửa.

**Dữ liệu liên quan** `applications`, `assets`, `events`.

## UC-02 — Kiểm duyệt hồ sơ bán hàng

Mỗi thao tác cần Trưởng BQL hoặc quyền Kiểm duyệt & Danh mục, kể cả duyệt thay đổi địa chỉ và xác minh chứng nhận. Sai quyền trả ERR-UC04-06.

`POST /admin/applications/{id}/decisions`

Quyết định có điều kiện theo trạng thái hiện tại. Duyệt lần đầu khởi tạo gian hàng Đang chuẩn bị. Xử lý thay đổi tên, địa chỉ và xác minh chứng nhận bằng lệnh riêng.

**Dữ liệu liên quan** `applications`, `shops`, `shop_changes`, `certificates`, `events`.

## UC-03 — Đăng ký tài khoản mua hàng

Chỉ Trưởng BQL được nạp sẵn. UC-34 tạo các tài khoản quản trị còn lại.

`POST /auth/register`

Kiểm tra duy nhất email tại DB. Băm mật khẩu. Gộp giỏ Guest theo UC-30 và khôi phục điểm vào.

**Dữ liệu liên quan** `accounts`, `sessions`, `cart_items`.

## UC-04 — Đăng nhập, đăng xuất

Kiểm mật khẩu trước trạng thái khóa. Sai mật khẩu trả ERR-UC04-01. Đúng mật khẩu nhưng Admin đã khóa trả ERR-UC04-07. Khi thiếu URL đích, chọn mục đầu có quyền theo UC-34 BR-9.

`POST /auth/login` · `POST /auth/logout`

Tách quyền mua và quyền vào kênh bán theo hồ sơ duyệt. Thu hồi phiên đăng xuất và gộp giỏ một lần khi xác thực thành công.

**Dữ liệu liên quan** `accounts`, `sessions`, `cart_items`.

## UC-05 — Quản lý thông tin tài khoản

Admin tự sửa họ tên và mật khẩu ở đây. Trưởng BQL đặt lại mật khẩu cho Admin khác qua UC-34.

`PATCH /me` · `POST /me/password`

Chỉ cập nhật trường được phép. Đổi mật khẩu kết thúc các phiên khác. Dùng bản chụp cho các giao dịch cũ.

**Dữ liệu liên quan** `accounts`, `sessions`.
