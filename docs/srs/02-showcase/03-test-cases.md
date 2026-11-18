---
title: "02 — Triển lãm số — Ca nghiệm thu"
code: SRS-02
type: srs
status: official
version: 0.4.0
date: 2026-11-18
---

# 02 — Triển lãm số — Ca nghiệm thu

[BRD 0.2.0](../../brd/02-showcase/usecase.md) · [ERD](../../design/erd.md) · [Phụ thuộc liên nhóm](../README.md) · [Kiến trúc và giao diện](01-architecture.md) · [Đặc tả chức năng](02-feature-description.md)

Các ca dưới đây truy vết tới AC của BRD. Chạy qua giao diện hoặc API tương ứng khi có ứng dụng.

## UC-06 — Xem trang chủ, giới thiệu làng nghề

| TC | AC và quy tắc | Chuẩn bị | Thao tác | Kết quả mong đợi |
|---|---|---|---|---|
| TC-6.1 | UC-06-AC-01 · Main Flow | Có gian hàng đã mở, sản phẩm Hiển thị và tin công khai | Guest mở trang chủ | Hiện giới thiệu làng nghề, các khối phân loại, xưởng, sản phẩm, tin; chọn lối vào thì chuyển đúng màn hình |
| TC-6.2 | UC-06-AC-02 · Alt-01 | Khối sản phẩm tải lỗi | Guest xem trang chủ | Khối đó hiện ERR-UC06-01 và nút Thử lại; các khối khác vẫn hiển thị |
| TC-6.3 | UC-06-AC-03 · Alt-02 | Chưa có xưởng nào mở gian hàng | Guest mở trang chủ | Khối xưởng hiện "Sắp có nội dung"; các khối còn lại hiển thị bình thường |
| TC-6.4 | UC-06-AC-04 · Alt-03 | Người dùng đăng nhập có quyền bán | Mở trang chủ | Menu có nút Kênh người bán |
| TC-6.5 | UC-06-AC-05 · BR-1 | Có gian hàng Đang chuẩn bị và sản phẩm Ẩn | Guest mở trang chủ | Xưởng và sản phẩm đó không xuất hiện |
| TC-6.6 | UC-06-AC-06 · BR-2 | Có tin nội bộ và tin công khai | Guest mở trang chủ | Chỉ thấy tin công khai |
| TC-6.7 | UC-06-AC-07 · BR-3 | Một khối phản hồi chậm | Guest mở trang chủ | Các khối khác hiển thị trước, không phải chờ |
| TC-6.8 | UC-06-AC-08 · BR-4 | Bất kỳ người dùng nào | Mở trang chủ | Giới thiệu làng nghề giống nhau; không có thao tác sửa nội dung này |
| TC-6.9 | UC-06-AC-09 · BR-5 | Xưởng A mở gian hàng trước xưởng B; A tạm ngừng rồi mở lại | Guest mở trang chủ | B đứng trước A trong khối Xưởng mới mở; mở lại không đưa A lên đầu |
| TC-6.10 | UC-06-AC-10 · Main Flow | Guest ở trang chủ | Chọn Đặt hàng theo yêu cầu | Chuyển sang UC-18 (gửi chung); Guest chưa đăng nhập thì đăng nhập trước rồi quay lại |
| TC-6.11 | UC-06-AC-11 · Main Flow, BR-6 | Có 25 tin công khai Đã đăng | Guest mở trang chủ, rồi chọn "Xem tất cả" ở khối tin | Khối tin chỉ hiện 3 tin mới nhất; "Xem tất cả" mở danh sách tin công khai ở trang 1, mới nhất trước, từ đó chọn được trang 2, 3 để đọc tin cũ hơn (UC-28-AC-04) |
| TC-6.12 | UC-06-AC-12 · BR-6 | Guest chưa đăng nhập, ở trang chủ hoặc một trang bất kỳ của Khu mua hàng | Chọn mục Tin tức ở menu | Mục Tin tức có ở menu; mở danh sách tin công khai ở trang 1 mà không bị chuyển sang đăng nhập |
| TC-6.13 | UC-06-AC-13 · Alt-02, BR-6 | Chưa có tin công khai nào | Guest mở trang chủ, rồi chọn mục Tin tức ở menu | Khối tin hiện "Sắp có nội dung", không có nút "Xem tất cả"; mục Tin tức mở danh sách trống theo UC-28 (Alt-04) |

