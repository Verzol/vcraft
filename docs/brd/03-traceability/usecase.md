---
title: "03 — Truy xuất nguồn gốc"
group: 03-traceability
type: brd
status: draft
version: 0.1.0
date: 2026-11-18
author: Khánh Chi
---

# 03 — Truy xuất nguồn gốc

Change history: [CHANGELOG.md](CHANGELOG.md)

## I. Overview

Nhóm chức năng giúp xưởng minh bạch hóa quy trình làm ra sản phẩm. Artisan tạo lô sản xuất cho một sản phẩm hoặc một đơn đặt riêng, ghi nhật ký từng công đoạn kèm ảnh, rồi in mã QR dán lên mọi sản phẩm trong lô. Bất kỳ ai quét QR đều xem được trang nguồn gốc kể lại quy trình từ ngày đầu tiên, không cần đăng nhập. Lô đi qua ba trạng thái: Đang thực hiện, Hoàn thành, Đã xuất xưởng; càng về sau càng bị khóa chặt để nhật ký đáng tin cậy.

### Terms

| Term | Meaning |
|---|---|
| Lô | Một đợt sản xuất thuộc một sản phẩm hoặc một đơn đặt riêng, có nhật ký và một QR chung cho mọi sản phẩm trong lô. |
| Mốc | Một công đoạn trong nhật ký: ngày, tên công đoạn, mô tả, ít nhất một ảnh. |
| Trạng thái lô | Đang thực hiện (sửa tự do), Hoàn thành (khóa mốc, in QR, mở lại được), Đã xuất xưởng (khóa cứng). Khác tên trạng thái của đơn đặt riêng. |
| Hoàn thành | Xưởng xác nhận đã sản xuất xong toàn bộ số lượng của lô và nhật ký đã ghi đủ các công đoạn. Hệ thống không tự kiểm số lượng hay công đoạn, chỉ kiểm lô có ít nhất một mốc. |
| Đã xuất xưởng | Hàng của lô đã rời xưởng; xưởng tự đánh dấu, không mở lại. |
| Ghi chú đính chính | Dòng ghi chú Artisan thêm vào lô Đã xuất xưởng để đính chính sai sót, hiện trên trang nguồn gốc; không sửa mốc cũ. |
| Trang nguồn gốc | Trang công khai mở bằng QR hoặc URL của lô, hiện nhật ký và thông tin xưởng. |

## II. Scope

| Business topic | Channel | Use case |
|---|---|---|
| Tạo lô, sinh QR, chuyển trạng thái lô | Kênh người bán | UC-14 |
| Ghi nhật ký sản xuất, ghi chú đính chính | Kênh người bán | UC-15 |
| Xem, tải, in QR | Kênh người bán | UC-16 |
| Xem nguồn gốc qua QR | Khu mua hàng | UC-17 |

**Ngoài phạm vi:** Xóa lô; chuyển lô sang sản phẩm hoặc đơn khác; Admin mở khóa lô Đã xuất xưởng; QR riêng cho từng sản phẩm trong lô (cả lô dùng chung một QR); chống sao chép tem QR.

**Liên quan**

| ID | Tài liệu | Quan hệ |
|---|---|---|
| BRD-02 | [02-showcase](../02-showcase/usecase.md) | UC-09 sản phẩm (xóa sản phẩm kiểm tra lô); UC-11 liệt kê lô và dẫn tới UC-17; UC-13 gian hàng đã mở quyết định trang QR. |
| BRD-04 | [04-quotation](../04-quotation/usecase.md) | UC-22 đơn đặt riêng là đối tượng của lô; chỉ tạo lô khi đơn Đang sản xuất, tức cọc (nếu có) đã thanh toán (UC-22 BR-3); UC-24 Buyer xem nhật ký lô của đơn. |

## III. Use Cases

| Use case ID | Use case | Description |
|---|---|---|
| UC-14 | Quản lý lô sản xuất, tự sinh QR | Artisan tạo lô, sửa thông tin, hoàn thành, mở lại, xuất xưởng; hệ thống sinh QR và URL cố định |
| UC-15 | Ghi nhật ký sản xuất, ghi chú đính chính | Artisan thêm, sửa, xóa mốc trong lô đang thực hiện; thêm ghi chú đính chính cho lô đã xuất xưởng |
| UC-16 | Xem, tải, in QR | Artisan xem, tải, in tem QR của lô đã hoàn thành |
| UC-17 | Xem nguồn gốc qua QR | Mọi người quét QR để xem nhật ký sản xuất, không cần đăng nhập |

### UC-14 — Quản lý lô sản xuất, tự sinh QR

#### Detail

| Field | Content |
|---|---|
| Use case ID | UC-14 |
| Description | Artisan tạo lô cho một sản phẩm hoặc một đơn đặt riêng (lô của đơn đặt riêng gắn thêm một phân loại); hệ thống sinh QR và URL cố định. Artisan sửa thông tin lô, đánh dấu Hoàn thành, mở lại hoặc đánh dấu Đã xuất xưởng. |
| Actors | Artisan |
| Channel | Kênh người bán |
| Preconditions | • Đã đăng nhập và có quyền bán<br>• Có ít nhất một sản phẩm của xưởng mình, hoặc một đơn đặt riêng của xưởng mình đang ở trạng thái Đang sản xuất |
| Expected | **Thành công:**<br>• Lô ở trạng thái Đang thực hiện, có QR và URL cố định<br>**Thất bại:**<br>• Hiển thị lỗi, không tạo lô hoặc giữ nguyên trạng thái lô |
| Screens | • Danh sách lô (bước 1)<br>• Form tạo lô (bước 2-4)<br>• Trang lô (bước 6) |
| Notifications | Không có |

#### Main Flow

Trigger: Artisan chọn Tạo lô trong mục Lô sản xuất.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Artisan | Artisan mở mục Lô sản xuất và chọn Tạo lô. | Form tạo lô hiển thị. |
| 2 | Artisan | Artisan chọn đối tượng của lô: một sản phẩm hoặc một đơn đặt riêng của xưởng mình. | Tên sản phẩm hoặc mã đơn hiển thị trong form. |
| 3 | Artisan | Artisan nhập tên lô, số lượng, ngày bắt đầu, mô tả (nếu có). Nếu đối tượng là đơn đặt riêng, Artisan chọn thêm phân loại. | Thông tin sẵn sàng để gửi. |
| 4 | Artisan | Artisan chọn Tạo lô. | Dữ liệu được gửi lên kiểm tra. |
| 5 | Backend | Backend kiểm tra, tạo lô ở trạng thái Đang thực hiện, sinh mã lô, QR và URL cố định. | Lô có mã, QR và URL gắn với lô. |
| 6 | Frontend | Frontend mở trang lô. | Hiển thị thông tin lô, QR, URL và nhật ký trống; sẵn sàng ghi mốc (UC-15). |

Outcome: Lô ở trạng thái Đang thực hiện, có QR và URL cố định.

#### Alternative Flows

##### Alt-01 — ERR-UC14-01 — Dữ liệu không hợp lệ

- At step: 4 · Trigger: Thiếu tên lô, số lượng không phải số nguyên dương, ngày bắt đầu ở tương lai; lô của đơn đặt riêng chưa chọn phân loại.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Backend | Backend từ chối tạo lô và đánh dấu trường sai. | Hiển thị ERR-UC14-01 tại trường sai. |

Resume: Quay lại bước 3, giữ nguyên dữ liệu đã nhập.

##### Alt-02 — ERR-UC14-02 — Đối tượng không hợp lệ

