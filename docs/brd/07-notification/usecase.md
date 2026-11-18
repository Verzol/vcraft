---
title: "07 — Trung tâm thông báo"
group: 07-notification
type: brd
status: draft
version: 0.2.0
date: 2026-11-18
author: Khánh Chi
---

# 07 — Trung tâm thông báo

Change history: [CHANGELOG.md](CHANGELOG.md)

## I. Overview

Nhóm chức năng Notification Center: nơi duy nhất để người dùng đã đăng nhập (Buyer, Artisan, Admin) nhận các thông báo trong hệ thống. Người dùng nhìn chuông ở đầu trang để biết số thông báo chưa đọc, mở bảng thông báo hoặc trang Thông báo, bấm một thông báo để đi tới đúng đối tượng liên quan (hồ sơ, yêu cầu báo giá, báo giá, đơn, tin tức) và thông báo được đánh dấu đã đọc. Nhóm này chỉ hiển thị thông báo và quản lý trạng thái đọc; sự kiện nào phát thông báo, gửi cho ai và nội dung gì do chính use case phát sinh sự kiện khai báo (mã NT-UCNN-xx). Thông báo chỉ nằm trong hệ thống; đọc thông báo không có nghĩa là đồng ý giá hay đã nhận hàng, mọi quyết định vẫn làm ở màn hình nghiệp vụ.

### Terms

| Term | Meaning |
|---|---|
| Thông báo | Một bản tin hệ thống gửi cho một tài khoản về một sự kiện: tiêu đề, nội dung, thời điểm gửi, trạng thái đọc và một đối tượng đích. Nội dung được chụp lúc gửi, không đổi khi dữ liệu gốc đổi. |
| Mẫu thông báo | Khuôn nội dung của một loại thông báo, mã dạng NT-UC18-01 (UC18 là use case phát). Do use case phát khai báo; tổng hợp ở Danh mục thông báo bên dưới. |
| Người nhận | Tài khoản được gửi thông báo. Thông báo gửi cho nhiều người (mọi Admin, mọi Artisan, nhiều xưởng) được tạo thành một bản riêng cho từng người nhận, mỗi người có trạng thái đọc riêng. |
| Chưa đọc, Đã đọc | Trạng thái của một thông báo với một người nhận. Chưa đọc là mặc định khi gửi. |
| Số chưa đọc | Số thông báo Chưa đọc của tài khoản, hiện trên chuông. |
| Đối tượng đích | Màn hình hoặc đối tượng nghiệp vụ mà thông báo dẫn tới khi bấm: hồ sơ bán hàng, yêu cầu báo giá, so sánh báo giá, đơn đặt riêng, tin tức... |
| Chuông thông báo | Biểu tượng ở đầu trang của người đã đăng nhập, ở Khu mua hàng, Kênh người bán và Trang quản trị. Guest không có chuông. |
| Bảng thông báo | Khung hiện ra khi bấm chuông, gồm các thông báo mới nhất. |
| Trang Thông báo | Màn hình danh sách đầy đủ, có tab Tất cả và Chưa đọc. |
| Nhãn khu vực | Nhãn Mua hàng, Bán hàng hoặc Quản trị trên mỗi thông báo, theo khu vực của đối tượng đích. |

## II. Scope

| Business topic | Channel | Use case |
|---|---|---|
| Hiển thị chuông và số thông báo chưa đọc ở đầu trang | Khu mua hàng, Kênh người bán, Trang quản trị | UC-29 |
| Xem bảng thông báo mới nhất và trang Thông báo đầy đủ, lọc chưa đọc | Khu mua hàng, Kênh người bán, Trang quản trị | UC-29 |
| Bấm thông báo để mở đúng đối tượng liên quan và đánh dấu đã đọc | Khu mua hàng, Kênh người bán, Trang quản trị | UC-29 |
| Đánh dấu tất cả thông báo đã đọc | Khu mua hàng, Kênh người bán, Trang quản trị | UC-29 |

**Ngoài phạm vi:** Quyết định sự kiện nào phát thông báo, ai nhận và nội dung gì (thuộc use case phát sinh sự kiện, xem Danh mục thông báo); xóa thông báo và tự xóa theo thời hạn (G-03); đánh dấu chưa đọc lại; tắt hoặc tùy chỉnh loại thông báo; thông báo qua email, SMS, push hoặc âm thanh (EN-05); cập nhật thời gian thực, mà số chưa đọc chỉ làm mới định kỳ (G-02); gom nhóm, tìm kiếm thông báo; nhắc lại thông báo chưa đọc; thống kê lượt đọc (EN-11); thông báo cho Guest; Admin xem thông báo của người khác.

**Liên quan**

| ID | Tài liệu | Quan hệ |
|---|---|---|
| BRD-01 | [01-account](../01-account/usecase.md) | Phát NT-UC01-01, NT-UC01-02, NT-UC02-01 đến NT-UC02-05. Thông báo hồ sơ mở Trang Trạng thái hồ sơ (UC-01) hoặc chi tiết hồ sơ ở Trang quản trị (UC-02). Đăng nhập, phiên hết hạn và chuyển khu vực không đăng nhập lại theo UC-04 (Alt-07, Alt-08, BR-1). |
| BRD-02 | [02-showcase](../02-showcase/usecase.md) | Phát NT-UC07-01 (xưởng xin đổi tên hoặc địa chỉ). |
| BRD-03 | [03-traceability](../03-traceability/usecase.md) | Không phát thông báo. |
| BRD-04 | [04-quotation](../04-quotation/usecase.md) | Phát NT-UC18-01 đến NT-UC18-05, NT-UC19-01 đến NT-UC19-05, NT-UC20-01 đến NT-UC20-05, NT-UC22-01 đến NT-UC22-04, NT-UC23-01. Thông báo mở So sánh báo giá (UC-20), chi tiết yêu cầu và đơn đặt riêng (UC-19, UC-21, UC-22). Đơn chuyển Đang sản xuất không có thông báo riêng (UC-22, G-04). |
| BRD-05 | [05-village-map](../05-village-map/usecase.md) | Không phát thông báo. |
| BRD-06 | [06-news](../06-news/usecase.md) | Phát NT-UC27-01. Mở thông báo dẫn tới UC-28 (Alt-02); tin đã Ẩn xử lý ở UC-28 (Alt-05). |
| BRD-08 | [08-order](../08-order/usecase.md) | Phát NT-UC31-01 đến NT-UC31-03, NT-UC32-01 đến NT-UC32-08, NT-UC33-01 đến NT-UC33-09. Thông báo mở chi tiết đơn mua hàng và đơn đặt riêng (UC-32, UC-22); nội dung không chứa họ tên, số điện thoại, địa chỉ nhận (UC-31 BR-12, UC-32 BR-9, UC-33 BR-14). |
| BRD-09 | [09-portal](../09-portal/usecase.md) | Tài khoản Admin Đã khóa ngừng nhận thông báo mới (UC-34 BR-6); phạm vi nhận thông báo của Admin theo nhóm quyền chốt ở G-01: NT-UC01-01, NT-UC01-02, NT-UC07-01 chỉ gửi Trưởng BQL và Admin có quyền `Kiểm duyệt & Danh mục`. |