## UC-07 — Quản lý thông tin gian hàng

| TC | AC và quy tắc | Chuẩn bị | Thao tác | Kết quả mong đợi |
|---|---|---|---|---|
| TC-7.1 | UC-07-AC-01 · Main Flow | Artisan đã đăng nhập, gian hàng bất kỳ | Sửa mô tả và phương thức giao rồi chọn Lưu | Thay đổi được lưu, hiển thị ngay |
| TC-7.2 | UC-07-AC-02 · Alt-01 | Artisan để trống tên xưởng | Chọn Lưu | Hiển thị ERR-UC07-01; dữ liệu đã nhập được giữ; không lưu |
| TC-7.3 | UC-07-AC-03 · Alt-02 | Artisan chọn ảnh PDF hoặc lớn hơn 10 MB | Tải ảnh | Hiển thị ERR-UC07-02; ảnh không được nhận |
| TC-7.4 | UC-07-AC-04 · Alt-03, BR-2 | Gian hàng đã duyệt, không có yêu cầu chờ | Đổi tên xưởng rồi Lưu | Tên cũ vẫn hiển thị công khai; yêu cầu chờ duyệt; Trưởng BQL và Admin có quyền Kiểm duyệt & Danh mục nhận NT-UC07-01 |
| TC-7.5 | UC-07-AC-05 · Alt-04, BR-3 | Đang có yêu cầu đổi tên chờ duyệt | Đổi địa chỉ rồi Lưu | Hiển thị ERR-UC07-03; không tạo yêu cầu thứ hai |
| TC-7.6 | UC-07-AC-06 · Alt-05, BR-6 | Gian hàng có sản phẩm có sẵn Hiển thị | Tắt cả ba phương thức giao | Hiển thị ERR-UC07-04; thiết lập cũ giữ nguyên |
| TC-7.7 | UC-07-AC-07 · Alt-06, BR-1 | Tài khoản Buyer chưa có quyền bán, hoặc Artisan khác | Mở hoặc gửi thay đổi gian hàng này | Hiển thị ERR-UC07-05; dữ liệu không đổi |
| TC-7.8 | UC-07-AC-08 · BR-4 | Gian hàng đang Tạm ngừng | Sửa mô tả rồi Lưu | Lưu thành công |
| TC-7.9 | UC-07-AC-09 · BR-5 | Gian hàng có chứng nhận | Guest xem trang gian hàng | Mỗi chứng nhận hiện nhãn "Do xưởng cung cấp" hoặc "BQL đã xác minh" |
| TC-7.10 | UC-07-AC-10 · BR-7 | Artisan tải ảnh hợp lệ | Lưu rồi xem danh sách xưởng | Danh sách dùng ảnh đã thu nhỏ, không tải ảnh gốc |
| TC-7.11 | UC-07-AC-11 · BR-8 | Hồ sơ UC-01 đã được duyệt | Artisan mở mục Gian hàng lần đầu | Tên, địa chỉ, ảnh xưởng, năm bắt đầu đã được điền sẵn |
| TC-7.12 | UC-07-AC-12 · BR-9 | Chứng nhận đã có nhãn "BQL đã xác minh" | Artisan thay ảnh chứng nhận rồi Lưu | Nhãn về "Do xưởng cung cấp"; chứng nhận chờ Admin xác minh lại |
| TC-7.13 | UC-07-AC-13 · Alt-07 | Artisan đang sửa thông tin, chưa lưu | Chọn Xem trước, chọn Quay lại sửa, rồi chọn Xem trước và Lưu | Xem trước hiện dữ liệu đang nhập mà chưa lưu hay công khai; Quay lại sửa giữ dữ liệu; Lưu từ xem trước thì lưu như bước 5 |
| TC-7.14 | UC-07-AC-14 · Alt-01, BR-10 | Artisan nhập 4 số điện thoại, hoặc hai số trùng nhau | Chọn Lưu | Hiển thị ERR-UC07-01 tại trường sai; không lưu. Nhập 1–3 số hợp lệ thì lưu được, Guest xem được các số này ở trang gian hàng; số trong hồ sơ bán hàng không hiện |

