# AgentCode Framework — AGENTS.md

> Canonical workflow reference. The figures in this file reflect the **actual state**
> of the repo (13 agents · 10 skills · 10 commands). When adding/removing a component, update this file **and** `README.md` + `MANIFEST.md`.

## Framework Overview

AgentCode Framework is an AI agent coding framework for automatically generating software documentation (SRS, SDD, RTM, Test Plan) according to IEEE / ISO standards.

**Main flow:** User → `doc-coordinator` (primary) → `srs-agent` / `sdd-agent` (coordinators) → worker agents (spawned in parallel) → skills → artifacts in `docs/{ProjectName}/`.

**Core principles:**
- `doc-coordinator` **does not** call skills directly — always delegates through sub-agents.
- Worker agents can spawn **in parallel** (`&` … `wait`).
- Diagrams are **linked** (external `.puml`), not embedded into documents.
- Before SRS, the requirements interview is **mandatory** — the user must type `CONFIRM` before continuing.

---

## Agent File Structure

```
agentcode-framework/
├── AGENTS.md                            # This file — workflow & registry
├── README.md                           # Overview
├── MANIFEST.md                         # Manifest & quick start
│
├── agents/                             # 13 agent definitions
│   ├── doc-coordinator.md              # PRIMARY — leader
│   ├── srs-agent.md                    # SUBAGENT — SRS coordinator
│   ├── sdd-agent.md                    # SUBAGENT — SDD coordinator
│   ├── requirements-agent.md           # worker — multi-spawn (per actor/UC)
│   ├── uc-diagram-agent.md             # worker — multi-spawn (per UC)
│   ├── component-agent.md              # worker — multi-spawn (per component)
│   ├── db-agent.md                     # worker — singleton
│   ├── api-agent.md                    # worker — singleton
│   ├── integration-agent.md            # post-processing
│   ├── traceability-agent.md           # RTM
│   ├── test-agent.md                   # Test plan
│   ├── quality-agent.md                # quality gate
│   └── formatter-agent.md              # export docx/pdf/html/xlsx (on demand)
│
├── skills/                             # 10 skills (each skill = SKILL.md)
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
| `doc-coordinator` | primary | Leader — coordinates everything, handles commands | No | User |
| `srs-agent` | subagent | Coordinates the SRS workflow | No | doc-coordinator |
| `sdd-agent` | subagent | Coordinates the SDD workflow | No | doc-coordinator |
| `requirements-agent` | subagent | Gathers requirements for 1 actor/UC | **YES** | srs-agent |
| `uc-diagram-agent` | subagent | Creates 6 diagrams for 1 UC | **YES** | srs-agent |
| `component-agent` | subagent | Designs 5 artifacts for 1 component | **YES** | sdd-agent |
| `db-agent` | subagent | Overall schema + ER diagram + DDL | No | sdd-agent |
| `api-agent` | subagent | OpenAPI 3.0 spec + error codes | No | sdd-agent |
| `integration-agent` | subagent | Index, consistency check, packaging | No | doc-coordinator |
| `traceability-agent` | subagent | RTM (FR→UC→Component→API→DB→Test) | No | integration-agent |
| `test-agent` | subagent | Test Plan + Test Cases + Postman | No | integration-agent |
| `quality-agent` | subagent | Quality gate: coverage, consistency, diagram syntax | No | integration-agent |
| `formatter-agent` | subagent | Converts markdown → docx/pdf/html/xlsx | No | doc-coordinator (on demand) |

### Agent configuration (frontmatter) — dual-host

Every file in `agents/` carries both OpenCode's and Claude Code's recognized fields side by side; each host reads what it understands and ignores the rest. `doc-coordinator` uses `mode: primary`; all other agents use `mode: subagent` (this `mode` distinction is OpenCode-only — Claude Code has no file-defined "primary" agent, since the main conversation itself is the orchestrator; `doc-coordinator` is simply the subagent that commands fork into).

```yaml
---
name: <agent-slug>       # required by Claude Code (~/.claude/agents/*.md); ignored by OpenCode
description: <agent description>
mode: subagent           # OpenCode: or primary for doc-coordinator; ignored by Claude Code
permission:
  edit: allow
  read: allow
  glob: allow
  grep: allow
  bash: allow            # only formatter-agent needs bash (pandoc/libreoffice)
