---
title: "01 — Tài khoản"
group: 01-account
type: brd
status: draft
version: 0.2.0
date: 2026-11-18
author: Khánh Chi
---

# 01 — Tài khoản

Change history: [CHANGELOG.md](CHANGELOG.md)

## I. Overview

Nhóm chức năng về tài khoản: đăng ký tài khoản mua hàng, đăng nhập, đăng ký bán hàng bằng tài khoản hiện có, kiểm duyệt hồ sơ bán hàng (kèm xác minh chứng nhận của xưởng) và quản lý thông tin tài khoản. Mỗi người dùng chỉ có một tài khoản và một phiên đăng nhập; quyền bán là quyền được thêm vào tài khoản mua hàng sau khi Admin (BQL làng nghề) duyệt hồ sơ, nhằm đảm bảo đúng người trong làng và tránh hàng nhái trà trộn (đề bài §3). Trong mọi trạng thái hồ sơ, tài khoản vẫn mua hàng bình thường; chỉ hộ đã được duyệt mới dùng được Kênh người bán và có gian hàng công khai.

Lộ trình của người muốn bán hàng: Guest xem trang giới thiệu bán hàng → đăng ký tài khoản mua hàng (UC-03) → gửi hồ sơ bán hàng (UC-01) → Admin duyệt (UC-02) → hộ chuẩn bị gian hàng (UC-07) và tự mở gian hàng (UC-13), cả hai thuộc nhóm `02-showcase`.

### Terms

| Term | Meaning |
|---|---|
| Hồ sơ bán hàng | Thông tin xưởng, liên hệ và minh chứng mà Buyer gửi để xin quyền bán. Mỗi tài khoản chỉ có một hồ sơ. |
| Minh chứng hộ làng nghề | Giấy tờ hoặc ảnh chứng minh là hộ làm nghề trong làng, ví dụ giấy xác nhận của thôn, chứng nhận nghệ nhân (danh mục chấp nhận: xem G-01). |
| Bản nháp hồ sơ | Hồ sơ Buyer đang soạn, chưa gửi. Chỉ Buyer đó thấy; chưa phải trạng thái duyệt. |
| Chờ duyệt | Hồ sơ đã gửi, đang chờ Admin xử lý. Không sửa, không nộp hồ sơ khác. |
| Cần chỉnh sửa | Admin từ chối và cho phép sửa, gửi lại trên cùng hồ sơ. Luôn có lý do. |
| Bị từ chối | Admin từ chối và không cho gửi lại. Luôn có lý do. |
| Đã duyệt | Hồ sơ được chấp nhận; tài khoản thành Artisan, gian hàng ở trạng thái Đang chuẩn bị. |
| Chứng nhận của xưởng | Ảnh chứng nhận mà xưởng đăng trên gian hàng (UC-07), khác với minh chứng trong hồ sơ bán hàng. Có nhãn "Do xưởng cung cấp" hoặc "BQL đã xác minh". |
| Kênh người bán | Khu vực quản lý gian hàng của Artisan, cùng phiên đăng nhập với Khu mua hàng. |
| Trang quản trị | Khu vực của Admin. Admin không mua, không bán. |

## II. Scope

| Business topic | Channel | Use case |
|---|---|---|
| Hộ đã có tài khoản gửi hồ sơ bán hàng kèm minh chứng | Khu mua hàng | UC-01 |
| Admin đối chiếu minh chứng, duyệt hoặc từ chối hồ sơ; duyệt yêu cầu đổi tên, địa chỉ xưởng; xác minh chứng nhận của xưởng | Trang quản trị | UC-02 |
| Khách tạo tài khoản mua hàng | Khu mua hàng | UC-03 |
| Đăng nhập, đăng xuất, phân quyền theo vai trò và trạng thái hồ sơ | Khu mua hàng, Kênh người bán, Trang quản trị | UC-04 |
| Xem và cập nhật tên, liên hệ, mật khẩu | Khu mua hàng, Kênh người bán, Trang quản trị | UC-05 |

**Ngoài phạm vi:** Xác thực email và khôi phục mật khẩu (EN-06); khóa tài khoản hoặc khóa xưởng (EN-01); rút hồ sơ đăng ký bán hàng (EN-18); Admin quản lý Admin khác (EN-19); gửi email kết quả duyệt (EN-05, MVP1 chỉ thông báo trong hệ thống); đổi email đăng nhập; xóa tài khoản; nộp lại hồ sơ đã Bị từ chối. Quản lý thông tin gian hàng thuộc UC-07.

**Liên quan**

| ID | Tài liệu | Quan hệ |
|---|---|---|
| SRS-01 | [SRS-01](../../srs/01-account/01-architecture.md) | Đặc tả kỹ thuật của nhóm này; chưa đồng bộ với BRD v0.3.0. |
| UC-07 | BRD `02-showcase` | Nhận thông tin từ hồ sơ để điền sẵn gian hàng; gửi yêu cầu đổi tên, địa chỉ để Admin duyệt (UC-02, Alt-05); chứng nhận xưởng đăng được Admin xác minh (UC-02, Alt-06). |
| UC-13 | BRD `02-showcase` | Hộ tự mở gian hàng sau khi hồ sơ được duyệt. |
| UC-29 | BRD `07-notification` | Hiển thị các thông báo NT-UC01-xx, NT-UC02-xx. |

## III. Use Cases

| Use case ID | Use case | Description |
|---|---|---|
| UC-01 | Đăng ký bán hàng bằng tài khoản hiện có | Buyer gửi hồ sơ bán hàng kèm minh chứng, chờ Admin duyệt |
| UC-02 | Kiểm duyệt hồ sơ bán hàng | Admin đối chiếu minh chứng, duyệt hoặc từ chối hồ sơ, duyệt yêu cầu đổi thông tin xưởng, xác minh chứng nhận của xưởng |
| UC-03 | Đăng ký tài khoản mua hàng | Guest tạo tài khoản Buyer bằng email và mật khẩu |
| UC-04 | Đăng nhập, đăng xuất | Người dùng vào hệ thống và được đưa đến đúng khu vực theo quyền |
| UC-05 | Quản lý thông tin tài khoản | Người dùng sửa tên, số điện thoại và đổi mật khẩu (Should) |

### UC-01 — Đăng ký bán hàng bằng tài khoản hiện có

#### Detail

| Field | Content |
|---|---|
| Use case ID | UC-01 |
| Description | Buyer nhập thông tin xưởng, liên hệ và tải minh chứng hộ làng nghề; hệ thống ghi nhận hồ sơ ở trạng thái Chờ duyệt và báo cho Admin. |
| Actors | Buyer. Guest cũng xem được trang giới thiệu bán hàng nhưng phải đăng ký hoặc đăng nhập trước khi bắt đầu (Alt-01). |
| Channel | Khu mua hàng |
| Preconditions | • Người dùng đã đăng nhập bằng tài khoản Buyer<br>• Tài khoản chưa có hồ sơ bán hàng nào đã gửi (hồ sơ Cần chỉnh sửa xử lý ở Alt-07) |
| Expected | **Thành công:**<br>• Hồ sơ ở trạng thái Chờ duyệt<br>• Admin nhận NT-UC01-01<br>• Buyer thấy trang Trạng thái hồ sơ và vẫn mua hàng bình thường<br>**Thất bại:**<br>• Hiển thị lỗi, hồ sơ không được gửi, dữ liệu đã nhập được giữ lại |
| Screens | • Trang giới thiệu bán hàng (bước 1)<br>• Form hồ sơ bán hàng: Thông tin xưởng (bước 3), Minh chứng và ảnh xưởng (bước 4), Xem lại và gửi (bước 5)<br>• Trang Trạng thái hồ sơ (bước 8) |
| Notifications | NT-UC01-01, NT-UC01-02 |

#### Main Flow

Trigger: Người dùng chọn "Bán hàng cùng vCraft".

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Buyer | Buyer chọn "Bán hàng cùng vCraft" ở đầu trang (hoặc ở chân trang, hoặc ở banner trang chủ). | Trang giới thiệu bán hàng hiển thị: lợi ích khi bán, các bước (gửi hồ sơ, BQL duyệt, chuẩn bị gian hàng, mở gian hàng), giấy tờ cần chuẩn bị, nút "Bắt đầu đăng ký bán hàng". |
| 2 | Buyer | Buyer chọn "Bắt đầu đăng ký bán hàng". | Form hồ sơ hiển thị; họ tên và email lấy từ tài khoản, chỉ đọc. |
| 3 | Buyer | Buyer nhập thông tin xưởng: tên xưởng, địa chỉ trong làng, năm bắt đầu làm nghề, số điện thoại liên hệ, giới thiệu ngắn. | Thông tin xưởng sẵn sàng để gửi. |
| 4 | Buyer | Buyer tải lên minh chứng hộ làng nghề và ảnh xưởng. | Ảnh hiển thị trong form. |
| 5 | Buyer | Buyer xem lại toàn bộ thông tin và chọn Gửi hồ sơ. | Hồ sơ được gửi. |
| 6 | Hệ thống | Hệ thống kiểm tra dữ liệu, kiểm tra tài khoản chưa có hồ sơ đã gửi, ghi nhận hồ sơ. | Hồ sơ ở trạng thái Chờ duyệt, không sửa được. |
| 7 | Hệ thống | Hệ thống thông báo cho mọi Admin. | NT-UC01-01 được gửi. |
| 8 | Khu mua hàng | Khu mua hàng hiển thị trang Trạng thái hồ sơ. | Buyer thấy trạng thái Chờ duyệt, ngày gửi và các bước tiếp theo; vẫn mua hàng bình thường. |

