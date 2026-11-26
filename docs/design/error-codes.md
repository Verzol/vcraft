# Danh mục Mã lỗi Hệ thống (Error Code Catalog)

> Cập nhật: 2026-11-26 · Liên quan: [Đặc tả API](api.md) · [Từ điển dữ liệu](data-dictionary.md) · [ERD](erd.md)
>
> Danh mục 159 mã lỗi nghiệp vụ (`ERR-UCxx-yy`) và 4 mã lỗi hệ thống (`ERR-SYS-*`), dùng chung giữa Backend và Frontend để thống nhất mã HTTP, nội dung thông báo và kiểu hiển thị.

---

## 1. Cấu trúc Response Lỗi & Quy ước

```json
{
  "code": "ERR-UC31-01",
  "message": "Một số sản phẩm không còn đủ để đặt: Giỏ mây đan hoa cúc. Vui lòng kiểm tra lại giỏ hàng.",
  "details": {
    "params": { "items": "Giỏ mây đan hoa cúc" },
    "fieldErrors": [
      { "field": "quantity", "reason": "Vượt số lượng còn bán" }
    ]
  }
}
```

- **Quy trình cập nhật:** Khi thêm hoặc sửa mã lỗi ở bất kỳ UC nào, cập nhật đồng thời file BRD/SRS tương ứng và [error-codes.md](error-codes.md) trong cùng PR.
- **Hiển thị lỗi:** Lỗi kiểu `Toast` hiển thị ở vị trí Top-Center trong **6 giây** (tạm dừng khi rê chuột, có nút đóng). Lỗi kiểu `Banner` giữ nguyên cho đến khi người dùng xử lý hoặc đóng.
- **Ánh xạ mã HTTP:**
  - `400` — Payload JSON sai cú pháp hoặc thiếu header bắt buộc (`ERR-SYS-01`).
  - `401` — Chưa đăng nhập, phiên hết hạn (`ERR-UC04-05`), sai email/mật khẩu (`ERR-UC04-01`).
  - `403` — Đã đăng nhập nhưng không đủ quyền, sai vai trò hoặc tài khoản Admin bị khóa.
  - `404` — Tài nguyên không tồn tại hoặc ngoài phạm vi truy cập.
  - `409` — Xung đột trạng thái, phiên bản cũ (optimistic lock), thao tác đồng thời hoặc trùng khóa duy nhất.
  - `422` — Dữ liệu đầu vào, tệp tải lên hoặc điều kiện nghiệp vụ không hợp lệ.
  - `429` — Vượt giới hạn đăng nhập (`ERR-UC04-02`) hoặc giới hạn tần suất (`ERR-SYS-02`).
  - `500` / `502` / `503` — Lỗi hệ thống nội bộ hoặc lỗi kết nối dịch vụ ngoài (ZaloPay/VNPay, GHN, Geocoding, bản đồ).

---

## 2. Mã lỗi Hệ thống Dùng chung (`ERR-SYS-*`)

| Mã lỗi | HTTP | Điều kiện | Thông điệp mặc định | Hiển thị |
|---|---:|---|---|---|
| `ERR-SYS-01` | 400 | Request JSON sai cú pháp hoặc thiếu `Idempotency-Key` bắt buộc | "Yêu cầu không hợp lệ. Vui lòng thử lại." | Toast |
| `ERR-SYS-02` | 429 | Vượt ngưỡng gọi API (rate limit) | "Bạn thao tác quá nhanh. Vui lòng thử lại sau ít phút." | Toast |
| `ERR-SYS-03` | 409 | Trùng `Idempotency-Key` nhưng khác nội dung payload | "Yêu cầu trùng khóa giao dịch với dữ liệu khác." | Toast |
| `ERR-SYS-04` | 500 | Lỗi máy chủ không xác định | "Hệ thống đang gặp sự cố. Vui lòng thử lại sau." | Toast / Banner |

---

## 3. Danh mục Mã lỗi Nghiệp vụ theo 9 Module

### 3.1 Nhóm `01-account` (UC-01 → UC-05)

