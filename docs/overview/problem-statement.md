# Đề bài — vCraft

Nguồn: tài liệu đề bài INT3306 - Phát triển ứng dụng web, Fall 2026 (Google Docs của giảng viên).

## Hướng dẫn thực hiện

- Làm theo nhóm 3 sinh viên.
- Tự chọn công nghệ, framework, library cho frontend và backend.
- Trình bày tại buổi thi vấn đáp: demo trực tiếp, không cần slide hay tài liệu.

## Tổng quan

vCraft là nền tảng web cho Ban quản lý (BQL) một làng nghề truyền thống. Trọng tâm là một "Triển lãm số" thay vì chỉ bán lẻ:

- Mỗi hộ gia đình / nghệ nhân có không gian riêng để kể câu chuyện làm nghề, trưng bày sản phẩm độc bản và minh bạch quy trình sản xuất (truy xuất nguồn gốc).
- BQL phân bổ đơn hàng lớn (mua buôn) cho nhiều hộ cùng thực hiện.

## Vai trò

**Hộ gia đình / Nghệ nhân (Artisan)**
- Quản lý hồ sơ xưởng: lịch sử gia đình, hình ảnh xưởng, chứng nhận chất lượng.
- Đăng sản phẩm kèm Nhật ký sản xuất (các mốc từ nguyên liệu đến thành phẩm).
- Nhận và chào giá các Yêu cầu báo giá (Quote Request) từ khách mua sỉ.

**Khách hàng (Buyer / Tourist)**
- Xem danh mục sản phẩm theo hộ gia đình hoặc theo phân loại.
- Xem bản đồ làng nghề để biết xưởng nào đang mở cửa đón khách tham quan / trải nghiệm.
- Tạo Yêu cầu báo giá; hệ thống gửi đến các hộ phù hợp để chào giá (bidding).

**Quản trị viên (Admin — Chủ tịch xã / Hội làng nghề)**
- Kiểm duyệt và cấp tài khoản cho hộ gia đình (đúng người trong làng, tránh hàng nhái).
- Quản lý tin tức cộng đồng: giá nguyên vật liệu, lịch cúp điện, chính sách hỗ trợ.

## Yêu cầu kỹ thuật

1. **Nhật ký sản xuất & truy xuất nguồn gốc** — Lưu vòng đời của từng lô sản phẩm (mảng mốc thời gian + ảnh minh chứng). Tự động sinh mã QR cho mỗi lô; quét QR mở landing page kể lại toàn bộ quy trình. Sản phẩm cùng lô dùng chung QR.
2. **Luồng báo giá & điều phối đơn** — State machine: Draft Request → Sent to Artisans → Artisans Bidding → Buyer Accepted → In Production → Delivered. Cần khóa bản ghi và cập nhật trạng thái chặt chẽ.
3. **Bản đồ số làng nghề** — Tích hợp Google Maps API hoặc Leaflet; hiển thị pin theo tọa độ xưởng, click pin mở popup thông tin vắn tắt.
4. **Giao diện & đa phương tiện** — Giao diện đẹp, tập trung vào hình ảnh. Upload nhiều ảnh, tối ưu / resize ảnh ở backend để danh sách hàng trăm sản phẩm vẫn tải nhanh.

## Tiêu chí chấm điểm

| STT | Tiêu chí | Hệ số |
|:-:|---|:-:|
| 1 | Chức năng và các features đã cài đặt | 0.35 |
| 2 | Thiết kế: logic, dễ sử dụng | 0.1 |
| 3 | Giao diện: responsive, đẹp, hiện đại, có bản sắc, đặc trưng nhận dạng thương hiệu nổi bật | 0.2 |
| 4 | Hiệu năng: dùng fetch hoặc AJAX để tải bộ phận, không tải lại trang, backend API, dữ liệu JSON, cập nhật DOM trên frontend | 0.1 |
| 5 | Phong cách lập trình: dùng mẫu thiết kế, tách biệt mã giao diện và mã xử lý nghiệp vụ, tổ chức gói thư viện, trình bày và chú thích mã… | 0.05 |
| 6 | Xử lý nhập liệu: kiểm tra hợp thức, tự động điền, gợi ý, chuyển đổi… | 0.05 |
| 7 | An ninh: xác thực, quản lý phiên, điều khiển truy cập, mã hóa… | 0.05 |
| 8 | Viết lại và/hoặc định tuyến URL | 0.05 |
| 9 | Thao tác CSDL theo lập trình hướng đối tượng và độc lập CSDL | 0.05 |
|  | **Tổng** | **1.0** |