Outcome: Hồ sơ bán hàng ở trạng thái Chờ duyệt.

#### Alternative Flows

##### Alt-01 — Guest chưa đăng nhập

- At step: 2 · Trigger: Người dùng chưa đăng nhập chọn "Bắt đầu đăng ký bán hàng". Guest vẫn xem được trang giới thiệu ở bước 1.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Khu mua hàng | Khu mua hàng yêu cầu đăng nhập hoặc đăng ký tài khoản mua hàng (UC-04, UC-03). | Hiển thị lựa chọn Đăng nhập / Đăng ký, kèm câu "Cần có tài khoản để gửi hồ sơ bán hàng". |
| 2 | Guest | Guest đăng nhập hoặc đăng ký xong. | Hệ thống tự quay lại thao tác đang làm. |

Resume: Quay lại bước 2.

##### Alt-02 — ERR-UC01-01 — Đã có hồ sơ

- At step: 1 hoặc 2 (kể cả mở thẳng đường dẫn form) · Trigger: Tài khoản đã có hồ sơ Chờ duyệt, Bị từ chối hoặc Đã duyệt.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Khu mua hàng | Khu mua hàng không mở form mới. | Hiển thị ERR-UC01-01 cùng trang Trạng thái hồ sơ hiện có (Đã duyệt thì có nút vào Kênh người bán). |

Resume: Kết thúc.

##### Alt-03 — ERR-UC01-02 — Thiếu minh chứng

- At step: 5 · Trigger: Buyer gửi hồ sơ khi chưa tải minh chứng nào.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Khu mua hàng | Khu mua hàng chặn gửi. | Hiển thị ERR-UC01-02. |

Resume: Quay lại bước 4.

##### Alt-04 — ERR-UC01-03 — Ảnh không hợp lệ

- At step: 4 · Trigger: Ảnh sai định dạng hoặc lớn hơn 10 MB.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Khu mua hàng | Khu mua hàng không nhận ảnh đó. | Hiển thị ERR-UC01-03; các ảnh hợp lệ khác vẫn giữ. |

Resume: Chọn ảnh khác, lặp lại bước 4.

##### Alt-05 — ERR-UC01-04 — Thông tin xưởng chưa hợp lệ

- At step: 5 · Trigger: Thiếu trường bắt buộc hoặc sai định dạng (tên xưởng, địa chỉ, số điện thoại, năm bắt đầu).

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Khu mua hàng | Khu mua hàng chặn gửi, đánh dấu trường sai. | Hiển thị ERR-UC01-04 và lỗi cụ thể ngay tại từng trường. |

Resume: Quay lại bước 3, giữ nguyên dữ liệu đã nhập.

##### Alt-06 — Thoát giữa chừng, lưu bản nháp

- At step: 3 đến 5 · Trigger: Buyer rời form khi chưa gửi.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Hệ thống | Hệ thống giữ thông tin và ảnh đã nhập thành bản nháp hồ sơ. | Chưa thông báo Admin, chưa tính là đã gửi. |
| 2 | Buyer | Lần sau Buyer chọn "Bán hàng cùng vCraft". | Trang giới thiệu hiển thị nút "Tiếp tục hồ sơ đang soạn". |

Resume: Buyer chọn tiếp tục thì quay lại bước đã dừng.

##### Alt-07 — Gửi lại hồ sơ Cần chỉnh sửa

- At step: 1 · Trigger: Hồ sơ của tài khoản đang ở trạng thái Cần chỉnh sửa.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Khu mua hàng | Khu mua hàng hiển thị trang Trạng thái hồ sơ. | Hiển thị lý do Admin yêu cầu chỉnh sửa và nút "Chỉnh sửa và gửi lại". |
| 2 | Buyer | Buyer chọn "Chỉnh sửa và gửi lại". | Form mở với dữ liệu hồ sơ cũ. |
| 3 | Buyer | Buyer sửa thông tin hoặc minh chứng rồi chọn Gửi hồ sơ. | Hồ sơ được gửi lại. |
| 4 | Hệ thống | Hệ thống kiểm tra dữ liệu như bước 6 của Main Flow, chuyển cùng hồ sơ về Chờ duyệt. | Không tạo hồ sơ mới; lịch sử các lần xử lý được giữ. |
| 5 | Hệ thống | Hệ thống thông báo cho mọi Admin. | NT-UC01-02 được gửi. |

Resume: Tiếp tục bước 8 của Main Flow. Lỗi dữ liệu dùng lại Alt-03 đến Alt-05.

#### Data Fields

| Field | Format | Validation | Required | Ghi chú |
|---|---|---|---|---|
| Họ tên chủ hộ | Text; length 100 | Lấy từ tài khoản | Có | Chỉ đọc. Sửa ở UC-05 |
| Email | Text; length 100 | Lấy từ tài khoản | Có | Chỉ đọc. Chỉ Admin xem |
| Tên xưởng | Text; length 150 | Không để trống | Có | Ví dụ: "Xưởng mây tre Bình An". Đổi sau khi duyệt phải qua Admin (BR-2 của UC-02) |
| Địa chỉ trong làng | Text; length 200 | Không để trống | Có | Ví dụ: "Thôn Phú Vinh, Chương Mỹ, Hà Nội". Đổi sau khi duyệt phải qua Admin |
| Năm bắt đầu làm nghề | Number; 4 chữ số | Từ 1900 đến năm hiện tại | Không | Ví dụ: 1985 |
| Số điện thoại liên hệ | Text; length 10 | Số di động Việt Nam, 10 chữ số | Có | Điền sẵn từ tài khoản nếu có, được sửa. Chỉ Admin xem ở bước này; không phải liên hệ công khai của gian hàng (xưởng nhập riêng ở UC-07) |
| Giới thiệu ngắn về xưởng | Text; length 500 | Không | Không | Giúp Admin hiểu xưởng. Điền sẵn vào gian hàng (UC-07) |
| Minh chứng hộ làng nghề | Ảnh JPG / PNG / WebP | 1–5 ảnh, mỗi ảnh ≤ 10 MB | Có | Chỉ Admin xem. Loại giấy tờ: xem G-01 |
| Ảnh xưởng | Ảnh JPG / PNG / WebP | 0–10 ảnh, mỗi ảnh ≤ 10 MB | Không | Điền sẵn vào gian hàng, công khai khi gian hàng được mở |
| Trạng thái hồ sơ | Chọn một: Chờ duyệt, Cần chỉnh sửa, Đã duyệt, Bị từ chối | Hệ thống đặt | — | Hiển thị trên trang Trạng thái hồ sơ |
| Lý do từ chối | Text; length 500 | Hệ thống đặt từ UC-02 | — | Chỉ hiển thị cho người nộp khi Cần chỉnh sửa hoặc Bị từ chối |

#### Error Messages

| Code | Condition | Message | Display | Flow |
|---|---|---|---|---|
| ERR-UC01-01 | Tài khoản đã có hồ sơ đã gửi | "Bạn đã có hồ sơ bán hàng. Mỗi tài khoản chỉ được gửi một hồ sơ." | Trang | Alt-02 |
| ERR-UC01-02 | Thiếu minh chứng | "Vui lòng tải lên ít nhất một giấy tờ minh chứng hộ làng nghề." | Inline | Alt-03 |
| ERR-UC01-03 | Ảnh không hợp lệ | "Ảnh không hợp lệ. Chỉ chấp nhận JPG, PNG, WebP, tối đa 10 MB." | Inline | Alt-04 |
| ERR-UC01-04 | Thông tin xưởng chưa hợp lệ | "Vui lòng kiểm tra lại các trường được đánh dấu." | Inline | Alt-05 |

#### Notifications

| Template ID | Trigger (step) | Channel | Recipient | Title | Body |
|---|---|---|---|---|---|
| NT-UC01-01 | 7 | In-app | Mọi Admin | Hồ sơ bán hàng mới chờ duyệt | "Xưởng {{workshopName}} vừa gửi hồ sơ bán hàng và đang chờ duyệt." |
| NT-UC01-02 | Alt-07 bước 5 | In-app | Mọi Admin | Hồ sơ bán hàng được gửi lại | "Xưởng {{workshopName}} vừa chỉnh sửa và gửi lại hồ sơ bán hàng." |

#### Business Rules

