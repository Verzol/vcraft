---
title: "04 — Đặt hàng riêng"
group: 04-quotation
type: brd
status: draft
version: 0.1.0
date: 2026-11-18
author: Khánh Chi
---

# 04 — Đặt hàng riêng

Change history: [CHANGELOG.md](CHANGELOG.md)

## I. Overview

Nhóm chức năng cho nhu cầu đặt làm riêng theo mẫu, kích thước, số lượng mà sản phẩm có sẵn không đáp ứng. Buyer gửi yêu cầu báo giá riêng cho một xưởng hoặc chung cho nhiều xưởng; xưởng nhận phản hồi bằng báo giá hoặc từ chối; Buyer so sánh, chấp nhận một báo giá (kèm đặt cọc nếu có) để tạo đơn đặt riêng, rồi theo dõi xưởng sản xuất và giao hàng. Mỗi yêu cầu có hạn nhận báo giá hoặc hạn phản hồi, mỗi báo giá có hiệu lực riêng, và hệ thống nhắc cả hai phía trước hạn. Báo giá là kín: Buyer thấy mọi báo giá, xưởng chỉ thấy báo giá của mình. Mỗi đơn do đúng một xưởng làm toàn bộ số lượng; BQL (Admin) không chia đơn và không xem giao dịch.

### Terms

| Term | Meaning |
|---|---|
| Yêu cầu báo giá | Nhu cầu đặt riêng của Buyer (mô tả mẫu, số lượng, hình thức nhận hàng, hạn), chưa phải đơn. Có mã dạng YC-2026-0001. |
| Yêu cầu gửi riêng | Yêu cầu gửi cho đúng một xưởng: từ trang sản phẩm (kèm sản phẩm mẫu) hoặc từ trang gian hàng (không gắn sản phẩm). |
| Yêu cầu gửi chung | Yêu cầu gửi cho nhiều xưởng theo một phân loại, từ mục Đặt hàng theo yêu cầu; không gắn sản phẩm. |
| Sản phẩm mẫu | Sản phẩm Buyer đang xem khi gửi yêu cầu từ trang sản phẩm. Hệ thống chụp tên và ảnh lúc gửi; sản phẩm sửa hoặc ẩn sau đó không đổi yêu cầu. |
| Hạn nhận báo giá | Ngày hạn chót để xưởng gửi hoặc cập nhật báo giá cho yêu cầu gửi chung; Buyer bắt buộc chọn ngày, hạn hết lúc 23:59 ngày đó. |
| Hạn phản hồi | Ngày hạn chót để xưởng phản hồi yêu cầu gửi riêng, hết lúc 23:59; Buyer không chọn thì hệ thống đặt 3 ngày kể từ ngày gửi. Khi không cần phân biệt, hai loại hạn này gọi chung là hạn của yêu cầu. Khác với hiệu lực báo giá. |
| Đóng nhận báo giá | Khi hết ngày hạn của yêu cầu, xưởng không gửi hay cập nhật báo giá nữa (trừ khi Buyer Yêu cầu cập nhật giá). Buyer vẫn so sánh và chọn trong thời gian báo giá còn hiệu lực. Chỉ là nhãn hiển thị "Chờ Buyer lựa chọn" trên trạng thái Đang nhận báo giá, không phải trạng thái riêng. |
| Báo giá | Đề nghị của xưởng cho toàn bộ số lượng: đơn giá, phí giao, thời gian hoàn thành, tỷ lệ cọc, hiệu lực. Mỗi xưởng một báo giá cho mỗi yêu cầu. |
| Phiên bản báo giá | Mỗi lần xưởng cập nhật báo giá tạo phiên bản mới thay phiên bản cũ; Buyer chấp nhận đúng phiên bản đang xem. |
| Hiệu lực báo giá | Ngày xưởng cam kết giữ giá, đến hết 23:59 ngày đó. Quá ngày thì báo giá Hết hiệu lực và không chấp nhận được. |
| Trạng thái báo giá | Còn hiệu lực, Hết hiệu lực, Đã đóng (yêu cầu đã chốt xưởng khác hoặc bị hủy), Được chấp nhận. |
| Phản hồi của xưởng | Chưa phản hồi, Đã báo giá, Không nhận (kèm lý do). Độc lập với trạng thái yêu cầu. |
| Báo giá kín | Buyer thấy mọi báo giá của yêu cầu; xưởng chỉ thấy báo giá của mình; xưởng không được chọn chỉ thấy "Đã đóng". |
| Tỷ lệ cọc | 0%, 30% hoặc 50% tổng giá trị báo giá, do xưởng chọn. Cọc trả online lúc chấp nhận; phần còn lại thu khi giao hoặc nhận hàng. |
| Yêu cầu cập nhật giá | Buyer nhờ xưởng xác nhận lại giá khi báo giá đã Hết hiệu lực mà vẫn muốn mua. |
| Trạng thái yêu cầu | Nháp, Đã gửi, Đang nhận báo giá, Đã chấp nhận, Đã hủy, Hết hạn. Khác tên trạng thái đơn đặt riêng. |
| Đơn đặt riêng | Đơn tạo khi Buyer chấp nhận một báo giá; chụp lại yêu cầu và báo giá lúc chấp nhận. Có mã dạng DR-2026-0001. |
| Trạng thái đơn đặt riêng | Chờ sản xuất, Đang sản xuất, Đang giao, Đã giao, Hoàn tất, Đã hủy. |

## II. Scope

| Business topic | Channel | Use case |
|---|---|---|
| Soạn, lưu nháp, xóa nháp, gửi yêu cầu báo giá (ba lối vào, gửi riêng hoặc gửi chung), đặt hạn, nhắc hạn, gia hạn, tạo nháp mới từ yêu cầu đã đóng | Khu mua hàng | UC-18 |
| Xưởng tiếp nhận yêu cầu, gửi hoặc cập nhật báo giá, hoặc không nhận kèm lý do | Kênh người bán | UC-19 |
| Buyer so sánh báo giá, chấp nhận một báo giá, đặt cọc, tạo đơn đặt riêng, yêu cầu cập nhật giá | Khu mua hàng | UC-20 |
| Xem danh sách và chi tiết yêu cầu, báo giá, đơn đặt riêng | Khu mua hàng, Kênh người bán | UC-21 |
| Xưởng cập nhật trạng thái sản xuất, giao hàng; Buyer theo dõi và xác nhận đã nhận | Kênh người bán, Khu mua hàng | UC-22 |
| Buyer hủy yêu cầu chưa chấp nhận | Khu mua hàng | UC-23 |
| Buyer xem nhật ký sản xuất của đơn đặt riêng | Khu mua hàng | UC-24 |

**Ngoài phạm vi:** Chia đơn cho nhiều xưởng, BQL chọn xưởng hoặc điều phối đơn; báo giá một phần số lượng; sửa yêu cầu sau khi gửi (hủy rồi tạo lại, trừ gia hạn); xưởng rút báo giá; phụ phí (làm gấp, khắc chữ, mức Should chưa mô tả); trao đổi tin nhắn theo yêu cầu (EN-04); yêu cầu có cấu trúc kích thước, hoa văn, vật liệu (EN-07); thông báo qua email (EN-05); thanh toán phần còn lại online và đối soát tiền cho xưởng (EN-15); giao từng phần, đổi trả, tranh chấp (EN-17); hủy đơn đặt riêng và hoàn cọc (UC-33, nhóm `08-order`); giới hạn số xưởng nhận mỗi yêu cầu gửi chung (KI-01); Admin xem hoặc can thiệp yêu cầu, báo giá, đơn.

**Liên quan**

| ID | Tài liệu | Quan hệ |
|---|---|---|
| BRD-02 | [02-showcase](../02-showcase/usecase.md) | UC-11 chuyển sang UC-18 kèm sản phẩm mẫu (hết hàng vẫn gửi được); UC-08 chuyển kèm xưởng, không kèm sản phẩm; UC-06 có lối vào Đặt hàng theo yêu cầu (gửi chung); UC-12 cung cấp phân loại để chọn xưởng nhận; UC-13 gian hàng Tạm ngừng vô hiệu nút gửi nhưng vẫn chấp nhận báo giá đã gửi (BR-4); UC-09 sửa hoặc ẩn sản phẩm không đổi yêu cầu đã gửi (BR-4). |
| BRD-03 | [03-traceability](../03-traceability/usecase.md) | UC-14 tạo lô cho đơn đặt riêng chưa Đã hủy hoặc Hoàn tất, một đơn nhiều lô; UC-17 trang nguồn gốc của lô đặt riêng không lộ Buyer, đơn, sản phẩm mẫu; UC-24 là nơi Buyer xem nhật ký lô của đơn. |
| BRD-07 | `07-notification` | UC-29 hiển thị các thông báo NT-UC18-xx đến NT-UC23-xx. |
| BRD-08 | `08-order` | UC-33 hủy đơn đặt riêng và hoàn cọc; cổng thanh toán cọc dùng chung cấu hình với đơn mua (ZaloPay sandbox, dự phòng VNPay sandbox). |

## III. Use Cases

| Use case ID | Use case | Description |
|---|---|---|
| UC-18 | Tạo, lưu nháp, xóa nháp và gửi yêu cầu báo giá | Buyer soạn yêu cầu từ ba lối vào, đặt hạn, gửi riêng một xưởng hoặc gửi chung nhiều xưởng; hệ thống theo dõi hạn, nhắc xưởng, cho gia hạn hoặc tạo yêu cầu mới từ yêu cầu đã đóng |
| UC-19 | Tiếp nhận yêu cầu, gửi báo giá hoặc không nhận | Xưởng nhận yêu cầu, gửi hoặc cập nhật báo giá (có hiệu lực), hoặc không nhận kèm lý do; xử lý Yêu cầu cập nhật giá |
| UC-20 | So sánh và chấp nhận báo giá | Buyer so sánh báo giá, chấp nhận một báo giá còn hiệu lực, đặt cọc nếu có, tạo đơn đặt riêng; yêu cầu cập nhật giá khi báo giá hết hiệu lực |
| UC-21 | Xem yêu cầu, báo giá và đơn đặt riêng | Buyer và xưởng xem danh sách, chi tiết yêu cầu, báo giá, đơn đặt riêng thuộc phạm vi của mình |
| UC-22 | Cập nhật và theo dõi đơn đặt riêng | Xưởng cập nhật trạng thái sản xuất, giao hàng; Buyer theo dõi và xác nhận đã nhận để đơn Hoàn tất |
| UC-23 | Hủy yêu cầu báo giá trước khi chấp nhận | Buyer hủy yêu cầu chưa chấp nhận; xưởng đã báo giá được thông báo (mức Should) |
| UC-24 | Xem nhật ký sản xuất của đơn đặt riêng | Buyer xem các lô và nhật ký sản xuất của đơn mình (mức Should) |

### UC-18 — Tạo, lưu nháp, xóa nháp và gửi yêu cầu báo giá

#### Detail

| Field | Content |
|---|---|
| Use case ID | UC-18 |
| Description | Buyer soạn yêu cầu báo giá cho nhu cầu đặt riêng (mẫu, số lượng, hình thức nhận hàng, hạn), lưu nháp hoặc gửi. Yêu cầu gửi riêng một xưởng (từ trang sản phẩm hoặc trang gian hàng) hoặc gửi chung nhiều xưởng theo phân loại (từ Đặt hàng theo yêu cầu). Hệ thống chốt danh sách xưởng nhận, thông báo xưởng, theo dõi hạn, nhắc xưởng trước hạn; Buyer gia hạn hoặc tạo yêu cầu mới từ yêu cầu đã đóng. |
| Actors | Buyer (Guest phải đăng nhập trước, xem Alt-01) |
| Channel | Khu mua hàng |
| Preconditions | • Đã đăng nhập bằng tài khoản Buyer (Artisan thao tác như Buyer, không gửi cho xưởng của chính mình)<br>• Khi Gửi yêu cầu: có ít nhất một xưởng đủ điều kiện nhận (Alt-07, Alt-09). Soạn và lưu nháp không cần điều kiện này |
| Expected | **Thành công:**<br>• Yêu cầu ở trạng thái Đã gửi, có mã yêu cầu, hạn và danh sách xưởng nhận đã chốt; từng xưởng nhận NT-UC18-01<br>• Hoặc yêu cầu được lưu ở trạng thái Nháp<br>**Thất bại:**<br>• Hiển thị lỗi, yêu cầu không được gửi, dữ liệu đã nhập được giữ |
| Screens | • Form yêu cầu báo giá (bước 1-6)<br>• Danh sách xưởng nhận (bước 2)<br>• Chi tiết yêu cầu (bước 9, UC-21) |
| Notifications | NT-UC18-01 đến NT-UC18-05 |

#### Main Flow

Trigger: Buyer chọn Gửi yêu cầu báo giá ở trang sản phẩm (UC-11) hoặc trang gian hàng (UC-08), hoặc chọn Đặt hàng theo yêu cầu ở trang chủ (UC-06).

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Buyer | Buyer chọn một trong ba lối vào. | Form hiển thị, điền sẵn theo lối vào: từ sản phẩm thì có tên, ảnh sản phẩm mẫu và xưởng nhận; từ gian hàng thì có tên xưởng nhận; từ Đặt hàng theo yêu cầu thì để trống. Nếu xưởng đích đang không nhận được yêu cầu thì xử lý ngay theo Alt-07; nếu không có xưởng nào đủ điều kiện nhận thì xử lý ngay theo Alt-09. |
| 2 | Buyer | Với yêu cầu gửi chung, Buyer chọn một phân loại. | Frontend hiện danh sách tên các xưởng sẽ nhận (xưởng đang hoạt động có sản phẩm Hiển thị thuộc phân loại, trừ xưởng của Buyer). |
| 3 | Buyer | Buyer nhập mô tả mẫu cần đặt, số lượng, đơn vị, ngày cần hàng (nếu có) và tải ảnh mẫu (nếu có). | Thông tin hiển thị trong form. |
| 4 | Buyer | Buyer chọn hình thức nhận hàng: Nhận tại xưởng, hoặc Giao tận nơi kèm địa chỉ và số điện thoại người nhận. | Thông tin nhận hàng hiển thị trong form. |
| 5 | Buyer | Buyer chọn ngày hạn: yêu cầu gửi chung bắt buộc chọn ngày hạn nhận báo giá; yêu cầu gửi riêng có thể để trống. | Ngày hạn hiển thị trong form (hết lúc 23:59); để trống thì hiện "Hạn phản hồi: 3 ngày kể từ ngày gửi". |
| 6 | Buyer | Buyer xem lại toàn bộ và chọn Gửi yêu cầu. | Dữ liệu được gửi lên kiểm tra. |
| 7 | Backend | Backend kiểm tra dữ liệu và hạn, kiểm tra từng xưởng nhận còn hợp lệ, chốt danh sách xưởng nhận, chụp tên và ảnh sản phẩm mẫu (nếu có), sinh mã yêu cầu và chuyển yêu cầu sang Đã gửi. | Yêu cầu Đã gửi; danh sách xưởng nhận không đổi sau đó. |
| 8 | Backend | Backend thông báo cho từng xưởng nhận. | NT-UC18-01 được gửi, nêu nguồn của yêu cầu. |
| 9 | Frontend | Frontend mở chi tiết yêu cầu. | Hiển thị mã yêu cầu, hạn, danh sách xưởng nhận với phản hồi Chưa phản hồi (UC-21). |

Outcome: Yêu cầu ở trạng thái Đã gửi; các xưởng nhận đã được thông báo.

#### Alternative Flows

##### Alt-01 — Guest chưa đăng nhập

- At step: 1 · Trigger: Guest chọn Gửi yêu cầu báo giá hoặc Đặt hàng theo yêu cầu.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Frontend | Frontend chuyển sang đăng nhập hoặc đăng ký (UC-04, UC-03) và nhớ lối vào đang dùng (sản phẩm, gian hàng hoặc gửi chung). | Sau khi đăng nhập hoặc đăng ký xong, hệ thống quay lại đúng sản phẩm, gian hàng hoặc mục Đặt hàng theo yêu cầu. |

Resume: Tiếp tục bước 1.

##### Alt-02 — ERR-UC18-01 — Dữ liệu không hợp lệ

- At step: 7 · Trigger: Thiếu mô tả hoặc đơn vị; số lượng không phải số nguyên từ 1 đến 1.000.000; ngày cần hàng ở quá khứ; Giao tận nơi nhưng thiếu địa chỉ hoặc số điện thoại sai định dạng; gửi chung chưa chọn phân loại.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Backend | Backend từ chối gửi và đánh dấu trường sai. | Hiển thị ERR-UC18-01 tại trường sai. |

Resume: Quay lại bước 3, giữ nguyên dữ liệu đã nhập.

##### Alt-03 — ERR-UC18-02 — Ảnh không hợp lệ

- At step: 3 · Trigger: Ảnh sai định dạng, lớn hơn 10 MB hoặc vượt 5 ảnh.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Frontend | Frontend không nhận ảnh. | Hiển thị ERR-UC18-02. |

Resume: Chọn ảnh khác, lặp lại bước 3.

##### Alt-04 — ERR-UC18-03 — Hạn không hợp lệ

- At step: 7 · Trigger: Ngày hạn không phải từ ngày mai trở đi hoặc muộn hơn 30 ngày kể từ ngày gửi; yêu cầu gửi chung để trống hạn.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Backend | Backend từ chối gửi. | Hiển thị ERR-UC18-03 tại ô hạn. |

Resume: Quay lại bước 5.

##### Alt-05 — Lưu nháp

- At step: 3 đến 6 · Trigger: Buyer chọn Lưu nháp thay vì Gửi yêu cầu.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Backend | Backend lưu yêu cầu ở trạng thái Nháp cùng nguồn (sản phẩm, gian hàng hoặc gửi chung); không kiểm tra đủ dữ liệu, chưa chốt danh sách xưởng nhận, chưa thông báo xưởng. | Nháp hiện trong tab Nháp (UC-21). |
| 2 | Buyer | Lần sau Buyer mở nháp từ danh sách, sửa và chọn Gửi yêu cầu. | Form mở với dữ liệu đã lưu; Gửi tiếp tục từ bước 6. |

Resume: Mở nháp thì quay lại bước đã dừng. Hạn đã nhập trong nháp mà trôi qua trước khi gửi thì báo ERR-UC18-03.

##### Alt-06 — Xóa nháp

- At step: 1 · Trigger: Buyer chọn Xóa trên một nháp.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Frontend | Frontend hiện hộp xác nhận xóa. | Buyer xác nhận hoặc hủy. |
| 2 | Backend | Backend xóa nháp. Yêu cầu đã gửi không xóa được, chỉ hủy (UC-23). | Nháp biến khỏi danh sách. |

Resume: Kết thúc.

##### Alt-07 — ERR-UC18-04 — Xưởng nhận không khả dụng

