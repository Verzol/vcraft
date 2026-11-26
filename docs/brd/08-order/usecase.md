---
title: "08 — Mua hàng có sẵn"
group: 08-order
type: brd
status: draft
version: 0.3.0
date: 2026-11-25
author: Khánh Chi
---

# 08 — Mua hàng có sẵn

Change history: [CHANGELOG.md](CHANGELOG.md)

## I. Overview

Nhóm chức năng cho luồng mua sản phẩm có sẵn (có giá niêm yết và số lượng còn bán) trên sàn: giỏ hàng, thanh toán và tạo đơn, quản lý đơn mua hàng, hủy đơn. Guest và Buyer thêm sản phẩm vào giỏ (thêm giỏ không giữ hàng); Buyer chọn món của một xưởng, chọn phương thức giao và thanh toán rồi đặt hàng, lúc này hệ thống mới giữ hàng. Xưởng xác nhận hoặc từ chối đơn, chuẩn bị, giao hàng và xác nhận đã thu tiền COD; Buyer theo dõi và xác nhận đã nhận để đơn Hoàn tất. Nhóm này cũng quy định việc hủy đơn cho cả đơn mua hàng và đơn đặt riêng (tạo ở `04-quotation`), kèm hoàn số lượng và hoàn tiền đã thanh toán online. Mỗi đơn do đúng một xưởng thực hiện; MVP1 không thu phí sàn và không đối soát tiền cho xưởng; Admin không mua, không bán và không can thiệp đơn (chỉ theo dõi ở Trang quản trị qua UC-35, nhóm `09-portal`).

### Terms

| Term | Meaning |
|---|---|
| Giỏ hàng | Danh sách món Guest hoặc Buyer định mua. Giỏ của Guest lưu trên trình duyệt; giỏ của Buyer gắn với tài khoản và còn nguyên sau khi đăng xuất. Thêm vào giỏ không giữ hàng. |
| Món trong giỏ | Một dòng gồm một sản phẩm có sẵn và số lượng. |
| Số còn bán | Số lượng còn bán của sản phẩm (UC-09). Giảm khi đơn được tạo (giữ hàng), tăng lại khi đơn Đã hủy hoặc Bị từ chối (trừ phần hàng hư hỏng khi xưởng hủy đơn Đang chuẩn bị). |
| Nhóm Không khả dụng | Phần cuối giỏ gồm các món không mua được lúc này (hết hàng, sản phẩm bị Ẩn, xưởng Tạm ngừng, sản phẩm của xưởng mình), kèm lý do. |
| Giữ hàng | Trừ số lượng đơn đặt khỏi số còn bán ngay khi đơn được tạo, kể cả đơn COD và đơn Chờ thanh toán. |
| Đơn mua hàng | Đơn tạo ở checkout cho sản phẩm có sẵn, của đúng một xưởng. Có mã dạng MH-2026-0001. Khác với đơn đặt riêng (mã DR, `04-quotation`). |
| Trạng thái đơn mua hàng | Chờ thanh toán, Chờ xác nhận, Đang chuẩn bị, Đang giao, Đã giao, Hoàn tất, Bị từ chối, Đã hủy. |
| Trạng thái thanh toán | Chưa thanh toán, Đã đặt cọc (chỉ đơn đặt riêng), Đã thanh toán, Đã hoàn tiền. Đi riêng với trạng thái đơn; giao hàng không tự chứng minh đã thanh toán. |
| COD | Thanh toán khi nhận hàng: xưởng thu tiền khi giao hoặc khi Buyer đến nhận, rồi xác nhận đã thu trên hệ thống. |
| Thanh toán online | Thanh toán qua cổng ZaloPay (môi trường sandbox; dự phòng VNPay sandbox). |
| Phương thức giao | Nhận tại xưởng (miễn phí), Xưởng tự giao (phí cố định do xưởng đặt ở UC-07), GHN (mức Should). Xưởng bật hoặc tắt từng phương thức. |
| Tiền hàng, Phí giao, Tổng thanh toán | Tiền hàng là tổng đơn giá nhân số lượng của các món; Tổng thanh toán là tiền hàng cộng phí giao. Đơn vị đồng (VND), số nguyên. |
| Hạn thanh toán | 15 phút kể từ lúc tạo đơn thanh toán online. Quá hạn mà chưa thanh toán thì đơn tự hủy. |
| Hạn xác nhận | 3 ngày (72 giờ) kể từ lúc đơn vào Chờ xác nhận. Xưởng chưa xác nhận hoặc từ chối thì đơn tự hủy. |
| Yêu cầu hủy | Đề nghị hủy đơn đặt riêng đang Đang sản xuất do Buyer gửi để xưởng quyết định. Có tình trạng Đang chờ xưởng, Xưởng đồng ý, Xưởng từ chối, Đã rút, Tự đóng. Không phải trạng thái của đơn. |
| Hoàn tiền | Trả lại tiền đã thanh toán online về nguồn thanh toán ban đầu qua API hoàn tiền của cổng. COD không có tiền để hoàn. |

## II. Scope

| Business topic | Channel | Use case |
|---|---|---|
| Thêm sản phẩm vào giỏ, xem giỏ theo xưởng, đổi số lượng, xóa món, chọn món để mua, gộp giỏ Guest khi đăng nhập | Khu mua hàng | UC-30 |
| Chọn phương thức giao, thanh toán (COD, ZaloPay sandbox), đặt hàng, giữ hàng, tạo đơn mua hàng | Khu mua hàng | UC-31 |
| Xưởng xác nhận hoặc từ chối đơn, chuẩn bị, giao, xác nhận đã thu COD; Buyer theo dõi và xác nhận đã nhận | Kênh người bán, Khu mua hàng | UC-32 |
| Hủy đơn mua hàng và đơn đặt riêng, yêu cầu hủy, hoàn số lượng và hoàn tiền | Khu mua hàng, Kênh người bán | UC-33 |

**Ngoài phạm vi:** Mã giảm giá, thanh toán một phần hoặc trả góp; thanh toán nhiều xưởng trong một lần; giữ hàng khi thêm giỏ; giỏ cho sản phẩm chỉ nhận đặt riêng (đi qua UC-18); biến thể kích thước, màu (Should, chưa mô tả); sửa đơn sau khi đã tạo (muốn đổi địa chỉ hoặc số lượng thì hủy rồi đặt lại); sổ địa chỉ nhiều địa chỉ; đổi trả, tranh chấp, khiếu nại giao thiếu hoặc hỏng (EN-17); thanh toán thật và đối soát tiền cho xưởng (EN-15); thu phí sàn; tích hợp vận chuyển thật, nhiều hãng (EN-16) và chi tiết vận đơn, webhook GHN (mức Should, chưa mô tả ngoài phần tính phí ở UC-31); hóa đơn; đánh giá sau giao dịch (EN-09); thông báo qua email (EN-05); Admin hủy hoặc can thiệp đơn (theo dõi chỉ xem ở Trang quản trị thuộc UC-35, nhóm `09-portal`).

**Liên quan**

| ID | Tài liệu | Quan hệ |
|---|---|---|
| OV | [overview](../../overview.md) | Khung use case, giỏ hàng, checkout, vòng đời đơn mua hàng, thanh toán và vận chuyển, quy tắc hủy đơn. |
| BRD-01 | [01-account](../01-account/usecase.md) | UC-03, UC-04 gộp giỏ Guest vào giỏ tài khoản và đưa người dùng quay lại thanh toán sau khi đăng nhập (UC-04, Alt-08, BR-3); UC-04 BR-6: Admin không mua. UC-05 cung cấp họ tên, số điện thoại điền sẵn. |
| BRD-02 | [02-showcase](../02-showcase/usecase.md) | UC-11 có nút Thêm vào giỏ (hết hàng, xưởng Tạm ngừng, sản phẩm xưởng mình thì vô hiệu hoặc ẩn); UC-09 quản lý số còn bán, giá, cân nặng, kích thước, Ẩn sản phẩm; UC-07 cấu hình phương thức giao và phí giao (BR-6 luôn còn ít nhất một phương thức); UC-13 xưởng Tạm ngừng không nhận đơn mới nhưng vẫn xử lý đơn đang thực hiện (BR-4). |
| BRD-03 | [03-traceability](../03-traceability/usecase.md) | Đơn mua hàng không gắn lô sản xuất; nguồn gốc sản phẩm xem ở UC-11 và UC-17. Lô đã tạo cho đơn đặt riêng bị hủy giữ nguyên (UC-14 BR-9). |
| BRD-04 | [04-quotation](../04-quotation/usecase.md) | UC-20 tạo đơn đặt riêng và thanh toán cọc qua cùng cổng thanh toán; UC-22 cập nhật đơn đặt riêng; UC-33 hủy đơn đặt riêng và hoàn cọc; UC-18 (Alt-15) tạo yêu cầu mới từ yêu cầu có đơn bị hủy; G-07 hạn thanh toán cọc. |
| BRD-05 | [05-village-map](../05-village-map/usecase.md) | Nhận tại xưởng dùng nút Chỉ đường của bản đồ (UC-26, Alt-05). |
| BRD-07 | [07-notification](../07-notification/usecase.md) | UC-29 hiển thị NT-UC31-01 đến NT-UC31-03, NT-UC32-01 đến NT-UC32-08, NT-UC33-01 đến NT-UC33-09; các mẫu này cần được bổ sung vào Danh mục thông báo (G-01 của BRD 07). |
| BRD-09 | [09-portal](../09-portal/usecase.md) | UC-35 theo dõi chỉ xem đơn mua hàng, đơn đặt riêng và trạng thái thanh toán/hoàn tiền toàn hệ thống ở Trang quản trị. |

## III. Use Cases

| Use case ID | Use case | Description |
|---|---|---|
| UC-30 | Quản lý giỏ hàng | Guest hoặc Buyer thêm sản phẩm có sẵn vào giỏ, xem theo xưởng, đổi số lượng, xóa món, chọn món để mua; giỏ Guest được gộp khi đăng nhập |
| UC-31 | Checkout: giao hàng, thanh toán, tạo đơn | Buyer chọn món của một xưởng, chọn phương thức giao và thanh toán, đặt hàng; hệ thống giữ hàng, tạo đơn mua hàng và báo xưởng |
| UC-32 | Quản lý đơn mua hàng | Xưởng xác nhận hoặc từ chối, chuẩn bị, giao, xác nhận đã thu COD; Buyer theo dõi và xác nhận đã nhận để đơn Hoàn tất |
| UC-33 | Hủy đơn hàng (đơn mua và đơn đặt riêng) | Buyer hoặc xưởng hủy đơn theo trạng thái đơn, gửi và xử lý yêu cầu hủy đơn đặt riêng, hoàn số lượng và hoàn tiền |

### UC-30 — Quản lý giỏ hàng

#### Detail

| Field | Content |
|---|---|
| Use case ID | UC-30 |
| Description | Guest hoặc Buyer thêm sản phẩm có sẵn vào giỏ, xem giỏ nhóm theo xưởng, đổi số lượng, xóa món và chọn món của một xưởng để mua. Giỏ của Guest lưu trên trình duyệt và được gộp vào giỏ tài khoản khi đăng nhập. Thêm vào giỏ không giữ hàng. |
| Actors | Guest, Buyer (Artisan thao tác như Buyer, không thêm sản phẩm của xưởng mình) |
| Channel | Khu mua hàng |
| Preconditions | • Không yêu cầu đăng nhập<br>• Thêm vào giỏ: sản phẩm loại Có sẵn, đang công khai (UC-11) |
| Expected | **Thành công:**<br>• Giỏ hiển thị đúng món, số lượng, giá hiện tại; món không mua được nằm ở nhóm Không khả dụng<br>• Số còn bán của sản phẩm không bị trừ<br>• Món đã chọn của một xưởng được chuyển sang thanh toán (UC-31)<br>**Thất bại:**<br>• Hiển thị lỗi, giỏ giữ nguyên |
| Screens | • Nút Thêm vào giỏ ở chi tiết sản phẩm (UC-11) (bước 1-3)<br>• Giỏ hàng (bước 4-9) |
| Notifications | Không có |

#### Main Flow

Trigger: Guest chọn Thêm vào giỏ ở trang chi tiết sản phẩm (UC-11).

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Guest | Guest chọn Thêm vào giỏ ở một sản phẩm Có sẵn còn hàng. | Yêu cầu thêm một đơn vị sản phẩm được gửi. |
| 2 | Backend | Backend kiểm tra sản phẩm đang Hiển thị, loại Có sẵn, còn hàng, gian hàng Đang hoạt động và không phải xưởng của người thêm; thêm một đơn vị vào giỏ, món đã có thì cộng số lượng, không vượt số còn bán. | Món vào giỏ; số còn bán của sản phẩm không bị trừ. |
| 3 | Frontend | Frontend cập nhật số món trên biểu tượng giỏ và hiện "Đã thêm vào giỏ" kèm nút Xem giỏ hàng. | Guest ở lại trang sản phẩm; trang không tải lại. |
| 4 | Guest | Guest mở Giỏ hàng. | Giỏ hàng hiển thị. |
| 5 | Frontend | Frontend hiển thị các món theo nhóm xưởng (tên xưởng, ô chọn từng món, ảnh, tên, đơn giá hiện tại, số lượng, thành tiền); món không mua được ở nhóm Không khả dụng; mỗi nhóm có tạm tính của các món đang được chọn. | Guest thấy rõ món của từng xưởng. |
| 6 | Guest | Guest đổi số lượng, chọn hoặc bỏ chọn một món, hoặc xóa một món. | Yêu cầu cập nhật được gửi. |
| 7 | Backend | Backend kiểm tra số lượng từ 1 đến số còn bán và lưu giỏ (giỏ tài khoản nếu đã đăng nhập, giỏ trên trình duyệt nếu là Guest). | Giỏ được cập nhật. |
| 8 | Frontend | Frontend cập nhật thành tiền, tạm tính và số món. | Giao diện cập nhật, không tải lại trang. |
| 9 | Buyer | Buyer chọn Mua hàng ở nhóm của một xưởng. | Chuyển sang thanh toán (UC-31) với các món khả dụng đang được chọn của nhóm đó; Guest chưa đăng nhập xử lý theo UC-31, Alt-01. |

Outcome: Giỏ chứa các món muốn mua; các món đã chọn của một xưởng được chuyển sang thanh toán.

#### Alternative Flows

##### Alt-01 — Gộp giỏ khi đăng nhập

- At step: Sau bước 3 (khi UC-03 bước 5 hoặc UC-04 bước 5 chạy) · Trigger: Guest có món trong giỏ trên trình duyệt rồi đăng nhập hoặc đăng ký.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Backend | Backend gộp giỏ trên trình duyệt vào giỏ tài khoản: món trùng cộng số lượng nhưng không vượt số còn bán; món của xưởng chính người dùng được giữ lại và xếp vào nhóm Không khả dụng. | Giỏ tài khoản chứa cả món cũ và món mới. |
| 2 | Frontend | Frontend xóa giỏ trên trình duyệt và cập nhật số món; nếu có món bị cắt số lượng thì hiện "Một số món đã được điều chỉnh theo số lượng còn bán." | Người dùng không mất món đang chọn. |

Resume: Tiếp tục bước 4.

##### Alt-02 — ERR-UC30-01 — Vượt số còn bán

- At step: 2, 5 hoặc 7 · Trigger: Số lượng sau khi thêm hoặc đổi lớn hơn số còn bán; hoặc số còn bán giảm xuống dưới số lượng trong giỏ khi mở giỏ.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Backend | Backend không nhận số lượng vượt, đặt số lượng bằng số còn bán. | Hiển thị ERR-UC30-01 ngay tại dòng đó; các món khác không đổi. |

Resume: Quay lại bước 6.

##### Alt-03 — ERR-UC30-02 — Số lượng không hợp lệ

- At step: 7 · Trigger: Số lượng không phải số nguyên hoặc nhỏ hơn 1.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Backend | Backend không lưu, giữ số lượng cũ. | Hiển thị ERR-UC30-02 tại dòng đó. |

Resume: Quay lại bước 6. Muốn bỏ món thì dùng Xóa (Alt-07).

##### Alt-04 — ERR-UC30-03 — Không thêm được vào giỏ

- At step: 2 · Trigger: Sản phẩm không phải loại Có sẵn, đã hết hàng, đã Ẩn, thuộc gian hàng Tạm ngừng hoặc chưa mở, hoặc là sản phẩm của chính xưởng người thêm. Giao diện thường đã ẩn hoặc vô hiệu nút (UC-11); nhánh này xảy ra với trang đã mở từ trước hoặc thao tác gọi trực tiếp.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Backend | Backend từ chối thêm, giỏ giữ nguyên. | Hiển thị ERR-UC30-03 kèm lý do cụ thể. |

Resume: Kết thúc.

##### Alt-05 — Món chuyển sang nhóm Không khả dụng

- At step: 5 · Trigger: Món hết hàng, sản phẩm đã Ẩn, gian hàng Tạm ngừng, hoặc là sản phẩm của xưởng chính người dùng.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Frontend | Frontend đưa món xuống nhóm Không khả dụng kèm lý do: "Hết hàng", "Sản phẩm không còn bán", "Xưởng đang tạm ngừng nhận giao dịch mới" hoặc "Sản phẩm của xưởng bạn". Món này không chọn được và không tính vào tạm tính. | Người dùng biết vì sao chưa mua được món; vẫn xóa được. |
| 2 | Frontend | Khi lý do không còn (hàng có lại, sản phẩm hiển thị lại, xưởng mở lại), món tự về nhóm xưởng ở lần tải giỏ tiếp theo. | Món mua được trở lại. |

Resume: Tiếp tục bước 6.

##### Alt-06 — Giỏ trống

- At step: 5 · Trigger: Giỏ không có món nào.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Frontend | Frontend hiện "Giỏ hàng của bạn đang trống" kèm nút Xem sản phẩm (UC-10). | Không có nhóm nào; nút Mua hàng bị vô hiệu. |

Resume: Kết thúc.

