# AgentCode Framework

An AI Agent Coding Framework with an **Agent**, **Skill**, **Command** system to automate the creation of software documentation (SRS, SDD, RTM, Test Plan) according to IEEE / ISO standards.

## Overview

| Component | Role |
|------------|---------|
| **Agents** | Coordinate the workflow, spawn workers in parallel, call skills |
| **Skills** | Detailed procedures (interviewing, writing SRS/SDD, UML, DB, API, RTM, test) |
| **Commands** | Entry points for the user (`/create-srs`, `/generate-docs`, …) |

**Main flow:** User → `doc-coordinator` → `@srs-agent` / `@sdd-agent` → worker agents → skills → artifacts in `docs/{ProjectName}/`.

Workflow details: [AGENTS.md](AGENTS.md), [docs/user-flow.md](docs/user-flow.md), [MANIFEST.md](MANIFEST.md).

---

## Directory Structure

```
agent-doc/
├── README.md                 # This document
├── AGENTS.md                 # Workflow, @mention, quality checklist (canonical)
├── MANIFEST.md               # Manifest & quick start
├── srs-sdd-template-patches.md
├── agents/                   # 13 agent definitions
├── skills/                   # 10 skills (each skill = SKILL.md)
├── commands/                 # 10 slash commands
├── docs/                     # Catalogues, user flow, question reviews
├── diagrams/workflow/        # PlantUML workflow diagrams
└── PlantUML/config.cfg       # PlantUML render config
```

---

## Agents (13)

### Primary & coordinators

| Agent | Mode | Description | Invoked by |
|-------|------|--------|---------|
| `doc-coordinator` | primary | Coordinates the whole SRS + SDD process; handles commands; spawns sub-agents | User |
| `srs-agent` | subagent | Coordinates the SRS workflow (interview → diagrams → write SRS) | doc-coordinator |
| `sdd-agent` | subagent | Coordinates the SDD workflow (reads SRS → components/DB/API → write SDD) | doc-coordinator |

### Worker agents (SRS path)

| Agent | Multi-spawn? | Description | Invoked by |
|-------|----------------|--------|---------|
| `requirements-agent` | **Yes** (per actor/UC) | Gathers requirements for each actor or use case | srs-agent |
| `uc-diagram-agent` | **Yes** (per UC) | Creates the 6 mandatory PlantUML diagrams for each use case | srs-agent |

### Worker agents (SDD path)

| Agent | Multi-spawn? | Description | Invoked by |
|-------|----------------|--------|---------|
| `component-agent` | **Yes** (per component) | Designs 1 module in detail: 5 artifacts (class BE/FE, sequence, state, SQL) | sdd-agent |
| `db-agent` | No | Overall schema, ER diagram, DDL, migrations | sdd-agent |
| `api-agent` | No | Overall REST API: OpenAPI 3.0, error codes registry | sdd-agent |

### Post-processing (after SRS + SDD)

| Agent | Multi-spawn? | Description | Invoked by |
|-------|----------------|--------|---------|
| `integration-agent` | No | Cross-checks consistency, `index.md`, changelog, package manifest, quality report | doc-coordinator |
| `quality-agent` | No | Quality gate: artifact coverage, cross-doc consistency, diagram syntax, completeness | integration-agent |
| `traceability-agent` | No | RTM: maps FR → UC → Component → API → DB → Test | integration-agent |
| `test-agent` | No | Test Plan, test cases per UC, Postman collection from OpenAPI | integration-agent |
| `formatter-agent` | No | Exports markdown → docx/pdf/html/xlsx (on demand) | doc-coordinator |

### Spawn architecture (simplified)

```
doc-coordinator
├── @srs-agent
│     ├── @requirements-agent × N
│     ├── @uc-diagram-agent × N
│     └── skill: srs-writing
├── @sdd-agent          (requires SRS + requirements-summary + "CONFIRM")
│     ├── @component-agent × N
│     ├── @db-agent & @api-agent
│     └── skill: sdd-writing
└── @integration-agent
      ├── @quality-agent        (quality gate — runs first)
      ├── @traceability-agent
      └── @test-agent
# @formatter-agent: called on demand via /export-docs
```

**Principle:** `doc-coordinator` does not call skills directly — it always delegates through sub-agents. Workers can spawn **in parallel** (`&` … `wait`).

---

## Skills (10)

| Skill | Version | Description | Used by |
|-------|---------|--------|----------|
| `requirements-gathering` | 4.0.0 | Mandatory 38-question / 12-step interview (AskUserQuestion); probes deeper; ends with "CONFIRM" | srs-agent, `/interview-stakeholder` |
| `srs-writing` | 4.0.0 | Writes IEEE SRS; AC per UC + RTI; **links** external PlantUML, not embedded | srs-agent |
| `sdd-writing` | 4.0.0 | Writes IEEE 1016 SDD; requirements mapping, error/logging/caching/rate-limit | sdd-agent |
| `uml-design` | 3.0.0 | Use case, screen flow, state, ERD, sequence, deployment, DFD, integration (PlantUML) | uc-diagram-agent, srs/sdd agents |
| `component-design` | 1.0.0 | 5 artifacts/component (class BE/FE, sequence, state, `*_tables.sql`) | component-agent |
| `database-design` | 1.0.0 | ER diagram, DDL, overall schema | db-agent |
| `api-design` | 3.0.0 | OpenAPI 3.0, error codes, auth, versioning | api-agent |
| `nfr-design` | 1.0.0 | Capacity planning, DR, i18n/l10n, accessibility (WCAG) | sdd-agent |
| `traceability` | 1.0.0 | RTM + coverage report (`traceability/RTM.md`, `coverage.md`) | traceability-agent |
| `test-design` | 1.0.0 | TEST-PLAN, test cases/UC, Postman from openapi.yaml | test-agent |

