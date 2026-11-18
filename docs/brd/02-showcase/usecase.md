---
title: "02 — Triển lãm số"
group: 02-showcase
type: brd
status: draft
version: 0.1.0
date: 2026-11-18
author: Khánh Chi
---

# 02 — Triển lãm số

Change history: [CHANGELOG.md](CHANGELOG.md)

## I. Overview

Nhóm chức năng trưng bày làng nghề: trang chủ giới thiệu làng nghề, gian hàng của từng xưởng, sản phẩm và phân loại. Artisan thiết lập gian hàng, mở gian hàng để công khai và đăng sản phẩm; Guest và Buyer khám phá xưởng, tìm kiếm và xem chi tiết sản phẩm; Admin quản lý phân loại. Kết quả cuối cùng là một "triển lãm số" nơi mỗi xưởng có không gian riêng, sản phẩm đang hiển thị tìm được dễ dàng và dẫn tới mua hàng, yêu cầu báo giá và truy xuất nguồn gốc.

### Terms

| Term | Meaning |
|---|---|
| Gian hàng | Không gian riêng của một xưởng trên sàn: giới thiệu, ảnh, chứng nhận, sản phẩm. Mỗi tài khoản Artisan quản lý đúng một gian hàng. |
| Trạng thái gian hàng | Đang chuẩn bị (chỉ chủ xưởng xem trước), Đang hoạt động, Tạm ngừng nhận giao dịch mới. |
| Gian hàng đã mở | Gian hàng đang ở trạng thái Đang hoạt động hoặc Tạm ngừng; hiển thị công khai. |
| Checklist mở gian hàng | Danh sách điều kiện xưởng phải đạt trước khi tự mở gian hàng. Admin không duyệt lần hai. |
| Sản phẩm có sẵn | Sản phẩm có giá và số lượng, mua qua giỏ hàng. |
| Sản phẩm chỉ nhận đặt riêng | Sản phẩm không có giá niêm yết; Buyer gửi yêu cầu báo giá. |
| Sản phẩm công khai | Sản phẩm đang Hiển thị thuộc gian hàng đã mở. |
| Phân loại | Nhóm sản phẩm do Admin quản lý, dùng để lọc xưởng và sản phẩm. |
| Phương thức giao | Cách xưởng giao hàng: Nhận tại xưởng, Xưởng tự giao (phí cố định), GHN (Should). |

## II. Scope

| Business topic | Channel | Use case |
|---|---|---|
| Giới thiệu làng nghề, lối vào các khu vực của sàn | Khu mua hàng | UC-06 |
| Cập nhật thông tin gian hàng, phương thức giao, xem trước | Kênh người bán | UC-07 |
| Khám phá xưởng: tìm theo tên, lọc phân loại và đón khách | Khu mua hàng | UC-08 |
| Đăng và quản lý sản phẩm | Kênh người bán | UC-09 |
| Xem danh sách, tìm kiếm, lọc sản phẩm | Khu mua hàng | UC-10 |
| Xem chi tiết sản phẩm | Khu mua hàng | UC-11 |
| Quản lý phân loại | Trang quản trị | UC-12 |
| Mở, tạm ngừng, mở lại gian hàng | Kênh người bán | UC-13 |

**Ngoài phạm vi:** Yêu thích, bán chạy, gợi ý cá nhân hóa, nội dung nổi bật; biến thể kích thước, màu (Should, chưa mô tả; mỗi sản phẩm mặc định một biến thể); khóa xưởng hoặc ẩn sản phẩm vi phạm (EN-01); công bố năng lực xưởng (EN-02); vị trí bản đồ và trạng thái đón khách (UC-25, nhóm `05-village-map`); lô sản xuất và QR (nhóm `03-traceability`).

**Liên quan**

| ID | Tài liệu | Quan hệ |
|---|---|---|
| BRD-01 | [01-account](../01-account/usecase.md) | UC-01 điền sẵn thông tin gian hàng; UC-02 duyệt yêu cầu đổi tên, địa chỉ do UC-07 gửi (Alt-05) và xác minh chứng nhận (Alt-06). |
| BRD-03 | [03-traceability](../03-traceability/usecase.md) | UC-11 liệt kê lô của sản phẩm và dẫn tới trang QR (UC-17). |
| BRD-04 | [04-quotation](../04-quotation/usecase.md) | UC-11 chuyển sang UC-18 kèm sản phẩm đang xem; UC-08 chuyển kèm xưởng, không kèm sản phẩm; UC-06 có lối vào Đặt hàng theo yêu cầu (gửi chung). Gian hàng Tạm ngừng (UC-13) chỉ được từ chối yêu cầu đã nhận, không gửi hay cập nhật báo giá (UC-19 BR-8). |
| BRD-05 | `05-village-map` | UC-25 cung cấp vị trí cho checklist; UC-26 hiển thị pin dẫn về UC-08. |
| BRD-06 | `06-news` | UC-28 cung cấp tin công khai cho trang chủ, và là nơi mở ra từ mục Tin tức ở menu và nút Xem tất cả ở khối tin (UC-06). |
| BRD-07 | `07-notification` | UC-29 hiển thị NT-UC07-01. |
| BRD-08 | `08-order` | UC-30, UC-31 dùng phương thức giao và phí giao cấu hình ở UC-07. |

## III. Use Cases

| Use case ID | Use case | Description |
|---|---|---|
| UC-06 | Xem trang chủ, giới thiệu làng nghề | Guest hoặc Buyer đọc giới thiệu làng nghề và đi tới xưởng, sản phẩm, bản đồ, tin tức, đặt hàng theo yêu cầu |
| UC-07 | Quản lý thông tin gian hàng | Artisan cập nhật thông tin và phương thức giao, xem trước hoặc lưu luôn; đổi tên hoặc địa chỉ thì gửi Admin duyệt |
| UC-08 | Khám phá xưởng | Guest hoặc Buyer tìm xưởng theo tên, lọc phân loại và đón khách, xem gian hàng |
| UC-09 | Quản lý sản phẩm | Artisan tạo, sửa, đổi loại, ẩn, hiển thị lại, xóa nháp sản phẩm |
| UC-10 | Xem danh sách, tìm kiếm, lọc sản phẩm | Guest hoặc Buyer xem trang Sản phẩm, tìm và lọc sản phẩm công khai |
| UC-11 | Xem chi tiết sản phẩm | Guest hoặc Buyer xem sản phẩm, các lô và đi tới mua hoặc yêu cầu báo giá |
| UC-12 | Quản lý phân loại | Admin thêm, sửa tên, ẩn, hiện lại phân loại |
| UC-13 | Quản lý trạng thái hoạt động của gian hàng | Artisan mở gian hàng, tạm ngừng nhận giao dịch mới, mở lại |

### UC-06 — Xem trang chủ, giới thiệu làng nghề

#### Detail

| Field | Content |
|---|---|
| Use case ID | UC-06 |
| Description | Guest hoặc Buyer mở trang chủ để đọc giới thiệu làng nghề và đi tới các khu vực chính của sàn. |
| Actors | Guest, Buyer (Buyer thao tác như Guest) |
| Channel | Khu mua hàng |
| Preconditions | • Không yêu cầu đăng nhập |
| Expected | **Thành công:**<br>• Trang chủ hiển thị giới thiệu làng nghề và các khối nội dung công khai<br>• Người dùng đi được tới từng khu vực chính<br>**Thất bại:**<br>• Khối tải lỗi hiện thông báo riêng, các khối khác vẫn dùng được |
| Screens | • Trang chủ (bước 1-3) |
| Notifications | Không có |

#### Main Flow

Trigger: Người dùng mở địa chỉ trang chủ của vCraft.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Guest | Guest mở trang chủ. | Hiển thị phần giới thiệu làng nghề: tên làng, câu chuyện ngắn, ảnh lớn. |
| 2 | Frontend | Frontend tải riêng từng khối: phân loại, xưởng mới mở, sản phẩm mới nhất, tin công khai. | Mỗi khối hiện khi tải xong, không chờ các khối khác. |
| 3 | Guest | Guest chọn một lối vào: Bản đồ, Xưởng, Sản phẩm, Tin tức (ở menu), một phân loại, một xưởng, một sản phẩm, một tin, "Xem tất cả" ở khối xưởng, sản phẩm hoặc tin, hoặc Đặt hàng theo yêu cầu. | Chuyển tới màn hình tương ứng (UC-26, UC-08, UC-10, UC-11, UC-28, UC-18). Mục Tin tức ở menu và "Xem tất cả" ở khối tin mở danh sách tin công khai ở trang 1, mới nhất trước (UC-28). |

Outcome: Guest vào được các khu vực chính của sàn.

#### Alternative Flows

##### Alt-01 — ERR-UC06-01 — Khối tải lỗi

- At step: 2 · Trigger: Một khối không tải được.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Frontend | Frontend hiện lỗi và nút Thử lại ngay tại khối đó. | Hiển thị ERR-UC06-01; các khối khác không bị ảnh hưởng. |

Resume: Guest chọn Thử lại thì lặp bước 2 cho khối đó.

##### Alt-02 — Khối chưa có gì để hiển thị

- At step: 2 · Trigger: Chưa có phân loại, gian hàng đã mở, sản phẩm Hiển thị hoặc tin công khai nào để đưa vào khối.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Frontend | Frontend thay khối bằng thông điệp "Sắp có nội dung". Khối tin trống thì không hiện nút "Xem tất cả". | Khối hiển thị trạng thái trống. Mục Tin tức ở menu vẫn mở được danh sách (UC-28, Alt-04). |

Resume: Tiếp tục bước 3.

##### Alt-03 — Người dùng có quyền bán

- At step: 1 · Trigger: Người dùng đã đăng nhập và có quyền bán.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Frontend | Frontend thêm nút Kênh người bán vào menu. | Người dùng vào Kênh người bán không phải đăng nhập lại. |

Resume: Tiếp tục bước 2.

#### Data Fields

| Field | Format | Validation | Required | Ghi chú |
|---|---|---|---|---|
| Giới thiệu làng nghề | Text và ảnh | Nội dung cố định (BR-4) | Có | Ví dụ: "Làng mây tre đan Phú Vinh" |
| Phân loại | Danh sách | Chỉ phân loại đang Hiển thị | Có | Từ UC-12 |
| Xưởng mới mở | Danh sách tối đa 8 | Chỉ gian hàng đã mở (kể cả Tạm ngừng); xếp theo thời điểm mở lần đầu, mới nhất trước (BR-5) | Không | Ảnh đại diện, tên xưởng. Chọn "Xem tất cả" để sang UC-08 |
| Sản phẩm mới nhất | Danh sách tối đa 12 | Chỉ sản phẩm công khai, mới nhất xếp trước | Không | Ảnh, tên, giá, xưởng. Chọn "Xem tất cả" để sang UC-10 |
| Tin công khai | Danh sách tối đa 3 | Chỉ tin Đã đăng đánh dấu công khai; mới nhất trước theo ngày đăng | Không | Từ UC-28. Mỗi tin hiện ảnh bìa (nếu có), loại tin, tiêu đề, ngày đăng. Chọn một tin để sang chi tiết tin; chọn "Xem tất cả" để sang danh sách tin (UC-28) |
| Mục Tin tức ở menu | Mục menu | Hiện ở mọi trang của Khu mua hàng, không cần đăng nhập (BR-6) | Có | Mở danh sách tin công khai ở trang 1 (UC-28) |

#### Error Messages

| Code | Condition | Message | Display | Flow |
|---|---|---|---|---|
| ERR-UC06-01 | Khối không tải được | "Không tải được nội dung. Vui lòng thử lại." | Inline | Alt-01 |

#### Business Rules

| ID | Rule | Source |
|---|---|---|
| BR-1 | Chỉ hiển thị gian hàng đã mở và sản phẩm đang Hiển thị. | Overview 3.2 |
| BR-2 | Chỉ tin công khai mới hiện ở trang chủ; tin nội bộ chỉ Artisan đọc trong Kênh người bán. | Overview 3.6 |
| BR-3 | Mỗi khối tải riêng bằng API, cập nhật giao diện không tải lại cả trang. | Đề bài §1 (tiêu chí 4) |
| BR-4 | Nội dung giới thiệu làng nghề là nội dung cố định, chưa có chức năng chỉnh sửa trong MVP1. | Chốt 2026-11-18 |
| BR-5 | Khối Xưởng mới mở xếp theo thời điểm mở gian hàng lần đầu (UC-13), mới nhất trước; mở lại sau Tạm ngừng không đổi thứ tự. | Chốt 2026-11-18 |
| BR-6 | Menu Khu mua hàng có mục Tin tức, hiện ở mọi trang kể cả với Guest. Khối tin ở trang chủ chỉ cho xem 3 tin mới nhất; tin cũ hơn đọc qua mục Tin tức hoặc "Xem tất cả" (danh sách phân trang ở UC-28). | Chốt 2026-11-18 |

