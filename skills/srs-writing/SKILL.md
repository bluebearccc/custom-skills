---
name: srs-writing
description: |
  Write a Software Requirement Specification (SRS) document following the IEEE standard, LINKing to external PlantUML files instead of embedding them directly.

  USE WHEN:
  - You need to create an SRS document from scratch
  - You need to finalize a partially-gathered SRS document
  - You need to review an SRS
  - A software project needs a requirements document
version: "4.0.0"
allowed-tools: ["Read", "Write", "Edit", "Glob"]
mode: false
---

# SRS Writing Skill

## Purpose
Write a complete Software Requirement Specification (SRS) document following the standard IEEE template. **LINK to external PlantUML files** instead of embedding them directly into the document.

## Important Principles

### DO NOT embed PlantUML directly
- ❌ DO NOT write `@startuml...@enduml` in the body of the SRS document
- ✅ USE links/references to external PlantUML files
- ✅ Diagrams are created by `uc-diagram-agent` instances spawned in parallel

### Rationale:
1. Separation of concerns: SRS document = text, Diagrams = PlantUML files
2. Parallel generation: N agents create diagrams in parallel, without blocking each other
3. Maintainability: Easy to update individual diagrams
4. Renderability: IDE/plugin renders PlantUML from a separate file

---

## SRS Template with External Links

### I. Record of Changes

| Version | Date | A/M/D* | In charge | Change Description |
| ----- | ----- | ----- | ----- | ----- |
| V1.0 | YYYY-MM-DD | A | Name | Initial version |

*A - Added M - Modified D - Deleted

---

### II. Software Requirement Specification

## 1. Product Overview

**1.1 Purpose**
- Describe the purpose of the document
- Describe the product being developed
- Describe the document's limitations

**1.2 Product Scope**
- Product name
- Key features
- Business objectives

**1.3 Target Users**
- End users
- Administrators
- Support staff

**1.4 Context Diagram**

*See details:* [Context Diagram](./diagrams/context-diagram.puml)

---

## 2. User Requirements

### 2.1 Actors

| # | Actor | Description |
| :---- | :---- | :---- |
| 1 | Super Admin | Manage and configure global system settings |
| 2 | Store Manager | Manage store-level operations |
| 3 | Staff | Operational users for sales and inventory |

### 2.2 Use Cases

---

#### **2.2.1 [Use Case Group Name]**

**Use Case Diagram:** [UC-XX - Name](./diagrams/uc-xx/uc-xx-use-case.puml)

| ID and Name: | UC-XX [Use Case Name] |  |  |
| ----: | :---- | ----: | :---- |
| Created By: | AuthorName | Date Created: | YYYY-MM-DD |
| Primary Actor: | [Primary Actor] | Secondary Actors: | [Secondary Actors] |
| Description: | [Detailed description of the use case] |  |  |
| Trigger: | [What triggers this use case] |  |  |
| Preconditions: | **PRE-1:** [Precondition 1] **PRE-2:** [Precondition 2] |  |  |
| Postconditions: | **POST-1 (Success):** [Postcondition on success] |  |  |
| Normal Flow: | 1. [Step 1] 2. [Step 2] 3. [Step 3] |  |  |
| Alternative Flows: | **A1. [Alternative Flow 1]** Trigger: [Condition] |  |  |
| Exceptions: | **E1. [Exception 1]** Trigger: [Error condition] |  |  |

**Diagrams:**
- Use Case: [UC-XX](./diagrams/uc-xx/uc-xx-use-case.puml)
- Sequence: [UC-XX](./diagrams/uc-xx/uc-xx-sequence.puml)

---

### 2.3 Acceptance Criteria per Use Case

> Every Use Case **MUST** have at least 3 Acceptance Criteria (AC) in Given-When-Then form.
> These ACs will become test cases in `test-plan/`.

#### 2.3.X UC-XX — {Use Case Name}

**AC-XX-01 (Happy Path):**
```
Given: {precondition — initial system state}
When:  {actor performs an action}
Then:  {expected result}
And:   {additional side effects, if any}
```

**AC-XX-02 (Alternative Flow):**
```
Given: {a different precondition}
When:  {the same or a different action}
Then:  {a different result}
```

**AC-XX-03 (Error / Boundary):**
```
Given: {invalid data or an error condition}
When:  {actor performs an action}
Then:  {specific error is displayed / system responds correctly}
```

