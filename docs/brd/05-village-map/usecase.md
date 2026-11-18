---
title: "05 — Bản đồ làng nghề"
group: 05-village-map
type: brd
status: draft
version: 0.1.0
date: 2026-11-18
author: Khánh Chi
---

# 05 — Bản đồ làng nghề

Change history: [CHANGELOG.md](CHANGELOG.md)

## I. Overview

Nhóm chức năng bản đồ số của làng nghề: mỗi xưởng đã mở gian hàng có một pin trên bản đồ, kèm trạng thái Đang đón khách để khách biết xưởng nào đang mở cửa cho tham quan, trải nghiệm. Artisan xác nhận vị trí xưởng (hệ thống gợi ý ghim theo địa chỉ đã nhập; khi xin đổi địa chỉ thì kiểm tra lại ghim ngay lúc gửi yêu cầu, ghim mới được áp dụng cùng địa chỉ khi Admin duyệt) và đặt lịch đón khách; trạng thái Đang đón khách tự bật, tắt theo lịch hằng tuần và các ngày nghỉ xưởng đã đặt. Guest và Buyer xem bản đồ, lọc xưởng đang đón khách, bấm pin xem popup có dòng giờ đón khách giống cách Google Maps hiển thị giờ mở cửa (ví dụ "Đang đón khách · Đóng cửa lúc 17:00"), xem giờ cả tuần, rồi đi tới gian hàng hoặc chỉ đường. Kết quả cuối cùng là một bản đồ luôn phản ánh đúng xưởng nào đang công khai, nằm ở đâu và lúc này có đón khách hay không, khi nào đóng hoặc mở lại.

### Terms

| Term | Meaning |
|---|---|
| Pin | Ghim đánh dấu vị trí một xưởng trên bản đồ. Chỉ xưởng có gian hàng đã mở (Đang hoạt động hoặc Tạm ngừng, xem BRD `02-showcase`) mới có pin công khai. |
| Vị trí xưởng | Điểm trên bản đồ do chủ xưởng xác nhận, mỗi gian hàng một vị trí. Tách khỏi địa chỉ chữ ở UC-07. |
| Vị trí gợi ý | Ghim hệ thống đặt sẵn theo địa chỉ xưởng đã nhập ở UC-07. Chưa là vị trí xưởng cho tới khi chủ xưởng chọn Lưu. |
| Vị trí đề nghị | Vị trí xưởng mới mà chủ xưởng kiểm tra trên bản đồ khi gửi yêu cầu đổi địa chỉ (UC-07, Alt-03). Đi kèm yêu cầu và chỉ thành vị trí xưởng khi Admin duyệt. |
| Chế độ đón khách | Cách xưởng đặt việc đón khách, có ba chế độ: Tắt (không đón khách, không hiển thị giờ đón khách trên bản đồ), Luôn bật (đón khách 24/7 trừ Lịch tắt), Theo lịch hằng tuần (đón khách theo ngày và khung giờ đã đặt, trừ Lịch tắt). |
| Lịch hằng tuần | Các ngày từ Thứ Hai đến Chủ nhật xưởng đón khách. Mỗi ngày được đặt giờ riêng, tối đa một khung giờ bắt đầu đến kết thúc, không qua nửa đêm (giờ Việt Nam). |
| Lịch tắt | Các ngày, hoặc ngày kèm khung giờ, xưởng tạm không đón khách dù chế độ là Luôn bật hoặc Theo lịch (ví dụ nghỉ lễ, có việc riêng). Luôn được ưu tiên hơn lịch hằng tuần và chế độ Luôn bật; nhiều mục trùng nhau được hợp nhất khi tính. |
| Giờ đón khách thực tế | Phần thời gian xưởng thực sự đón khách sau khi trừ Lịch tắt khỏi chế độ đón khách và lịch hằng tuần. Dòng giờ đón khách, Giờ cả tuần và bộ lọc Đang đón khách ở UC-26 đều dùng cùng một cách tính này (UC-25, BR-4). |
| Đang đón khách | Trạng thái hiện tại của xưởng tại thời điểm xem: chế độ khác Tắt, đang trong giờ đón khách thực tế. Hệ thống tự tính, chủ xưởng không bật tay từng lần. Độc lập với trạng thái hoạt động của gian hàng (UC-13). |
| Dòng giờ đón khách | Dòng trạng thái ở popup xưởng, gồm một trong ba nhãn (Đang đón khách, Không đón khách, Tạm nghỉ) và mốc giờ đóng hoặc mở kế tiếp, giống cách Google Maps hiện giờ mở cửa. Ví dụ: "Đang đón khách · Đóng cửa lúc 17:00". |
| Giờ cả tuần | Danh sách 7 ngày liên tiếp bắt đầu từ hôm nay, mở ra từ popup xưởng, mỗi ngày hiện các khoảng giờ đón khách thực tế (sau khi trừ Lịch tắt). |
| Ghi chú tham quan | Dòng chữ ngắn của xưởng về hoạt động trải nghiệm, lưu ý khi đến, hiện ở popup xưởng. Mức Should. |
| Popup xưởng | Khung thông tin tóm tắt hiện khi bấm một pin. |
| Cụm pin | Nhiều pin ở gần nhau được gom thành một dấu có số xưởng khi bản đồ thu nhỏ. |

## II. Scope

| Business topic | Channel | Use case |
|---|---|---|
| Gợi ý ghim theo địa chỉ, xác nhận và điều chỉnh vị trí xưởng trên bản đồ | Kênh người bán | UC-25 |
| Kiểm tra lại ghim khi xin đổi địa chỉ; vị trí đề nghị được áp dụng cùng địa chỉ khi Admin duyệt | Kênh người bán | UC-25 |
| Đặt chế độ đón khách, lịch hằng tuần, lịch tắt; trạng thái Đang đón khách tự đổi theo lịch; nhập ghi chú tham quan | Kênh người bán | UC-25 |
| Xem pin các xưởng, lọc xưởng đang đón khách, xem popup kèm dòng giờ đón khách và giờ cả tuần, đi tới gian hàng, chỉ đường | Khu mua hàng | UC-26 |
| Chủ xưởng xem trước ghim của gian hàng chưa mở | Khu mua hàng | UC-26 |

**Ngoài phạm vi:** Đặt lịch tham quan, trải nghiệm (EN-10); tìm xưởng theo tên (thuộc UC-08, không làm trên bản đồ); tính lộ trình, khoảng cách hoặc xác định vị trí của người dùng trong hệ thống; ô tìm địa chỉ riêng trên bản đồ (chủ xưởng tự kéo bản đồ, bấm hoặc kéo ghim); nhập nhiều khung giờ trong một ngày, khung giờ qua nửa đêm (Giờ cả tuần chỉ hiển thị nhiều khoảng giờ sau khi trừ Lịch tắt, không phải chức năng nhập); tự nhận biết ngày lễ (xưởng tự thêm vào Lịch tắt); nhắc chủ xưởng cập nhật lịch hoặc vị trí; tự làm mới bản đồ đang mở khi tới giờ bắt đầu hoặc kết thúc; hiện giờ đón khách ngay trên pin hoặc dạng chữ nổi khi rê chuột ngoài dấu Đang đón khách; nhãn "Sắp đóng cửa"; công khai lý do của lịch tắt; Admin duyệt vị trí riêng, ngoài yêu cầu đổi địa chỉ, hoặc sửa vị trí xưởng; giới hạn vị trí trong khu vực làng nghề; hiển thị xưởng chưa mở gian hàng cho Guest, Buyer; thống kê lượt xem bản đồ (EN-11).

**Liên quan**

| ID | Tài liệu | Quan hệ |
|---|---|---|
| BRD-01 | [01-account](../01-account/usecase.md) | UC-02 (Alt-05) duyệt yêu cầu đổi địa chỉ xưởng; khi duyệt, vị trí đề nghị đi kèm được áp dụng cùng địa chỉ mới, khi từ chối thì địa chỉ và vị trí cũ giữ nguyên (UC-25, Alt-11, BR-8). |
| BRD-02 | [02-showcase](../02-showcase/usecase.md) | UC-07 cung cấp địa chỉ để gợi ý ghim; khi Artisan đổi địa chỉ (Alt-03), form hiện bản đồ để kiểm tra ghim và yêu cầu gửi đi kèm vị trí đề nghị; khi đang có yêu cầu đổi địa chỉ chờ duyệt thì không gửi thêm yêu cầu (UC-25, Alt-11, BR-12). UC-13 dùng mục Vị trí bản đồ làm một điều kiện của checklist mở gian hàng, quy định gian hàng Đang chuẩn bị chưa lên bản đồ công khai và Tạm ngừng vẫn có pin (BR-2, BR-4), trạng thái hoạt động độc lập với Đang đón khách (BR-5). UC-08 hiện nhãn và bộ lọc Đang đón khách theo trạng thái hiện tại do UC-25 quy định, có nút Xem trên bản đồ dẫn tới UC-26; ở chế độ xem trước gian hàng Đang chuẩn bị (UC-08, Alt-05) nút này dẫn tới bản đồ xem trước (UC-26, Alt-08). UC-06 có lối vào Bản đồ. |
| BRD-07 | `07-notification` | Nhóm này không phát sinh thông báo. |
| BRD-08 | `08-order` | Phương thức Nhận tại xưởng (UC-07, UC-31) dùng Chỉ đường từ bản đồ (UC-26, Alt-05). |

## III. Use Cases

