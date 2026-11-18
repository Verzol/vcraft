# 01-account — CHANGELOG

Lịch sử thay đổi của `usecase.md` và SRS liên kết. Mỗi lần sửa: tăng `version` trong frontmatter và thêm một dòng ở đây.

| Date | Document | Version | Change | By |
|---|---|---|---|---|
| 2026-10-30 | [usecase.md](usecase.md) | 0.1.0 | Khởi tạo BRD: UC-01 đăng ký tài khoản Artisan viết hoàn chỉnh làm ví dụ mẫu. | Khánh Chi |
| 2026-11-16 | [usecase.md](usecase.md) | 0.2.0 | Mô hình một tài khoản, Guest đăng ký mua hàng trước rồi đăng ký bán hàng. Viết đủ UC-01 đến UC-05. UC-01 chuyển thành đăng ký bán hàng bằng tài khoản hiện có, thêm trang giới thiệu bán hàng, lối vào "Bán hàng cùng vCraft", bản nháp hồ sơ, gửi lại hồ sơ Cần chỉnh sửa (tham khảo Etsy). UC-02 thêm hai kiểu từ chối, xử lý đồng thời, duyệt yêu cầu đổi tên/địa chỉ xưởng. Thêm UC-03, UC-04, UC-05. Thêm G-03 đến G-08. | Khánh Chi |
| 2026-11-18 | [usecase.md](usecase.md) | 0.3.0 | Đồng bộ với nhóm `09-portal` (UC-34): bỏ giới hạn EN-19 khỏi Ngoài phạm vi, bổ sung liên kết tới `09-portal` và cập nhật UC-03 BR-4, UC-05 BR-5 về tài khoản Trưởng BQL và Admin do Trưởng BQL tạo và phân quyền. | Khánh Chi |
| 2026-11-18 | [usecase.md](usecase.md) | 0.4.0 | Đồng bộ với `09-portal`: UC-04 bước 6 đưa Admin về mục đầu tiên có quyền (UC-34 BR-9); thêm Alt-10/ERR-UC04-07/BR-9 cho Admin Đã khóa đăng nhập; UC-02 BR-1, Alt-06 và ERR-UC04-06 áp nhóm quyền `Kiểm duyệt & Danh mục`; làm rõ EN-01 trong Ngoài phạm vi chỉ gồm Buyer, Artisan, xưởng; chốt G-01 — NT-UC01-01, NT-UC01-02 chỉ gửi Trưởng BQL và Admin có quyền Kiểm duyệt & Danh mục. | Khánh Chi |
