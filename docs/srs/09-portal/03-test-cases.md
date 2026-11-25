---
title: "09 — Quản trị & Giám sát sàn — Ca nghiệm thu"
code: SRS-09
type: srs
status: official
version: 0.4.0
date: 2026-11-18
---

# 09 — Quản trị & Giám sát sàn — Ca nghiệm thu

[BRD 0.2.0](../../brd/09-portal/usecase.md) · [ERD](../../design/erd.md) · [Phụ thuộc liên nhóm](../README.md) · [Kiến trúc và giao diện](01-architecture.md) · [Đặc tả chức năng](02-feature-description.md)

Các ca dưới đây truy vết tới AC của BRD. Chạy qua giao diện hoặc API tương ứng khi có ứng dụng.

## UC-34 — Quản lý tài khoản và phân quyền quản trị viên

| TC | AC và quy tắc | Chuẩn bị | Thao tác | Kết quả mong đợi |
|---|---|---|---|---|
| TC-34.1 | UC-34-AC-01 · Main Flow, BR-1, BR-3 | Đã đăng nhập bằng Trưởng BQL; email `canbo1@phuvinh.vn` chưa có trên hệ thống | Mở Quản trị viên, chọn Thêm quản trị viên, nhập đủ thông tin và tích nhóm quyền Kiểm duyệt & Danh mục, chọn Tạo tài khoản | Tài khoản Admin được tạo ở trạng thái Đang hoạt động, hiện ở đầu danh sách; đăng nhập được bằng email và mật khẩu khởi tạo |
| TC-34.2 | UC-34-AC-02 · Alt-01, BR-3 | Đang tạo Admin | Bỏ trống họ tên, nhập mật khẩu "abcdefgh" (thiếu số) hoặc "abc12" (dưới 8 ký tự), hoặc không chọn nhóm quyền nào | Hiển thị ERR-UC34-01 tại trường sai; không tạo tài khoản |
| TC-34.3 | UC-34-AC-03 · Alt-02, BR-1, BR-5 | Đã có tài khoản Buyer dùng email `lan.tran@gmail.com` | Trưởng BQL tạo Admin với email `Lan.Tran@Gmail.com` | Hiển thị ERR-UC34-02; không tạo tài khoản trùng và không nâng cấp tài khoản Buyer đó |
| TC-34.4 | UC-34-AC-04 · Alt-03, BR-4 | Admin A đang đăng nhập và chỉ có quyền Tin tức cộng đồng | Trưởng BQL bỏ quyền Tin tức cộng đồng và gán quyền Giám sát giao dịch cho Admin A | Ở thao tác kế tiếp mà không cần đăng nhập lại, Admin A vào được UC-35 và bị chặn bằng ERR-UC27-06 khi gọi UC-27 |
| TC-34.5 | UC-34-AC-05 · Alt-04, BR-6 | Admin A đang đăng nhập và trước đó đã duyệt hồ sơ xưởng X | Trưởng BQL khóa tài khoản Admin A | Admin A bị kết thúc phiên ở thao tác kế tiếp; thử đăng nhập lại đúng mật khẩu bị từ chối với `ERR-UC04-07`; hồ sơ xưởng X vẫn ghi người duyệt là Admin A |
| TC-34.6 | UC-34-AC-06 · Alt-05, BR-6 | Admin A đã bị khóa, có một hồ sơ mới gửi sau khi khóa | Trưởng BQL mở khóa Admin A; trước đó kiểm tra thông báo | Trong thời gian bị khóa, Admin A không nhận thông báo mới; sau khi mở khóa, Admin A đăng nhập lại được với nhóm quyền cũ |
| TC-34.7 | UC-34-AC-07 · Alt-06, BR-8 | Admin A đang đăng nhập ở một trình duyệt | Trưởng BQL đặt lại mật khẩu mới cho Admin A | Phiên đang mở của Admin A bị kết thúc; Admin A phải đăng nhập lại bằng mật khẩu mới |
| TC-34.8 | UC-34-AC-08 · Alt-07, BR-7 | Trưởng BQL đang đăng nhập | Gửi thao tác khóa hoặc sửa nhóm quyền của chính tài khoản Trưởng BQL | Hiển thị ERR-UC34-03; tài khoản Trưởng BQL vẫn Đang hoạt động và đủ toàn quyền |
| TC-34.9 | UC-34-AC-09 · Alt-08, BR-4 | Admin B chỉ có quyền Kiểm duyệt & Danh mục | Mở thẳng URL Quản trị viên (UC-34), Tin tức cộng đồng (UC-27) hoặc Giám sát giao dịch (UC-35) | Hiển thị ERR-UC34-04 ở Quản trị viên, ERR-UC27-06 ở Tin tức cộng đồng, ERR-UC35-04 ở Giám sát giao dịch; vào được UC-02 và UC-12 bình thường |
| TC-34.10 | UC-34-AC-10 · BR-2 | Tài khoản Admin mới tạo | Mở đường dẫn giỏ hàng hoặc gửi yêu cầu báo giá, hoặc xem đầu trang | Không có chức năng mua/bán, bị từ chối theo UC-30 / UC-18 |
| TC-34.11 | UC-34-AC-11 · BR-6 | Admin đang ở trạng thái Đã khóa (trước khi mở khóa) | Tìm thao tác xóa vĩnh viễn tài khoản Admin | Không có chức năng xóa cứng tài khoản |
| TC-34.12 | UC-34-AC-12 · BR-5, BR-8 | Admin A tự muốn đổi mật khẩu và họ tên của mình | Mở Tài khoản của tôi (UC-05) | Sửa được họ tên và mật khẩu của chính mình; trường email và nhóm quyền không sửa được ở UC-05 |
| TC-34.13 | UC-34-AC-13 · BR-9 | Admin C chỉ được gán quyền Giám sát giao dịch | Đăng nhập không có URL đích trước đó | Sidebar chỉ hiện mục Giám sát giao dịch; hệ thống tự chuyển vào UC-35 (không vào Hồ sơ bán hàng) |
| TC-34.14 | UC-34-AC-14 · Alt-09 | API tải danh sách tài khoản quản trị gặp lỗi | Mở mục Quản trị viên | Hiển thị ERR-UC34-05 kèm nút Thử lại |
| TC-34.15 | UC-34-AC-15 · BR-7 | Danh sách tài khoản quản trị đang hiện, gồm dòng Trưởng BQL | Tìm nút Khóa / Sửa quyền ở dòng Trưởng BQL | Không có nút Khóa hay Sửa quyền ở dòng đó |