## UC-08 — Khám phá xưởng

| TC | AC và quy tắc | Chuẩn bị | Thao tác | Kết quả mong đợi |
|---|---|---|---|---|
| TC-8.1 | UC-08-AC-01 · Main Flow | Có nhiều gian hàng đã mở | Guest mở mục Xưởng, tìm tên, lọc phân loại, mở một xưởng | Danh sách lọc đúng; trang gian hàng hiện đủ thông tin và sản phẩm Hiển thị |
| TC-8.2 | UC-08-AC-02 · Alt-01 | Không xưởng nào khớp | Guest nhập tên không tồn tại | Hiện "Không tìm thấy xưởng phù hợp" và nút Xóa bộ lọc |
| TC-8.3 | UC-08-AC-03 · Alt-02, BR-1 | Gian hàng Đang chuẩn bị | Guest mở link trực tiếp | Hiển thị ERR-UC08-01 |
| TC-8.4 | UC-08-AC-04 · Alt-03 | API danh sách lỗi | Guest lọc phân loại | Hiển thị ERR-UC08-02; bộ lọc được giữ; có nút Thử lại |
| TC-8.5 | UC-08-AC-05 · Alt-04, BR-2 | Gian hàng Tạm ngừng | Guest mở trang gian hàng | Có dòng tạm ngừng; nút Gửi yêu cầu báo giá bị vô hiệu; xưởng vẫn nằm trong danh sách |
| TC-8.6 | UC-08-AC-06 · Alt-05, BR-7 | Artisan mở gian hàng của mình đang Đang chuẩn bị | Xem trang gian hàng | Thấy đầy đủ, có dòng "Chỉ bạn xem được"; không có nút Gửi yêu cầu báo giá |
| TC-8.7 | UC-08-AC-07 · BR-3 | Xưởng A có sản phẩm Hiển thị thuộc phân loại X | Guest lọc phân loại X | Xưởng A có trong kết quả; xưởng chỉ có sản phẩm Ẩn thì không |
| TC-8.8 | UC-08-AC-08 · BR-4 | Xưởng đang bật Đang đón khách và đang Tạm ngừng | Guest lọc Đang đón khách | Xưởng vẫn có trong kết quả |
| TC-8.9 | UC-08-AC-09 · BR-5 | Gian hàng có chứng nhận | Guest xem gian hàng | Mỗi chứng nhận có nhãn nguồn |
| TC-8.10 | UC-08-AC-10 · BR-6 | Gian hàng "Xưởng mây tre Bình An" | Guest mở trang gian hàng | URL dạng chữ dễ đọc, không dùng số id |
| TC-8.11 | UC-08-AC-11 · BR-8 | Tên xưởng "Bình An" | Guest nhập "binh an" | Xưởng có trong kết quả |
| TC-8.12 | UC-08-AC-12 · BR-9 | Guest đang lọc | Đổi bộ lọc | Danh sách cập nhật mà trang không tải lại |
| TC-8.13 | UC-08-AC-13 · Alt-06 | Guest chưa đăng nhập đang xem gian hàng | Chọn Gửi yêu cầu báo giá | Chuyển sang đăng nhập; sau đó quay lại đúng gian hàng |
| TC-8.14 | UC-08-AC-14 · BR-10 | Xưởng có sản phẩm thuộc ba phân loại | Guest xem danh sách xưởng và trang gian hàng | Thẻ xưởng không hiện phân loại; trang gian hàng hiện đủ ba phân loại |
| TC-8.15 | UC-08-AC-15 · Main Flow, BR-11 | Guest đã đăng nhập xem gian hàng đang hoạt động | Chọn Gửi yêu cầu báo giá | Chuyển sang UC-18 kèm xưởng đang xem, không kèm sản phẩm |

