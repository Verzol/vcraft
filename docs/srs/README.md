# SRS vCraft và các phụ thuộc liên nhóm

Bản SRS chính thức ngày 18/11/2026 dựa trên tám BRD tại commit `c70bcfe`. Mỗi nhóm quy định API, dữ liệu vào, quyền và lỗi nghiệp vụ. Cột nghiệm thu dẫn tới các ca cần chạy khi có ứng dụng. [ERD và từ điển dữ liệu](../design/erd.md)

| Nhóm | UC | Nghiệp vụ | Thiết kế | Chi tiết | Nghiệm thu |
|---|---|---|---|---|---|
| Tài khoản | 01–05 | [BRD](../brd/01-account/usecase.md) | [API và dữ liệu](01-account/01-architecture.md) | [Chức năng](01-account/02-feature-description.md) | [Test case](01-account/03-test-cases.md) |
| Triển lãm số | 06–13 | [BRD](../brd/02-showcase/usecase.md) | [API và dữ liệu](02-showcase/01-architecture.md) | [Chức năng](02-showcase/02-feature-description.md) | [Test case](02-showcase/03-test-cases.md) |
| Truy xuất nguồn gốc | 14–17 | [BRD](../brd/03-traceability/usecase.md) | [API và dữ liệu](03-traceability/01-architecture.md) | [Chức năng](03-traceability/02-feature-description.md) | [Test case](03-traceability/03-test-cases.md) |
| Đặt hàng riêng | 18–24 | [BRD](../brd/04-quotation/usecase.md) | [API và dữ liệu](04-quotation/01-architecture.md) | [Chức năng](04-quotation/02-feature-description.md) | [Test case](04-quotation/03-test-cases.md) |
| Bản đồ làng nghề | 25–26 | [BRD](../brd/05-village-map/usecase.md) | [API và dữ liệu](05-village-map/01-architecture.md) | [Chức năng](05-village-map/02-feature-description.md) | [Test case](05-village-map/03-test-cases.md) |
| Tin tức cộng đồng | 27–28 | [BRD](../brd/06-news/usecase.md) | [API và dữ liệu](06-news/01-architecture.md) | [Chức năng](06-news/02-feature-description.md) | [Test case](06-news/03-test-cases.md) |
| Trung tâm thông báo | 29 | [BRD](../brd/07-notification/usecase.md) | [API và dữ liệu](07-notification/01-architecture.md) | [Chức năng](07-notification/02-feature-description.md) | [Test case](07-notification/03-test-cases.md) |
| Mua hàng có sẵn | 30–33 | [BRD](../brd/08-order/usecase.md) | [API và dữ liệu](08-order/01-architecture.md) | [Chức năng](08-order/02-feature-description.md) | [Test case](08-order/03-test-cases.md) |

## Hợp đồng và ca kiểm tra xuyên nhóm

