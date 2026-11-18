---
title: "06 — Tin tức cộng đồng"
group: 06-news
type: brd
status: draft
version: 0.2.0
date: 2026-11-18
author: Khánh Chi
---

# 06 — Tin tức cộng đồng

Change history: [CHANGELOG.md](CHANGELOG.md)

## I. Overview

Nhóm chức năng tin tức cộng đồng của làng nghề: Admin (BQL) soạn và đăng tin về thị trường và nguyên liệu, hạ tầng và vận hành, chính sách, hoạt động làng nghề và thông báo chung, với nội dung có định dạng, ảnh và tệp đính kèm; mỗi tin có một loại tin và được chọn công khai hoặc nội bộ (mặc định nội bộ). Tin nội bộ chỉ Artisan đọc trong Kênh người bán; tin công khai còn hiện ở trang chủ và mọi người đọc được mà không cần đăng nhập. Khi một tin được đăng lần đầu, mọi Artisan nhận thông báo trong hệ thống. Kết quả cuối cùng là BQL có một kênh thông tin chính thức tới các hộ làm nghề, đồng thời làng nghề có một mục tin tức công khai dành cho khách.

### Terms

| Term | Meaning |
|---|---|
| Tin | Một bài tin do Admin soạn, gồm tiêu đề, loại tin, nội dung có định dạng, ảnh bìa, ảnh trong bài, tệp đính kèm (đều tùy chọn) và phạm vi đọc. |
| Loại tin | Một trong năm loại cố định: Thị trường & Nguyên liệu, Hạ tầng & Vận hành, Chính sách & Quy định, Hoạt động & Sự kiện làng nghề, Thông báo chung. Mỗi tin đúng một loại (nội dung từng loại: UC-27, BR-2). |
| Trạng thái tin | Nháp (đang soạn, chỉ Admin thấy), Đã đăng (người đọc thấy theo phạm vi), Ẩn (gỡ khỏi người đọc, chỉ Admin thấy). |
| Tin công khai | Tin Đã đăng có Công khai là Có: hiện ở trang chủ và mục Tin tức, mọi người đọc được không cần đăng nhập. |
| Tin nội bộ | Tin Đã đăng có Công khai là Không (mặc định): chỉ Artisan đọc, trong Kênh người bán. |
| Artisan | Tài khoản đã được duyệt quyền bán (BRD `01-account`), ở mọi trạng thái gian hàng (Đang chuẩn bị, Đang hoạt động, Tạm ngừng). |
| Ngày đăng | Thời điểm tin được đăng lần đầu. Không đổi khi sửa hoặc đăng lại. |

## II. Scope

| Business topic | Channel | Use case |
|---|---|---|
| Soạn tin có định dạng, ảnh và tệp đính kèm; chọn loại tin và phạm vi (công khai hoặc nội bộ), lưu nháp, xem trước, đăng | Trang quản trị | UC-27 |
| Sửa tin, đổi phạm vi, ẩn, đăng lại, xóa tin nháp | Trang quản trị | UC-27 |
| Thông báo cho mọi Artisan khi có tin mới | Kênh người bán | UC-27 |
| Đọc tin công khai: khối tin ở trang chủ, danh sách lọc theo loại và phân trang theo số trang, chi tiết tin kèm ảnh và tệp tải về | Khu mua hàng | UC-28 |
| Artisan đọc mọi tin đã đăng (công khai và nội bộ), mở tin từ thông báo | Kênh người bán | UC-28 |

**Ngoài phạm vi:** Thêm hoặc sửa loại tin (năm loại cố định); video nhúng; xem trước tệp đính kèm trực tiếp (chỉ tải về); hẹn giờ đăng; ngày hết hiệu lực hoặc tự ẩn tin; ghim hoặc nổi bật tin (ngoài MVP1); tìm tin theo từ khóa; bình luận, thích, chia sẻ; theo dõi ai đã đọc hoặc lượt xem (EN-11); thông báo qua email (EN-05); gửi thông báo cho Buyer hoặc Guest; lịch sử phiên bản và khôi phục bản cũ; Admin đọc tin qua UC-28 (Admin xem trước ở UC-27).

**Liên quan**

| ID | Tài liệu | Quan hệ |
|---|---|---|
| BRD-01 | [01-account](../01-account/usecase.md) | Admin đăng nhập vào Trang quản trị (UC-04). Mở đường dẫn Kênh người bán khi chưa đăng nhập thì chuyển sang đăng nhập (UC-04, Alt-07); tài khoản chưa có quyền bán bị từ chối (ERR-UC04-03, ERR-UC04-04), nên tin nội bộ chỉ Artisan đọc được (UC-28, BR-3). |
| BRD-02 | [02-showcase](../02-showcase/usecase.md) | UC-06 hiện tối đa 3 tin công khai mới nhất lấy từ UC-28 và dẫn tới UC-28 khi chọn một tin (UC-28, BR-4, Alt-03); UC-06 có mục Tin tức ở menu và nút Xem tất cả ở khối tin, cùng mở danh sách tin công khai (UC-28, Main Flow, Alt-03; G-02). |
| BRD-07 | `07-notification` | UC-29 hiển thị NT-UC27-01; mở thông báo dẫn tới UC-28 (Alt-02). |
| BRD-09 | [09-portal](../09-portal/usecase.md) | UC-34 quy định chỉ Trưởng BQL hoặc Admin có nhóm quyền `Tin tức cộng đồng` mới quản lý tin ở UC-27 (UC-34 BR-4). |

## III. Use Cases

| Use case ID | Use case | Description |
|---|---|---|
| UC-27 | Quản lý tin tức | Admin soạn tin, chọn loại tin và phạm vi đọc, lưu nháp, đăng; sửa, đổi phạm vi, ẩn, đăng lại tin và xóa tin nháp |
| UC-28 | Đọc tin tức | Guest hoặc Buyer đọc tin công khai; Artisan đọc thêm tin nội bộ trong Kênh người bán |

### UC-27 — Quản lý tin tức

#### Detail

| Field | Content |
|---|---|
| Use case ID | UC-27 |
| Description | Admin soạn tin, chọn loại tin và phạm vi đọc (công khai hoặc nội bộ), lưu nháp hoặc đăng; sửa, đổi phạm vi, ẩn và đăng lại tin đã đăng; xóa tin nháp. Khi một tin được đăng lần đầu, mọi Artisan nhận thông báo. |
| Actors | Admin |
| Channel | Trang quản trị |
| Preconditions | • Admin đã đăng nhập |
| Expected | **Thành công:**<br>• Tin ở trạng thái Đã đăng; Artisan đọc được trong Kênh người bán, tin công khai còn hiện ở trang chủ và mục Tin tức (UC-28)<br>• Mọi Artisan nhận NT-UC27-01<br>**Thất bại:**<br>• Hiển thị lỗi, tin giữ nguyên trạng thái, dữ liệu đã nhập được giữ |
| Screens | • Danh sách tin (bước 1)<br>• Form tin (bước 2-5)<br>• Hộp xác nhận đăng công khai (bước 5)<br>• Xem trước tin (Alt-03) |
| Notifications | NT-UC27-01 |

#### Main Flow