#### Acceptance Criteria

| ID | Given | When | Then | Covers |
|---|---|---|---|---|
| UC-06-AC-01 | Có gian hàng đã mở, sản phẩm Hiển thị và tin công khai | Guest mở trang chủ | Hiện giới thiệu làng nghề, các khối phân loại, xưởng, sản phẩm, tin; chọn lối vào thì chuyển đúng màn hình | Main Flow |
| UC-06-AC-02 | Khối sản phẩm tải lỗi | Guest xem trang chủ | Khối đó hiện ERR-UC06-01 và nút Thử lại; các khối khác vẫn hiển thị | Alt-01 |
| UC-06-AC-03 | Chưa có xưởng nào mở gian hàng | Guest mở trang chủ | Khối xưởng hiện "Sắp có nội dung"; các khối còn lại hiển thị bình thường | Alt-02 |
| UC-06-AC-04 | Người dùng đăng nhập có quyền bán | Mở trang chủ | Menu có nút Kênh người bán | Alt-03 |
| UC-06-AC-05 | Có gian hàng Đang chuẩn bị và sản phẩm Ẩn | Guest mở trang chủ | Xưởng và sản phẩm đó không xuất hiện | BR-1 |
| UC-06-AC-06 | Có tin nội bộ và tin công khai | Guest mở trang chủ | Chỉ thấy tin công khai | BR-2 |
| UC-06-AC-07 | Một khối phản hồi chậm | Guest mở trang chủ | Các khối khác hiển thị trước, không phải chờ | BR-3 |
| UC-06-AC-08 | Bất kỳ người dùng nào | Mở trang chủ | Giới thiệu làng nghề giống nhau; không có thao tác sửa nội dung này | BR-4 |
| UC-06-AC-09 | Xưởng A mở gian hàng trước xưởng B; A tạm ngừng rồi mở lại | Guest mở trang chủ | B đứng trước A trong khối Xưởng mới mở; mở lại không đưa A lên đầu | BR-5 |
| UC-06-AC-10 | Guest ở trang chủ | Chọn Đặt hàng theo yêu cầu | Chuyển sang UC-18 (gửi chung); Guest chưa đăng nhập thì đăng nhập trước rồi quay lại | Main Flow |
| UC-06-AC-11 | Có 25 tin công khai Đã đăng | Guest mở trang chủ, rồi chọn "Xem tất cả" ở khối tin | Khối tin chỉ hiện 3 tin mới nhất; "Xem tất cả" mở danh sách tin công khai ở trang 1, mới nhất trước, từ đó chọn được trang 2, 3 để đọc tin cũ hơn (UC-28-AC-04) | Main Flow, BR-6 |
| UC-06-AC-12 | Guest chưa đăng nhập, ở trang chủ hoặc một trang bất kỳ của Khu mua hàng | Chọn mục Tin tức ở menu | Mục Tin tức có ở menu; mở danh sách tin công khai ở trang 1 mà không bị chuyển sang đăng nhập | BR-6 |
| UC-06-AC-13 | Chưa có tin công khai nào | Guest mở trang chủ, rồi chọn mục Tin tức ở menu | Khối tin hiện "Sắp có nội dung", không có nút "Xem tất cả"; mục Tin tức mở danh sách trống theo UC-28 (Alt-04) | Alt-02, BR-6 |

### UC-07 — Quản lý thông tin gian hàng

#### Detail

| Field | Content |
|---|---|
| Use case ID | UC-07 |
| Description | Artisan cập nhật thông tin gian hàng (giới thiệu, ảnh, chứng nhận, số điện thoại liên hệ, phương thức giao), xem trước rồi lưu hoặc lưu luôn; đổi tên hoặc địa chỉ đã duyệt thì gửi Admin duyệt. |
| Actors | Artisan |
| Channel | Kênh người bán |
| Preconditions | • Đã đăng nhập và có quyền bán (hồ sơ đã được duyệt ở UC-02)<br>• Gian hàng ở bất kỳ trạng thái nào |
| Expected | **Thành công:**<br>• Thông tin mới được lưu và có hiệu lực ngay<br>• Riêng tên hoặc địa chỉ: yêu cầu chờ duyệt, thông tin cũ vẫn hiển thị, Admin nhận NT-UC07-01<br>**Thất bại:**<br>• Hiển thị lỗi, không lưu thay đổi |
| Screens | • Thông tin gian hàng (bước 1-2, 4-6)<br>• Phương thức giao (bước 3)<br>• Xem trước gian hàng (Alt-07) |
| Notifications | NT-UC07-01 |

#### Main Flow

Trigger: Artisan chọn mục Gian hàng trong Kênh người bán.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Artisan | Artisan mở mục Gian hàng. | Hiển thị thông tin hiện tại (điền sẵn từ hồ sơ UC-01) và trạng thái gian hàng. |
| 2 | Artisan | Artisan sửa phần giới thiệu: mô tả, ảnh đại diện, ảnh xưởng, chứng nhận, số điện thoại liên hệ công khai (tối đa 3 số). | Nội dung sửa hiển thị trong form. |
| 3 | Artisan | Artisan thiết lập phương thức giao: bật hoặc tắt Nhận tại xưởng, Xưởng tự giao (nhập phí cố định), GHN. | Thiết lập hiển thị trong form. |
| 4 | Artisan | Artisan chọn Lưu, hoặc chọn Xem trước trước khi lưu (Alt-07). | Dữ liệu được gửi lên kiểm tra khi chọn Lưu. |
| 5 | Backend | Backend kiểm tra dữ liệu và lưu thay đổi. | Thông tin mới có hiệu lực ngay. |
| 6 | Frontend | Frontend báo lưu thành công. | Artisan thấy thông tin đã cập nhật. |

Outcome: Thông tin gian hàng đã lưu và hiển thị đúng ở mọi nơi.

#### Alternative Flows

##### Alt-01 — ERR-UC07-01 — Dữ liệu không hợp lệ

- At step: 4 · Trigger: Một trường sai quy tắc (tên trống, số điện thoại liên hệ sai định dạng, trùng nhau hoặc quá 3 số, phí giao không hợp lệ).

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Backend | Backend từ chối lưu và đánh dấu trường sai. | Hiển thị ERR-UC07-01 tại trường sai. |

Resume: Quay lại bước 2, giữ nguyên dữ liệu đã nhập.

##### Alt-02 — ERR-UC07-02 — Ảnh không hợp lệ

- At step: 2 · Trigger: Ảnh sai định dạng, lớn hơn 10 MB hoặc vượt số lượng cho phép.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Frontend | Frontend không nhận ảnh. | Hiển thị ERR-UC07-02. |

Resume: Chọn ảnh khác, lặp lại bước 2.

##### Alt-03 — Đổi tên hoặc địa chỉ đã duyệt

- At step: 5 · Trigger: Artisan đổi tên xưởng hoặc địa chỉ.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Backend | Backend lưu yêu cầu đổi ở trạng thái chờ duyệt; các thay đổi khác lưu bình thường. | Tên và địa chỉ cũ vẫn hiển thị công khai. |
| 2 | Backend | Backend thông báo cho Admin. | NT-UC07-01 được gửi. |
| 3 | Frontend | Frontend hiện dòng "Yêu cầu đổi tên hoặc địa chỉ đang chờ BQL duyệt". | Artisan biết yêu cầu đang chờ. |

Resume: Tiếp tục bước 6. Kết quả duyệt xử lý ở UC-02.

##### Alt-04 — ERR-UC07-03 — Đang có yêu cầu đổi chờ duyệt

- At step: 4 · Trigger: Artisan đổi tên hoặc địa chỉ khi yêu cầu trước vẫn đang chờ duyệt.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Backend | Backend từ chối phần đổi tên hoặc địa chỉ. | Hiển thị ERR-UC07-03. |

Resume: Quay lại bước 2 để sửa các trường khác.

##### Alt-05 — ERR-UC07-04 — Tắt hết phương thức giao khi đang bán hàng có sẵn

- At step: 4 · Trigger: Gian hàng có sản phẩm có sẵn đang Hiển thị và Artisan tắt tất cả phương thức giao.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Backend | Backend từ chối lưu phần phương thức giao. | Hiển thị ERR-UC07-04. |

Resume: Quay lại bước 3.

##### Alt-06 — ERR-UC07-05 — Không có quyền

- At step: 1 · Trigger: Tài khoản chưa có quyền bán hoặc thao tác trên gian hàng không phải của mình.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Backend | Backend từ chối thao tác. | Hiển thị ERR-UC07-05; không có dữ liệu nào thay đổi. |

Resume: Kết thúc.

##### Alt-07 — Xem trước trước khi lưu

- At step: 4 · Trigger: Artisan chọn Xem trước thay vì Lưu.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Frontend | Frontend hiển thị trang gian hàng đúng như người mua sẽ thấy (UC-08) với dữ liệu đang nhập, kể cả khi gian hàng Đang chuẩn bị. | Chưa lưu và chưa công khai; tên hoặc địa chỉ mới hiện như đã áp dụng dù chưa được duyệt. |
| 2 | Artisan | Artisan chọn Quay lại sửa hoặc chọn Lưu. | Quay lại sửa thì dữ liệu đang nhập được giữ nguyên. |

Resume: Quay lại sửa thì về bước 2; Lưu thì tiếp tục bước 5. Muốn xem gian hàng đã lưu, Artisan mở trang gian hàng của mình (UC-08, Alt-05).

#### Data Fields

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

#### Error Messages

| Code | Condition | Message | Display | Flow |
|---|---|---|---|---|
| ERR-UC07-01 | Dữ liệu không hợp lệ | "Thông tin chưa hợp lệ. Vui lòng kiểm tra các trường được đánh dấu." | Inline | Alt-01 |
| ERR-UC07-02 | Ảnh không hợp lệ | "Ảnh không hợp lệ. Chỉ chấp nhận JPG, PNG, WebP, tối đa 10 MB." | Inline | Alt-02 |
| ERR-UC07-03 | Đang có yêu cầu đổi chờ duyệt | "Yêu cầu đổi tên hoặc địa chỉ trước đó đang chờ BQL duyệt. Vui lòng chờ kết quả." | Inline | Alt-04 |
| ERR-UC07-04 | Tắt hết phương thức giao khi đang bán hàng có sẵn | "Gian hàng đang bán hàng có sẵn nên cần ít nhất một phương thức giao." | Inline | Alt-05 |
| ERR-UC07-05 | Không có quyền | "Bạn không có quyền thực hiện thao tác này." | Trang lỗi | Alt-06 |

#### Notifications

| Template ID | Trigger (step) | Channel | Recipient | Title | Body |
|---|---|---|---|---|---|
| NT-UC07-01 | Alt-03 bước 2 | In-app | Mọi Admin | Xưởng xin đổi thông tin | "Xưởng {{workshopName}} xin đổi tên hoặc địa chỉ và đang chờ duyệt." |

#### Business Rules

| ID | Rule | Source |
|---|---|---|
| BR-1 | Mỗi tài khoản quản lý đúng một gian hàng và chỉ sửa được gian hàng của mình. | Overview mục 1 |
| BR-2 | Đổi tên xưởng hoặc địa chỉ đã duyệt phải qua Admin duyệt (UC-02); trong lúc chờ vẫn hiển thị thông tin cũ. | Overview 3.1 |
| BR-3 | Mỗi lúc chỉ có một yêu cầu đổi tên hoặc địa chỉ đang chờ duyệt. | Nhóm đề xuất |
| BR-4 | Sửa thông tin được ở mọi trạng thái gian hàng, kể cả Tạm ngừng. | Nhóm đề xuất |
| BR-5 | Chứng nhận có nhãn "Do xưởng cung cấp" hoặc "BQL đã xác minh". | Overview 3.2 |
| BR-6 | Gian hàng có sản phẩm có sẵn đang Hiển thị phải còn ít nhất một phương thức giao. | Nhóm đề xuất |
| BR-7 | Ảnh tải lên được backend thu nhỏ để danh sách không tải ảnh gốc. | Đề bài §4.4 |
| BR-8 | Thông tin từ hồ sơ UC-01 được điền sẵn. | Overview 3.2 |
| BR-9 | Nhãn "BQL đã xác minh" do Admin gắn ở UC-02 (Alt-06); chứng nhận chưa xác minh mang nhãn "Do xưởng cung cấp". Xưởng sửa tên hoặc thay ảnh chứng nhận thì nhãn về "Do xưởng cung cấp". Admin không nhận thông báo khi có chứng nhận mới. | Chốt 2026-11-18 |
| BR-10 | Liên hệ công khai là danh sách 1–3 số điện thoại do xưởng nhập riêng, không dùng số trong hồ sơ bán hàng (chỉ Admin xem). | Chốt 2026-11-18 |