| ID | Rule | Source |
|---|---|---|
| BR-1 | Gửi hồ sơ bán hàng cần tài khoản Buyer đã đăng nhập. Guest xem được trang giới thiệu bán hàng nhưng phải đăng ký hoặc đăng nhập trước khi bắt đầu, và sau đó quay lại đúng bước đang làm. | Overview §1, §3.1; Nhóm đề xuất (tham khảo Etsy) |
| BR-2 | Mỗi tài khoản chỉ có một hồ sơ và sở hữu tối đa một gian hàng; không nộp hồ sơ thứ hai. | Overview §1, §3.1 |
| BR-3 | Khi hồ sơ ở trạng thái Chờ duyệt, Buyer không sửa được hồ sơ. | Overview §3.1 |
| BR-4 | Ở mọi trạng thái hồ sơ, tài khoản vẫn mua hàng bình thường. Tài khoản chưa Đã duyệt không có Kênh người bán và gian hàng không xuất hiện công khai. | Overview §1 |
| BR-5 | Minh chứng, email và số điện thoại trong hồ sơ chỉ Admin và chính người nộp xem được. Số điện thoại này tách khỏi số liên hệ công khai của gian hàng (UC-07). | Đề bài §3.3; Nhóm đề xuất; Chốt 2026-11-18 |
| BR-6 | Hồ sơ Cần chỉnh sửa được sửa và gửi lại trên cùng hồ sơ; hồ sơ Bị từ chối không gửi lại được. | Overview §3.1 |
| BR-7 | Lối vào "Bán hàng cùng vCraft" luôn hiện ở đầu trang cho Guest và Buyer chưa có quyền bán, kèm lối phụ ở chân trang và trang chủ; không hiện cho Admin. Tên mục đổi theo trạng thái: "Tiếp tục hồ sơ" (có bản nháp), "Trạng thái hồ sơ" (đã gửi), "Kênh người bán" (Đã duyệt). | Nhóm đề xuất (tham khảo Etsy); chờ chốt G-04 |
| BR-8 | Hồ sơ chưa gửi được giữ thành bản nháp khi Buyer thoát giữa chừng. | Nhóm đề xuất (tham khảo Etsy); chờ chốt G-05 |
| BR-9 | Thông tin trong hồ sơ được điền sẵn vào gian hàng (UC-07) khi hồ sơ được duyệt. | Overview §3.2 |

#### Acceptance Criteria

| ID | Given | When | Then | Covers |
|---|---|---|---|---|
| UC-01-AC-01 | Buyer đã đăng nhập, chưa có hồ sơ | Điền đủ thông tin xưởng, tải minh chứng, xem lại và chọn Gửi hồ sơ | Hồ sơ ở trạng thái Chờ duyệt; mọi Admin nhận NT-UC01-01; hiện trang Trạng thái hồ sơ | Main Flow |
| UC-01-AC-02 | Guest chưa đăng nhập đang ở trang giới thiệu bán hàng | Chọn "Bắt đầu đăng ký bán hàng", rồi đăng nhập | Quay lại đúng form hồ sơ, không phải trang chủ | Alt-01, BR-1 |
| UC-01-AC-03 | Tài khoản đã có hồ sơ Chờ duyệt, Bị từ chối hoặc Đã duyệt | Mở form hồ sơ (kể cả bằng đường dẫn trực tiếp) | Hiển thị ERR-UC01-01 và trạng thái hiện có; không mở form mới | Alt-02, BR-2 |
| UC-01-AC-04 | Chưa tải minh chứng | Chọn Gửi hồ sơ | Hiển thị ERR-UC01-02; hồ sơ không được gửi | Alt-03 |
| UC-01-AC-05 | Đang ở bước tải ảnh | Chọn tệp PDF hoặc ảnh lớn hơn 10 MB | Hiển thị ERR-UC01-03; ảnh không được nhận | Alt-04 |
| UC-01-AC-06 | Bỏ trống tên xưởng hoặc nhập số điện thoại sai | Chọn Gửi hồ sơ | Hiển thị ERR-UC01-04 và lỗi ngay tại trường sai; dữ liệu đã nhập được giữ | Alt-05 |
| UC-01-AC-07 | Buyer đã nhập một phần thông tin | Đóng trang rồi quay lại "Bán hàng cùng vCraft" | Thấy "Tiếp tục hồ sơ đang soạn" với dữ liệu đã nhập; Admin chưa nhận thông báo | Alt-06, BR-8 |
| UC-01-AC-08 | Hồ sơ Cần chỉnh sửa có lý do | Sửa và gửi lại | Cùng hồ sơ về Chờ duyệt; mọi Admin nhận NT-UC01-02; không tạo hồ sơ thứ hai | Alt-07, BR-6 |
| UC-01-AC-09 | Hồ sơ đang Chờ duyệt | Buyer mở hồ sơ để sửa | Không có nút sửa; chỉ xem được trạng thái | BR-3 |
| UC-01-AC-10 | Hồ sơ Chờ duyệt, Cần chỉnh sửa hoặc Bị từ chối | Buyer mua hàng bình thường; Guest tìm xưởng trên trang công khai | Mua hàng được; xưởng không xuất hiện công khai; không có Kênh người bán | BR-4 |
| UC-01-AC-11 | Có hồ sơ đã gửi | Buyer khác, Guest hoặc Artisan khác xem minh chứng; Guest xem trang gian hàng của xưởng đã mở | Minh chứng không xem được, chỉ Admin và người nộp xem; số điện thoại trong hồ sơ không hiện ở trang công khai | BR-5 |
| UC-01-AC-12 | Người dùng là Guest, Buyer chưa có hồ sơ, Buyer đã gửi hồ sơ, Artisan, Admin | Xem đầu trang | Guest và Buyer chưa có hồ sơ thấy "Bán hàng cùng vCraft"; Buyer đã gửi thấy "Trạng thái hồ sơ"; Artisan thấy "Kênh người bán"; Admin không thấy mục bán hàng | BR-7 |
| UC-01-AC-13 | Hồ sơ được duyệt | Artisan mở gian hàng ở UC-07 | Tên xưởng, địa chỉ, giới thiệu, ảnh xưởng đã điền sẵn từ hồ sơ | BR-9 |

### UC-02 — Kiểm duyệt hồ sơ bán hàng

#### Detail

| Field | Content |
|---|---|
| Use case ID | UC-02 |
| Description | Admin đối chiếu thông tin và minh chứng của hồ sơ bán hàng, rồi duyệt hoặc từ chối kèm lý do; người nộp được báo kết quả. Admin cũng duyệt yêu cầu đổi tên, địa chỉ xưởng và xác minh chứng nhận mà xưởng đăng trên gian hàng. |
| Actors | Admin |
| Channel | Trang quản trị |
| Preconditions | • Admin đã đăng nhập<br>• Có hồ sơ ở trạng thái Chờ duyệt (hoặc yêu cầu đổi thông tin xưởng, xem Alt-05; hoặc chứng nhận cần xác minh, xem Alt-06) |
| Expected | **Thành công:**<br>• Duyệt: hồ sơ Đã duyệt, tài khoản có quyền bán, gian hàng Đang chuẩn bị được tạo sẵn, người nộp nhận NT-UC02-01<br>• Từ chối: hồ sơ Cần chỉnh sửa hoặc Bị từ chối kèm lý do, người nộp nhận NT-UC02-02 hoặc NT-UC02-03<br>• Xác minh chứng nhận: chứng nhận có nhãn "BQL đã xác minh"<br>**Thất bại:**<br>• Hiển thị lỗi, hồ sơ hoặc chứng nhận giữ nguyên trạng thái |
| Screens | • Danh sách hồ sơ (bước 1)<br>• Chi tiết hồ sơ và minh chứng (bước 2-3)<br>• Hộp xác nhận quyết định (bước 4)<br>• Danh sách chứng nhận của xưởng (Alt-06) |
| Notifications | NT-UC02-01 đến NT-UC02-05 |

#### Main Flow

Trigger: Admin mở mục Hồ sơ bán hàng (hoặc bấm vào NT-UC01-01 trong chuông thông báo).

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Admin | Admin mở mục Hồ sơ bán hàng. | Danh sách hồ sơ Chờ duyệt hiển thị, hồ sơ gửi sớm nhất đứng đầu; có tên xưởng, người nộp, ngày gửi, nhãn "Gửi lại" nếu là hồ sơ đã chỉnh sửa. |
| 2 | Admin | Admin chọn một hồ sơ. | Chi tiết hiển thị: thông tin tài khoản, thông tin xưởng, ảnh minh chứng (xem phóng to), ảnh xưởng, lịch sử các lần xử lý trước. |
| 3 | Admin | Admin đối chiếu minh chứng với thông tin xưởng và xác minh hộ làm nghề trong làng. | Admin sẵn sàng ra quyết định. |
| 4 | Admin | Admin chọn Duyệt và xác nhận. | Quyết định được gửi. |
| 5 | Hệ thống | Hệ thống kiểm tra hồ sơ còn Chờ duyệt, đổi sang Đã duyệt, cấp quyền bán cho tài khoản, tạo gian hàng Đang chuẩn bị với thông tin điền sẵn từ hồ sơ. | Hồ sơ Đã duyệt; ghi người duyệt và thời điểm. |
| 6 | Hệ thống | Hệ thống thông báo cho người nộp. | NT-UC02-01 được gửi. |
| 7 | Trang quản trị | Trang quản trị cập nhật danh sách. | Hồ sơ rời danh sách Chờ duyệt, hiển thị ở nhóm Đã duyệt. |

Outcome: Hồ sơ Đã duyệt; tài khoản thành Artisan, gian hàng Đang chuẩn bị.

#### Alternative Flows

##### Alt-01 — Từ chối, cho phép sửa và gửi lại

- At step: 4 · Trigger: Admin chọn "Yêu cầu chỉnh sửa".

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Admin | Admin nhập lý do cần chỉnh sửa và xác nhận. | Quyết định được gửi. |
| 2 | Hệ thống | Hệ thống kiểm tra hồ sơ còn Chờ duyệt, đổi sang Cần chỉnh sửa, lưu lý do, người xử lý, thời điểm. | Hồ sơ Cần chỉnh sửa; người nộp mở được form để sửa (UC-01, Alt-07). |
| 3 | Hệ thống | Hệ thống thông báo cho người nộp. | NT-UC02-02 được gửi. |