Trigger: Admin chọn Thêm tin ở mục Tin tức trong Trang quản trị.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Admin | Admin mở mục Tin tức. | Danh sách mọi tin hiển thị, mới cập nhật trước, 20 tin mỗi trang, có thanh chọn trang: tiêu đề, loại tin, phạm vi (Công khai hoặc Nội bộ), trạng thái, ngày đăng, cập nhật lúc; có bộ lọc theo trạng thái, loại tin, phạm vi. |
| 2 | Admin | Admin chọn Thêm tin. | Form tin trống hiển thị; Công khai mặc định là Không. |
| 3 | Admin | Admin nhập tiêu đề, chọn loại tin, soạn nội dung (định dạng chữ, danh sách, bảng, liên kết, chèn ảnh trong bài), tải ảnh bìa và tệp đính kèm (nếu có) và chọn phạm vi đọc (giữ Không công khai, hoặc chọn Công khai). | Thông tin, ảnh và danh sách tệp đính kèm hiển thị trong form; nội dung hiện đúng định dạng đã soạn. |
| 4 | Admin | Admin chọn Lưu nháp. | Backend kiểm tra dữ liệu tối thiểu và lưu tin ở trạng thái Nháp; tin có trong danh sách, chỉ Admin thấy. |
| 5 | Admin | Admin chọn Đăng. Với tin Công khai, Frontend hiện hộp xác nhận "Tin công khai sẽ hiện ở trang chủ và ai cũng đọc được. Bạn có chắc muốn đăng?" và Admin xác nhận. | Yêu cầu đăng được gửi đi. |
| 6 | Backend | Backend kiểm tra đủ thông tin, chuyển tin sang Đã đăng, ghi ngày đăng và người đăng. | Tin Đã đăng; Artisan đọc được; tin công khai hiện ở trang chủ và mục Tin tức. |
| 7 | Backend | Backend thông báo cho mọi Artisan. | NT-UC27-01 được gửi. |
| 8 | Frontend | Frontend cập nhật danh sách. | Tin hiện trạng thái Đã đăng kèm ngày đăng; Admin thấy "Đã đăng tin". |

Outcome: Tin ở trạng thái Đã đăng; Artisan đọc được (tin công khai thì ai cũng đọc được) và đã được báo.

#### Alternative Flows

##### Alt-01 — ERR-UC27-01 — Dữ liệu không hợp lệ

- At step: 4 · Trigger: Tiêu đề trống hoặc quá 150 ký tự; nội dung quá 10000 ký tự chữ; loại tin không thuộc năm loại cố định.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Backend | Backend từ chối lưu và đánh dấu trường sai. | Hiển thị ERR-UC27-01 tại trường sai. |

Resume: Quay lại bước 3, giữ nguyên dữ liệu đã nhập.

##### Alt-02 — ERR-UC27-02 — Ảnh không hợp lệ

- At step: 3 · Trigger: Ảnh bìa hoặc ảnh trong bài sai định dạng, lớn hơn 10 MB; chọn nhiều hơn 1 ảnh bìa hoặc quá 10 ảnh trong bài.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Frontend | Frontend không nhận ảnh. | Hiển thị ERR-UC27-02. |

Resume: Chọn ảnh khác, lặp lại bước 3.

##### Alt-03 — Xem trước

- At step: 3 · Trigger: Admin chọn Xem trước thay vì Lưu nháp hoặc Đăng.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Frontend | Frontend hiển thị tin đúng như người đọc sẽ thấy ở UC-28 với dữ liệu đang nhập (kể cả ảnh trong bài và tệp đính kèm đã tải lên, Admin mở được ảnh và tải được tệp theo BR-15), kèm nhãn Công khai hoặc Nội bộ. | Chưa lưu và chưa ai ngoài Admin thấy. |
| 2 | Admin | Admin chọn Quay lại sửa, Lưu nháp hoặc Đăng. | Quay lại sửa thì dữ liệu đang nhập được giữ nguyên. |

Resume: Quay lại sửa thì về bước 3; Lưu nháp thì tiếp tục bước 4; Đăng thì tiếp tục bước 5.

##### Alt-04 — ERR-UC27-03 — Chưa đủ thông tin để đăng

- At step: 6 · Trigger: Tin thiếu tiêu đề, loại tin hoặc nội dung khi đăng, đăng lại hoặc khi sửa một tin đang Đã đăng.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Backend | Backend từ chối và liệt kê mục thiếu. | Hiển thị ERR-UC27-03; tin giữ trạng thái hiện tại; không gửi thông báo. |

Resume: Quay lại bước 3, giữ nguyên dữ liệu đã nhập.

##### Alt-05 — Không xác nhận đăng công khai

- At step: 5 · Trigger: Admin chọn Quay lại ở hộp xác nhận đăng công khai.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Frontend | Frontend đóng hộp xác nhận. | Tin không được đăng; giữ nguyên trạng thái hiện tại và dữ liệu đang nhập. |

Resume: Quay lại bước 3.

##### Alt-06 — Sửa tin

- At step: 1 · Trigger: Admin chọn một tin trong danh sách (ở bất kỳ trạng thái nào) rồi chọn Sửa.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Admin | Admin sửa tiêu đề, loại tin, nội dung, ảnh, tệp đính kèm hoặc Công khai rồi chọn Lưu. | Dữ liệu được gửi lên kiểm tra. |
| 2 | Backend | Backend kiểm tra theo ERR-UC27-01, ERR-UC27-02 và ERR-UC27-08; tin đang Đã đăng còn phải đủ thông tin (ERR-UC27-03). Sau đó lưu thay đổi. | Thay đổi có hiệu lực ngay; ngày đăng không đổi; không gửi thông báo; tin Đã đăng đã sửa hiện thêm "Cập nhật lúc" cho người đọc. Đổi tiêu đề thì địa chỉ tin đổi theo tiêu đề mới, link cũ vẫn mở được (UC-28, Alt-07). |

Resume: Kết thúc. Tin Nháp hoặc Ẩn đổi phạm vi bằng cách sửa Công khai ở đây; phạm vi chỉ có hiệu lực với người đọc khi tin Đã đăng.

##### Alt-07 — Đổi phạm vi đọc của tin đã đăng

- At step: 1 · Trigger: Admin đổi Công khai của một tin Đã đăng (từ Không sang Có, hoặc từ Có sang Không) rồi chọn Lưu.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Frontend | Đổi sang Công khai thì Frontend hiện hộp xác nhận như bước 5. | Admin xác nhận hoặc quay lại (Alt-05). |
| 2 | Backend | Backend lưu phạm vi mới. | Sang Công khai: tin hiện thêm ở trang chủ và mục Tin tức, ai cũng đọc được. Sang Nội bộ: tin biến khỏi trang chủ và Khu mua hàng, chỉ Artisan đọc trong Kênh người bán; link ở Khu mua hàng báo tin không khả dụng (UC-28, Alt-05). Không gửi thông báo. |

Resume: Kết thúc.

##### Alt-08 — Ẩn tin

- At step: 1 · Trigger: Admin chọn Ẩn trên một tin Đã đăng.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Frontend | Frontend hiện hộp xác nhận "Tin sẽ biến khỏi Kênh người bán, trang chủ và mục Tin tức". | Admin xác nhận hoặc hủy. |
| 2 | Backend | Backend chuyển tin sang Ẩn. | Không ai ngoài Admin thấy tin; thông báo NT-UC27-01 đã gửi vẫn nằm trong chuông của Artisan nhưng mở ra báo tin không khả dụng (UC-28, Alt-05). |

