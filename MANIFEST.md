# AgentCode Framework — Manifest

## Overview

AgentCode Framework is an AI agent coding framework for automating the creation of software documentation (SRS, SDD, RTM, Test Plan) following international standards (IEEE, ISO).

**Components:** 13 agents · 10 skills · 10 commands. See [AGENTS.md](AGENTS.md) for the full workflow.

## Framework Components

### 1. AGENTS (13)

| Agent | Type | Description |
|-------|------|-------------|
| `doc-coordinator.md` | PRIMARY | Coordinates the entire process |
| `srs-agent.md` | SUBAGENT | SRS workflow coordinator |
| `sdd-agent.md` | SUBAGENT | SDD workflow coordinator |
| `requirements-agent.md` | SUBAGENT | Gathers requirements for 1 actor/UC (multi-spawn) |
| `uc-diagram-agent.md` | SUBAGENT | Generates 6 diagrams for 1 UC (multi-spawn) |
| `component-agent.md` | SUBAGENT | Designs 5 artifacts for 1 component (multi-spawn) |
| `db-agent.md` | SUBAGENT | Overall schema + ER diagram + DDL |
| `api-agent.md` | SUBAGENT | OpenAPI 3.0 spec + error codes |
| `integration-agent.md` | SUBAGENT | Index, consistency check, packaging |
| `traceability-agent.md` | SUBAGENT | RTM (FR→UC→Component→API→DB→Test) |
| `test-agent.md` | SUBAGENT | Test Plan + Test Cases + Postman |
| `quality-agent.md` | SUBAGENT | Quality gate: coverage, consistency, diagram syntax |
| `formatter-agent.md` | SUBAGENT | Convert markdown → docx/pdf/html/xlsx |

**Agent File Format** (YAML frontmatter):
```yaml
---
description: Agent description
mode: subagent          # primary for doc-coordinator
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
| `requirements-gathering` | 3.2.0 | Gathers requirements from stakeholders (AskUserQuestion) |
| `srs-writing` | 4.0.0 | Writes the SRS document + AC per UC + RTI |
| `sdd-writing` | 4.0.0 | Writes the SDD document + error/logging/caching/rate-limit |
| `uml-design` | 3.0.0 | Generates UML diagrams (use case, sequence, state, deployment, DFD) |
| `component-design` | 1.0.0 | 5 artifacts for 1 component |
| `database-design` | 1.0.0 | Designs the database schema |
| `api-design` | 3.0.0 | Designs the REST API + OpenAPI 3.0 |
| `nfr-design` | 1.0.0 | Capacity, DR, i18n/l10n, accessibility |
| `traceability` | 1.0.0 | Generates the RTM + coverage report |
| `test-design` | 1.0.0 | Test Plan, Test Cases, Postman Collection |

### 3. COMMANDS (10)

| Command | Description |
|---------|-------------|
| `/create-srs` | Generate the SRS |
| `/create-sdd` | Generate the SDD |
| `/generate-docs` | Full pipeline (SRS + SDD + quality + RTM + test) |
| `/interview-stakeholder` | Interview a stakeholder |
| `/create-srs-template` | Generate the SRS template |
| `/create-sdd-template` | Generate the SDD template |
| `/create-diagram` | Generate a UML diagram |
| `/update-srs` | Update the SRS (delta + semantic versioning) |
| `/update-sdd` | Update the SDD (delta, component-level) |
| `/export-docs` | Export to docx/pdf/html/xlsx |

## Quick Start

```
/create-srs ProjectName       # Interview → SRS
/create-sdd ProjectName        # SRS already exists → SDD
/generate-docs ProjectName     # Full pipeline
/export-docs ProjectName        # Export docx/pdf/html
```

## File Structure

See [AGENTS.md › Agent File Structure](AGENTS.md) for the full directory tree.

```
agentcode-framework/
├── AGENTS.md            # Workflow & registry (canonical)
├── README.md            # Overview
├── MANIFEST.md          # This file
├── agents/              # 13 agent definitions
├── skills/              # 10 skills (each skill = SKILL.md)
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

| Host | Usage |
|------|-----------|
| OpenCode | Copy into `~/.config/opencode/skills/` |
| Claude Code | Copy into `~/.claude/skills/` |
| Custom Agent | Import following the instructions in [AGENTS.md](AGENTS.md) |

## License

Proprietary — AgentCode Framework