- At step: 1 hoặc 7 · Trigger: Yêu cầu gửi riêng mà xưởng nhận chưa mở gian hàng, đang Tạm ngừng hoặc là xưởng của chính Buyer (phát hiện khi mở form, hoặc khi gửi nếu trạng thái xưởng vừa đổi).

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Backend | Backend không gửi yêu cầu và giữ nguyên dữ liệu đã nhập. | Hiển thị ERR-UC18-04 kèm hai lựa chọn: Gửi chung theo phân loại, hoặc Lưu nháp. |
| 2 | Buyer | Buyer chọn Gửi chung theo phân loại. | Form chuyển sang gửi chung: bỏ xưởng nhận và sản phẩm mẫu cũ, giữ mô tả, số lượng, ảnh, hình thức nhận hàng; Buyer chọn phân loại và hạn nhận báo giá. |

Resume: Chọn Gửi chung thì tiếp tục bước 2; chọn Lưu nháp thì kết thúc (Alt-05).

##### Alt-08 — Gửi chung chưa có xưởng đúng phân loại

- At step: 2 · Trigger: Phân loại đã chọn chưa có xưởng nào đủ điều kiện nhận.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Frontend | Frontend hỏi "Chưa có xưởng thuộc phân loại này. Gửi tới tất cả xưởng đang nhận yêu cầu? Yêu cầu sẽ đến cả những xưởng không làm sản phẩm thuộc phân loại này." | Buyer chọn Đồng ý hoặc Chọn phân loại khác. |
| 2 | Buyer | Buyer chọn Đồng ý. | Frontend hiện danh sách tên tất cả xưởng đang nhận yêu cầu (đang hoạt động, trừ xưởng của Buyer). |

Resume: Đồng ý thì tiếp tục bước 3; chọn phân loại khác thì quay lại bước 2.

##### Alt-09 — ERR-UC18-05 — Không có xưởng nào để gửi

- At step: 1, 2 và 7 (Backend kiểm tra lần cuối) · Trigger: Không có xưởng nào đủ điều kiện nhận: không xưởng nào đang hoạt động, tất cả đang Tạm ngừng hoặc chưa mở, hoặc chỉ còn xưởng của chính Buyer; hoặc tình trạng này xảy ra giữa lúc soạn và lúc gửi.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Frontend | Frontend phát hiện ngay khi Buyer mở Đặt hàng theo yêu cầu hoặc chọn phân loại, và vô hiệu nút Gửi yêu cầu; Lưu nháp vẫn dùng được. | Hiển thị ERR-UC18-05. |
| 2 | Backend | Nếu hết xưởng đúng lúc Buyer chọn Gửi yêu cầu, Backend từ chối và giữ yêu cầu ở trạng thái Nháp. | Hiển thị ERR-UC18-05. |

Resume: Kết thúc.

##### Alt-10 — Phiên đăng nhập hết hạn khi đang soạn

- At step: 6 · Trigger: Phiên hết hạn trước khi Buyer chọn Gửi yêu cầu.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Backend | Backend đưa Buyer về form đăng nhập và giữ nội dung đang soạn (UC-04, Alt-08). | Hiển thị ERR-UC04-05. |

Resume: Sau khi đăng nhập quay lại bước 6 với nội dung nguyên vẹn.

##### Alt-11 — Nhắc xưởng trước hạn

- At step: Sau bước 9 (hệ thống tự chạy) · Trigger: Còn 24 giờ trước hạn của yêu cầu, hạn cách ngày gửi từ 2 ngày trở lên, yêu cầu còn Đã gửi hoặc Đang nhận báo giá.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Backend | Backend nhắc từng xưởng nhận chưa phản hồi (không phải Đã báo giá hoặc Không nhận). | NT-UC18-02 được gửi; mỗi xưởng nhận một lần nhắc. |

Resume: Kết thúc. Yêu cầu có hạn chỉ cách ngày gửi 1 ngày thì không nhắc.

##### Alt-12 — Đến hạn của yêu cầu

- At step: Sau bước 9 (hệ thống tự chạy) · Trigger: Hết 23:59 ngày hạn (hạn nhận báo giá của yêu cầu gửi chung, hạn phản hồi của yêu cầu gửi riêng) mà yêu cầu còn Đã gửi hoặc Đang nhận báo giá. Hạn của yêu cầu là lúc xưởng ngừng gửi và cập nhật báo giá; hiệu lực báo giá là thời hạn Buyer còn chấp nhận được báo giá đó.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Backend | Backend đóng nhận báo giá: xưởng không gửi hoặc cập nhật báo giá được nữa. | Các báo giá hiện có giữ nguyên trạng thái. |
| 2 | Backend | Nếu còn ít nhất một báo giá Còn hiệu lực: yêu cầu giữ trạng thái Đang nhận báo giá, hiển thị nhãn "Chờ Buyer lựa chọn" (không thêm trạng thái); Backend thông báo Buyer vào so sánh và chốt. | NT-UC18-03 được gửi. |
| 3 | Backend | Nếu không còn báo giá nào Còn hiệu lực: yêu cầu chuyển Hết hạn. Lý do hiển thị: "Quá hạn phản hồi" (gửi riêng chưa có báo giá), "Hết hạn nhận báo giá" (gửi chung chưa có báo giá) hoặc "Mọi báo giá đã hết hiệu lực" (đã có báo giá nhưng đều hết hiệu lực). | NT-UC18-04 được gửi cho Buyer. |

Resume: Kết thúc. Gửi riêng Hết hạn do "Quá hạn phản hồi" thì Buyer gia hạn (Alt-14) hoặc tạo yêu cầu mới (Alt-15); các trường hợp khác tạo yêu cầu mới (Alt-15) hoặc Yêu cầu cập nhật giá (UC-20, Alt-08).

##### Alt-13 — Mọi báo giá hết hiệu lực

- At step: Sau bước 9 (hệ thống tự chạy) · Trigger: Yêu cầu đã đóng nhận báo giá và báo giá cuối cùng còn hiệu lực vừa Hết hiệu lực (UC-20, Alt-09), yêu cầu chưa Đã chấp nhận hoặc Đã hủy.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Backend | Backend chuyển yêu cầu sang Hết hạn với lý do "Mọi báo giá đã hết hiệu lực". | Yêu cầu Hết hạn. |
| 2 | Backend | Backend thông báo Buyer. | NT-UC18-04 được gửi. |

Resume: Kết thúc. Buyer Yêu cầu cập nhật giá (UC-20, Alt-08) hoặc tạo yêu cầu mới (Alt-15).

##### Alt-14 — Gia hạn yêu cầu gửi riêng

- At step: Sau Alt-12 · Trigger: Yêu cầu gửi riêng đang Hết hạn với lý do "Quá hạn phản hồi", chưa gia hạn lần nào và Buyer chọn Gia hạn.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Buyer | Buyer chọn ngày hạn phản hồi mới (từ ngày mai đến tối đa 30 ngày kể từ ngày gia hạn) và xác nhận. | Yêu cầu gia hạn được gửi. |
| 2 | Backend | Backend kiểm tra yêu cầu chưa Đã chấp nhận hoặc Đã hủy, xưởng chưa phản hồi và đang hoạt động, chưa gia hạn lần nào; đặt hạn mới, đánh dấu đã gia hạn và đưa yêu cầu về Đã gửi. | Yêu cầu Đã gửi với hạn mới; xưởng gửi báo giá hoặc từ chối được trở lại. |
| 3 | Backend | Backend thông báo xưởng nhận. | NT-UC18-05 được gửi. |

Resume: Kết thúc. Nhắc hạn chạy lại theo Alt-11 với hạn mới. Gia hạn tối đa một lần; vẫn quá hạn phản hồi thì Buyer tạo yêu cầu mới (Alt-15). Xưởng đã Không nhận thì không gia hạn được (Alt-16).

##### Alt-15 — Tạo nháp mới từ yêu cầu đã đóng

- At step: 1 · Trigger: Buyer mở yêu cầu Hết hạn, Đã hủy (UC-23), yêu cầu Đã chấp nhận mà đơn đã bị hủy (UC-33), rồi chọn Tạo yêu cầu mới từ yêu cầu này (gợi ý Gửi chung theo phân loại).

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Backend | Backend tạo nháp mới sao chép mô tả, số lượng, đơn vị, ngày cần hàng, ảnh mẫu (kèm ảnh sản phẩm mẫu), hình thức nhận hàng và địa chỉ. | Nháp mới hiện trong tab Nháp; yêu cầu cũ giữ nguyên. |
| 2 | Buyer | Buyer giữ nguồn cũ hoặc chọn Gửi chung theo phân loại. | Chọn gửi chung thì sản phẩm mẫu và xưởng nhận cũ được bỏ; Buyer chọn phân loại. |
| 3 | Buyer | Buyer chọn hạn rồi gửi. | Tiếp tục bước 6. |

Resume: Tiếp tục bước 6 của Main Flow.

##### Alt-16 — ERR-UC18-06 — Không gia hạn được

- At step: Alt-14 bước 2 · Trigger: Yêu cầu không còn Hết hạn do quá hạn phản hồi, đã gia hạn một lần, xưởng đã phản hồi (kể cả Không nhận) hoặc đang Tạm ngừng, hạn mới không hợp lệ.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Backend | Backend từ chối gia hạn. | Hiển thị ERR-UC18-06. |

Resume: Kết thúc.

##### Alt-17 — ERR-UC18-07 — Không có quyền

- At step: 1 · Trigger: Người dùng mở, sửa, xóa nháp hoặc gia hạn yêu cầu của người khác; tài khoản Admin gửi yêu cầu.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Backend | Backend từ chối thao tác. | Hiển thị ERR-UC18-07; dữ liệu không đổi. |

Resume: Kết thúc.

#### Data Fields

| Field | Format | Validation | Required | Ghi chú |
|---|---|---|---|---|
| Nguồn yêu cầu | Chỉ xem | Từ sản phẩm, Gửi riêng cho xưởng, Gửi chung theo phân loại | — | Hiện ở form và ở xưởng nhận. Không đổi sau khi gửi |
| Sản phẩm mẫu | Tên và ảnh, chỉ xem | Chỉ có khi gửi từ trang sản phẩm; chụp lúc gửi (BR-6) | — | Xưởng thấy "từ sản phẩm {tên}" |
| Xưởng nhận | Danh sách tên, chỉ xem | Đang hoạt động, không phải xưởng của Buyer; chốt lúc gửi (BR-3) | — | Gửi riêng: một xưởng. Gửi chung: các xưởng đúng phân loại, hoặc tất cả xưởng đang nhận nếu Buyer đồng ý (Alt-08) |
| Phân loại | Chọn một | Chỉ phân loại đang Hiển thị | Có điều kiện | Bắt buộc với gửi chung. Từ UC-12 |
| Mô tả mẫu cần đặt | Text; length 2000 | Không để trống | Có | Ví dụ: "Cốc tre khắc logo Trường ĐH Công nghệ, cao 10 cm, màu tự nhiên" |
| Số lượng | Number | Số nguyên từ 1 đến 1.000.000 | Có | Ví dụ: 5000 |
| Đơn vị | Text; length 30 | Không để trống | Có | Ví dụ: "chiếc", "bộ" |
| Ảnh mẫu | Ảnh JPG / PNG / WebP | 0–5 ảnh, mỗi ảnh ≤ 10 MB | Không | Ảnh sản phẩm mẫu hiện riêng, không tính vào 5 ảnh |
| Ngày cần hàng | Date | Không ở quá khứ | Không | Chỉ là mong muốn; xưởng báo thời gian hoàn thành ở báo giá (UC-19) |
| Hình thức nhận hàng | Chọn một | Nhận tại xưởng hoặc Giao tận nơi | Có | Quyết định phí giao trong báo giá |
| Địa chỉ nhận hàng | Tỉnh/thành, quận/huyện, phường/xã chọn theo danh mục; địa chỉ chi tiết text length 150 | Đủ bốn mục | Có điều kiện | Bắt buộc khi Giao tận nơi. Xưởng chỉ thấy đến quận/huyện trước khi báo giá được chấp nhận (BR-8) |
| Số điện thoại người nhận | Text; length 10 | Số di động Việt Nam, 10 chữ số | Có điều kiện | Bắt buộc khi Giao tận nơi; điền sẵn từ tài khoản, được sửa. Xưởng chỉ thấy sau khi báo giá được chấp nhận. Ví dụ: 0912345678 |
| Hạn nhận báo giá | Date | Từ ngày mai đến tối đa 30 ngày kể từ ngày gửi; hết lúc 23:59 | Có điều kiện | Bắt buộc với yêu cầu gửi chung. Buyer chỉ chọn ngày |
| Hạn phản hồi | Date | Từ ngày mai đến tối đa 30 ngày kể từ ngày gửi; hết lúc 23:59 | Không | Yêu cầu gửi riêng. Để trống thì hệ thống đặt 3 ngày kể từ ngày gửi |
| Mã yêu cầu | Text | Hệ thống sinh, duy nhất | — | Ví dụ: "YC-2026-0001" |
| Trạng thái yêu cầu | Chỉ xem | Nháp, Đã gửi, Đang nhận báo giá, Đã chấp nhận, Đã hủy, Hết hạn | — | |
| Lý do hết hạn | Chỉ xem | Quá hạn phản hồi; Xưởng không nhận; Tất cả xưởng không nhận; Hết hạn nhận báo giá; Mọi báo giá đã hết hiệu lực | — | Chỉ khi Hết hạn |
| Số lần gia hạn | Chỉ xem | Tối đa 1 | — | Chỉ yêu cầu gửi riêng (Alt-14) |

#### Error Messages

| Code | Condition | Message | Display | Flow |
|---|---|---|---|---|
| ERR-UC18-01 | Dữ liệu không hợp lệ | "Thông tin yêu cầu chưa hợp lệ. Vui lòng kiểm tra các trường được đánh dấu." | Inline | Alt-02 |
| ERR-UC18-02 | Ảnh không hợp lệ | "Ảnh không hợp lệ. Chỉ chấp nhận JPG, PNG, WebP, tối đa 10 MB, tối đa 5 ảnh." | Inline | Alt-03 |
| ERR-UC18-03 | Hạn không hợp lệ | "Hạn phải từ ngày mai đến tối đa 30 ngày kể từ ngày gửi. Yêu cầu gửi chung cần chọn hạn nhận báo giá." | Inline | Alt-04 |
| ERR-UC18-04 | Xưởng nhận không khả dụng | "Xưởng này hiện không nhận yêu cầu báo giá. Bạn có thể gửi chung theo phân loại hoặc lưu nháp." | Toast | Alt-07 |
| ERR-UC18-05 | Không có xưởng để gửi | "Hiện chưa có xưởng nào đang nhận yêu cầu báo giá. Yêu cầu được lưu nháp, bạn có thể gửi lại sau." | Toast | Alt-09 |
| ERR-UC18-06 | Không gia hạn được | "Không thể gia hạn yêu cầu này. Bạn có thể tạo yêu cầu mới từ yêu cầu này." | Toast | Alt-16 |
| ERR-UC18-07 | Không có quyền | "Bạn không có quyền thực hiện thao tác này." | Trang lỗi | Alt-17 |

#### Notifications

| Template ID | Trigger (step) | Channel | Recipient | Title | Body |
|---|---|---|---|---|---|
| NT-UC18-01 | 8 | In-app | Từng xưởng nhận | Có yêu cầu báo giá mới | "{{source}}: cần {{quantity}} {{unit}}. Hạn: {{deadline}}." ({{source}}: "Từ sản phẩm {{productName}}", "Gửi riêng cho xưởng bạn" hoặc "Yêu cầu gửi chung") |
| NT-UC18-02 | Alt-11 bước 1 | In-app | Xưởng nhận chưa phản hồi | Yêu cầu báo giá sắp hết hạn | "Yêu cầu {{requestCode}} sẽ đóng nhận báo giá vào cuối ngày {{deadline}}. Bạn chưa phản hồi." |
| NT-UC18-03 | Alt-12 bước 2 | In-app | Buyer sở hữu yêu cầu | Yêu cầu chờ bạn lựa chọn | "Yêu cầu {{requestCode}} đã đóng nhận báo giá với {{quoteCount}} báo giá còn hiệu lực. Hãy so sánh và chọn một báo giá." |
| NT-UC18-04 | Alt-12 bước 3; Alt-13 bước 2 | In-app | Buyer sở hữu yêu cầu | Yêu cầu báo giá đã hết hạn | "Yêu cầu {{requestCode}} đã hết hạn: {{reason}}." |
| NT-UC18-05 | Alt-14 bước 3 | In-app | Xưởng nhận | Yêu cầu báo giá được gia hạn | "Yêu cầu {{requestCode}} được gia hạn đến {{deadline}}. Hãy phản hồi trước hạn." |

#### Business Rules

