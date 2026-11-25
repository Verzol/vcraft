# 08-order — CHANGELOG

Lịch sử thay đổi BRD nhóm 08-order (Mua hàng có sẵn, UC-30 đến UC-33).

| Date | Document | Version | Change | By |
|---|---|---|---|---|
| 2026-11-18 | [usecase.md](usecase.md) | 0.1.0 | Khởi tạo BRD: UC-30 Quản lý giỏ hàng (gộp giỏ Guest, nhóm theo xưởng, nhóm Không khả dụng), UC-31 Checkout (giao hàng, COD và ZaloPay sandbox, giữ hàng, hạn thanh toán 15 phút), UC-32 Quản lý đơn mua hàng (xác nhận, từ chối, giao, xác nhận đã thu COD, tự hủy sau 3 ngày, tự hoàn tất sau 7 ngày), UC-33 Hủy đơn hàng cho đơn mua và đơn đặt riêng (yêu cầu hủy, hoàn số lượng, hoàn tiền qua cổng); khai báo NT-UC31-01 đến NT-UC33-09; 10 Open Questions (G-01 đến G-10) | Khánh Chi |
| 2026-11-18 | [usecase.md](usecase.md) | 0.2.0 | Đồng bộ với nhóm `09-portal` (UC-35): bổ sung liên kết `BRD-09`, cập nhật Overview, Ngoài phạm vi và UC-32 BR-2 về quyền theo dõi chỉ xem của Admin tại Trang quản trị. | Khánh Chi |
| 2026-11-25 | [usecase.md](usecase.md) | 0.2.1 | Sửa liên kết tổng quan gãy ở mục Liên quan từ `../../overview/use-case-overview.md` sang `../../overview.md`. | Phan Văn Phước Hưng |
| 2026-11-25 | [usecase.md](usecase.md) | 0.3.0 | Chốt G-02 (idempotency key, retry 5 lần `1p → 5p → 15p → 1h → 6h`, lỗi vĩnh viễn/hết ngưỡng đặt `refunds.state = FAILED` không đổi trạng thái đơn), cập nhật UC-33 Alt-09 cho callback cọc muộn sau khi hủy quá hạn, và đóng G-10 đồng bộ với danh mục thông báo của `07-notification`. | Phan Văn Phước Hưng |