##### Alt-07 — Xóa món

- At step: 6 · Trigger: Người dùng chọn Xóa trên một món (kể cả món ở nhóm Không khả dụng).

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Backend | Backend xóa món khỏi giỏ. | Món biến khỏi giỏ; nhóm không còn món nào thì biến luôn. |
| 2 | Frontend | Frontend cập nhật số món, tạm tính, không tải lại trang. | Giỏ hiển thị đúng sau khi xóa. |

Resume: Tiếp tục bước 8.

##### Alt-08 — ERR-UC30-04 — Không tải được giỏ

- At step: 4 hoặc 7 · Trigger: Tải giỏ hoặc lưu thay đổi bị lỗi.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Frontend | Frontend giữ giỏ đang hiển thị, hiện lỗi và nút Thử lại ngay tại giỏ. | Hiển thị ERR-UC30-04; phần còn lại của trang dùng được. |

Resume: Chọn Thử lại thì lặp bước đang làm.

##### Alt-09 — Phiên đăng nhập hết hạn

- At step: 2 hoặc 7 · Trigger: Buyer đã đăng nhập nhưng phiên hết hạn do không hoạt động quá lâu.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Backend | Backend đưa người dùng về form đăng nhập và giữ lại thao tác đang làm (UC-04, Alt-08). | Hiển thị ERR-UC04-05. |

Resume: Sau khi đăng nhập quay lại đúng thao tác với giỏ đã gộp (Alt-01).

##### Alt-10 — ERR-UC30-05 — Tài khoản quản trị

- At step: 2 hoặc 4 · Trigger: Tài khoản Admin mở giỏ hoặc thêm sản phẩm.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Backend | Backend từ chối thao tác. | Hiển thị ERR-UC30-05; Admin không có giỏ hàng. |

Resume: Kết thúc.

#### Data Fields

| Field | Format | Validation | Required | Ghi chú |
|---|---|---|---|---|
| Số lượng | Number | Số nguyên từ 1 đến số còn bán | Có | Mặc định 1 khi thêm; thêm lại món đã có thì cộng thêm 1. Ví dụ: 2 |
| Chọn món | Hộp chọn | Chỉ món khả dụng | Không | Chỉ là lựa chọn trên màn hình, không đổi giỏ; Mua hàng ở nhóm nào thì chỉ lấy món đã chọn của nhóm đó (BR-4) |
| Nhóm xưởng | Chỉ xem | Tên xưởng, link về gian hàng (UC-08) | — | Mỗi xưởng một nhóm |
| Đơn giá | VND, chỉ xem | Giá hiện tại của sản phẩm | — | Ví dụ: "250.000 ₫" |
| Thành tiền | VND, chỉ xem | Đơn giá nhân số lượng | — | |
| Tạm tính của nhóm | VND, chỉ xem | Tổng thành tiền các món khả dụng đang được chọn trong nhóm | — | Chưa gồm phí giao; phí giao tính ở UC-31 |
| Nhóm Không khả dụng | Chỉ xem | Món hết hàng, bị Ẩn, của xưởng Tạm ngừng, hoặc của xưởng mình; kèm lý do | — | Không chọn được |
| Số món trên biểu tượng giỏ | Number, chỉ xem | Số dòng món trong giỏ; từ 100 trở lên hiện "99+" | — | Hiện ở đầu trang, cập nhật không tải lại trang |

#### Error Messages

| Code | Condition | Message | Display | Flow |
|---|---|---|---|---|
| ERR-UC30-01 | Vượt số còn bán | "Chỉ còn {{available}} sản phẩm. Số lượng trong giỏ đã được điều chỉnh." | Inline | Alt-02 |
| ERR-UC30-02 | Số lượng không hợp lệ | "Số lượng phải là số nguyên từ 1 đến {{available}}." | Inline | Alt-03 |
| ERR-UC30-03 | Không thêm được vào giỏ | "Không thể thêm sản phẩm này vào giỏ: {{reason}}." ({{reason}}: "sản phẩm đã hết hàng", "sản phẩm không còn bán", "xưởng đang tạm ngừng nhận giao dịch mới", "đây là sản phẩm của xưởng bạn" hoặc "sản phẩm này chỉ nhận đặt riêng") | Toast | Alt-04 |
| ERR-UC30-04 | Không tải được giỏ | "Không tải được giỏ hàng. Vui lòng thử lại." | Inline | Alt-08 |
| ERR-UC30-05 | Tài khoản Admin | "Tài khoản quản trị không dùng được chức năng mua hàng." | Trang lỗi | Alt-10 |

#### Business Rules

| ID | Rule | Source |
|---|---|---|
| BR-1 | Thêm vào giỏ không giữ hàng, không trừ số còn bán; chỉ khi đơn được tạo (UC-31) mới giữ hàng. | Overview 3.8 |
| BR-2 | Chỉ sản phẩm loại Có sẵn, đang Hiển thị của gian hàng Đang hoạt động, còn hàng mới thêm được vào giỏ. Sản phẩm chỉ nhận đặt riêng không vào giỏ (gửi yêu cầu báo giá, UC-18). | Overview 3.2, 3.8; UC-09 BR-11 (BRD 02) |
| BR-3 | Giỏ của Guest lưu trên trình duyệt; giỏ của Buyer gắn với tài khoản và còn nguyên sau khi đăng xuất, đăng nhập lại, đổi thiết bị. Khi đăng nhập hoặc đăng ký, giỏ Guest được gộp vào giỏ tài khoản: món trùng cộng số lượng, không vượt số còn bán. | Overview 3.8; UC-03 BR-5, UC-04 BR-3 (BRD 01) |
| BR-4 | Giỏ nhóm theo xưởng; mỗi nhóm có nút Mua hàng riêng và chỉ chuyển sang UC-31 các món khả dụng đang được chọn của nhóm đó, nên mỗi lần chỉ thanh toán món của một xưởng. Việc chọn món ở nhóm khác giữ nguyên. | Overview 3.8 |
| BR-5 | Món hết hàng, sản phẩm bị Ẩn, hoặc của xưởng Tạm ngừng chuyển xuống nhóm Không khả dụng và trở lại nhóm xưởng khi hết lý do. Không thêm được sản phẩm của xưởng mình; món của xưởng mình đã có sẵn trong giỏ (do gộp giỏ, hoặc xưởng mới được duyệt) xếp ở nhóm Không khả dụng. | Overview 3.8; Nhóm đề xuất |
| BR-6 | Giỏ luôn hiển thị giá và tình trạng hiện tại của sản phẩm, không giữ giá lúc thêm. Giá được chốt khi đơn được tạo (UC-31). | Nhóm đề xuất |
| BR-7 | Số lượng mỗi món là số nguyên từ 1 đến số còn bán; khi số còn bán giảm thấp hơn số lượng trong giỏ thì số lượng được điều chỉnh về số còn bán khi mở giỏ. | Overview 3.8; Nhóm đề xuất |
| BR-8 | Thêm, đổi số lượng, xóa, chọn món cập nhật bằng API, không tải lại trang. | Đề bài §1 (tiêu chí 4); Overview mục 8 |
| BR-9 | Admin không mua nên không có giỏ hàng. | Overview mục 1; UC-04 BR-6 (BRD 01) |
| BR-10 | Giỏ của Buyer không có thời hạn lưu; giỏ Guest lưu cho đến khi xóa dữ liệu trình duyệt. | Nhóm đề xuất; chờ chốt G-08 |

#### Acceptance Criteria

| ID | Given | When | Then | Covers |
|---|---|---|---|---|
| UC-30-AC-01 | Guest xem sản phẩm "Giỏ mây đan hoa cúc" (250.000 ₫, còn 10) của xưởng Đang hoạt động | Chọn Thêm vào giỏ, rồi mở Giỏ hàng, đổi số lượng thành 2 | Hiện "Đã thêm vào giỏ"; giỏ có nhóm xưởng với thành tiền 500.000 ₫; số còn bán của sản phẩm vẫn là 10; trang không tải lại | Main Flow, BR-1, BR-8 |
| UC-30-AC-02 | Giỏ có món của xưởng A và xưởng B | Mở Giỏ hàng | Món tách thành hai nhóm theo xưởng; mỗi nhóm có tạm tính riêng và nút Mua hàng riêng | Main Flow, BR-4 |
| UC-30-AC-03 | Đã chọn một món ở nhóm xưởng A và một món ở nhóm xưởng B | Chọn Mua hàng ở nhóm A | Chuyển sang UC-31 chỉ với món đã chọn của nhóm A; lựa chọn ở nhóm B giữ nguyên | BR-4 |
| UC-30-AC-04 | Giỏ đã có 3 "Giỏ mây đan hoa cúc", số còn bán 4 | Chọn Thêm vào giỏ hai lần nữa | Lần đầu giỏ có 4; lần sau hiển thị ERR-UC30-01 và số lượng giữ ở 4 | Alt-02, BR-7 |
| UC-30-AC-05 | Giỏ có món, số còn bán vừa giảm từ 5 xuống 2 (đơn của người khác) | Buyer mở Giỏ hàng | Số lượng được điều chỉnh về 2 kèm ERR-UC30-01; các món khác không đổi | Alt-02, BR-7 |
| UC-30-AC-06 | Giỏ có món số lượng 2 | Nhập số lượng 0 hoặc "abc" | Hiển thị ERR-UC30-02; số lượng cũ được giữ | Alt-03 |
| UC-30-AC-07 | Trang sản phẩm mở sẵn, sau đó xưởng Tạm ngừng | Guest chọn Thêm vào giỏ | Hiển thị ERR-UC30-03 nêu lý do xưởng tạm ngừng; giỏ không đổi | Alt-04, BR-2 |
| UC-30-AC-08 | Sản phẩm chỉ nhận đặt riêng, hoặc sản phẩm của xưởng mình | Gọi thao tác thêm vào giỏ trực tiếp | Hiển thị ERR-UC30-03 nêu lý do tương ứng; giỏ không đổi | Alt-04, BR-2, BR-5 |
| UC-30-AC-09 | Giỏ có 3 món: một hết hàng, một sản phẩm bị Ẩn, một của xưởng Tạm ngừng | Mở Giỏ hàng | Ba món nằm ở nhóm Không khả dụng kèm đúng lý do; không chọn được, không tính tạm tính; xóa được | Alt-05, BR-5 |
| UC-30-AC-10 | Món đang ở Không khả dụng vì hết hàng, xưởng nhập thêm hàng | Mở lại Giỏ hàng | Món về nhóm xưởng và chọn được | Alt-05 |
| UC-30-AC-11 | Giỏ trống | Mở Giỏ hàng | Hiện "Giỏ hàng của bạn đang trống" kèm nút Xem sản phẩm; không có nút Mua hàng | Alt-06 |
| UC-30-AC-12 | Giỏ có 2 món | Xóa một món | Món biến khỏi giỏ; số món và tạm tính cập nhật không tải lại trang; nhóm không còn món thì biến luôn | Alt-07, BR-8 |
| UC-30-AC-13 | API giỏ lỗi | Buyer mở Giỏ hàng | Hiển thị ERR-UC30-04 kèm nút Thử lại; giỏ đang hiển thị được giữ | Alt-08 |
| UC-30-AC-14 | Phiên hết hạn khi Buyer đang đổi số lượng | Lưu thay đổi | Hiển thị ERR-UC04-05; đăng nhập xong thì thao tác được thực hiện tiếp | Alt-09 |
| UC-30-AC-15 | Tài khoản Admin | Mở Giỏ hàng hoặc thêm sản phẩm | Hiển thị ERR-UC30-05; không có giỏ | Alt-10, BR-9 |
| UC-30-AC-16 | Guest có 2 món trong giỏ trình duyệt (trong đó 1 món đã có trong giỏ tài khoản với số lượng 1) | Đăng nhập | Giỏ tài khoản chứa cả hai món; món trùng được cộng số lượng nhưng không vượt số còn bán; giỏ trên trình duyệt được xóa | Alt-01, BR-3 |
| UC-30-AC-17 | Guest có món của xưởng X trong giỏ | Đăng nhập bằng tài khoản Artisan của xưởng X | Món của xưởng X nằm ở nhóm Không khả dụng với lý do "Sản phẩm của xưởng bạn"; không bị mất | Alt-01, BR-5 |
| UC-30-AC-18 | Buyer đã có giỏ, rồi đăng xuất | Đăng nhập lại, kể cả từ thiết bị khác | Giỏ còn nguyên | BR-3, BR-10 |
| UC-30-AC-19 | Giá một sản phẩm trong giỏ vừa đổi từ 250.000 ₫ thành 270.000 ₫ | Mở Giỏ hàng | Giỏ hiện giá hiện tại 270.000 ₫ và thành tiền theo giá mới | BR-6 |
| UC-30-AC-20 | Giỏ có 105 dòng món | Xem biểu tượng giỏ | Hiện "99+" | Data Fields |

### UC-31 — Checkout: giao hàng, thanh toán, tạo đơn

#### Detail

| Field | Content |
|---|---|
| Use case ID | UC-31 |
| Description | Buyer thanh toán các món đã chọn của một xưởng: kiểm tra lại món, chọn phương thức giao, nhập thông tin nhận hàng, chọn COD hoặc thanh toán online (ZaloPay sandbox) rồi đặt hàng. Hệ thống giữ hàng, chốt giá, tạo đơn mua hàng và báo xưởng. |
| Actors | Buyer (Artisan thao tác như Buyer với sản phẩm của xưởng khác), Hệ thống (hạn thanh toán, cổng thanh toán) |
| Channel | Khu mua hàng |
| Preconditions | • Đã đăng nhập (Guest xử lý ở Alt-01)<br>• Có ít nhất một món khả dụng được chọn ở một nhóm xưởng trong giỏ (UC-30) |
| Expected | **Thành công:**<br>• Đơn mua hàng được tạo với mã MH-năm-số; số còn bán đã giảm<br>• COD: đơn Chờ xác nhận, xưởng nhận NT-UC31-01<br>• Online: đơn Chờ thanh toán đến khi thanh toán thành công (đơn Chờ xác nhận, trạng thái thanh toán Đã thanh toán) hoặc quá 15 phút (đơn Đã hủy)<br>• Món đã đặt được bỏ khỏi giỏ<br>**Thất bại:**<br>• Hiển thị lỗi; không tạo đơn, không trừ số còn bán, giỏ giữ nguyên |
| Screens | • Thanh toán (bước 1-6)<br>• Chuyển sang cổng thanh toán và quay lại (Alt-02)<br>• Kết quả đặt hàng (bước 7) |
| Notifications | NT-UC31-01, NT-UC31-02, NT-UC31-03 |

#### Main Flow

Trigger: Buyer chọn Mua hàng ở nhóm của một xưởng trong giỏ (UC-30, bước 9).

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Backend | Backend kiểm tra lại từng món đã chọn (đang Hiển thị, còn hàng, gian hàng Đang hoạt động, không phải xưởng của Buyer), số lượng không vượt số còn bán và lấy giá hiện tại. | Danh sách món đủ điều kiện được trả về. |
| 2 | Frontend | Frontend hiển thị trang Thanh toán: tên xưởng; từng món (ảnh, tên, đơn giá, số lượng, thành tiền); họ tên và số điện thoại điền sẵn từ hồ sơ (UC-05) và sửa được; phương thức giao; phương thức thanh toán; ô ghi chú cho xưởng; tiền hàng, phí giao, tổng thanh toán. | Buyer thấy toàn bộ đơn trước khi đặt. |
| 3 | Buyer | Buyer chọn phương thức giao trong các phương thức xưởng đang bật. | Phí giao và tổng thanh toán cập nhật không tải lại trang. |
| 4 | Buyer | Buyer nhập thông tin nhận hàng: với Xưởng tự giao nhập địa chỉ nhận; với Nhận tại xưởng không nhập địa chỉ, màn hình hiển thị địa chỉ xưởng kèm nút Chỉ đường (UC-26, Alt-05). | Thông tin nhận hàng đủ theo phương thức giao đã chọn. |
| 5 | Buyer | Buyer chọn phương thức thanh toán (COD hoặc ZaloPay), có thể nhập ghi chú, rồi chọn Đặt hàng. | Yêu cầu đặt hàng được gửi một lần; nút Đặt hàng bị khóa trong lúc xử lý. |
| 6 | Backend | Backend kiểm tra lại dữ liệu và từng món; trong một thao tác duy nhất trừ số còn bán của từng sản phẩm (giữ hàng), chốt giá, số lượng, phí giao, tổng thanh toán, lưu thông tin sản phẩm và người nhận vào đơn, tạo đơn mua hàng với mã MH-năm-số. COD: đơn ở Chờ xác nhận, trạng thái thanh toán Chưa thanh toán. Online: đơn ở Chờ thanh toán, bắt đầu tính hạn thanh toán 15 phút. Bỏ các món đã đặt khỏi giỏ. | Đơn được tạo; hàng đã được giữ. |
| 7 | Frontend | COD: Frontend hiển thị "Đặt hàng thành công" kèm mã đơn, phương thức giao, phương thức thanh toán, tổng thanh toán và nút Xem đơn hàng (UC-32). Online: Frontend không báo thành công mà hiển thị "Đơn hàng đã được tạo. Vui lòng hoàn tất thanh toán trong 15 phút" kèm mã đơn, tổng thanh toán, thời gian còn lại và nút Thanh toán, rồi chuyển sang cổng (Alt-02). | COD: Buyer biết đơn đã được ghi nhận. Online: Buyer biết đơn chưa hoàn tất cho đến khi thanh toán. |
| 8 | Hệ thống | COD: hệ thống gửi NT-UC31-01 cho xưởng. | Xưởng thấy đơn mới ở Chờ xác nhận (UC-32). |

Outcome: Đơn mua hàng được tạo, hàng được giữ; COD chờ xưởng xác nhận, thanh toán online chờ Buyer thanh toán trong 15 phút.

#### Alternative Flows

##### Alt-01 — Guest chưa đăng nhập

