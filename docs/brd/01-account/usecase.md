---
title: "01 — Tài khoản"
group: 01-account
type: brd
status: draft
version: 0.1.0
date: 2026-10-30
author: BA
---

<!--
VÍ DỤ MẪU: use case UC-01 được viết hoàn chỉnh để tham khảo cách viết.
Use case UC-02 (Admin kiểm duyệt) mới chỉ có tên trong danh sách, chưa viết.
-->

# 01 — Tài khoản

Change history: [CHANGELOG.md](CHANGELOG.md)

## I. Overview

Nhóm chức năng về tài khoản: đăng ký, kiểm duyệt hộ làng nghề, đăng nhập. Hộ gia đình / nghệ nhân trong làng đăng ký tài khoản Artisan, kèm minh chứng là hộ làm nghề tại Phú Vinh. Tài khoản ở trạng thái chờ duyệt cho đến khi Admin (BQL làng nghề) đối chiếu và duyệt, nhằm đảm bảo đúng người trong làng và tránh hàng nhái trà trộn (đề bài §3). Chỉ xưởng đã duyệt mới dùng được Portal và xuất hiện công khai.

### Terms

| Term | Meaning |
|---|---|
| Hồ sơ Artisan | Thông tin chủ hộ, thông tin xưởng và minh chứng gửi lên khi đăng ký. |
| Minh chứng hộ làng nghề | Giấy tờ hoặc ảnh chứng minh là hộ làm nghề trong làng, ví dụ giấy xác nhận của thôn, chứng nhận nghệ nhân. |
| Pending approval | Hồ sơ đã gửi, đang chờ Admin xử lý. |

## II. Scope

| Business topic | Channel | Use case |
|---|---|---|
| Hộ đăng ký tài khoản Artisan kèm minh chứng | Web (Frontend) | UC-01 |
| Admin kiểm duyệt, duyệt hoặc từ chối hồ sơ | Portal | UC-02 |

**Ngoài phạm vi:** Tạm khóa tài khoản Artisan; xác thực email khi đăng ký; gửi email kết quả duyệt.

**Liên quan**

| ID | Tài liệu | Quan hệ |
|---|---|---|
| SRS-01 | [SRS-01](../../srs/01-account/01-architecture.md) | Đặc tả kỹ thuật của nhóm chức năng này. |

## III. Use Cases

| Use case ID | Use case | Description |
|---|---|---|
| UC-01 | Đăng ký tài khoản Artisan | Hộ gửi hồ sơ đăng ký kèm minh chứng, chờ Admin duyệt |
| UC-02 | Kiểm duyệt hồ sơ Artisan | Admin đối chiếu minh chứng, duyệt hoặc từ chối hồ sơ — *chưa viết* |

### UC-01 — Đăng ký tài khoản Artisan

#### Detail

| Field | Content |
|---|---|
| Use case ID | UC-01 |
| Description | Người đại diện hộ nhập thông tin chủ hộ, thông tin xưởng và tải minh chứng; hệ thống tạo tài khoản Artisan ở trạng thái Pending approval và báo cho Admin. |
| Actors | Guest (người đại diện hộ) |
| Channel | Web (Frontend) |
| Preconditions | • Người dùng chưa đăng nhập<br>• Email chưa được dùng cho tài khoản nào |
| Expected | **Thành công:**<br>• Tài khoản Artisan được tạo với trạng thái Pending approval<br>• Admin nhận NT-UC01-01<br>• Người dùng thấy màn hình Chờ duyệt<br>**Thất bại:**<br>• Hiển thị lỗi, không tạo tài khoản |
| Screens | • Form đăng ký Artisan (bước 1-3)<br>• Tải minh chứng và ảnh xưởng (bước 4)<br>• Màn hình Chờ duyệt (bước 8) |
| Notifications | NT-UC01-01 |

#### Main Flow

Trigger: Guest chọn "Đăng ký làm hộ làng nghề".

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Guest | Guest chọn đăng ký tài khoản Artisan. | Form đăng ký Artisan hiển thị. |
| 2 | Guest | Guest nhập thông tin chủ hộ: họ tên, email, số điện thoại, mật khẩu. | Thông tin tài khoản sẵn sàng để gửi. |
| 3 | Guest | Guest nhập thông tin xưởng: tên xưởng, địa chỉ trong làng, năm bắt đầu làm nghề. | Thông tin xưởng sẵn sàng để gửi. |
| 4 | Guest | Guest tải lên minh chứng hộ làng nghề và ảnh xưởng. | Ảnh hiển thị trong form. |
| 5 | Guest | Guest chọn Gửi đăng ký. | Hồ sơ được gửi. |
| 6 | Backend | Backend kiểm tra dữ liệu, email chưa tồn tại, tạo tài khoản Artisan và xưởng. | Tài khoản ở trạng thái Pending approval. |
| 7 | Backend | Backend thông báo cho Admin. | NT-UC01-01 được gửi. |
| 8 | Frontend | Frontend hiển thị màn hình Chờ duyệt. | Guest biết hồ sơ đang chờ BQL duyệt. |

Outcome: Hồ sơ Artisan ở trạng thái Pending approval.

#### Alternative Flows

##### Alt-01 — ERR-UC01-01 — Email đã được sử dụng

- At step: 6 · Trigger: Email đã gắn với một tài khoản khác.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Backend | Backend từ chối tạo tài khoản. | Hiển thị ERR-UC01-01. |

Resume: Quay lại bước 2, giữ nguyên dữ liệu đã nhập.

##### Alt-02 — ERR-UC01-02 — Thiếu minh chứng

- At step: 5 · Trigger: Guest gửi hồ sơ khi chưa tải minh chứng.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Frontend | Frontend chặn gửi. | Hiển thị ERR-UC01-02. |

