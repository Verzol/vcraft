---
title: "SRS-01 — Tài khoản — Test Cases"
code: SRS-01
type: srs
status: draft
version: 0.1.0
date: 2026-10-30
author: SA
---

<!-- VÍ DỤ MẪU: chỉ đặc tả UC-01 (Đăng ký tài khoản Artisan). -->

# Tài khoản — Test Cases

> **Related:** [Architecture](01-architecture.md) · [Feature Description](02-feature-description.md) · [BRD 01-account](../../brd/01-account/usecase.md)

---

## 1. Acceptance Test Cases

**TC-1.1 — Đăng ký thành công** · Covers `UC-01-AC-01`, `AC-07`
- **Given** người dùng chưa đăng nhập, email `binh.maytre@gmail.com` chưa tồn tại
- **When** điền đủ thông tin, tải 1 ảnh minh chứng và 2 ảnh xưởng, chọn Gửi đăng ký
- **Then** thấy màn hình Chờ duyệt; tài khoản gắn với 1 xưởng ở `PENDING_APPROVAL`; Admin có thông báo `NT-UC01-01`.

**TC-1.2 — Email đã tồn tại** · Covers `UC-01-AC-02`, `AC-06`
- **Given** đã có tài khoản Buyer với email `binh.maytre@gmail.com`
- **When** đăng ký Artisan bằng email đó
- **Then** hiển thị `ERR-UC01-01`; dữ liệu đã nhập được giữ; không tạo tài khoản mới.

**TC-1.3 — Thiếu minh chứng** · Covers `UC-01-AC-03`
- **Given** form đã điền đủ nhưng chưa tải minh chứng
- **When** chọn Gửi đăng ký
- **Then** hiển thị `ERR-UC01-02`; không có request nào được gửi.

**TC-1.4 — Ảnh không hợp lệ** · Covers `UC-01-AC-04`
- **Given** đang ở bước tải ảnh
- **When** chọn tệp `giay-xac-nhan.pdf` hoặc ảnh 15 MB
- **Then** hiển thị `ERR-UC01-03`; ảnh không được nhận.

**TC-1.5 — Artisan chưa duyệt** · Covers `UC-01-AC-05`
- **Given** tài khoản Artisan vừa đăng ký ở TC-1.1
- **When** đăng nhập Portal và mở trực tiếp URL quản lý sản phẩm; đồng thời Guest tìm "Bình An" trên trang công khai
- **Then** Artisan chỉ thấy trang Trạng thái hồ sơ; Guest không thấy xưởng.

---

## 2. API Tests

| Test ID | Request | Điều kiện | Kết quả mong đợi |
|---|---|---|---|
| `API-01` | `POST /api/auth/register-artisan` | Dữ liệu hợp lệ, 1 evidence, 2 photos | 201; 1 `users`, 1 `workshops` (`PENDING_APPROVAL`), 3 `media`, 1 `notifications` cho mỗi Admin |
| `API-02` | `POST /api/auth/register-artisan` | Email trùng | 409 `ERR-UC01-01`; không có bản ghi mới |
| `API-03` | `POST /api/auth/register-artisan` | Không có `evidence[]` | 400 `ERR-UC01-02` |
| `API-04` | `POST /api/auth/register-artisan` | File PDF hoặc > 10 MB | 400 `ERR-UC01-03`; storage không còn file rác |
| `API-05` | `GET /api/me/application` | Token của Artisan vừa đăng ký | 200 `{ status: "PENDING_APPROVAL" }` |

---

## 3. Security Tests

**SEC-01 — Artisan chưa duyệt gọi API khác**
- **Given** token của Artisan đang `PENDING_APPROVAL`
- **When** gọi một API Portal khác, ví dụ tạo sản phẩm
- **Then** 403.

**SEC-02 — Minh chứng không công khai**
- **Given** một ảnh có `kind = EVIDENCE`
- **When** Guest truy cập URL hoặc storage key của ảnh
- **Then** không xem được.

**SEC-03 — Mật khẩu**
- **When** đăng ký thành công rồi kiểm tra bảng `users` và log Backend
- **Then** chỉ có `password_hash` (bcrypt); mật khẩu gốc không xuất hiện ở đâu.