- At step: 1 · Trigger: Guest chọn Mua hàng ở giỏ (UC-30, bước 9).

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Frontend | Frontend chuyển Guest sang form đăng nhập và giữ lại ý định thanh toán (UC-04, Alt-08). | Hiển thị form đăng nhập; giỏ trên trình duyệt chưa bị xóa. |
| 2 | Backend | Sau khi đăng nhập hoặc đăng ký thành công, giỏ trên trình duyệt được gộp vào giỏ tài khoản (UC-30, Alt-01). | Giỏ tài khoản đầy đủ. |

Resume: Quay lại giỏ với các món đã chọn rồi tiếp tục bước 1.

##### Alt-02 — Thanh toán online qua cổng

- At step: 6 · Trigger: Buyer chọn thanh toán ZaloPay.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Backend | Backend tạo giao dịch thanh toán với cổng cho đúng tổng thanh toán của đơn và trả về đường dẫn thanh toán. | Đơn Chờ thanh toán đã có giao dịch. |
| 2 | Frontend | Frontend chuyển Buyer sang trang thanh toán của cổng, đồng thời hiển thị thời gian còn lại của hạn thanh toán. | Buyer thanh toán tại cổng. |
| 3 | Hệ thống | Cổng báo kết quả thanh toán thành công và Backend xác thực kết quả. Đơn chuyển Chờ xác nhận; trạng thái thanh toán Đã thanh toán; bắt đầu tính hạn xác nhận 3 ngày. | Đơn vào hàng chờ xác nhận của xưởng. |
| 4 | Hệ thống | Hệ thống gửi NT-UC31-02 cho Buyer và NT-UC31-01 cho xưởng. | Hai bên biết kết quả. |
| 5 | Frontend | Frontend đưa Buyer về trang kết quả, hiển thị "Đặt hàng thành công" kèm mã đơn và trạng thái thanh toán "Đã thanh toán". | Buyer thấy đơn đã thanh toán. |

Resume: Kết thúc.

##### Alt-03 — ERR-UC31-05 — Cổng báo thanh toán thất bại hoặc Buyer hủy tại cổng

- At step: Alt-02 bước 2 hoặc 3 · Trigger: Cổng trả kết quả xác định là thanh toán thất bại, hoặc Buyer chọn hủy ngay trên trang cổng. Việc Buyer đóng trang cổng mà cổng chưa báo kết quả không thuộc nhánh này (xem Alt-14).

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Frontend | Frontend đưa Buyer về trang đơn, hiển thị ERR-UC31-05 và hai nút Thanh toán lại, Hủy đơn (UC-33, Alt-02). | Đơn vẫn Chờ thanh toán; hạn 15 phút không bị kéo dài. |
| 2 | Backend | Chọn Thanh toán lại thì Backend tra trạng thái giao dịch cũ với cổng trước. Cổng báo đã thành công thì xử lý như Alt-02 bước 3 và không tạo giao dịch mới; chưa thành công thì tạo giao dịch mới cho cùng đơn nếu còn hạn. | Buyer thanh toán lại được trong hạn mà không bị trả tiền hai lần. |

Resume: Chọn Thanh toán lại thì lặp Alt-02 từ bước 2; còn không chờ hết hạn (Alt-04) hoặc hủy theo UC-33.

##### Alt-04 — Quá hạn thanh toán 15 phút

- At step: Sau bước 6 · Trigger: Đơn ở Chờ thanh toán quá 15 phút mà chưa có thanh toán thành công.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Hệ thống | Hệ thống chuyển đơn sang Đã hủy với lý do "Quá hạn thanh toán" và hoàn số lượng đã giữ về số còn bán. | Hàng được trả về cho người khác mua. |
| 2 | Hệ thống | Hệ thống gửi NT-UC31-03 cho Buyer. Buyer vào xem đơn thì thấy đơn Đã hủy và nút Mua lại (đưa các món còn mua được về giỏ). | Buyer biết đơn đã hết hạn. |

Resume: Kết thúc. Nếu cổng báo thanh toán thành công sau khi đơn đã hủy thì xử lý hoàn tiền theo UC-33, Alt-09.

##### Alt-05 — ERR-UC31-01 — Món không còn mua được

- At step: 1 hoặc 6 · Trigger: Một món đã hết hàng, số còn bán thấp hơn số lượng đặt (kể cả khi người khác mua trước lúc Buyer bấm Đặt hàng), sản phẩm bị Ẩn hoặc gian hàng Tạm ngừng.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Backend | Backend không tạo đơn và không trừ số còn bán của món nào (không giữ hàng một phần). | Hiển thị ERR-UC31-01 liệt kê món vướng và số còn bán hiện tại. |
| 2 | Frontend | Buyer quay lại giỏ; món vướng được điều chỉnh hoặc nằm ở nhóm Không khả dụng (UC-30). | Buyer sửa giỏ rồi đặt lại. |

Resume: Quay lại giỏ rồi bắt đầu lại từ bước 1.

##### Alt-06 — ERR-UC31-02 — Thông tin nhận hàng chưa hợp lệ

- At step: 5 · Trigger: Họ tên, số điện thoại hoặc địa chỉ nhận (khi giao tận nơi) bị bỏ trống hoặc sai định dạng; ghi chú quá dài.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Frontend | Frontend kiểm tra từng trường và hiển thị lỗi ngay tại trường sai. | Hiển thị ERR-UC31-02; không gửi yêu cầu. |

Resume: Quay lại bước 4.

##### Alt-07 — ERR-UC31-03 — Phương thức giao không còn dùng

- At step: 3 hoặc 6 · Trigger: Xưởng vừa tắt phương thức Buyer chọn, hoặc phí giao vừa thay đổi so với lúc Buyer mở trang.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Backend | Backend không tạo đơn, trả về danh sách phương thức giao và phí hiện tại. | Hiển thị ERR-UC31-03; tổng thanh toán được cập nhật. |

Resume: Quay lại bước 3.

##### Alt-08 — ERR-UC31-04 — Giá hoặc tổng tiền đã thay đổi

- At step: 6 · Trigger: Giá của một món đã đổi kể từ lúc Buyer mở trang Thanh toán.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Backend | Backend không tạo đơn và trả về giá mới. | Hiển thị ERR-UC31-04; trang cập nhật giá và tổng mới. |

Resume: Quay lại bước 5 để Buyer xem lại và đặt hàng với giá mới.

##### Alt-09 — Cổng thanh toán không khởi tạo được

- At step: Alt-02 bước 1 · Trigger: ZaloPay không phản hồi hoặc từ chối tạo giao dịch.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Backend | Backend thử cổng dự phòng VNPay sandbox nếu được cấu hình; không có hoặc cũng lỗi thì giữ đơn ở Chờ thanh toán. | Hiển thị ERR-UC31-06; Buyer thấy đơn kèm nút Thanh toán lại và Hủy đơn. |

Resume: Buyer chọn Thanh toán lại (Alt-03) hoặc hủy theo UC-33; hạn 15 phút vẫn chạy.

##### Alt-10 — ERR-UC31-07 — Không tính được phí giao bằng GHN

- At step: 3 · Trigger: Buyer chọn GHN (mức Should) nhưng nhà cung cấp lỗi hoặc không tính được phí theo tuyến và cân nặng.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Backend | Backend dùng phí cố định của xưởng cho phương thức giao này. Xưởng chưa đặt phí cố định thì không tính được phí. | Có phí cố định: Frontend hiển thị ERR-UC31-07 (thông báo dùng phí cố định) và cập nhật tổng. Không có: hiển thị ERR-UC31-07 (yêu cầu chọn phương thức khác). |

Resume: Có phí cố định thì tiếp tục bước 4; không có thì quay lại bước 3.

##### Alt-11 — Bấm Đặt hàng nhiều lần

- At step: 5 · Trigger: Buyer bấm Đặt hàng hai lần liên tiếp hoặc gửi trùng yêu cầu.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Backend | Backend chỉ tạo một đơn cho cùng một lần đặt, yêu cầu trùng trả về đơn đã tạo. | Buyer chỉ có một đơn, hàng chỉ bị giữ một lần. |

Resume: Tiếp tục bước 7.

##### Alt-12 — Phiên đăng nhập hết hạn

- At step: 5 · Trigger: Phiên hết hạn khi Buyer đang ở trang Thanh toán.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Backend | Backend đưa Buyer về form đăng nhập và giữ lại thao tác đang làm (UC-04, Alt-08). | Hiển thị ERR-UC04-05; chưa tạo đơn. |

Resume: Sau khi đăng nhập quay lại trang Thanh toán với dữ liệu đã nhập.

##### Alt-13 — Không được thanh toán

- At step: 1 · Trigger: Tài khoản Admin; hoặc Buyer thanh toán món của chính xưởng mình; hoặc xưởng Tạm ngừng.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Backend | Backend từ chối. | Hiển thị lỗi tương ứng (ERR-UC30-05 cho Admin, ERR-UC31-01 cho các trường hợp còn lại); không tạo đơn. |

Resume: Kết thúc.

##### Alt-14 — ERR-UC31-08 — Chưa có kết quả thanh toán

- At step: Alt-02 bước 2 · Trigger: Buyer đóng tab hoặc trang cổng, bấm Quay lại của trình duyệt, hoặc mất kết nối mà cổng chưa báo kết quả. Đóng trang không chứng minh giao dịch thất bại.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Backend | Backend không coi là thất bại; đơn giữ Chờ thanh toán, hạn 15 phút không đổi. Backend tra trạng thái giao dịch với cổng khi Buyer quay lại đơn và định kỳ cho đến hết hạn (BR-7). | Đơn không bị hủy hay đánh dấu thất bại khi chưa có kết quả. |
| 2 | Frontend | Frontend hiển thị ở đơn ERR-UC31-08 kèm các nút Kiểm tra lại kết quả, Thanh toán lại, Hủy đơn (UC-33, Alt-02). | Buyer biết hệ thống đang chờ kết quả từ cổng. |
| 3 | Hệ thống | Cổng báo thành công (kể cả sau khi Buyer đã đóng trang) thì xử lý như Alt-02 bước 3 đến 5; báo thất bại thì như Alt-03; hết hạn vẫn chưa thành công thì như Alt-04. | Kết quả cuối cùng khớp với cổng. |

Resume: Theo kết quả ở bước 3; Buyer chọn Thanh toán lại thì như Alt-03 bước 2.

#### Data Fields

| Field | Format | Validation | Required | Ghi chú |
|---|---|---|---|---|
| Món thanh toán | Chỉ xem | Các món đã chọn của một xưởng, không vượt số còn bán | — | Ảnh, tên, đơn giá, số lượng, thành tiền |
| Họ tên người nhận | Text | 1-100 ký tự | Có | Điền sẵn từ hồ sơ, sửa được; chỉ áp cho đơn này. Ví dụ: "Nguyễn Văn A" |
| Số điện thoại | Text | Số điện thoại Việt Nam hợp lệ, cùng quy tắc UC-05 | Có | Điền sẵn từ hồ sơ, sửa được; chỉ áp cho đơn này |
| Địa chỉ nhận | Chọn theo danh mục cộng Text | Tỉnh hoặc thành phố, quận hoặc huyện, phường hoặc xã chọn theo danh mục (cấp dưới phụ thuộc cấp trên); số nhà, đường 5-255 ký tự | Có khi Xưởng tự giao hoặc GHN; không có khi Nhận tại xưởng | Nhập tại thanh toán; không có sổ địa chỉ (ngoài phạm vi) |
| Phương thức giao | Chọn một | Chỉ phương thức xưởng đang bật | Có | Nhận tại xưởng (0 ₫), Xưởng tự giao (phí cố định của xưởng), GHN (Should) |
| Phương thức thanh toán | Chọn một | COD hoặc ZaloPay | Có | Mặc định COD |
| Ghi chú cho xưởng | Text | Tối đa 500 ký tự | Không | |
| Tiền hàng | VND, chỉ xem | Tổng thành tiền các món | — | Số nguyên |
| Phí giao | VND, chỉ xem | Theo phương thức giao; từ 0 đến 1.000.000 ₫ với Xưởng tự giao | — | |
| Tổng thanh toán | VND, chỉ xem | Tiền hàng cộng phí giao | — | Đây là số tiền gửi sang cổng thanh toán |
| Mã đơn | Chỉ xem | MH-năm-số 4 chữ số, tăng dần | — | Ví dụ: MH-2026-0001 |
| Hạn thanh toán | Thời gian, chỉ xem | 15 phút kể từ lúc tạo đơn online | — | Chỉ có với đơn Chờ thanh toán |

#### Error Messages

| Code | Condition | Message | Display | Flow |
|---|---|---|---|---|
| ERR-UC31-01 | Món không còn mua được | "Một số sản phẩm không còn đủ để đặt: {{items}}. Vui lòng kiểm tra lại giỏ hàng." | Banner | Alt-05, Alt-13 |
| ERR-UC31-02 | Thông tin nhận hàng chưa hợp lệ | "Vui lòng nhập {{field}} hợp lệ." | Inline | Alt-06 |
| ERR-UC31-03 | Phương thức giao không còn dùng | "Phương thức giao hàng đã thay đổi. Vui lòng chọn lại." | Banner | Alt-07 |
| ERR-UC31-04 | Giá đã thay đổi | "Giá một số sản phẩm đã thay đổi. Vui lòng xem lại tổng thanh toán." | Banner | Alt-08 |
| ERR-UC31-05 | Thanh toán thất bại hoặc bị hủy | "Thanh toán chưa thành công. Bạn có thể thanh toán lại trong {{minutesLeft}} phút." | Banner | Alt-03 |
| ERR-UC31-06 | Cổng thanh toán không khởi tạo được | "Hiện chưa kết nối được cổng thanh toán. Vui lòng thử lại." | Banner | Alt-09 |
| ERR-UC31-07 | Không tính được phí GHN | "Chưa tính được phí giao hàng GHN. {{fallback}}" ({{fallback}}: "Phí giao được tính theo mức cố định của xưởng." hoặc "Vui lòng chọn phương thức giao khác.") | Inline | Alt-10 |
| ERR-UC31-08 | Chưa có kết quả thanh toán | "Chưa nhận được kết quả thanh toán. Hệ thống đang kiểm tra; bạn có thể thanh toán lại trong {{minutesLeft}} phút." | Banner | Alt-14 |
| ERR-UC31-09 | Không thể thanh toán lại đơn mua (quá hạn 15 phút, đã thanh toán hoặc đã hủy) | "Không thể thanh toán lại vì đơn hàng đã quá hạn thanh toán, đã thanh toán hoặc đã bị hủy." | Banner | Alt-03 |

#### Notifications

| ID | Recipient | Trigger | Content | Channel | Flow |
|---|---|---|---|---|---|
| NT-UC31-01 | Artisan (xưởng) | Đơn mua hàng mới: COD vừa đặt, hoặc đơn online vừa thanh toán thành công | "Có đơn hàng mới {{orderCode}} ({{paymentNote}}). Vui lòng xác nhận trong 3 ngày." ({{paymentNote}}: "thu tiền khi giao" hoặc "đã thanh toán online") | In-app | Main Flow bước 8, Alt-02 |
| NT-UC31-02 | Buyer | Thanh toán online thành công | "Đơn hàng {{orderCode}} đã thanh toán thành công và đang chờ xưởng xác nhận." | In-app | Alt-02 |
| NT-UC31-03 | Buyer | Đơn quá hạn thanh toán 15 phút và tự hủy | "Đơn hàng {{orderCode}} đã bị hủy do quá hạn thanh toán." | In-app | Alt-04 |

#### Business Rules

| ID | Rule | Source |
|---|---|---|
| BR-1 | Mỗi lần thanh toán chỉ gồm món của một xưởng; mỗi đơn do đúng một xưởng thực hiện. Muốn mua của xưởng khác thì thanh toán riêng. | Overview 3.8 |
| BR-2 | Chỉ Buyer đã đăng nhập mới thanh toán. Admin không mua; Artisan không mua sản phẩm của xưởng mình. Xưởng Tạm ngừng không nhận đơn mới. | Overview mục 1, 3.8; UC-04 BR-6 (BRD 01); UC-13 BR-4 (BRD 02) |
| BR-3 | Hàng được giữ ngay khi đơn được tạo, kể cả COD và đơn Chờ thanh toán, bằng cách trừ số còn bán. Không giữ hàng một phần: một món vướng thì cả đơn không được tạo. Khi nhiều người cùng đặt món cuối, chỉ một đơn được tạo, các đơn còn lại nhận ERR-UC31-01. | Overview 3.8; Nhóm đề xuất |
| BR-4 | Giá, số lượng, tên và ảnh sản phẩm, phí giao, tổng thanh toán, thông tin người nhận được chốt vào đơn lúc tạo; sau đó đổi giá hay sửa, xóa sản phẩm không làm đổi đơn. | Nhóm đề xuất |
| BR-5 | Phương thức giao: Nhận tại xưởng (miễn phí, không cần địa chỉ), Xưởng tự giao (phí cố định do xưởng đặt, từ 0 đến 1.000.000 ₫), GHN (Should). Chỉ hiển thị phương thức xưởng đang bật; xưởng luôn bật ít nhất một phương thức. Phí giao được cộng vào tổng thanh toán của đơn. | Overview 3.8; UC-07 BR-6 (BRD 02) |
| BR-6 | Phương thức thanh toán: COD (xưởng thu khi giao hoặc khi Buyer đến nhận) hoặc thanh toán online qua ZaloPay sandbox; VNPay sandbox là dự phòng. Gọi cổng qua giao diện thanh toán chung để đổi cổng không đổi nghiệp vụ. | Overview 3.8 |
| BR-7 | COD: đơn vào Chờ xác nhận ngay. Online: đơn vào Chờ thanh toán với hạn 15 phút; thanh toán thành công thì đơn sang Chờ xác nhận và trạng thái thanh toán là Đã thanh toán. Chỉ ghi nhận thanh toán sau khi Backend xác thực kết quả từ cổng; không nhận được kết quả báo về thì Backend tra trạng thái giao dịch với cổng. Chỉ kết quả thất bại hoặc hủy do cổng báo mới là thất bại; Buyer đóng trang cổng hoặc chưa có kết quả thì đơn giữ Chờ thanh toán trong hạn (Alt-14). | Overview 3.8 |
| BR-8 | Đơn Chờ thanh toán quá 15 phút tự hủy và hoàn số lượng đã giữ; thanh toán tới sau khi đơn đã hủy thì hoàn tiền (UC-33). Trong hạn, Buyer thanh toán lại hoặc hủy đơn (UC-33) tùy ý, hạn không được kéo dài. | Overview 3.8; Nhóm đề xuất |
| BR-9 | Mã đơn mua hàng có dạng MH-năm-số 4 chữ số tăng dần, không trùng; đơn đặt riêng dùng mã DR (BRD 04). | Nhóm đề xuất |
| BR-10 | Một lần đặt chỉ tạo một đơn; gửi trùng không tạo đơn thứ hai. | Nhóm đề xuất |
| BR-11 | Sau khi đơn được tạo, các món đã đặt được bỏ khỏi giỏ; món khác trong giỏ giữ nguyên. Đơn bị hủy không tự đưa món về giỏ, Buyer dùng nút Mua lại. | Nhóm đề xuất |
| BR-12 | Họ tên, số điện thoại, địa chỉ nhận chỉ lưu theo đơn, không ghi vào hồ sơ. Nội dung thông báo không chứa họ tên, số điện thoại, địa chỉ nhận; xưởng xem chi tiết trong đơn (UC-32). | Nhóm đề xuất; UC-29 BR-11 (BRD 07) |
| BR-13 | MVP1 không thu phí sàn và không đối soát tiền cho xưởng; tiền thanh toán online chỉ ghi nhận trạng thái đơn, không chia cho xưởng. | Overview 3.8 |
| BR-14 | Thanh toán thật (cổng ở môi trường thật) ngoài phạm vi; chỉ dùng sandbox. | Đề bài; Overview 3.8 |

