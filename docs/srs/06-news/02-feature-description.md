---
title: "06 — Tin tức cộng đồng — Đặc tả chức năng"
code: SRS-06
type: srs
status: official
version: 0.4.0
date: 2026-11-18
---

# 06 — Tin tức cộng đồng — Đặc tả chức năng

[BRD 0.2.0](../../brd/06-news/usecase.md) · [ERD](../../design/erd.md) · [Phụ thuộc liên nhóm](../README.md) · [Kiến trúc và giao diện](01-architecture.md) · [Ca nghiệm thu](03-test-cases.md)

Giới hạn dữ liệu và mã lỗi theo phiên bản BRD trên. Các phương án đang mở trong mục Open Questions cần nhóm xác nhận.

## UC-27 — Quản lý tin tức

Backend yêu cầu Trưởng BQL hoặc quyền Tin tức cộng đồng cho cả danh sách quản trị và lệnh ghi. Sai quyền trả ERR-UC27-06.

Admin soạn tin, chọn loại tin và phạm vi đọc (công khai hoặc nội bộ), lưu nháp hoặc đăng; sửa, đổi phạm vi, ẩn và đăng lại tin đã đăng; xóa tin nháp. Khi một tin được đăng lần đầu, mọi Artisan nhận thông báo.

**Quyền và điều kiện** Admin đã đăng nhập

Kiểm quyền Admin, lọc HTML và tệp đính kèm. Giữ ngày đăng đầu qua lần ẩn/đăng lại, chỉ phát sự kiện theo quy tắc UC-27.

| Field | Format | Validation | Required | Ghi chú |
|---|---|---|---|---|
| Tiêu đề | Text; length 150 | Không để trống | Có | Ví dụ: "Lịch cắt điện tuần 47" |
| Loại tin | Chọn một | Thị trường & Nguyên liệu, Hạ tầng & Vận hành, Chính sách & Quy định, Hoạt động & Sự kiện làng nghề, Thông báo chung (BR-2) | Có điều kiện | Bắt buộc khi đăng; lưu nháp không cần |
| Nội dung | Văn bản định dạng; tối đa 10000 ký tự chữ (không tính định dạng) | Chỉ giữ các định dạng cho phép: tiêu đề phụ, in đậm, in nghiêng, danh sách, bảng, liên kết, ảnh (BR-10); không để trống khi đăng | Có điều kiện | Ví dụ: "Điện lực thông báo cắt điện ngày 24/11 từ 07:00 đến 11:00 ở thôn Phú Vinh." Thẻ tin ở danh sách lấy đoạn đầu phần chữ |
| Ảnh bìa | Ảnh JPG / PNG / WebP | 0–1 ảnh, ≤ 10 MB | Không | Hiện ở thẻ tin và đầu bài; backend thu nhỏ (BR-11) |
| Ảnh trong bài | Ảnh JPG / PNG / WebP | 0–10 ảnh, mỗi ảnh ≤ 10 MB | Không | Chèn vào vị trí bất kỳ trong nội dung; backend thu nhỏ (BR-11) |
| Tệp đính kèm | PDF, DOC, DOCX, XLS, XLSX | 0–5 tệp, mỗi tệp ≤ 10 MB | Không | Hiện cuối bài kèm tên và dung lượng, người đọc tải về (BR-15). Ví dụ: "Bang-gia-may-thang-11.xlsx" |
| Công khai | Có / Không | Mặc định Không | Không | Có: hiện ở trang chủ và ai cũng đọc được. Không: chỉ Artisan đọc (tin nội bộ). Đổi được cả sau khi đăng (Alt-07) |
| Trạng thái | Chỉ xem | Nháp, Đã đăng, Ẩn | — | Đổi bằng Đăng, Ẩn, Đăng lại |
| Ngày đăng | Chỉ xem; dd/MM/yyyy HH:mm, giờ Việt Nam | Ghi lần đăng đầu (BR-8) | — | Trống khi tin chưa từng đăng |
| Cập nhật lúc | Chỉ xem; dd/MM/yyyy HH:mm, giờ Việt Nam | Hệ thống ghi mỗi lần lưu | — | Người đọc chỉ thấy khi tin được sửa sau khi đăng |
| Bộ lọc danh sách | Chọn trạng thái, loại tin, phạm vi | Mặc định tất cả | Không | Danh sách mới cập nhật trước, 20 tin mỗi trang, phân trang theo số trang |