- At step: 4 · Trigger: Sản phẩm hoặc đơn không thuộc xưởng mình; hoặc đơn không ở trạng thái Đang sản xuất (còn Chờ sản xuất vì chưa bắt đầu hoặc chưa đặt cọc, hoặc đã Đang giao, Đã giao, Hoàn tất, Đã hủy).

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Backend | Backend từ chối tạo lô. | Hiển thị ERR-UC14-02. |

Resume: Quay lại bước 2.

##### Alt-03 — ERR-UC14-06 — Không sinh được QR

- At step: 5 · Trigger: Việc sinh QR thất bại.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Backend | Backend vẫn tạo lô và giữ URL của lô. | Hiển thị ERR-UC14-06 kèm nút Sinh lại QR. |
| 2 | Artisan | Artisan chọn Sinh lại QR. | QR được sinh lại với cùng URL. |

Resume: Tiếp tục bước 6.

##### Alt-04 — Sửa thông tin lô

- At step: 1 · Trigger: Artisan chọn một lô Đang thực hiện và chọn Sửa.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Artisan | Artisan sửa tên lô, số lượng, ngày bắt đầu, mô tả (và phân loại nếu lô của đơn đặt riêng) rồi Lưu. | Backend kiểm tra theo ERR-UC14-01 và lưu. |
| 2 | Frontend | Frontend cập nhật trang lô. | Thông tin mới hiển thị; mã lô, QR, URL không đổi. |

Resume: Kết thúc.

##### Alt-05 — ERR-UC14-04 — Sửa thông tin lô Hoàn thành

- At step: 1 · Trigger: Artisan sửa thông tin của lô đang Hoàn thành.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Backend | Backend từ chối sửa. | Hiển thị ERR-UC14-04; gợi ý mở lại lô. |

Resume: Kết thúc.

##### Alt-06 — Đánh dấu Hoàn thành

- At step: 1 · Trigger: Artisan chọn Hoàn thành trên một lô Đang thực hiện.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Artisan | Artisan xác nhận trong hộp thoại, vốn nhắc lô đã sản xuất xong toàn bộ số lượng và sẽ khóa mốc. | Yêu cầu được gửi. |
| 2 | Backend | Backend kiểm tra lô có ít nhất một mốc và chuyển lô sang Hoàn thành. | Lô khóa mốc và thông tin; QR cho phép in, tải (UC-16). |

Resume: Kết thúc.

##### Alt-07 — ERR-UC14-03 — Lô chưa có mốc

- At step: 1 · Trigger: Artisan chọn Hoàn thành khi lô chưa có mốc nào.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Backend | Backend từ chối chuyển trạng thái. | Hiển thị ERR-UC14-03; lô giữ Đang thực hiện. |

Resume: Kết thúc.

##### Alt-08 — Mở lại lô

- At step: 1 · Trigger: Artisan chọn Mở lại trên một lô Hoàn thành.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Artisan | Artisan xác nhận trong hộp thoại cảnh báo nhật ký sẽ được sửa lại. | Yêu cầu được gửi. |
| 2 | Backend | Backend chuyển lô về Đang thực hiện và ghi nhận lô đã từng Hoàn thành. | Mốc sửa được; mốc sửa hoặc thêm từ nay hiện nhãn "đã chỉnh sửa" (UC-15). |

Resume: Kết thúc.

##### Alt-09 — Đánh dấu Đã xuất xưởng

- At step: 1 · Trigger: Artisan chọn Đã xuất xưởng trên một lô Hoàn thành.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Artisan | Artisan xác nhận trong hộp thoại, vốn nhắc hàng của lô đã rời xưởng và lô sẽ khóa cứng, không mở lại. | Yêu cầu được gửi. |
| 2 | Backend | Backend chuyển lô sang Đã xuất xưởng. | Lô khóa cứng; chỉ thêm được ghi chú đính chính (UC-15). |

Resume: Kết thúc.

##### Alt-10 — ERR-UC14-05 — Mở lại hoặc sửa lô Đã xuất xưởng

- At step: 1 · Trigger: Artisan mở lại hoặc sửa một lô Đã xuất xưởng.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Backend | Backend từ chối thao tác. | Hiển thị ERR-UC14-05; gợi ý thêm ghi chú đính chính. |

Resume: Kết thúc.

##### Alt-11 — ERR-UC14-07 — Không có quyền

- At step: 1 · Trigger: Tài khoản chưa có quyền bán, hoặc thao tác trên lô của xưởng khác.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Backend | Backend từ chối thao tác. | Hiển thị ERR-UC14-07; dữ liệu không đổi. |

Resume: Kết thúc.

##### Alt-12 — ERR-UC14-08 — Chưa có phân loại phù hợp

- At step: 3 · Trigger: Lô của đơn đặt riêng, và chưa có phân loại nào đang Hiển thị hoặc Artisan không thấy phân loại phù hợp.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Frontend | Frontend hiện ERR-UC14-08 ở ô Phân loại; Artisan không tự thêm phân loại. | Artisan chọn phân loại gần nhất, hoặc liên hệ BQL để thêm phân loại (UC-12). Nếu chưa có phân loại nào đang Hiển thị thì chưa tạo được lô. |

Resume: Quay lại bước 3; phân loại sửa lại được khi lô còn Đang thực hiện (Alt-04).

#### Data Fields

| Field | Format | Validation | Required | Ghi chú |
|---|---|---|---|---|
| Đối tượng của lô | Chọn một | Sản phẩm của xưởng mình, hoặc đơn đặt riêng của xưởng mình đang Đang sản xuất | Có | Chọn lúc tạo, không đổi sau đó |
| Tên lô | Text; length 150 | Không để trống | Có | Ví dụ: "Lô giỏ mây tháng 11". Công khai trên trang nguồn gốc của lô đặt riêng, không nhập thông tin Buyer |
| Mã lô | Text | Hệ thống sinh, duy nhất | — | Ví dụ: "LO-2026-0001". In trên tem |
| Số lượng trong lô | Number | Số nguyên lớn hơn 0 | Có | Ví dụ: 200 |
| Ngày bắt đầu | Date | Không ở tương lai | Có | |
| Mô tả lô | Text; length 1000 | — | Không | |
| Phân loại | Chọn một | Chỉ phân loại đang Hiển thị | Có điều kiện | Từ UC-12. Bắt buộc với lô của đơn đặt riêng; lô của sản phẩm lấy theo sản phẩm, không nhập. Artisan không tự thêm phân loại mới |
| Trạng thái lô | Chỉ xem | Đang thực hiện, Hoàn thành, Đã xuất xưởng | — | |
| QR và URL | Chỉ xem | URL cố định suốt vòng đời lô | — | Sinh khi tạo lô |

#### Error Messages

| Code | Condition | Message | Display | Flow |
|---|---|---|---|---|
| ERR-UC14-01 | Dữ liệu không hợp lệ | "Thông tin lô chưa hợp lệ. Vui lòng kiểm tra các trường báo lỗi." | Inline | Alt-01 |
| ERR-UC14-02 | Đối tượng không hợp lệ | "Không thể tạo lô cho sản phẩm hoặc đơn này. Đơn đặt riêng chỉ tạo lô được khi đang sản xuất." | Inline | Alt-02 |
| ERR-UC14-03 | Lô chưa có mốc khi hoàn thành | "Lô chưa có mốc sản xuất nào. Hãy thêm ít nhất một mốc trước khi hoàn thành." | Toast | Alt-07 |
| ERR-UC14-04 | Sửa thông tin lô Hoàn thành | "Lô đang ở trạng thái Hoàn thành, không sửa thông tin lô. Hãy mở lại lô để chỉnh sửa." | Toast | Alt-05 |
| ERR-UC14-05 | Mở lại hoặc sửa lô Đã xuất xưởng | "Lô đã xuất xưởng, không mở lại hoặc sửa thông tin lô. Hãy thêm ghi chú đính chính nếu cần đính chính." | Toast | Alt-10 |
| ERR-UC14-06 | Không sinh được QR | "Chưa sinh mã QR thành công. Vui lòng thử lại." | Toast | Alt-03 |
| ERR-UC14-07 | Không có quyền | "Bạn không có quyền thực hiện thao tác này." | Trang lỗi | Alt-11 |
| ERR-UC14-08 | Chưa có phân loại phù hợp cho lô đặt riêng | "Chưa có phân loại phù hợp để chọn. Vui lòng chọn phân loại gần nhất hoặc liên hệ BQL để thêm phân loại." | Inline | Alt-12 |