---
```

`tools:`/`disallowedTools:` are intentionally omitted — Claude Code then lets the subagent inherit the host's full tool set, which lines up with the `permission: allow` grants above. (Do not add a Claude Code–style `tools:` list here: OpenCode has its own deprecated `tools:` field with an incompatible shape — a `{toolName: boolean}` map instead of a list — and reusing the key would collide.)

`doc-coordinator` calls sub-agents via **@mention** (e.g. `@srs-agent`). Workers are spawned in parallel with `&` … `wait`.

---

## Available Skills (10)

| Skill | Version | Purpose | Location |
|-------|---------|---------|----------|
| `requirements-gathering` | 4.0.0 | Mandatory 38-question / 12-step interview (AskUserQuestion), ending with "CONFIRM" | skills/requirements-gathering/SKILL.md |
| `srs-writing` | 4.0.0 | Writes IEEE SRS; AC per UC + RTI; links external PlantUML | skills/srs-writing/SKILL.md |
| `sdd-writing` | 4.0.0 | Writes IEEE 1016 SDD; requirements mapping, error/logging/caching/rate-limit | skills/sdd-writing/SKILL.md |
| `uml-design` | 3.0.0 | Use case, screen flow, state, ERD, sequence, deployment, DFD, integration | skills/uml-design/SKILL.md |
| `component-design` | 1.0.0 | 5 artifacts/component (class BE/FE, sequence, state, `*_tables.sql`) | skills/component-design/SKILL.md |
| `database-design` | 1.0.0 | ER diagram, DDL, overall schema | skills/database-design/SKILL.md |
| `api-design` | 3.0.0 | OpenAPI 3.0, error codes, auth, versioning | skills/api-design/SKILL.md |
| `nfr-design` | 1.0.0 | Capacity planning, DR, i18n/l10n, accessibility (WCAG) | skills/nfr-design/SKILL.md |
| `traceability` | 1.0.0 | RTM + coverage report | skills/traceability/SKILL.md |
| `test-design` | 1.0.0 | TEST-PLAN, test cases/UC, Postman from openapi.yaml | skills/test-design/SKILL.md |

---

## Available Commands (10)

| Command | Agent | Description |
|---------|-------|-------------|
| `/create-srs [project]` | doc-coordinator | Creates SRS (interview → UC diagrams → SRS) |
| `/create-sdd [project]` | doc-coordinator | Creates SDD (requires an existing SRS) |
| `/generate-docs [project]` | doc-coordinator | Full pipeline: SRS + SDD + quality + RTM + test |
| `/interview-stakeholder` | doc-coordinator | Interviews a stakeholder |
| `/create-srs-template` | any | Empty SRS template (IEEE) |
| `/create-sdd-template` | any | Empty SDD template (IEEE) |
| `/create-diagram` | any | Creates UML from a text description |
| `/update-srs [project] [--scope]` | doc-coordinator | Updates SRS with delta changes + semantic versioning |
| `/update-sdd [project] [--scope]` | doc-coordinator | Updates SDD with delta changes (component-level) |
| `/export-docs [project] [--format]` | doc-coordinator | Exports docx/pdf/html/xlsx for stakeholders |

---

## Spawn Architecture

```
doc-coordinator (primary)
├── @srs-agent
│     ├── @requirements-agent × N          (skill: requirements-gathering)
│     ├── @uc-diagram-agent × N            (skill: uml-design)
│     └── skill: srs-writing
│
├── @sdd-agent                             (requires SRS + requirements-summary + "CONFIRM")
│     ├── @component-agent × N             (skill: component-design)
│     ├── @db-agent                        (skill: database-design)
│     ├── @api-agent                       (skill: api-design)
│     ├── skill: nfr-design
│     └── skill: sdd-writing
│
├── @integration-agent
│     ├── @quality-agent                   ← RUNS FIRST (quality gate)
│     ├── @traceability-agent              (skill: traceability)
│     └── @test-agent                      (skill: test-design)
│
└── @formatter-agent                       (on demand, via /export-docs)
```

---

## Workflow: `/generate-docs` (full pipeline)

```
1. Project Setup
       └── Initializes docs/{ProjectName}/