## UC-09 — Quản lý sản phẩm

| TC | AC và quy tắc | Chuẩn bị | Thao tác | Kết quả mong đợi |
|---|---|---|---|---|
| TC-9.1 | UC-09-AC-01 · Main Flow | Artisan đã đăng nhập; có phân loại Hiển thị | Tạo sản phẩm đủ thông tin, Lưu nháp rồi Cho hiển thị | Sản phẩm ở trạng thái Hiển thị và hiện trong danh sách |
| TC-9.2 | UC-09-AC-02 · Alt-01 | Tên sản phẩm để trống | Chọn Lưu nháp | Hiển thị ERR-UC09-01; dữ liệu được giữ; không lưu |
| TC-9.3 | UC-09-AC-03 · Alt-02 | Ảnh PDF hoặc lớn hơn 10 MB | Tải ảnh | Hiển thị ERR-UC09-02; ảnh không được nhận |
| TC-9.4 | UC-09-AC-04 · Alt-03, BR-6 | Sản phẩm Nháp thiếu ảnh | Chọn Cho hiển thị | Hiển thị ERR-UC09-03 liệt kê mục thiếu; vẫn Nháp |
| TC-9.5 | UC-09-AC-05 · Alt-04, BR-4 | Sản phẩm Hiển thị đã có đơn | Đổi giá rồi Lưu | Giá mới áp dụng cho sản phẩm; đơn cũ giữ giá cũ |
| TC-9.6 | UC-09-AC-06 · Alt-05, BR-2 | Sản phẩm Hiển thị, gian hàng đã mở, có lô | Chọn Ẩn | Không còn trong tìm kiếm, bản đồ, link trực tiếp; trong giỏ ở nhóm "Không khả dụng"; trang QR của lô vẫn xem được, link sản phẩm thay bằng "Sản phẩm hiện không bán" |
| TC-9.7 | UC-09-AC-07 · Alt-06, BR-1 | Sản phẩm Ẩn đủ thông tin | Chọn Hiển thị lại | Sản phẩm công khai lại |
| TC-9.8 | UC-09-AC-08 · Alt-07, BR-5 | Sản phẩm Nháp chưa có lô | Chọn Xóa và xác nhận | Sản phẩm bị xóa khỏi danh sách |
| TC-9.9 | UC-09-AC-09 · Alt-08, BR-5 | Sản phẩm đã từng Hiển thị, hoặc Nháp đã có lô | Chọn Xóa | Hiển thị ERR-UC09-04; sản phẩm giữ nguyên |
| TC-9.10 | UC-09-AC-10 · Alt-09, BR-8 | Artisan khác | Gửi thao tác sửa sản phẩm này | Hiển thị ERR-UC09-05; dữ liệu không đổi |
| TC-9.11 | UC-09-AC-11 · BR-2 | Gian hàng Đang chuẩn bị | Cho sản phẩm hiển thị | Sản phẩm ở Hiển thị nhưng chưa xuất hiện công khai |
| TC-9.12 | UC-09-AC-12 · BR-3 | Có hai loại sản phẩm | Tạo sản phẩm loại Chỉ nhận đặt riêng | Không bắt nhập giá, số lượng, cân nặng, kích thước; lưu và cho hiển thị được |
| TC-9.13 | UC-09-AC-13 · BR-7 | Sản phẩm có sẵn, số lượng 0 | Guest xem sản phẩm | Hiện "Hết hàng"; không thêm được vào giỏ |
| TC-9.14 | UC-09-AC-14 · BR-9 | Artisan tải ảnh hợp lệ | Lưu rồi xem danh sách sản phẩm | Danh sách dùng ảnh đã thu nhỏ |
| TC-9.15 | UC-09-AC-15 · Alt-10 | Chưa có phân loại nào đang Hiển thị | Artisan tạo sản phẩm, Lưu nháp rồi Cho hiển thị | Lưu nháp được và hiện ERR-UC09-06 ở ô Phân loại; Cho hiển thị bị ERR-UC09-03 nêu thiếu phân loại |
| TC-9.16 | UC-09-AC-16 · Main Flow, BR-6 | Sản phẩm Nháp chưa chọn phân loại | Lưu nháp, rồi chọn phân loại và Cho hiển thị | Lưu nháp thành công không cần phân loại; Cho hiển thị thành công sau khi có phân loại |
| TC-9.17 | UC-09-AC-17 · Alt-11, BR-10 | Sản phẩm Hiển thị loại Chỉ nhận đặt riêng, gian hàng có phương thức giao | Đổi sang Có sẵn, nhập giá và số lượng, Lưu | Sản phẩm thành Có sẵn, thêm vào giỏ được; yêu cầu báo giá đã gửi trước đó giữ nguyên |
| TC-9.18 | UC-09-AC-18 · Alt-12, BR-10 | Sản phẩm Có sẵn đã từng Hiển thị | Đổi sang Chỉ nhận đặt riêng | Hiển thị ERR-UC09-07; loại không đổi. Sản phẩm Nháp thì đổi được hai chiều |
| TC-9.19 | UC-09-AC-19 · Alt-13, BR-10 | Gian hàng chưa bật phương thức giao nào, sản phẩm đặt riêng đang Hiển thị | Đổi sang Có sẵn | Hiển thị ERR-UC09-08; sản phẩm giữ loại cũ |
| TC-9.20 | UC-09-AC-20 · BR-11 | Sản phẩm Chỉ nhận đặt riêng đang Hiển thị | Guest mở sản phẩm | Không có Thêm vào giỏ; gửi được yêu cầu báo giá kèm sản phẩm này, riêng xưởng của sản phẩm |

