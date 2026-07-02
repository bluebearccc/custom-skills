---
description: Generate a Software Design Document (SDD)
agent: doc-coordinator
---

Generate a Software Design Document (SDD) following the IEEE standard.

Project name: $ARGUMENTS

Process:
1. Check that SRS_$ARGUMENTS_v1.0.0.md exists
2. Spawn SDD Agent: @sdd-agent
3. The SDD Agent reads the SRS, designs the architecture, generates diagrams, and writes the SDD
4. Output: SDD_$ARGUMENTS_v1.0.0.md + diagrams/ + db/ + api/

Requirements:
- The SDD must follow the IEEE Std 1016 template
- Architecture diagrams must be in PlantUML format (.puml)
- Each UC must have its own folder under diagrams/ with multiple diagram files
- The database schema must be MySQL DDL
- The API must be in OpenAPI 3.0 format