| ID | Rule | Source |
|---|---|---|
| BR-1 | Ba lối vào, hai cách gửi: từ sản phẩm (gửi riêng, gắn sản phẩm mẫu), từ gian hàng (gửi riêng, không gắn sản phẩm), từ Đặt hàng theo yêu cầu (gửi chung, không gắn sản phẩm). | Overview 3.4 |
| BR-2 | Gửi yêu cầu cần tài khoản đã đăng nhập; Guest đăng nhập xong quay lại đúng sản phẩm, gian hàng hoặc mục đang xem; nháp giữ nguồn. | Overview 3.1, 3.4 |
| BR-3 | Xưởng nhận phải đang hoạt động (không Đang chuẩn bị, không Tạm ngừng) và không phải xưởng của người gửi; danh sách chốt lúc gửi, không đổi sau đó. | Overview 3.4 |
| BR-4 | Gửi chung: Buyer chọn một phân loại; xưởng thuộc phân loại khi có sản phẩm Hiển thị thuộc phân loại đó (UC-08 BR-3). Không có xưởng đúng phân loại thì hỏi Buyer có gửi tới tất cả xưởng đang nhận không, kèm cảnh báo yêu cầu sẽ đến cả xưởng không làm sản phẩm thuộc phân loại đó. Không có xưởng nào đủ điều kiện nhận thì chặn gửi ngay khi mở hoặc chọn phân loại; Backend kiểm tra lần cuối lúc gửi. Chưa giới hạn số xưởng nhận (KI-01). | Overview 3.4, mục 12; Nhóm đề xuất (G-10) |
| BR-5 | Yêu cầu lưu nguồn; xưởng nhận thấy nguồn, không thấy số xưởng khác nhận. | Overview 3.4 |
| BR-6 | Sản phẩm mẫu lưu bản chụp tên và ảnh lúc gửi; sản phẩm sửa hoặc ẩn sau đó không đổi yêu cầu. Buyer vẫn sửa mô tả, số lượng và thêm ảnh. | Overview 3.4 |
| BR-7 | Yêu cầu đã gửi không sửa được; muốn đổi thì hủy (UC-23) rồi tạo yêu cầu mới (Alt-15). Ngoại lệ duy nhất là gia hạn hạn phản hồi của yêu cầu gửi riêng, tối đa một lần (Alt-14). | Overview 3.4; Chốt 2026-11-18; giới hạn một lần chờ chốt G-06 |
| BR-8 | Hình thức nhận hàng và địa chỉ chọn lúc soạn yêu cầu; xưởng báo phí giao tương ứng. Xưởng chỉ thấy địa chỉ đến cấp quận/huyện, và chỉ thấy họ tên, số điện thoại, địa chỉ chi tiết của Buyer sau khi báo giá được chấp nhận. | Chốt 2026-11-18 (hình thức nhận); Nhóm đề xuất (che địa chỉ) |
| BR-9 | Hạn: yêu cầu gửi chung bắt buộc chọn ngày hạn nhận báo giá, là hạn chót để các xưởng nộp báo giá cạnh tranh. Yêu cầu gửi riêng không bắt buộc; để trống thì hạn phản hồi là 3 ngày kể từ ngày gửi, để yêu cầu không treo mãi nếu xưởng không kiểm tra hệ thống. Buyer chỉ chọn ngày; hạn hết lúc 23:59 ngày đó, từ ngày mai đến tối đa 30 ngày kể từ ngày gửi. | Chốt 2026-11-18; chờ chốt G-05 |
| BR-10 | Nhắc xưởng chưa phản hồi 24 giờ trước hạn, chỉ khi hạn cách ngày gửi từ 2 ngày trở lên; mỗi xưởng tối đa một lần nhắc. | Chốt 2026-11-18; ngưỡng cụ thể chờ chốt G-05 |
| BR-11 | Hạn của yêu cầu là lúc xưởng ngừng gửi và cập nhật báo giá; khác với hiệu lực báo giá là thời hạn Buyer còn chấp nhận được báo giá. Đến hạn thì đóng nhận báo giá: còn báo giá hiệu lực thì Buyer so sánh và chốt (yêu cầu giữ Đang nhận báo giá, hiển thị nhãn "Chờ Buyer lựa chọn"); chưa có thì yêu cầu Hết hạn. | Chốt 2026-11-18; chờ chốt G-06 |
| BR-12 | Chuyển trạng thái yêu cầu: Nháp sang Đã gửi; Đã gửi sang Đang nhận báo giá khi có báo giá đầu tiên; Đang nhận báo giá sang Đã chấp nhận (UC-20); Đã gửi hoặc Đang nhận báo giá sang Đã hủy (UC-23) hoặc Hết hạn (Alt-12, Alt-13; hoặc xưởng Không nhận ở yêu cầu gửi riêng, hoặc tất cả xưởng Không nhận ở yêu cầu gửi chung, UC-19 Alt-02); Hết hạn về Đã gửi (gia hạn, Alt-14) hoặc về Đang nhận báo giá (khi xưởng gửi báo giá mới còn hiệu lực theo Yêu cầu cập nhật giá, UC-19 Alt-07). Mọi chuyển lưu người thực hiện và thời điểm. | Overview mục 4; Chốt 2026-11-18 |
| BR-13 | Admin không tạo, không xem yêu cầu. Artisan gửi yêu cầu với vai trò Buyer và không gửi cho xưởng của chính mình. | Overview mục 1 |
| BR-14 | Các thời hạn trong nhóm và người đặt: hạn nhận báo giá và hạn phản hồi (Buyer chọn ngày); hiệu lực báo giá (xưởng chọn, tối đa 90 ngày); hạn trả lời Yêu cầu cập nhật giá 3 ngày (hệ thống); xác nhận nhận hàng 7 ngày (hệ thống, UC-22). | Nhóm đề xuất |

#### Acceptance Criteria

| ID | Given | When | Then | Covers |
|---|---|---|---|---|
| UC-18-AC-01 | Buyer đã đăng nhập, xem sản phẩm "Giỏ mây đan hoa cúc" của xưởng đang hoạt động | Chọn Gửi yêu cầu báo giá, nhập mô tả, số lượng 500, đơn vị "chiếc", chọn Nhận tại xưởng, bỏ trống hạn, chọn Gửi yêu cầu | Yêu cầu Đã gửi có mã; kèm tên và ảnh sản phẩm mẫu; chỉ xưởng của sản phẩm nhận NT-UC18-01 nêu "Từ sản phẩm Giỏ mây đan hoa cúc"; hạn phản hồi là 3 ngày sau | Main Flow, BR-1, BR-9 |
| UC-18-AC-02 | Guest chưa đăng nhập đang xem sản phẩm hoặc gian hàng | Chọn Gửi yêu cầu báo giá, rồi đăng nhập | Quay lại đúng sản phẩm hoặc gian hàng đang xem và mở form | Alt-01, BR-2 |
| UC-18-AC-03 | Để trống mô tả, hoặc số lượng 0, hoặc Giao tận nơi thiếu địa chỉ | Chọn Gửi yêu cầu | Hiển thị ERR-UC18-01 tại trường sai; dữ liệu giữ nguyên; yêu cầu không được gửi | Alt-02 |
| UC-18-AC-04 | Đang chọn ảnh mẫu | Chọn tệp PDF, ảnh lớn hơn 10 MB hoặc ảnh thứ 6 | Hiển thị ERR-UC18-02; ảnh không được nhận | Alt-03 |
| UC-18-AC-05 | Yêu cầu gửi chung bỏ trống hạn, hoặc yêu cầu gửi riêng có hạn là hôm nay hoặc 45 ngày sau | Chọn Gửi yêu cầu | Hiển thị ERR-UC18-03; yêu cầu không được gửi | Alt-04, BR-9 |
| UC-18-AC-06 | Buyer đã nhập một phần thông tin | Chọn Lưu nháp, đóng trang rồi mở lại từ tab Nháp | Nháp giữ nguồn và dữ liệu đã nhập; xưởng chưa nhận thông báo; chọn Gửi thì gửi được | Alt-05, BR-2 |
| UC-18-AC-07 | Buyer có một nháp | Chọn Xóa và xác nhận | Nháp biến khỏi danh sách; yêu cầu đã gửi không có nút Xóa | Alt-06 |
| UC-18-AC-08 | Xưởng của sản phẩm vừa chuyển Tạm ngừng sau khi Buyer mở form | Chọn Gửi yêu cầu | Hiển thị ERR-UC18-04 kèm lựa chọn Gửi chung theo phân loại hoặc Lưu nháp; dữ liệu đã nhập được giữ; chọn Gửi chung thì mô tả, số lượng, ảnh được giữ còn sản phẩm mẫu và xưởng nhận cũ bị bỏ; xưởng không nhận thông báo | Alt-07, BR-3 |
| UC-18-AC-09 | Artisan có gian hàng, và có xưởng khác cùng phân loại | Xem sản phẩm, gian hàng của mình; rồi gửi chung | Không có nút gửi yêu cầu ở sản phẩm, gian hàng của mình; xưởng của mình không có trong danh sách xưởng nhận | BR-3, BR-13 |
| UC-18-AC-10 | Phân loại "Đèn mây" chưa có xưởng nào có sản phẩm Hiển thị | Buyer chọn phân loại này | Hiện câu hỏi gửi tất cả xưởng đang nhận kèm cảnh báo xưởng có thể không làm sản phẩm thuộc phân loại; Đồng ý thì danh sách là tất cả xưởng đang nhận trừ xưởng của Buyer | Alt-08, BR-4 |
| UC-18-AC-11 | Không có xưởng nào đủ điều kiện nhận (không xưởng hoạt động, hoặc chỉ còn xưởng của Buyer) | Buyer mở Đặt hàng theo yêu cầu hoặc chọn phân loại | Hiển thị ERR-UC18-05 ngay; nút Gửi bị vô hiệu, Lưu nháp vẫn dùng được; nếu hết xưởng đúng lúc bấm Gửi thì Backend cũng từ chối với ERR-UC18-05 và giữ nháp | Alt-09 |
| UC-18-AC-12 | Phiên hết hạn khi Buyer đang soạn | Chọn Gửi yêu cầu | Hiển thị ERR-UC04-05; đăng nhập lại xong, nội dung đang soạn còn nguyên | Alt-10 |
| UC-18-AC-13 | Yêu cầu gửi chung hạn sau 3 ngày; xưởng A chưa phản hồi, xưởng B đã báo giá, xưởng C đã Không nhận | Còn 24 giờ trước hạn | Chỉ xưởng A nhận NT-UC18-02; B và C không nhận | Alt-11, BR-10 |
| UC-18-AC-14 | Một yêu cầu gửi ngày 18 có hạn ngày 19, một yêu cầu khác có hạn ngày 21 | Đến thời điểm nhắc | Yêu cầu hạn ngày 19 không nhắc; yêu cầu hạn ngày 21 nhắc một lần trước hạn 24 giờ | BR-10 |
| UC-18-AC-15 | Yêu cầu gửi chung có hai báo giá còn hiệu lực | Đến hạn nhận báo giá | Yêu cầu vẫn Đang nhận báo giá, có nhãn "Chờ Buyer lựa chọn"; xưởng chưa báo giá không gửi mới được (ERR-UC19-02); Buyer nhận NT-UC18-03 và vẫn chấp nhận được | Alt-12, BR-11 |
| UC-18-AC-16 | Yêu cầu gửi riêng, xưởng chưa phản hồi | Đến hạn phản hồi | Yêu cầu Hết hạn, lý do "Quá hạn phản hồi"; Buyer nhận NT-UC18-04; hiện nút Gia hạn và Tạo yêu cầu mới | Alt-12 |
| UC-18-AC-17 | Yêu cầu gửi chung đến hạn mà chưa có báo giá nào | Đến hạn nhận báo giá | Yêu cầu Hết hạn, lý do "Hết hạn nhận báo giá"; Buyer nhận NT-UC18-04 | Alt-12 |
| UC-18-AC-18 | Yêu cầu đã đóng nhận báo giá, còn một báo giá hiệu lực | Báo giá đó hết hiệu lực | Yêu cầu Hết hạn, lý do "Mọi báo giá đã hết hiệu lực"; Buyer nhận NT-UC18-04 | Alt-13 |
| UC-18-AC-19 | Yêu cầu gửi riêng Hết hạn do quá hạn phản hồi | Buyer chọn Gia hạn và nhập hạn mới hợp lệ | Yêu cầu về Đã gửi với hạn mới; xưởng nhận NT-UC18-05; gia hạn lần hai bị ERR-UC18-06 | Alt-14, BR-7 |
| UC-18-AC-20 | Yêu cầu Hết hạn nhưng xưởng đã phản hồi, hoặc xưởng đang Tạm ngừng, hoặc yêu cầu đã gia hạn một lần | Buyer chọn Gia hạn | Hiển thị ERR-UC18-06; yêu cầu giữ nguyên | Alt-16 |
| UC-18-AC-21 | Yêu cầu Hết hạn, Đã hủy hoặc gửi riêng bị xưởng Không nhận | Buyer chọn Tạo yêu cầu mới từ yêu cầu này, rồi chọn Gửi chung | Nháp mới điền sẵn nội dung; sản phẩm mẫu và xưởng nhận cũ bị bỏ; yêu cầu cũ giữ nguyên | Alt-15, BR-7 |
| UC-18-AC-22 | Người dùng khác hoặc Admin | Mở, sửa nháp của Buyer, hoặc gửi yêu cầu bằng tài khoản Admin | Hiển thị ERR-UC18-07; dữ liệu không đổi | Alt-17, BR-13 |
| UC-18-AC-23 | Yêu cầu gửi chung gửi cho 5 xưởng | Một xưởng mở yêu cầu | Thấy nguồn "Yêu cầu gửi chung"; không thấy số xưởng khác nhận | BR-5 |
| UC-18-AC-24 | Yêu cầu có sản phẩm mẫu; sau đó xưởng đổi tên hoặc Ẩn sản phẩm | Xưởng và Buyer mở yêu cầu | Vẫn thấy tên và ảnh sản phẩm mẫu lúc gửi | BR-6 |
| UC-18-AC-25 | Yêu cầu Đã gửi | Buyer tìm thao tác sửa nội dung | Không có thao tác sửa (chỉ Hủy, và Gia hạn trong trường hợp Alt-14) | BR-7 |
| UC-18-AC-26 | Yêu cầu Giao tận nơi chưa được chấp nhận | Xưởng nhận mở yêu cầu | Thấy tỉnh/thành và quận/huyện; không thấy họ tên, số điện thoại, địa chỉ chi tiết của Buyer | BR-8 |
| UC-18-AC-27 | Mỗi khi yêu cầu đổi trạng thái | Xem lịch sử yêu cầu (UC-21) | Mỗi lần ghi người thực hiện, thời điểm; chuyển trạng thái chỉ theo các bước của BR-12 | BR-12 |
| UC-18-AC-28 | Yêu cầu gửi chung có hạn ngày 2026-11-25 | Xưởng gửi báo giá lúc 23:50 ngày 25, rồi lúc 00:05 ngày 26 | Lần đầu thành công; lần sau bị ERR-UC19-02 | Alt-12, BR-9, BR-11 |
| UC-18-AC-29 | Yêu cầu gửi riêng chưa có báo giá; yêu cầu gửi chung chưa có báo giá; yêu cầu có hai báo giá đều đã hết hiệu lực | Đến hết ngày hạn | Lý do Hết hạn lần lượt là "Quá hạn phản hồi", "Hết hạn nhận báo giá", "Mọi báo giá đã hết hiệu lực" | Alt-12 |
| UC-18-AC-30 | Mọi thời hạn trong nhóm | Xem mô tả hạn trong form, thông báo | Buyer chỉ chọn ngày hạn; xưởng chọn hiệu lực; các hạn 3 ngày và 7 ngày do hệ thống đặt | BR-14 |

### UC-19 — Tiếp nhận yêu cầu, gửi báo giá hoặc không nhận

#### Detail

| Field | Content |
|---|---|
| Use case ID | UC-19 |
| Description | Xưởng nhận yêu cầu báo giá, xem chi tiết, rồi gửi báo giá cho toàn bộ số lượng (đơn giá, phí giao, thời gian hoàn thành, tỷ lệ cọc, hiệu lực), cập nhật báo giá trước hạn, hoặc không nhận kèm lý do. Xưởng cũng xử lý Yêu cầu cập nhật giá của Buyer khi báo giá đã hết hiệu lực. |
| Actors | Artisan (chủ xưởng nhận yêu cầu) |
| Channel | Kênh người bán |
| Preconditions | • Đã đăng nhập<br>• Xưởng có trong danh sách xưởng nhận của yêu cầu<br>• Gửi hoặc cập nhật báo giá: gian hàng đang hoạt động. Không nhận: được cả khi gian hàng đang Tạm ngừng (Alt-05) |
| Expected | **Thành công:**<br>• Báo giá được lưu (phiên bản 1) và Buyer được thông báo; hoặc phản hồi Không nhận được ghi nhận kèm lý do<br>**Thất bại:**<br>• Hiển thị lỗi, báo giá chưa gửi, dữ liệu đã nhập được giữ |
| Screens | • Danh sách yêu cầu nhận (UC-21)<br>• Chi tiết yêu cầu (bước 2)<br>• Form báo giá (bước 3-5) |
| Notifications | NT-UC19-01 đến NT-UC19-05 |

#### Main Flow

Trigger: Xưởng mở thông báo NT-UC18-01 hoặc chọn một yêu cầu trong danh sách yêu cầu nhận.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Artisan | Artisan mở yêu cầu. | Chi tiết yêu cầu hiển thị. |
| 2 | Frontend | Frontend hiển thị nguồn, tên và ảnh sản phẩm mẫu (nếu có), mô tả, ảnh mẫu, số lượng, ngày cần hàng, hình thức nhận hàng (địa chỉ đến quận/huyện), hạn và trạng thái. | Xưởng nắm đủ nhu cầu để báo giá. |
| 3 | Artisan | Artisan chọn Gửi báo giá, nhập đơn giá, phí giao (0 nếu nhận tại xưởng), thời gian hoàn thành, tỷ lệ cọc (0%, 30% hoặc 50%), hiệu lực đến ngày và ghi chú (nếu có). | Form báo giá hiển thị; tổng giá trị và số tiền cọc tự tính. |
| 4 | Artisan | Artisan xem lại tổng giá trị, số tiền cọc và chọn Gửi báo giá. | Dữ liệu được gửi lên kiểm tra. |
| 5 | Backend | Backend kiểm tra dữ liệu; kiểm tra xưởng nhận hợp lệ, yêu cầu còn nhận báo giá và xưởng chưa có báo giá cho yêu cầu này; lưu báo giá phiên bản 1. Nếu đây là báo giá đầu tiên, yêu cầu chuyển sang Đang nhận báo giá. | Báo giá ở trạng thái Còn hiệu lực; phản hồi của xưởng là Đã báo giá. |
| 6 | Backend | Backend thông báo Buyer. | NT-UC19-01 được gửi. |
| 7 | Frontend | Frontend mở chi tiết báo giá của xưởng. | Hiển thị báo giá, phiên bản, hiệu lực và nút Cập nhật báo giá (còn trong hạn). |

Outcome: Báo giá Còn hiệu lực được lưu và Buyer đã được thông báo.

#### Alternative Flows

##### Alt-01 — Cập nhật báo giá trước hạn

- At step: 7 · Trigger: Xưởng chọn Cập nhật báo giá khi yêu cầu còn nhận báo giá và báo giá chưa được chấp nhận (kể cả báo giá đã Hết hiệu lực trước hạn).

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Artisan | Artisan sửa các trường báo giá rồi chọn Gửi báo giá. | Dữ liệu được gửi lên kiểm tra. |
| 2 | Backend | Backend lưu thành phiên bản mới thay phiên bản cũ; giữ lịch sử các phiên bản. | Báo giá có số phiên bản tăng thêm 1. |
| 3 | Backend | Backend thông báo Buyer. | NT-UC19-02 được gửi, nêu rõ báo giá đã đổi nên Buyer cần xem lại. |

Resume: Kết thúc. Buyer đang xem phiên bản cũ phải xem phiên bản mới trước khi chấp nhận (UC-20, Alt-04).

##### Alt-02 — Không nhận yêu cầu

- At step: 2 · Trigger: Xưởng chưa phản hồi chọn Không nhận.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Artisan | Artisan chọn lý do (Không làm được mẫu này, Không đủ năng lực cho số lượng, Không kịp thời gian, Khác) và nhập ghi chú nếu chọn Khác. | Hộp xác nhận hiển thị. |
| 2 | Backend | Backend ghi nhận phản hồi Không nhận kèm lý do, không tạo báo giá. | Xưởng chỉ còn xem lý do; hết nút Gửi báo giá và Không nhận; không nhận nhắc hạn nữa. |
| 3 | Backend | Yêu cầu gửi riêng: yêu cầu chuyển Hết hạn ngay, lý do "Xưởng không nhận", không chờ đến hạn phản hồi; Backend thông báo Buyer. | NT-UC19-03 được gửi. |
| 4 | Backend | Yêu cầu gửi chung: chỉ cập nhật phản hồi của xưởng trong danh sách, không thông báo từng xưởng; các xưởng khác tiếp tục báo giá. Nếu mọi xưởng nhận đều đã Không nhận thì yêu cầu chuyển Hết hạn, lý do "Tất cả xưởng không nhận", và Backend thông báo Buyer. | NT-UC19-03 chỉ được gửi khi tất cả xưởng đã từ chối. |