## III. Use Cases

| Use case ID | Use case | Description |
|---|---|---|
| UC-29 | Chuông thông báo, số chưa đọc, mở đúng đối tượng | Người dùng đã đăng nhập xem số chưa đọc, mở danh sách thông báo, bấm một thông báo để tới đúng đối tượng và đánh dấu đã đọc |

### UC-29 — Chuông thông báo, số chưa đọc, mở đúng đối tượng

#### Detail

| Field | Content |
|---|---|
| Use case ID | UC-29 |
| Description | Người dùng đã đăng nhập nhìn chuông thông báo để biết số thông báo chưa đọc, mở bảng thông báo hoặc trang Thông báo, bấm một thông báo để đi tới đúng đối tượng liên quan; thông báo được đánh dấu đã đọc. Người dùng cũng đánh dấu được tất cả thông báo đã đọc. UC-29 chỉ hiển thị và quản lý trạng thái đọc; thông báo do các use case khác phát. |
| Actors | Buyer, Artisan, Admin |
| Channel | Khu mua hàng, Kênh người bán, Trang quản trị |
| Preconditions | • Người dùng đã đăng nhập bằng tài khoản Buyer, Artisan hoặc Admin |
| Expected | **Thành công:**<br>• Chuông hiện đúng số chưa đọc của tài khoản<br>• Thông báo được bấm chuyển sang Đã đọc, số chưa đọc giảm tương ứng<br>• Người dùng ở đúng đối tượng đích<br>**Thất bại:**<br>• Hiển thị lỗi, danh sách và số chưa đọc đang có được giữ nguyên, trạng thái đọc không đổi |
| Screens | • Chuông và bảng thông báo (bước 1-3)<br>• Trang Thông báo (Alt-01) |
| Notifications | Không có (UC-29 chỉ hiển thị; thông báo do các use case khác phát, xem Danh mục thông báo) |

#### Main Flow

Trigger: Người dùng đã đăng nhập mở một trang của vCraft và thấy chuông thông báo ở đầu trang.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Frontend | Frontend hiển thị chuông ở đầu trang và tải số chưa đọc của tài khoản. | Chuông hiện số chưa đọc; số bằng 0 thì chuông không hiện số. |
| 2 | Người dùng | Người dùng bấm chuông. | Bảng thông báo mở với 10 thông báo mới nhất, mỗi dòng có nhãn khu vực, tiêu đề, đoạn đầu nội dung, thời gian gửi và chấm Chưa đọc; có nút Xem tất cả và Đánh dấu tất cả đã đọc. Mở bảng chưa làm thông báo nào thành Đã đọc. |
| 3 | Người dùng | Người dùng chọn một thông báo. | Yêu cầu mở thông báo được gửi. |
| 4 | Backend | Backend kiểm tra thông báo thuộc tài khoản đang đăng nhập và đánh dấu Đã đọc nếu đang Chưa đọc. | Thông báo Đã đọc, ghi thời điểm đọc. |
| 5 | Frontend | Frontend cập nhật số chưa đọc và chuyển tới đối tượng đích theo Danh mục thông báo; đích thuộc khu vực khác thì chuyển khu vực mà không đăng nhập lại. | Màn hình đích hiển thị đúng hồ sơ, yêu cầu, báo giá, đơn hoặc tin mà thông báo nói tới. |

Outcome: Thông báo Đã đọc, số chưa đọc giảm, người dùng đang ở đúng đối tượng liên quan.

#### Alternative Flows

##### Alt-01 — Xem tất cả thông báo

- At step: 2 · Trigger: Người dùng chọn Xem tất cả trong bảng thông báo.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Frontend | Frontend mở trang Thông báo với tab Tất cả. | Danh sách mới nhất trước, 20 thông báo mỗi lần tải; mỗi dòng đủ nhãn khu vực, tiêu đề, nội dung đầy đủ, thời gian gửi, trạng thái đọc. |
| 2 | Người dùng | Người dùng chọn tab Chưa đọc, hoặc cuộn, hoặc chọn Xem thêm. | Danh sách thu hẹp theo tab hoặc tải thêm 20 thông báo, không tải lại trang. |

Resume: Người dùng chọn một thông báo thì tiếp tục bước 3.

##### Alt-02 — Đánh dấu tất cả đã đọc

- At step: 2 hoặc Alt-01 · Trigger: Người dùng chọn Đánh dấu tất cả đã đọc.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Backend | Backend đánh dấu Đã đọc mọi thông báo Chưa đọc của tài khoản, kể cả những thông báo chưa được tải lên màn hình. | Số chưa đọc về 0. |
| 2 | Frontend | Frontend bỏ chấm Chưa đọc trên danh sách đang hiển thị và ẩn số trên chuông. | Người dùng ở nguyên màn hình hiện tại, không chuyển trang. |

Resume: Kết thúc. Thông báo đến sau đó vẫn là Chưa đọc.

##### Alt-03 — Danh sách trống