## UC-10 — Xem danh sách, tìm kiếm, lọc sản phẩm

| TC | AC và quy tắc | Chuẩn bị | Thao tác | Kết quả mong đợi |
|---|---|---|---|---|
| TC-10.1 | UC-10-AC-01 · Main Flow | Có sản phẩm công khai | Mở mục Sản phẩm, gõ "giỏ", chọn gợi ý, lọc phân loại, sắp xếp giá tăng, mở một sản phẩm | Kết quả khớp, đúng thứ tự; mở được chi tiết sản phẩm |
| TC-10.2 | UC-10-AC-02 · Alt-01 | Không sản phẩm nào khớp | Tìm từ khóa lạ | Hiện "Không tìm thấy sản phẩm phù hợp" và nút Xóa bộ lọc |
| TC-10.3 | UC-10-AC-03 · Alt-02 | API kết quả lỗi | Đổi bộ lọc | Hiển thị ERR-UC10-01; kết quả cũ được giữ; có nút Thử lại |
| TC-10.4 | UC-10-AC-04 · Alt-03 | Bật lọc khoảng giá | Nhập giá từ lớn hơn giá đến | Hiển thị ERR-UC10-02; bộ lọc giá không áp dụng |
| TC-10.5 | UC-10-AC-05 · Alt-04 | Kết quả có sản phẩm Chỉ nhận đặt riêng | Sắp xếp theo giá | Sản phẩm không giá ở cuối và ghi "Theo báo giá" |
| TC-10.6 | UC-10-AC-06 · BR-1 | Có sản phẩm Ẩn, sản phẩm của gian hàng Đang chuẩn bị và của gian hàng Tạm ngừng | Mở trang Sản phẩm | Hai loại đầu không xuất hiện; sản phẩm của gian hàng Tạm ngừng vẫn xuất hiện |
| TC-10.7 | UC-10-AC-07 · BR-2 | Chưa chọn sắp xếp | Mở trang Sản phẩm | Sản phẩm mới nhất xếp trước |
| TC-10.8 | UC-10-AC-08 · BR-3 | Sản phẩm "Giỏ mây" | Gõ "gio may" | Sản phẩm có trong kết quả |
| TC-10.9 | UC-10-AC-09 · BR-4 | Người dùng đổi bộ lọc | Thao tác lọc | Danh sách cập nhật mà trang không tải lại |
| TC-10.10 | UC-10-AC-10 · BR-5 | Có hơn 12 sản phẩm | Cuộn hoặc chọn Xem thêm | Mỗi lần tải thêm 12 sản phẩm |
| TC-10.11 | UC-10-AC-11 · BR-6 | Đã lọc phân loại X | Sao chép URL và mở lại | Cùng bộ lọc được áp dụng; URL dễ đọc |
| TC-10.12 | UC-10-AC-12 · Alt-05 | Guest đang ở trang chủ | Gõ "giỏ" vào ô tìm trên đầu trang và bấm Tìm | Chuyển tới trang Sản phẩm với từ khóa "giỏ" đã áp |
| TC-10.13 | UC-10-AC-13 · Alt-06 | Chưa có sản phẩm công khai nào | Mở mục Sản phẩm | Hiện "Sắp có sản phẩm"; không có ô lọc kết quả |
| TC-10.14 | UC-10-AC-14 · BR-7 | Có sản phẩm "Giỏ mây" và xưởng "Bình An" | Gõ "binh an" vào ô tìm | Gợi ý chỉ gồm tên sản phẩm, không có gợi ý xưởng |
| TC-10.15 | UC-10-AC-15 · BR-8 | Có sản phẩm công khai | Mở mục Sản phẩm, chưa gõ từ khóa, chưa lọc | Hiện toàn bộ sản phẩm công khai, mới nhất trước |
| TC-10.16 | UC-10-AC-16 · Main Flow, BR-8 | Guest ở trang chủ, có phân loại X | Chọn phân loại X | Chuyển tới trang Sản phẩm đã lọc sẵn phân loại X |

