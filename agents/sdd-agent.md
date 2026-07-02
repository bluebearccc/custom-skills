---
description: Agent that orchestrates the entire SDD document generation workflow
mode: subagent
permission:
  edit: allow
  read: allow
  glob: allow
  grep: allow
---

# SDD Agent

## Purpose
SDD Agent is responsible for orchestrating the entire **Software Design Document (SDD)** generation workflow — from reading the SRS, designing the architecture, spawning component workers, through to writing the complete document following the IEEE 1016-2009 standard.

## Trigger
Spawned by `doc-coordinator` when:
- Receiving the `/create-sdd` command (SRS already exists)
- Receiving the `/generate-docs` command (after SRS Phase 1 is complete)

## Prerequisites

```
╔══════════════════════════════════════════════════════════════╗
║  ⛔ MUST NOT START IF:                                     ║
║     - SRS_{ProjectName}_v*.md DOES NOT YET EXIST           ║
║     - requirements-summary.md DOES NOT YET EXIST           ║
║  → Report an error to doc-coordinator immediately          ║
╚══════════════════════════════════════════════════════════════╝
```

## Skills Used
| Skill | When to call |
|-------|-------------|
| `uml-design` | Phase 1 — creates architecture-level diagrams |
| `database-design` | Phase 2 — creates the overall ER diagram + schema.sql |
| `api-design` | Phase 2 — creates the overall openapi.yaml |
| `nfr-design` | Phase 3 — creates NFR sections (capacity, DR, i18n, accessibility) from the NFRs in the SRS |
| `sdd-writing` | Phase 3 — writes the final SDD document |

## Agents Spawned
| Agent | Count | When |
|-------|----------|---------|
| `@component-agent` | N (1 per component) | Phase 2, in parallel |
| `@db-agent` | 1 | Phase 2, in parallel with component-agents |
| `@api-agent` | 1 | Phase 2, in parallel with component-agents |

---

## 3-Phase Process

### Phase 1: READ & ANALYZE THE SRS

**Step 1 — Verify prerequisites:**
```
Check that the following files exist:
✅ docs/{ProjectName}/SRS_{ProjectName}_v*.md
✅ docs/{ProjectName}/requirements-summary.md

IF MISSING → Return an error:
  "⛔ The SDD requires a complete SRS. SRS_{ProjectName} was not found.
   Please run /create-srs {ProjectName} first."
```

**Step 2 — Read and analyze:**
```
Read requirements-summary.md → extract:
  - Project name
  - Actors list
  - Use Cases list (UC01, UC02, ...)
  - Features list (MoSCoW)
  - Data entities list
  - Non-Functional Requirements
  - Technology stack (if available)

From Features/UCs → determine Components:
  Example mapping:
  UC-Login + UC-Register → AuthModule
  UC-ViewProduct + UC-SearchProduct → ProductModule
  UC-PlaceOrder + UC-ViewOrder → OrderModule
  UC-Payment + UC-Refund → PaymentModule
  ...

⚠️ The COMPONENT LIST is DYNAMIC — do not hardcode it
   Read the SRS to determine the actual N components
```

**Step 3 — Design the architecture (call the uml-design skill):**
```
skill({ name: "uml-design", type: "architecture" })

Output:
- diagrams/system-overview.puml       (if not already produced from the SRS)
- diagrams/layered-architecture.puml  (if not already produced)
- diagrams/deployment.puml            (if not already produced)
- diagrams/integration.puml           (if not already produced)
- diagrams/components/component-interaction.puml  ← NEW from the SDD
```

---

### Phase 2: DETAILED DESIGN (Parallel)

**Spawn all workers IN PARALLEL:**

```
# Spawn N component-agents (dynamic based on the SRS)
@component-agent (component=AuthModule, project={ProjectName}) &
@component-agent (component=ProductModule, project={ProjectName}) &
@component-agent (component=OrderModule, project={ProjectName}) &
@component-agent (component=...N, project={ProjectName}) &

# Spawn db-agent and api-agent simultaneously
@db-agent (project={ProjectName}) &
@api-agent (project={ProjectName}) &

wait  # Wait for ALL agents to complete
```

**Verification (Quality Gate) after all have completed:**
```
For EACH component in the list:
  glob("diagrams/components/{component}/*.puml") → must have all 4 files:
    - {component}-class-backend.puml
    - {component}-class-frontend.puml
    - {component}-sequence.puml
    - {component}-state.puml
  glob("db/tables/{component}_tables.sql") → must exist

Check db-agent output:
  glob("db/schema.sql") → must exist
  glob("diagrams/entity-relationship.puml") → must exist

Check api-agent output:
  glob("api/openapi.yaml") → must exist

IF ANY file is MISSING → Respawn the corresponding agent, re-verify
```

---

### Phase 3: WRITE THE SDD DOCUMENT

```
┌─────────────────────────────────────────────────────────────┐
│  PHASE 3 — SDD WRITING                                     │
├─────────────────────────────────────────────────────────────┤
│  1. Read SRS_{ProjectName}_v*.md                            │
│  2. Read requirements-summary.md                            │
│  3. Read all diagrams created so far (glob)                 │
│  4. Read db/schema.sql                                      │
│  5. Read api/openapi.yaml                                   │
│  6. Call the nfr-design skill → NFR sections (from SRS NFRs)│
│  7. Call the sdd-writing skill (insert NFR sections into SDD)│
│  8. Create SDD_{ProjectName}_v1.0.0.md                      │
│  9. Update index.md                                         │
│ 10. Report results to doc-coordinator                       │
└─────────────────────────────────────────────────────────────┘
```

---

## Output Structure

```
docs/{ProjectName}/
├── SDD_{ProjectName}_v1.0.0.md         ⭐ Main output
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

| Situation | Action |
|------------|-----------|
| SRS does not exist | Return an error immediately, require /create-srs to run first |
| Component list is empty after reading the SRS | Ask the user for manual confirmation |
| Missing artifacts after spawn | Respawn the specific agent, re-verify |
| db-agent fails | Log the error, try creating a basic schema from the data entities in the SRS |
| api-agent fails | Log the error, try creating a basic OpenAPI spec from the use cases |

---

## Result Report for doc-coordinator

After completion:
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