2. @srs-agent  — SRS Documentation
       ├── @requirements-agent × N → interview (REQUIRES "CONFIRM")
       ├── @uc-diagram-agent × N   → 6 diagrams/UC
       └── skill: srs-writing      → SRS_{Project}_v1.0.0.md + requirements-summary.md

3. @sdd-agent  — SDD Documentation  (requires verified SRS)
       ├── @component-agent × N    → 5 artifacts/component
       ├── @db-agent & @api-agent  (in parallel)
       ├── skill: nfr-design       → NFR sections
       └── skill: sdd-writing      → SDD_{Project}_v1.0.0.md

4. @integration-agent  — Post-processing
       │
       ├── @quality-agent          ← RUNS FIRST
       │     ├── Dim 1: Artifact Coverage (glob checks)
       │     ├── Dim 2: Cross-document Consistency (SRS vs SDD)
       │     ├── Dim 3: Diagram Syntax (@startuml/@enduml, placeholders)
       │     ├── Dim 4: Content Completeness (no TBD/TODO)
       │     └── Output: quality-gate-report.md
       │
       │   IF FAIL → doc-coordinator RESPAWNS the specific agents to fix issues
       │             RE-RUNS quality-agent until PASS/WARN
       │
       ├── (PASS/WARN) → index.md, CHANGELOG.md, MANIFEST.json, quality-report.md
       ├── @traceability-agent     → traceability/RTM.md + coverage.md
       └── @test-agent             → test-plan/TEST-PLAN.md, test-cases/, postman-collection.json, TEST-SUMMARY.md

5. (On demand) @formatter-agent  — via /export-docs
       └── exports/{ProjectName}_docs_v{version}_{date}.zip
```

`/create-srs` only runs steps 1–2. `/create-sdd` runs step 3 (blocked if there is no SRS yet). `/update-srs` and `/update-sdd` apply delta changes + bump the version + write to `changes/`.

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
│   ├── uc-01/ … uc-N/                   (6 diagrams per UC)
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
- [ ] All stakeholders have been interviewed (ending with "CONFIRM")
- [ ] All actors & use cases have been identified
- [ ] Each UC has ≥ 3 Acceptance Criteria (Given-When-Then)
- [ ] Functional requirements are complete; NFRs are specific and measurable
- [ ] Business rules have been recorded; the RTI section (FR→UC) has been filled in
- [ ] Each UC has all 6 diagram files (linked, not embedded)

### SDD
- [ ] Architecture aligns with SRS requirements (Requirements Mapping)
- [ ] All components have been designed in detail (5 artifacts)
- [ ] Database schema supports all data requirements
- [ ] API endpoints cover all functional requirements (OpenAPI 3.0)
- [ ] UML diagrams are accurate and consistent
- [ ] Error handling, logging, caching, rate limiting have been defined
- [ ] Security & NFR (capacity, DR, i18n, accessibility) have been addressed

### Quality gate (quality-agent)
- [ ] Artifact coverage: every required file exists
- [ ] Cross-document consistency: no contradictions between SRS ↔ SDD
- [ ] Diagram syntax: all `.puml` files have valid `@startuml`/`@enduml`
- [ ] Content completeness: no remaining `[…]` placeholders / TBD / TODO

---

## Integration with the agent host

The repo works unmodified on both OpenCode and Claude Code — see [README.md § IDE / agent host integration](README.md#ide--agent-host-integration) for the exact copy targets (`agents/`, `skills/`, `commands/` each have a global and project-level path per host). For a custom host, import per this file — configure `mode: primary`/`subagent`, permissions.
</content>