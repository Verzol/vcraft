# Tài liệu dự án vCraft

> Tinh hoa làng nghề, muôn phương lan tỏa — Bài tập lớn INT3306, Fall 2026.
> Làng nghề nhóm chọn: **Mây tre đan Phú Vinh**. Đề bài: [overview/problem-statement.md](overview/problem-statement.md)

## Cấu trúc

```text
docs/
├── overview.md                        # tổng quan hệ thống + 8 sơ đồ tuần tự draw.io
├── overview/problem-statement.md      # đề bài & tiêu chí chấm điểm
├── brd/                               # yêu cầu nghiệp vụ — 9 nhóm (01-account .. 09-portal) + _template/
│   ├── _template/
│   └── 01-account/ .. 09-portal/
│       ├── usecase.md
│       └── CHANGELOG.md
├── srs/                               # đặc tả kỹ thuật — 9 nhóm đi cặp với BRD + README.md (hợp đồng liên nhóm)
│   ├── README.md
│   ├── _template/
│   └── 01-account/ .. 09-portal/
│       ├── 01-architecture.md
│       ├── 02-feature-description.md
│       └── 03-test-cases.md
└── design/                            # thiết kế kỹ thuật dùng chung toàn hệ thống
    ├── architecture.md                # kiến trúc high-level, design patterns & quyết định công nghệ (ADR)
    ├── erd.md                         # sơ đồ quan hệ thực thể & ranh giới giao dịch
    ├── data-dictionary.md             # từ điển dữ liệu chi tiết (28 bảng, 35 enum, privacy DTOs)
    ├── api.md                         # quy ước REST (/api/v1) & danh mục đầy đủ endpoint 9 module
    ├── error-codes.md                 # danh mục 159 mã lỗi nghiệp vụ + 4 mã lỗi hệ thống & HTTP status
    ├── design-system.md               # màu sắc, typography, spacing & UI components
    └── ci-cd.md                       # quy trình nhánh, PR & kiểm tra tự động GitHub Actions
```

### Quy tắc đặt tên

- Thư mục nhóm chức năng: `<NN>-<slug>`, ví dụ `01-account`, `02-workshop`. `NN` là số thứ tự nhóm; `slug` là tiếng Anh, chữ thường, nối bằng `-`.
- BRD và SRS của cùng một nhóm chức năng dùng **cùng một tên thư mục**: `brd/01-account/` ↔ `srs/01-account/`.
- Tên file cố định, không đổi theo nhóm: BRD là `usecase.md` + `CHANGELOG.md`; SRS là `01-architecture.md`, `02-feature-description.md`, `03-test-cases.md` (số thể hiện thứ tự đọc).
- Không đưa ngày hay phiên bản vào tên file / thư mục; hai thông tin đó nằm trong frontmatter và `CHANGELOG.md`.
- Thư mục bắt đầu bằng `_` (như `_template/`) là mẫu, không phải tài liệu thật.

## BRD và SRS khác nhau thế nào

| | BRD (`usecase.md`) | SRS (`01`–`03`) |
|---|---|---|
| Trả lời | **Làm gì**: ai làm gì, hệ thống phản hồi ra sao, quy tắc nào | **Làm thế nào**: bảng dữ liệu, API, luồng giữa các thành phần |
| Người viết | Người phụ trách nghiệp vụ của nhóm chức năng | Dev phụ trách nhóm chức năng |
| Người đọc | Cả nhóm, giảng viên | Dev, tester |
| Không chứa | Tên bảng, API, framework | Lý do nghiệp vụ (trỏ về BRD) |

## Cách viết một nhóm chức năng mới

1. Đọc ví dụ [UC-01](brd/01-account/usecase.md) trước.
2. Copy `brd/_template/` sang `brd/<NN>-<slug>/`, ví dụ `brd/02-workshop/`.
3. Viết BRD theo thứ tự: Overview → Scope → danh sách Use Case → từng Use Case (Main Flow → Alt Flows → Data Fields → Errors → Business Rules → Acceptance Criteria) → Open Questions. Comment `<!-- -->` trong template giải thích từng mục.
4. Khi bắt đầu code: copy `srs/_template/` sang `srs/<NN>-<slug>/`, rồi thêm SRS vào bảng **Liên quan** của BRD.
5. Mỗi lần sửa: tăng `version` trong frontmatter và thêm một dòng vào `CHANGELOG.md`.
6. Mọi thay đổi đi qua Pull Request để cả nhóm review.

## Quy ước mã định danh

| Mã | Ý nghĩa | Ví dụ |
|---|---|---|
| `UC-nn` | Use case, đánh số tăng dần **trên toàn dự án** (không reset theo nhóm) | `UC-01` |
| `BR-n` | Business rule, đánh số trong một use case | `BR-2` |
| `ERR-UCnn-nn` | Mã lỗi, dùng chung giữa BRD, SRS và code | `ERR-UC01-01` |
| `NT-UCnn-nn` | Template thông báo | `NT-UC01-01` |
| `<UC>-AC-nn` | Acceptance criteria | `UC-01-AC-05` |
| `G-nn` | Câu hỏi mở, đánh số trong một nhóm chức năng | `G-01` |
| `TC-x.y`, `API-nn`, `SEC-nn` | Test case trong SRS; mỗi TC ghi rõ AC mà nó cover | `TC-1.2` |

Mỗi mã nối sang tầng kế tiếp: **Use case → BR / ERR → AC → TC**. Khi sửa một rule, lần theo mã là biết AC và TC nào phải sửa theo.

Thêm use case mới: lấy số UC lớn nhất hiện có + 1, rồi đặt vào nhóm chức năng phù hợp.

## Diagram

Vẽ bằng [Mermaid](https://mermaid.js.org) ngay trong file `.md` (flowchart, erDiagram, sequenceDiagram, stateDiagram). GitHub và VS Code render sẵn, và diff được trong Pull Request như text bình thường.