| Mã lỗi | HTTP | Điều kiện | Thông điệp mặc định | Hiển thị |
|---|---:|---|---|---|
| `ERR-UC01-01` | 409 | Tài khoản đã có hồ sơ đã gửi | "Bạn đã có hồ sơ bán hàng. Mỗi tài khoản chỉ được gửi một hồ sơ." | Trang |
| `ERR-UC01-02` | 422 | Thiếu minh chứng | "Vui lòng tải lên ít nhất một giấy tờ minh chứng hộ làng nghề." | Inline |
| `ERR-UC01-03` | 422 | Ảnh không hợp lệ | "Ảnh không hợp lệ. Chỉ chấp nhận JPG, PNG, WebP, tối đa 10 MB." | Inline |
| `ERR-UC01-04` | 422 | Thông tin xưởng chưa hợp lệ | "Vui lòng kiểm tra lại các trường được đánh dấu." | Inline |
| `ERR-UC02-01` | 422 | Lý do trống hoặc dưới 10 ký tự | "Vui lòng nhập lý do (ít nhất 10 ký tự) để người nộp biết cần xử lý gì." | Inline |
| `ERR-UC02-02` | 409 | Hồ sơ hoặc chứng nhận đã được xử lý hoặc đã thay đổi | "Nội dung này đã được xử lý hoặc vừa thay đổi. Vui lòng tải lại để xem trạng thái mới." | Toast |
| `ERR-UC03-01` | 409 | Email đã được sử dụng | "Email này đã được sử dụng. Vui lòng dùng email khác hoặc đăng nhập." | Inline |
| `ERR-UC03-02` | 422 | Mật khẩu không đạt | "Mật khẩu cần từ 8 đến 64 ký tự, gồm cả chữ và số." | Inline |
| `ERR-UC03-03` | 422 | Mật khẩu nhập lại không khớp | "Mật khẩu nhập lại không khớp." | Inline |
| `ERR-UC03-04` | 422 | Họ tên trống hoặc email sai định dạng | "Vui lòng nhập họ tên và một địa chỉ email hợp lệ." | Inline |
| `ERR-UC03-05` | 422 | Chưa đồng ý điều khoản | "Vui lòng đồng ý Điều khoản sử dụng để tạo tài khoản." | Inline |
| `ERR-UC04-01` | 401 | Sai email hoặc mật khẩu | "Email hoặc mật khẩu không đúng." | Inline |
| `ERR-UC04-02` | 429 | Đăng nhập sai 5 lần liên tiếp | "Bạn đã nhập sai quá nhiều lần. Vui lòng thử lại sau 15 phút." | Inline |
| `ERR-UC04-03` | 403 | Chưa có hồ sơ bán hàng | "Tài khoản của bạn chưa có quyền bán hàng. Đăng ký bán hàng để bắt đầu." | Trang |
| `ERR-UC04-04` | 403 | Hồ sơ chưa được duyệt | "Kênh người bán chỉ mở sau khi hồ sơ bán hàng được duyệt." | Trang |
| `ERR-UC04-05` | 401 | Phiên hết hạn | "Phiên đăng nhập đã hết hạn. Vui lòng đăng nhập lại." | Toast |
| `ERR-UC04-06` | 403 | Không phải Admin mở Trang quản trị, hoặc Admin thiếu nhóm quyền mở UC-02 | "Bạn không có quyền truy cập trang này." | Trang |
| `ERR-UC04-07` | 403 | Tài khoản Admin đang Đã khóa | "Tài khoản quản trị này đã bị khóa. Liên hệ Trưởng BQL để được hỗ trợ." | Inline |
| `ERR-UC05-01` | 422 | Họ tên trống hoặc số điện thoại sai | "Vui lòng nhập họ tên và số điện thoại hợp lệ (10 chữ số)." | Inline |
| `ERR-UC05-02` | 422 | Mật khẩu hiện tại sai | "Mật khẩu hiện tại không đúng." | Inline |
| `ERR-UC05-03` | 422 | Mật khẩu mới không hợp lệ hoặc không khớp | "Mật khẩu mới cần từ 8 đến 64 ký tự, gồm cả chữ và số, và nhập lại phải trùng khớp." | Inline |

### 3.2 Nhóm `02-showcase` (UC-06 → UC-13)