Resume: Quay lại bước 7.

##### Alt-02 — Từ chối, không cho gửi lại

- At step: 4 · Trigger: Admin chọn "Từ chối hẳn".

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Trang quản trị | Trang quản trị nhắc rõ người nộp sẽ không gửi lại được. | Hộp xác nhận hiển thị. |
| 2 | Admin | Admin nhập lý do và xác nhận. | Quyết định được gửi. |
| 3 | Hệ thống | Hệ thống kiểm tra hồ sơ còn Chờ duyệt, đổi sang Bị từ chối, lưu lý do, người xử lý, thời điểm. | Hồ sơ Bị từ chối; tài khoản vẫn là Buyer. |
| 4 | Hệ thống | Hệ thống thông báo cho người nộp. | NT-UC02-03 được gửi. |

Resume: Quay lại bước 7.

##### Alt-03 — ERR-UC02-01 — Thiếu hoặc quá ngắn lý do

- At step: 4 (Alt-01, Alt-02, Alt-05 khi từ chối) · Trigger: Admin để trống lý do hoặc nhập dưới 10 ký tự.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Trang quản trị | Trang quản trị chặn gửi quyết định. | Hiển thị ERR-UC02-01. |

Resume: Admin nhập lại lý do, quay lại bước 4.

##### Alt-04 — ERR-UC02-02 — Hồ sơ đã được xử lý hoặc đã thay đổi

- At step: 5 (hoặc Alt-06 bước 2) · Trigger: Admin khác đã xử lý hồ sơ, hoặc hồ sơ hay chứng nhận đã bị thay đổi sau khi Admin mở chi tiết.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Hệ thống | Hệ thống không áp dụng quyết định thứ hai. | Hiển thị ERR-UC02-02; hồ sơ hoặc chứng nhận giữ kết quả của quyết định đầu tiên. |

Resume: Quay lại bước 1.

##### Alt-05 — Xử lý yêu cầu đổi tên xưởng hoặc địa chỉ

- At step: 1 · Trigger: Artisan đã gửi yêu cầu đổi tên xưởng hoặc địa chỉ từ UC-07.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Admin | Admin chọn yêu cầu loại "Đổi thông tin xưởng" trong danh sách. | Chi tiết hiển thị giá trị hiện tại và giá trị đề nghị cạnh nhau. |
| 2 | Admin | Admin chọn Duyệt, hoặc chọn Từ chối và nhập lý do (kiểm tra theo Alt-03). | Quyết định được gửi. |
| 3 | Hệ thống | Hệ thống kiểm tra yêu cầu còn đang chờ. Duyệt thì áp dụng thông tin mới cho gian hàng; từ chối thì giữ thông tin cũ. | Thông tin gian hàng cập nhật hoặc giữ nguyên. |
| 4 | Hệ thống | Hệ thống thông báo cho Artisan. | NT-UC02-04 (duyệt) hoặc NT-UC02-05 (từ chối). |

Resume: Quay lại bước 7. Trong lúc yêu cầu chờ duyệt, gian hàng vẫn hiện thông tin cũ.

##### Alt-06 — Xác minh chứng nhận của xưởng

- At step: 1 · Trigger: Admin muốn đối chiếu chứng nhận mà xưởng đã đăng trên gian hàng (UC-07).

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Admin | Admin mở danh sách và lọc theo loại "Chứng nhận của xưởng". | Danh sách chứng nhận hiển thị: tên xưởng, tên chứng nhận, ngày đăng, nhãn hiện tại ("Do xưởng cung cấp" hoặc "BQL đã xác minh"). Admin không nhận thông báo riêng cho từng chứng nhận mới. |
| 2 | Admin | Admin chọn một chứng nhận, xem ảnh phóng to và chọn Xác minh. | Quyết định được gửi. |
| 3 | Hệ thống | Hệ thống kiểm tra chứng nhận chưa bị sửa hoặc thay ảnh kể từ khi Admin mở, gắn nhãn "BQL đã xác minh", ghi người xác minh và thời điểm. | Chứng nhận hiện nhãn "BQL đã xác minh" ở trang gian hàng công khai (UC-08). |

Resume: Kết thúc. Khi xưởng sửa hoặc thay ảnh chứng nhận, nhãn về "Do xưởng cung cấp" và chứng nhận quay lại danh sách chờ đối chiếu (UC-07, BR-9).

#### Data Fields

| Field | Format | Validation | Required | Ghi chú |
|---|---|---|---|---|
| Bộ lọc loại và trạng thái | Chọn một | Mặc định: Chờ duyệt | Không | Loại: Hồ sơ bán hàng, Đổi thông tin xưởng, Chứng nhận của xưởng. Với chứng nhận, trạng thái là nhãn: Do xưởng cung cấp, BQL đã xác minh |
| Thông tin hồ sơ | Hiển thị | — | — | Toàn bộ trường của UC-01, chỉ Admin xem email, số điện thoại, minh chứng |
| Lịch sử xử lý | Hiển thị | — | — | Quyết định, lý do, người xử lý, thời điểm của các lần trước |
| Lý do | Text; length 10–500 | Không để trống khi từ chối | Có điều kiện | Bắt buộc với Yêu cầu chỉnh sửa, Từ chối hẳn, Từ chối đổi thông tin. Ví dụ: "Ảnh giấy xác nhận bị mờ, chưa đọc được tên thôn." |

#### Error Messages

| Code | Condition | Message | Display | Flow |
|---|---|---|---|---|
| ERR-UC02-01 | Lý do trống hoặc dưới 10 ký tự | "Vui lòng nhập lý do (ít nhất 10 ký tự) để người nộp biết cần xử lý gì." | Inline | Alt-03 |
| ERR-UC02-02 | Hồ sơ hoặc chứng nhận đã được xử lý hoặc đã thay đổi | "Nội dung này đã được xử lý hoặc vừa thay đổi. Vui lòng tải lại để xem trạng thái mới." | Toast | Alt-04 |

#### Notifications

| Template ID | Trigger (step) | Channel | Recipient | Title | Body |
|---|---|---|---|---|---|
| NT-UC02-01 | 6 | In-app | Người nộp hồ sơ | Hồ sơ bán hàng đã được duyệt | "Hồ sơ của xưởng {{workshopName}} đã được duyệt. Hãy chuẩn bị và mở gian hàng của bạn." |
| NT-UC02-02 | Alt-01 bước 3 | In-app | Người nộp hồ sơ | Hồ sơ cần chỉnh sửa | "Hồ sơ của xưởng {{workshopName}} cần chỉnh sửa: {{reason}}" |
| NT-UC02-03 | Alt-02 bước 4 | In-app | Người nộp hồ sơ | Hồ sơ bán hàng bị từ chối | "Hồ sơ của xưởng {{workshopName}} không được duyệt: {{reason}}" |
| NT-UC02-04 | Alt-05 bước 4 | In-app | Artisan | Yêu cầu đổi thông tin xưởng đã được duyệt | "Thông tin mới của xưởng {{workshopName}} đã được áp dụng." |
| NT-UC02-05 | Alt-05 bước 4 | In-app | Artisan | Yêu cầu đổi thông tin xưởng bị từ chối | "Yêu cầu đổi thông tin của xưởng {{workshopName}} bị từ chối: {{reason}}" |

#### Business Rules

| ID | Rule | Source |
|---|---|---|
| BR-1 | Chỉ Admin xử lý hồ sơ, và chỉ xử lý được hồ sơ ở trạng thái Chờ duyệt. Admin không sửa nội dung hồ sơ thay người nộp. | Đề bài §3.3; Nhóm đề xuất |
| BR-2 | Đổi tên xưởng hoặc địa chỉ đã duyệt phải qua Admin duyệt; trong lúc chờ, gian hàng vẫn hiện thông tin cũ. | Overview §3.1 |
| BR-3 | Từ chối luôn ghi lý do. Có hai kiểu: cho gửi lại (Cần chỉnh sửa) và không cho gửi lại (Bị từ chối). | Overview §1, §3.1 |
| BR-4 | Duyệt hồ sơ cấp quyền bán và tạo gian hàng Đang chuẩn bị; Admin không duyệt lần hai khi hộ mở gian hàng, hộ tự mở khi đủ checklist (UC-13). | Overview §3.2 |
| BR-5 | Mỗi hồ sơ chỉ có một quyết định có hiệu lực tại một thời điểm; khi hai Admin xử lý cùng lúc, quyết định đến trước được giữ. | Đề bài §4.2 (khóa bản ghi); Nhóm đề xuất |
| BR-6 | Minh chứng chỉ Admin xem, không công khai. | Đề bài §3.3; Nhóm đề xuất |
| BR-7 | Mọi quyết định lưu người xử lý, thời điểm và lý do để tra lại. | Nhóm đề xuất |
| BR-8 | Nhãn "BQL đã xác minh" của chứng nhận chỉ do Admin gắn ở Alt-06; chứng nhận chưa xác minh mang nhãn "Do xưởng cung cấp". Xưởng sửa hoặc thay ảnh chứng nhận thì nhãn về "Do xưởng cung cấp". | Chốt 2026-11-18 |
| BR-9 | Xưởng thêm chứng nhận không gửi thông báo cho Admin; Admin tự lọc danh sách theo loại "Chứng nhận của xưởng". Việc xác minh không làm thay đổi trạng thái hồ sơ hay gian hàng. | Chốt 2026-11-18 |

