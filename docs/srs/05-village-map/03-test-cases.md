---
title: "05 — Bản đồ làng nghề — Ca nghiệm thu"
code: SRS-05
type: srs
status: official
version: 0.3.0
date: 2026-11-18
---

# 05 — Bản đồ làng nghề — Ca nghiệm thu

[BRD 0.1.0](../../brd/05-village-map/usecase.md) · [ERD](../../design/erd.md) · [Phụ thuộc liên nhóm](../README.md) · [Kiến trúc và giao diện](01-architecture.md) · [Đặc tả chức năng](02-feature-description.md)

Các ca dưới đây truy vết tới AC của BRD. Chạy qua giao diện hoặc API tương ứng khi có ứng dụng.

## UC-25 — Quản lý vị trí và lịch đón khách

| TC | AC và quy tắc | Chuẩn bị | Thao tác | Kết quả mong đợi |
|---|---|---|---|---|
| TC-25.1 | UC-25-AC-01 · Main Flow, BR-3, BR-4 | Artisan đã đăng nhập, gian hàng Đang hoạt động chưa có vị trí, địa chỉ đã nhập ở UC-07 | Mở mục Vị trí và lịch đón khách, kiểm tra ghim gợi ý, chọn Theo lịch, bật Thứ Hai đến Thứ Sáu 08:00 đến 17:00, chọn Lưu | Ghim gợi ý nằm theo địa chỉ kèm dòng yêu cầu kiểm tra; sau Lưu vị trí và lịch được lưu; vĩ độ, kinh độ tự điền; pin hiện trên bản đồ công khai; xưởng hiện Đang đón khách trong giờ lịch |
| TC-25.2 | UC-25-AC-02 · Main Flow, BR-3 | Ghim gợi ý lệch vị trí thật của xưởng | Artisan kéo ghim đến đúng chỗ rồi Lưu | Vị trí lưu là điểm Artisan chọn, không phải điểm gợi ý |
| TC-25.3 | UC-25-AC-03 · BR-3 | Artisan mở mục, thấy ghim gợi ý | Đóng trang mà không chọn Lưu | Gian hàng vẫn chưa có vị trí; không có pin công khai |
| TC-25.4 | UC-25-AC-04 · Alt-01 | Artisan chưa có ghim (không tìm được vị trí theo địa chỉ) hoặc ghi chú dài 301 ký tự | Chọn Lưu | Hiển thị ERR-UC25-01 tại trường sai; dữ liệu đã nhập được giữ; không lưu |
| TC-25.5 | UC-25-AC-05 · Alt-02, BR-9 | Chế độ Theo lịch | Không bật ngày nào; hoặc đặt giờ kết thúc 08:00 cho giờ bắt đầu 17:00; hoặc thêm lịch tắt bắt đầu hôm qua; rồi chọn Lưu | Hiển thị ERR-UC25-04 tại dòng sai; không lưu thiết lập nào |
| TC-25.6 | UC-25-AC-06 · Alt-03, BR-7 | Gian hàng Đang hoạt động đã có vị trí, không có yêu cầu đổi địa chỉ đang chờ | Kéo ghim sang điểm khác và Lưu | Pin trên bản đồ công khai chuyển sang vị trí mới ngay, không còn pin ở vị trí cũ |
| TC-25.7 | UC-25-AC-07 · Alt-11, BR-8 | Gian hàng Đang hoạt động đã có vị trí; Artisan xin đổi địa chỉ, kiểm tra ghim gợi ý theo địa chỉ mới, kéo chỉnh và Lưu | Guest xem bản đồ trong lúc chờ duyệt; Admin mở yêu cầu rồi duyệt; Guest xem lại bản đồ | Admin thấy địa chỉ và vị trí hiện tại cạnh địa chỉ và vị trí đề nghị; trong lúc chờ pin và địa chỉ giữ như cũ; sau khi duyệt pin ở vị trí đề nghị cùng lúc với địa chỉ mới, không còn pin ở vị trí cũ |
| TC-25.8 | UC-25-AC-08 · Alt-04 | Dịch vụ tìm địa chỉ lỗi hoặc không thấy địa chỉ | Artisan mở mục khi chưa có vị trí | Hiển thị ERR-UC25-05; không có ghim gợi ý; Artisan bấm lên bản đồ chọn được vị trí |
| TC-25.9 | UC-25-AC-09 · Alt-05, BR-4, BR-10 | Xưởng đang Theo lịch | Artisan chọn Luôn bật, rồi chọn Tắt, mỗi lần chọn Lưu | Luôn bật thì không còn Lịch hằng tuần, xưởng đón khách 24/7, Đang đón khách mọi lúc trừ lịch tắt; Tắt thì xưởng Không đón khách, popup không hiện ghi chú và không có dòng giờ đón khách; nội dung lịch và ghi chú cũ vẫn còn khi chọn lại chế độ |
| TC-25.10 | UC-25-AC-10 · Alt-07, BR-4 | Xưởng Theo lịch Thứ Hai 08:00 đến 17:00 | Guest mở bản đồ lúc 07:59, 08:00, 17:00 và 17:01 thứ Hai (giờ Việt Nam) | 07:59 Không đón khách; 08:00 và 17:00 Đang đón khách; 17:01 Không đón khách |
| TC-25.11 | UC-25-AC-11 · Alt-07, BR-4, BR-9 | Xưởng Theo lịch, có lịch tắt cả ngày thứ Hai tới | Guest xem bản đồ vào thứ Hai đó, trong và ngoài khung giờ lịch | Xưởng Tạm nghỉ cả ngày thứ Hai vì lịch tắt ưu tiên hơn lịch hằng tuần; thứ Ba theo lịch bình thường; sau khi lịch tắt qua, mục tự ẩn khỏi danh sách |
| TC-25.12 | UC-25-AC-12 · Alt-06 | Có lịch tắt đang áp dụng | Artisan xóa mục đó, xác nhận và Lưu | Xưởng đón khách trở lại theo lịch từ lúc Lưu |
| TC-25.13 | UC-25-AC-13 · Alt-07, BR-4, BR-9 | Xưởng Luôn bật, có lịch tắt 12:00 đến 13:00 mỗi ngày trong khoảng 3 ngày | Guest xem bản đồ lúc 11:59, 12:00, 12:30, 13:00 và 14:00 trong khoảng đó | 11:59 Đang đón khách; 12:00 và 12:30 Tạm nghỉ; 13:00 và 14:00 Đang đón khách |
| TC-25.14 | UC-25-AC-14 · Alt-08, BR-2, BR-6 | Gian hàng Đang chuẩn bị | Đặt vị trí và lịch rồi Lưu | Lưu thành công, hiện dòng "Gian hàng chưa mở nên xưởng chưa hiện trên bản đồ công khai"; Guest không thấy pin; mục Vị trí bản đồ trong checklist (UC-13) đạt; khi gian hàng được mở thì pin hiện |
| TC-25.15 | UC-25-AC-15 · Alt-09 | Bản đồ không tải được | Artisan mở mục | Hiển thị ERR-UC25-02 kèm nút Thử lại |
| TC-25.16 | UC-25-AC-16 · Alt-10, BR-1 | Tài khoản Buyer chưa có quyền bán, tài khoản Admin, hoặc Artisan khác | Mở hoặc gửi thay đổi của gian hàng này | Hiển thị ERR-UC25-03; thiết lập không đổi |
| TC-25.17 | UC-25-AC-17 · BR-2 | Gian hàng chưa có vị trí, các mục khác của checklist đủ | Artisan mở gian hàng (UC-13) | Bị từ chối vì thiếu mục Vị trí bản đồ; sau khi lưu vị trí thì mục này đạt |
| TC-25.18 | UC-25-AC-18 · BR-5 | Gian hàng đang Tạm ngừng | Đặt chế độ Luôn bật và Lưu | Lưu thành công; pin vẫn hiện và xưởng Đang đón khách |
| TC-25.19 | UC-25-AC-19 · BR-1 | Gian hàng đã có vị trí | Artisan tìm thao tác xóa vị trí | Không có thao tác xóa, chỉ có đổi vị trí |
| TC-25.20 | UC-25-AC-20 · BR-9 | Artisan nhập 21 mục lịch tắt; hoặc nghỉ ngày lễ | Lưu | Mục thứ 21 bị ERR-UC25-04; hệ thống không tự thêm ngày lễ |
| TC-25.21 | UC-25-AC-21 · Main Flow bước 10, BR-4, BR-11 | Xưởng Theo lịch Thứ Hai đến Thứ Sáu 08:00 đến 17:00 | Artisan lưu lịch rồi xem đầu form lúc 10:00 ngày thường; sau đó thêm lịch tắt 12:00 đến 13:00 hôm nay và Lưu | Đầu form hiện "Đang đón khách · Đóng cửa lúc 17:00", đúng dòng Guest thấy ở popup; sau khi thêm lịch tắt hiện "Đang đón khách · Đóng cửa lúc 12:00" |
| TC-25.22 | UC-25-AC-22 · Alt-11, BR-8 | Yêu cầu đổi địa chỉ kèm vị trí đề nghị đang chờ duyệt | Admin từ chối yêu cầu kèm lý do | Địa chỉ và pin giữ nguyên như cũ; vị trí đề nghị không được áp dụng; vị trí được mở khóa |
| TC-25.23 | UC-25-AC-23 · Alt-12, Alt-04, BR-8 | Artisan đã đổi địa chỉ, không tìm được vị trí theo địa chỉ mới và chưa bấm chọn trên bản đồ | Chọn Lưu | Hiển thị ERR-UC25-06; không tạo yêu cầu chờ duyệt; dữ liệu đã nhập được giữ; bấm chọn vị trí rồi Lưu thì yêu cầu được tạo |
| TC-25.24 | UC-25-AC-24 · Alt-03, Alt-11, BR-8, BR-12 | Gian hàng đã có vị trí; có yêu cầu đổi địa chỉ kèm vị trí đề nghị đang chờ Admin duyệt | Artisan mở mục Vị trí và lịch đón khách, thử kéo ghim hoặc chọn Đặt theo địa chỉ; thử gửi yêu cầu đổi địa chỉ thứ hai ở UC-07; đổi chế độ và lịch đón khách rồi Lưu; sau đó Admin duyệt yêu cầu | Ghim và nút Đặt theo địa chỉ bị khóa kèm dòng "Yêu cầu đổi địa chỉ đang chờ BQL duyệt...", nếu vẫn gửi lên thì hiển thị ERR-UC25-07; yêu cầu thứ hai bị từ chối bằng ERR-UC07-03 và không được tạo; chế độ và lịch lưu bình thường; địa chỉ và vị trí đang lưu giữ nguyên cho tới khi duyệt; sau khi duyệt địa chỉ mới và vị trí đề nghị được áp dụng đồng thời |
| TC-25.25 | UC-25-AC-25 · Alt-02, BR-4, BR-9 | Xưởng Theo lịch Thứ Hai đến Thứ Sáu 08:00 đến 17:00, Artisan nhập hai mục lịch tắt trùng nhau Thứ Hai 12:00 đến 14:00 và 13:00 đến 15:00 | Lưu; Guest bấm pin lúc 14:30 Thứ Hai và xem Giờ cả tuần | Lưu thành công, không báo lỗi; hệ thống hợp nhất thành một khoảng nghỉ 12:00 đến 15:00; 14:30 hiện "Tạm nghỉ · Mở lại 15:00"; Giờ cả tuần của Thứ Hai ghi "08:00–12:00, 15:00–17:00" |