| Use case ID | Use case | Description |
|---|---|---|
| UC-25 | Quản lý vị trí và lịch đón khách | Artisan xác nhận vị trí xưởng (gợi ý theo địa chỉ, kiểm tra lại khi đổi địa chỉ), đặt chế độ, lịch hằng tuần, lịch tắt cho trạng thái Đang đón khách và nhập ghi chú tham quan |
| UC-26 | Khám phá xưởng trên bản đồ | Guest hoặc Buyer xem pin các xưởng, lọc xưởng đang đón khách, bấm pin xem popup có dòng giờ đón khách và giờ cả tuần, đi tới gian hàng hoặc chỉ đường; chủ xưởng xem trước ghim của gian hàng chưa mở |

### UC-25 — Quản lý vị trí và lịch đón khách

#### Detail

| Field | Content |
|---|---|
| Use case ID | UC-25 |
| Description | Artisan xác nhận vị trí xưởng trên bản đồ (hệ thống gợi ý ghim theo địa chỉ, chủ xưởng kiểm tra và kéo ghim nếu chưa đúng), đặt chế độ đón khách, lịch hằng tuần, lịch tắt và ghi chú tham quan. Trạng thái Đang đón khách của xưởng tự bật, tắt theo các thiết lập này; giờ đã đặt là nguồn của dòng giờ đón khách khách hàng thấy ở UC-26. Khi xưởng xin đổi địa chỉ (UC-07), chủ xưởng kiểm tra lại ghim cho địa chỉ mới ngay lúc gửi yêu cầu, và vị trí mới được áp dụng cùng địa chỉ khi Admin duyệt. |
| Actors | Artisan |
| Channel | Kênh người bán |
| Preconditions | • Đã đăng nhập và có quyền bán (hồ sơ đã được duyệt ở UC-02)<br>• Gian hàng ở bất kỳ trạng thái nào (Đang chuẩn bị, Đang hoạt động, Tạm ngừng) |
| Expected | **Thành công:**<br>• Vị trí, chế độ đón khách, lịch và ghi chú được lưu, có hiệu lực ngay<br>• Gian hàng đã mở thì pin hiện hoặc đổi chỗ trên bản đồ công khai; mục Vị trí bản đồ trong checklist mở gian hàng (UC-13) đạt<br>• Từ đó trạng thái Đang đón khách tự đổi theo lịch<br>**Thất bại:**<br>• Hiển thị lỗi, thiết lập cũ giữ nguyên |
| Screens | • Vị trí và lịch đón khách (bước 1-8, 10)<br>• Bản đồ trong form đổi địa chỉ ở mục Gian hàng (Alt-11) |
| Notifications | Không có |

#### Main Flow

Trigger: Artisan chọn mục Vị trí và lịch đón khách trong Kênh người bán.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Artisan | Artisan mở mục Vị trí và lịch đón khách. | Bản đồ hiển thị khu làng nghề. Đã có vị trí thì ghim nằm ở vị trí đang lưu; hiển thị chế độ đón khách, lịch hằng tuần, lịch tắt hiện tại (mặc định chế độ Tắt). |
| 2 | Backend | Nếu gian hàng chưa có vị trí, Backend tìm vị trí theo địa chỉ xưởng ở UC-07 và đặt ghim gợi ý. | Ghim gợi ý hiện trên bản đồ kèm dòng "Vị trí gợi ý theo địa chỉ của xưởng. Vui lòng kiểm tra và kéo ghim nếu chưa đúng"; vĩ độ, kinh độ tự điền; chưa lưu. |
| 3 | Artisan | Artisan kiểm tra ghim; nếu chưa đúng thì bấm lên bản đồ hoặc kéo ghim đến đúng vị trí xưởng. | Ghim ở điểm xưởng chọn; vĩ độ, kinh độ tự điền lại, chỉ xem. |
| 4 | Artisan | Artisan chọn Chế độ đón khách: Theo lịch. | Hiện phần Lịch hằng tuần. |
| 5 | Artisan | Artisan đặt Lịch hằng tuần: bật các ngày xưởng đón khách từ Thứ Hai đến Chủ nhật và chọn giờ bắt đầu, giờ kết thúc riêng cho từng ngày (tối đa một khung giờ mỗi ngày). | Lịch hằng tuần hiển thị trong form. |
| 6 | Artisan | Artisan thêm Lịch tắt (nếu có): chọn từ ngày, đến ngày, nghỉ cả ngày hoặc một khung giờ cụ thể, và lý do (không bắt buộc, không công khai). | Mục lịch tắt hiển thị trong danh sách. |
| 7 | Artisan | Artisan nhập ghi chú tham quan (nếu có). | Ghi chú hiển thị trong form. |
| 8 | Artisan | Artisan chọn Lưu. | Dữ liệu được gửi lên kiểm tra. |
| 9 | Backend | Backend kiểm tra dữ liệu, lưu vị trí, chế độ, lịch, ghi chú và tính trạng thái Đang đón khách hiện tại. | Thông tin mới có hiệu lực ngay. |
| 10 | Frontend | Frontend báo lưu thành công. | Artisan thấy thiết lập đã cập nhật và dòng giờ đón khách như khách sẽ thấy (ví dụ "Đang đón khách · Đóng cửa lúc 17:00" hoặc "Không đón khách · Mở lại 08:00 ngày mai"); gian hàng đã mở thì pin hiện trên bản đồ công khai (UC-26) và nhãn Đang đón khách hiện ở UC-08 khi xưởng đang trong giờ đón khách. |

Outcome: Vị trí xưởng và lịch đón khách đã lưu; trạng thái Đang đón khách tự đổi theo lịch.

#### Alternative Flows

##### Alt-01 — ERR-UC25-01 — Dữ liệu vị trí hoặc ghi chú không hợp lệ

- At step: 9 · Trigger: Chưa xác nhận vị trí khi Lưu (chưa có ghim); tọa độ ngoài khoảng hợp lệ; ghi chú tham quan quá 300 ký tự.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Backend | Backend từ chối lưu và đánh dấu trường sai. | Hiển thị ERR-UC25-01 tại trường sai. |

Resume: Quay lại bước 3, giữ nguyên dữ liệu đã nhập.

##### Alt-02 — ERR-UC25-04 — Lịch đón khách không hợp lệ

- At step: 9 · Trigger: Chế độ Theo lịch mà không bật ngày nào; giờ kết thúc không sau giờ bắt đầu; mục lịch tắt có ngày bắt đầu ở quá khứ, ngày kết thúc trước ngày bắt đầu hoặc quá 1 năm kể từ hôm nay; quá 20 mục lịch tắt; lý do quá 100 ký tự. Các mục lịch tắt trùng nhau không bị coi là lỗi (BR-4).

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Backend | Backend từ chối lưu và đánh dấu dòng lịch sai. | Hiển thị ERR-UC25-04 tại dòng lịch sai. |

Resume: Quay lại bước 5 hoặc 6, giữ nguyên dữ liệu đã nhập.

##### Alt-03 — Đổi vị trí đã lưu, đặt lại theo địa chỉ

- At step: 3 · Trigger: Gian hàng đã có vị trí; Artisan kéo ghim sang điểm khác, hoặc chọn Đặt theo địa chỉ (ví dụ vị trí đang lưu bị lệch dù địa chỉ không đổi). Khi đổi địa chỉ thì dùng Alt-11.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Backend | Nếu Artisan chọn Đặt theo địa chỉ, Backend tìm vị trí theo địa chỉ hiện tại ở UC-07 và đặt ghim gợi ý; Artisan vẫn kiểm tra và kéo ghim như bước 3. | Ghim gợi ý thay ghim đang lưu trên form; vị trí đang lưu chưa đổi. |
| 2 | Backend | Sau khi Artisan chọn Lưu, Backend lưu vị trí mới thay vị trí cũ; không giữ lịch sử vị trí. | Gian hàng đã mở thì pin trên bản đồ công khai chuyển sang vị trí mới ngay, không còn pin ở vị trí cũ. |

Resume: Tiếp tục bước 10.

Ngoại lệ — ERR-UC25-07: Khi đang có yêu cầu đổi địa chỉ chờ Admin duyệt thì luồng này không áp dụng (BR-12). Frontend khóa ghim và nút Đặt theo địa chỉ, hiện dòng "Yêu cầu đổi địa chỉ đang chờ BQL duyệt. Vị trí mới sẽ được áp dụng cùng địa chỉ khi yêu cầu được duyệt". Nếu thao tác đổi vị trí vẫn được gửi lên, Backend từ chối và hiển thị ERR-UC25-07; vị trí đang lưu không đổi. Chế độ, lịch và ghi chú vẫn lưu bình thường.

##### Alt-04 — ERR-UC25-05 — Không tìm được vị trí theo địa chỉ

- At step: 2, Alt-03 bước 1 hoặc Alt-11 bước 1 · Trigger: Dịch vụ tìm địa chỉ không phản hồi hoặc không tìm thấy vị trí khớp với địa chỉ xưởng.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Backend | Backend không đặt ghim gợi ý. | Hiển thị ERR-UC25-05; bản đồ ở khu làng nghề, chưa có ghim hoặc giữ ghim đang lưu. |

Resume: Tiếp tục bước 3 (hoặc Alt-11 bước 2), Artisan tự kéo bản đồ rồi bấm lên bản đồ để chọn vị trí.

##### Alt-05 — Chế độ Tắt hoặc Luôn bật

- At step: 4 · Trigger: Artisan chọn Chế độ đón khách là Tắt hoặc Luôn bật thay vì Theo lịch.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Frontend | Frontend ẩn Lịch hằng tuần; với Luôn bật vẫn cho đặt Lịch tắt; với Tắt ẩn cả Lịch tắt và Ghi chú tham quan nhưng giữ nội dung đã nhập. | Tắt thì xưởng không bao giờ ở trạng thái Đang đón khách và không hiển thị giờ đón khách trên bản đồ; Luôn bật thì xưởng đón khách 24/7, Đang đón khách mọi lúc trừ khi nằm trong Lịch tắt. |

Resume: Tiếp tục bước 7 hoặc bước 8.