#### Acceptance Criteria

| ID | Given | When | Then | Covers |
|---|---|---|---|---|
| UC-07-AC-01 | Artisan đã đăng nhập, gian hàng bất kỳ | Sửa mô tả và phương thức giao rồi chọn Lưu | Thay đổi được lưu, hiển thị ngay | Main Flow |
| UC-07-AC-02 | Artisan để trống tên xưởng | Chọn Lưu | Hiển thị ERR-UC07-01; dữ liệu đã nhập được giữ; không lưu | Alt-01 |
| UC-07-AC-03 | Artisan chọn ảnh PDF hoặc lớn hơn 10 MB | Tải ảnh | Hiển thị ERR-UC07-02; ảnh không được nhận | Alt-02 |
| UC-07-AC-04 | Gian hàng đã duyệt, không có yêu cầu chờ | Đổi tên xưởng rồi Lưu | Tên cũ vẫn hiển thị công khai; yêu cầu chờ duyệt; Admin nhận NT-UC07-01 | Alt-03, BR-2 |
| UC-07-AC-05 | Đang có yêu cầu đổi tên chờ duyệt | Đổi địa chỉ rồi Lưu | Hiển thị ERR-UC07-03; không tạo yêu cầu thứ hai | Alt-04, BR-3 |
| UC-07-AC-06 | Gian hàng có sản phẩm có sẵn Hiển thị | Tắt cả ba phương thức giao | Hiển thị ERR-UC07-04; thiết lập cũ giữ nguyên | Alt-05, BR-6 |
| UC-07-AC-07 | Tài khoản Buyer chưa có quyền bán, hoặc Artisan khác | Mở hoặc gửi thay đổi gian hàng này | Hiển thị ERR-UC07-05; dữ liệu không đổi | Alt-06, BR-1 |
| UC-07-AC-08 | Gian hàng đang Tạm ngừng | Sửa mô tả rồi Lưu | Lưu thành công | BR-4 |
| UC-07-AC-09 | Gian hàng có chứng nhận | Guest xem trang gian hàng | Mỗi chứng nhận hiện nhãn "Do xưởng cung cấp" hoặc "BQL đã xác minh" | BR-5 |
| UC-07-AC-10 | Artisan tải ảnh hợp lệ | Lưu rồi xem danh sách xưởng | Danh sách dùng ảnh đã thu nhỏ, không tải ảnh gốc | BR-7 |
| UC-07-AC-11 | Hồ sơ UC-01 đã được duyệt | Artisan mở mục Gian hàng lần đầu | Tên, địa chỉ, ảnh xưởng, năm bắt đầu đã được điền sẵn | BR-8 |
| UC-07-AC-12 | Chứng nhận đã có nhãn "BQL đã xác minh" | Artisan thay ảnh chứng nhận rồi Lưu | Nhãn về "Do xưởng cung cấp"; chứng nhận chờ Admin xác minh lại | BR-9 |
| UC-07-AC-13 | Artisan đang sửa thông tin, chưa lưu | Chọn Xem trước, chọn Quay lại sửa, rồi chọn Xem trước và Lưu | Xem trước hiện dữ liệu đang nhập mà chưa lưu hay công khai; Quay lại sửa giữ dữ liệu; Lưu từ xem trước thì lưu như bước 5 | Alt-07 |
| UC-07-AC-14 | Artisan nhập 4 số điện thoại, hoặc hai số trùng nhau | Chọn Lưu | Hiển thị ERR-UC07-01 tại trường sai; không lưu. Nhập 1–3 số hợp lệ thì lưu được, Guest xem được các số này ở trang gian hàng; số trong hồ sơ bán hàng không hiện | Alt-01, BR-10 |

### UC-08 — Khám phá xưởng

#### Detail

| Field | Content |
|---|---|
| Use case ID | UC-08 |
| Description | Guest hoặc Buyer tìm xưởng theo tên, lọc theo phân loại và trạng thái đón khách, rồi xem gian hàng của xưởng (hai màn hình: danh sách xưởng và trang gian hàng). |
| Actors | Guest, Buyer (Buyer thao tác như Guest) |
| Channel | Khu mua hàng |
| Preconditions | • Không yêu cầu đăng nhập |
| Expected | **Thành công:**<br>• Người dùng xem được trang gian hàng và đi tiếp tới sản phẩm, bản đồ hoặc yêu cầu báo giá<br>**Thất bại:**<br>• Hiển thị lỗi, giữ nguyên bộ lọc đang chọn |
| Screens | • Danh sách xưởng (bước 1-3)<br>• Trang gian hàng (bước 4-5) |
| Notifications | Không có |

#### Main Flow

Trigger: Guest chọn mục Xưởng.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Guest | Guest mở mục Xưởng. | Danh sách xưởng đã mở: ảnh đại diện, tên, nhãn "Đang đón khách" nếu có. |
| 2 | Guest | Guest nhập tên xưởng vào ô tìm. | Danh sách lọc theo tên, không tải lại trang. |
| 3 | Guest | Guest chọn phân loại và hoặc bật lọc "Đang đón khách". | Danh sách cập nhật theo bộ lọc. |
| 4 | Guest | Guest chọn một xưởng. | Trang gian hàng: ảnh đại diện, tên, câu chuyện, ảnh xưởng, chứng nhận kèm nhãn, địa chỉ, số điện thoại liên hệ, phân loại sản phẩm của xưởng, trạng thái đón khách, sản phẩm đang Hiển thị. |
| 5 | Guest | Guest chọn Xem trên bản đồ, một sản phẩm hoặc Gửi yêu cầu báo giá cho xưởng này (yêu cầu chung của xưởng, không gắn sản phẩm). | Chuyển tới UC-26, UC-11 hoặc UC-18; UC-18 nhận kèm xưởng đang xem, không kèm sản phẩm. |

Outcome: Guest nắm được thông tin xưởng và đi tiếp tới sản phẩm hoặc bản đồ.

#### Alternative Flows

##### Alt-01 — Không có kết quả

- At step: 2 · Trigger: Tên hoặc bộ lọc không khớp xưởng nào.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Frontend | Frontend hiện "Không tìm thấy xưởng phù hợp" và nút Xóa bộ lọc. | Danh sách trống, người dùng biết cách bỏ lọc. |

Resume: Chọn Xóa bộ lọc thì quay lại bước 1.

##### Alt-02 — ERR-UC08-01 — Gian hàng không khả dụng

- At step: 4 · Trigger: Guest mở link trực tiếp của gian hàng chưa mở hoặc không tồn tại.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Backend | Backend từ chối hiển thị gian hàng. | Hiển thị ERR-UC08-01 và nút Về danh sách xưởng. |

Resume: Kết thúc, hoặc quay lại bước 1.

##### Alt-03 — ERR-UC08-02 — Không tải được danh sách

- At step: 1 · Trigger: Tải hoặc lọc danh sách xưởng bị lỗi.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Frontend | Frontend giữ danh sách và bộ lọc hiện tại, hiện lỗi và nút Thử lại. | Hiển thị ERR-UC08-02. |

Resume: Chọn Thử lại thì lặp bước đang làm.

##### Alt-04 — Xưởng đang tạm ngừng

- At step: 4 · Trigger: Gian hàng ở trạng thái Tạm ngừng nhận giao dịch mới.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Frontend | Frontend hiện dòng "Xưởng đang tạm ngừng nhận giao dịch mới" và vô hiệu nút Gửi yêu cầu báo giá. | Người dùng vẫn xem được toàn bộ nội dung; sản phẩm không thêm được vào giỏ (UC-11). |

Resume: Tiếp tục bước 5.

##### Alt-05 — Chủ xưởng xem gian hàng của mình

- At step: 4 · Trigger: Artisan đã đăng nhập mở gian hàng của chính mình, kể cả khi Đang chuẩn bị.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Frontend | Frontend hiển thị đầy đủ và ẩn nút Gửi yêu cầu báo giá; nếu gian hàng Đang chuẩn bị thì hiện dòng "Chỉ bạn xem được, gian hàng chưa mở". | Artisan xem trước gian hàng như người mua. |

Resume: Tiếp tục bước 5.

##### Alt-06 — Guest gửi yêu cầu báo giá

- At step: 5 · Trigger: Guest chưa đăng nhập chọn Gửi yêu cầu báo giá.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Frontend | Frontend chuyển sang đăng nhập (UC-04) và nhớ xưởng đang xem. | Sau đăng nhập quay lại đúng gian hàng. |

Resume: Tiếp tục bước 5.

#### Data Fields

| Field | Format | Validation | Required | Ghi chú |
|---|---|---|---|---|
| Từ khóa tên xưởng | Text; length 100 | Không phân biệt hoa thường và dấu (BR-8) | Không | Ví dụ: "binh an" tìm ra "Xưởng mây tre Bình An" |
| Phân loại | Chọn từ danh sách | Chỉ phân loại đang Hiển thị | Không | Từ UC-12. Chỉ dùng làm bộ lọc, thẻ xưởng không hiện phân loại (BR-10) |
| Đang đón khách | Bật / tắt | — | Không | Trạng thái do Artisan đặt ở UC-25 |

#### Error Messages

| Code | Condition | Message | Display | Flow |
|---|---|---|---|---|
| ERR-UC08-01 | Gian hàng chưa mở hoặc không tồn tại | "Gian hàng này chưa mở hoặc không tồn tại." | Trang lỗi | Alt-02 |
| ERR-UC08-02 | Không tải được danh sách | "Không tải được danh sách xưởng. Vui lòng thử lại." | Inline | Alt-03 |

#### Business Rules

| ID | Rule | Source |
|---|---|---|
| BR-1 | Gian hàng đã mở (Đang hoạt động, Tạm ngừng) hiển thị công khai; gian hàng Đang chuẩn bị chỉ chủ xưởng xem trước. | Overview 3.2 |
| BR-2 | Gian hàng Tạm ngừng vẫn hiển thị trên danh sách. | Overview 3.2 |
| BR-3 | Xưởng thuộc một phân loại khi có ít nhất một sản phẩm Hiển thị thuộc phân loại đó. | Nhóm đề xuất |
| BR-4 | Trạng thái Đang đón khách độc lập với trạng thái tạm ngừng. | Overview 3.2 |
| BR-5 | Chứng nhận có nhãn "Do xưởng cung cấp" hoặc "BQL đã xác minh". | Overview 3.2 |
| BR-6 | URL gian hàng dễ đọc, theo tên xưởng. | Đề bài §1 (tiêu chí 8) |
| BR-7 | Chủ xưởng không có nút Gửi yêu cầu báo giá cho chính xưởng mình. | Overview 3.4 |
| BR-8 | Tìm theo tên không phân biệt hoa thường và dấu tiếng Việt. | Nhóm đề xuất |
| BR-9 | Lọc và tìm cập nhật danh sách bằng API, không tải lại trang. | Đề bài §1 (tiêu chí 4) |
| BR-10 | Thẻ xưởng ở danh sách không hiện phân loại vì một xưởng có thể thuộc nhiều phân loại; phân loại chỉ là bộ lọc và hiện đầy đủ ở trang gian hàng. | Chốt 2026-11-18 |
| BR-11 | Nút Gửi yêu cầu báo giá ở trang gian hàng tạo yêu cầu gửi riêng cho xưởng này, không gắn sản phẩm (UC-18). | Chốt 2026-11-18 |

#### Acceptance Criteria