#### Business Rules

| ID | Rule | Source |
|---|---|---|
| BR-1 | Lô thuộc một sản phẩm hoặc một đơn đặt riêng, chọn lúc tạo và không đổi sau đó. | Overview 3.3 |
| BR-2 | Một đơn đặt riêng có thể có nhiều lô. | Chốt 2026-11-17 |
| BR-3 | Không xóa lô. | Overview 3.3 |
| BR-4 | QR và URL sinh ngay khi tạo lô; URL cố định không đổi; mọi sản phẩm cùng lô dùng chung một QR. | Đề bài §4.1 |
| BR-5 | Vòng đời lô: Đang thực hiện sang Hoàn thành; Hoàn thành về Đang thực hiện (mở lại) hoặc sang Đã xuất xưởng. | Chốt 2026-11-18 |
| BR-6 | Lô Hoàn thành khóa mốc và thông tin lô; Artisan mở lại được, có hộp thoại cảnh báo. | Chốt 2026-11-18 |
| BR-7 | Lô Đã xuất xưởng khóa cứng, không mở lại; Admin không mở khóa trong MVP1. | Chốt 2026-11-18 |
| BR-8 | Chỉ Hoàn thành được lô có ít nhất một mốc. | Nhóm đề xuất |
| BR-9 | Chỉ tạo lô cho sản phẩm hoặc đơn của xưởng mình. Đơn đặt riêng chỉ tạo lô được khi đang ở trạng thái Đang sản xuất: đơn Chờ sản xuất chưa bắt đầu (có cọc thì cọc chưa thanh toán, UC-22 BR-3); đơn đã sang Đang giao, Đã giao, Hoàn tất hoặc Đã hủy thì không thêm lô. Lô đã tạo vẫn ghi nhật ký, hoàn thành, xuất xưởng bình thường sau khi đơn đổi trạng thái. | Nhóm đề xuất (G-04) |
| BR-10 | Tạo được lô cho sản phẩm ở mọi trạng thái (Nháp, Hiển thị, Ẩn). | Nhóm đề xuất |
| BR-11 | Chỉ chủ xưởng quản lý lô của xưởng mình. | Overview mục 1 |
| BR-12 | Lô của đơn đặt riêng phải có một phân loại, chọn từ phân loại đang Hiển thị (UC-12); Artisan không tự thêm phân loại, cần phân loại mới thì liên hệ BQL. Phân loại bị ẩn sau đó vẫn hiện trên trang nguồn gốc của lô đã tạo. Lô của sản phẩm lấy phân loại theo sản phẩm. | Chốt 2026-11-18 |
| BR-13 | Hoàn thành nghĩa là xưởng xác nhận đã sản xuất xong toàn bộ số lượng của lô và nhật ký đã ghi đủ công đoạn; hệ thống không tự kiểm, chỉ kiểm lô có ít nhất một mốc (BR-8). Đã xuất xưởng nghĩa là hàng của lô đã rời xưởng, do xưởng tự đánh dấu. Hộp thoại xác nhận nhắc rõ từng nội dung. | Chốt 2026-11-18 |

#### Acceptance Criteria

| ID | Given | When | Then | Covers |
|---|---|---|---|---|
| UC-14-AC-01 | Artisan đã đăng nhập, có sản phẩm | Chọn sản phẩm, nhập tên, số lượng, ngày bắt đầu rồi Tạo lô | Lô ở Đang thực hiện; có mã lô, QR, URL; mở trang lô | Main Flow |
| UC-14-AC-02 | Để trống tên lô | Chọn Tạo lô | Hiển thị ERR-UC14-01; dữ liệu giữ nguyên; không tạo lô | Alt-01 |
| UC-14-AC-03 | Chọn đơn Đã hủy; hoặc đơn còn Chờ sản xuất vì Buyer chưa đặt cọc; hoặc đơn đã Đang giao | Chọn Tạo lô | Hiển thị ERR-UC14-02 trong cả ba trường hợp; không tạo lô | Alt-02, BR-9 |
| UC-14-AC-04 | Sinh QR thất bại | Tạo lô | Lô vẫn được tạo; hiện ERR-UC14-06 và nút Sinh lại; sinh lại thì URL không đổi | Alt-03, BR-4 |
| UC-14-AC-05 | Lô Đang thực hiện | Sửa tên lô, số lượng rồi Lưu | Thông tin mới hiển thị; mã lô, QR, URL không đổi | Alt-04 |
| UC-14-AC-06 | Lô Hoàn thành | Sửa tên lô | Hiển thị ERR-UC14-04; gợi ý mở lại lô; tên lô không đổi | Alt-05, BR-6 |
| UC-14-AC-07 | Lô Đang thực hiện có ít nhất một mốc | Chọn Hoàn thành và xác nhận | Lô Hoàn thành; mốc bị khóa; in, tải QR được | Alt-06, BR-5 |
| UC-14-AC-08 | Lô Đang thực hiện chưa có mốc | Chọn Hoàn thành | Hiển thị ERR-UC14-03; lô giữ trạng thái | Alt-07, BR-8 |
| UC-14-AC-09 | Lô Hoàn thành | Chọn Mở lại và xác nhận | Lô Đang thực hiện; mốc sửa được; mốc sửa hoặc thêm sau đó hiện nhãn "đã chỉnh sửa" | Alt-08, BR-6 |
| UC-14-AC-10 | Lô Hoàn thành | Chọn Đã xuất xưởng và xác nhận | Lô Đã xuất xưởng; khóa cứng | Alt-09, BR-7 |
| UC-14-AC-11 | Lô Đã xuất xưởng | Chọn Mở lại hoặc Sửa | Hiển thị ERR-UC14-05 gợi ý ghi chú đính chính; trạng thái không đổi | Alt-10, BR-7 |
| UC-14-AC-12 | Artisan khác | Gửi thao tác trên lô này | Hiển thị ERR-UC14-07; dữ liệu không đổi | Alt-11, BR-11 |
| UC-14-AC-13 | Đã tạo lô cho sản phẩm A | Đổi đối tượng sang sản phẩm B | Không có thao tác này; đối tượng không đổi | BR-1 |
| UC-14-AC-14 | Đơn đặt riêng X đang Đang sản xuất | Tạo hai lô cho đơn X | Cả hai lô tạo được | BR-2 |
| UC-14-AC-15 | Lô đã tạo | Tìm thao tác xóa lô | Không có chức năng xóa lô | BR-3 |
| UC-14-AC-16 | Một lô 200 sản phẩm | Kiểm tra QR của lô | Cả lô dùng chung một QR và một URL | BR-4 |
| UC-14-AC-17 | Sản phẩm đang Nháp | Tạo lô cho sản phẩm | Tạo được | BR-10 |
| UC-14-AC-18 | Đơn đặt riêng Đang sản xuất; có phân loại Hiển thị | Tạo lô, chọn phân loại "Giỏ và làn", nhập tên lô, số lượng, ngày bắt đầu | Lô tạo được và mang phân loại "Giỏ và làn"; để trống phân loại thì hiển thị ERR-UC14-01 và không tạo lô | Main Flow, BR-12 |
| UC-14-AC-19 | Lô đặt riêng đang Đang thực hiện | Sửa phân loại rồi Lưu | Phân loại mới hiển thị; mã lô, QR, URL không đổi | Alt-04, BR-12 |
| UC-14-AC-20 | Đơn đặt riêng; không có phân loại phù hợp hoặc chưa có phân loại Hiển thị nào | Tạo lô, mở ô Phân loại | Hiển thị ERR-UC14-08; không có chỗ nhập phân loại mới; có phân loại thì chọn gần nhất được, chưa có thì chưa tạo được lô | Alt-12, BR-12 |
| UC-14-AC-21 | Lô đặt riêng đã tạo; Admin Ẩn phân loại đó | Mở trang nguồn gốc của lô | Phân loại vẫn hiện trên trang nguồn gốc | BR-12 |
| UC-14-AC-22 | Lô Đang thực hiện, rồi lô Hoàn thành | Chọn Hoàn thành, rồi chọn Đã xuất xưởng | Hộp thoại Hoàn thành nhắc đã sản xuất xong toàn bộ số lượng; hộp thoại Đã xuất xưởng nhắc hàng đã rời xưởng; hệ thống chỉ chặn khi lô chưa có mốc | BR-13, BR-8 |
| UC-14-AC-23 | Lô của đơn tạo khi đơn Đang sản xuất; sau đó đơn chuyển Đang giao | Artisan thêm mốc, hoàn thành, xuất xưởng lô đó | Thao tác trên lô đã tạo vẫn làm được; chỉ không tạo được lô mới cho đơn | BR-9 |

