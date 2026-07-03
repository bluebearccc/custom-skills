# AgentCode Framework — Workflow & Documentation Review

> Review date: 2026-07-03 · Scope: the whole doc-creation pipeline (SRS / SDD / diagrams).
> Goal: find where the workflow is under-specified or where the documentation has drifted
> out of sync with the actual skills/agents.

This review looks at the framework end-to-end: `doc-coordinator` → `srs-agent` / `sdd-agent`
→ worker agents → skills → artifacts. Findings are grouped by severity. Items marked **FIXED**
were corrected in the same branch as this report; items marked **RECOMMENDATION** are process
gaps left for a human decision because fixing them safely requires redesign, not just an edit.

---

## Summary of findings

| # | Severity | Area | Finding | Status |
|---|----------|------|---------|--------|
| 1 | High | Docs drift | `user-flow.md` still documents the **old 23-question / 11-step** interview; the actual `requirements-gathering` skill is a **38-question / 12-step** interview (v4.0.0). | **FIXED** |
| 2 | Medium | Version drift | `requirements-gathering` is listed as **3.2.0** in README, AGENTS.md and MANIFEST.md, but the skill file declares **4.0.0**. | **FIXED** |
| 3 | Medium | Workflow gap | No explicit **SRS review/approval gate** between "SRS written" and "SDD started". The interview `CONFIRM` only signs off the *requirements*, not the generated SRS document. | **RECOMMENDATION** |
| 4 | Low | Workflow gap | The two `*-template` commands are handled by "any" agent with no skill and no pointer to `srs-sdd-template-patches.md`, so the template content source is implicit. | **RECOMMENDATION** |
| 5 | Low | Traceability | `requirements-gathering` now collects far more than the SRS template consumes (compliance, monitoring, support, config, success metrics, timeline). Several answers have no defined home in the SRS/SDD sections. | **RECOMMENDATION** |

---

## 1. `user-flow.md` describes a stale interview (HIGH)

`docs/user-flow.md` is the user-facing description of what happens when someone runs
`/create-srs`. It documents a **23-question interview across 11 steps**:

- "You will be asked 23 questions, one at a time."
- "### Step 3: THE 23-QUESTION INTERVIEW"
- "📝 Question 1/23 … Question 5/23"
- "## Detailed Interview Flow (23 Questions)" with STEP 1–11.

The actual skill `skills/requirements-gathering/SKILL.md` (v4.0.0) runs a **38-question interview
across 12 steps** and now includes whole topic areas the old flow never mentioned:

- Stakeholders & approval authority (Q5)
- Per-use-case detailed specification loop — preconditions/main flow/alternative/exception/postconditions (Q10)
- Validation & error handling (Q13)
- Reporting & analytics (Q14)
- Entity relationships & cardinality (Q16)
- External API specs & data-sync strategy (Q19–Q20)
- Internationalization / i18n (Q24)
- Scalability, Availability/SLA, Backup & recovery (Q26–Q28)
- Regulatory compliance, data classification, audit trail (Q30–Q32)
- Deployment/infra, monitoring/alerting, support/training, configuration management, success metrics, timeline (Q33–Q38)

**Why it matters:** `user-flow.md` is cited by README and AGENTS.md as the "step-by-step user
flow". Anyone (human or agent) treating it as the specification of the interview will believe the
system collects far less than it actually does — under-selling the mandatory interview and giving a
misleading picture of the requirements coverage. The 8-field use-case loop (Q10), which the diagram
agents depend on, is entirely absent from the old flow.

**Fix applied:** updated all `23` references to `38`, `11 steps` to `12 steps`, and replaced the
"Detailed Interview Flow" section so it mirrors the 12-step / 38-question structure of the current
skill, including the mandatory per-UC specification loop.

---

## 2. `requirements-gathering` version drift (MEDIUM)

The skill file header declares `version: "4.0.0"`, but every registry that lists it shows `3.2.0`:

- `README.md` (Skills table)
- `AGENTS.md` (Available Skills table)
- `MANIFEST.md` (Skills table)

AGENTS.md explicitly states its figures "reflect the actual state of the repo", so a stale version
number here is a self-contradiction. `srs-writing` and `sdd-writing` are correctly listed at 4.0.0,
so only `requirements-gathering` is out of date.

**Fix applied:** bumped the three registry entries from 3.2.0 to 4.0.0 to match the skill file.

---

## 3. No explicit SRS approval gate before SDD (RECOMMENDATION)

The mandatory gate today is the interview `CONFIRM`, which signs off `requirements-summary.md`.
`doc-coordinator`'s "Before spawning sdd-agent" checklist contains an **unchecked** item:

```
- [ ] SRS has been reviewed and approved
```

Nothing in the workflow actually surfaces the *generated SRS document* to the user for review
before `sdd-agent` starts designing from it. In `/generate-docs`, SDD begins as soon as the
requirements phase finishes — so the SRS is never independently approved. If the SRS mis-derives a
use case from the requirements, that error silently propagates into components, DB, API and tests.

**Recommendation:** add an optional "SRS review" checkpoint after Phase 3 of `srs-agent` — at least
in `/create-srs` and `/generate-docs` — where the coordinator presents the SRS path/summary and
waits for an explicit "approve SRS" (or an auto-approve flag for non-interactive runs). This closes
the loop the checklist already implies but the flow never enforces.

---

## 4. Template commands have an implicit content source (RECOMMENDATION)

`/create-srs-template` and `/create-sdd-template` are routed to "any" agent, use no skill, and don't
reference `srs-sdd-template-patches.md`, even though that file exists to hold template content.
A reader has no way to know where the template body comes from.

**Recommendation:** have the two template commands explicitly read `srs-sdd-template-patches.md`
(or move the canonical empty templates into the skills that own SRS/SDD writing) so the source of
truth is unambiguous.

---

## 5. Interview now collects more than the SRS/SDD templates consume (RECOMMENDATION)

The v4.0.0 interview gathers operational topics — monitoring/alerting (Q34), support & training
(Q35), configuration management (Q36), success metrics (Q37), timeline/phases (Q38) — that don't map
cleanly onto the documented SRS sections (Product Overview, User Requirements, Software Features,
NFRs, Appendix) or the SDD sections. `requirements-summary.md` captures them, but they can fall on
the floor when the SRS is written.

**Recommendation:** either (a) add explicit SRS/SDD appendix subsections (e.g. "Operations &
Support", "Success Metrics", "Project Timeline") so every interview answer has a destination, or
(b) mark those questions as "captured in requirements-summary only, not carried into the SRS" so the
traceability expectation is clear. Right now the mapping is undefined, which weakens the RTM the
`traceability-agent` is supposed to build.

---

## What changed in this branch

- `docs/user-flow.md` — interview updated from 23-question/11-step to 38-question/12-step.
- `README.md`, `AGENTS.md`, `MANIFEST.md` — `requirements-gathering` version 3.2.0 → 4.0.0.
- `docs/WORKFLOW_REVIEW.md` — this report.

Findings 3–5 are left as recommendations; they require product/workflow decisions rather than a
mechanical edit and should not be auto-applied.
