---
title: "02 — Triển lãm số — Đặc tả chức năng"
code: SRS-02
type: srs
status: official
version: 0.4.0
date: 2026-11-18
---

# 02 — Triển lãm số — Đặc tả chức năng

[BRD 0.2.0](../../brd/02-showcase/usecase.md) · [ERD](../../design/erd.md) · [Phụ thuộc liên nhóm](../README.md) · [Kiến trúc và giao diện](01-architecture.md) · [Ca nghiệm thu](03-test-cases.md)

Giới hạn dữ liệu và mã lỗi theo phiên bản BRD trên. Các phương án đang mở trong mục Open Questions cần nhóm xác nhận.

## UC-06 — Xem trang chủ, giới thiệu làng nghề

Guest hoặc Buyer mở trang chủ để đọc giới thiệu làng nghề và đi tới các khu vực chính của sàn.

**Quyền và điều kiện** Không yêu cầu đăng nhập

Ghép nội dung làng nghề, dữ liệu triển lãm và ba tin công khai mới nhất. Các liên kết giữ đúng điểm vào RFQ và danh sách tin.

| Field | Format | Validation | Required | Ghi chú |
|---|---|---|---|---|
| Giới thiệu làng nghề | Text và ảnh | Nội dung cố định (BR-4) | Có | Ví dụ: "Làng mây tre đan Phú Vinh" |
| Phân loại | Danh sách | Chỉ phân loại đang Hiển thị | Có | Từ UC-12 |
| Xưởng mới mở | Danh sách tối đa 8 | Chỉ gian hàng đã mở (kể cả Tạm ngừng); xếp theo thời điểm mở lần đầu, mới nhất trước (BR-5) | Không | Ảnh đại diện, tên xưởng. Chọn "Xem tất cả" để sang UC-08 |
| Sản phẩm mới nhất | Danh sách tối đa 12 | Chỉ sản phẩm công khai, mới nhất xếp trước | Không | Ảnh, tên, giá, xưởng. Chọn "Xem tất cả" để sang UC-10 |
| Tin công khai | Danh sách tối đa 3 | Chỉ tin Đã đăng đánh dấu công khai; mới nhất trước theo ngày đăng | Không | Từ UC-28. Mỗi tin hiện ảnh bìa (nếu có), loại tin, tiêu đề, ngày đăng. Chọn một tin để sang chi tiết tin; chọn "Xem tất cả" để sang danh sách tin (UC-28) |
| Mục Tin tức ở menu | Mục menu | Hiện ở mọi trang của Khu mua hàng, không cần đăng nhập (BR-6) | Có | Mở danh sách tin công khai ở trang 1 (UC-28) |

| Mã lỗi | Điều kiện trả lỗi |
|---|---|
| ERR-UC06-01 | Khối không tải được |

## UC-07 — Quản lý thông tin gian hàng

NT-UC07-01 chỉ gửi Trưởng BQL và Admin đang hoạt động có quyền Kiểm duyệt & Danh mục.

Artisan cập nhật thông tin gian hàng (giới thiệu, ảnh, chứng nhận, số điện thoại liên hệ, phương thức giao), xem trước rồi lưu hoặc lưu luôn; đổi tên hoặc địa chỉ đã duyệt thì gửi Admin duyệt.

**Quyền và điều kiện** Đã đăng nhập và có quyền bán (hồ sơ đã được duyệt ở UC-02) Gian hàng ở bất kỳ trạng thái nào

Lưu riêng dữ liệu chủ hộ và liên hệ công khai. Chuyển tên/địa chỉ cần duyệt sang hàng chờ UC-02. Đổi ảnh chứng nhận thu hồi nhãn.