- At step: 2 hoặc Alt-01 · Trigger: Tài khoản chưa có thông báo nào, hoặc đang ở tab Chưa đọc mà đã đọc hết.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Frontend | Frontend hiện "Bạn chưa có thông báo nào" nếu chưa có thông báo; hiện "Bạn đã đọc hết thông báo" nếu tab Chưa đọc trống. | Danh sách trống, nút Đánh dấu tất cả đã đọc bị vô hiệu. |

Resume: Kết thúc.

##### Alt-04 — Đối tượng đích không còn khả dụng

- At step: 5 · Trigger: Đối tượng thông báo trỏ tới không xem được nữa, ví dụ tin đã Ẩn (UC-27, Alt-08) hoặc đối tượng không còn thuộc phạm vi của người nhận.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Frontend | Frontend vẫn chuyển tới đích; use case đích hiển thị lỗi của nó (ví dụ ERR-UC28-01 với tin, ERR-UC21-01 với yêu cầu hoặc đơn). | Người dùng biết nội dung không còn khả dụng; thông báo vẫn Đã đọc và vẫn nằm trong danh sách. |

Resume: Kết thúc. Yêu cầu Đã hủy hoặc Hết hạn, đơn Hoàn tất vẫn là đối tượng xem được nên không thuộc nhánh này.

##### Alt-05 — Không đánh dấu được đã đọc

- At step: 4 · Trigger: Việc đánh dấu Đã đọc của một thông báo bị lỗi.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Frontend | Frontend không chặn người dùng, vẫn chuyển tới đối tượng đích và không hiện lỗi. | Người dùng tới đích bình thường; thông báo giữ Chưa đọc, số chưa đọc chưa giảm. |

Resume: Tiếp tục bước 5. Lần bấm hoặc lần làm mới số chưa đọc sau đó đồng bộ lại.

##### Alt-06 — ERR-UC29-01 — Không tải được thông báo

- At step: 1, 2 hoặc Alt-01 · Trigger: Tải số chưa đọc, bảng thông báo, trang Thông báo hoặc trang tiếp theo bị lỗi.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Frontend | Frontend giữ số chưa đọc và danh sách đang có, hiện lỗi và nút Thử lại ngay tại bảng hoặc trang. | Hiển thị ERR-UC29-01; phần còn lại của trang vẫn dùng được. |

Resume: Chọn Thử lại thì lặp bước đang làm.

##### Alt-07 — ERR-UC29-02 — Không đánh dấu được tất cả đã đọc

- At step: Alt-02 bước 1 · Trigger: Việc đánh dấu tất cả đã đọc bị lỗi.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Backend | Backend không đổi trạng thái thông báo nào. | Hiển thị ERR-UC29-02; số chưa đọc và chấm Chưa đọc giữ nguyên. |

Resume: Chọn lại Đánh dấu tất cả đã đọc thì lặp Alt-02.

##### Alt-08 — Số chưa đọc tự làm mới

- At step: Sau bước 1 (hệ thống tự chạy) · Trigger: Trang đang mở và đến chu kỳ làm mới (chu kỳ: G-02).

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Frontend | Frontend tải lại số chưa đọc. | Số trên chuông tăng khi có thông báo mới mà không cần tải lại trang. |

Resume: Kết thúc. Bảng đang mở không tự thêm dòng mới; thông báo mới hiện ở lần mở bảng hoặc tải trang tiếp theo.

##### Alt-09 — ERR-UC29-03 — Thông báo không tồn tại hoặc không có quyền

- At step: 3 hoặc 4 · Trigger: Người dùng mở thẳng đường dẫn của thông báo thuộc tài khoản khác, hoặc thông báo không tồn tại.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Backend | Backend từ chối, dùng cùng một thông điệp cho cả hai trường hợp, không đổi trạng thái đọc của thông báo nào. | Hiển thị ERR-UC29-03; không cho biết thông báo có tồn tại hay không. |

Resume: Kết thúc.

##### Alt-10 — Phiên đăng nhập hết hạn

- At step: Bất kỳ thao tác nào · Trigger: Phiên đã hết hạn do không hoạt động quá lâu.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Backend | Backend đưa người dùng về form đăng nhập và giữ lại thao tác đang làm (UC-04, Alt-08). | Hiển thị ERR-UC04-05. |

Resume: Sau khi đăng nhập quay lại đúng thông báo đã chọn (bước 3).

##### Alt-11 — Guest mở trang Thông báo bằng đường dẫn trực tiếp

- At step: 1 · Trigger: Người dùng chưa đăng nhập mở đường dẫn trang Thông báo. Guest không có chuông.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Frontend | Frontend chuyển sang form đăng nhập (UC-04, Alt-07). | Sau khi đăng nhập thành công, vào trang Thông báo của tài khoản. |

Resume: Tiếp tục bước 1.

#### Data Fields

| Field | Format | Validation | Required | Ghi chú |
|---|---|---|---|---|
| Chuông thông báo | Biểu tượng kèm số | Chỉ hiện khi đã đăng nhập, ở mọi khu vực | — | Guest không có (BR-8) |
| Số chưa đọc | Number, chỉ xem | Số thông báo Chưa đọc của tài khoản; bằng 0 thì ẩn số; từ 100 trở lên hiện "99+" | — | Ví dụ: 3 (BR-5) |
| Danh sách thông báo | Chỉ xem | Chỉ thông báo của tài khoản đang đăng nhập; mới nhất trước theo thời điểm gửi; bảng 10 thông báo, trang Thông báo 20 thông báo mỗi lần tải | — | BR-2, BR-6 |
| Tiêu đề | Text, chỉ xem | Lấy từ mẫu thông báo lúc gửi | — | Ví dụ: "Có báo giá mới" |
| Nội dung | Text, chỉ xem | Đã điền biến lúc gửi (BR-9); bảng rút gọn tối đa 100 ký tự, trang Thông báo hiện đủ | — | Ví dụ: "Xưởng Bình An đã báo giá cho yêu cầu YC-2026-0001: 42.500.000 đồng, hiệu lực đến 2026-12-20." |
| Thời gian gửi | Chỉ xem | Dưới 1 phút: "Vừa xong"; dưới 60 phút: "{n} phút trước"; dưới 24 giờ: "{n} giờ trước"; từ 24 giờ: dd/MM/yyyy HH:mm, giờ Việt Nam | — | |
| Trạng thái đọc | Chỉ xem | Chưa đọc (có chấm nổi bật) hoặc Đã đọc | — | Đổi khi bấm thông báo hoặc Đánh dấu tất cả đã đọc (BR-4) |
| Nhãn khu vực | Chỉ xem | Mua hàng, Bán hàng hoặc Quản trị theo đối tượng đích | — | Giúp Artisan phân biệt thông báo mua và bán (BR-8) |
| Bộ lọc | Chọn một | Tất cả (mặc định) hoặc Chưa đọc | Không | Chỉ ở trang Thông báo |

