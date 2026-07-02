# AgentCode Framework — AGENTS.md

> Canonical workflow reference. Số liệu trong file này phản ánh **trạng thái thực tế** của repo
> (13 agents · 10 skills · 10 commands). Khi thêm/xóa component, cập nhật file này **và** `README.md` + `MANIFEST.md`.

## Framework Overview

AgentCode Framework là một AI agent coding framework để tự động tạo tài liệu phần mềm (SRS, SDD, RTM, Test Plan) theo các chuẩn IEEE / ISO.

**Luồng chính:** User → `doc-coordinator` (primary) → `srs-agent` / `sdd-agent` (coordinators) → worker agents (spawn song song) → skills → artifacts trong `docs/{ProjectName}/`.

**Nguyên tắc cốt lõi:**
- `doc-coordinator` **không** gọi skills trực tiếp — luôn ủy quyền qua sub-agents.
- Worker agents có thể spawn **song song** (`&` … `wait`).
- Diagrams được **link** (external `.puml`), không embed vào tài liệu.
- Trước SRS, phỏng vấn requirements là **bắt buộc** — user phải gõ `XÁC NHẬN` mới tiếp tục.

---

## Agent File Structure

```
agentcode-framework/
├── AGENTS.md                            # File này — workflow & registry
├── README.md                           # Tổng quan
├── MANIFEST.md                         # Manifest & quick start
│
├── agents/                             # 13 agent definitions
│   ├── doc-coordinator.md              # PRIMARY — leader
│   ├── srs-agent.md                    # SUBAGENT — SRS coordinator
│   ├── sdd-agent.md                    # SUBAGENT — SDD coordinator
│   ├── requirements-agent.md           # worker — multi-spawn (theo actor/UC)
│   ├── uc-diagram-agent.md             # worker — multi-spawn (theo UC)
│   ├── component-agent.md              # worker — multi-spawn (theo component)
│   ├── db-agent.md                     # worker — singleton
│   ├── api-agent.md                    # worker — singleton
│   ├── integration-agent.md            # post-processing
│   ├── traceability-agent.md           # RTM
│   ├── test-agent.md                   # Test plan
│   ├── quality-agent.md                # quality gate
│   └── formatter-agent.md              # export docx/pdf/html/xlsx (on demand)
│
├── skills/                             # 10 skills (mỗi skill = SKILL.md)
│   ├── requirements-gathering/
│   ├── srs-writing/
│   ├── sdd-writing/
│   ├── uml-design/
│   ├── component-design/
│   ├── database-design/
│   ├── api-design/
│   ├── nfr-design/
│   ├── traceability/
│   └── test-design/
│
└── commands/                           # 10 slash commands
    ├── create-srs.md
    ├── create-sdd.md
    ├── generate-docs.md
    ├── interview-stakeholder.md
    ├── create-srs-template.md
    ├── create-sdd-template.md
    ├── create-diagram.md
    ├── update-srs.md
    ├── update-sdd.md
    └── export-docs.md
```

---

## All Available Agents (13)

| Agent | Mode | Description | Can Spawn Multiple? | Invoked By |
|-------|------|-------------|---------------------|------------|
| `doc-coordinator` | primary | Leader — điều phối toàn bộ, xử lý commands | No | User |
| `srs-agent` | subagent | Coordinator SRS workflow | No | doc-coordinator |
| `sdd-agent` | subagent | Coordinator SDD workflow | No | doc-coordinator |
| `requirements-agent` | subagent | Thu thập requirements cho 1 actor/UC | **YES** | srs-agent |
| `uc-diagram-agent` | subagent | Tạo 6 diagrams cho 1 UC | **YES** | srs-agent |
| `component-agent` | subagent | Thiết kế 5 artifacts cho 1 component | **YES** | sdd-agent |
| `db-agent` | subagent | Schema tổng thể + ER diagram + DDL | No | sdd-agent |
| `api-agent` | subagent | OpenAPI 3.0 spec + error codes | No | sdd-agent |
| `integration-agent` | subagent | Index, consistency check, packaging | No | doc-coordinator |
| `traceability-agent` | subagent | RTM (FR→UC→Component→API→DB→Test) | No | integration-agent |
| `test-agent` | subagent | Test Plan + Test Cases + Postman | No | integration-agent |
| `quality-agent` | subagent | Quality gate: coverage, consistency, diagram syntax | No | integration-agent |
| `formatter-agent` | subagent | Convert markdown → docx/pdf/html/xlsx | No | doc-coordinator (on demand) |

### OpenCode agent configuration (frontmatter)

`doc-coordinator` dùng `mode: primary`; tất cả agent còn lại dùng `mode: subagent`. Permission tối thiểu:

```yaml
---
description: <mô tả agent>
mode: subagent           # hoặc primary cho doc-coordinator
permission:
  edit: allow
  read: allow
  glob: allow
  grep: allow
  bash: allow            # chỉ formatter-agent cần bash (pandoc/libreoffice)
---
```

