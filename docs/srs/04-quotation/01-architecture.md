---
title: "04 — Đặt hàng riêng — Kiến trúc và giao diện"
code: SRS-04
type: srs
status: official
version: 0.4.0
date: 2026-11-18
---

# 04 — Đặt hàng riêng — Kiến trúc và giao diện

[BRD 0.2.0](../../brd/04-quotation/usecase.md) · [ERD](../../design/erd.md) · [Phụ thuộc liên nhóm](../README.md) · [Đặc tả chức năng](02-feature-description.md) · [Ca nghiệm thu](03-test-cases.md)

API dùng tiền tố `/api`. Backend lấy danh tính từ phiên và kiểm quyền trên đối tượng ở mỗi thao tác. Các lệnh dưới đây mô tả giao diện chính của từng UC.

## UC-18 — Tạo, lưu nháp, xóa nháp và gửi yêu cầu báo giá

Admin chỉ tra cứu RFQ đã gửi qua UC-35.

`POST /rfqs` · `PATCH, DELETE /rfqs/{id}` · `POST /rfqs/{id}/send`

Phân biệt ba nguồn yêu cầu. Kiểm lại xưởng đủ điều kiện khi gửi, đóng băng danh sách nhận cùng RFQ. Lịch nhắc, hết hạn và gia hạn dựa trên hạn phản hồi.

**Dữ liệu liên quan** `rfqs`, `invitations`, `shops`, `categories`, `products`, `events`.

## UC-19 — Tiếp nhận yêu cầu, gửi báo giá hoặc không nhận

`POST /invitations/{id}/quotes` · `POST /invitations/{id}/decline`

Kiểm quyền đúng xưởng nhận. Lưu phiên bản kế tiếp, tính tiền hàng, phí giao và cọc theo báo giá. Hạn hiệu lực thuộc phiên bản.

**Dữ liệu liên quan** `invitations`, `quotes`, `rfqs`, `events`.

## UC-20 — So sánh và chấp nhận báo giá

Quyền Giám sát giao dịch chỉ cho đọc qua UC-35. Chấp nhận báo giá vẫn thuộc Buyer.

`GET /rfqs/{id}/comparison` · `POST /rfqs/{id}/accept`

Buyer xác nhận phiên bản còn hiệu lực. Khóa cùng RFQ với UC-23. Tạo một đơn DR ngay lúc chấp nhận và đóng lựa chọn còn lại.

**Dữ liệu liên quan** `rfqs`, `invitations`, `quotes`, `orders`, `order_lines`, `payment_attempts`, `events`.

## UC-21 — Xem yêu cầu, báo giá và đơn đặt riêng

UC-35 dùng giao diện đọc riêng cho quản trị. Quyền xem tất cả phiên bản báo giá ở đó không mở rộng quyền của xưởng tại UC-21.

`GET /me/rfqs` · `GET /rfqs/{id}` · `GET /me/custom-orders`

Chọn góc nhìn mua/bán từ phiên. Xưởng chỉ đọc lời mời và giá của mình. Đọc đơn qua Buyer hoặc xưởng trúng.

**Dữ liệu liên quan** `rfqs`, `invitations`, `quotes`, `orders`.

## UC-22 — Cập nhật và theo dõi đơn đặt riêng

`POST /custom-orders/{id}/transitions`

Chặn bắt đầu nếu còn thiếu cọc. Ghi tiến độ, xác nhận thu phần còn lại và mốc giao. Dùng UC-33 cho hủy hoặc hoàn cọc.

**Dữ liệu liên quan** `orders`, `payment_attempts`, `order_events`, `events`.

## UC-23 — Hủy yêu cầu báo giá trước khi chấp nhận

`POST /rfqs/{id}/cancel`

Khóa yêu cầu, kiểm tra chủ sở hữu và chưa chấp nhận. Yêu cầu đã có đơn chuyển xử lý hủy theo UC-33.

**Dữ liệu liên quan** `rfqs`, `invitations`, `quotes`, `events`.

## UC-24 — Xem nhật ký sản xuất của đơn đặt riêng

`GET /custom-orders/{id}/lots`

Kiểm Buyer của đơn trước khi đọc toàn bộ lô gắn order_id. Mốc và đính chính do UC-14/15 quản lý.

**Dữ liệu liên quan** `orders`, `lots`, `lot_steps`, `corrections`.