#### Acceptance Criteria

| ID | Given | When | Then | Covers |
|---|---|---|---|---|
| UC-02-AC-01 | Có hồ sơ Chờ duyệt | Admin mở chi tiết, chọn Duyệt và xác nhận | Hồ sơ Đã duyệt; tài khoản có Kênh người bán; gian hàng Đang chuẩn bị điền sẵn thông tin; người nộp nhận NT-UC02-01 | Main Flow, BR-4 |
| UC-02-AC-02 | Có hồ sơ Chờ duyệt | Admin chọn Yêu cầu chỉnh sửa, nhập lý do hợp lệ | Hồ sơ Cần chỉnh sửa; người nộp nhận NT-UC02-02 và sửa, gửi lại được | Alt-01, BR-3 |
| UC-02-AC-03 | Có hồ sơ Chờ duyệt | Admin chọn Từ chối hẳn, nhập lý do hợp lệ | Hồ sơ Bị từ chối; người nộp nhận NT-UC02-03; không còn nút gửi lại | Alt-02, BR-3 |
| UC-02-AC-04 | Admin đang từ chối | Để trống lý do hoặc nhập dưới 10 ký tự | Hiển thị ERR-UC02-01; hồ sơ giữ nguyên | Alt-03 |
| UC-02-AC-05 | Hai Admin cùng mở một hồ sơ Chờ duyệt | Admin A duyệt, rồi Admin B từ chối | Hồ sơ giữ kết quả Duyệt; Admin B thấy ERR-UC02-02 | Alt-04, BR-5 |
| UC-02-AC-06 | Artisan gửi yêu cầu đổi tên xưởng | Admin duyệt; sau đó với yêu cầu khác, Admin từ chối kèm lý do | Duyệt: gian hàng hiện tên mới, NT-UC02-04. Từ chối: giữ tên cũ, NT-UC02-05. Trong lúc chờ, gian hàng hiện tên cũ | Alt-05, BR-2 |
| UC-02-AC-07 | Người dùng là Buyer, Artisan hoặc Guest | Mở Trang quản trị hoặc chi tiết hồ sơ bằng đường dẫn trực tiếp | Bị từ chối truy cập (ERR-UC04-06) | BR-1 |
| UC-02-AC-08 | Hồ sơ Đã duyệt | Khách và Buyer khác xem trang công khai, tìm xưởng | Ảnh minh chứng không xuất hiện ở bất kỳ trang công khai nào | BR-6 |
| UC-02-AC-09 | Hồ sơ đã qua nhiều lần xử lý | Admin mở chi tiết | Lịch sử hiển thị mỗi lần: quyết định, lý do, người xử lý, thời điểm | BR-7 |
| UC-02-AC-10 | Xưởng đã đăng chứng nhận nhãn "Do xưởng cung cấp" | Admin lọc loại "Chứng nhận của xưởng", mở chứng nhận và chọn Xác minh | Chứng nhận có nhãn "BQL đã xác minh" ở trang gian hàng công khai; ghi người xác minh và thời điểm | Alt-06, BR-8 |
| UC-02-AC-11 | Chứng nhận đã được xác minh | Xưởng sửa hoặc thay ảnh chứng nhận | Nhãn về "Do xưởng cung cấp"; chứng nhận quay lại danh sách chờ đối chiếu của Admin | Alt-06, BR-8 |
| UC-02-AC-12 | Admin đang mở chi tiết chứng nhận, xưởng vừa thay ảnh | Admin chọn Xác minh | Hiển thị ERR-UC02-02; chứng nhận không đổi nhãn | Alt-04, Alt-06 |
| UC-02-AC-13 | Xưởng vừa thêm một chứng nhận mới | Admin mở chuông thông báo | Không có thông báo về chứng nhận mới; chứng nhận chỉ xuất hiện khi Admin lọc danh sách | BR-9 |

### UC-03 — Đăng ký tài khoản mua hàng

#### Detail

| Field | Content |
|---|---|
| Use case ID | UC-03 |
| Description | Guest nhập họ tên, email, mật khẩu để tạo tài khoản Buyer; hệ thống tự đăng nhập và đưa Guest về thao tác đang làm. |
| Actors | Guest |
| Channel | Khu mua hàng |
| Preconditions | • Người dùng chưa đăng nhập<br>• Email chưa được dùng cho tài khoản nào |
| Expected | **Thành công:**<br>• Tài khoản Buyer được tạo và đã đăng nhập<br>• Giỏ hàng và yêu cầu báo giá đang soạn được giữ<br>**Thất bại:**<br>• Hiển thị lỗi, không tạo tài khoản, dữ liệu đã nhập được giữ (trừ mật khẩu) |
| Screens | • Form đăng ký (bước 1-3) |
| Notifications | Không có |

#### Main Flow

Trigger: Guest chọn "Đăng ký" ở đầu trang, hoặc hệ thống yêu cầu đăng ký khi Guest làm một thao tác cần tài khoản (thanh toán, gửi yêu cầu báo giá, đăng ký bán hàng).

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Guest | Guest chọn Đăng ký. | Form đăng ký hiển thị, có liên kết "Đã có tài khoản? Đăng nhập". |
| 2 | Guest | Guest nhập họ tên, email, mật khẩu, nhập lại mật khẩu và đồng ý Điều khoản sử dụng. | Thông tin sẵn sàng để gửi. |
| 3 | Guest | Guest chọn Tạo tài khoản. | Thông tin được gửi. |
| 4 | Hệ thống | Hệ thống kiểm tra dữ liệu, kiểm tra email chưa tồn tại, tạo tài khoản Buyer. | Tài khoản Buyer được tạo, chưa có quyền bán. |
| 5 | Hệ thống | Hệ thống tự đăng nhập, gộp giỏ hàng Guest vào tài khoản (UC-30) và giữ yêu cầu báo giá đang soạn. | Người dùng đã đăng nhập, không mất dữ liệu đang làm. |
| 6 | Khu mua hàng | Khu mua hàng đưa người dùng về đúng trang hoặc thao tác đang làm; nếu không có thì về trang chủ. | Hiển thị lời chào; đầu trang hiện tên tài khoản. |

Outcome: Tài khoản Buyer đã đăng nhập.

#### Alternative Flows

##### Alt-01 — ERR-UC03-01 — Email đã được sử dụng

- At step: 4 · Trigger: Email đã gắn với một tài khoản khác, bất kể vai trò.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Hệ thống | Hệ thống từ chối tạo tài khoản. | Hiển thị ERR-UC03-01 kèm liên kết Đăng nhập. |

Resume: Quay lại bước 2, giữ nguyên dữ liệu đã nhập (trừ mật khẩu).

##### Alt-02 — ERR-UC03-02 — Mật khẩu không đạt yêu cầu

- At step: 3 · Trigger: Mật khẩu dưới 8 ký tự hoặc thiếu chữ, thiếu số.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Khu mua hàng | Khu mua hàng chặn gửi. | Hiển thị ERR-UC03-02 ngay tại trường mật khẩu. |

Resume: Quay lại bước 2.

##### Alt-03 — ERR-UC03-03 — Mật khẩu nhập lại không khớp

- At step: 3 · Trigger: Hai lần nhập mật khẩu khác nhau.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Khu mua hàng | Khu mua hàng chặn gửi. | Hiển thị ERR-UC03-03. |

Resume: Quay lại bước 2.

##### Alt-04 — ERR-UC03-04 — Thông tin chưa hợp lệ

- At step: 3 · Trigger: Họ tên trống, email sai định dạng.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Khu mua hàng | Khu mua hàng chặn gửi, đánh dấu trường sai. | Hiển thị ERR-UC03-04 tại từng trường. |

Resume: Quay lại bước 2.

##### Alt-05 — ERR-UC03-05 — Chưa đồng ý điều khoản

- At step: 3 · Trigger: Guest chưa chọn đồng ý Điều khoản sử dụng.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Khu mua hàng | Khu mua hàng chặn gửi. | Hiển thị ERR-UC03-05. |

Resume: Quay lại bước 2.

#### Data Fields

| Field | Format | Validation | Required | Ghi chú |
|---|---|---|---|---|
| Họ tên | Text; length 100 | Không để trống | Có | Ví dụ: "Trần Thị Lan" |
| Email | Text; length 100 | Đúng định dạng email; duy nhất, không phân biệt chữ hoa chữ thường, bỏ khoảng trắng đầu cuối (BR-1) | Có | Dùng để đăng nhập. Ví dụ: `lan.tran@gmail.com` |
| Mật khẩu | Text; length 8–64 | Tối thiểu 8 ký tự, có chữ và số | Có | Không hiển thị lại, không ghi nhật ký. Có nút xem/ẩn khi nhập |
| Nhập lại mật khẩu | Text; length 8–64 | Trùng mật khẩu | Có | |
| Đồng ý Điều khoản sử dụng | Hộp chọn | Phải chọn | Có | Nội dung điều khoản: xem G-07 |

Số điện thoại không bắt buộc khi đăng ký; được nhập khi thanh toán (UC-31) hoặc khi gửi hồ sơ bán hàng (UC-01).