| Mã lỗi | HTTP | Điều kiện | Thông điệp mặc định | Hiển thị |
|---|---:|---|---|---|
| `ERR-UC06-01` | 500 | Khối không tải được | "Không tải được nội dung. Vui lòng thử lại." | Inline |
| `ERR-UC07-01` | 422 | Dữ liệu không hợp lệ | "Thông tin chưa hợp lệ. Vui lòng kiểm tra các trường được đánh dấu." | Inline |
| `ERR-UC07-02` | 422 | Ảnh không hợp lệ | "Ảnh không hợp lệ. Chỉ chấp nhận JPG, PNG, WebP, tối đa 10 MB." | Inline |
| `ERR-UC07-03` | 409 | Đang có yêu cầu đổi chờ duyệt | "Yêu cầu đổi tên hoặc địa chỉ trước đó đang chờ BQL duyệt. Vui lòng chờ kết quả." | Inline |
| `ERR-UC07-04` | 422 | Tắt hết phương thức giao khi đang bán hàng có sẵn | "Gian hàng đang bán hàng có sẵn nên cần ít nhất một phương thức giao." | Inline |
| `ERR-UC07-05` | 403 | Không có quyền | "Bạn không có quyền thực hiện thao tác này." | Trang lỗi |
| `ERR-UC08-01` | 404 | Gian hàng chưa mở hoặc không tồn tại | "Gian hàng này chưa mở hoặc không tồn tại." | Trang lỗi |
| `ERR-UC08-02` | 500 | Không tải được danh sách | "Không tải được danh sách xưởng. Vui lòng thử lại." | Inline |
| `ERR-UC09-01` | 422 | Dữ liệu không hợp lệ | "Thông tin sản phẩm chưa hợp lệ. Vui lòng kiểm tra các trường được đánh dấu." | Inline |
| `ERR-UC09-02` | 422 | Ảnh không hợp lệ | "Ảnh không hợp lệ. Chỉ chấp nhận JPG, PNG, WebP, tối đa 10 MB, tối đa 10 ảnh." | Inline |
| `ERR-UC09-03` | 422 | Chưa đủ thông tin để hiển thị | "Chưa thể hiển thị. Vui lòng bổ sung: {{danhSachMucThieu}}." | Inline |
| `ERR-UC09-04` | 409 | Không xóa được sản phẩm | "Chỉ xóa được sản phẩm nháp chưa có lô. Hãy dùng Ẩn để gỡ sản phẩm khỏi sàn." | Toast |
| `ERR-UC09-05` | 403 | Không có quyền | "Bạn không có quyền thực hiện thao tác này." | Trang lỗi |
| `ERR-UC09-06` | 422 | Chưa có phân loại nào đang Hiển thị | "Chưa có phân loại để chọn. Bạn vẫn lưu nháp được; vui lòng liên hệ BQL để thêm phân loại." | Inline |
| `ERR-UC09-07` | 409 | Đổi sản phẩm Có sẵn đã từng Hiển thị sang Chỉ nhận đặt riêng | "Sản phẩm có sẵn đã từng hiển thị không đổi sang chỉ nhận đặt riêng được. Hãy ẩn sản phẩm này và tạo sản phẩm mới." | Toast |
| `ERR-UC09-08` | 422 | Đổi sang Có sẵn khi gian hàng chưa có phương thức giao nào | "Gian hàng cần ít nhất một phương thức giao để bán sản phẩm có sẵn. Vui lòng bật ở mục Phương thức giao." | Inline |
| `ERR-UC10-01` | 500 | Không tải được kết quả | "Không tải được kết quả. Vui lòng thử lại." | Inline |
| `ERR-UC10-02` | 422 | Giá từ lớn hơn giá đến | "Giá từ phải nhỏ hơn hoặc bằng giá đến." | Inline |
| `ERR-UC11-01` | 404 | Sản phẩm không khả dụng | "Sản phẩm này không còn khả dụng." | Trang lỗi |
| `ERR-UC12-01` | 422 | Tên trống, quá dài hoặc trùng | "Tên phân loại không hợp lệ hoặc đã tồn tại." | Inline |
| `ERR-UC12-02` | 409 | Còn sản phẩm đang Hiển thị | "Không thể ẩn vì phân loại còn sản phẩm đang hiển thị." | Toast |
| `ERR-UC12-03` | 403 | Không có quyền | "Bạn không có quyền thực hiện thao tác này." | Trang lỗi |
| `ERR-UC13-01` | 422 | Checklist chưa đủ | "Gian hàng chưa đủ điều kiện mở. Vui lòng bổ sung: {{danhSachMucThieu}}." | Inline |
| `ERR-UC13-02` | 409 | Thao tác không hợp lệ với trạng thái hiện tại | "Gian hàng đã đổi trạng thái. Vui lòng xem lại trạng thái hiện tại." | Toast |
| `ERR-UC13-03` | 403 | Không có quyền | "Bạn không có quyền thực hiện thao tác này." | Trang lỗi |

### 3.3 Nhóm `03-traceability` (UC-14 → UC-17)