`doc-coordinator` gọi subagent qua **@mention** (vd: `@srs-agent`). Worker được spawn song song với `&` … `wait`.

---

## Available Skills (10)

| Skill | Version | Purpose | Location |
|-------|---------|---------|----------|
| `requirements-gathering` | 3.2.0 | Phỏng vấn bắt buộc từng câu (AskUserQuestion), kết thúc bằng "XÁC NHẬN" | skills/requirements-gathering/SKILL.md |
| `srs-writing` | 4.0.0 | Viết SRS IEEE; AC per UC + RTI; link PlantUML ngoài | skills/srs-writing/SKILL.md |
| `sdd-writing` | 4.0.0 | Viết SDD IEEE 1016; requirements mapping, error/logging/caching/rate-limit | skills/sdd-writing/SKILL.md |
| `uml-design` | 3.0.0 | Use case, screen flow, state, ERD, sequence, deployment, DFD, integration | skills/uml-design/SKILL.md |
| `component-design` | 1.0.0 | 5 artifacts/component (class BE/FE, sequence, state, `*_tables.sql`) | skills/component-design/SKILL.md |
| `database-design` | 1.0.0 | ER diagram, DDL, schema tổng thể | skills/database-design/SKILL.md |
| `api-design` | 3.0.0 | OpenAPI 3.0, error codes, auth, versioning | skills/api-design/SKILL.md |
| `nfr-design` | 1.0.0 | Capacity planning, DR, i18n/l10n, accessibility (WCAG) | skills/nfr-design/SKILL.md |
| `traceability` | 1.0.0 | RTM + coverage report | skills/traceability/SKILL.md |
| `test-design` | 1.0.0 | TEST-PLAN, test cases/UC, Postman từ openapi.yaml | skills/test-design/SKILL.md |

---

## Available Commands (10)

| Command | Agent | Description |
|---------|-------|-------------|
| `/create-srs [project]` | doc-coordinator | Tạo SRS (phỏng vấn → UC diagrams → SRS) |
| `/create-sdd [project]` | doc-coordinator | Tạo SDD (yêu cầu SRS đã có) |
| `/generate-docs [project]` | doc-coordinator | Full pipeline: SRS + SDD + quality + RTM + test |
| `/interview-stakeholder` | doc-coordinator | Phỏng vấn stakeholder |
| `/create-srs-template` | any | Template SRS rỗng (IEEE) |
| `/create-sdd-template` | any | Template SDD rỗng (IEEE) |
| `/create-diagram` | any | Tạo UML từ mô tả text |
| `/update-srs [project] [--scope]` | doc-coordinator | Cập nhật SRS với delta changes + semantic versioning |
| `/update-sdd [project] [--scope]` | doc-coordinator | Cập nhật SDD với delta changes (component-level) |
| `/export-docs [project] [--format]` | doc-coordinator | Xuất docx/pdf/html/xlsx cho stakeholder |

---

## Spawn Architecture

```
doc-coordinator (primary)
├── @srs-agent
│     ├── @requirements-agent × N          (skill: requirements-gathering)
│     ├── @uc-diagram-agent × N            (skill: uml-design)
│     └── skill: srs-writing
│
├── @sdd-agent                             (cần SRS + requirements-summary + "XÁC NHẬN")
│     ├── @component-agent × N             (skill: component-design)
│     ├── @db-agent                        (skill: database-design)
│     ├── @api-agent                       (skill: api-design)
│     ├── skill: nfr-design
│     └── skill: sdd-writing
│
├── @integration-agent
│     ├── @quality-agent                   ← CHẠY ĐẦU TIÊN (quality gate)
│     ├── @traceability-agent              (skill: traceability)
│     └── @test-agent                      (skill: test-design)
│
└── @formatter-agent                       (on demand, qua /export-docs)
```

---

## Workflow: `/generate-docs` (full pipeline)

```
1. Project Setup
       └── Khởi tạo docs/{ProjectName}/

2. @srs-agent  — SRS Documentation
       ├── @requirements-agent × N → phỏng vấn (BẮT BUỘC "XÁC NHẬN")
       ├── @uc-diagram-agent × N   → 6 diagrams/UC
       └── skill: srs-writing      → SRS_{Project}_v1.0.0.md + requirements-summary.md

3. @sdd-agent  — SDD Documentation  (cần SRS verified)
       ├── @component-agent × N    → 5 artifacts/component
       ├── @db-agent & @api-agent  (song song)
       ├── skill: nfr-design       → NFR sections
       └── skill: sdd-writing      → SDD_{Project}_v1.0.0.md

4. @integration-agent  — Post-processing
       │
       ├── @quality-agent          ← CHẠY ĐẦU TIÊN
       │     ├── Dim 1: Artifact Coverage (glob checks)
       │     ├── Dim 2: Cross-document Consistency (SRS vs SDD)
       │     ├── Dim 3: Diagram Syntax (@startuml/@enduml, placeholders)
       │     ├── Dim 4: Content Completeness (no TBD/TODO)
       │     └── Output: quality-gate-report.md
       │
       │   IF FAIL → doc-coordinator RESPAWN agents cụ thể để fix
       │             RE-RUN quality-agent đến khi PASS/WARN
       │
       ├── (PASS/WARN) → index.md, CHANGELOG.md, MANIFEST.json, quality-report.md
       ├── @traceability-agent     → traceability/RTM.md + coverage.md
       └── @test-agent             → test-plan/TEST-PLAN.md, test-cases/, postman-collection.json, TEST-SUMMARY.md

5. (On demand) @formatter-agent  — qua /export-docs
       └── exports/{ProjectName}_docs_v{version}_{date}.zip
```