##### Alt-06 — Xóa mục lịch tắt

- At step: 6 · Trigger: Artisan chọn Xóa trên một mục lịch tắt.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Frontend | Frontend hiện hộp xác nhận xóa. | Artisan xác nhận hoặc hủy. |
| 2 | Backend | Sau khi Artisan chọn Lưu, Backend xóa mục lịch tắt. | Xưởng đón khách trở lại theo lịch kể từ lúc lưu, kể cả khi mục đó đang áp dụng. |

Resume: Tiếp tục bước 10.

##### Alt-07 — Trạng thái tự đổi theo chế độ và lịch

- At step: Sau bước 10 (hệ thống tự chạy) · Trigger: Tới thời điểm đổi trạng thái: giờ bắt đầu hoặc kết thúc của khung giờ hằng tuần (chế độ Theo lịch), hoặc thời điểm bắt đầu hoặc kết thúc của một mục lịch tắt (chế độ Luôn bật hoặc Theo lịch). Chế độ Tắt không có thời điểm đổi trạng thái.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Backend | Backend tính lại trạng thái theo giờ Việt Nam, chính xác đến phút, theo chế độ: Tắt thì Không đón khách; Luôn bật thì Đang đón khách nếu không nằm trong lịch tắt; Theo lịch thì Đang đón khách nếu đang trong khung giờ của ngày tương ứng và không nằm trong lịch tắt. Đang nằm trong lịch tắt thì Tạm nghỉ, ngoài giờ đón khách thì Không đón khách (BR-4). | Nhãn Đang đón khách, dòng giờ đón khách, bộ lọc ở UC-26 và UC-08 phản ánh trạng thái mới ở lần tải hoặc lần bấm pin tiếp theo; chủ xưởng không phải thao tác. |
| 2 | Backend | Backend tự ẩn các mục lịch tắt đã qua khỏi danh sách. | Danh sách lịch tắt chỉ còn mục đang hoặc sắp áp dụng. |

Resume: Kết thúc.

##### Alt-08 — Gian hàng chưa mở

- At step: 10 · Trigger: Gian hàng đang Đang chuẩn bị.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Frontend | Frontend báo lưu thành công và hiện dòng "Gian hàng chưa mở nên xưởng chưa hiện trên bản đồ công khai". | Thiết lập đã lưu nhưng chưa công khai; mục Vị trí bản đồ trong checklist mở gian hàng chuyển sang đạt (UC-13). |

Resume: Kết thúc. Khi gian hàng được mở (UC-13), pin hiện trên bản đồ công khai. Trước đó chủ xưởng xem trước ghim của mình ở UC-26 (Alt-08).

##### Alt-09 — ERR-UC25-02 — Không tải được bản đồ

- At step: 1 · Trigger: Bản đồ hoặc nền bản đồ không tải được.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Frontend | Frontend hiện lỗi và nút Thử lại; chưa cho Lưu vị trí. | Hiển thị ERR-UC25-02. |

Resume: Chọn Thử lại thì lặp bước 1.

##### Alt-10 — ERR-UC25-03 — Không có quyền

- At step: 1 · Trigger: Tài khoản chưa có quyền bán, tài khoản Admin, hoặc thao tác trên gian hàng không phải của mình.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Backend | Backend từ chối thao tác. | Hiển thị ERR-UC25-03; thiết lập không đổi. |

Resume: Kết thúc.

##### Alt-11 — Đổi địa chỉ xưởng, kiểm tra lại ghim

- At step: Tại UC-07 (BRD 02, Alt-03), ngoài luồng chính · Trigger: Artisan sửa địa chỉ xưởng đã duyệt ở mục Gian hàng, khi không có yêu cầu đổi địa chỉ nào khác đang chờ duyệt (nếu có, UC-07 từ chối bằng ERR-UC07-03, BR-12).

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Backend | Khi địa chỉ được đổi, Backend tìm vị trí theo địa chỉ mới và đặt ghim gợi ý trên bản đồ hiện ngay trong form (như bước 2 của luồng chính). | Ghim gợi ý hiện kèm dòng "Vị trí gợi ý theo địa chỉ mới. Vui lòng kiểm tra và kéo ghim nếu chưa đúng"; vĩ độ, kinh độ tự điền; chưa lưu. |
| 2 | Artisan | Artisan kiểm tra ghim, kéo hoặc bấm lên bản đồ nếu chưa đúng rồi chọn Lưu. | Dữ liệu được gửi lên kiểm tra. |
| 3 | Backend | Backend lưu yêu cầu đổi địa chỉ ở trạng thái chờ duyệt (UC-07, Alt-03) kèm vị trí đề nghị. | Địa chỉ và vị trí đang lưu không đổi; pin công khai vẫn ở vị trí cũ trong lúc chờ; vị trí đang lưu bị khóa cho tới khi yêu cầu được xử lý (BR-12). |
| 4 | Admin | Admin mở yêu cầu và duyệt ở UC-02 (Alt-05). | Chi tiết hiển thị địa chỉ và vị trí hiện tại cạnh địa chỉ và vị trí đề nghị. |
| 5 | Backend | Nếu Admin duyệt, Backend áp dụng địa chỉ mới và vị trí đề nghị cùng lúc; nếu Admin từ chối, giữ nguyên cả địa chỉ và vị trí cũ. | Duyệt: gian hàng đã mở thì pin chuyển sang vị trí mới ngay, không còn pin cũ. Từ chối: pin ở vị trí cũ. Vị trí được mở khóa. |

Resume: Kết thúc. Thông báo kết quả cho Artisan do UC-02 phát (NT-UC02-04, NT-UC02-05).

##### Alt-12 — ERR-UC25-06 — Chưa có vị trí cho địa chỉ mới

- At step: Alt-11 bước 2 · Trigger: Artisan chọn Lưu khi đã đổi địa chỉ nhưng chưa có ghim (không tìm được vị trí theo địa chỉ mới và chưa bấm chọn trên bản đồ).

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Backend | Backend từ chối gửi yêu cầu đổi địa chỉ. | Hiển thị ERR-UC25-06; không tạo yêu cầu chờ duyệt; các thay đổi khác ở mục Gian hàng lưu bình thường. |

Resume: Quay lại Alt-11 bước 2, giữ nguyên dữ liệu đã nhập.

#### Data Fields

| Field | Format | Validation | Required | Ghi chú |
|---|---|---|---|---|
| Vị trí xưởng | Ghim trên bản đồ, chọn bằng cách bấm hoặc kéo | Phải có ghim khi Lưu | Có | Khi chưa có vị trí, hệ thống gợi ý ghim theo địa chỉ ở UC-07; chủ xưởng phải Lưu để xác nhận. Bắt buộc để mở gian hàng (UC-13). Mỗi gian hàng một vị trí. Bị khóa khi có yêu cầu đổi địa chỉ chờ duyệt (BR-12) |
| Vị trí đề nghị (khi đổi địa chỉ) | Ghim trên bản đồ trong form đổi địa chỉ, chọn bằng cách bấm hoặc kéo | Phải có ghim khi gửi yêu cầu đổi địa chỉ | Có điều kiện | Gửi cùng yêu cầu đổi địa chỉ; chỉ áp dụng khi Admin duyệt, trong lúc chờ không ảnh hưởng vị trí đang lưu |
| Vĩ độ, kinh độ | Number; 6 chữ số thập phân, chỉ xem | Vĩ độ từ −90 đến 90, kinh độ từ −180 đến 180 | — | Tự điền theo ghim, người dùng không nhập tay. Ví dụ: 20.900000, 105.700000 |
| Chế độ đón khách | Chọn một | Tắt, Luôn bật hoặc Theo lịch | Có | Mặc định Tắt |
| Lịch hằng tuần | 7 dòng Thứ Hai đến Chủ nhật; mỗi dòng bật hoặc tắt, giờ bắt đầu và giờ kết thúc riêng (HH:mm) | Chỉ khi Theo lịch; ít nhất một ngày bật; giờ kết thúc sau giờ bắt đầu, trong cùng ngày, không qua nửa đêm; mỗi ngày một khung giờ; giờ Việt Nam | Có điều kiện | Ví dụ: Thứ Hai đến Thứ Sáu 08:00 đến 17:00, Thứ Bảy 08:00 đến 12:00, Chủ nhật nghỉ. Là nguồn của dòng giờ đón khách và Giờ cả tuần ở UC-26 |
| Lịch tắt | Danh sách tối đa 20 mục; mỗi mục gồm từ ngày, đến ngày, giờ từ, giờ đến (tùy chọn), lý do (text; length 100, tùy chọn) | Từ ngày không ở quá khứ; đến ngày không trước từ ngày và không quá 1 năm kể từ hôm nay; giờ đến sau giờ từ; không chọn giờ là nghỉ cả ngày, có chọn giờ thì áp dụng cho từng ngày trong khoảng; các mục trùng nhau được chấp nhận và hợp nhất khi tính | Không | Áp dụng khi chế độ Luôn bật hoặc Theo lịch; luôn được ưu tiên hơn lịch hằng tuần và Luôn bật. Mục đã qua tự ẩn. Lý do chỉ chủ xưởng thấy, không hiện công khai. Ví dụ: 2026-12-20 đến 2026-12-22, cả ngày, "Nghỉ lễ" |
| Ghi chú tham quan | Text; length 300 | Chỉ nhập khi chế độ khác Tắt | Không | Mức Should. Ví dụ: "Có trải nghiệm đan giỏ mây, nên báo trước qua điện thoại." |
| Trạng thái đón khách hiện tại | Chỉ xem | Đang đón khách, Không đón khách hoặc Tạm nghỉ; hệ thống tính theo chế độ, lịch hằng tuần, lịch tắt và giờ Việt Nam | — | Hiện cho chủ xưởng ở đầu form cùng dòng giờ đón khách như khách thấy ở popup (UC-26), để chủ xưởng thấy ngay kết quả của lịch |

