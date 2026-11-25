---
title: "08 — Mua hàng có sẵn — Kiến trúc và giao diện"
code: SRS-08
type: srs
status: official
version: 0.4.0
date: 2026-11-18
---

# 08 — Mua hàng có sẵn — Kiến trúc và giao diện

[BRD 0.2.0](../../brd/08-order/usecase.md) · [ERD](../../design/erd.md) · [Phụ thuộc liên nhóm](../README.md) · [Đặc tả chức năng](02-feature-description.md) · [Ca nghiệm thu](03-test-cases.md)

API dùng tiền tố `/api`. Backend lấy danh tính từ phiên và kiểm quyền trên đối tượng ở mỗi thao tác. Các lệnh dưới đây mô tả giao diện chính của từng UC.

## UC-30 — Quản lý giỏ hàng

`GET /me/cart` · `PUT, DELETE /me/cart/items/{productId}`

Giỏ Guest ở trình duyệt. Gộp theo sản phẩm, giới hạn bằng số còn bán. Nhóm theo xưởng và tách món không khả dụng.

**Dữ liệu liên quan** `cart_items`, `products`, `shops`.

## UC-31 — Checkout: giao hàng, thanh toán, tạo đơn

`POST /checkout` · `POST /payments/{id}/retry`

Giữ hàng nguyên tử cho đúng một xưởng. COD vào Chờ xác nhận, online vào Chờ thanh toán 15 phút. Xác minh kết quả ở Backend.

**Dữ liệu liên quan** `orders`, `order_lines`, `cart_items`, `products`, `delivery_options`, `payment_attempts`, `events`.

## UC-32 — Quản lý đơn mua hàng

Tra cứu toàn sàn thuộc UC-35. Admin không được chuyển trạng thái đơn.

`GET /orders/{id}` · `POST /orders/{id}/transitions`

Kiểm quyền Buyer/xưởng từng bước. Xử lý nhận tại xưởng và giao hàng theo luồng khác nhau. Tác vụ hết hạn kiểm tra lại trạng thái.

**Dữ liệu liên quan** `orders`, `order_lines`, `order_events`, `payment_attempts`, `events`.

## UC-33 — Hủy đơn hàng (đơn mua và đơn đặt riêng)

UC-35 đọc thanh toán, yêu cầu hủy và hoàn tiền từ cùng nguồn. Mỗi đơn tồn đọng chỉ đếm một lần bất kể số lần thử hoàn.

`POST /orders/{id}/cancel` · `POST /orders/{id}/cancel-requests`

Phân nhánh theo loại đơn, bên yêu cầu, trạng thái và cọc đã thu. Hoàn tồn đơn mua đúng lượng được trả. Callback muộn chỉ hoàn tiền, không khôi phục đơn.

**Dữ liệu liên quan** `orders`, `cancel_requests`, `refunds`, `payment_attempts`, `products`, `order_lines`, `order_events`, `events`.