### UC-15 — Ghi nhật ký sản xuất, ghi chú đính chính

#### Detail

| Field | Content |
|---|---|
| Use case ID | UC-15 |
| Description | Artisan thêm, sửa, xóa mốc sản xuất trong lô đang thực hiện; thêm ghi chú đính chính cho lô đã xuất xưởng. |
| Actors | Artisan |
| Channel | Kênh người bán |
| Preconditions | • Đã đăng nhập và có quyền bán<br>• Lô thuộc xưởng mình; luồng chính cần lô Đang thực hiện |
| Expected | **Thành công:**<br>• Mốc hiện trên timeline của lô theo thứ tự ngày<br>**Thất bại:**<br>• Hiển thị lỗi, giữ dữ liệu đã nhập |
| Screens | • Trang lô và timeline (bước 1, 5)<br>• Form mốc (bước 2-4) |
| Notifications | Không có |

#### Main Flow

Trigger: Artisan chọn Thêm mốc trên một lô Đang thực hiện.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Artisan | Artisan mở trang lô đang thực hiện. | Timeline hiện các mốc đã ghi. |
| 2 | Artisan | Artisan chọn Thêm mốc. | Form mốc hiển thị. |
| 3 | Artisan | Artisan chọn ngày, nhập tên công đoạn, mô tả và tải ít nhất một ảnh. | Ảnh hiển thị trong form. |
| 4 | Artisan | Artisan chọn Lưu mốc. | Backend kiểm tra dữ liệu và lưu mốc cùng thời điểm ghi. |
| 5 | Frontend | Frontend cập nhật timeline. | Mốc mới nằm đúng vị trí theo ngày. |

Outcome: Lô có thêm một mốc trong nhật ký.

#### Alternative Flows

##### Alt-01 — ERR-UC15-01 — Dữ liệu không hợp lệ

- At step: 4 · Trigger: Thiếu tên công đoạn hoặc mô tả; ngày ở tương lai hoặc trước ngày bắt đầu lô; ghi chú đính chính để trống.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Backend | Backend từ chối lưu và đánh dấu trường sai. | Hiển thị ERR-UC15-01. |

Resume: Quay lại bước 3, giữ nguyên dữ liệu đã nhập.

##### Alt-02 — ERR-UC15-02 — Thiếu ảnh

- At step: 4 · Trigger: Artisan lưu mốc khi chưa tải ảnh nào.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Frontend | Frontend chặn lưu. | Hiển thị ERR-UC15-02. |

Resume: Quay lại bước 3.

##### Alt-03 — ERR-UC15-03 — Ảnh không hợp lệ

- At step: 3 · Trigger: Ảnh sai định dạng, lớn hơn 10 MB hoặc vượt 10 ảnh.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Frontend | Frontend không nhận ảnh. | Hiển thị ERR-UC15-03. |

Resume: Chọn ảnh khác, lặp lại bước 3.

##### Alt-04 — ERR-UC15-04 — Lô Hoàn thành đã khóa mốc

- At step: 2 · Trigger: Artisan thêm, sửa hoặc xóa mốc khi lô đang Hoàn thành.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Backend | Backend từ chối thao tác. | Hiển thị ERR-UC15-04; gợi ý mở lại lô (UC-14). |

Resume: Kết thúc.

##### Alt-05 — ERR-UC15-05 — Không lưu được

- At step: 4 · Trigger: Mất kết nối hoặc lỗi khi lưu.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Frontend | Frontend giữ dữ liệu và ảnh đã chọn. | Hiển thị ERR-UC15-05 kèm nút Thử lại. |

Resume: Chọn Thử lại thì lặp bước 4.

##### Alt-06 — Sửa mốc

- At step: 1 · Trigger: Artisan chọn Sửa trên một mốc của lô Đang thực hiện.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Artisan | Artisan sửa ngày, tên công đoạn, mô tả hoặc ảnh rồi Lưu. | Backend kiểm tra theo ERR-UC15-01 đến ERR-UC15-03. |
| 2 | Backend | Backend lưu thay đổi. | Mốc cập nhật. Nếu lô đã từng Hoàn thành thì mốc hiện nhãn "đã chỉnh sửa". |

Resume: Kết thúc.

##### Alt-07 — Xóa mốc

- At step: 1 · Trigger: Artisan chọn Xóa trên một mốc của lô Đang thực hiện.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Frontend | Frontend hiện hộp xác nhận xóa. | Artisan xác nhận hoặc hủy. |
| 2 | Backend | Backend xóa mốc. | Mốc biến khỏi timeline. |

Resume: Kết thúc.

##### Alt-08 — Thêm ghi chú đính chính

- At step: 1 · Trigger: Artisan mở một lô Đã xuất xưởng và chọn Thêm ghi chú đính chính.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Artisan | Artisan nhập nội dung đính chính và chọn Đăng. | Backend kiểm tra theo ERR-UC15-01. |
| 2 | Backend | Backend lưu ghi chú cùng thời điểm ghi, không đổi mốc nào. | Ghi chú hiện trên trang nguồn gốc (UC-17) kèm ngày giờ. |

Resume: Kết thúc.

##### Alt-09 — ERR-UC15-07 — Ghi chú đính chính khi lô chưa xuất xưởng

- At step: 1 · Trigger: Artisan thêm ghi chú đính chính cho lô Đang thực hiện hoặc Hoàn thành.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Backend | Backend từ chối thêm ghi chú. | Hiển thị ERR-UC15-07; gợi ý sửa mốc trực tiếp, mở lại lô nếu lô đang Hoàn thành. |

Resume: Kết thúc.

##### Alt-10 — ERR-UC15-06 — Không có quyền

- At step: 1 · Trigger: Tài khoản chưa có quyền bán, hoặc thao tác trên lô của xưởng khác.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Backend | Backend từ chối thao tác. | Hiển thị ERR-UC15-06; dữ liệu không đổi. |

Resume: Kết thúc.

##### Alt-11 — ERR-UC15-08 — Lô Đã xuất xưởng đã khóa mốc

- At step: 2 · Trigger: Artisan thêm, sửa hoặc xóa mốc khi lô đã Đã xuất xưởng.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Backend | Backend từ chối thao tác. | Hiển thị ERR-UC15-08; gợi ý thêm ghi chú đính chính. |