#### Acceptance Criteria

| ID | Given | When | Then | Covers |
|---|---|---|---|---|
| UC-31-AC-01 | Buyer chọn món "Giỏ mây đan hoa cúc" x2 (250.000 ₫) của xưởng Đang hoạt động, chọn Xưởng tự giao phí 30.000 ₫ và COD | Điền họ tên, số điện thoại, địa chỉ rồi chọn Đặt hàng | Tạo đơn MH-2026-0001 Chờ xác nhận, tổng 530.000 ₫, số còn bán giảm 2; hiện "Đặt hàng thành công"; xưởng nhận NT-UC31-01 ghi "thu tiền khi giao"; món đã đặt biến khỏi giỏ | Main Flow, BR-3, BR-7, BR-11 |
| UC-31-AC-02 | Trang Thanh toán mở | Đổi phương thức giao từ Xưởng tự giao sang Nhận tại xưởng | Phí giao về 0 ₫, tổng cập nhật không tải lại trang, ô địa chỉ nhận ẩn, hiện địa chỉ xưởng và nút Chỉ đường | Main Flow, BR-5 |
| UC-31-AC-03 | Xưởng chỉ bật Nhận tại xưởng | Buyer mở trang Thanh toán | Chỉ hiển thị phương thức Nhận tại xưởng | BR-5 |
| UC-31-AC-04 | Hồ sơ Buyer đã có họ tên và số điện thoại | Mở trang Thanh toán | Hai trường được điền sẵn, sửa được; sửa không đổi hồ sơ | Main Flow, BR-12 |
| UC-31-AC-05 | Buyer chọn ZaloPay | Chọn Đặt hàng | Tạo đơn Chờ thanh toán với hạn 15 phút; hiển thị "Đơn hàng đã được tạo. Vui lòng hoàn tất thanh toán trong 15 phút" (không hiện "Đặt hàng thành công") rồi chuyển sang cổng; số còn bán đã giảm | Main Flow, Alt-02, BR-3, BR-7 |
| UC-31-AC-06 | Đơn Chờ thanh toán | Thanh toán thành công tại cổng trong hạn | Đơn Chờ xác nhận, thanh toán Đã thanh toán; Buyer nhận NT-UC31-02, xưởng nhận NT-UC31-01 ghi "đã thanh toán online"; Buyer về trang kết quả và lúc này mới thấy "Đặt hàng thành công" | Alt-02, BR-7 |
| UC-31-AC-07 | Đơn Chờ thanh toán | Buyer chọn hủy tại cổng hoặc cổng báo thanh toán thất bại | Hiển thị ERR-UC31-05; đơn vẫn Chờ thanh toán với hạn cũ; có Thanh toán lại và Hủy đơn | Alt-03, BR-8 |
| UC-31-AC-08 | Đơn Chờ thanh toán, đã 15 phút chưa thanh toán | Hết hạn | Đơn Đã hủy, số còn bán được hoàn lại, Buyer nhận NT-UC31-03; có nút Mua lại | Alt-04, BR-8 |
| UC-31-AC-09 | Đơn đã tự hủy do quá hạn, cổng báo thanh toán thành công muộn | Hệ thống nhận kết quả | Đơn vẫn Đã hủy; khoản tiền được hoàn theo UC-33, Alt-09 | Alt-04, BR-8 |
| UC-31-AC-10 | Còn 1 sản phẩm, hai Buyer cùng bấm Đặt hàng | Hai yêu cầu đến gần nhau | Một đơn được tạo; Buyer còn lại nhận ERR-UC31-01 và không có đơn | Alt-05, BR-3 |
| UC-31-AC-11 | Đơn gồm hai món, một món chỉ còn 1 trong khi Buyer đặt 2 | Chọn Đặt hàng | Không tạo đơn, số còn bán của món còn lại không đổi; hiển thị ERR-UC31-01 liệt kê món vướng | Alt-05, BR-3 |
| UC-31-AC-12 | Thiếu địa chỉ nhận khi giao tận nơi, hoặc số điện thoại sai | Chọn Đặt hàng | Hiển thị ERR-UC31-02 tại trường sai; không gửi yêu cầu | Alt-06 |
| UC-31-AC-13 | Xưởng vừa tắt phương thức Buyer đã chọn | Chọn Đặt hàng | Hiển thị ERR-UC31-03 kèm danh sách và phí hiện tại; không tạo đơn | Alt-07, BR-5 |
| UC-31-AC-14 | Giá một món vừa đổi từ 250.000 ₫ thành 270.000 ₫ khi Buyer đang ở trang Thanh toán | Chọn Đặt hàng | Hiển thị ERR-UC31-04 với giá mới; không tạo đơn; đặt lần sau thì đơn chốt giá 270.000 ₫ | Alt-08, BR-4 |
| UC-31-AC-15 | Đơn đã tạo với giá 250.000 ₫, sau đó xưởng đổi giá hoặc xóa sản phẩm | Buyer xem đơn | Đơn vẫn hiển thị giá, tên, ảnh lúc đặt | BR-4 |
| UC-31-AC-16 | Cổng ZaloPay lỗi, VNPay không cấu hình | Chọn Đặt hàng với thanh toán online | Hiển thị ERR-UC31-06; đơn Chờ thanh toán kèm nút Thanh toán lại và Hủy đơn; hạn vẫn chạy | Alt-09 |
| UC-31-AC-17 | Buyer chọn GHN (Should) nhưng nhà cung cấp lỗi; trường hợp 1: xưởng có phí cố định, trường hợp 2: không có | Chọn GHN | Trường hợp 1: hiển thị ERR-UC31-07, tổng tính theo phí cố định. Trường hợp 2: hiển thị ERR-UC31-07 yêu cầu chọn phương thức khác | Alt-10, BR-5 |
| UC-31-AC-18 | Buyer bấm Đặt hàng hai lần | Hai yêu cầu liên tiếp | Chỉ có một đơn; hàng chỉ bị giữ một lần | Alt-11, BR-10 |
| UC-31-AC-19 | Phiên hết hạn trên trang Thanh toán | Chọn Đặt hàng | Hiển thị ERR-UC04-05; đăng nhập xong quay lại trang Thanh toán với dữ liệu đã nhập; chưa có đơn | Alt-12 |
| UC-31-AC-20 | Guest có món trong giỏ | Chọn Mua hàng, rồi đăng nhập | Về giỏ đã gộp với các món đã chọn, rồi vào trang Thanh toán | Alt-01 |
| UC-31-AC-21 | Tài khoản Admin; hoặc món của xưởng mình; hoặc xưởng Tạm ngừng | Thanh toán | Bị từ chối bằng lỗi tương ứng; không tạo đơn | Alt-13, BR-2 |
| UC-31-AC-22 | Giỏ có món của hai xưởng | Chọn Mua hàng ở một nhóm | Chỉ món của nhóm đó vào thanh toán và tạo đơn; món nhóm kia còn trong giỏ | BR-1, BR-11 |
| UC-31-AC-23 | Đơn vừa tạo | Xem nội dung NT-UC31-01 | Chỉ có mã đơn và ghi chú thanh toán; không có họ tên, số điện thoại, địa chỉ | BR-12 |
| UC-31-AC-24 | Hai đơn trong cùng năm | Tạo lần lượt | Mã khác nhau và tăng dần theo dạng MH-2026-xxxx | BR-9 |
| UC-31-AC-25 | Trang Thanh toán mở | Xem phương thức thanh toán và tổng thanh toán | Chỉ có COD và ZaloPay, mặc định COD; thanh toán online chạy trên môi trường sandbox; tổng chỉ gồm tiền hàng và phí giao, không có phí sàn | BR-6, BR-13, BR-14 |
| UC-31-AC-26 | Đơn Chờ thanh toán, Buyer đang ở trang cổng | Buyer đóng tab cổng, cổng chưa báo kết quả | Đơn vẫn Chờ thanh toán, hạn không đổi, không bị đánh dấu thất bại; Buyer quay lại đơn thấy ERR-UC31-08 kèm Kiểm tra lại kết quả, Thanh toán lại, Hủy đơn | Alt-14, BR-7 |
| UC-31-AC-27 | Như UC-31-AC-26, sau đó cổng báo thanh toán thành công trong hạn | Hệ thống nhận kết quả hoặc Buyer chọn Kiểm tra lại kết quả | Đơn Chờ xác nhận, thanh toán Đã thanh toán; Buyer nhận NT-UC31-02, xưởng nhận NT-UC31-01 | Alt-14, Alt-02, BR-7 |
| UC-31-AC-28 | Như UC-31-AC-26, giao dịch cũ thực tế đã thành công | Buyer chọn Thanh toán lại | Hệ thống tra giao dịch cũ, ghi nhận thành công, không tạo giao dịch mới; Buyer không bị trừ tiền hai lần | Alt-03, Alt-14, BR-7 |

### UC-32 — Quản lý đơn mua hàng

#### Detail

| Field | Content |
|---|---|
| Use case ID | UC-32 |
| Description | Xưởng xem đơn mua hàng của mình, xác nhận hoặc từ chối đơn, chuẩn bị, giao, xác nhận đã thu COD. Buyer xem đơn của mình, theo dõi trạng thái và xác nhận đã nhận để đơn Hoàn tất. Hệ thống tự hủy đơn xưởng không xác nhận sau 3 ngày và tự hoàn tất đơn Buyer không xác nhận sau 7 ngày. |
| Actors | Artisan (xưởng), Buyer, Hệ thống |
| Channel | Kênh người bán (Đơn bán hàng), Khu mua hàng (Đơn mua của tôi) |
| Preconditions | • Đã đăng nhập<br>• Artisan: gian hàng có đơn mua hàng (xưởng Tạm ngừng vẫn xử lý đơn đang thực hiện)<br>• Buyer: tài khoản có đơn mua hàng |
| Expected | **Thành công:**<br>• Trạng thái đơn và trạng thái thanh toán cập nhật đúng, có ghi lịch sử<br>• Bên còn lại nhận thông báo<br>• Đơn Hoàn tất khi Buyer xác nhận đã nhận hoặc sau 7 ngày kể từ Đã giao<br>**Thất bại:**<br>• Hiển thị lỗi, trạng thái đơn giữ nguyên |
| Screens | • Đơn bán hàng: danh sách và chi tiết (bước 1-6)<br>• Đơn mua của tôi: danh sách và chi tiết (bước 7-8) |
| Notifications | NT-UC32-01 đến NT-UC32-08 |

#### Main Flow

Trigger: Artisan mở Đơn bán hàng trong Kênh người bán (thường sau khi nhận NT-UC31-01).

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Artisan | Artisan mở Đơn bán hàng. | Danh sách đơn mua hàng của xưởng hiển thị, đơn mới nhất trước. |
| 2 | Frontend | Frontend hiển thị mỗi đơn: mã đơn, ngày đặt, số món, tổng thanh toán, phương thức giao, phương thức thanh toán, trạng thái đơn, trạng thái thanh toán; đơn Chờ xác nhận có thời gian còn lại của hạn xác nhận. Có lọc theo trạng thái và tìm theo mã đơn. | Artisan thấy đơn nào cần xử lý trước. |
| 3 | Artisan | Artisan mở một đơn Chờ xác nhận. | Chi tiết đơn hiển thị: các món theo bản chụp lúc đặt, họ tên, số điện thoại, địa chỉ nhận (hoặc "Nhận tại xưởng"), phương thức giao và phí, phương thức và trạng thái thanh toán, ghi chú của Buyer, lịch sử trạng thái. |
| 4 | Artisan | Artisan chọn Xác nhận đơn. | Yêu cầu xác nhận được gửi. |
| 5 | Backend | Backend kiểm tra đơn thuộc xưởng và còn Chờ xác nhận; chuyển đơn sang Đang chuẩn bị, ghi lịch sử. | Đơn Đang chuẩn bị. |
| 6 | Hệ thống | Hệ thống gửi NT-UC32-01 cho Buyer. | Buyer biết xưởng đã nhận đơn. |
| 7 | Artisan | Artisan chuẩn bị xong hàng và giao bằng Xưởng tự giao: chọn Bắt đầu giao; backend chuyển đơn sang Đang giao, gửi NT-UC32-03 cho Buyer. Khi giao thành công, Artisan chọn Đã giao (đơn COD phải xác nhận đã thu đủ tiền cùng lúc); backend chuyển đơn sang Đã giao, đơn COD chuyển trạng thái thanh toán sang Đã thanh toán, bắt đầu tính hạn 7 ngày, gửi NT-UC32-04 cho Buyer. | Đơn Đã giao; Buyer được báo. |
| 8 | Buyer | Buyer mở Đơn mua của tôi, chọn đơn rồi chọn Đã nhận hàng. Backend chuyển đơn sang Hoàn tất và gửi NT-UC32-08 cho xưởng. | Đơn Hoàn tất; giao dịch kết thúc. |

Outcome: Đơn đi từ Chờ xác nhận đến Hoàn tất; mỗi lần đổi trạng thái đều có lịch sử và thông báo cho bên còn lại.

#### Alternative Flows

##### Alt-01 — Xưởng từ chối đơn

- At step: 4 · Trigger: Artisan chọn Từ chối ở đơn Chờ xác nhận (ví dụ hết hàng thực tế, không giao được tới địa chỉ).

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Frontend | Frontend hiển thị hộp nhập lý do bắt buộc. | Artisan nhập lý do và xác nhận. |
| 2 | Backend | Backend chuyển đơn sang Bị từ chối kèm lý do, hoàn số lượng đã giữ về số còn bán. Đơn đã thanh toán online thì hoàn tiền (UC-33, Alt-08). | Đơn Bị từ chối; hàng được trả về cho người khác mua. |
| 3 | Hệ thống | Hệ thống gửi NT-UC32-02 cho Buyer kèm lý do. | Buyer biết lý do. |

Resume: Kết thúc.

##### Alt-02 — Nhận tại xưởng

- At step: 7 · Trigger: Đơn có phương thức giao Nhận tại xưởng.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Artisan | Khi Buyer đến lấy hàng, Artisan chọn Đã bàn giao tại xưởng (đơn COD xác nhận đã thu đủ tiền cùng lúc). Không có bước Đang giao. | Yêu cầu đổi trạng thái được gửi. |
| 2 | Backend | Backend chuyển đơn từ Đang chuẩn bị sang Đã giao, đơn COD chuyển trạng thái thanh toán sang Đã thanh toán, bắt đầu tính hạn 7 ngày, gửi NT-UC32-04 cho Buyer. | Đơn Đã giao. |

Resume: Tiếp tục bước 8.

##### Alt-03 — Quá 3 ngày xưởng chưa xác nhận

- At step: Sau bước 3 · Trigger: Đơn ở Chờ xác nhận quá 3 ngày mà xưởng chưa xác nhận hoặc từ chối.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Hệ thống | Hệ thống chuyển đơn sang Đã hủy với lý do "Xưởng không xác nhận trong 3 ngày", hoàn số lượng đã giữ; đơn đã thanh toán online thì hoàn tiền (UC-33, Alt-08). | Đơn Đã hủy; hàng được trả về. |
| 2 | Hệ thống | Hệ thống gửi NT-UC32-06 cho Buyer và NT-UC32-07 cho xưởng. | Hai bên biết kết quả. |

Resume: Kết thúc.

##### Alt-04 — Tự hoàn tất sau 7 ngày

- At step: Sau bước 7 · Trigger: Đơn ở Đã giao quá 7 ngày mà Buyer chưa xác nhận đã nhận.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Hệ thống | Hệ thống chuyển đơn sang Hoàn tất. | Đơn Hoàn tất. |
| 2 | Hệ thống | Hệ thống gửi NT-UC32-05 cho Buyer và NT-UC32-08 cho xưởng. | Hai bên biết đơn đã hoàn tất. |

Resume: Kết thúc.

##### Alt-05 — Buyer xem đơn

