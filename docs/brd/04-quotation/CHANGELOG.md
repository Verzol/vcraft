# 04-quotation — CHANGELOG

Lịch sử thay đổi BRD nhóm 04-quotation (Đặt hàng riêng, UC-18 đến UC-24).

| Date | Document | Version | Change | By |
|---|---|---|---|---|
| 2026-11-18 | [usecase.md](usecase.md) | 0.1.0 | Khởi tạo BRD: UC-18 đến UC-24 (yêu cầu báo giá gửi riêng và gửi chung, hạn và nhắc hạn hai chiều, báo giá có hiệu lực và phiên bản, chấp nhận báo giá và đặt cọc, đơn đặt riêng, hủy yêu cầu, nhật ký sản xuất). | Khánh Chi |
| 2026-11-18 | [usecase.md](usecase.md) | 0.2.0 | Đồng bộ với nhóm `09-portal` (UC-35): bổ sung liên kết `BRD-09`, cập nhật Overview, Ngoài phạm vi và UC-18 BR-13, UC-20 BR-13, UC-21 BR-1 về quyền theo dõi chỉ xem của Admin tại Trang quản trị. | Khánh Chi |
| 2026-11-25 | [usecase.md](usecase.md) | 0.3.0 | Chốt G-07: UC-18 bắt buộc nhập hạn cọc tối thiểu mong muốn (1–14 ngày, mặc định 3); UC-19 nhập hạn đặt cọc trong `[mức Buyer chọn, 14]` khi cọc > 0% (bỏ qua khi 0%); UC-20 chụp cứng `deposit_due_at`, quá hạn tự hủy ở UC-33; đồng bộ giữ UC-14 BR-9 chỉ tạo lô khi đơn Đang sản xuất. | Phan Văn Phước Hưng |