Resume: Kết thúc.

##### Alt-09 — Đăng lại tin đã ẩn

- At step: 1 · Trigger: Admin chọn Đăng lại trên một tin Ẩn.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Frontend | Tin Công khai thì Frontend hiện hộp xác nhận như bước 5. | Admin xác nhận hoặc hủy. |
| 2 | Backend | Backend kiểm tra đủ thông tin (ERR-UC27-03) và chuyển tin sang Đã đăng. | Tin hiện lại theo phạm vi đang đặt; ngày đăng vẫn là lần đăng đầu; không gửi thông báo. |

Resume: Kết thúc.

##### Alt-10 — Xóa tin nháp

- At step: 1 · Trigger: Admin chọn Xóa trên một tin Nháp.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Frontend | Frontend hiện hộp xác nhận xóa. | Admin xác nhận hoặc hủy. |
| 2 | Backend | Backend xóa tin. | Tin biến khỏi danh sách. |

Resume: Kết thúc.

##### Alt-11 — ERR-UC27-04 — Không xóa được tin

- At step: 1 · Trigger: Admin xóa tin không ở trạng thái Nháp.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Backend | Backend từ chối xóa. | Hiển thị ERR-UC27-04, gợi ý dùng Ẩn; tin giữ nguyên. |

Resume: Kết thúc.

##### Alt-12 — ERR-UC27-05 — Tin vừa được Admin khác thay đổi

- At step: 6 (và mọi thao tác sửa, đăng, đổi phạm vi, ẩn, đăng lại, xóa) · Trigger: Admin khác đã sửa, đổi trạng thái hoặc xóa tin sau khi Admin này mở tin.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Backend | Backend không áp dụng thao tác đến sau, giữ kết quả của thao tác đến trước; thao tác đăng chỉ gửi thông báo một lần. | Hiển thị ERR-UC27-05; không ghi đè dữ liệu. |
| 2 | Frontend | Frontend tải lại tin với nội dung và trạng thái mới. | Admin thấy dữ liệu hiện hành. |

Resume: Quay lại bước 1.

##### Alt-13 — ERR-UC27-06 — Không có quyền

- At step: 1 · Trigger: Người không phải Admin (Guest, Buyer, Artisan) gửi thao tác quản lý tin, hoặc Admin không có nhóm quyền `Tin tức cộng đồng` (UC-34 BR-4, `09-portal`).

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Backend | Backend từ chối thao tác. | Hiển thị ERR-UC27-06; dữ liệu không đổi. |

Resume: Kết thúc. Mở đường dẫn Trang quản trị mà không phải Admin xử lý ở UC-04 (ERR-UC04-06).

##### Alt-14 — ERR-UC27-07 — Không tải được danh sách

- At step: 1 · Trigger: Tải hoặc lọc danh sách tin bị lỗi.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Frontend | Frontend giữ danh sách và bộ lọc hiện tại, hiện lỗi và nút Thử lại. | Hiển thị ERR-UC27-07. |

Resume: Chọn Thử lại thì lặp bước đang làm.

##### Alt-15 — Danh sách trống

- At step: 1 · Trigger: Chưa có tin nào, hoặc bộ lọc không khớp tin nào.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Frontend | Chưa có tin thì Frontend hiện "Chưa có tin nào. Chọn Thêm tin để bắt đầu." Bộ lọc không khớp thì hiện "Không có tin phù hợp" và nút Xóa bộ lọc. | Danh sách trống, Admin biết bước tiếp theo. |

Resume: Chọn Xóa bộ lọc thì quay lại bước 1.

##### Alt-16 — ERR-UC27-08 — Tệp đính kèm không hợp lệ

- At step: 3 · Trigger: Tệp sai định dạng (ngoài PDF, DOC, DOCX, XLS, XLSX), lớn hơn 10 MB hoặc quá 5 tệp.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Frontend | Frontend không nhận tệp. | Hiển thị ERR-UC27-08; các tệp hợp lệ khác vẫn giữ. |

Resume: Chọn tệp khác, lặp lại bước 3.

#### Data Fields

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

#### Error Messages

| Code | Condition | Message | Display | Flow |
|---|---|---|---|---|
| ERR-UC27-01 | Dữ liệu không hợp lệ | "Thông tin tin tức chưa hợp lệ. Vui lòng kiểm tra các trường được đánh dấu." | Inline | Alt-01 |
| ERR-UC27-02 | Ảnh không hợp lệ | "Ảnh không hợp lệ. Chỉ chấp nhận JPG, PNG, WebP, tối đa 10 MB mỗi ảnh, 1 ảnh bìa và 10 ảnh trong bài." | Inline | Alt-02 |
| ERR-UC27-03 | Chưa đủ thông tin để đăng | "Chưa thể đăng tin. Vui lòng bổ sung: {{danhSachMucThieu}}." | Inline | Alt-04 |
| ERR-UC27-04 | Không xóa được tin | "Chỉ xóa được tin nháp. Hãy dùng Ẩn để gỡ tin đã đăng khỏi người đọc." | Toast | Alt-11 |
| ERR-UC27-05 | Tin vừa được Admin khác thay đổi | "Tin này vừa được thay đổi. Vui lòng xem lại nội dung mới trước khi thao tác." | Toast | Alt-12 |
| ERR-UC27-06 | Không có quyền | "Bạn không có quyền thực hiện thao tác này." | Trang lỗi | Alt-13 |
| ERR-UC27-07 | Không tải được danh sách | "Không tải được danh sách tin. Vui lòng thử lại." | Inline | Alt-14 |
| ERR-UC27-08 | Tệp đính kèm không hợp lệ | "Tệp không hợp lệ. Chỉ chấp nhận PDF, DOC, DOCX, XLS, XLSX, tối đa 10 MB mỗi tệp, tối đa 5 tệp." | Inline | Alt-16 |

#### Notifications

| Template ID | Trigger (step) | Channel | Recipient | Title | Body |
|---|---|---|---|---|---|
| NT-UC27-01 | 7 | In-app | Mọi Artisan | Tin mới từ BQL | "BQL vừa đăng tin {{newsType}}: {{newsTitle}}" |

#### Business Rules