| Mã lỗi | Điều kiện trả lỗi |
|---|---|
| ERR-UC27-01 | Dữ liệu không hợp lệ |
| ERR-UC27-02 | Ảnh không hợp lệ |
| ERR-UC27-03 | Chưa đủ thông tin để đăng |
| ERR-UC27-04 | Không xóa được tin |
| ERR-UC27-05 | Tin vừa được Admin khác thay đổi |
| ERR-UC27-06 | Không có quyền |
| ERR-UC27-07 | Không tải được danh sách |
| ERR-UC27-08 | Tệp đính kèm không hợp lệ |

| Thông báo | Người nhận |
|---|---|
| NT-UC27-01 | Mọi Artisan |

## UC-28 — Đọc tin tức

Guest hoặc Buyer đọc tin công khai (khối ở trang chủ, danh sách lọc theo loại, chi tiết tin) mà không cần đăng nhập. Artisan đọc thêm tin nội bộ trong Kênh người bán.

**Quyền và điều kiện** Tin công khai: không yêu cầu đăng nhập Tin nội bộ: Artisan đã đăng nhập và có quyền bán

Áp phạm vi độc giả trước khi phân trang 10 tin và khi mở bài/tệp. Frontend dùng lại truy vấn tin công khai cho UC-06.

| Field | Format | Validation | Required | Ghi chú |
|---|---|---|---|---|
| Loại tin (bộ lọc) | Chọn một | Thị trường & Nguyên liệu, Hạ tầng & Vận hành, Chính sách & Quy định, Hoạt động & Sự kiện làng nghề, Thông báo chung | Không | Mặc định tất cả loại |
| Thanh chọn trang | Trước, các số trang, Sau | Rút gọn bằng dấu ... khi nhiều trang; trang hiện tại được tô đậm | — | Số trang nằm trên địa chỉ (BR-5) |
| Danh sách tin | Chỉ xem | Tin Đã đăng thuộc phạm vi người đọc (BR-1); mới nhất trước theo ngày đăng; 10 tin mỗi trang (BR-5) | — | Thẻ: ảnh bìa, loại tin, tiêu đề, ngày đăng, đoạn đầu nội dung (tối đa 120 ký tự). Trong Kênh người bán thêm nhãn Công khai hoặc Nội bộ |
| Tiêu đề, loại tin | Text, chỉ xem | — | — | Từ UC-27 |
| Địa chỉ tin | Chỉ xem; dạng phần-chữ-mã-tin | Phần chữ sinh từ tiêu đề, mã tin cố định ở cuối (BR-6) | — | Ví dụ: `/tin-tuc/lich-cat-dien-tuan-47-1024` |
| Ngày đăng | Chỉ xem; dd/MM/yyyy HH:mm, giờ Việt Nam | Thời điểm đăng lần đầu (UC-27, BR-8) | — | |
| Cập nhật lúc | Chỉ xem; dd/MM/yyyy HH:mm, giờ Việt Nam | Chỉ hiện khi tin được sửa sau khi đăng | Không | |
| Ảnh bìa | Ảnh, chỉ xem | Ảnh thu nhỏ ở danh sách, ảnh lớn ở chi tiết | Không | |
| Nội dung | Văn bản định dạng, chỉ xem | Chỉ các định dạng cho phép (UC-27, BR-10) | — | |
| Ảnh trong bài | Ảnh, chỉ xem | Ảnh thu nhỏ trong bài, phóng to khi chọn | Không | |
| Tệp đính kèm | Danh sách, chỉ xem | Tên tệp, dung lượng, nút Tải về | Không | Theo phạm vi của tin (BR-12) |

| Mã lỗi | Điều kiện trả lỗi |
|---|---|
| ERR-UC28-01 | Tin không khả dụng |
| ERR-UC28-02 | Không tải được tin |