#### Error Messages

| Code | Condition | Message | Display | Flow |
|---|---|---|---|---|
| ERR-UC29-01 | Không tải được thông báo | "Không tải được thông báo. Vui lòng thử lại." | Inline | Alt-06 |
| ERR-UC29-02 | Không đánh dấu được tất cả đã đọc | "Chưa đánh dấu đã đọc được. Vui lòng thử lại." | Toast | Alt-07 |
| ERR-UC29-03 | Thông báo không tồn tại hoặc không có quyền | "Không tìm thấy thông báo này hoặc bạn không có quyền xem." | Trang lỗi | Alt-09 |

#### Business Rules

| ID | Rule | Source |
|---|---|---|
| BR-1 | UC-29 chỉ hiển thị thông báo và quản lý trạng thái đọc. Sự kiện nào phát thông báo, người nhận và nội dung do use case phát sinh sự kiện khai báo (mã NT-UCNN-xx) và được tổng hợp ở Danh mục thông báo. | Overview 3.7 |
| BR-2 | Mỗi thông báo thuộc đúng một tài khoản nhận; chỉ chủ tài khoản xem, đọc, đánh dấu thông báo của mình, Admin không xem thông báo của người khác. Thông báo gửi cho nhiều người được tạo thành bản riêng, mỗi người đọc riêng. Người nhận được xác định lúc gửi; tài khoản có quyền sau đó không nhận thông báo bù (UC-27, BR-7). | Requirements mục 5; Nhóm đề xuất |
| BR-3 | Bấm thông báo mở đúng đối tượng đích theo Danh mục thông báo và đánh dấu Đã đọc; thông báo đã đọc bấm lại thì chỉ mở đích. Thông báo không có nút thao tác nghiệp vụ: chấp nhận báo giá, xác nhận đã nhận hàng... đều làm ở màn hình đích. Đọc thông báo không làm đổi trạng thái nghiệp vụ. | Requirements mục 4.1; Nhóm đề xuất |
| BR-4 | Thông báo chỉ chuyển Đã đọc khi người dùng bấm vào thông báo hoặc chọn Đánh dấu tất cả đã đọc; mở bảng hay xem danh sách không tự đánh dấu. Không có thao tác đánh dấu chưa đọc lại. | Nhóm đề xuất |
| BR-5 | Số chưa đọc là số thông báo Chưa đọc của tài khoản; bằng 0 thì chuông không hiện số; từ 100 trở lên hiện "99+". | Nhóm đề xuất |
| BR-6 | Danh sách xếp mới nhất trước theo thời điểm gửi; bảng 10 thông báo, trang Thông báo 20 thông báo mỗi lần tải. Mở bảng, đổi tab, xem thêm, đánh dấu đã đọc cập nhật bằng API, không tải lại trang. | Đề bài §1 (tiêu chí 4); Overview mục 8 |
| BR-7 | Số chưa đọc làm mới khi tải trang, khi bấm chuông và định kỳ khi trang đang mở; không có đẩy thời gian thực. Bảng đang mở không tự đổi nội dung. Giao diện nghiệp vụ vẫn là nguồn trạng thái chính khi thông báo đến chậm. | Requirements mục 5; chờ chốt G-02 |
| BR-8 | Chuông có ở Khu mua hàng, Kênh người bán và Trang quản trị cho người đã đăng nhập; Guest không có. Một tài khoản một chuông: Artisan nhận cả thông báo mua hàng và bán hàng, mỗi dòng mang nhãn khu vực. Bấm thông báo thuộc khu vực khác thì chuyển khu vực mà không đăng nhập lại. | Overview mục 1; UC-04 BR-1 (BRD 01); Nhóm đề xuất |
| BR-9 | Nội dung thông báo được chụp lúc gửi: đổi tên xưởng, sửa sản phẩm, đổi trạng thái đối tượng sau đó không làm đổi thông báo cũ, và thông báo đã gửi không bị thu hồi khi đối tượng gốc bị ẩn hoặc đóng. | Nhóm đề xuất; UC-27 BR-14 (BRD 06) |
| BR-10 | Đối tượng đích không còn khả dụng thì màn hình đích báo lỗi theo use case của nó. Thông báo vẫn Đã đọc, vẫn nằm trong danh sách; UC-29 không tự xóa thông báo. Ví dụ: Admin bị Trưởng BQL gỡ nhóm quyền liên quan (UC-34 Alt-03, `09-portal`) rồi bấm lại thông báo cũ — thông báo chuyển Đã đọc như thường, màn đích báo lỗi không có quyền của use case đó (ERR-UC04-06, ERR-UC12-03, ERR-UC27-06 hoặc ERR-UC35-04). | UC-27 Alt-08, UC-28 Alt-05 (BRD 06); UC-34 Alt-03 (`09-portal`); Nhóm đề xuất |
| BR-11 | Thông báo không lộ thông tin ngoài quyền của người nhận. Use case phát chịu trách nhiệm về nội dung: ví dụ NT-UC20-03 không nêu xưởng được chọn hay giá, NT-UC18-01 không nêu họ tên, số điện thoại, địa chỉ chi tiết của Buyer. UC-29 không hiển thị dữ liệu nào ngoài nội dung đã chụp. | UC-21 BR-2, UC-18 BR-8 (BRD 04); Requirements mục 5 |
| BR-12 | Một sự kiện chỉ phát một thông báo cho mỗi người nhận, kể cả khi thao tác gửi lặp hoặc đồng thời. Không gửi cho chính người thao tác. Không thông báo khi lưu nháp, chỉ xem hoặc thao tác thất bại. | Requirements mục 5; UC-27 BR-7 (BRD 06) |
| BR-13 | Lỗi tạo hoặc chuyển thông báo không làm mất hoặc hoàn tác kết quả nghiệp vụ; người dùng vẫn xem được kết quả ở màn hình nghiệp vụ. | Requirements mục 5 |
| BR-14 | Mọi thao tác đều kiểm tra quyền, không chỉ ẩn nút hoặc menu: mở thẳng đường dẫn thông báo của người khác bị từ chối. | Đề bài §1 (An ninh); Overview mục 8 |
| BR-15 | MVP1 chỉ có thông báo trong hệ thống (in-app), không có email, SMS, push (EN-05). Người dùng không xóa được thông báo; thời hạn lưu xem G-03. | Overview mục 6 (EN-05); Nhóm đề xuất |