| ID | Rule | Source |
|---|---|---|
| BR-1 | Chỉ Trưởng BQL hoặc Admin có nhóm quyền `Tin tức cộng đồng` quản lý tin và xem được tin ở mọi trạng thái. | Đề bài §3.3; Overview 3.6; UC-34 BR-4 (`09-portal`) |
| BR-2 | Có năm loại tin cố định, mỗi tin đúng một loại: Thị trường & Nguyên liệu (biến động giá mây, tre, phụ kiện, mùa vụ nguyên liệu, xu hướng mẫu mã); Hạ tầng & Vận hành (lịch cắt điện, nước ở làng nghề, bảo trì hệ thống sàn, gián đoạn vận chuyển); Chính sách & Quy định (quy định của sàn, chính sách hỗ trợ làng nghề của địa phương và nhà nước); Hoạt động & Sự kiện làng nghề (hội chợ, triển lãm, lễ hội truyền thống, lớp tập huấn kỹ thuật); Thông báo chung (tin tổng hợp, thông báo lễ Tết, nội dung đột xuất khác). Admin không thêm hoặc sửa loại tin trong MVP1. | Overview 3.6; Chốt 2026-11-18 (G-06) |
| BR-3 | Tin mặc định không công khai (nội bộ): chỉ Artisan đọc. Chọn Công khai thì tin hiện thêm ở trang chủ và mọi người đọc được; đăng hoặc đổi sang Công khai phải qua hộp xác nhận. | Overview 3.6; Nhóm đề xuất |
| BR-4 | Vòng đời tin: Nháp sang Đã đăng; Đã đăng và Ẩn chuyển qua lại; không quay về Nháp. Tin Nháp và Ẩn chỉ Admin thấy. | Nhóm đề xuất |
| BR-5 | Chỉ xóa được tin Nháp; tin Đã đăng và Ẩn không xóa, chỉ Ẩn. | Nhóm đề xuất (cùng nguyên tắc UC-09, BRD 02) |
| BR-6 | Lưu nháp chỉ cần tiêu đề. Đăng và đăng lại cần đủ tiêu đề, loại tin, nội dung; tin đang Đã đăng khi sửa vẫn phải đủ ba mục này. | Nhóm đề xuất |
| BR-7 | NT-UC27-01 gửi đúng một lần cho mỗi tin, vào lần đăng đầu tiên, tới mọi Artisan tại thời điểm đăng. Sửa, đổi phạm vi, ẩn, đăng lại không gửi thêm; thao tác đăng đồng thời chỉ gửi một lần. Chưa có Artisan nào thì tin vẫn đăng và không có thông báo; Artisan được duyệt sau đọc được tin cũ nhưng không nhận thông báo bù. | Overview 3.6, 3.7; Nhóm đề xuất (G-01) |
| BR-8 | Ngày đăng ghi thời điểm đăng lần đầu, không đổi khi sửa hoặc đăng lại; Admin không sửa tay. Danh sách người đọc xếp theo ngày đăng. | Nhóm đề xuất (G-05) |
| BR-9 | Sửa tin có hiệu lực ngay, không đổi ngày đăng, không gửi thông báo. Tin đã sửa sau khi đăng hiện thêm "Cập nhật lúc" cho người đọc. Đổi tiêu đề làm đổi phần chữ trong địa chỉ tin, không làm hỏng link cũ (UC-28, BR-6). | Nhóm đề xuất |
| BR-10 | Nội dung cho phép định dạng: tiêu đề phụ, in đậm, in nghiêng, danh sách, bảng, liên kết và ảnh. Hệ thống chỉ giữ các định dạng này; mã script hoặc nội dung ngoài danh sách bị loại khi lưu, không bao giờ chạy khi người đọc mở tin. | Đề bài §1 (An ninh); Chốt 2026-11-18 (G-07) |
| BR-11 | Ảnh bìa và ảnh trong bài được backend thu nhỏ để danh sách và trang tin không tải ảnh gốc. | Đề bài §4.4 |
| BR-12 | Khi hai Admin thao tác cùng một tin, thao tác đến trước được giữ; thao tác đến sau báo ERR-UC27-05, không ghi đè. | Đề bài §4.2 (khóa bản ghi); Nhóm đề xuất (cùng nguyên tắc UC-02 BR-5, BRD 01) |
| BR-13 | Mỗi thao tác đều kiểm tra quyền Admin (nhóm quyền `Tin tức cộng đồng`, UC-34 BR-4 `09-portal`), không chỉ ẩn nút hoặc menu: mở thẳng đường dẫn ngoài quyền bị từ chối. | Đề bài §1 (An ninh) |
| BR-14 | Ẩn có hiệu lực ngay ở mọi nơi: Kênh người bán, trang chủ, mục Tin tức và link trực tiếp. Thông báo đã gửi không bị thu hồi. | Nhóm đề xuất |
| BR-15 | Ảnh trong bài và tệp đính kèm theo phạm vi của tin: người đọc được tin (UC-28, BR-1) mới mở được ảnh và tải được tệp; với tin Nháp, Ẩn hoặc tin nội bộ mở ở Khu mua hàng, link trực tiếp tới ảnh và tệp bị từ chối. Admin đã đăng nhập luôn mở được ảnh và tải được tệp của tin ở mọi trạng thái để xem trước và quản lý (kể cả Nháp và Ẩn); quyền này kiểm tra ở backend theo phiên Admin, không phụ thuộc trạng thái tin. Người dùng khác (Guest, Buyer, Artisan) chỉ theo phạm vi đọc ở trên. Tệp chỉ tải về, không xem trực tiếp. | Đề bài §1 (An ninh); Chốt 2026-11-18 (G-07) |

#### Acceptance Criteria

