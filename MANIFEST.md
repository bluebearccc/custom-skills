# AgentCode Framework — Manifest

## Tổng quan

AgentCode Framework là một AI agent coding framework để tự động hóa việc tạo tài liệu phần mềm (SRS, SDD, RTM, Test Plan) theo các chuẩn quốc tế (IEEE, ISO).

**Thành phần:** 13 agents · 10 skills · 10 commands. Chi tiết workflow xem [AGENTS.md](AGENTS.md).

## Framework Components

### 1. AGENTS (13)

| Agent | Type | Description |
|-------|------|-------------|
| `doc-coordinator.md` | PRIMARY | Điều phối toàn bộ quy trình |
| `srs-agent.md` | SUBAGENT | Coordinator SRS workflow |
| `sdd-agent.md` | SUBAGENT | Coordinator SDD workflow |
| `requirements-agent.md` | SUBAGENT | Thu thập requirements 1 actor/UC (multi-spawn) |
| `uc-diagram-agent.md` | SUBAGENT | Tạo 6 diagrams cho 1 UC (multi-spawn) |
| `component-agent.md` | SUBAGENT | Thiết kế 5 artifacts cho 1 component (multi-spawn) |
| `db-agent.md` | SUBAGENT | Schema tổng thể + ER diagram + DDL |
| `api-agent.md` | SUBAGENT | OpenAPI 3.0 spec + error codes |
| `integration-agent.md` | SUBAGENT | Index, consistency check, packaging |
| `traceability-agent.md` | SUBAGENT | RTM (FR→UC→Component→API→DB→Test) |
| `test-agent.md` | SUBAGENT | Test Plan + Test Cases + Postman |
| `quality-agent.md` | SUBAGENT | Quality gate: coverage, consistency, diagram syntax |
| `formatter-agent.md` | SUBAGENT | Convert markdown → docx/pdf/html/xlsx |

**Agent File Format** (YAML frontmatter):
```yaml
---
description: Mô tả agent
mode: subagent          # primary cho doc-coordinator
permission:
  edit: allow
  read: allow
  glob: allow
  grep: allow
---
System prompt here
```

### 2. SKILLS (10)

| Skill | Version | Description |
|-------|---------|-------------|
| `requirements-gathering` | 3.2.0 | Thu thập yêu cầu từ stakeholders (AskUserQuestion) |
| `srs-writing` | 4.0.0 | Viết tài liệu SRS + AC per UC + RTI |
| `sdd-writing` | 4.0.0 | Viết tài liệu SDD + error/logging/caching/rate-limit |
| `uml-design` | 3.0.0 | Tạo UML diagrams (use case, sequence, state, deployment, DFD) |
| `component-design` | 1.0.0 | 5 artifacts cho 1 component |
| `database-design` | 1.0.0 | Thiết kế database schema |
| `api-design` | 3.0.0 | Thiết kế REST API + OpenAPI 3.0 |
| `nfr-design` | 1.0.0 | Capacity, DR, i18n/l10n, accessibility |
| `traceability` | 1.0.0 | Tạo RTM + coverage report |
| `test-design` | 1.0.0 | Test Plan, Test Cases, Postman Collection |

### 3. COMMANDS (10)

| Command | Description |
|---------|-------------|
| `/create-srs` | Tạo SRS |
| `/create-sdd` | Tạo SDD |
| `/generate-docs` | Full pipeline (SRS + SDD + quality + RTM + test) |
| `/interview-stakeholder` | Phỏng vấn stakeholder |
| `/create-srs-template` | Tạo template SRS |
| `/create-sdd-template` | Tạo template SDD |
| `/create-diagram` | Tạo UML diagram |
| `/update-srs` | Cập nhật SRS (delta + semantic versioning) |
| `/update-sdd` | Cập nhật SDD (delta, component-level) |
| `/export-docs` | Xuất docx/pdf/html/xlsx |

## Quick Start

```
/create-srs ProjectName       # Phỏng vấn → SRS
/create-sdd ProjectName        # SRS đã có → SDD
/generate-docs ProjectName     # Toàn bộ pipeline
/export-docs ProjectName        # Xuất docx/pdf/html
```

## File Structure

Xem [AGENTS.md › Agent File Structure](AGENTS.md) cho cây thư mục đầy đủ.

```
agentcode-framework/
├── AGENTS.md            # Workflow & registry (canonical)
├── README.md            # Tổng quan
├── MANIFEST.md          # File này
├── agents/              # 13 agent definitions
├── skills/              # 10 skills (mỗi skill = SKILL.md)
├── commands/            # 10 slash commands
├── docs/                # Catalogues, user flow, question reviews
└── diagrams/workflow/   # PlantUML workflow diagrams
```

## Templates

### SRS Template Structure
```
I.  Record of Changes
II. Software Requirement Specification
    1. Product Overview
    2. User Requirements (+ Acceptance Criteria per UC)
    3. Software Features
    4. Non-Functional Requirements
    5. Requirement Appendix (+ RTI)
```

### SDD Template Structure
```
1. Introduction (+ Requirements Mapping)
2. System Architecture
3. Software Architecture Design
4. Detailed Component Design (+ Error/Logging/Caching)
5. Database Design
6. API Design (+ Rate Limiting)
7. Security Design
8. Deployment Architecture
9. Reliability / Disaster Recovery
```

## Integration

| Host | Cách dùng |
|------|-----------|
| OpenCode | Copy vào `~/.config/opencode/skills/` |
| Claude Code | Copy vào `~/.claude/skills/` |
| Custom Agent | Import theo hướng dẫn trong [AGENTS.md](AGENTS.md) |

## License

Proprietary — AgentCode Framework