Resume: Kết thúc. Xưởng đã Không nhận không đổi sang báo giá được (đơn giản cho MVP); muốn đặt tiếp thì Buyer tạo yêu cầu mới (UC-18, Alt-15).

##### Alt-03 — ERR-UC19-01 — Dữ liệu báo giá không hợp lệ

- At step: 5 · Trigger: Đơn giá hoặc phí giao ngoài khoảng cho phép; Nhận tại xưởng mà phí giao khác 0; thời gian hoàn thành không phải số ngày từ 1 đến 365; tỷ lệ cọc ngoài 0%, 30%, 50%; thiếu hiệu lực hoặc hiệu lực ngoài khoảng cho phép (BR-5).

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Backend | Backend từ chối gửi và đánh dấu trường sai. | Hiển thị ERR-UC19-01 tại trường sai. |

Resume: Quay lại bước 3, giữ nguyên dữ liệu đã nhập.

##### Alt-04 — ERR-UC19-02 — Yêu cầu đã đóng nhận báo giá

- At step: 5 · Trigger: Yêu cầu đã hết ngày hạn, đã Đã chấp nhận, Đã hủy hoặc Hết hạn (trừ khi đang có Yêu cầu cập nhật giá còn hạn, Alt-07).

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Backend | Backend từ chối gửi hoặc cập nhật. | Hiển thị ERR-UC19-02; dữ liệu không đổi. |

Resume: Kết thúc.

##### Alt-05 — ERR-UC19-03 — Gian hàng đang Tạm ngừng

- At step: 5 · Trigger: Xưởng bị chuyển Tạm ngừng sau khi nhận yêu cầu, rồi chọn Gửi báo giá hoặc Cập nhật báo giá.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Backend | Backend từ chối gửi hoặc cập nhật báo giá; vẫn cho Không nhận. | Hiển thị ERR-UC19-03. |

Resume: Kết thúc. Báo giá đã gửi trước khi Tạm ngừng vẫn được Buyer chấp nhận (UC-20, BR-9).

##### Alt-06 — ERR-UC19-04 — Đã phản hồi

- At step: 5 · Trigger: Request cũ hoặc đồng thời mà xưởng đã có báo giá lại gửi báo giá mới hoặc chọn Không nhận; hoặc xưởng đã Không nhận lại gửi báo giá hoặc Không nhận lần nữa. Giao diện đã ẩn các nút này (BR-13).

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Backend | Backend từ chối. | Hiển thị ERR-UC19-04. |

Resume: Kết thúc. Mỗi xưởng chỉ có một báo giá cho mỗi yêu cầu (BR-3).

##### Alt-07 — Xử lý Yêu cầu cập nhật giá

- At step: 7 · Trigger: Xưởng nhận NT-UC20-04 vì Buyer Yêu cầu cập nhật giá cho báo giá đã Hết hiệu lực (UC-20, Alt-08), trong 3 ngày kể từ lúc Buyer yêu cầu.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Artisan | Artisan mở báo giá, thấy nhãn "Buyer yêu cầu cập nhật giá, hạn trả lời {{updateDeadline}}", rồi chọn Cập nhật giá. | Form báo giá hiển thị với nội dung báo giá cũ; hiệu lực mới bắt buộc nhập lại. |
| 2 | Backend | Backend kiểm tra Yêu cầu cập nhật giá còn đang chờ (chưa quá 3 ngày, chưa bị đóng) và dữ liệu như Main Flow; lưu phiên bản mới ở trạng thái Còn hiệu lực với hiệu lực mới; không kiểm tra hạn của yêu cầu. Yêu cầu đang Hết hạn thì quay về Đang nhận báo giá (hiển thị nhãn "Chờ Buyer lựa chọn"). | Báo giá Còn hiệu lực trở lại; Yêu cầu cập nhật giá được đóng. |
| 3 | Backend | Backend thông báo Buyer. | NT-UC19-04 được gửi. |

Resume: Kết thúc. Xưởng không muốn cập nhật thì chọn Từ chối cập nhật (Alt-10). Sau hạn 3 ngày thì xem Alt-11.

##### Alt-08 — Xưởng không trả lời Yêu cầu cập nhật giá

- At step: Sau Alt-07 (hệ thống tự chạy) · Trigger: Qua 3 ngày kể từ lúc Buyer Yêu cầu cập nhật giá mà xưởng chưa cập nhật hoặc từ chối.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Backend | Backend đóng Yêu cầu cập nhật giá; báo giá giữ Hết hiệu lực; yêu cầu giữ nguyên trạng thái (đang Hết hạn thì vẫn Hết hạn). | Buyer không còn nút Yêu cầu cập nhật giá cho báo giá này; nhãn "Đang chờ cập nhật giá" được gỡ. |
| 2 | Backend | Backend thông báo Buyer. | NT-UC19-05 được gửi. |

Resume: Kết thúc. Buyer tạo yêu cầu mới (UC-18, Alt-15).

##### Alt-09 — ERR-UC19-05 — Không có quyền

- At step: 1 · Trigger: Xưởng khác (không có trong danh sách nhận), Buyer hoặc Admin mở hoặc gửi báo giá cho yêu cầu này.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Backend | Backend từ chối thao tác. | Hiển thị ERR-UC19-05; dữ liệu không đổi. |

Resume: Kết thúc.

##### Alt-10 — Từ chối cập nhật giá

- At step: Alt-07 bước 1 · Trigger: Xưởng chọn Từ chối cập nhật khi có Yêu cầu cập nhật giá còn hạn.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Backend | Backend đóng Yêu cầu cập nhật giá; báo giá giữ Hết hiệu lực; yêu cầu giữ nguyên trạng thái. | Nhãn "Đang chờ cập nhật giá" được gỡ. |
| 2 | Backend | Backend thông báo Buyer. | NT-UC19-05 được gửi với lý do "đã từ chối". |

Resume: Kết thúc. Buyer tạo yêu cầu mới (UC-18, Alt-15).

##### Alt-11 — ERR-UC19-06 — Yêu cầu cập nhật giá đã đóng

- At step: Alt-07 bước 2 · Trigger: Xưởng cập nhật giá sau 3 ngày, hoặc Yêu cầu cập nhật giá đã bị đóng (xưởng đã từ chối, yêu cầu đã Đã chấp nhận hoặc Đã hủy).

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Backend | Backend từ chối. | Hiển thị ERR-UC19-06; báo giá giữ Hết hiệu lực. |

Resume: Kết thúc.

#### Data Fields

| Field | Format | Validation | Required | Ghi chú |
|---|---|---|---|---|
| Thông tin yêu cầu | Chỉ xem | Nguồn, sản phẩm mẫu, mô tả, ảnh, số lượng, ngày cần hàng, hình thức nhận, hạn | — | Địa chỉ chỉ hiện đến quận/huyện cho đến khi báo giá được chấp nhận (UC-18, BR-8) |
| Đơn giá | Number (VND, mỗi đơn vị) | Số nguyên từ 1.000 đến 1.000.000.000 | Có | Ví dụ: 85000 |
| Phí giao | Number (VND) | Số nguyên từ 0 đến 50.000.000; bằng 0 nếu Nhận tại xưởng | Có | Ví dụ: 150000 |
| Thời gian hoàn thành | Number (ngày) | Số nguyên từ 1 đến 365 | Có | Tính từ lúc Buyer chấp nhận. Ví dụ: 21 |
| Tỷ lệ cọc | Chọn một | 0%, 30% hoặc 50% | Có | Xưởng chọn (BR-4) |
| Hiệu lực đến ngày | Date | Hết 23:59 ngày chọn; theo BR-5 | Có | Ví dụ: 2026-12-20 |
| Ghi chú | Text; length 500 | — | Không | Chất liệu, quy cách, điều kiện |
| Tổng giá trị | Number, chỉ xem | Đơn giá nhân số lượng, cộng phí giao | — | Hệ thống tính, hiển thị cho cả hai bên |
| Số tiền cọc | Number, chỉ xem | Tổng giá trị nhân tỷ lệ cọc, làm tròn đến đồng | — | 0 nếu tỷ lệ cọc 0% |
| Phiên bản | Number, chỉ xem | Bắt đầu từ 1, tăng 1 mỗi lần cập nhật | — | |
| Trạng thái báo giá | Chỉ xem | Còn hiệu lực, Hết hiệu lực, Đã đóng, Được chấp nhận | — | |
| Phản hồi của xưởng | Chỉ xem | Chưa phản hồi, Đã báo giá, Không nhận | — | |
| Lý do không nhận | Chọn một | Không làm được mẫu này; Không đủ năng lực cho số lượng; Không kịp thời gian; Khác | Có (khi Không nhận) | |
| Ghi chú lý do | Text; length 300 | Bắt buộc khi chọn Khác | Có điều kiện | |

#### Error Messages

| Code | Condition | Message | Display | Flow |
|---|---|---|---|---|
| ERR-UC19-01 | Dữ liệu báo giá không hợp lệ | "Thông tin báo giá chưa hợp lệ. Vui lòng kiểm tra các trường được đánh dấu." | Inline | Alt-03 |
| ERR-UC19-02 | Yêu cầu đã đóng nhận báo giá | "Yêu cầu này đã đóng nhận báo giá nên không thể gửi hoặc cập nhật báo giá." | Toast | Alt-04 |
| ERR-UC19-03 | Gian hàng đang Tạm ngừng | "Gian hàng đang tạm ngừng nên không thể gửi hoặc cập nhật báo giá. Bạn vẫn có thể từ chối yêu cầu." | Toast | Alt-05 |
| ERR-UC19-04 | Đã phản hồi | "Bạn đã phản hồi yêu cầu này nên không thể phản hồi lại. Nếu đã báo giá, hãy dùng Cập nhật báo giá." | Toast | Alt-06 |
| ERR-UC19-05 | Không có quyền | "Bạn không có quyền thực hiện thao tác này." | Trang lỗi | Alt-09 |
| ERR-UC19-06 | Yêu cầu cập nhật giá đã đóng | "Yêu cầu cập nhật giá này đã đóng nên không thể cập nhật giá." | Toast | Alt-11 |

#### Notifications

| Template ID | Trigger (step) | Channel | Recipient | Title | Body |
|---|---|---|---|---|---|
| NT-UC19-01 | 6 | In-app | Buyer sở hữu yêu cầu | Có báo giá mới | "Xưởng {{workshopName}} đã báo giá cho yêu cầu {{requestCode}}: {{total}} đồng, hiệu lực đến {{validUntil}}." |
| NT-UC19-02 | Alt-01 bước 3 | In-app | Buyer sở hữu yêu cầu | Báo giá đã được cập nhật | "Xưởng {{workshopName}} vừa cập nhật báo giá cho yêu cầu {{requestCode}} (phiên bản {{version}}). Vui lòng xem lại trước khi chấp nhận." |
| NT-UC19-03 | Alt-02 bước 3, bước 4 | In-app | Buyer sở hữu yêu cầu | Yêu cầu không được xưởng nhận | Gửi riêng: "Xưởng {{workshopName}} không nhận yêu cầu {{requestCode}}: {{reason}}." Gửi chung (khi tất cả xưởng đã từ chối): "Tất cả xưởng đã từ chối yêu cầu {{requestCode}}." |
| NT-UC19-04 | Alt-07 bước 3 | In-app | Buyer sở hữu yêu cầu | Xưởng đã cập nhật giá | "Xưởng {{workshopName}} đã cập nhật giá cho yêu cầu {{requestCode}}, hiệu lực đến {{validUntil}}." |
| NT-UC19-05 | Alt-08 bước 2; Alt-10 bước 2 | In-app | Buyer sở hữu yêu cầu | Xưởng không cập nhật giá | "Xưởng {{workshopName}} không cập nhật giá cho yêu cầu {{requestCode}} ({{reason}}). Bạn có thể tạo yêu cầu mới." ({{reason}}: "đã từ chối" hoặc "quá 3 ngày chưa trả lời") |

#### Business Rules

| ID | Rule | Source |
|---|---|---|
| BR-1 | Chỉ xưởng nằm trong danh sách nhận (chốt lúc gửi) được xem, báo giá hoặc từ chối yêu cầu; gian hàng phải đang hoạt động khi gửi hoặc cập nhật báo giá. | Overview 3.4 |
| BR-2 | Báo giá cho toàn bộ số lượng của yêu cầu, một xưởng làm cả đơn; không báo giá một phần, không chia đơn. | Overview mục 1; Đề bài |
| BR-3 | Mỗi xưởng một báo giá cho mỗi yêu cầu; sửa bằng cách cập nhật thành phiên bản mới, không tạo báo giá thứ hai. | Overview 3.4 |
| BR-4 | Báo giá gồm đơn giá, phí giao (theo hình thức nhận hàng Buyer đã chọn, bằng 0 khi nhận tại xưởng), thời gian hoàn thành, tỷ lệ cọc 0%, 30% hoặc 50% do xưởng chọn, hiệu lực. Tổng giá trị và số tiền cọc do hệ thống tính. | Overview 3.4; Chốt 2026-11-18 |
| BR-5 | Hiệu lực báo giá bắt buộc nhập ở mọi lần gửi hoặc cập nhật, để xưởng không chịu rủi ro biến động giá nguyên vật liệu và nhân công. Hiệu lực hết lúc 23:59 ngày chọn; chọn từ ngày mai đến tối đa 90 ngày. Không bắt buộc sau hạn của yêu cầu, để xưởng không phải giữ giá quá dài; hiệu lực sớm hơn hạn của yêu cầu thì giao diện cảnh báo xưởng "Báo giá sẽ hết hiệu lực trước khi yêu cầu đóng nhận báo giá". | Chốt 2026-11-18; ngưỡng 90 ngày chờ chốt G-05; quan hệ với hạn chờ chốt G-11 |
| BR-6 | Xưởng chỉ gửi hoặc cập nhật báo giá đến hết ngày hạn của yêu cầu, khi báo giá chưa được chấp nhận. Ngoại lệ: trả lời Yêu cầu cập nhật giá trong 3 ngày, không phụ thuộc hạn của yêu cầu. | Chốt 2026-11-18 |
| BR-7 | Cập nhật báo giá tạo phiên bản mới thay thế phiên bản cũ; Buyer chỉ chấp nhận đúng phiên bản đang xem. Lịch sử phiên bản được giữ. | Overview 3.4 |
| BR-8 | Xưởng đang Tạm ngừng không gửi hoặc cập nhật báo giá, chỉ từ chối được; báo giá đã gửi trước đó vẫn được chấp nhận. | Chốt 2026-11-18; UC-13 BR-4 (BRD 02) |
| BR-9 | Phản hồi của xưởng (Chưa phản hồi, Đã báo giá, Không nhận) lưu riêng với trạng thái yêu cầu. Không nhận phải có lý do và không đổi sang báo giá được; muốn đặt tiếp thì Buyer tạo yêu cầu mới. | Overview 3.4; Nhóm đề xuất (danh sách lý do; không đổi phản hồi) |
| BR-10 | Báo giá kín: xưởng chỉ thấy báo giá của mình, không thấy xưởng khác báo hay không báo giá. | Overview 3.4 |
| BR-11 | Yêu cầu cập nhật giá: xưởng có 3 ngày để cập nhật (hiệu lực mới bắt buộc) hoặc từ chối; quá 3 ngày hoặc từ chối thì báo giá giữ Hết hiệu lực và yêu cầu giữ nguyên trạng thái. | Chốt 2026-11-18; thời hạn 3 ngày chờ chốt G-06 |
| BR-12 | Xưởng không xem thông tin liên hệ của Buyer cho đến khi báo giá được chấp nhận (UC-18, BR-8). | Nhóm đề xuất |
| BR-13 | Thao tác hiển thị theo tình trạng, Backend vẫn kiểm tra lại: Chưa phản hồi và còn hạn: Gửi báo giá, Không nhận; Đã báo giá và còn hạn: Xem, Cập nhật báo giá; Đã báo giá và hết hạn: chỉ Xem; Đã Không nhận: chỉ Xem lý do; Buyer yêu cầu cập nhật giá còn hạn trả lời: Cập nhật giá, Từ chối cập nhật (kể cả khi yêu cầu đã hết hạn hoặc Hết hạn); các trường hợp còn lại của yêu cầu Đã chấp nhận, Đã hủy hoặc Hết hạn: chỉ Xem. | Nhóm đề xuất |
| BR-14 | Gửi riêng: xưởng Không nhận thì yêu cầu Hết hạn ngay. Gửi chung: xưởng Không nhận chỉ cập nhật trong danh sách; yêu cầu chỉ Hết hạn khi tất cả xưởng nhận đều Không nhận. | Nhóm đề xuất |

#### Acceptance Criteria