- At step: 8 · Trigger: Buyer mở Đơn mua của tôi tại bất kỳ lúc nào.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Frontend | Frontend hiển thị danh sách đơn của Buyer (mã đơn, xưởng, số món, tổng thanh toán, trạng thái đơn, trạng thái thanh toán), lọc theo trạng thái; mở đơn thì xem món theo bản chụp, thông tin nhận hàng, phương thức giao và thanh toán, lịch sử trạng thái, lý do từ chối hoặc hủy nếu có. | Buyer theo dõi được đơn của mình. |
| 2 | Frontend | Tùy trạng thái, màn hình có nút phù hợp: Chờ thanh toán có Thanh toán lại, Hủy đơn (UC-31, Alt-03); Chờ xác nhận có Hủy đơn (UC-33); Đã giao có Đã nhận hàng; đơn Đã hủy hoặc Bị từ chối có Mua lại (đưa các món còn mua được về giỏ). | Buyer biết bước tiếp theo. |

Resume: Tiếp tục bước 8 hoặc kết thúc.

##### Alt-06 — Hủy đơn

- At step: 3 hoặc 8 · Trigger: Buyer chọn Hủy đơn ở đơn Chờ xác nhận; hoặc Artisan chọn Hủy đơn ở đơn Đang chuẩn bị.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Hệ thống | Chuyển sang UC-33, Main Flow (Buyer hủy đơn Chờ xác nhận) hoặc UC-33, Alt-01 (xưởng hủy đơn Đang chuẩn bị). | Xử lý hủy đơn theo UC-33. |

Resume: Kết thúc.

##### Alt-07 — ERR-UC32-01 — Đơn đã đổi trạng thái

- At step: 4, 7 hoặc 8 · Trigger: Trạng thái đơn đã đổi trước khi thao tác được xử lý (Buyer vừa hủy, hệ thống vừa tự hủy, hai thiết bị cùng thao tác).

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Backend | Backend không đổi trạng thái. | Hiển thị ERR-UC32-01. |
| 2 | Frontend | Frontend tải lại đơn với trạng thái mới. | Người dùng thấy đúng tình trạng đơn. |

Resume: Kết thúc.

##### Alt-08 — ERR-UC32-02 — Lý do không hợp lệ

- At step: Alt-01 bước 1 hoặc UC-33 · Trigger: Lý do bỏ trống hoặc ngoài độ dài cho phép.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Frontend | Frontend không gửi yêu cầu. | Hiển thị ERR-UC32-02 tại ô lý do. |

Resume: Quay lại nhập lý do.

##### Alt-09 — ERR-UC32-03 — Chuyển trạng thái không hợp lệ

- At step: 4, 7 hoặc 8 · Trigger: Thao tác không đúng thứ tự trạng thái (ví dụ Đã giao khi đơn còn Chờ xác nhận, Bắt đầu giao với đơn Nhận tại xưởng, Đã nhận hàng khi đơn chưa Đã giao), thường do trang mở từ trước hoặc gọi trực tiếp.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Backend | Backend từ chối. | Hiển thị ERR-UC32-03; đơn giữ nguyên. |

Resume: Kết thúc.

##### Alt-10 — ERR-UC32-04 — Chưa xác nhận đã thu tiền COD

- At step: 7 hoặc Alt-02 bước 1 · Trigger: Artisan chọn Đã giao hoặc Đã bàn giao tại xưởng cho đơn COD nhưng chưa xác nhận đã thu đủ tiền.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Frontend | Frontend không gửi yêu cầu. | Hiển thị ERR-UC32-04; đơn giữ nguyên. |

Resume: Artisan xác nhận đã thu tiền rồi chọn lại.

##### Alt-11 — ERR-UC32-05 — Không xem được đơn

- At step: 1, 3 hoặc 8 · Trigger: Người dùng mở đơn không thuộc xưởng hoặc tài khoản của mình, hoặc đơn không tồn tại; hoặc tài khoản Admin mở danh sách đơn.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Backend | Backend từ chối và không tiết lộ đơn có tồn tại hay không. | Hiển thị ERR-UC32-05. |

Resume: Kết thúc.

##### Alt-12 — Không có đơn

- At step: 2 hoặc Alt-05 bước 1 · Trigger: Chưa có đơn nào, hoặc bộ lọc không có kết quả.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Frontend | Frontend hiện "Chưa có đơn hàng nào" hoặc "Không có đơn phù hợp bộ lọc" kèm nút Xóa bộ lọc; Buyer chưa có đơn thì có thêm nút Xem sản phẩm. | Không có danh sách trống vô nghĩa. |

Resume: Kết thúc.

##### Alt-13 — ERR-UC32-06 — Không tải được đơn

- At step: 1, 3 hoặc Alt-05 bước 1 · Trigger: Tải danh sách hoặc chi tiết đơn bị lỗi.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Frontend | Frontend hiện lỗi và nút Thử lại ngay tại khu vực danh sách hoặc chi tiết. | Hiển thị ERR-UC32-06; phần còn lại của trang dùng được. |

Resume: Chọn Thử lại thì lặp bước đang làm.

##### Alt-14 — Phiên đăng nhập hết hạn

- At step: 4, 7 hoặc 8 · Trigger: Phiên hết hạn khi người dùng đang thao tác.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Backend | Backend đưa người dùng về form đăng nhập và giữ lại thao tác đang làm (UC-04, Alt-08). | Hiển thị ERR-UC04-05; trạng thái đơn chưa đổi. |

Resume: Sau khi đăng nhập quay lại đúng đơn đang xử lý.

#### Data Fields

| Field | Format | Validation | Required | Ghi chú |
|---|---|---|---|---|
| Mã đơn | Chỉ xem | Dạng MH-năm-số | — | Ví dụ: MH-2026-0001 |
| Trạng thái đơn | Chỉ xem | Chờ thanh toán, Chờ xác nhận, Đang chuẩn bị, Đang giao, Đã giao, Hoàn tất, Bị từ chối, Đã hủy | — | |
| Trạng thái thanh toán | Chỉ xem | Chưa thanh toán, Đã thanh toán, Đã hoàn tiền | — | Độc lập với trạng thái đơn; Đã đặt cọc chỉ có với đơn đặt riêng |
| Hạn xác nhận còn lại | Thời gian, chỉ xem | 3 ngày kể từ lúc đơn vào Chờ xác nhận | — | Chỉ hiện ở đơn Chờ xác nhận |
| Lý do từ chối | Text | 10-500 ký tự | Có khi từ chối | Hiện cho Buyer ở NT-UC32-02 và chi tiết đơn |
| Xác nhận đã thu đủ tiền | Hộp chọn | Phải chọn | Có với đơn COD khi chuyển sang Đã giao | Chỉ có ở đơn COD; không chọn thì không đổi được trạng thái |
| Thông tin người nhận | Chỉ xem | Họ tên, số điện thoại, địa chỉ nhận | — | Chỉ Buyer và xưởng của đơn xem |
| Lịch sử trạng thái | Chỉ xem | Mỗi dòng gồm trạng thái, thời điểm, người thực hiện (Buyer, xưởng, hệ thống), lý do nếu có | — | Mới nhất trước |
| Lọc trạng thái | Chọn một | Tất cả hoặc một trong tám trạng thái | Không | Lọc không tải lại trang |
| Tìm theo mã đơn | Text | Tối đa 20 ký tự | Không | Khớp một phần |

#### Error Messages

| Code | Condition | Message | Display | Flow |
|---|---|---|---|---|
| ERR-UC32-01 | Đơn đã đổi trạng thái | "Đơn hàng vừa được cập nhật. Vui lòng xem lại trạng thái mới." | Banner | Alt-07 |
| ERR-UC32-02 | Lý do không hợp lệ | "Vui lòng nhập lý do từ 10 đến 500 ký tự." | Inline | Alt-08 |
| ERR-UC32-03 | Chuyển trạng thái không hợp lệ | "Không thể chuyển đơn từ {{from}} sang {{to}}." | Toast | Alt-09 |
| ERR-UC32-04 | Chưa xác nhận đã thu tiền | "Vui lòng xác nhận đã thu đủ {{amount}} trước khi đánh dấu đã giao." | Inline | Alt-10 |
| ERR-UC32-05 | Không xem được đơn | "Không tìm thấy đơn hàng hoặc bạn không có quyền xem đơn này." | Trang lỗi | Alt-11 |
| ERR-UC32-06 | Không tải được đơn | "Không tải được đơn hàng. Vui lòng thử lại." | Inline | Alt-13 |

#### Notifications

| ID | Recipient | Trigger | Content | Channel | Flow |
|---|---|---|---|---|---|
| NT-UC32-01 | Buyer | Xưởng xác nhận đơn (Đang chuẩn bị) | "Xưởng đã xác nhận đơn hàng {{orderCode}} và đang chuẩn bị hàng." | In-app | Main Flow bước 6 |
| NT-UC32-02 | Buyer | Xưởng từ chối đơn (Bị từ chối) | "Xưởng đã từ chối đơn hàng {{orderCode}}. Lý do: {{reason}}." | In-app | Alt-01 |
| NT-UC32-03 | Buyer | Xưởng bắt đầu giao (Đang giao) | "Đơn hàng {{orderCode}} đang được giao." | In-app | Main Flow bước 7 |
| NT-UC32-04 | Buyer | Đơn chuyển Đã giao | "Đơn hàng {{orderCode}} đã giao. Vui lòng xác nhận đã nhận hàng; đơn tự hoàn tất sau 7 ngày." | In-app | Main Flow bước 7, Alt-02 |
| NT-UC32-05 | Buyer | Đơn tự hoàn tất sau 7 ngày | "Đơn hàng {{orderCode}} đã tự động hoàn tất." | In-app | Alt-04 |
| NT-UC32-06 | Buyer | Đơn tự hủy do xưởng không xác nhận trong 3 ngày | "Đơn hàng {{orderCode}} đã bị hủy do xưởng không xác nhận trong 3 ngày." | In-app | Alt-03 |
| NT-UC32-07 | Artisan (xưởng) | Đơn tự hủy do xưởng không xác nhận trong 3 ngày | "Đơn hàng {{orderCode}} đã bị hủy do quá 3 ngày chưa được xác nhận." | In-app | Alt-03 |
| NT-UC32-08 | Artisan (xưởng) | Đơn Hoàn tất (Buyer xác nhận hoặc tự hoàn tất) | "Đơn hàng {{orderCode}} đã hoàn tất." | In-app | Main Flow bước 8, Alt-04 |

#### Business Rules

| ID | Rule | Source |
|---|---|---|
| BR-1 | Đơn mua hàng chỉ chuyển trạng thái theo các bước: Chờ thanh toán sang Chờ xác nhận (thanh toán thành công) hoặc Đã hủy (quá 15 phút); Chờ xác nhận sang Đang chuẩn bị (xưởng xác nhận), Bị từ chối (xưởng từ chối), Đã hủy (Buyer hủy hoặc quá 3 ngày); Đang chuẩn bị sang Đang giao, Đã giao (nhận tại xưởng) hoặc Đã hủy (xưởng hủy); Đang giao sang Đã giao; Đã giao sang Hoàn tất. Bị từ chối, Đã hủy, Hoàn tất là trạng thái cuối. | Overview mục 4 |
| BR-2 | Chỉ xưởng của đơn được xác nhận, từ chối, bắt đầu giao, đánh dấu Đã giao. Buyer chỉ xác nhận đã nhận và hủy theo UC-33. Hệ thống tự hủy (15 phút, 3 ngày) và tự hoàn tất (7 ngày). Admin không thao tác đơn và không xem qua UC-32 (chỉ theo dõi ở Trang quản trị qua UC-35, nhóm `09-portal`). | Overview mục 1, 3.8; UC-35 (`09-portal`) |
| BR-3 | Xưởng từ chối đơn phải nhập lý do và chỉ từ chối được ở Chờ xác nhận. Từ chối hoàn số lượng đã giữ; đơn đã thanh toán online được hoàn tiền (UC-33). | Overview 3.8 |
| BR-4 | Hạn xác nhận 3 ngày tính từ lúc đơn vào Chờ xác nhận (COD: khi tạo đơn; online: khi thanh toán thành công). Hết hạn đơn tự hủy (trạng thái Đã hủy, không phải Bị từ chối), hoàn số lượng và hoàn tiền nếu đã thanh toán online. | Overview 3.8, mục 4 |
| BR-5 | Nhận tại xưởng đi thẳng từ Đang chuẩn bị sang Đã giao, không qua Đang giao. Xưởng tự giao đi qua Đang giao. | Overview mục 4 |
| BR-6 | Đơn COD: xưởng xác nhận đã thu đủ tiền khi đánh dấu Đã giao, lúc đó trạng thái thanh toán mới chuyển Đã thanh toán. Đơn online đã Đã thanh toán từ checkout. Giao hàng không tự chứng minh đã thanh toán. | Overview 3.8, mục 4 |
| BR-7 | Buyer xác nhận đã nhận ở đơn Đã giao thì đơn Hoàn tất; sau 7 ngày kể từ Đã giao mà Buyer chưa xác nhận thì hệ thống tự hoàn tất. | Overview mục 4 |
| BR-8 | Trạng thái thanh toán và trạng thái đơn đi riêng, đổi độc lập theo các sự kiện thanh toán, giao COD, hoàn tiền. | Overview mục 4 |
| BR-9 | Mỗi lần đổi trạng thái ghi lịch sử (trạng thái, thời điểm, người hoặc hệ thống thực hiện, lý do nếu có) và gửi thông báo cho bên còn lại. Nội dung thông báo không chứa họ tên, số điện thoại, địa chỉ nhận. | Overview 3.7; UC-29 BR-11 (BRD 07) |
| BR-10 | Xưởng chỉ xem được đơn của xưởng mình; Buyer chỉ xem được đơn của tài khoản mình; họ tên, số điện thoại, địa chỉ nhận chỉ hiện cho hai bên này. Kiểm tra quyền ở mọi thao tác, không chỉ ẩn nút. | Overview mục 1, mục 8 (An ninh) |
| BR-11 | Xưởng Tạm ngừng không nhận đơn mới nhưng vẫn xử lý đơn đang thực hiện (xác nhận, giao, xác nhận đã thu, hủy theo UC-33). | Overview 3.2; UC-13 BR-4 (BRD 02) |
| BR-12 | Từ Đang giao trở đi không hủy được. Xử lý sau giao (thiếu, hỏng, trả hàng) thuộc đổi trả, tranh chấp (EN-17), ngoài phạm vi MVP1. | Overview 3.8 |
| BR-13 | Đơn hiển thị sản phẩm theo bản chụp lúc đặt (tên, ảnh, giá, số lượng); có liên kết về sản phẩm khi sản phẩm còn công khai. | UC-31 BR-4; Nhóm đề xuất |
| BR-14 | Danh sách đơn tải từng phần, lọc và tìm không tải lại trang. Đơn mới có thể xem ngay sau khi nhận thông báo, không cần tải lại cả trang. | Đề bài §1 (tiêu chí 4); Overview mục 8 |
| BR-15 | Với GHN (Should), xưởng vẫn cập nhật trạng thái Đang giao, Đã giao bằng tay như Xưởng tự giao; tự động cập nhật qua webhook GHN chưa mô tả trong BRD này. | Overview mục 5 |

#### Acceptance Criteria

