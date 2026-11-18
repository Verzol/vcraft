---
title: "06 — Tin tức cộng đồng — Ca nghiệm thu"
code: SRS-06
type: srs
status: official
version: 0.3.0
date: 2026-11-18
---

# 06 — Tin tức cộng đồng — Ca nghiệm thu

[BRD 0.1.0](../../brd/06-news/usecase.md) · [ERD](../../design/erd.md) · [Phụ thuộc liên nhóm](../README.md) · [Kiến trúc và giao diện](01-architecture.md) · [Đặc tả chức năng](02-feature-description.md)

Các ca dưới đây truy vết tới AC của BRD. Chạy qua giao diện hoặc API tương ứng khi có ứng dụng.

## UC-27 — Quản lý tin tức

| TC | AC và quy tắc | Chuẩn bị | Thao tác | Kết quả mong đợi |
|---|---|---|---|---|
| TC-27.1 | UC-27-AC-01 · Main Flow | Admin đã đăng nhập, có 2 Artisan | Thêm tin loại Hạ tầng & Vận hành, giữ Không công khai, Lưu nháp rồi Đăng | Tin Đã đăng, ghi ngày đăng và người đăng; cả 2 Artisan nhận NT-UC27-01 và đọc được trong Kênh người bán; Guest không thấy |
| TC-27.2 | UC-27-AC-02 · Alt-01 | Form tin | Để trống tiêu đề, hoặc nhập tiêu đề 151 ký tự, hoặc nội dung 10001 ký tự chữ, rồi chọn Lưu nháp | Hiển thị ERR-UC27-01 tại trường sai; dữ liệu đã nhập được giữ; không lưu |
| TC-27.3 | UC-27-AC-03 · Alt-02 | Form tin | Tải ảnh bìa PDF, hoặc ảnh lớn hơn 10 MB, hoặc chọn 2 ảnh bìa, hoặc chèn 11 ảnh trong bài | Hiển thị ERR-UC27-02; ảnh không được nhận |
| TC-27.4 | UC-27-AC-04 · Alt-03 | Admin đang soạn tin, chưa lưu, đã tải 1 ảnh trong bài và 1 tệp PDF | Chọn Xem trước, chọn Quay lại sửa, rồi Xem trước và Đăng | Xem trước hiện đúng như người đọc, kèm nhãn Công khai hoặc Nội bộ, ảnh trong bài hiển thị và tệp tải về được, mà chưa lưu hay công khai; Quay lại sửa giữ dữ liệu; Đăng từ xem trước thì đăng như bước 5 |
| TC-27.5 | UC-27-AC-05 · Alt-04, BR-6 | Tin Nháp thiếu nội dung và loại tin | Chọn Đăng | Hiển thị ERR-UC27-03 liệt kê mục thiếu; tin vẫn Nháp; không có thông báo |
| TC-27.6 | UC-27-AC-06 · Alt-05, BR-3 | Tin Nháp đủ thông tin, Công khai là Có | Chọn Đăng rồi chọn Quay lại ở hộp xác nhận; sau đó Đăng lại và xác nhận | Lần đầu tin vẫn Nháp, không thông báo; lần sau tin Đã đăng và hiện ở trang chủ |
| TC-27.7 | UC-27-AC-07 · Alt-06, BR-9, BR-6 | Tin Đã đăng | Admin sửa nội dung rồi Lưu | Nội dung mới hiện ngay cho người đọc; ngày đăng không đổi; không có thông báo mới; người đọc thấy "Cập nhật lúc". Sửa xóa hết nội dung thì hiện ERR-UC27-03 và không lưu. Đổi tiêu đề thì địa chỉ tin theo tiêu đề mới và link cũ vẫn mở được (UC-28-AC-20) |
| TC-27.8 | UC-27-AC-08 · Alt-07, BR-3 | Tin Đã đăng nội bộ | Admin đổi sang Công khai, xác nhận, Lưu; sau đó đổi lại sang Không công khai và Lưu | Sang Công khai: hiện ở trang chủ, Guest đọc được. Sang Nội bộ: biến khỏi trang chủ; Guest mở link thấy ERR-UC28-01; Artisan vẫn đọc được. Cả hai lần không có thông báo mới |
| TC-27.9 | UC-27-AC-09 · Alt-08, BR-14 | Tin công khai Đã đăng | Admin chọn Ẩn và xác nhận | Tin biến khỏi Kênh người bán, trang chủ, mục Tin tức; Guest và Artisan mở link thấy ERR-UC28-01; Admin vẫn thấy tin ở trạng thái Ẩn; thông báo cũ của Artisan vẫn còn trong chuông |
| TC-27.10 | UC-27-AC-10 · Alt-09, BR-8 | Tin Ẩn đăng lần đầu ngày D1, đăng lại ngày D2 | Admin chọn Đăng lại | Tin hiện lại; ngày đăng vẫn là D1; thứ tự trong danh sách người đọc không đổi; không có thông báo mới |
| TC-27.11 | UC-27-AC-11 · Alt-10, BR-5 | Tin Nháp | Admin chọn Xóa và xác nhận | Tin biến khỏi danh sách |
| TC-27.12 | UC-27-AC-12 · Alt-11, BR-5 | Tin Đã đăng, hoặc tin Ẩn | Admin chọn Xóa | Hiển thị ERR-UC27-04; tin giữ nguyên |
| TC-27.13 | UC-27-AC-13 · Alt-12, BR-12, BR-7 | Hai Admin cùng mở một tin Nháp; Admin A bấm Đăng rồi Admin B bấm Đăng | Hai thao tác gần như đồng thời | Tin Đã đăng một lần; mỗi Artisan nhận đúng một NT-UC27-01; Admin B thấy ERR-UC27-05 và dữ liệu được tải lại |
| TC-27.14 | UC-27-AC-14 · Alt-13, BR-13 | Tài khoản Buyer, Artisan hoặc Guest | Gửi thao tác tạo, sửa, đăng, ẩn hoặc xóa tin trực tiếp | Hiển thị ERR-UC27-06; dữ liệu không đổi. Mở Trang quản trị thì ERR-UC04-06 |
| TC-27.15 | UC-27-AC-15 · Alt-14 | API danh sách lỗi | Admin lọc theo trạng thái | Hiển thị ERR-UC27-07; danh sách và bộ lọc đang chọn được giữ; có nút Thử lại |
| TC-27.16 | UC-27-AC-16 · Alt-15 | Chưa có tin nào; hoặc có tin nhưng bộ lọc không khớp | Admin mở mục Tin tức; hoặc lọc | Hiện "Chưa có tin nào. Chọn Thêm tin để bắt đầu."; hoặc "Không có tin phù hợp" và nút Xóa bộ lọc |
| TC-27.17 | UC-27-AC-17 · BR-1, BR-4 | Có tin Nháp, Đã đăng, Ẩn | Admin mở danh sách | Thấy cả ba trạng thái; Guest, Buyer, Artisan không thấy tin Nháp và Ẩn |
| TC-27.18 | UC-27-AC-18 · BR-2 | Form tin | Admin mở danh sách Loại tin và tìm cách thêm loại mới | Chỉ có năm loại cố định (Thị trường & Nguyên liệu, Hạ tầng & Vận hành, Chính sách & Quy định, Hoạt động & Sự kiện làng nghề, Thông báo chung); không có thao tác thêm hoặc sửa loại; mỗi tin chọn đúng một loại |
| TC-27.19 | UC-27-AC-19 · BR-3 | Admin thêm tin mới | Không chạm vào Công khai rồi Đăng | Công khai mặc định Không; tin chỉ Artisan đọc, không có ở trang chủ |
| TC-27.20 | UC-27-AC-20 · BR-4 | Tin Đã đăng hoặc Ẩn | Admin tìm cách đưa về Nháp | Không có thao tác nào làm được |
| TC-27.21 | UC-27-AC-21 · BR-6 | Form tin chỉ có tiêu đề | Chọn Lưu nháp; sau đó chọn Đăng | Lưu nháp thành công; Đăng bị ERR-UC27-03 nêu thiếu loại tin và nội dung |
| TC-27.22 | UC-27-AC-22 · BR-7 | Có Artisan A, B và Buyer C, Admin | Admin đăng một tin; sau đó sửa, đổi phạm vi, ẩn, đăng lại tin đó | A và B mỗi người nhận đúng một NT-UC27-01 ở lần đăng đầu; C và Admin không nhận; các thao tác sau không phát thêm thông báo |
| TC-27.23 | UC-27-AC-23 · BR-7 | Chưa có Artisan nào; sau đó Admin duyệt Artisan D | Admin đăng một tin; Artisan D mở mục Tin tức | Tin vẫn Đã đăng, không có thông báo; D đọc được tin cũ nhưng không nhận NT-UC27-01 cho tin đó |
| TC-27.24 | UC-27-AC-24 · BR-10 | Nội dung tin có đoạn in đậm, một bảng, một ảnh chèn trong bài và chuỗi `<script>alert(1)</script>` | Admin đăng; Guest mở tin công khai | In đậm, bảng và ảnh hiển thị đúng vị trí đã soạn; chuỗi script bị loại hoặc hiện như chữ, không chạy mã |
| TC-27.25 | UC-27-AC-25 · BR-11 | Tin có ảnh bìa và ảnh trong bài hợp lệ | Admin đăng rồi Guest xem danh sách và chi tiết tin | Danh sách dùng ảnh bìa đã thu nhỏ; trang tin dùng ảnh trong bài đã thu nhỏ, không tải ảnh gốc |
| TC-27.26 | UC-27-AC-26 · Alt-16 | Form tin | Tải tệp .exe, hoặc PDF 11 MB, hoặc tệp đính kèm thứ 6 | Hiển thị ERR-UC27-08; tệp không được nhận; các tệp hợp lệ đã tải vẫn giữ |
| TC-27.27 | UC-27-AC-27 · BR-15 | Tin nội bộ Đã đăng có 1 ảnh trong bài và 1 tệp PDF | Guest mở link trực tiếp tới ảnh và tệp; Artisan tải tệp; sau đó Admin Ẩn tin và Artisan mở lại link | Guest bị từ chối; Artisan tải được tệp; sau khi Ẩn, link ảnh và tệp bị từ chối với Artisan |
| TC-27.28 | UC-27-AC-28 · Main Flow | Có 45 tin | Admin mở mục Tin tức, chọn trang 3 | Trang 1 có 20 tin, trang 3 có 5 tin; đổi trang không tải lại toàn trang |
| TC-27.29 | UC-27-AC-29 · BR-15 | Tin Nháp và tin Ẩn, mỗi tin có 1 ảnh trong bài và 1 tệp PDF | Admin đã đăng nhập mở ảnh và tải tệp (từ form sửa, xem trước hoặc link trực tiếp); Guest, Buyer, Artisan mở cùng các link | Admin mở được ảnh và tải được tệp của cả hai tin; Guest, Buyer, Artisan bị từ chối bằng ERR-UC28-01 |