| ID | Given | When | Then | Covers |
|---|---|---|---|---|
| UC-19-AC-01 | Xưởng nhận yêu cầu 500 giỏ, nhận tại xưởng | Nhập đơn giá 85.000, phí giao 0, 21 ngày, cọc 30%, hiệu lực 2026-12-20, chọn Gửi báo giá | Tổng giá trị 42.500.000 đồng, tiền cọc 12.750.000 đồng; báo giá Còn hiệu lực phiên bản 1; yêu cầu sang Đang nhận báo giá; Buyer nhận NT-UC19-01 | Main Flow, BR-4 |
| UC-19-AC-02 | Báo giá phiên bản 1 chưa chấp nhận, yêu cầu còn trong hạn | Xưởng cập nhật đơn giá và gửi | Phiên bản 2 thay phiên bản 1; Buyer nhận NT-UC19-02; lịch sử còn phiên bản 1 | Alt-01, BR-7 |
| UC-19-AC-03 | Yêu cầu gửi chung còn trong hạn, 3 xưởng nhận | Một xưởng chọn Không nhận với lý do "Không kịp thời gian" | Phản hồi Không nhận chỉ cập nhật trong danh sách; Buyer không nhận thông báo; xưởng đó không nhận nhắc hạn và không báo giá lại được | Alt-02, BR-9, BR-14 |
| UC-19-AC-04 | Yêu cầu gửi riêng còn trong hạn | Xưởng Không nhận | Yêu cầu chuyển Hết hạn ngay, lý do "Xưởng không nhận"; Buyer nhận NT-UC19-03 | Alt-02, BR-14 |
| UC-19-AC-05 | Đơn giá 0, hoặc cọc 40%, hoặc để trống hiệu lực | Chọn Gửi báo giá | Hiển thị ERR-UC19-01 tại trường sai; báo giá chưa lưu | Alt-03, BR-4, BR-5 |
| UC-19-AC-06 | Nhận tại xưởng | Nhập phí giao 50.000 | Hiển thị ERR-UC19-01 | Alt-03, BR-4 |
| UC-19-AC-07 | Yêu cầu gửi chung hạn 2026-11-25 | Xưởng chọn hiệu lực 2026-11-24, rồi hiệu lực hôm nay, rồi hiệu lực sau 100 ngày | Ngày 2026-11-24 gửi được kèm cảnh báo hết hiệu lực trước khi yêu cầu đóng; hôm nay và 100 ngày bị ERR-UC19-01 | BR-5 |
| UC-19-AC-08 | Yêu cầu đã đến hạn nhận báo giá | Xưởng chưa báo giá chọn Gửi báo giá | Hiển thị ERR-UC19-02 | Alt-04, BR-6 |
| UC-19-AC-09 | Yêu cầu đã Đã chấp nhận hoặc Đã hủy | Xưởng gửi hoặc cập nhật báo giá | Hiển thị ERR-UC19-02 | Alt-04 |
| UC-19-AC-10 | Xưởng chuyển Tạm ngừng | Gửi hoặc cập nhật báo giá; rồi chọn Không nhận | Gửi và cập nhật bị ERR-UC19-03; Không nhận thực hiện được | Alt-05, BR-8 |
| UC-19-AC-11 | Xưởng Tạm ngừng sau khi đã gửi báo giá | Buyer chấp nhận báo giá đó | Chấp nhận thành công (UC-20) | BR-8 |
| UC-19-AC-12 | Xưởng đã có báo giá | Gửi báo giá mới thay vì Cập nhật | Hiển thị ERR-UC19-04 | Alt-06, BR-3 |
| UC-19-AC-13 | Báo giá Hết hiệu lực, Buyer đã Yêu cầu cập nhật giá | Xưởng cập nhật giá với hiệu lực mới | Phiên bản mới Còn hiệu lực; yêu cầu đang Hết hạn quay về Đang nhận báo giá; Buyer nhận NT-UC19-04 | Alt-07, BR-6, BR-11 |
| UC-19-AC-14 | Yêu cầu cập nhật giá tạo cách đây 3 ngày, xưởng chưa trả lời | Đến 3 ngày | Báo giá giữ Hết hiệu lực; Buyer nhận NT-UC19-05; không còn nút Yêu cầu cập nhật giá | Alt-08, BR-11 |
| UC-19-AC-15 | Xưởng không thuộc danh sách nhận, Buyer hoặc Admin | Mở yêu cầu hoặc gửi báo giá | Hiển thị ERR-UC19-05 | Alt-09, BR-1 |
| UC-19-AC-16 | Ba xưởng cùng báo giá | Xưởng A mở yêu cầu | Chỉ thấy báo giá của mình, không thấy xưởng B, C | BR-10 |
| UC-19-AC-17 | Yêu cầu Giao tận nơi chưa được chấp nhận | Xưởng mở yêu cầu | Chỉ thấy địa chỉ đến quận/huyện; không thấy tên, số điện thoại, địa chỉ chi tiết | BR-12 |
| UC-19-AC-18 | Yêu cầu 300 chiếc | Xưởng thử báo giá cho 100 chiếc | Không có chỗ nhập số lượng; báo giá áp cho toàn bộ số lượng | BR-2 |
| UC-19-AC-19 | Yêu cầu gửi chung có 3 xưởng nhận, không báo giá nào | Cả 3 xưởng chọn Không nhận | Sau xưởng cuối, yêu cầu Hết hạn, lý do "Tất cả xưởng không nhận"; Buyer nhận NT-UC19-03 một lần | Alt-02, BR-14 |
| UC-19-AC-20 | Xưởng đã Không nhận, hoặc đã báo giá | Mở yêu cầu; hoặc gửi request cũ | Giao diện ẩn Gửi báo giá, Không nhận (xưởng đã báo giá còn Cập nhật báo giá nếu còn hạn); request cũ bị ERR-UC19-04 | Alt-06, BR-13 |
| UC-19-AC-21 | Yêu cầu cập nhật giá quá 3 ngày | Xưởng cập nhật giá | Hiển thị ERR-UC19-06; báo giá giữ Hết hiệu lực | Alt-11, BR-11 |
| UC-19-AC-22 | Yêu cầu cập nhật giá còn hạn | Xưởng chọn Từ chối cập nhật | Báo giá giữ Hết hiệu lực; yêu cầu giữ trạng thái; Buyer nhận NT-UC19-05 | Alt-10, BR-11 |

### UC-20 — So sánh và chấp nhận báo giá

#### Detail

| Field | Content |
|---|---|
| Use case ID | UC-20 |
| Description | Buyer so sánh các báo giá của yêu cầu, chấp nhận một báo giá còn hiệu lực, thanh toán cọc (nếu có) để tạo đơn đặt riêng; khi báo giá hết hiệu lực mà vẫn muốn mua thì Yêu cầu cập nhật giá. Hệ thống tự cho báo giá hết hiệu lực và nhắc Buyer trước hạn. |
| Actors | Buyer (chủ yêu cầu) |
| Channel | Khu mua hàng |
| Preconditions | • Đã đăng nhập, là chủ yêu cầu<br>• Yêu cầu ở Đang nhận báo giá (có ít nhất một báo giá Còn hiệu lực), hoặc Hết hạn khi dùng Yêu cầu cập nhật giá (Alt-08) |
| Expected | **Thành công:**<br>• Yêu cầu Đã chấp nhận; đơn đặt riêng ở Chờ sản xuất; các báo giá khác Đã đóng<br>**Thất bại:**<br>• Hiển thị lỗi, yêu cầu và báo giá giữ nguyên |
| Screens | • So sánh báo giá (bước 1-3)<br>• Hộp xác nhận chấp nhận (bước 4)<br>• Thanh toán cọc ZaloPay sandbox (bước 8)<br>• Chi tiết đơn đặt riêng (bước 10, UC-21) |
| Notifications | NT-UC20-01 đến NT-UC20-05 |

#### Main Flow

Trigger: Buyer mở thông báo NT-UC19-01 hoặc chọn một yêu cầu Đang nhận báo giá trong danh sách (UC-21).

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Buyer | Buyer mở yêu cầu và chọn So sánh báo giá. | Bảng so sánh hiển thị. |
| 2 | Frontend | Frontend hiển thị mọi báo giá của yêu cầu: tên xưởng, đơn giá, phí giao, tổng giá trị, thời gian hoàn thành, tỷ lệ cọc và số tiền cọc, hiệu lực, phiên bản, trạng thái; cho sắp xếp theo giá hoặc thời gian hoàn thành. | Buyer so sánh được các báo giá; xưởng Không nhận hoặc chưa phản hồi hiện kèm nhãn, không có nút chấp nhận. |
| 3 | Buyer | Buyer chọn một báo giá Còn hiệu lực và chọn Chấp nhận báo giá. | Hộp xác nhận hiển thị. |
| 4 | Frontend | Frontend nêu tên xưởng, phiên bản, tổng giá trị, số tiền cọc, phần còn lại thu khi giao hoặc nhận, hình thức nhận hàng, thời gian hoàn thành, và nhắc "Đơn do một xưởng thực hiện toàn bộ". | Buyer chọn Xác nhận chấp nhận. |
| 5 | Backend | Backend kiểm tra báo giá còn hiệu lực, đúng phiên bản Buyer đang xem, yêu cầu chưa chốt hoặc hủy. Nếu nhiều người cùng chấp nhận thì chỉ một thao tác thành công. | Việc chấp nhận được khóa cho đúng một báo giá. |
| 6 | Backend | Backend chuyển yêu cầu sang Đã chấp nhận, báo giá được chọn sang Được chấp nhận, các báo giá còn lại sang Đã đóng; tạo đơn đặt riêng ở Chờ sản xuất, chụp lại yêu cầu và báo giá, sinh mã đơn. | Đơn có mã DR; trạng thái thanh toán Chưa thanh toán. |
| 7 | Backend | Backend thông báo ngay xưởng được chọn và các xưởng còn lại, không chờ thanh toán cọc. | NT-UC20-02 gửi xưởng được chọn (nêu số tiền cọc và trạng thái thanh toán); NT-UC20-03 gửi các xưởng khác đã nhận yêu cầu. |
| 8 | Backend | Nếu tỷ lệ cọc lớn hơn 0%, Backend tạo giao dịch cọc và chuyển Buyer sang cổng ZaloPay (sandbox); không đặt hạn thanh toán. Cọc 0% thì bỏ qua bước này và bước 9 (Alt-07). | Buyer thanh toán cọc. |
| 9 | Backend | Khi cổng báo thanh toán cọc thành công, Backend ghi nhận trạng thái thanh toán Đã đặt cọc và thông báo xưởng. | NT-UC20-05 gửi xưởng được chọn. |
| 10 | Frontend | Frontend mở chi tiết đơn đặt riêng. | Hiển thị mã đơn, xưởng, tổng giá trị, số cọc, trạng thái đơn Chờ sản xuất và trạng thái thanh toán (UC-21). |

Outcome: Đơn đặt riêng ở Chờ sản xuất; cọc đã thanh toán nếu có.

#### Alternative Flows

##### Alt-01 — Chưa có báo giá nào

- At step: 1 · Trigger: Yêu cầu Đã gửi, chưa xưởng nào báo giá.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Frontend | Frontend hiện "Chưa có xưởng nào báo giá" kèm hạn và danh sách xưởng nhận với phản hồi của từng xưởng. | Không có nút chấp nhận. |

Resume: Kết thúc.

##### Alt-02 — Yêu cầu đã đóng nhận báo giá

- At step: 1 · Trigger: Yêu cầu đã đến hạn nhận báo giá mà còn báo giá Còn hiệu lực.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Frontend | Frontend hiện nhãn "Chờ Buyer lựa chọn" và danh sách báo giá hiện có. | Buyer vẫn chọn và chấp nhận báo giá còn hiệu lực. |

Resume: Tiếp tục bước 3.

##### Alt-03 — ERR-UC20-01 — Báo giá đã hết hiệu lực

- At step: 5 · Trigger: Báo giá hết hiệu lực trước khi Buyer xác nhận.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Backend | Backend từ chối chấp nhận. | Hiển thị ERR-UC20-01; nút Chấp nhận bị khóa, hiện nút Yêu cầu cập nhật giá (Alt-08). |

Resume: Quay lại bước 2 hoặc Alt-08.

##### Alt-04 — ERR-UC20-02 — Báo giá có phiên bản mới

- At step: 5 · Trigger: Xưởng cập nhật báo giá sau khi Buyer mở nhưng trước khi xác nhận.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Backend | Backend từ chối chấp nhận phiên bản cũ. | Hiển thị ERR-UC20-02 và tải lại báo giá phiên bản mới. |

Resume: Quay lại bước 2; Buyer xem phiên bản mới rồi chấp nhận lại.

##### Alt-05 — ERR-UC20-03 — Yêu cầu đã được chốt hoặc đã đóng

- At step: 5 · Trigger: Yêu cầu đã Đã chấp nhận (do thao tác khác cùng lúc), Đã hủy hoặc Hết hạn.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Backend | Backend từ chối; chỉ thao tác đầu tiên thành công. | Hiển thị ERR-UC20-03; dữ liệu không đổi. |

Resume: Kết thúc.

##### Alt-06 — ERR-UC20-04 — Thanh toán cọc không thành công

- At step: 8 · Trigger: Buyer hủy cổng, giao dịch thất bại hoặc cổng không phản hồi.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Backend | Backend giữ nguyên đơn ở Chờ sản xuất, trạng thái thanh toán Chưa thanh toán; đơn được giữ, không có hạn thanh toán. | Hiển thị ERR-UC20-04 kèm nút Thanh toán lại. |
| 2 | Buyer | Buyer chọn Thanh toán lại từ chi tiết đơn (UC-21). | Quay lại bước 8. |

Resume: Thanh toán thành công thì tiếp tục bước 9. Xưởng đã biết được chọn từ bước 7 nhưng chỉ bắt đầu sản xuất khi cọc đã thanh toán (UC-22, BR-3). Buyer muốn dừng sớm thì hủy đơn qua UC-33.

##### Alt-07 — Tỷ lệ cọc 0%

- At step: 8 · Trigger: Báo giá được chọn có tỷ lệ cọc 0%.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Backend | Backend không tạo giao dịch cọc; toàn bộ giá trị thu khi giao hoặc nhận. | Trạng thái thanh toán Chưa thanh toán; đơn sẵn sàng cho xưởng bắt đầu sản xuất. |

Resume: Tiếp tục bước 10.

##### Alt-08 — Yêu cầu cập nhật giá

- At step: 2 · Trigger: Báo giá Hết hiệu lực mà Buyer vẫn muốn mua và chọn Yêu cầu cập nhật giá.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Backend | Backend kiểm tra yêu cầu chưa Đã chấp nhận hoặc Đã hủy, báo giá đang Hết hiệu lực và chưa có Yêu cầu cập nhật giá đang chờ; ghi nhận yêu cầu với hạn trả lời 3 ngày. Yêu cầu giữ nguyên trạng thái trong thời gian chờ (đang Hết hạn thì vẫn Hết hạn). | Báo giá hiện nhãn "Đang chờ xưởng cập nhật giá"; yêu cầu Hết hạn hiện nhãn "Đang chờ cập nhật giá". |
| 2 | Backend | Backend thông báo xưởng. | NT-UC20-04 được gửi. |

Resume: Xưởng cập nhật giá (UC-19, Alt-07) thì báo giá Còn hiệu lực trở lại, yêu cầu Hết hạn quay về Đang nhận báo giá và Buyer quay lại bước 2. Xưởng từ chối hoặc quá 3 ngày thì Yêu cầu cập nhật giá đóng và yêu cầu giữ trạng thái (UC-19, Alt-08, Alt-10).

##### Alt-09 — Báo giá tự hết hiệu lực

- At step: Sau bước 2 (hệ thống tự chạy) · Trigger: Qua 23:59 ngày hiệu lực mà báo giá chưa được chấp nhận.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Backend | Backend chuyển báo giá sang Hết hiệu lực và khóa nút Chấp nhận; hiện nút Yêu cầu cập nhật giá cho Buyer. | Báo giá không chấp nhận được. |
| 2 | Backend | Nếu yêu cầu đã đóng nhận báo giá và đây là báo giá Còn hiệu lực cuối cùng thì yêu cầu chuyển Hết hạn (UC-18, Alt-13). | Yêu cầu Hết hạn nếu không còn báo giá nào Còn hiệu lực. |

Resume: Kết thúc.

##### Alt-10 — Nhắc Buyer trước khi báo giá hết hiệu lực

- At step: Sau bước 2 (hệ thống tự chạy) · Trigger: Còn 24 giờ trước khi báo giá hết hiệu lực, báo giá Còn hiệu lực và Buyer chưa chốt.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Backend | Backend nhắc Buyer cho mỗi báo giá Còn hiệu lực; mỗi báo giá một lần nhắc. | NT-UC20-01 được gửi. |

Resume: Kết thúc. Báo giá có hiệu lực dưới 24 giờ kể từ lúc gửi thì không nhắc.

##### Alt-11 — ERR-UC20-05 — Không có quyền

- At step: 1 · Trigger: Người dùng khác, xưởng hoặc Admin thao tác chấp nhận hoặc Yêu cầu cập nhật giá trên yêu cầu của Buyer.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Backend | Backend từ chối thao tác. | Hiển thị ERR-UC20-05; dữ liệu không đổi. |

Resume: Kết thúc.

##### Alt-12 — ERR-UC20-06 — Không yêu cầu cập nhật giá được

- At step: Alt-08 bước 1 · Trigger: Báo giá chưa Hết hiệu lực, đã có Yêu cầu cập nhật giá đang chờ, hoặc yêu cầu đã Đã chấp nhận hoặc Đã hủy.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Backend | Backend từ chối. | Hiển thị ERR-UC20-06. |

Resume: Kết thúc.

#### Data Fields

| Field | Format | Validation | Required | Ghi chú |
|---|---|---|---|---|
| Danh sách báo giá | Bảng, chỉ xem | Mọi báo giá của yêu cầu; xưởng, đơn giá, phí giao, tổng giá trị, thời gian hoàn thành, tỷ lệ cọc, số tiền cọc, hiệu lực, phiên bản, trạng thái | — | Sắp xếp theo giá hoặc thời gian hoàn thành |
| Báo giá được chọn | Chọn một | Còn hiệu lực, đúng phiên bản đang xem | Có | |
| Số tiền cọc | Number, chỉ xem | Tổng giá trị nhân tỷ lệ cọc, làm tròn đến đồng | — | Thanh toán qua ZaloPay sandbox |
| Phần còn lại | Number, chỉ xem | Tổng giá trị trừ số tiền cọc | — | Thu khi giao hoặc nhận (UC-22) |
| Mã đơn | Text | Hệ thống sinh, duy nhất | — | Ví dụ: "DR-2026-0001" |
| Trạng thái thanh toán | Chỉ xem | Chưa thanh toán, Đã đặt cọc, Đã thanh toán, Đã hoàn tiền | — | |
| Hạn trả lời cập nhật giá | Date-time, chỉ xem | 3 ngày kể từ lúc Buyer yêu cầu | — | Hiện cho cả Buyer và xưởng |

#### Error Messages

| Code | Condition | Message | Display | Flow |
|---|---|---|---|---|
| ERR-UC20-01 | Báo giá đã hết hiệu lực | "Báo giá này đã hết hiệu lực. Bạn có thể yêu cầu xưởng cập nhật giá." | Toast | Alt-03 |
| ERR-UC20-02 | Báo giá có phiên bản mới | "Xưởng vừa cập nhật báo giá này. Vui lòng xem lại phiên bản mới trước khi chấp nhận." | Toast | Alt-04 |
| ERR-UC20-03 | Yêu cầu đã chốt hoặc đã đóng | "Yêu cầu này đã được chốt hoặc đã đóng nên không thể chấp nhận báo giá." | Toast | Alt-05 |
| ERR-UC20-04 | Thanh toán cọc không thành công | "Chưa thanh toán được tiền cọc. Đơn đã được tạo, bạn có thể thanh toán lại trong chi tiết đơn." | Toast | Alt-06 |
| ERR-UC20-05 | Không có quyền | "Bạn không có quyền thực hiện thao tác này." | Trang lỗi | Alt-11 |
| ERR-UC20-06 | Không yêu cầu cập nhật giá được | "Không thể yêu cầu cập nhật giá cho báo giá này." | Toast | Alt-12 |

#### Notifications