#### Error Messages

| Code | Condition | Message | Display | Flow |
|---|---|---|---|---|
| ERR-UC25-01 | Dữ liệu vị trí hoặc ghi chú không hợp lệ | "Thông tin chưa hợp lệ. Vui lòng xác nhận vị trí xưởng trên bản đồ và kiểm tra ghi chú tham quan (tối đa 300 ký tự)." | Inline | Alt-01 |
| ERR-UC25-02 | Không tải được bản đồ | "Không tải được bản đồ. Vui lòng thử lại." | Inline | Alt-09 |
| ERR-UC25-03 | Không có quyền | "Bạn không có quyền thực hiện thao tác này." | Trang lỗi | Alt-10 |
| ERR-UC25-04 | Lịch đón khách không hợp lệ | "Lịch đón khách chưa hợp lệ. Vui lòng kiểm tra các dòng được đánh dấu." | Inline | Alt-02 |
| ERR-UC25-05 | Không tìm được vị trí theo địa chỉ | "Chưa tìm được vị trí theo địa chỉ của xưởng. Vui lòng bấm lên bản đồ để chọn vị trí." | Inline | Alt-04 |
| ERR-UC25-06 | Chưa có vị trí cho địa chỉ mới | "Vui lòng kiểm tra và xác nhận vị trí xưởng trên bản đồ cho địa chỉ mới trước khi gửi yêu cầu." | Inline | Alt-12 |
| ERR-UC25-07 | Đổi vị trí khi đang có yêu cầu đổi địa chỉ chờ duyệt | "Yêu cầu đổi địa chỉ đang chờ BQL duyệt nên chưa thể đổi vị trí xưởng. Vị trí mới sẽ được áp dụng cùng địa chỉ khi yêu cầu được duyệt." | Inline | Alt-03 |

#### Business Rules

| ID | Rule | Source |
|---|---|---|
| BR-1 | Mỗi gian hàng có đúng một vị trí; chỉ chủ xưởng xác nhận và đổi vị trí của mình, không có thao tác xóa vị trí, chỉ đổi. Admin không duyệt riêng vị trí; chỉ khi xưởng xin đổi địa chỉ thì vị trí đề nghị đi kèm yêu cầu và Admin xem cùng khi duyệt. Không giới hạn vị trí trong khu vực làng nghề. | Overview mục 1, 3.5; Nhóm đề xuất; Chốt 2026-11-18 |
| BR-2 | Vị trí là điều kiện bắt buộc để mở gian hàng và mở lại (UC-13); mục Vị trí bản đồ trong checklist đạt khi vị trí đã được lưu. | Overview 3.2; UC-13 (BRD 02) |
| BR-3 | Hệ thống gợi ý ghim theo địa chỉ ở UC-07; vị trí gợi ý không tự lưu, chủ xưởng phải kiểm tra, chỉnh nếu cần rồi Lưu. Không tìm được thì chủ xưởng tự kéo bản đồ và chọn vị trí; không có ô tìm địa chỉ riêng. Vĩ độ, kinh độ tự điền, không phải nhập số. | Đề bài §4.3; Overview mục 8; Chốt 2026-11-18 |
| BR-4 | Trạng thái Đang đón khách do hệ thống tự tính theo giờ Việt Nam, chính xác đến phút, Artisan không bật tắt thủ công mỗi ngày. Chế độ Tắt thì Không đón khách và không hiển thị giờ đón khách trên bản đồ; Luôn bật thì đón khách 24/7, Đang đón khách nếu không nằm trong Lịch tắt; Theo lịch thì Đang đón khách nếu đang trong khung giờ của ngày tương ứng và không nằm trong Lịch tắt. Mặc định chế độ Tắt. Lịch tắt luôn được ưu tiên hơn lịch hằng tuần và chế độ Luôn bật; nhiều mục lịch tắt trùng nhau được hợp nhất thành các khoảng nghỉ liên tục để tính. Phần thời gian còn lại sau khi trừ lịch tắt là giờ đón khách thực tế; dòng giờ đón khách, Giờ cả tuần và bộ lọc ở UC-26 đều dùng cùng cách tính này. Khung giờ đón khách tính cả phút kết thúc (08:00 đến 17:00 thì 17:00 vẫn là Đang đón khách, từ 17:01 là Không đón khách); lịch tắt theo khung giờ có hiệu lực từ phút bắt đầu đến trước phút kết thúc (lịch tắt 12:00 đến 13:00 thì 12:00 đã Tạm nghỉ, 13:00 xưởng đón khách trở lại). | Đề bài §3.2; Overview 3.2, 3.5; Chốt 2026-11-18 |
| BR-5 | Chế độ đón khách đặt được ở mọi trạng thái gian hàng và độc lập với trạng thái hoạt động: xưởng Tạm ngừng vẫn có thể Đang đón khách. | Overview 3.2; UC-13 BR-5 (BRD 02) |
| BR-6 | Gian hàng Đang chuẩn bị đặt được vị trí và lịch đón khách nhưng chưa lên bản đồ công khai và danh sách xưởng; gian hàng đã mở (kể cả Tạm ngừng) hiện công khai. | Overview 3.2, 3.5; UC-13 BR-2, BR-4 (BRD 02) |
| BR-7 | Lưu vị trí hoặc lịch có hiệu lực ngay trên bản đồ (UC-26) và danh sách xưởng (UC-08); việc bật, tắt theo giờ do hệ thống thực hiện, hiện ở lần tải tiếp theo của bản đồ hoặc danh sách, và ở dòng giờ đón khách mỗi lần bấm pin. | Nhóm đề xuất |
| BR-8 | Địa chỉ chữ (UC-07) và vị trí bản đồ là hai thông tin riêng, nhưng khi đổi địa chỉ phải kiểm tra lại ghim: yêu cầu đổi địa chỉ bắt buộc kèm vị trí đề nghị; khi Admin duyệt (UC-02, Alt-05) địa chỉ mới và vị trí đề nghị được áp dụng đồng thời; khi từ chối thì cả hai giữ nguyên. Trong lúc chờ duyệt, địa chỉ và pin vẫn như cũ và vị trí không đổi được (BR-12). | Chốt 2026-11-18 |
| BR-9 | Lịch hằng tuần: Artisan chọn các ngày từ Thứ Hai đến Chủ nhật, mỗi ngày đặt giờ riêng với tối đa một khung giờ bắt đầu đến kết thúc, không qua nửa đêm, ít nhất một ngày được bật; không nhập nhiều ca trong một ngày. Lịch tắt: nghỉ cả ngày hoặc một khung giờ cụ thể, tối đa 20 mục, ngày kết thúc không quá 1 năm kể từ hôm nay, mục đã qua tự ẩn, lý do không bắt buộc và không công khai. Hệ thống không tự nhận biết ngày lễ, xưởng tự thêm vào lịch tắt. | Chốt 2026-11-18 |
| BR-10 | Ghi chú tham quan (mức Should) chỉ hiện ở popup khi xưởng đang Đang đón khách; ngoài giờ hoặc khi chế độ Tắt thì ẩn nhưng giữ nội dung để dùng lại. | Overview 3.5; Nhóm đề xuất |
| BR-11 | Giờ đón khách thực tế (sau khi trừ lịch tắt) được hiển thị công khai ở popup xưởng (UC-26) dưới dạng dòng giờ đón khách và Giờ cả tuần; lý do lịch tắt chỉ chủ xưởng thấy. Giờ cả tuần có thể hiện nhiều khoảng giờ trong một ngày do trừ lịch tắt, nhưng chủ xưởng vẫn chỉ nhập một khung giờ mỗi ngày (BR-9). | Chốt 2026-11-18 |
| BR-12 | Khi có yêu cầu đổi địa chỉ đang chờ Admin duyệt, Artisan không được thay đổi vị trí xưởng hiện tại hoặc gửi thêm yêu cầu đổi địa chỉ. Vẫn được chỉnh sửa chế độ và lịch đón khách. Địa chỉ và vị trí mới chỉ được áp dụng đồng thời khi Admin duyệt. | Chốt 2026-11-18 |

#### Acceptance Criteria

