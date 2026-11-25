---
title: "03 — Truy xuất nguồn gốc — Ca nghiệm thu"
code: SRS-03
type: srs
status: official
version: 0.3.0
date: 2026-11-18
---

# 03 — Truy xuất nguồn gốc — Ca nghiệm thu

[BRD 0.1.0](../../brd/03-traceability/usecase.md) · [ERD](../../design/erd.md) · [Phụ thuộc liên nhóm](../README.md) · [Kiến trúc và giao diện](01-architecture.md) · [Đặc tả chức năng](02-feature-description.md)

Các ca dưới đây truy vết tới AC của BRD. Chạy qua giao diện hoặc API tương ứng khi có ứng dụng.

## UC-14 — Quản lý lô sản xuất, tự sinh QR

| TC | AC và quy tắc | Chuẩn bị | Thao tác | Kết quả mong đợi |
|---|---|---|---|---|
| TC-14.1 | UC-14-AC-01 · Main Flow | Artisan đã đăng nhập, có sản phẩm | Chọn sản phẩm, nhập tên, số lượng, ngày bắt đầu rồi Tạo lô | Lô ở Đang thực hiện; có mã lô, QR, URL; mở trang lô |
| TC-14.2 | UC-14-AC-02 · Alt-01 | Để trống tên lô | Chọn Tạo lô | Hiển thị ERR-UC14-01; dữ liệu giữ nguyên; không tạo lô |
| TC-14.3 | UC-14-AC-03 · Alt-02, BR-9 | Chọn đơn Đã hủy; hoặc đơn còn Chờ sản xuất vì Buyer chưa đặt cọc; hoặc đơn đã Đang giao | Chọn Tạo lô | Hiển thị ERR-UC14-02 trong cả ba trường hợp; không tạo lô |
| TC-14.4 | UC-14-AC-04 · Alt-03, BR-4 | Sinh QR thất bại | Tạo lô | Lô vẫn được tạo; hiện ERR-UC14-06 và nút Sinh lại; sinh lại thì URL không đổi |
| TC-14.5 | UC-14-AC-05 · Alt-04 | Lô Đang thực hiện | Sửa tên lô, số lượng rồi Lưu | Thông tin mới hiển thị; mã lô, QR, URL không đổi |
| TC-14.6 | UC-14-AC-06 · Alt-05, BR-6 | Lô Hoàn thành | Sửa tên lô | Hiển thị ERR-UC14-04; gợi ý mở lại lô; tên lô không đổi |
| TC-14.7 | UC-14-AC-07 · Alt-06, BR-5 | Lô Đang thực hiện có ít nhất một mốc | Chọn Hoàn thành và xác nhận | Lô Hoàn thành; mốc bị khóa; in, tải QR được |
| TC-14.8 | UC-14-AC-08 · Alt-07, BR-8 | Lô Đang thực hiện chưa có mốc | Chọn Hoàn thành | Hiển thị ERR-UC14-03; lô giữ trạng thái |
| TC-14.9 | UC-14-AC-09 · Alt-08, BR-6 | Lô Hoàn thành | Chọn Mở lại và xác nhận | Lô Đang thực hiện; mốc sửa được; mốc sửa hoặc thêm sau đó hiện nhãn "đã chỉnh sửa" |
| TC-14.10 | UC-14-AC-10 · Alt-09, BR-7 | Lô Hoàn thành | Chọn Đã xuất xưởng và xác nhận | Lô Đã xuất xưởng; khóa cứng |
| TC-14.11 | UC-14-AC-11 · Alt-10, BR-7 | Lô Đã xuất xưởng | Chọn Mở lại hoặc Sửa | Hiển thị ERR-UC14-05 gợi ý ghi chú đính chính; trạng thái không đổi |
| TC-14.12 | UC-14-AC-12 · Alt-11, BR-11 | Artisan khác | Gửi thao tác trên lô này | Hiển thị ERR-UC14-07; dữ liệu không đổi |
| TC-14.13 | UC-14-AC-13 · BR-1 | Đã tạo lô cho sản phẩm A | Đổi đối tượng sang sản phẩm B | Không có thao tác này; đối tượng không đổi |
| TC-14.14 | UC-14-AC-14 · BR-2 | Đơn đặt riêng X đang Đang sản xuất | Tạo hai lô cho đơn X | Cả hai lô tạo được |
| TC-14.15 | UC-14-AC-15 · BR-3 | Lô đã tạo | Tìm thao tác xóa lô | Không có chức năng xóa lô |
| TC-14.16 | UC-14-AC-16 · BR-4 | Một lô 200 sản phẩm | Kiểm tra QR của lô | Cả lô dùng chung một QR và một URL |
| TC-14.17 | UC-14-AC-17 · BR-10 | Sản phẩm đang Nháp | Tạo lô cho sản phẩm | Tạo được |
| TC-14.18 | UC-14-AC-18 · Main Flow, BR-12 | Đơn đặt riêng Đang sản xuất; có phân loại Hiển thị | Tạo lô, chọn phân loại "Giỏ và làn", nhập tên lô, số lượng, ngày bắt đầu | Lô tạo được và mang phân loại "Giỏ và làn"; để trống phân loại thì hiển thị ERR-UC14-01 và không tạo lô |
| TC-14.19 | UC-14-AC-19 · Alt-04, BR-12 | Lô đặt riêng đang Đang thực hiện | Sửa phân loại rồi Lưu | Phân loại mới hiển thị; mã lô, QR, URL không đổi |
| TC-14.20 | UC-14-AC-20 · Alt-12, BR-12 | Đơn đặt riêng; không có phân loại phù hợp hoặc chưa có phân loại Hiển thị nào | Tạo lô, mở ô Phân loại | Hiển thị ERR-UC14-08; không có chỗ nhập phân loại mới; có phân loại thì chọn gần nhất được, chưa có thì chưa tạo được lô |
| TC-14.21 | UC-14-AC-21 · BR-12 | Lô đặt riêng đã tạo; Admin Ẩn phân loại đó | Mở trang nguồn gốc của lô | Phân loại vẫn hiện trên trang nguồn gốc |
| TC-14.22 | UC-14-AC-22 · BR-13, BR-8 | Lô Đang thực hiện, rồi lô Hoàn thành | Chọn Hoàn thành, rồi chọn Đã xuất xưởng | Hộp thoại Hoàn thành nhắc đã sản xuất xong toàn bộ số lượng; hộp thoại Đã xuất xưởng nhắc hàng đã rời xưởng; hệ thống chỉ chặn khi lô chưa có mốc |
| TC-14.23 | UC-14-AC-23 · BR-9 | Lô của đơn tạo khi đơn Đang sản xuất; sau đó đơn chuyển Đang giao | Artisan thêm mốc, hoàn thành, xuất xưởng lô đó | Thao tác trên lô đã tạo vẫn làm được; chỉ không tạo được lô mới cho đơn |