`/create-srs` chỉ chạy bước 1–2. `/create-sdd` chạy bước 3 (chặn nếu chưa có SRS). `/update-srs` và `/update-sdd` áp dụng delta changes + bump version + ghi `changes/`.

---

## Output Structure

```
docs/{ProjectName}/
├── requirements-summary.md              ← srs-agent
├── SRS_{ProjectName}_v{X.Y.Z}.md        ← srs-agent
├── SDD_{ProjectName}_v{X.Y.Z}.md        ← sdd-agent
├── index.md                             ← integration-agent
├── CHANGELOG.md                         ← integration-agent
├── MANIFEST.json                        ← integration-agent
├── quality-gate-report.md               ← quality-agent
│
├── diagrams/
│   ├── context-diagram.puml
│   ├── system-overview.puml
│   ├── deployment-{dev,staging,prod}.puml
│   ├── dfd-level1.puml
│   ├── entity-relationship.puml
│   ├── uc-01/ … uc-N/                   (6 diagrams mỗi UC)
│   └── components/
│       ├── component-interaction.puml
│       └── {component}/                 (class-backend, class-frontend, sequence, state)
│
├── db/
│   ├── schema.sql
│   └── migrations/
│
├── api/
│   ├── openapi.yaml
│   └── error-codes.md
│
├── traceability/
│   ├── RTM.md
│   └── coverage.md
│
├── test-plan/
│   ├── TEST-PLAN.md
│   ├── TEST-SUMMARY.md
│   ├── test-cases/TC-{UC-ID}-{name}.md
│   └── api-tests/postman-collection.json
│
├── changes/                             ← /update-srs · /update-sdd
│   └── change-{timestamp}-{scope}.md
│
└── exports/                             ← formatter-agent
    ├── SRS_{ProjectName}_v{version}.docx / .pdf
    ├── SDD_{ProjectName}_v{version}.docx / .pdf
    ├── RTM_{ProjectName}_v{version}.xlsx
    ├── index.html
    └── {ProjectName}_docs_v{version}_{date}.zip
```

---

## Templates

### SRS sections
1. Record of Changes
2. Product Overview
3. User Requirements (actors, use cases, Acceptance Criteria per UC)
4. Software Features
5. Non-Functional Requirements
6. Requirement Appendix (Business Rules, RTI)

### SDD sections
1. Introduction (+ Requirements Mapping)
2. System Architecture
3. Software Architecture Design
4. Detailed Component Design (+ Error Handling, Logging, Caching)
5. Database Design
6. API Design (+ Rate Limiting)
7. Security Design
8. Deployment Architecture
9. Reliability / Disaster Recovery (nfr-design)

---

## Quality Checklist

### SRS
- [ ] Tất cả stakeholders đã được phỏng vấn (kết thúc bằng "XÁC NHẬN")
- [ ] Tất cả actors & use cases đã được xác định
- [ ] Mỗi UC có ≥ 3 Acceptance Criteria (Given-When-Then)
- [ ] Functional requirements đầy đủ; NFR cụ thể, đo lường được
- [ ] Business rules đã ghi nhận; Section RTI (FR→UC) đã điền
- [ ] Mỗi UC có đủ 6 diagram files (link, không embed)

### SDD
- [ ] Architecture phù hợp với SRS requirements (Requirements Mapping)
- [ ] Tất cả components đã thiết kế chi tiết (5 artifacts)
- [ ] Database schema support tất cả data requirements
- [ ] API endpoints cover tất cả functional requirements (OpenAPI 3.0)
- [ ] UML diagrams chính xác và nhất quán
- [ ] Error handling, logging, caching, rate limiting đã định nghĩa
- [ ] Security & NFR (capacity, DR, i18n, accessibility) đã address

### Quality gate (quality-agent)
- [ ] Artifact coverage: mọi file bắt buộc tồn tại
- [ ] Cross-document consistency: SRS ↔ SDD không mâu thuẫn
- [ ] Diagram syntax: tất cả `.puml` có `@startuml`/`@enduml` hợp lệ
- [ ] Content completeness: không còn placeholder `[…]` / TBD / TODO

---

## Integration với agent host

| Host | Cách dùng |
|------|-----------|
| OpenCode | Copy vào `~/.config/opencode/skills/` |
| Claude Code | Copy vào `~/.claude/skills/` |
| Custom | Import theo file này — cấu hình `mode: primary`/`subagent`, permissions |