Resume: Kết thúc.

#### Data Fields

| Field | Format | Validation | Required | Ghi chú |
|---|---|---|---|---|
| Ngày mốc | Date | Không ở tương lai; không trước ngày bắt đầu lô | Có | Người dùng chọn |
| Thời điểm ghi | Date-time | Hệ thống tự lưu | — | Chỉ xem, dùng sắp xếp mốc cùng ngày |
| Tên công đoạn | Text; length 100 | Không để trống | Có | Ví dụ: "Chẻ tre", "Đan thân giỏ" |
| Mô tả mốc | Text; length 1000 | Không để trống | Có | |
| Ảnh mốc | Ảnh JPG / PNG / WebP | 1–10 ảnh, mỗi ảnh ≤ 10 MB | Có | Công khai trên trang nguồn gốc |
| Nhãn "đã chỉnh sửa" | Chỉ xem | Hiện khi mốc sửa hoặc thêm sau khi lô đã từng Hoàn thành | — | |
| Ghi chú đính chính | Text; length 1000 | Không để trống; chỉ lô Đã xuất xưởng; đăng rồi không sửa, không xóa | Có điều kiện | Tự gắn ngày giờ đăng |

#### Error Messages

| Code | Condition | Message | Display | Flow |
|---|---|---|---|---|
| ERR-UC15-01 | Dữ liệu không hợp lệ | "Thông tin chưa hợp lệ. Vui lòng kiểm tra các trường báo lỗi." | Inline | Alt-01 |
| ERR-UC15-02 | Thiếu ảnh | "Mỗi mốc cần ít nhất một ảnh minh chứng." | Inline | Alt-02 |
| ERR-UC15-03 | Ảnh không hợp lệ | "Ảnh không hợp lệ. Chỉ chấp nhận JPG, PNG, WebP, tối đa 10 MB, tối đa 10 ảnh." | Inline | Alt-03 |
| ERR-UC15-04 | Thêm, sửa, xóa mốc khi lô Hoàn thành | "Lô đang ở trạng thái Hoàn thành, không thêm, sửa hoặc xóa mốc. Hãy mở lại lô để chỉnh sửa." | Toast | Alt-04 |
| ERR-UC15-05 | Không lưu được | "Chưa lưu thành công. Hệ thống vẫn giữ dữ liệu bạn nhập, vui lòng thử lại." | Toast | Alt-05 |
| ERR-UC15-06 | Không có quyền | "Bạn không có quyền thực hiện thao tác này." | Trang lỗi | Alt-10 |
| ERR-UC15-07 | Ghi chú đính chính khi lô chưa xuất xưởng | "Ghi chú đính chính chỉ dành cho lô đã xuất xưởng. Với lô này, hãy sửa trực tiếp mốc sai và mở lại lô trước nếu lô đang Hoàn thành." | Toast | Alt-09 |
| ERR-UC15-08 | Thêm, sửa, xóa mốc khi lô Đã xuất xưởng | "Lô đã xuất xưởng, không thêm, sửa hoặc xóa mốc. Hãy thêm ghi chú đính chính nếu cần đính chính." | Toast | Alt-11 |

#### Business Rules

| ID | Rule | Source |
|---|---|---|
| BR-1 | Mốc gồm ngày do người dùng chọn và thời điểm ghi do hệ thống lưu thêm. | Overview 3.3 |
| BR-2 | Mỗi mốc có ít nhất một ảnh. | Đề bài §4.1; Overview 3.3 |
| BR-3 | Lô Đang thực hiện: thêm, sửa, xóa mốc tự do; xóa có hộp xác nhận. | Chốt 2026-11-18 |
| BR-4 | Lô Hoàn thành và Đã xuất xưởng khóa mốc: không thêm, sửa, xóa. | Chốt 2026-11-18 |
| BR-5 | Mốc sửa hoặc thêm sau khi lô đã từng Hoàn thành hiện nhãn "đã chỉnh sửa". | Overview 3.3 |
| BR-6 | Ghi chú đính chính chỉ dành cho lô Đã xuất xưởng; đăng rồi không sửa, không xóa; không đổi mốc cũ. | Chốt 2026-11-18 |
| BR-7 | Timeline sắp theo ngày mốc tăng dần; cùng ngày theo thời điểm ghi. | Nhóm đề xuất |
| BR-8 | Ảnh tải lên được backend thu nhỏ để trang nguồn gốc tải nhanh. | Đề bài §4.4 |
| BR-9 | Artisan chỉ ghi nhật ký cho lô của xưởng mình. | Overview mục 1 |

#### Acceptance Criteria

| ID | Given | When | Then | Covers |
|---|---|---|---|---|
| UC-15-AC-01 | Lô Đang thực hiện | Thêm mốc đủ ngày, công đoạn, mô tả, một ảnh | Mốc hiện trên timeline đúng thứ tự ngày | Main Flow |
| UC-15-AC-02 | Để trống tên công đoạn | Chọn Lưu mốc | Hiển thị ERR-UC15-01; dữ liệu được giữ; không lưu | Alt-01 |
| UC-15-AC-03 | Chưa tải ảnh | Chọn Lưu mốc | Hiển thị ERR-UC15-02; mốc không được lưu | Alt-02, BR-2 |
| UC-15-AC-04 | Đang chọn ảnh | Chọn tệp PDF hoặc ảnh lớn hơn 10 MB | Hiển thị ERR-UC15-03; ảnh không được nhận | Alt-03 |
| UC-15-AC-05 | Lô Hoàn thành | Thêm mốc | Hiển thị ERR-UC15-04 gợi ý mở lại lô; không có mốc mới | Alt-04, BR-4 |
| UC-15-AC-06 | Mất kết nối khi lưu | Chọn Lưu mốc | Hiển thị ERR-UC15-05; dữ liệu và ảnh được giữ; Thử lại lưu được | Alt-05 |
| UC-15-AC-07 | Lô Đang thực hiện, chưa từng Hoàn thành | Sửa mô tả mốc | Mốc cập nhật, không có nhãn "đã chỉnh sửa" | Alt-06, BR-3 |
| UC-15-AC-08 | Lô đã mở lại sau Hoàn thành | Sửa một mốc, thêm một mốc | Cả hai mốc hiện nhãn "đã chỉnh sửa" | Alt-06, BR-5 |
| UC-15-AC-09 | Lô Đang thực hiện | Xóa mốc và xác nhận | Mốc biến khỏi timeline; nếu không xác nhận thì mốc giữ nguyên | Alt-07, BR-3 |
| UC-15-AC-10 | Lô Đã xuất xưởng | Thêm ghi chú đính chính | Ghi chú hiện trên trang nguồn gốc kèm ngày giờ; mốc cũ không đổi | Alt-08, BR-6 |
| UC-15-AC-11 | Lô Đang thực hiện hoặc Hoàn thành | Thêm ghi chú đính chính | Hiển thị ERR-UC15-07; không có ghi chú mới | Alt-09 |
| UC-15-AC-12 | Artisan khác | Gửi thao tác ghi nhật ký cho lô này | Hiển thị ERR-UC15-06; dữ liệu không đổi | Alt-10, BR-9 |
| UC-15-AC-13 | Ghi chú đính chính đã đăng | Tìm thao tác sửa hoặc xóa ghi chú | Không có thao tác nào | BR-6 |
| UC-15-AC-14 | Lô Đã xuất xưởng | Sửa hoặc xóa một mốc | Hiển thị ERR-UC15-08 gợi ý ghi chú đính chính; mốc không đổi | Alt-11, BR-4 |
| UC-15-AC-15 | Mốc ngày 03/11 thêm sau mốc ngày 05/11; hai mốc cùng ngày | Xem timeline | Mốc 03/11 trước mốc 05/11; mốc cùng ngày theo thời điểm ghi | BR-7, BR-1 |
| UC-15-AC-16 | Artisan tải ảnh hợp lệ | Xem trang nguồn gốc | Dùng ảnh đã thu nhỏ | BR-8 |