| ID | Given | When | Then | Covers |
|---|---|---|---|---|
| UC-32-AC-01 | Xưởng có đơn COD Chờ xác nhận, giao bằng Xưởng tự giao | Artisan mở đơn, chọn Xác nhận đơn | Đơn Đang chuẩn bị, có dòng lịch sử; Buyer nhận NT-UC32-01 | Main Flow, BR-9 |
| UC-32-AC-02 | Đơn Đang chuẩn bị, Xưởng tự giao | Artisan chọn Bắt đầu giao | Đơn Đang giao; Buyer nhận NT-UC32-03 | Main Flow, BR-1 |
| UC-32-AC-03 | Đơn COD Đang giao | Artisan chọn Đã giao, đã tích xác nhận đã thu đủ tiền | Đơn Đã giao, trạng thái thanh toán Đã thanh toán, hạn 7 ngày bắt đầu; Buyer nhận NT-UC32-04 | Main Flow, BR-6 |
| UC-32-AC-04 | Đơn COD Đang giao | Artisan chọn Đã giao nhưng chưa tích xác nhận đã thu | Hiển thị ERR-UC32-04; đơn vẫn Đang giao, thanh toán vẫn Chưa thanh toán | Alt-10, BR-6 |
| UC-32-AC-05 | Đơn online đã thanh toán, Đang giao | Artisan chọn Đã giao | Đơn Đã giao; không yêu cầu xác nhận thu tiền; trạng thái thanh toán vẫn Đã thanh toán | BR-6 |
| UC-32-AC-06 | Đơn Đã giao | Buyer chọn Đã nhận hàng | Đơn Hoàn tất; xưởng nhận NT-UC32-08 | Main Flow, BR-7 |
| UC-32-AC-07 | Đơn Đã giao, Buyer không xác nhận | Qua 7 ngày kể từ Đã giao | Đơn tự Hoàn tất; Buyer nhận NT-UC32-05, xưởng nhận NT-UC32-08 | Alt-04, BR-7 |
| UC-32-AC-08 | Đơn Chờ xác nhận | Artisan chọn Từ chối và nhập lý do 20 ký tự | Đơn Bị từ chối kèm lý do; số còn bán tăng lại; Buyer nhận NT-UC32-02 kèm lý do | Alt-01, BR-3 |
| UC-32-AC-09 | Đơn Chờ xác nhận đã thanh toán online | Xưởng từ chối | Đơn Bị từ chối và khoản thanh toán được hoàn theo UC-33, Alt-08 | Alt-01, BR-3 |
| UC-32-AC-10 | Hộp lý do từ chối bỏ trống | Xác nhận từ chối | Hiển thị ERR-UC32-02; đơn không đổi | Alt-08, BR-3 |
| UC-32-AC-11 | Đơn COD Chờ xác nhận, xưởng chưa phản hồi | Qua 3 ngày kể từ lúc tạo đơn | Đơn Đã hủy lý do "Xưởng không xác nhận trong 3 ngày"; số còn bán được hoàn; Buyer nhận NT-UC32-06, xưởng nhận NT-UC32-07 | Alt-03, BR-4 |
| UC-32-AC-12 | Đơn online Chờ xác nhận, đã thanh toán, xưởng chưa phản hồi | Qua 3 ngày kể từ lúc thanh toán | Đơn Đã hủy và khoản thanh toán được hoàn theo UC-33, Alt-08 | Alt-03, BR-4 |
| UC-32-AC-13 | Đơn Nhận tại xưởng, Đang chuẩn bị | Buyer đến lấy; Artisan chọn Đã bàn giao tại xưởng | Đơn Đã giao, không qua Đang giao; Buyer nhận NT-UC32-04 | Alt-02, BR-5 |
| UC-32-AC-14 | Đơn Nhận tại xưởng | Gọi thao tác Bắt đầu giao | Hiển thị ERR-UC32-03; đơn giữ nguyên | Alt-09, BR-5 |
| UC-32-AC-15 | Buyer vừa hủy đơn Chờ xác nhận ở một tab; xưởng mở trang cũ ở tab khác | Artisan chọn Xác nhận đơn | Hiển thị ERR-UC32-01; trang tải lại với trạng thái Đã hủy | Alt-07 |
| UC-32-AC-16 | Đơn Chờ xác nhận | Gọi thao tác Đã nhận hàng | Hiển thị ERR-UC32-03; đơn giữ nguyên | Alt-09, BR-1 |
| UC-32-AC-17 | Đơn của xưởng A | Artisan xưởng B mở đơn; hoặc Buyer khác mở đơn; hoặc Admin mở danh sách đơn | Hiển thị ERR-UC32-05; không lộ đơn có tồn tại | Alt-11, BR-10 |
| UC-32-AC-18 | Xưởng có 30 đơn ở nhiều trạng thái | Lọc Đang chuẩn bị rồi tìm "MH-2026-0007" | Danh sách thu hẹp không tải lại trang; đơn Chờ xác nhận hiển thị hạn còn lại | Main Flow, BR-14 |
| UC-32-AC-19 | Xưởng chưa có đơn; Buyer chưa có đơn | Mở danh sách đơn | Hiện "Chưa có đơn hàng nào"; Buyer có nút Xem sản phẩm | Alt-12 |
| UC-32-AC-20 | API đơn lỗi | Mở danh sách hoặc chi tiết đơn | Hiển thị ERR-UC32-06 kèm nút Thử lại | Alt-13 |
| UC-32-AC-21 | Phiên hết hạn khi xưởng đang xác nhận đơn | Chọn Xác nhận đơn | Hiển thị ERR-UC04-05; đăng nhập xong quay lại đơn đó, trạng thái chưa đổi | Alt-14 |
| UC-32-AC-22 | Xưởng đang Tạm ngừng, có đơn Đang chuẩn bị | Artisan chọn Bắt đầu giao | Thao tác thành công; đơn Đang giao | BR-11 |
| UC-32-AC-23 | Đơn Chờ thanh toán; đơn Đã hủy | Buyer mở đơn | Chờ thanh toán có Thanh toán lại và Hủy đơn; Đã hủy có Mua lại, món còn mua được vào giỏ | Alt-05 |
| UC-32-AC-24 | Đơn Đang giao | Gọi thao tác hủy từ Buyer hoặc xưởng | Bị từ chối theo UC-33; đơn giữ nguyên | BR-12 |
| UC-32-AC-25 | Đơn có người nhận tên "Nguyễn Văn A", số 0912345678 | Xem nội dung NT-UC32-01 đến NT-UC32-08 | Chỉ có mã đơn, lý do (NT-UC32-02) và nội dung nêu ở bảng; không có tên, số điện thoại, địa chỉ | BR-9 |
| UC-32-AC-26 | Sản phẩm trong đơn vừa bị Ẩn và đổi giá | Buyer xem đơn | Đơn hiển thị tên, ảnh, giá lúc đặt; liên kết sản phẩm không hiển thị vì sản phẩm không còn công khai | BR-13 |
| UC-32-AC-27 | Đơn Chờ xác nhận; đơn Đang chuẩn bị | Buyer chọn Hủy đơn ở đơn thứ nhất; Artisan chọn Hủy đơn ở đơn thứ hai | Hệ thống chuyển sang UC-33 (Buyer hủy đơn Chờ xác nhận; xưởng hủy đơn Đang chuẩn bị) | Alt-06 |
| UC-32-AC-28 | Đơn Chờ xác nhận của xưởng A | Buyer gọi thao tác Xác nhận đơn hoặc Bắt đầu giao | Hiển thị ERR-UC32-05; đơn giữ nguyên; Buyer chỉ có Đã nhận hàng và hủy theo UC-33 | BR-2 |
| UC-32-AC-29 | Đơn online Chờ xác nhận, đã thanh toán, xưởng vừa từ chối | Cổng chưa xác nhận hoàn tiền, sau đó xác nhận | Đơn là Bị từ chối ngay khi từ chối, trạng thái thanh toán vẫn Đã thanh toán cho đến khi cổng xác nhận rồi mới chuyển Đã hoàn tiền | BR-8 |
| UC-32-AC-30 | Đơn giao bằng GHN (Should), Đang chuẩn bị | Artisan chọn Bắt đầu giao rồi Đã giao | Đơn đổi trạng thái theo thao tác của xưởng như Xưởng tự giao; không có cập nhật tự động từ GHN | BR-15 |

### UC-33 — Hủy đơn hàng (đơn mua và đơn đặt riêng)

#### Detail

| Field | Content |
|---|---|
| Use case ID | UC-33 |
| Description | Buyer hoặc xưởng hủy đơn theo trạng thái của đơn. Đơn mua hàng: Buyer tự hủy khi Chờ xác nhận, chỉ xưởng hủy khi Đang chuẩn bị, từ Đang giao không hủy. Đơn đặt riêng: Buyer hoặc xưởng hủy khi Chờ sản xuất; khi Đang sản xuất Buyer gửi yêu cầu hủy và xưởng quyết định; từ Đang giao không hủy. Hủy đơn mua hoàn số lượng về số còn bán (trừ phần hàng hư hỏng khi xưởng hủy đơn Đang chuẩn bị); tiền đã thanh toán online được hoàn qua cổng thanh toán. |
| Actors | Buyer, Artisan (xưởng), Hệ thống, Cổng thanh toán |
| Channel | Khu mua hàng (Đơn mua của tôi, Đơn đặt riêng của tôi), Kênh người bán (Đơn bán hàng, Đơn đặt riêng) |
| Preconditions | • Đã đăng nhập<br>• Đơn thuộc tài khoản Buyer hoặc gian hàng của xưởng<br>• Đơn đang ở trạng thái cho phép hủy với người thao tác (BR-1, BR-2) |
| Expected | **Thành công:**<br>• Đơn chuyển Đã hủy; đơn mua hàng được hoàn số lượng còn bán được<br>• Tiền đã thanh toán online được hoàn (trừ khoản cọc xưởng giữ theo BR-5); trạng thái thanh toán cập nhật<br>• Bên còn lại nhận thông báo<br>**Thất bại:**<br>• Hiển thị lỗi, đơn và tiền giữ nguyên |
| Screens | • Chi tiết đơn có nút Hủy đơn, Gửi yêu cầu hủy, Rút yêu cầu hủy (Khu mua hàng)<br>• Chi tiết đơn có nút Hủy đơn, hộp phản hồi yêu cầu hủy (Kênh người bán)<br>• Hộp xác nhận hủy |
| Notifications | NT-UC33-01 đến NT-UC33-09 |

#### Main Flow

Trigger: Buyer mở đơn mua hàng đang Chờ xác nhận và chọn Hủy đơn.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Buyer | Buyer mở đơn trong Đơn mua của tôi và chọn Hủy đơn. | Hộp xác nhận hiển thị. |
| 2 | Frontend | Frontend hiển thị hộp xác nhận nêu mã đơn, tổng thanh toán, việc số lượng được trả về cho người khác mua, việc hoàn tiền (nếu đã thanh toán online), kèm ô lý do không bắt buộc. | Buyer biết hậu quả trước khi hủy. |
| 3 | Buyer | Buyer chọn Xác nhận hủy. | Yêu cầu hủy được gửi một lần; nút bị khóa trong lúc xử lý. |
| 4 | Backend | Backend kiểm tra đơn thuộc Buyer và còn Chờ xác nhận; chuyển đơn sang Đã hủy kèm lý do và người hủy; hoàn số lượng đã giữ về số còn bán; ghi lịch sử. | Đơn Đã hủy; hàng được trả về. |
| 5 | Hệ thống | Đơn đã thanh toán online thì thực hiện hoàn tiền (Alt-08). Đơn COD không có tiền để hoàn. | Khoản thanh toán được hoàn hoặc không phát sinh hoàn tiền. |
| 6 | Hệ thống | Hệ thống gửi NT-UC33-01 cho xưởng. | Xưởng biết đơn đã hủy, không cần xử lý. |
| 7 | Frontend | Frontend hiển thị đơn Đã hủy kèm trạng thái thanh toán và nút Mua lại (đưa các món còn mua được về giỏ). | Buyer thấy kết quả. |

Outcome: Đơn mua hàng Đã hủy, hàng được trả về và tiền đã thanh toán (nếu có) được hoàn.

#### Alternative Flows

##### Alt-01 — Xưởng hủy đơn mua đang chuẩn bị

- At step: 1 · Trigger: Artisan chọn Hủy đơn ở đơn mua hàng Đang chuẩn bị (ví dụ hàng hư hỏng khi đóng gói, không còn nguyên liệu).

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Frontend | Frontend hiển thị hộp xác nhận với ô lý do bắt buộc; với từng món có ô Số lượng trả về hàng còn bán (từ 0 đến số lượng đặt, mặc định bằng số lượng đặt), phần còn lại được ghi là hàng hư hỏng, không bán lại. | Artisan nhập lý do, giảm số lượng trả về nếu có hàng hỏng rồi xác nhận. |
| 2 | Backend | Backend kiểm tra đơn thuộc xưởng và còn Đang chuẩn bị; chuyển đơn sang Đã hủy kèm lý do; chỉ cộng số lượng trả về vào số còn bán (BR-3), số hư hỏng không cộng và được ghi vào lịch sử đơn; ghi lịch sử. | Đơn Đã hủy; chỉ hàng còn bán được trả về. |
| 3 | Hệ thống | Đơn đã thanh toán online thì hoàn tiền (Alt-08). Hệ thống gửi NT-UC33-02 cho Buyer kèm lý do. | Buyer biết lý do và việc hoàn tiền. |

Resume: Kết thúc.

##### Alt-02 — Buyer hủy đơn Chờ thanh toán

- At step: 1 · Trigger: Buyer chọn Hủy đơn ở đơn online đang Chờ thanh toán (chưa thanh toán, chưa quá 15 phút). Đã chốt ở G-01.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Backend | Backend chuyển đơn sang Đã hủy, hoàn số lượng đã giữ về số còn bán, đóng giao dịch thanh toán đang mở với cổng nếu cổng cho phép. | Đơn Đã hủy; không có tiền để hoàn. |
| 2 | Frontend | Frontend hiển thị đơn Đã hủy và nút Mua lại. Xưởng chưa biết đơn này nên không có thông báo cho xưởng. | Buyer thấy kết quả. |

Resume: Kết thúc. Nếu cổng vẫn ghi nhận thanh toán sau khi đơn đã hủy thì xử lý theo Alt-09.

##### Alt-03 — Hủy đơn đặt riêng Chờ sản xuất

- At step: 1 · Trigger: Buyer hoặc Artisan chọn Hủy đơn ở đơn đặt riêng đang Chờ sản xuất (UC-21, UC-22).

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Frontend | Frontend hiển thị hộp xác nhận: Buyer có ô lý do không bắt buộc, xưởng có ô lý do bắt buộc; hộp nêu số cọc đã trả sẽ được hoàn đủ. | Người hủy biết hậu quả. |
| 2 | Backend | Backend kiểm tra đơn còn Chờ sản xuất; chuyển đơn sang Đã hủy kèm lý do và người hủy; ghi lịch sử. | Đơn đặt riêng Đã hủy. |
| 3 | Hệ thống | Đơn đã đặt cọc thì hoàn đủ cọc (Alt-08); đơn không có cọc (tỷ lệ 0%) thì không có tiền để hoàn. Hệ thống gửi NT-UC33-07 cho bên còn lại. | Bên còn lại biết đơn đã hủy; cọc được hoàn. |
| 4 | Frontend | Frontend hiển thị đơn Đã hủy; Buyer thấy nút Tạo yêu cầu mới từ yêu cầu cũ (UC-18, Alt-15). | Buyer có thể bắt đầu lại. |

Resume: Kết thúc. Lô sản xuất đã tạo cho đơn (nếu có) giữ nguyên (UC-14, BR-9).

##### Alt-04 — Buyer gửi yêu cầu hủy đơn đặt riêng đang sản xuất

- At step: 1 · Trigger: Buyer chọn Gửi yêu cầu hủy ở đơn đặt riêng Đang sản xuất.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Frontend | Frontend hiển thị hộp nêu rõ: đây là yêu cầu để xưởng quyết định; xưởng đồng ý thì có thể giữ cọc để bù nguyên liệu trừ khi xưởng chọn hoàn; kèm ô lý do bắt buộc. | Buyer hiểu mình đang gửi yêu cầu, chưa phải hủy ngay. |
| 2 | Backend | Backend kiểm tra đơn còn Đang sản xuất và chưa có yêu cầu hủy đang chờ; tạo Yêu cầu hủy tình trạng Đang chờ xưởng. Đơn vẫn Đang sản xuất. | Yêu cầu được ghi nhận; đơn không đổi trạng thái. |
| 3 | Hệ thống | Hệ thống gửi NT-UC33-03 cho xưởng. | Xưởng biết có yêu cầu cần phản hồi. |
| 4 | Frontend | Frontend hiển thị ở chi tiết đơn của Buyer thẻ "Đang chờ xưởng phản hồi yêu cầu hủy" kèm nút Rút yêu cầu hủy; chi tiết đơn của xưởng hiện yêu cầu kèm lý do và hai nút Đồng ý, Từ chối. | Hai bên thấy cùng một yêu cầu. |

Resume: Xưởng phản hồi ở Alt-05 hoặc Alt-06; Buyer rút ở Alt-11; hoặc yêu cầu tự đóng ở Alt-12. Xưởng không có hạn phản hồi (G-03).

##### Alt-05 — Xưởng đồng ý yêu cầu hủy

- At step: Alt-04 bước 4 · Trigger: Artisan chọn Đồng ý ở yêu cầu hủy.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Frontend | Với đơn có cọc, Frontend hiển thị lựa chọn Giữ cọc (mặc định, bù nguyên liệu) hoặc Hoàn cọc. Đơn không có cọc thì không có lựa chọn này. | Artisan chọn cách xử lý cọc. |
| 2 | Backend | Backend kiểm tra đơn còn Đang sản xuất và yêu cầu còn Đang chờ xưởng; chuyển yêu cầu sang Xưởng đồng ý, đơn sang Đã hủy; ghi lịch sử. | Đơn Đã hủy. |
| 3 | Hệ thống | Chọn Hoàn cọc thì hoàn đủ cọc (Alt-08); chọn Giữ cọc thì giữ nguyên trạng thái thanh toán Đã đặt cọc và không hoàn. Hệ thống gửi NT-UC33-05 cho Buyer nêu cách xử lý cọc. | Buyer biết cọc được giữ hay hoàn. |

Resume: Kết thúc. Lô sản xuất đã tạo cho đơn giữ nguyên (UC-14, BR-9).

##### Alt-06 — Xưởng từ chối yêu cầu hủy

- At step: Alt-04 bước 4 · Trigger: Artisan chọn Từ chối ở yêu cầu hủy.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Frontend | Frontend hiển thị ô lý do bắt buộc. | Artisan nhập lý do và xác nhận. |
| 2 | Backend | Backend chuyển yêu cầu sang Xưởng từ chối kèm lý do; đơn vẫn Đang sản xuất. | Đơn tiếp tục sản xuất. |
| 3 | Hệ thống | Hệ thống gửi NT-UC33-06 cho Buyer kèm lý do. | Buyer biết lý do. |

Resume: Kết thúc. Buyer có thể gửi yêu cầu hủy mới (G-03).

##### Alt-07 — Xưởng chủ động hủy đơn đặt riêng đang sản xuất

- At step: 1 · Trigger: Artisan chọn Hủy đơn ở đơn đặt riêng Đang sản xuất (không cần có yêu cầu của Buyer).

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Frontend | Frontend hiển thị hộp xác nhận với ô lý do bắt buộc và nêu cọc sẽ được hoàn đủ. | Artisan nhập lý do và xác nhận. |
| 2 | Backend | Backend kiểm tra đơn còn Đang sản xuất; chuyển đơn sang Đã hủy; nếu đang có yêu cầu hủy đang chờ thì đóng yêu cầu đó (Tự đóng); ghi lịch sử. | Đơn Đã hủy. |
| 3 | Hệ thống | Hoàn đủ cọc (Alt-08). Hệ thống gửi NT-UC33-07 cho Buyer kèm lý do. | Buyer biết lý do và việc hoàn cọc. |

Resume: Kết thúc. Lô sản xuất đã tạo cho đơn giữ nguyên (UC-14, BR-9).

##### Alt-08 — Hoàn tiền qua cổng thanh toán

- At step: Main Flow bước 5, Alt-01 bước 3, Alt-03 bước 3, Alt-05 bước 3, Alt-07 bước 3, UC-31 Alt-04, UC-32 Alt-01 và Alt-03 · Trigger: Đơn Đã hủy hoặc Bị từ chối có khoản đã thanh toán online cần hoàn.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Hệ thống | Hệ thống gọi API hoàn tiền của cổng cho giao dịch đã thanh toán, với số tiền cần hoàn: tổng thanh toán của đơn mua, hoặc cọc đã trả của đơn đặt riêng. | Cổng nhận yêu cầu hoàn tiền. |
| 2 | Hệ thống | Cổng xác nhận hoàn tiền thành công: trạng thái thanh toán chuyển Đã hoàn tiền; ghi lịch sử. | Khoản tiền về nguồn thanh toán ban đầu của Buyer. |
| 3 | Hệ thống | Hệ thống gửi NT-UC33-08 cho Buyer; gửi NT-UC33-09 cho xưởng nếu xưởng đã được báo về đơn này (đơn đã vào Chờ xác nhận hoặc là đơn đặt riêng). | Hai bên biết khoản thanh toán đã hoàn. |

