---
name: update-sdd
description: Update an existing SDD — add/modify components, schema, API with semantic versioning and change tracking
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

## Purpose
Update an **existing SDD document** with delta changes — add/modify components, API endpoints, or DB schema without regenerating from scratch.

## Prerequisites

```
✅ docs/{ProjectName}/SDD_{ProjectName}_v*.md MUST exist
✅ docs/{ProjectName}/SRS_{ProjectName}_v*.md MUST exist
✅ docs/{ProjectName}/api/openapi.yaml MUST exist
✅ docs/{ProjectName}/db/schema.sql MUST exist

IF MISSING → Instruct the user to run /create-sdd {ProjectName} first
```

---

## Scope Options

| Scope | Meaning | Version Bump | Agents respawned |
|-------|---------|--------------|-----------------|
| `component={Name}` | Modify/add a specific component | MINOR | @component-agent(Name) |
| `api` | Add/modify API endpoints | MINOR | @api-agent |
| `db` | Add/modify DB tables/schema | MINOR | @db-agent |
| `nfr` | Modify NFR design decisions | PATCH | — (direct edit) |
| `security` | Update security design | PATCH | — (direct edit) |
| `architecture` | Change the architecture | MAJOR | @sdd-agent (full) |
| (none) | Review everything and determine scope | varies | interactive |

---

## Process

### Step 1: Determine current version and scope

```
Read SDD_{ProjectName}_v{X.Y.Z}.md
Display:
  "📄 Current SDD: v{X.Y.Z} ({date})
   
   Which part do you want to update?
   A) A specific component (add/modify diagrams + code structure)
   B) API endpoints (add/modify openapi.yaml)
   C) Database schema (add table/column)
   D) NFR/Security/Architecture
   E) Everything (major revision — review all)"
```

### Step 2: Gather the delta (scope-specific)

**If scope = component:**
```
"Which component needs updating? (exact name)"
"What is the change?"
  → Add a new class/method?
  → Modify a sequence flow?
  → Add a DB table?
  → Modify a state machine?
"Does it affect any other component?"
```

**If scope = api:**
```
"New endpoint, or which endpoint needs to change?"
"How do the Method + Path + Request/Response change?"
"Is there a breaking change? (response format/field name changes)"
  → If breaking: Automatically bump the MAJOR version
"Which UC triggers this new endpoint?"
```

**If scope = db:**
```
"Which table needs to be added/modified?"
"New column, or a column that changed?"
"Is a migration required? (existing data handling)"
"Any new index to add?"
```

### Step 3: Create a change record

```
docs/{ProjectName}/changes/
└── change-{timestamp}-sdd-{scope}.md

Contents:
  - Change ID: CHG-SDD-{YYYYMMDD}-{seq}
  - Type: ADD/MODIFY/REMOVE
  - Scope: component | api | db | nfr
  - Breaking change: yes/no
  - Affected files: list
  - Version: {old} → {new}
  - Migration required: yes/no
```

### Step 4: Apply delta changes (scope-specific agents)

```
IF scope == "component":
  @component-agent (component={Name}, mode=UPDATE)
  → Read the existing diagrams
  → Update only the changed files
  → Leave unaffected diagrams unchanged

IF scope == "api":
  @api-agent (mode=UPDATE)
  → Read the existing openapi.yaml
  → Add/modify the specific paths + schemas
  → Update error-codes.md if there are new codes

IF scope == "db":
  @db-agent (mode=UPDATE)
  → Read the existing schema.sql
  → Add ALTER TABLE or new CREATE TABLE statements
  → Generate migration file V{version}__update.sql
  → Update the ER diagram

IF scope IN ["nfr", "security", "architecture"]:
  → Directly edit the corresponding sections in the SDD
```

### Step 5: Breaking Change Detection

```
IF the API endpoint response schema changes (field removed/renamed):
  → WARN: "⚠️ Breaking change detected!"
  → Prompt user: "Bump MAJOR version? (1.2.0 → 2.0.0)"
  → Suggest adding a deprecation notice in openapi.yaml

IF a db column is removed/type changed:
  → WARN: "⚠️ DB breaking change!"
  → Require writing a migration script to handle existing data
  → Automatically generate: db/migrations/V{version}__breaking_change.sql
```

### Step 6: RTM + Test Plan sync

```
IF the scope changes UCs or APIs:
  → Flag that RTM.md needs updating (if it exists):
    "⚠️ The Traceability Matrix may be outdated.
     Run /update-traceability {ProjectName} to sync."

IF the scope changes APIs:
  → Flag that postman-collection.json needs updating:
    "⚠️ API tests may be outdated.
     Run /update-tests {ProjectName} to sync."
```

### Step 7: Version bump and CHANGELOG

```
Rename: SDD_{ProjectName}_v{X.Y.Z}.md → SDD_{ProjectName}_v{X'.Y'.Z'}.md

Update the Record of Changes table:
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

### Step 8: Re-run quality-agent

```
@quality-agent → validate consistency
IF PASS → Update index.md, MANIFEST.json with the new version
IF FAIL → Fix → retry
```

---

## Output

```
docs/{ProjectName}/
├── SDD_{ProjectName}_v{X'.Y'.Z'}.md    ← Updated SDD
├── api/openapi.yaml                      ← Updated (if api scope)
├── db/schema.sql                         ← Updated (if db scope)
├── db/migrations/
│   └── V{version}__update.sql           ← New migration (if db scope)
├── CHANGELOG.md                          ← Updated
└── changes/
    └── change-{timestamp}-sdd-{scope}.md ← Delta record
```

---

## Usage Example

```
User: /update-sdd MyProject --scope component=PaymentModule

Agent: 📄 Current SDD: v1.2.0 (2026-05-01)
       Updating component: PaymentModule

       What is the change?

User: Need to add a refund feature — add UC09-Refund to PaymentModule

Agent: ✅ Scope confirmed:
       - Component: PaymentModule
       - Change: Add Refund flow
       - Files to update: sequence.puml, state.puml, class-backend.puml
       - DB: add refunds table
       - API: add POST /api/v1/orders/{id}/refund
       - Version: v1.2.0 → v1.3.0

       Type CONFIRM to proceed...
```