### UC-16 — Xem, tải, in QR

#### Detail

| Field | Content |
|---|---|
| Use case ID | UC-16 |
| Description | Artisan xem QR và URL của lô, tải ảnh QR và in tem QR để dán lên sản phẩm trong lô. |
| Actors | Artisan |
| Channel | Kênh người bán |
| Preconditions | • Đã đăng nhập và có quyền bán<br>• Lô thuộc xưởng mình; luồng chính cần lô Hoàn thành hoặc Đã xuất xưởng |
| Expected | **Thành công:**<br>• Artisan có ảnh QR hoặc bản in tem đúng số lượng cần<br>**Thất bại:**<br>• Hiển thị lỗi, không tải hoặc in |
| Screens | • Trang lô (bước 1)<br>• Hộp thoại in QR (bước 3) |
| Notifications | Không có |

#### Main Flow

Trigger: Artisan chọn mục QR trên trang lô.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Artisan | Artisan mở trang lô Hoàn thành hoặc Đã xuất xưởng. | Hiển thị QR, URL và mã lô. |
| 2 | Artisan | Artisan chọn Tải QR. | Ảnh QR (PNG) được tải về máy. |
| 3 | Artisan | Artisan chọn In QR và nhập số tem cần in. | Bản in gồm QR, tên sản phẩm hoặc mã đơn, mã lô, tên xưởng; số tem mặc định bằng số lượng của lô. |
| 4 | Artisan | Artisan chọn Sao chép link hoặc Xem trước trang công khai. | Link được sao chép, hoặc trang nguồn gốc (UC-17) mở ra. |

Outcome: Artisan có tem QR để dán lên mọi sản phẩm trong lô.

#### Alternative Flows

##### Alt-01 — ERR-UC16-01 — Lô chưa hoàn thành

- At step: 2 · Trigger: Artisan tải hoặc in QR khi lô đang Đang thực hiện.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Frontend | Frontend vô hiệu Tải QR và In QR. | Hiển thị ERR-UC16-01; vẫn xem được QR, URL và Xem trước. |

Resume: Quay lại bước 1 sau khi lô Hoàn thành.

##### Alt-02 — ERR-UC16-02 — Số tem không hợp lệ

- At step: 3 · Trigger: Số tem không phải số nguyên từ 1 đến 1000.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Frontend | Frontend chặn in. | Hiển thị ERR-UC16-02. |

Resume: Quay lại bước 3.

##### Alt-03 — ERR-UC16-03 — Không tải hoặc in được

- At step: 2 · Trigger: Việc tạo ảnh QR hoặc bản in thất bại.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Frontend | Frontend giữ nguyên trang lô. | Hiển thị ERR-UC16-03 kèm nút Thử lại. |

Resume: Chọn Thử lại thì lặp bước đang làm.

##### Alt-04 — Gian hàng chưa mở

- At step: 1 · Trigger: Gian hàng đang Đang chuẩn bị (chưa từng mở).

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Frontend | Frontend hiện cảnh báo "Gian hàng chưa mở, người quét QR chỉ thấy thông báo xưởng đang chuẩn bị". | Vẫn tải và in được; Artisan biết cần mở gian hàng (UC-13). |

Resume: Tiếp tục bước 2.

##### Alt-05 — ERR-UC16-04 — Không có quyền

- At step: 1 · Trigger: Tài khoản chưa có quyền bán, hoặc mở lô của xưởng khác.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Backend | Backend từ chối truy cập. | Hiển thị ERR-UC16-04. |

Resume: Kết thúc.

#### Data Fields

| Field | Format | Validation | Required | Ghi chú |
|---|---|---|---|---|
| QR của lô | Ảnh PNG | Chỉ tải, in khi lô Hoàn thành hoặc Đã xuất xưởng | — | Trỏ tới URL cố định của lô |
| URL của lô | Text | Chỉ xem, sao chép được | — | Không đổi suốt vòng đời lô |
| Mã lô | Text | Chỉ xem | — | In trên tem |
| Số tem cần in | Number | Số nguyên từ 1 đến 1000 | Có | Mặc định bằng số lượng của lô |

#### Error Messages

| Code | Condition | Message | Display | Flow |
|---|---|---|---|---|
| ERR-UC16-01 | Lô chưa hoàn thành | "Chưa thể tải hoặc in QR. Hãy đánh dấu lô Hoàn thành trước." | Toast | Alt-01 |
| ERR-UC16-02 | Số tem không hợp lệ | "Số tem phải là số nguyên từ 1 đến 1000." | Inline | Alt-02 |
| ERR-UC16-03 | Không tải hoặc in được | "Chưa tải hoặc in mã QR thành công. Vui lòng thử lại." | Toast | Alt-03 |
| ERR-UC16-04 | Không có quyền | "Bạn không có quyền xem lô này." | Trang lỗi | Alt-05 |

#### Business Rules

| ID | Rule | Source |
|---|---|---|
| BR-1 | QR trỏ tới URL cố định của lô; mọi tem của cùng một lô dùng cùng một QR. | Đề bài §4.1 |
| BR-2 | Chỉ tải và in QR khi lô Hoàn thành hoặc Đã xuất xưởng; lô Đang thực hiện chỉ xem QR và xem trước. | Chốt 2026-11-18 |
| BR-3 | Bản in gồm QR, tên sản phẩm hoặc mã đơn, mã lô và tên xưởng. | Nhóm đề xuất |
| BR-4 | Số tem mặc định bằng số lượng của lô; in lại không giới hạn số lần. | Nhóm đề xuất |
| BR-5 | Chỉ chủ xưởng xem, tải, in QR của lô mình. | Overview mục 1 |

#### Acceptance Criteria

| ID | Given | When | Then | Covers |
|---|---|---|---|---|
| UC-16-AC-01 | Lô Hoàn thành, số lượng 200 | Tải QR rồi In QR với số tem mặc định | Có ảnh PNG; bản in 200 tem gồm QR, tên sản phẩm, mã lô, tên xưởng | Main Flow |
| UC-16-AC-02 | Lô Đang thực hiện | Chọn Tải QR hoặc In QR | Nút bị vô hiệu, hiển thị ERR-UC16-01; vẫn xem QR và xem trước được | Alt-01, BR-2 |
| UC-16-AC-03 | Hộp thoại in | Nhập số tem 0 hoặc 1500 | Hiển thị ERR-UC16-02; không in | Alt-02 |
| UC-16-AC-04 | Tạo ảnh QR thất bại | Chọn Tải QR | Hiển thị ERR-UC16-03 và nút Thử lại | Alt-03 |
| UC-16-AC-05 | Gian hàng chưa mở, lô Hoàn thành | Mở trang lô | Có cảnh báo gian hàng chưa mở; vẫn tải và in được | Alt-04 |
| UC-16-AC-06 | Artisan khác | Mở trang lô này | Hiển thị ERR-UC16-04 | Alt-05, BR-5 |
| UC-16-AC-07 | Hai lần in cùng một lô | So sánh QR | Cả hai bản in cùng URL; không giới hạn số lần in lại | BR-1, BR-4 |
| UC-16-AC-08 | Bản in | Kiểm tra nội dung tem | Có QR, tên sản phẩm hoặc mã đơn, mã lô, tên xưởng | BR-3 |

### UC-17 — Xem nguồn gốc qua QR

#### Detail