Resume: Quay lại bước 4.

##### Alt-03 — ERR-UC01-03 — Ảnh không hợp lệ

- At step: 4 · Trigger: Ảnh sai định dạng hoặc lớn hơn 10 MB.

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | Frontend | Frontend không nhận ảnh. | Hiển thị ERR-UC01-03. |

Resume: Chọn ảnh khác, lặp lại bước 4.

#### Data Fields

| Field | Format | Validation | Required | Ghi chú |
|---|---|---|---|---|
| Họ tên chủ hộ | Text; length 100 | Không để trống | Có | Ví dụ: "Nguyễn Văn Bình" |
| Email | Text; length 100 | Đúng định dạng email; duy nhất trong hệ thống (BR-2) | Có | Chỉ Admin xem. Ví dụ: `binh.maytre@gmail.com` |
| Số điện thoại | Text; length 10 | Số di động Việt Nam, 10 chữ số | Có | Chỉ Admin xem. Ví dụ: "0912345678" |
| Mật khẩu | Text; length 8–64 | Tối thiểu 8 ký tự, có chữ và số | Có | Không hiển thị lại, không ghi log |
| Tên xưởng | Text; length 150 | Không để trống | Có | Ví dụ: "Xưởng mây tre Bình An" |
| Địa chỉ trong làng | Text; length 200 | Không để trống | Có | Ví dụ: "Thôn Phú Vinh, Chương Mỹ, Hà Nội" |
| Năm bắt đầu làm nghề | Number; 4 chữ số | Từ 1900 đến năm hiện tại | Không | Ví dụ: 1985 |
| Minh chứng hộ làng nghề | Ảnh JPG / PNG / WebP | 1–5 ảnh, mỗi ảnh ≤ 10 MB | Có | Chỉ Admin xem. Loại giấy tờ: xem G-01 |
| Ảnh xưởng | Ảnh JPG / PNG / WebP | 0–10 ảnh, mỗi ảnh ≤ 10 MB | Không | Công khai sau khi được duyệt |

#### Error Messages

| Code | Condition | Message | Display | Flow |
|---|---|---|---|---|
| ERR-UC01-01 | Email đã được sử dụng | "Email này đã được sử dụng. Vui lòng dùng email khác hoặc đăng nhập." | Inline | Alt-01 |
| ERR-UC01-02 | Thiếu minh chứng | "Vui lòng tải lên ít nhất một giấy tờ minh chứng hộ làng nghề." | Inline | Alt-02 |
| ERR-UC01-03 | Ảnh không hợp lệ | "Ảnh không hợp lệ. Chỉ chấp nhận JPG, PNG, WebP, tối đa 10 MB." | Inline | Alt-03 |

#### Notifications

| Template ID | Trigger (step) | Channel | Recipient | Title | Body |
|---|---|---|---|---|---|
| NT-UC01-01 | 7 | In-app | Admin | Hồ sơ hộ mới chờ duyệt | "Xưởng {{workshopName}} vừa đăng ký và đang chờ duyệt." |

#### Business Rules

| ID | Rule | Source |
|---|---|---|
| BR-1 | Tài khoản Artisan mới luôn ở trạng thái Pending approval: chỉ xem được trang Trạng thái hồ sơ và không hiển thị công khai cho đến khi được duyệt. | Đề bài §3 |
| BR-2 | Mỗi email chỉ gắn với một tài khoản, bất kể vai trò. | Nhóm đề xuất |
| BR-3 | Mỗi tài khoản Artisan sở hữu đúng một xưởng. | Nhóm đề xuất |

#### Acceptance Criteria

| ID | Given | When | Then | Covers |
|---|---|---|---|---|
| UC-01-AC-01 | Người dùng chưa đăng nhập; email chưa tồn tại | Điền đủ thông tin, tải minh chứng, chọn Gửi đăng ký | Tài khoản ở trạng thái Pending approval; Admin nhận NT-UC01-01; hiện màn hình Chờ duyệt | Main Flow |
| UC-01-AC-02 | Email đã có tài khoản | Gửi đăng ký bằng email đó | Hiển thị ERR-UC01-01; dữ liệu đã nhập được giữ; không tạo tài khoản | Alt-01 |
| UC-01-AC-03 | Chưa tải minh chứng | Chọn Gửi đăng ký | Hiển thị ERR-UC01-02; hồ sơ không được gửi | Alt-02 |
| UC-01-AC-04 | Đang ở bước tải ảnh | Chọn tệp PDF hoặc ảnh lớn hơn 10 MB | Hiển thị ERR-UC01-03; ảnh không được nhận | Alt-03 |
| UC-01-AC-05 | Tài khoản Artisan đang Pending approval | Đăng nhập Portal hoặc Guest tìm xưởng trên trang công khai | Artisan chỉ thấy trang Trạng thái hồ sơ; xưởng không xuất hiện công khai | BR-1 |
| UC-01-AC-06 | Email đã dùng cho một tài khoản Buyer | Đăng ký Artisan bằng email đó | Bị từ chối với ERR-UC01-01 | BR-2 |
| UC-01-AC-07 | Đăng ký thành công | Kiểm tra tài khoản vừa tạo | Tài khoản gắn với đúng một xưởng | BR-3 |

## IV. Open Questions

| ID | Question | Affects | Decision |
|---|---|---|---|
| G-01 | Những giấy tờ nào được chấp nhận làm minh chứng (giấy xác nhận của thôn / xã, chứng nhận nghệ nhân, ảnh xưởng…)? | UC-01 Data Fields | Open |
| G-02 | Có cần xác thực email trước khi gửi hồ sơ không? | UC-01 | Open |
