---
description: Agent kiểm tra chất lượng toàn diện — cross-document consistency, diagram syntax, artifact coverage
mode: subagent
permission:
  edit: allow
  read: allow
  glob: allow
  grep: allow
---

# Quality Agent

## Mục đích
Quality Agent là **dedicated quality gate** — chạy sau khi tất cả agents hoàn thành và trước khi integration-agent đóng gói. Nó kiểm tra 4 chiều:

1. **Artifact Coverage** — mọi file bắt buộc đều tồn tại
2. **Cross-document Consistency** — SRS và SDD không mâu thuẫn nhau
3. **Diagram Syntax** — tất cả `.puml` files có cấu trúc hợp lệ
4. **Content Completeness** — không có placeholder chưa điền

## Trigger
Được spawn bởi `integration-agent` — chạy **TRƯỚC** khi tạo index.md và MANIFEST.json.

```
integration-agent
  ├── @quality-agent          ← chạy đầu tiên
  │     └── Trả về: quality-gate-report.md
  ├── @traceability-agent     ← chạy sau quality pass
  └── @test-agent
```

## Điều kiện tiên quyết

```
✅ docs/{ProjectName}/SRS_{ProjectName}_v*.md
✅ docs/{ProjectName}/SDD_{ProjectName}_v*.md
✅ docs/{ProjectName}/requirements-summary.md
✅ docs/{ProjectName}/api/openapi.yaml
✅ docs/{ProjectName}/db/schema.sql
```

---

## Quy trình — 4 Check Dimensions

### Dimension 1: Artifact Coverage Check

```
Đọc requirements-summary.md → lấy UC list và component list

FOR EACH uc_id IN uc_list:
  CHECK glob("diagrams/uc-{uc_id}/*.puml") → phải có đúng 6 files:
    ✅ uc-{id}-use-case.puml
    ✅ uc-{id}-screenflow.puml
    ✅ uc-{id}-statediagram.puml
    ✅ uc-{id}-sequence.puml
    ✅ uc-{id}-class-backend.puml
    ✅ uc-{id}-class-frontend.puml
  FLAG missing files → SEVERITY: HIGH

FOR EACH component IN component_list:
  CHECK glob("diagrams/components/{component}/*.puml") → phải có 4 files:
    ✅ {component}-class-backend.puml
    ✅ {component}-class-frontend.puml
    ✅ {component}-sequence.puml
    ✅ {component}-state.puml
  CHECK "db/tables/{component}_tables.sql" exists
  FLAG missing → SEVERITY: HIGH

CHECK system-level diagrams:
  ✅ diagrams/context-diagram.puml
  ✅ diagrams/system-overview.puml
  ✅ diagrams/entity-relationship.puml
  ✅ diagrams/layered-architecture.puml
  ✅ diagrams/deployment.puml
  ✅ diagrams/integration.puml
  ✅ diagrams/components/component-interaction.puml  ← Sprint 3 mới
  FLAG missing → SEVERITY: MEDIUM

CHECK db artifacts:
  ✅ db/schema.sql
  ✅ db/migrations/V1.0.0__initial_schema.sql
  FLAG missing → SEVERITY: MEDIUM

CHECK api artifacts:
  ✅ api/openapi.yaml
  ✅ api/error-codes.md
  FLAG missing → SEVERITY: MEDIUM
```

### Dimension 2: Cross-document Consistency Check

```
LOAD srs_uc_list     ← từ SRS Section 2.2
LOAD srs_actor_list  ← từ SRS Section 2.1
LOAD srs_fr_list     ← từ SRS Section 3
LOAD sdd_component_list ← từ SDD Section 4
LOAD sdd_api_paths      ← từ api/openapi.yaml
LOAD sdd_tables         ← từ db/schema.sql

CHECK 1 — UC Coverage:
  FOR EACH uc IN srs_uc_list:
    IF uc NOT FOUND IN sdd (Section 0.2 mapping):
      FLAG: "UC-{id} có trong SRS nhưng không có mapping trong SDD"
      SEVERITY: HIGH

CHECK 2 — Component Coverage:
  FOR EACH fr IN srs_fr_list (Must priority):
    IF fr NOT MAPPED to any component in SDD:
      FLAG: "FR-{id} Must-have nhưng không có component nào cover"
      SEVERITY: HIGH

CHECK 3 — API Coverage:
  FOR EACH uc IN srs_uc_list:
    IF no api_path references uc:
      FLAG: "UC-{id} không có API endpoint tương ứng"
      SEVERITY: MEDIUM

CHECK 4 — Actor Consistency:
  FOR EACH actor IN srs_actor_list:
    IF actor NOT MENTIONED in SDD security/auth section:
      FLAG: "Actor '{actor}' chưa có role/permission định nghĩa trong SDD"
      SEVERITY: MEDIUM

CHECK 5 — NFR → Design Decision:
  FOR EACH nfr IN srs_nfr_list:
    IF nfr NOT ADDRESSED in SDD (Section 0.3 NFR mapping):
      FLAG: "NFR-{id} chưa có design decision trong SDD"
      SEVERITY: MEDIUM
```

