---
name: update-sdd
description: Cập nhật SDD đã có — thêm/sửa component, schema, API với semantic versioning và change tracking
syntax: /update-sdd [project_name] [--scope <scope>]
examples:
  - "/update-sdd MyProject"
  - "/update-sdd MyProject --scope component=PaymentModule"
  - "/update-sdd MyProject --scope api"
  - "/update-sdd MyProject --scope db"
  - "/update-sdd MyProject --scope nfr"
agent: doc-coordinator
---

# Command: /update-sdd

## Mục đích
Cập nhật **tài liệu SDD đã tồn tại** với delta changes — thêm/sửa component, API endpoint, DB schema mà không tạo lại từ đầu.

## Điều kiện tiên quyết

```
✅ docs/{ProjectName}/SDD_{ProjectName}_v*.md PHẢI tồn tại
✅ docs/{ProjectName}/SRS_{ProjectName}_v*.md PHẢI tồn tại
✅ docs/{ProjectName}/api/openapi.yaml PHẢI tồn tại
✅ docs/{ProjectName}/db/schema.sql PHẢI tồn tại

NẾU THIẾU → Hướng dẫn user chạy /create-sdd {ProjectName} trước
```

---

## Scope Options

| Scope | Ý nghĩa | Version Bump | Agents respawned |
|-------|---------|--------------|-----------------|
| `component={Name}` | Sửa/thêm 1 component cụ thể | MINOR | @component-agent(Name) |
| `api` | Thêm/sửa API endpoints | MINOR | @api-agent |
| `db` | Thêm/sửa DB tables/schema | MINOR | @db-agent |
| `nfr` | Sửa NFR design decisions | PATCH | — (direct edit) |
| `security` | Cập nhật security design | PATCH | — (direct edit) |
| `architecture` | Thay đổi kiến trúc | MAJOR | @sdd-agent (full) |
| (none) | Review toàn bộ và xác định scope | varies | interactive |

---

## Quy trình

### Bước 1: Xác định current version và scope

```
Đọc SDD_{ProjectName}_v{X.Y.Z}.md
Hiển thị:
  "📄 SDD hiện tại: v{X.Y.Z} ({date})
   
   Bạn muốn update phần nào?
   A) Component cụ thể (thêm/sửa diagrams + code structure)
   B) API endpoints (thêm/sửa openapi.yaml)
   C) Database schema (thêm table/column)
   D) NFR/Security/Architecture
   E) Tất cả (major revision — review toàn bộ)"
```

### Bước 2: Thu thập delta (scope-specific)

**Nếu scope = component:**
```
"Component nào cần update? (tên chính xác)"
"Thay đổi là gì?"
  → Thêm class/method mới?
  → Sửa sequence flow?
  → Thêm DB table?
  → Sửa state machine?
"Có ảnh hưởng đến component khác không?"
```

**Nếu scope = api:**
```
"Endpoint mới hoặc endpoint nào cần sửa?"
"Method + Path + Request/Response thay đổi như thế nào?"
"Có breaking change không? (thay đổi response format/field name)"
  → Nếu breaking: Tự động bump MAJOR version
"UC nào trigger endpoint mới này?"
```

**Nếu scope = db:**
```
"Table nào cần thêm/sửa?"
"Column mới hoặc column bị thay đổi?"
"Có migration cần thiết không? (existing data handling)"
"Index mới cần thêm?"
```

### Bước 3: Tạo change record

```
docs/{ProjectName}/changes/
└── change-{timestamp}-sdd-{scope}.md

Nội dung:
  - Change ID: CHG-SDD-{YYYYMMDD}-{seq}
  - Type: ADD/MODIFY/REMOVE
  - Scope: component | api | db | nfr
  - Breaking change: yes/no
  - Affected files: list
  - Version: {old} → {new}
  - Migration required: yes/no
```

### Bước 4: Apply delta changes (scope-specific agents)

