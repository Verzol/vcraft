---
title: "03 — Truy xuất nguồn gốc — Đặc tả chức năng"
code: SRS-03
type: srs
status: official
version: 0.3.0
date: 2026-11-18
---

# 03 — Truy xuất nguồn gốc — Đặc tả chức năng

[BRD 0.1.0](../../brd/03-traceability/usecase.md) · [ERD](../../design/erd.md) · [Phụ thuộc liên nhóm](../README.md) · [Kiến trúc và giao diện](01-architecture.md) · [Ca nghiệm thu](03-test-cases.md)

Giới hạn dữ liệu và mã lỗi theo phiên bản BRD trên. Các phương án đang mở trong mục Open Questions cần nhóm xác nhận.

## UC-14 — Quản lý lô sản xuất, tự sinh QR

Artisan tạo lô cho một sản phẩm hoặc một đơn đặt riêng (lô của đơn đặt riêng gắn thêm một phân loại); hệ thống sinh QR và URL cố định. Artisan sửa thông tin lô, đánh dấu Hoàn thành, mở lại hoặc đánh dấu Đã xuất xưởng.

**Quyền và điều kiện** Đã đăng nhập và có quyền bán Có ít nhất một sản phẩm của xưởng mình, hoặc một đơn đặt riêng của xưởng mình đang ở trạng thái Đang sản xuất

Kiểm đúng một nguồn, đúng xưởng và trạng thái nguồn. Sinh mã truy xuất ổn định. Kiểm ít nhất một mốc khi hoàn thành lô.

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

| Mã lỗi | Điều kiện trả lỗi |
|---|---|
| ERR-UC14-01 | Dữ liệu không hợp lệ |
| ERR-UC14-02 | Đối tượng không hợp lệ |
| ERR-UC14-03 | Lô chưa có mốc khi hoàn thành |
| ERR-UC14-04 | Sửa thông tin lô Hoàn thành |
| ERR-UC14-05 | Mở lại hoặc sửa lô Đã xuất xưởng |
| ERR-UC14-06 | Không sinh được QR |
| ERR-UC14-07 | Không có quyền |
| ERR-UC14-08 | Chưa có phân loại phù hợp cho lô đặt riêng |

## UC-15 — Ghi nhật ký sản xuất, ghi chú đính chính

Artisan thêm, sửa, xóa mốc sản xuất trong lô đang thực hiện; thêm ghi chú đính chính cho lô đã xuất xưởng.

**Quyền và điều kiện** Đã đăng nhập và có quyền bán Lô thuộc xưởng mình; luồng chính cần lô Đang thực hiện

Khóa lô trước khi sửa/xóa mốc. Đang thực hiện cho sửa, Hoàn thành cần mở lại, Đã xuất xưởng chỉ ghi đính chính theo BRD.

| Field | Format | Validation | Required | Ghi chú |
|---|---|---|---|---|
| Ngày mốc | Date | Không ở tương lai; không trước ngày bắt đầu lô | Có | Người dùng chọn |
| Thời điểm ghi | Date-time | Hệ thống tự lưu | — | Chỉ xem, dùng sắp xếp mốc cùng ngày |
| Tên công đoạn | Text; length 100 | Không để trống | Có | Ví dụ: "Chẻ tre", "Đan thân giỏ" |
| Mô tả mốc | Text; length 1000 | Không để trống | Có | |
| Ảnh mốc | Ảnh JPG / PNG / WebP | 1–10 ảnh, mỗi ảnh ≤ 10 MB | Có | Công khai trên trang nguồn gốc |
| Nhãn "đã chỉnh sửa" | Chỉ xem | Hiện khi mốc sửa hoặc thêm sau khi lô đã từng Hoàn thành | — | |
| Ghi chú đính chính | Text; length 1000 | Không để trống; chỉ lô Đã xuất xưởng; đăng rồi không sửa, không xóa | Có điều kiện | Tự gắn ngày giờ đăng |

