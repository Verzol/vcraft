---
title: "05 — Bản đồ làng nghề — Đặc tả chức năng"
code: SRS-05
type: srs
status: official
version: 0.3.0
date: 2026-11-18
---

# 05 — Bản đồ làng nghề — Đặc tả chức năng

[BRD 0.1.0](../../brd/05-village-map/usecase.md) · [ERD](../../design/erd.md) · [Phụ thuộc liên nhóm](../README.md) · [Kiến trúc và giao diện](01-architecture.md) · [Ca nghiệm thu](03-test-cases.md)

Giới hạn dữ liệu và mã lỗi theo phiên bản BRD trên. Các phương án đang mở trong mục Open Questions cần nhóm xác nhận.

## UC-25 — Quản lý vị trí và lịch đón khách

Artisan xác nhận vị trí xưởng trên bản đồ (hệ thống gợi ý ghim theo địa chỉ, chủ xưởng kiểm tra và kéo ghim nếu chưa đúng), đặt chế độ đón khách, lịch hằng tuần, lịch tắt và ghi chú tham quan. Trạng thái Đang đón khách của xưởng tự bật, tắt theo các thiết lập này; giờ đã đặt là nguồn của dòng giờ đón khách khách hàng thấy ở UC-26. Khi xưởng xin đổi địa chỉ (UC-07), chủ xưởng kiểm tra lại ghim cho địa chỉ mới ngay lúc gửi yêu cầu, và vị trí mới được áp dụng cùng địa chỉ khi Admin duyệt.

**Quyền và điều kiện** Đã đăng nhập và có quyền bán (hồ sơ đã được duyệt ở UC-02) Gian hàng ở bất kỳ trạng thái nào (Đang chuẩn bị, Đang hoạt động, Tạm ngừng)

Ghim đang chờ đổi địa chỉ bị khóa. Dùng lịch tuần trừ khoảng nghỉ theo phút UTC+7 cho mọi màn hình.

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

| Mã lỗi | Điều kiện trả lỗi |
|---|---|
| ERR-UC25-01 | Dữ liệu vị trí hoặc ghi chú không hợp lệ |
| ERR-UC25-02 | Không tải được bản đồ |
| ERR-UC25-03 | Không có quyền |
| ERR-UC25-04 | Lịch đón khách không hợp lệ |
| ERR-UC25-05 | Không tìm được vị trí theo địa chỉ |
| ERR-UC25-06 | Chưa có vị trí cho địa chỉ mới |
| ERR-UC25-07 | Đổi vị trí khi đang có yêu cầu đổi địa chỉ chờ duyệt |

## UC-26 — Khám phá xưởng trên bản đồ

Guest hoặc Buyer mở bản đồ làng nghề, xem pin các xưởng, lọc xưởng đang đón khách, bấm pin xem popup tóm tắt kèm dòng giờ đón khách (giống cách Google Maps hiện giờ mở cửa) và giờ cả tuần, rồi đi tới trang gian hàng hoặc chỉ đường tới xưởng. Chủ xưởng có thể xem trước ghim của gian hàng chưa mở (Alt-08).

**Quyền và điều kiện** Không yêu cầu đăng nhập (riêng xem trước ở Alt-08 cần đăng nhập Artisan)

Chỉ công khai xưởng đã mở, gồm Tạm ngừng. Nạp popup khi bấm và tính lại giờ đón khách tại thời điểm đó.

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

| Mã lỗi | Điều kiện trả lỗi |
|---|---|
| ERR-UC26-01 | Không tải được bản đồ hoặc pin |
| ERR-UC26-02 | Không tải được popup |