```
IF scope == "component":
  @component-agent (component={Name}, mode=UPDATE)
  → Đọc các diagrams hiện có
  → Chỉ cập nhật files bị thay đổi
  → Giữ nguyên diagrams không bị ảnh hưởng

IF scope == "api":
  @api-agent (mode=UPDATE)
  → Đọc openapi.yaml hiện có
  → Thêm/sửa paths + schemas cụ thể
  → Cập nhật error-codes.md nếu có code mới

IF scope == "db":
  @db-agent (mode=UPDATE)
  → Đọc schema.sql hiện có
  → Thêm ALTER TABLE hoặc CREATE TABLE mới
  → Tạo migration file V{version}__update.sql
  → Update ER diagram

IF scope IN ["nfr", "security", "architecture"]:
  → Direct edit các sections tương ứng trong SDD
```

### Bước 5: Breaking Change Detection

```
IF api endpoint response schema thay đổi (field removed/renamed):
  → WARN: "⚠️ Breaking change detected!"
  → Prompt user: "Bump MAJOR version? (1.2.0 → 2.0.0)"
  → Gợi ý thêm deprecation notice trong openapi.yaml

IF db column removed/type changed:
  → WARN: "⚠️ DB breaking change!"
  → Yêu cầu viết migration script xử lý existing data
  → Tự động tạo: db/migrations/V{version}__breaking_change.sql
```

### Bước 6: RTM + Test Plan sync

```
IF scope changes UCs or APIs:
  → Flag RTM.md cần update (nếu tồn tại):
    "⚠️ Traceability Matrix có thể outdated.
     Chạy /update-traceability {ProjectName} để sync."

IF scope changes APIs:
  → Flag postman-collection.json cần update:
    "⚠️ API tests có thể outdated.
     Chạy /update-tests {ProjectName} để sync."
```

### Bước 7: Version bump và CHANGELOG

```
Rename: SDD_{ProjectName}_v{X.Y.Z}.md → SDD_{ProjectName}_v{X'.Y'.Z'}.md

Update Record of Changes table:
  | v{X'.Y'.Z'} | {date} | M | AgentCode | {description} |

Update CHANGELOG.md:
  ## SDD v{X'.Y'.Z'} — {date}
  ### Changed
  - Component: {description}
  ### Added
  - API: POST /api/v1/... — {description}
  ### Migration Required
  - db/migrations/V{version}__update.sql — {description}
```

### Bước 8: Re-run quality-agent

```
@quality-agent → validate consistency
IF PASS → Update index.md, MANIFEST.json với version mới
IF FAIL → Fix → retry
```

---

## Output

```
docs/{ProjectName}/
├── SDD_{ProjectName}_v{X'.Y'.Z'}.md    ← Updated SDD
├── api/openapi.yaml                      ← Updated (nếu api scope)
├── db/schema.sql                         ← Updated (nếu db scope)
├── db/migrations/
│   └── V{version}__update.sql           ← New migration (nếu db scope)
├── CHANGELOG.md                          ← Updated
└── changes/
    └── change-{timestamp}-sdd-{scope}.md ← Delta record
```

---

## Ví dụ sử dụng

```
User: /update-sdd MyProject --scope component=PaymentModule

Agent: 📄 SDD hiện tại: v1.2.0 (2026-05-01)
       Updating component: PaymentModule

       Thay đổi là gì?

User: Cần thêm tính năng hoàn tiền (refund) — thêm UC09-Refund vào PaymentModule

Agent: ✅ Đã xác nhận scope:
       - Component: PaymentModule
       - Thay đổi: Thêm Refund flow
       - Files cần update: sequence.puml, state.puml, class-backend.puml
       - DB: thêm table refunds
       - API: thêm POST /api/v1/orders/{id}/refund
       - Version: v1.2.0 → v1.3.0

       Gõ XÁC NHẬN để tiến hành...
```