| Field | Content |
|---|---|
| Use case ID | UC-17 |
| Description | Người dùng quét QR hoặc mở link để xem nhật ký sản xuất của lô, thông tin xưởng và đi tới sản phẩm hoặc gian hàng. |
| Actors | Guest (mọi người, kể cả Buyer và Artisan; không cần đăng nhập) |
| Channel | Khu mua hàng (ưu tiên điện thoại) |
| Preconditions | • Có link hoặc QR của lô |
| Expected | **Thành công:**<br>• Trang nguồn gốc hiện nhật ký và thông tin xưởng<br>**Thất bại:**<br>• Hiển thị thông báo phù hợp, không lộ dữ liệu ngoài phạm vi công khai |
| Screens | • Trang nguồn gốc (bước 1-5) |
| Notifications | Không có |

#### Main Flow

Trigger: Người dùng quét QR trên sản phẩm bằng điện thoại hoặc mở URL của lô.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Guest | Guest quét QR hoặc mở link của lô. | Trang nguồn gốc của đúng lô mở ra. |
| 2 | Frontend | Frontend hiển thị phần đầu trang. | Tên sản phẩm, mã lô, trạng thái lô, ngày bắt đầu, số lượng, tên xưởng. |
| 3 | Guest | Guest xem timeline và chạm vào ảnh để phóng to. | Các mốc xếp từ ngày đầu tiên, mỗi mốc có ngày, công đoạn, mô tả, ảnh. |
| 4 | Guest | Guest xem thông tin xưởng và ghi chú đính chính (nếu có). | Hiển thị phần giới thiệu xưởng; ghi chú đính chính kèm ngày giờ. |
| 5 | Guest | Guest chọn link sang sản phẩm hoặc gian hàng. | Chuyển tới UC-11 hoặc UC-08. |

Outcome: Guest nắm được câu chuyện sản xuất của lô.

#### Alternative Flows

##### Alt-01 — ERR-UC17-01 — Lô không tồn tại

- At step: 1 · Trigger: Mã hoặc URL không khớp lô nào.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Backend | Backend không tìm thấy lô. | Hiển thị ERR-UC17-01 và nút Về trang chủ. |

Resume: Kết thúc.

##### Alt-02 — ERR-UC17-02 — Gian hàng chưa từng mở

- At step: 1 · Trigger: Gian hàng của lô đang Đang chuẩn bị.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Backend | Backend không công khai nhật ký. | Hiển thị ERR-UC17-02; không lộ nội dung lô. |

Resume: Kết thúc.

##### Alt-03 — ERR-UC17-03 — Không tải được trang

- At step: 1 · Trigger: Tải trang bị lỗi.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Frontend | Frontend hiện lỗi và nút Thử lại. | Hiển thị ERR-UC17-03. |

Resume: Chọn Thử lại thì lặp bước 1.

##### Alt-04 — Lô chưa có mốc

- At step: 3 · Trigger: Lô chưa có mốc nào.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Frontend | Frontend hiện "Xưởng chưa cập nhật nhật ký sản xuất". | Timeline trống kèm thông điệp. |

Resume: Tiếp tục bước 4.

##### Alt-05 — Lô đang thực hiện

- At step: 2 · Trigger: Lô ở trạng thái Đang thực hiện.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Frontend | Frontend hiện nhãn "Lô đang thực hiện, nhật ký có thể được cập nhật thêm". | Người xem biết nhật ký chưa chốt. |

Resume: Tiếp tục bước 3.

##### Alt-06 — Lô từng được mở lại

- At step: 3 · Trigger: Lô đã từng Hoàn thành rồi được mở lại.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Frontend | Frontend hiện dòng "Nhật ký được cập nhật lại ngày {{ngay}}" và nhãn "đã chỉnh sửa" ở các mốc sửa hoặc thêm sau đó. | Người xem thấy lịch sử cập nhật. |

Resume: Tiếp tục bước 4.

##### Alt-07 — Sản phẩm ẩn hoặc xưởng tạm ngừng

- At step: 5 · Trigger: Sản phẩm của lô đang Ẩn, hoặc gian hàng đang Tạm ngừng.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Frontend | Frontend vẫn hiện toàn bộ nhật ký; nếu sản phẩm Ẩn thì thay link sản phẩm bằng "Sản phẩm hiện không bán". | Nguồn gốc vẫn xem được; không dẫn tới sản phẩm đã ẩn. |

Resume: Kết thúc.

##### Alt-08 — Lô của đơn đặt riêng

- At step: 2 · Trigger: Lô thuộc một đơn đặt riêng.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Frontend | Frontend hiện nhãn "Sản xuất theo đơn đặt riêng" cùng tên lô và phân loại của lô, thay tên sản phẩm; không hiện link sản phẩm, không hiện thông tin Buyer hay đơn. | Trang chỉ có nhật ký và thông tin xưởng. |

Resume: Tiếp tục bước 3.

##### Alt-09 — Ảnh không tải được

- At step: 3 · Trigger: Một ảnh mốc không tải được.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Frontend | Frontend hiện ảnh thay thế tại vị trí đó. | Phần còn lại của timeline không bị ảnh hưởng. |

Resume: Tiếp tục bước 3.

#### Data Fields

| Field | Format | Validation | Required | Ghi chú |
|---|---|---|---|---|
| Tên sản phẩm; hoặc nhãn "Sản xuất theo đơn đặt riêng", tên lô, phân loại | Text | Lô của đơn không hiện tên sản phẩm | Có | Phân loại lấy từ UC-14 |
| Mã lô, trạng thái lô | Text | Trạng thái: Đang thực hiện, Hoàn thành, Đã xuất xưởng | Có | |
| Ngày bắt đầu, số lượng | Date, Number | — | Có | |
| Mốc | Danh sách | Ngày, công đoạn, mô tả, ảnh; ngày tăng dần | Không | Nhãn "đã chỉnh sửa" nếu có |
| Ghi chú đính chính | Danh sách | Mới nhất cuối, kèm ngày giờ | Không | Chỉ lô Đã xuất xưởng |
| Thông tin xưởng | Text và ảnh | Tên, ảnh đại diện, link gian hàng | Có | Từ UC-07 |
| Link sản phẩm | Link | Ẩn nếu sản phẩm Ẩn hoặc lô của đơn đặt riêng | Không | Về UC-11 |

#### Error Messages

| Code | Condition | Message | Display | Flow |
|---|---|---|---|---|
| ERR-UC17-01 | Lô không tồn tại | "Không tìm thấy lô này. Vui lòng kiểm tra lại mã QR." | Trang lỗi | Alt-01 |
| ERR-UC17-02 | Gian hàng chưa từng mở | "Xưởng đang chuẩn bị. Thông tin nguồn gốc sẽ hiển thị sau khi xưởng mở gian hàng." | Trang thông báo | Alt-02 |
| ERR-UC17-03 | Không tải được trang | "Trang chưa tải xong. Vui lòng thử lại." | Inline | Alt-03 |

#### Business Rules

| ID | Rule | Source |
|---|---|---|
| BR-1 | Xem trang nguồn gốc không cần đăng nhập. | Overview mục 1 |
| BR-2 | Gian hàng đã từng mở thì xem được đầy đủ, kể cả khi sản phẩm Ẩn hoặc gian hàng Tạm ngừng; chưa từng mở thì chỉ hiện thông báo xưởng đang chuẩn bị. | Overview 3.3 |
| BR-3 | Lô của đơn đặt riêng không lộ thông tin Buyer hay đơn, và không gắn với sản phẩm mẫu tham chiếu trong yêu cầu báo giá ban đầu (UC-18). | Nhóm đề xuất; Overview 3.3 |
| BR-4 | Mốc xếp theo ngày tăng dần để kể câu chuyện từ ngày đầu tiên. | Đề bài §4.1 |
| BR-5 | Mốc sửa hoặc thêm sau khi lô đã từng Hoàn thành hiện nhãn "đã chỉnh sửa"; trang còn hiện dòng ngày lô được cập nhật lại. | Overview 3.3; Nhóm đề xuất (dòng cập nhật lại) |
| BR-6 | Ghi chú đính chính hiển thị kèm ngày giờ, không thay đổi mốc cũ. | Chốt 2026-11-18 |
| BR-7 | Trang ưu tiên điện thoại, ảnh lớn, tải từng phần. | Đề bài §1 (tiêu chí 3, 4) |
| BR-8 | URL của lô cố định, dùng mã lô, không đổi khi tên sản phẩm hay tên lô đổi. | Đề bài §1 (tiêu chí 8); Overview 3.3 |
| BR-9 | Lô Đang thực hiện vẫn xem được, kèm nhãn trạng thái. | Nhóm đề xuất (G-01) |