| Template ID | Trigger (step) | Channel | Recipient | Title | Body |
|---|---|---|---|---|---|
| NT-UC20-01 | Alt-10 bước 1 | In-app | Buyer sở hữu yêu cầu | Báo giá sắp hết hiệu lực | "Báo giá của xưởng {{workshopName}} cho yêu cầu {{requestCode}} hết hiệu lực lúc {{validUntil}}. Hãy chốt trước hạn để giữ mức giá đã chào." |
| NT-UC20-02 | 7 | In-app | Xưởng được chọn | Báo giá được chấp nhận | "Buyer đã chấp nhận báo giá cho yêu cầu {{requestCode}}. Đơn {{orderCode}} ở trạng thái Chờ sản xuất. Tiền cọc: {{depositAmount}} đồng ({{paymentStatus}}). Chỉ bắt đầu sản xuất khi Buyer đã đặt cọc (nếu có)." |
| NT-UC20-03 | 7 | In-app | Các xưởng còn lại đã nhận yêu cầu | Yêu cầu đã chốt | "Yêu cầu {{requestCode}} đã được chốt. Báo giá của bạn đã đóng." |
| NT-UC20-04 | Alt-08 bước 2 | In-app | Xưởng có báo giá hết hiệu lực | Buyer yêu cầu cập nhật giá | "Buyer muốn cập nhật giá cho yêu cầu {{requestCode}}. Vui lòng trả lời trước {{updateDeadline}}." |
| NT-UC20-05 | 9 | In-app | Xưởng được chọn | Đã nhận tiền cọc | "Buyer đã đặt cọc {{depositAmount}} đồng cho đơn {{orderCode}}. Bạn có thể bắt đầu sản xuất." |

#### Business Rules

| ID | Rule | Source |
|---|---|---|
| BR-1 | Báo giá kín: Buyer thấy mọi báo giá của yêu cầu; xưởng chỉ thấy báo giá của mình; xưởng không được chọn chỉ thấy "Đã đóng". | Overview 3.4 |
| BR-2 | Chỉ chủ yêu cầu chấp nhận; chấp nhận cần Buyer xác nhận qua hộp thoại tóm tắt. | Overview 3.4 |
| BR-3 | Chỉ chấp nhận được báo giá Còn hiệu lực và đúng phiên bản Buyer đang xem; phiên bản mới thì Buyer phải xem lại. | Overview 3.4; Chốt 2026-11-18 |
| BR-4 | Chấp nhận thành công đúng một lần dù nhiều thao tác cùng lúc; chấp nhận xong thì các báo giá còn lại tự Đã đóng. | Overview 3.4 |
| BR-5 | Một đơn do đúng một xưởng làm toàn bộ số lượng; Buyer chọn một báo giá, không chia đơn. | Overview mục 1 |
| BR-6 | Đơn đặt riêng chụp lại yêu cầu và báo giá lúc chấp nhận; trạng thái đơn khởi đầu là Chờ sản xuất, thanh toán Chưa thanh toán. | Overview mục 4 |
| BR-7 | Cọc 0%, 30% hoặc 50% do xưởng chọn; cọc trả online qua ZaloPay sandbox (dự phòng VNPay sandbox) ngay sau khi chấp nhận; phần còn lại thu khi giao hoặc nhận hàng, xưởng xác nhận đã thu (UC-22). | Overview 3.4; Chốt 2026-11-18 |
| BR-8 | Đơn tạo ngay lúc chấp nhận. Có cọc thì Buyer thanh toán cọc qua cổng và được thanh toán lại nhiều lần; xưởng chỉ bắt đầu sản xuất khi cọc đã thanh toán. Chưa đặt hạn thanh toán cọc: đơn chưa thanh toán giữ ở Chờ sản xuất cho đến khi Buyer thanh toán hoặc hủy qua UC-33. Cọc 0% không cần thanh toán trước. | Nhóm đề xuất; chưa có hạn cọc, xem G-07 |
| BR-9 | Báo giá đã gửi trước khi xưởng Tạm ngừng vẫn được chấp nhận; xưởng Tạm ngừng không gửi hoặc cập nhật báo giá. | Overview 3.4; UC-13 BR-4 (BRD 02) |
| BR-10 | Đến hạn nhận báo giá thì xưởng ngừng gửi và cập nhật, nhưng Buyer vẫn chốt báo giá còn hiệu lực. | Chốt 2026-11-18 |
| BR-11 | Quá hiệu lực thì báo giá Hết hiệu lực và khóa nút chấp nhận; Buyer vẫn muốn mua thì chọn Yêu cầu cập nhật giá để xưởng xác nhận giá mới. Mỗi báo giá chỉ có một Yêu cầu cập nhật giá đang chờ. | Chốt 2026-11-18 |
| BR-12 | Nhắc Buyer trước 24 giờ khi báo giá sắp hết hiệu lực mà chưa chốt; mỗi báo giá một lần nhắc. | Chốt 2026-11-18 |
| BR-13 | Admin không xem, không can thiệp báo giá và đơn. Hủy đơn đặt riêng và hoàn cọc thuộc UC-33. | Overview mục 1 |

#### Acceptance Criteria

| ID | Given | When | Then | Covers |
|---|---|---|---|---|
| UC-20-AC-01 | Yêu cầu có 3 báo giá Còn hiệu lực | Buyer mở So sánh báo giá | Thấy đủ 3 báo giá với đơn giá, phí giao, tổng, thời gian hoàn thành, cọc, hiệu lực; sắp xếp được theo giá | Main Flow, BR-1 |
| UC-20-AC-02 | Báo giá tổng 42.500.000 đồng, cọc 30%, nhận tại xưởng | Buyer chấp nhận và xác nhận | Hộp xác nhận nêu cọc 12.750.000 đồng và phần còn lại 29.750.000 đồng; yêu cầu Đã chấp nhận; đơn Chờ sản xuất; chuyển sang ZaloPay sandbox | Main Flow, BR-6, BR-7 |
| UC-20-AC-03 | Cọc đã tạo giao dịch | Thanh toán thành công | Trạng thái thanh toán Đã đặt cọc; xưởng nhận NT-UC20-05 | Main Flow, BR-7 |
| UC-20-AC-04 | Yêu cầu có 3 báo giá | Buyer chấp nhận một báo giá rồi đóng cổng thanh toán, chưa đặt cọc | Hai báo giá còn lại Đã đóng; xưởng được chọn vẫn nhận NT-UC20-02 ngay (ghi trạng thái Chưa thanh toán); hai xưởng còn lại nhận NT-UC20-03 và chỉ thấy "Đã đóng" | BR-1, BR-4 |
| UC-20-AC-05 | Hai Buyer cùng một tài khoản chấp nhận hai báo giá cùng lúc | Gửi hai thao tác | Chỉ một thành công; thao tác còn lại hiển thị ERR-UC20-03 | Alt-05, BR-4 |
| UC-20-AC-06 | Yêu cầu Đã gửi chưa có báo giá | Buyer mở So sánh báo giá | Hiện "Chưa có xưởng nào báo giá"; không có nút chấp nhận | Alt-01 |
| UC-20-AC-07 | Yêu cầu đã đóng nhận báo giá, còn báo giá hiệu lực | Buyer chấp nhận | Chấp nhận thành công | Alt-02, BR-10 |
| UC-20-AC-08 | Báo giá hết hiệu lực lúc Buyer đang xem | Chọn Xác nhận chấp nhận | Hiển thị ERR-UC20-01; nút chấp nhận khóa; hiện Yêu cầu cập nhật giá | Alt-03, BR-11 |
| UC-20-AC-09 | Xưởng cập nhật báo giá khi Buyer đang xem phiên bản 1 | Buyer xác nhận chấp nhận | Hiển thị ERR-UC20-02; tải phiên bản 2; chấp nhận phiên bản 2 thành công | Alt-04, BR-3 |
| UC-20-AC-10 | Yêu cầu đã Đã hủy | Buyer chấp nhận | Hiển thị ERR-UC20-03 | Alt-05 |
| UC-20-AC-11 | Buyer đóng cổng ZaloPay | Quay lại | Hiển thị ERR-UC20-04; đơn giữ Chờ sản xuất, thanh toán Chưa thanh toán; có Thanh toán lại; Buyer thanh toán lại được bất cứ lúc nào | Alt-06, BR-8 |
| UC-20-AC-12 | Báo giá cọc 0% | Buyer chấp nhận | Không chuyển sang cổng thanh toán; đơn tạo ngay; thanh toán Chưa thanh toán | Alt-07 |
| UC-20-AC-13 | Báo giá Hết hiệu lực | Buyer chọn Yêu cầu cập nhật giá | Báo giá hiện "Đang chờ xưởng cập nhật giá"; xưởng nhận NT-UC20-04; yêu cầu Hết hạn giữ nguyên trạng thái, hiện nhãn "Đang chờ cập nhật giá"; xưởng gửi giá mới thì yêu cầu về Đang nhận báo giá | Alt-08, BR-11 |
| UC-20-AC-14 | Báo giá đang chờ cập nhật giá | Buyer chọn Yêu cầu cập nhật giá lần nữa, hoặc báo giá còn hiệu lực | Hiển thị ERR-UC20-06 | Alt-12, BR-11 |
| UC-20-AC-15 | Báo giá hiệu lực đến 2026-12-20 | Qua 23:59 ngày 2026-12-20 | Báo giá Hết hiệu lực; nút Chấp nhận bị khóa | Alt-09, BR-11 |
| UC-20-AC-16 | Báo giá còn hiệu lực 24 giờ nữa, Buyer chưa chốt | Đến thời điểm nhắc | Buyer nhận NT-UC20-01 một lần cho báo giá này | Alt-10, BR-12 |
| UC-20-AC-17 | Xưởng Tạm ngừng sau khi gửi báo giá | Buyer chấp nhận báo giá | Thành công; đơn tạo bình thường | BR-9 |
| UC-20-AC-18 | Người dùng khác, xưởng hoặc Admin | Chấp nhận hoặc Yêu cầu cập nhật giá | Hiển thị ERR-UC20-05; dữ liệu không đổi | Alt-11, BR-2 |
| UC-20-AC-19 | Yêu cầu đã chấp nhận một báo giá | Buyer tìm cách chọn thêm xưởng khác hoặc chia đơn | Không có thao tác này | BR-5 |
| UC-20-AC-20 | Đơn đã tạo; xưởng sửa báo giá hoặc sản phẩm mẫu bị đổi sau đó | Mở chi tiết đơn | Đơn vẫn hiện yêu cầu và báo giá lúc chấp nhận | BR-6 |

### UC-21 — Xem yêu cầu, báo giá và đơn đặt riêng

#### Detail

| Field | Content |
|---|---|
| Use case ID | UC-21 |
| Description | Buyer xem danh sách và chi tiết yêu cầu báo giá, báo giá nhận được và đơn đặt riêng của mình. Xưởng xem danh sách yêu cầu nhận, báo giá của mình và đơn đặt riêng của xưởng. Mỗi bên chỉ thấy phạm vi của mình; báo giá là kín. |
| Actors | Buyer, Artisan |
| Channel | Khu mua hàng (Buyer), Kênh người bán (Artisan) |
| Preconditions | • Đã đăng nhập<br>• Buyer: có ít nhất một yêu cầu hoặc đơn; Artisan: gian hàng đã từng nhận yêu cầu hoặc đơn |
| Expected | **Thành công:**<br>• Danh sách, chi tiết hiển thị đúng phạm vi và trạng thái<br>**Thất bại:**<br>• Hiển thị lỗi, không lộ dữ liệu của người khác |
| Screens | • Danh sách yêu cầu, tab Nháp (bước 1-2)<br>• Chi tiết yêu cầu (bước 3)<br>• Danh sách và chi tiết đơn đặt riêng (bước 4) |
| Notifications | Không có |

#### Main Flow

Trigger: Buyer chọn Yêu cầu báo giá của tôi trong Khu mua hàng, hoặc Artisan chọn Yêu cầu nhận trong Kênh người bán.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Buyer hoặc Artisan | Mở danh sách yêu cầu. | Danh sách hiển thị theo thứ tự mới nhất, 10 mục mỗi trang. |
| 2 | Frontend | Hiển thị theo tab. Buyer: Nháp, Đang xử lý (Đã gửi, Đang nhận báo giá), Đã chấp nhận, Đã hủy hoặc Hết hạn. Artisan: Chưa phản hồi, Đã báo giá, Không nhận, Đã đóng (yêu cầu có Yêu cầu cập nhật giá còn hạn trả lời nằm ở tab Đã báo giá, kèm nhãn "Cần cập nhật giá", kể cả khi yêu cầu đã Hết hạn); lọc được theo mã yêu cầu. | Mỗi dòng gồm mã, nguồn, số lượng, hạn, trạng thái; Buyer thấy thêm số báo giá nhận được; Artisan thấy thêm phản hồi của mình. |
| 3 | Buyer hoặc Artisan | Chọn một yêu cầu. | Chi tiết yêu cầu hiển thị: nội dung đã gửi, hạn, nhãn trạng thái (BR-3), lịch sử trạng thái. Buyer thấy danh sách xưởng nhận với phản hồi của từng xưởng và mọi báo giá (UC-20); Artisan thấy báo giá của mình, hoặc "Đã đóng" nếu yêu cầu đã chốt xưởng khác. |
| 4 | Buyer hoặc Artisan | Chọn một đơn đặt riêng trong danh sách đơn. | Chi tiết đơn: yêu cầu và báo giá lúc chấp nhận, trạng thái đơn, trạng thái thanh toán, lịch sử, các thao tác theo UC-22 và UC-24. |

Outcome: Người dùng xem đúng yêu cầu, báo giá, đơn thuộc phạm vi của mình.

#### Alternative Flows

##### Alt-01 — Danh sách trống

- At step: 1 · Trigger: Không có yêu cầu hoặc đơn nào ở tab đang chọn.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Frontend | Frontend hiện "Chưa có yêu cầu nào" (Buyer kèm nút Đặt hàng theo yêu cầu). | Không có dòng nào. |

Resume: Kết thúc.

##### Alt-02 — Mở và tiếp tục nháp

- At step: 3 · Trigger: Buyer chọn một nháp ở tab Nháp.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Frontend | Frontend mở form với dữ liệu đã lưu (UC-18, Alt-05). | Buyer sửa, gửi hoặc xóa nháp. |

Resume: Tiếp tục theo UC-18.

##### Alt-03 — ERR-UC21-01 — Không có quyền hoặc không tồn tại

- At step: 3 hoặc 4 · Trigger: Mở yêu cầu hoặc đơn của người khác, xưởng không có trong danh sách nhận, mã không tồn tại, hoặc tài khoản Admin.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Backend | Backend từ chối, không tiết lộ yêu cầu hay đơn có tồn tại. | Hiển thị ERR-UC21-01. |

Resume: Kết thúc.

##### Alt-04 — ERR-UC21-02 — Không tải được

- At step: 1 đến 4 · Trigger: Lỗi tải dữ liệu.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Frontend | Frontend giữ trang và hiện lỗi. | Hiển thị ERR-UC21-02 và nút Thử lại. |

Resume: Thử lại, tiếp tục bước đang thực hiện.

##### Alt-05 — Xưởng không được chọn

- At step: 3 · Trigger: Xưởng đã nhận yêu cầu nhưng Buyer chấp nhận xưởng khác.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Frontend | Frontend hiện báo giá của xưởng với nhãn "Đã đóng"; không hiện xưởng được chọn, giá hay đơn. | Xưởng không biết chi tiết đối thủ. |

Resume: Kết thúc.

#### Data Fields

| Field | Format | Validation | Required | Ghi chú |
|---|---|---|---|---|
| Tab | Chọn một | Theo vai trò (xem bước 2) | — | |
| Mã tìm kiếm | Text; length 20 | Mã yêu cầu hoặc mã đơn | Không | Ví dụ: "YC-2026-0001" |
| Trang | Number | 10 mục mỗi trang | — | |
| Lịch sử trạng thái | Danh sách, chỉ xem | Trạng thái, người thực hiện, thời điểm | — | |
| Nội dung yêu cầu | Chỉ xem | Như UC-18 Data Fields | — | Xưởng thấy địa chỉ đến quận/huyện cho đến khi báo giá được chấp nhận |
| Báo giá | Chỉ xem | Như UC-19 Data Fields | — | Báo giá kín (BR-2) |
| Đơn đặt riêng | Chỉ xem | Mã, xưởng, Buyer (xưởng thấy sau khi chấp nhận), yêu cầu và báo giá lúc chấp nhận, trạng thái đơn, trạng thái thanh toán | — | |

#### Error Messages

| Code | Condition | Message | Display | Flow |
|---|---|---|---|---|
| ERR-UC21-01 | Không có quyền hoặc không tồn tại | "Không tìm thấy nội dung này hoặc bạn không có quyền xem." | Trang lỗi | Alt-03 |
| ERR-UC21-02 | Không tải được | "Chưa tải được dữ liệu. Vui lòng thử lại." | Inline | Alt-04 |

#### Business Rules

| ID | Rule | Source |
|---|---|---|
| BR-1 | Buyer chỉ thấy yêu cầu, báo giá, đơn của mình; xưởng chỉ thấy yêu cầu mình được nhận, báo giá và đơn của xưởng mình; Admin không xem. | Overview mục 1 |
| BR-2 | Báo giá kín: Buyer thấy mọi báo giá; xưởng chỉ thấy báo giá của mình; xưởng không được chọn thấy "Đã đóng". | Overview 3.4 |
| BR-3 | Trạng thái yêu cầu: Nháp, Đã gửi, Đang nhận báo giá, Đã chấp nhận, Đã hủy, Hết hạn; phản hồi của xưởng (Chưa phản hồi, Đã báo giá, Không nhận) hiển thị riêng. Nhãn hiển thị (không phải trạng thái): "Chờ báo giá" (còn nhận báo giá), "Chờ Buyer lựa chọn" (đã đóng nhận báo giá, còn báo giá hiệu lực), "Đang chờ cập nhật giá" (Hết hạn có Yêu cầu cập nhật giá đang chờ). Bên xưởng: nhãn "Cần cập nhật giá" khi Buyer đã Yêu cầu cập nhật giá và còn hạn trả lời. | Overview mục 4; Chốt 2026-11-18 |
| BR-4 | Trạng thái đơn đặt riêng: Chờ sản xuất, Đang sản xuất, Đang giao, Đã giao, Hoàn tất, Đã hủy; trạng thái thanh toán hiển thị riêng. | Overview mục 4 |
| BR-5 | Thông tin liên hệ của Buyer (tên, số điện thoại, địa chỉ chi tiết) chỉ hiện cho xưởng được chọn, sau khi báo giá được chấp nhận. | Nhóm đề xuất |
| BR-6 | Yêu cầu và đơn đã tạo giữ nguyên nội dung khi sản phẩm mẫu bị sửa hoặc ẩn, hoặc xưởng Tạm ngừng. | Overview 3.4; UC-13 BR-4 (BRD 02) |

#### Acceptance Criteria