| Mã lỗi | HTTP | Điều kiện | Thông điệp mặc định | Hiển thị |
|---|---:|---|---|---|
| `ERR-UC14-01` | 422 | Dữ liệu không hợp lệ | "Thông tin lô chưa hợp lệ. Vui lòng kiểm tra các trường báo lỗi." | Inline |
| `ERR-UC14-02` | 422 | Đối tượng không hợp lệ | "Không thể tạo lô cho sản phẩm hoặc đơn này. Đơn đặt riêng chỉ tạo lô được khi đang sản xuất." | Inline |
| `ERR-UC14-03` | 422 | Lô chưa có mốc khi hoàn thành | "Lô chưa có mốc sản xuất nào. Hãy thêm ít nhất một mốc trước khi hoàn thành." | Toast |
| `ERR-UC14-04` | 409 | Sửa thông tin lô Hoàn thành | "Lô đang ở trạng thái Hoàn thành, không sửa thông tin lô. Hãy mở lại lô để chỉnh sửa." | Toast |
| `ERR-UC14-05` | 409 | Mở lại hoặc sửa lô Đã xuất xưởng | "Lô đã xuất xưởng, không mở lại hoặc sửa thông tin lô. Hãy thêm ghi chú đính chính nếu cần đính chính." | Toast |
| `ERR-UC14-06` | 500 | Không sinh được QR | "Chưa sinh mã QR thành công. Vui lòng thử lại." | Toast |
| `ERR-UC14-07` | 403 | Không có quyền | "Bạn không có quyền thực hiện thao tác này." | Trang lỗi |
| `ERR-UC14-08` | 422 | Chưa có phân loại phù hợp cho lô đặt riêng | "Chưa có phân loại phù hợp để chọn. Vui lòng chọn phân loại gần nhất hoặc liên hệ BQL để thêm phân loại." | Inline |
| `ERR-UC15-01` | 422 | Dữ liệu không hợp lệ | "Thông tin chưa hợp lệ. Vui lòng kiểm tra các trường báo lỗi." | Inline |
| `ERR-UC15-02` | 422 | Thiếu ảnh | "Mỗi mốc cần ít nhất một ảnh minh chứng." | Inline |
| `ERR-UC15-03` | 422 | Ảnh không hợp lệ | "Ảnh không hợp lệ. Chỉ chấp nhận JPG, PNG, WebP, tối đa 10 MB, tối đa 10 ảnh." | Inline |
| `ERR-UC15-04` | 409 | Thêm, sửa, xóa mốc khi lô Hoàn thành | "Lô đang ở trạng thái Hoàn thành, không thêm, sửa hoặc xóa mốc. Hãy mở lại lô để chỉnh sửa." | Toast |
| `ERR-UC15-05` | 500 | Không lưu được | "Chưa lưu thành công. Hệ thống vẫn giữ dữ liệu bạn nhập, vui lòng thử lại." | Toast |
| `ERR-UC15-06` | 403 | Không có quyền | "Bạn không có quyền thực hiện thao tác này." | Trang lỗi |
| `ERR-UC15-07` | 409 | Ghi chú đính chính khi lô chưa xuất xưởng | "Ghi chú đính chính chỉ dành cho lô đã xuất xưởng. Với lô này, hãy sửa trực tiếp mốc sai và mở lại lô trước nếu lô đang Hoàn thành." | Toast |
| `ERR-UC15-08` | 409 | Thêm, sửa, xóa mốc khi lô Đã xuất xưởng | "Lô đã xuất xưởng, không thêm, sửa hoặc xóa mốc. Hãy thêm ghi chú đính chính nếu cần đính chính." | Toast |
| `ERR-UC16-01` | 409 | Lô chưa hoàn thành | "Chưa thể tải hoặc in QR. Hãy đánh dấu lô Hoàn thành trước." | Toast |
| `ERR-UC16-02` | 422 | Số tem không hợp lệ | "Số tem phải là số nguyên từ 1 đến 1000." | Inline |
| `ERR-UC16-03` | 500 | Không tải hoặc in được | "Chưa tải hoặc in mã QR thành công. Vui lòng thử lại." | Toast |
| `ERR-UC16-04` | 403 | Không có quyền | "Bạn không có quyền xem lô này." | Trang lỗi |
| `ERR-UC17-01` | 404 | Lô không tồn tại | "Không tìm thấy lô này. Vui lòng kiểm tra lại mã QR." | Trang lỗi |
| `ERR-UC17-02` | 404 | Gian hàng chưa từng mở | "Xưởng đang chuẩn bị. Thông tin nguồn gốc sẽ hiển thị sau khi xưởng mở gian hàng." | Trang thông báo |
| `ERR-UC17-03` | 500 | Không tải được trang | "Trang chưa tải xong. Vui lòng thử lại." | Inline |

### 3.4 Nhóm `04-quotation` (UC-18 → UC-24)