| ID | Given | When | Then | Covers |
|---|---|---|---|---|
| UC-25-AC-01 | Artisan đã đăng nhập, gian hàng Đang hoạt động chưa có vị trí, địa chỉ đã nhập ở UC-07 | Mở mục Vị trí và lịch đón khách, kiểm tra ghim gợi ý, chọn Theo lịch, bật Thứ Hai đến Thứ Sáu 08:00 đến 17:00, chọn Lưu | Ghim gợi ý nằm theo địa chỉ kèm dòng yêu cầu kiểm tra; sau Lưu vị trí và lịch được lưu; vĩ độ, kinh độ tự điền; pin hiện trên bản đồ công khai; xưởng hiện Đang đón khách trong giờ lịch | Main Flow, BR-3, BR-4 |
| UC-25-AC-02 | Ghim gợi ý lệch vị trí thật của xưởng | Artisan kéo ghim đến đúng chỗ rồi Lưu | Vị trí lưu là điểm Artisan chọn, không phải điểm gợi ý | Main Flow, BR-3 |
| UC-25-AC-03 | Artisan mở mục, thấy ghim gợi ý | Đóng trang mà không chọn Lưu | Gian hàng vẫn chưa có vị trí; không có pin công khai | BR-3 |
| UC-25-AC-04 | Artisan chưa có ghim (không tìm được vị trí theo địa chỉ) hoặc ghi chú dài 301 ký tự | Chọn Lưu | Hiển thị ERR-UC25-01 tại trường sai; dữ liệu đã nhập được giữ; không lưu | Alt-01 |
| UC-25-AC-05 | Chế độ Theo lịch | Không bật ngày nào; hoặc đặt giờ kết thúc 08:00 cho giờ bắt đầu 17:00; hoặc thêm lịch tắt bắt đầu hôm qua; rồi chọn Lưu | Hiển thị ERR-UC25-04 tại dòng sai; không lưu thiết lập nào | Alt-02, BR-9 |
| UC-25-AC-06 | Gian hàng Đang hoạt động đã có vị trí, không có yêu cầu đổi địa chỉ đang chờ | Kéo ghim sang điểm khác và Lưu | Pin trên bản đồ công khai chuyển sang vị trí mới ngay, không còn pin ở vị trí cũ | Alt-03, BR-7 |
| UC-25-AC-07 | Gian hàng Đang hoạt động đã có vị trí; Artisan xin đổi địa chỉ, kiểm tra ghim gợi ý theo địa chỉ mới, kéo chỉnh và Lưu | Guest xem bản đồ trong lúc chờ duyệt; Admin mở yêu cầu rồi duyệt; Guest xem lại bản đồ | Admin thấy địa chỉ và vị trí hiện tại cạnh địa chỉ và vị trí đề nghị; trong lúc chờ pin và địa chỉ giữ như cũ; sau khi duyệt pin ở vị trí đề nghị cùng lúc với địa chỉ mới, không còn pin ở vị trí cũ | Alt-11, BR-8 |
| UC-25-AC-08 | Dịch vụ tìm địa chỉ lỗi hoặc không thấy địa chỉ | Artisan mở mục khi chưa có vị trí | Hiển thị ERR-UC25-05; không có ghim gợi ý; Artisan bấm lên bản đồ chọn được vị trí | Alt-04 |
| UC-25-AC-09 | Xưởng đang Theo lịch | Artisan chọn Luôn bật, rồi chọn Tắt, mỗi lần chọn Lưu | Luôn bật thì không còn Lịch hằng tuần, xưởng đón khách 24/7, Đang đón khách mọi lúc trừ lịch tắt; Tắt thì xưởng Không đón khách, popup không hiện ghi chú và không có dòng giờ đón khách; nội dung lịch và ghi chú cũ vẫn còn khi chọn lại chế độ | Alt-05, BR-4, BR-10 |
| UC-25-AC-10 | Xưởng Theo lịch Thứ Hai 08:00 đến 17:00 | Guest mở bản đồ lúc 07:59, 08:00, 17:00 và 17:01 thứ Hai (giờ Việt Nam) | 07:59 Không đón khách; 08:00 và 17:00 Đang đón khách; 17:01 Không đón khách | Alt-07, BR-4 |
| UC-25-AC-11 | Xưởng Theo lịch, có lịch tắt cả ngày thứ Hai tới | Guest xem bản đồ vào thứ Hai đó, trong và ngoài khung giờ lịch | Xưởng Tạm nghỉ cả ngày thứ Hai vì lịch tắt ưu tiên hơn lịch hằng tuần; thứ Ba theo lịch bình thường; sau khi lịch tắt qua, mục tự ẩn khỏi danh sách | Alt-07, BR-4, BR-9 |
| UC-25-AC-12 | Có lịch tắt đang áp dụng | Artisan xóa mục đó, xác nhận và Lưu | Xưởng đón khách trở lại theo lịch từ lúc Lưu | Alt-06 |
| UC-25-AC-13 | Xưởng Luôn bật, có lịch tắt 12:00 đến 13:00 mỗi ngày trong khoảng 3 ngày | Guest xem bản đồ lúc 11:59, 12:00, 12:30, 13:00 và 14:00 trong khoảng đó | 11:59 Đang đón khách; 12:00 và 12:30 Tạm nghỉ; 13:00 và 14:00 Đang đón khách | Alt-07, BR-4, BR-9 |
| UC-25-AC-14 | Gian hàng Đang chuẩn bị | Đặt vị trí và lịch rồi Lưu | Lưu thành công, hiện dòng "Gian hàng chưa mở nên xưởng chưa hiện trên bản đồ công khai"; Guest không thấy pin; mục Vị trí bản đồ trong checklist (UC-13) đạt; khi gian hàng được mở thì pin hiện | Alt-08, BR-2, BR-6 |
| UC-25-AC-15 | Bản đồ không tải được | Artisan mở mục | Hiển thị ERR-UC25-02 kèm nút Thử lại | Alt-09 |
| UC-25-AC-16 | Tài khoản Buyer chưa có quyền bán, tài khoản Admin, hoặc Artisan khác | Mở hoặc gửi thay đổi của gian hàng này | Hiển thị ERR-UC25-03; thiết lập không đổi | Alt-10, BR-1 |
| UC-25-AC-17 | Gian hàng chưa có vị trí, các mục khác của checklist đủ | Artisan mở gian hàng (UC-13) | Bị từ chối vì thiếu mục Vị trí bản đồ; sau khi lưu vị trí thì mục này đạt | BR-2 |
| UC-25-AC-18 | Gian hàng đang Tạm ngừng | Đặt chế độ Luôn bật và Lưu | Lưu thành công; pin vẫn hiện và xưởng Đang đón khách | BR-5 |
| UC-25-AC-19 | Gian hàng đã có vị trí | Artisan tìm thao tác xóa vị trí | Không có thao tác xóa, chỉ có đổi vị trí | BR-1 |
| UC-25-AC-20 | Artisan nhập 21 mục lịch tắt; hoặc nghỉ ngày lễ | Lưu | Mục thứ 21 bị ERR-UC25-04; hệ thống không tự thêm ngày lễ | BR-9 |
| UC-25-AC-21 | Xưởng Theo lịch Thứ Hai đến Thứ Sáu 08:00 đến 17:00 | Artisan lưu lịch rồi xem đầu form lúc 10:00 ngày thường; sau đó thêm lịch tắt 12:00 đến 13:00 hôm nay và Lưu | Đầu form hiện "Đang đón khách · Đóng cửa lúc 17:00", đúng dòng Guest thấy ở popup; sau khi thêm lịch tắt hiện "Đang đón khách · Đóng cửa lúc 12:00" | Main Flow bước 10, BR-4, BR-11 |
| UC-25-AC-22 | Yêu cầu đổi địa chỉ kèm vị trí đề nghị đang chờ duyệt | Admin từ chối yêu cầu kèm lý do | Địa chỉ và pin giữ nguyên như cũ; vị trí đề nghị không được áp dụng; vị trí được mở khóa | Alt-11, BR-8 |
| UC-25-AC-23 | Artisan đã đổi địa chỉ, không tìm được vị trí theo địa chỉ mới và chưa bấm chọn trên bản đồ | Chọn Lưu | Hiển thị ERR-UC25-06; không tạo yêu cầu chờ duyệt; dữ liệu đã nhập được giữ; bấm chọn vị trí rồi Lưu thì yêu cầu được tạo | Alt-12, Alt-04, BR-8 |
| UC-25-AC-24 | Gian hàng đã có vị trí; có yêu cầu đổi địa chỉ kèm vị trí đề nghị đang chờ Admin duyệt | Artisan mở mục Vị trí và lịch đón khách, thử kéo ghim hoặc chọn Đặt theo địa chỉ; thử gửi yêu cầu đổi địa chỉ thứ hai ở UC-07; đổi chế độ và lịch đón khách rồi Lưu; sau đó Admin duyệt yêu cầu | Ghim và nút Đặt theo địa chỉ bị khóa kèm dòng "Yêu cầu đổi địa chỉ đang chờ BQL duyệt...", nếu vẫn gửi lên thì hiển thị ERR-UC25-07; yêu cầu thứ hai bị từ chối bằng ERR-UC07-03 và không được tạo; chế độ và lịch lưu bình thường; địa chỉ và vị trí đang lưu giữ nguyên cho tới khi duyệt; sau khi duyệt địa chỉ mới và vị trí đề nghị được áp dụng đồng thời | Alt-03, Alt-11, BR-8, BR-12 |
| UC-25-AC-25 | Xưởng Theo lịch Thứ Hai đến Thứ Sáu 08:00 đến 17:00, Artisan nhập hai mục lịch tắt trùng nhau Thứ Hai 12:00 đến 14:00 và 13:00 đến 15:00 | Lưu; Guest bấm pin lúc 14:30 Thứ Hai và xem Giờ cả tuần | Lưu thành công, không báo lỗi; hệ thống hợp nhất thành một khoảng nghỉ 12:00 đến 15:00; 14:30 hiện "Tạm nghỉ · Mở lại 15:00"; Giờ cả tuần của Thứ Hai ghi "08:00–12:00, 15:00–17:00" | Alt-02, BR-4, BR-9 |

### UC-26 — Khám phá xưởng trên bản đồ

#### Detail

| Field | Content |
|---|---|
| Use case ID | UC-26 |
| Description | Guest hoặc Buyer mở bản đồ làng nghề, xem pin các xưởng, lọc xưởng đang đón khách, bấm pin xem popup tóm tắt kèm dòng giờ đón khách (giống cách Google Maps hiện giờ mở cửa) và giờ cả tuần, rồi đi tới trang gian hàng hoặc chỉ đường tới xưởng. Chủ xưởng có thể xem trước ghim của gian hàng chưa mở (Alt-08). |
| Actors | Guest, Buyer (Buyer thao tác như Guest); Artisan khi xem trước gian hàng chưa mở (Alt-08) |
| Channel | Khu mua hàng |
| Preconditions | • Không yêu cầu đăng nhập (riêng xem trước ở Alt-08 cần đăng nhập Artisan) |
| Expected | **Thành công:**<br>• Bản đồ hiển thị pin các xưởng đã mở; người dùng xem được popup với giờ đón khách, đi tới trang gian hàng hoặc chỉ đường<br>**Thất bại:**<br>• Hiển thị lỗi ngay tại phần tải lỗi, giữ nguyên bộ lọc đang chọn |
| Screens | • Bản đồ làng nghề (bước 1-5)<br>• Popup xưởng (bước 6-7) |
| Notifications | Không có |