## UC-11 — Xem chi tiết sản phẩm

| TC | AC và quy tắc | Chuẩn bị | Thao tác | Kết quả mong đợi |
|---|---|---|---|---|
| TC-11.1 | UC-11-AC-01 · Main Flow | Sản phẩm có sẵn công khai, có lô | Guest mở trang, xem ảnh, xem lô, thêm giỏ | Thông tin đầy đủ; lô mới nhất trước; sản phẩm vào giỏ |
| TC-11.2 | UC-11-AC-02 · Alt-01, BR-1 | Sản phẩm Ẩn | Guest mở link trực tiếp | Hiển thị ERR-UC11-01 |
| TC-11.3 | UC-11-AC-03 · Alt-02, BR-2 | Artisan xem sản phẩm của xưởng mình | Mở trang | Không có nút Thêm vào giỏ và Gửi yêu cầu báo giá |
| TC-11.4 | UC-11-AC-04 · Alt-03 | Số lượng bằng 0 | Mở trang | Ghi "Hết hàng"; nút Thêm vào giỏ vô hiệu; nút Gửi yêu cầu báo giá vẫn dùng được |
| TC-11.5 | UC-11-AC-05 · Alt-04 | Gian hàng Tạm ngừng | Mở trang | Có dòng tạm ngừng; hai nút giao dịch vô hiệu; vẫn xem được lô |
| TC-11.6 | UC-11-AC-06 · Alt-05 | Sản phẩm chưa có lô | Mở trang | Không hiện mục lô |
| TC-11.7 | UC-11-AC-07 · Alt-06 | Guest chưa đăng nhập | Chọn Gửi yêu cầu báo giá | Chuyển sang đăng nhập; sau đó quay lại đúng sản phẩm |
| TC-11.8 | UC-11-AC-08 · BR-3 | Sản phẩm có ba lô | Mở trang | Lô mới nhất ở đầu danh sách |
| TC-11.9 | UC-11-AC-09 · BR-4 | Sản phẩm Chỉ nhận đặt riêng | Mở trang | Hiện "Theo báo giá"; chỉ có nút Gửi yêu cầu báo giá |
| TC-11.10 | UC-11-AC-10 · BR-5 | Thêm vào giỏ | Kiểm tra số lượng còn bán của sản phẩm | Số lượng còn bán không bị trừ |
| TC-11.11 | UC-11-AC-11 · BR-6 | Sản phẩm "Giỏ mây đan hoa cúc" | Mở trang | URL dạng chữ dễ đọc |
| TC-11.12 | UC-11-AC-12 · BR-7 | Sản phẩm Có sẵn còn hàng | Mở trang | Có cả nút Thêm vào giỏ và Gửi yêu cầu báo giá |
| TC-11.13 | UC-11-AC-13 · Main Flow, BR-7 | Guest đã đăng nhập xem sản phẩm "Giỏ mây đan hoa cúc" | Chọn Gửi yêu cầu báo giá | Chuyển sang UC-18 kèm sản phẩm này làm mẫu tham chiếu; yêu cầu chỉ gửi cho xưởng của sản phẩm |