| ID | Given | When | Then | Covers |
|---|---|---|---|---|
| UC-27-AC-01 | Admin đã đăng nhập, có 2 Artisan | Thêm tin loại Hạ tầng & Vận hành, giữ Không công khai, Lưu nháp rồi Đăng | Tin Đã đăng, ghi ngày đăng và người đăng; cả 2 Artisan nhận NT-UC27-01 và đọc được trong Kênh người bán; Guest không thấy | Main Flow |
| UC-27-AC-02 | Form tin | Để trống tiêu đề, hoặc nhập tiêu đề 151 ký tự, hoặc nội dung 10001 ký tự chữ, rồi chọn Lưu nháp | Hiển thị ERR-UC27-01 tại trường sai; dữ liệu đã nhập được giữ; không lưu | Alt-01 |
| UC-27-AC-03 | Form tin | Tải ảnh bìa PDF, hoặc ảnh lớn hơn 10 MB, hoặc chọn 2 ảnh bìa, hoặc chèn 11 ảnh trong bài | Hiển thị ERR-UC27-02; ảnh không được nhận | Alt-02 |
| UC-27-AC-04 | Admin đang soạn tin, chưa lưu, đã tải 1 ảnh trong bài và 1 tệp PDF | Chọn Xem trước, chọn Quay lại sửa, rồi Xem trước và Đăng | Xem trước hiện đúng như người đọc, kèm nhãn Công khai hoặc Nội bộ, ảnh trong bài hiển thị và tệp tải về được, mà chưa lưu hay công khai; Quay lại sửa giữ dữ liệu; Đăng từ xem trước thì đăng như bước 5 | Alt-03 |
| UC-27-AC-05 | Tin Nháp thiếu nội dung và loại tin | Chọn Đăng | Hiển thị ERR-UC27-03 liệt kê mục thiếu; tin vẫn Nháp; không có thông báo | Alt-04, BR-6 |
| UC-27-AC-06 | Tin Nháp đủ thông tin, Công khai là Có | Chọn Đăng rồi chọn Quay lại ở hộp xác nhận; sau đó Đăng lại và xác nhận | Lần đầu tin vẫn Nháp, không thông báo; lần sau tin Đã đăng và hiện ở trang chủ | Alt-05, BR-3 |
| UC-27-AC-07 | Tin Đã đăng | Admin sửa nội dung rồi Lưu | Nội dung mới hiện ngay cho người đọc; ngày đăng không đổi; không có thông báo mới; người đọc thấy "Cập nhật lúc". Sửa xóa hết nội dung thì hiện ERR-UC27-03 và không lưu. Đổi tiêu đề thì địa chỉ tin theo tiêu đề mới và link cũ vẫn mở được (UC-28-AC-20) | Alt-06, BR-9, BR-6 |
| UC-27-AC-08 | Tin Đã đăng nội bộ | Admin đổi sang Công khai, xác nhận, Lưu; sau đó đổi lại sang Không công khai và Lưu | Sang Công khai: hiện ở trang chủ, Guest đọc được. Sang Nội bộ: biến khỏi trang chủ; Guest mở link thấy ERR-UC28-01; Artisan vẫn đọc được. Cả hai lần không có thông báo mới | Alt-07, BR-3 |
| UC-27-AC-09 | Tin công khai Đã đăng | Admin chọn Ẩn và xác nhận | Tin biến khỏi Kênh người bán, trang chủ, mục Tin tức; Guest và Artisan mở link thấy ERR-UC28-01; Admin vẫn thấy tin ở trạng thái Ẩn; thông báo cũ của Artisan vẫn còn trong chuông | Alt-08, BR-14 |
| UC-27-AC-10 | Tin Ẩn đăng lần đầu ngày D1, đăng lại ngày D2 | Admin chọn Đăng lại | Tin hiện lại; ngày đăng vẫn là D1; thứ tự trong danh sách người đọc không đổi; không có thông báo mới | Alt-09, BR-8 |
| UC-27-AC-11 | Tin Nháp | Admin chọn Xóa và xác nhận | Tin biến khỏi danh sách | Alt-10, BR-5 |
| UC-27-AC-12 | Tin Đã đăng, hoặc tin Ẩn | Admin chọn Xóa | Hiển thị ERR-UC27-04; tin giữ nguyên | Alt-11, BR-5 |
| UC-27-AC-13 | Hai Admin cùng mở một tin Nháp; Admin A bấm Đăng rồi Admin B bấm Đăng | Hai thao tác gần như đồng thời | Tin Đã đăng một lần; mỗi Artisan nhận đúng một NT-UC27-01; Admin B thấy ERR-UC27-05 và dữ liệu được tải lại | Alt-12, BR-12, BR-7 |
| UC-27-AC-14 | Tài khoản Buyer, Artisan hoặc Guest | Gửi thao tác tạo, sửa, đăng, ẩn hoặc xóa tin trực tiếp | Hiển thị ERR-UC27-06; dữ liệu không đổi. Mở Trang quản trị thì ERR-UC04-06 | Alt-13, BR-13 |
| UC-27-AC-15 | API danh sách lỗi | Admin lọc theo trạng thái | Hiển thị ERR-UC27-07; danh sách và bộ lọc đang chọn được giữ; có nút Thử lại | Alt-14 |
| UC-27-AC-16 | Chưa có tin nào; hoặc có tin nhưng bộ lọc không khớp | Admin mở mục Tin tức; hoặc lọc | Hiện "Chưa có tin nào. Chọn Thêm tin để bắt đầu."; hoặc "Không có tin phù hợp" và nút Xóa bộ lọc | Alt-15 |
| UC-27-AC-17 | Có tin Nháp, Đã đăng, Ẩn | Admin mở danh sách | Thấy cả ba trạng thái; Guest, Buyer, Artisan không thấy tin Nháp và Ẩn | BR-1, BR-4 |
| UC-27-AC-18 | Form tin | Admin mở danh sách Loại tin và tìm cách thêm loại mới | Chỉ có năm loại cố định (Thị trường & Nguyên liệu, Hạ tầng & Vận hành, Chính sách & Quy định, Hoạt động & Sự kiện làng nghề, Thông báo chung); không có thao tác thêm hoặc sửa loại; mỗi tin chọn đúng một loại | BR-2 |
| UC-27-AC-19 | Admin thêm tin mới | Không chạm vào Công khai rồi Đăng | Công khai mặc định Không; tin chỉ Artisan đọc, không có ở trang chủ | BR-3 |
| UC-27-AC-20 | Tin Đã đăng hoặc Ẩn | Admin tìm cách đưa về Nháp | Không có thao tác nào làm được | BR-4 |
| UC-27-AC-21 | Form tin chỉ có tiêu đề | Chọn Lưu nháp; sau đó chọn Đăng | Lưu nháp thành công; Đăng bị ERR-UC27-03 nêu thiếu loại tin và nội dung | BR-6 |
| UC-27-AC-22 | Có Artisan A, B và Buyer C, Admin | Admin đăng một tin; sau đó sửa, đổi phạm vi, ẩn, đăng lại tin đó | A và B mỗi người nhận đúng một NT-UC27-01 ở lần đăng đầu; C và Admin không nhận; các thao tác sau không phát thêm thông báo | BR-7 |
| UC-27-AC-23 | Chưa có Artisan nào; sau đó Admin duyệt Artisan D | Admin đăng một tin; Artisan D mở mục Tin tức | Tin vẫn Đã đăng, không có thông báo; D đọc được tin cũ nhưng không nhận NT-UC27-01 cho tin đó | BR-7 |
| UC-27-AC-24 | Nội dung tin có đoạn in đậm, một bảng, một ảnh chèn trong bài và chuỗi `<script>alert(1)</script>` | Admin đăng; Guest mở tin công khai | In đậm, bảng và ảnh hiển thị đúng vị trí đã soạn; chuỗi script bị loại hoặc hiện như chữ, không chạy mã | BR-10 |
| UC-27-AC-25 | Tin có ảnh bìa và ảnh trong bài hợp lệ | Admin đăng rồi Guest xem danh sách và chi tiết tin | Danh sách dùng ảnh bìa đã thu nhỏ; trang tin dùng ảnh trong bài đã thu nhỏ, không tải ảnh gốc | BR-11 |
| UC-27-AC-26 | Form tin | Tải tệp .exe, hoặc PDF 11 MB, hoặc tệp đính kèm thứ 6 | Hiển thị ERR-UC27-08; tệp không được nhận; các tệp hợp lệ đã tải vẫn giữ | Alt-16 |
| UC-27-AC-27 | Tin nội bộ Đã đăng có 1 ảnh trong bài và 1 tệp PDF | Guest mở link trực tiếp tới ảnh và tệp; Artisan tải tệp; sau đó Admin Ẩn tin và Artisan mở lại link | Guest bị từ chối; Artisan tải được tệp; sau khi Ẩn, link ảnh và tệp bị từ chối với Artisan | BR-15 |
| UC-27-AC-28 | Có 45 tin | Admin mở mục Tin tức, chọn trang 3 | Trang 1 có 20 tin, trang 3 có 5 tin; đổi trang không tải lại toàn trang | Main Flow |
| UC-27-AC-29 | Tin Nháp và tin Ẩn, mỗi tin có 1 ảnh trong bài và 1 tệp PDF | Admin đã đăng nhập mở ảnh và tải tệp (từ form sửa, xem trước hoặc link trực tiếp); Guest, Buyer, Artisan mở cùng các link | Admin mở được ảnh và tải được tệp của cả hai tin; Guest, Buyer, Artisan bị từ chối bằng ERR-UC28-01 | BR-15 |

### UC-28 — Đọc tin tức

#### Detail