## UC-35 — Theo dõi và thống kê giao dịch, đơn hàng toàn hệ thống

| TC | AC và quy tắc | Chuẩn bị | Thao tác | Kết quả mong đợi |
|---|---|---|---|---|
| TC-35.1 | UC-35-AC-01 · Main Flow, BR-5 | Có 3 đơn MH (2 Hoàn tất tổng 1.500.000 đ, 1 Đang giao), 2 đơn DR (1 Hoàn tất 5.000.000 đ, 1 Chờ sản xuất) và 2 yêu cầu YC đã gửi trong 30 ngày qua | Admin có quyền Giám sát giao dịch mở UC-35 | Tổng quan mặc định 30 ngày qua hiện đúng: MH = 3, DR = 2, YC = 2, Đang xử lý = 2, Hoàn tất = 3, Tổng giá trị hoàn tất = 6.500.000 đ; bảng mặc định hiện Đơn mua hàng mới nhất trước |
| TC-35.2 | UC-35-AC-02 · Alt-01, BR-8 | Có 20 đơn MH trong 7 ngày qua và 5 đơn MH từ 15 ngày trước | Đổi kỳ thống kê từ 30 ngày qua sang 7 ngày qua, rồi thử Tùy chọn | Khối thống kê cập nhật về 20 đơn mà không tải lại trang; bộ lọc danh sách bên dưới giữ nguyên |
| TC-35.3 | UC-35-AC-03 · Alt-02 | Đang ở bộ lọc ngày thống kê hoặc danh sách | Chọn Từ ngày 2026-11-20, Đến ngày 2026-11-10 (hoặc Từ ngày ở tương lai) | Hiển thị ERR-UC35-01; số liệu cũ giữ nguyên |
| TC-35.4 | UC-35-AC-04 · Alt-03, BR-2, BR-4 | Yêu cầu YC-2026-0001 gửi 3 xưởng: xưởng A báo giá 2 phiên bản, xưởng B Không nhận, xưởng C chưa phản hồi; Buyer đã chọn xưởng A | Admin mở chi tiết YC-2026-0001 ở UC-35 | Thấy cả 3 xưởng, lý do Không nhận của xưởng B, cả 2 phiên bản báo giá của xưởng A và liên kết tới đơn DR đã tạo; không có nút sửa hay chọn lại xưởng |
| TC-35.5 | UC-35-AC-05 · Alt-04 | Tìm mã `MH-9999-0000` không khớp đơn nào | Nhấn tìm kiếm ở tab Đơn mua hàng | Hiện "Không tìm thấy giao dịch phù hợp" kèm nút Xóa bộ lọc |
| TC-35.6 | UC-35-AC-06 · Alt-05, BR-3 | Có một yêu cầu YC đang ở trạng thái Nháp của Buyer | Admin xem thống kê/danh sách YC và thử mở thẳng URL của bản nháp đó | Bản nháp không tính vào thống kê, không có trong danh sách và mở thẳng báo ERR-UC35-02 |
| TC-35.7 | UC-35-AC-07 · Alt-06, BR-8 | API khối thống kê lỗi, API danh sách hoạt động bình thường | Mở Giám sát giao dịch | Khối thống kê hiện ERR-UC35-03 kèm nút Thử lại; bảng danh sách đơn vẫn tải và lọc được |
| TC-35.8 | UC-35-AC-08 · Alt-07, BR-1 | Admin chỉ có quyền Kiểm duyệt & Danh mục | Mở mục hoặc gọi API của UC-35 | Hiển thị ERR-UC35-04; không lộ số liệu hay danh sách đơn |
| TC-35.9 | UC-35-AC-09 · BR-6, BR-7 | Đơn MH-2026-0004 đã thanh toán online 300.000 đ rồi Đã hủy nhưng cổng hoàn tiền chưa hoàn tất | Xem khối thống kê và lọc đơn chờ hoàn tiền ở UC-35 | Chỉ số Giao dịch đang chờ hoàn tiền tăng 1; chi tiết đơn hiện trạng thái đơn Đã hủy và trạng thái thanh toán tách riêng là Đã thanh toán (kèm nhãn đang xử lý hoàn tiền) |
| TC-35.10 | UC-35-AC-10 · BR-2 | Admin mở chi tiết đơn MH và đơn DR bất kỳ ở UC-35 | Tìm các nút xác nhận, cập nhật giao hàng, thu tiền hoặc hủy đơn | Không có bất kỳ thao tác thay đổi trạng thái nào; mọi API sửa/hủy đơn từ chối tài khoản Admin |
| TC-35.11 | UC-35-AC-11 · Main Flow, BR-8 | Có 25 đơn MH thuộc nhiều xưởng | Chuyển tab, lọc theo xưởng A và sang trang 2 | Dữ liệu cập nhật qua API không tải lại trang; trang 2 hiện 5 đơn còn lại |
| TC-35.12 | UC-35-AC-12 · BR-9 | Đơn DR-2026-0007 bị Buyer gửi yêu cầu hủy, xưởng đồng ý và giữ cọc; đơn có 2 lô sản xuất đã tạo (UC-14) | Admin mở chi tiết DR-2026-0007 | Thấy lý do hủy, quyết định giữ cọc của xưởng, liên kết tới YC gốc và danh sách 2 lô sản xuất |
| TC-35.13 | UC-35-AC-13 · BR-10 | Đang lọc tab Đơn đặt riêng theo trạng thái "Đang sản xuất" và trạng thái thanh toán "Đã đặt cọc" | Chuyển sang tab Yêu cầu báo giá | Bộ lọc trạng thái reset về Tất cả; bộ lọc trạng thái thanh toán bị ẩn (không áp dụng cho YC); khoảng ngày tạo và ô tìm theo mã giữ nguyên |
| TC-35.14 | UC-35-AC-14 · BR-10 | Kỳ thống kê đang là 30 ngày qua; danh sách đang lọc khoảng ngày tạo 01/10–31/10 | Đổi kỳ thống kê sang 7 ngày qua | Khối thống kê đổi theo kỳ mới; bộ lọc khoảng ngày tạo của danh sách không bị ảnh hưởng |