| Field | Format | Validation | Required | Ghi chú |
|---|---|---|---|---|
| Tên xưởng | Text; length 150 | Không để trống; đổi phải qua Admin duyệt (BR-2) | Có | Điền sẵn từ hồ sơ. Ví dụ: "Xưởng mây tre Bình An" |
| Địa chỉ | Text; length 200 | Không để trống; đổi phải qua Admin duyệt (BR-2) | Có | Điền sẵn từ hồ sơ. Ví dụ: "Thôn Phú Vinh, Chương Mỹ, Hà Nội" |
| Ảnh đại diện | Ảnh JPG / PNG / WebP | 1 ảnh, ≤ 10 MB | Có điều kiện | Bắt buộc để mở gian hàng (UC-13). Công khai |
| Mô tả, câu chuyện xưởng | Text; length 2000 | Không để trống khi mở gian hàng | Có điều kiện | Bắt buộc để mở gian hàng (UC-13). Công khai |
| Ảnh xưởng | Ảnh JPG / PNG / WebP | 0–10 ảnh, mỗi ảnh ≤ 10 MB | Không | Điền sẵn từ hồ sơ. Công khai |
| Năm bắt đầu làm nghề | Number; 4 chữ số | Từ 1900 đến năm hiện tại | Không | Ví dụ: 1985 |
| Chứng nhận | Tên (text; length 150) và 1–3 ảnh | 0–5 chứng nhận, mỗi ảnh ≤ 10 MB | Không | Có nhãn "Do xưởng cung cấp" hoặc "BQL đã xác minh" (BR-5). Admin gắn nhãn ở UC-02; sửa tên hoặc thay ảnh thì nhãn về "Do xưởng cung cấp" (BR-9) |
| Số điện thoại liên hệ công khai | Danh sách 1–3 số; mỗi số text length 10 | Số di động Việt Nam, 10 chữ số; không trùng nhau | Có điều kiện | Ít nhất một số để mở gian hàng. Hiện cho mọi người xem gian hàng; tách khỏi số trong hồ sơ bán hàng (BR-10). Ví dụ: 0912345678 |
| Nhận tại xưởng | Bật / tắt | — | Không | Miễn phí, chỉ đường từ bản đồ |
| Xưởng tự giao | Bật / tắt và phí giao | Khi bật: phí là số nguyên VND từ 0 đến 1.000.000 | Có điều kiện | Phí cố định cho mọi đơn. Ví dụ: 30000 |
| GHN | Bật / tắt | Mức Should; cần cân nặng, kích thước đóng gói ở sản phẩm | Không | Phí tính theo tuyến và cân nặng |
| Trạng thái gian hàng | Chỉ xem | — | — | Đổi ở UC-13 |

| Mã lỗi | Điều kiện trả lỗi |
|---|---|
| ERR-UC07-01 | Dữ liệu không hợp lệ |
| ERR-UC07-02 | Ảnh không hợp lệ |
| ERR-UC07-03 | Đang có yêu cầu đổi chờ duyệt |
| ERR-UC07-04 | Tắt hết phương thức giao khi đang bán hàng có sẵn |
| ERR-UC07-05 | Không có quyền |

| Thông báo | Người nhận |
|---|---|
| NT-UC07-01 | Trưởng BQL và Admin có quyền Kiểm duyệt & Danh mục |

## UC-08 — Khám phá xưởng

Guest hoặc Buyer tìm xưởng theo tên, lọc theo phân loại và trạng thái đón khách, rồi xem gian hàng của xưởng (hai màn hình: danh sách xưởng và trang gian hàng).

**Quyền và điều kiện** Không yêu cầu đăng nhập

Phân trang xưởng công khai. Lọc phân loại từ sản phẩm Hiển thị, tính đón khách như UC-25. Mở đúng xưởng khi gửi RFQ riêng.

| Field | Format | Validation | Required | Ghi chú |
|---|---|---|---|---|
| Từ khóa tên xưởng | Text; length 100 | Không phân biệt hoa thường và dấu (BR-8) | Không | Ví dụ: "binh an" tìm ra "Xưởng mây tre Bình An" |
| Phân loại | Chọn từ danh sách | Chỉ phân loại đang Hiển thị | Không | Từ UC-12. Chỉ dùng làm bộ lọc, thẻ xưởng không hiện phân loại (BR-10) |
| Đang đón khách | Bật / tắt | — | Không | Trạng thái do Artisan đặt ở UC-25 |

| Mã lỗi | Điều kiện trả lỗi |
|---|---|
| ERR-UC08-01 | Gian hàng chưa mở hoặc không tồn tại |
| ERR-UC08-02 | Không tải được danh sách |

## UC-09 — Quản lý sản phẩm

Artisan tạo sản phẩm, lưu nháp và cho hiển thị; sửa, đổi loại, ẩn, hiển thị lại và xóa sản phẩm nháp.

**Quyền và điều kiện** Đã đăng nhập và có quyền bán

Kiểm tra chủ xưởng và trạng thái sản phẩm. Chỉ xóa nháp chưa có lô. Tồn theo sản phẩm, đối chiếu UC-31 trước khi cập nhật đồng thời.