| Field | Content |
|---|---|
| Use case ID | UC-28 |
| Description | Guest hoặc Buyer đọc tin công khai (khối ở trang chủ, danh sách lọc theo loại, chi tiết tin) mà không cần đăng nhập. Artisan đọc thêm tin nội bộ trong Kênh người bán. |
| Actors | Guest, Buyer (Buyer thao tác như Guest), Artisan |
| Channel | Khu mua hàng (tin công khai); Kênh người bán (mọi tin Đã đăng, chỉ Artisan) |
| Preconditions | • Tin công khai: không yêu cầu đăng nhập<br>• Tin nội bộ: Artisan đã đăng nhập và có quyền bán |
| Expected | **Thành công:**<br>• Người đọc xem được danh sách và chi tiết tin thuộc phạm vi của mình<br>**Thất bại:**<br>• Báo tin không khả dụng hoặc lỗi tải; không lộ tin ngoài phạm vi đọc |
| Screens | • Danh sách tin tức (bước 1-3)<br>• Chi tiết tin (bước 4)<br>• Danh sách và chi tiết tin trong Kênh người bán (Alt-01, Alt-02) |
| Notifications | Không có (thông báo tin mới do UC-27 phát, NT-UC27-01) |

#### Main Flow

Trigger: Guest chọn mục Tin tức ở menu.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Guest | Guest mở mục Tin tức. | Frontend tải danh sách tin công khai, mới nhất trước, 10 tin mỗi trang, có thanh chọn trang ở cuối danh sách; mỗi thẻ có ảnh bìa (nếu có), loại tin, tiêu đề, ngày đăng và đoạn đầu nội dung. |
| 2 | Guest | Guest chọn một loại tin ở bộ lọc. | Danh sách thu hẹp theo loại và về trang 1, không tải lại toàn trang. |
| 3 | Guest | Guest chọn một số trang, hoặc chọn Trước hoặc Sau ở thanh chọn trang. | Hiển thị đúng trang đó, 10 tin mỗi trang; số trang nằm trên địa chỉ để chia sẻ hoặc quay lại; không tải lại toàn trang. |
| 4 | Guest | Guest chọn một tin. | Trang chi tiết hiện với địa chỉ dễ đọc: tiêu đề, loại tin, ngày đăng, "Cập nhật lúc" nếu tin đã sửa, ảnh bìa, nội dung đúng định dạng đã soạn kèm ảnh trong bài, danh sách tệp đính kèm (tên, dung lượng, nút Tải về) và nút Về danh sách tin. |

Outcome: Guest đọc được tin công khai mà mình chọn.

#### Alternative Flows

##### Alt-01 — Artisan đọc trong Kênh người bán

- At step: 1 · Trigger: Artisan chọn mục Tin tức trong Kênh người bán.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Frontend | Frontend hiển thị mọi tin Đã đăng (cả công khai và nội bộ), mới nhất trước, 10 tin mỗi trang, có thanh chọn trang. | Mỗi thẻ có nhãn Công khai hoặc Nội bộ. |
| 2 | Artisan | Artisan lọc theo loại tin, chọn trang hoặc chọn một tin. | Chi tiết hiển thị ngay trong Kênh người bán, kèm nhãn Công khai hoặc Nội bộ; các thao tác lọc, chọn trang như bước 2 và 3. |

Resume: Kết thúc.

##### Alt-02 — Mở từ thông báo

- At step: 4 · Trigger: Artisan chọn thông báo NT-UC27-01 ở chuông thông báo (UC-29).

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Frontend | Frontend mở chi tiết tin trong Kênh người bán. | Artisan thấy đúng tin vừa được đăng. |

Resume: Kết thúc. Tin đã Ẩn hoặc không còn thì xử lý ở Alt-05.

##### Alt-03 — Mở từ trang chủ

- At step: 1 · Trigger: Guest chọn một tin ở khối Tin công khai của trang chủ (UC-06), hoặc chọn Xem tất cả ở khối đó.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Frontend | Frontend mở chi tiết tin đã chọn; hoặc mở danh sách tin tức nếu Guest chọn Xem tất cả. | Guest thấy chi tiết tin, hoặc danh sách tin công khai mới nhất trước. |

Resume: Mở chi tiết thì kết thúc; mở danh sách thì tiếp tục bước 2.

##### Alt-04 — Danh sách trống

- At step: 1 hoặc 2 · Trigger: Chưa có tin nào trong phạm vi người đọc, hoặc loại đã chọn chưa có tin.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Frontend | Frontend hiện "Chưa có tin nào" nếu không có tin; hiện "Chưa có tin thuộc loại này" và nút Bỏ lọc nếu đang lọc. | Danh sách trống, người đọc biết cách bỏ lọc. |

Resume: Chọn Bỏ lọc thì quay lại bước 1.

##### Alt-05 — ERR-UC28-01 — Tin không khả dụng

- At step: 4 · Trigger: Link trực tiếp hoặc thông báo trỏ tới tin Nháp, Ẩn, đã xóa hoặc không tồn tại; hoặc tin nội bộ được mở ở Khu mua hàng; hoặc link trực tiếp tới ảnh, tệp của các tin đó (Admin đã đăng nhập không bị từ chối ở ảnh và tệp, UC-27 BR-15).

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Backend | Backend từ chối hiển thị tin, dùng cùng một thông điệp cho mọi trường hợp trên. | Hiển thị ERR-UC28-01 và nút Về danh sách tin; không cho biết tin có tồn tại hay không. |

Resume: Kết thúc, hoặc quay lại bước 1.

##### Alt-06 — ERR-UC28-02 — Không tải được tin

- At step: 1 đến 4 · Trigger: Tải danh sách, trang tiếp theo hoặc chi tiết bị lỗi.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Frontend | Frontend giữ danh sách và bộ lọc hiện tại, hiện lỗi và nút Thử lại. | Hiển thị ERR-UC28-02. |

Resume: Chọn Thử lại thì lặp bước đang làm.

##### Alt-07 — Mở tin bằng địa chỉ cũ hoặc sai phần chữ

- At step: 4 · Trigger: Người đọc mở địa chỉ tin có đúng mã tin nhưng phần chữ khác tiêu đề hiện tại (tin đã đổi tiêu đề, hoặc địa chỉ bị sửa tay).

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Backend | Backend tìm tin theo mã tin trong địa chỉ, không theo phần chữ. Tin thuộc phạm vi người đọc thì chuyển hướng vĩnh viễn sang địa chỉ hiện tại. | Người đọc thấy đúng tin; thanh địa chỉ hiện địa chỉ mới. |
| 2 | Backend | Tin không thuộc phạm vi người đọc (Nháp, Ẩn, đã xóa, nội bộ ở Khu mua hàng) hoặc mã tin không tồn tại thì không chuyển hướng. | Hiển thị ERR-UC28-01 (Alt-05); không lộ tiêu đề của tin. |

Resume: Kết thúc.

#### Data Fields

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

#### Error Messages

| Code | Condition | Message | Display | Flow |
|---|---|---|---|---|
| ERR-UC28-01 | Tin không khả dụng | "Tin này không còn khả dụng hoặc không tồn tại." | Trang lỗi | Alt-05 |
| ERR-UC28-02 | Không tải được tin | "Không tải được tin tức. Vui lòng thử lại." | Inline | Alt-06 |

#### Business Rules