| Mã lỗi | Điều kiện trả lỗi |
|---|---|
| ERR-UC15-01 | Dữ liệu không hợp lệ |
| ERR-UC15-02 | Thiếu ảnh |
| ERR-UC15-03 | Ảnh không hợp lệ |
| ERR-UC15-04 | Thêm, sửa, xóa mốc khi lô Hoàn thành |
| ERR-UC15-05 | Không lưu được |
| ERR-UC15-06 | Không có quyền |
| ERR-UC15-07 | Ghi chú đính chính khi lô chưa xuất xưởng |
| ERR-UC15-08 | Thêm, sửa, xóa mốc khi lô Đã xuất xưởng |

## UC-16 — Xem, tải, in QR

Artisan xem QR và URL của lô, tải ảnh QR và in tem QR để dán lên sản phẩm trong lô.

**Quyền và điều kiện** Đã đăng nhập và có quyền bán Lô thuộc xưởng mình; luồng chính cần lô Hoàn thành hoặc Đã xuất xưởng

Mã hóa URL công khai theo token đã lưu. Tải hoặc in lại cùng URL, kiểm quyền chủ xưởng khi quản lý tem.

| Field | Format | Validation | Required | Ghi chú |
|---|---|---|---|---|
| QR của lô | Ảnh PNG | Chỉ tải, in khi lô Hoàn thành hoặc Đã xuất xưởng | — | Trỏ tới URL cố định của lô |
| URL của lô | Text | Chỉ xem, sao chép được | — | Không đổi suốt vòng đời lô |
| Mã lô | Text | Chỉ xem | — | In trên tem |
| Số tem cần in | Number | Số nguyên từ 1 đến 1000 | Có | Mặc định bằng số lượng của lô |

| Mã lỗi | Điều kiện trả lỗi |
|---|---|
| ERR-UC16-01 | Lô chưa hoàn thành |
| ERR-UC16-02 | Số tem không hợp lệ |
| ERR-UC16-03 | Không tải hoặc in được |
| ERR-UC16-04 | Không có quyền |

## UC-17 — Xem nguồn gốc qua QR

Người dùng quét QR hoặc mở link để xem nhật ký sản xuất của lô, thông tin xưởng và đi tới sản phẩm hoặc gian hàng.

**Quyền và điều kiện** Có link hoặc QR của lô

Tra token độc lập với trạng thái niêm yết sản phẩm. Xuất dữ liệu truy xuất theo UC-17, loại liên hệ Buyer và giá đơn.

| Field | Format | Validation | Required | Ghi chú |
|---|---|---|---|---|
| Tên sản phẩm; hoặc nhãn "Sản xuất theo đơn đặt riêng", tên lô, phân loại | Text | Lô của đơn không hiện tên sản phẩm | Có | Phân loại lấy từ UC-14 |
| Mã lô, trạng thái lô | Text | Trạng thái: Đang thực hiện, Hoàn thành, Đã xuất xưởng | Có | |
| Ngày bắt đầu, số lượng | Date, Number | — | Có | |
| Mốc | Danh sách | Ngày, công đoạn, mô tả, ảnh; ngày tăng dần | Không | Nhãn "đã chỉnh sửa" nếu có |
| Ghi chú đính chính | Danh sách | Mới nhất cuối, kèm ngày giờ | Không | Chỉ lô Đã xuất xưởng |
| Thông tin xưởng | Text và ảnh | Tên, ảnh đại diện, link gian hàng | Có | Từ UC-07 |
| Link sản phẩm | Link | Ẩn nếu sản phẩm Ẩn hoặc lô của đơn đặt riêng | Không | Về UC-11 |

| Mã lỗi | Điều kiện trả lỗi |
|---|---|
| ERR-UC17-01 | Lô không tồn tại |
| ERR-UC17-02 | Gian hàng chưa từng mở |
| ERR-UC17-03 | Không tải được trang |