## UC-28 — Đọc tin tức

| TC | AC và quy tắc | Chuẩn bị | Thao tác | Kết quả mong đợi |
|---|---|---|---|---|
| TC-28.1 | UC-28-AC-01 · Main Flow, BR-5, BR-9 | Có 25 tin công khai Đã đăng, thuộc nhiều loại | Guest mở mục Tin tức, chọn lọc Thị trường & Nguyên liệu, bỏ lọc, chọn trang 3, mở một tin | Trang 1 có 10 tin mới nhất trước; lọc chỉ còn tin loại Thị trường & Nguyên liệu và về trang 1; trang 3 có 5 tin; chi tiết hiện đủ tiêu đề, loại, ngày đăng, nội dung |
| TC-28.2 | UC-28-AC-02 · Alt-01, BR-1 | Có tin công khai và tin nội bộ Đã đăng; Artisan đã đăng nhập | Artisan mở mục Tin tức trong Kênh người bán, lọc theo loại, mở một tin nội bộ | Thấy cả hai loại tin, mỗi thẻ có nhãn Công khai hoặc Nội bộ; chi tiết mở trong Kênh người bán kèm nhãn |
| TC-28.3 | UC-28-AC-03 · Alt-02 | Admin vừa đăng một tin; Artisan có NT-UC27-01 trong chuông | Artisan chọn thông báo | Mở đúng chi tiết tin đó trong Kênh người bán |
| TC-28.4 | UC-28-AC-04 · Alt-03, BR-4 | Trang chủ có khối Tin công khai | Guest chọn một tin ở khối; sau đó chọn Xem tất cả | Mở đúng chi tiết tin; Xem tất cả mở danh sách tin công khai trang 1, mới nhất trước |
| TC-28.5 | UC-28-AC-05 · Alt-04 | Chưa có tin nào; hoặc chưa có tin loại Chính sách & Quy định | Guest mở mục Tin tức; hoặc lọc Chính sách & Quy định | Hiện "Chưa có tin nào"; hoặc "Chưa có tin thuộc loại này" kèm nút Bỏ lọc; Bỏ lọc thì hiện lại danh sách |
| TC-28.6 | UC-28-AC-06 · Alt-05, BR-2 | Tin Nháp, tin Ẩn, tin nội bộ và địa chỉ tin không tồn tại | Guest mở lần lượt từng link trực tiếp; Artisan mở thông báo của tin vừa bị Ẩn | Mọi trường hợp hiện cùng ERR-UC28-01 và nút Về danh sách tin; không cho biết tin có tồn tại hay không |
| TC-28.7 | UC-28-AC-07 · Alt-06 | API danh sách hoặc chi tiết lỗi | Guest lọc loại tin hoặc mở một tin | Hiển thị ERR-UC28-02; danh sách và bộ lọc đang chọn được giữ; có nút Thử lại |
| TC-28.8 | UC-28-AC-08 · BR-1 | Có tin công khai và tin nội bộ Đã đăng | Guest, Buyer và Artisan lần lượt xem danh sách tin | Guest và Buyer chỉ thấy tin công khai ở Khu mua hàng; Artisan thấy cả hai ở Kênh người bán |
| TC-28.9 | UC-28-AC-09 · BR-3 | Guest chưa đăng nhập; Buyer chưa có hồ sơ bán hàng; Buyer có hồ sơ Chờ duyệt | Mở đường dẫn Tin tức trong Kênh người bán | Guest được chuyển sang đăng nhập; Buyer chưa có hồ sơ gặp ERR-UC04-03; Buyer có hồ sơ chưa duyệt gặp ERR-UC04-04; không ai thấy nội dung tin nội bộ |
| TC-28.10 | UC-28-AC-10 · BR-4 | Có 5 tin công khai và 2 tin nội bộ Đã đăng | Guest mở trang chủ | Khối tin chỉ có 3 tin công khai mới nhất; không có tin nội bộ |
| TC-28.11 | UC-28-AC-11 · BR-5 | Có 45 tin công khai (5 trang) | Guest chọn trang 4, rồi sao chép địa chỉ và mở ở tab mới; lọc một loại; sửa địa chỉ thành trang 99 | Mỗi trang 10 tin mới nhất trước theo ngày đăng; địa chỉ có số trang nên mở lại đúng trang 4; đổi loại tin thì về trang 1; trang 99 hiện trang cuối; chuyển trang và lọc không tải lại toàn trang |
| TC-28.12 | UC-28-AC-12 · BR-6 | Tin có tiêu đề "Lịch cắt điện tuần 47" (mã tin 1024) | Guest mở trang chi tiết | Địa chỉ có dạng `lich-cat-dien-tuan-47-1024`: chữ thường, không dấu, nối bằng dấu gạch ngang, kết thúc bằng mã tin |
| TC-28.13 | UC-28-AC-13 · BR-7 | Guest đang mở danh sách có tin X; Admin Ẩn tin X | Guest chưa làm mới; sau đó mở tin X; rồi làm mới danh sách | Trước khi làm mới danh sách vẫn còn thẻ tin X; mở tin X hiện ERR-UC28-01; sau khi làm mới tin X không còn |
| TC-28.14 | UC-28-AC-14 · BR-8 | Guest chưa đăng nhập; Buyer đã đăng nhập | Mở mục Tin tức và một tin công khai | Cả hai đọc được, không bị chuyển sang đăng nhập; Buyer thấy giống Guest |
| TC-28.15 | UC-28-AC-15 · BR-9 | Tin đã sửa sau khi đăng; Artisan và Guest cùng mở | Xem chi tiết | Có dòng "Cập nhật lúc"; Artisan thấy thêm nhãn Công khai hoặc Nội bộ, Guest không có nhãn này; tin chưa sửa thì không có "Cập nhật lúc" |
| TC-28.16 | UC-28-AC-16 · BR-10 | Artisan mở nhiều tin; Admin xem quản trị | Artisan đọc xong, Admin kiểm tra | Không có đánh dấu đã đọc hoặc chưa đọc theo từng tin; Admin không thấy ai đã đọc |
| TC-28.17 | UC-28-AC-17 · BR-11 | Ba Artisan có gian hàng Đang chuẩn bị, Đang hoạt động, Tạm ngừng | Mỗi người mở mục Tin tức trong Kênh người bán | Cả ba thấy cùng danh sách tin |
| TC-28.18 | UC-28-AC-18 · BR-9, BR-12 | Tin công khai có 1 ảnh trong bài và 1 tệp PDF | Guest mở chi tiết và chọn Tải về; sau đó Admin Ẩn tin và Guest mở lại link tệp | Chi tiết hiện ảnh trong bài và dòng tệp (tên, dung lượng, Tải về); tải được tệp; sau khi Ẩn link tệp bị từ chối bằng ERR-UC28-01 với Guest, còn Admin vẫn tải được (UC-27-AC-29) |
| TC-28.19 | UC-28-AC-19 · BR-6 | Hai tin công khai cùng tiêu đề "Thông báo lễ Tết" (mã 1030 và 1031); một tiêu đề chỉ có ký tự đặc biệt "!!!" (mã 1032); một tiêu đề dài hơn 80 ký tự | Guest mở lần lượt từng tin | Hai tin có hai địa chỉ khác nhau (`thong-bao-le-tet-1030`, `thong-bao-le-tet-1031`) và mở đúng từng tin; tin "!!!" có địa chỉ `tin-tuc-1032`; phần chữ của tin dài tối đa 80 ký tự, không cắt giữa từ |
| TC-28.20 | UC-28-AC-20 · Alt-07, BR-6 | Tin công khai Đã đăng có địa chỉ `lich-cat-dien-tuan-47-1024`; Admin đổi tiêu đề thành "Lịch cắt điện tuần 48" | Guest mở địa chỉ cũ; Guest mở địa chỉ cũ sau khi Admin Ẩn tin; Guest mở `sai-chu-1024` | Địa chỉ cũ chuyển hướng sang `lich-cat-dien-tuan-48-1024` và hiện đúng tin; tin Ẩn hiện ERR-UC28-01 và không chuyển hướng, không lộ tiêu đề; `sai-chu-1024` khi tin còn công khai cũng chuyển hướng về địa chỉ hiện tại |