> **Example UC-01 Login:**
>
> **AC-01-01 (Happy Path):**
> ```
> Given: User is not logged in, account is active, and email is verified
> When:  User enters the correct email + password and submits
> Then:  System returns access_token and refresh_token
> And:   User is redirected to the Dashboard
> ```
>
> **AC-01-02 (Wrong Password):**
> ```
> Given: User is not logged in
> When:  User enters the correct email but the wrong password
> Then:  System displays the error "Incorrect email or password"
> And:   Does not reveal which field is wrong
> ```
>
> **AC-01-03 (Account Disabled):**
> ```
> Given: Account is disabled (is_active = false)
> When:  User enters the correct email + password
> Then:  System displays "This account has been disabled"
> ```

---

## 3. Software Features

**3.1 Functional Overview**

**3.2 <<Feature Name>>**

| Item | Description |
|------|-------------|
| Feature ID | F1 |
| Feature Name | |
| Description | |
| Priority | Must / Should / Could / Won't |

**Functional Requirements:**
- FR1.1:
- FR1.2:

**User Interactions:**
1. [Step 1]
2. [Step 2]

**Diagrams:**
- Screen Flow: [UC-XX Screenflow](./diagrams/uc-xx/uc-xx-screenflow.puml)
- State: [UC-XX State](./diagrams/uc-xx/uc-xx-statediagram.puml)

---

## 4. Non-Functional Requirements

### 4.1 Performance Requirements
- Response time: < 200ms (P95)
- Throughput: 1000 concurrent users

### 4.2 Security Requirements
- Authentication: JWT tokens
- Authorization: RBAC

### 4.3 Availability
- Uptime: 99.9%

### 4.4 Scalability
- Horizontal scaling support

---

## 5. Requirement Appendix

**5.1 Business Rules**

| Rule ID | Rule | Description |
|---------|------|-------------|
| BR1 | | |

**5.2 Common Requirements**

| Requirement ID | Description |
|----------------|-------------|
| CR1 | |

**5.3 Application Messages List**

| Message Type | Format | Description |
|--------------|--------|-------------|
| | | |

**5.4 Other Requirements**
> [Other requirements]

### 5.5 Requirements Traceability Index (RTI)

> Summary linking table — see `traceability/RTM.md` for full details.

| FR ID | Feature | UC | Priority | Status |
|-------|---------|-----|----------|--------|
| FR-1.1 | {Feature name} | UC-01, UC-02 | Must | Draft |
| FR-1.2 | {Feature name} | UC-03 | Must | Draft |
| FR-2.1 | {Feature name} | UC-04 | Should | Draft |

*Status column: Draft → Reviewed → Approved → Implemented → Tested → Done*

---

## Output File
The SRS document is saved in the format: `SRS_{ProjectName}_v{Version}.md`

## Diagrams Directory Structure

```
diagrams/
├── context-diagram.puml              # System context
├── entity-relationship.puml           # Overall ER diagram
├── screen-flow.puml                   # System-wide screen flow
├── uc-01/
│   ├── uc-01-use-case.puml           # Use case diagram
│   ├── uc-01-screenflow.puml         # Screen flow
│   ├── uc-01-statediagram.puml       # State machine
│   ├── uc-01-sequence.puml            # Sequence diagram
│   ├── uc-01-class-backend.puml       # Backend classes
│   └── uc-01-class-frontend.puml      # Frontend classes
├── uc-02/
│   └── ...
└── ...
```

## Quality Checklist

- [ ] All stakeholders have been interviewed
- [ ] All actors have been identified
- [ ] Context Diagram: [context-diagram.puml](./diagrams/context-diagram.puml) ✓
- [ ] ER Diagram: [entity-relationship.puml](./diagrams/entity-relationship.puml) ✓
- [ ] Screen Flow: [screen-flow.puml](./diagrams/screen-flow.puml) ✓
- [ ] Each UC has all 6 diagram files
- [ ] Each UC has at least 3 Acceptance Criteria (Given-When-Then)
- [ ] Functional requirements are complete
- [ ] Non-functional requirements are concrete and measurable
- [ ] Section 5.5 RTI is fully filled in with the FR → UC mapping
- [ ] The document has been reviewed by stakeholders

## Commands

Use the `/create-srs` command to trigger this skill.