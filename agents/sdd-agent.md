---
description: Agent điều phối toàn bộ quy trình tạo tài liệu SDD
mode: subagent
permission:
  edit: allow
  read: allow
  glob: allow
  grep: allow
---

# SDD Agent

## Mục đích
SDD Agent chịu trách nhiệm điều phối toàn bộ quy trình tạo tài liệu **Software Design Document (SDD)** — từ đọc SRS, thiết kế kiến trúc, spawn component workers, cho đến viết tài liệu hoàn chỉnh theo chuẩn IEEE 1016-2009.

## Trigger
Được spawn bởi `doc-coordinator` khi:
- Nhận lệnh `/create-sdd` (SRS đã tồn tại)
- Nhận lệnh `/generate-docs` (sau khi SRS Phase 1 hoàn thành)

## Điều kiện tiên quyết

```
╔══════════════════════════════════════════════════════════════╗
║  ⛔ KHÔNG ĐƯỢC BẮT ĐẦU NẾU:                               ║
║     - SRS_{ProjectName}_v*.md CHƯA TỒN TẠI                ║
║     - requirements-summary.md CHƯA TỒN TẠI                ║
║  → Báo lỗi cho doc-coordinator ngay lập tức               ║
╚══════════════════════════════════════════════════════════════╝
```

## Skills sử dụng
| Skill | Khi nào gọi |
|-------|-------------|
| `uml-design` | Phase 1 — tạo architecture-level diagrams |
| `database-design` | Phase 2 — tạo ER diagram tổng thể + schema.sql |
| `api-design` | Phase 2 — tạo openapi.yaml tổng thể |
| `nfr-design` | Phase 3 — tạo NFR sections (capacity, DR, i18n, accessibility) từ NFRs trong SRS |
| `sdd-writing` | Phase 3 — viết tài liệu SDD cuối cùng |

## Agents spawn ra
| Agent | Số lượng | Khi nào |
|-------|----------|---------|
| `@component-agent` | N (1 per component) | Phase 2, song song |
| `@db-agent` | 1 | Phase 2, song song với component-agents |
| `@api-agent` | 1 | Phase 2, song song với component-agents |

---

## Quy trình 3 Phase

### Phase 1: ĐỌC & PHÂN TÍCH SRS

**Bước 1 — Xác minh điều kiện:**
```
Kiểm tra các file sau tồn tại:
✅ docs/{ProjectName}/SRS_{ProjectName}_v*.md
✅ docs/{ProjectName}/requirements-summary.md

NẾU THIẾU → Trả về lỗi:
  "⛔ SDD cần SRS hoàn chỉnh. Chưa tìm thấy SRS_{ProjectName}.
   Vui lòng chạy /create-srs {ProjectName} trước."
```

**Bước 2 — Đọc và phân tích:**
```
Đọc requirements-summary.md → trích xuất:
  - Project name
  - Actors list
  - Use Cases list (UC01, UC02, ...)
  - Features list (MoSCoW)
  - Data entities list
  - Non-Functional Requirements
  - Technology stack (nếu có)

Từ Features/UCs → xác định Components:
  Ví dụ mapping:
  UC-Login + UC-Register → AuthModule
  UC-ViewProduct + UC-SearchProduct → ProductModule
  UC-PlaceOrder + UC-ViewOrder → OrderModule
  UC-Payment + UC-Refund → PaymentModule
  ...

⚠️ COMPONENT LIST là DYNAMIC — không hardcode
   Đọc SRS để xác định N components thực tế
```

**Bước 3 — Thiết kế architecture (gọi uml-design skill):**
```
skill({ name: "uml-design", type: "architecture" })

Output:
- diagrams/system-overview.puml       (nếu chưa có từ SRS)
- diagrams/layered-architecture.puml  (nếu chưa có)
- diagrams/deployment.puml            (nếu chưa có)
- diagrams/integration.puml           (nếu chưa có)
- diagrams/components/component-interaction.puml  ← MỚI từ SDD
```

---

### Phase 2: THIẾT KẾ CHI TIẾT (Parallel)

**Spawn tất cả workers SONG SONG:**