## UC-12 — Quản lý phân loại

| TC | AC và quy tắc | Chuẩn bị | Thao tác | Kết quả mong đợi |
|---|---|---|---|---|
| TC-12.1 | UC-12-AC-01 · Main Flow | Admin đã đăng nhập | Thêm phân loại "Giỏ và làn" | Phân loại ở trạng thái Hiển thị; chọn được ở UC-09 và lọc được ở UC-08, UC-10 |
| TC-12.2 | UC-12-AC-02 · Alt-01 | Đã có "Giỏ và làn" | Thêm "giỏ và làn" | Hiển thị ERR-UC12-01; không tạo phân loại |
| TC-12.3 | UC-12-AC-03 · Alt-02 | Phân loại có tên cũ | Sửa tên rồi Lưu | Tên mới hiện ở mọi nơi dùng phân loại |
| TC-12.4 | UC-12-AC-04 · Alt-03, BR-3 | Phân loại không có sản phẩm Hiển thị | Chọn Ẩn | Phân loại Ẩn |
| TC-12.5 | UC-12-AC-05 · Alt-04, BR-3 | Phân loại còn sản phẩm Hiển thị | Chọn Ẩn | Hiển thị ERR-UC12-02; phân loại giữ nguyên |
| TC-12.6 | UC-12-AC-06 · Alt-05 | Phân loại đang Ẩn | Chọn Hiện lại | Phân loại Hiển thị và dùng lại được |
| TC-12.7 | UC-12-AC-07 · Alt-06, BR-1 | Tài khoản Artisan hoặc Buyer | Gọi thao tác quản lý phân loại | Hiển thị ERR-UC12-03; dữ liệu không đổi |
| TC-12.8 | UC-12-AC-08 · BR-2 | Phân loại đã tạo | Tìm thao tác xóa | Không có chức năng xóa, chỉ có Ẩn |
| TC-12.9 | UC-12-AC-09 · BR-4 | Phân loại Ẩn | Artisan tạo sản phẩm; Guest mở bộ lọc | Không chọn được ở UC-09; không hiện trong bộ lọc |
| TC-12.10 | UC-12-AC-10 · BR-5 | Artisan tạo sản phẩm | Chọn phân loại | Chỉ chọn được một phân loại |
| TC-12.11 | UC-12-AC-11 · BR-6 | Nhóm không triển khai UC-12 | Khởi tạo dữ liệu mẫu | Danh sách phân loại cố định có sẵn; không có màn hình quản lý phân loại; UC-08, UC-09, UC-10 vẫn dùng bình thường |

