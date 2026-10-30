---
title: "SRS-NN — <Tên nhóm chức năng> — Architecture"
code: SRS-NN
type: srs
status: draft
version: 0.1.0
date: <yyyy-mm-dd>
author: <Tên người viết>
---

<!--
HƯỚNG DẪN
- SRS trả lời câu hỏi "làm thế nào": bảng dữ liệu, API, luồng giữa các thành phần, bảo mật.
- Mỗi SRS đi kèm một BRD. Mọi mã UC / BR / ERR / NT phải khớp với BRD.
- Diagram viết bằng Mermaid ngay trong file.
- Xem ví dụ: srs/01-account/
-->

# <Tên nhóm chức năng> — Architecture

> **Component:** <tên-component>
> **Scope:** <1–2 câu: phần kỹ thuật nào của nhóm chức năng được đặc tả ở đây.>
> **Related:** [Feature Description](02-feature-description.md) · [Test Cases](03-test-cases.md) · [BRD NN-<slug>](../../brd/NN-<slug>/usecase.md)

---

## 0. Summary

<!-- Một đoạn mô tả luồng kỹ thuật từ đầu đến cuối, rồi liệt kê phạm vi. -->

<Mô tả.>

**In scope:**

- <...>

**Out of scope:**

- <...>

---

## 1. System Context

<!-- Ai / hệ thống nào tham gia, và giao tiếp qua kênh gì. -->

| Actor / System | Role | Direction | Channel |
|---|---|---|---|
| **<Actor>** | <...> | Inbound | HTTPS / Frontend |
| **Backend API** | <...> | Core | REST |
| **Database** | <...> | Outbound | SQL |

---

## 2. Module Structure

<!-- Module nào trong code chịu trách nhiệm gì. Khi có code thì ghi đường dẫn thật. -->

| Module | Trách nhiệm | Thành phần chính |
|---|---|---|
| `<module>` | <...> | <...> |

---

## 3. Data Model

<!-- Chỉ các bảng và cột nhóm chức năng này dùng tới. Ghi rõ PK, FK, UK, giá trị enum. -->

```mermaid
erDiagram
    TABLE_A ||--o{ TABLE_B : "has"
    TABLE_A {
        uuid id PK
        string status "VALUE_1 | VALUE_2"
    }
```

---

## 4. API Surface

| Method | Endpoint | Role | Description | UC |
|---|---|---|---|---|
| `POST` | `/api/<...>` | <Role> | <...> | UC-NN |

**Định dạng lỗi:** `{ "code": "ERR-UCNN-01", "message": "..." }`

| Code | HTTP |
|---|---|
| `ERR-UCNN-01` <Điều kiện> | 400 |

---

## 5. Technical Flows

<!-- Một sequence diagram cho mỗi luồng chính; dùng alt / else cho các nhánh lỗi. -->

### 5.1 <Tên luồng>

```mermaid
sequenceDiagram
    autonumber
    actor U as User
    participant FE as Frontend
    participant BE as Backend
    participant DB as Database
    U->>FE: <hành động>
    FE->>BE: <request>
    BE->>DB: <truy vấn>
    BE-->>FE: <response>
```

---

## 6. Security & Non-Functional

<!-- Phân quyền, dữ liệu nhạy cảm, toàn vẹn dữ liệu, hiệu năng — chỉ những gì liên quan nhóm chức năng này. -->

1. **<Chủ đề>:** <...>
