---
title: "SRS-01 — Tài khoản — Feature Description"
code: SRS-01
type: srs
status: draft
version: 0.1.0
date: 2026-10-30
author: SA
---

<!-- VÍ DỤ MẪU: chỉ đặc tả UC-01 (Đăng ký tài khoản Artisan). -->

# Tài khoản — Feature Description

> **Related:** [Architecture](01-architecture.md) · [Test Cases](03-test-cases.md) · [BRD 01-account](../../brd/01-account/usecase.md)

---

## Feature List

1. [Đăng ký tài khoản Artisan kèm minh chứng](#feature-1-đăng-ký-tài-khoản-artisan-kèm-minh-chứng)
2. [Trang Trạng thái hồ sơ](#feature-2-trang-trạng-thái-hồ-sơ)

---

## Feature Details

### Feature 1: Đăng ký tài khoản Artisan kèm minh chứng

- **Who:** Guest (người đại diện hộ). UC: UC-01.
- **What:** Tạo tài khoản Artisan và xưởng ở trạng thái chờ duyệt.
- **How it works:**
  1. Frontend hiển thị form gồm 3 phần: chủ hộ, xưởng, ảnh.
  2. Frontend kiểm tra sơ bộ (trường bắt buộc, định dạng email và số điện thoại, có ít nhất 1 minh chứng) rồi gửi `POST /api/auth/register-artisan` dạng multipart.
  3. Backend kiểm tra lại toàn bộ, lưu ảnh, tạo `users` + `workshops` + `media` trong một transaction.
  4. Backend tạo `NT-UC01-01` cho mọi Admin.
  5. Frontend chuyển sang màn hình Chờ duyệt.
- **Business rules:**
  - Email duy nhất toàn hệ thống (BR-2) → `ERR-UC01-01`.
  - Thiếu minh chứng → `ERR-UC01-02`; ảnh sai định dạng hoặc quá 10 MB → `ERR-UC01-03`.
  - Mỗi Artisan đúng một xưởng (BR-3), đảm bảo bằng ràng buộc `UNIQUE(owner_id)`.

---

### Feature 2: Trang Trạng thái hồ sơ

- **Who:** Artisan có xưởng đang `PENDING_APPROVAL`. UC: UC-01.
- **What:** Sau khi đăng nhập, Artisan chưa được duyệt chỉ thấy trang này.
- **How it works:** Portal gọi `GET /api/me/application`; nếu trạng thái khác `APPROVED` thì chặn mọi route khác và hiển thị "Hồ sơ đang chờ BQL duyệt".
- **Business rules:** Backend vẫn trả 403 cho các API khác (BR-1), không chỉ dựa vào việc ẩn menu ở giao diện.
- **Giới hạn hiện tại:** Chưa hiển thị lý do bị từ chối — thuộc UC-02.
