---
name: update-srs
description: Cập nhật SRS đã có — thêm/sửa/xóa requirements với semantic versioning và change tracking
syntax: /update-srs [project_name] [--scope <scope>]
examples:
  - "/update-srs MyProject"
  - "/update-srs MyProject --scope uc"
  - "/update-srs MyProject --scope nfr"
  - "/update-srs MyProject --scope feature"
agent: doc-coordinator
---

# Command: /update-srs

## Mục đích
Cập nhật **tài liệu SRS đã tồn tại** với delta changes — không tạo lại từ đầu. Tự động bump version theo semantic versioning và ghi CHANGELOG.

## Điều kiện tiên quyết

```
✅ docs/{ProjectName}/SRS_{ProjectName}_v*.md PHẢI tồn tại
✅ docs/{ProjectName}/requirements-summary.md PHẢI tồn tại

NẾU THIẾU → Hướng dẫn user chạy /create-srs {ProjectName} trước
```

---

## Scope Options

| Scope | Ý nghĩa | Version Bump |
|-------|---------|--------------|
| `uc` | Thêm/sửa/xóa Use Case | MINOR (1.0.0 → 1.1.0) |
| `feature` | Thêm/sửa Feature, FR | MINOR (1.0.0 → 1.1.0) |
| `nfr` | Sửa Non-Functional Requirements | PATCH (1.0.0 → 1.0.1) |
| `actor` | Thêm/sửa Actor | MINOR (1.0.0 → 1.1.0) |
| `fix` | Sửa lỗi typo, clarification | PATCH (1.0.0 → 1.0.1) |
| (none) | Interview toàn bộ — major revision | MAJOR (1.0.0 → 2.0.0) |

---

## Quy trình

### Bước 1: Xác định current version

```
Đọc SRS_{ProjectName}_v{X.Y.Z}.md
Extract current version từ frontmatter hoặc filename
Hiển thị:
  "📄 SRS hiện tại: v{X.Y.Z} ({date})
   Scope: {scope}
   Version sau khi update: v{X'.Y'.Z'}"
```

### Bước 2: Thu thập delta changes (không phỏng vấn lại từ đầu)

```
Hiển thị cho user:
╔══════════════════════════════════════════════════════════════╗
║  📝 UPDATE SRS — {ProjectName} v{X.Y.Z} → v{X'.Y'.Z'}      ║
║                                                              ║
║  Chỉ hỏi về những thay đổi MỚI hoặc CẦN SỬA.              ║
║  Không cần trả lời lại những gì đã có.                     ║
╚══════════════════════════════════════════════════════════════╝

Câu hỏi 1: Bạn muốn THÊM / SỬA / XÓA gì?
  → Describe thay đổi

Câu hỏi 2 (nếu scope=uc): Use case nào bị ảnh hưởng?
  → Danh sách UC IDs

Câu hỏi 3: Có requirements nào khác bị ảnh hưởng bởi thay đổi này?
  → Cross-impact analysis

Câu hỏi 4: Acceptance Criteria mới/cập nhật cho UC bị thay đổi?

XÁC NHẬN: "Gõ XÁC NHẬN để tiến hành update"
```

### Bước 3: Tạo change record

```
Tạo change record trước khi chỉnh sửa:

docs/{ProjectName}/changes/
└── change-{timestamp}-{scope}.md

Nội dung:
  - Change ID: CHG-{YYYYMMDD}-{seq}
  - Timestamp
  - Scope
  - Description
  - Affected sections
  - Version: {old} → {new}
  - Author: user/agent
```

### Bước 4: Apply delta changes

```
IF scope == "uc" OR "actor" OR "feature":
  → Spawn @srs-agent với mode=UPDATE
  → srs-agent đọc file hiện có
  → Chỉ UPDATE sections bị ảnh hưởng
  → Giữ nguyên sections không liên quan
  → Spawn @uc-diagram-agent CHỈ cho UCs bị thay đổi

IF scope == "nfr" OR "fix":
  → Edit trực tiếp các sections cụ thể
  → Không cần spawn uc-diagram-agent
```

### Bước 5: Version bump và CHANGELOG

```
Rename file:
  SRS_{ProjectName}_v{X.Y.Z}.md → SRS_{ProjectName}_v{X'.Y'.Z'}.md

Update Record of Changes table (đầu document):
  | v{X'.Y'.Z'} | {date} | M | AgentCode | {change description} |

Update CHANGELOG.md:
  ## v{X'.Y'.Z'} — {date}
  ### Changed
  - {description of change}
  ### Added (nếu có)
  - {new items}
  ### Removed (nếu có)
  - {removed items}
```

### Bước 6: Re-run quality-agent

```
@quality-agent → kiểm tra SRS mới không có inconsistency
Nếu PASS → Update index.md, MANIFEST.json
Nếu FAIL → Fix và retry
```

---

## Semantic Versioning Rules

```
MAJOR.MINOR.PATCH

MAJOR bump (X.0.0):
  - Thay đổi fundamental scope của dự án
  - Xóa UC/Feature lớn
  - Thay đổi kiến trúc cốt lõi

MINOR bump (X.Y.0):
  - Thêm UC mới
  - Thêm Actor mới
  - Thêm Feature/FR mới
  - Sửa AC đáng kể

PATCH bump (X.Y.Z):
  - Sửa lỗi mô tả (clarification)
  - Cập nhật NFR values
  - Fix typos
  - Minor wording improvements
```

---

## Output

```
docs/{ProjectName}/
├── SRS_{ProjectName}_v{X'.Y'.Z'}.md    ← Updated SRS (new version)
├── CHANGELOG.md                          ← Updated changelog
└── changes/
    └── change-{timestamp}-{scope}.md    ← Delta record
```

> File SRS cũ được GIỮ LẠI (không xóa) để có thể rollback.
> Nếu muốn dọn dẹp, dùng: `/archive-docs {ProjectName}`