| ID | Given | When | Then | Covers |
|---|---|---|---|---|
| UC-08-AC-01 | Có nhiều gian hàng đã mở | Guest mở mục Xưởng, tìm tên, lọc phân loại, mở một xưởng | Danh sách lọc đúng; trang gian hàng hiện đủ thông tin và sản phẩm Hiển thị | Main Flow |
| UC-08-AC-02 | Không xưởng nào khớp | Guest nhập tên không tồn tại | Hiện "Không tìm thấy xưởng phù hợp" và nút Xóa bộ lọc | Alt-01 |
| UC-08-AC-03 | Gian hàng Đang chuẩn bị | Guest mở link trực tiếp | Hiển thị ERR-UC08-01 | Alt-02, BR-1 |
| UC-08-AC-04 | API danh sách lỗi | Guest lọc phân loại | Hiển thị ERR-UC08-02; bộ lọc được giữ; có nút Thử lại | Alt-03 |
| UC-08-AC-05 | Gian hàng Tạm ngừng | Guest mở trang gian hàng | Có dòng tạm ngừng; nút Gửi yêu cầu báo giá bị vô hiệu; xưởng vẫn nằm trong danh sách | Alt-04, BR-2 |
| UC-08-AC-06 | Artisan mở gian hàng của mình đang Đang chuẩn bị | Xem trang gian hàng | Thấy đầy đủ, có dòng "Chỉ bạn xem được"; không có nút Gửi yêu cầu báo giá | Alt-05, BR-7 |
| UC-08-AC-07 | Xưởng A có sản phẩm Hiển thị thuộc phân loại X | Guest lọc phân loại X | Xưởng A có trong kết quả; xưởng chỉ có sản phẩm Ẩn thì không | BR-3 |
| UC-08-AC-08 | Xưởng đang bật Đang đón khách và đang Tạm ngừng | Guest lọc Đang đón khách | Xưởng vẫn có trong kết quả | BR-4 |
| UC-08-AC-09 | Gian hàng có chứng nhận | Guest xem gian hàng | Mỗi chứng nhận có nhãn nguồn | BR-5 |
| UC-08-AC-10 | Gian hàng "Xưởng mây tre Bình An" | Guest mở trang gian hàng | URL dạng chữ dễ đọc, không dùng số id | BR-6 |
| UC-08-AC-11 | Tên xưởng "Bình An" | Guest nhập "binh an" | Xưởng có trong kết quả | BR-8 |
| UC-08-AC-12 | Guest đang lọc | Đổi bộ lọc | Danh sách cập nhật mà trang không tải lại | BR-9 |
| UC-08-AC-13 | Guest chưa đăng nhập đang xem gian hàng | Chọn Gửi yêu cầu báo giá | Chuyển sang đăng nhập; sau đó quay lại đúng gian hàng | Alt-06 |
| UC-08-AC-14 | Xưởng có sản phẩm thuộc ba phân loại | Guest xem danh sách xưởng và trang gian hàng | Thẻ xưởng không hiện phân loại; trang gian hàng hiện đủ ba phân loại | BR-10 |
| UC-08-AC-15 | Guest đã đăng nhập xem gian hàng đang hoạt động | Chọn Gửi yêu cầu báo giá | Chuyển sang UC-18 kèm xưởng đang xem, không kèm sản phẩm | Main Flow, BR-11 |

### UC-09 — Quản lý sản phẩm

#### Detail

| Field | Content |
|---|---|
| Use case ID | UC-09 |
| Description | Artisan tạo sản phẩm, lưu nháp và cho hiển thị; sửa, đổi loại, ẩn, hiển thị lại và xóa sản phẩm nháp. |
| Actors | Artisan |
| Channel | Kênh người bán |
| Preconditions | • Đã đăng nhập và có quyền bán |
| Expected | **Thành công:**<br>• Sản phẩm ở trạng thái Hiển thị; công khai ngay nếu gian hàng đã mở<br>**Thất bại:**<br>• Hiển thị lỗi, sản phẩm giữ trạng thái hiện tại |
| Screens | • Danh sách sản phẩm (bước 1)<br>• Form sản phẩm (bước 2-6) |
| Notifications | Không có |

#### Main Flow

Trigger: Artisan chọn Thêm sản phẩm.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Artisan | Artisan mở mục Sản phẩm và chọn Thêm sản phẩm. | Form sản phẩm hiển thị. |
| 2 | Artisan | Artisan chọn loại sản phẩm: Có sẵn hoặc Chỉ nhận đặt riêng. | Hiện các trường tương ứng; loại Có sẵn có thêm giá và số lượng. |
| 3 | Artisan | Artisan nhập tên, mô tả, tải nhiều ảnh và chọn phân loại (phân loại chỉ bắt buộc khi cho hiển thị). | Ảnh hiển thị trong form. |
| 4 | Artisan | Artisan nhập giá, số lượng còn bán, cân nặng và kích thước đóng gói (nếu loại Có sẵn). | Thông tin sẵn sàng để lưu. |
| 5 | Artisan | Artisan chọn Lưu nháp. | Backend kiểm tra dữ liệu tối thiểu và lưu sản phẩm ở trạng thái Nháp. |
| 6 | Artisan | Artisan chọn Cho hiển thị. | Backend kiểm tra đủ thông tin và chuyển sản phẩm sang Hiển thị. |
| 7 | Frontend | Frontend cập nhật danh sách sản phẩm. | Sản phẩm hiện trạng thái Hiển thị; công khai ngay nếu gian hàng đã mở. |

Outcome: Sản phẩm ở trạng thái Hiển thị.

#### Alternative Flows

##### Alt-01 — ERR-UC09-01 — Dữ liệu không hợp lệ

- At step: 5 · Trigger: Thiếu tên, giá không phải số dương, số lượng âm hoặc không nguyên.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Backend | Backend từ chối lưu và đánh dấu trường sai. | Hiển thị ERR-UC09-01 tại trường sai. |

Resume: Quay lại bước 3, giữ nguyên dữ liệu đã nhập.

##### Alt-02 — ERR-UC09-02 — Ảnh không hợp lệ

- At step: 3 · Trigger: Ảnh sai định dạng, lớn hơn 10 MB hoặc vượt 10 ảnh.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Frontend | Frontend không nhận ảnh. | Hiển thị ERR-UC09-02. |

Resume: Chọn ảnh khác, lặp lại bước 3.

##### Alt-03 — ERR-UC09-03 — Chưa đủ thông tin để hiển thị

- At step: 6 · Trigger: Sản phẩm thiếu ảnh, mô tả, phân loại, hoặc (loại Có sẵn) thiếu giá hay số lượng.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Backend | Backend từ chối chuyển sang Hiển thị và liệt kê mục thiếu. | Hiển thị ERR-UC09-03; sản phẩm giữ trạng thái Nháp. |

Resume: Quay lại bước 3 hoặc 4 để bổ sung.

##### Alt-04 — Sửa sản phẩm

- At step: 1 · Trigger: Artisan chọn một sản phẩm có sẵn trong danh sách thay vì Thêm sản phẩm.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Artisan | Artisan sửa thông tin và chọn Lưu. | Backend kiểm tra theo ERR-UC09-01, ERR-UC09-02; sản phẩm Hiển thị mà thiếu thông tin thì báo ERR-UC09-03. |
| 2 | Backend | Backend lưu thay đổi. | Thay đổi có hiệu lực ngay; đơn và giỏ đã tạo không đổi giá chụp lúc tạo. |

Resume: Kết thúc.

##### Alt-05 — Ẩn sản phẩm

- At step: 1 · Trigger: Artisan chọn Ẩn trên một sản phẩm đang Hiển thị.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Backend | Backend chuyển sản phẩm sang Ẩn. | Sản phẩm biến khỏi tìm kiếm, bản đồ, link trực tiếp; trong giỏ chuyển xuống nhóm "Không khả dụng"; trang QR của lô vẫn xem được. |

Resume: Kết thúc.

##### Alt-06 — Hiển thị lại sản phẩm

- At step: 1 · Trigger: Artisan chọn Hiển thị lại trên một sản phẩm đang Ẩn.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Backend | Backend kiểm tra đủ thông tin và chuyển sản phẩm sang Hiển thị. | Sản phẩm công khai lại nếu gian hàng đã mở. Thiếu thông tin thì báo ERR-UC09-03. |

Resume: Kết thúc.

##### Alt-07 — Xóa sản phẩm Nháp

- At step: 1 · Trigger: Artisan chọn Xóa trên một sản phẩm đang Nháp.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Frontend | Frontend hiện hộp xác nhận xóa. | Artisan xác nhận hoặc hủy. |
| 2 | Backend | Backend xóa sản phẩm nếu chưa có lô nào gắn với sản phẩm. | Sản phẩm biến khỏi danh sách. Còn lô gắn thì báo ERR-UC09-04. |

Resume: Kết thúc.

##### Alt-08 — ERR-UC09-04 — Không xóa được sản phẩm

- At step: 1 · Trigger: Artisan xóa sản phẩm không ở trạng thái Nháp hoặc đã có lô.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Backend | Backend từ chối xóa. | Hiển thị ERR-UC09-04; gợi ý dùng Ẩn. |

Resume: Kết thúc.

##### Alt-09 — ERR-UC09-05 — Không có quyền

- At step: 1 · Trigger: Tài khoản chưa có quyền bán hoặc thao tác trên sản phẩm của xưởng khác.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Backend | Backend từ chối thao tác. | Hiển thị ERR-UC09-05; dữ liệu không đổi. |

Resume: Kết thúc.

##### Alt-10 — ERR-UC09-06 — Chưa có phân loại để chọn

- At step: 3 · Trigger: Chưa có phân loại nào đang Hiển thị.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Frontend | Frontend hiện thông báo ở ô Phân loại và vẫn cho Lưu nháp. | Hiển thị ERR-UC09-06; chưa Cho hiển thị được cho tới khi có phân loại. |

Resume: Tiếp tục bước 4.

##### Alt-11 — Đổi loại sản phẩm

- At step: 1 · Trigger: Artisan chọn một sản phẩm trong danh sách và chọn Đổi loại.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Artisan | Artisan chọn loại mới. Sản phẩm Nháp đổi được hai chiều; sản phẩm đã từng Hiển thị chỉ đổi từ Chỉ nhận đặt riêng sang Có sẵn (BR-10). | Form hiện các trường của loại mới; đổi sang Chỉ nhận đặt riêng thì giá, số lượng đã nhập bị bỏ. |
| 2 | Artisan | Artisan nhập giá, số lượng (và cân nặng, kích thước nếu bật GHN) khi đổi sang Có sẵn, rồi chọn Lưu. | Backend kiểm tra theo ERR-UC09-01; sản phẩm đang Hiển thị mà thiếu thông tin thì báo ERR-UC09-03, gian hàng chưa có phương thức giao thì báo ERR-UC09-08. |
| 3 | Backend | Backend lưu loại mới. | Sản phẩm đổi loại; yêu cầu báo giá và đơn đã tạo giữ nguyên. |

Resume: Kết thúc.

##### Alt-12 — ERR-UC09-07 — Không đổi được loại

- At step: 1 · Trigger: Artisan đổi sản phẩm Có sẵn đã từng Hiển thị sang Chỉ nhận đặt riêng.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Backend | Backend từ chối đổi loại. | Hiển thị ERR-UC09-07; gợi ý Ẩn sản phẩm này và tạo sản phẩm mới. |

Resume: Kết thúc.

##### Alt-13 — ERR-UC09-08 — Chưa có phương thức giao

- At step: Alt-11 bước 2 · Trigger: Đổi sản phẩm đang Hiển thị sang Có sẵn khi gian hàng chưa bật phương thức giao nào.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Backend | Backend từ chối đổi loại. | Hiển thị ERR-UC09-08 kèm link tới Phương thức giao (UC-07); sản phẩm giữ loại cũ. |

Resume: Kết thúc.

#### Data Fields

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

#### Error Messages

| Code | Condition | Message | Display | Flow |
|---|---|---|---|---|
| ERR-UC09-01 | Dữ liệu không hợp lệ | "Thông tin sản phẩm chưa hợp lệ. Vui lòng kiểm tra các trường được đánh dấu." | Inline | Alt-01 |
| ERR-UC09-02 | Ảnh không hợp lệ | "Ảnh không hợp lệ. Chỉ chấp nhận JPG, PNG, WebP, tối đa 10 MB, tối đa 10 ảnh." | Inline | Alt-02 |
| ERR-UC09-03 | Chưa đủ thông tin để hiển thị | "Chưa thể hiển thị. Vui lòng bổ sung: {{danhSachMucThieu}}." | Inline | Alt-03 |
| ERR-UC09-04 | Không xóa được sản phẩm | "Chỉ xóa được sản phẩm nháp chưa có lô. Hãy dùng Ẩn để gỡ sản phẩm khỏi sàn." | Toast | Alt-08 |
| ERR-UC09-05 | Không có quyền | "Bạn không có quyền thực hiện thao tác này." | Trang lỗi | Alt-09 |
| ERR-UC09-06 | Chưa có phân loại nào đang Hiển thị | "Chưa có phân loại để chọn. Bạn vẫn lưu nháp được; vui lòng liên hệ BQL để thêm phân loại." | Inline | Alt-10 |
| ERR-UC09-07 | Đổi sản phẩm Có sẵn đã từng Hiển thị sang Chỉ nhận đặt riêng | "Sản phẩm có sẵn đã từng hiển thị không đổi sang chỉ nhận đặt riêng được. Hãy ẩn sản phẩm này và tạo sản phẩm mới." | Toast | Alt-12 |
| ERR-UC09-08 | Đổi sang Có sẵn khi gian hàng chưa có phương thức giao nào | "Gian hàng cần ít nhất một phương thức giao để bán sản phẩm có sẵn. Vui lòng bật ở mục Phương thức giao." | Inline | Alt-13 |