| ID | Rule | Source |
|---|---|---|
| BR-1 | Chỉ tin Đã đăng được đọc. Tin công khai: mọi người, ở Khu mua hàng. Tin nội bộ: chỉ Artisan, ở Kênh người bán. Artisan đọc được cả hai loại trong Kênh người bán. | Overview 3.6; Đề bài §3.3 |
| BR-2 | Tin Nháp và Ẩn không ai ngoài Admin thấy. Link tới tin không khả dụng (Nháp, Ẩn, đã xóa, không tồn tại, hoặc tin nội bộ mở ở Khu mua hàng) đều báo ERR-UC28-01, không cho biết tin có tồn tại hay không. | Overview 3.6; Nhóm đề xuất |
| BR-3 | Tin nội bộ chỉ mở trong Kênh người bán. Mở đường dẫn Kênh người bán khi chưa đăng nhập thì chuyển sang đăng nhập (UC-04, Alt-07); tài khoản chưa có quyền bán bị từ chối theo UC-04 (ERR-UC04-03, ERR-UC04-04). | Overview 3.6; UC-04 (BRD 01) |
| BR-4 | Trang chủ (UC-06) hiện tối đa 3 tin công khai Đã đăng mới nhất; tin nội bộ không hiện ở trang chủ. | Overview 3.6; UC-06 BR-2 (BRD 02) |
| BR-5 | Danh sách xếp mới nhất trước theo ngày đăng, 10 tin mỗi trang, phân trang theo số trang (Trước, các số trang, Sau; rút gọn bằng dấu ... khi nhiều trang), lọc được theo loại tin. Số trang và loại tin nằm trên địa chỉ để chia sẻ và quay lại; đổi loại tin thì về trang 1; số trang vượt quá thì hiện trang cuối. Lọc và chuyển trang cập nhật bằng API, không tải lại toàn trang. | Nhóm đề xuất (G-02); Đề bài §1 (tiêu chí 4, 8) |
| BR-6 | Địa chỉ trang tin dạng chữ dễ đọc theo tiêu đề, kết thúc bằng mã tin: `phần-chữ-mã-tin`. Phần chữ sinh từ tiêu đề: chữ thường, bỏ dấu tiếng Việt (đ thành d), ký tự đặc biệt bỏ, khoảng trắng thành dấu gạch ngang, tối đa 80 ký tự và không cắt giữa từ; tiêu đề không còn chữ nào thì dùng "tin-tuc". Mã tin là số cố định của tin nên hai tin trùng tiêu đề vẫn có hai địa chỉ khác nhau. Hệ thống tìm tin theo mã tin, không theo phần chữ: đổi tiêu đề thì địa chỉ mới dùng tiêu đề mới, địa chỉ cũ hoặc phần chữ bị sửa vẫn mở đúng tin bằng chuyển hướng vĩnh viễn sang địa chỉ hiện tại; riêng tin không thuộc phạm vi người đọc thì không chuyển hướng mà báo ERR-UC28-01. | Đề bài §1 (tiêu chí 8); Nhóm đề xuất |
| BR-7 | Người đang mở danh sách hoặc chi tiết khi tin bị ẩn hoặc đổi sang nội bộ chỉ thấy thay đổi ở lần tải tiếp theo. | Nhóm đề xuất |
| BR-8 | Đọc tin công khai không cần đăng nhập; Buyer thao tác như Guest. | Overview 3.6 |
| BR-9 | Chi tiết tin hiện tiêu đề, loại tin, ngày đăng, ảnh bìa (nếu có), nội dung kèm ảnh trong bài, danh sách tệp đính kèm tải về; tin sửa sau khi đăng hiện thêm "Cập nhật lúc". Trong Kênh người bán có thêm nhãn Công khai hoặc Nội bộ. | Nhóm đề xuất |
| BR-10 | Hệ thống không theo dõi ai đã đọc tin và không đánh dấu đã đọc hoặc chưa đọc theo từng tin; việc đọc thông báo thuộc UC-29. | Nhóm đề xuất |
| BR-11 | Artisan đọc được tin ở mọi trạng thái gian hàng (Đang chuẩn bị, Đang hoạt động, Tạm ngừng). | Overview 3.6; Nhóm đề xuất |
| BR-12 | Ảnh trong bài và tệp đính kèm chỉ mở và tải được bởi người đọc được tin đó, và bởi Admin đã đăng nhập ở mọi trạng thái tin để xem trước và quản lý (UC-27, BR-15). | Đề bài §1 (An ninh); Chốt 2026-11-18 |

#### Acceptance Criteria

