---
description: Tạo tài liệu Software Requirement Specification (SRS)
agent: doc-coordinator
---

Tạo tài liệu Software Requirement Specification (SRS) theo chuẩn IEEE.

Project name: $ARGUMENTS

Quy trình:
1. Xác nhận tên dự án: $ARGUMENTS
2. Spawn SRS Agent: @srs-agent
3. SRS Agent sẽ thu thập requirements, tạo PlantUML diagrams, và viết SRS
4. Output: SRS_$ARGUMENTS_v1.0.0.md + diagrams/

Yêu cầu:
- SRS phải theo template IEEE
- Tất cả diagrams phải là PlantUML format (.puml)
- Mỗi use case phải có file riêng trong diagrams/