#### Business Rules

| ID | Rule | Source |
|---|---|---|
| BR-1 | Vòng đời sản phẩm: Nháp, Hiển thị, Ẩn. Ẩn và Hiển thị qua lại; không quay về Nháp. | Overview 3.2 |
| BR-2 | Sản phẩm công khai khi gian hàng đã mở và sản phẩm đang Hiển thị; áp dụng cho tìm kiếm, bản đồ, link trực tiếp. Trang nguồn gốc (QR) mở được khi gian hàng đã mở, bất kể sản phẩm Nháp, Hiển thị hay Ẩn; sản phẩm Ẩn thì link sản phẩm trên trang QR đổi thành "Sản phẩm hiện không bán" (UC-17). | Overview 3.2, 3.3 |
| BR-3 | Có hai loại sản phẩm: Có sẵn (giá, số lượng) và Chỉ nhận đặt riêng. Không có Admin duyệt từng sản phẩm. | Overview 3.2 |
| BR-4 | Sửa hoặc ẩn sản phẩm không làm đổi đơn đã tạo và yêu cầu báo giá đã gửi. | Overview 3.2 |
| BR-5 | Chỉ xóa được sản phẩm đang Nháp và chưa có lô; các trường hợp còn lại chỉ Ẩn. | Chốt 2026-11-18 |
| BR-6 | "Đủ thông tin" nghĩa là có tên, phân loại, mô tả, ít nhất một ảnh; loại Có sẵn thêm giá và số lượng; cân nặng và kích thước đóng gói khi bật GHN. | Nhóm đề xuất |
| BR-7 | Sản phẩm có số lượng 0 vẫn Hiển thị, ghi "Hết hàng" và không thêm được vào giỏ. | Nhóm đề xuất |
| BR-8 | Artisan chỉ quản lý sản phẩm của xưởng mình. | Overview mục 1 |
| BR-9 | Ảnh được backend thu nhỏ để danh sách không tải ảnh gốc. | Đề bài §4.4 |
| BR-10 | Đổi loại: sản phẩm Nháp đổi hai chiều tự do; sản phẩm đã từng Hiển thị (đang Hiển thị hoặc Ẩn) chỉ đổi được từ Chỉ nhận đặt riêng sang Có sẵn. Đổi sang Có sẵn cần đủ giá, số lượng (và cân nặng, kích thước nếu bật GHN) và gian hàng còn ít nhất một phương thức giao (UC-07 BR-6). | Chốt 2026-11-18 |
| BR-11 | Sản phẩm Chỉ nhận đặt riêng không có giá, số lượng, cân nặng, kích thước và không thêm được vào giỏ. Buyer chỉ gửi được yêu cầu báo giá gắn sản phẩm này, riêng xưởng của sản phẩm (UC-11, UC-18). | Chốt 2026-11-18 |

#### Acceptance Criteria

| ID | Given | When | Then | Covers |
|---|---|---|---|---|
| UC-09-AC-01 | Artisan đã đăng nhập; có phân loại Hiển thị | Tạo sản phẩm đủ thông tin, Lưu nháp rồi Cho hiển thị | Sản phẩm ở trạng thái Hiển thị và hiện trong danh sách | Main Flow |
| UC-09-AC-02 | Tên sản phẩm để trống | Chọn Lưu nháp | Hiển thị ERR-UC09-01; dữ liệu được giữ; không lưu | Alt-01 |
| UC-09-AC-03 | Ảnh PDF hoặc lớn hơn 10 MB | Tải ảnh | Hiển thị ERR-UC09-02; ảnh không được nhận | Alt-02 |
| UC-09-AC-04 | Sản phẩm Nháp thiếu ảnh | Chọn Cho hiển thị | Hiển thị ERR-UC09-03 liệt kê mục thiếu; vẫn Nháp | Alt-03, BR-6 |
| UC-09-AC-05 | Sản phẩm Hiển thị đã có đơn | Đổi giá rồi Lưu | Giá mới áp dụng cho sản phẩm; đơn cũ giữ giá cũ | Alt-04, BR-4 |
| UC-09-AC-06 | Sản phẩm Hiển thị, gian hàng đã mở, có lô | Chọn Ẩn | Không còn trong tìm kiếm, bản đồ, link trực tiếp; trong giỏ ở nhóm "Không khả dụng"; trang QR của lô vẫn xem được, link sản phẩm thay bằng "Sản phẩm hiện không bán" | Alt-05, BR-2 |
| UC-09-AC-07 | Sản phẩm Ẩn đủ thông tin | Chọn Hiển thị lại | Sản phẩm công khai lại | Alt-06, BR-1 |
| UC-09-AC-08 | Sản phẩm Nháp chưa có lô | Chọn Xóa và xác nhận | Sản phẩm bị xóa khỏi danh sách | Alt-07, BR-5 |
| UC-09-AC-09 | Sản phẩm đã từng Hiển thị, hoặc Nháp đã có lô | Chọn Xóa | Hiển thị ERR-UC09-04; sản phẩm giữ nguyên | Alt-08, BR-5 |
| UC-09-AC-10 | Artisan khác | Gửi thao tác sửa sản phẩm này | Hiển thị ERR-UC09-05; dữ liệu không đổi | Alt-09, BR-8 |
| UC-09-AC-11 | Gian hàng Đang chuẩn bị | Cho sản phẩm hiển thị | Sản phẩm ở Hiển thị nhưng chưa xuất hiện công khai | BR-2 |
| UC-09-AC-12 | Có hai loại sản phẩm | Tạo sản phẩm loại Chỉ nhận đặt riêng | Không bắt nhập giá, số lượng, cân nặng, kích thước; lưu và cho hiển thị được | BR-3 |
| UC-09-AC-13 | Sản phẩm có sẵn, số lượng 0 | Guest xem sản phẩm | Hiện "Hết hàng"; không thêm được vào giỏ | BR-7 |
| UC-09-AC-14 | Artisan tải ảnh hợp lệ | Lưu rồi xem danh sách sản phẩm | Danh sách dùng ảnh đã thu nhỏ | BR-9 |
| UC-09-AC-15 | Chưa có phân loại nào đang Hiển thị | Artisan tạo sản phẩm, Lưu nháp rồi Cho hiển thị | Lưu nháp được và hiện ERR-UC09-06 ở ô Phân loại; Cho hiển thị bị ERR-UC09-03 nêu thiếu phân loại | Alt-10 |
| UC-09-AC-16 | Sản phẩm Nháp chưa chọn phân loại | Lưu nháp, rồi chọn phân loại và Cho hiển thị | Lưu nháp thành công không cần phân loại; Cho hiển thị thành công sau khi có phân loại | Main Flow, BR-6 |
| UC-09-AC-17 | Sản phẩm Hiển thị loại Chỉ nhận đặt riêng, gian hàng có phương thức giao | Đổi sang Có sẵn, nhập giá và số lượng, Lưu | Sản phẩm thành Có sẵn, thêm vào giỏ được; yêu cầu báo giá đã gửi trước đó giữ nguyên | Alt-11, BR-10 |
| UC-09-AC-18 | Sản phẩm Có sẵn đã từng Hiển thị | Đổi sang Chỉ nhận đặt riêng | Hiển thị ERR-UC09-07; loại không đổi. Sản phẩm Nháp thì đổi được hai chiều | Alt-12, BR-10 |
| UC-09-AC-19 | Gian hàng chưa bật phương thức giao nào, sản phẩm đặt riêng đang Hiển thị | Đổi sang Có sẵn | Hiển thị ERR-UC09-08; sản phẩm giữ loại cũ | Alt-13, BR-10 |
| UC-09-AC-20 | Sản phẩm Chỉ nhận đặt riêng đang Hiển thị | Guest mở sản phẩm | Không có Thêm vào giỏ; gửi được yêu cầu báo giá kèm sản phẩm này, riêng xưởng của sản phẩm | BR-11 |

### UC-10 — Xem danh sách, tìm kiếm, lọc sản phẩm

#### Detail

| Field | Content |
|---|---|
| Use case ID | UC-10 |
| Description | Guest hoặc Buyer xem toàn bộ sản phẩm công khai ở trang Sản phẩm, rồi tìm bằng từ khóa và lọc theo phân loại, xưởng, loại sản phẩm. |
| Actors | Guest, Buyer (Buyer thao tác như Guest) |
| Channel | Khu mua hàng |
| Preconditions | • Không yêu cầu đăng nhập |
| Expected | **Thành công:**<br>• Trang Sản phẩm hiện danh sách khớp từ khóa và bộ lọc (hoặc toàn bộ khi chưa có); người dùng mở được chi tiết sản phẩm<br>**Thất bại:**<br>• Giữ kết quả cũ và báo lỗi tải |
| Screens | • Danh sách sản phẩm (bước 1-7) |
| Notifications | Không có |

#### Main Flow

Trigger: Guest chọn mục Sản phẩm, hoặc chọn một phân loại ở trang chủ.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Guest | Guest mở mục Sản phẩm (hoặc chọn một phân loại ở trang chủ). | Trang Sản phẩm hiện toàn bộ sản phẩm công khai: ảnh, tên, giá, xưởng, loại; mới nhất trước, mỗi lần 12 sản phẩm. Phân loại đã chọn ở trang chủ được áp sẵn làm bộ lọc. |
| 2 | Guest | Guest gõ từ khóa vào ô tìm. | Từ 2 ký tự trở lên, hiện gợi ý tên sản phẩm (tối đa 8). |
| 3 | Guest | Guest chọn một gợi ý hoặc bấm Tìm. | Danh sách thu hẹp theo từ khóa, không tải lại trang. |
| 4 | Guest | Guest lọc theo phân loại, xưởng, loại sản phẩm (Có sẵn, Chỉ nhận đặt riêng). | Danh sách cập nhật theo bộ lọc. |
| 5 | Guest | Guest chọn sắp xếp: mới nhất, giá tăng, giá giảm. | Danh sách sắp xếp lại. |
| 6 | Guest | Guest cuộn hoặc chọn Xem thêm. | Tải thêm 12 sản phẩm. |
| 7 | Guest | Guest chọn một sản phẩm. | Chuyển tới chi tiết sản phẩm (UC-11). |

Outcome: Guest tìm được sản phẩm cần xem.

#### Alternative Flows

##### Alt-01 — Không có kết quả

- At step: 3 hoặc 4 · Trigger: Từ khóa và bộ lọc không khớp sản phẩm nào.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Frontend | Frontend hiện "Không tìm thấy sản phẩm phù hợp" và nút Xóa bộ lọc. | Danh sách trống, có đường bỏ lọc. |

Resume: Chọn Xóa bộ lọc thì quay lại bước 1.

##### Alt-02 — ERR-UC10-01 — Không tải được kết quả

- At step: 1 đến 6 · Trigger: Tải danh sách, gợi ý hoặc trang tiếp theo bị lỗi.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Frontend | Frontend giữ kết quả và bộ lọc hiện tại, hiện lỗi và nút Thử lại. | Hiển thị ERR-UC10-01. |

Resume: Chọn Thử lại thì lặp bước đang làm.

##### Alt-03 — ERR-UC10-02 — Khoảng giá không hợp lệ (Should)

- At step: 4 · Trigger: Giá từ lớn hơn giá đến.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Frontend | Frontend không áp dụng bộ lọc giá. | Hiển thị ERR-UC10-02. |

Resume: Quay lại bước 4.

##### Alt-04 — Sắp xếp theo giá có sản phẩm chỉ nhận đặt riêng

- At step: 5 · Trigger: Kết quả có sản phẩm loại Chỉ nhận đặt riêng (không có giá).

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Frontend | Frontend xếp các sản phẩm không có giá xuống cuối và ghi "Theo báo giá". | Thứ tự giá đúng cho sản phẩm có giá. |

Resume: Tiếp tục bước 6.

##### Alt-05 — Tìm từ ô tìm trên đầu trang

- At step: 1 · Trigger: Guest nhập từ khóa vào ô tìm trên đầu trang khi đang ở màn hình khác.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Frontend | Frontend chuyển tới trang Sản phẩm với từ khóa đã áp. | Danh sách thu hẹp theo từ khóa. |