| Mã lỗi | HTTP | Điều kiện | Thông điệp mặc định | Hiển thị |
|---|---:|---|---|---|
| `ERR-UC18-01` | 422 | Dữ liệu không hợp lệ | "Thông tin yêu cầu chưa hợp lệ. Vui lòng kiểm tra các trường được đánh dấu." | Inline |
| `ERR-UC18-02` | 422 | Ảnh không hợp lệ | "Ảnh không hợp lệ. Chỉ chấp nhận JPG, PNG, WebP, tối đa 10 MB, tối đa 5 ảnh." | Inline |
| `ERR-UC18-03` | 422 | Hạn không hợp lệ | "Hạn phải từ ngày mai đến tối đa 30 ngày kể từ ngày gửi. Yêu cầu gửi chung cần chọn hạn nhận báo giá." | Inline |
| `ERR-UC18-04` | 409 | Xưởng nhận không khả dụng | "Xưởng này hiện không nhận yêu cầu báo giá. Bạn có thể gửi chung theo phân loại hoặc lưu nháp." | Toast |
| `ERR-UC18-05` | 409 | Không có xưởng để gửi | "Hiện chưa có xưởng nào đang nhận yêu cầu báo giá. Yêu cầu được lưu nháp, bạn có thể gửi lại sau." | Toast |
| `ERR-UC18-06` | 409 | Không gia hạn được | "Không thể gia hạn yêu cầu này. Bạn có thể tạo yêu cầu mới từ yêu cầu này." | Toast |
| `ERR-UC18-07` | 403 | Không có quyền | "Bạn không có quyền thực hiện thao tác này." | Trang lỗi |
| `ERR-UC19-01` | 422 | Dữ liệu báo giá không hợp lệ | "Thông tin báo giá chưa hợp lệ. Vui lòng kiểm tra các trường được đánh dấu." | Inline |
| `ERR-UC19-02` | 409 | Yêu cầu đã đóng nhận báo giá | "Yêu cầu này đã đóng nhận báo giá nên không thể gửi hoặc cập nhật báo giá." | Toast |
| `ERR-UC19-03` | 409 | Gian hàng đang Tạm ngừng | "Gian hàng đang tạm ngừng nên không thể gửi hoặc cập nhật báo giá. Bạn vẫn có thể từ chối yêu cầu." | Toast |
| `ERR-UC19-04` | 409 | Đã phản hồi | "Bạn đã phản hồi yêu cầu này nên không thể phản hồi lại. Nếu đã báo giá, hãy dùng Cập nhật báo giá." | Toast |
| `ERR-UC19-05` | 403 | Không có quyền | "Bạn không có quyền thực hiện thao tác này." | Trang lỗi |
| `ERR-UC19-06` | 409 | Yêu cầu cập nhật giá đã đóng | "Yêu cầu cập nhật giá này đã đóng nên không thể cập nhật giá." | Toast |
| `ERR-UC20-01` | 409 | Báo giá đã hết hiệu lực | "Báo giá này đã hết hiệu lực. Bạn có thể yêu cầu xưởng cập nhật giá." | Toast |
| `ERR-UC20-02` | 409 | Báo giá có phiên bản mới | "Xưởng vừa cập nhật báo giá này. Vui lòng xem lại phiên bản mới trước khi chấp nhận." | Toast |
| `ERR-UC20-03` | 409 | Yêu cầu đã chốt hoặc đã đóng | "Yêu cầu này đã được chốt hoặc đã đóng nên không thể chấp nhận báo giá." | Toast |
| `ERR-UC20-04` | 502 | Thanh toán cọc không thành công | "Chưa thanh toán được tiền cọc. Đơn đã được tạo, bạn có thể thanh toán lại trong chi tiết đơn." | Toast |
| `ERR-UC20-05` | 403 | Không có quyền | "Bạn không có quyền thực hiện thao tác này." | Trang lỗi |
| `ERR-UC20-06` | 409 | Không yêu cầu cập nhật giá được | "Không thể yêu cầu cập nhật giá cho báo giá này." | Toast |
| `ERR-UC20-07` | 409 | Không thể thanh toán lại cọc (đã quá hạn `deposit_due_at`, đã đặt cọc hoặc đơn đã hủy) | "Không thể thanh toán lại tiền cọc vì đơn đã quá hạn thanh toán, đã đặt cọc hoặc đã bị hủy." | Banner |
| `ERR-UC21-01` | 404 | Không có quyền hoặc không tồn tại | "Không tìm thấy nội dung này hoặc bạn không có quyền xem." | Trang lỗi |
| `ERR-UC21-02` | 500 | Không tải được | "Chưa tải được dữ liệu. Vui lòng thử lại." | Inline |
| `ERR-UC22-01` | 409 | Chuyển trạng thái sai thứ tự | "Không thể chuyển đơn sang trạng thái này ở bước hiện tại." | Toast |
| `ERR-UC22-02` | 409 | Chưa đặt cọc | "Buyer chưa thanh toán tiền cọc nên chưa thể bắt đầu sản xuất." | Toast |
| `ERR-UC22-03` | 409 | Đơn đã kết thúc | "Đơn đã hoàn tất hoặc đã hủy nên không thể thay đổi." | Toast |
| `ERR-UC22-04` | 403 | Không có quyền | "Bạn không có quyền thực hiện thao tác này." | Trang lỗi |
| `ERR-UC23-01` | 409 | Yêu cầu đã được chấp nhận | "Bạn đã chấp nhận một báo giá cho yêu cầu này. Nếu muốn hủy, vui lòng hủy đơn đặt riêng." | Toast |
| `ERR-UC23-02` | 409 | Yêu cầu không thể hủy | "Yêu cầu này đã hết hạn hoặc đã được hủy." | Toast |
| `ERR-UC23-03` | 403 | Không có quyền | "Bạn không có quyền thực hiện thao tác này." | Trang lỗi |
| `ERR-UC24-01` | 403 | Không có quyền | "Bạn không có quyền xem nhật ký của đơn này." | Trang lỗi |
| `ERR-UC24-02` | 500 | Không tải được | "Chưa tải được nhật ký sản xuất. Vui lòng thử lại." | Inline |

