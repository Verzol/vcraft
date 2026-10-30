---
title: "NN — <Tên nhóm chức năng>"
group: NN-<slug>
type: brd
status: draft
version: 0.1.0
date: <yyyy-mm-dd>
author: <Tên người viết>
---

<!--
HƯỚNG DẪN CHUNG
- Copy cả thư mục `_template/` sang `brd/<NN>-<slug>/`, ví dụ `brd/02-workshop/`.
- BRD chỉ mô tả NGHIỆP VỤ: ai làm gì, hệ thống phản hồi ra sao, quy tắc là gì.
  KHÔNG viết tên bảng, API, framework — những thứ đó thuộc SRS.
- Xóa các khối comment hướng dẫn và mọi chỗ <...> sau khi điền.
- Xem ví dụ hoàn chỉnh: brd/01-account/usecase.md
-->

# NN — <Tên nhóm chức năng>

Change history: [CHANGELOG.md](CHANGELOG.md)

## I. Overview

<!-- 2–4 câu: nhóm chức năng này giải quyết việc gì, ai tham gia, kết quả cuối cùng là gì. -->
<Mô tả nhóm chức năng.>

### Terms

<!-- Chỉ ghi thuật ngữ người đọc có thể hiểu sai. Không có thì xóa mục này. -->

| Term | Meaning |
|---|---|
| <Thuật ngữ> | <Định nghĩa> |

## II. Scope

<!-- Mỗi dòng là một nhu cầu nghiệp vụ, trỏ tới use case xử lý nó. -->

| Business topic | Channel | Use case |
|---|---|---|
| <Nhu cầu nghiệp vụ> | <Web (Frontend) / Portal> | UC-NN |

**Ngoài phạm vi:** <Những gì cố ý KHÔNG làm trong nhóm chức năng này, để tránh hiểu nhầm.>

**Liên quan**

| ID | Tài liệu | Quan hệ |
|---|---|---|
| <ID> | <link BRD / SRS liên quan> | <Phụ thuộc thế nào> |

## III. Use Cases

<!-- Mã UC đánh số tăng dần trên toàn dự án: lấy số UC lớn nhất hiện có + 1. Lỗi và thông báo theo mã UC: ERR-UC05-01, NT-UC05-01. -->

| Use case ID | Use case | Description |
|---|---|---|
| UC-NN | <Tên use case, bắt đầu bằng động từ> | <1 câu> |

### UC-NN — <Tên use case>

#### Detail

| Field | Content |
|---|---|
| Use case ID | UC-NN |
| Description | <Ai làm gì, để đạt được gì.> |
| Actors | <Vai trò khởi tạo use case> |
| Channel | <Web (Frontend) / Portal> |
| Preconditions | • <Điều kiện phải đúng TRƯỚC khi bắt đầu> |
| Expected | **Thành công:**<br>• <Trạng thái nghiệp vụ sau khi xong><br>**Thất bại:**<br>• <Hệ thống làm gì khi lỗi> |
| Screens | • <Tên màn hình> (bước n-m) |
| Notifications | <NT-UCNN-01 hoặc "Không có"> |

#### Main Flow

<!--
- Chỉ viết luồng THÀNH CÔNG, từng bước một.
- Mỗi bước có đúng một chủ thể: người dùng hoặc một hệ thống (Frontend / Portal / Backend).
- Cột Result ghi điều QUAN SÁT ĐƯỢC hoặc TRẠNG THÁI nghiệp vụ, không ghi chi tiết kỹ thuật.
-->

Trigger: <Sự kiện bắt đầu use case.>

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | <Actor> | <Hành động> | <Kết quả quan sát được> |

Outcome: <Trạng thái cuối cùng.>

#### Alternative Flows

<!--
Mỗi nhánh rẽ khỏi Main Flow (lỗi, hủy, trường hợp đặc biệt) là một Alt.
Ghi rõ rẽ ở bước nào, và sau đó quay lại bước nào hay kết thúc.
-->

##### Alt-01 — <ERR-UCNN-01 — Tên lỗi, hoặc tên nhánh>

- At step: <n> · Trigger: <Điều kiện xảy ra>

| Step | Actor / System | Action | Result |
|---|---|---|---|
| 1 | <System> | <Hành động> | <Kết quả> |

Resume: <Quay lại bước n / Kết thúc.>

#### Data Fields

<!-- Mọi trường người dùng nhập hoặc nhìn thấy. Format: kiểu + độ dài. Ghi chú: ví dụ, ai được xem. -->

| Field | Format | Validation | Required | Ghi chú |
|---|---|---|---|---|
| <Tên trường> | <Text; length 100> | <Quy tắc> | <Có / Không / Có điều kiện> | <Ví dụ: "..."> |

#### Error Messages

<!-- Mỗi lỗi phải có một Alt flow tương ứng. Message là câu hiển thị THẬT cho người dùng. -->

| Code | Condition | Message | Display | Flow |
|---|---|---|---|---|
| ERR-UCNN-01 | <Điều kiện> | "<Thông điệp hiển thị>" | <Inline / Toast / Trang lỗi> | Alt-01 |

#### Notifications

<!-- Xóa mục này nếu use case không gửi thông báo. -->

| Template ID | Trigger (step) | Channel | Recipient | Title | Body |
|---|---|---|---|---|---|
| NT-UCNN-01 | <bước> | In-app | <Người nhận> | <Tiêu đề> | "<Nội dung, biến dạng {{tenBien}}>" |

#### Business Rules

<!-- Quy tắc nghiệp vụ luôn đúng, không phụ thuộc màn hình. Source: đề bài §..., hoặc "Nhóm đề xuất". -->

| ID | Rule | Source |
|---|---|---|
| BR-1 | <Quy tắc> | <Nguồn> |

#### Acceptance Criteria

<!--
Điều kiện để coi use case là XONG, viết dạng Given / When / Then.
Tối thiểu: 1 AC cho Main Flow, 1 AC cho mỗi Alt, 1 AC cho mỗi Business Rule.
Tester dùng bảng này để viết test case trong SRS.
-->

| ID | Given | When | Then | Covers |
|---|---|---|---|---|
| UC-NN-AC-01 | <Tiền điều kiện> | <Hành động> | <Kết quả kiểm chứng được> | Main Flow |

## IV. Open Questions

<!-- Điều chưa chốt. Khi chốt, ghi quyết định vào cột Decision và sửa phần liên quan ở trên. -->

| ID | Question | Affects | Decision |
|---|---|---|---|
| G-01 | <Câu hỏi> | <UC / rule bị ảnh hưởng> | Open |
