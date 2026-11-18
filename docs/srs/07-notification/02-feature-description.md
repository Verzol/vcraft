---
title: "07 — Trung tâm thông báo — Đặc tả chức năng"
code: SRS-07
type: srs
status: official
version: 0.3.0
date: 2026-11-18
---

# 07 — Trung tâm thông báo — Đặc tả chức năng

[BRD 0.1.0](../../brd/07-notification/usecase.md) · [ERD](../../design/erd.md) · [Phụ thuộc liên nhóm](../README.md) · [Kiến trúc và giao diện](01-architecture.md) · [Ca nghiệm thu](03-test-cases.md)

Giới hạn dữ liệu và mã lỗi theo phiên bản BRD trên. Các phương án đang mở trong mục Open Questions cần nhóm xác nhận.

## UC-29 — Chuông thông báo, số chưa đọc, mở đúng đối tượng

Người dùng đã đăng nhập nhìn chuông thông báo để biết số thông báo chưa đọc, mở bảng thông báo hoặc trang Thông báo, bấm một thông báo để đi tới đúng đối tượng liên quan; thông báo được đánh dấu đã đọc. Người dùng cũng đánh dấu được tất cả thông báo đã đọc. UC-29 chỉ hiển thị và quản lý trạng thái đọc; thông báo do các use case khác phát.

**Quyền và điều kiện** Người dùng đã đăng nhập bằng tài khoản Buyer, Artisan hoặc Admin

Chỉ đánh dấu dòng thuộc phiên hiện tại. Khi mở, kiểm lại quyền đích và khu vực mua/bán. Lỗi ghi đã đọc vẫn cho mở đích hợp lệ.

| Field | Format | Validation | Required | Ghi chú |
|---|---|---|---|---|
| Chuông thông báo | Biểu tượng kèm số | Chỉ hiện khi đã đăng nhập, ở mọi khu vực | — | Guest không có (BR-8) |
| Số chưa đọc | Number, chỉ xem | Số thông báo Chưa đọc của tài khoản; bằng 0 thì ẩn số; từ 100 trở lên hiện "99+" | — | Ví dụ: 3 (BR-5) |
| Danh sách thông báo | Chỉ xem | Chỉ thông báo của tài khoản đang đăng nhập; mới nhất trước theo thời điểm gửi; bảng 10 thông báo, trang Thông báo 20 thông báo mỗi lần tải | — | BR-2, BR-6 |
| Tiêu đề | Text, chỉ xem | Lấy từ mẫu thông báo lúc gửi | — | Ví dụ: "Có báo giá mới" |
| Nội dung | Text, chỉ xem | Đã điền biến lúc gửi (BR-9); bảng rút gọn tối đa 100 ký tự, trang Thông báo hiện đủ | — | Ví dụ: "Xưởng Bình An đã báo giá cho yêu cầu YC-2026-0001: 42.500.000 đồng, hiệu lực đến 2026-12-20." |
| Thời gian gửi | Chỉ xem | Dưới 1 phút: "Vừa xong"; dưới 60 phút: "{n} phút trước"; dưới 24 giờ: "{n} giờ trước"; từ 24 giờ: dd/MM/yyyy HH:mm, giờ Việt Nam | — | |
| Trạng thái đọc | Chỉ xem | Chưa đọc (có chấm nổi bật) hoặc Đã đọc | — | Đổi khi bấm thông báo hoặc Đánh dấu tất cả đã đọc (BR-4) |
| Nhãn khu vực | Chỉ xem | Mua hàng, Bán hàng hoặc Quản trị theo đối tượng đích | — | Giúp Artisan phân biệt thông báo mua và bán (BR-8) |
| Bộ lọc | Chọn một | Tất cả (mặc định) hoặc Chưa đọc | Không | Chỉ ở trang Thông báo |

| Mã lỗi | Điều kiện trả lỗi |
|---|---|
| ERR-UC29-01 | Không tải được thông báo |
| ERR-UC29-02 | Không đánh dấu được tất cả đã đọc |
| ERR-UC29-03 | Thông báo không tồn tại hoặc không có quyền |