### 3.5 Nhóm `05-village-map` (UC-25 → UC-26)

| Mã lỗi | HTTP | Điều kiện | Thông điệp mặc định | Hiển thị |
|---|---:|---|---|---|
| `ERR-UC25-01` | 422 | Dữ liệu vị trí hoặc ghi chú không hợp lệ | "Thông tin chưa hợp lệ. Vui lòng xác nhận vị trí xưởng trên bản đồ và kiểm tra ghi chú tham quan (tối đa 300 ký tự)." | Inline |
| `ERR-UC25-02` | 503 | Không tải được bản đồ | "Không tải được bản đồ. Vui lòng thử lại." | Inline |
| `ERR-UC25-03` | 403 | Không có quyền | "Bạn không có quyền thực hiện thao tác này." | Trang lỗi |
| `ERR-UC25-04` | 422 | Lịch đón khách không hợp lệ | "Lịch đón khách chưa hợp lệ. Vui lòng kiểm tra các dòng được đánh dấu." | Inline |
| `ERR-UC25-05` | 502 | Không tìm được vị trí theo địa chỉ | "Chưa tìm được vị trí theo địa chỉ của xưởng. Vui lòng bấm lên bản đồ để chọn vị trí." | Inline |
| `ERR-UC25-06` | 422 | Chưa có vị trí cho địa chỉ mới | "Vui lòng kiểm tra và xác nhận vị trí xưởng trên bản đồ cho địa chỉ mới trước khi gửi yêu cầu." | Inline |
| `ERR-UC25-07` | 409 | Đổi vị trí khi đang có yêu cầu đổi địa chỉ chờ duyệt | "Yêu cầu đổi địa chỉ đang chờ BQL duyệt nên chưa thể đổi vị trí xưởng. Vị trí mới sẽ được áp dụng cùng địa chỉ khi yêu cầu được duyệt." | Inline |
| `ERR-UC26-01` | 503 | Không tải được bản đồ hoặc pin | "Không tải được bản đồ. Vui lòng thử lại." | Inline |
| `ERR-UC26-02` | 500 | Không tải được popup | "Không tải được thông tin xưởng. Vui lòng thử lại." | Inline |

### 3.6 Nhóm `06-news` (UC-27 → UC-28)

| Mã lỗi | HTTP | Điều kiện | Thông điệp mặc định | Hiển thị |
|---|---:|---|---|---|
| `ERR-UC27-01` | 422 | Dữ liệu không hợp lệ | "Thông tin tin tức chưa hợp lệ. Vui lòng kiểm tra các trường được đánh dấu." | Inline |
| `ERR-UC27-02` | 422 | Ảnh không hợp lệ | "Ảnh không hợp lệ. Chỉ chấp nhận JPG, PNG, WebP, tối đa 10 MB mỗi ảnh, 1 ảnh bìa và 10 ảnh trong bài." | Inline |
| `ERR-UC27-03` | 422 | Chưa đủ thông tin để đăng | "Chưa thể đăng tin. Vui lòng bổ sung: {{danhSachMucThieu}}." | Inline |
| `ERR-UC27-04` | 409 | Không xóa được tin | "Chỉ xóa được tin nháp. Hãy dùng Ẩn để gỡ tin đã đăng khỏi người đọc." | Toast |
| `ERR-UC27-05` | 409 | Tin vừa được Admin khác thay đổi | "Tin này vừa được thay đổi. Vui lòng xem lại nội dung mới trước khi thao tác." | Toast |
| `ERR-UC27-06` | 403 | Không có quyền | "Bạn không có quyền thực hiện thao tác này." | Trang lỗi |
| `ERR-UC27-07` | 500 | Không tải được danh sách | "Không tải được danh sách tin. Vui lòng thử lại." | Inline |
| `ERR-UC27-08` | 422 | Tệp đính kèm không hợp lệ | "Tệp không hợp lệ. Chỉ chấp nhận PDF, DOC, DOCX, XLS, XLSX, tối đa 10 MB mỗi tệp, tối đa 5 tệp." | Inline |
| `ERR-UC28-01` | 404 | Tin không khả dụng | "Tin này không còn khả dụng hoặc không tồn tại." | Trang lỗi |
| `ERR-UC28-02` | 500 | Không tải được tin | "Không tải được tin tức. Vui lòng thử lại." | Inline |