| Field | Format | Validation | Required | Ghi chú |
|---|---|---|---|---|
| Loại sản phẩm | Chọn một | Có sẵn hoặc Chỉ nhận đặt riêng | Có | Quyết định có giá và số lượng hay không. Đổi loại: xem BR-10 |
| Tên sản phẩm | Text; length 150 | Không để trống | Có | Ví dụ: "Giỏ mây đan hoa cúc" |
| Phân loại | Chọn một | Chỉ phân loại đang Hiển thị | Có điều kiện | Từ UC-12. Bắt buộc khi cho hiển thị; lưu nháp không cần |
| Mô tả | Text; length 2000 | Không để trống khi cho hiển thị | Có điều kiện | |
| Ảnh sản phẩm | Ảnh JPG / PNG / WebP | 1–10 ảnh, mỗi ảnh ≤ 10 MB | Có điều kiện | Cần ít nhất 1 ảnh khi cho hiển thị |
| Giá | Number; VND | Số nguyên lớn hơn 0 | Có điều kiện | Chỉ loại Có sẵn. Ví dụ: 250000 |
| Số lượng còn bán | Number | Số nguyên từ 0 | Có điều kiện | Chỉ loại Có sẵn. Bằng 0 là hết hàng |
| Cân nặng | Number; gram | Số nguyên lớn hơn 0 | Có điều kiện | Bắt buộc khi bật GHN (Should) |
| Kích thước đóng gói | Ba số; cm | Dài, rộng, cao là số nguyên lớn hơn 0 | Có điều kiện | Bắt buộc khi bật GHN (Should) |
| Trạng thái | Chỉ xem | Nháp, Hiển thị, Ẩn | — | Thay đổi bằng các thao tác ở trên |

| Mã lỗi | Điều kiện trả lỗi |
|---|---|
| ERR-UC09-01 | Dữ liệu không hợp lệ |
| ERR-UC09-02 | Ảnh không hợp lệ |
| ERR-UC09-03 | Chưa đủ thông tin để hiển thị |
| ERR-UC09-04 | Không xóa được sản phẩm |
| ERR-UC09-05 | Không có quyền |
| ERR-UC09-06 | Chưa có phân loại nào đang Hiển thị |
| ERR-UC09-07 | Đổi sản phẩm Có sẵn đã từng Hiển thị sang Chỉ nhận đặt riêng |
| ERR-UC09-08 | Đổi sang Có sẵn khi gian hàng chưa có phương thức giao nào |

## UC-10 — Xem danh sách, tìm kiếm, lọc sản phẩm

Guest hoặc Buyer xem toàn bộ sản phẩm công khai ở trang Sản phẩm, rồi tìm bằng từ khóa và lọc theo phân loại, xưởng, loại sản phẩm.

**Quyền và điều kiện** Không yêu cầu đăng nhập

Áp quyền công khai trước tìm, lọc và phân trang. Gợi ý tên sản phẩm. Trả trạng thái và số còn bán tại lúc đọc.

| Field | Format | Validation | Required | Ghi chú |
|---|---|---|---|---|
| Từ khóa | Text; length 100 | Không phân biệt hoa thường và dấu | Không | Khớp tên sản phẩm. Gợi ý từ 2 ký tự, tối đa 8 gợi ý |
| Phân loại | Chọn từ danh sách | Chỉ phân loại đang Hiển thị | Không | Có thể được áp sẵn từ trang chủ |
| Xưởng | Chọn từ danh sách | Chỉ gian hàng đã mở | Không | Lọc sản phẩm theo xưởng, không phải tìm xưởng (UC-08) |
| Loại sản phẩm | Chọn một | Có sẵn, Chỉ nhận đặt riêng | Không | |
| Khoảng giá | Hai số; VND | Từ không lớn hơn đến | Không | Mức Should |
| Sắp xếp | Chọn một | Mới nhất (mặc định), Giá tăng, Giá giảm | Không | |

| Mã lỗi | Điều kiện trả lỗi |
|---|---|
| ERR-UC10-01 | Không tải được kết quả |
| ERR-UC10-02 | Giá từ lớn hơn giá đến |

## UC-11 — Xem chi tiết sản phẩm

Guest hoặc Buyer xem chi tiết sản phẩm, các lô sản xuất của sản phẩm, rồi thêm vào giỏ hoặc gửi yêu cầu báo giá gắn với sản phẩm đang xem.