Resume: Quay lại luồng gọi.

##### Alt-09 — Thanh toán tới sau khi đơn đã hủy

- At step: Sau Main Flow bước 4 hoặc UC-31 Alt-04 · Trigger: Cổng báo thanh toán thành công cho một đơn đã Đã hủy (hết hạn 15 phút hoặc Buyer đã hủy lúc Chờ thanh toán với đơn mua, hoặc đơn đặt riêng tự hủy do quá hạn cọc `deposit_due_at`).

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Hệ thống | Hệ thống không khôi phục đơn; ghi nhận khoản thanh toán và hoàn đủ tiền qua Alt-08. | Đơn vẫn Đã hủy; khoản tiền được hoàn; trạng thái thanh toán Đã hoàn tiền. |

Resume: Kết thúc.

##### Alt-10 — ERR-UC33-06 — Hoàn tiền thất bại

- At step: Alt-08 bước 1 · Trigger: Cổng từ chối hoặc không phản hồi yêu cầu hoàn tiền.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Hệ thống | Hệ thống dùng idempotency key (`payment_id + reason`), tra trạng thái giao dịch tại cổng trước mỗi lần gọi lại và tự thử tối đa 5 lần theo lịch `1p → 5p → 15p → 1h → 6h` khi gặp lỗi tạm thời; đơn vẫn Đã hủy hoặc Bị từ chối và giữ nguyên `payment_state`. | Chống hoàn lặp và không chặn việc hủy. |
| 2 | Hệ thống | Nếu gặp lỗi vĩnh viễn từ cổng hoặc hết 5 lần thử lại vẫn thất bại: hệ thống dừng retry, chuyển `refunds.state = FAILED` (giữ nguyên `orders.state` và `payment_state`) và tiếp tục ghi nhận đơn trong chỉ số tồn đọng hoàn tiền (`Hoàn tiền thất bại`) tại Trang quản trị (UC-35). | Admin theo dõi và xử lý ngoài hệ thống ở MVP1. |
| 3 | Frontend | Khi đang retry hiển thị ERR-UC33-06 ("Đang xử lý hoàn tiền"); khi `refunds.state = FAILED` hiển thị ERR-UC33-08 ở chi tiết đơn của Buyer ("Hoàn tiền chưa thành công, vui lòng liên hệ Ban quản lý kèm mã đơn") và gửi NT-UC33-11 cho Buyer. | Buyer biết tình trạng hoàn tiền và cách liên hệ BQL. |

Resume: Khi hoàn tiền thành công tiếp tục Alt-08 bước 2.

##### Alt-11 — Buyer rút yêu cầu hủy

- At step: Alt-04 bước 4 · Trigger: Buyer chọn Rút yêu cầu hủy khi yêu cầu còn Đang chờ xưởng.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Backend | Backend chuyển yêu cầu sang Đã rút; đơn vẫn Đang sản xuất. | Yêu cầu không còn hiệu lực. |
| 2 | Hệ thống | Hệ thống gửi NT-UC33-04 cho xưởng. | Xưởng biết không cần phản hồi. |

Resume: Kết thúc.

##### Alt-12 — Yêu cầu hủy tự đóng

- At step: Alt-04 bước 4 · Trigger: Khi yêu cầu còn Đang chờ xưởng, đơn chuyển sang Đang giao hoặc Đã giao, hoặc bị hủy theo đường khác (Alt-07).

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Hệ thống | Hệ thống chuyển yêu cầu sang Tự đóng. Không có thông báo riêng; Buyer đã nhận thông báo đổi trạng thái của đơn (UC-22). | Không còn yêu cầu treo trên đơn không còn hủy được. |

Resume: Kết thúc.

##### Alt-13 — ERR-UC33-01 — Không hủy được ở trạng thái hiện tại

- At step: 1, Alt-01 đến Alt-07 · Trigger: Đơn ở trạng thái không cho phép hủy với người thao tác (đơn mua từ Đang giao; đơn đặt riêng từ Đang giao; đơn Hoàn tất, Đã hủy, Bị từ chối; Buyer hủy đơn mua Đang chuẩn bị). Giao diện thường đã ẩn nút; nhánh này xảy ra với trang mở từ trước hoặc gọi trực tiếp.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Backend | Backend từ chối, đơn giữ nguyên. | Hiển thị ERR-UC33-01 kèm trạng thái hiện tại của đơn. |

Resume: Kết thúc.

##### Alt-14 — ERR-UC33-02 — Đơn đã đổi trạng thái

- At step: 3, Alt-01 đến Alt-07 · Trigger: Trạng thái đơn đã đổi giữa lúc mở hộp xác nhận và lúc xử lý (xưởng vừa xác nhận, hệ thống vừa tự hủy, hai thiết bị cùng thao tác).

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Backend | Backend không hủy, chỉ một thao tác thắng. | Hiển thị ERR-UC33-02. |
| 2 | Frontend | Frontend tải lại đơn với trạng thái mới. | Người dùng thấy đúng tình trạng đơn. |

Resume: Kết thúc. Người dùng hủy lại nếu đơn còn hủy được.

##### Alt-15 — ERR-UC33-03 — Lý do không hợp lệ

- At step: Alt-01, Alt-03 (xưởng), Alt-04, Alt-06, Alt-07 · Trigger: Lý do bắt buộc bị bỏ trống hoặc ngoài độ dài cho phép (ô lý do không bắt buộc của Buyer nhập quá dài).

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Frontend | Frontend không gửi yêu cầu. | Hiển thị ERR-UC33-03 tại ô lý do. |

Resume: Quay lại nhập lý do.

##### Alt-16 — ERR-UC33-04 — Đã có yêu cầu hủy đang chờ

- At step: Alt-04 bước 2 · Trigger: Đơn đang có yêu cầu hủy Đang chờ xưởng (gửi từ thiết bị khác hoặc trang mở từ trước).

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Backend | Backend không tạo yêu cầu thứ hai. | Hiển thị ERR-UC33-04; chi tiết đơn tải lại và hiện yêu cầu đang chờ. |

Resume: Kết thúc.

##### Alt-17 — ERR-UC33-05 — Không có quyền hủy

- At step: 1, Alt-01 đến Alt-07 · Trigger: Người thao tác không phải Buyer của đơn hoặc xưởng của đơn; hoặc tài khoản Admin.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Backend | Backend từ chối và không tiết lộ đơn có tồn tại hay không. | Hiển thị ERR-UC33-05. |

Resume: Kết thúc.

##### Alt-18 — Phiên đăng nhập hết hạn

- At step: 3, Alt-01 đến Alt-07 · Trigger: Phiên hết hạn khi người dùng đang ở hộp xác nhận.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Backend | Backend đưa người dùng về form đăng nhập và giữ lại thao tác đang làm (UC-04, Alt-08). | Hiển thị ERR-UC04-05; đơn chưa đổi. |

Resume: Sau khi đăng nhập quay lại đúng đơn; người dùng xác nhận lại.

##### Alt-19 — ERR-UC33-07 — Số lượng trả về không hợp lệ

- At step: Alt-01 bước 1 · Trigger: Số lượng trả về của một món không phải số nguyên hoặc ngoài khoảng từ 0 đến số lượng đặt.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Frontend | Frontend không gửi yêu cầu. | Hiển thị ERR-UC33-07 tại món sai. |

Resume: Quay lại nhập số lượng trả về.

##### Alt-20 — Hệ thống tự hủy đơn đặt riêng quá hạn đặt cọc (`deposit_due_at`)

- At step: Sau UC-20 bước 8 (hệ thống tự chạy) · Trigger: Quá hạn `deposit_due_at` mà đơn đặt riêng có cọc (> 0%) vẫn ở trạng thái Chờ sản xuất và thanh toán Chưa thanh toán.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Hệ thống | Hệ thống khóa đơn, kiểm tra lại trạng thái Chờ sản xuất và Chưa thanh toán; chuyển đơn sang Đã hủy (người hủy: hệ thống, lý do: quá hạn đặt cọc) và ghi lịch sử đơn. Không cộng tồn kho sản phẩm mẫu. | Đơn chuyển Đã hủy. |
| 2 | Hệ thống | Hệ thống gửi NT-UC33-10 cho cả Buyer và Xưởng của đơn. | Hai bên biết đơn đã tự hủy do quá hạn đặt cọc. |

Resume: Kết thúc. Nếu cổng thanh toán báo nhận cọc muộn sau khi đơn đã hủy, xử lý hoàn đủ cọc theo Alt-09 và Alt-08.

#### Data Fields

| Field | Format | Validation | Required | Ghi chú |
|---|---|---|---|---|
| Lý do hủy | Text | 10-500 ký tự | Buyer hủy đơn mua hoặc đơn Chờ sản xuất: không. Buyer gửi yêu cầu hủy: có. Xưởng hủy đơn, xưởng từ chối yêu cầu hủy: có | Hiện cho bên còn lại trong thông báo và chi tiết đơn |
| Số lượng trả về hàng còn bán | Number, theo từng món | Số nguyên từ 0 đến số lượng đặt | Có khi xưởng hủy đơn Đang chuẩn bị | Mặc định bằng số lượng đặt; phần còn lại là hàng hư hỏng, không cộng vào số còn bán. Các trường hợp hủy khác luôn trả đủ |
| Cách xử lý cọc | Chọn một | Giữ cọc hoặc Hoàn cọc | Có khi xưởng đồng ý yêu cầu hủy đơn có cọc | Mặc định Giữ cọc; xưởng hủy chủ động luôn hoàn đủ nên không có lựa chọn |
| Tình trạng yêu cầu hủy | Chỉ xem | Đang chờ xưởng, Xưởng đồng ý, Xưởng từ chối, Đã rút, Tự đóng | — | Mỗi đơn chỉ có một yêu cầu Đang chờ xưởng tại một thời điểm |
| Người hủy | Chỉ xem | Buyer, xưởng, hệ thống | — | Hiện trong lịch sử đơn |
| Số tiền hoàn | VND, chỉ xem | Không vượt số đã thanh toán | — | Đơn mua: tổng thanh toán; đơn đặt riêng: cọc đã trả |
| Trạng thái thanh toán | Chỉ xem | Đã thanh toán hoặc Đã đặt cọc, chuyển Đã hoàn tiền khi hoàn thành công | — | Giữ nguyên khi xưởng giữ cọc |

#### Error Messages

| Code | Condition | Message | Display | Flow |
|---|---|---|---|---|
| ERR-UC33-01 | Không hủy được ở trạng thái hiện tại | "Đơn hàng đang ở trạng thái {{status}} nên không thể hủy." | Toast | Alt-13 |
| ERR-UC33-02 | Đơn đã đổi trạng thái | "Đơn hàng vừa được cập nhật. Vui lòng xem lại trạng thái mới." | Banner | Alt-14 |
| ERR-UC33-03 | Lý do không hợp lệ | "Vui lòng nhập lý do từ 10 đến 500 ký tự." | Inline | Alt-15 |
| ERR-UC33-04 | Đã có yêu cầu hủy đang chờ | "Đơn hàng đã có yêu cầu hủy đang chờ xưởng phản hồi." | Banner | Alt-16 |
| ERR-UC33-05 | Không có quyền hủy | "Bạn không có quyền hủy đơn hàng này." | Trang lỗi | Alt-17 |
| ERR-UC33-06 | Hoàn tiền thất bại | "Khoản hoàn {{amount}} đang được xử lý. Chúng tôi sẽ cập nhật khi tiền được hoàn." | Banner | Alt-10 |
| ERR-UC33-07 | Số lượng trả về không hợp lệ | "Số lượng trả về của {{product}} phải là số nguyên từ 0 đến {{quantity}}." | Inline | Alt-19 |
| ERR-UC33-08 | Hoàn tiền thất bại vĩnh viễn hoặc hết số lần thử lại (`refunds.state = FAILED`) | "Hoàn tiền chưa thành công, vui lòng liên hệ Ban quản lý kèm mã đơn {{orderCode}}." | Banner | Alt-10 |

#### Notifications

| ID | Recipient | Trigger | Content | Channel | Flow |
|---|---|---|---|---|---|
| NT-UC33-01 | Artisan (xưởng) | Buyer hủy đơn mua hàng Chờ xác nhận | "Người mua đã hủy đơn hàng {{orderCode}}." | In-app | Main Flow bước 6 |
| NT-UC33-02 | Buyer | Xưởng hủy đơn mua hàng Đang chuẩn bị | "Xưởng đã hủy đơn hàng {{orderCode}}. Lý do: {{reason}}. {{refundNote}}" ({{refundNote}}: "Số tiền đã thanh toán sẽ được hoàn lại." hoặc để trống với đơn COD) | In-app | Alt-01 |
| NT-UC33-03 | Artisan (xưởng) | Buyer gửi yêu cầu hủy đơn đặt riêng Đang sản xuất | "Người mua gửi yêu cầu hủy đơn đặt riêng {{orderCode}}. Vui lòng phản hồi." | In-app | Alt-04 |
| NT-UC33-04 | Artisan (xưởng) | Buyer rút yêu cầu hủy | "Người mua đã rút yêu cầu hủy đơn đặt riêng {{orderCode}}." | In-app | Alt-11 |
| NT-UC33-05 | Buyer | Xưởng đồng ý yêu cầu hủy | "Xưởng đồng ý hủy đơn đặt riêng {{orderCode}}. {{depositNote}}" ({{depositNote}}: "Xưởng giữ khoản cọc để bù nguyên liệu.", "Khoản cọc sẽ được hoàn lại." hoặc để trống với đơn không có cọc) | In-app | Alt-05 |
| NT-UC33-06 | Buyer | Xưởng từ chối yêu cầu hủy | "Xưởng từ chối yêu cầu hủy đơn đặt riêng {{orderCode}}. Lý do: {{reason}}. Đơn tiếp tục được sản xuất." | In-app | Alt-06 |
| NT-UC33-07 | Bên còn lại (Buyer hoặc Artisan) | Đơn đặt riêng bị hủy trực tiếp: Buyer hoặc xưởng hủy khi Chờ sản xuất, hoặc xưởng hủy khi Đang sản xuất | "Đơn đặt riêng {{orderCode}} đã bị {{actor}} hủy.{{reasonNote}} {{depositNote}}" ({{actor}}: "người mua" hoặc "xưởng"; {{reasonNote}}: " Lý do: …" khi xưởng hủy; {{depositNote}}: "Khoản cọc sẽ được hoàn lại." hoặc để trống) | In-app | Alt-03, Alt-07 |
| NT-UC33-08 | Buyer | Hoàn tiền thành công | "Đã hoàn {{amount}} cho đơn hàng {{orderCode}} về nguồn thanh toán ban đầu." | In-app | Alt-08 |
| NT-UC33-09 | Artisan (xưởng) | Hoàn tiền thành công cho đơn xưởng đã được báo | "Khoản thanh toán {{amount}} của đơn hàng {{orderCode}} đã được hoàn cho người mua." | In-app | Alt-08 |
| NT-UC33-10 | Buyer và Artisan (xưởng) | Hệ thống tự hủy đơn đặt riêng do quá hạn đặt cọc (`deposit_due_at`) | "Đơn đặt riêng {{orderCode}} đã tự hủy do quá hạn thanh toán đặt cọc." | In-app | Alt-20 |
| NT-UC33-11 | Buyer | Hoàn tiền thất bại (`refunds.state = FAILED`) sau số lần thử lại tối đa hoặc lỗi vĩnh viễn | "Hoàn tiền cho đơn hàng {{orderCode}} chưa thành công. Vui lòng liên hệ Ban quản lý kèm mã đơn để được hỗ trợ." | In-app | Alt-10 |

#### Business Rules