## UC-15 — Ghi nhật ký sản xuất, ghi chú đính chính

| TC | AC và quy tắc | Chuẩn bị | Thao tác | Kết quả mong đợi |
|---|---|---|---|---|
| TC-15.1 | UC-15-AC-01 · Main Flow | Lô Đang thực hiện | Thêm mốc đủ ngày, công đoạn, mô tả, một ảnh | Mốc hiện trên timeline đúng thứ tự ngày |
| TC-15.2 | UC-15-AC-02 · Alt-01 | Để trống tên công đoạn | Chọn Lưu mốc | Hiển thị ERR-UC15-01; dữ liệu được giữ; không lưu |
| TC-15.3 | UC-15-AC-03 · Alt-02, BR-2 | Chưa tải ảnh | Chọn Lưu mốc | Hiển thị ERR-UC15-02; mốc không được lưu |
| TC-15.4 | UC-15-AC-04 · Alt-03 | Đang chọn ảnh | Chọn tệp PDF hoặc ảnh lớn hơn 10 MB | Hiển thị ERR-UC15-03; ảnh không được nhận |
| TC-15.5 | UC-15-AC-05 · Alt-04, BR-4 | Lô Hoàn thành | Thêm mốc | Hiển thị ERR-UC15-04 gợi ý mở lại lô; không có mốc mới |
| TC-15.6 | UC-15-AC-06 · Alt-05 | Mất kết nối khi lưu | Chọn Lưu mốc | Hiển thị ERR-UC15-05; dữ liệu và ảnh được giữ; Thử lại lưu được |
| TC-15.7 | UC-15-AC-07 · Alt-06, BR-3 | Lô Đang thực hiện, chưa từng Hoàn thành | Sửa mô tả mốc | Mốc cập nhật, không có nhãn "đã chỉnh sửa" |
| TC-15.8 | UC-15-AC-08 · Alt-06, BR-5 | Lô đã mở lại sau Hoàn thành | Sửa một mốc, thêm một mốc | Cả hai mốc hiện nhãn "đã chỉnh sửa" |
| TC-15.9 | UC-15-AC-09 · Alt-07, BR-3 | Lô Đang thực hiện | Xóa mốc và xác nhận | Mốc biến khỏi timeline; nếu không xác nhận thì mốc giữ nguyên |
| TC-15.10 | UC-15-AC-10 · Alt-08, BR-6 | Lô Đã xuất xưởng | Thêm ghi chú đính chính | Ghi chú hiện trên trang nguồn gốc kèm ngày giờ; mốc cũ không đổi |
| TC-15.11 | UC-15-AC-11 · Alt-09 | Lô Đang thực hiện hoặc Hoàn thành | Thêm ghi chú đính chính | Hiển thị ERR-UC15-07; không có ghi chú mới |
| TC-15.12 | UC-15-AC-12 · Alt-10, BR-9 | Artisan khác | Gửi thao tác ghi nhật ký cho lô này | Hiển thị ERR-UC15-06; dữ liệu không đổi |
| TC-15.13 | UC-15-AC-13 · BR-6 | Ghi chú đính chính đã đăng | Tìm thao tác sửa hoặc xóa ghi chú | Không có thao tác nào |
| TC-15.14 | UC-15-AC-14 · Alt-11, BR-4 | Lô Đã xuất xưởng | Sửa hoặc xóa một mốc | Hiển thị ERR-UC15-08 gợi ý ghi chú đính chính; mốc không đổi |
| TC-15.15 | UC-15-AC-15 · BR-7, BR-1 | Mốc ngày 03/11 thêm sau mốc ngày 05/11; hai mốc cùng ngày | Xem timeline | Mốc 03/11 trước mốc 05/11; mốc cùng ngày theo thời điểm ghi |
| TC-15.16 | UC-15-AC-16 · BR-8 | Artisan tải ảnh hợp lệ | Xem trang nguồn gốc | Dùng ảnh đã thu nhỏ |

