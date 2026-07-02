---
name: export-docs
description: Xuất tài liệu SRS/SDD sang định dạng Word, PDF, HTML để bàn giao stakeholder
syntax: /export-docs [project_name] [--format <docx|pdf|html|all>] [--docs <srs|sdd|all>]
examples:
  - "/export-docs MyProject"
  - "/export-docs MyProject --format pdf"
  - "/export-docs MyProject --format docx --docs srs"
  - "/export-docs MyProject --format all"
agent: doc-coordinator
---

# Command: /export-docs

## Mục đích
Chuyển đổi tài liệu Markdown sang định dạng phù hợp để bàn giao cho stakeholder không kỹ thuật.

## Parameters

| Parameter | Default | Mô tả |
|-----------|---------|-------|
| `project_name` | required | Tên dự án |
| `--format` | `all` | `docx` / `pdf` / `html` / `all` |
| `--docs` | `all` | `srs` / `sdd` / `rtm` / `test` / `all` |

## Quy trình

```
1. Kiểm tra docs/{ProjectName} tồn tại
2. Kiểm tra docs đã có (SRS, SDD)
3. Spawn @formatter-agent với params
4. Formatter-agent tạo exports/
5. Thông báo kết quả và đường dẫn files
```

## Output Message

```
╔══════════════════════════════════════════════════════════════╗
║  ✅ EXPORT HOÀN THÀNH — {ProjectName} v{version}           ║
╠══════════════════════════════════════════════════════════════╣
║  📄 SRS:                                                    ║
║     → exports/SRS_{ProjectName}_v{version}.docx            ║
║     → exports/SRS_{ProjectName}_v{version}.pdf             ║
║  📐 SDD:                                                    ║
║     → exports/SDD_{ProjectName}_v{version}.docx            ║
║     → exports/SDD_{ProjectName}_v{version}.pdf             ║
║  📊 RTM (Excel):                                           ║
║     → exports/RTM_{ProjectName}_v{version}.xlsx            ║
║  🌐 HTML:                                                   ║
║     → exports/index.html                                    ║
║  📦 Full Package:                                           ║
║     → exports/{ProjectName}_docs_v{version}_{date}.zip     ║
╚══════════════════════════════════════════════════════════════╝
```
