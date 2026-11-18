---
title: "02 — Triển lãm số — Kiến trúc và giao diện"
code: SRS-02
type: srs
status: official
version: 0.3.0
date: 2026-11-18
---

# 02 — Triển lãm số — Kiến trúc và giao diện

[BRD 0.1.0](../../brd/02-showcase/usecase.md) · [ERD](../../design/erd.md) · [Phụ thuộc liên nhóm](../README.md) · [Đặc tả chức năng](02-feature-description.md) · [Ca nghiệm thu](03-test-cases.md)

API dùng tiền tố `/api`. Backend lấy danh tính từ phiên và kiểm quyền trên đối tượng ở mỗi thao tác. Các lệnh dưới đây mô tả giao diện chính của từng UC.

## UC-06 — Xem trang chủ, giới thiệu làng nghề

`GET /home`

Ghép nội dung làng nghề, dữ liệu triển lãm và ba tin công khai mới nhất. Các liên kết giữ đúng điểm vào RFQ và danh sách tin.

**Dữ liệu liên quan** `shops`, `products`, `articles`.

## UC-07 — Quản lý thông tin gian hàng

`PATCH /seller/shop` · `POST /seller/shop/change-requests`

Lưu riêng dữ liệu chủ hộ và liên hệ công khai. Chuyển tên/địa chỉ cần duyệt sang hàng chờ UC-02. Đổi ảnh chứng nhận thu hồi nhãn.

**Dữ liệu liên quan** `shops`, `shop_changes`, `certificates`, `delivery_options`, `assets`.

## UC-08 — Khám phá xưởng

`GET /shops` · `GET /shops/{id}`

Phân trang xưởng công khai. Lọc phân loại từ sản phẩm Hiển thị, tính đón khách như UC-25. Mở đúng xưởng khi gửi RFQ riêng.

**Dữ liệu liên quan** `shops`, `products`, `categories`, `weekly_hours`, `closures`.

## UC-09 — Quản lý sản phẩm

`POST /seller/products` · `PATCH, DELETE /seller/products/{id}`

Kiểm tra chủ xưởng và trạng thái sản phẩm. Chỉ xóa nháp chưa có lô. Tồn theo sản phẩm, đối chiếu UC-31 trước khi cập nhật đồng thời.

**Dữ liệu liên quan** `products`, `categories`, `assets`, `lots`.

## UC-10 — Xem danh sách, tìm kiếm, lọc sản phẩm

`GET /products` · `GET /products/suggestions`

Áp quyền công khai trước tìm, lọc và phân trang. Gợi ý tên sản phẩm. Trả trạng thái và số còn bán tại lúc đọc.

**Dữ liệu liên quan** `products`, `shops`, `categories`.

## UC-11 — Xem chi tiết sản phẩm

`GET /products/{id}`

Trả giá, ảnh và lô được liệt kê. Hành động mua dùng giỏ UC-30, đặt theo mẫu chuyển product_id cho UC-18.

**Dữ liệu liên quan** `products`, `shops`, `lots`.

## UC-12 — Quản lý phân loại

`POST /admin/categories` · `PATCH /admin/categories/{id}`

Admin quản lý thứ tự và khả năng chọn. Giữ liên kết lịch sử cho nội dung đã dùng phân loại.

**Dữ liệu liên quan** `categories`, `products`, `lots`, `rfqs`.

## UC-13 — Quản lý trạng thái hoạt động của gian hàng

`POST /seller/shop/status`

Kiểm checklist ngay lúc mở hoặc mở lại, gồm vị trí đã lưu và sản phẩm Hiển thị. Tạm ngừng chặn giao dịch mới theo BRD, vẫn cho xử lý đơn đang có.

**Dữ liệu liên quan** `shops`, `products`, `delivery_options`.