## UC-16 — Xem, tải, in QR

| TC | AC và quy tắc | Chuẩn bị | Thao tác | Kết quả mong đợi |
|---|---|---|---|---|
| TC-16.1 | UC-16-AC-01 · Main Flow | Lô Hoàn thành, số lượng 200 | Tải QR rồi In QR với số tem mặc định | Có ảnh PNG; bản in 200 tem gồm QR, tên sản phẩm, mã lô, tên xưởng |
| TC-16.2 | UC-16-AC-02 · Alt-01, BR-2 | Lô Đang thực hiện | Chọn Tải QR hoặc In QR | Nút bị vô hiệu, hiển thị ERR-UC16-01; vẫn xem QR và xem trước được |
| TC-16.3 | UC-16-AC-03 · Alt-02 | Hộp thoại in | Nhập số tem 0 hoặc 1500 | Hiển thị ERR-UC16-02; không in |
| TC-16.4 | UC-16-AC-04 · Alt-03 | Tạo ảnh QR thất bại | Chọn Tải QR | Hiển thị ERR-UC16-03 và nút Thử lại |
| TC-16.5 | UC-16-AC-05 · Alt-04 | Gian hàng chưa mở, lô Hoàn thành | Mở trang lô | Có cảnh báo gian hàng chưa mở; vẫn tải và in được |
| TC-16.6 | UC-16-AC-06 · Alt-05, BR-5 | Artisan khác | Mở trang lô này | Hiển thị ERR-UC16-04 |
| TC-16.7 | UC-16-AC-07 · BR-1, BR-4 | Hai lần in cùng một lô | So sánh QR | Cả hai bản in cùng URL; không giới hạn số lần in lại |
| TC-16.8 | UC-16-AC-08 · BR-3 | Bản in | Kiểm tra nội dung tem | Có QR, tên sản phẩm hoặc mã đơn, mã lô, tên xưởng |

