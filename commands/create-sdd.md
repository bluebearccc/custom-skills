---
description: Tạo tài liệu Software Design Document (SDD)
agent: doc-coordinator
---

Tạo tài liệu Software Design Document (SDD) theo chuẩn IEEE.

Project name: $ARGUMENTS

Quy trình:
1. Kiểm tra SRS_$ARGUMENTS_v1.0.0.md có tồn tại
2. Spawn SDD Agent: @sdd-agent
3. SDD Agent sẽ đọc SRS, thiết kế architecture, tạo diagrams, và viết SDD
4. Output: SDD_$ARGUMENTS_v1.0.0.md + diagrams/ + db/ + api/

Yêu cầu:
- SDD phải theo template IEEE Std 1016
- Architecture diagrams phải là PlantUML format (.puml)
- Mỗi UC phải có folder riêng trong diagrams/ với nhiều diagram files
- Database schema phải là MySQL DDL
- API phải là OpenAPI 3.0 format