#### Acceptance Criteria

| ID | Given | When | Then | Covers |
|---|---|---|---|---|
| UC-29-AC-01 | Buyer đã đăng nhập, có 3 thông báo Chưa đọc, trong đó NT-UC19-01 của yêu cầu YC-2026-0001 | Mở một trang bất kỳ, bấm chuông, chọn thông báo NT-UC19-01 | Chuông hiện số 3; bảng xếp mới nhất trước; sau khi chọn, thông báo Đã đọc, số còn 2 và mở So sánh báo giá của đúng yêu cầu YC-2026-0001 | Main Flow |
| UC-29-AC-02 | Tài khoản có 25 thông báo, trong đó 8 Chưa đọc | Chọn Xem tất cả, chuyển sang tab Chưa đọc, rồi quay lại tab Tất cả và chọn Xem thêm | Trang Thông báo hiện 20 thông báo đầu; tab Chưa đọc chỉ có 8 thông báo chưa đọc; Xem thêm tải 5 thông báo còn lại; trang không tải lại | Alt-01, BR-6 |
| UC-29-AC-03 | Tài khoản có 40 thông báo Chưa đọc, màn hình mới tải 20 | Chọn Đánh dấu tất cả đã đọc | Cả 40 thông báo Đã đọc kể cả 20 thông báo chưa tải; số chuông về 0 và ẩn; không chuyển trang; thông báo đến sau đó vẫn Chưa đọc | Alt-02 |
| UC-29-AC-04 | Tài khoản chưa có thông báo nào; tài khoản khác đã đọc hết | Mở bảng; mở tab Chưa đọc | Hiện "Bạn chưa có thông báo nào"; hiện "Bạn đã đọc hết thông báo"; nút Đánh dấu tất cả đã đọc bị vô hiệu | Alt-03 |
| UC-29-AC-05 | Artisan có NT-UC27-01 chưa đọc; Admin sau đó Ẩn tin | Artisan bấm thông báo | Thông báo Đã đọc; hiển thị ERR-UC28-01; thông báo vẫn còn trong danh sách với nội dung cũ | Alt-04, BR-9, BR-10 |
| UC-29-AC-06 | Xưởng có NT-UC18-01 cũ của yêu cầu đã bị Buyer hủy | Xưởng bấm thông báo | Mở chi tiết yêu cầu nhận ở trạng thái Đã hủy, chỉ xem; không hiện lỗi; thông báo Đã đọc | Alt-04, BR-10 |
| UC-29-AC-07 | Việc đánh dấu Đã đọc của một thông báo bị lỗi | Người dùng bấm thông báo đó | Vẫn mở đúng đối tượng đích, không hiện lỗi chặn; thông báo giữ Chưa đọc; lần bấm sau đánh dấu được | Alt-05 |
| UC-29-AC-08 | API thông báo lỗi | Người dùng bấm chuông | Hiển thị ERR-UC29-01 trong bảng kèm nút Thử lại; số chưa đọc đang có giữ nguyên; chọn Thử lại thì tải lại | Alt-06 |
| UC-29-AC-09 | Có thông báo Chưa đọc; việc đánh dấu tất cả lỗi | Chọn Đánh dấu tất cả đã đọc | Hiển thị ERR-UC29-02; số chưa đọc và chấm Chưa đọc giữ nguyên | Alt-07 |
| UC-29-AC-10 | Buyer đang mở trang và bảng thông báo; một xưởng vừa gửi báo giá | Đến chu kỳ làm mới | Số trên chuông tăng mà trang không tải lại; bảng đang mở không tự thêm dòng; đóng rồi mở lại bảng thì thông báo mới đứng đầu | Alt-08, BR-7 |
| UC-29-AC-11 | Buyer B; thông báo của Buyer A; mã thông báo không tồn tại | Buyer B mở thẳng đường dẫn thông báo của A, rồi mở mã không tồn tại | Cả hai trường hợp hiển thị ERR-UC29-03; thông báo của A vẫn Chưa đọc | Alt-09, BR-14 |
| UC-29-AC-12 | Phiên đã hết hạn khi người dùng đang xem bảng thông báo | Bấm một thông báo | Hiển thị ERR-UC04-05; đăng nhập lại xong, thông báo đã chọn được mở | Alt-10 |
| UC-29-AC-13 | Guest chưa đăng nhập | Mở đầu trang; mở đường dẫn trang Thông báo | Không có chuông; được chuyển sang đăng nhập, đăng nhập xong vào trang Thông báo | Alt-11, BR-8 |
| UC-29-AC-14 | Có hai Admin A, B cùng có quyền Kiểm duyệt & Danh mục, và một Artisan; Buyer gửi hồ sơ bán hàng | Admin A mở NT-UC01-01 và đọc | Thông báo của A Đã đọc, số của A giảm; thông báo của B vẫn Chưa đọc, số của B không đổi; Artisan không có thông báo này | BR-2 |
| UC-29-AC-15 | Buyer có NT-UC20-01 (báo giá sắp hết hiệu lực) và NT-UC22-02 (đơn đã giao) | Bấm từng thông báo | Mở So sánh báo giá; mở chi tiết đơn; báo giá chưa được chấp nhận và đơn chưa Hoàn tất cho tới khi Buyer tự thao tác ở màn hình đích; bấm lại thông báo đã đọc chỉ mở đích | BR-3 |
| UC-29-AC-16 | Tài khoản có 5 thông báo Chưa đọc | Mở bảng, xem, đóng bảng mà không bấm thông báo nào | Vẫn 5 thông báo Chưa đọc; không có thao tác đánh dấu chưa đọc lại | BR-4 |
| UC-29-AC-17 | Tài khoản lần lượt có 0, 7 và 150 thông báo Chưa đọc | Xem chuông | Không hiện số; hiện "7"; hiện "99+" | BR-5 |
| UC-29-AC-18 | Tài khoản có 12 thông báo | Mở bảng; rồi chuyển tab Tất cả và Chưa đọc ở trang Thông báo | Bảng hiện 10 thông báo mới nhất xếp từ mới tới cũ; đổi tab và xem thêm không tải lại trang | BR-6 |
| UC-29-AC-19 | Artisan có thông báo bán hàng NT-UC18-01 và mua hàng NT-UC19-01; Admin có thông báo quản trị | Artisan đang ở Kênh người bán mở chuông và bấm NT-UC19-01; Admin mở chuông | Artisan thấy cả hai thông báo với nhãn Bán hàng và Mua hàng; bấm NT-UC19-01 chuyển sang Khu mua hàng mở So sánh báo giá mà không bị hỏi đăng nhập lại; Admin chỉ thấy thông báo nhãn Quản trị | BR-8 |
| UC-29-AC-20 | Buyer nhận NT-UC19-01 nêu "Xưởng Bình An"; sau đó xưởng đổi tên và được Admin duyệt | Buyer mở lại thông báo | Thông báo vẫn nêu "Xưởng Bình An" như lúc gửi | BR-9 |
| UC-29-AC-21 | Yêu cầu giao tận nơi gửi cho 3 xưởng, Buyer chấp nhận xưởng A | Xưởng B mở NT-UC20-03; xưởng A mở NT-UC18-01 cũ | NT-UC20-03 chỉ nêu yêu cầu đã chốt và báo giá đã đóng, không có tên xưởng A, giá, mã đơn; NT-UC18-01 không có họ tên, số điện thoại, địa chỉ chi tiết của Buyer | BR-11 |
| UC-29-AC-22 | Yêu cầu gửi chung cho 5 xưởng; Buyer lưu nháp một yêu cầu khác; Buyer đồng thời gửi lặp | Gửi yêu cầu rồi kiểm tra thông báo | Mỗi xưởng nhận đúng một NT-UC18-01; Buyer không nhận thông báo vì chính thao tác của mình; lưu nháp không tạo thông báo | BR-12 |
| UC-29-AC-23 | Việc tạo thông báo gặp lỗi khi Buyer chấp nhận báo giá | Buyer chấp nhận báo giá | Đơn đặt riêng vẫn được tạo; xưởng vẫn thấy đơn trong danh sách đơn (UC-21) dù chưa có thông báo | BR-13 |
| UC-29-AC-24 | Sau một sự kiện có thông báo | Kiểm tra các kênh và thao tác khả dụng | Chỉ có thông báo trong hệ thống, không có email, SMS, push; không có thao tác xóa thông báo | BR-15 |
| UC-29-AC-25 | Mỗi mẫu thông báo trong Danh mục thông báo | Người nhận bấm thông báo thuộc mẫu đó | Mở đúng đối tượng đích và khu vực nêu trong danh mục | Main Flow, BR-1, BR-3 |
| UC-29-AC-26 | Artisan vừa là chủ xưởng vừa mua hàng của xưởng khác; đơn MH-2026-0001 của xưởng nhận NT-UC31-01, đơn MH-2026-0002 do chính Artisan mua nhận NT-UC32-01 | Artisan mở chuông và bấm lần lượt hai thông báo | NT-UC31-01 nhãn Bán hàng, mở Đơn bán hàng ở Kênh người bán; NT-UC32-01 nhãn Mua hàng, mở Đơn mua của tôi ở Khu mua hàng; không bị hỏi đăng nhập lại | BR-8 |
| UC-29-AC-27 | Đơn đặt riêng DR-2026-0001 đang Chờ sản xuất; đơn mua MH-2026-0003 đã thanh toán online | Buyer hủy đơn DR-2026-0001; xưởng hủy đơn MH-2026-0003 | NT-UC33-07 chỉ gửi cho xưởng (bên còn lại), không gửi cho Buyer là người hủy; NT-UC33-02 gửi cho Buyer; mỗi người nhận đúng một bản và mở đúng đơn phía của mình | BR-12 |