## UC-17 — Xem nguồn gốc qua QR

| TC | AC và quy tắc | Chuẩn bị | Thao tác | Kết quả mong đợi |
|---|---|---|---|---|
| TC-17.1 | UC-17-AC-01 · Main Flow | Gian hàng đã mở; lô Đã xuất xưởng có ba mốc và một ghi chú đính chính | Guest chưa đăng nhập quét QR | Hiện đầu trang, timeline từ ngày đầu, thông tin xưởng, ghi chú đính chính; chọn link thì sang sản phẩm hoặc gian hàng |
| TC-17.2 | UC-17-AC-02 · Alt-01 | URL lô sai | Mở link | Hiển thị ERR-UC17-01 |
| TC-17.3 | UC-17-AC-03 · Alt-02, BR-2 | Gian hàng Đang chuẩn bị | Mở link của lô | Hiển thị ERR-UC17-02; không lộ nhật ký |
| TC-17.4 | UC-17-AC-04 · Alt-03 | Tải trang lỗi | Mở link | Hiển thị ERR-UC17-03 và nút Thử lại |
| TC-17.5 | UC-17-AC-05 · Alt-04 | Lô chưa có mốc | Mở link | Hiện "Xưởng chưa cập nhật nhật ký sản xuất" |
| TC-17.6 | UC-17-AC-06 · Alt-05, BR-9 | Lô Đang thực hiện | Mở link | Hiện nhãn "Lô đang thực hiện" cùng nhật ký hiện có |
| TC-17.7 | UC-17-AC-07 · Alt-06, BR-5 | Lô đã mở lại, có một mốc đã sửa | Mở link | Hiện dòng cập nhật lại; mốc sửa có nhãn "đã chỉnh sửa" |
| TC-17.8 | UC-17-AC-08 · Alt-07, BR-2 | Sản phẩm của lô Ẩn, gian hàng Tạm ngừng | Mở link | Nhật ký vẫn xem được; link sản phẩm thay bằng "Sản phẩm hiện không bán" |
| TC-17.9 | UC-17-AC-09 · Alt-08, BR-3 | Lô thuộc đơn đặt riêng, có phân loại "Giỏ và làn", yêu cầu báo giá ban đầu có gắn sản phẩm mẫu | Mở link | Hiện nhãn "Sản xuất theo đơn đặt riêng", tên lô, phân loại "Giỏ và làn"; không có tên Buyer, mã đơn, link sản phẩm, kể cả sản phẩm mẫu |
| TC-17.10 | UC-17-AC-10 · Alt-09 | Một ảnh mốc lỗi | Mở link | Ảnh thay thế tại chỗ đó; các mốc khác hiển thị bình thường |
| TC-17.11 | UC-17-AC-11 · BR-1 | Khách chưa đăng nhập | Mở link | Xem được, không bị chuyển sang đăng nhập |
| TC-17.12 | UC-17-AC-12 · BR-4 | Mốc nhập ngược thứ tự ngày | Mở link | Timeline xếp từ ngày đầu tiên |
| TC-17.13 | UC-17-AC-13 · BR-6 | Ghi chú đính chính đã đăng | Mở link | Ghi chú có ngày giờ; mốc cũ không đổi |
| TC-17.14 | UC-17-AC-14 · BR-7 | Điện thoại màn hình hẹp | Mở link | Bố cục vừa màn hình, ảnh tải từng phần |
| TC-17.15 | UC-17-AC-15 · BR-8 | Đổi tên sản phẩm hoặc tên lô | Mở lại URL cũ | URL vẫn mở đúng lô |