| ID | Given | When | Then | Covers |
|---|---|---|---|---|
| UC-21-AC-01 | Buyer có 1 nháp, 2 yêu cầu Đang nhận báo giá, 1 Hết hạn | Mở danh sách yêu cầu | Mỗi tab hiện đúng yêu cầu; mới nhất lên đầu; yêu cầu hiện số báo giá | Main Flow, BR-3 |
| UC-21-AC-02 | Xưởng nhận 5 yêu cầu: 2 chưa phản hồi, 2 đã báo giá, 1 Không nhận | Mở Yêu cầu nhận | Tab hiển thị đúng phản hồi của xưởng | Main Flow, BR-3 |
| UC-21-AC-03 | Buyer chưa có yêu cầu | Mở danh sách | Hiện "Chưa có yêu cầu nào" kèm nút Đặt hàng theo yêu cầu | Alt-01 |
| UC-21-AC-04 | Buyer có nháp | Chọn nháp | Form mở với dữ liệu đã lưu | Alt-02 |
| UC-21-AC-05 | Buyer A | Mở yêu cầu của Buyer B; xưởng không được nhận mở yêu cầu; Admin mở yêu cầu | Hiển thị ERR-UC21-01 trong cả ba trường hợp | Alt-03, BR-1 |
| UC-21-AC-06 | Lỗi tải dữ liệu | Mở danh sách | Hiển thị ERR-UC21-02 và nút Thử lại | Alt-04 |
| UC-21-AC-07 | Yêu cầu có 3 xưởng báo giá, Buyer chấp nhận xưởng A | Xưởng B mở yêu cầu | Chỉ thấy báo giá của mình với nhãn "Đã đóng"; không thấy xưởng A hay giá | Alt-05, BR-2 |
| UC-21-AC-08 | Báo giá chưa được chấp nhận, Giao tận nơi | Xưởng mở yêu cầu | Chỉ thấy tỉnh/thành, quận/huyện; sau khi chấp nhận, xưởng được chọn thấy tên, số điện thoại, địa chỉ chi tiết | BR-5 |
| UC-21-AC-09 | Đơn Đang sản xuất, thanh toán Đã đặt cọc | Buyer và xưởng mở chi tiết đơn | Thấy yêu cầu và báo giá lúc chấp nhận, trạng thái đơn, trạng thái thanh toán riêng | Main Flow, BR-4 |
| UC-21-AC-10 | Sản phẩm mẫu bị Ẩn, xưởng Tạm ngừng | Mở yêu cầu và đơn | Nội dung không đổi | BR-6 |
| UC-21-AC-11 | Yêu cầu đã hết hạn nhận báo giá còn báo giá hiệu lực; yêu cầu Hết hạn có Yêu cầu cập nhật giá đang chờ | Buyer mở danh sách | Yêu cầu thứ nhất nằm tab Đang xử lý với nhãn "Chờ Buyer lựa chọn"; yêu cầu thứ hai nằm tab Hết hạn với nhãn "Đang chờ cập nhật giá" | Main Flow, BR-3 |
| UC-21-AC-12 | Yêu cầu đã Hết hạn, Buyer vừa Yêu cầu cập nhật giá cho báo giá của xưởng A | Xưởng A mở Yêu cầu nhận | Yêu cầu nằm ở tab Đã báo giá với nhãn "Cần cập nhật giá"; hết hạn trả lời hoặc đã trả lời thì nhãn mất | Main Flow, BR-3 |

### UC-22 — Cập nhật và theo dõi đơn đặt riêng

#### Detail

| Field | Content |
|---|---|
| Use case ID | UC-22 |
| Description | Xưởng cập nhật đơn đặt riêng qua các trạng thái sản xuất và giao hàng, xác nhận đã thu phần còn lại khi giao hoặc nhận. Buyer theo dõi và xác nhận đã nhận để đơn Hoàn tất; không xác nhận thì sau 7 ngày đơn tự Hoàn tất. |
| Actors | Artisan (xưởng của đơn), Buyer (chủ đơn) |
| Channel | Kênh người bán, Khu mua hàng |
| Preconditions | • Đã đăng nhập<br>• Có đơn đặt riêng chưa Hoàn tất hoặc Đã hủy |
| Expected | **Thành công:**<br>• Trạng thái đơn và thanh toán được cập nhật; bên còn lại được thông báo<br>**Thất bại:**<br>• Hiển thị lỗi, trạng thái giữ nguyên |
| Screens | • Chi tiết đơn của xưởng (bước 1-4)<br>• Chi tiết đơn của Buyer (bước 5) |
| Notifications | NT-UC22-01 đến NT-UC22-04 |

#### Main Flow

Trigger: Xưởng mở đơn đặt riêng ở Chờ sản xuất (từ NT-UC20-02, NT-UC20-05 hoặc danh sách đơn, UC-21).

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Artisan | Artisan chọn Bắt đầu sản xuất. | Yêu cầu chuyển trạng thái được gửi. |
| 2 | Backend | Backend kiểm tra cọc (nếu có) đã thanh toán, chuyển đơn sang Đang sản xuất và ghi lịch sử. | Đơn Đang sản xuất. |
| 3 | Backend | Backend ghi lịch sử và hiển thị trạng thái mới cho Buyer trong chi tiết đơn; không gửi thông báo riêng. | Buyer thấy đơn Đang sản xuất. |
| 4 | Artisan | Với đơn Giao tận nơi, khi hàng sẵn sàng, Artisan chọn Giao hàng và nhập đơn vị vận chuyển hoặc ghi chú giao hàng (nếu có). | Đơn Đang giao; NT-UC22-01 được gửi cho Buyer. |
| 5 | Artisan | Khi hàng đã giao, Artisan chọn Đã giao, rồi chọn Đã thu hoặc Chưa thu phần còn lại. | Đơn Đã giao; thanh toán Đã thanh toán nếu đã thu; NT-UC22-02 được gửi cho Buyer. |
| 6 | Buyer | Buyer mở đơn và chọn Xác nhận đã nhận. | Đơn Hoàn tất; NT-UC22-03 được gửi cho xưởng. |

Outcome: Đơn ở Hoàn tất; thanh toán Đã thanh toán nếu xưởng đã xác nhận thu phần còn lại, nếu chưa thì giữ trạng thái cũ đến khi xưởng xác nhận (Alt-05).

#### Alternative Flows

##### Alt-01 — Nhận tại xưởng

- At step: 4 · Trigger: Đơn có hình thức Nhận tại xưởng.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Artisan | Khi Buyer đến nhận, Artisan chọn Đã giao ngay từ Đang sản xuất, rồi chọn Đã thu hoặc Chưa thu phần còn lại. | Đơn chuyển thẳng Đã giao; thanh toán Đã thanh toán nếu đã thu; NT-UC22-02 được gửi cho Buyer. |

Resume: Tiếp tục bước 6.

##### Alt-02 — Tự động Hoàn tất sau 7 ngày

- At step: Sau bước 5 (hệ thống tự chạy) · Trigger: Qua 7 ngày kể từ lúc đơn Đã giao mà Buyer chưa xác nhận.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Backend | Backend chuyển đơn sang Hoàn tất. | Đơn Hoàn tất. |
| 2 | Backend | Backend thông báo Buyer và xưởng. | NT-UC22-04 được gửi. |

Resume: Kết thúc.

##### Alt-03 — ERR-UC22-01 — Chuyển trạng thái sai thứ tự

- At step: 1, 4, 5 hoặc 6 · Trigger: Chọn thao tác không đúng với trạng thái hiện tại (ví dụ Đã giao khi đơn còn Chờ sản xuất).

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Backend | Backend từ chối. | Hiển thị ERR-UC22-01. |

Resume: Kết thúc.

##### Alt-04 — ERR-UC22-02 — Chưa đặt cọc

- At step: 1 · Trigger: Đơn có cọc lớn hơn 0 mà thanh toán còn Chưa thanh toán.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Backend | Backend từ chối bắt đầu sản xuất. | Hiển thị ERR-UC22-02. |

Resume: Kết thúc. Buyer thanh toán lại cọc (UC-20, Alt-06), xưởng nhận NT-UC20-05 rồi bắt đầu sản xuất.

##### Alt-05 — Chưa thu đủ phần còn lại khi giao

- At step: 5 hoặc Alt-01 · Trigger: Xưởng giao hoặc Buyer đã nhận hàng nhưng xưởng chưa thu đủ phần còn lại.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Artisan | Artisan chọn Đã giao và chọn Chưa thu. | Đơn vẫn chuyển Đã giao (trạng thái giao tách khỏi trạng thái thu tiền); thanh toán giữ nguyên (Đã đặt cọc, hoặc Chưa thanh toán với cọc 0%). |
| 2 | Artisan | Khi thu xong, Artisan chọn Xác nhận đã thu; thao tác này dùng được khi đơn Đã giao hoặc Hoàn tất. | Thanh toán chuyển Đã thanh toán. |

Resume: Kết thúc.

##### Alt-06 — ERR-UC22-03 — Đơn đã kết thúc

- At step: 1 đến 6 · Trigger: Đơn đã Hoàn tất hoặc Đã hủy (UC-33), trừ thao tác Xác nhận đã thu cho đơn Hoàn tất (Alt-05).

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Backend | Backend từ chối thay đổi. | Hiển thị ERR-UC22-03. |

Resume: Kết thúc.

##### Alt-07 — ERR-UC22-04 — Không có quyền

- At step: 1, 4, 5 hoặc 6 · Trigger: Xưởng khác, Buyer khác hoặc Admin thao tác trên đơn.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Backend | Backend từ chối. | Hiển thị ERR-UC22-04; dữ liệu không đổi. |

Resume: Kết thúc.

##### Alt-08 — Xưởng Tạm ngừng khi đơn đang thực hiện

- At step: 1 đến 5 · Trigger: Gian hàng chuyển Tạm ngừng khi đơn chưa Hoàn tất.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Frontend | Frontend vẫn cho xưởng cập nhật đơn như bình thường. | Đơn tiếp tục các bước. |

Resume: Tiếp tục bước đang thực hiện.

#### Data Fields

| Field | Format | Validation | Required | Ghi chú |
|---|---|---|---|---|
| Trạng thái đơn | Chỉ xem | Chờ sản xuất, Đang sản xuất, Đang giao, Đã giao, Hoàn tất, Đã hủy | — | |
| Trạng thái thanh toán | Chỉ xem | Chưa thanh toán, Đã đặt cọc, Đã thanh toán, Đã hoàn tiền | — | |
| Đơn vị vận chuyển, ghi chú giao | Text; length 200 | — | Không | Chỉ với Giao tận nơi |
| Đã thu phần còn lại | Chọn một | Đã thu hoặc Chưa thu khi chuyển Đã giao; Xác nhận đã thu sau được | Có | Phần còn lại là tổng giá trị trừ cọc |
| Lịch sử trạng thái | Danh sách, chỉ xem | Trạng thái, người thực hiện, thời điểm | — | |

#### Error Messages

| Code | Condition | Message | Display | Flow |
|---|---|---|---|---|
| ERR-UC22-01 | Chuyển trạng thái sai thứ tự | "Không thể chuyển đơn sang trạng thái này ở bước hiện tại." | Toast | Alt-03 |
| ERR-UC22-02 | Chưa đặt cọc | "Buyer chưa thanh toán tiền cọc nên chưa thể bắt đầu sản xuất." | Toast | Alt-04 |
| ERR-UC22-03 | Đơn đã kết thúc | "Đơn đã hoàn tất hoặc đã hủy nên không thể thay đổi." | Toast | Alt-06 |
| ERR-UC22-04 | Không có quyền | "Bạn không có quyền thực hiện thao tác này." | Trang lỗi | Alt-07 |

#### Notifications

| Template ID | Trigger (step) | Channel | Recipient | Title | Body |
|---|---|---|---|---|---|
| NT-UC22-01 | 4 | In-app | Buyer | Đơn đang được giao | "Đơn {{orderCode}} đang được giao. {{shippingNote}}" |
| NT-UC22-02 | 5, Alt-01 | In-app | Buyer | Đơn đã giao | "Đơn {{orderCode}} đã giao. Vui lòng xác nhận đã nhận; đơn sẽ tự hoàn tất sau 7 ngày nếu bạn không xác nhận." |
| NT-UC22-03 | 6 | In-app | Xưởng | Buyer đã nhận hàng | "Buyer đã xác nhận nhận đơn {{orderCode}}. Đơn đã hoàn tất." |
| NT-UC22-04 | Alt-02 bước 2 | In-app | Buyer và xưởng | Đơn tự động hoàn tất | "Đơn {{orderCode}} đã tự động hoàn tất sau 7 ngày kể từ khi giao." |

#### Business Rules

| ID | Rule | Source |
|---|---|---|
| BR-1 | Chỉ xưởng của đơn cập nhật trạng thái sản xuất và giao; chỉ Buyer chủ đơn xác nhận đã nhận; Admin không can thiệp. | Overview mục 1 |
| BR-2 | Trạng thái đơn đi theo thứ tự: Chờ sản xuất, Đang sản xuất, Đang giao, Đã giao, Hoàn tất; đơn Nhận tại xưởng đi từ Đang sản xuất thẳng sang Đã giao. Không bỏ bước, không quay lại. | Overview mục 4 |
| BR-3 | Đơn có cọc chỉ chuyển sang Đang sản xuất khi cọc đã thanh toán. Cọc 0% thì bắt đầu sản xuất được ngay. | Nhóm đề xuất (G-07) |
| BR-4 | Phần còn lại thu khi giao hoặc nhận, ngoài hệ thống. Trạng thái giao và trạng thái thanh toán tách riêng: xưởng đánh dấu Đã giao dù chưa thu; thu xong thì xác nhận đã thu (kể cả khi đơn đã Hoàn tất) để thanh toán thành Đã thanh toán. Hệ thống không đối soát tiền. | Chốt 2026-11-18; EN-15 |
| BR-5 | Đơn Đã giao chuyển Hoàn tất khi Buyer xác nhận đã nhận, hoặc tự động sau 7 ngày. | Overview mục 4 |
| BR-6 | Đơn Đã hủy (UC-33) hoặc Hoàn tất không cập nhật được nữa, trừ Xác nhận đã thu cho đơn Hoàn tất (Alt-05); hủy đơn và hoàn cọc thuộc UC-33. | Overview mục 4 |
| BR-7 | Gian hàng Tạm ngừng không ngăn xưởng hoàn thành đơn đang thực hiện. | UC-13 BR-4 (BRD 02) |
| BR-8 | Mỗi lần chuyển trạng thái ghi người thực hiện và thời điểm. | Nhóm đề xuất |
| BR-9 | Xưởng tạo lô sản xuất cho đơn tại UC-14; Buyer xem nhật ký tại UC-24. | BRD 03 |

#### Acceptance Criteria

| ID | Given | When | Then | Covers |
|---|---|---|---|---|
| UC-22-AC-01 | Đơn Chờ sản xuất, cọc đã thanh toán | Xưởng chọn Bắt đầu sản xuất | Đơn Đang sản xuất; Buyer thấy trạng thái mới trong chi tiết đơn | Main Flow, BR-3 |
| UC-22-AC-02 | Đơn Giao tận nơi Đang sản xuất | Xưởng chọn Giao hàng, nhập ghi chú | Đơn Đang giao; Buyer nhận NT-UC22-01 | Main Flow, BR-2 |
| UC-22-AC-03 | Đơn Đang giao, phần còn lại 29.750.000 đồng | Xưởng chọn Đã giao, rồi chọn Đã thu phần còn lại | Đơn Đã giao; thanh toán Đã thanh toán; Buyer nhận NT-UC22-02 | Main Flow, BR-4 |
| UC-22-AC-04 | Đơn Đã giao | Buyer chọn Xác nhận đã nhận | Đơn Hoàn tất; xưởng nhận NT-UC22-03 | Main Flow, BR-5 |
| UC-22-AC-05 | Đơn Nhận tại xưởng, Đang sản xuất | Xưởng chọn Đã giao, rồi chọn Đã thu phần còn lại | Đơn chuyển thẳng Đã giao, không qua Đang giao | Alt-01, BR-2 |
| UC-22-AC-06 | Đơn Đã giao, Buyer không xác nhận | Qua 7 ngày | Đơn tự Hoàn tất; Buyer và xưởng nhận NT-UC22-04 | Alt-02, BR-5 |
| UC-22-AC-07 | Đơn Chờ sản xuất | Xưởng chọn Đã giao | Hiển thị ERR-UC22-01; trạng thái giữ nguyên | Alt-03, BR-2 |
| UC-22-AC-08 | Đơn có cọc 12.750.000 đồng chưa thanh toán | Xưởng chọn Bắt đầu sản xuất | Hiển thị ERR-UC22-02; sau khi Buyer thanh toán thì bắt đầu được | Alt-04, BR-3 |
| UC-22-AC-09 | Đơn cọc 0% | Xưởng chọn Bắt đầu sản xuất | Chuyển Đang sản xuất, không cần cọc | BR-3 |
| UC-22-AC-10 | Xưởng đã giao nhưng chưa thu đủ phần còn lại | Chọn Đã giao, rồi chọn Chưa thu; sau đó thu xong và chọn Xác nhận đã thu | Đơn vẫn Đã giao; thanh toán giữ Đã đặt cọc; sau khi xác nhận thì thanh toán Đã thanh toán, kể cả khi đơn đã Hoàn tất | Alt-05, BR-4 |
| UC-22-AC-11 | Đơn Hoàn tất hoặc Đã hủy | Xưởng hoặc Buyer thao tác | Hiển thị ERR-UC22-03; dữ liệu không đổi | Alt-06, BR-6 |
| UC-22-AC-12 | Xưởng khác, Buyer khác hoặc Admin | Thao tác trên đơn | Hiển thị ERR-UC22-04 | Alt-07, BR-1 |
| UC-22-AC-13 | Xưởng chuyển Tạm ngừng khi đơn Đang sản xuất | Xưởng cập nhật đơn | Cập nhật được bình thường | Alt-08, BR-7 |
| UC-22-AC-14 | Mỗi lần đơn đổi trạng thái | Xem lịch sử đơn | Ghi người thực hiện và thời điểm | BR-8 |

### UC-23 — Hủy yêu cầu báo giá trước khi chấp nhận

#### Detail

| Field | Content |
|---|---|
| Use case ID | UC-23 |
| Description | Buyer hủy yêu cầu báo giá đã gửi mà chưa chấp nhận báo giá nào. Các báo giá của yêu cầu chuyển Đã đóng; xưởng đã báo giá được thông báo. |
| Actors | Buyer (chủ yêu cầu) |
| Channel | Khu mua hàng |
| Preconditions | • Đã đăng nhập, là chủ yêu cầu<br>• Yêu cầu ở Đã gửi hoặc Đang nhận báo giá |
| Expected | **Thành công:**<br>• Yêu cầu Đã hủy; báo giá Đã đóng; xưởng đã báo giá được thông báo<br>**Thất bại:**<br>• Hiển thị lỗi, yêu cầu giữ nguyên |
| Screens | • Chi tiết yêu cầu (bước 1)<br>• Hộp xác nhận hủy (bước 2) |
| Notifications | NT-UC23-01 |