Resume: Tiếp tục bước 4.

##### Alt-06 — Chưa có sản phẩm công khai

- At step: 1 · Trigger: Chưa có sản phẩm công khai nào.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Frontend | Frontend hiện "Sắp có sản phẩm". | Trang trống, không có ô lọc kết quả. |

Resume: Kết thúc.

#### Data Fields

| Field | Format | Validation | Required | Ghi chú |
|---|---|---|---|---|
| Từ khóa | Text; length 100 | Không phân biệt hoa thường và dấu | Không | Khớp tên sản phẩm. Gợi ý từ 2 ký tự, tối đa 8 gợi ý |
| Phân loại | Chọn từ danh sách | Chỉ phân loại đang Hiển thị | Không | Có thể được áp sẵn từ trang chủ |
| Xưởng | Chọn từ danh sách | Chỉ gian hàng đã mở | Không | Lọc sản phẩm theo xưởng, không phải tìm xưởng (UC-08) |
| Loại sản phẩm | Chọn một | Có sẵn, Chỉ nhận đặt riêng | Không | |
| Khoảng giá | Hai số; VND | Từ không lớn hơn đến | Không | Mức Should |
| Sắp xếp | Chọn một | Mới nhất (mặc định), Giá tăng, Giá giảm | Không | |

#### Error Messages

| Code | Condition | Message | Display | Flow |
|---|---|---|---|---|
| ERR-UC10-01 | Không tải được kết quả | "Không tải được kết quả. Vui lòng thử lại." | Inline | Alt-02 |
| ERR-UC10-02 | Giá từ lớn hơn giá đến | "Giá từ phải nhỏ hơn hoặc bằng giá đến." | Inline | Alt-03 |

#### Business Rules

| ID | Rule | Source |
|---|---|---|
| BR-1 | Chỉ hiện sản phẩm công khai: sản phẩm Hiển thị của gian hàng đã mở; gian hàng Tạm ngừng vẫn hiện. | Overview 3.2 |
| BR-2 | Sắp xếp mặc định là mới nhất. | Overview 3.2 |
| BR-3 | Tìm không phân biệt hoa thường và dấu tiếng Việt. | Nhóm đề xuất |
| BR-4 | Gợi ý, lọc, sắp xếp, tải thêm cập nhật bằng API, không tải lại trang. | Đề bài §1 (tiêu chí 4, 6) |
| BR-5 | Mỗi lần tải 12 sản phẩm. | Nhóm đề xuất |
| BR-6 | Bộ lọc nằm trên URL dễ đọc để chia sẻ và quay lại. | Đề bài §1 (tiêu chí 8) |
| BR-7 | Gợi ý khi gõ chỉ gồm tên sản phẩm; tìm xưởng thuộc UC-08. Bộ lọc Xưởng là lọc sản phẩm theo xưởng. | Chốt 2026-11-18 |
| BR-8 | Trang Sản phẩm luôn hiện toàn bộ sản phẩm công khai khi chưa có từ khóa hay bộ lọc; ô tìm trên đầu trang dẫn về trang này. | Chốt 2026-11-18 |

#### Acceptance Criteria

| ID | Given | When | Then | Covers |
|---|---|---|---|---|
| UC-10-AC-01 | Có sản phẩm công khai | Mở mục Sản phẩm, gõ "giỏ", chọn gợi ý, lọc phân loại, sắp xếp giá tăng, mở một sản phẩm | Kết quả khớp, đúng thứ tự; mở được chi tiết sản phẩm | Main Flow |
| UC-10-AC-02 | Không sản phẩm nào khớp | Tìm từ khóa lạ | Hiện "Không tìm thấy sản phẩm phù hợp" và nút Xóa bộ lọc | Alt-01 |
| UC-10-AC-03 | API kết quả lỗi | Đổi bộ lọc | Hiển thị ERR-UC10-01; kết quả cũ được giữ; có nút Thử lại | Alt-02 |
| UC-10-AC-04 | Bật lọc khoảng giá | Nhập giá từ lớn hơn giá đến | Hiển thị ERR-UC10-02; bộ lọc giá không áp dụng | Alt-03 |
| UC-10-AC-05 | Kết quả có sản phẩm Chỉ nhận đặt riêng | Sắp xếp theo giá | Sản phẩm không giá ở cuối và ghi "Theo báo giá" | Alt-04 |
| UC-10-AC-06 | Có sản phẩm Ẩn, sản phẩm của gian hàng Đang chuẩn bị và của gian hàng Tạm ngừng | Mở trang Sản phẩm | Hai loại đầu không xuất hiện; sản phẩm của gian hàng Tạm ngừng vẫn xuất hiện | BR-1 |
| UC-10-AC-07 | Chưa chọn sắp xếp | Mở trang Sản phẩm | Sản phẩm mới nhất xếp trước | BR-2 |
| UC-10-AC-08 | Sản phẩm "Giỏ mây" | Gõ "gio may" | Sản phẩm có trong kết quả | BR-3 |
| UC-10-AC-09 | Người dùng đổi bộ lọc | Thao tác lọc | Danh sách cập nhật mà trang không tải lại | BR-4 |
| UC-10-AC-10 | Có hơn 12 sản phẩm | Cuộn hoặc chọn Xem thêm | Mỗi lần tải thêm 12 sản phẩm | BR-5 |
| UC-10-AC-11 | Đã lọc phân loại X | Sao chép URL và mở lại | Cùng bộ lọc được áp dụng; URL dễ đọc | BR-6 |
| UC-10-AC-12 | Guest đang ở trang chủ | Gõ "giỏ" vào ô tìm trên đầu trang và bấm Tìm | Chuyển tới trang Sản phẩm với từ khóa "giỏ" đã áp | Alt-05 |
| UC-10-AC-13 | Chưa có sản phẩm công khai nào | Mở mục Sản phẩm | Hiện "Sắp có sản phẩm"; không có ô lọc kết quả | Alt-06 |
| UC-10-AC-14 | Có sản phẩm "Giỏ mây" và xưởng "Bình An" | Gõ "binh an" vào ô tìm | Gợi ý chỉ gồm tên sản phẩm, không có gợi ý xưởng | BR-7 |
| UC-10-AC-15 | Có sản phẩm công khai | Mở mục Sản phẩm, chưa gõ từ khóa, chưa lọc | Hiện toàn bộ sản phẩm công khai, mới nhất trước | BR-8 |
| UC-10-AC-16 | Guest ở trang chủ, có phân loại X | Chọn phân loại X | Chuyển tới trang Sản phẩm đã lọc sẵn phân loại X | Main Flow, BR-8 |

### UC-11 — Xem chi tiết sản phẩm

#### Detail

| Field | Content |
|---|---|
| Use case ID | UC-11 |
| Description | Guest hoặc Buyer xem chi tiết sản phẩm, các lô sản xuất của sản phẩm, rồi thêm vào giỏ hoặc gửi yêu cầu báo giá gắn với sản phẩm đang xem. |
| Actors | Guest, Buyer (Buyer thao tác như Guest) |
| Channel | Khu mua hàng |
| Preconditions | • Không yêu cầu đăng nhập<br>• Sản phẩm công khai |
| Expected | **Thành công:**<br>• Trang chi tiết hiển thị đủ thông tin; người dùng đi được tới giỏ, yêu cầu báo giá, trang QR hoặc gian hàng<br>**Thất bại:**<br>• Báo sản phẩm không khả dụng |
| Screens | • Chi tiết sản phẩm (bước 1-5) |
| Notifications | Không có |

#### Main Flow

Trigger: Guest chọn một sản phẩm từ danh sách, gian hàng hoặc link trực tiếp.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Guest | Guest mở trang chi tiết sản phẩm. | Hiển thị thư viện ảnh, tên, giá hoặc "Theo báo giá", tình trạng, mô tả, phân loại, xưởng. |
| 2 | Guest | Guest xem ảnh lớn và chuyển giữa các ảnh. | Ảnh được phóng to. |
| 3 | Guest | Guest xem danh sách lô của sản phẩm. | Các lô liệt kê mới nhất trước: tên lô, ngày, trạng thái, link xem nguồn gốc (UC-17). |
| 4 | Guest | Guest chọn Thêm vào giỏ (sản phẩm có sẵn). | Sản phẩm vào giỏ theo UC-30; không giữ hàng. |
| 5 | Guest | Guest chọn Gửi yêu cầu báo giá. | Chuyển sang UC-18, gửi riêng xưởng của sản phẩm, kèm sản phẩm đang xem làm mẫu tham chiếu. |

Outcome: Guest nắm thông tin sản phẩm và đi tiếp tới mua, yêu cầu báo giá hoặc xem nguồn gốc.

#### Alternative Flows

##### Alt-01 — ERR-UC11-01 — Sản phẩm không khả dụng

- At step: 1 · Trigger: Link trực tiếp tới sản phẩm Ẩn, Nháp, của gian hàng chưa mở hoặc không tồn tại.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Backend | Backend từ chối hiển thị sản phẩm. | Hiển thị ERR-UC11-01 và nút Về trang chủ. |

Resume: Kết thúc.

##### Alt-02 — Sản phẩm của xưởng mình

- At step: 1 · Trigger: Artisan đã đăng nhập xem sản phẩm của chính xưởng mình.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Frontend | Frontend ẩn nút Thêm vào giỏ và Gửi yêu cầu báo giá. | Artisan chỉ xem. |

Resume: Tiếp tục bước 2.

##### Alt-03 — Hết hàng

- At step: 4 · Trigger: Sản phẩm có sẵn nhưng số lượng bằng 0.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Frontend | Frontend ghi "Hết hàng" và vô hiệu nút Thêm vào giỏ. | Guest không thêm được vào giỏ nhưng vẫn gửi được yêu cầu báo giá. |

Resume: Tiếp tục bước 5.

##### Alt-04 — Xưởng đang tạm ngừng

- At step: 4 · Trigger: Gian hàng ở trạng thái Tạm ngừng nhận giao dịch mới.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Frontend | Frontend hiện dòng "Xưởng đang tạm ngừng nhận giao dịch mới" và vô hiệu nút Thêm vào giỏ và Gửi yêu cầu báo giá. | Guest xem được thông tin nhưng không giao dịch được. |

Resume: Tiếp tục bước 3 để xem nguồn gốc.

##### Alt-05 — Chưa có lô

- At step: 3 · Trigger: Sản phẩm chưa có lô nào.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Frontend | Frontend ẩn mục nguồn gốc. | Trang không hiện danh sách lô trống. |

Resume: Tiếp tục bước 4.

##### Alt-06 — Guest gửi yêu cầu báo giá

- At step: 5 · Trigger: Guest chưa đăng nhập chọn Gửi yêu cầu báo giá.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Frontend | Frontend chuyển sang đăng nhập (UC-04) và nhớ sản phẩm đang xem. | Sau đăng nhập quay lại đúng sản phẩm. |

Resume: Tiếp tục bước 5.

#### Data Fields

| Field | Format | Validation | Required | Ghi chú |
|---|---|---|---|---|
| Ảnh sản phẩm | Thư viện ảnh | Ảnh đã thu nhỏ cho danh sách, ảnh lớn khi phóng to | Có | |
| Tên sản phẩm | Text | — | Có | |
| Giá | VND | Chỉ sản phẩm có sẵn; sản phẩm đặt riêng hiện "Theo báo giá" | Có điều kiện | Ví dụ: "250.000 ₫" |
| Tình trạng | Text | Còn hàng, Hết hàng | Có điều kiện | Chỉ sản phẩm có sẵn |
| Mô tả, phân loại, xưởng | Text, link | Link xưởng về UC-08 | Có | |
| Danh sách lô | Danh sách | Mới nhất trước | Không | Mỗi dòng: tên lô, ngày, trạng thái, link UC-17 |

#### Error Messages

| Code | Condition | Message | Display | Flow |
|---|---|---|---|---|
| ERR-UC11-01 | Sản phẩm không khả dụng | "Sản phẩm này không còn khả dụng." | Trang lỗi | Alt-01 |

#### Business Rules