#### Main Flow

Trigger: Guest chọn Bản đồ ở menu hoặc ở trang chủ (UC-06), hoặc chọn Xem trên bản đồ ở trang gian hàng (UC-08).

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Guest | Guest mở Bản đồ. | Bản đồ khu làng nghề hiển thị cùng bộ lọc Đang đón khách và chú giải pin. |
| 2 | Frontend | Frontend tải danh sách pin của các xưởng đã mở kèm trạng thái Đang đón khách tại thời điểm tải. | Mỗi xưởng một pin; pin xưởng đang Đang đón khách có dấu riêng; trang không tải lại. |
| 3 | Guest | Guest kéo, phóng to hoặc thu nhỏ bản đồ. | Bản đồ cập nhật, pin giữ đúng vị trí. |
| 4 | Guest | Guest bật bộ lọc Đang đón khách. | Chỉ còn pin các xưởng đang Đang đón khách; tắt bộ lọc thì hiện lại tất cả pin. |
| 5 | Guest | Guest bấm một pin. | Popup xưởng mở tại pin đó. |
| 6 | Frontend | Frontend lấy thông tin tóm tắt của xưởng; Backend tính dòng giờ đón khách theo giờ Việt Nam tại lúc bấm. | Popup gồm ảnh đại diện, tên xưởng, địa chỉ, dòng giờ đón khách (nếu xưởng có đặt chế độ đón khách, ví dụ "Đang đón khách · Đóng cửa lúc 17:00"), ghi chú tham quan (nếu đang đón khách và có ghi chú, mức Should), liên kết Xem giờ cả tuần, nút Xem gian hàng và nút Chỉ đường. |
| 7 | Guest | Guest chọn Xem gian hàng trong popup. | Chuyển tới trang gian hàng của xưởng (UC-08). |

Outcome: Guest biết xưởng nào ở đâu, xưởng nào đang đón khách, bao giờ đóng hoặc mở lại và đi tiếp tới gian hàng hoặc đường tới xưởng.

#### Alternative Flows

##### Alt-01 — Chưa có xưởng nào trên bản đồ

- At step: 2 · Trigger: Chưa có gian hàng nào đã mở.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Frontend | Frontend vẫn hiện bản đồ và thông điệp "Chưa có xưởng nào trên bản đồ". | Bản đồ không có pin; bộ lọc Đang đón khách bị vô hiệu. |

Resume: Kết thúc.

##### Alt-02 — Không có xưởng nào đang đón khách

- At step: 4 · Trigger: Bật bộ lọc Đang đón khách mà lúc này không xưởng nào đang đón khách.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Frontend | Frontend hiện "Hiện chưa có xưởng nào đang đón khách" và nút Bỏ lọc. | Bản đồ không có pin. |

Resume: Chọn Bỏ lọc thì quay lại bước 3.

##### Alt-03 — ERR-UC26-01 — Không tải được bản đồ hoặc pin

- At step: 1 hoặc 2 · Trigger: Bản đồ, nền bản đồ hoặc danh sách pin không tải được.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Frontend | Frontend giữ trang và bộ lọc đang chọn, hiện lỗi và nút Thử lại. | Hiển thị ERR-UC26-01. |

Resume: Chọn Thử lại thì lặp bước đang làm.

##### Alt-04 — ERR-UC26-02 — Không tải được popup

- At step: 6 · Trigger: Thông tin xưởng của popup không tải được.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Frontend | Frontend giữ popup ở pin đó, hiện lỗi và nút Thử lại; bản đồ và các pin khác không bị ảnh hưởng. | Hiển thị ERR-UC26-02 trong popup. |

Resume: Chọn Thử lại thì lặp bước 6; bấm pin khác thì quay lại bước 5.

##### Alt-05 — Chỉ đường tới xưởng

- At step: 6 · Trigger: Guest chọn Chỉ đường trong popup.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Frontend | Frontend mở dịch vụ bản đồ bên ngoài ở tab mới với điểm đến là vị trí xưởng; điểm xuất phát do dịch vụ đó xác định. | Guest xem đường đi trên dịch vụ bên ngoài; hệ thống không tính lộ trình. |

Resume: Kết thúc, trang bản đồ vẫn giữ nguyên ở tab cũ.

##### Alt-06 — Xưởng đang tạm ngừng

- At step: 6 · Trigger: Pin được bấm thuộc gian hàng Tạm ngừng nhận giao dịch mới.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Frontend | Frontend thêm dòng "Xưởng đang tạm ngừng nhận giao dịch mới" vào popup. | Popup vẫn có nút Xem gian hàng và Chỉ đường; dòng giờ đón khách hiện theo trạng thái đón khách của xưởng. |

Resume: Tiếp tục bước 7.

##### Alt-07 — Mở từ trang gian hàng

- At step: 1 · Trigger: Guest chọn Xem trên bản đồ ở trang gian hàng (UC-08).

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Frontend | Frontend mở bản đồ, đưa pin của xưởng đó vào giữa màn hình và mở sẵn popup. | Guest thấy ngay xưởng vừa xem; các pin khác vẫn hiện. |

Resume: Tiếp tục bước 3.

##### Alt-08 — Chủ xưởng xem trước ghim khi gian hàng chưa mở

- At step: 1 · Trigger: Chủ xưởng đang xem trước gian hàng Đang chuẩn bị (UC-08, Alt-05) chọn Xem trên bản đồ.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Frontend | Frontend mở bản đồ như bình thường và hiển thị ghim của xưởng ở vị trí đã lưu ở UC-25. Ghim này chỉ hiển thị với chính Artisan, không xuất hiện trên bản đồ công khai của Guest, Buyer hoặc Artisan khác. | Ghim xem trước hiện ở vị trí đã lưu, kèm thông báo "Bạn đang xem trước vị trí xưởng. Gian hàng chưa mở nên vị trí này chưa được hiển thị công khai trên bản đồ." |
| 2 | Artisan | Artisan bấm ghim xem trước. | Popup mở như khi gian hàng đã mở (ảnh, tên, địa chỉ, dòng giờ đón khách, ghi chú tham quan, nút Xem gian hàng, nút Chỉ đường); không có thao tác chỉnh vị trí tại đây, muốn sửa phải quay về UC-25. |

Nếu gian hàng chưa có vị trí đã lưu: Frontend không hiện ghim xem trước mà hiện dòng "Gian hàng chưa có vị trí. Vui lòng đặt vị trí ở mục Vị trí và lịch đón khách."

Resume: Tiếp tục bước 3.

##### Alt-09 — Nhiều pin ở gần nhau

- At step: 3 · Trigger: Nhiều xưởng nằm gần nhau nên các pin chồng lên nhau ở mức thu phóng hiện tại.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Frontend | Frontend gom các pin thành cụm pin kèm số xưởng. | Bản đồ không bị chồng pin. |
| 2 | Guest | Guest bấm cụm pin hoặc phóng to bản đồ. | Bản đồ phóng tới vùng đó và tách cụm thành từng pin. |

Resume: Tiếp tục bước 5.

##### Alt-10 — Xem giờ cả tuần

- At step: 6 · Trigger: Guest chọn Xem giờ cả tuần trong popup.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Frontend | Frontend mở rộng popup, hiện 7 ngày liên tiếp bắt đầu từ hôm nay, hôm nay in đậm; mỗi ngày hiện các khoảng giờ đón khách thực tế sau khi trừ lịch tắt (BR-12). | Mỗi dòng có thứ, ngày và khoảng giờ, ví dụ "Thứ Tư 18/11: 08:00–17:00", hoặc "Thứ Tư 18/11: 08:00–12:00, 13:00–17:00" nếu có lịch tắt một phần; "Nghỉ" nếu xưởng không đón khách ngày đó; "Tạm nghỉ" nếu cả thời gian đón khách của ngày đó nằm trong lịch tắt. |
| 2 | Guest | Guest chọn Thu gọn. | Popup trở về dạng tóm tắt. |

Resume: Tiếp tục bước 7.

#### Data Fields

| Field | Format | Validation | Required | Ghi chú |
|---|---|---|---|---|
| Đang đón khách (bộ lọc) | Bật / tắt | — | Không | Mặc định Tắt, hiện tất cả xưởng. Lọc theo trạng thái tại thời điểm tải |
| Pin xưởng | Chỉ xem | Mỗi xưởng đã mở một pin theo vị trí ở UC-25 | — | Pin xưởng đang Đang đón khách có dấu riêng; có chú giải. Giờ đón khách chỉ hiện ở popup |
| Ảnh đại diện, tên xưởng | Chỉ xem | — | — | Từ UC-07 |
| Địa chỉ | Text, chỉ xem | Địa chỉ công khai của gian hàng | — | Từ UC-07 |
| Dòng giờ đón khách | Text, chỉ xem; ba nhãn tô màu: Đang đón khách, Không đón khách, Tạm nghỉ | Chỉ hiện khi chế độ khác Tắt; tính theo giờ Việt Nam tại lúc bấm pin theo quy tắc BR-11 | Không | Ví dụ: "Đang đón khách · Đóng cửa lúc 17:00"; "Đang đón khách · Mở cả ngày"; "Không đón khách · Mở lúc 08:00"; "Không đón khách · Mở lại 08:00 ngày mai"; "Tạm nghỉ · Mở lại 13:00"; "Tạm nghỉ · Mở lại 08:00 Thứ Hai". Không có nhãn "Sắp đóng cửa" |
| Giờ cả tuần | Danh sách 7 dòng, thu gọn mặc định | Chỉ hiện khi chế độ khác Tắt; 7 ngày liên tiếp từ hôm nay, tính theo giờ đón khách thực tế sau khi trừ lịch tắt (BR-12) | Không | Mỗi dòng: thứ, ngày dd/MM và các khoảng giờ, hoặc "Cả ngày" (Luôn bật không lịch tắt), "Nghỉ", "Tạm nghỉ". Ví dụ: "Thứ Tư 18/11: 08:00–12:00, 13:00–17:00". Không hiện lý do lịch tắt |
| Ghi chú tham quan | Text; length 300, chỉ xem | Hiện khi xưởng đang Đang đón khách và có ghi chú | Không | Mức Should. Từ UC-25 |
| Dòng tạm ngừng | Chỉ xem | Hiện khi gian hàng Tạm ngừng | — | "Xưởng đang tạm ngừng nhận giao dịch mới" |
| Thông báo xem trước | Chỉ xem | Chỉ hiện cho chủ xưởng khi xem trước gian hàng Đang chuẩn bị (Alt-08) | — | "Bạn đang xem trước vị trí xưởng. Gian hàng chưa mở nên vị trí này chưa được hiển thị công khai trên bản đồ." |