#### Main Flow

Trigger: Buyer chọn Hủy yêu cầu ở chi tiết yêu cầu.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Buyer | Buyer mở yêu cầu Đã gửi hoặc Đang nhận báo giá và chọn Hủy yêu cầu. | Hộp xác nhận hiển thị. |
| 2 | Buyer | Buyer nhập lý do (nếu có) và xác nhận. | Yêu cầu hủy được gửi. |
| 3 | Backend | Backend kiểm tra yêu cầu chưa Đã chấp nhận; chuyển yêu cầu sang Đã hủy, các báo giá sang Đã đóng; đóng các Yêu cầu cập nhật giá đang chờ; ghi lịch sử. | Yêu cầu Đã hủy; xưởng không gửi hoặc cập nhật báo giá được nữa. |
| 4 | Backend | Backend thông báo các xưởng đã báo giá. | NT-UC23-01 được gửi. |

Outcome: Yêu cầu Đã hủy; báo giá Đã đóng.

#### Alternative Flows

##### Alt-01 — ERR-UC23-01 — Yêu cầu đã được chấp nhận

- At step: 3 · Trigger: Buyer đã chấp nhận một báo giá (Đã chấp nhận), kể cả vừa chấp nhận cùng lúc.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Backend | Backend từ chối hủy. | Hiển thị ERR-UC23-01 hướng Buyer hủy đơn đặt riêng (UC-33). |

Resume: Kết thúc.

##### Alt-02 — ERR-UC23-02 — Yêu cầu không thể hủy

- At step: 3 · Trigger: Yêu cầu đã Hết hạn hoặc Đã hủy.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Backend | Backend từ chối. | Hiển thị ERR-UC23-02. |

Resume: Kết thúc. Buyer có thể tạo yêu cầu mới từ yêu cầu này (UC-18, Alt-15).

##### Alt-03 — ERR-UC23-03 — Không có quyền

- At step: 1 · Trigger: Người dùng khác hoặc Admin hủy yêu cầu của Buyer.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Backend | Backend từ chối. | Hiển thị ERR-UC23-03; dữ liệu không đổi. |

Resume: Kết thúc.

##### Alt-04 — Buyer không xác nhận

- At step: 2 · Trigger: Buyer chọn Không hủy.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Frontend | Frontend đóng hộp xác nhận. | Yêu cầu giữ nguyên. |

Resume: Kết thúc.

#### Data Fields

| Field | Format | Validation | Required | Ghi chú |
|---|---|---|---|---|
| Lý do hủy | Text; length 300 | — | Không | Xưởng đã báo giá thấy lý do |

#### Error Messages

| Code | Condition | Message | Display | Flow |
|---|---|---|---|---|
| ERR-UC23-01 | Yêu cầu đã được chấp nhận | "Bạn đã chấp nhận một báo giá cho yêu cầu này. Nếu muốn hủy, vui lòng hủy đơn đặt riêng." | Toast | Alt-01 |
| ERR-UC23-02 | Yêu cầu không thể hủy | "Yêu cầu này đã hết hạn hoặc đã được hủy." | Toast | Alt-02 |
| ERR-UC23-03 | Không có quyền | "Bạn không có quyền thực hiện thao tác này." | Trang lỗi | Alt-03 |

#### Notifications

| Template ID | Trigger (step) | Channel | Recipient | Title | Body |
|---|---|---|---|---|---|
| NT-UC23-01 | 4 | In-app | Xưởng đã báo giá | Yêu cầu báo giá đã bị hủy | "Buyer đã hủy yêu cầu {{requestCode}}. Báo giá của bạn đã đóng." |

#### Business Rules

| ID | Rule | Source |
|---|---|---|
| BR-1 | Chỉ chủ yêu cầu hủy được, khi yêu cầu ở Đã gửi hoặc Đang nhận báo giá (kể cả khi đã đóng nhận báo giá mà chưa chốt). Nháp thì xóa (UC-18, Alt-06). | Overview 3.4 |
| BR-2 | Yêu cầu đã Đã chấp nhận không hủy được ở đây; hủy đơn và hoàn cọc theo UC-33. | Overview mục 4 |
| BR-3 | Hủy xong, báo giá Đã đóng; yêu cầu không mở lại, muốn đặt tiếp thì tạo yêu cầu mới (UC-18, Alt-15). | Overview 3.4; Nhóm đề xuất (G-09) |
| BR-4 | Chỉ xưởng đã báo giá được thông báo; xưởng chưa báo giá chỉ thấy yêu cầu chuyển Đã hủy. | Nhóm đề xuất (G-08) |

#### Acceptance Criteria

| ID | Given | When | Then | Covers |
|---|---|---|---|---|
| UC-23-AC-01 | Yêu cầu Đang nhận báo giá, có 2 báo giá | Buyer chọn Hủy yêu cầu và xác nhận | Yêu cầu Đã hủy; 2 báo giá Đã đóng; 2 xưởng nhận NT-UC23-01 | Main Flow, BR-1 |
| UC-23-AC-02 | Yêu cầu Đã gửi chưa có báo giá | Buyer hủy | Yêu cầu Đã hủy; không thông báo xưởng nào | Main Flow, BR-4 |
| UC-23-AC-03 | Yêu cầu đã đóng nhận báo giá, còn báo giá hiệu lực | Buyer hủy | Hủy được | BR-1 |
| UC-23-AC-04 | Yêu cầu Đã chấp nhận | Buyer tìm nút Hủy yêu cầu, hoặc thao tác cùng lúc với chấp nhận | Không có nút hủy; thao tác muộn hơn bị ERR-UC23-01 | Alt-01, BR-2 |
| UC-23-AC-05 | Yêu cầu Hết hạn hoặc Đã hủy | Buyer hủy | Hiển thị ERR-UC23-02 | Alt-02 |
| UC-23-AC-06 | Người dùng khác hoặc Admin | Hủy yêu cầu | Hiển thị ERR-UC23-03 | Alt-03 |
| UC-23-AC-07 | Hộp xác nhận hủy | Buyer chọn Không hủy | Yêu cầu giữ nguyên | Alt-04 |
| UC-23-AC-08 | Yêu cầu Đã hủy | Buyer tìm cách mở lại | Không có thao tác mở lại; có nút Tạo yêu cầu mới từ yêu cầu này | BR-3 |

### UC-24 — Xem nhật ký sản xuất của đơn đặt riêng

#### Detail

| Field | Content |
|---|---|
| Use case ID | UC-24 |
| Description | Buyer xem các lô và nhật ký sản xuất xưởng ghi cho đơn đặt riêng của mình, gồm tên lô, phân loại, trạng thái lô, các mốc và ghi chú đính chính. |
| Actors | Buyer (chủ đơn) |
| Channel | Khu mua hàng |
| Preconditions | • Đã đăng nhập, là chủ đơn |
| Expected | **Thành công:**<br>• Nhật ký các lô của đơn hiển thị<br>**Thất bại:**<br>• Hiển thị lỗi, không lộ nhật ký của đơn khác |
| Screens | • Chi tiết đơn, mục Nhật ký sản xuất (bước 1-2)<br>• Nhật ký của một lô (bước 3) |
| Notifications | Không có |

#### Main Flow

Trigger: Buyer chọn mục Nhật ký sản xuất ở chi tiết đơn đặt riêng (UC-21).

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Buyer | Buyer mở chi tiết đơn và chọn Nhật ký sản xuất. | Backend lấy các lô của đơn. |
| 2 | Frontend | Frontend hiển thị danh sách lô: tên lô, phân loại, số lượng, ngày bắt đầu, trạng thái lô. | Buyer thấy mọi lô của đơn. |
| 3 | Buyer | Buyer chọn một lô. | Timeline mốc theo ngày tăng dần, ghi chú đính chính (nếu có) và nhãn "đã chỉnh sửa" của mốc hiển thị. |

Outcome: Buyer nắm được tiến độ sản xuất của đơn.

#### Alternative Flows

##### Alt-01 — Đơn chưa có lô

- At step: 2 · Trigger: Xưởng chưa tạo lô nào cho đơn.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Frontend | Frontend hiện "Xưởng chưa cập nhật nhật ký sản xuất". | Không có lô nào. |

Resume: Kết thúc.

##### Alt-02 — Lô chưa có mốc hoặc đang thực hiện

- At step: 3 · Trigger: Lô Đang thực hiện hoặc chưa có mốc.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Frontend | Frontend hiện nhãn trạng thái lô và các mốc hiện có; lô chưa có mốc hiện "Xưởng chưa cập nhật nhật ký sản xuất". | Buyer thấy tiến độ đến thời điểm hiện tại. |

Resume: Kết thúc.

##### Alt-03 — Đơn đã Hoàn tất hoặc Đã hủy

- At step: 1 · Trigger: Đơn ở Hoàn tất hoặc Đã hủy.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Frontend | Frontend vẫn hiện các lô đã có ở chế độ chỉ xem. | Không thêm lô mới cho đơn (UC-14, BR-9). |

Resume: Tiếp tục bước 2.

##### Alt-04 — ERR-UC24-01 — Không có quyền

- At step: 1 · Trigger: Người dùng khác, xưởng khác hoặc Admin mở nhật ký của đơn.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Backend | Backend từ chối. | Hiển thị ERR-UC24-01. |

Resume: Kết thúc.

##### Alt-05 — ERR-UC24-02 — Không tải được

- At step: 1 đến 3 · Trigger: Lỗi tải dữ liệu.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Frontend | Frontend giữ trang và hiện lỗi. | Hiển thị ERR-UC24-02 và nút Thử lại. |

Resume: Thử lại, tiếp tục bước đang thực hiện.

#### Data Fields

| Field | Format | Validation | Required | Ghi chú |
|---|---|---|---|---|
| Danh sách lô | Chỉ xem | Lô của đơn: tên lô, phân loại, số lượng, ngày bắt đầu, trạng thái lô (Đang thực hiện, Hoàn thành, Đã xuất xưởng) | — | Một đơn có thể có nhiều lô |
| Mốc | Danh sách, chỉ xem | Ngày, công đoạn, mô tả, ảnh; ngày tăng dần | — | Nhãn "đã chỉnh sửa" nếu có |
| Ghi chú đính chính | Danh sách, chỉ xem | Mới nhất cuối, kèm ngày giờ | — | Chỉ lô Đã xuất xưởng |

#### Error Messages

| Code | Condition | Message | Display | Flow |
|---|---|---|---|---|
| ERR-UC24-01 | Không có quyền | "Bạn không có quyền xem nhật ký của đơn này." | Trang lỗi | Alt-04 |
| ERR-UC24-02 | Không tải được | "Chưa tải được nhật ký sản xuất. Vui lòng thử lại." | Inline | Alt-05 |

#### Business Rules

| ID | Rule | Source |
|---|---|---|
| BR-1 | Chỉ Buyer chủ đơn xem nhật ký lô của đơn trong chi tiết đơn; xưởng ghi nhật ký ở UC-14 và UC-15. | Chốt 2026-11-17 (G-05, BRD 03) |
| BR-2 | Một đơn có thể có nhiều lô; Buyer xem toàn bộ. | Chốt 2026-11-17 (G-06, BRD 03) |
| BR-3 | Buyer xem được cả lô Đang thực hiện, kèm nhãn trạng thái. | Nhóm đề xuất |
| BR-4 | Trang nguồn gốc công khai của lô đặt riêng (UC-17) không hiện Buyer, đơn hay sản phẩm mẫu; chi tiết đơn là nơi duy nhất liên kết lô với đơn. | UC-17 BR-3 (BRD 03) |
| BR-5 | Đơn Hoàn tất hoặc Đã hủy vẫn xem được nhật ký đã có; không thêm lô mới. | UC-14 BR-9 (BRD 03); G-04 (BRD 03) |

#### Acceptance Criteria

| ID | Given | When | Then | Covers |
|---|---|---|---|---|
| UC-24-AC-01 | Đơn có 2 lô | Buyer mở Nhật ký sản xuất | Thấy cả 2 lô với tên, phân loại, số lượng, trạng thái | Main Flow, BR-2 |
| UC-24-AC-02 | Lô có 3 mốc và một ghi chú đính chính | Buyer chọn lô | Timeline từ ngày đầu; ghi chú đính chính có ngày giờ | Main Flow |
| UC-24-AC-03 | Đơn chưa có lô | Buyer mở Nhật ký sản xuất | Hiện "Xưởng chưa cập nhật nhật ký sản xuất" | Alt-01 |
| UC-24-AC-04 | Lô Đang thực hiện | Buyer chọn lô | Hiện nhãn trạng thái và các mốc hiện có | Alt-02, BR-3 |
| UC-24-AC-05 | Đơn Hoàn tất | Buyer mở nhật ký | Xem được, không có thao tác thêm lô | Alt-03, BR-5 |
| UC-24-AC-06 | Buyer khác, xưởng khác hoặc Admin | Mở nhật ký của đơn | Hiển thị ERR-UC24-01 | Alt-04, BR-1 |
| UC-24-AC-07 | Lô của đơn đặt riêng | Mở trang nguồn gốc công khai của lô | Không có tên Buyer, mã đơn, sản phẩm mẫu | BR-4 |

## IV. Open Questions

| ID | Question | Affects | Decision |
|---|---|---|---|
| G-01 | Có giới hạn số xưởng nhận mỗi yêu cầu gửi chung không (KI-01)? Đang theo hướng: chưa giới hạn trong MVP1. | UC-18 BR-4 | Open |
| G-02 | Che địa chỉ chi tiết, tên và số điện thoại của Buyer với xưởng cho đến khi báo giá được chấp nhận. Đang theo hướng: che, xưởng chỉ thấy đến quận/huyện để tính phí giao. | UC-18 BR-8; UC-19 BR-12; UC-21 BR-5 | Open |
| G-03 | Danh sách lý do xưởng không nhận (Không làm được mẫu này, Không đủ năng lực cho số lượng, Không kịp thời gian, Khác) có phù hợp không? | UC-19 Data Fields | Open |
| G-04 | Phần còn lại sau cọc thu khi giao hoặc nhận, xưởng đánh dấu đã thu; hệ thống không đối soát tiền. | UC-20 BR-7; UC-22 BR-4 | Đã chốt 2026-11-18: thu khi giao hoặc nhận |
| G-05 | Các ngưỡng thời gian: hạn của yêu cầu chọn theo ngày, từ ngày mai đến tối đa 30 ngày, hết 23:59; nhắc xưởng 24 giờ trước hạn khi hạn cách ngày gửi từ 2 ngày; hiệu lực báo giá từ ngày mai đến tối đa 90 ngày; tự hoàn tất 7 ngày. Đang theo hướng: giữ các ngưỡng này. | UC-18 BR-9, BR-10, BR-14; UC-19 BR-5; UC-20 BR-8; UC-22 BR-5 | Open |
| G-06 | Mô hình hạn hai chiều: đến hạn chỉ đóng nhận báo giá (nhãn "Chờ Buyer lựa chọn", không thêm trạng thái), báo giá còn hiệu lực vẫn chấp nhận được; Yêu cầu cập nhật giá xưởng có 3 ngày trả lời và yêu cầu giữ trạng thái trong thời gian chờ; gia hạn chỉ cho yêu cầu gửi riêng Quá hạn phản hồi, tối đa một lần; chuyển nhanh sang gửi chung bằng nháp mới. Đang theo hướng: như mô tả. | UC-18 BR-7, BR-11, Alt-12, Alt-14, Alt-15; UC-19 BR-11; UC-20 BR-10, BR-11 | Đã chốt 2026-11-18 về mô hình; Open về 3 ngày và gia hạn tối đa một lần |
| G-07 | Hiện chưa đặt hạn thanh toán cọc: đơn tạo ngay lúc chấp nhận và giữ Chờ sản xuất đến khi Buyer thanh toán hoặc hủy qua UC-33. Rủi ro: (1) đơn chưa cọc treo vô thời hạn, xưởng không sản xuất được; (2) các báo giá khác đã Đã đóng, Buyer hủy thì phải tạo yêu cầu mới; (3) UC-14 (BRD 03) cho tạo lô với đơn chưa Đã hủy, kể cả đơn chưa cọc. Phương án khi cần: hạn thanh toán 24 giờ rồi tự hủy đơn, không hoàn tiền vì chưa thu, thống nhất với UC-33 (BRD 08); hoặc chỉ cho tạo lô khi đơn đã Đang sản xuất. Đang theo hướng: để không hạn trong MVP. | UC-20 BR-8; UC-22 BR-3; UC-14 BR-9 (BRD 03); UC-33 | Open |
| G-08 | Hủy yêu cầu có thông báo cho xưởng chưa báo giá không? Đang theo hướng: không, chỉ thông báo xưởng đã báo giá. | UC-23 BR-4 | Open |
| G-09 | Đơn đặt riêng bị hủy (UC-33) có mở lại yêu cầu không? Đang theo hướng: không, Buyer tạo yêu cầu mới từ yêu cầu cũ. | UC-23 BR-3; UC-33 | Open |
| G-10 | Gửi chung khi chưa có xưởng đúng phân loại: gửi tới tất cả xưởng đang nhận (kèm cảnh báo) hay không cho gửi và gợi ý chọn phân loại khác? Đang theo hướng: giữ gửi tất cả kèm cảnh báo, vì yêu cầu đặt riêng thường không khớp phân loại sẵn có. | UC-18 BR-4, Alt-08 | Open |
| G-11 | Hiệu lực báo giá có bắt buộc sau hạn của yêu cầu không? Đang theo hướng: không bắt buộc, chỉ cảnh báo xưởng, để xưởng không phải giữ giá tới 30 ngày; báo giá hết hiệu lực sớm thì xưởng cập nhật hoặc Buyer Yêu cầu cập nhật giá. Phương án khác: bắt buộc sau hạn, Buyer luôn có thời gian chốt nhưng xưởng chịu rủi ro giá. | UC-19 BR-5; UC-18 Alt-12, Alt-13; UC-20 Alt-09 | Open |
| G-12 | Đơn Đã giao tự Hoàn tất sau 7 ngày dù không có bằng chứng độc lập Buyer đã nhận hàng (xưởng tự đánh dấu Đã giao). Đang theo hướng: giữ như overview, thông báo cho Buyer biết hạn 7 ngày; chưa làm nút "Báo chưa nhận hàng" vì tranh chấp ngoài phạm vi (EN-17). | UC-22 BR-5, Alt-02 | Open |
| G-13 | Tổng số lượng các lô của một đơn đặt riêng có cần kiểm soát không (UC-14 chỉ yêu cầu số lượng lô là số nguyên dương)? Đang theo hướng: cảnh báo không chặn, xử lý ở BRD 03. | UC-24 BR-2; UC-14 (BRD 03) | Open |