### 3.7 Nhóm `07-notification` (UC-29)

| Mã lỗi | HTTP | Điều kiện | Thông điệp mặc định | Hiển thị |
|---|---:|---|---|---|
| `ERR-UC29-01` | 500 | Không tải được thông báo | "Không tải được thông báo. Vui lòng thử lại." | Inline |
| `ERR-UC29-02` | 500 | Không đánh dấu được tất cả đã đọc | "Chưa đánh dấu đã đọc được. Vui lòng thử lại." | Toast |
| `ERR-UC29-03` | 404 | Thông báo không tồn tại hoặc không có quyền | "Không tìm thấy thông báo này hoặc bạn không có quyền xem." | Trang lỗi |

### 3.8 Nhóm `08-order` (UC-30 → UC-33)

| Mã lỗi | HTTP | Điều kiện | Thông điệp mặc định | Hiển thị |
|---|---:|---|---|---|
| `ERR-UC30-01` | 409 | Vượt số còn bán | "Chỉ còn {{available}} sản phẩm. Số lượng trong giỏ đã được điều chỉnh." | Inline |
| `ERR-UC30-02` | 422 | Số lượng không hợp lệ | "Số lượng phải là số nguyên từ 1 đến {{available}}." | Inline |
| `ERR-UC30-03` | 409 | Không thêm được vào giỏ | "Không thể thêm sản phẩm này vào giỏ: {{reason}}." | Toast |
| `ERR-UC30-04` | 500 | Không tải được giỏ | "Không tải được giỏ hàng. Vui lòng thử lại." | Inline |
| `ERR-UC30-05` | 403 | Tài khoản Admin | "Tài khoản quản trị không dùng được chức năng mua hàng." | Trang lỗi |
| `ERR-UC31-01` | 409 | Món không còn mua được | "Một số sản phẩm không còn đủ để đặt: {{items}}. Vui lòng kiểm tra lại giỏ hàng." | Banner |
| `ERR-UC31-02` | 422 | Thông tin nhận hàng chưa hợp lệ | "Vui lòng nhập {{field}} hợp lệ." | Inline |
| `ERR-UC31-03` | 409 | Phương thức giao không còn dùng | "Phương thức giao hàng đã thay đổi. Vui lòng chọn lại." | Banner |
| `ERR-UC31-04` | 409 | Giá đã thay đổi | "Giá một số sản phẩm đã thay đổi. Vui lòng xem lại tổng thanh toán." | Banner |
| `ERR-UC31-05` | 502 | Thanh toán thất bại hoặc bị hủy | "Thanh toán chưa thành công. Bạn có thể thanh toán lại trong {{minutesLeft}} phút." | Banner |
| `ERR-UC31-06` | 502 | Cổng thanh toán không khởi tạo được | "Hiện chưa kết nối được cổng thanh toán. Vui lòng thử lại." | Banner |
| `ERR-UC31-07` | 502 | Không tính được phí GHN | "Chưa tính được phí giao hàng GHN. {{fallback}}" | Inline |
| `ERR-UC31-08` | 503 | Chưa có kết quả thanh toán | "Chưa nhận được kết quả thanh toán. Hệ thống đang kiểm tra; bạn có thể thanh toán lại trong {{minutesLeft}} phút." | Banner |
| `ERR-UC31-09` | 409 | Không thể thanh toán lại đơn mua (đã quá hạn 15 phút, đã thanh toán hoặc đơn đã hủy) | "Không thể thanh toán lại vì đơn hàng đã quá hạn thanh toán, đã thanh toán hoặc đã bị hủy." | Banner |
| `ERR-UC32-01` | 409 | Đơn đã đổi trạng thái | "Đơn hàng vừa được cập nhật. Vui lòng xem lại trạng thái mới." | Banner |
| `ERR-UC32-02` | 422 | Lý do không hợp lệ | "Vui lòng nhập lý do từ 10 đến 500 ký tự." | Inline |
| `ERR-UC32-03` | 409 | Chuyển trạng thái không hợp lệ | "Không thể chuyển đơn từ {{from}} sang {{to}}." | Toast |
| `ERR-UC32-04` | 422 | Chưa xác nhận đã thu tiền | "Vui lòng xác nhận đã thu đủ {{amount}} trước khi đánh dấu đã giao." | Inline |
| `ERR-UC32-05` | 404 | Không xem được đơn | "Không tìm thấy đơn hàng hoặc bạn không có quyền xem đơn này." | Trang lỗi |
| `ERR-UC32-06` | 500 | Không tải được đơn | "Không tải được đơn hàng. Vui lòng thử lại." | Inline |
| `ERR-UC33-01` | 409 | Không hủy được ở trạng thái hiện tại | "Đơn hàng đang ở trạng thái {{status}} nên không thể hủy." | Toast |
| `ERR-UC33-02` | 409 | Đơn đã đổi trạng thái | "Đơn hàng vừa được cập nhật. Vui lòng xem lại trạng thái mới." | Banner |
| `ERR-UC33-03` | 422 | Lý do không hợp lệ | "Vui lòng nhập lý do từ 10 đến 500 ký tự." | Inline |
| `ERR-UC33-04` | 409 | Đã có yêu cầu hủy đang chờ | "Đơn hàng đã có yêu cầu hủy đang chờ xưởng phản hồi." | Banner |
| `ERR-UC33-05` | 403 | Không có quyền hủy | "Bạn không có quyền hủy đơn hàng này." | Trang lỗi |
| `ERR-UC33-06` | 502 | Hoàn tiền đang thử lại | "Khoản hoàn {{amount}} đang được xử lý. Chúng tôi sẽ cập nhật khi tiền được hoàn." | Banner |
| `ERR-UC33-07` | 422 | Số lượng trả về không hợp lệ | "Số lượng trả về của {{product}} phải là số nguyên từ 0 đến {{quantity}}." | Inline |
| `ERR-UC33-08` | 502 | Hoàn tiền thất bại vĩnh viễn hoặc hết 5 lần thử lại (`refunds.state = FAILED`) | "Hoàn tiền chưa thành công, vui lòng liên hệ Ban quản lý kèm mã đơn {{orderCode}}." | Banner |

