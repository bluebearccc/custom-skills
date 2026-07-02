---
name: export-docs
description: Export SRS/SDD documents to Word, PDF, HTML formats for stakeholder handoff
syntax: /export-docs [project_name] [--format <docx|pdf|html|all>] [--docs <srs|sdd|all>]
examples:
  - "/export-docs MyProject"
  - "/export-docs MyProject --format pdf"
  - "/export-docs MyProject --format docx --docs srs"
  - "/export-docs MyProject --format all"
agent: doc-coordinator
---

# Command: /export-docs

## Purpose
Convert Markdown documents into a format suitable for handoff to non-technical stakeholders.

## Parameters

| Parameter | Default | Description |
|-----------|---------|-------|
| `project_name` | required | Project name |
| `--format` | `all` | `docx` / `pdf` / `html` / `all` |
| `--docs` | `all` | `srs` / `sdd` / `rtm` / `test` / `all` |

## Process

```
1. Check that docs/{ProjectName} exists
2. Check which docs already exist (SRS, SDD)
3. Spawn @formatter-agent with params
4. Formatter-agent creates exports/
5. Report the result and file paths
```

## Output Message

```
╔══════════════════════════════════════════════════════════════╗
║  ✅ EXPORT COMPLETE — {ProjectName} v{version}              ║
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
