---
title: "07 — Trung tâm thông báo — Kiến trúc và giao diện"
code: SRS-07
type: srs
status: official
version: 0.4.0
date: 2026-11-18
---

# 07 — Trung tâm thông báo — Kiến trúc và giao diện

[BRD 0.2.0](../../brd/07-notification/usecase.md) · [ERD](../../design/erd.md) · [Phụ thuộc liên nhóm](../README.md) · [Đặc tả chức năng](02-feature-description.md) · [Ca nghiệm thu](03-test-cases.md)

API dùng tiền tố `/api`. Backend lấy danh tính từ phiên và kiểm quyền trên đối tượng ở mỗi thao tác. Các lệnh dưới đây mô tả giao diện chính của từng UC.

## UC-29 — Chuông thông báo, số chưa đọc, mở đúng đối tượng

Admin bị khóa ngừng nhận tin mới. Khi mở tin cũ sau khi bị gỡ quyền, ghi đã đọc rồi kiểm quyền tại màn hình đích. Ba mẫu NT-UC01-01, NT-UC01-02 và NT-UC07-01 chỉ gửi Trưởng BQL và Admin hoạt động có quyền Kiểm duyệt & Danh mục.

`GET /me/notifications` · `POST /me/notifications/{id}/read` · `POST /me/notifications/read-all`

Chỉ đánh dấu dòng thuộc phiên hiện tại. Khi mở, kiểm lại quyền đích và khu vực mua/bán. Lỗi ghi đã đọc vẫn cho mở đích hợp lệ.

**Dữ liệu liên quan** `notifications`, `events`, `accounts`.
