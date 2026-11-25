---
title: "05 — Bản đồ làng nghề — Kiến trúc và giao diện"
code: SRS-05
type: srs
status: official
version: 0.3.0
date: 2026-11-18
---

# 05 — Bản đồ làng nghề — Kiến trúc và giao diện

[BRD 0.1.0](../../brd/05-village-map/usecase.md) · [ERD](../../design/erd.md) · [Phụ thuộc liên nhóm](../README.md) · [Đặc tả chức năng](02-feature-description.md) · [Ca nghiệm thu](03-test-cases.md)

API dùng tiền tố `/api`. Backend lấy danh tính từ phiên và kiểm quyền trên đối tượng ở mỗi thao tác. Các lệnh dưới đây mô tả giao diện chính của từng UC.

## UC-25 — Quản lý vị trí và lịch đón khách

`PUT /seller/shop/location` · `PUT /seller/shop/visiting-hours`

Ghim đang chờ đổi địa chỉ bị khóa. Dùng lịch tuần trừ khoảng nghỉ theo phút UTC+7 cho mọi màn hình.

**Dữ liệu liên quan** `shops`, `weekly_hours`, `closures`, `shop_changes`.

## UC-26 — Khám phá xưởng trên bản đồ

`GET /map/pins` · `GET /map/pins/{id}`

Chỉ công khai xưởng đã mở, gồm Tạm ngừng. Nạp popup khi bấm và tính lại giờ đón khách tại thời điểm đó.

**Dữ liệu liên quan** `shops`, `weekly_hours`, `closures`.