| ID | Given | When | Then | Covers |
|---|---|---|---|---|
| UC-28-AC-01 | Có 25 tin công khai Đã đăng, thuộc nhiều loại | Guest mở mục Tin tức, chọn lọc Thị trường & Nguyên liệu, bỏ lọc, chọn trang 3, mở một tin | Trang 1 có 10 tin mới nhất trước; lọc chỉ còn tin loại Thị trường & Nguyên liệu và về trang 1; trang 3 có 5 tin; chi tiết hiện đủ tiêu đề, loại, ngày đăng, nội dung | Main Flow, BR-5, BR-9 |
| UC-28-AC-02 | Có tin công khai và tin nội bộ Đã đăng; Artisan đã đăng nhập | Artisan mở mục Tin tức trong Kênh người bán, lọc theo loại, mở một tin nội bộ | Thấy cả hai loại tin, mỗi thẻ có nhãn Công khai hoặc Nội bộ; chi tiết mở trong Kênh người bán kèm nhãn | Alt-01, BR-1 |
| UC-28-AC-03 | Admin vừa đăng một tin; Artisan có NT-UC27-01 trong chuông | Artisan chọn thông báo | Mở đúng chi tiết tin đó trong Kênh người bán | Alt-02 |
| UC-28-AC-04 | Trang chủ có khối Tin công khai | Guest chọn một tin ở khối; sau đó chọn Xem tất cả | Mở đúng chi tiết tin; Xem tất cả mở danh sách tin công khai trang 1, mới nhất trước | Alt-03, BR-4 |
| UC-28-AC-05 | Chưa có tin nào; hoặc chưa có tin loại Chính sách & Quy định | Guest mở mục Tin tức; hoặc lọc Chính sách & Quy định | Hiện "Chưa có tin nào"; hoặc "Chưa có tin thuộc loại này" kèm nút Bỏ lọc; Bỏ lọc thì hiện lại danh sách | Alt-04 |
| UC-28-AC-06 | Tin Nháp, tin Ẩn, tin nội bộ và địa chỉ tin không tồn tại | Guest mở lần lượt từng link trực tiếp; Artisan mở thông báo của tin vừa bị Ẩn | Mọi trường hợp hiện cùng ERR-UC28-01 và nút Về danh sách tin; không cho biết tin có tồn tại hay không | Alt-05, BR-2 |
| UC-28-AC-07 | API danh sách hoặc chi tiết lỗi | Guest lọc loại tin hoặc mở một tin | Hiển thị ERR-UC28-02; danh sách và bộ lọc đang chọn được giữ; có nút Thử lại | Alt-06 |
| UC-28-AC-08 | Có tin công khai và tin nội bộ Đã đăng | Guest, Buyer và Artisan lần lượt xem danh sách tin | Guest và Buyer chỉ thấy tin công khai ở Khu mua hàng; Artisan thấy cả hai ở Kênh người bán | BR-1 |
| UC-28-AC-09 | Guest chưa đăng nhập; Buyer chưa có hồ sơ bán hàng; Buyer có hồ sơ Chờ duyệt | Mở đường dẫn Tin tức trong Kênh người bán | Guest được chuyển sang đăng nhập; Buyer chưa có hồ sơ gặp ERR-UC04-03; Buyer có hồ sơ chưa duyệt gặp ERR-UC04-04; không ai thấy nội dung tin nội bộ | BR-3 |
| UC-28-AC-10 | Có 5 tin công khai và 2 tin nội bộ Đã đăng | Guest mở trang chủ | Khối tin chỉ có 3 tin công khai mới nhất; không có tin nội bộ | BR-4 |
| UC-28-AC-11 | Có 45 tin công khai (5 trang) | Guest chọn trang 4, rồi sao chép địa chỉ và mở ở tab mới; lọc một loại; sửa địa chỉ thành trang 99 | Mỗi trang 10 tin mới nhất trước theo ngày đăng; địa chỉ có số trang nên mở lại đúng trang 4; đổi loại tin thì về trang 1; trang 99 hiện trang cuối; chuyển trang và lọc không tải lại toàn trang | BR-5 |
| UC-28-AC-12 | Tin có tiêu đề "Lịch cắt điện tuần 47" (mã tin 1024) | Guest mở trang chi tiết | Địa chỉ có dạng `lich-cat-dien-tuan-47-1024`: chữ thường, không dấu, nối bằng dấu gạch ngang, kết thúc bằng mã tin | BR-6 |
| UC-28-AC-13 | Guest đang mở danh sách có tin X; Admin Ẩn tin X | Guest chưa làm mới; sau đó mở tin X; rồi làm mới danh sách | Trước khi làm mới danh sách vẫn còn thẻ tin X; mở tin X hiện ERR-UC28-01; sau khi làm mới tin X không còn | BR-7 |
| UC-28-AC-14 | Guest chưa đăng nhập; Buyer đã đăng nhập | Mở mục Tin tức và một tin công khai | Cả hai đọc được, không bị chuyển sang đăng nhập; Buyer thấy giống Guest | BR-8 |
| UC-28-AC-15 | Tin đã sửa sau khi đăng; Artisan và Guest cùng mở | Xem chi tiết | Có dòng "Cập nhật lúc"; Artisan thấy thêm nhãn Công khai hoặc Nội bộ, Guest không có nhãn này; tin chưa sửa thì không có "Cập nhật lúc" | BR-9 |
| UC-28-AC-16 | Artisan mở nhiều tin; Admin xem quản trị | Artisan đọc xong, Admin kiểm tra | Không có đánh dấu đã đọc hoặc chưa đọc theo từng tin; Admin không thấy ai đã đọc | BR-10 |
| UC-28-AC-17 | Ba Artisan có gian hàng Đang chuẩn bị, Đang hoạt động, Tạm ngừng | Mỗi người mở mục Tin tức trong Kênh người bán | Cả ba thấy cùng danh sách tin | BR-11 |
| UC-28-AC-18 | Tin công khai có 1 ảnh trong bài và 1 tệp PDF | Guest mở chi tiết và chọn Tải về; sau đó Admin Ẩn tin và Guest mở lại link tệp | Chi tiết hiện ảnh trong bài và dòng tệp (tên, dung lượng, Tải về); tải được tệp; sau khi Ẩn link tệp bị từ chối bằng ERR-UC28-01 với Guest, còn Admin vẫn tải được (UC-27-AC-29) | BR-9, BR-12 |
| UC-28-AC-19 | Hai tin công khai cùng tiêu đề "Thông báo lễ Tết" (mã 1030 và 1031); một tiêu đề chỉ có ký tự đặc biệt "!!!" (mã 1032); một tiêu đề dài hơn 80 ký tự | Guest mở lần lượt từng tin | Hai tin có hai địa chỉ khác nhau (`thong-bao-le-tet-1030`, `thong-bao-le-tet-1031`) và mở đúng từng tin; tin "!!!" có địa chỉ `tin-tuc-1032`; phần chữ của tin dài tối đa 80 ký tự, không cắt giữa từ | BR-6 |
| UC-28-AC-20 | Tin công khai Đã đăng có địa chỉ `lich-cat-dien-tuan-47-1024`; Admin đổi tiêu đề thành "Lịch cắt điện tuần 48" | Guest mở địa chỉ cũ; Guest mở địa chỉ cũ sau khi Admin Ẩn tin; Guest mở `sai-chu-1024` | Địa chỉ cũ chuyển hướng sang `lich-cat-dien-tuan-48-1024` và hiện đúng tin; tin Ẩn hiện ERR-UC28-01 và không chuyển hướng, không lộ tiêu đề; `sai-chu-1024` khi tin còn công khai cũng chuyển hướng về địa chỉ hiện tại | Alt-07, BR-6 |

## IV. Open Questions

| ID | Question | Affects | Decision |
|---|---|---|---|
| G-01 | Thông báo tin mới gửi cho Artisan với mọi tin đã đăng hay chỉ tin nội bộ? Overview ghi sự kiện là "Tin nội bộ mới". Đang theo hướng: gửi cho mọi tin đã đăng (cả công khai), vì Artisan đọc cả hai loại trong Kênh người bán và tin công khai như lịch cúp điện cũng cần báo tới hộ. | UC-27 BR-7, NT-UC27-01; UC-29 (BRD 07) | Open |
| G-02 | UC-06 hiện khối 3 tin công khai nhưng chưa có lối vào danh sách đầy đủ, nên tin cũ hơn 3 tin mới nhất không tới được. | UC-28 Main Flow, Alt-03, BR-5; UC-06 (BRD 02) | Đã chốt 2026-11-18: danh sách tin phân trang theo số trang (10 tin mỗi trang, Trước, các số trang, Sau), có mục Tin tức ở menu và nút Xem tất cả ở khối tin trang chủ. UC-06 ở BRD `02-showcase` đã có hai lối vào này |
| G-03 | Tin có cần ngày hết hiệu lực hoặc tự ẩn (ví dụ lịch cắt điện đã qua ngày) không? | UC-27 phạm vi | Đã chốt 2026-11-18: không; Admin tự Ẩn |
| G-04 | Admin được sửa tin đã đăng thì người đọc có cần được báo không? | UC-27 Alt-06, BR-9; UC-28 BR-9 | Đã chốt 2026-11-18: không gửi thông báo, chỉ hiện "Cập nhật lúc" trên tin |
| G-05 | Khi đăng lại tin đã ẩn, ngày đăng giữ lần đầu hay lấy ngày mới? | UC-27 Alt-09, BR-8; UC-28 BR-5 | Đã chốt 2026-11-18: giữ lần đầu; muốn đưa tin lên đầu danh sách thì tạo tin mới |
| G-06 | Loại tin gồm những loại nào? | UC-27 BR-2, Data Fields; UC-28 bộ lọc | Đã chốt 2026-11-18: năm loại cố định (Thị trường & Nguyên liệu, Hạ tầng & Vận hành, Chính sách & Quy định, Hoạt động & Sự kiện làng nghề, Thông báo chung) |
| G-07 | Nội dung tin có cho định dạng, chèn ảnh, tệp đính kèm không? | UC-27 Data Fields, BR-10, BR-15; UC-28 BR-9, BR-12 | Đã chốt 2026-11-18: có; định dạng chữ, bảng, liên kết, ảnh trong bài (tối đa 10), ảnh bìa, tệp đính kèm (tối đa 5, PDF, DOC, DOCX, XLS, XLSX, mỗi tệp tối đa 10 MB); chỉ giữ định dạng cho phép |