#### Error Messages

| Code | Condition | Message | Display | Flow |
|---|---|---|---|---|
| ERR-UC03-01 | Email đã được sử dụng | "Email này đã được sử dụng. Vui lòng dùng email khác hoặc đăng nhập." | Inline | Alt-01 |
| ERR-UC03-02 | Mật khẩu không đạt | "Mật khẩu cần từ 8 đến 64 ký tự, gồm cả chữ và số." | Inline | Alt-02 |
| ERR-UC03-03 | Mật khẩu nhập lại không khớp | "Mật khẩu nhập lại không khớp." | Inline | Alt-03 |
| ERR-UC03-04 | Họ tên trống hoặc email sai định dạng | "Vui lòng nhập họ tên và một địa chỉ email hợp lệ." | Inline | Alt-04 |
| ERR-UC03-05 | Chưa đồng ý điều khoản | "Vui lòng đồng ý Điều khoản sử dụng để tạo tài khoản." | Inline | Alt-05 |

#### Business Rules

| ID | Rule | Source |
|---|---|---|
| BR-1 | Mỗi email chỉ gắn với một tài khoản, bất kể vai trò. Email so sánh không phân biệt chữ hoa chữ thường. | Overview §3.1 |
| BR-2 | Tài khoản mới luôn là Buyer, chưa có quyền bán. Quyền bán chỉ có sau khi hồ sơ được duyệt (UC-01, UC-02). | Overview §1 |
| BR-3 | Chưa xác thực email khi đăng ký; tài khoản dùng được ngay. | Overview §3.1 (EN-06) |
| BR-4 | Không có đăng ký Admin công khai; tài khoản Admin được nạp sẵn. | Overview §3.1 |
| BR-5 | Sau đăng ký, giữ giỏ hàng của Guest và yêu cầu báo giá đang soạn, và đưa người dùng về đúng thao tác đang làm. | Overview §3.1 |

#### Acceptance Criteria

| ID | Given | When | Then | Covers |
|---|---|---|---|---|
| UC-03-AC-01 | Guest chưa đăng nhập, email chưa tồn tại | Điền đủ thông tin hợp lệ, đồng ý điều khoản, chọn Tạo tài khoản | Tài khoản Buyer được tạo; người dùng đã đăng nhập; về đúng trang đang xem | Main Flow |
| UC-03-AC-02 | Email đã có tài khoản (Buyer, Artisan hoặc Admin) | Đăng ký bằng email đó | Hiển thị ERR-UC03-01; không tạo tài khoản; dữ liệu đã nhập được giữ trừ mật khẩu | Alt-01, BR-1 |
| UC-03-AC-03 | Mật khẩu "abcdefgh" (thiếu số) | Chọn Tạo tài khoản | Hiển thị ERR-UC03-02; không tạo tài khoản | Alt-02 |
| UC-03-AC-04 | Hai lần nhập mật khẩu khác nhau | Chọn Tạo tài khoản | Hiển thị ERR-UC03-03 | Alt-03 |
| UC-03-AC-05 | Họ tên trống hoặc email "lan@" | Chọn Tạo tài khoản | Hiển thị ERR-UC03-04 tại trường sai | Alt-04 |
| UC-03-AC-06 | Chưa chọn đồng ý điều khoản | Chọn Tạo tài khoản | Hiển thị ERR-UC03-05 | Alt-05 |
| UC-03-AC-07 | Email đã đăng ký là `lan.tran@gmail.com` | Đăng ký bằng `Lan.Tran@Gmail.com` (có khoảng trắng ở cuối) | Hiển thị ERR-UC03-01 | BR-1 |
| UC-03-AC-08 | Đăng ký thành công | Kiểm tra quyền của tài khoản | Chỉ là Buyer; không có Kênh người bán; không thấy Trang quản trị | BR-2, BR-4 |
| UC-03-AC-09 | Đăng ký thành công ngay sau khi chưa mở email | Dùng tài khoản | Dùng được ngay, không yêu cầu xác thực email | BR-3 |
| UC-03-AC-10 | Guest có 2 món trong giỏ, hoặc bắt đầu đăng ký từ nút "Bắt đầu đăng ký bán hàng" | Đăng ký thành công | Giỏ còn 2 món; hoặc quay lại đúng form hồ sơ bán hàng | BR-5 |

### UC-04 — Đăng nhập, đăng xuất

#### Detail

| Field | Content |
|---|---|
| Use case ID | UC-04 |
| Description | Người dùng nhập email và mật khẩu để vào hệ thống, được đưa về nơi đang làm hoặc khu vực đúng quyền; có thể đăng xuất bất kỳ lúc nào. |
| Actors | Guest (trở thành Buyer, Artisan hoặc Admin sau khi đăng nhập) |
| Channel | Khu mua hàng, Kênh người bán, Trang quản trị |
| Preconditions | • Người dùng chưa đăng nhập (với đăng nhập)<br>• Người dùng đã có tài khoản |
| Expected | **Thành công:**<br>• Phiên đăng nhập được tạo, menu hiện theo quyền<br>• Người dùng về đúng thao tác đang làm hoặc khu vực mặc định<br>**Thất bại:**<br>• Hiển thị lỗi, không tạo phiên |
| Screens | • Form đăng nhập (bước 1-3)<br>• Trang thông báo chưa có quyền (Alt-04, Alt-05, Alt-06) |
| Notifications | Không có |

#### Main Flow

Trigger: Guest chọn "Đăng nhập", hoặc hệ thống yêu cầu đăng nhập khi Guest làm thao tác cần tài khoản.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Guest | Guest chọn Đăng nhập. | Form đăng nhập hiển thị, có liên kết "Chưa có tài khoản? Đăng ký". |
| 2 | Guest | Guest nhập email và mật khẩu. | Thông tin sẵn sàng để gửi. |
| 3 | Guest | Guest chọn Đăng nhập. | Thông tin được gửi. |
| 4 | Hệ thống | Hệ thống xác thực email và mật khẩu, tạo phiên đăng nhập. | Người dùng đã đăng nhập. |
| 5 | Hệ thống | Hệ thống gộp giỏ hàng Guest vào giỏ tài khoản (UC-30) và giữ yêu cầu báo giá đang soạn (UC-18). | Không mất dữ liệu đang làm. |
| 6 | Khu mua hàng | Khu mua hàng đưa người dùng về thao tác hoặc trang đang làm. Nếu không có: Buyer và Artisan về Khu mua hàng (Artisan có nút Kênh người bán, Buyer đã gửi hồ sơ có mục Trạng thái hồ sơ); Admin về Trang quản trị. | Menu và nút hiển thị đúng quyền. |

Outcome: Người dùng đã đăng nhập, đang ở đúng khu vực.

#### Alternative Flows

##### Alt-01 — ERR-UC04-01 — Sai email hoặc mật khẩu

- At step: 4 · Trigger: Email không tồn tại hoặc mật khẩu sai.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Hệ thống | Hệ thống từ chối, không nói rõ sai email hay sai mật khẩu. | Hiển thị ERR-UC04-01. |

Resume: Quay lại bước 2.

##### Alt-02 — ERR-UC04-02 — Đăng nhập sai nhiều lần

- At step: 4 · Trigger: Nhập sai 5 lần liên tiếp cho cùng một email.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Hệ thống | Hệ thống tạm dừng đăng nhập cho email đó trong 15 phút, kể cả khi lần sau nhập đúng. | Hiển thị ERR-UC04-02. |

Resume: Kết thúc; sau 15 phút bắt đầu lại từ bước 1.

##### Alt-03 — Đăng nhập bằng Google (Should)

- At step: 2 · Trigger: Guest chọn "Đăng nhập bằng Google".

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Hệ thống | Hệ thống xác thực qua tài khoản Google. Nếu email đã có tài khoản thì đăng nhập vào tài khoản đó; nếu chưa thì tạo tài khoản Buyer mới. | Người dùng đã đăng nhập. |

Resume: Tiếp tục bước 5. Email là khóa nhận diện, mỗi email vẫn một tài khoản.

##### Alt-04 — ERR-UC04-03 — Mở Kênh người bán khi chưa có hồ sơ

- At step: 6 · Trigger: Buyer chưa có hồ sơ bán hàng mở Kênh người bán (kể cả bằng đường dẫn trực tiếp).

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Khu mua hàng | Khu mua hàng không vào Kênh người bán. | Hiển thị ERR-UC04-03 kèm nút "Đăng ký bán hàng" (UC-01). |

Resume: Kết thúc.

##### Alt-05 — ERR-UC04-04 — Mở Kênh người bán khi hồ sơ chưa được duyệt

- At step: 6 · Trigger: Hồ sơ đang Chờ duyệt, Cần chỉnh sửa hoặc Bị từ chối mà người dùng mở Kênh người bán.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Khu mua hàng | Khu mua hàng không vào Kênh người bán. | Hiển thị ERR-UC04-04 kèm nút "Xem trạng thái hồ sơ". |

Resume: Kết thúc.

##### Alt-06 — ERR-UC04-06 — Không có quyền vào Trang quản trị

- At step: 6 · Trigger: Người dùng không phải Admin mở Trang quản trị.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Hệ thống | Hệ thống từ chối truy cập. | Hiển thị ERR-UC04-06. |

Resume: Kết thúc.