| Bên cung cấp → bên sử dụng | Dữ liệu và trách nhiệm | Thao tác kiểm tra → kết quả mong đợi |
|---|---|---|
| UC-01/02 → UC-07/13/29 | Duyệt hồ sơ cấp quyền bán và tạo gian hàng Đang chuẩn bị cho tài khoản mua hiện có. | Hai Admin duyệt cùng hồ sơ → một xưởng, một thông báo cho chủ hộ. Buyer vẫn mở được đơn mua cũ. |
| UC-07/25 → UC-02 → UC-08/26 | Tên/địa chỉ đang chờ tách khỏi dữ liệu công khai. Yêu cầu đổi địa chỉ giữ cả ghim đề nghị. | Gửi đổi địa chỉ rồi xem bản đồ → còn địa chỉ và ghim cũ. Thử sửa ghim trong lúc chờ → bị chặn. Duyệt → cả hai cùng đổi. |
| UC-09/25 → UC-13 → UC-08/18/30/31 | Checklist mở xưởng cần sản phẩm Hiển thị và vị trí đã lưu. Trạng thái hoạt động và lịch đón khách độc lập. | Tạm ngừng một xưởng đang trong giờ đón khách → bản đồ vẫn có pin, không nhận RFQ hay đơn mua mới. Báo giá gửi trước đó còn hiệu lực vẫn được chấp nhận theo UC-20. |
| UC-06/08/11/12 → UC-18/19 | Phân loại và trạng thái sản phẩm xác định nguồn yêu cầu và tập xưởng. Xưởng nhận được chụp tại lúc gửi. | Gửi từ sản phẩm → đúng xưởng và mẫu. Gửi chung → chọn phân loại, loại xưởng của người gửi. Sau khi gửi, ẩn sản phẩm → bản chụp yêu cầu vẫn đọc được. |
| UC-19/20 → UC-22/23/33 | Một RFQ chọn một phiên bản của một xưởng. Hạn phản hồi RFQ khác hạn hiệu lực giá. UC-23 hủy yêu cầu trước chấp nhận, UC-33 xử lý đơn sau chấp nhận. | Hai tab chọn hai giá hoặc một tab hủy → chỉ một kết quả thắng. Đơn cọc 30% vừa tạo ở Chờ sản xuất, chặn bắt đầu đến khi cổng xác nhận thu cọc. |
| UC-22 → UC-14/15 → UC-17/24 | Lô đặt riêng gắn một đơn DR. UC-24 kiểm quyền Buyer của đơn. Trang QR công khai dùng tập trường riêng. | Tạo hai lô cho một đơn đang sản xuất → Buyer thấy cả hai. Hủy đơn theo UC-33 → lô đã tạo vẫn còn. Quét QR → không lộ người mua, tiền hoặc địa chỉ giao. |
| UC-03/04/07/09 → UC-30/31 → UC-32 | Gộp giỏ Guest khi xác thực. Giá, tồn và phương thức giao lấy lại tại checkout. Đặt từng xưởng trong một giao dịch. | Hai Buyer đặt món cuối → chỉ một đơn giữ được hàng. Nếu một trong hai món cùng đơn thiếu, không món nào bị trừ. Sửa giá sau đó → đơn giữ giá lúc đặt. |
| UC-31/32 hoặc UC-20/22 → UC-33 | Dùng chung xử lý tiền qua cổng sandbox. Áp quy tắc hủy, trả tồn, hoàn tiền hoặc giữ cọc theo loại đơn và người hủy. | Đơn mua online hết 15 phút rồi mới nhận callback thành công hai lần → đơn vẫn hủy, hoàn tồn một lần, một khoản hoàn. Đơn đặt riêng bị hủy → không cộng tồn sản phẩm mẫu. |
| UC-27 → UC-06/28/29 | Trang chủ chỉ đọc tin công khai. Tin nội bộ và tệp đi kèm kiểm tra vùng người bán. Thông báo chụp người nhận khi đăng đầu. | Đăng tin nội bộ, ẩn rồi mở từ chuông → Guest không đọc được, người nhận còn thông báo và màn hình đích báo bài không khả dụng. Đăng lại không nhân thông báo. |
| UC phát sự kiện → UC-29 → UC đích | Sự kiện thành công ghi người nhận, khu vực, đối tượng và nội dung lúc phát. Mỗi sự kiện/người nhận chỉ một thông báo. | Cùng tài khoản có đơn mua và đơn bán → mở từ chuông vào đúng khu vực. Đọc thông báo không thay trạng thái đơn. Gửi bù không chạy lại nghiệp vụ nguồn. |

## Các điểm cần thống nhất trước khi triển khai