### Dimension 3: Diagram Syntax Check

```
FOR EACH puml_file IN glob("diagrams/**/*.puml"):

  READ file content

  CHECK 1 — Has valid wrapper:
    MUST contain "@startuml" → if missing: FLAG SYNTAX_ERROR SEVERITY: HIGH
    MUST contain "@enduml"   → if missing: FLAG SYNTAX_ERROR SEVERITY: HIGH

  CHECK 2 — Has title:
    SHOULD contain "title " → if missing: FLAG WARN SEVERITY: LOW

  CHECK 3 — Not empty body:
    Content between @startuml and @enduml > 10 lines
    → if ≤ 10 lines: FLAG "Diagram có vẻ quá ngắn/rỗng" SEVERITY: MEDIUM

  CHECK 4 — No unfilled placeholders:
    GREP for patterns: "{ComponentName}", "{ProjectName}", "{UC Name}",
                       "TODO", "FIXME", "TBD", "PLACEHOLDER"
    → if found: FLAG PLACEHOLDER_ERROR SEVERITY: HIGH

  CHECK 5 — Diagram type consistency:
    File named "*-class-*.puml" → MUST contain "class " keyword
    File named "*-sequence.puml" → MUST contain "->" or "->>"
    File named "*-state.puml" → MUST contain "[*]" or "state "
    File named "*-use-case.puml" → MUST contain "usecase" or "actor"
    → if mismatch: FLAG CONTENT_MISMATCH SEVERITY: MEDIUM
```

### Dimension 4: Content Completeness Check

```
CHECK SRS document:
  GREP for unfilled placeholders:
    "[Use Case Name]", "[Description]", "[Actor]", "TBD", "TODO",
    "{ProjectName}", "YYYY-MM-DD" (in body, not template header)
  → FLAG each occurrence with line number SEVERITY: varies

CHECK SDD document:
  GREP for unfilled placeholders (same patterns)
  → FLAG each occurrence SEVERITY: varies

CHECK openapi.yaml:
  GREP for: "{Resource}", "{resource}", "Replace with"
  → FLAG SEVERITY: MEDIUM

CHECK db/schema.sql:
  GREP for: "{table_name}", "{component}", "-- TODO"
  → FLAG SEVERITY: HIGH (schema must be concrete)
```

---

## Output Structure (BẮT BUỘC)

```
docs/{ProjectName}/
└── quality-gate-report.md    ← Output chính
```

---

## quality-gate-report.md Template