##### Alt-07 — Mở thẳng đường dẫn Kênh người bán khi chưa đăng nhập

- At step: 1 · Trigger: Guest mở đường dẫn Kênh người bán hoặc Trang quản trị.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Khu mua hàng | Khu mua hàng chuyển sang form đăng nhập. | Sau khi đăng nhập thành công, đi tiếp bước 6 theo quyền (có thể gặp Alt-04, Alt-05, Alt-06). |

Resume: Tiếp tục bước 2.

##### Alt-08 — ERR-UC04-05 — Phiên hết hạn

- At step: Bất kỳ thao tác nào khi đang đăng nhập · Trigger: Phiên đã hết hạn do không hoạt động quá lâu.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Hệ thống | Hệ thống đưa người dùng về form đăng nhập, giữ lại thao tác đang làm. | Hiển thị ERR-UC04-05. |

Resume: Sau khi đăng nhập thì quay lại đúng thao tác đó (bước 6).

##### Alt-09 — Đăng xuất

- At step: Luồng độc lập · Trigger: Người dùng đang đăng nhập chọn Đăng xuất.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Hệ thống | Hệ thống kết thúc phiên đăng nhập. | Mọi trang cần đăng nhập không còn truy cập được. |
| 2 | Khu mua hàng | Khu mua hàng đưa người dùng về trang chủ. | Người dùng trở lại là Guest. |

Resume: Kết thúc. Giỏ hàng tài khoản vẫn lưu cho lần đăng nhập sau.

#### Data Fields

| Field | Format | Validation | Required | Ghi chú |
|---|---|---|---|---|
| Email | Text; length 100 | Không để trống | Có | Không phân biệt chữ hoa chữ thường |
| Mật khẩu | Text; length 8–64 | Không để trống | Có | Không hiển thị lại, không ghi nhật ký. Có nút xem/ẩn |

#### Error Messages

| Code | Condition | Message | Display | Flow |
|---|---|---|---|---|
| ERR-UC04-01 | Sai email hoặc mật khẩu | "Email hoặc mật khẩu không đúng." | Inline | Alt-01 |
| ERR-UC04-02 | Đăng nhập sai 5 lần liên tiếp | "Bạn đã nhập sai quá nhiều lần. Vui lòng thử lại sau 15 phút." | Inline | Alt-02 |
| ERR-UC04-03 | Chưa có hồ sơ bán hàng | "Tài khoản của bạn chưa có quyền bán hàng. Đăng ký bán hàng để bắt đầu." | Trang | Alt-04 |
| ERR-UC04-04 | Hồ sơ chưa được duyệt | "Kênh người bán chỉ mở sau khi hồ sơ bán hàng được duyệt." | Trang | Alt-05 |
| ERR-UC04-05 | Phiên hết hạn | "Phiên đăng nhập đã hết hạn. Vui lòng đăng nhập lại." | Toast | Alt-08 |
| ERR-UC04-06 | Không phải Admin mở Trang quản trị | "Bạn không có quyền truy cập trang này." | Trang | Alt-06 |

#### Business Rules

| ID | Rule | Source |
|---|---|---|
| BR-1 | Một web app, một phiên đăng nhập; menu hiện theo quyền. Buyer đã duyệt bán chuyển giữa Khu mua hàng và Kênh người bán mà không đăng nhập lại. | Overview §1 |
| BR-2 | Mỗi thao tác đều kiểm tra quyền, không chỉ ẩn nút hoặc menu: mở thẳng đường dẫn ngoài quyền bị từ chối. | Đề bài §1 (An ninh); Overview §1 |
| BR-3 | Sau đăng nhập quay lại đúng thao tác đang làm; giữ giỏ hàng và yêu cầu báo giá đang soạn. | Overview §3.1 |
| BR-4 | Thông báo lỗi đăng nhập không cho biết email có tồn tại hay không. | Nhóm đề xuất |
| BR-5 | Sai 5 lần liên tiếp thì tạm dừng đăng nhập email đó 15 phút. Phiên hết hạn sau thời gian không hoạt động; giá trị cụ thể xem G-06. | Nhóm đề xuất; chờ chốt G-06 |
| BR-6 | Admin đăng nhập vào Trang quản trị; Admin không mua và không bán. | Overview §1 |
| BR-7 | Tài khoản Buyer có hồ sơ chưa Đã duyệt vẫn đăng nhập và mua hàng bình thường. | Overview §1 |
| BR-8 | Đăng nhập bằng Google là Should; không bắt buộc cho demo. | Overview §3.1 |

#### Acceptance Criteria

| ID | Given | When | Then | Covers |
|---|---|---|---|---|
| UC-04-AC-01 | Buyer có tài khoản | Nhập đúng email, mật khẩu | Đăng nhập thành công, về trang đang xem hoặc Khu mua hàng | Main Flow |
| UC-04-AC-02 | Nhập sai mật khẩu, và riêng một lần nhập email không tồn tại | Chọn Đăng nhập | Cả hai trường hợp đều hiện cùng ERR-UC04-01 | Alt-01, BR-4 |
| UC-04-AC-03 | Đã nhập sai 4 lần | Nhập sai lần thứ 5, rồi nhập đúng ngay sau đó | Hiện ERR-UC04-02; lần nhập đúng vẫn bị từ chối cho đến hết 15 phút | Alt-02, BR-5 |
| UC-04-AC-04 | Guest bấm "Đăng nhập bằng Google" (nếu có làm) | Xác thực Google thành công | Email đã có tài khoản thì vào tài khoản đó; chưa có thì tạo Buyer; không tạo hai tài khoản cùng email | Alt-03, BR-8 |
| UC-04-AC-05 | Buyer chưa có hồ sơ | Mở thẳng đường dẫn Kênh người bán | Hiện ERR-UC04-03 kèm nút Đăng ký bán hàng | Alt-04, BR-2 |
| UC-04-AC-06 | Buyer có hồ sơ Chờ duyệt, Cần chỉnh sửa hoặc Bị từ chối | Mở Kênh người bán | Hiện ERR-UC04-04 kèm nút Xem trạng thái hồ sơ | Alt-05, BR-7 |
| UC-04-AC-07 | Buyer hoặc Artisan đã đăng nhập | Mở thẳng đường dẫn Trang quản trị | Hiện ERR-UC04-06; không thấy dữ liệu quản trị | Alt-06, BR-2 |
| UC-04-AC-08 | Guest chưa đăng nhập | Mở thẳng đường dẫn Kênh người bán, rồi đăng nhập bằng tài khoản Artisan | Vào đúng Kênh người bán | Alt-07 |
| UC-04-AC-09 | Phiên đã hết hạn khi đang soạn yêu cầu báo giá | Bấm Gửi | Hiện ERR-UC04-05; đăng nhập lại xong, yêu cầu đang soạn còn nguyên | Alt-08, BR-3 |
| UC-04-AC-10 | Người dùng đang đăng nhập | Chọn Đăng xuất, rồi bấm nút Quay lại của trình duyệt | Về trang chủ; trang cần đăng nhập không mở lại được; giỏ tài khoản còn nguyên khi đăng nhập lần sau | Alt-09 |
| UC-04-AC-11 | Artisan đã duyệt đang ở Khu mua hàng | Vào Kênh người bán rồi quay về sàn | Không bị hỏi đăng nhập lại | BR-1 |
| UC-04-AC-12 | Guest có 2 món trong giỏ | Đăng nhập | Giỏ gộp vào giỏ tài khoản; món trùng cộng số lượng | BR-3 |
| UC-04-AC-13 | Admin | Đăng nhập | Về Trang quản trị; không có giỏ hàng, nút mua hay Kênh người bán | BR-6 |

### UC-05 — Quản lý thông tin tài khoản

#### Detail

| Field | Content |
|---|---|
| Use case ID | UC-05 |
| Description | Người dùng đã đăng nhập xem và sửa họ tên, số điện thoại, đổi mật khẩu của chính mình. (Mức Should) |
| Actors | Buyer, Artisan, Admin |
| Channel | Khu mua hàng, Kênh người bán, Trang quản trị |
| Preconditions | • Người dùng đã đăng nhập |
| Expected | **Thành công:**<br>• Thông tin hoặc mật khẩu mới được lưu<br>**Thất bại:**<br>• Hiển thị lỗi, giữ nguyên thông tin cũ |
| Screens | • Trang Tài khoản của tôi (bước 1-3)<br>• Hộp đổi mật khẩu (Alt-01) |
| Notifications | Không có |

#### Main Flow

Trigger: Người dùng chọn "Tài khoản của tôi" ở menu người dùng.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Người dùng | Người dùng mở "Tài khoản của tôi". | Hiển thị họ tên, email (chỉ đọc), số điện thoại. |
| 2 | Người dùng | Người dùng sửa họ tên và/hoặc số điện thoại, chọn Lưu. | Thông tin được gửi. |
| 3 | Hệ thống | Hệ thống kiểm tra dữ liệu và lưu. | Thông tin mới được lưu. |
| 4 | Khu mua hàng | Khu mua hàng hiển thị xác nhận. | Hiển thị "Đã lưu thông tin"; tên mới hiện ở menu. |

Outcome: Họ tên và số điện thoại đã cập nhật.

#### Alternative Flows

##### Alt-01 — Đổi mật khẩu