### 3.9 Nhóm `09-portal` (UC-34 → UC-35)

| Mã lỗi | HTTP | Điều kiện | Thông điệp mặc định | Hiển thị |
|---|---:|---|---|---|
| `ERR-UC34-01` | 422 | Dữ liệu không hợp lệ hoặc chưa chọn nhóm quyền | "Thông tin chưa hợp lệ hoặc chưa chọn nhóm quyền. Vui lòng kiểm tra lại các trường được đánh dấu." | Inline |
| `ERR-UC34-02` | 409 | Email đã tồn tại trên hệ thống | "Email này đã gắn với một tài khoản khác trên hệ thống." | Inline |
| `ERR-UC34-03` | 403 | Tự khóa hoặc đổi quyền tài khoản Trưởng BQL | "Không thể tự khóa hoặc thay đổi quyền của tài khoản Trưởng BQL." | Toast |
| `ERR-UC34-04` | 403 | Không đủ quyền quản trị cho thao tác này | "Bạn không có quyền thực hiện chức năng quản trị này." | Trang lỗi |
| `ERR-UC34-05` | 500 | Lỗi tải danh sách tài khoản quản trị | "Chưa tải được danh sách tài khoản quản trị. Vui lòng thử lại." | Inline |
| `ERR-UC35-01` | 422 | Khoảng ngày lọc không hợp lệ | "Khoảng ngày không hợp lệ. Ngày bắt đầu phải nhỏ hơn hoặc bằng ngày kết thúc và không ở tương lai." | Inline |
| `ERR-UC35-02` | 404 | Giao dịch không tồn tại hoặc đang là nháp của Buyer | "Không tìm thấy giao dịch hoặc giao dịch chưa được gửi." | Trang lỗi |
| `ERR-UC35-03` | 500 | Lỗi tải thống kê hoặc danh sách | "Chưa tải được dữ liệu giám sát. Vui lòng thử lại." | Inline |
| `ERR-UC35-04` | 403 | Không có quyền Giám sát giao dịch | "Bạn không có quyền truy cập mục Giám sát giao dịch." | Trang lỗi |