#### Acceptance Criteria

| ID | Given | When | Then | Covers |
|---|---|---|---|---|
| UC-17-AC-01 | Gian hàng đã mở; lô Đã xuất xưởng có ba mốc và một ghi chú đính chính | Guest chưa đăng nhập quét QR | Hiện đầu trang, timeline từ ngày đầu, thông tin xưởng, ghi chú đính chính; chọn link thì sang sản phẩm hoặc gian hàng | Main Flow |
| UC-17-AC-02 | URL lô sai | Mở link | Hiển thị ERR-UC17-01 | Alt-01 |
| UC-17-AC-03 | Gian hàng Đang chuẩn bị | Mở link của lô | Hiển thị ERR-UC17-02; không lộ nhật ký | Alt-02, BR-2 |
| UC-17-AC-04 | Tải trang lỗi | Mở link | Hiển thị ERR-UC17-03 và nút Thử lại | Alt-03 |
| UC-17-AC-05 | Lô chưa có mốc | Mở link | Hiện "Xưởng chưa cập nhật nhật ký sản xuất" | Alt-04 |
| UC-17-AC-06 | Lô Đang thực hiện | Mở link | Hiện nhãn "Lô đang thực hiện" cùng nhật ký hiện có | Alt-05, BR-9 |
| UC-17-AC-07 | Lô đã mở lại, có một mốc đã sửa | Mở link | Hiện dòng cập nhật lại; mốc sửa có nhãn "đã chỉnh sửa" | Alt-06, BR-5 |
| UC-17-AC-08 | Sản phẩm của lô Ẩn, gian hàng Tạm ngừng | Mở link | Nhật ký vẫn xem được; link sản phẩm thay bằng "Sản phẩm hiện không bán" | Alt-07, BR-2 |
| UC-17-AC-09 | Lô thuộc đơn đặt riêng, có phân loại "Giỏ và làn", yêu cầu báo giá ban đầu có gắn sản phẩm mẫu | Mở link | Hiện nhãn "Sản xuất theo đơn đặt riêng", tên lô, phân loại "Giỏ và làn"; không có tên Buyer, mã đơn, link sản phẩm, kể cả sản phẩm mẫu | Alt-08, BR-3 |
| UC-17-AC-10 | Một ảnh mốc lỗi | Mở link | Ảnh thay thế tại chỗ đó; các mốc khác hiển thị bình thường | Alt-09 |
| UC-17-AC-11 | Khách chưa đăng nhập | Mở link | Xem được, không bị chuyển sang đăng nhập | BR-1 |
| UC-17-AC-12 | Mốc nhập ngược thứ tự ngày | Mở link | Timeline xếp từ ngày đầu tiên | BR-4 |
| UC-17-AC-13 | Ghi chú đính chính đã đăng | Mở link | Ghi chú có ngày giờ; mốc cũ không đổi | BR-6 |
| UC-17-AC-14 | Điện thoại màn hình hẹp | Mở link | Bố cục vừa màn hình, ảnh tải từng phần | BR-7 |
| UC-17-AC-15 | Đổi tên sản phẩm hoặc tên lô | Mở lại URL cũ | URL vẫn mở đúng lô | BR-8 |

## IV. Open Questions

| ID | Question | Affects | Decision |
|---|---|---|---|
| G-01 | Lô Đang thực hiện có công khai trên trang QR không? Đang theo hướng: xem được, kèm nhãn trạng thái. | UC-17 BR-9; UC-11 | Open |
| G-02 | Lô đã từng mở lại có cần dòng ghi ngày "cập nhật lại" ở đầu trang ngoài nhãn từng mốc không? Đề xuất: có, để việc xóa mốc sau khi mở lại không mất dấu vết. | UC-17 BR-5, Alt-06 | Open |
| G-03 | Số tem in mặc định bằng số lượng lô, tối đa 1000, có phù hợp không? | UC-16 Data Fields | Open |
| G-04 | Lô của đơn đặt riêng tạo được khi nào? Đang theo hướng: chỉ khi đơn Đang sản xuất (cọc, nếu có, đã thanh toán); không tạo cho đơn Chờ sản xuất, và không thêm lô sau khi đơn đã sang Đang giao, Đã giao, Hoàn tất hoặc Đã hủy. Lô đã tạo vẫn xử lý tiếp bình thường. | UC-14 BR-9; UC-22 BR-3 (BRD 04) | Open |
| G-05 | Quan hệ UC-14 và UC-24 (cũ UC-23). | UC-14; UC-24 | Đã chốt 2026-11-17: UC-14 tạo lô cho sản phẩm hoặc đơn; UC-24 chỉ còn Buyer xem nhật ký lô của đơn |
| G-06 | Một đơn có nhiều lô không? | UC-14 BR-2 | Đã chốt 2026-11-17: được |
| G-07 | Có cho xóa và sửa nhật ký, mở lại lô, xử lý lô đã xuất xưởng như thế nào? | UC-14, UC-15 | Đã chốt 2026-11-18: ba trạng thái lô; Đang thực hiện sửa xóa tự do; Hoàn thành khóa nhưng mở lại được; Đã xuất xưởng khóa cứng, chỉ thêm ghi chú đính chính; Admin không mở khóa trong MVP1 |
| G-08 | Trang sản phẩm (UC-11) chỉ nên liệt kê lô còn hàng để khách không bị nhiễu; lô đã bán hết ẩn khỏi trang sản phẩm nhưng URL và QR giữ nguyên để người đã mua quét tem vẫn xem được nhật ký (phần URL giữ nguyên đã có ở UC-17 BR-2, BR-8). Hệ thống hiện chỉ theo dõi tồn kho theo sản phẩm, không theo lô. Cách xác định lô hết hàng: (a) công tắc "Hiển thị ở trang sản phẩm" ở UC-14, mặc định bật, xưởng tự tắt khi lô bán hết; (b) tự động theo tồn kho từng lô, cần theo dõi tồn kho theo lô. Đang theo hướng (a). | UC-14; UC-17 BR-2, BR-8; UC-11 (BRD 02) | Open |
| G-09 | Xưởng cần phân loại mới cho lô đặt riêng thì làm thế nào? Đang theo hướng: Artisan không tự thêm, chọn phân loại gần nhất hoặc liên hệ BQL (UC-12); có thể bổ sung chức năng gửi đề xuất phân loại mới nếu cần. | UC-14 BR-12, Alt-12; UC-09 ERR-UC09-06 | Open |
| G-10 | Tổng số lượng các lô của một đơn đặt riêng có cần khớp hoặc không vượt số lượng của đơn không? Hiện UC-14 chỉ yêu cầu số lượng mỗi lô là số nguyên dương. Đang theo hướng: không chặn, chỉ hiện tổng số lượng các lô cạnh số lượng của đơn để xưởng tự đối chiếu. | UC-14 Data Fields; UC-24 (BRD 04) | Open |