| ID | Rule | Source |
|---|---|---|
| BR-1 | Chỉ sản phẩm công khai mới xem được qua link trực tiếp. | Overview 3.2 |
| BR-2 | Sản phẩm của xưởng mình không có nút mua và nút gửi yêu cầu. | Overview 3.2 |
| BR-3 | Danh sách lô sắp xếp mới nhất trước. | Overview 3.2 |
| BR-4 | Sản phẩm Chỉ nhận đặt riêng chỉ có nút Gửi yêu cầu báo giá. | Nhóm đề xuất |
| BR-5 | Thêm vào giỏ không giữ hàng; vượt số còn bán xử lý theo UC-30. | Overview 3.8 |
| BR-6 | URL sản phẩm dễ đọc. | Đề bài §1 (tiêu chí 8) |
| BR-7 | Sản phẩm Có sẵn có cả Thêm vào giỏ và Gửi yêu cầu báo giá (kể cả khi hết hàng). Yêu cầu gửi từ trang sản phẩm gắn sản phẩm này làm mẫu tham chiếu và gửi riêng xưởng của sản phẩm (UC-18). | Chốt 2026-11-18 |

#### Acceptance Criteria

| ID | Given | When | Then | Covers |
|---|---|---|---|---|
| UC-11-AC-01 | Sản phẩm có sẵn công khai, có lô | Guest mở trang, xem ảnh, xem lô, thêm giỏ | Thông tin đầy đủ; lô mới nhất trước; sản phẩm vào giỏ | Main Flow |
| UC-11-AC-02 | Sản phẩm Ẩn | Guest mở link trực tiếp | Hiển thị ERR-UC11-01 | Alt-01, BR-1 |
| UC-11-AC-03 | Artisan xem sản phẩm của xưởng mình | Mở trang | Không có nút Thêm vào giỏ và Gửi yêu cầu báo giá | Alt-02, BR-2 |
| UC-11-AC-04 | Số lượng bằng 0 | Mở trang | Ghi "Hết hàng"; nút Thêm vào giỏ vô hiệu; nút Gửi yêu cầu báo giá vẫn dùng được | Alt-03 |
| UC-11-AC-05 | Gian hàng Tạm ngừng | Mở trang | Có dòng tạm ngừng; hai nút giao dịch vô hiệu; vẫn xem được lô | Alt-04 |
| UC-11-AC-06 | Sản phẩm chưa có lô | Mở trang | Không hiện mục lô | Alt-05 |
| UC-11-AC-07 | Guest chưa đăng nhập | Chọn Gửi yêu cầu báo giá | Chuyển sang đăng nhập; sau đó quay lại đúng sản phẩm | Alt-06 |
| UC-11-AC-08 | Sản phẩm có ba lô | Mở trang | Lô mới nhất ở đầu danh sách | BR-3 |
| UC-11-AC-09 | Sản phẩm Chỉ nhận đặt riêng | Mở trang | Hiện "Theo báo giá"; chỉ có nút Gửi yêu cầu báo giá | BR-4 |
| UC-11-AC-10 | Thêm vào giỏ | Kiểm tra số lượng còn bán của sản phẩm | Số lượng còn bán không bị trừ | BR-5 |
| UC-11-AC-11 | Sản phẩm "Giỏ mây đan hoa cúc" | Mở trang | URL dạng chữ dễ đọc | BR-6 |
| UC-11-AC-12 | Sản phẩm Có sẵn còn hàng | Mở trang | Có cả nút Thêm vào giỏ và Gửi yêu cầu báo giá | BR-7 |
| UC-11-AC-13 | Guest đã đăng nhập xem sản phẩm "Giỏ mây đan hoa cúc" | Chọn Gửi yêu cầu báo giá | Chuyển sang UC-18 kèm sản phẩm này làm mẫu tham chiếu; yêu cầu chỉ gửi cho xưởng của sản phẩm | Main Flow, BR-7 |

### UC-12 — Quản lý phân loại

#### Detail

| Field | Content |
|---|---|
| Use case ID | UC-12 |
| Description | Admin thêm, sửa tên, ẩn và hiện lại phân loại sản phẩm. |
| Actors | Admin |
| Channel | Trang quản trị |
| Preconditions | • Admin đã đăng nhập |
| Expected | **Thành công:**<br>• Danh sách phân loại được cập nhật và áp dụng cho UC-08, UC-09, UC-10<br>**Thất bại:**<br>• Hiển thị lỗi, danh sách giữ nguyên |
| Screens | • Danh sách phân loại (bước 1)<br>• Form phân loại (bước 2-4) |
| Notifications | Không có |

#### Main Flow

Trigger: Admin chọn Thêm phân loại.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Admin | Admin mở mục Phân loại. | Danh sách: tên, số sản phẩm đang Hiển thị, trạng thái. |
| 2 | Admin | Admin chọn Thêm phân loại. | Form hiển thị. |
| 3 | Admin | Admin nhập tên phân loại và chọn Lưu. | Dữ liệu được gửi lên kiểm tra. |
| 4 | Backend | Backend kiểm tra tên không trùng và lưu phân loại ở trạng thái Hiển thị. | Phân loại mới có trong danh sách và dùng được ở UC-08, UC-09, UC-10. |

Outcome: Phân loại mới ở trạng thái Hiển thị.

#### Alternative Flows

##### Alt-01 — ERR-UC12-01 — Tên không hợp lệ

- At step: 4 · Trigger: Tên để trống, quá 100 ký tự hoặc trùng phân loại khác.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Backend | Backend từ chối lưu. | Hiển thị ERR-UC12-01. |

Resume: Quay lại bước 3, giữ nguyên dữ liệu đã nhập.

##### Alt-02 — Sửa tên phân loại

- At step: 1 · Trigger: Admin chọn Sửa trên một phân loại.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Admin | Admin sửa tên và chọn Lưu. | Backend kiểm tra theo ERR-UC12-01 và lưu; tên mới hiện ở mọi nơi dùng phân loại. |

Resume: Kết thúc.

##### Alt-03 — Ẩn phân loại

- At step: 1 · Trigger: Admin chọn Ẩn trên một phân loại đang Hiển thị.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Backend | Backend chuyển phân loại sang Ẩn nếu không còn sản phẩm Hiển thị thuộc phân loại đó. | Phân loại không còn trong bộ lọc và không chọn được ở UC-09. Còn sản phẩm Hiển thị thì báo ERR-UC12-02. |

Resume: Kết thúc.

##### Alt-04 — ERR-UC12-02 — Không ẩn được vì còn sản phẩm

- At step: 1 · Trigger: Admin ẩn phân loại còn ít nhất một sản phẩm đang Hiển thị.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Backend | Backend từ chối ẩn. | Hiển thị ERR-UC12-02; phân loại giữ nguyên. |

Resume: Kết thúc.

##### Alt-05 — Hiện lại phân loại

- At step: 1 · Trigger: Admin chọn Hiện lại trên một phân loại đang Ẩn.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Backend | Backend chuyển phân loại sang Hiển thị. | Phân loại dùng lại được ở UC-08, UC-09, UC-10. |

Resume: Kết thúc.

##### Alt-06 — ERR-UC12-03 — Không có quyền

- At step: 1 · Trigger: Người không phải Admin truy cập hoặc gửi thao tác.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Backend | Backend từ chối thao tác. | Hiển thị ERR-UC12-03; dữ liệu không đổi. |

Resume: Kết thúc.

#### Data Fields

| Field | Format | Validation | Required | Ghi chú |
|---|---|---|---|---|
| Tên phân loại | Text; length 100 | Không để trống; duy nhất, không phân biệt hoa thường | Có | Ví dụ: "Giỏ và làn" |
| Trạng thái | Chỉ xem | Hiển thị, Ẩn | — | Đổi bằng Ẩn và Hiện lại |
| Số sản phẩm đang Hiển thị | Number | Chỉ xem | — | Quyết định có ẩn được hay không |

#### Error Messages

| Code | Condition | Message | Display | Flow |
|---|---|---|---|---|
| ERR-UC12-01 | Tên trống, quá dài hoặc trùng | "Tên phân loại không hợp lệ hoặc đã tồn tại." | Inline | Alt-01 |
| ERR-UC12-02 | Còn sản phẩm đang Hiển thị | "Không thể ẩn vì phân loại còn sản phẩm đang hiển thị." | Toast | Alt-04 |
| ERR-UC12-03 | Không có quyền | "Bạn không có quyền thực hiện thao tác này." | Trang lỗi | Alt-06 |

#### Business Rules

| ID | Rule | Source |
|---|---|---|
| BR-1 | Chỉ Admin quản lý phân loại. | Overview mục 1 |
| BR-2 | Phân loại không bị xóa, chỉ ẩn. | Nhóm đề xuất |
| BR-3 | Chỉ ẩn được phân loại không còn sản phẩm Hiển thị. | Overview 3.2 |
| BR-4 | Phân loại Ẩn không chọn được ở UC-09 và không hiện trong bộ lọc UC-08, UC-10. | Nhóm đề xuất |
| BR-5 | Mỗi sản phẩm thuộc đúng một phân loại. | Nhóm đề xuất |
| BR-6 | Nếu thiếu thời gian, danh sách phân loại được nạp sẵn cố định và UC-12 không triển khai. | Overview 3.2 |

#### Acceptance Criteria

| ID | Given | When | Then | Covers |
|---|---|---|---|---|
| UC-12-AC-01 | Admin đã đăng nhập | Thêm phân loại "Giỏ và làn" | Phân loại ở trạng thái Hiển thị; chọn được ở UC-09 và lọc được ở UC-08, UC-10 | Main Flow |
| UC-12-AC-02 | Đã có "Giỏ và làn" | Thêm "giỏ và làn" | Hiển thị ERR-UC12-01; không tạo phân loại | Alt-01 |
| UC-12-AC-03 | Phân loại có tên cũ | Sửa tên rồi Lưu | Tên mới hiện ở mọi nơi dùng phân loại | Alt-02 |
| UC-12-AC-04 | Phân loại không có sản phẩm Hiển thị | Chọn Ẩn | Phân loại Ẩn | Alt-03, BR-3 |
| UC-12-AC-05 | Phân loại còn sản phẩm Hiển thị | Chọn Ẩn | Hiển thị ERR-UC12-02; phân loại giữ nguyên | Alt-04, BR-3 |
| UC-12-AC-06 | Phân loại đang Ẩn | Chọn Hiện lại | Phân loại Hiển thị và dùng lại được | Alt-05 |
| UC-12-AC-07 | Tài khoản Artisan hoặc Buyer | Gọi thao tác quản lý phân loại | Hiển thị ERR-UC12-03; dữ liệu không đổi | Alt-06, BR-1 |
| UC-12-AC-08 | Phân loại đã tạo | Tìm thao tác xóa | Không có chức năng xóa, chỉ có Ẩn | BR-2 |
| UC-12-AC-09 | Phân loại Ẩn | Artisan tạo sản phẩm; Guest mở bộ lọc | Không chọn được ở UC-09; không hiện trong bộ lọc | BR-4 |
| UC-12-AC-10 | Artisan tạo sản phẩm | Chọn phân loại | Chỉ chọn được một phân loại | BR-5 |
| UC-12-AC-11 | Nhóm không triển khai UC-12 | Khởi tạo dữ liệu mẫu | Danh sách phân loại cố định có sẵn; không có màn hình quản lý phân loại; UC-08, UC-09, UC-10 vẫn dùng bình thường | BR-6 |

### UC-13 — Quản lý trạng thái hoạt động của gian hàng

#### Detail

| Field | Content |
|---|---|
| Use case ID | UC-13 |
| Description | Artisan mở gian hàng khi đủ checklist, tạm ngừng nhận giao dịch mới và mở lại. |
| Actors | Artisan |
| Channel | Kênh người bán |
| Preconditions | • Đã đăng nhập và có quyền bán<br>• Gian hàng đang Đang chuẩn bị (luồng chính mở lần đầu) |
| Expected | **Thành công:**<br>• Gian hàng chuyển sang Đang hoạt động và công khai<br>**Thất bại:**<br>• Hiển thị mục còn thiếu hoặc lỗi, trạng thái giữ nguyên |
| Screens | • Trạng thái và checklist gian hàng (bước 1-4) |
| Notifications | Không có |

#### Main Flow

Trigger: Artisan chọn mục Trạng thái gian hàng.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Artisan | Artisan mở mục Trạng thái gian hàng. | Hiển thị trạng thái hiện tại "Đang chuẩn bị" và checklist, mỗi mục có dấu đạt hoặc chưa đạt. |
| 2 | Artisan | Artisan chọn Mở gian hàng khi checklist đủ. | Hộp xác nhận: gian hàng sẽ công khai. |
| 3 | Artisan | Artisan xác nhận. | Yêu cầu mở được gửi đi. |
| 4 | Backend | Backend kiểm tra lại checklist và chuyển gian hàng sang Đang hoạt động. | Gian hàng công khai; Admin không duyệt lần hai; ghi thời điểm mở lần đầu. |
| 5 | Frontend | Frontend báo thành công. | Xưởng có trong danh sách xưởng và bản đồ; sản phẩm Hiển thị công khai. |