## UC-26 — Khám phá xưởng trên bản đồ

| TC | AC và quy tắc | Chuẩn bị | Thao tác | Kết quả mong đợi |
|---|---|---|---|---|
| TC-26.1 | UC-26-AC-01 · Main Flow, BR-2, BR-3 | Có 5 gian hàng đã mở, trong đó 2 xưởng đang trong giờ đón khách | Guest mở Bản đồ, bật lọc Đang đón khách, bỏ lọc, bấm một pin rồi chọn Xem gian hàng | Hiện 5 pin, 2 pin có dấu Đang đón khách; lọc chỉ còn 2 pin; bỏ lọc hiện lại 5 pin; popup có đủ thông tin; chuyển tới trang gian hàng của xưởng đó |
| TC-26.2 | UC-26-AC-02 · Alt-01 | Chưa có gian hàng nào đã mở | Guest mở Bản đồ | Bản đồ hiện, không có pin, có thông điệp "Chưa có xưởng nào trên bản đồ" |
| TC-26.3 | UC-26-AC-03 · Alt-02 | Có xưởng đã mở nhưng lúc này không xưởng nào đang đón khách | Guest bật lọc Đang đón khách | Hiện "Hiện chưa có xưởng nào đang đón khách" và nút Bỏ lọc; chọn Bỏ lọc thì hiện lại tất cả pin |
| TC-26.4 | UC-26-AC-04 · Alt-03 | Tải pin lỗi | Guest mở Bản đồ | Hiển thị ERR-UC26-01 kèm nút Thử lại; bộ lọc đang chọn giữ nguyên; chọn Thử lại thì tải lại |
| TC-26.5 | UC-26-AC-05 · Alt-04 | Tải thông tin popup lỗi | Guest bấm một pin | Popup hiện ERR-UC26-02 kèm nút Thử lại; bản đồ và các pin khác vẫn dùng được |
| TC-26.6 | UC-26-AC-06 · Alt-05, BR-7 | Popup của một xưởng đang mở | Guest chọn Chỉ đường | Dịch vụ bản đồ bên ngoài mở ở tab mới với điểm đến là vị trí xưởng; trang bản đồ ở tab cũ giữ nguyên |
| TC-26.7 | UC-26-AC-07 · Alt-06, BR-1, BR-3 | Gian hàng Tạm ngừng, đang trong giờ đón khách | Guest bấm pin của xưởng | Pin có mặt trên bản đồ; popup có dòng "Xưởng đang tạm ngừng nhận giao dịch mới", dòng giờ đón khách với nhãn Đang đón khách, nút Xem gian hàng và Chỉ đường; xưởng vẫn có trong kết quả lọc Đang đón khách |
| TC-26.8 | UC-26-AC-08 · Alt-07, BR-10 | Guest đang xem trang gian hàng của xưởng A | Chọn Xem trên bản đồ | Bản đồ mở với pin xưởng A ở giữa màn hình và popup đã mở; các pin khác vẫn hiện |
| TC-26.9 | UC-26-AC-09 · Alt-08, BR-1 | Artisan đang xem trước gian hàng Đang chuẩn bị đã lưu vị trí ở UC-25; một Guest đang mở bản đồ công khai | Artisan chọn Xem trên bản đồ, rồi bấm ghim xem trước | Bản đồ mở có ghim xem trước ở vị trí đã lưu kèm thông báo "Bạn đang xem trước vị trí xưởng. Gian hàng chưa mở nên vị trí này chưa được hiển thị công khai trên bản đồ."; popup mở như khi gian hàng đã mở nhưng không có thao tác chỉnh vị trí; Guest không thấy ghim của xưởng này |
| TC-26.10 | UC-26-AC-10 · Alt-09, BR-8 | Ba xưởng nằm sát nhau | Guest thu nhỏ bản đồ, rồi bấm cụm pin | Hiện một cụm pin có số 3; bấm cụm thì bản đồ phóng tới và tách thành 3 pin |
| TC-26.11 | UC-26-AC-11 · BR-1, BR-9 | Gian hàng Đang chuẩn bị, hoặc đã có vị trí nhưng chưa mở | Guest mở bản đồ; sau đó chủ xưởng mở gian hàng (UC-13) và Guest mở lại bản đồ | Lúc đầu không có pin; sau khi gian hàng được mở thì pin hiện |
| TC-26.12 | UC-26-AC-12 · BR-2, BR-9 | Xưởng đổi vị trí ở UC-25 | Guest mở lại bản đồ | Pin ở vị trí mới, không còn ở vị trí cũ |
| TC-26.13 | UC-26-AC-13 · BR-3, BR-4, BR-11 | Xưởng Theo lịch 08:00 đến 17:00 hôm nay và cả ngày mai, không có lịch tắt, có ghi chú tham quan | Guest bấm pin lúc 10:00, rồi lúc 19:00 | Lúc 10:00 popup có "Đang đón khách · Đóng cửa lúc 17:00" và ghi chú tham quan; lúc 19:00 popup có "Không đón khách · Mở lại 08:00 ngày mai", không có ghi chú; cả hai không hiện phân loại |
| TC-26.14 | UC-26-AC-14 · BR-5 | Guest chưa đăng nhập | Mở Bản đồ và bấm pin | Xem được, không bị chuyển sang đăng nhập |
| TC-26.15 | UC-26-AC-15 · BR-6 | Guest đang xem bản đồ | Bật, tắt lọc; bấm pin | Danh sách pin và popup cập nhật mà trang không tải lại; thông tin popup chỉ tải khi bấm pin |
| TC-26.16 | UC-26-AC-16 · BR-3, BR-9 | Bản đồ đang mở lúc 16:59, xưởng đón khách tới 17:00 | Đến 17:01 mà Guest chưa làm mới, Guest bấm pin xưởng đó; sau đó làm mới bản đồ | Trước khi làm mới, dấu pin chưa tự đổi nhưng popup hiện "Không đón khách" theo giờ lúc bấm; sau khi làm mới, xưởng không còn dấu Đang đón khách và không còn trong kết quả lọc |
| TC-26.17 | UC-26-AC-17 · BR-3, BR-9 | Xưởng đổi chế độ sang Tắt, hoặc đang nằm trong lịch tắt ở UC-25 | Guest mở lại bản đồ | Pin xưởng đó mất dấu Đang đón khách và không còn trong kết quả lọc |
| TC-26.18 | UC-26-AC-18 · BR-11 | Xưởng Theo lịch 08:00 đến 17:00, hôm nay là Thứ Tư, Chủ nhật tắt, Thứ Hai đến Thứ Sáu có lịch, không có lịch tắt | Guest bấm pin lúc 07:00, 10:00, 16:30, 17:00 và 17:01 Thứ Tư; rồi lúc 17:30 Thứ Sáu | 07:00 "Không đón khách · Mở lúc 08:00"; 10:00 "Đang đón khách · Đóng cửa lúc 17:00"; 16:30 và 17:00 vẫn "Đang đón khách · Đóng cửa lúc 17:00"; 17:01 "Không đón khách · Mở lại 08:00 ngày mai"; 17:30 Thứ Sáu thì lần mở kế tiếp là Thứ Hai nên hiện "Không đón khách · Mở lại 08:00 Thứ Hai" nếu Thứ Bảy, Chủ nhật không có lịch; không có trường hợp nào hiện "Sắp đóng cửa" |
| TC-26.19 | UC-26-AC-19 · BR-11, BR-12 | Xưởng Luôn bật không có lịch tắt | Guest bấm pin | "Đang đón khách · Mở cả ngày"; Giờ cả tuần ghi "Cả ngày" cho 7 dòng |
| TC-26.20 | UC-26-AC-20 · Alt-10, BR-11, BR-12 | Xưởng Theo lịch đang nằm trong lịch tắt cả ngày hôm nay, lý do "Nghỉ lễ", ngày mai có lịch 08:00 | Guest bấm pin, rồi chọn Xem giờ cả tuần | Dòng giờ "Tạm nghỉ · Mở lại 08:00 ngày mai"; Giờ cả tuần ghi "Tạm nghỉ" cho hôm nay; không hiện chữ "Nghỉ lễ" ở đâu trong popup |
| TC-26.21 | UC-26-AC-21 · Alt-10, BR-12 | Xưởng Theo lịch 08:00 đến 17:00 Thứ Hai đến Thứ Sáu, Chủ nhật tắt | Guest bấm pin, chọn Xem giờ cả tuần, rồi Thu gọn | Hiện 7 dòng liên tiếp từ hôm nay, hôm nay in đậm, mỗi dòng ghi khung giờ (ví dụ "08:00–17:00"), ngày không có lịch ghi "Nghỉ"; Thu gọn thì popup về dạng tóm tắt |
| TC-26.22 | UC-26-AC-22 · BR-4, BR-11 | Xưởng chế độ Tắt | Guest bấm pin | Popup không có dòng giờ đón khách, không có Xem giờ cả tuần, không có ghi chú tham quan; vẫn có Xem gian hàng và Chỉ đường |
| TC-26.23 | UC-26-AC-23 · Alt-10, BR-3, BR-11, BR-12 | Hôm nay là Thứ Tư 18/11; xưởng Theo lịch 08:00 đến 17:00, có lịch tắt 12:00 đến 13:00 hôm nay, ngày mai có lịch 08:00 và không có lịch tắt | Guest mở bản đồ và bấm pin lúc 07:00, 10:00, 11:59, 12:00, 12:30, 13:00, 14:00, 17:00 và 17:01; rồi xem Giờ cả tuần | 07:00 "Không đón khách · Mở lúc 08:00"; 10:00 và 11:59 "Đang đón khách · Đóng cửa lúc 12:00"; 12:00 và 12:30 "Tạm nghỉ · Mở lại 13:00"; 13:00, 14:00 và 17:00 "Đang đón khách · Đóng cửa lúc 17:00"; 17:01 "Không đón khách · Mở lại 08:00 ngày mai"; dấu pin và bộ lọc lúc tải bản đồ: có dấu Đang đón khách lúc 10:00 và 14:00, không có lúc 12:30; Giờ cả tuần hôm nay ghi "Thứ Tư 18/11: 08:00–12:00, 13:00–17:00" |
| TC-26.24 | UC-26-AC-24 · BR-11, BR-12 | Xưởng Luôn bật, có lịch tắt 12:00 đến 13:00 hôm nay | Guest bấm pin lúc 10:00, 12:30 và 14:00; rồi xem Giờ cả tuần | 10:00 "Đang đón khách · Đóng cửa lúc 12:00"; 12:30 "Tạm nghỉ · Mở lại 13:00"; 14:00 "Đang đón khách · Mở cả ngày"; Giờ cả tuần hôm nay ghi "00:00–12:00, 13:00–24:00" |
| TC-26.25 | UC-26-AC-25 · BR-11 | Xưởng Theo lịch 08:00 đến 12:00 hôm nay và ngày mai, có lịch tắt hôm nay 11:00 đến 13:00 | Guest bấm pin lúc 11:30 | "Tạm nghỉ · Mở lại 08:00 ngày mai" (mốc mở lại không phải 13:00 vì sau 12:00 xưởng không còn trong giờ đón khách); lúc 10:00 hiện "Đang đón khách · Đóng cửa lúc 11:00" |
| TC-26.26 | UC-26-AC-26 · BR-11, BR-12 | Xưởng Theo lịch có lịch tắt cả ngày liên tục trong 10 ngày tới | Guest bấm pin hôm nay | Hiện "Tạm nghỉ" mà không có mốc mở lại vì không tìm được lần mở trong 7 ngày tới; Giờ cả tuần ghi "Tạm nghỉ" cho cả 7 ngày |
| TC-26.27 | UC-26-AC-27 · Alt-08, BR-1 | Artisan có gian hàng Đang chuẩn bị chưa lưu vị trí | Chọn Xem trên bản đồ ở chế độ xem trước | Bản đồ mở, không có ghim xem trước; hiện "Gian hàng chưa có vị trí. Vui lòng đặt vị trí ở mục Vị trí và lịch đón khách." |
