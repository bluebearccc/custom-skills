# AgentCode Framework

AI Agent Coding Framework với hệ thống **Agent**, **Skill**, **Command** để tự động hóa tạo tài liệu phần mềm (SRS, SDD, RTM, Test Plan) theo chuẩn IEEE / ISO.

## Tổng quan

| Thành phần | Vai trò |
|------------|---------|
| **Agents** | Điều phối workflow, spawn worker song song, gọi skills |
| **Skills** | Quy trình chi tiết (phỏng vấn, viết SRS/SDD, UML, DB, API, RTM, test) |
| **Commands** | Entry point cho người dùng (`/create-srs`, `/generate-docs`, …) |

**Luồng chính:** User → `doc-coordinator` → `@srs-agent` / `@sdd-agent` → worker agents → skills → artifacts trong `docs/{ProjectName}/`.

Chi tiết workflow: [AGENTS.md](AGENTS.md), [docs/user-flow.md](docs/user-flow.md), [MANIFEST.md](MANIFEST.md).

---

## Cấu trúc thư mục

```
agent-doc/
├── README.md                 # Tài liệu này
├── AGENTS.md                 # Workflow, @mention, quality checklist (canonical)
├── MANIFEST.md               # Manifest & quick start
├── srs-sdd-template-patches.md
├── agents/                   # 13 agent definitions
├── skills/                   # 10 skills (mỗi skill = SKILL.md)
├── commands/                 # 10 slash commands
├── docs/                     # Catalogues, user flow, question reviews
├── diagrams/workflow/        # PlantUML workflow diagrams
└── PlantUML/config.cfg       # PlantUML render config
```

---

## Agents (13)

### Primary & coordinators

| Agent | Mode | Mô tả | Gọi bởi |
|-------|------|--------|---------|
| `doc-coordinator` | primary | Điều phối toàn bộ SRS + SDD; xử lý commands; spawn sub-agents | User |
| `srs-agent` | subagent | Điều phối workflow SRS (phỏng vấn → diagrams → viết SRS) | doc-coordinator |
| `sdd-agent` | subagent | Điều phối workflow SDD (đọc SRS → components/DB/API → viết SDD) | doc-coordinator |

### Worker agents (SRS path)

| Agent | Spawn nhiều? | Mô tả | Gọi bởi |
|-------|----------------|--------|---------|
| `requirements-agent` | **Có** (theo actor/UC) | Thu thập requirements cho từng actor hoặc use case | srs-agent |
| `uc-diagram-agent` | **Có** (theo UC) | Tạo 6 PlantUML diagrams bắt buộc cho mỗi use case | srs-agent |

### Worker agents (SDD path)

| Agent | Spawn nhiều? | Mô tả | Gọi bởi |
|-------|----------------|--------|---------|
| `component-agent` | **Có** (theo component) | Thiết kế chi tiết 1 module: 5 artifacts (class BE/FE, sequence, state, SQL) | sdd-agent |
| `db-agent` | Không | Schema tổng thể, ER diagram, DDL, migrations | sdd-agent |
| `api-agent` | Không | REST API tổng thể: OpenAPI 3.0, error codes registry | sdd-agent |

### Post-processing (sau SRS + SDD)

| Agent | Spawn nhiều? | Mô tả | Gọi bởi |
|-------|----------------|--------|---------|
| `integration-agent` | Không | Cross-check consistency, `index.md`, changelog, package manifest, quality report | doc-coordinator |
| `quality-agent` | Không | Quality gate: artifact coverage, cross-doc consistency, diagram syntax, completeness | integration-agent |
| `traceability-agent` | Không | RTM: mapping FR → UC → Component → API → DB → Test | integration-agent |
| `test-agent` | Không | Test Plan, test cases theo UC, Postman collection từ OpenAPI | integration-agent |
| `formatter-agent` | Không | Export markdown → docx/pdf/html/xlsx (on demand) | doc-coordinator |