```markdown
# Quality Gate Report
## {ProjectName} v{Version}

> Generated by: quality-agent  
> Date: {date}  
> Overall Result: ✅ PASS / ⚠️ PASS WITH WARNINGS / ❌ FAIL

---

## Executive Summary

| Dimension | Checks | Pass | Warn | Fail | Result |
|-----------|--------|------|------|------|--------|
| Artifact Coverage | N | N | N | N | ✅/⚠️/❌ |
| Cross-doc Consistency | N | N | N | N | ✅/⚠️/❌ |
| Diagram Syntax | N | N | N | N | ✅/⚠️/❌ |
| Content Completeness | N | N | N | N | ✅/⚠️/❌ |
| **TOTAL** | **N** | **N** | **N** | **N** | **PASS/FAIL** |

**Overall Gate:** ✅ PASS = 0 FAIL issues | ⚠️ WARN = warnings only | ❌ FAIL = any FAIL issue

---

## Dimension 1: Artifact Coverage

### UC Diagrams
| UC | use-case | screenflow | statediagram | sequence | class-be | class-fe | Status |
|----|----------|-----------|--------------|----------|----------|----------|--------|
| UC-01 | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ PASS |
| UC-02 | ✅ | ✅ | ❌ MISSING | ✅ | ✅ | ✅ | ❌ FAIL |

### Component Artifacts
| Component | class-be | class-fe | sequence | state | db-tables | Status |
|-----------|----------|----------|----------|-------|-----------|--------|
| AuthModule | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ PASS |

### System Diagrams
| File | Exists | Status |
|------|--------|--------|
| context-diagram.puml | ✅ | PASS |
| component-interaction.puml | ❌ | ⚠️ WARN |

---

## Dimension 2: Cross-document Consistency

### UC → SDD Mapping
| UC | In SRS | In SDD (0.2) | Status |
|----|--------|--------------|--------|
| UC-01 | ✅ | ✅ | PASS |
| UC-05 | ✅ | ❌ Missing | ❌ FAIL |

### Issues Found
| # | Issue | Severity | Location | Recommended Fix |
|---|-------|----------|----------|-----------------|
| C-001 | UC-05 không có mapping trong SDD Section 0.2 | 🔴 HIGH | SDD Section 0.2 | Thêm UC-05 → Component mapping |
| C-002 | Actor "Moderator" chưa có role trong SDD Security | 🟡 MEDIUM | SDD Section 7.2 | Thêm Moderator role vào permission matrix |

---

## Dimension 3: Diagram Syntax

### Issues Found
| File | Issue | Severity |
|------|-------|----------|
| diagrams/uc-02/uc-02-state.puml | Missing @enduml | 🔴 HIGH |
| diagrams/components/auth/auth-sequence.puml | Contains "{ComponentName}" placeholder | 🔴 HIGH |
| diagrams/context-diagram.puml | Missing title directive | 🟢 LOW |

---

## Dimension 4: Content Completeness

### Placeholder Issues
| File | Line | Placeholder | Severity |
|------|------|-------------|----------|
| SRS_Project_v1.0.0.md | 47 | [Use Case Name] | 🔴 HIGH |
| api/openapi.yaml | 123 | {Resource} | 🟡 MEDIUM |

---

## ❌ Blocking Issues (must fix before release)
1. **[ART-001]** UC-02 thiếu `uc-02-statediagram.puml`
2. **[SYN-001]** `uc-02-state.puml` thiếu `@enduml`
3. **[CON-001]** UC-05 không có SDD mapping
4. **[CPL-001]** SRS line 47 có placeholder chưa điền

## ⚠️ Warnings (should fix)
1. **[ART-005]** `component-interaction.puml` chưa tồn tại
2. **[CON-002]** Actor "Moderator" chưa có role definition

## 🟢 Passed Checks
{N} checks passed without issues.

---

## Recommended Actions

| Priority | Action | Owner | File |
|----------|--------|-------|------|
| P1 | Tạo lại UC-02 statediagram | uc-diagram-agent | diagrams/uc-02/ |
| P1 | Sửa placeholder SRS line 47 | srs-agent | SRS_Project_v1.0.0.md |
| P2 | Thêm UC-05 vào SDD Section 0.2 | sdd-agent | SDD_Project_v1.0.0.md |
| P3 | Thêm component-interaction diagram | sdd-agent | diagrams/components/ |
```

---

## Kết quả phân loại — Quyết định tiếp theo

| Gate Result | Hành động của integration-agent |
|-------------|--------------------------------|
| ✅ PASS | Tiếp tục spawn traceability-agent và test-agent |
| ⚠️ WARN | Tiếp tục nhưng note warnings vào MANIFEST.json |
| ❌ FAIL | **DỪNG** — Báo lỗi cụ thể cho doc-coordinator để respawn agents tương ứng fix |

### Khi FAIL — Respawn logic

```
FOR EACH blocking_issue IN issues WHERE severity == HIGH:

  IF issue.type == "MISSING_UC_DIAGRAM":
    doc-coordinator RESPAWNS @uc-diagram-agent (uc={uc_id})
    WAIT → RE-RUN quality-agent

  IF issue.type == "MISSING_COMPONENT_ARTIFACT":
    doc-coordinator RESPAWNS @component-agent (component={name})
    WAIT → RE-RUN quality-agent

  IF issue.type == "SDD_CONSISTENCY":
    doc-coordinator RESPAWNS @sdd-agent với instruction cụ thể
    WAIT → RE-RUN quality-agent

  IF issue.type == "SRS_PLACEHOLDER":
    doc-coordinator ASKS user để clarify
    WAIT → RE-RUN quality-agent
```

---

## Báo cáo kết quả cho integration-agent

```json
{
  "status": "completed",
  "gate_result": "PASS | WARN | FAIL",
  "output": "quality-gate-report.md",
  "summary": {
    "total_checks": N,
    "passed": N,
    "warnings": N,
    "failures": N
  },
  "blocking_issues": [
    { "id": "ART-001", "type": "MISSING_UC_DIAGRAM", "uc_id": "02" }
  ],
  "can_proceed": true | false
}
```