Outcome: Gian hàng Đang hoạt động.

#### Alternative Flows

##### Alt-01 — ERR-UC13-01 — Checklist chưa đủ

- At step: 2 · Trigger: Còn mục chưa đạt khi Artisan mở, hoặc Backend kiểm tra lại thấy thiếu ở bước 4.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Backend | Backend từ chối mở và liệt kê mục thiếu. | Hiển thị ERR-UC13-01 kèm link tới màn hình cần bổ sung. |

Resume: Quay lại bước 1.

##### Alt-02 — Tạm ngừng nhận giao dịch mới

- At step: 1 · Trigger: Gian hàng Đang hoạt động và Artisan chọn Tạm ngừng.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Artisan | Artisan xác nhận tạm ngừng. | Hộp xác nhận nêu rõ hệ quả. |
| 2 | Backend | Backend chuyển gian hàng sang Tạm ngừng nhận giao dịch mới. | Gian hàng vẫn hiển thị; không nhận đơn hoặc yêu cầu báo giá mới; giao dịch cũ xử lý tiếp. Với yêu cầu báo giá đã nhận trước đó, xưởng không gửi hay cập nhật báo giá được, chỉ từ chối được; báo giá đã gửi trước vẫn được Buyer chấp nhận (UC-19 BR-8). |

Resume: Kết thúc.

##### Alt-03 — Mở lại

- At step: 1 · Trigger: Gian hàng đang Tạm ngừng và Artisan chọn Mở lại.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Backend | Backend kiểm tra lại checklist và chuyển gian hàng sang Đang hoạt động. | Gian hàng nhận giao dịch mới trở lại. Thiếu mục thì báo ERR-UC13-01. |

Resume: Kết thúc.

##### Alt-04 — ERR-UC13-02 — Thao tác không hợp lệ với trạng thái hiện tại

- At step: 3 · Trigger: Gian hàng đã đổi trạng thái từ nơi khác (ví dụ tab cũ) nên thao tác không còn phù hợp.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Backend | Backend từ chối thao tác. | Hiển thị ERR-UC13-02 và tải lại trạng thái hiện tại. |

Resume: Quay lại bước 1.

##### Alt-05 — ERR-UC13-03 — Không có quyền

- At step: 1 · Trigger: Tài khoản chưa có quyền bán hoặc đổi trạng thái gian hàng của người khác.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Backend | Backend từ chối thao tác. | Hiển thị ERR-UC13-03; trạng thái không đổi. |

Resume: Kết thúc.

#### Data Fields

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

#### Error Messages

| Code | Condition | Message | Display | Flow |
|---|---|---|---|---|
| ERR-UC13-01 | Checklist chưa đủ | "Gian hàng chưa đủ điều kiện mở. Vui lòng bổ sung: {{danhSachMucThieu}}." | Inline | Alt-01 |
| ERR-UC13-02 | Thao tác không hợp lệ với trạng thái hiện tại | "Gian hàng đã đổi trạng thái. Vui lòng xem lại trạng thái hiện tại." | Toast | Alt-04 |
| ERR-UC13-03 | Không có quyền | "Bạn không có quyền thực hiện thao tác này." | Trang lỗi | Alt-05 |

#### Business Rules

| ID | Rule | Source |
|---|---|---|
| BR-1 | Vòng đời: Đang chuẩn bị sang Đang hoạt động; Đang hoạt động và Tạm ngừng chuyển qua lại; không quay về Đang chuẩn bị. | Overview 3.2 |
| BR-2 | Đang chuẩn bị: chỉ chủ xưởng xem trước, chưa lên tìm kiếm và bản đồ. | Overview 3.2 |
| BR-3 | Đủ checklist thì chủ xưởng tự mở; Admin không duyệt lần hai. | Overview 3.2 |
| BR-4 | Tạm ngừng: vẫn hiển thị (kể cả pin trên bản đồ), không nhận đơn hoặc yêu cầu mới, giao dịch cũ xử lý tiếp. Với yêu cầu báo giá đã nhận trước khi tạm ngừng, xưởng chỉ từ chối được, không gửi hay cập nhật báo giá; báo giá xưởng đã gửi trước khi tạm ngừng vẫn được Buyer chấp nhận. | Overview 3.2, 3.4; UC-19 BR-8 (BRD 04) |
| BR-5 | Trạng thái hoạt động độc lập với trạng thái Đang đón khách (UC-25). | Overview 3.2 |
| BR-6 | Mở lại từ Tạm ngừng cũng kiểm lại checklist. | Chốt 2026-11-18 |
| BR-7 | Chỉ chủ xưởng đổi được trạng thái gian hàng của mình. | Overview mục 1 |
| BR-8 | "Ít nhất một sản phẩm đủ thông tin" nghĩa là có sản phẩm đang Hiển thị (đủ thông tin theo UC-09 BR-6). Checklist chỉ kiểm lúc mở và mở lại; xưởng ẩn hết sản phẩm sau đó thì gian hàng không tự tạm ngừng. | Chốt 2026-11-18 |
| BR-9 | Hệ thống ghi thời điểm mở gian hàng lần đầu; mở lại sau Tạm ngừng không đổi mốc này. Mốc này quyết định thứ tự khối Xưởng mới mở ở trang chủ (UC-06). | Chốt 2026-11-18 |

#### Acceptance Criteria

| ID | Given | When | Then | Covers |
|---|---|---|---|---|
| UC-13-AC-01 | Gian hàng Đang chuẩn bị, checklist đủ | Chọn Mở gian hàng và xác nhận | Gian hàng Đang hoạt động; xuất hiện ở danh sách xưởng, bản đồ; sản phẩm Hiển thị công khai | Main Flow |
| UC-13-AC-02 | Checklist thiếu ảnh đại diện | Chọn Mở gian hàng | Hiển thị ERR-UC13-01 kèm mục thiếu; gian hàng vẫn Đang chuẩn bị | Alt-01 |
| UC-13-AC-03 | Gian hàng Đang hoạt động | Chọn Tạm ngừng và xác nhận | Gian hàng Tạm ngừng; vẫn hiển thị; nút mua và gửi yêu cầu mới bị vô hiệu | Alt-02, BR-4 |
| UC-13-AC-04 | Gian hàng Tạm ngừng, checklist đủ | Chọn Mở lại | Gian hàng Đang hoạt động, nhận giao dịch mới | Alt-03 |
| UC-13-AC-05 | Mở gian hàng ở tab khác đã xong | Mở tiếp ở tab cũ | Hiển thị ERR-UC13-02; trạng thái hiện tại được tải lại | Alt-04 |
| UC-13-AC-06 | Tài khoản khác | Gửi thao tác đổi trạng thái gian hàng này | Hiển thị ERR-UC13-03; trạng thái không đổi | Alt-05, BR-7 |
| UC-13-AC-07 | Gian hàng Đang hoạt động | Tìm cách đưa về Đang chuẩn bị | Không có thao tác nào làm được | BR-1 |
| UC-13-AC-08 | Gian hàng Đang chuẩn bị | Guest tìm xưởng và mở bản đồ | Xưởng không xuất hiện; chỉ chủ xưởng xem trước | BR-2 |
| UC-13-AC-09 | Checklist đủ | Artisan mở gian hàng | Không có bước Admin duyệt | BR-3 |
| UC-13-AC-10 | Xưởng đã báo giá trước khi tạm ngừng | Buyer chấp nhận báo giá đó | Chấp nhận được; đơn mới và yêu cầu mới thì không | BR-4 |
| UC-13-AC-11 | Xưởng Tạm ngừng | Bật Đang đón khách | Hai trạng thái đặt độc lập, không ảnh hưởng nhau | BR-5 |
| UC-13-AC-12 | Gian hàng Tạm ngừng, đã ẩn hết sản phẩm | Chọn Mở lại | Hiển thị ERR-UC13-01; không mở lại | BR-6 |
| UC-13-AC-13 | Gian hàng Đang hoạt động, xưởng ẩn hết sản phẩm | Xem trạng thái gian hàng | Gian hàng vẫn Đang hoạt động, không tự tạm ngừng; checklist chỉ kiểm khi mở hoặc mở lại | BR-8 |
| UC-13-AC-14 | Gian hàng mở lần đầu ngày D1, tạm ngừng rồi mở lại ngày D2 | Xem thứ tự khối Xưởng mới mở ở trang chủ | Thứ tự tính theo D1, không phải D2 | BR-9 |
| UC-13-AC-15 | Xưởng đã nhận yêu cầu báo giá chưa phản hồi, rồi chuyển Tạm ngừng | Xưởng chọn Gửi báo giá, rồi chọn Không nhận | Gửi báo giá bị từ chối (UC-19 ERR-UC19-03); Không nhận thực hiện được | Alt-02, BR-4 |

## IV. Open Questions

| ID | Question | Affects | Decision |
|---|---|---|---|
| G-01 | Nội dung giới thiệu làng nghề trên trang chủ do ai quản lý? | UC-06 BR-4 | Đã chốt 2026-11-18: nội dung cố định do nhóm nhập khi triển khai; MVP1 không có chức năng chỉnh sửa |
| G-02 | Liên hệ công khai của gian hàng gồm gì? Số điện thoại chủ hộ ở hồ sơ chỉ Admin xem. | UC-07 Data Fields, BR-10; UC-13 checklist | Đã chốt 2026-11-18: xưởng nhập riêng 1–3 số điện thoại, bỏ Zalo, tách khỏi số trong hồ sơ bán hàng |
| G-03 | Nhãn "BQL đã xác minh" do Admin gắn ở đâu và khi nào? | UC-07 BR-9; UC-02 | Đã chốt 2026-11-18: ở UC-02 (Alt-06); xưởng sửa hoặc thay ảnh chứng nhận thì nhãn về "Do xưởng cung cấp"; Admin không nhận thông báo khi có chứng nhận mới |
| G-04 | Mở lại từ Tạm ngừng có kiểm lại checklist không? | UC-13 Alt-03, BR-6 | Đã chốt 2026-11-18: có |
| G-05 | UC-02 ở `01-account` cần bổ sung duyệt yêu cầu đổi tên, địa chỉ và thông báo kết quả cho xưởng. | UC-07 Alt-03; UC-02 | Đã chốt 2026-11-18: BRD `01-account` đã cập nhật (UC-02 Alt-05; xác minh chứng nhận ở Alt-06) |
| G-06 | "Ít nhất một sản phẩm đủ thông tin" trong checklist là sản phẩm đang Hiển thị hay chỉ cần Nháp đã đủ thông tin? | UC-13 BR-8 | Đã chốt 2026-11-18: sản phẩm đang Hiển thị |
| G-07 | Phương thức giao cấu hình ở đâu? | UC-07 | Đã chốt 2026-11-18: trong UC-07 |
| G-08 | Có xóa sản phẩm không? | UC-09 | Đã chốt 2026-11-18: chỉ xóa sản phẩm Nháp chưa có lô, còn lại chỉ Ẩn |
| G-09 | Xem trước ở UC-07 diễn ra khi nào? | UC-07 Alt-07 | Đã chốt 2026-11-18: Artisan chọn Xem trước dữ liệu đang nhập rồi mới lưu, hoặc lưu luôn |
| G-10 | Thẻ xưởng có hiện phân loại không, khi một xưởng thuộc nhiều phân loại? | UC-08 BR-10 | Đã chốt 2026-11-18: không; phân loại chỉ là bộ lọc và hiện đầy đủ ở trang gian hàng |
| G-11 | Có đổi loại sản phẩm sau khi tạo không? | UC-09 Alt-11, Alt-12, BR-10 | Đã chốt 2026-11-18: sản phẩm Nháp đổi hai chiều; sản phẩm đã từng Hiển thị chỉ đổi từ Chỉ nhận đặt riêng sang Có sẵn |
| G-12 | Yêu cầu báo giá gửi từ trang gian hàng và trang sản phẩm khác nhau thế nào? | UC-08 BR-11; UC-11 BR-7; UC-06 | Đã chốt 2026-11-18: ba lối vào (sản phẩm gắn sản phẩm làm mẫu; gian hàng gửi riêng, không gắn sản phẩm; Đặt hàng theo yêu cầu gửi chung); sản phẩm Có sẵn hết hàng vẫn gửi được yêu cầu |
| G-13 | UC-10 bắt đầu từ đâu và gợi ý gồm gì? | UC-10 Main Flow, BR-7, BR-8 | Đã chốt 2026-11-18: bắt đầu từ trang Sản phẩm hiện toàn bộ sản phẩm công khai; gợi ý chỉ gồm tên sản phẩm |