### Kiến trúc spawn (rút gọn)

```
doc-coordinator
├── @srs-agent
│     ├── @requirements-agent × N
│     ├── @uc-diagram-agent × N
│     └── skill: srs-writing
├── @sdd-agent          (cần SRS + requirements-summary + "XÁC NHẬN")
│     ├── @component-agent × N
│     ├── @db-agent & @api-agent
│     └── skill: sdd-writing
└── @integration-agent
      ├── @quality-agent        (quality gate — chạy đầu tiên)
      ├── @traceability-agent
      └── @test-agent
# @formatter-agent: gọi on demand qua /export-docs
```

**Nguyên tắc:** `doc-coordinator` không gọi skills trực tiếp — luôn ủy quyền qua sub-agents. Worker có thể spawn **song song** (`&` … `wait`).

---

## Skills (10)

| Skill | Version | Mô tả | Dùng bởi |
|-------|---------|--------|----------|
| `requirements-gathering` | 3.2.0 | Phỏng vấn bắt buộc từng câu (AskUserQuestion); đào sâu; kết thúc bằng "XÁC NHẬN" | srs-agent, `/interview-stakeholder` |
| `srs-writing` | 4.0.0 | Viết SRS IEEE; AC per UC + RTI; **link** PlantUML ngoài, không embed | srs-agent |
| `sdd-writing` | 4.0.0 | Viết SDD IEEE 1016; requirements mapping, error/logging/caching/rate-limit | sdd-agent |
| `uml-design` | 3.0.0 | Use case, screen flow, state, ERD, sequence, deployment, DFD, integration (PlantUML) | uc-diagram-agent, srs/sdd agents |
| `component-design` | 1.0.0 | 5 artifacts/component (class BE/FE, sequence, state, `*_tables.sql`) | component-agent |
| `database-design` | 1.0.0 | ER diagram, DDL, schema tổng thể | db-agent |
| `api-design` | 3.0.0 | OpenAPI 3.0, error codes, auth, versioning | api-agent |
| `nfr-design` | 1.0.0 | Capacity planning, DR, i18n/l10n, accessibility (WCAG) | sdd-agent |
| `traceability` | 1.0.0 | RTM + coverage report (`traceability/RTM.md`, `coverage.md`) | traceability-agent |
| `test-design` | 1.0.0 | TEST-PLAN, test cases/UC, Postman từ openapi.yaml | test-agent |

---

## Commands (10)

| Command | File | Mô tả | Agent xử lý |
|---------|------|--------|-------------|
| `/create-srs [project_name]` | `commands/create-srs.md` | Tạo SRS (phỏng vấn → UC diagrams → SRS) | doc-coordinator → srs-agent |
| `/create-sdd [project_name]` | `commands/create-sdd.md` | Tạo SDD (yêu cầu SRS đã có) | doc-coordinator → sdd-agent |
| `/generate-docs [project_name]` | `commands/generate-docs.md` | SRS + SDD + quality + RTM + test | doc-coordinator (full pipeline) |
| `/interview-stakeholder` | `commands/interview-stakeholder.md` | Phỏng vấn stakeholder (user/manager/dev) | doc-coordinator |
| `/create-srs-template` | `commands/create-srs-template.md` | Template SRS rỗng (IEEE) | any |
| `/create-sdd-template` | `commands/create-sdd-template.md` | Template SDD rỗng (IEEE) | any |
| `/create-diagram` | `commands/create-diagram.md` | Tạo UML từ mô tả text (use case, class, sequence, …) | any |
| `/update-srs [project] [--scope]` | `commands/update-srs.md` | Cập nhật SRS với delta changes + semantic versioning | doc-coordinator |
| `/update-sdd [project] [--scope]` | `commands/update-sdd.md` | Cập nhật SDD với delta changes (component-level) | doc-coordinator |
| `/export-docs [project] [--format]` | `commands/export-docs.md` | Xuất docx/pdf/html/xlsx cho stakeholder | doc-coordinator → formatter-agent |