### Danh mục thông báo

Danh mục tổng hợp các mẫu thông báo đã khai báo trong các BRD; tiêu đề, nội dung và bước phát nằm ở use case phát. Danh mục này quy định đối tượng đích khi người dùng bấm thông báo (Main Flow bước 5). Khi một BRD thêm hoặc đổi mẫu thông báo thì cập nhật danh mục này.

| Template ID | Sự kiện | Người nhận | Đích mở khi bấm | Khu vực | Use case phát |
|---|---|---|---|---|---|
| NT-UC01-01 | Có hồ sơ bán hàng mới | Trưởng BQL và Admin có quyền Kiểm duyệt & Danh mục | Chi tiết hồ sơ bán hàng (UC-02) | Quản trị | UC-01 |
| NT-UC01-02 | Hồ sơ bán hàng được gửi lại | Trưởng BQL và Admin có quyền Kiểm duyệt & Danh mục | Chi tiết hồ sơ bán hàng, nhãn "Gửi lại" (UC-02) | Quản trị | UC-01 |
| NT-UC02-01 | Hồ sơ được duyệt | Người nộp hồ sơ | Trang Trạng thái hồ sơ (UC-01) | Mua hàng | UC-02 |
| NT-UC02-02 | Hồ sơ cần chỉnh sửa | Người nộp hồ sơ | Trang Trạng thái hồ sơ kèm lý do và nút Chỉnh sửa và gửi lại (UC-01, Alt-07) | Mua hàng | UC-02 |
| NT-UC02-03 | Hồ sơ bị từ chối | Người nộp hồ sơ | Trang Trạng thái hồ sơ kèm lý do (UC-01) | Mua hàng | UC-02 |
| NT-UC02-04 | Yêu cầu đổi tên, địa chỉ xưởng được duyệt | Artisan gửi yêu cầu | Thông tin gian hàng (UC-07) | Bán hàng | UC-02 |
| NT-UC02-05 | Yêu cầu đổi tên, địa chỉ xưởng bị từ chối | Artisan gửi yêu cầu | Thông tin gian hàng (UC-07) | Bán hàng | UC-02 |
| NT-UC07-01 | Xưởng xin đổi tên hoặc địa chỉ | Trưởng BQL và Admin có quyền Kiểm duyệt & Danh mục | Chi tiết yêu cầu "Đổi thông tin xưởng" (UC-02, Alt-05) | Quản trị | UC-07 |
| NT-UC18-01 | Có yêu cầu báo giá mới | Từng xưởng nhận | Chi tiết yêu cầu nhận (UC-19) | Bán hàng | UC-18 |
| NT-UC18-02 | Nhắc yêu cầu sắp đóng nhận báo giá | Xưởng nhận chưa phản hồi | Chi tiết yêu cầu nhận (UC-19) | Bán hàng | UC-18 |
| NT-UC18-03 | Yêu cầu đã đóng nhận báo giá, chờ Buyer lựa chọn | Buyer sở hữu yêu cầu | So sánh báo giá (UC-20) | Mua hàng | UC-18 |
| NT-UC18-04 | Yêu cầu hết hạn | Buyer sở hữu yêu cầu | Chi tiết yêu cầu kèm lý do hết hạn (UC-21) | Mua hàng | UC-18 |
| NT-UC18-05 | Yêu cầu được gia hạn | Xưởng nhận | Chi tiết yêu cầu nhận (UC-19) | Bán hàng | UC-18 |
| NT-UC19-01 | Có báo giá mới | Buyer sở hữu yêu cầu | So sánh báo giá của yêu cầu (UC-20) | Mua hàng | UC-19 |
| NT-UC19-02 | Báo giá được cập nhật | Buyer sở hữu yêu cầu | So sánh báo giá của yêu cầu (UC-20) | Mua hàng | UC-19 |
| NT-UC19-03 | Xưởng không nhận, hoặc tất cả xưởng không nhận | Buyer sở hữu yêu cầu | Chi tiết yêu cầu (UC-21) | Mua hàng | UC-19 |
| NT-UC19-04 | Xưởng đã cập nhật giá | Buyer sở hữu yêu cầu | So sánh báo giá của yêu cầu (UC-20) | Mua hàng | UC-19 |
| NT-UC19-05 | Xưởng không cập nhật giá | Buyer sở hữu yêu cầu | Chi tiết yêu cầu, có nút Tạo yêu cầu mới (UC-21, UC-18 Alt-15) | Mua hàng | UC-19 |
| NT-UC20-01 | Báo giá sắp hết hiệu lực | Buyer sở hữu yêu cầu | So sánh báo giá của yêu cầu (UC-20) | Mua hàng | UC-20 |
| NT-UC20-02 | Báo giá được chấp nhận | Xưởng được chọn | Chi tiết đơn đặt riêng (UC-21, UC-22) | Bán hàng | UC-20 |
| NT-UC20-03 | Yêu cầu đã chốt xưởng khác | Các xưởng còn lại đã nhận yêu cầu | Chi tiết yêu cầu nhận, báo giá "Đã đóng" (UC-21) | Bán hàng | UC-20 |
| NT-UC20-04 | Buyer yêu cầu cập nhật giá | Xưởng có báo giá hết hiệu lực | Chi tiết yêu cầu nhận, nhãn "Cần cập nhật giá" (UC-19, Alt-07) | Bán hàng | UC-20 |
| NT-UC20-05 | Đã nhận tiền cọc | Xưởng được chọn | Chi tiết đơn đặt riêng (UC-22) | Bán hàng | UC-20 |
| NT-UC22-01 | Đơn đặt riêng đang được giao | Buyer | Chi tiết đơn đặt riêng (UC-22) | Mua hàng | UC-22 |
| NT-UC22-02 | Đơn đặt riêng đã giao | Buyer | Chi tiết đơn đặt riêng, nút Xác nhận đã nhận (UC-22) | Mua hàng | UC-22 |
| NT-UC22-03 | Buyer đã nhận hàng | Xưởng của đơn | Chi tiết đơn đặt riêng (UC-22) | Bán hàng | UC-22 |
| NT-UC22-04 | Đơn tự động hoàn tất | Buyer và xưởng của đơn, mỗi bên một thông báo | Chi tiết đơn đặt riêng phía của mình (UC-22) | Mua hàng với Buyer; Bán hàng với xưởng | UC-22 |
| NT-UC23-01 | Yêu cầu bị Buyer hủy | Xưởng đã báo giá | Chi tiết yêu cầu nhận ở trạng thái Đã hủy (UC-21) | Bán hàng | UC-23 |
| NT-UC27-01 | Có tin mới từ BQL | Mọi Artisan | Chi tiết tin trong Kênh người bán (UC-28, Alt-02) | Bán hàng | UC-27 |
| NT-UC31-01 | Có đơn mua hàng mới: COD vừa đặt hoặc đơn online vừa thanh toán thành công | Xưởng của đơn | Chi tiết đơn ở Đơn bán hàng, đơn Chờ xác nhận (UC-32) | Bán hàng | UC-31 |
| NT-UC31-02 | Thanh toán online thành công | Buyer đặt đơn | Chi tiết đơn ở Đơn mua của tôi (UC-32) | Mua hàng | UC-31 |
| NT-UC31-03 | Đơn tự hủy do quá hạn thanh toán 15 phút | Buyer đặt đơn | Chi tiết đơn Đã hủy, nút Mua lại (UC-32, Alt-05) | Mua hàng | UC-31 |
| NT-UC32-01 | Xưởng xác nhận đơn | Buyer đặt đơn | Chi tiết đơn ở Đơn mua của tôi (UC-32) | Mua hàng | UC-32 |
| NT-UC32-02 | Xưởng từ chối đơn | Buyer đặt đơn | Chi tiết đơn Bị từ chối kèm lý do, nút Mua lại (UC-32, Alt-05) | Mua hàng | UC-32 |
| NT-UC32-03 | Đơn đang được giao | Buyer đặt đơn | Chi tiết đơn ở Đơn mua của tôi (UC-32) | Mua hàng | UC-32 |
| NT-UC32-04 | Đơn đã giao | Buyer đặt đơn | Chi tiết đơn, nút Đã nhận hàng (UC-32) | Mua hàng | UC-32 |
| NT-UC32-05 | Đơn tự hoàn tất sau 7 ngày | Buyer đặt đơn | Chi tiết đơn Hoàn tất (UC-32) | Mua hàng | UC-32 |
| NT-UC32-06 | Đơn tự hủy do xưởng không xác nhận trong 3 ngày | Buyer đặt đơn | Chi tiết đơn Đã hủy, nút Mua lại (UC-32, Alt-05) | Mua hàng | UC-32 |
| NT-UC32-07 | Đơn tự hủy do xưởng không xác nhận trong 3 ngày | Xưởng của đơn | Chi tiết đơn Đã hủy ở Đơn bán hàng (UC-32) | Bán hàng | UC-32 |
| NT-UC32-08 | Đơn Hoàn tất (Buyer xác nhận hoặc tự hoàn tất) | Xưởng của đơn | Chi tiết đơn Hoàn tất ở Đơn bán hàng (UC-32) | Bán hàng | UC-32 |
| NT-UC33-01 | Buyer hủy đơn mua hàng Chờ xác nhận | Xưởng của đơn | Chi tiết đơn Đã hủy ở Đơn bán hàng (UC-32) | Bán hàng | UC-33 |
| NT-UC33-02 | Xưởng hủy đơn mua hàng Đang chuẩn bị | Buyer đặt đơn | Chi tiết đơn Đã hủy kèm lý do, nút Mua lại (UC-32, Alt-05) | Mua hàng | UC-33 |
| NT-UC33-03 | Buyer gửi yêu cầu hủy đơn đặt riêng Đang sản xuất | Xưởng của đơn | Chi tiết đơn đặt riêng, yêu cầu hủy kèm nút Đồng ý, Từ chối (UC-33, Alt-04) | Bán hàng | UC-33 |
| NT-UC33-04 | Buyer rút yêu cầu hủy | Xưởng của đơn | Chi tiết đơn đặt riêng (UC-22) | Bán hàng | UC-33 |
| NT-UC33-05 | Xưởng đồng ý yêu cầu hủy | Buyer của đơn | Chi tiết đơn đặt riêng Đã hủy, ghi cách xử lý cọc (UC-22) | Mua hàng | UC-33 |
| NT-UC33-06 | Xưởng từ chối yêu cầu hủy | Buyer của đơn | Chi tiết đơn đặt riêng Đang sản xuất kèm lý do (UC-22) | Mua hàng | UC-33 |
| NT-UC33-07 | Đơn đặt riêng bị hủy trực tiếp | Bên còn lại: Buyer nếu xưởng hủy, xưởng nếu Buyer hủy | Chi tiết đơn đặt riêng Đã hủy (UC-22) phía của mình | Mua hàng với Buyer; Bán hàng với xưởng | UC-33 |
| NT-UC33-08 | Hoàn tiền thành công | Buyer của đơn | Chi tiết đơn mua hàng (UC-32) hoặc đơn đặt riêng (UC-22), trạng thái thanh toán Đã hoàn tiền | Mua hàng | UC-33 |
| NT-UC33-09 | Hoàn tiền thành công, xưởng đã được báo về đơn | Xưởng của đơn | Chi tiết đơn mua hàng (UC-32) hoặc đơn đặt riêng (UC-22) ở phía xưởng | Bán hàng | UC-33 |