#### Error Messages

| Code | Condition | Message | Display | Flow |
|---|---|---|---|---|
| ERR-UC26-01 | Không tải được bản đồ hoặc pin | "Không tải được bản đồ. Vui lòng thử lại." | Inline | Alt-03 |
| ERR-UC26-02 | Không tải được popup | "Không tải được thông tin xưởng. Vui lòng thử lại." | Inline | Alt-04 |

#### Business Rules

| ID | Rule | Source |
|---|---|---|
| BR-1 | Mỗi xưởng công khai (gian hàng Đang hoạt động hoặc Tạm ngừng) có đúng một pin; gian hàng Đang chuẩn bị không có pin công khai. Riêng chủ xưởng được xem trước ghim của gian hàng mình ở vị trí đã lưu (Alt-08); ghim xem trước chỉ hiện với chính Artisan, không hiện với Guest, Buyer hoặc Artisan khác, và không chỉnh vị trí tại bản đồ này (muốn sửa phải quay về UC-25). | Overview 3.2, 3.5; UC-13 BR-2, BR-4 (BRD 02); Chốt 2026-11-18 |
| BR-2 | Pin đặt đúng vị trí xưởng đã lưu ở UC-25; bấm pin mở popup thông tin vắn tắt của xưởng. | Đề bài §4.3 |
| BR-3 | Đang đón khách là trạng thái tính theo giờ đón khách thực tế (UC-25, BR-4); nhãn trong popup chỉ có ba giá trị: Đang đón khách, Không đón khách, Tạm nghỉ. Dấu riêng của pin và bộ lọc tính tại lúc tải bản đồ; dòng giờ đón khách trong popup tính lại tại lúc bấm pin. Đang đón khách độc lập với Tạm ngừng nên xưởng Tạm ngừng vẫn có thể được lọc ra. | Đề bài §3.2; Overview 3.2, 3.5; UC-13 BR-5 (BRD 02); Chốt 2026-11-18 |
| BR-4 | Popup chỉ tóm tắt: ảnh đại diện, tên, địa chỉ, dòng giờ đón khách, ghi chú tham quan (Should), liên kết Xem giờ cả tuần, nút Xem gian hàng và Chỉ đường. Phân loại, câu chuyện, chứng nhận, liên hệ và sản phẩm xem ở trang gian hàng (UC-08). | Đề bài §4.3; Overview 3.5; Nhóm đề xuất |
| BR-5 | Xem bản đồ và popup không cần đăng nhập. | Overview 3.5 |
| BR-6 | Danh sách pin, bộ lọc và popup tải bằng API, cập nhật giao diện không tải lại trang; thông tin popup chỉ tải khi bấm pin. | Đề bài §1 (tiêu chí 4); Overview mục 8 |
| BR-7 | Chỉ đường mở dịch vụ bản đồ bên ngoài ở tab mới, điểm đến là vị trí xưởng; hệ thống không tính lộ trình hay khoảng cách. | Overview mục 5 (Nhận tại xưởng: chỉ đường từ bản đồ); Nhóm đề xuất |
| BR-8 | Các pin ở gần nhau được gom thành cụm pin có số xưởng; phóng to thì tách ra. | Nhóm đề xuất |
| BR-9 | Dấu pin và bộ lọc phản ánh thay đổi ở lần tải tiếp theo, không phải chờ: xưởng mở gian hàng (UC-13), đổi vị trí hoặc lịch (UC-25), tới giờ bắt đầu hoặc kết thúc đón khách. Bản đồ đang mở không tự cập nhật khi đến giờ đổi trạng thái; riêng popup luôn tính lại trạng thái và giờ đóng, mở khi bấm pin nên dòng giờ đón khách đúng tại lúc bấm. Không có cơ chế tự làm mới, nhắc lịch hay đặt lịch tham quan. | Nhóm đề xuất |
| BR-10 | Xem trên bản đồ từ trang gian hàng đưa pin xưởng đó vào giữa màn hình và mở sẵn popup. | Overview 3.5; Nhóm đề xuất |
| BR-11 | Dòng giờ đón khách tính theo giờ Việt Nam tại lúc bấm pin, dựa trên giờ đón khách thực tế sau khi áp dụng lịch tắt (UC-25, BR-4), chỉ hiện khi chế độ khác Tắt và chỉ có ba nhãn. (1) Đang đón khách: trong giờ đón khách và không nằm trong lịch tắt; kèm "Đóng cửa lúc HH:mm" với mốc đóng kế tiếp là thời điểm sớm hơn giữa giờ kết thúc khung giờ hôm nay và giờ bắt đầu lịch tắt kế tiếp trong hôm nay (không phải giờ kết thúc lịch hằng tuần nếu lịch tắt bắt đầu sớm hơn); nếu hôm nay không còn mốc đóng nào (ví dụ Luôn bật không có lịch tắt sắp áp dụng) thì "Mở cả ngày". (2) Tạm nghỉ: đang nằm trong lịch tắt; kèm lần mở lại. (3) Không đón khách: ngoài giờ đón khách; kèm lần mở kế tiếp. Lần mở kế tiếp là thời điểm sớm nhất sau hiện tại mà xưởng thực sự đón khách, tức thuộc khung giờ đón khách và đã hết các khoảng lịch tắt liên quan, tìm trong 7 ngày tới. Hiện dạng: Không đón khách "Mở lúc HH:mm" nếu trong hôm nay; Tạm nghỉ "Mở lại HH:mm" nếu trong hôm nay; "Mở lại HH:mm ngày mai" nếu là ngày mai; "Mở lại HH:mm Thứ X" nếu trong 7 ngày tới. Không tìm được lần mở trong 7 ngày tới thì chỉ hiện nhãn, không tự tạo thời điểm mở lại. Không có nhãn "Sắp đóng cửa". | Chốt 2026-11-18 |
| BR-12 | Giờ cả tuần gồm 7 ngày liên tiếp bắt đầu từ hôm nay, hôm nay in đậm, thu gọn mặc định và mở khi Guest chọn Xem giờ cả tuần. Mỗi ngày hiện giờ đón khách thực tế theo cùng cách tính với BR-11: ngày có lịch, không có lịch tắt thì hiện khung giờ đã đặt; ngày có lịch tắt một phần thì hiện các khoảng giờ còn đón khách (ví dụ lịch 08:00–17:00, lịch tắt 12:00–13:00 hiện "08:00–12:00, 13:00–17:00"); ngày bị lịch tắt toàn bộ thời gian đón khách thì "Tạm nghỉ"; ngày không có lịch đón khách thì "Nghỉ"; Luôn bật không có lịch tắt thì "Cả ngày"; Luôn bật có lịch tắt theo giờ thì hiện các khoảng giờ còn đón khách. Đây chỉ là cách tính và hiển thị, không phải chức năng nhập nhiều khung giờ ở UC-25. Lý do lịch tắt không hiển thị công khai. | Chốt 2026-11-18 |

#### Acceptance Criteria