- **Điều kiện tạo lô đặt riêng.** UC-14 BR-9 hiện yêu cầu đơn Đang sản xuất. G-07 của nhóm 04 vẫn mô tả khả năng tạo lô cho đơn chưa cọc. SRS áp dụng điều kiện ở UC-14 và UC-22 BR-3. Lô đã tạo tiếp tục được quản lý khi trạng thái đơn đổi.
- **Phạm vi thông báo tin mới.** G-01 nhóm 06 còn mở giữa chỉ tin nội bộ và mọi tin đăng lần đầu. Tạm dùng UC-27 BR-7 cho cả hai phạm vi và chỉ gửi tới Artisan tại thời điểm đăng.
- **Thông báo đơn và tiến độ.** G-10 nhóm 08 vẫn ghi Open dù danh mục UC-29 đã có các mẫu UC-31/32/33. Đã đối chiếu mã khai báo tại UC nguồn với danh mục UC-29. Riêng sự kiện đơn đặt riêng bắt đầu sản xuất chưa có mẫu riêng, theo G-04 nhóm 07.
- **Thời hạn thanh toán và hoàn tiền.** Hạn 15 phút áp dụng cho đơn mua online. Hạn cọc đơn đặt riêng còn mở tại G-07 nhóm 04. Với G-02 nhóm 08, SRS đề xuất thử hoàn sau 1, 5 và 15 phút rồi mỗi giờ khi còn lỗi tạm thời. Mỗi lần tra lại cùng mã hoàn ở cổng trước khi gửi lại. Nhóm cần duyệt lịch này và cách xử lý lỗi vĩnh viễn.

## Cách đối chiếu

Đọc UC ở nơi phát sinh thay đổi, kiểm tra bản ghi và ràng buộc tương ứng trong ERD, rồi đối chiếu kết quả ở UC sử dụng. Ví dụ UC-20 tạo đơn đặt riêng, UC-22 đọc tình trạng cọc, UC-14 dùng trạng thái sản xuất để cho tạo lô và UC-24 đọc lại các lô theo đơn. Mỗi ca trong bảng trên cần được chạy xuyên các nhóm liên quan.

## Giao diện dùng chung

Backend kiểm tra phiên và quyền trên tài nguyên trước khi trả dữ liệu. Lỗi nghiệp vụ trả JSON gồm `code` theo bảng ERR của UC và `message` hiển thị. Dùng HTTP 401 khi cần đăng nhập, 403 khi thiếu quyền, 404 khi nội dung công khai không khả dụng, 409 cho phiên bản hoặc trạng thái đã thay đổi và 422 cho dữ liệu không hợp lệ. Dữ liệu danh sách được lọc quyền trước khi đếm và phân trang.

| Thao tác | Request tối thiểu | Kết quả có thể kiểm chứng |
|---|---|---|
| Chấp nhận báo giá | `POST /api/rfqs/{id}/accept` với `quoteId`, `revision`, khóa chống gửi lặp | 201 kèm `orderId`, mã DR, trạng thái Chờ sản xuất và số tiền cọc. Lệnh lặp cùng khóa và dữ liệu trả đơn đã ghi. Nếu phiên bản đã đổi, trả lỗi của UC-20 để Buyer xem lại. |
| Checkout | `POST /api/checkout` với các dòng giỏ đã chọn, số lượng, phương thức giao và thanh toán, thông tin nhận hàng theo phương thức, khóa chống gửi lặp | Backend tính lại giá và phí theo UC-31. 201 kèm mã đơn, tổng tiền và bước tiếp theo. Nếu một món không đủ hàng, báo lỗi cho toàn bộ lần đặt. |
| Callback thanh toán | Bộ tích hợp sandbox xác minh giao dịch, số tiền và chữ ký, ánh xạ mã giao dịch về đúng lần thanh toán | Ghi chuyển trạng thái một lần. Callback lặp không phát lại thông báo. Tiền đến sau khi hủy chuyển sang xử lý hoàn. |
| Mở thông báo | Lấy đích từ dòng của chính tài khoản, gửi yêu cầu đánh dấu đọc rồi mở màn hình theo khu vực | Mở lại cùng dòng không giảm lần nữa số chưa đọc. Màn hình đích kiểm quyền theo trạng thái hiện tại kể cả khi đánh dấu đọc bị lỗi. |
