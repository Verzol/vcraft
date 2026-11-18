---
title: "06 — Tin tức cộng đồng — Kiến trúc và giao diện"
code: SRS-06
type: srs
status: official
version: 0.3.0
date: 2026-11-18
---

# 06 — Tin tức cộng đồng — Kiến trúc và giao diện

[BRD 0.1.0](../../brd/06-news/usecase.md) · [ERD](../../design/erd.md) · [Phụ thuộc liên nhóm](../README.md) · [Đặc tả chức năng](02-feature-description.md) · [Ca nghiệm thu](03-test-cases.md)

API dùng tiền tố `/api`. Backend lấy danh tính từ phiên và kiểm quyền trên đối tượng ở mỗi thao tác. Các lệnh dưới đây mô tả giao diện chính của từng UC.

## UC-27 — Quản lý tin tức

`POST /admin/articles` · `PATCH /admin/articles/{id}`

Kiểm quyền Admin, lọc HTML và tệp đính kèm. Giữ ngày đăng đầu qua lần ẩn/đăng lại, chỉ phát sự kiện theo quy tắc UC-27.

**Dữ liệu liên quan** `articles`, `assets`, `events`.

## UC-28 — Đọc tin tức

`GET /news` · `GET /news/{id}`

Áp phạm vi độc giả trước khi phân trang 10 tin và khi mở bài/tệp. Frontend dùng lại truy vấn tin công khai cho UC-06.

**Dữ liệu liên quan** `articles`, `assets`, `shops`.