---

## Commands (10)

| Command | File | Description | Handling agent |
|---------|------|--------|-------------|
| `/create-srs [project_name]` | `commands/create-srs.md` | Creates SRS (interview → UC diagrams → SRS) | doc-coordinator → srs-agent |
| `/create-sdd [project_name]` | `commands/create-sdd.md` | Creates SDD (requires an existing SRS) | doc-coordinator → sdd-agent |
| `/generate-docs [project_name]` | `commands/generate-docs.md` | SRS + SDD + quality + RTM + test | doc-coordinator (full pipeline) |
| `/interview-stakeholder` | `commands/interview-stakeholder.md` | Interviews a stakeholder (user/manager/dev) | doc-coordinator |
| `/create-srs-template` | `commands/create-srs-template.md` | Empty SRS template (IEEE) | any |
| `/create-sdd-template` | `commands/create-sdd-template.md` | Empty SDD template (IEEE) | any |
| `/create-diagram` | `commands/create-diagram.md` | Creates UML from a text description (use case, class, sequence, …) | any |
| `/update-srs [project] [--scope]` | `commands/update-srs.md` | Updates SRS with delta changes + semantic versioning | doc-coordinator |
| `/update-sdd [project] [--scope]` | `commands/update-sdd.md` | Updates SDD with delta changes (component-level) | doc-coordinator |
| `/export-docs [project] [--format]` | `commands/export-docs.md` | Exports docx/pdf/html/xlsx for stakeholders | doc-coordinator → formatter-agent |

### Quick start

```text
/create-srs MyProject
/create-sdd MyProject
/generate-docs MyProject
```

**Note:** Before SRS, the requirements interview is **mandatory** — the user must answer all questions and type `CONFIRM` before continuing. `/create-sdd` is blocked if there is no SRS yet.

---

## Output artifacts (per project)

After running the full pipeline, the expected directory looks like:

```
docs/{ProjectName}/
├── SRS_{Project}_v1.0.0.md
├── SDD_{Project}_v1.0.0.md
├── requirements-summary.md
├── requirements/              # Interview details
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

## Supporting documentation (`docs/`)

| File | Purpose |
|------|----------|
| `docs/user-flow.md` | Detailed user flow for each command |
| `docs/SRS_Questions_Catalogue.md` | SRS question catalogue |
| `docs/SRS_Questions_Review.md` | SRS question review |
| `docs/SDD_Questions_Catalogue.md` | SDD question catalogue |
| `docs/SDD_Questions_Review.md` | SDD question review |

---

## Diagrams & PlantUML

| Location | Content |
|--------|----------|
| `diagrams/workflow/` | `agentcode-workflow.puml`, `agentcode-sequence.puml`, `agentcode-architecture.puml`, `mandatory-interview-flow.puml`, `mandatory-requirements-flow.puml`, `external-linking.puml`, `user-journey.puml` |
| `PlantUML/config.cfg` | PlantUML render configuration |

**UC diagram convention (per use case):** use case, screen flow, state, sequence, class backend, class frontend — produced by `uc-diagram-agent` + skill `uml-design`.

---

## SRS / SDD Templates

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

Empty templates: `/create-srs-template`, `/create-sdd-template`. Additional templates: `srs-sdd-template-patches.md`.

---

## IDE / agent host integration

This framework's files are written to work on **both OpenCode and Claude Code at the same time**, with no separate fork needed:
- `agents/*.md` carry both `name`/`description` (read by Claude Code) and `mode`/`permission` (read by OpenCode) — each host reads the fields it understands and ignores the rest.
- `commands/*.md` carry `agent: <name>`, which OpenCode uses directly to run the command as that agent; Claude Code honors the same field when paired with `context: fork` (also present), forking the command into that named subagent.
- `skills/*/SKILL.md` use the shared `name` / `description` / `allowed-tools` fields both hosts recognize natively.

Copy the whole `agents/`, `skills/`, and `commands/` folders into the matching directories below for whichever host(s) you use:

| Host | Agents | Skills | Commands |
|------|--------|--------|----------|
| OpenCode — global | `~/.config/opencode/agents/` | `~/.config/opencode/skills/` | `~/.config/opencode/commands/` |
| OpenCode — project | `.opencode/agents/` | `.opencode/skills/` | `.opencode/commands/` |
| Claude Code — global | `~/.claude/agents/` | `~/.claude/skills/` | `~/.claude/commands/` |
| Claude Code — project | `.claude/agents/` | `.claude/skills/` | `.claude/commands/` |
| Custom host | Import per [AGENTS.md](AGENTS.md) — configure `mode: primary` / `subagent`, permissions |

---

## Quick reference files

| File | When to read it |
|------|----------------|
| [AGENTS.md](AGENTS.md) | Workflow, @mention, registry, quality checklist (canonical) |
| [MANIFEST.md](MANIFEST.md) | Component list, condensed file structure |
| [docs/user-flow.md](docs/user-flow.md) | Step-by-step user flow |

---

## License

Proprietary — AgentCode Framework
</content>
