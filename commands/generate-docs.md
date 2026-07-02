---
description: Generate the full document set (SRS + SDD)
agent: doc-coordinator
---

Generate the full set of software documentation, including SRS and SDD.

Project name: $ARGUMENTS

Process:
Phase 1: Project Setup
- Confirm project name: $ARGUMENTS
- Create the docs/ directory structure

Phase 2: SRS
- Spawn SRS Agent: @srs-agent
- SRS Agent: Gathers requirements, generates PlantUML UC diagrams, writes the SRS
- Output: SRS_$ARGUMENTS_v1.0.0.md + diagrams/

Phase 3: SDD
- Spawn SDD Agent: @sdd-agent
- SDD Agent: Reads the SRS, designs the architecture, generates PlantUML diagrams, writes the SDD
- Output: SDD_$ARGUMENTS_v1.0.0.md + diagrams/ + db/ + api/

Phase 4: Package
- Cross-reference the SRS and SDD
- Generate index.md
- Final verification

Output structure:
docs/$ARGUMENTS/
├── SRS_$ARGUMENTS_v1.0.0.md
├── SDD_$ARGUMENTS_v1.0.0.md
├── index.md
├── diagrams/
│   ├── context-diagram.puml
│   ├── screen-flow.puml
│   ├── entity-relationship.puml
│   ├── system-overview.puml
│   ├── layered-architecture.puml
│   ├── deployment.puml
│   ├── integration.puml
│   ├── uc-01/ (6 files)
│   └── uc-02/ (6 files)
├── api/
│   └── openapi.yaml
└── db/
    └── schema.sql
