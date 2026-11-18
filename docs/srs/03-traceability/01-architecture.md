---
title: "03 — Truy xuất nguồn gốc — Kiến trúc và giao diện"
code: SRS-03
type: srs
status: official
version: 0.3.0
date: 2026-11-18
---

# 03 — Truy xuất nguồn gốc — Kiến trúc và giao diện

[BRD 0.1.0](../../brd/03-traceability/usecase.md) · [ERD](../../design/erd.md) · [Phụ thuộc liên nhóm](../README.md) · [Đặc tả chức năng](02-feature-description.md) · [Ca nghiệm thu](03-test-cases.md)

API dùng tiền tố `/api`. Backend lấy danh tính từ phiên và kiểm quyền trên đối tượng ở mỗi thao tác. Các lệnh dưới đây mô tả giao diện chính của từng UC.

## UC-14 — Quản lý lô sản xuất, tự sinh QR

`POST /seller/lots` · `PATCH /seller/lots/{id}`

Kiểm đúng một nguồn, đúng xưởng và trạng thái nguồn. Sinh mã truy xuất ổn định. Kiểm ít nhất một mốc khi hoàn thành lô.

**Dữ liệu liên quan** `lots`, `orders`, `products`, `categories`.

## UC-15 — Ghi nhật ký sản xuất, ghi chú đính chính

`POST /seller/lots/{id}/steps` · `POST /seller/lots/{id}/corrections`

Khóa lô trước khi sửa/xóa mốc. Đang thực hiện cho sửa, Hoàn thành cần mở lại, Đã xuất xưởng chỉ ghi đính chính theo BRD.

**Dữ liệu liên quan** `lots`, `lot_steps`, `corrections`, `assets`.

## UC-16 — Xem, tải, in QR

`GET /seller/lots/{id}/qr`

Mã hóa URL công khai theo token đã lưu. Chỉ tải PNG hoặc in khi lô Hoàn thành hoặc Đã xuất xưởng. Tải hoặc in lại cùng URL, kiểm quyền chủ xưởng khi quản lý tem.

**Dữ liệu liên quan** `lots`.

## UC-17 — Xem nguồn gốc qua QR

`GET /trace/{token}`

Tra token độc lập với trạng thái niêm yết sản phẩm. Xuất dữ liệu truy xuất theo UC-17, loại liên hệ Buyer và giá đơn.

**Dữ liệu liên quan** `lots`, `lot_steps`, `corrections`, `shops`.

## Liên kết với Trang quản trị

UC-35 tra các lô theo đơn đặt riêng qua lots.order_id và lấy QR hiện có. Chi tiết giám sát chỉ đọc. Các quyền quản lý lô và điều kiện in tem tiếp tục kiểm tra tại UC-14 đến UC-16.