### Quick start

```text
/create-srs MyProject
/create-sdd MyProject
/generate-docs MyProject
```

**Lưu ý:** Trước SRS, phỏng vấn requirements là **bắt buộc** — user phải trả lời đủ và gõ `XÁC NHẬN` trước khi tiếp tục. `/create-sdd` bị chặn nếu chưa có SRS.

---

## Output artifacts (theo project)

Sau khi chạy đầy đủ pipeline, thư mục dự kiến:

```
docs/{ProjectName}/
├── SRS_{Project}_v1.0.0.md
├── SDD_{Project}_v1.0.0.md
├── requirements-summary.md
├── requirements/              # Chi tiết phỏng vấn
├── interviews/                # interview_{name}_{date}.md
├── diagrams/                  # PlantUML per UC + system-level
├── api/
│   ├── openapi.yaml
│   └── error-codes.md
├── db/
│   └── schema.sql
├── traceability/
│   ├── RTM.md
│   └── coverage.md
├── test-plan/
│   ├── TEST-PLAN.md
│   ├── TEST-SUMMARY.md
│   ├── test-cases/TC-{UC-ID}-*.md
│   └── api-tests/postman-collection.json
├── index.md                   # integration-agent
├── CHANGELOG.md
├── MANIFEST.json
└── quality-report.md
```

---

## Tài liệu hỗ trợ (`docs/`)

| File | Mục đích |
|------|----------|
| `docs/user-flow.md` | User flow chi tiết cho từng command |
| `docs/SRS_Questions_Catalogue.md` | Catalogue câu hỏi SRS |
| `docs/SRS_Questions_Review.md` | Review câu hỏi SRS |
| `docs/SDD_Questions_Catalogue.md` | Catalogue câu hỏi SDD |
| `docs/SDD_Questions_Review.md` | Review câu hỏi SDD |

---

## Diagrams & PlantUML

| Vị trí | Nội dung |
|--------|----------|
| `diagrams/workflow/` | `agentcode-workflow.puml`, `agentcode-sequence.puml`, `agentcode-architecture.puml`, `mandatory-interview-flow.puml`, `mandatory-requirements-flow.puml`, `external-linking.puml`, `user-journey.puml` |
| `PlantUML/config.cfg` | Cấu hình render PlantUML |

**Quy ước UC diagrams (mỗi use case):** use case, screen flow, state, sequence, class backend, class frontend — do `uc-diagram-agent` + skill `uml-design`.

---

## Template SRS / SDD

### SRS (sections)

1. Record of Changes  
2. Product Overview  
3. User Requirements (actors, use cases)  
4. Software Features  
5. Non-Functional Requirements  
6. Requirement Appendix  

### SDD (sections)

1. Introduction  
2. System Architecture  
3. Software Architecture Design  
4. Detailed Component Design  
5. Database Design  
6. API Design  
7. Security Design  
8. Deployment Architecture  

Templates rỗng: `/create-srs-template`, `/create-sdd-template`. Bổ sung template: `srs-sdd-template-patches.md`.

---

## Tích hợp IDE / agent host

| Host | Cách dùng |
|------|-----------|
| OpenCode | Copy vào `~/.config/opencode/skills/` |
| Claude Code | Copy vào `~/.claude/skills/` |
| Custom | Import theo [AGENTS.md](AGENTS.md) — cấu hình `mode: primary` / `subagent`, permissions |

---

## File tham chiếu nhanh

| File | Khi nào đọc |
|------|----------------|
| [AGENTS.md](AGENTS.md) | Workflow, @mention, registry, quality checklist (canonical) |
| [MANIFEST.md](MANIFEST.md) | Component list, file structure tóm tắt |
| [docs/user-flow.md](docs/user-flow.md) | Luồng người dùng từng bước |

---

## License

Proprietary — AgentCode Framework