| ID | Rule | Source |
|---|---|---|
| BR-1 | Đơn mua hàng: Chờ xác nhận, Buyer hủy ngay không cần xưởng đồng ý; Đang chuẩn bị, chỉ xưởng hủy và lý do bắt buộc; từ Đang giao không hủy. Hệ thống tự hủy ở Chờ thanh toán (15 phút) và Chờ xác nhận (3 ngày). Buyer hủy ở Chờ thanh toán đã chốt ở G-01. | Overview 3.8, mục 4 |
| BR-2 | Đơn đặt riêng: Chờ sản xuất, Buyer hoặc xưởng hủy (hoàn đủ cọc nếu đã cọc), hoặc hệ thống tự hủy khi quá hạn đặt cọc `deposit_due_at` (chốt G-07 25/11/2026); Đang sản xuất, Buyer gửi yêu cầu hủy và xưởng quyết định, xưởng hủy thì hoàn đủ cọc; từ Đang giao không hủy, xử lý qua đổi trả (EN-17, ngoài phạm vi). | Overview 3.8; Chốt 2026-11-25 (G-07) |
| BR-3 | Hủy hoặc từ chối đơn mua hàng hoàn số lượng đã giữ về số còn bán của sản phẩm gốc, kể cả khi sản phẩm đã bị Ẩn (G-09). Riêng khi xưởng hủy đơn Đang chuẩn bị, xưởng khai số lượng trả về hàng còn bán của từng món; phần hư hỏng không cộng vào số còn bán và chỉ lưu vết trong lịch sử đơn, để hàng hỏng không bị bán tiếp. Buyer hủy, hệ thống tự hủy và xưởng từ chối đơn (hàng chưa chuẩn bị) luôn trả đủ. Đơn đặt riêng không có số còn bán nên không có bước này. | Overview 3.8; Nhóm đề xuất |
| BR-4 | Tiền đã thanh toán online được hoàn qua API hoàn tiền của cổng, về nguồn thanh toán ban đầu, theo số đã thanh toán: tổng thanh toán của đơn mua, cọc đã trả của đơn đặt riêng. COD không có tiền để hoàn. | Overview 3.8, mục 5 |
| BR-5 | Buyer hủy đơn đặt riêng khi Đang sản xuất mà xưởng đồng ý: xưởng giữ cọc để bù nguyên liệu, trừ khi xưởng tự chọn hoàn. Xưởng chủ động hủy thì luôn hoàn đủ cọc. | Overview 3.8; UC-22 BR-3, BR-4 (BRD 04) |
| BR-6 | Mỗi đơn có tối đa một yêu cầu hủy Đang chờ xưởng; Buyer gửi phải nêu lý do. Yêu cầu không có hạn phản hồi. Buyer rút được khi còn Đang chờ xưởng. Yêu cầu tự đóng khi đơn sang Đang giao hoặc bị hủy theo đường khác. Yêu cầu hủy không phải trạng thái của đơn. | Nhóm đề xuất (G-03) |
| BR-7 | Lý do bắt buộc khi xưởng hủy đơn, xưởng từ chối yêu cầu hủy và Buyer gửi yêu cầu hủy; Buyer hủy đơn mua hoặc đơn Chờ sản xuất thì lý do không bắt buộc. | Overview 3.8; Nhóm đề xuất (G-04) |
| BR-8 | Hủy đơn là thao tác một chiều: đơn Đã hủy không khôi phục được. Muốn mua lại thì đặt đơn mới (nút Mua lại); với đơn đặt riêng thì tạo yêu cầu mới từ yêu cầu cũ (UC-18, Alt-15). Lô sản xuất đã tạo cho đơn đặt riêng bị hủy giữ nguyên. | Nhóm đề xuất; UC-14 BR-9 (BRD 03) |
| BR-9 | Trạng thái thanh toán chuyển Đã hoàn tiền chỉ khi cổng xác nhận hoàn thành công; xưởng giữ cọc thì giữ nguyên Đã đặt cọc. | Overview mục 4 |
| BR-10 | Thanh toán tới sau khi đơn đã hủy (hết hạn hoặc Buyer đã hủy) thì đơn không khôi phục và khoản tiền được hoàn đủ tự động. | Nhóm đề xuất |
| BR-11 | Hoàn tiền thất bại không chặn việc hủy: đơn vẫn Đã hủy hoặc Bị từ chối; dùng idempotency key (`payment_id + reason`), tra trạng thái cổng trước khi gửi lại, retry tối đa 5 lần (`1p → 5p → 15p → 1h → 6h`). Hết 5 lần hoặc gặp lỗi vĩnh viễn thì dừng, chuyển `refunds.state = FAILED` (giữ nguyên `orders.state` và `payment_state`), báo Buyer liên hệ BQL kèm mã đơn và đưa vào tồn đọng hoàn tiền ở UC-35. | Chốt 2026-11-25 (G-02) |
| BR-12 | Chỉ Buyer của đơn và xưởng của đơn được hủy theo đúng trạng thái; Admin không hủy hay can thiệp đơn. Kiểm tra quyền và trạng thái ở mọi thao tác; hủy và xác nhận cùng lúc thì chỉ một thao tác thắng. | Overview mục 1, mục 8 (An ninh) |
| BR-13 | Xưởng Tạm ngừng vẫn hủy đơn và phản hồi yêu cầu hủy của đơn đang thực hiện. | UC-13 BR-4 (BRD 02) |
| BR-14 | Nội dung thông báo không chứa họ tên, số điện thoại, địa chỉ nhận của Buyer. | UC-29 BR-11 (BRD 07) |
| BR-15 | Mọi lần hủy, đồng ý, từ chối, rút yêu cầu và hoàn tiền ghi lịch sử trên đơn (thời điểm, người hoặc hệ thống thực hiện, lý do nếu có). | Nhóm đề xuất |
| BR-16 | Thanh toán thật và đối soát tiền cho xưởng ngoài phạm vi MVP1: khoản giữ lại hay hoàn chỉ được ghi trạng thái, không chuyển tiền cho xưởng. | Overview 3.8; EN-15 |

#### Acceptance Criteria

| ID | Given | When | Then | Covers |
|---|---|---|---|---|
| UC-33-AC-01 | Đơn COD Chờ xác nhận, mua 2 "Giỏ mây đan hoa cúc" | Buyer chọn Hủy đơn rồi Xác nhận hủy | Đơn Đã hủy; số còn bán tăng 2; xưởng nhận NT-UC33-01; không có tiền để hoàn | Main Flow, BR-1, BR-3 |
| UC-33-AC-02 | Đơn online đã thanh toán, Chờ xác nhận | Buyer hủy | Đơn Đã hủy; khoản tiền được hoàn qua cổng, thanh toán Đã hoàn tiền; Buyer nhận NT-UC33-08; xưởng nhận NT-UC33-01 và NT-UC33-09 | Main Flow, Alt-08, BR-4 |
| UC-33-AC-03 | Hộp xác nhận hủy mở | Buyer xem hộp | Hộp nêu mã đơn, tổng thanh toán, việc trả hàng về cho người khác mua và việc hoàn tiền nếu đã thanh toán online; lý do không bắt buộc | Main Flow |
| UC-33-AC-04 | Đơn Đang chuẩn bị | Buyer gọi thao tác hủy | Hiển thị ERR-UC33-01; đơn giữ nguyên; giao diện không có nút Hủy đơn cho Buyer | Alt-13, BR-1 |
| UC-33-AC-05 | Đơn Đang chuẩn bị mua 2 "Giỏ mây đan hoa cúc", đã thanh toán online | Xưởng hủy kèm lý do 20 ký tự, giữ số lượng trả về mặc định | Đơn Đã hủy kèm lý do; số còn bán tăng đủ 2; hoàn tiền; Buyer nhận NT-UC33-02 và NT-UC33-08 | Alt-01, BR-1, BR-3, BR-7 |
| UC-33-AC-06 | Đơn COD Đang chuẩn bị | Xưởng hủy kèm lý do | Đơn Đã hủy; không có hoàn tiền; NT-UC33-02 không có câu hoàn tiền | Alt-01, BR-4 |
| UC-33-AC-07 | Xưởng hủy đơn Đang chuẩn bị | Bỏ trống lý do | Hiển thị ERR-UC33-03; đơn giữ nguyên | Alt-15, BR-7 |
| UC-33-AC-08 | Đơn Đang giao hoặc Đã giao | Buyer hoặc xưởng gọi thao tác hủy | Hiển thị ERR-UC33-01; đơn giữ nguyên | Alt-13, BR-1 |
| UC-33-AC-09 | Đơn online Chờ thanh toán, chưa quá 15 phút | Buyer hủy đơn | Đơn Đã hủy, số còn bán tăng lại, không có tiền để hoàn; xưởng không nhận thông báo | Alt-02, BR-1 |
| UC-33-AC-10 | Đơn Chờ thanh toán đã hủy, cổng báo thanh toán thành công muộn | Hệ thống nhận kết quả | Đơn vẫn Đã hủy; khoản tiền được hoàn đủ; Buyer nhận NT-UC33-08; xưởng không nhận thông báo | Alt-09, BR-10 |
| UC-33-AC-11 | Đơn đặt riêng Chờ sản xuất đã đặt cọc 30% | Buyer hủy | Đơn Đã hủy; hoàn đủ cọc; xưởng nhận NT-UC33-07 và NT-UC33-09; Buyer có nút Tạo yêu cầu mới | Alt-03, BR-2, BR-8 |
| UC-33-AC-12 | Đơn đặt riêng Chờ sản xuất, cọc 0% | Xưởng hủy kèm lý do | Đơn Đã hủy; không có tiền để hoàn; Buyer nhận NT-UC33-07 kèm lý do | Alt-03, BR-7 |
| UC-33-AC-13 | Đơn đặt riêng Đang sản xuất | Buyer gửi yêu cầu hủy kèm lý do | Yêu cầu Đang chờ xưởng; đơn vẫn Đang sản xuất; xưởng nhận NT-UC33-03; Buyer thấy thẻ chờ xưởng phản hồi | Alt-04, BR-6 |
| UC-33-AC-14 | Đơn đặt riêng Đang sản xuất đã có yêu cầu hủy đang chờ | Buyer gửi thêm yêu cầu | Hiển thị ERR-UC33-04; không tạo yêu cầu thứ hai | Alt-16, BR-6 |
| UC-33-AC-15 | Yêu cầu hủy Đang chờ xưởng, đơn có cọc | Xưởng đồng ý và chọn Giữ cọc | Đơn Đã hủy; yêu cầu Xưởng đồng ý; cọc không hoàn, thanh toán vẫn Đã đặt cọc; Buyer nhận NT-UC33-05 nêu xưởng giữ cọc | Alt-05, BR-5, BR-9 |
| UC-33-AC-16 | Yêu cầu hủy Đang chờ xưởng, đơn có cọc | Xưởng đồng ý và chọn Hoàn cọc | Đơn Đã hủy; cọc được hoàn, thanh toán Đã hoàn tiền; Buyer nhận NT-UC33-05 và NT-UC33-08 | Alt-05, BR-5 |
| UC-33-AC-17 | Yêu cầu hủy Đang chờ xưởng | Xưởng từ chối kèm lý do | Yêu cầu Xưởng từ chối; đơn tiếp tục Đang sản xuất; Buyer nhận NT-UC33-06 kèm lý do | Alt-06, BR-7 |
| UC-33-AC-18 | Đơn Đang sản xuất, chưa có yêu cầu hủy | Xưởng chủ động hủy kèm lý do | Đơn Đã hủy; hoàn đủ cọc, không có lựa chọn giữ cọc; Buyer nhận NT-UC33-07 | Alt-07, BR-5 |
| UC-33-AC-19 | Yêu cầu hủy Đang chờ xưởng | Buyer chọn Rút yêu cầu hủy | Yêu cầu Đã rút; đơn vẫn Đang sản xuất; xưởng nhận NT-UC33-04 | Alt-11, BR-6 |
| UC-33-AC-20 | Yêu cầu hủy Đang chờ xưởng | Xưởng chuyển đơn sang Đang giao | Yêu cầu Tự đóng; không có thông báo riêng cho yêu cầu | Alt-12, BR-6 |
| UC-33-AC-21 | Yêu cầu hủy Đang chờ xưởng | Xưởng chủ động hủy đơn | Đơn Đã hủy; yêu cầu Tự đóng | Alt-07, Alt-12 |
| UC-33-AC-22 | Buyer đang mở hộp hủy đơn Chờ xác nhận; xưởng vừa xác nhận đơn | Buyer chọn Xác nhận hủy | Hiển thị ERR-UC33-02; đơn là Đang chuẩn bị, không bị hủy | Alt-14, BR-12 |
| UC-33-AC-23 | Cổng từ chối yêu cầu hoàn tiền | Xưởng hủy đơn online đã thanh toán | Đơn vẫn Đã hủy; thanh toán giữ nguyên; Buyer thấy ERR-UC33-06; hệ thống thử lại và khi thành công thì Đã hoàn tiền | Alt-10, BR-11 |
| UC-33-AC-24 | Buyer khác, xưởng khác hoặc Admin | Hủy đơn không thuộc mình | Hiển thị ERR-UC33-05; không lộ đơn có tồn tại | Alt-17, BR-12 |
| UC-33-AC-25 | Phiên hết hạn trên hộp xác nhận hủy | Chọn Xác nhận hủy | Hiển thị ERR-UC04-05; đăng nhập xong quay lại đơn, đơn chưa đổi | Alt-18 |
| UC-33-AC-26 | Xưởng Tạm ngừng, có đơn Đang chuẩn bị và yêu cầu hủy | Xưởng hủy đơn hoặc phản hồi yêu cầu | Thao tác thành công | BR-13 |
| UC-33-AC-27 | Đơn đặt riêng bị hủy, đã có lô sản xuất | Xem lô | Lô giữ nguyên | Alt-03, BR-8 |
| UC-33-AC-28 | Đơn có người nhận tên "Nguyễn Văn A", số 0912345678 | Xem nội dung NT-UC33-01 đến NT-UC33-09 | Không có tên, số điện thoại, địa chỉ nhận | BR-14 |
| UC-33-AC-29 | Đơn đặt riêng có yêu cầu hủy được xưởng đồng ý và hoàn cọc | Xem lịch sử đơn | Có các dòng: yêu cầu hủy, xưởng đồng ý, đơn Đã hủy, hoàn tiền; mỗi dòng có thời điểm, người hoặc hệ thống thực hiện, lý do nếu có | BR-15 |
| UC-33-AC-30 | Xưởng chọn Giữ cọc, hoặc hoàn tiền xong | Kiểm tra dữ liệu thanh toán của đơn | Chỉ có trạng thái thanh toán (giữ Đã đặt cọc hoặc chuyển Đã hoàn tiền); không có bút toán chuyển tiền cho xưởng | BR-16 |
| UC-33-AC-31 | Đơn Đang chuẩn bị mua 3 "Giỏ mây đan hoa cúc", 1 chiếc hư hỏng khi đóng gói | Xưởng hủy kèm lý do, nhập số lượng trả về 2 | Đơn Đã hủy; số còn bán chỉ tăng 2; 1 chiếc hư hỏng không được cộng và lịch sử đơn ghi số hư hỏng; Buyer được hoàn đủ theo đơn | Alt-01, BR-3, BR-15 |
| UC-33-AC-32 | Xưởng hủy đơn Đang chuẩn bị mua 3 món | Nhập số lượng trả về 5, hoặc -1, hoặc "abc" | Hiển thị ERR-UC33-07 tại món sai; đơn giữ nguyên | Alt-19, BR-3 |
| UC-33-AC-33 | Đơn Chờ xác nhận mua 3 món; đơn khác bị xưởng từ chối; đơn khác tự hủy do quá 3 ngày | Buyer hủy, xưởng từ chối, hệ thống tự hủy | Cả ba trường hợp số còn bán tăng đủ 3; không có ô số lượng hư hỏng | BR-3 |

## IV. Open Questions

| ID | Question | Status | Ghi chú |
|---|---|---|---|
| G-01 | Buyer có được tự hủy đơn online đang Chờ thanh toán không? Overview chỉ cho đơn này tự hủy khi quá 15 phút. | Đã chốt | Chốt: Buyer được tự hủy đơn Chờ thanh toán (UC-33, Alt-02): đơn Đã hủy, hoàn số lượng, không có tiền để hoàn, đóng giao dịch đang mở nếu cổng cho phép; thanh toán tới muộn thì hoàn tiền (Alt-09). |
| G-02 | Hoàn tiền qua cổng thất bại thì xử lý thế nào khi MVP1 không có đối soát và Admin không xem đơn? | Đã chốt | Chốt 2026-11-25: dùng idempotency key (`payment_id + reason`), tra trạng thái cổng trước khi gọi lại, retry tối đa 5 lần (`1p → 5p → 15p → 1h → 6h`). Hết ngưỡng hoặc lỗi vĩnh viễn dừng retry, đặt `refunds.state = FAILED` (giữ nguyên `orders.state` và `payment_state`), báo Buyer liên hệ BQL kèm mã đơn và đưa vào tồn đọng hoàn tiền ở UC-35. |
| G-03 | Yêu cầu hủy đơn đặt riêng đang sản xuất: xưởng có hạn phản hồi không; Buyer gửi lại được sau khi bị từ chối không? | Open | Đang theo hướng: không có hạn phản hồi; Buyer rút được; bị từ chối thì được gửi yêu cầu mới, mỗi lần một yêu cầu đang chờ. Rủi ro: yêu cầu treo lâu hoặc gửi liên tục. |
| G-04 | Lý do hủy: bắt buộc với bên nào? Overview chỉ nêu bắt buộc với xưởng hủy đơn mua. | Open | Đang theo hướng: bắt buộc khi xưởng hủy bất kỳ đơn nào, xưởng từ chối yêu cầu hủy và Buyer gửi yêu cầu hủy; Buyer hủy đơn mua hoặc đơn Chờ sản xuất thì không bắt buộc (BR-7). |
| G-05 | Đơn Nhận tại xưởng: ai bấm chuyển sang Đã giao? | Open | Đang theo hướng: xưởng bấm Đã bàn giao tại xưởng khi Buyer đến lấy (UC-32, Alt-02); Buyer vẫn xác nhận đã nhận hoặc đơn tự hoàn tất sau 7 ngày. Đơn không có bước Đang giao. |
| G-06 | Mã đơn mua hàng dùng dạng nào? | Open | Đang theo hướng: MH-năm-số 4 chữ số (UC-31 BR-9), phân biệt với đơn đặt riêng mã DR. |
| G-07 | Hết hạn xác nhận 3 ngày của đơn online đã thanh toán: tính từ lúc tạo đơn hay lúc thanh toán thành công? | Open | Đang theo hướng: từ lúc đơn vào Chờ xác nhận (thanh toán thành công với đơn online) để xưởng đủ 3 ngày. |
| G-08 | Giỏ hàng có thời hạn lưu không? | Open | Đang theo hướng: không thời hạn; giỏ Guest lưu đến khi xóa dữ liệu trình duyệt (UC-30 BR-10). |
| G-09 | Hoàn số lượng khi sản phẩm của đơn bị hủy đã Ẩn hoặc đổi giá: có cộng lại số còn bán không? | Open | Đang theo hướng: luôn cộng lại vào sản phẩm gốc, kể cả khi sản phẩm đang Ẩn; sản phẩm Ẩn lên lại thì số còn bán đã đúng. |
| G-10 | Danh mục thông báo của BRD 07 cần khai báo NT-UC31-01 đến NT-UC31-03, NT-UC32-01 đến NT-UC32-08, NT-UC33-01 đến NT-UC33-09 (G-01 của BRD 07). | Đã chốt | Chốt 2026-11-25: đồng bộ trạng thái đóng với G-01 của BRD `07-notification` (đã khai báo đủ NT-UC31 đến NT-UC33). |
