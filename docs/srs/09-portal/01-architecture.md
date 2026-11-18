---
title: "09 — Quản trị & Giám sát sàn — Kiến trúc và giao diện"
code: SRS-09
type: srs
status: official
version: 0.4.0
date: 2026-11-18
---

# 09 — Quản trị & Giám sát sàn — Kiến trúc và giao diện

[BRD 0.2.0](../../brd/09-portal/usecase.md) · [ERD](../../design/erd.md) · [Phụ thuộc liên nhóm](../README.md) · [Đặc tả chức năng](02-feature-description.md) · [Ca nghiệm thu](03-test-cases.md)

API dùng tiền tố `/api`. Backend lấy danh tính từ phiên và kiểm quyền trên đối tượng ở mỗi thao tác. Các lệnh dưới đây mô tả giao diện chính của từng UC.

## UC-34 — Quản lý tài khoản và phân quyền quản trị viên

`GET, POST /admin/admins` · `PATCH /admin/admins/{id}/permissions` · `PUT /admin/admins/{id}/status` · `POST /admin/admins/{id}/reset-password`

Chỉ Trưởng BQL được tạo Admin, sửa quyền, khóa, mở khóa và đặt lại mật khẩu. Kiểm tra quyền hiện hành ở Backend trước mỗi lệnh. Thay tập quyền nguyên tử và yêu cầu ít nhất một nhóm. Khóa hoặc đặt lại mật khẩu thu hồi mọi phiên trong cùng giao dịch. Bảo vệ tài khoản Trưởng BQL duy nhất cả khi gọi API trực tiếp.

## UC-35 — Theo dõi và thống kê giao dịch, đơn hàng toàn hệ thống

`GET /admin/monitor/summary` · `GET /admin/monitor/{kind}` · `GET /admin/monitor/{kind}/{id}`

Tách truy vấn tổng quan, danh sách và chi tiết. Kiểm quyền Giám sát giao dịch trên từng API. Loại nháp ở cả ba truy vấn. Thống kê theo ngày tạo đơn hoặc lần gửi RFQ đầu tiên, phân nhóm theo trạng thái hiện tại. Chỉ số chờ hoàn đếm đơn hủy/từ chối có tiền online đang chờ hoàn trên mọi kỳ. Lấy đầy đủ thông tin nhận hàng cho quản trị đủ quyền. Chi tiết nối YC, phiên bản báo giá, DR, lô/QR, yêu cầu hủy và giao dịch tiền bằng ID.