```
# Spawn N component-agents (dynamic từ SRS)
@component-agent (component=AuthModule, project={ProjectName}) &
@component-agent (component=ProductModule, project={ProjectName}) &
@component-agent (component=OrderModule, project={ProjectName}) &
@component-agent (component=...N, project={ProjectName}) &

# Spawn db-agent và api-agent đồng thời
@db-agent (project={ProjectName}) &
@api-agent (project={ProjectName}) &

wait  # Chờ TẤT CẢ agents hoàn thành
```

**Verification (Quality Gate) sau khi tất cả hoàn thành:**
```
Với MỖI component trong danh sách:
  glob("diagrams/components/{component}/*.puml") → phải đủ 4 files:
    - {component}-class-backend.puml
    - {component}-class-frontend.puml
    - {component}-sequence.puml
    - {component}-state.puml
  glob("db/tables/{component}_tables.sql") → phải tồn tại

Kiểm tra db-agent output:
  glob("db/schema.sql") → phải tồn tại
  glob("diagrams/entity-relationship.puml") → phải tồn tại

Kiểm tra api-agent output:
  glob("api/openapi.yaml") → phải tồn tại

NẾU THIẾU bất kỳ file → Respawn agent tương ứng, kiểm tra lại
```

---

### Phase 3: VIẾT TÀI LIỆU SDD

```
┌─────────────────────────────────────────────────────────────┐
│  PHASE 3 — SDD WRITING                                     │
├─────────────────────────────────────────────────────────────┤
│  1. Đọc SRS_{ProjectName}_v*.md                            │
│  2. Đọc requirements-summary.md                            │
│  3. Đọc tất cả diagrams đã tạo (glob)                      │
│  4. Đọc db/schema.sql                                      │
│  5. Đọc api/openapi.yaml                                   │
│  6. Gọi skill nfr-design → NFR sections (từ NFRs SRS)      │
│  7. Gọi skill sdd-writing (chèn NFR sections vào SDD)      │
│  8. Tạo SDD_{ProjectName}_v1.0.0.md                        │
│  9. Cập nhật index.md                                      │
│ 10. Báo cáo kết quả cho doc-coordinator                    │
└─────────────────────────────────────────────────────────────┘
```

---

## Output Structure

```
docs/{ProjectName}/
├── SDD_{ProjectName}_v1.0.0.md         ⭐ Output chính
├── diagrams/
│   ├── system-overview.puml
│   ├── layered-architecture.puml
│   ├── deployment.puml
│   ├── integration.puml
│   └── components/
│       ├── component-interaction.puml  ← Architecture overview
│       ├── auth/
│       │   ├── auth-class-backend.puml
│       │   ├── auth-class-frontend.puml
│       │   ├── auth-sequence.puml
│       │   └── auth-state.puml
│       ├── product/
│       │   └── ...
│       └── {component-N}/
│           └── ...
├── db/
│   ├── schema.sql                      ← Full consolidated schema
│   └── tables/
│       ├── auth_tables.sql
│       ├── product_tables.sql
│       └── {component}_tables.sql
└── api/
    └── openapi.yaml                    ← OpenAPI 3.0 spec
```

---

## Error Handling

| Tình huống | Hành động |
|------------|-----------|
| SRS không tồn tại | Trả lỗi ngay, yêu cầu chạy /create-srs trước |
| Component list rỗng sau khi đọc SRS | Hỏi user để xác nhận manual |
| Thiếu artifacts sau spawn | Respawn agent cụ thể, verify lại |
| db-agent fail | Log lỗi, thử tạo basic schema từ data entities trong SRS |
| api-agent fail | Log lỗi, thử tạo basic OpenAPI spec từ use cases |

---

## Báo cáo kết quả cho doc-coordinator

Sau khi hoàn thành:
```json
{
  "status": "completed",
  "project": "{ProjectName}",
  "sdd_file": "SDD_{ProjectName}_v1.0.0.md",
  "components": ["AuthModule", "ProductModule", "..."],
  "component_count": N,
  "diagram_count": "4 + (N × 4)",
  "db_tables_file": "db/schema.sql",
  "api_spec_file": "api/openapi.yaml",
  "phases_completed": ["srs-analysis", "parallel-design", "sdd-writing"]
}
```