## UC-13 — Quản lý trạng thái hoạt động của gian hàng

| TC | AC và quy tắc | Chuẩn bị | Thao tác | Kết quả mong đợi |
|---|---|---|---|---|
| TC-13.1 | UC-13-AC-01 · Main Flow | Gian hàng Đang chuẩn bị, checklist đủ | Chọn Mở gian hàng và xác nhận | Gian hàng Đang hoạt động; xuất hiện ở danh sách xưởng, bản đồ; sản phẩm Hiển thị công khai |
| TC-13.2 | UC-13-AC-02 · Alt-01 | Checklist thiếu ảnh đại diện | Chọn Mở gian hàng | Hiển thị ERR-UC13-01 kèm mục thiếu; gian hàng vẫn Đang chuẩn bị |
| TC-13.3 | UC-13-AC-03 · Alt-02, BR-4 | Gian hàng Đang hoạt động | Chọn Tạm ngừng và xác nhận | Gian hàng Tạm ngừng; vẫn hiển thị; nút mua và gửi yêu cầu mới bị vô hiệu |
| TC-13.4 | UC-13-AC-04 · Alt-03 | Gian hàng Tạm ngừng, checklist đủ | Chọn Mở lại | Gian hàng Đang hoạt động, nhận giao dịch mới |
| TC-13.5 | UC-13-AC-05 · Alt-04 | Mở gian hàng ở tab khác đã xong | Mở tiếp ở tab cũ | Hiển thị ERR-UC13-02; trạng thái hiện tại được tải lại |
| TC-13.6 | UC-13-AC-06 · Alt-05, BR-7 | Tài khoản khác | Gửi thao tác đổi trạng thái gian hàng này | Hiển thị ERR-UC13-03; trạng thái không đổi |
| TC-13.7 | UC-13-AC-07 · BR-1 | Gian hàng Đang hoạt động | Tìm cách đưa về Đang chuẩn bị | Không có thao tác nào làm được |
| TC-13.8 | UC-13-AC-08 · BR-2 | Gian hàng Đang chuẩn bị | Guest tìm xưởng và mở bản đồ | Xưởng không xuất hiện; chỉ chủ xưởng xem trước |
| TC-13.9 | UC-13-AC-09 · BR-3 | Checklist đủ | Artisan mở gian hàng | Không có bước Admin duyệt |
| TC-13.10 | UC-13-AC-10 · BR-4 | Xưởng đã báo giá trước khi tạm ngừng | Buyer chấp nhận báo giá đó | Chấp nhận được; đơn mới và yêu cầu mới thì không |
| TC-13.11 | UC-13-AC-11 · BR-5 | Xưởng Tạm ngừng | Bật Đang đón khách | Hai trạng thái đặt độc lập, không ảnh hưởng nhau |
| TC-13.12 | UC-13-AC-12 · BR-6 | Gian hàng Tạm ngừng, đã ẩn hết sản phẩm | Chọn Mở lại | Hiển thị ERR-UC13-01; không mở lại |
| TC-13.13 | UC-13-AC-13 · BR-8 | Gian hàng Đang hoạt động, xưởng ẩn hết sản phẩm | Xem trạng thái gian hàng | Gian hàng vẫn Đang hoạt động, không tự tạm ngừng; checklist chỉ kiểm khi mở hoặc mở lại |
| TC-13.14 | UC-13-AC-14 · BR-9 | Gian hàng mở lần đầu ngày D1, tạm ngừng rồi mở lại ngày D2 | Xem thứ tự khối Xưởng mới mở ở trang chủ | Thứ tự tính theo D1, không phải D2 |
| TC-13.15 | UC-13-AC-15 · Alt-02, BR-4 | Xưởng đã nhận yêu cầu báo giá chưa phản hồi, rồi chuyển Tạm ngừng | Xưởng chọn Gửi báo giá, rồi chọn Không nhận | Gửi báo giá bị từ chối (UC-19 ERR-UC19-03); Không nhận thực hiện được |
