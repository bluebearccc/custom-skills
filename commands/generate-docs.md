---
description: Tạo toàn bộ tài liệu (SRS + SDD)
agent: doc-coordinator
---

Tạo toàn bộ tài liệu phần mềm bao gồm SRS và SDD.

Project name: $ARGUMENTS

Quy trình:
Phase 1: Project Setup
- Xác nhận project name: $ARGUMENTS
- Tạo cấu trúc thư mục docs/

Phase 2: SRS
- Spawn SRS Agent: @srs-agent
- SRS Agent: Thu thập requirements, tạo PlantUML UC diagrams, viết SRS
- Output: SRS_$ARGUMENTS_v1.0.0.md + diagrams/

Phase 3: SDD
- Spawn SDD Agent: @sdd-agent
- SDD Agent: Đọc SRS, thiết kế architecture, tạo PlantUML diagrams, viết SDD
- Output: SDD_$ARGUMENTS_v1.0.0.md + diagrams/ + db/ + api/

Phase 4: Package
- Cross-reference SRS và SDD
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
