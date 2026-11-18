# 09-portal — CHANGELOG

Lịch sử thay đổi của `usecase.md` và SRS liên kết. Mỗi lần sửa: tăng `version` trong frontmatter và thêm một dòng ở đây.

| Date | Document | Version | Change | By |
|---|---|---|---|---|
| 2026-11-18 | [usecase.md](usecase.md) | 0.1.0 | Khởi tạo BRD `09-portal`: bổ sung UC-34 (Quản lý tài khoản và phân quyền quản trị viên cho Trưởng BQL với 3 nhóm quyền) và UC-35 (Theo dõi và thống kê giao dịch, đơn hàng toàn hệ thống ở chế độ chỉ đọc). | Khánh Chi |
| 2026-11-18 | [usecase.md](usecase.md) | 0.2.0 | Đồng bộ chéo với BRD 01–08: thu hẹp `ERR-UC34-04` chỉ dùng cho UC-34, mỗi use case giữ mã lỗi không có quyền riêng (UC-34 BR-4); thống nhất 7 chỉ số thống kê UC-35 theo đơn tạo trong kỳ, Giao dịch đang chờ hoàn tiền là tồn đọng hiện thời (BR-5, BR-6, Terms); thêm UC-34 BR-9 điều hướng mặc định theo nhóm quyền; chi tiết giao dịch thêm yêu cầu hủy/cọc, liên kết YC ↔ DR và lô sản xuất (UC-35 BR-9, thêm liên kết `BRD-03`); tách hai bộ lọc ngày và quy tắc reset khi đổi tab (UC-35 BR-10), đưa tìm theo Buyer vào Ngoài phạm vi; thêm UC-34 Alt-09/`ERR-UC34-05` và ẩn nút Khóa/Sửa quyền ở dòng Trưởng BQL; dẫn chiếu `ERR-UC04-07` cho Admin Đã khóa; chốt G-01, G-02. | Khánh Chi |
