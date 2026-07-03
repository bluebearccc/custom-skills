---
name: update-srs
description: Update an existing SRS — add/modify/remove requirements with semantic versioning and change tracking
syntax: /update-srs [project_name] [--scope <scope>]
examples:
  - "/update-srs MyProject"
  - "/update-srs MyProject --scope uc"
  - "/update-srs MyProject --scope nfr"
  - "/update-srs MyProject --scope feature"
agent: doc-coordinator
context: fork
---

# Command: /update-srs

## Purpose
Update an **existing SRS document** with delta changes — without regenerating from scratch. Automatically bumps the version according to semantic versioning and records the CHANGELOG.

## Prerequisites

```
✅ docs/{ProjectName}/SRS_{ProjectName}_v*.md MUST exist
✅ docs/{ProjectName}/requirements-summary.md MUST exist

IF MISSING → Instruct the user to run /create-srs {ProjectName} first
```

---

## Scope Options

| Scope | Meaning | Version Bump |
|-------|---------|--------------|
| `uc` | Add/modify/remove a Use Case | MINOR (1.0.0 → 1.1.0) |
| `feature` | Add/modify a Feature, FR | MINOR (1.0.0 → 1.1.0) |
| `nfr` | Modify Non-Functional Requirements | PATCH (1.0.0 → 1.0.1) |
| `actor` | Add/modify an Actor | MINOR (1.0.0 → 1.1.0) |
| `fix` | Fix typos, clarification | PATCH (1.0.0 → 1.0.1) |
| (none) | Full interview — major revision | MAJOR (1.0.0 → 2.0.0) |

---

## Process

### Step 1: Determine the current version

```
Read SRS_{ProjectName}_v{X.Y.Z}.md
Extract the current version from the frontmatter or filename
Display:
  "📄 Current SRS: v{X.Y.Z} ({date})
   Scope: {scope}
   Version after update: v{X'.Y'.Z'}"
```

### Step 2: Gather delta changes (no full re-interview)

```
Show the user:
╔══════════════════════════════════════════════════════════════╗
║  📝 UPDATE SRS — {ProjectName} v{X.Y.Z} → v{X'.Y'.Z'}      ║
║                                                              ║
║  Only ask about NEW changes or things that NEED FIXING.    ║
║  No need to re-answer what already exists.                 ║
╚══════════════════════════════════════════════════════════════╝

Question 1: What do you want to ADD / MODIFY / REMOVE?
  → Describe the change

Question 2 (if scope=uc): Which use case is affected?
  → List of UC IDs

Question 3: Are any other requirements affected by this change?
  → Cross-impact analysis

Question 4: New/updated Acceptance Criteria for the changed UC?

CONFIRM: "Type CONFIRM to proceed with the update"
```

### Step 3: Create a change record

```
Create a change record before making edits:

docs/{ProjectName}/changes/
└── change-{timestamp}-{scope}.md

Contents:
  - Change ID: CHG-{YYYYMMDD}-{seq}
  - Timestamp
  - Scope
  - Description
  - Affected sections
  - Version: {old} → {new}
  - Author: user/agent
```

### Step 4: Apply delta changes

```
IF scope == "uc" OR "actor" OR "feature":
  → Spawn @srs-agent with mode=UPDATE
  → srs-agent reads the existing file
  → Only UPDATE the affected sections
  → Leave unrelated sections unchanged
  → Spawn @uc-diagram-agent ONLY for the changed UCs

IF scope == "nfr" OR "fix":
  → Edit the specific sections directly
  → No need to spawn uc-diagram-agent
```

### Step 5: Version bump and CHANGELOG

```
Rename file:
  SRS_{ProjectName}_v{X.Y.Z}.md → SRS_{ProjectName}_v{X'.Y'.Z'}.md

Update the Record of Changes table (top of the document):
  | v{X'.Y'.Z'} | {date} | M | AgentCode | {change description} |

Update CHANGELOG.md:
  ## v{X'.Y'.Z'} — {date}
  ### Changed
  - {description of change}
  ### Added (if any)
  - {new items}
  ### Removed (if any)
  - {removed items}
```

### Step 6: Re-run quality-agent

```
@quality-agent → check that the new SRS has no inconsistencies
If PASS → Update index.md, MANIFEST.json
If FAIL → Fix and retry
```

---

## Semantic Versioning Rules

```
MAJOR.MINOR.PATCH

MAJOR bump (X.0.0):
  - Fundamental change to the project scope
  - Remove a large UC/Feature
  - Change the core architecture

MINOR bump (X.Y.0):
  - Add a new UC
  - Add a new Actor
  - Add a new Feature/FR
  - Significant AC changes

PATCH bump (X.Y.Z):
  - Fix a description (clarification)
  - Update NFR values
  - Fix typos
  - Minor wording improvements
```

---

## Output

```
docs/{ProjectName}/
├── SRS_{ProjectName}_v{X'.Y'.Z'}.md    ← Updated SRS (new version)
├── CHANGELOG.md                          ← Updated changelog
└── changes/
    └── change-{timestamp}-{scope}.md    ← Delta record
```

> The old SRS file is KEPT (not deleted) so it can be rolled back.
> To clean up, use: `/archive-docs {ProjectName}`