- At step: 1 · Trigger: Người dùng chọn "Đổi mật khẩu".

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Người dùng | Người dùng nhập mật khẩu hiện tại, mật khẩu mới, nhập lại mật khẩu mới, chọn Đổi mật khẩu. | Thông tin được gửi. |
| 2 | Hệ thống | Hệ thống kiểm tra, lưu mật khẩu mới, kết thúc các phiên đăng nhập khác của tài khoản (phiên đang dùng giữ nguyên). | Mật khẩu mới có hiệu lực. |
| 3 | Khu mua hàng | Khu mua hàng hiển thị xác nhận. | Hiển thị "Đã đổi mật khẩu". |

Resume: Kết thúc.

##### Alt-02 — ERR-UC05-01 — Thông tin chưa hợp lệ

- At step: 2 · Trigger: Họ tên trống hoặc số điện thoại sai định dạng.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Khu mua hàng | Khu mua hàng chặn lưu. | Hiển thị ERR-UC05-01 tại trường sai. |

Resume: Quay lại bước 2.

##### Alt-03 — ERR-UC05-02 — Mật khẩu hiện tại sai

- At step: Alt-01 bước 2 · Trigger: Mật khẩu hiện tại không đúng.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Hệ thống | Hệ thống từ chối đổi. | Hiển thị ERR-UC05-02. |

Resume: Quay lại Alt-01 bước 1.

##### Alt-04 — ERR-UC05-03 — Mật khẩu mới không hợp lệ

- At step: Alt-01 bước 1 · Trigger: Mật khẩu mới không đạt yêu cầu hoặc nhập lại không khớp.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Khu mua hàng | Khu mua hàng chặn gửi. | Hiển thị ERR-UC05-03. |

Resume: Quay lại Alt-01 bước 1.

#### Data Fields

| Field | Format | Validation | Required | Ghi chú |
|---|---|---|---|---|
| Họ tên | Text; length 100 | Không để trống | Có | |
| Email | Text; length 100 | — | — | Chỉ đọc, không đổi được (BR-1) |
| Số điện thoại | Text; length 10 | Số di động Việt Nam, 10 chữ số | Không | Buyer, Artisan nhập khi cần liên hệ giao hàng. Admin có thể bỏ trống |
| Mật khẩu hiện tại | Text; length 8–64 | Không để trống | Có (khi đổi mật khẩu) | Không hiển thị lại, không ghi nhật ký |
| Mật khẩu mới | Text; length 8–64 | Tối thiểu 8 ký tự, có chữ và số | Có (khi đổi mật khẩu) | Như trên |
| Nhập lại mật khẩu mới | Text; length 8–64 | Trùng mật khẩu mới | Có (khi đổi mật khẩu) | |

#### Error Messages

| Code | Condition | Message | Display | Flow |
|---|---|---|---|---|
| ERR-UC05-01 | Họ tên trống hoặc số điện thoại sai | "Vui lòng nhập họ tên và số điện thoại hợp lệ (10 chữ số)." | Inline | Alt-02 |
| ERR-UC05-02 | Mật khẩu hiện tại sai | "Mật khẩu hiện tại không đúng." | Inline | Alt-03 |
| ERR-UC05-03 | Mật khẩu mới không hợp lệ hoặc không khớp | "Mật khẩu mới cần từ 8 đến 64 ký tự, gồm cả chữ và số, và nhập lại phải trùng khớp." | Inline | Alt-04 |

#### Business Rules

| ID | Rule | Source |
|---|---|---|
| BR-1 | Email không đổi được trong MVP1 (chưa có xác thực email, EN-06). | Overview §3.1 |
| BR-2 | Chỉ sửa được thông tin của chính mình. Thông tin gian hàng (tên xưởng, địa chỉ, ảnh) quản lý ở UC-07, không ở đây. | Overview §3.1 |
| BR-3 | Đổi họ tên hoặc số điện thoại không làm đổi thông tin đã ghi trong đơn và yêu cầu cũ. | Nhóm đề xuất (cùng nguyên tắc "sửa không đổi đơn cũ", Overview §3.2) |
| BR-4 | Đổi mật khẩu kết thúc các phiên đăng nhập khác của tài khoản. | Nhóm đề xuất |
| BR-5 | Tài khoản Admin được nạp sẵn; Admin chỉ sửa họ tên và mật khẩu của chính mình. | Overview §3.1 |

#### Acceptance Criteria

| ID | Given | When | Then | Covers |
|---|---|---|---|---|
| UC-05-AC-01 | Người dùng đã đăng nhập | Đổi họ tên và số điện thoại hợp lệ, chọn Lưu | Hiển thị "Đã lưu thông tin"; tên mới hiện ở menu | Main Flow |
| UC-05-AC-02 | Người dùng đã đăng nhập | Đổi mật khẩu với mật khẩu hiện tại đúng, mật khẩu mới hợp lệ | Mật khẩu mới có hiệu lực; đăng nhập lần sau dùng mật khẩu mới | Alt-01 |
| UC-05-AC-03 | Họ tên trống hoặc số điện thoại 9 chữ số | Chọn Lưu | Hiển thị ERR-UC05-01; thông tin cũ giữ nguyên | Alt-02 |
| UC-05-AC-04 | Nhập mật khẩu hiện tại sai | Chọn Đổi mật khẩu | Hiển thị ERR-UC05-02; mật khẩu không đổi | Alt-03 |
| UC-05-AC-05 | Mật khẩu mới thiếu số, hoặc nhập lại không khớp | Chọn Đổi mật khẩu | Hiển thị ERR-UC05-03; mật khẩu không đổi | Alt-04 |
| UC-05-AC-06 | Người dùng mở "Tài khoản của tôi" | Tìm cách đổi email | Trường email chỉ đọc, không có nút đổi | BR-1 |
| UC-05-AC-07 | Artisan mở "Tài khoản của tôi" | Tìm tên xưởng, địa chỉ xưởng | Không có ở trang này; chỉ có ở Kênh người bán (UC-07) | BR-2 |
| UC-05-AC-08 | Buyer đã có đơn dưới tên cũ | Đổi họ tên | Đơn cũ vẫn hiển thị tên lúc đặt | BR-3 |
| UC-05-AC-09 | Tài khoản đăng nhập ở hai trình duyệt | Đổi mật khẩu ở trình duyệt A | Phiên ở trình duyệt B bị kết thúc, phải đăng nhập lại; phiên A giữ nguyên | BR-4 |
| UC-05-AC-10 | Admin | Mở "Tài khoản của tôi" | Sửa được họ tên và mật khẩu; không có mục gian hàng hay hồ sơ bán hàng | BR-5 |

## IV. Open Questions

| ID | Question | Affects | Decision |
|---|---|---|---|
| G-01 | Những giấy tờ nào được chấp nhận làm minh chứng (giấy xác nhận của thôn / xã, chứng nhận nghệ nhân, ảnh xưởng…)? | UC-01 Data Fields, UC-02 | Open |
| G-02 | Có cần xác thực email trước khi dùng tài khoản không? | UC-03, UC-05 | Đã chốt: chưa làm trong MVP1, để EN-06 (Overview §3.1) |
| G-03 | Hồ sơ Bị từ chối hẳn có cách nào xử lý lại nếu Admin từ chối nhầm không (hiện chưa nộp lại, chưa có rút hồ sơ EN-18)? | UC-01, UC-02 | Open. Tạm thời: liên hệ BQL ngoài hệ thống |
| G-04 | Chốt lối vào bán hàng: "Bán hàng cùng vCraft" ở đầu trang (Guest và Buyer), lối phụ ở chân trang và trang chủ, kèm trang giới thiệu bán hàng. Đề xuất sau khi tham khảo Etsy: chỉ để ở chân trang thì người muốn bán khó tìm | UC-01, BR-7 | Open. Đề xuất như mô tả |
| G-05 | Có lưu bản nháp hồ sơ khi Buyer thoát giữa chừng không? Đề xuất có, vì hồ sơ có nhiều ảnh | UC-01, BR-8 | Open. Đề xuất: có |
| G-06 | Số lần sai và thời gian tạm dừng đăng nhập (đề xuất 5 lần, 15 phút); thời gian phiên hết hạn khi không hoạt động | UC-04, BR-5 | Open |
| G-07 | Nội dung Điều khoản sử dụng khi đăng ký; có thể dùng bản ngắn do nhóm soạn cho demo | UC-03 | Open |
| G-08 | Yêu cầu đổi tên xưởng, địa chỉ: gửi ở UC-07 (`02-showcase`) và xử lý ở UC-02 Alt-05. Cần thống nhất mô tả ở BRD `02-showcase` và mã thông báo | UC-02, UC-07 | Đã chốt 2026-11-18: BRD `02-showcase` v0.2.0 mô tả gửi yêu cầu ở UC-07 Alt-03; UC-02 Alt-05 xử lý và gửi NT-UC02-04, NT-UC02-05 |
| G-09 | Nhãn "BQL đã xác minh" của chứng nhận xưởng do Admin gắn ở đâu, khi nào bị gỡ, Admin có được báo khi có chứng nhận mới không? | UC-02 Alt-06, BR-8, BR-9; UC-07 | Đã chốt 2026-11-18: gắn ở UC-02 Alt-06; sửa hoặc thay ảnh thì nhãn về "Do xưởng cung cấp"; Admin không nhận thông báo, tự lọc danh sách |