## IV. Open Questions

| ID | Question | Affects | Decision |
|---|---|---|---|
| G-01 | Mẫu thông báo của nhóm `08-order` (đơn mua hàng, thanh toán, hoàn tiền, hủy đơn và yêu cầu hủy). | Danh mục thông báo; UC-29 AC-25, AC-26 | Đã chốt 2026-11-18: BRD `08-order` đã khai báo NT-UC31-01 đến NT-UC31-03, NT-UC32-01 đến NT-UC32-08, NT-UC33-01 đến NT-UC33-09; đã đưa vào Danh mục thông báo |
| G-02 | Chu kỳ làm mới số chưa đọc khi trang đang mở. Đang theo hướng: khoảng 60 giây, không đẩy thời gian thực, vì thông báo chỉ hỗ trợ, trạng thái chính nằm ở màn hình nghiệp vụ. | UC-29 Alt-08, BR-7 | Open |
| G-03 | Thời hạn lưu thông báo và có cho người dùng xóa không. Đang theo hướng: MVP1 không cho xóa và không tự xóa; danh sách đã phân trang nên chưa ảnh hưởng hiệu năng. | UC-29 BR-15; phạm vi | Open |
| G-04 | Đơn đặt riêng chuyển Đang sản xuất hiện không có thông báo riêng (BRD `04-quotation`, UC-22 bước 3), trong khi overview ghi sự kiện chung "Đơn đặt riêng đổi trạng thái". Đang theo hướng: giữ như BRD `04-quotation`; Buyer thấy trạng thái mới ở chi tiết đơn. | Danh mục thông báo; UC-22 (BRD 04) | Open |
| G-05 | Artisan có cả thông báo mua và bán trong cùng một chuông. Đang theo hướng: một chuông, mỗi dòng có nhãn khu vực, bấm thông báo khu vực khác thì chuyển khu vực không đăng nhập lại; chưa tách hai chuông. | UC-29 BR-8, Data Fields | Open |
