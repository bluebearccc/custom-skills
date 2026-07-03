---
description: Generate a Software Requirement Specification (SRS)
agent: doc-coordinator
context: fork
---

Generate a Software Requirement Specification (SRS) following the IEEE standard.

Project name: $ARGUMENTS

Process:
1. Confirm project name: $ARGUMENTS
2. Spawn SRS Agent: @srs-agent
3. The SRS Agent gathers requirements, generates PlantUML diagrams, and writes the SRS
4. Output: SRS_$ARGUMENTS_v1.0.0.md + diagrams/

Requirements:
- The SRS must follow the IEEE template
- All diagrams must be in PlantUML format (.puml)
- Each use case must have its own file under diagrams/