**Quyền và điều kiện** Không yêu cầu đăng nhập Sản phẩm công khai

Trả giá, ảnh và lô được liệt kê. Hành động mua dùng giỏ UC-30, đặt theo mẫu chuyển product_id cho UC-18.

| Field | Format | Validation | Required | Ghi chú |
|---|---|---|---|---|
| Ảnh sản phẩm | Thư viện ảnh | Ảnh đã thu nhỏ cho danh sách, ảnh lớn khi phóng to | Có | |
| Tên sản phẩm | Text | — | Có | |
| Giá | VND | Chỉ sản phẩm có sẵn; sản phẩm đặt riêng hiện "Theo báo giá" | Có điều kiện | Ví dụ: "250.000 ₫" |
| Tình trạng | Text | Còn hàng, Hết hàng | Có điều kiện | Chỉ sản phẩm có sẵn |
| Mô tả, phân loại, xưởng | Text, link | Link xưởng về UC-08 | Có | |
| Danh sách lô | Danh sách | Mới nhất trước | Không | Mỗi dòng: tên lô, ngày, trạng thái, link UC-17 |

| Mã lỗi | Điều kiện trả lỗi |
|---|---|
| ERR-UC11-01 | Sản phẩm không khả dụng |

## UC-12 — Quản lý phân loại

Backend yêu cầu Trưởng BQL hoặc quyền Kiểm duyệt & Danh mục. Sai quyền trả ERR-UC12-03.

Admin thêm, sửa tên, ẩn và hiện lại phân loại sản phẩm.

**Quyền và điều kiện** Admin đã đăng nhập

Admin quản lý thứ tự và khả năng chọn. Giữ liên kết lịch sử cho nội dung đã dùng phân loại.

| Field | Format | Validation | Required | Ghi chú |
|---|---|---|---|---|
| Tên phân loại | Text; length 100 | Không để trống; duy nhất, không phân biệt hoa thường | Có | Ví dụ: "Giỏ và làn" |
| Trạng thái | Chỉ xem | Hiển thị, Ẩn | — | Đổi bằng Ẩn và Hiện lại |
| Số sản phẩm đang Hiển thị | Number | Chỉ xem | — | Quyết định có ẩn được hay không |

| Mã lỗi | Điều kiện trả lỗi |
|---|---|
| ERR-UC12-01 | Tên trống, quá dài hoặc trùng |
| ERR-UC12-02 | Còn sản phẩm đang Hiển thị |
| ERR-UC12-03 | Không có quyền |

## UC-13 — Quản lý trạng thái hoạt động của gian hàng

Artisan mở gian hàng khi đủ checklist, tạm ngừng nhận giao dịch mới và mở lại.

**Quyền và điều kiện** Đã đăng nhập và có quyền bán Gian hàng đang Đang chuẩn bị (luồng chính mở lần đầu)

Kiểm checklist ngay lúc mở hoặc mở lại, gồm vị trí đã lưu và sản phẩm Hiển thị. Tạm ngừng chặn giao dịch mới theo BRD, vẫn cho xử lý đơn đang có.

| Field | Format | Validation | Required | Ghi chú |
|---|---|---|---|---|
| Tên gian hàng | Mục checklist | Đã có tên | Có | UC-07 |
| Ảnh đại diện | Mục checklist | Đã có 1 ảnh | Có | UC-07 |
| Mô tả | Mục checklist | Không để trống | Có | UC-07 |
| Địa chỉ và liên hệ | Mục checklist | Đã có địa chỉ và ít nhất một số điện thoại liên hệ | Có | UC-07 |
| Vị trí bản đồ | Mục checklist | Đã chọn vị trí | Có | UC-25 |
| Sản phẩm | Mục checklist | Ít nhất một sản phẩm đủ thông tin và đang Hiển thị | Có | UC-09, BR-8 |
| Phương thức giao | Mục checklist | Ít nhất một phương thức đang bật | Có điều kiện | Chỉ khi có sản phẩm có sẵn Hiển thị; UC-07 |
| Trạng thái gian hàng | Chỉ xem | Đang chuẩn bị, Đang hoạt động, Tạm ngừng nhận giao dịch mới | — | |

| Mã lỗi | Điều kiện trả lỗi |
|---|---|
| ERR-UC13-01 | Checklist chưa đủ |
| ERR-UC13-02 | Thao tác không hợp lệ với trạng thái hiện tại |
| ERR-UC13-03 | Không có quyền |