| ID | Given | When | Then | Covers |
|---|---|---|---|---|
| UC-26-AC-01 | Có 5 gian hàng đã mở, trong đó 2 xưởng đang trong giờ đón khách | Guest mở Bản đồ, bật lọc Đang đón khách, bỏ lọc, bấm một pin rồi chọn Xem gian hàng | Hiện 5 pin, 2 pin có dấu Đang đón khách; lọc chỉ còn 2 pin; bỏ lọc hiện lại 5 pin; popup có đủ thông tin; chuyển tới trang gian hàng của xưởng đó | Main Flow, BR-2, BR-3 |
| UC-26-AC-02 | Chưa có gian hàng nào đã mở | Guest mở Bản đồ | Bản đồ hiện, không có pin, có thông điệp "Chưa có xưởng nào trên bản đồ" | Alt-01 |
| UC-26-AC-03 | Có xưởng đã mở nhưng lúc này không xưởng nào đang đón khách | Guest bật lọc Đang đón khách | Hiện "Hiện chưa có xưởng nào đang đón khách" và nút Bỏ lọc; chọn Bỏ lọc thì hiện lại tất cả pin | Alt-02 |
| UC-26-AC-04 | Tải pin lỗi | Guest mở Bản đồ | Hiển thị ERR-UC26-01 kèm nút Thử lại; bộ lọc đang chọn giữ nguyên; chọn Thử lại thì tải lại | Alt-03 |
| UC-26-AC-05 | Tải thông tin popup lỗi | Guest bấm một pin | Popup hiện ERR-UC26-02 kèm nút Thử lại; bản đồ và các pin khác vẫn dùng được | Alt-04 |
| UC-26-AC-06 | Popup của một xưởng đang mở | Guest chọn Chỉ đường | Dịch vụ bản đồ bên ngoài mở ở tab mới với điểm đến là vị trí xưởng; trang bản đồ ở tab cũ giữ nguyên | Alt-05, BR-7 |
| UC-26-AC-07 | Gian hàng Tạm ngừng, đang trong giờ đón khách | Guest bấm pin của xưởng | Pin có mặt trên bản đồ; popup có dòng "Xưởng đang tạm ngừng nhận giao dịch mới", dòng giờ đón khách với nhãn Đang đón khách, nút Xem gian hàng và Chỉ đường; xưởng vẫn có trong kết quả lọc Đang đón khách | Alt-06, BR-1, BR-3 |
| UC-26-AC-08 | Guest đang xem trang gian hàng của xưởng A | Chọn Xem trên bản đồ | Bản đồ mở với pin xưởng A ở giữa màn hình và popup đã mở; các pin khác vẫn hiện | Alt-07, BR-10 |
| UC-26-AC-09 | Artisan đang xem trước gian hàng Đang chuẩn bị đã lưu vị trí ở UC-25; một Guest đang mở bản đồ công khai | Artisan chọn Xem trên bản đồ, rồi bấm ghim xem trước | Bản đồ mở có ghim xem trước ở vị trí đã lưu kèm thông báo "Bạn đang xem trước vị trí xưởng. Gian hàng chưa mở nên vị trí này chưa được hiển thị công khai trên bản đồ."; popup mở như khi gian hàng đã mở nhưng không có thao tác chỉnh vị trí; Guest không thấy ghim của xưởng này | Alt-08, BR-1 |
| UC-26-AC-10 | Ba xưởng nằm sát nhau | Guest thu nhỏ bản đồ, rồi bấm cụm pin | Hiện một cụm pin có số 3; bấm cụm thì bản đồ phóng tới và tách thành 3 pin | Alt-09, BR-8 |
| UC-26-AC-11 | Gian hàng Đang chuẩn bị, hoặc đã có vị trí nhưng chưa mở | Guest mở bản đồ; sau đó chủ xưởng mở gian hàng (UC-13) và Guest mở lại bản đồ | Lúc đầu không có pin; sau khi gian hàng được mở thì pin hiện | BR-1, BR-9 |
| UC-26-AC-12 | Xưởng đổi vị trí ở UC-25 | Guest mở lại bản đồ | Pin ở vị trí mới, không còn ở vị trí cũ | BR-2, BR-9 |
| UC-26-AC-13 | Xưởng Theo lịch 08:00 đến 17:00 hôm nay và cả ngày mai, không có lịch tắt, có ghi chú tham quan | Guest bấm pin lúc 10:00, rồi lúc 19:00 | Lúc 10:00 popup có "Đang đón khách · Đóng cửa lúc 17:00" và ghi chú tham quan; lúc 19:00 popup có "Không đón khách · Mở lại 08:00 ngày mai", không có ghi chú; cả hai không hiện phân loại | BR-3, BR-4, BR-11 |
| UC-26-AC-14 | Guest chưa đăng nhập | Mở Bản đồ và bấm pin | Xem được, không bị chuyển sang đăng nhập | BR-5 |
| UC-26-AC-15 | Guest đang xem bản đồ | Bật, tắt lọc; bấm pin | Danh sách pin và popup cập nhật mà trang không tải lại; thông tin popup chỉ tải khi bấm pin | BR-6 |
| UC-26-AC-16 | Bản đồ đang mở lúc 16:59, xưởng đón khách tới 17:00 | Đến 17:01 mà Guest chưa làm mới, Guest bấm pin xưởng đó; sau đó làm mới bản đồ | Trước khi làm mới, dấu pin chưa tự đổi nhưng popup hiện "Không đón khách" theo giờ lúc bấm; sau khi làm mới, xưởng không còn dấu Đang đón khách và không còn trong kết quả lọc | BR-3, BR-9 |
| UC-26-AC-17 | Xưởng đổi chế độ sang Tắt, hoặc đang nằm trong lịch tắt ở UC-25 | Guest mở lại bản đồ | Pin xưởng đó mất dấu Đang đón khách và không còn trong kết quả lọc | BR-3, BR-9 |
| UC-26-AC-18 | Xưởng Theo lịch 08:00 đến 17:00, hôm nay là Thứ Tư, Chủ nhật tắt, Thứ Hai đến Thứ Sáu có lịch, không có lịch tắt | Guest bấm pin lúc 07:00, 10:00, 16:30, 17:00 và 17:01 Thứ Tư; rồi lúc 17:30 Thứ Sáu | 07:00 "Không đón khách · Mở lúc 08:00"; 10:00 "Đang đón khách · Đóng cửa lúc 17:00"; 16:30 và 17:00 vẫn "Đang đón khách · Đóng cửa lúc 17:00"; 17:01 "Không đón khách · Mở lại 08:00 ngày mai"; 17:30 Thứ Sáu thì lần mở kế tiếp là Thứ Hai nên hiện "Không đón khách · Mở lại 08:00 Thứ Hai" nếu Thứ Bảy, Chủ nhật không có lịch; không có trường hợp nào hiện "Sắp đóng cửa" | BR-11 |
| UC-26-AC-19 | Xưởng Luôn bật không có lịch tắt | Guest bấm pin | "Đang đón khách · Mở cả ngày"; Giờ cả tuần ghi "Cả ngày" cho 7 dòng | BR-11, BR-12 |
| UC-26-AC-20 | Xưởng Theo lịch đang nằm trong lịch tắt cả ngày hôm nay, lý do "Nghỉ lễ", ngày mai có lịch 08:00 | Guest bấm pin, rồi chọn Xem giờ cả tuần | Dòng giờ "Tạm nghỉ · Mở lại 08:00 ngày mai"; Giờ cả tuần ghi "Tạm nghỉ" cho hôm nay; không hiện chữ "Nghỉ lễ" ở đâu trong popup | Alt-10, BR-11, BR-12 |
| UC-26-AC-21 | Xưởng Theo lịch 08:00 đến 17:00 Thứ Hai đến Thứ Sáu, Chủ nhật tắt | Guest bấm pin, chọn Xem giờ cả tuần, rồi Thu gọn | Hiện 7 dòng liên tiếp từ hôm nay, hôm nay in đậm, mỗi dòng ghi khung giờ (ví dụ "08:00–17:00"), ngày không có lịch ghi "Nghỉ"; Thu gọn thì popup về dạng tóm tắt | Alt-10, BR-12 |
| UC-26-AC-22 | Xưởng chế độ Tắt | Guest bấm pin | Popup không có dòng giờ đón khách, không có Xem giờ cả tuần, không có ghi chú tham quan; vẫn có Xem gian hàng và Chỉ đường | BR-4, BR-11 |
| UC-26-AC-23 | Hôm nay là Thứ Tư 18/11; xưởng Theo lịch 08:00 đến 17:00, có lịch tắt 12:00 đến 13:00 hôm nay, ngày mai có lịch 08:00 và không có lịch tắt | Guest mở bản đồ và bấm pin lúc 07:00, 10:00, 11:59, 12:00, 12:30, 13:00, 14:00, 17:00 và 17:01; rồi xem Giờ cả tuần | 07:00 "Không đón khách · Mở lúc 08:00"; 10:00 và 11:59 "Đang đón khách · Đóng cửa lúc 12:00"; 12:00 và 12:30 "Tạm nghỉ · Mở lại 13:00"; 13:00, 14:00 và 17:00 "Đang đón khách · Đóng cửa lúc 17:00"; 17:01 "Không đón khách · Mở lại 08:00 ngày mai"; dấu pin và bộ lọc lúc tải bản đồ: có dấu Đang đón khách lúc 10:00 và 14:00, không có lúc 12:30; Giờ cả tuần hôm nay ghi "Thứ Tư 18/11: 08:00–12:00, 13:00–17:00" | Alt-10, BR-3, BR-11, BR-12 |
| UC-26-AC-24 | Xưởng Luôn bật, có lịch tắt 12:00 đến 13:00 hôm nay | Guest bấm pin lúc 10:00, 12:30 và 14:00; rồi xem Giờ cả tuần | 10:00 "Đang đón khách · Đóng cửa lúc 12:00"; 12:30 "Tạm nghỉ · Mở lại 13:00"; 14:00 "Đang đón khách · Mở cả ngày"; Giờ cả tuần hôm nay ghi "00:00–12:00, 13:00–24:00" | BR-11, BR-12 |
| UC-26-AC-25 | Xưởng Theo lịch 08:00 đến 12:00 hôm nay và ngày mai, có lịch tắt hôm nay 11:00 đến 13:00 | Guest bấm pin lúc 11:30 | "Tạm nghỉ · Mở lại 08:00 ngày mai" (mốc mở lại không phải 13:00 vì sau 12:00 xưởng không còn trong giờ đón khách); lúc 10:00 hiện "Đang đón khách · Đóng cửa lúc 11:00" | BR-11 |
| UC-26-AC-26 | Xưởng Theo lịch có lịch tắt cả ngày liên tục trong 10 ngày tới | Guest bấm pin hôm nay | Hiện "Tạm nghỉ" mà không có mốc mở lại vì không tìm được lần mở trong 7 ngày tới; Giờ cả tuần ghi "Tạm nghỉ" cho cả 7 ngày | BR-11, BR-12 |
| UC-26-AC-27 | Artisan có gian hàng Đang chuẩn bị chưa lưu vị trí | Chọn Xem trên bản đồ ở chế độ xem trước | Bản đồ mở, không có ghim xem trước; hiện "Gian hàng chưa có vị trí. Vui lòng đặt vị trí ở mục Vị trí và lịch đón khách." | Alt-08, BR-1 |

## IV. Open Questions

Không còn câu hỏi mở.
